---
title: Azure Site Recovery - 미리 보기의 VMware VM 재해 복구 아키텍처
description: 이 문서에서는 Azure Site Recovery- 미리 보기를 사용하여 온-프레미스 VMware VM과 Azure 간 재해 복구를 설정할 때 사용되는 구성 요소와 아키텍처를 간략하게 설명합니다.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 999c2c74c92c6e6106bc6e1e36ff4f62e29e6e1c
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272795"
---
# <a name="vmware-to-azure-disaster-recovery-architecture---preview"></a>VMware와 Azure 간 재해 복구 아키텍처 - 미리 보기

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스 - 미리 보기를 사용하여 온-프레미스 VMware 사이트와 Azure 간에 VMware VM(가상 머신)의 재해 복구 복제, 장애 조치(failover), 복구를 배포할 때 사용되는 아키텍처와 프로세스를 설명합니다.

>[!NOTE]
> 미리 보기 어플라이언스를 설정하기 위해 새 Recovery Services 자격 증명 모음을 만들어야 합니다. 기존 자격 증명 모음을 사용하지 마세요.

클래식 아키텍처의 Azure Site Recovery 아키텍처에 관한 내용은 [이 문서](vmware-azure-architecture.md)를 참조하세요.


## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그림은 VMware VM/물리적 머신과 Azure 간 재해 복구에 사용되는 구성 요소를 개략적으로 보여 줍니다.

[![VMware에서 Azure로 아키텍처](./media/vmware-azure-architecture-preview/architecture-preview.png)](./media/vmware-azure-architecture-preview/architecture-preview.png#lightbox)

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독, 캐시에 대한 Azure Storage 계정, 관리 디스크 및 Azure 네트워크입니다. | 온-프레미스 VM에서 복제된 데이터는 Azure Storage에 저장됩니다. 장애 조치를 온-프레미스에서 Azure로 실행할 때 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**Azure Site Recovery 복제 어플라이언스** |     이는 전체 Azure Site Recovery 온-프레미스 인프라의 기본 구성 요소입니다. <br/><br/> 어플라이언스의 모든 구성 요소는 복제 어플라이언스에서 작동합니다. 이 서비스는 보호된 머신, 데이터 복제, 자동 업데이트 등의 상태 모니터링을 포함하여 모든 엔드투엔드 Site Recovery 작업을 감독합니다. | 어플라이언스는 다음과 같은 다양한 중요 구성 요소를 호스트합니다.<br/><br/>**Azure Site Recovery 프록시 서버:** 이 구성 요소는 클라우드에서 모바일 에이전트와 Site Recovery 서비스 간에 프록시 채널 역할을 합니다. 복구 지점을 생성하려면 프로덕션 워크로드에서 필요한 추가 인터넷 연결이 없는지 확인합니다.<br/><br/>**검색된 항목:** 이 구성 요소는 vCenter의 정보를 수집하며 클라우드의 Azure Site Recovery 관리 서비스에서 작동합니다.<br/><br/>**다시 보호 서버:** 이 구성 요소는 다시 보호 및 장애 조치(failback) 작업 중에 Azure와 온-프레미스 머신 간에 작동합니다.<br/><br/>**Azure Site Recovery 프로세스 서버:** 이 구성 요소는 Azure로 전송되기 전에 데이터의 캐싱, 압축에 사용됩니다. <br/><br/> 복제 어플라이언스 및 여러 복제 어플라이언스를 사용하는 방법에 관해 [자세히 알아봅니다](switch-replication-appliance-preview.md).<br/><br/>**복구 서비스 에이전트:** 이 구성 요소는 Site Recovery 서비스를 구성/등록하고 모든 구성 요소의 상태를 모니터링하는 데 사용됩니다.<br/><br/>**Site Recovery 공급자:** 이 구성 요소는 다시 보호를 가능하게 하는 데 사용됩니다. 원본 머신에 대한 대체 위치 다시 보호와 원래 위치 다시 보호 간에 식별합니다. <br/><br/> **복제 서비스:** 이 구성 요소는 원본 위치에서 Azure로 데이터를 복제하는 데 사용됩니다.
**VMware 서버** | VMware VM은 온-프레미스 vSphere ESXi 서버에서 호스트됩니다. 호스트를 관리하려면 vCenter 서버를 사용하는 것이 좋습니다. | Site Recovery 배포 중에 VMware 서버를e Recovery Services 자격 증명 모음에 추가합니다.
**복제된 컴퓨터** | 복제한 각 VMware VM에 모바일 서비스가 설치됩니다. | Mobility Service의 자동 설치를 허용하는 것이 좋습니다. 또는 [수동으로 서비스](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui-preview)를 설치할 수 있습니다.


## <a name="set-up-outbound-network-connectivity"></a>아웃바운드 네트워크 연결 설정

Site Recovery가 예상대로 작동하려면 아웃바운드 네트워크 연결을 수정하여 환경 복제를 허용해야 합니다.

> [!NOTE]
> Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다.

### <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.

| **URL**                  | **세부 정보**                             |
| ------------------------- | -------------------------------------------|
| portal.azure.com          | Azure Portal로 이동합니다.              |
| `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Azure 구독에 로그인하려면.  |
|`*.microsoftonline.com `|어플라이언스에서 Azure Site Recovery와 통신할 수 있도록 Azure AD(Active Directory) 앱을 만듭니다. |
|management.azure.com |어플라이언스에서 Azure Site Recovery 서비스와 통신할 수 있도록 Azure AD 앱을 만듭니다. |
|`*.services.visualstudio.com `|내부 모니터링에 사용되는 앱 로그를 업로드합니다. |
|`*.vault.azure.net `|Azure Key Vault에서 비밀을 관리합니다. 참고: 복제할 컴퓨터에서 이 URL에 액세스할 수 있는지 확인합니다. |
|aka.ms |aka 링크에 대한 액세스를 허용합니다. Azure Site Recovery 어플라이언스 업데이트에 사용됩니다. |
|download.microsoft.com/download |Microsoft 다운로드에서 다운로드할 수 있습니다. |
|`*.servicebus.windows.net `|어플라이언스와 Azure Site Recovery 서비스 간의 통신입니다. |
|`*.discoverysrv.windowsazure.com `|Azure Site Recovery 검색 서비스 URL에 연결합니다. |
|`*.hypervrecoverymanager.windowsazure.com `|Azure Site Recovery 마이크로 서비스 URL에 연결합니다.  |
|`*.blob.core.windows.net `|대상 디스크를 만드는 데 사용되는 Azure Storage에 데이터를 업로드합니다. |
|`*.backup.windowsazure.com `|보호 서비스 URL – Azure에서 복제된 디스크를 처리하고 만들기 위해 Azure Site Recovery에서 사용하는 마이크로 서비스입니다. |



## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 다음 사항에 유의하세요.
    - VMware VM의 경우 복제는 VM에서 실행 중인 모바일 서비스 에이전트를 사용하여 블록 수준에서 거의 지속적으로 이루어집니다.
    - 복제 정책 설정이 적용됩니다.
        - **RPO 임계값**: 이 설정은 복제에 영향을 주지 않습니다. 모니터링에 도움이 됩니다. 현재 RPO가 지정 임계값 한도를 초과하는 경우 이벤트가 발생하고 선택적으로 메일이 전송됩니다.
        - **복구 지점 보존**: 이 설정은 중단이 발생하는 경우 얼마나 오래전으로 되돌아갈지 지정합니다. Premium Storage의 최대 보존 기간은 24시간입니다. 표준 스토리지의 경우는 72시간입니다.
        - **앱 일치 스냅샷**. 앱의 요구 사항에 따라 1시간에서 12시간마다 앱 일치 스냅샷을 만들 수 있습니다. 스냅샷은 표준 Azure Blob 스냅샷입니다. VM에서 실행되는 모바일 에이전트는 이 설정에 따라 VSS 스냅샷을 요청하며 이 특정 시점을 복제 스트림의 애플리케이션 일치 지점으로 북마크합니다.

2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. 또는 Azure ExpressRoute와 [Microsoft 피어링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)을 함께 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제 작업을 수행하면 복제 사용 설정 시 컴퓨터의 전체 데이터가 Azure로 전송됩니다. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 머신의 추적된 변경 내용이 프로세스 서버로 전송됩니다.
4. 다음과 같이 통신이 발생합니다.

    - VM은 복제 관리를 위해 HTTPS 443 인바운드 포트에서 온-프레미스 어플라이언스와 통신합니다.
    - 어플라이언스는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제를 오케스트레이션합니다.
    - VM은 복제 데이터를 HTTPS 9443 인바운드 포트에서 프로세스 서버로 보냅니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.
5. 복제 데이터 로그는 먼저 Azure의 캐시 스토리지 계정으로 이동합니다. 이러한 로그는 처리되고 데이터는 Azure 관리 디스크(*asrseeddisk* 라고 함)에 저장됩니다. 이 디스크에는 복구 지점이 만들어집니다.

## <a name="resynchronization-process"></a>다시 동기화 프로세스

1. 경우에 따라서는 초기 복제 또는 델타 변경 내용 전송 중에 원본 컴퓨터와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 네트워크 연결에 문제가 있을 수 있습니다. 이들 중 어떤 문제든 Azure로의 데이터 전송이 잠시 실패하는 원인이 될 수 있습니다.
2. 데이터 무결성 문제를 방지하고 데이터 전송 비용을 최소화하기 위해 Site Recovery는 다시 동기화할 컴퓨터를 표시합니다.
3. 또한 원본 머신과 Azure에 저장된 데이터 간의 일관성을 유지하기 위해 다음과 같은 상황에서 머신은 다시 동기화하도록 표시될 수 있습니다.
    - 컴퓨터가 강제 종료되는 경우
    - 컴퓨터에서 디스크 크기 조정과 같은 구성 변경을 수행하는 경우(디스크 크기를 2TB에서 4TB로 수정)
4. 다시 동기화할 때는 델타 데이터만 Azure에 전송됩니다. 온-프레미스와 Azure 간의 데이터 전송은 원본 컴퓨터와 Azure에 저장된 데이터 간의 데이터 체크섬 계산을 통해 최소화됩니다.
5. 기본적으로 다시 동기화는 업무 시간 이외에 자동으로 실행되도록 예약됩니다. 일정대로 기본 다시 동기화 시간까지 기다릴 수 없으면 수동으로 VM을 다시 동기화할 수 있습니다. 이렇게 하려면 Azure Portal에서 VM > **다시 동기화** 를 선택합니다.
6. 업무 시간이 아닐 때 기본적인 다시 동기화가 실패하고 수동 개입이 필요한 경우 Azure Portal의 특정 컴퓨터에서 오류가 생성됩니다. 오류를 해결하고 다시 동기화를 수동으로 트리거할 수 있습니다.
7. 다시 동기화가 완료되면 델타 변경 내용의 복제가 다시 시작됩니다.

## <a name="replication-policy"></a>복제 정책

Azure VM 복제를 활성화하면 기본적으로 Site Recovery는 표에 요약된 기본 설정을 사용하여 새 복제 정책을 만듭니다.

**정책 설정** | **세부 정보** | **기본**
--- | --- | ---
**복구 지점 보존** | Site Recovery에서 복구 지점을 유지하는 기간을 지정합니다. | 72시간
**앱 일치 스냅샷 빈도** | Site Recovery에서 앱 일치 스냅샷을 만드는 주기입니다. | 4시간마다

### <a name="managing-replication-policies"></a>복제 정책 관리

다음과 같이 기본 복제 정책 설정을 관리 및 수정할 수 있습니다.
- 복제를 사용하도록 설정하면 설정을 수정할 수 있습니다.
- 복제를 사용하도록 설정하는 동안 새 복제 정책을 만들거나 편집할 수 있습니다.

### <a name="multi-vm-consistency"></a>다중 VM 일관성

VM을 함께 복제하고, 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 가지려는 경우 복제 그룹으로 함께 수집할 수 있습니다. 다중 VM 일관성은 워크로드 성능에 영향을 미치며, 모든 머신에서 일관성이 필요한 VM 4개 워크로드에만 사용해야 합니다.



## <a name="snapshots-and-recovery-points"></a>스냅샷 및 복구 지점

복구 지점은 특정 시점에 생성되는 VM 디스크의 스냅샷에서 생성됩니다. VM을 장애 조치(failover)할 때 복구 지점을 사용하여 대상 위치에서 VM을 복원합니다.

장애 조치(failover)할 때 일반적으로 손상 또는 데이터 손실 없이 VM을 시작하고, VM 데이터가 운영 체제 및 VM에서 실행되는 앱에 대해 일관되는지 확인하려고 합니다. 이는 생성되는 스냅샷의 유형에 따라 달라집니다.

Site Recovery는 다음과 같이 스냅샷을 생성합니다.

1. Site Recovery는 빈도를 지정하는 경우 기본적으로 데이터의 크래시 일관성이 있는 스냅샷 및 앱 일치 스냅샷을 생성합니다.
2. 복구 지점은 스냅샷에서 생성되고, 복제 정책에서 보존 설정에 따라 저장됩니다.

### <a name="consistency"></a>일관성

다음 표에서는 여러 유형의 일관성에 대해 설명합니다.

### <a name="crash-consistent"></a>크래시 일관성

**설명** | **세부 정보** | **권장**
--- | --- | ---
크래시 일관성 스냅샷은 스냅샷을 만들 때 디스크에 있는 데이터를 캡처합니다. 메모리의 데이터를 포함하지 않습니다.<br/><br/> VM이 충돌하거나 스냅샷이 생성된 순간 전원 코드를 서버에서 가져온 경우 존재하는 디스크의 데이터에 해당합니다.<br/><br/> 크래시 일관성은 운영 체제 또는 VM의 앱에 대한 데이터 일관성을 보장하지 않습니다. | Site Recovery는 기본적으로 5분마다 크래시 일관성 복구 지점을 만듭니다. 이 설정은 수정할 수 없습니다.<br/><br/>  | 오늘날 대부분의 앱은 크래시 일관성 지점에서도 제대로 복구할 수 있습니다.<br/><br/> 크래시 일관성 복구 지점은 일반적으로 운영 체제 및 DHCP 서버 및 인쇄 서버와 같은 앱의 복제를 위해 충분합니다.

### <a name="app-consistent"></a>앱 일치

**설명** | **세부 정보** | **권장**
--- | --- | ---
앱 일치 복구 지점은 앱 일치 스냅샷에서 생성됩니다.<br/><br/> 앱 일치 스냅샷은 크래시 일관성 스냅샷의 모든 정보와 메모리의 모든 데이터 및 진행 중인 트랜잭션을 포함합니다. | 앱 일치 스냅샷은 VSS(볼륨 섀도 복사본 서비스)를 사용합니다.<br/><br/>   1) Azure Site Recovery는 Microsoft SQL의 트랜잭션 로그 백업 시간 및 시퀀스 번호를 변경하지 않는 복사 전용 백업(VSS_BT_COPY) 방법을 사용합니다. </br></br> 2) 스냅샷이 시작되면 VSS는 볼륨에 COW(기록 중 복사) 작업을 수행합니다.<br/><br/>   3) COW를 수행하기 전에 VSS는 머신의 모든 앱에 해당 메모리 상주 데이터를 디스크에 플러시해야 함을 알립니다.<br/><br/>   4) 그런 다음, VSS는 백업/재해 복구 앱(이 경우 Site Recovery)에서 스냅샷 데이터를 읽고 진행하도록 허용합니다. | 앱 일치 스냅샷은 지정하는 빈도에 따라 생성됩니다. 이 빈도는 항상 복구 지점 유지에 대한 설정보다 작아야 합니다. 예를 들어 24시간의 기본 설정을 사용하여 복구 지점을 보존하는 경우 빈도를 24시간 미만으로 설정해야 합니다.<br/><br/>크래시 일관성 스냅샷보다 더 복잡하며 완료하는 데 시간이 더 걸립니다.<br/><br/> 복제에 대해 활성화된 VM에서 실행되는 앱의 성능에 영향을 미칩니다.

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

복제가 설정되고 재해 복구 훈련(테스트 장애 조치)을 실행하여 모든 것이 예상대로 작동하는지 확인한 후 필요에 따라 장애 조치와 장애 복구를 실행할 수 있습니다.

1. 단일 머신에서 장애 조치를 실행하거나 여러 VM을 동시에 장애 조치할 복구 계획을 만들 수 있습니다. 단일 컴퓨터 장애 조치(Failover) 이외의 복구 계획을 사용할 때의 이점은 다음과 같습니다.
    - 단일 복구 계획에 앱의 모든 VM을 포함하여 앱 종속성을 모델링할 수 있습니다.
    - 스크립트, Azure Runbook 및 수동 작업에 대한 일시 중지를 추가할 수 있습니다.
2. 초기 장애 조치를 트리거한 후 이를 커밋하여 Azure VM에서 워크로드 액세스를 시작합니다.
3. 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구(Failback)를 준비할 수 있습니다. 대규모 트래픽을 장애 복구해야 하는 경우 새 Azure Site Recovery 복제 어플라이언스를 설정합니다.

    - 1단계: Azure VM을 다시 보호하여 Azure에서 다시 온-프레미스 VMware VM으로의 복제하도록 합니다.
    - 2단계: 온-프레미스 사이트에서 장애 조치를 실행합니다.
    - 3단계: 워크로드가 장애 복구되면 온-프레미스 VM에 대한 복제를 다시 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

[이 자습서](vmware-azure-tutorial.md)를 따라 VMware와 Azure 간의 복제를 활성화합니다.
