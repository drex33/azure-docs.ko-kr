---
title: VM에서 이미지 만들기
description: Azure PowerShell을 사용하여 Azure의 기존 VM에서 Shared Image Gallery에 이미지를 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b3b0de7b9229f8bfd43e41f26ccb7d7d7c790b92
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205118"
---
# <a name="create-an-image-from-a-vm"></a>VM에서 이미지 만들기

기존 VM으로 동일한 VM을 여러 개 만들려는 경우, Azure PowerShell을 사용하여 Shared Image Gallery에 이미지를 만드는 데 해당 VM을 사용할 수 있습니다. 또한 [Azure CLI](image-version-vm-cli.md)를 사용하여 VM에서 이미지를 만들 수 있습니다.

Azure PowerShell를 사용하여 [특수화되고 일반화된](./shared-image-galleries.md#generalized-and-specialized-images) VM에서 이미지를 캡처할 수 있습니다. 

이미지 갤러리의 이미지에는 두 가지 구성 요소가 있으며, 다음 예제에서는 해당 구성 요소를 생성합니다.
- **이미지 정의** 에는 이미지에 대한 정보 및 이미지 사용에 대한 요구 사항이 포함되어 있습니다. 여기에는 이미지가 Windows인지 Linux인지, 특수한 이미지인지 일반화된 이미지인지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 Shared Image Gallery를 사용할 때 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 생성할 때 이미지 버전은 VM의 새 디스크를 만드는 데 사용됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 Shared Image Gallery와 Azure에서 원본으로 사용할 기존 VM이 있어야 합니다. 

VM에 데이터 디스크가 연결된 경우 데이터 디스크 크기는 1TB를 초과할 수 없습니다.

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.


## <a name="get-the-gallery"></a>갤러리 가져오기

모든 갤러리 및 이미지 정의를 이름으로 나열할 수 있습니다. 결과는 `gallery\image definition\image version` 형식입니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

오른쪽 갤러리 및 이미지 정의를 찾았으면 나중에 사용할 수 있도록 그에 대한 변수를 만듭니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에서 *myGallery* 라는 갤러리를 가져옵니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>VM 가져오기

[Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 리소스 그룹에서 사용할 수 있는 VM 목록을 볼 수 있습니다. VM 이름과 해당 VM이 있는 리소스 그룹을 알 수 있으면 `Get-AzVM`을 다시 사용하여 VM 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *sourceVM* 이라는 VM을 가져온 후 변수 *$sourceVM* 에 할당합니다. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

[Stop-AzVM](/powershell/module/az.compute/stop-azvm)을 사용하여 이미지를 만들기 전에 VM을 중지/할당 취소하는 것이 좋습니다.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이는 이미지에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때 모든 정보가 올바른지 확인합니다. VM을 일반화한 경우(Windows용 Sysprep 사용 또는 Linux용 waagent -프로비전 해제) `-OsState generalized`를 사용하여 이미지 정의를 만들어야 합니다. VM을 일반화하지 않은 경우 `-OsState specialized`를 사용하여 이미지 정의를 만듭니다 .

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 Windows를 실행하는 특수 VM에 대한 것입니다. Linux를 사용하여 이미지에 대한 정의를 만들려면 `-OsType Linux`를 사용합니다. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```
> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `-PurchasePlanName`, `-PurchasePlanProduct` 및 `-PurchasePlanPublisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>

## <a name="create-an-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위해 대상 지역을 선택할 때 *원본* 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다.

VM에서 이미지 버전을 만들려면 `-SourceImageId`에 대해 `$vm.Id.ToString()`을 사용합니다.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Premium_LRS`를 추가하여 프리미엄 스토리지에 추가하거나 `-StorageAccountType Standard_ZRS`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

새 이미지 버전이 제대로 작동하는지 확인한 후 VM을 만들 수 있습니다. [일반화된 이미지 버전](vm-specialized-image-version-powershell.md) 또는 [특수화된 이미지 버전](vm-generalized-image-version-powershell.md)에서 VM을 만듭니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
