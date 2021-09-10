---
title: Azure PowerShell을 사용하여 일반화 이미지에서 확장 집합 만들기
description: PowerShell을 사용하여 Shared Image Gallery에서 일반화 이미지를 사용하는 확장 집합을 만듭니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 591ad83cf9beeff7b25ffa1234441109dc169aa2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691261"
---
# <a name="create-a-scale-set-from-a-generalized-image-using-powershell"></a>PowerShell을 사용하여 일반화 이미지에서 확장 집합 만들기 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

[Shared Image Gallery](../virtual-machines/shared-image-galleries.md)에 저장된 일반화 이미지 버전에서 VM을 만듭니다. 특수화 이미지를 사용하여 확장 집합을 만들려면 [특수화 이미지에서 확장 집합 인스턴스 만들기](instance-specialized-image-version-powershell.md)를 참조하세요.

일반화 이미지를 사용하는 경우 [New-AzVmss](/powershell/module/az.compute/new-azvmss) cmdlet을 사용하여 가상 머신 확장 집합을 만들 수 있습니다. 

이 예에서는 이미지 정의 ID를 사용하여 새 VM에서 최신 버전의 이미지를 사용할 수 있도록 합니다. `-ImageReferenceId`에 대한 이미지 버전 ID를 사용하여 특정 버전을 사용할 수도 있습니다. 예를 들어 이미지 버전 *1.0.0* 형식을 사용하려면 `-ImageReferenceId "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`을 입력합니다. 

특정 이미지 버전이 지역에서 삭제되거나 제거되어 사용할 수 없는 경우, 해당 이미지 버전을 사용하면 자동화가 실패할 수 있습니다. 특정 이미지 버전이 필요하지 않은 경우 이미지 정의 ID를 사용하여 새 VM을 만드는 것이 좋습니다.


다음 예에서는 *SouthCentralUS* 위치의 *myVMSSRG* 리소스 그룹에 *myScaleSet* 라는 확장 집합을 만듭니다. 확장 집합은 *myGalleryRG* 리소스 그룹의 *myGallery* 이미지 갤러리에 있는 *myImageDefinition* 이미지에서 생성됩니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 사용자 고유의 관리 자격 증명을 설정합니다.


## <a name="simplified-parameter-set"></a>간소화된 매개 변수 세트

최소 정보를 제공하면서 확장 집합을 빠르게 만들려면 간소화된 매개 변수 세트를 사용하여 Shared Image Gallery 이미지에서 확장 집합을 만듭니다.

```azurepowershell-interactive
$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create the resource group and scale set
New-AzResourceGroup -ResourceGroupName myVMSSRG -Location SouthCentralUS
New-AzVmss `
   -Credential $cred `
   -VMScaleSetName myScaleSet `
   -ImageName $imageDefinition.Id `
   -UpgradePolicyMode Automatic `
   -ResourceGroupName myVMSSRG
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="extended-parameter-set"></a>확장된 매개 변수 세트

이름 지정을 포함하여 모든 리소스를 완전히 제어하려면 전체 매개 변수 세트를 사용하여 Shared Image Gallery 이미지를 통해 확장 집합을 만듭니다. 

```azurepowershell-interactive
# Get the image definition

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myGalleryRG `
   -Name myImageDefinition

# Create user object

$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."
   
# Define variables for the scale set
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $imageDefinition.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계
[Azure Image Builder(미리 보기)](../virtual-machines/image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](../virtual-machines/linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](../virtual-machines/shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../virtual-machines/troubleshooting-shared-images.md)을 참조하세요.
