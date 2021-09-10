---
title: 지역간 Azure VMware Solution 리소스 이동
description: 이 문서에서는 Azure VMware Solution 리소스를 Azure 지역 간에 이동하는 방법을 설명합니다.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 116350ccf32c18e232f22788d5dd3d693d7e1e9d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538719"
---
# <a name="move-azure-vmware-solution-resources-to-another-region"></a>Azure VMware Solution 리소스를 다른 지역으로 이동

>[!IMPORTANT]
>이 문서의 단계는 한 지역의 Azure VMware Solution(원본)을 다른 지역의 Azure VMware Solution(대상)으로 이동하는 용도로 제한됩니다. 

여러 가지 이유로 Azure VMware Solution 리소스를 다른 지역으로 이동할 수 있습니다. 예를 들어, 특정 지역에서만 사용할 수 있는 기능이나 서비스를 배포하거나, 정책 및 거버넌스 요구사항을 충족하거나, 용량 계획 요구사항에 대응합니다. 

이 문서를 사용하여 Azure VMware Solution을 계획하고 Azure 지역 A에서 Azure 지역 B로 마이그레이션하는 것과 같이 Azure 지역 간에 마이그레이션할 수 있습니다.


이 다이어그램에서는 두 Azure VMware Solution 환경 간에 권장되는 ExpressRoute 연결을 보여 줍니다.  두 환경 간에 HCX 사이트 페어링과 서비스 메시가 생성됩니다.  두 환경 간에 HCX 마이그레이션 트래픽과 계층 2 확장이 이동합니다(빨간색 선으로 표시됨). VMware 권장 HCX 계획에 관해서는 [HCX 마이그레이션 계획](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section1)을 참조하세요.

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-2.png" alt-text="원본 및 대상 Azure VMware Solution 환경 간 ExpressRoute Global Reach 통신을 보여 주는 다이어그램" border="false":::

>[!NOTE]
>트래픽이 프라이빗 클라우드(원본과 대상) 간에 이동하므로 모든 워크플로를 온-프레미스로 다시 마이그레이션할 필요는 없습니다.
>
>**Azure VMware Solution 프라이빗 클라우드(원본) > ExpressRoute 게이트웨이(원본) > ExpressRoute 게이트웨이(대상) > Azure VMware Solution 프라이빗 클라우드(대상)**

이 다이어그램에서는 두 Azure VMware Solution 환경 간 연결을 보여 줍니다. 

:::image type="content" source="media/move-across-regions/move-ea-csp-across-regions-connectivity-diagram.png" alt-text="원본 Azure VMware Solution 환경과 대상 Azure VMware Solution 환경 간 통신을 보여 주는 다이어그램" border="false":::


이 문서에서는 다음과 같은 단계를 안내합니다. 

> [!div class="checklist"]
> * 다른 Azure 지역으로 이동 준비 및 계획
> * 두 Azure VMware Solution 프라이빗 클라우드 간 네트워크 연결 설정
> * Azure VMware Solution 원본 환경에서 구성 내보내기
> * Azure VMware Solution 대상 환경에 지원되는 구성 요소 다시 적용
> * VMware HCX를 사용하여 워크로드 마이그레이션

## <a name="prerequisites"></a>필수 구성 요소

- 마이그레이션 문제(있는 경우)를 방지하기 위해 [VMware HCX 어플라이언스가 최신 패치로 업그레이드됩니다](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-02DB88E1-EC81-434B-9AE9-D100E427B31C.html).

- 원본의 로컬 콘텐츠 라이브러리가 [게시된 콘텐츠 라이브러리](https://docs.vmware.com/en/VMware-Validated-Design/services/deployment-of-vrealize-suite-2019-on-vmware-cloud-foundation-310/GUID-59E0CBA1-2CF6-488D-AA58-C97C76FD8159.html?hWord=N4IghgNiBcIA4FcBGECWBnAFgAgMYHsA7AFwFMTs0kAnMagTxAF8g)입니다.

## <a name="prepare"></a>준비

다음 단계에서는 다른 Azure VMware Solution 프라이빗 클라우드로 이동할 Azure VMware Solution 프라이빗 클라우드를 준비하는 방법을 보여 줍니다. 

### <a name="export-the-source-configuration"></a>원본 구성 내보내기

1. 원본에서 [확장 세그먼트, 방화벽 규칙, 포트 세부 정보, 경로 테이블을 내보냅니다](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-FCE6567E-1174-49CC-90F1-BA7B695B28F0.html).

1. [인벤토리 목록 보기의 콘텐츠를 CSV 파일로 내보냅니다](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.vcenterhost.doc/GUID-C0E8DD52-677E-464F-A3EA-044EE20B7B92.html).

1. [워크로드를 마이그레이션 그룹(마이그레이션 웨이브)으로 정렬합니다](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1).


### <a name="deploy-the-target-environment"></a>대상 환경 배포

원본 구성을 이동하려면 먼저 [대상 환경을 배포](plan-private-cloud-deployment.md)해야 합니다.


### <a name="back-up-the-source-configuration"></a>원본 구성 백업

VC, NSX-T, 방화벽 정책과 규칙을 포함하는 Azure VMware Solution(원본) 구성을 백업합니다. 

- **컴퓨팅:** 기존 인벤토리 구성을 내보냅니다. 인벤토리 백업의 경우 RVtool(오픈 소스 앱)을 사용할 수 있습니다.

- **네트워크 및 방화벽 정책과 규칙:** Azure VMware Solution 대상에서 원본 환경과 동일한 네트워크 세그먼트를 만듭니다.

Azure VMware Solution은 모든 백업 솔루션을 지원합니다. 설치, 데이터 백업, 백업 복원에 대한 CloudAdmin 권한이 필요합니다. 자세한 내용은 [Azure VMware Solution VM에 대한 백업 솔루션](ecosystem-back-up-vms.md)을 참조하세요.

- Commvault 솔루션을 사용하는 VM 워크로드 백업:

   - Azure VMware Solution vCenter의 명령 센터에서 [VMware 클라이언트를 만듭니다](https://documentation.commvault.com/commvault/v11_sp20/article?p=119380.htm).

   - 백업에 필요한 VM을 사용하여 [VM 그룹을 만듭니다](https://documentation.commvault.com/commvault/v11_sp20/article?p=121182.htm).

   - [VM 그룹에서 백업을 실행합니다](https://documentation.commvault.com/commvault/v11_sp20/article?p=121657.htm).

   - [VM을 복원합니다](https://documentation.commvault.com/commvault/v11_sp20/article?p=87275.htm).

- [Veritas NetBackup 솔루션](https://vrt.as/nb4avs)을 사용하는 VM 워크로드 백업. 

>[!TIP]
>[Azure Resource Mover](../resource-mover/move-region-within-resource-group.md?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)를 사용하면 Azure VMware Solution을 사용하는 지역 간에 이동하도록 지원되는 리소스 목록을 확인하고 마이그레이션할 수 있습니다.

### <a name="locate-the-source-expressroute-circuit-id"></a>원본 ExpressRoute 회로 ID 찾기

1. 원본에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **관리** > **연결** > **ExpressRoute** 를 선택합니다.

3. 원본의 **ExpressRoute ID** 를 복사합니다.  프라이빗 클라우드 간에 피어링하는 데 필요합니다.


### <a name="create-the-targets-authorization-key"></a>대상의 인증 키 만들기

1. 대상에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **관리** > **연결** > **ExpressRoute** 를 선택한 다음, **+ 권한 부여 키 요청** 을 선택합니다.

   :::image type="content" source="media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute 권한 부여 키를 요청하는 방법을 보여주는 스크린샷." border="true" lightbox="media/expressroute-global-reach/start-request-authorization-key.png":::

1. 이름을 입력하고 **만들기** 를 선택합니다.

   키를 만드는 데 30초 정도 걸릴 수 있습니다. 만들기가 완료되면 프라이빗 클라우드의 권한 부여 키 목록에 새 키가 표시됩니다.

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Retach 권한 부여 키를 보여주는 스크린샷.":::
  
1. 권한 부여 키와 ExpressRoute ID를 복사합니다. 피어링을 완료하는 데 필요합니다. 권한 부여 키는 일정 시간 후에 사라지기 때문에 표시되는 즉시 복사합니다.

### <a name="peer-between-private-clouds"></a>프라이빗 클라우드 간에 피어링

이제 두 환경에 대해 모두 ExpressRoute 회로 ID와 인증 키가 있으므로 원본을 대상에 피어링할 수 있습니다. 프라이빗 클라우드 ExpressRoute 회로의 리소스 ID와 인증 키를 사용하여 피어링을 완료합니다.
 
1. 대상에서 원본의 ExpressRoute 회로와 동일한 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 관리에서 **연결** > **ExpressRoute Global Reach** > **추가** 를 선택합니다.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution 프라이빗 클라우드의 ExpressRoute Global Reach 탭을 보여주는 스크린샷.":::

1. 이전 단계에서 만든 ExpressRoute 회로 ID와 대상 인증 키를 붙여넣습니다.  **만들기** 를 선택합니다.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="연결 정보를 입력하는 대화 상자를 보여주는 스크린샷":::   

### <a name="create-a-site-pairing-between-private-clouds"></a>프라이빗 클라우드 간에 사이트 페어링 만들기

연결을 설정한 후 VM을 쉽게 마이그레이션할 수 있도록 프라이빗 클라우드 간에 VMware HCX 사이트 페어링을 만듭니다. Azure VMware Solution의 VMware HCX Cloud Manager를 데이터 센터의 VMware HCX Connector와 연결하거나 페어링할 수 있습니다. 

1. 원본의 vCenter에 로그인하고 **홈** 에서 **HCX** 를 선택합니다.

1. **인프라** 에서 **사이트 페어링** 을 선택하고 **원격 사이트에 연결** 옵션(화면 가운데)을 선택합니다. 

1. 앞에서 기록해 둔 Azure VMware Solution HCX Cloud Manager URL이나 IP 주소(`https://x.x.x.9`), Azure VMware Solution cloudadmin\@vsphere.local 사용자 이름과 암호를 입력합니다. 그런 다음 **연결** 을 선택합니다.

   > [!NOTE]
   > 사이트 쌍을 성공적으로 설정하려면 다음을 수행합니다.
   > * VMware HCX Connector는 443 포트를 통해 HCX Cloud Manager IP로 라우팅할 수 있어야 합니다.
   >
   > * vCenter에 로그인할 때 사용한 것과 동일한 암호를 사용합니다. 초기 배포 화면에서 이 암호를 정의했습니다.

   Azure VMware Solution의 VMware HCX Cloud Manager와 온-프레미스 VMware HCX Connector가 연결(페어링)된 것을 보여주는 화면이 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution의 HCX Manager와 VMware HCX Connector의 페어링을 보여주는 스크린샷.":::

### <a name="create-a-service-mesh-between-private-clouds"></a>프라이빗 클라우드 간에 서비스 메시 만들기

> [!NOTE]
> Azure VMware Solution을 사용하여 서비스 메시를 성공적으로 설정하려면 다음을 수행합니다.
>
> * 포트 UDP 500/4500은 온-프레미스 VMware HCX Connector '업링크' 네트워크 프로필 주소와 Azure VMware Solution HCX Cloud '업링크' 네트워크 프로필 주소 사이에서 열립니다.
>
> * [VMware HCX 필수 포트](https://ports.vmware.com/home/VMware-HCX)를 검토하세요.

1. **인프라** 에서 **상호 연결** > **서비스 메시** > **서비스 메시 만들기** 를 선택합니다.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="서비스 메시 만들기를 시작하기 위한 선택 항목의 스크린샷" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 미리 채워진 사이트를 검토한 다음, **계속** 을 선택합니다. 

   > [!NOTE]
   > 이번이 첫 번째 서비스 메시 구성인 경우 이 화면을 수정할 필요가 없습니다.  

1. 드롭다운 목록에서 원본 및 원격 컴퓨팅 프로필을 선택한 다음, **계속** 을 선택합니다.  

   선택하는 항목에 따라 VM이 VMware HCX 서비스를 사용할 수 있는 리소스가 정의됩니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="원본 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="원격 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 사용할 서비스를 검토한 다음, **계속** 을 선택합니다.  

1. **고급 구성 - 업링크 네트워크 프로필 재정의** 에서 **계속** 을 선택합니다.  

   업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크를 통해 연결됩니다.  
  
1. **고급 구성 - 네트워크 확장 어플라이언스 스케일 아웃** 을 검토하고 **계속** 을 선택합니다. 

   어플라이언스당 최대 8개의 VLAN을 가질 수 있지만 다른 어플라이언스를 배포하여 8개의 VLAN을 추가할 수 있습니다. 또한 추가 어플라이언스의 계정을 고려하여 IP 공간이 있어야 하며 어플라이언스당 IP 하나입니다.  자세한 내용은 [VMware HCX 구성 제한](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)을 참조하세요.
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN 수를 늘릴 위치를 보여주는 스크린샷." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **고급 구성 - 트래픽 엔지니어링** 을 검토하고 필요한 대로 수정한 다음, **계속** 를 선택합니다.

1. 토폴로지 미리 보기를 검토하고 **계속** 을 선택합니다.

1. 이 서비스 메시에 사용할 사용자에게 친숙한 이름을 입력하고 **완료** 를 선택하여 마칩니다.  

1. **작업 보기** 를 선택하여 배포를 모니터링합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="작업을 살펴보는 단추를 보여주는 스크린샷.":::

   서비스 메시 배포가 성공적으로 완료되면 서비스가 녹색으로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="서비스의 녹색 표시기를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 어플라이언스 상태를 검사하여 서비스 메시의 상태를 확인합니다. 

1. **상호 연결** > **어플라이언스** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="어플라이언스의 상태를 확인하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

## <a name="move"></a>이동

다음 단계에서는 다른 지역의 다른 Azure VMware Solution 프라이빗 클라우드로 Azure VMware Solution 프라이빗 클라우드 리소스를 이동하는 방법을 보여 줍니다. 

이 섹션에서는 다음을 마이그레이션합니다.

- 리소스 풀 구성 및 폴더 만들기

- VM 템플릿 및 연결된 태그

- 원본 포트 그룹과 연결된 VLAN을 기반으로 하는 논리 세그먼트 배포 

- 네트워크 보안 서비스 및 그룹

- 원본 방화벽 정책을 기반으로 하는 게이트웨이 방화벽 정책 및 규칙

### <a name="migrate-the-source-vsphere-configuration"></a>원본 vSphere 구성 마이그레이션

이 단계에서는 원본 vSphere 구성을 복사하고 대상 환경으로 이동합니다. 

1. 원본 vCenter에서 동일한 리소스 풀 구성을 사용하고 대상 vCenter에 [동일한 리소스 풀 구성을 만듭니다](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html#example-creating-resource-pools-4).

2. 원본 vCenter에서 동일한 VM 폴더 이름을 사용하고 **폴더** 에서 대상 vCenter에 [동일한 VM 폴더를 만듭니다](https://docs.vmware.com/en/VMware-Validated-Design/6.1/sddc-deployment-of-cloud-operations-and-automation-in-the-first-region/GUID-9D935BBC-1228-4F9D-A61D-B86C504E469C.html).

3. VMware HCX를 사용하여 원본 vCenter의 모든 VM 템플릿을 대상 vCenter로 마이그레이션합니다.

   1. 원본에서 기존 템플릿을 VM으로 변환한 다음, 대상으로 마이그레이션합니다.

   2. 대상에서 VM을 VM 템플릿으로 변환합니다.

4. 원본 환경에서 동일한 VM 태그 이름을 사용하고 [대상 vCenter에 동일한 VM 태그를 만듭니다](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vcenterhost.doc/GUID-05323758-1EBF-406F-99B6-B1A33E893453.html). 

5. 원본 vCenter 콘텐츠 라이브러리에서 구독한 라이브러리 옵션을 사용하여 ISO, OVF, OVA, VM 템플릿을 대상 콘텐츠 라이브러리에 복사합니다.

   1. 콘텐츠 라이브러리가 아직 게시되지 않은 경우 **게시 사용** 옵션을 선택합니다.

   2. 원본 콘텐츠 라이브러리에서 게시된 라이브러리의 URL을 복사합니다.

   3. 대상에서 원본 라이브러리의 URL을 사용하여 [구독한 콘텐츠 라이브러리를 만듭니다](deploy-vm-content-library.md).

   4. **지금 동기화** 를 선택합니다.


### <a name="configure-the-target-nsx-t-environment"></a>대상 NSX-T 환경 구성

이 단계에서는 원본 NSX-T 구성을 사용하여 대상 NSX-T 환경을 구성합니다.

>[!NOTE]
>원본 NSX-T에 여러 기능이 구성되어 있으므로 원본 NXS-T에서 복사하거나 읽고 대상 프라이빗 클라우드에서 다시 만들어야 합니다. IP 변경과 관련 구성으로 인한 가동 중지 시간을 방지하려면 L2 확장을 사용하여 원본을 대상 AVS 프라이빗 클라우드로 마이그레이션하는 동안 VM의 IP 주소와 Mac 주소를 동일하게 유지합니다.

1. 기본 계층 1 게이트웨이 아래 대상 환경에서 필요한 [NSX 네트워크 구성 요소를 구성](tutorial-nsx-t-network-segment.md)합니다.

1. [보안 그룹 구성을 만듭니다](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [분산형 방화벽 정책과 규칙을 만듭니다](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-41CC06DF-1CD4-4233-B43E-492A9A3AD5F6.html).

1. [게이트웨이 방화벽 정책과 규칙을 만듭니다](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-DE6FE8CB-017E-41C8-85FC-D71CF27F85C2.html).

1. [DHCP 서버나 DHCP 릴레이 서비스를 만듭니다](configure-dhcp-azure-vmware-solution.md). 

1. [포트 미러링을 구성합니다](configure-port-mirroring-azure-vmware-solution.md).

1. [DNS 전달자를 구성합니다](configure-dns-azure-vmware-solution.md).

1. [새 계층 1 게이트웨이(기본값 제외)를 구성합니다](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A6042263-374F-4292-892E-BC86876325A4.html).  이 구성은 원본에 구성된 NSX-T를 기반으로 합니다. 

### <a name="migrate-the-vms-from-the-source"></a>원본에서 VM 마이그레이션 

이 단계에서는 VMware HCX를 사용하여 원본에서 대상으로 VM을 마이그레이션합니다. 원본에서 계층 2 확장을 수행하고 HCX를 사용하여 최소한의 중단으로 원본에서 대상으로 VM을 vMotion하는 옵션이 있습니다. 

vMotion 외에도 대량 및 콜드 vMotion과 같은 다른 방법이 권장됩니다.  다음에 대해 자세히 알아봅니다.

- [HCX 마이그레이션 계획](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#sec4-sub1)

- [HCX를 사용하여 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-14D48C15-3D75-485B-850F-C5FCB96B5637.html)

### <a name="cutover-extended-networks"></a>확장된 네트워크 중단 

이 단계에서는 최종 게이트웨이 중단을 수행하여 확장된 네트워크를 종료합니다. 원본 Azure VMware Solution 환경에서 대상 환경으로 게이트웨이를 이동(마이그레이션)할 수도 있습니다.

>[!IMPORTANT]
>대상 Azure VMware Solution 환경으로 VLAN 워크로드 마이그레이션 후 게이트웨이 중단을 수행해야 합니다. 또한 원본 및 대상 환경에 대한 VM 종속성이 없어야 합니다.

게이트웨이 중단 전에 마이그레이션된 모든 워크로드 서비스와 성능을 확인합니다. 애플리케이션 및 웹 서비스 소유자가 성능에 동의하면(대기 시간 문제 제외) 게이트웨이 중단을 계속 진행할 수 있습니다.  중단을 완료하면 퍼블릭 DNS A 및 PTR 레코드를 수정해야 합니다. 

VMware 권장 사항은 [확장된 네트워크 중단](https://vmc.techzone.vmware.com/vmc-solutions/docs/deploy/planning-an-hcx-migration#section9)을 참조하세요.


### <a name="public-ip-dnat-for-migrated-dmz-vms"></a>마이그레이션된 DMZ VM의 공용 IP DNAT

지금까지 워크로드를 대상 환경으로 마이그레이션했습니다. 이 애플리케이션 워크로드는 퍼블릭 인터넷에서 연결할 수 있어야 합니다. 대상 환경에서는 애플리케이션을 호스트하는 두 가지 방법을 제공합니다. 애플리케이션은 다음과 같을 수 있습니다.

- 애플리케이션 게이트웨이 부하 분산 장치에서 호스트되고 게시됩니다.

- vWAN의 공용 IP 기능을 통해 게시됩니다.

공용 IP는 일반적으로 Azure Firewall로 변환되는 대상 NAT입니다. DNAT 규칙을 사용하면 방화벽 정책은 공용 IP 주소 요청을 포트를 포함한 프라이빗 주소(웹 서버)로 변환합니다. 자세한 내용은 [Azure Virtual WAN에서 공용 IP 기능을 사용하는 방법](./enable-public-internet-access.md)을 참조하세요.

>[!NOTE]
>SNAT는 Azure VMware Solution에 기본적으로 구성되어 있으므로 관리 탭 아래 Azure VMware Solution 프라이빗 클라우드 연결 설정에서 SNAT를 사용하도록 설정해야 합니다.

## <a name="decommission"></a>서비스 해제

이 마지막 단계에서는 네트워크 구성을 포함하여 모든 VM 워크로드가 성공적으로 마이그레이션되었는지 확인합니다. 종속성이 없는 경우 원본 환경에서 HCX 서비스 메시, 사이트 페어링, 네트워크 연결을 끊을 수 있습니다. 

>[!NOTE]
>프라이빗 클라우드의 서비스를 해제한 후에는 구성과 데이터가 손실되므로 실행을 취소할 수 없습니다.


## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Microsoft.AVS에 대한 이동 작업 지원](../azure-resource-manager/management/move-support-resources.md#microsoftavs)
- [네트워킹 리소스 이동 지침](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [가상 머신 이동 지침](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [App Service 리소스 이동 지침](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)
