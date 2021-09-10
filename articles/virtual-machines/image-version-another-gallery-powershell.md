---
title: PowerShell을 사용하여 다른 갤러리에서 이미지 복사
description: Azure PowerShell을 사용하여 다른 갤러리에서 이미지를 복사합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c5d06e510cfa78a0651f73c0f300b11c4d5b01e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697054"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>PowerShell을 사용하여 다른 갤러리에서 이미지 복사

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

조직에 여러 갤러리가 있는 경우 다른 갤러리에 저장된 이미지로부터 이미지를 만들 수 있습니다. 예를 들어 새 이미지를 만들고 테스트하기 위해 개발 및 테스트 갤러리를 사용할 수 있습니다. 프로덕션 환경에서 사용할 준비가 되면 이 예제를 사용하여 프로덕션 갤러리에 복사할 수 있습니다. [Azure CLI](image-version-another-gallery-cli.md)를 사용하여 다른 갤러리의 이미지로부터 이미지를 만들 수도 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 원본 갤러리, 이미지 정의, 이미지 버전이 있어야 합니다. 대상 갤러리도 있어야 합니다. 

원본 이미지 버전은 대상 갤러리가 있는 지역에 복제되어야 합니다. 

대상 갤러리에 새 이미지 정의와 이미지 버전이 만들어집니다.


이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.


## <a name="get-the-source-image"></a>원본 이미지 가져오기 

대상 갤러리에서 복사본을 만들 수 있으려면 원본 이미지 정의의 정보가 필요합니다.

[Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet을 사용하여 기존 갤러리, 이미지 정의, 이미지 버전에 대한 정보를 나열합니다.

결과는 `gallery\image definition\image version` 형식입니다.

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

필요한 정보가 모두 있으면 [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion)을 사용하여 원본 이미지 버전의 ID를 가져올 수 있습니다. 이 예제에서는 `myResourceGroup` 리소스 그룹에 있는 `myGallery` 원본 갤러리에서 `myImageDefinition` 정의의 `1.0.0` 이미지 버전을 가져옵니다.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>이미지 정의 만들기 

운영 체제, 운영 체제 상태 및 원본 이미지 버전이 포함된 이미지 정의의 Hyper-V 생성과 일치하는 새 이미지 정의를 만들어야 합니다. [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition)을 사용하여 이미지 정의를 다시 만드는 데 필요한 모든 정보를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


출력은 다음과 비슷할 것입니다.

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

대상 갤러리에서 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) cmdlet 및 위의 출력 정보를 사용하여 새 이미지 정의를 만듭니다.


이 예의 경우 *myDestinationGallery* 라는 갤러리에서 이미지 정의에 *myDestinationImgDef* 라는 이름이 지정됩니다.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `-PurchasePlanName`, `-PurchasePlanProduct` 및 `-PurchasePlanPublisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>


## <a name="create-the-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 버전을 만듭니다. 대상 갤러리에서 이미지 버전을 만들기 위해 `-SourceImageId`매개 변수에서 원본 이미지의 ID를 전달해야 합니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예에서 ‘미국 서부’ 위치에 있는 *myDestinationRG* 리소스 그룹의 대상 갤러리 이름이 *myDestinationGallery* 로 지정됩니다. 이미지 버전은 *1.0.0* 이며 ‘미국 중남부’ 지역에서 복제본 1개, ‘미국 서부’ 지역에서 복제본 2개를 만듭니다.   


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 관리 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Premium_LRS`를 추가하여 프리미엄 스토리지에 추가하거나 `-StorageAccountType Standard_ZRS`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>


## <a name="next-steps"></a>다음 단계

[일반화](vm-generalized-image-version-powershell.md)된 이미지 버전 또는 [특화](vm-specialized-image-version-powershell.md)된 이미지 버전에서 VM을 만듭니다.

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
