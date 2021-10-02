---
title: Azure Virtual Network에서 IPv6에 IPv4 애플리케이션 추가 - PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure PowerShell을 사용하여 Azure 가상 네트워크의 기존 응용 프로그램에 IPv6 주소를 배포하는 방법을 설명합니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 127206a092c00f3496fe11c09a46e20c248a9820
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373064"
---
# <a name="add-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Azure 가상 네트워크에서 IPv6에 IPv4 애플리케이션 추가 - PowerShell

이 문서에서는 표준 부하 분산 장치 및 공용 IP를 사용하여 Azure 가상 네트워크의 기존 IPv4 응용 프로그램에 IPv6 연결을 추가하는 방법을 설명합니다. 현재 위치 업그레이드에는 다음이 포함됩니다.
- 가상 네트워크 및 서브넷에 대한 IPv6 주소 공간
- IPv4 및 IPV6 프런트 엔드 구성이 모두 있는 표준 Load Balancer
- IPv4 + IPv6 구성이 모두 있는 NIC가 있는 VM
- 부하 분산 장치에 인터넷 연결 IPv6 연결이 있도록 하는 IPv6 공용 IP

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [빠른 시작: 표준 부하 분산 장치 만들기 - Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md)에 설명된 대로 표준 부하 분산 장치를 배포했다고 가정합니다.

## <a name="retrieve-the-resource-group"></a>리소스 그룹을 검색합니다.

이중 스택 가상 네트워크를 만들려면 먼저 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)을 사용하여 리소스 그룹을 검색해야 합니다.

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP 주소 만들기

표준 부하 분산 장치에 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 를 사용하여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *PublicIP_v6* 라는 IPv6 공용 IP 주소를 *myResourceGroupSLB* 리소스 그룹에 만듭니다.

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>부하 분산 장치 프런트 엔드 구성

기존 부하 분산 장치 구성을 검색한 다음 다음과 같이 [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) 를 사용하여 새 IPv6 IP 주소를 추가합니다.

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀 구성

부하 분산 장치 구성의 로컬 복사본에 백 엔드 풀을 만들고 다음과 같이 새 백 엔드 풀 구성으로 실행 중인 부하 분산 장치를 업데이트합니다.

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>부하 분산 장치 규칙 구성
기존 부하 분산 장치 프런트 엔드 및 백 엔드 풀 구성을 검색한 다음, [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)를 사용하여 새 부하 분산 규칙을 추가합니다.

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 주소 범위 추가

다음과 같이 VM을 호스팅하는 가상 네트워크 및 서브넷에 IPv6 주소 범위를 추가합니다.

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>NIC에 IPv6 구성 추가

다음과 같이 [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) 를 사용하여 IPv6 주소로 모든 VM NIC를 구성합니다.

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기

다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에 *myVnet* 을 입력합니다.
2. **myVnet** 이 검색 결과에 표시되면 이를 선택합니다. 그러면 **개요** 페이지가 *myVNet* 이라는 이중 스택 가상 네트워크에서 시작됩니다. 이중 스택 가상 네트워크에는 *mySubnet* 이라는 이중 스택 서브넷에 IPv4 및 IPv6 구성이 둘 다 있는 세 개의 NIC가 표시됩니다.

  ![Azure의 IPv6 이중 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용하여 기존 표준 부하 분산 장치를 이중 스택(IPv4 및 IPv6) 구성으로 업데이트했습니다. 또한 백 엔드 풀에 있는 VM의 NIC 및 IPv6 구성을 호스팅하는 Virtual Network에 IPv6 구성을 추가했습니다. Azure 가상 네트워크의 IPv6 지원에 관해 자세히 알아보려면 [Azure 가상 네트워크용 IPv6란?](../virtual-network/ip-services/ipv6-overview.md) 을 참조하세요
