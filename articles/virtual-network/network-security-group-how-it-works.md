---
title: 네트워크 보안 그룹 - 작동 방식
titlesuffix: Azure Virtual Network
description: 네트워크 보안 그룹이 Azure 리소스 간의 네트워크 트래픽을 필터링하는 데 어떻게 도움이 되는지 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537901"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>네트워크 보안 그룹이 네트워크 트래픽을 필터링하는 방법
<a name="network-security-groups"></a>

Azure 네트워크 보안 그룹을 사용하면 Azure 가상 네트워크의 Azure 리소스와 주고받는 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹에는 여러 종류의 Azure 리소스에서 오는 인바운드 트래픽 또는 이러한 리소스로 나가는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 [보안 규칙](./network-security-groups-overview.md#security-rules)이 포함됩니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다.

여러 Azure 서비스의 리소스를 한 Azure 가상 네트워크에 배포할 수 있습니다. 전체 목록은 [가상 네트워크에 배포할 수 있는 서비스 목록](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)을 참조하세요. 가상 머신의 각 가상 네트워크 [서브넷](virtual-network-manage-subnet.md#change-subnet-settings) 및 [네트워크 인터페이스](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)에 네트워크 보안 그룹을 하나 연결하거나 연결하지 않을 수 있습니다. 동일한 네트워크 보안 그룹을 원하는 수의 서브넷 및 네트워크 인터페이스에 연결할 수 있습니다.

다음 그림은 TCP 포트 80을 통해 인터넷의 네트워크 트래픽을 허용하도록 네트워크 보안 그룹을 배포하는 다양한 시나리오를 보여줍니다.

![NSG 처리](./media/network-security-group-how-it-works/network-security-group-interaction.png)

다음 텍스트와 함께 앞의 그림을 참조하여 Azure가 네트워크 보안 그룹에 대한 인바운드 및 아웃바운드 규칙을 처리하는 방식을 이해하세요.

## <a name="inbound-traffic"></a>인바운드 트래픽

인바운드 트래픽의 경우 Azure는 서브넷에 연결된 네트워크 보안 그룹이 있으면 이 그룹의 규칙을 먼저 처리한 후 네트워크 인터페이스에 연결된 네트워크 보안 그룹이 있으면 이 그룹의 규칙을 처리합니다.

- **VM1**: *NSG1* 이 *Subnet1* 에 연결되었고 *VM1* 이 *Subnet1* 에 있으므로 NSG1의 보안 규칙이 처리됩니다. 포트 80 인바운드를 허용하는 규칙을 만들지 않은 이상, [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) 기본 보안 규칙이 트래픽을 거부하고 *NSG2* 는 트래픽을 평가하지 않습니다. 왜냐하면 *NSG2* 가 네트워크 인터페이스에 연결되었기 때문입니다. *NSG1* 에 포트 80을 허용하는 보안 규칙이 있으면 트래픽이 *NSG2* 에 의해 처리됩니다. 가상 머신에 포트 80을 허용하려면 *NSG1* 및 *NSG2* 모두 인터넷에서 포트 80을 허용하는 규칙이 있어야 합니다.
- **VM2**: *VM2* 역시 *Subnet1* 에 있기 때문에 *NSG1* 의 규칙이 처리됩니다. *VM2* 은 네트워크 인터페이스에 연결된 네트워크 보안 그룹이 없으므로 *NSG1* 을 통해 허용된 트래픽 또는 *NSG1* 에 의해 거부된 트래픽을 모두 수신합니다. 네트워크 보안 그룹이 서브넷에 연결된 경우 동일한 서브넷의 모든 리소스에 대한 트래픽이 허용되거나 거부됩니다.
- **VM3**: *Subnet2* 에 연결된 네트워크 보안 그룹이 없으므로 서브넷으로 가는 트래픽이 허용되고 *NSG2* 에 의해 처리됩니다. *NSG2* 는 *VM3* 에 연결된 네트워크 인터페이스에 연결되었기 때문입니다.
- **VM4**: 네트워크 보안 그룹이 *Subnet3* 또는 가상 머신의 네트워크 인터페이스에 연결되지 않았기 때문에 *VM4* 로 가는 트래픽이 허용됩니다. 서브넷 및 네트워크 인터페이스에 네트워크 보안 그룹이 연결되지 않으면 서브넷 및 네트워크 인터페이스를 통과하는 모든 네트워크 트래픽이 허용됩니다.

## <a name="outbound-traffic"></a>아웃바운드 트래픽

인바운드 트래픽의 경우 Azure는 네트워크 인터페이스에 연결된 네트워크 보안 그룹이 있으면 이 그룹의 규칙을 먼저 처리한 후 서브넷에 연결된 네트워크 보안 그룹이 있으면 이 그룹의 규칙을 처리합니다.

- **VM1**: *NSG2* 의 보안 규칙이 처리됩니다. 인터넷으로 나가는 포트 80을 거부하는 보안 규칙을 만들지 않는 이상, *NSG1* 및 *NSG2* 의 [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) 기본 보안 규칙에 트래픽을 허용합니다. *NSG2* 에 포트 80을 거부하는 보안 규칙이 있으면 트래픽은 거부되고 *NSG1* 에 의해 평가되지 않습니다. 두 네트워크 보안 그룹 중 어느 하나 또는 둘 모두의 가상 머신에서 포트 80을 거부하려면 규칙이 네트워크 보안 그룹에 인터넷으로 나가는 포트 80을 거부하는 있어야 합니다.
- **VM2**: *VM2* 에 연결된 네트워크 인터페이스에 네트워크 보안 그룹이 연결되지 않았기 때문에 모든 트래픽이 네트워크 인터페이스를 통해 서브넷으로 전송됩니다. *NSG1* 의 규칙이 처리됩니다.
- **VM3**: *NSG2* 에 포트 80을 거부하는 보안 규칙이 있으면 트래픽이 거부됩니다. *NSG2* 에 포트 80을 허용하는 보안 규칙이 있으면 포트 80에서 인터넷 아웃바운드가 허용됩니다. 네트워크 보안 그룹이 *Subnet2* 에 연결되지 않았기 때문입니다.
- **VM4**: 네트워크 보안 그룹이 가상 머신에 연결된 네트워크 인터페이스 또는 *Subnet3* 에 연결되지 않았기 때문에 *VM4* 에서 오는 모든 트래픽이 허용됩니다.


## <a name="intra-subnet-traffic"></a>서브넷 내 트래픽

서브넷에 연결된 NSG의 보안 규칙은 서브넷 내 VM 간의 연결에 영향을 줄 수 있다는 점에 유의해야 합니다. 예를 들어 모든 인바운드 및 아웃바운드 트래픽을 거부하는 규칙이 *NSG1* 에 추가되면 *VM1* 과 *VM2* 는 더 이상 서로 통신할 수 없습니다. 이를 허용하려면 특별히 다른 규칙을 추가해야 합니다. 

네트워크 인터페이스의 [유효 보안 규칙](virtual-network-network-interface.md#view-effective-security-rules)을 확인하여 네트워크 인터페이스에 적용되는 집계 규칙을 쉽게 볼 수 있습니다. Azure Network Watcher에서 [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 기능을 사용하여 통신이 네트워크 인터페이스 간에 허용되는지 여부를 결정할 수도 있습니다. IP 흐름 확인은 통신이 허용 또는 거부되는지 여부와 트래픽을 허용하거나 거부하는 네트워크 보안 규칙을 알려줍니다.

> [!NOTE]
> 네트워크 보안 그룹은 서브넷 또는 클래식 배포 모델에 배포된 가상 머신 및 클라우드 서비스 및 Resource Manager 배포 모델의 서브넷 또는 네트워크 인터페이스에 연결됩니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

> [!TIP]
> 특별한 이유가 없다면 네트워크 보안 그룹을 서브넷 또는 네트워크 인터페이스 중 한 쪽에만 연결하는 것이 좋습니다. 서브넷에 연결된 네트워크 보안 그룹의 규칙이 네트워크 인터페이스에 연결된 네트워크 보안 그룹의 규칙과 충돌할 수 있으므로 예기치 않은 통신 문제가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에 배포하고 네트워크 보안 그룹을 연결할 수 있는 Azure 리소스에 대한 자세한 내용은 [Azure 서비스용 가상 네트워크 통합](virtual-network-for-azure-services.md)을 참조하세요.
* 네트워크 보안 그룹을 만들어 본 경험이 전혀 없는 경우 빠른 [자습서](tutorial-filter-network-traffic.md)를 완료하여 직접 경험해 볼 수 있습니다.
* 네트워크 보안 그룹에 익숙하고 네트워크 보안 그룹을 관리해야 하는 경우 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요. 
* 통신에 문제가 있고 네트워크 보안 그룹 문제를 해결해야 하는 경우 [가상 머신 네트워크 트래픽 필터 문제 진단](diagnose-network-traffic-filter-problem.md)을 참조하세요. 
* 연결된 네트워크 보안 그룹이 있는 리소스와 주고받는 네트워크 트래픽을 분석하기 위해 [네트워크 보안 그룹 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하도록 설정하는 방법을 알아봅니다.