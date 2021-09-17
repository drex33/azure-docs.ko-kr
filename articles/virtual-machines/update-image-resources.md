---
title: 이미지 리소스 나열, 업데이트, 삭제
description: 공유 이미지 갤러리에서 이미지 리소스를 나열, 업데이트 및 삭제합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: af41cd667da05abcd05d4c04d913e82567eec5ca
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452681"
---
# <a name="list-update-and-delete-image-resources"></a>이미지 리소스 나열, 업데이트, 삭제 

Azure CLI 또는 Azure PowerShell 사용하여 공유 이미지 갤러리 리소스를 관리할 수 있습니다.

## <a name="list-information"></a>정보 나열

### <a name="cli"></a>[CLI](#tab/cli)

[az sig list](/cli/azure/sig#az_sig_list)를 사용하여 사용 가능한 이미지 갤러리에 대한 위치, 상태 및 기타 정보를 가져올 수 있습니다.

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)를 사용하여 OS 유형 및 상태에 대한 정보를 포함한 갤러리의 이미지 정의를 나열합니다.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)를 사용하여 갤러리의 공유 이미지 버전을 나열합니다.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

[az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show)를 사용하여 이미지 버전의 ID를 가져옵니다.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

이름별로 모든 갤러리를 나열합니다.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

이름별로 모든 이미지 정의를 나열합니다.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

이름별로 모든 이미지 버전을 나열합니다.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

이미지 버전을 삭제합니다. 이 예제에서는 *1.0.0* 이라는 이미지 버전을 삭제합니다.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>리소스 업데이트

### <a name="cli"></a>[CLI](#tab/cli)
업데이트할 수 있는 항목에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리형 이미지를 삭제하지 마세요. 원본 관리형 이미지는 이미지 버전을 추가 지역에 복제하는 데 필요합니다. 

([az sig update](/cli/azure/sig#az_sig_update)를 사용하여 갤러리에 대한 설명을 업데이트합니다. 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update)를 사용하여 이미지 정의에 대한 설명을 업데이트합니다.

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update)를 사용하여 복제할 지역을 추가하도록 이미지 버전을 업데이트합니다. 이 변경 내용은 이미지가 새 지역에 복제될 때까지 시간이 오래 걸립니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

이 예제에서는 [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update)를 사용하여 이 이미지 버전을 *최신* 이미지로 사용되지 않도록 제외하는 방법을 보여줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

이 예제에서는 [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update)를 사용하여 이 이미지 버전을 *최신* 이미지로 고려하는 방법을 보여줍니다.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


업데이트할 수 있는 항목에는 몇 가지 제한 사항이 있습니다. 다음 항목을 업데이트할 수 있습니다. 

공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신에서 제외
- 수명 주기 끝

복제본 영역을 추가하려는 경우 원본 관리형 이미지를 삭제하지 마세요. 원본 관리형 이미지는 이미지 버전을 추가 지역에 복제하는 데 필요합니다. 

갤러리에 대한 설명을 업데이트하려면 [Update-AzGallery](/powershell/module/az.compute/update-azgallery)를 사용합니다.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

이 예제에서는 [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition)을 사용하여 이미지 정의의 수명 종료 날짜를 업데이트하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

이 예제에서는 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion)을 사용하여 이 이미지 버전을 *최신* 이미지로 사용되지 않도록 제외하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

이 예제에서는 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion)을 사용하여 이 이미지 버전을 *최신* 이미지로 고려하는 방법을 보여줍니다.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>리소스 삭제

먼저 이미지 버전을 삭제하여 리소스를 역순으로 삭제해야 합니다. 모든 이미지 버전이 삭제되면 이미지 정의를 삭제할 수 있습니다. 모든 이미지 정의가 삭제되면 갤러리를 삭제할 수 있습니다. 

### <a name="cli"></a>[CLI](#tab/cli)

[az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete)를 사용하여 이미지 버전을 삭제합니다.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete)를 사용하여 이미지 정의를 삭제합니다.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[az sig delete](/cli/azure/sig#az_sig_delete)를 사용하여 이미지 갤러리를 삭제합니다.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>다음 단계

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다.