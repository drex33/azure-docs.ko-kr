---
title: Azure Virtual Desktop 재해 복구 계획
description: 데이터를 보호하기 위해 Azure Virtual Desktop 배포에 대한 재해 복구 계획을 수립합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54202b49e2881d1d128136b876cbf88c2bc87400
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359760"
---
# <a name="azure-virtual-desktop-disaster-recovery"></a>Azure Virtual Desktop 재해 복구

조직의 데이터를 안전하게 유지하려면 BCDR(비즈니스 연속성 및 재해 복구) 전략을 채택해야 할 수 있습니다. 안전한 BCDR 전략을 통해 계획되거나 계획되지 않은 서비스 또는 Azure 중단 시에도 애플리케이션과 워크로드를 계속 실행 상태로 유지할 수 있습니다.

Azure Virtual Desktop은 Azure Virtual Desktop 서비스에 BCDR을 제공하여 중단 중에 고객 메타데이터를 보존합니다. 지역에서 중단이 발생하면 서비스 인프라 구성 요소가 보조 위치로 장애 조치(failover)되어 계속 정상적으로 작동합니다. 서비스 관련 메타데이터에 계속 액세스할 수 있으며 사용자는 사용 가능한 호스트에 계속 연결할 수 있습니다. 최종 사용자 연결은 테넌트 환경 또는 호스트에 액세스할 수 있는 한 온라인 상태를 유지합니다.

지역 중단 중에 사용자가 계속 연결할 수 있도록 하려면 사용자의 VM(가상 머신)을 다른 위치에 복제해야 합니다. 중단된 경우, 기본 사이트는 보조 위치에 있는 복제된 VM으로 장애 조치(failover)됩니다. 사용자는 중단 없이 보조 위치에서 앱에 계속 액세스할 수 있습니다. VM 복제 외에도 보조 위치에서 액세스할 수 있는 사용자 ID를 유지해야 합니다. 프로필 컨테이너를 사용하는 경우에도 복제해야 합니다. 마지막으로 기본 위치의 데이터에 의존하는 비즈니스 앱이 나머지 데이터로 장애 조치(failover)될 수 있는지 확인합니다.

요약하면, 중단 중에 사용자를 연결 상태로 유지하려면 다음 작업을 이 순서대로 수행해야 합니다.

- 보조 위치에서 VM을 복제합니다.
- 프로필 컨테이너를 사용하는 경우 보조 위치에서 데이터 복제를 설정합니다.
- 기본 위치에서 설정한 사용자 ID를 보조 위치에서 사용할 수 있는지 확인합니다.
- 기본 위치의 데이터를 사용하는 모든 LOB(기간 업무) 애플리케이션이 보조 위치로 장애 조치(failover)되었는지 확인합니다.

## <a name="vm-replication"></a>VM 복제

먼저 VM을 보조 위치에 복제해야 합니다. 이를 위한 옵션은 VM 구성 방법에 따라 다릅니다.

- Azure Site Recovery를 사용하여 풀링된 호스트 풀과 개인 호스트 풀 모두에 대해 모든 VM을 구성할 수 있습니다. 이 방법을 사용하면 하나의 호스트 풀과 관련 앱 그룹 및 작업 영역만 설정하면 됩니다.
- 장애 조치(failover) 위치의 모든 리소스를 꺼진 상태로 유지하면서 장애 조치(failover) 지역에 새 호스트 풀을 만들 수 있습니다. 이 방법의 경우 장애 조치(failover) 지역에서 새 앱 그룹 및 작업 영역을 설정해야 합니다. 그런 다음, Azure Site Recovery 계획을 사용하여 호스트 풀을 켤 수 있습니다.
- 장애 조치(failover) 지역의 VM을 꺼진 상태로 유지하면서 기본 및 장애 조치(failover) 지역에 빌드된 VM으로 채워진 호스트 풀을 만들 수 있습니다. 이 경우, 하나의 호스트 풀과 관련 앱 그룹 및 작업 영역만 설정하면 됩니다. Azure Site Recovery 플랜을 사용하여 이 방법으로 호스트 풀의 전원을 켤 수 있습니다.

[Azure-to-Azure 재해 복구 아키텍처](../site-recovery/azure-to-azure-architecture.md)에 설명된 대로 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)를 사용하여 다른 Azure 위치에서 VM 복제를 관리하는 것이 좋습니다. Azure Site Recovery는 [서버 기반 및 클라이언트 기반 SKU](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)를 모두 지원하므로 특히 개인 호스트 풀에 Azure Site Recovery를 사용하는 것이 좋습니다.

Azure Site Recovery를 사용하는 경우 이러한 VM을 수동으로 등록할 필요가 없습니다. 보조 VM의 Azure Virtual Desktop 에이전트는 자동으로 최신 보안 토큰을 사용하여 가장 가까운 서비스 인스턴스에 연결합니다. 보조 위치의 VM(세션 호스트)은 자동으로 호스트 풀의 일부가 됩니다. 최종 사용자는 프로세스 중에 다시 연결해야 하지만, 그 외에는 다른 수동 작업이 없습니다.

중단 중에 기존 사용자 연결이 있는 경우 관리자가 보조 지역으로 장애 조치(failover)를 시작하기 전에 현재 지역에서 사용자 연결을 종료해야 합니다.

Azure Virtual Desktop(클래식)에서 사용자의 연결을 끊으려면 다음 cmdlet을 실행합니다.

```powershell
Invoke-RdsUserSessionLogoff
```

Azure 통합 버전의 Azure Virtual Desktop에서 사용자 연결을 끊으려면 다음 cmdlet을 실행합니다.

```powershell
Remove-AzWvdUserSession
```

기본 지역의 모든 사용자를 로그아웃하면 기본 지역의 VM을 장애 조치(failover)하고 사용자가 보조 지역의 VM에 연결할 수 있습니다. 이 프로세스의 작동 방식에 대한 자세한 내용은 [Azure VM을 다른 Azure 지역에 복제](../site-recovery/azure-to-azure-how-to-enable-replication.md)를 참조하세요.

## <a name="virtual-network"></a>가상 네트워크

그 다음, 중단 중에 네트워크 연결을 고려합니다. 보조 지역에서 VNET(가상 네트워크)를 설정했는지 확인해야 합니다. 사용자가 온-프레미스 리소스에 액세스해야 하는 경우 이에 액세스할 수 있도록 이 VNET를 구성해야 합니다. VPN, ExpressRoute 또는 가상 WAN을 사용하여 온-프레미스 연결을 설정할 수 있습니다.

Azure Site Recovery를 사용하여 기본 네트워크의 설정을 유지하고 피어링이 필요하지 않으므로 장애 조치(failover) 지역에서 VNET을 설정하는 것이 좋습니다.

## <a name="user-identities"></a>사용자 ID

다음으로 도메인 컨트롤러를 보조 위치에서 사용할 수 있는지 확인합니다. 

도메인 컨트롤러를 사용 가능하게 유지하는 세 가지 방법이 있습니다.

   - 보조 위치에 Active Directory 도메인 컨트롤러 포함
   - 온-프레미스 Active Directory 도메인 컨트롤러 사용
   - [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)를 사용하여 Active Directory 도메인 컨트롤러 복제

## <a name="user-and-app-data"></a>사용자 및 앱 데이터

프로필 컨테이너를 사용하는 경우 다음 단계는 보조 위치에서 데이터 복제를 설정하는 것입니다. FSLogix 프로필을 저장하는 5가지 옵션이 있습니다.

   - S2D(스토리지 공간 다이렉트)
   - 네트워크 드라이브(추가 드라이브가 있는 VM)
   - Azure Files
   - Azure NetApp Files
   - 복제용 클라우드 캐시

자세한 내용은 [ Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 저장소 옵션](store-fslogix-profile.md)을 확인하세요.

프로필에 대한 재해 복구를 설정하는 경우 다음과 같은 옵션이 있습니다.

   - 네이티브 Azure 복제(예: Azure Files Standard 스토리지 계정 복제, Azure NetApp Files 복제 또는 파일 서버용 Azure Files Sync)를 설정합니다.
    
     >[!NOTE]
     >NetApp 복제는 처음 설정한 후 자동으로 수행됩니다. Azure Site Recovery 계획을 사용하면 비 VM 리소스를 장애 조치(failover)하여 Azure Storage 리소스를 복제하기 위해 사전 스크립트 및 사후 스크립트를 추가할 수 있습니다.

   - 앱 및 사용자 데이터 모두에 대해 FSLogix Cloud Cache를 설정합니다.
   - 항상 중요 비즈니스용 데이터에 액세스할 수 있도록 앱 데이터에 대해서만 재해 복구를 설정합니다. 이 방법을 사용하면 중단이 끝난 후 사용자 데이터를 검색할 수 있습니다.

각 옵션에 대해 재해 복구를 설정하도록 FSLogix를 구성하는 방법을 살펴보겠습니다.

### <a name="fslogix-configuration"></a>FSLogix 구성

FSLogix에 대한 레지스트리 항목을 구성하는 경우 FSLogix 에이전트는 여러 프로필 위치를 지원할 수 있습니다.

레지스트리 항목을 구성하려면 다음을 수행합니다.

1. **레지스트리 편집기** 를 엽니다.
2. **컴퓨터** > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix** > **Profiles** 로 이동합니다.
   
     > [!div class="mx-imgBorder"]
     > ![레지스트리 편집기에서 프로필 창의 스크린샷. VHDLocation이 선택됩니다.](media/regedit-profiles.png)

3. **VHDLocations** 를 마우스 오른쪽 단추로 클릭하고 **다중 문자열 편집** 을 선택합니다.

     > [!div class="mx-imgBorder"]
     > ![다중 문자열 편집 창의 스크린샷. 값 데이터에는 미국 중부 및 미국 동부 위치가 나열됩니다.](media/multi-string-edit.png)

4. **값 데이터** 필드에 사용하려는 위치를 입력합니다.
5. 완료되면 **확인** 을 선택합니다.

첫 번째 위치를 사용할 수 없는 경우 FSLogix 에이전트는 자동으로 두 번째 위치로 장애 조치(failover)합니다.

기본 지역의 보조 위치에 대한 경로를 사용하여 FSLogix 에이전트를 구성하는 것이 좋습니다. 기본 위치가 종료되면 FLogix 에이전트는 VM Azure Site Recovery 복제의 일부로 복제됩니다. 복제된 VM이 준비되면 에이전트는 자동으로 보조 지역으로의 경로를 시도합니다.

예를 들어 기본 세션 호스트 VM이 미국 중부 지역에 있지만 프로필 컨테이너가 성능상의 이유로 미국 중부 지역에 있다고 가정해 보겠습니다.

이 경우 미국 중부의 스토리지 경로를 사용하여 FSLogix 에이전트를 구성합니다. 미국 서부에서 복제할 세션 호스트 VM을 구성합니다. 미국 중부 경로가 실패하면 에이전트는 대신 미국 서부에 새 스토리지 경로를 만들려고 시도할 것입니다.

### <a name="s2d"></a>S2D

S2D는 내부적으로 복제를 처리하므로 보조 경로를 수동으로 설정할 필요가 없습니다.

### <a name="network-drives-vm-with-extra-drives"></a>네트워크 드라이브(추가 드라이브가 있는 VM)

세션 호스트 VM처럼 Azure Site Recovery를 사용하여 네트워크 스토리지 VM을 복제하는 경우 복구는 동일한 경로를 유지하므로 FSlogix를 다시 구성할 필요가 없습니다.

### <a name="azure-files"></a>Azure Files

Azure Files는 스토리지 계정을 만들 때 지정할 수 있는 지역 간 비동기 복제를 지원합니다. Azure Files의 비동기 특성이 이미 재해 복구 목표를 충족하는 경우 추가 구성을 수행할 필요가 없습니다.

데이터 손실을 최소화하기 위해 동기 복제가 필요한 경우 대신 FSLogix Cloud Cache를 사용하는 것이 좋습니다.

>[!NOTE]
>이 섹션에서는 Azure Files에 대한 장애 조치(failover) 인증 메커니즘을 다루지 않습니다.

### <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files에 대한 복제 피어링 만들기](../azure-netapp-files/cross-region-replication-create-peering.md)에서 Azure NetApp Files에 대해 자세히 알아보세요.

## <a name="app-dependencies"></a>앱 종속성

마지막으로 기본 지역에 있는 데이터에 의존하는 모든 비즈니스 앱이 보조 위치로 장애 조치(failover)할 수 있는지 확인합니다. 또한 앱이 새 위치에서 작동하는 데 필요한 설정을 구성해야 합니다. 예를 들어 앱 중 하나가 SQL 백 엔드에 종속된 경우 보조 위치에 SQL을 복제해야 합니다. 보조 위치를 장애 조치(failover) 프로세스의 일부로 사용하거나 기본 구성으로 사용하도록 앱을 구성해야 합니다. Azure Site Recovery 계획에 대한 앱 종속성을 모델링할 수 있습니다. 자세한 내용은 [복구 계획 정보](../site-recovery/recovery-plan-overview.md)를 참조하세요.

## <a name="disaster-recovery-testing"></a>재해 복구 테스트

재해 복구 설정을 완료한 후 계획을 테스트하여 작동하는지 확인하는 것이 좋습니다.

다음은 계획을 테스트하는 방법에 대한 몇 가지 제안입니다.

- 테스트 VM에 인터넷 액세스 권한이 있는 경우 새 연결을 위해 기존 세션 호스트를 대신하지만 원래 세션 호스트에 대한 모든 기존 연결은 활성 상태로 유지됩니다. 계획을 테스트하기 전에 테스트를 실행하는 관리자가 모든 활성 사용자를 로그아웃해야 합니다. 
- 사용자를 방해하지 않도록 유지 관리 기간 동안에만 전체 재해 복구 테스트를 수행해야 합니다. 테스트를 위해 유효성 검사 환경에서 호스트 풀을 사용할 수도 있습니다. 
- 테스트가 모든 중요 비즈니스용 앱을 포함하는지 확인합니다.
- 한 번에 최대 100개의 VM만 장애 조치(failover)하는 것이 좋습니다. 그보다 많은 VM이 있는 경우 10분 간격으로 일괄적으로 장애 조치(failover)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

중단 계획 외에도 데이터를 안전하게 유지하는 방법에 대한 질문이 있으면 [보안 가이드](security-guide.md)를 확인하세요.