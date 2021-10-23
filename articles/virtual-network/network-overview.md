---
title: Azure의 가상 네트워크 및 가상 머신
description: Azure의 가상 머신과 관련된 네트워킹에 관해 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: allensu
ms.openlocfilehash: d8ca819e7a63c7a0c4d507709c8495b604d19002
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257673"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Azure의 가상 네트워크 및 가상 머신

VM(가상 머신)을 만들 때 [가상 네트워크](../virtual-network/virtual-networks-overview.md)를 만들거나 기존 가상 네트워크를 사용합니다. 가상 네트워크에서 가상 머신에 액세스하는 방법을 결정합니다. [리소스를 만들기 전에 계획](../virtual-network/virtual-network-vnet-plan-design-arm.md)하는 것이 중요하고 [네트워킹 리소스의 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)를 이해해야 합니다.

다음 그림에서 가상 머신은 웹 서버와 애플리케이션 서버로 표시됩니다. 각 가상 머신 세트는 가상 네트워크의 개별 서브넷에 할당됩니다.

:::image type="content" source="./media/network-overview/load-balancer.png" alt-text="다중 계층, 다중 서브넷 애플리케이션의 다이어그램" border="true":::

가상 머신을 만들기 전에 가상 네트워크를 만들거나, 가상 머신을 만들 때 가상 네트워크를 만들 수 있습니다. 

가상 머신에 대한 통신을 지원하기 위해 다음 리소스를 만듭니다.

- 네트워크 인터페이스
- IP 주소
- 가상 네트워크 및 서브넷

또한 다음 선택적 리소스를 고려합니다.

- 네트워크 보안 그룹
- 부하 분산 장치

## <a name="network-interfaces"></a>네트워크 인터페이스

[NIC(네트워크 인터페이스)](../virtual-network/virtual-network-network-interface.md)는 가상 머신과 가상 네트워크 간의 상호 연결입니다. 가상 머신에는 하나 이상의 NIC가 있어야 합니다. 가상 머신은 생성되는 VM 크기에 따라 NIC를 두 개 이상 포함할 수 있습니다. 각 가상 머신 크기가 지원하는 NIC 수에 관한 자세한 내용은 [VM 크기](../virtual-machines/sizes.md)를 참조하세요.

여러 개의 NIC가 있는 VM을 만들거나 VM 수명 주기 동안 NIC를 추가 또는 제거할 수 있습니다. 여러 NIC를 사용하면 VM이 여러 서브넷에 연결할 수 있습니다.

VM에 연결된 각각의 NIC는 해당 VM과 동일한 위치와 구독에 있어야 합니다. 각각의 NIC는 해당 NIC와 동일한 Azure 위치와 구독에 있는 VNet에 연결되어야 합니다. VM을 만든 후에는 VM이 연결된 서브넷을 변경할 수 있습니다. 가상 네트워크는 변경할 수 없습니다. VM에 연결된 각각의 NIC에는 해당 VM을 삭제할 때까지 변경되지 않는 MAC 주소가 할당됩니다.

다음 표에서는 NIC를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](https://portal.azure.com) | Azure Portal에서 VM을 만들 때 네트워크 인터페이스가 자동으로 만들어집니다. 포털에서는 NIC 하나만 사용하는 VM을 만듭니다. NIC가 둘 이상 있는 VM을 만들려면 다른 방법으로 VM을 만들어야 합니다. |
| [Azure PowerShell](../virtual-machines/windows/multiple-nics.md) | **`-PublicIpAddressId`** 매개 변수와 함께 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 이전에 만든 공용 IP 주소의 식별자를 제공합니다. |
| [Azure CLI](../virtual-machines/linux/multiple-nics.md) | **`--public-ip-address`** 매개 변수와 함께 [az network nic create](/cli/azure/network/nic)를 사용하여 이전에 만든 공용 IP 주소의 식별자를 제공합니다. |
| [템플릿](../virtual-network/template-samples.md) | 템플릿을 사용하여 네트워킹 인터페이스를 배포하는 방법에 관한 내용은 [공용 IP 주소를 사용하는 가상 네트워크의 네트워크 인터페이스](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)를 참조하세요. |

## <a name="ip-addresses"></a>IP 주소

Azure에서 네트워크 인터페이스에 다음 유형의 [IP 주소](./ip-services/public-ip-addresses.md)를 할당할 수 있습니다.

- **공용 IP 주소** - 인터넷과 가상 네트워크에 연결되지 않은 다른 Azure 리소스와 인바운드 및 아웃바운드(NAT(Network Address Translation) 사용 안 함) 통신하는 데 사용됩니다. 공용 IP 주소를 NIC에 할당하는 것은 선택 사항입니다. 공용 IP 주소에는 명목 상의 요금이 청구되며, 구독당 사용할 수 있는 최대 개수가 있습니다.
- **개인 IP 주소** - 가상 네트워크, 온-프레미스 네트워크, 인터넷(NAT 사용) 내 통신에 사용됩니다. 하나 이상의 개인 IP 주소를 VM에 할당해야 합니다. Azure의 NAT에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md)를 참조하세요.

공용 IP 주소를 다음에 할당할 수 있습니다.

* 가상 머신
* 공용 부하 분산 장치

개인 IP 주소를 다음에 할당할 수 있습니다.

* 가상 머신
* 내부 부하 분산 장치

네트워크 인터페이스를 사용하여 IP 주소를 VM에 할당합니다.

IP 주소를 리소스에 제공하는 두 가지 방법, 즉 동적 또는 고정 할당 방법이 있습니다. Azure가 IP 주소를 제공하는 기본 방법은 동적입니다. IP 주소는 생성 시 제공되지 않습니다. 대신 VM을 만들거나 중지된 VM을 시작할 때 IP 주소가 제공됩니다. VM을 중지하거나 삭제하면 IP 주소가 해제됩니다.

VM의 IP 주소를 동일하게 유지하려면 정적 할당 방법을 명시적으로 설정하면 됩니다. 이 경우 IP 주소는 즉시 할당되며, 고정 방법은 VM을 삭제하거나 동적 할당 방법으로 변경할 때만 해제됩니다.

다음 표에서는 IP 주소를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](./ip-services/virtual-network-deploy-static-pip-arm-portal.md) | 기본적으로 공용 IP 주소는 동적입니다. VM이 중지되거나 삭제되면 IP 주소가 변경될 수 있습니다. VM에서 항상 동일한 공용 IP 주소를 사용하도록 하려면 정적 공용 IP 주소를 만들어야 합니다. 기본적으로 포털은 VM을 만들 때 동적 개인 IP 주소를 NIC에 할당합니다. 이 IP 주소는 VM을 만든 후에 고정으로 변경할 수 있습니다.|
| [Azure PowerShell](./ip-services/virtual-network-deploy-static-pip-arm-ps.md) | Dynamic 또는 Static인 **`-AllocationMethod`** 매개 변수와 함께 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용합니다. |
| [Azure CLI](./ip-services/virtual-network-deploy-static-pip-arm-cli.md) | Dynamic 또는 Static인 **`--allocation-method`** 매개 변수와 함께 [az network public-ip create](/cli/azure/network/public-ip)를 사용합니다. |
| [템플릿](../virtual-network/template-samples.md) | 템플릿을 사용하여 공용 IP 주소를 배포하는 방법에 관한 자세한 내용은 [공용 IP 주소를 사용하는 가상 네트워크의 네트워크 인터페이스](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)를 참조하세요. |

공용 IP 주소를 만든 후에는 이 주소를 NIC에 할당하여 VM과 연결할 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="virtual-network-and-subnets"></a>가상 네트워크 및 서브넷

서브넷은 가상 네트워크 내 IP 주소의 범위입니다. 조직 및 보안을 위해 가상 네트워크를 여러 서브넷으로 나눌 수 있습니다. VM의 각 NIC는 하나의 가상 네트워크에서 하나의 서브넷에 연결됩니다. 가상 네트워크 내에서 동일하거나 다른 서브넷에 연결된 NIC는 별도의 구성 없이 서로 통신할 수 있습니다.

가상 네트워크를 설정할 때 사용 가능한 주소 공간과 서브넷을 포함하여 토폴로지를 지정합니다. 가상 네트워크가 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결된 경우에는 겹치지 않는 주소 범위를 선택합니다. IP 주소는 개인 IP 주소이며 인터넷에서 액세스할 수 없습니다. Azure는 모든 주소 범위를 프라이빗 가상 네트워크 IP 주소 공간의 일부로 처리합니다. 주소 범위는 가상 네트워크 내, 상호 연결된 가상 네트워크 내, 온-프레미스 위치에서만 연결할 수 있습니다.

다른 사용자가 내부 네트워크를 담당하는 조직 내에서 작업하는 경우 주소 공간을 선택하기 전에 해당 사용자에게 문의합니다. 주소 공간이 겹치지 않는지 확인합니다. 동일한 IP 주소 범위를 사용하지 않도록 다른 사용자에게 사용하려는 공간을 알립니다.

서브넷 간에는 기본적으로 보안 경계가 없습니다. 이러한 각 서브넷의 가상 머신은 통신할 수 있습니다. 배포에 보안 경계가 필요한 경우 서브넷 간, VM 간에 트래픽 흐름을 제어할 수 있는 **NSG(네트워크 보안 그룹)** 를 사용합니다.

다음 표에서는 가상 네트워크와 서브넷을 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 메서드 | Description |
| ------ | ----------- |
| [Azure Portal](../virtual-network/quick-create-portal.md) | Azure에서 VM을 만들 때 가상 네트워크를 만들도록 하는 경우 해당 이름은 가상 네트워크를 포함하는 리소스 그룹 이름과 **`-vnet`** 의 조합입니다. 주소 공간은 10.0.0.0/24이고, 필수 서브넷 이름은 **default** 이며, 서브넷 주소 범위는 10.0.0.0/24입니다. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) 및 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 서브넷과 가상 네트워크를 만듭니다. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig)를 사용하여 기존 가상 네트워크에 서브넷을 추가할 수도 있습니다. |
| [Azure CLI](../virtual-network/quick-create-cli.md) | 서브넷과 가상 네트워크는 동시에 만들어집니다. [az network vnet create](/cli/azure/network/vnet)에 서브넷 이름으로 **`--subnet-name`** 매개 변수를 제공합니다. |
| [템플릿](../virtual-network/template-samples.md) | 템플릿을 사용하여 가상 네트워크와 서브넷을 만드는 방법에 관한 자세한 내용은 [두 서브넷을 사용하는 가상 네트워크](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-two-subnets)를 참조하세요. |

## <a name="network-security-groups"></a>네트워크 보안 그룹

[NSG(네트워크 보안 그룹)](../virtual-network/virtual-network-vnet-plan-design-arm.md)에는 서브넷, NIC 또는 둘 모두에 대한 네트워크 트래픽을 허용하거나 거부하는 ACL(Access Control 목록) 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 서브넷에 연결된 개별 NIC와 연결될 수 있습니다. NSG를 서브넷과 연결하는 경우 ACL 규칙이 해당 서브넷의 모든 VM에 적용됩니다. NSG를 NIC에 직접 연결하여 개별 NIC에 대한 트래픽을 제한할 수 있습니다.

NSG에는 두 가지 규칙 집합, 즉 인바운드 및 아웃바운드가 포함됩니다. 규칙에 대한 우선 순위는 각 집합 내에서 고유해야 합니다. 

각 규칙에는 다음 속성이 있습니다.

* 프로토콜
* 원본 및 대상 포트 범위
* 주소 접두사
* 트래픽 방향
* 우선 순위
* 액세스 유형

모든 NSG에는 기본 규칙 집합이 포함됩니다. 기본 규칙은 삭제할 수 없습니다. 가장 낮은 우선 순위가 할당되며 만드는 규칙을 통해 재정의할 수 없습니다.

NSG를 NIC에 연결하는 경우 NSG의 네트워크 액세스 규칙은 NIC에만 적용됩니다. NSG를 다중 NIC VM의 단일 NIC에 적용하는 경우 다른 NIC에 대한 트래픽에는 영향을 미치지 않습니다. NIC(또는 배포 모델에 따라 VM) 및 NIC 또는 VM에서 바인딩하는 서브넷에 다른 NSG를 연결할 수 있습니다. 우선 순위는 트래픽 방향에 따라 부여됩니다.

가상 머신과 가상 네트워크를 계획하는 경우 NSG를 [계획](../virtual-network/virtual-network-vnet-plan-design-arm.md)해야 합니다.

다음 표에서는 NSG를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Azure Portal에서 VM을 만들 때 NSG가 자동으로 만들어지고 포털에서 만든 NIC에 연결됩니다. NSG 이름은 VM 이름과 **`-nsg`** 의 조합입니다. </br> 이 NSG에는 다음과 같은 하나의 인바운드 규칙이 포함됩니다. </br> 우선 순위가 1000입니다. </br> 서비스가 RDP로 설정됩니다. </br> 프로토콜이 TCP로 설정됩니다. </br> 포트가 3389로 설정됩니다. </br> 작업이 **허용** 으로 설정됩니다. </br> VM에 대한 다른 인바운드 트래픽을 허용하려면 다른 규칙을 만듭니다. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하고 필요한 규칙 정보를 제공합니다. [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 NSG를 만듭니다. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)를 사용하여 NSG를 서브넷에 구성합니다. [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 NSG를 가상 네트워크에 추가합니다. |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | [az network nsg create](/cli/azure/network/nsg)를 사용하여 NSG를 처음으로 만듭니다. [az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 규칙을 NSG에 추가합니다. [az network vnet subnet update](/cli/azure/network/vnet/subnet)를 사용하여 NSG를 서브넷에 추가합니다. |
| [템플릿](../virtual-network/template-samples.md) | 템플릿을 사용하여 네트워크 보안 그룹을 배포하기 위한 지침으로 [네트워크 보안 그룹 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/security-group-create)를 사용합니다. |

## <a name="load-balancers"></a>부하 분산 장치

[Azure Load Balancer](../load-balancer/load-balancer-overview.md)는 애플리케이션에 고가용성 및 네트워크 성능을 제공합니다. 부하 분산 장치는 [들어오는 인터넷 트래픽을 VM에 분산](../load-balancer/components.md#frontend-ip-configurations)하거나 [VNet에서 VM 간의 트래픽 균형을 조정](../load-balancer/components.md#frontend-ip-configurations)하도록 구성할 수 있습니다. 또한 부하 분산 장치는 온-프레미스 네트워크의 온-프레미스 컴퓨터와 VM 간의 트래픽 균형을 조정하거나 외부 트래픽을 특정 VM으로 전달할 수도 있습니다.

부하 분산 장치는 다음 항목 간에 들어오는 트래픽과 나가는 트래픽을 매핑합니다.  

* 부하 분산 장치의 공용 IP 주소와 포트.

* VM의 개인 IP 주소와 포트.

부하 분산 장치를 만들 때는 다음 구성 요소도 고려해야 합니다.

- **프런트 엔드 IP 구성** - 부하 분산 장치는 하나 이상의 프런트 엔드 IP 주소를 포함할 수 있습니다. 이러한 IP 주소는 트래픽에 대한 수신으로 사용됩니다.
- **백 엔드 주소 풀** - 부하가 분산되는 NIC와 연결된 IP 주소입니다.
- **[포트 전달](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** - 인바운드 트래픽이 프런트 엔드 IP를 통과하고 인바운드 NAT 규칙을 사용하여 백 엔드 IP로 분산되는 방식을 정의합니다.
- **부하 분산 장치 규칙** - 지정된 프런트 엔드 IP와 포트의 조합을 백 엔드 IP 주소와 포트의 조합 집합에 매핑합니다. 부하 분산 장치 하나에 여러 부하 분산 규칙이 포함될 수 있습니다. 각 규칙은 VM과 연결된 백 엔드 IP와 포트 및 프런트 엔드 IP와 포트의 조합입니다.
- **[프로브](../load-balancer/load-balancer-custom-probe-overview.md)** - VM의 상태를 모니터링합니다. 프로브에서 응답하지 않으면 부하 분산 장치에서 비정상 VM에 대한 새 연결을 보내지 않습니다. 기존 연결은 영향을 받지 않으며, 새 연결은 정상 VM으로 보내집니다.
- **[아웃바운드 규칙](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** - 아웃바운드 규칙은 표준 Load Balancer의 백 엔드 풀에서 식별된 모든 가상 머신 또는 인스턴스의 아웃바운드 NAT(Network Address Translation)를 프런트 엔드로 변환하도록 구성합니다.

다음 표에서는 인터넷 연결 부하 분산 장치를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| Azure portal |  [Azure Portal을 사용하여 인터넷 트래픽 부하를 VM에 분산](../load-balancer/quickstart-load-balancer-standard-public-portal.md)할 수 있습니다. |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | 공용 IP 주소의 식별자를 제공하려면 **`-PublicIpAddress`** 매개 변수와 함께 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용합니다. [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀의 구성을 만듭니다. [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)를 사용하여 사용자가 만든 프런트 엔드 IP 구성과 연결되는 인바운드 NAT 규칙을 만듭니다. [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig)를 사용하여 필요한 프로브를 작성합니다. [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 구성을 만듭니다. [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 부하 분산 장치를 만듭니다.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | [az network lb create](/cli/azure/network/lb)를 사용하여 초기 부하 분산 장치 구성을 작성합니다. [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip)를 사용하여 이전에 만든 공용 IP 주소를 추가합니다. [az network lb address-pool create](/cli/azure/network/lb/address-pool)를 사용하여 백 엔드 주소 풀의 구성을 추가합니다. [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule)를 사용하여 NAT 규칙을 추가합니다. [az network lb rule create](/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 추가합니다. [az network lb probe create](/cli/azure/network/lb/probe)를 사용하여 프로브를 추가합니다. |
| [템플릿](../load-balancer/quickstart-load-balancer-standard-public-template.md) | 템플릿을 사용하여 부하 분산 장치를 배포하기 위한 지침으로 [Load Balancer의 3개 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/load-balancer-standard-create)을 사용합니다. |

다음 표에서는 내부 부하 분산 장치를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| Azure portal | [Azure Portal에서 부하 분산 장치로 내부 트래픽을 부하 분산](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)할 수 있습니다. |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | 개인 IP 주소를 네트워크 서브넷에 제공하려면 **`-PrivateIpAddress`** 매개 변수와 함께 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용합니다. [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀의 구성을 만듭니다. [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)를 사용하여 사용자가 만든 프런트 엔드 IP 구성과 연결되는 인바운드 NAT 규칙을 만듭니다. [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig)를 사용하여 필요한 프로브를 작성합니다. [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 구성을 만듭니다. [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 부하 분산 장치를 만듭니다.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | [az network lb create](/cli/azure/network/lb) 명령을 사용하여 초기 부하 분산 장치 구성을 만듭니다. 개인 IP 주소를 정의하려면 **`--private-ip-address`** 매개 변수와 함께 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip)를 사용합니다. [az network lb address-pool create](/cli/azure/network/lb/address-pool)를 사용하여 백 엔드 주소 풀의 구성을 추가합니다. [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule)를 사용하여 NAT 규칙을 추가합니다. [az network lb rule create](/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 추가합니다. [az network lb probe create](/cli/azure/network/lb/probe)를 사용하여 프로브를 추가합니다.|
| [템플릿](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | 템플릿을 사용하여 부하 분산 장치를 배포하기 위한 지침으로 [Load Balancer의 2개 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer)을 사용합니다. |

## <a name="virtual-machines"></a>가상 머신

가상 머신은 동일한 가상 네트워크에 만들 수 있으며 개인 IP 주소를 사용하여 서로 연결할 수 있습니다. 서로 다른 서브넷에 있는 가상 머신이 연결될 수 있습니다. 게이트웨이를 구성하거나 공용 IP 주소를 사용하지 않고도 연결됩니다. VM을 가상 네트워크에 배치하려면 가상 네트워크를 만듭니다. 각 VM을 만들 때 가상 네트워크와 서브넷에 할당합니다. 가상 머신은 배포 또는 시작 중에 네트워크 설정을 가져옵니다.

가상 머신은 배포될 때 IP 주소가 할당됩니다. 여러 VM을 가상 네트워크 또는 서브넷에 배포하면 VM이 부팅될 때 IP 주소가 할당됩니다. VM에 고정 IP를 할당할 수도 있습니다. 고정 IP를 할당하는 경우 실수로 다른 VM의 고정 IP를 다시 사용하지 않도록 특정 서브넷을 사용하는 것이 좋습니다.

VM을 만들고 나중에 해당 VM을 가상 네트워크로 마이그레이션하려는 경우 단순히 구성만 변경하는 것이 아닙니다. 가상 네트워크에 VM을 다시 배포합니다. 다시 배포하는 가장 쉬운 방법은 VM을 삭제하고 연결된 디스크를 제거하지 않은 채 가상 네트워크의 원본 디스크를 사용하여 VM을 다시 만드는 것입니다.

다음 표에서는 VNet에서 VM을 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../virtual-machines/windows/quick-create-portal.md) | 단일 NIC를 사용하는 VM을 만들기 위해 앞에서 설명한 기본 네트워크 설정을 사용합니다. 여러 NIC를 사용하는 VM을 만들려면 다른 방법을 사용해야 합니다. |
| [Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md) | [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)를 사용하여 이전에 만든 NIC를 VM 구성에 추가합니다. |
| [Azure CLI](../virtual-machines/linux/create-cli-complete.md) | VM을 만들고 개별 단계로 빌드되는 가상 네트워크, 서브넷, NIC에 연결합니다. |
| [템플릿](../virtual-machines/windows/ps-template.md) | 템플릿을 사용하여 VM을 배포하기 위한 지침으로 [매우 간단한 Windows VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows)(영문)를 사용합니다. |

## <a name="virtual-network-nat"></a>Virtual Network NAT

Virtual Network NAT(Network Address Translation)는 가상 네트워크에 대한 아웃바운드 전용 인터넷 연결을 간소화합니다. 서브넷에 구성되는 경우 모든 아웃바운드 연결에서 지정된 고정 공용 IP 주소를 사용합니다. 가상 머신에 직접 연결되는 부하 분산 장치 또는 공용 IP 주소가 없으면 아웃바운드 연결이 가능합니다. NAT는 완전 관리형이며 복원력이 높습니다.

NAT를 사용하여 각 서브넷에 대한 아웃바운드 연결을 정의할 수 있습니다. 동일한 가상 네트워크 내의 여러 서브넷에는 서로 다른 NAT가 있을 수 있습니다. 서브넷은 사용할 NAT 게이트웨이 리소스를 지정하여 구성됩니다. 모든 가상 머신 인스턴스의 모든 UDP 및 TCP 아웃바운드 흐름은 NAT를 사용합니다.
NAT는 표준 SKU 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 이 둘의 조합과 호환됩니다. 공용 IP 접두사를 직접 사용하거나 접두사의 공용 IP 주소를 여러 NAT 게이트웨이 리소스에 배포할 수 있습니다. NAT는 모든 트래픽을 접두사의 IP 주소 범위로 그루밍합니다. 배포의 모든 IP를 더 쉽게 필터링할 수 있습니다.

고객이 구성하지 않고도 NAT에서 서브넷의 모든 아웃바운드 트래픽을 자동으로 처리합니다. 사용자 정의 경로는 필요하지 않습니다. NAT는 다른 아웃바운드 시나리오보다 우선적으로 적용되며 서브넷의 기본 인터넷 대상을 대체합니다.

Virtual machine scale sets에서 만든 가상 머신 유연한 오케스트레이션 모드에는 기본 아웃 바운드 액세스가 없습니다. Virtual network NAT는 가상 머신 확장 집합의 유연한 오케스트레이션 모드에 권장 되는 아웃 바운드 액세스 방법입니다.

NAT 게이트웨이 리소스와 가상 네트워크 NAT에 관한 자세한 내용은 [Azure Virtual Network NAT란?](./nat-gateway/nat-overview.md)을 참조하세요.

다음 표에서는 NAT 게이트웨이 리소스를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 메서드 | Description |
| ------ | ----------- |
| [Azure Portal](./nat-gateway/tutorial-create-nat-gateway-portal.md) | NAT 게이트웨이 리소스를 테스트하기 위해 가상 네트워크, 서브넷, 공용 IP, NAT 게이트웨이, 가상 머신을 만듭니다. |
| [Azure PowerShell](./nat-gateway/tutorial-create-nat-gateway-powershell.md) | NAT 게이트웨이 리소스를 만들기 위해 [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) 사용을 포함합니다. NAT 게이트웨이 리소스를 테스트하기 위해 가상 네트워크, 서브넷, 공용 IP, NAT 게이트웨이, 가상 머신을 만듭니다. |
| [Azure CLI](./nat-gateway/tutorial-create-nat-gateway-cli.md) | NAT 게이트웨이 리소스를 만들기 위해 [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) 사용을 포함합니다. NAT 게이트웨이 리소스를 테스트하기 위해 가상 네트워크, 서브넷, 공용 IP, NAT 게이트웨이, 가상 머신을 만듭니다. |
| [템플릿](./nat-gateway/quickstart-create-nat-gateway-template.md) | 가상 네트워크, 서브넷, 공용 IP, NAT 게이트웨이 리소스를 만듭니다. |

## <a name="azure-bastion"></a>Azure Bastion 

Azure Bastion은 가상 네트워크의 가상 머신에 대한 안전한 관리 연결을 제공하기 위해 배포됩니다. Azure Bastion 서비스를 사용하면 가상 네트워크의 VM에 안전하고 원활하게 RDP 및 SSH를 제공할 수 있습니다. Azure Bastion을 사용하면 VM에서 공용 IP를 노출하지 않고 연결할 수 있습니다. 추가 클라이언트/에이전트 또는 소프트웨어를 설치하지 않아도 Azure Portal에서 직접 연결할 수 있습니다. Azure Bastion은 표준 SKU 공용 IP 주소를 지원합니다.

Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](../bastion/bastion-overview.md)을 참조하세요.

다음 표에서는 Azure Bastion 배포를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 메서드 | Description |
| ------ | ----------- |
| [Azure Portal](../bastion/quickstart-host-portal.md) | 가상 네트워크, 서브넷, 공용 IP, 베스천 호스트, 가상 머신을 만듭니다. |
| [Azure PowerShell](../bastion/bastion-create-host-powershell.md) | 가상 네트워크, 서브넷, 공용 IP, 베스천 호스트를 만듭니다. 베스천 호스트를 만들기 위해 [New-AzBastion](/powershell/module/az.network/new-azbastion) 사용을 포함합니다. |
| [Azure CLI](../bastion/create-host-cli.md) | 가상 네트워크, 서브넷, 공용 IP, 베스천 호스트를 만듭니다. 베스천 호스트를 만들기 위해 [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) 사용을 포함합니다. |
| [템플릿](../virtual-network/template-samples.md) | Azure Bastion 호스트를 샘플 배포와 통합하는 템플릿 배포의 예제는 [빠른 시작: ARM 템플릿을 사용하여 VM 부하를 분산하는 퍼블릭 부하 분산 장치 만들기](../load-balancer/quickstart-load-balancer-standard-public-template.md)를 참조하세요. |

## <a name="next-steps"></a>다음 단계
VM용 Azure 가상 네트워크를 관리하는 방법에 대한 VM별 단계는 [Windows](../virtual-machines/windows/tutorial-virtual-network.md) 또는 [Linux](../virtual-machines/linux/tutorial-virtual-network.md) 자습서를 참조하세요.

또한 VM 부하를 분산하고 항상 사용 가능한 [Windows](../virtual-machines/windows/tutorial-load-balancer.md) 또는 [Linux](../virtual-machines/linux/tutorial-load-balancer.md)용 애플리케이션을 만드는 방법에 대한 자습서도 있습니다.

- [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 구성하는 방법에 대해 알아봅니다.
- [VNet 간 연결](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 구성하는 방법에 대해 알아봅니다.
- [경로 문제 해결](../virtual-network/diagnose-network-routing-problem.md) 방법에 대해 알아봅니다.
- [가상 머신 네트워크 대역폭](../virtual-network/virtual-machine-network-throughput.md)에 대해 자세히 알아봅니다.