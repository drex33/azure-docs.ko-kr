---
title: Azure NAT Gateway를 사용하여 SNAT 포트 스케일링
description: Azure Firewall을 NAT Gateway와 통합하여 SNAT 포트를 늘릴 수 있습니다.
services: firewall
author: jocortems
ms.service: firewall
ms.topic: how-to
ms.date: 04/23/2021
ms.author: jocorte
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2070ab097407e377df123260965d7066512c1153
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228754"
---
# <a name="scale-snat-ports-with-azure-nat-gateway"></a>Azure NAT Gateway를 사용하여 SNAT 포트 스케일링

Azure Firewall은 구성된 공용 IP 주소당 2,048개의 SNAT 포트를 제공하며 최대 [250개의 공용 IP 주소](./deploy-multi-public-ip-powershell.md)를 연결할 수 있습니다. 이 구성에서는 아키텍처 및 트래픽 패턴에 따라 사용 가능한 SNAT 포트가 512,000개 넘게 필요할 수 있습니다. Microsoft 365 앱과 통합되는 많은 [Windows Virtual Desktop 배포](./protect-azure-virtual-desktop.md)를 보호하는 데 사용하는 경우를 예로 들 수 있습니다.

많은 수의 공용 IP 주소를 사용하는 데 따르는 또 다른 문제는 다운스트림 IP 주소 필터링 요구 사항이 있는 경우 발생합니다. Azure Firewall은 연결에 사용할 원본 공용 IP 주소를 임의로 선택하므로 모든 공용 IP 주소에서 연결이 허용되어야 합니다. [공용 IP 주소 접두사](../virtual-network/ip-services/public-ip-address-prefix.md)를 사용하고 아웃바운드 SNAT 포트 요구 사항을 충족하도록 250개의 공용 IP 주소를 연결해야 하는 경우에도 16개의 공용 IP 주소 접두사를 만들고 연결을 허용해야 합니다.

아웃바운드 SNAT 포트를 스케일링하는 효율적인 방법은 [NAT Gateway 리소스](../virtual-network/nat-gateway/nat-overview.md)를 사용하는 것입니다. 공용 IP 주소당 64,000개의 SNAT 포트를 제공하고 최대 16개의 공용 IP 주소를 지원하므로 실질적으로 최대 1,024,000개의 아웃바운드 SNAT 포트를 제공합니다.

NAT Gateway 리소스가 Azure Firewall 서브넷과 연결되면 모든 아웃바운드 인터넷 트래픽에서 자동으로 NAT Gateway의 공용 IP 주소를 사용합니다. [사용자 정의 경로](../virtual-network/tutorial-create-route-table-portal.md)를 구성할 필요가 없습니다. 응답 트래픽은 Azure Firewall 공용 IP 주소를 사용하여 흐름의 균형을 유지합니다. NAT Gateway와 연결된 IP 주소가 여러 개인 경우 IP 주소가 임의로 선택됩니다. 사용할 주소를 지정할 수 없습니다.

이 아키텍처에서 NAT는 하나만 있습니다. Azure Firewall 인스턴스는 Azure Firewall 공용 IP 주소가 아닌 개인 IP 주소를 사용하여 NAT Gateway로 트래픽을 보냅니다.

> [!NOTE]
> [여러 가용성 영역에 Azure 방화벽](deploy-availability-zone-powershell.md)을 배포한 경우 현재 Azure NAT Gateway 사용은 Azure Firewall과 호환되지 않습니다. 자세한 내용은 [Azure NAT Gateway 및 가용성 영역](../virtual-network/nat-gateway/nat-gateway-resource.md#cross-zone-outbound-scenarios-not-supported)을 참조하세요.

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-powershell"></a>NAT Gateway와 Azure Firewall 서브넷 연결 - Azure PowerShell

다음 예제에서는 Azure PowerShell을 사용하여 NAT Gateway를 만들고 Azure Firewall 서브넷과 연결합니다.

```azurepowershell-interactive
# Create public IP addresses
New-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'
New-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'

# Create NAT gateway
$PublicIPAddress1 = Get-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg
$PublicIPAddress2 = Get-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg
New-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg -PublicIpAddress $PublicIPAddress1,$PublicIPAddress2 -Location 'South Central US' -Sku Standard

# Associate NAT gateway to subnet
$virtualNetwork = Get-AzVirtualNetwork -Name nat-vnet -ResourceGroupName nat-rg
$natGateway = Get-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg
$firewallSubnet = $virtualNetwork.subnets | Where-Object -Property Name -eq AzureFirewallSubnet
$firewallSubnet.NatGateway = $natGateway
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-cli"></a>NAT Gateway와 Azure Firewall 서브넷 연결 - Azure CLI

다음 예제에서는 Azure CLI를 사용하여 NAT Gateway를 만들고 Azure Firewall 서브넷과 연결합니다.

```azurecli-interactive
# Create public IP addresses
az network public-ip create --name public-ip-1 --resource-group nat-rg --sku standard
az network public-ip create --name public-ip-2 --resource-group nat-rg --sku standard

# Create NAT gateway
az network nat gateway create --name firewall-nat --resource-group nat-rg --public-ip-addresses public-ip-1 public-ip-2

# Associate NAT gateway to subnet
az network vnet subnet update --name AzureFirewallSubnet --vnet-name nat-vnet --resource-group nat-rg --nat-gateway firewall-nat
```

## <a name="next-steps"></a>다음 단계

- [NAT 게이트웨이 리소스를 사용하여 가상 네트워크 설계](../virtual-network/nat-gateway/nat-gateway-resource.md)