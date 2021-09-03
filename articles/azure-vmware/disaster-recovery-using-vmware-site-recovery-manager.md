---
title: VMware Site Recovery Manager로 재해 복구 배포
description: Azure VMware Solution 프라이빗 클라우드에서 VMware SRM(Site Recovery Manager)을 사용하여 재해 복구를 배포합니다.
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 7c4f518623801e8cb489452dbeb3be76e1104e62
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608068"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager로 재해 복구 배포

이 문서에서는 온-프레미스 VMware VM(가상 머신) 또는 Azure VMware Solution 기반 VM에 대한 재해 복구를 구현하는 방법을 설명합니다.  이 문서의 솔루션은 Azure VMware Solution의 [VMware SRM(Site Recovery Manager)](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 및 vSphere 복제를 사용합니다. SRM 및 복제 서버의 인스턴스는 보호된 사이트와 복구 사이트 모두에 배포됩니다.       

SRM은 재해가 발생한 경우 Azure VMware Solution 환경에서 가상 머신의 가동 중지 시간을 최소화하도록 설계된 재해 복구 솔루션입니다. SRM은 장애 조치(failover) 및 장애 복구(failback)를 자동화하고 오케스트레이션하여 재해 발생 시 가동 중지 시간을 최소화합니다. 또한 기본 제공되는 무중단 테스트를 통해 복구 시간 목표를 달성할 수 있습니다. 전반적으로 SRM은 자동화를 통해 관리를 단순화하고 빠르고 예측 가능한 복구 시간을 보장합니다.  

vSphere 복제는 vSphere VM을 위한 VMware의 하이퍼바이저 기반 복제 기술입니다. 부분적이거나 완전한 사이트 장애로부터 VM을 보호합니다. 또한 스토리지 독립적인 VM 중심 복제를 통해 DR 보호를 단순화합니다. vSphere 복제는 VM별로 구성되어 복제되는 VM을 더 잘 제어할 수 있습니다.

이 문서에서는 온-프레미스 VMware VM(가상 머신) 또는 Azure VMware Solution 기반 VM에 대한 재해 복구를 구현합니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

SRM을 사용하면 보호된 vCenter Server 사이트와 복구 vCenter Server 사이트 간의 VM 복구를 계획, 테스트 및 실행할 수 있습니다. 다음 두 가지 DR 시나리오에서 Azure VMware Solution과 함께 SRM을 사용할 수 있습니다. 

- 온-프레미스 VMware-Azure VMware Solution 프라이빗 클라우드 재해 복구 
- 기본 Azure VMware Solution에서 보조 Azure VMware Solution 프라이빗 클라우드 재해 복구 

보조 Azure VMware Solution 시나리오에 대한 기본 Azure VMware Solution 배포를 보여 주는 다이어그램.

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="Azure VMware Solution의 VMware SRM(Site Recovery Manager) 재해 복구 솔루션을 보여 주는 다이어그램." border="false":::

SRM을 사용하여 다음과 같은 다양한 유형의 복구를 구현할 수 있습니다.

- **계획된 마이그레이션** 은 기본 및 보조 Azure VMware Solution 사이트가 모두 실행되고 완전히 작동할 때 시작됩니다. 워크로드를 순서대로 마이그레이션할 때 데이터 손실이 예상되지 않는 보호된 사이트에서 복구 사이트로 가상 머신을 순서대로 마이그레이션하는 것입니다. 

- SRM을 사용한 **재해 복구** 는 보호된 Azure VMware Solution 사이트가 예기치 않게 오프라인 상태가 될 때 호출될 수 있습니다. Site Recovery Manager는 데이터 손실과 시스템 가동 중지 시간을 최소화하기 위해 복제 메커니즘으로 복구 프로세스를 오케스트레이션합니다.

   Azure VMware Solution에서는 vSphere 복제와 함께 SRM을 사용하여 호스트에서 개별 VM만 보호할 수 있습니다.

- **양방향 보호** 는 쌍으로 된 SRM 사이트의 단일 집합을 사용하여 양방향으로 VM을 보호합니다. 각 사이트는 동시에 보호된 사이트와 복구 사이트가 될 수 있지만 VM 집합은 다릅니다.

>[!IMPORTANT]
>Azure VMware Solution은 다음을 지원하지 않습니다. 
>
>- 어레이 기반 복제 및 스토리지 정책 보호 그룹 
>- VVOL 보호 그룹 
>- SRM 명령줄 도구를 사용한 SRM IP 사용자 지정
>- 일대다 및 다대일 토폴로지 
>- 사용자 지정 SRM 플러그 인 식별자 또는 확장 ID


## <a name="deployment-workflow"></a>배포 워크플로

기본 Azure VMware Solution에서 보조 워크플로를 보여 주는 워크플로 다이어그램. 또한 Azure Portal 및 Azure VMware Solution의 VMware 환경 내에서 VM의 엔드투엔드 보호를 달성하기 위해 수행하는 단계를 보여 줍니다. 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="Azure VMware Solution에서 VMware Site Recovery Manager의 배포 워크플로를 보여 주는 다이어그램." border="false":::

## <a name="prerequisites"></a>필수 구성 요소

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>시나리오: 온-프레미스에서 Azure VMware Solution으로 

- 보조 지역으로 배포된 Azure VMware Solution 프라이빗 클라우드.

- 온-프레미스 SRM 및 가상 클라우드 어플라이언스에 대한 [DNS 확인](configure-dns-azure-vmware-solution.md).

   >[!NOTE]
   >2021년 7월 1일 이후에 만들어진 프라이빗 클라우드의 경우 프라이빗 DNS 확인을 구성할 수 있습니다. 2021년 7월 1일 이전에 만들어져 프라이빗 DNS 확인이 필요한 프라이빗 클라우드의 경우 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 열어 **프라이빗 DNS 구성** 을 요청하세요.

- 온-프레미스와 Azure VMware Solution 간의 ExpressRoute 연결 - 2Gbps.

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>시나리오: 기본 Azure VMware Solution에서 보조로

- Azure VMware Solution 프라이빗 클라우드는 기본 및 보조 지역에 배포해야 합니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="별도의 지역에 있는 두 개의 Azure VMware Solution 프라이빗 클라우드를 보여 주는 스크린샷.":::
 
- ExpressRoute Global Reach와 같은 연결은 원본과 대상 Azure VMware Solution 프라이빗 클라우드 간의 연결입니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="원본과 대상 프라이빗 클라우드 간의 연결을 보여 주는 스크린샷.":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>Azure VMware Solution에 SRM 설치

1. 온-프레미스 데이터 센터에서 VMware SRM 및 vSphere를 설치합니다.

   >[!NOTE]
   >[PSC당 하나의 vCenter Server 인스턴스가 있는 2개 사이트 토폴로지](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) 배포 모델을 사용합니다. 또한 [필요한 vSphere 복제 네트워크 포트](https://kb.VMware.com/s/article/2087769)가 열려 있는지 확인합니다.

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **추가 항목** > **재해 복구** 를 선택합니다.

   Azure VMware Solution 프라이빗 클라우드의 기본 CloudAdmin 사용자는 VMware SRM 또는 vSphere 복제를 설치할 수 있는 충분한 권한이 없습니다. 설치 프로세스에는 [필수 조건](#prerequisites) 섹션에 설명된 여러 단계가 포함됩니다. 대신 Azure VMware Solution 프라이빗 클라우드에서 추가 서비스로 vSphere 복제와 함께 VMware SRM을 설치할 수 있습니다.

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="추가 기능으로 vSphere Replication과 함께 VMware SRM을 설치하기 위한 Azure VMware Solution 프라이빗 클라우드의 스크린샷." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. **재해 복구 솔루션** 드롭다운에서 **VMware SRM(Site Recovery Manager) vSphere 복제** 를 선택합니다. 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="VMware SRM(Site Recovery Manager) - vSphere 복제가 선택된 추가 기능 아래의 재해 복구 탭을 보여 주는 스크린샷." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. 라이선스 키를 제공하고 사용 약관에 동의한 다음 **설치** 를 선택합니다.

   >[!NOTE]
   >라이선스 키를 제공하지 않으면 SRM이 평가 모드로 설치됩니다. 라이선스는 VMware SRM을 사용하도록 설정하는 데만 사용됩니다.
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="라이선스 키 필드가 선택된 추가 항목 아래의 재해 복구 탭을 보여 주는 스크린샷." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>vSphere 복제 디바이스 설치

SRM 디바이스가 성공적으로 설치되면 vSphere 복제 디바이스를 설치해야 합니다. 각 복제 서버는 최대 200개의 보호된 VM을 수용합니다. 필요에 따라 스케일 인 또는 스케일 아웃합니다. 

1. **복제 방법** 드롭다운의 **재해 복구** 탭에서 **vSphere 복제** 를 선택합니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="다음을 사용하여 복제 옵션에 대해 선택한 vSphere 복제를 보여 주는 스크린샷.":::

1. vSphere 서버 슬라이더를 이동하여 보호할 VM 수에 따라 원하는 복제 서버 수를 나타냅니다. **설치** 를 선택합니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="복제 서버 수를 늘리거나 줄이는 방법을 보여 주는 스크린샷.":::

1. 설치가 끝나면 SRM과 vSphere 복제 디바이스가 모두 설치되어 있는지 확인합니다.

   >[!TIP]
   >제거 단추는 SRM과 vSphere 복제 디바이스가 모두 현재 설치되어 있음을 나타냅니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="SRM과 복제 어플라이언스가 모두 설치되어 있음을 보여 주는 스크린샷.":::
  

## <a name="configure-site-pairing-in-vcenter"></a>vCenter에서 사이트 페어링 구성

VMware SRM 및 vSphere 복제를 설치한 후 vCenter에서 구성 및 사이트 페어링을 완료해야 합니다.

1. vCenter에 cloudadmin@vsphere.local로 로그인합니다.

1. **Site Recovery** 로 이동하여 vSphere 복제와 VMware SRM의 상태를 모두 확인한 다음 **사이트 복구 열기** 를 선택하여 클라이언트를 시작합니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="vSphere 복제 및 Site Recovery Manager 설치 상태가 정상인 vSphere 클라이언트를 보여 주는 스크린샷." border="true":::


1. 열리는 새 탭의 SR(Site Recovery) 클라이언트에서 **새 사이트 쌍** 을 선택합니다.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="Site Recovery에 대해 새 사이트 쌍 단추가 선택된 vSphere 클라이언트를 보여 주는 스크린샷." border="true":::

1. 원격 사이트 세부 정보를 입력하고 **다음** 을 선택합니다.

   >[!NOTE]
   >Azure VMware Solution 프라이빗 클라우드는 내장된 PSC(Platform Services Controller)와 함께 작동하므로 하나의 로컬 vCenter만 선택할 수 있습니다. 원격 vCenter가 내장형 PSC(Platform Service Controller)를 사용하는 경우 vCenter의 FQDN(또는 해당 IP 주소) 및 포트를 사용하여 PSC를 지정합니다. 
   >
   >원격 사용자는 페어링을 수행할 수 있는 충분한 권한이 있어야 합니다. 이를 확인하는 쉬운 방법은 해당 사용자에게 원격 vCenter에서 VRM 관리자 및 SRM 관리자 역할을 부여하는 것입니다. 원격 Azure VMware Solution 프라이빗 클라우드의 경우 cloudadmin은 해당 역할로 구성됩니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="새 사이트 쌍에 대한 사이트 세부 정보를 보여 주는 스크린샷." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. 원격 vCenter에 대한 인증서를 수락하려면 **연결** 을 선택합니다.

   이 시점에서 클라이언트는 페어링할 서비스로 양쪽에서 VRM 및 SRM 어플라이언스를 검색해야 합니다.

1. 페어링할 어플라이언스를 선택한 후 **다음** 을 선택합니다.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="새 사이트 쌍에 대한 vCenter Server 및 서비스 세부 정보를 보여 주는 스크린샷." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. 원격 VMware SRM 및 원격 vCenter에 대한 인증서를 수락하려면 **연결** 을 선택합니다(다시).

1. 로컬 VMware SRM 및 로컬 vCenter에 대한 인증서를 수락하려면 **연결** 을 선택합니다.

1. 설정을 검토한 다음 **마침** 을 선택합니다.

   성공하면 클라이언트는 페어링을 위한 다른 패널을 표시합니다. 그러나 실패하면 경보가 보고됩니다.

1. 하단의 오른쪽 모서리에서 이중 위쪽 화살표를 선택하여 패널을 확장함으로써 **최근 작업** 및 **경보** 를 표시합니다.

   >[!NOTE]
   >SR 클라이언트를 새로 고치는 데 시간이 오래 걸리는 경우가 있습니다. 작업이 너무 오래 걸리거나 "멈춤"으로 나타나면 메뉴 모음에서 새로 고침 아이콘을 선택합니다. 

1. **세부 정보 보기** 를 선택하여 원격 사이트 페어링 패널을 열면 원격 vCenter에 로그인할 수 있는 대화 상자가 열립니다.

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="Site Recovery Manager 및 vSphere 복제의 새 사이트 쌍 세부 정보를 보여 주는 스크린샷." border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. 복제 및 사이트 복구를 수행할 수 있는 충분한 권한이 있는 사용자 이름을 입력한 다음 **로그인** 을 선택합니다. 

   페어링의 경우 종종 다른 사용자인 로그인은 페어링을 설정하기 위한 일회성 작업입니다. SR 클라이언트는 페어링 작업을 위해 클라이언트가 시작될 때마다 로그인이 필요합니다.

   >[!NOTE] 
   >충분한 권한이 있는 사용자에게는 원격 vCenter에서 부여된 **VRM 관리자** 및 **SRM 관리자** 역할이 있어야 합니다. 사용자는 폴더 및 데이터 저장소와 같은 원격 vCenter 인벤토리에도 액세스할 수 있어야 합니다. 원격 Azure VMware Solution 프라이빗 클라우드의 경우 cloudadmin 사용자는 적절한 권한과 액세스 권한을 갖습니다. 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="vCenter Server 자격 증명을 보여 주는 스크린샷." border="true":::

   로컬 VRM에 포함된 VRS가 실행되고 있지 않음을 나타내는 경고 메시지가 표시됩니다.  이는 Azure VMware Solution이 Azure VMware Solution 프라이빗 클라우드에 포함된 VRS를 사용하지 않기 때문입니다.  대신 VRS 어플라이언스를 사용합니다. 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="Site Recovery Manager 및 vSphere 복제에 대한 사이트 쌍 요약을 보여 주는 스크린샷." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>SRM 보호, 재보호 및 장애 복구(failback)

사이트 쌍을 만든 후 Azure Portal에서 VM을 엔드투엔드 보호하려면 아래에 언급된 VMware 설명서를 따르세요.

- [Site Recovery Manager와 함께 vSphere 복제 사용(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [어레이 기반 복제 보호 그룹 및 vSphere 복제 보호 그룹에 대한 인벤토리 매핑(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [자리 표시자 가상 머신 정보(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [vSphere 복제 보호 그룹(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [복구 계획 만들기, 테스트 및 실행(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [복구 계획 구성(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [가상 머신의 IP 속성 사용자 지정(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [Site Recovery Manager가 vSphere 복제를 사용하여 가상 머신을 다시 보호하는 방법(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [장애 복구(failback) 수행(vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)



## <a name="ongoing-management-of-your-srm-solution"></a>SRM 솔루션의 지속적인 관리

Microsoft는 Azure VMware Solution 프라이빗 클라우드에서 VMware SRM 및 vSphere 복제 설치를 단순화하는 것을 목표로 하고 있지만 사용자는 재해 복구 솔루션의 라이선스와 일상적인 운영을 관리해야 합니다. 

## <a name="scale-limitations"></a>규모 제한

규모 제한은 프라이빗 클라우드별로 적용됩니다.

| 구성 | 제한 |
| --- | --- |
| 보호된 가상 머신 수  | 1000  |
| 복구 계획당 가상 머신 수  | 1000  |
| 복구 계획당 보호 그룹 수  | 250  |
| RPO 값  | 5분 이상*  |
| 보호 그룹당 총 가상 머신 수  | 500  |
| 총 복구 계획 수  | 250  |

\* 15분 미만의 RPO(복구 지점 목표)에 대한 자세한 내용은 _vSphere 복제 관리 가이드_ 에서 [5분 복구 지점 목표의 작동 방식](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html)을 참조하세요.



## <a name="srm-licenses"></a>SRM 라이선스

평가판 라이선스 또는 프로덕션 라이선스를 사용하여 VMware SRM을 설치할 수 있습니다.  평가판 라이선스는 60일 동안 유효합니다. 평가 기간이 끝나면 VMware SRM의 프로덕션 라이선스를 취득해야 합니다. 

Azure VMware Solution 프라이빗 클라우드에 기존 온-프레미스 VMware SRM 라이선스를 사용할 수 없습니다. 영업 팀 및 VMware와 협력하여 VMware SRM의 새로운 기간 프로덕션 라이선스를 획득합니다. 

SRM의 프로덕션 라이선스를 획득하면 Azure VMware Solution 포털을 사용하여 새 프로덕션 라이선스로 SRM을 업데이트할 수 있습니다. 


## <a name="uninstall-srm"></a>SRM 제거 

더 이상 SRM이 필요하지 않으면 깨끗하게 제거해야 합니다. SRM을 제거하기 전에 올바른 순서로 두 사이트에서 모든 SRM 구성을 제거해야 합니다. SRM을 제거하기 전에 모든 구성을 제거하지 않으면 자리 표시자 VM과 같은 일부 SRM 구성 요소가 Azure VMware Solution 인프라에 남아 있을 수 있습니다.

1. vSphere 클라이언트 또는 vSphere 웹 클라이언트에서 **Site Recovery** > **Site Recovery 열기** 를 선택합니다.

2. **Site Recovery** 홈 탭에서 사이트 쌍을 선택하고 **세부 정보 보기** 를 선택합니다.

3. **복구 계획** 탭을 선택하고 복구 계획을 마우스 오른쪽 단추로 클릭한 다음 **삭제** 를 선택합니다.

   >[!NOTE]
   >실행 중인 복구 계획은 삭제할 수 없습니다.

4. **보호 그룹** 탭을 선택하고 보호 그룹을 선택한 다음 **가상 머신** 탭을 선택합니다.

5. 모든 가상 머신을 강조 표시하고 마우스 오른쪽 단추를 클릭한 다음 **보호 제거** 를 선택합니다.

   VM에서 보호를 제거하면 복구 사이트에서 자리 표시자 VM이 삭제됩니다. 모든 보호 그룹에 대해 이 작업을 반복합니다.

6. **보호 그룹** 탭에서 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

   >[!NOTE] 
   >복구 계획에 포함된 보호 그룹은 삭제할 수 없습니다. 보호가 아직 구성되어 있는 가상 시스템이 포함된 vSphere 복제 보호 그룹은 삭제할 수 없습니다.

7. **사이트 쌍** > **구성** 을 선택하고 모든 인벤토리 매핑을 제거합니다.

   a. **네트워크 매핑**, **폴더 매핑** 및 **리소스 매핑** 탭을 각각 선택합니다.

   b. 각 탭에서 사이트를 선택하고 매핑을 마우스 오른쪽 단추로 클릭한 다음 **삭제** 를 선택합니다.

8. 두 사이트 모두에서 **자리 표시자 데이터 저장소** 를 선택하고 자리 표시자 데이터 저장소를 마우스 오른쪽 단추로 클릭한 다음 **제거** 를 선택합니다.

9. **사이트 쌍** > **요약** 을 선택하고 **사이트 쌍 중단** 을 선택합니다.

   >[!NOTE] 
   >사이트 페어링을 중단하면 원격 사이트의 Site Recovery Manager, vCenter Server 및 Platform Services Controller를 사용하여 Site Recovery Manager를 등록하는 것과 관련된 모든 정보가 제거됩니다.

10. 프라이빗 클라우드의 **관리** 에서 **추가 항목** > **재해 복구** 를 선택한 다음 **복제 어플라이언스 제거** 를 선택합니다.

11. 복제 어플라이언스가 제거되면 **재해 복구** 탭에서 **Site Recovery Manager 제거** 를 선택합니다.

12. 보조 Azure VMware Solution 사이트에서 이 단계를 반복합니다.


## <a name="support"></a>지원 

VMware SRM은 VMware의 재해 복구 솔루션입니다.  

Microsoft는 Azure VMware Solution 내에서 SRM 및 vSphere Replication Manager의 설치/제거 및 vSphere 복제 디바이스의 스케일 업/다운만 지원합니다. 

구성 및 복제와 같은 다른 모든 문제의 경우 VMware에 지원을 요청합니다.

VMware 및 Microsoft 지원 팀은 Azure VMware Solution의 SRM 문제를 해결하기 위해 필요에 따라 서로 협력합니다.


## <a name="references"></a>참조

- [VMware Site Recovery Manager 문서](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [VMware Site Recovery Manager 8.3의 호환성 매트릭스](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [VMware SRM 8.3 릴리스 정보](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [VMware vSphere 복제 문서](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [vSphere 복제 8.3의 호환성 매트릭스](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Site Recovery Manager 8.3에 대한 운영 제한](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [vSphere 복제 8.3의 작동 제한](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [vSphere 복제의 대역폭 계산](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [SRM 설치 및 구성](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [vSphere 복제 관리](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [SRM 설치를 위한 필수 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [SRM용 네트워크 포트](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [vSphere 복제용 네트워크 포트](https://kb.vmware.com/s/article/2087769)
