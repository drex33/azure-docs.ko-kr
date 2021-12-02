---
title: '자습서: Azure PowerShell을 사용하여 지역 간 Load Balancer 만들기'
titleSuffix: Azure Load Balancer
description: Azure PowerShell을 사용하여 지역 간 Azure Load Balancer를 배포하는 이 자습서를 시작하세요.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3718cbacedf6f8770d0b7d1c83eb4035cb5676b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784853"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 지역 간 Azure Load Balancer 만들기

지역 간 부하 분산 장치는 여러 Azure 지역에서 서비스를 전역적으로 사용할 수 있도록 합니다. 한 지역이 실패하는 경우 트래픽이 다음의 가장 가까운 정상적인 지역 부하 분산 장치로 라우팅됩니다.  

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 지역 간 부하 분산 장치 만들기
> * 부하 분산 장치 규칙 만들기
> * 두 개의 지역 부하 분산 장치를 포함하는 백 엔드 풀 만들기
> * 부하 분산 장치 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 두 개의 서로 다른 Azure 지역에 배포된 백 엔드 풀을 사용하는 두 개의 **표준** SKU Azure Load Balancer
    - 지역 표준 부하 분산 장치 및 백 엔드 풀에 대한 가상 머신을 만드는 방법에 대한 자세한 내용은 [빠른 시작: Azure PowerShell을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-powershell.md)를 참조하세요.
        - 각 지역의 부하 분산 장치 및 가상 머신의 이름을 **-R1** 및 **-R2** 를 사용하여 추가합니다. 
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell.


PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-cross-region-load-balancer"></a>지역 간 부하 분산 장치 만들기


### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>영역 간 부하 분산 장치 리소스 만들기

이 섹션에서는 지역 간 부하 분산 장치에 필요한 리소스를 만듭니다.

지역간 부하 분산 장치의 프런트 엔드에는 글로벌 표준 sku 공용 IP가 사용됩니다.

* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다.

* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 다음과 같은 프런트 엔드 IP 구성을 만듭니다.

* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다.

* [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 지역 간 부하 분산 장치를 만듭니다.

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>백 엔드 풀 구성

이 섹션에서는 지역 간 부하 분산 장치의 백 엔드 풀에 두 개의 지역 표준 부하 분산 장치를 추가합니다.

> [!IMPORTANT]
> 이러한 단계를 완료하려면 백 엔드 풀을 사용하는 두 개의 지역 부하 분산 장치가 구독에 배포되어 있는지 확인합니다.  자세한 내용은 **[빠른 시작: Azure PowerShell을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-powershell.md)** 를 참조하세요.

* [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) 및 [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig)를 사용하여 지역 부하 분산 장치 정보를 변수에 저장합니다.

* [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig)를 사용하여 부하 분산 장치에 대한 백 엔드 주소 풀 구성을 만듭니다.

* [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool)을 사용하여 지역 부하 분산 장치 프런트 엔드를 지역 간 백 엔드 풀에 추가합니다.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

이 섹션에서는 지역 간 부하 분산 장치를 테스트합니다. 웹 브라우저에서 공용 IP 주소에 연결합니다.  지역 부하 분산 장치 백 엔드 풀 중 하나에서 가상 머신을 중지하고 장애 조치(failover)를 관찰합니다.

1. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

3. 지역 부하 분산 장치 중 하나의 백 엔드 풀에서 가상 머신을 중지합니다.

4. 웹 브라우저를 새로 고치고 다른 지역 부하 분산 장치에 대한 연결의 장애 조치(failover)를 관찰합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 나머지 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

* 글로벌 IP 주소를 만들었습니다.
* 지역 간 부하 분산 장치 만들기
* 부하 분산 규칙 만들기
* 지역 간 부하 분산 장치의 백 엔드 풀에 지역 부하 분산 장치 추가
* 부하 분산 장치를 테스트했습니다.


아래 내용에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [가용성 영역 간 VM 부하 분산 장치](./quickstart-load-balancer-standard-public-portal.md)