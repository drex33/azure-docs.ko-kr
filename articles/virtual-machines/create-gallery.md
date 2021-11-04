---
title: 리소스 공유를 위한 Azure Compute 갤러리 만들기
description: Azure Compute 갤러리를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/05/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 924cce1b23cf86a6e29ef4fd28f862bf2fa12363
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459694"
---
# <a name="create-a-gallery-for-storing-and-sharing-resources"></a>리소스 저장 및 공유를 위한 갤러리 만들기


[Azure Compute](./shared-image-galleries.md) 갤러리(이전의 Shared Image Gallery)는 조직 전체에서 이미지 및 애플리케이션 패키지와 같은 리소스 공유를 간소화합니다.  

Azure Compute 갤러리를 사용하면 사용자 지정 VM 이미지 및 애플리케이션 패키지를 AAD 테넌트 내의 조직 내 또는 지역 간에 조직의 다른 사용자와 공유할 수 있습니다. 공유하려는 지역, 공유하려는 지역 및 공유하려는 지역을 선택합니다. 리소스를 논리적으로 그룹화할 수 있도록 여러 갤러리를 만들 수 있습니다. 

갤러리는 전체 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 

## <a name="create-a-gallery"></a>갤러리 만들기

갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

갤러리를 만들기 위한 아래 옵션을 선택합니다.

### <a name="portal"></a>[포털](#tab/portal)

다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. 검색 상자에 **Azure Compute 갤러리** 유형을 사용하고 결과에서 Azure Compute **갤러리를** 선택합니다.
1. Azure Compute **갤러리** 페이지에서 **추가를** 클릭합니다.
1. Azure Compute **갤러리 만들기** 페이지에서 올바른 구독을 선택합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 이름으로 *myGalleryRG* 를 입력합니다.
1. **이름** 에서 갤러리 이름으로 *myGallery* 를 입력합니다.
1. **지역** 은 기본값 그대로 둡니다.
1. 테스트를 위해 내 갤러리와 같은 갤러리에 대한 간단한 설명을 입력할 수 *있습니다.* 그런 다음 **검토 + 만들기** 를 클릭합니다.
1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="cli"></a>[CLI](#tab/cli)

[az sig create를](/cli/azure/sig#az_sig_create)사용하여 갤러리를 만듭니다. 다음 예제에서는 *미국 동부* 에 *myGalleryRG* 라는 리소스 그룹 및 *myGallery* 라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

[New-AzGallery](/powershell/module/az.compute/new-azgallery)를 사용하여 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


### <a name="rest"></a>[REST (영문)](#tab/rest)

[REST API](/rest/api/resources/resource-groups/create-or-update) 사용하여 리소스 그룹을 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

[REST API](/rest/api/compute/galleries/create-or-update) 사용하여 갤러리를 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Azure Compute Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>다음 단계

- 이미지 [정의 및 이미지 버전 를](image-version.md)만듭니다.

- [갤러리에서 VM 애플리케이션을 만듭니다.](vm-applications-how-to.md)

- Azure Compute 갤러리는 템플릿을 사용하여 [Azure Compute 갤러리를 만들](https://azure.microsoft.com/resources/templates/sig-create/) 수도 있습니다.

- [Azure Image Builder](./image-builder-overview.md) 이미지 버전 만들기를 자동화하는 데 도움이 될 수 있으며, [기존 이미지 버전 에서 새 이미지 버전을](./windows/image-builder-gallery-update-image-version.md)업데이트하고 만드는 데 사용할 수도 있습니다. 
