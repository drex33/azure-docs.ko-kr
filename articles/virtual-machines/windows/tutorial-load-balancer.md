---
title: 자습서 - Azure에서 Windows 가상 머신의 부하 분산
description: 이 자습서에서는 Azure PowerShell을 사용하여 세 Windows 가상 머신에서 고가용성의 안전한 애플리케이션을 위한 부하 분산 장치를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1becf1dde4c094bc3a904dee2945640f4d2db01a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690109"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>자습서: Azure PowerShell을 통해 Azure에서 Windows 가상 머신의 부하를 분산하여 고가용성 애플리케이션을 만드는 방법
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

부하 분산은 들어오는 요청을 여러 가상 머신에 분산하여 높은 수준의 가용성을 제공합니다. 이 자습서에서는 트래픽을 분산하고 고가용성을 제공하는 Azure Load Balancer의 여러 다른 구성 요소에 대해 알아봅니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Load Balancer 만들기
> * 부하 분산 장치 상태 프로브 만들기
> * 부하 분산 장치 트래픽 규칙 만들기
> * 사용자 지정 스크립트 확장을 사용하여 기본 IIS 사이트 만들기
> * 가상 머신 만들기 및 부하 분산 장치에 연결
> * 부하 분산 장치의 실제 동작 보기
> * 부하 분산 장치에서 VM 추가 및 제거

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer 개요
Azure Load Balancer는 들어오는 트래픽을 정상 VM 간에 분산하여 고가용성을 제공하는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다.

하나 이상의 공용 IP 주소를 포함하는 프런트 엔드 IP 구성을 정의할 수 있습니다. 이 프런트 엔드 IP 구성을 사용하여 인터넷을 통해 부하 분산 장치 및 애플리케이션에 액세스하도록 할 수 있습니다. 

가상 머신은 가상 NIC(네트워크 인터페이스 카드)를 사용하여 부하 분산 장치에 연결합니다. VM으로 트래픽을 분산하기 위해 백 엔드 주소 풀에 부하 분산 장치에 연결된 가상(NIC)의 주소가 포함됩니다.

트래픽 흐름을 제어하려면 VM에 매핑되는 특정 포트 및 프로토콜에 대해 부하 분산 장치 규칙을 정의합니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-azure-load-balancer"></a>Azure Load Balancer 만들기
이 섹션에서는 부하 분산 장치의 각 구성 요소를 만들고 구성하는 방법을 자세히 설명합니다. 부하 분산 장치를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myResourceGroupLoadBalancer* 라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
인터넷에서 앱에 액세스하려면 부하 분산 장치에 대한 공용 IP 주소가 필요합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 *myResourceGroupLoadBalancer* 리소스 그룹에 *myPublicIP* 라는 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>부하 분산 장치 만들기
[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP 풀을 만듭니다. 다음 예제에서는 *myFrontEndPool* 이라는 프런트 엔드 IP 풀을 만들고 *myPublicIP* 주소를 연결합니다. 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결합니다. 다음 예제는 *myBackEndPool* 이라는 백 엔드 주소 풀을 만듭니다.

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

이제 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제에서는 이전 단계에서 만든 프런트 엔드 및 백 엔드 IP 풀을 사용하여 *myLoadBalancer* 라는 부하 분산 장치를 만듭니다.

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>상태 프로브 만들기
부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다. 앱의 프로토콜 또는 특정 상태 확인 페이지에 따라 상태 프로브를 만듭니다. 

다음 예제에서는 TCP 프로브를 만듭니다. 좀 더 미세 조정된 상태 검사를 위해 사용자 지정 HTTP 프로브를 만들 수도 있습니다. 사용자 지정 HTTP 프로브를 사용할 경우 *healthcheck.aspx* 와 같은 상태 확인 페이지를 만들어야 합니다. 부하 분산 상태가 호스트를 순환 상태를 유지하려면 프로브는 **HTTP 200 정상** 응답을 반환해야 합니다.

TCP 상태 프로브를 만들려면 [Add-Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용합니다. 다음 예제에서는 *TCP* 포트 *80* 에서 각 VM을 모니터링하는 *myHealthProbe* 라는 상태 프로브를 만듭니다.

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

상태 프로브를 적용하려면 [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)를 사용하여 부하 분산 장치를 업데이트합니다.

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기
부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 사용할 상태 프로브도 정의합니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 *myLoadBalancerRule* 이라는 부하 분산 장치 규칙을 만들고 *TCP* 포트 *80* 에서 트래픽 부하를 분산합니다.

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)를 사용하여 부하 분산 장치를 업데이트합니다.

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>가상 네트워크 구성
일부 VM을 배포하고 부하 분산 장치를 테스트하려면 지원하는 가상 네트워크 리소스를 만듭니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 관리](tutorial-virtual-network.md) 자습서를 참조하세요.

### <a name="create-network-resources"></a>네트워크 리소스 만들기
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *myVnet* 이라는 가상 네트워크와 *mySubnet* 을 만듭니다.

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 가상 NIC를 만듭니다. 다음 예제에서는 3개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩) 언제든지 추가 가상 NIC 및 VM을 만든 후 부하 분산 장치에 추가할 수 있습니다.

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>가상 머신 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet* 이라는 가용성 집합을 만듭니다.

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 3개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다.

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

`-AsJob` 매개 변수는 VM을 백그라운드 작업으로 만들므로 PowerShell 프롬프트가 반환됩니다. `Job` cmdlet을 사용하여 백그라운드 작업의 세부 정보를 볼 수 있습니다. 3개의 VM을 만들고 구성하는 데 몇 분 정도 걸립니다.


### <a name="install-iis-with-custom-script-extension"></a>사용자 지정 스크립트 확장을 사용하여 IIS 설치
[Windows 가상 머신을 사용자 지정하는 방법](tutorial-automate-vm-deployment.md)에 대한 이전 자습서에서 Windows용 사용자 지정 스크립트 확장을 사용하여 VM 사용자 지정을 자동화하는 방법을 배웠습니다. 동일한 접근 방법을 사용하여 VM에서 IIS를 설치하고 구성할 수 있습니다.

[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 확장은 `powershell Add-WindowsFeature Web-Server`를 실행하여 IIS 웹 서버를 설치한 다음 *Default.htm* 페이지를 업데이트하여 VM의 호스트 이름을 표시합니다.

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>부하 분산 장치 테스트
[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIP* 의 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 웹 사이트가 표시됩니다.

![실행 중인 IIS 웹 사이트](./media/tutorial-load-balancer/running-iis-website.png)

앱이 실행되는 3개의 모든 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다.


## <a name="add-and-remove-vms"></a>VM 추가 및 제거
앱이 실행되는 동안 OS 업데이트와 같은 유지 관리 작업을 VM에서 수행해야 할 수 있습니다. 앱에 대해 증가된 트래픽을 처리하기 위해 VM을 더 추가해야 할 수 있습니다. 이 섹션에서는 부하 분산 장치에서 VM을 추가 또는 제거하는 방법을 보여 줍니다.

### <a name="remove-a-vm-from-the-load-balancer"></a>부하 분산 장치에서 VM 제거
[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)를 사용하여 네트워크 인터페이스 카드를 가져오고 가상 NIC의 *LoadBalancerBackendAddressPools* 속성을 *$null* 로 설정합니다. 마지막으로 가상 NIC를 업데이트합니다.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

앱이 실행되는 나머지 2개의 VM에서 부하 분산 장치가 트래픽을 분산하는 것을 확인하기 위해 웹 브라우저를 강제로 새로 고칠 수 있습니다. 이제 OS 업데이트 설치 또는 VM 다시 부팅을 수행 등의 유지 관리 작업을 VM에서 수행할 수 있습니다.

### <a name="add-a-vm-to-the-load-balancer"></a>부하 분산 장치에 VM 추가
VM 유지 관리를 수행한 후에 또는 용량을 확장해야 하는 경우 가상 NIC의 *LoadBalancerBackendAddressPools* 속성을 [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer)에서 *BackendAddressPool* 로 설정합니다.

부하 분산 장치를 가져옵니다.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 부하 분산 장치를 만들고 VM에 연결했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Load Balancer 만들기
> * 부하 분산 장치 상태 프로브 만들기
> * 부하 분산 장치 트래픽 규칙 만들기
> * 사용자 지정 스크립트 확장을 사용하여 기본 IIS 사이트 만들기
> * 가상 머신 만들기 및 부하 분산 장치에 연결
> * 부하 분산 장치의 실제 동작 보기
> * 부하 분산 장치에서 VM 추가 및 제거

VM 네트워킹을 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM 및 가상 네트워크 관리](./tutorial-virtual-network.md)
