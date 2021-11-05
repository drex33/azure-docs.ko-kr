---
title: 자습서 - Azure PowerShell을 사용하여 확장 집합에서 사용자 지정 VM 이미지 사용
description: Azure PowerShell을 사용하여 가상 머신 확장 집합을 배포하는 데 사용할 수 있는 사용자 지정 VM 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c0ba3a58906275d136a437f3b18d32e3fe70a24
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456787"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합에 대한 사용자 지정 이미지 만들기 및 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. VM 인스턴스가 배포된 후 작업의 수를 줄이려면 사용자 지정 VM 이미지를 사용할 수 있습니다. 이 사용자 지정 VM 이미지에는 필요한 모든 애플리케이션 설치 또는 구성이 포함됩니다. 확장 집합에서 만들어진 모든 VM 인스턴스는 사용자 지정 VM 이미지를 사용하며, 애플리케이션 트래픽을 처리할 준비가 되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 확장 집합 만들기 
> * 이미지 갤러리 공유

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 확장 집합을 만드는 데 사용할 수 있는 재사용 가능한 사용자 지정 이미지로 변환하는 방법을 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 이 자습서에 사용할 VM을 만드는 [PowerShell 빠른 시작](quick-create-powershell.md)을 볼 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="get-the-vm"></a>VM 가져오기

[Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 리소스 그룹에서 사용할 수 있는 VM 목록을 볼 수 있습니다. VM 이름과 해당 리소스 그룹을 알 수 있으면 `Get-AzVM`을 다시 사용하여 VM 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *sourceVM* 이라는 VM을 가져온 후 변수 *$vm* 에 할당합니다. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *미국 동부* 지역에 *myGalleryRG* 라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

[New-AzGallery](/powershell/module/az.compute/new-azgallery)를 사용하여 이미지 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 이 예제에서 갤러리 이미지는 *myGalleryImage* 로 이름이 지정되고 특수 이미지에 대해 만들어집니다. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 VM에서 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 동부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제할 대상 영역을 선택할 때 *원본* 지역을 복제 대상으로 포함해야 합니다.

VM에서 이미지 버전을 만들려면 `-Source`에 대해 `$vm.Id.ToString()`을 사용합니다.

```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='East US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

이미지를 모든 대상 영역에 복제하는 데 시간이 걸릴 수 있습니다.

## <a name="create-a-scale-set-from-the-image"></a>이미지에서 확장 집합 만들기
이제 이전 단계에서 만든 사용자 지정 VM 이미지를 정의하기 위해 `-ImageName` 매개 변수를 사용하는 [New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치에는 80 TCP 포트에서 트래픽을 분산할 뿐만 아니라 3389 TCP 포트의 원격 데스크톱 트래픽 및 5985 TCP 포트의 PowerShell 원격을 허용하는 규칙이 포함되어 있습니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

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
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
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


## <a name="share-the-gallery"></a>갤러리 공유

이미지 갤러리 수준에서 액세스를 공유하는 것이 좋습니다. 이메일 주소 및 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 사용자의 개체 ID를 가져온 다음, [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 갤러리에 대한 액세스 권한을 제공합니다. 이 예제에서 alinne_montes@contoso.com 예제 이메일을 사용자 고유의 정보로 바꿉니다.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure 이미지 작성기

Azure는 [Azure VM 이미지 작성기](../virtual-machines/image-builder-overview.md)인 Packer를 기반으로 하는 서비스도 제공합니다. 템플릿에서 사용자 지정을 설명하기만 하면 이미지 만들기가 처리됩니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 확장 집합에 대한 사용자 지정 VM 이미지를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 확장 집합 만들기 
> * 이미지 갤러리 공유

애플리케이션을 확장 집합에 배포하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합에 애플리케이션 배포](tutorial-install-apps-powershell.md)
