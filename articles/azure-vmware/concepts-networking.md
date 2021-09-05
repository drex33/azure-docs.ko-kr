---
title: 개념 - Network 상호 연결
description: Azure VMware Solution에서 네트워킹 및 상호 연결의 주요 측면과 사용 사례에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 91bd1bfc207a2fb08b644e4ecf54be7b254c39fa
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537623"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Azure VMware Solution 네트워킹 및 상호 연결 개념

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Azure VMware Solution 프라이빗 클라우드에서 상호 연결하는 방법에는 두 가지가 있습니다.

- [**기본 Azure 전용 상호 연결**](#azure-virtual-network-interconnectivity)을 사용하면 Azure의 단일 가상 네트워크로 프라이빗 클라우드를 관리하고 사용할 수 있습니다. 해당 구현은 온-프레미스 환경에서 액세스할 필요가 없는 Azure VMware Solution 평가 또는 구현에 가장 적합합니다.

- [**온-프레미스에서 프라이빗 클라우드로의 완전한 상호 연결**](#on-premises-interconnectivity)은 기본 Azure 전용 구현을 확장하여 온-프레미스와 Azure VMware Solution 프라이빗 클라우드 간 상호 연결을 포함합니다.
 
이 문서에서는 요구 사항 및 제한 사항을 포함하여 네트워킹 및 상호 연결을 설정하는 주요 개념을 다룹니다. 또한 이 문서에서는 Azure VMware Solution을 사용하여 네트워킹을 구성하는 데 필요한 정보를 제공합니다.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Azure VMware Solution 프라이빗 클라우드 사용 사례

Azure VMware Solution 프라이빗 클라우드의 사용 사례는 다음과 같습니다.
- 클라우드의 새 VMware VM 워크로드
- 클라우드로의 VM 워크로드 버스팅(온-프레미스에서 Azure VMware Solution으로만)
- 클라우드로의 VM 워크로드 마이그레이션(온-프레미스에서 Azure VMware Solution으로만)
- 재해 복구(Azure VMware Solution 간 또는 온-프레미스에서 Azure VMware Solution으로)
- Azure 서비스 사용

> [!TIP]
> Azure VMware Solution 서비스의 모든 사용 사례는 온-프레미스에서 프라이빗 클라우드로의 연결을 통해 사용하도록 설정됩니다.

## <a name="azure-virtual-network-interconnectivity"></a>Azure 가상 네트워크 상호 연결

Azure 가상 네트워크를 Azure VMware Solution 프라이빗 클라우드 구현과 상호 연결할 수 있습니다. Azure VMware Solution 프라이빗 클라우드를 관리하고, 프라이빗 클라우드에서 워크로드를 사용하고, 다른 Azure 서비스에 액세스할 수 있습니다.

아래 다이어그램은 프라이빗 클라우드 배포 시 설정된 기본 네트워크 상호 연결을 보여 줍니다. Azure의 가상 네트워크와 프라이빗 클라우드의 논리적 네트워킹을 보여 줍니다. 해당 연결은 Azure VMware Solution 서비스의 일부인 백 엔드 ExpressRoute를 통해 설정됩니다. 상호 연결은 다음과 같은 주요 사용 사례를 충족합니다.

- Azure 구독의 VM에서 액세스할 수 있는 vCenter Server 및 NSX-T 관리자에 대한 인바운드 액세스.
- 프라이빗 클라우드의 VM에서 Azure 서비스로의 아웃바운드 액세스.
- 프라이빗 클라우드에서 실행되는 워크로드의 인바운드 액세스.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Azure VMware Solution 프라이빗 클라우드 배포 시 설정된 기본 네트워크 상호 연결을 보여주는 다이어그램." border="false":::

## <a name="on-premises-interconnectivity"></a>온-프레미스 상호 연결

완전히 상호 연결된 시나리오에서는 Azure 가상 네트워크 및 온-프레미스에서 Azure VMware Solution에 액세스할 수 있습니다. 해당 구현은 이전 섹션에서 설명한 기본 구현의 확장입니다. ExpressRoute 회로는 온-프레미스에서 Azure의 Azure VMware Solution 프라이빗 클라우드로 연결하는 데 필요합니다.

아래 다이어그램은 온-프레미스에서 프라이빗 클라우드로의 상호 연결을 보여 주며, 다음과 같은 사용 사례를 사용할 수 있습니다.

- 온-프레미스와 Azure VMware Solution 간의 핫/콜드 vCenter vMotion.
- 온-프레미스에서 Azure VMware Solution으로의 프라이빗 클라우드 관리 액세스.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="가상 네트워크 및 온-프레미스에서 프라이빗 클라우드 간 상호 연결을 보여주는 다이어그램." border="false":::

프라이빗 클라우드에 대한 전체 상호 연결을 위해 ExpressRoute Global Reach를 사용하고 Azure Portal에서 Global Reach에 대한 인증 키 및 개인 피어링 ID를 요청해야 합니다. 권한 부여 키와 피어링 ID는 구독의 ExpressRoute 회로와 프라이빗 클라우드의 ExpressRoute 회로 간에 Global Reach를 설정하는 데 사용됩니다. 연결되면 두 개의 ExpressRoute 회로에서 온-프레미스 환경 간의 네트워크 트래픽을 프라이빗 클라우드로 라우팅합니다. 절차에 대한 자세한 내용은 [프라이빗 클라우드로 ExpressRoute Global Reach 피어링 만들기에 대한 자습서](tutorial-expressroute-global-reach-private-cloud.md)를 참조하세요.

## <a name="limitations"></a>제한 사항
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>다음 단계 

Azure VMware Solution 네트워크 및 상호 연결 개념을 살펴보았으므로 이제 다음에 대해 알아볼 수 있습니다.

- [Azure VMware Solution 스토리지 개념](concepts-storage.md)
- [Azure VMware Solution ID 개념](concepts-identity.md)
- [Azure VMware Solution 리소스 공급자 사용](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
[concepts-storage]: ./concepts-storage.md
