---
title: Azure 네트워크 기능 관리자 정보
description: Azure Portal을 사용하는 일관된 하이브리드 환경을 위해 Azure Stack Edge Pro with GPU에서 네트워크 함수를 배포하고 프로비저닝할 수 있는 완전 관리형 클라우드 네이티브 오케스트레이션 서비스인 Azure Network Function Manager에 관해 알아봅니다.
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55ea5ebe1dbeeb0d3e1d17c2085c19d471396b21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024110"
---
# <a name="what-is-azure-network-function-manager"></a>Azure 네트워크 기능 관리자란?

Azure Network Function Manager는 모바일 패킷 코어, SD-WAN 에지, VPN 서비스 등의 네트워크 기능을 온-프레미스 환경에서 실행되는 [Azure Stack Edge 디바이스](https://azure.microsoft.com/products/azure-stack/edge/)에 배포하기 위한 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 환경을 제공합니다. 이제 Azure 관리 포털에서 직접 에지 디바이스에 개인 모바일 네트워크 서비스나 SD-WAN 솔루션을 신속하게 배포할 수 있습니다. Network Function Manager는 [파트너](#partners)의 성장하는 에코시스템에서 네트워크 기능을 가져옵니다. Network Function Manager는 [Azure Stack Edge Pro with GPU](../databox-online/azure-stack-edge-gpu-overview.md)에서 지원됩니다.

## <a name="features"></a><a name="features"></a> 기능

* **일관된 관리 환경 –** Network Function Manager는 엔터프라이즈 에지에 배포된 다양한 파트너의 네트워크 기능을 위한 일관된 Azure 관리 환경을 제공합니다. 이를 통해 거버넌스와 관리를 간소화할 수 있습니다. 친숙한 Azure 도구와 SDK를 사용하여 선언적 템플릿을 통해 네트워크 기능 배포를 자동화할 수 있습니다. Azure Stack Edge 디바이스에서 네트워크 기능을 전역으로 배포하기 위해 Azure 역할 기반 액세스 제어인 [Azure RBAC](../role-based-access-control/overview.md)를 적용할 수도 있습니다.

* **5G 네트워크 기능의 Azure Marketplace 환경 –** Azure Marketplace에서 직접 LTE 및 5G 모바일 패킷 코어 네트워크 기능 중에서 선택하여 Azure Stack Edge 디바이스에서 개인 모바일 네트워크 솔루션의 배포 시간을 단축합니다.

* **SD-WAN 및 VPN 솔루션의 원활한 클라우드-에지 환경 –** Network Function Manager는 퍼블릭 클라우드에서 친숙한 Marketplace 네트워크 기능의 Azure 관리 환경을 에지 디바이스로 확장합니다. 이렇게 하면 Azure 퍼블릭 클라우드나 Azure Stack Edge 디바이스에 배포된 선택한 SD-WAN 및 VPN 파트너 네트워크 기능의 일관된 배포 환경을 활용할 수 있습니다.

* **엔터프라이즈 에지에 있는 네트워크 기능의 Azure Managed Applications 환경 –** Network Function Manager를 사용하면 Azure Stack Edge 디바이스에서 모바일 패킷 코어와 같은 특수화된 네트워크 기능의 배포 환경을 간소화할 수 있습니다. 파트너가 제공하는 승인된 네트워크 기능 이미지의 수명 주기 관리에 대한 유효성을 미리 검사했습니다. 네트워크 기능 리소스가 전체 환경에서 일관된 상태로 배포된다는 것을 확신할 수 있습니다. 자세한 내용은 [Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md)를 참조하세요.

* **네트워크 기능에 대한 동적 또는 고정 IP 할당의 네트워크 가속화 및 선택 –** Network Function Manager와 [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md)는 네트워크 기능 워크로드의 향상된 네트워크 성능을 지원합니다. 모바일 패킷 코어와 같은 특수화된 네트워크 기능은 이제 액세스 지점 네트워크와 사용자 평면 네트워크에서 더 빠른 데이터 경로 성능을 제공하는 Azure Stack Edge 디바이스에 배포할 수 있습니다. 네트워크 기능 배포를 위해 서로 다른 가상 인터페이스에 대해 고정 또는 동적 IP 할당 중에서 선택할 수도 있습니다. 이 네트워킹 기능에 대한 지원은 네트워크 기능 파트너에 문의하세요.  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>네트워크 기능을 위한 Azure Managed Applications

Network Function Manager를 사용하여 배포할 수 있는 네트워크 기능은 Azure Stack Edge 디바이스에서 특별히 실행하도록 엔지니어링됩니다. 네트워크 기능 제품은 Azure Marketplace에 [Azure Managed Application](../azure-resource-manager/managed-applications/overview.md)으로 게시됩니다. 고객은 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)에서 직접 배포하거나 Azure Portal을 통해 Network Function Manager 디바이스 리소스에서 배포할 수 있습니다. 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="관리형 애플리케이션 워크플로의 다이어그램":::

Network Function Manager를 사용하여 배포할 수 있는 모든 네트워크 기능 제품에는 Azure Marketplace에서 제공되는 관리형 애플리케이션이 있습니다. 관리형 애플리케이션을 통해 파트너는 다음을 수행할 수 있습니다.

* Azure Portal 환경을 사용하여 네트워크 기능의 사용자 지정 배포 환경을 빌드합니다. 

* Azure Stack Edge 디바이스에서 직접 네트워크 기능을 만들 수 있는 특수화된 Resource Manager 템플릿을 제공합니다.

* 소프트웨어 라이선스 비용을 직접 또는 Azure Marketplace를 통해 청구합니다. 

* 사용자 지정 속성 및 리소스 미터를 표시합니다.

네트워크 기능 파트너는 어플라이언스 배포, 구성 라이선스, 관리 요구 사항 등에 따라 다양한 리소스를 만들 수 있습니다. 모든 Azure Managed Applications의 경우와 처럼 고객이 Azure Stack Edge 디바이스에서 네트워크 기능을 만들 때 해당 구독에는 두 개의 리소스 그룹이 생성됩니다.

* **고객 리소스 그룹 -** 리소스 그룹에 관리형 애플리케이션의 애플리케이션 자리 표시자가 포함됩니다. 파트너는 이를 사용하여 이 곳에서 선택하는 모든 고객 속성을 노출할 수 있습니다. 

* **관리되는 리소스 그룹** - 관리형 애플리케이션의 게시자가 제어하므로 이 리소스 그룹의 리소스를 직접 구성하거나 변경할 수 없습니다. 이 리소스 그룹에는 Network Function Manager **네트워크 기능** 리소스가 포함됩니다.

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="관리형 애플리케이션 리소스 그룹의 다이어그램":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>네트워크 기능 구성 프로세스 

Network Function Manager를 사용하여 Azure 관리형 애플리케이션을 제공하는 네트워크 기능 파트너는 배포 프로세스의 일부로 자동으로 네트워크 기능을 구성하는 환경을 제공합니다. 관리형 애플리케이션 배포가 성공적이고 네트워크 기능 인스턴스가 Azure Stack Edge로 프로비저닝된 후 네트워크 기능에 필요할 수 있는 추가 구성은 네트워크 기능 파트너 관리 포털을 통해 수행해야 합니다. Azure Stack Edge 디바이스에 배포된 네트워크 기능의 엔드투엔드 관리 환경에 관해서는 네트워크 기능 파트너에게 문의하세요.

## <a name="region-availability"></a><a name="regions"></a>지역 가용성

Azure Stack Edge 리소스, Azure Network Function Manager 디바이스 및 네트워크 기능용 Azure 관리형 애플리케이션은 동일한 Azure 지역에 있어야 합니다. Azure Stack Edge Pro GPU 물리적 디바이스는 동일한 지역에 있지 않아도 됩니다.

* **리소스 가용성 -** Network Function Manager 리소스는 다음 지역에서 사용할 수 있습니다.

   [!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

* **디바이스 가용성 -** Azure Stack Edge Pro GPU 디바이스를 사용할 수 있는 모든 국가/지역 목록을 보려면 [Azure Stack Edge Pro GPU 가격 책정](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)으로 이동합니다. **Azure Stack Edge Pro** 탭에서  **가용성** 섹션을 확인합니다.

현재 릴리스에서 Network Function Manager는 지역 서비스입니다. 지역 전체 중단의 경우 Network Function Manager 리소스의 관리 작업은 영향을 받지만 Azure Stack Edge 디바이스에서 실행되는 네트워크 기능은 지역 전체 중단의 영향을 받지 않습니다.

## <a name="partner-solutions"></a><a name="partners"></a>파트너 솔루션

개인 모바일 네트워크, SD-WAN 및 VPN 솔루션용 Marketplace 관리형 애플리케이션을 제공하는 파트너의 성장하는 에코시스템에 관해서는 Network Function Manager [파트너 페이지](partners.md)를 참조하세요.

## <a name="faq"></a><a name="faq"></a>FAQ

FAQ에 관해서는 [Network Function Manager FAQ](faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[디바이스 리소스 만들기](create-device.md)
