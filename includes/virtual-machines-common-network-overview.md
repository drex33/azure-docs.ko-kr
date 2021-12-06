---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a665c3c27cca03a084896895ae51c56732ceb7b6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237706"
---
Azure VM(가상 머신)을 만들 때 [VNet(가상 네트워크)](../articles/virtual-network/virtual-networks-overview.md)을 만들거나 기존 VNet을 사용해야 합니다. 또한 VNet에서 VM을 액세스하는 방법도 결정해야 합니다. [리소스를 만들기 전에 계획을 수립](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md)하고 [네트워킹 리소스의 제한](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 이해해야 합니다.

다음 그림에서 VM은 웹 서버 및 데이터베이스 서버로 표시됩니다. 각 VM 집합은 VNet에서 별도의 서브넷에 할당됩니다.

![Azure 가상 네트워크](./media/virtual-machines-common-network-overview/vnetoverview.png)

VM을 만들기 전이나 만드는 중에 VNet을 만들 수 있습니다. 다음과 같은 리소스를 만들어 VM과의 통신을 지원합니다.

- 네트워크 인터페이스
- IP 주소
- 가상 네트워크 및 서브넷

이러한 기본 리소스뿐만 아니라 다음과 같은 선택적 리소스도 고려해야 합니다.

- 네트워크 보안 그룹
- 부하 분산 장치 

## <a name="network-interfaces"></a>네트워크 인터페이스

[NIC(네트워크 인터페이스)](../articles/virtual-network/virtual-network-network-interface.md)는 VM과 VNet 간의 상호 연결입니다. VM마다 NIC가 하나 이상 있어야 하지만, 만드는 VM의 크기에 따라 NIC가 둘 이상 있을 수 있습니다. [Windows](../articles/virtual-machines/windows/sizes.md) 또는 [Linux](../articles/virtual-machines/linux/sizes.md)에 대해 각 VM 크기에서 지원하는 NIC 수에 대해 알아보세요.

여러 개의 NIC가 있는 VM을 만들거나 VM 수명 주기 동안 NIC를 추가 또는 제거할 수 있습니다. NIC를 여러 개 사용하면 VM에서 서로 다른 서브넷에 연결하고 가장 적절한 인터페이스를 통해 트래픽을 보내거나 받을 수 있습니다. 여러 개의 네트워크 인터페이스가 있는 VM은 VM 크기에서 지원하는 수의 동일한 가용성 집합에 있을 수 있습니다. 

VM에 연결된 각각의 NIC는 해당 VM과 동일한 위치와 구독에 있어야 합니다. 각각의 NIC는 해당 NIC와 동일한 Azure 위치와 구독에 있는 VNet에 연결되어야 합니다. VM을 만든 후에 연결되는 서브넷을 변경할 수 있지만, 해당 VNet은 변경할 수 없습니다. VM에 연결된 각각의 NIC에는 해당 VM을 삭제할 때까지 변경되지 않는 MAC 주소가 할당됩니다.

다음 표에서는 NIC를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| Azure portal | Azure Portal에서 VM을 만들 때 NIC가 자동으로 만들어집니다(별도로 만든 NIC는 사용할 수 없음). 포털에서는 NIC 하나만 사용하는 VM을 만듭니다. NIC가 둘 이상 있는 VM을 만들려면 다른 방법으로 VM을 만들어야 합니다. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | **-PublicIpAddressId** 매개 변수와 함께 [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)를 사용하여 이전에 만든 공용 IP 주소의 식별자를 제공합니다. |
| [Azure CLI](../articles/virtual-machines/linux/multiple-nics.md) | **--public-ip-address** 매개 변수와 함께 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic)를 사용하여 이전에 만든 공용 IP 주소의 식별자를 제공합니다. |
| [템플릿](../articles/virtual-network/template-samples.md) | 템플릿을 사용하여 네트워크 인터페이스를 배포하기 위한 지침으로 [공용 IP 주소를 사용하는 Virtual Network의 네트워크 인터페이스](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet)를 사용합니다. |

## <a name="ip-addresses"></a>IP 주소 

Azure에서 다음 유형의 [IP 주소](../articles/virtual-network/public-ip-addresses.md)를 NIC에 할당할 수 있습니다.

- **공용 IP 주소** - 인터넷 및 VNet에 연결되지 않은 다른 Azure 리소스와의 인바운드 및 아웃바운드(NAT(Network Address Translation) 사용 안 함) 통신에 사용됩니다. 공용 IP 주소를 NIC에 할당하는 것은 선택 사항입니다. 공용 IP 주소에는 명목 상의 요금이 청구되며, 구독당 사용할 수 있는 최대 개수가 있습니다.
- **개인 IP 주소** - VNet, 온-프레미스 네트워크 및 인터넷(NAT 사용) 내 통신에 사용됩니다. VM에는 개인 IP 주소를 하나 이상 할당해야 합니다. Azure의 NAT에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](../articles/load-balancer/load-balancer-outbound-connections.md)를 참조하세요.

공용 IP 주소는 VM 또는 인터넷에 연결된 부하 분산 장치에 할당할 수 있으며, 개인 IP 주소는 VM 및 내부 부하 분산 장치에 할당할 수 있습니다. 네트워크 인터페이스를 사용하여 IP 주소를 VM에 할당합니다.

IP 주소를 리소스에 할당하는 두 가지 방법, 즉 동적 또는 정적 할당 방법이 있습니다. 기본 할당 방법은 동적 방법이며, 여기서는 IP 주소를 만들 때 할당하지 않습니다. 대신 VM을 만들거나 중지된 VM을 시작할 때 P 주소를 할당합니다. VM을 중지하거나 삭제하면 IP 주소가 해제됩니다. 

VM의 IP 주소를 동일하게 유지하려면 정적 할당 방법을 명시적으로 설정하면 됩니다. 이 경우 IP 주소는 즉시 할당되며, VM을 삭제하거나 동적 할당 방법으로 변경할 때에만 해제됩니다.
    
다음 표에서는 IP 주소를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | 기본적으로 공용 IP 주소는 동적이며, VM을 중지하거나 삭제할 때 이와 연결된 주소가 변경될 수 있습니다. VM에서 항상 동일한 공용 IP 주소를 사용하도록 하려면 정적 공용 IP 주소를 만들어야 합니다. 기본적으로 포털은 VM을 만들 때 동적 개인 IP 주소를 NIC에 할당합니다. 이 IP 주소는 VM을 만든 후에 고정으로 변경할 수 있습니다.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Dynamic 또는 Static인 **-AllocationMethod** 매개 변수와 함께 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress)를 사용합니다. |
| [Azure CLI](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Dynamic 또는 Static인 **--allocation-method** 매개 변수와 함께 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip)를 사용합니다. |
| [템플릿](../articles/virtual-network/template-samples.md) | 템플릿을 사용하여 공용 IP 주소를 배포하기 위한 지침으로 [공용 IP 주소를 사용하는 Virtual Network의 네트워크 인터페이스](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet)를 사용합니다. |

공용 IP 주소를 만든 후에는 이 주소를 NIC에 할당하여 VM과 연결할 수 있습니다.

## <a name="virtual-network-and-subnets"></a>가상 네트워크 및 서브넷

서브넷은 VNet의 IP 주소 범위입니다. 조직 및 보안을 위해 VNet을 여러 서브넷으로 나눌 수 있습니다. VM의 각 NIC는 하나의 VNet에서 하나의 서브넷에 연결됩니다. VNet 내에서 동일하거나 다른 서브넷에 연결된 NIC는 별도의 구성 없이 서로 통신할 수 있습니다.

VNet을 설정할 때 사용 가능한 주소 공간과 서브넷을 포함하여 토폴로지를 지정합니다. VNet을 다른 VNets 또는 온-프레미스 네트워크에 연결하려면 중첩되지 않는 주소 범위를 선택해야 합니다. 개인 IP 주소이며 인터넷에서 액세스할 수 없습니다. 즉 10.0.0.0/8, 172.16.0.0/12 또는 192.168.0.0/16과 같이 라우팅할 수 없는 IP 주소에만 해당되었습니다. 이제 Azure에서는 모든 주소 범위를 VNet 내, 상호 연결된 VNet 내 및 온-프레미스 위치에서만 연결할 수 있는 프라이빗 VNet IP 주소 공간의 일부로 처리합니다. 

다른 사용자가 내부 네트워크를 담당하는 조직 내에서 작업하는 경우 주소 공간을 선택하기 전에 해당 사용자에게 문의해야 합니다. 즉, 주소가 겹치지 않는지 확인하고 사용하려는 공간을 알려 주어 동일한 IP 주소 범위를 사용하지 않도록 합니다. 

기본적으로 서브넷 간에는 보안 경계가 없기 때문에 이러한 각 서브넷의 VM 간에 서로 통신할 수 있습니다. 그러나 서브넷 간 및 VM 간의 트래픽 흐름을 제어할 수 있는 NSG(네트워크 보안 그룹)를 설정할 수 있습니다. 

다음 표에서는 VNet과 서브넷을 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.    

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/quick-create-portal.md) | Azure에서 VM을 만들 때 VNet을 만들도록 하는 경우 VNet 이름은 해당 VNet을 포함하는 리소스 그룹 이름과 **-vnet**의 조합입니다. 주소 공간은 10.0.0.0/24이고, 필수 서브넷 이름은 **default**이며, 서브넷 주소 범위는 10.0.0.0/24입니다. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) 및 [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 서브넷 및 VNet을 만듭니다. [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig)를 사용하여 기존 VNet에 서브넷을 추가할 수도 있습니다. |
| [Azure CLI](../articles/virtual-network/quick-create-cli.md) | 서브넷과 VNet은 동시에 만들어집니다. [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet)에 서브넷 이름으로 **--subnet-name** 매개 변수를 제공합니다. |
| 템플릿 | VNet 및 서브넷을 만드는 가장 쉬운 방법은 [두 개의 서브넷이 있는 Virtual Network](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)와 같은 기존 템플릿을 다운로드하고 요구에 맞게 수정하는 것입니다. |

## <a name="network-security-groups"></a>네트워크 보안 그룹

[NSG(네트워크 보안 그룹)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md)에는 서브넷, NIC 또는 둘 모두에 대한 네트워크 트래픽을 허용하거나 거부하는 ACL(Access Control 목록) 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 서브넷에 연결된 개별 NIC와 연결될 수 있습니다. NSG를 서브넷과 연결하는 경우 ACL 규칙이 해당 서브넷의 모든 VM에 적용됩니다. 또한 NSG를 NIC에 직접 연결하여 개별 NIC에 대한 트래픽을 제한할 수 있습니다.

NSG에는 인바운드 및 아웃바운드의 두 가지 규칙 집합이 포함되어 있습니다. 규칙에 대한 우선 순위는 각 집합 내에서 고유해야 합니다. 각 규칙에는 프로토콜, 원본 및 대상 포트 범위, 주소 접두사, 트래픽 방향, 우선 순위 및 액세스 유형에 관한 속성이 있습니다. 

모든 NSG에는 기본 규칙 집합이 포함됩니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다. 

NSG를 NIC에 연결하는 경우 NSG의 네트워크 액세스 규칙은 NIC에만 적용됩니다. NSG를 다중 NIC VM의 단일 NIC에 적용하는 경우 다른 NIC에 대한 트래픽에는 영향을 미치지 않습니다. NIC(또는 배포 모델에 따라 VM) 및 NIC 또는 VM에서 바인딩하는 서브넷에 다른 NSG를 연결할 수 있습니다. 우선 순위는 트래픽 방향에 따라 부여됩니다.

VM 및 VNet을 계획할 때는 NSG를 [계획](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md)해야 합니다.

다음 표에서는 NSG를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-network/tutorial-filter-network-traffic.md) | Azure Portal에서 VM을 만들 때 NSG가 자동으로 만들어지고 포털에서 만든 NIC에 연결됩니다. NSG 이름은 VM 이름과 **-nsg**의 조합입니다. 이 NSG에는 1000으로 설정된 우선 순위, RDP로 설정된 서비스, TCP로 설정된 프로토콜, 3389로 설정된 포트 및 Allow로 설정된 작업이 포함된 인바운드 규칙이 있습니다. VM에 대해 다른 인바운드 트래픽을 허용하려면 해당 NSG에 추가 규칙을 추가해야 합니다. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하고 필요한 규칙 정보를 제공합니다. [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 NSG를 만듭니다. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig)를 사용하여 NSG를 서브넷에 구성합니다. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 NSG를 VNet에 추가합니다. |
| [Azure CLI](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg)를 사용하여 NSG를 처음으로 만듭니다. [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule)를 사용하여 규칙을 NSG에 추가합니다. [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)를 사용하여 NSG를 서브넷에 추가합니다. |
| [템플릿](../articles/virtual-network/template-samples.md) | 템플릿을 사용하여 네트워크 보안 그룹을 배포하기 위한 지침으로 [네트워크 보안 그룹 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create)를 사용합니다. |

## <a name="load-balancers"></a>부하 분산 장치

[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md)는 애플리케이션에 고가용성 및 네트워크 성능을 제공합니다.  Load Balancer는 [들어오는 인터넷 트래픽을 VM에 분산](../articles/load-balancer/load-balancer-internet-overview.md)하거나 [VNet에서 VM 간의 트래픽 균형을 조정](../articles/load-balancer/load-balancer-internal-overview.md)하도록 구성할 수 있습니다. 또한 Load Balancer는 온-프레미스 네트워크의 온-프레미스 컴퓨터와 VM 간의 트래픽 균형을 조정하거나 외부 트래픽을 특정 VM으로 전달할 수도 있습니다.

부하 분산 장치는 부하 분산 장치의 공용 IP 주소 및 포트와 VM의 개인 IP 주소 및 포트 간에 들어오고 나가는 트래픽을 매핑합니다.

부하 분산 장치를 만들 때는 다음 구성 요소도 고려해야 합니다.

- **프런트 엔드 IP 구성** - 부하 분산 장치는 하나 이상의 프런트 엔드 IP 주소를 포함할 수 있습니다. 이러한 IP 주소는 트래픽에 대한 수신으로 사용됩니다.
- **백 엔드 주소 풀** - 부하가 분산되는 NIC와 연결된 IP 주소입니다.
- **[포트 전달](../articles/load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** - 인바운드 트래픽이 프런트 엔드 IP를 통과하고 인바운드 NAT 규칙을 활용하여 백 엔드 IP로 분산되는 방식을 정의합니다.
- **부하 분산 장치 규칙** - 지정된 프런트 엔드 IP와 포트의 조합을 백 엔드 IP 주소와 포트의 조합 집합에 매핑합니다. 부하 분산 장치 하나에 여러 부하 분산 규칙이 포함될 수 있습니다. 각 규칙은 VM과 연결된 백 엔드 IP와 포트 및 프런트 엔드 IP와 포트의 조합입니다.
- **[프로브](../articles/load-balancer/load-balancer-custom-probe-overview.md)** - VM의 상태를 모니터링합니다. 프로브에서 응답하지 않으면 부하 분산 장치에서 비정상 VM에 대한 새 연결을 보내지 않습니다. 기존 연결은 영향을 받지 않으며, 새 연결은 정상 VM으로 보내집니다.
- **[아웃바운드 규칙](../articles/load-balancer/load-balancer-outbound-rules-overview.md)** - 아웃바운드 규칙은 표준 Load Balancer의 백 엔드 풀에서 식별된 모든 가상 머신 또는 인스턴스의 아웃바운드 NAT(Network Address Translation)를 프런트 엔드로 변환하도록 구성합니다.

다음 표에서는 인터넷 연결 부하 분산 장치를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| Azure portal |  [Azure Portal을 사용하여 인터넷 트래픽 부하를 VM에 분산](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md)할 수 있습니다. |
| [Azure PowerShell](/azure/load-balancer/load-balancer-get-started-ilb-arm-ps) | **-PublicIpAddress** 매개 변수와 함께 [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 이전에 만든 공용 IP 주소의 식별자를 제공합니다. [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀의 구성을 만듭니다. [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)를 사용하여 사용자가 만든 프런트 엔드 IP 구성과 연결되는 인바운드 NAT 규칙을 만듭니다. [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig)를 사용하여 필요한 프로브를 작성합니다. [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 구성을 만듭니다. [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer)를 사용하여 부하 분산 장치를 만듭니다.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | [az network lb create](https://docs.microsoft.com/cli/azure/network/lb)를 사용하여 초기 부하 분산 장치 구성을 작성합니다. [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip)를 사용하여 이전에 만든 공용 IP 주소를 추가합니다. [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool)를 사용하여 백 엔드 주소 풀의 구성을 추가합니다. [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule)를 사용하여 NAT 규칙을 추가합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 추가합니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe)를 사용하여 프로브를 추가합니다. |
| [템플릿](../articles/load-balancer/quickstart-load-balancer-standard-public-template.md) | 템플릿을 사용하여 부하 분산 장치를 배포하기 위한 지침으로 [부하 분산 장치의 2개 VM 및 LB에 NAT 규칙 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create)(영문)을 사용합니다. |
    
다음 표에서는 내부 부하 분산 장치를 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| Azure portal | [Azure Portal에서 부하 분산 장치로 내부 트래픽을 부하 분산](../articles/load-balancer/tutorial-load-balancer-standard-internal-portal.md)할 수 있습니다. |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | **-PrivateIpAddress** 매개 변수와 함께 [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 개인 IP 주소를 네트워크 서브넷에 제공합니다. [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀의 구성을 만듭니다. [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig)를 사용하여 사용자가 만든 프런트 엔드 IP 구성과 연결되는 인바운드 NAT 규칙을 만듭니다. [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig)를 사용하여 필요한 프로브를 작성합니다. [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 구성을 만듭니다. [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer)를 사용하여 부하 분산 장치를 만듭니다.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) 명령을 사용하여 초기 부하 분산 장치 구성을 만듭니다. **--private-ip-address** 매개 변수와 함께 [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip)를 사용하여 개인 IP 주소를 정의합니다. [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool)를 사용하여 백 엔드 주소 풀의 구성을 추가합니다. [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule)를 사용하여 NAT 규칙을 추가합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 추가합니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe)를 사용하여 프로브를 추가합니다.|
| [템플릿](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | 템플릿을 사용하여 부하 분산 장치를 배포하기 위한 지침으로 [부하 분산 장치의 2개 VM 및 LB에 NAT 규칙 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)(영문)을 사용합니다. |

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

부하 분산 장치 및 가상 머신 확장 집합에 대한 자세한 내용은 [Azure 가상 머신 확장 집합에 대한 네트워킹](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)을 참조하세요.

## <a name="vms"></a>VM

VM은 동일한 VNet에 만들 수 있으며 개인 IP 주소를 사용하여 서로 연결할 수 있습니다. 게이트웨이를 구성하거나 공용 IP 주소를 사용할 필요 없이 다른 서브넷에 있더라도 VM을 연결할 수 있습니다. VM을 VNet에 배치하려면 VNet을 만든 다음 각 VM을 만들 때 해당 VNet과 서브넷에 할당합니다. VM은 배포 또는 시작 중에 네트워크 설정을 가져옵니다.  

VM을 배포할 때 IP 주소가 해당 VM에 할당됩니다. 여러 VM을 VNet 또는 서브넷에 배포하는 경우 부팅할 때 IP 주소가 할당됩니다. VM에 정적 IP를 할당할 수도 있습니다. 정적 IP를 할당하는 경우 실수로 다른 VM의 정적 IP를 다시 사용하지 않도록 특정 서브넷을 사용하는 것이 좋습니다.  

VM을 만들고 나중에 해당 VM을 VNet으로 마이그레이션하려는 경우 단순히 구성만 변경하는 것이 아니라 해당 VM을 VNet에 다시 배포해야 합니다. 다시 배포하는 가장 쉬운 방법은 VM을 삭제하고 연결된 디스크를 제거하지 않은 채 VNet의 원본 디스크를 사용하여 VM을 다시 만드는 것입니다. 

다음 표에서는 VNet에서 VM을 만드는 데 사용할 수 있는 방법을 나열하고 있습니다.

| 방법 | Description |
| ------ | ----------- |
| [Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md) | 단일 NIC를 사용하는 VM을 만들기 위해 앞에서 설명한 기본 네트워크 설정을 사용합니다. 여러 NIC를 사용하는 VM을 만들려면 다른 방법을 사용해야 합니다. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)를 사용하여 이전에 만든 NIC를 VM 구성에 추가합니다. |
| [Azure CLI](../articles/virtual-machines/linux/create-cli-complete.md) | VM을 만들고 개별 단계로 작성되는 Vnet, 서브넷 및 NIC에 연결합니다. |
| [템플릿](../articles/virtual-machines/windows/ps-template.md) | 템플릿을 사용하여 VM을 배포하기 위한 지침으로 [매우 간단한 Windows VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)(영문)를 사용합니다. |

## <a name="next-steps"></a>다음 단계
VM용 Azure 가상 네트워크를 관리하는 방법에 대한 VM별 단계는 [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) 또는 [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md) 자습서를 참조하세요.

또한 VM 부하를 분산하고 항상 사용 가능한 [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) 또는 [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md)용 애플리케이션을 만드는 방법에 대한 자습서도 있습니다.

- [사용자 정의 경로 및 IP 전달](../articles/virtual-network/virtual-networks-udr-overview.md)을 구성하는 방법에 대해 알아봅니다. 
- [VNet 간 연결](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 구성하는 방법에 대해 알아봅니다.
- [경로 문제 해결](../articles/virtual-network/diagnose-network-routing-problem.md) 방법에 대해 알아봅니다.
- [가상 머신 네트워크 대역폭](../articles/virtual-network/virtual-machine-network-throughput.md)에 대해 자세히 알아봅니다.
