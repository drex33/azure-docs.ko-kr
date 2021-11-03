---
title: Azure VMware Solution Zerto 재해 복구 배포(초기 가용성)
description: 온-프레미스 VMware 또는 Azure VMware Solution 가상 머신에 대한 Zerto 재해 복구를 구현하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 8f7fcd27cdde9915f2e0c9c2467576aa59a0a478
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016912"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Azure VMware Solution Zerto 재해 복구 배포(초기 가용성)

이 문서에서는 온-프레미스 VMware 또는 Azure VMware Solution 기반 VM(가상 머신)에 대한 재해 복구를 구현하는 방법을 설명합니다. 이 문서의 솔루션은 [Zerto 재해 복구 를](https://www.zerto.com/solutions/use-cases/disaster-recovery/)사용합니다. Zerto 인스턴스는 보호된 사이트와 복구 사이트 모두에 배포됩니다. 

Zerto는 재해가 발생한 경우 VM의 가동 중지 시간을 최소화하도록 설계된 재해 복구 솔루션입니다. Zerto의 플랫폼은 데이터 손실을 최소화하거나 거의 최소화할 수 있는 CDP(연속 데이터 보호)를 기반으로 합니다. 많은 중요 비즈니스용 및 중요 업무용 엔터프라이즈 애플리케이션에 대해 원하는 보호 수준을 제공합니다. 또한 Zerto는 장애 조치(failover) 및 장애 복구(failback)를 자동화하고 오케스트레이션하여 재해 발생 시 가동 중지 시간을 최소화합니다. 전반적으로 Zerto는 자동화를 통해 관리를 간소화하고 빠르고 예측 가능한 복구 시간을 보장합니다.


## <a name="core-components-of-the-zerto-platform"></a>Zerto 플랫폼의 핵심 구성 요소

| 구성 요소 | 설명 |
| --- | --- |
| **ZVM(Zerto Virtual Manager)**   | Zerto용 관리 애플리케이션은 Windows VM에 설치된 Windows 서비스로 구현됩니다. 프라이빗 클라우드 관리자는 Windows VM을 설치하고 관리합니다. ZVM은 0일차 및 2일차 재해 복구 구성을 사용하도록 설정합니다. 예를 들어 기본 및 재해 복구 사이트 구성, VM 보호, VM 복구 등이 있습니다. 그러나 보호된 고객 VM의 복제 데이터는 처리하지 않습니다.     |
| **vRA(가상 복제 어플라이언스)**   | 원본에서 복제 대상으로의 데이터 복제를 처리하는 Linux VM입니다. ESXi 호스트당 하나의 vRA 인스턴스가 설치되어 프라이빗 클라우드의 호스트와 함께 증가 및 축소되는 진정한 규모 아키텍처를 제공합니다. VRA는 보호된 VM에서 로컬 또는 원격 대상으로의 데이터 복제를 관리하여 저널의 데이터를 저장합니다.    |
| **Zerto ESXi 호스트 드라이버**   | Zerto 재해 복구를 위해 구성된 각 VMware ESXi 호스트에 설치됩니다. 호스트 드라이버는 vSphere VM의 IO를 가로채서 해당 호스트에 대해 선택한 vRA로 복제 데이터를 보냅니다. 그런 다음 vRA는 VM의 데이터를 하나 이상의 재해 복구 대상으로 복제합니다.    |
| **ZCA(Zerto Cloud Appliance)**   | Windows VM은 Zerto를 사용하여 vSphere VM을 Azure 네이티브 IaaS VM으로 복구하는 경우에만 사용됩니다. ZCA는 다음으로 구성됩니다.<ul><li>**ZVM:** UI를 호스트하고 관리 및 오케스트레이션을 위해 Azure의 네이티브 API와 통합되는 Windows 서비스입니다.</li><li>**VRA:** Azure에서 또는 Azure로 데이터를 복제하는 Windows 서비스입니다.</li></ul>ZCA는 기본적으로 배포된 플랫폼과 통합되어 Microsoft Azure 스토리지 계정 내에서 Azure Blob Storage를 사용할 수 있습니다. 따라서 이러한 각 플랫폼에서 가장 비용 효율적인 배포를 보장합니다.   |
| **VPG(가상 보호 그룹)**   | ZVM에서 만든 VM의 논리적 그룹입니다. Zerto를 사용하면 VPG에서 재해 복구, 백업 및 모바일 정책을 구성할 수 있습니다. 이 메커니즘을 사용하면 일관된 정책 집합을 VM 그룹에 적용할 수 있습니다.  |


Zerto 플랫폼 아키텍처에 대한 자세한 내용은 [Zerto 플랫폼 아키텍처 가이드를 참조하세요.](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf) 


## <a name="supported-zerto-scenarios"></a>지원되는 Zerto 시나리오

다음 세 가지 시나리오에서 Azure VMware Solution Zerto를 사용할 수 있습니다. 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>시나리오 1: 재해 복구를 Azure VMware Solution 온-프레미스 VMware

이 시나리오에서 기본 사이트는 온-프레미스 vSphere 기반 환경입니다. 재해 복구 사이트는 Azure VMware Solution 프라이빗 클라우드입니다. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Azure VMware Solution Zerto 재해 복구 솔루션에 대한 시나리오 1을 보여 주는 다이어그램":::


### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>시나리오 2: 클라우드 재해 복구를 Azure VMware Solution Azure VMware Solution

이 시나리오에서 기본 사이트는 하나의 Azure 지역에 Azure VMware Solution 프라이빗 클라우드입니다. 재해 복구 사이트는 다른 Azure 지역의 Azure VMware Solution 프라이빗 클라우드입니다.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Azure VMware Solution Zerto 재해 복구 솔루션에 대한 시나리오 2를 보여 주는 다이어그램" border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-disaster-recovery"></a>시나리오 3: IaaS VM 클라우드 재해 복구에 Azure VMware Solution

이 시나리오에서 기본 사이트는 하나의 Azure 지역에 Azure VMware Solution 프라이빗 클라우드입니다. Azure Blob 및 Azure IaaS(Hyper-V 기반) VM은 재해 발생 시 사용됩니다.

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Azure VMware Solution Zerto 재해 복구 솔루션에 대한 시나리오 3을 보여 주는 다이어그램" border="false":::



## <a name="prerequisites"></a>사전 요구 사항

### <a name="on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>재해 복구를 Azure VMware Solution 온-프레미스 VMware

- 보조 지역으로 배포된 Azure VMware Solution 프라이빗 클라우드.

- 온-프레미스와 Azure VMware Solution 간의 VPN 또는 ExpressRoute 연결



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>클라우드 재해 복구를 Azure VMware Solution Azure VMware Solution

- Azure VMware Solution 프라이빗 클라우드는 기본 및 보조 지역에 배포해야 합니다.

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="다이어그램은 Azure VMware Solution Zerto 재해 복구 솔루션의 시나리오 2에 대한 첫 번째 필수 구성 요소입니다.":::
 
- ExpressRoute Global Reach와 같은 연결은 원본과 대상 Azure VMware Solution 프라이빗 클라우드 간의 연결입니다.

### <a name="azure-vmware-solution-iaas-vms-cloud-disaster-recovery"></a>IaaS VM 클라우드 재해 복구 Azure VMware Solution

- Azure VMware Solution 재해 복구에 사용되는 vNET까지 ExpressRoute 기반 네트워크 연결.   

- 나머지 필수 구성 Enterprise [Zerto 가상 복제 Azure](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf) Enterprise 지침을 따릅니다.



## <a name="install-zerto-on-azure-vmware-solution"></a>Azure VMware Solution Zerto 설치

현재 Azure VMware Solution Zerto 재해 복구는 IA(초기 가용성) 단계에 있습니다. IA 단계에서 IA 지원을 요청하고 자격을 얻으려면 Microsoft에 문의해야 합니다.

Azure VMware Solution Zerto에 대한 IA 지원을 요청하려면 에 이메일 요청을 zertoonavs@microsoft.com 보냅니다. IA 단계에서 Azure VMware Solution Zerto의 수동 설치 및 온보딩만 지원합니다. 그러나 Microsoft는 사용자와 협력하여 프라이빗 클라우드에 Zerto를 수동으로 설치할 수 있도록 합니다.

> [!NOTE]
> 수동 설치의 일부로 Microsoft는 Zerto에 대한 새 vCenter 사용자 계정을 만듭니다. 이 사용자 계정은 Zerto Virtual Manager(ZVM)가 Azure VMware Solution vCenter에서 작업을 수행하는 데만 사용할 수 있습니다. Azure VMware Solution ZVM을 설치할 때 "Zerto vCenter 권한을 사용하여 역할 및 권한을 적용하려면 선택" 옵션을 선택하지 마세요. 


ZVM을 설치한 후 Zerto Virtual Manager **사이트 설정** 아래 옵션을 선택합니다. 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-install-5.png" alt-text="파란색 확인란에 대해 나열된 모든 옵션을 선택하는 방법을 보여 주는 워크로드 자동화 섹션의 스크린샷.":::

>[!NOTE]
>Azure VMware Solution 일반 공급은 Azure VMware Solution Zerto의 셀프 서비스 설치 및 2일차 작업을 가능하게 합니다.


## <a name="configure-zerto-for-disaster-recovery"></a>재해 복구를 위한 Zerto 구성

온-프레미스 VMware가 재해 복구를 Azure VMware Solution 클라우드 재해 복구 시나리오를 Azure VMware Solution Azure VMware Solution [Zerto 가상 관리자 관리 가이드 vSphere 환경을](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409)참조하세요.


자세한 내용은 [Zerto 기술 설명서를 참조하세요.](https://www.zerto.com/myzerto/technical-documentation/) 또는 Zerto [소프트웨어 PDF용 v8.5 검색 도구 설명서 번들의 모든 Zerto](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409)가이드 부분을 다운로드할 수 있습니다.



## <a name="ongoing-management-of-zerto"></a>Zerto의 지속적인 관리

- Azure VMware Solution 프라이빗 클라우드 작업을 확장할 때 Zerto 보호를 위해 새 Azure VMware Solution 호스트를 추가하거나 새 Azure VMware Solution vSphere 클러스터에 Zerto 재해 복구를 구성해야 할 수 있습니다. 이러한 두 시나리오에서는 초기 가용성 단계에서 Azure VMware Solution 팀과 함께 지원 요청을 열어야 합니다. 이러한 2일차 구성에 대한 Azure Portal [지원 티켓을](https://rc.portal.azure.com/#create/Microsoft.Support) 열 수 있습니다. 

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Zerto 재해 복구 구성 2일차에 대한 지원 요청을 보여주는 스크린샷":::

- GA 단계에서 위의 모든 작업은 자동화된 셀프 서비스 방식으로 사용하도록 설정됩니다.


## <a name="faqs"></a>FAQ

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Azure VMware Solution 기존 Zerto 제품 라이선스를 사용할 수 있나요?

Azure VMware Solution 환경에 기존 Zerto 제품 라이선스를 다시 사용할 수 있습니다. 새 Zerto 라이선스가 필요한 경우 에서 Zerto에 이메일을 **info@zerto.com** 보내 새 라이선스를 획득합니다. 

### <a name="how-is-zerto-supported"></a>Zerto는 어떻게 지원하나요?

Zerto 재해 복구는 Zerto에서 판매되고 지원되는 솔루션입니다. Zerto 재해 복구와 관련된 지원 문제는 항상 [Zerto 지원 에](https://www.zerto.com/company/support-and-service/support/)문의합니다.

Zerto 및 Microsoft 지원 팀은 필요에 따라 서로 참여하여 Azure VMware Solution Zerto 재해 복구 문제를 해결합니다.

