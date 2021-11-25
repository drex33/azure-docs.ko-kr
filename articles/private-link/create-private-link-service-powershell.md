---
title: '빠른 시작: Azure PowerShell을 사용하여 Azure 프라이빗 링크 서비스 만들기'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure 프라이빗 링크 서비스를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: bbc51f21d7f9dd76fef6d3e34e97c2581f1b2dcb
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133057521"
---
# <a name="quickstart-create-a-private-link-service-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Private Link 서비스 만들기

서비스를 참조하는 Private Link 서비스 만들기를 시작합니다.  Azure 표준 Load Balancer 배후에 배포된 서비스 또는 리소스에 대한 Private Link 액세스를 제공합니다.  서비스 사용자는 가상 네트워크에서 비공개로 액세스할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```

## <a name="create-an-internal-load-balancer"></a>내부 부하 분산 장치 만들기

이 섹션에서는 가상 네트워크 및 내부 Azure Load Balancer를 만듭니다.

### <a name="virtual-network"></a>가상 네트워크

이 섹션에서는 Private Link 서비스에 액세스하는 부하 분산 장치를 호스팅할 가상 네트워크와 서브넷을 만듭니다.

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

* 프런트 엔드 IP 풀에 대해 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 이 IP는 부하 분산 장치에서 들어오는 트래픽을 수신합니다.

* 부하 분산 장치의 프런트 엔드에서 보낸 트래픽에 대해 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. 이 풀은 백 엔드 가상 머신을 배포하는 위치입니다.

* 백 엔드 VM 인스턴스의 상태를 결정하는 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 상태 프로브를 만듭니다.

* 트래픽을 VM에 분산하는 방법을 정의하는 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 공용 부하 분산 장치를 만듭니다.


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기

이 섹션에서는 이전 단계에서 만든 표준 Azure Load Balancer를 사용하는 프라이빗 링크 서비스를 만듭니다.

* [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig)를 사용하여 프라이빗 링크 서비스 IP 구성을 만듭니다.

* [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice)를 사용하여 프라이빗 링크 서비스를 만듭니다.

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

프라이빗 링크 서비스가 생성되고 트래픽을 수신할 수 있습니다. 트래픽 흐름을 보려면 표준 부하 분산 장치 뒤에 애플리케이션을 구성합니다.

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 프라이빗 링크 서비스를 프라이빗 엔드포인트에 매핑합니다. 가상 네트워크는 프라이빗 링크 서비스에 대한 프라이빗 엔드포인트를 포함합니다. 이 가상 네트워크에는 프라이빗 링크 서비스에 액세스하는 리소스가 포함되어 있습니다.

### <a name="create-private-endpoint-virtual-network"></a>프라이빗 엔드포인트 가상 네트워크 만들기

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>엔드포인트 및 연결 만들기

* [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice)를 사용하여 초기에 만든 프라이빗 링크 서비스의 구성을 나중에 사용할 수 있도록 변수에 배치합니다.

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection)을 사용하여 연결 구성을 만듭니다.

* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)를 사용하여 엔드포인트를 만듭니다.



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인

이 섹션에서는 이전 단계에서 만든 연결을 승인합니다.

* [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection)을 사용하여 연결을 승인합니다.

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>프라이빗 엔드포인트의 IP 주소

이 섹션에서는 부하 분산 장치 및 프라이빗 링크 서비스에 해당하는 프라이빗 엔드포인트의 IP 주소를 찾을 수 있습니다.

* [Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint)를 사용하여 IP 주소를 검색합니다.

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 나머지 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음 작업을 수행했습니다.

* 가상 네트워크 및 내부 Azure Load Balancer를 만들었습니다.
* 프라이빗 링크 서비스를 만들었습니다.

Azure 프라이빗 엔드포인트에 대해 자세히 알아보려면 계속 진행하세요.
> [!div class="nextstepaction"]
> [빠른 시작: Azure Powershell을 사용하여 Private Endpoint 만들기](create-private-endpoint-powershell.md)
