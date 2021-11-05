---
title: 자습서 - Azure PowerShell을 사용하여 사용자 지정 VM 이미지 만들기
description: 이 자습서에서는 Azure PowerShell을 사용하여 Azure Compute Gallery에서 저장된 Windows 사용자 지정 가상 머신 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 5c7ce289d515892db72b681e1e7ef5337eb7cb9b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421643"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows VM 이미지 만들기
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

이미지를 사용하여 배포를 부트스트랩하고 여러 VM에서 일관성을 유지할 수 있습니다. 이 자습서에서는 PowerShell을 사용하여 Azure 가상 머신의 특수한 이미지를 만들고 Azure Compute Gallery(이전의 Shared Image Gallery)에 저장합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 VM 만들기 
> * 갤러리 공유



## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM을 만드는 데 사용할 수 있는 재사용 가능한 사용자 지정 이미지로 변환하는 방법을 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 이 자습서에 사용할 VM을 만드는 [PowerShell 빠른 시작](quick-create-powershell.md)을 볼 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.

## <a name="overview"></a>개요

[Azure Compute Gallery](../shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Azure Compute Gallery를 사용하면 사용자 지정 VM 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 

Azure Compute Gallery 기능에는 여러 가지 리소스가 있습니다.

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="get-the-vm"></a>VM 가져오기

[Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 리소스 그룹에서 사용할 수 있는 VM 목록을 볼 수 있습니다. VM 이름과 해당 리소스 그룹을 알 수 있으면 `Get-AzVM`을 다시 사용하여 VM 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *sourceVM* 이라는 VM을 가져온 후 변수 *$sourceVM* 에 할당합니다. 

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

## <a name="create-a-gallery"></a>갤러리 만들기 

갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

[New-AzGallery](/powershell/module/az.compute/new-azgallery)를 사용하여 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimagedefinition)을 사용하여 이미지 정의를 만듭니다. 이 예제에서 갤러리 이미지는 *myGalleryImage* 로 이름이 지정되고 특수 이미지에 대해 만들어집니다. 

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


## <a name="create-a-vm"></a>VM 만들기 

특수 이미지를 만든 후에 하나 이상의 새 VM을 만들 수 있습니다. [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet을 사용합니다. 이미지를 사용하려면 `Set-AzVMSourceImage`를 사용하고 `-Id`를 이미지 정의 ID(이 경우 $galleryImage.Id)로 설정하여 항상 최신 이미지 버전을 사용합니다. 

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>갤러리 공유

갤러리 수준에서 액세스를 공유하는 것이 좋습니다. 이메일 주소 및 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 사용자의 개체 ID를 가져온 다음, [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 갤러리에 대한 액세스 권한을 제공합니다. 이 예제에서 alinne_montes@contoso.com 예제 이메일을 사용자 고유의 정보로 바꿉니다.

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

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure 이미지 작성기

Azure는 [Azure VM 이미지 작성기](../image-builder-overview.md)인 Packer를 기반으로 하는 서비스도 제공합니다. 템플릿에서 사용자 지정을 설명하기만 하면 이미지 만들기가 처리됩니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 특수한 VM 이미지를 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 VM 만들기 
> * 갤러리 공유

다음 자습서로 이동하여 고가용성 가상 머신을 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [고가용성 VM 만들기](tutorial-availability-sets.md)
