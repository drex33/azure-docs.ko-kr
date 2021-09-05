---
title: Azure VMware Solution용 vRealize Operations 구성
description: Azure VMware Solution 프라이빗 클라우드에 대해 vRealize Operations를 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: b4c854eefb11eaeb42153f39280d8251c2aa8476
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434015"
---
# <a name="configure-vrealize-operations-for-azure-vmware-solution"></a>Azure VMware Solution용 vRealize Operations 구성


vRealize Operations Manager는 VMware 인프라 관리자가 시스템 리소스를 모니터링할 수 있도록 하는 운영 관리 플랫폼입니다. 이러한 시스템 리소스는 애플리케이션 수준 또는 인프라 수준(실제 및 가상) 개체일 수 있습니다. 대부분의 VMware 관리자는 VMware 프라이빗 클라우드 구성 요소(vCenter, ESXi, NSX, Vrealize 및 VMware HCX)를 모니터링하고 관리하기 위해 vRealize Operations를 사용했습니다.  프로비저닝된 각 Azure VMware Solution 프라이빗 클라우드에는 전용 vCenter, NSX, vSAN 및 HCX 배포가 포함됩니다. 

[시작하기 전에](#before-you-begin) 및 [필수 구성 요소](#prerequisites)를 철저히 검토합니다. 그런 후 다음과 같은 두 가지 일반적인 배포 토폴로지를 안내합니다.

> [!div class="checklist"]
> * [Azure VMware Solution 배포를 관리하는 온-프레미스 vRealize Operations](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware Solution 배포에서 실행되는 vRealize Operations](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>시작하기 전에
* vRealize Operations를 배포하는 방법에 대한 자세한 내용은 [vrealize Operations Manager 제품 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)를 검토하세요. 
* 기본 Azure VMware Solution SDDC(소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md)를 검토합니다.
* 필요에 따라 Azure VMware Solution 배포 옵션을 관리하는 온-프레미스 vRealize Operations에 대해서는 [vRealize Operations 원격 컨트롤러](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 제품 설명서를 검토합니다. 


## <a name="prerequisites"></a>필수 구성 요소
* [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)를 설치해야 합니다.
* 온-프레미스와 Azure VMware Solution SDDC 간에 VPN 또는 Azure ExpressRoute를 구성해야 합니다.
* Azure VMware Solution 프라이빗 클라우드를 Azure에 배포해야 합니다.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware Solution 배포를 관리하는 온-프레미스 vRealize Operations
대부분의 고객에게 하나 이상의 온-프레미스 vCenter 도메인을 관리하기 위한 vRealize Operations의 기존 온-프레미스 배포가 있습니다. Azure VMware Solution 프라이빗 클라우드를 프로비저닝할 때 Azure ExpressRoute 또는 계층 3 VPN 솔루션을 사용하여 프라이빗 클라우드에 온-프레미스 환경을 연결합니다.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware Solution 배포를 관리하는 온-프레미스 vRealize Operations를 보여주는 다이어그램." border="false":::

Azure VMware Solution 프라이빗 클라우드로 vRealize Operations 기능을 확장하려면 [프라이빗 클라우드 리소스에 대한 어댑터 인스턴스](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)를 만듭니다. Azure VMware Solution 프라이빗 클라우드에서 데이터를 수집하여 온-프레미스 vRealize Operations로 가져옵니다. 온-프레미스 vRealize Operations Manager 인스턴스는 Azure VMware Solution의 vCenter 및 NSX Manager에 직접 연결할 수 있습니다. 필요에 따라 Azure VMware Solution 프라이빗 클라우드에 vRealize Operations 원격 수집기를 배포할 수 있습니다. 이 수집기는 프라이빗 클라우드에서 수집된 데이터를 압축하고 암호화한 후에 ExpressRoute 또는 VPN 네트워크를 통해 온-프레미스에서 실행되는 vRealize Operations Manager으로 전송합니다. 

> [!TIP]
> VRealize Operations Manager를 설치하는 단계별 가이드는 [VMware 설명서](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)를 참조하세요. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware Solution 배포에서 실행되는 vRealize Operations

또 다른 옵션은 프라이빗 클라우드의 vSphere 클러스터에 vRealize Operations Manager 인스턴스를 배포하는 것입니다. 

>[!IMPORTANT]
>이 옵션은 현재 VMware에서 지원되지 않습니다.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware Solution에서 실행되는 vRealize Operations를 보여주는 다이어그램." border="false":::

인스턴스가 배포된 후 vCenter, ESXi, NSX-T, vSAN 및 HCX에서 데이터를 수집하도록 vRealize Operations를 구성할 수 있습니다. 



## <a name="known-limitations"></a>알려진 제한 사항

- Azure VMware Solution의 **cloudadmin \@vsphere.local** 사용자에게는 [제한된 권한](concepts-identity.md)이 있습니다.  Azure VMware Solution의 VM(가상 머신)은 VMware 도구를 사용하는 게스트 내 메모리 수집을 지원하지 않습니다.  이 경우 활성 및 사용된 메모리 사용률은 계속 작동합니다.
- Azure VMware Solution은 DRS 설정을 포함하는 클러스터 구성을 관리하므로 호스트 기반 비즈니스 의도에 대한 워크로드 최적화는 작동하지 않습니다.
- 클러스터 기반 비즈니스 의도를 사용하는 SDDC 내에서 클러스터 간 배치에 대한 워크로드 최적화는 vRealize Operations Manager 8.0 이상부터 완벽하게 지원됩니다. 그러나 워크로드 최적화는 리소스 풀을 인식하지 않으며 클러스터 수준에서 VM을 배치합니다. 사용자는 Azure VMware Solution vCenter Server 인터페이스에서 이 문제를 수동으로 수정할 수 있습니다.
- Azure VMware Solution vCenter Server 자격 증명을 사용하여 vRealize Operations Manager에 로그인할 수 없습니다. 
- Azure VMware Solution은 vRealize Operations Manager 플러그 인을 지원하지 않습니다.

vCenter Server 클라우드 계정을 사용하여 Azure VMware Solution vCenter를 vRealize Operations Manager에 연결하면 다음과 같은 경고가 표시됩니다.

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="어댑터 인스턴스가 성공적으로 생성되었음을 알리는 경고 메시지를 보여주는 스크린샷.":::

이 경고는 Azure VMware Solution의 **cloudadmin \@vsphere.local** 사용자에게 등록에 필요한 모든 vCenter Server 작업을 수행할 수 있는 충분한 권한이 있지는 않으므로 발생합니다. 그러나 아래와 같이 어댑터 인스턴스에서 데이터를 수집하기 위한 권한은 충분합니다.

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="데이터를 수집하는 어댑터 인스턴스를 보여주는 스크린샷.":::

자세한 내용은 [vCenter 어댑터 인스턴스를 구성하는 데 필요한 권한](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)을 참조하세요.

<!-- LINKS - external -->


<!-- LINKS - internal -->




