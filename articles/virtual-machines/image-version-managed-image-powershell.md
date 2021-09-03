---
title: Shared Image Gallery에 관리형 이미지 복제
description: Azure PowerShell을 사용하여 관리형 이미지를 Shared Image Gallery의 이미지 버전으로 복제하는 방법에 대해 알아봅니다.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 282c463f1045cda63abec0358da38f294e41b05a
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205152"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>Shared Image Gallery 이미지에 관리형 이미지 복제

기존 관리형 이미지를 Shared Image Gallery로 복제 및 이동하려는 경우, 관리형 이미지에서 직접 Shared Image Gallery 이미지를 만들 수 있습니다. 새 이미지 만들기를 테스트한 후에는 원본 관리형 이미지를 삭제할 수 있습니다. [Azure CLI](image-version-managed-image-cli.md)을 사용하여 관리형 이미지에서 Shared Image Gallery로 마이그레이션할 수도 있습니다.

이미지 갤러리의 이미지에는 두 가지 구성 요소가 있으며, 다음 예제에서는 해당 구성 요소를 생성합니다.
- **이미지 정의** 에는 이미지에 대한 정보 및 이미지 사용에 대한 요구 사항이 포함되어 있습니다. 여기에는 이미지가 Windows인지 Linux인지, 특수한 이미지인지 일반화된 이미지인지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 Shared Image Gallery를 사용할 때 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 생성할 때 이미지 버전은 VM의 새 디스크를 만드는 데 사용됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 관리형 이미지가 있어야 합니다. 관리형 이미지에 데이터 디스크가 포함되어 있는 경우 데이터 디스크 크기는 1TB를 초과할 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="get-the-gallery"></a>갤러리 가져오기

모든 갤러리 및 이미지 정의를 이름으로 나열할 수 있습니다. 결과는 `gallery\image definition\image version` 형식으로 제공됩니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

올바른 갤러리를 찾았으면 나중에 사용할 변수를 만듭니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에서 *myGallery* 라는 갤러리를 가져옵니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이는 이미지에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때 모든 정보가 올바른지 확인합니다. 관리형 이미지는 항상 일반화되므로 `-OsState generalized`을 설정해야 합니다. 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 다음 예제에서 이미지 정의는 이름이 *myImageDefinition* 이며, 일반적인 Windows OS 이미지에 대한 것입니다. Linux OS를 사용하여 이미지에 대한 정의를 만들려면 `-OsType Linux`를 사용합니다. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `-PurchasePlanName`, `-PurchasePlanProduct` 및 `-PurchasePlanPublisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>

## <a name="get-the-managed-image"></a>관리되는 이미지 가져오기

[Get-AzImage](/powershell/module/az.compute/get-azimage)를 사용하여 리소스 그룹에서 사용할 수 있는 이미지 목록을 볼 수 있습니다. 이미지 이름과 해당 이미지가 어떤 리소스 그룹에 들어 있는지 알 수 있으면 `Get-AzImage`를 다시 사용하여 이미지 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *myImage* 라는 이미지를 가져온 후 변수 *$managedImage* 에 할당합니다. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 관리형 이미지에서 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위해 대상 지역을 선택할 때 *원본* 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 진행률을 표시하려면 `$job.State`을 입력합니다.

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다. 
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Premium_LRS`를 추가하여 프리미엄 스토리지에 추가하거나 `-StorageAccountType Standard_ZRS`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

## <a name="delete-the-managed-image"></a>관리형 도메인 삭제

새 이미지 버전이 제대로 작동하는지 확인한 후 관리형 이미지를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

복제가 완료되었는지 확인한 후에는 [일반화된 이미지](vm-generalized-image-version-powershell.md)에서 VM을 만들 수 있습니다.

구매 계획 정보를 제공하는 방법에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
