---
title: '자습서: 게이트웨이 부하 분산 장치 만들기 - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: 이 자습서에서 Azure PowerShell을 사용하여 게이트웨이 부하 분산 장치를 만드는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/17/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: d4210910dc1918eae5eb391c4f2e635d7176bc89
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758911"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 게이트웨이 부하 분산 장치 만들기

Azure Load Balancer는 표준, 기본 및 게이트웨이 SKU로 구성됩니다. 게이트웨이 부하 분산 장치는 NVA(네트워크 가상 어플라이언스)를 사용자가 인식하지 못하는 새 삽입하는 데 사용됩니다. NVA의 고성능 및 높은 확장성이 필요한 시나리오의 경우 게이트웨이 부하 분산 장치를 사용합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크를 만듭니다.
> * 네트워크 보안 그룹을 만듭니다.
> * 게이트웨이 부하 분산 장치를 만듭니다.
> * 부하 분산 장치 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다.

> [!IMPORTANT]
> 게이트웨이 Azure Load Balancer는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 기존 공용 표준 SKU Azure Load Balancer 부하 분산 장치 만들기에 대한 자세한 내용은 **[Azure PowerShell을 사용하여 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-powershell.md)** 를 참조하세요.
    - 이 자습서에서는 예제의 기존 부하 분산 장치의 이름을 **myLoadBalancer** 로 지정합니다.
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 다음 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorGwLB-rg' -Location 'eastus'

```

## <a name="create-virtual-network"></a>가상 네트워크 만들기

가상 네트워크는 게이트웨이 부하 분산 장치의 백 엔드 풀에 있는 리소스에 필요합니다. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. [New-AzBastion](/powershell/module/az.network/new-azbastion)을 사용하여 가상 네트워크의 리소스를 안전하게 관리하기 위한 베스천 호스트를 배포합니다.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="create-nsg"></a>NSG 만들기

다음 예제를 사용하여 네트워크 보안 그룹을 만듭니다. 이전에 만든 가상 네트워크의 네트워크 트래픽에 필요한 NSG 규칙을 구성합니다.

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 NSG에 대한 규칙을 만듭니다. [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 NSG를 만듭니다.

```azurepowershell-interactive
## Create rule for network security group and place in variable. ##
$nsgrule1 = @{
    Name = 'myNSGRule-AllowAll'
    Description = 'Allow all'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule1

$nsgrule2 = @{
    Name = 'myNSGRule-AllowAll-TCP-Out'
    Description = 'Allow all TCP Out'
    Protocol = 'TCP'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Outbound'
}
$rule2 = New-AzNetworkSecurityRuleConfig @nsgrule2

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    SecurityRules = $rule1,$rule2
}
New-AzNetworkSecurityGroup @nsg
```

## <a name="create-gateway-load-balancer"></a>게이트웨이 부하 분산 장치 만들기

이 섹션에서는 구성을 만들고 게이트웨이 부하 분산 장치를 배포합니다. [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 부하 분산 장치의 프런트 엔드 IP 구성을 만듭니다. 

[New-AzLoadBalancerTunnelInterface](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 부하 분산 장치의 두 터널 인터페이스를 만듭니다. 

NVA에 대한 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)을 사용하여 백 엔드 풀을 만듭니다. 

부하 분산 장치에서 백 엔드 인스턴스의 상태를 모니터링하려면 상태 프로브가 필요합니다. [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig)를 사용하여 상태 프로브를 만듭니다.

백 엔드 인스턴스로 향하는 트래픽은 부하 분산 규칙을 통해 라우팅됩니다. [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig)를 사용하여 부하 분산 규칙을 만듭니다.

부하 분산 장치를 만들고 배포하려면 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용합니다.

```azurepowershell-interactive
## Place virtual network configuration in a variable for later use. ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
}
$vnet = Get-AzVirtualNetwork @net

## Create load balancer frontend configuration and place in variable. ## 
$fe = @{
    Name = 'myFrontend'
    SubnetId = $vnet.subnets[0].id
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create backend address pool configuration and place in variable. ## 
$int1 = @{
    Type = 'Internal'
    Protocol = 'Vxlan'
    Identifier = '800'
    Port = '10800'
}
$tunnelInterface1 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int1

$int2 = @{
    Type = 'External'
    Protocol = 'Vxlan'
    Identifier = '801'
    Port = '10801'
}
$tunnelInterface2 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int2

$pool = @{
    Name = 'myBackendPool'
    TunnelInterface = $tunnelInterface1,$tunnelInterface2
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @pool

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
$para = @{
    Name = 'myLBRule'
    Protocol = 'All'
    FrontendPort = '0'
    BackendPort = '0'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
$rule = New-AzLoadBalancerRuleConfig @para

## Create the load balancer resource. ## 
$lb = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
    Location = 'eastus'
    Sku = 'Gateway'
    LoadBalancingRule = $rule
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
New-AzLoadBalancer @lb

```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>게이트웨이 부하 분산 장치 백 엔드 풀에 네트워크 가상 어플라이언스 추가
Azure Marketplace를 통해 NVA를 배포합니다. 배포되면 [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)를 사용하여 백엔드 풀에 가상 머신을 추가합니다.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>부하 분산 장치 프런트 엔드를 게이트웨이 부하 분산 장치에 연결

이 예제에서는 표준 부하 분산 장치의 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다. 

구독에 있는 기존 부하 분산 장치의 프런트 엔드 IP에 프런트 엔드를 추가합니다.

[Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig)를 사용하여 게이트웨이 부하 분산 장치 프런트 엔드를 기존 부하 분산 장치에 연결합니다.

```azurepowershell-interactive
## Place the gateway load balancer configuration into a variable. ##
$par1 = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
}
$gwlb = Get-AzLoadBalancer @par1

## Place the existing load balancer into a variable. ##
$par2 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @par2

## Place the existing public IP for the existing load balancer into a variable.
$par3 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}
$publicIP = Get-AzPublicIPAddress @par3

## Chain the gateway load balancer to your existing load balancer frontend. ##
$par4 = @{
    Name = 'myFrontEndIP'
    PublicIPAddress = $publicIP
    LoadBalancer = $lb
    GatewayLoadBalancerId = $gwlb.FrontendIpConfigurations.Id
}
$config = Set-AzLoadBalancerFrontendIpConfig @par4

$config | Set-AzLoadBalancer

```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 나머지 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorGwLB-rg'
```

## <a name="next-steps"></a>다음 단계

Azure에서 네트워크 가상 어플라이언스를 만듭니다. 

NVA를 만들 때 이 자습서에서 만든 리소스를 선택합니다.

* 가상 네트워크

* 서브넷

* 네트워크 보안 그룹

* 게이트웨이 부하 분산 장치

영역 간 Azure Load Balancer를 만드는 방법에 대해 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [영역 간 부하 분산 장치](tutorial-cross-region-powershell.md)
