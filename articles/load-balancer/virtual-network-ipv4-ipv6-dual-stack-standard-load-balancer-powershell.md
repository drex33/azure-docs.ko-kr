---
title: IPv6 이중 스택 애플리케이션 배포 - 표준 Load Balancer - PowerShell
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Powershell을 사용하여 Azure 가상 네트워크에서 표준 Load Balancer로 IPv6 이중 스택 애플리케이션을 배포하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2020
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e4bc7f20815e75214fb3ce6bc0c4e2917227375
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373028"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell"></a>Azure에서 IPv6 이중 스택 애플리케이션 배포 - PowerShell

이 문서에서는 이중 스택 가상 네트워크 및 서브넷, 이중(IPv4 + IPv6) 프런트 엔드 구성이 포함된 표준 Load Balancer, 이중 IP 구성이 있는 NIC를 사용하는 VM, 네트워크 보안 그룹 및 공용 IP를 포함하는 Azure의 표준 Load Balancer를 사용하여 이중 스택(IPv4 + IPv6) 애플리케이션을 배포하는 방법을 보여 줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이중 스택 가상 네트워크를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *미국 동부* 위치에 *myRGDualStack* 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 및 IPv6 공용 IP 주소 만들기
인터넷에서 가상 머신에 액세스하려면 부하 분산 장치에 대한 IPv4 및 IPv6 공용 IP 주소가 필요합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다. 다음 예에서는 *dsRG1* 리소스 그룹에 *dsPublicIP_v4* 및 *dsPublicIP_v6* 이라는 IPv4 및 IPv6 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4 `
  -Sku Standard
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6 `
  -Sku Standard
```
RDP 연결을 사용하여 가상 머신에 액세스하려면 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 가상 머신에 대한 IPV4 퍼블릭 IP 주소를 만듭니다.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -Sku Standard `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Static `
   -Sku Standard `
   -IpAddressVersion IPv4
```

## <a name="create-standard-load-balancer"></a>표준 Load Balancer 만들기

이 섹션에서는 부하 분산 장치의 이중 프런트 엔드 IP(IPv4 및 IPv6) 및 백 엔드 주소 풀을 구성한 다음, 표준 Load Balancer를 만듭니다.

### <a name="create-front-end-ip"></a>프런트 엔드 IP 만들기

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP를 만듭니다. 다음 예제에서는 *dsLbFrontEnd_v4* 및 *dsLbFrontEnd_v6* 라는 IPv4 및 IPv6 프런트 엔드 IP 구성을 만듭니다.

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>백 엔드 주소 풀 구성

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. VM은 나머지 단계에서 이 백 엔드 풀에 연결됩니다. 다음 예제에서는 IPV4 및 IPv6 NIC 구성을 모두 사용하여 VM을 포함하도록 *dsLbBackEndPool_v4* 및 *dsLbBackEndPool_v6* 라는 백 엔드 주소 풀을 만듭니다.

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>상태 프로브 만들기
[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)를 사용하여 VM 상태를 모니터링하는 상태 프로브를 만듭니다.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 수신할 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. 정상 VM만 트래픽을 수신하도록 하려면 필요에 따라 상태 프로브도 정의할 수 있습니다. 기본 부하 분산 장치는 IPv4 프로브를 사용하여 VM의 IPv4 및 IPv6 엔드포인트 둘 다의 상태를 평가합니다. 표준 부하 분산 장치는 명시적으로 IPv6 상태 프로브를 지원합니다.

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예에서는 *dsLBrule_v4* 및 *dsLBrule_v6* 이라는 부하 분산 장치 규칙을 만들고 IPv4 및 IPv6 프런트 엔드 IP 구성에 대한 *TCP* 포트 *80* 의 트래픽을 분산합니다.

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
   -probe $probe
```

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)를 사용하여 표준 Load Balancer를 만듭니다. 다음 예제는 이전 단계에서 만든 IPv4 및 IPv6 프런트 엔드 IP 구성, 백 엔드 풀, 부하 분산 규칙을 사용하여 *myLoadBalancer* 라는 퍼블릭 표준 Load Balancer를 만듭니다.

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Standard" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6 `
-Probe $probe
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기
일부 VM을 배포하고 부하 분산 장치를 테스트하려면 먼저 지원되는 네트워크 리소스(가용성 집합, 네트워크 보안 그룹, 가상 네트워크 및 가상 NIC)를 만들어야 합니다. 
### <a name="create-an-availability-set"></a>가용성 집합 만들기
앱의 고가용성을 향상시키려면 VM을 가용성 집합에 배치합니다.

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet* 이라는 가용성 집합을 만듭니다.

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>네트워크 보안 그룹 만들기

VNET에서 인바운드 및 아웃바운드 통신을 제어하는 규칙에 대한 네트워크 보안 그룹을 만듭니다.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>포트 3389에 대한 네트워크 보안 그룹 규칙 만들기

포트 3389를 통해 RDP 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>포트 80에 대한 네트워크 보안 그룹 규칙 만들기

포트 80을 통해 인터넷 연결을 허용하도록 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *mySubnet* 을 사용하여 *dsVnet* 이라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>NIC 만들기

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 가상 NIC를 만듭니다. 다음 예제에서는 IPv4 및 IPv6 구성을 사용하여 두 개의 가상 NIC를 만듭니다. (다음 단계에서 앱에 대해 만드는 각 VM에 대해 가상 NIC 하나씩)

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 2개의 VM 및 아직 없는 경우 필요한 가상 네트워크 구성 요소를 만듭니다. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>IPv4 및 IPv6 엔드포인트의 IP 주소 확인
`get-AzNetworkInterface`를 사용하여 이 배포에 사용되는 IP를 요약하려면 리소스 그룹의 모든 네트워크 인터페이스 개체를 가져옵니다. 또한 `get-AzpublicIpAddress`를 사용하여 IPv4 및 IPv6 엔드포인트의 Load Balancer 프런트 엔드 주소를 가져옵니다.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
다음 그림은 두 VM의 프라이빗 IPv4 및 IPv6 주소와 Load Balancer의 프런트 엔드 IPv4 및 IPv6 IP 주소를 나열하는 샘플 출력을 보여 줍니다.

![Azure의 이중 스택(IPv4/IPv6) 애플리케이션 배포에 대한 IP 요약](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기
다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에 *dsVnet* 을 입력합니다.
2. 검색 결과에서 표시되는 **dsVnet** 을 선택합니다. 그러면 *dsVnet* 이라는 이중 스택 가상 네트워크의 **개요** 페이지가 시작됩니다. 이중 스택 가상 네트워크에서는 *dsSubnet* 이라는 이중 스택 서브넷에 IPv4 및 IPv6가 모두 구성된 두 개의 NIC가 표시됩니다.

  ![Azure의 IPv6 이중 스택 가상 네트워크](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 프런트 엔드 IP 구성(IPv4 및 IPv6)으로 표준 Load Balancer를 만들었습니다. 또한 부하 분산 장치의 백 엔드 풀에 추가된 이중 IP(IPV4 + IPv6)가 구성된 NIC가 포함된 두 개의 가상 머신을 만들었습니다. Azure virtual network의 IPv6 지원에 대 한 자세한 내용은 [Azure Virtual Network에 대 한 i p v 6 이란?]를 참조 하세요. ((.. /virtual-network/ip-services/ipv6-overview.md)
