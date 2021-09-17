---
title: Shared Image Gallery 만들기
description: Azure에서 공유 이미지 갤러리를 만드는 방법 알아보기
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452683"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>이미지를 저장 하 고 공유 하기 위한 갤러리 만들기


[공유 이미지 갤러리](./shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 배포 작업에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 전체 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다.

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>갤러리 만들기

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다. 갤러리 이름은 구독 내에서 고유해야 합니다. 

갤러리를 만들려면 아래 옵션을 선택 합니다.

### <a name="portal"></a>[포털](#tab/portal)

다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. 검색 상자에서 **공유 이미지 갤러리** 유형을 사용하고 결과에서 **공유 이미지 갤러리** 를 선택합니다.
1. **공유 이미지 갤러리** 페이지에서 **추가** 를 클릭합니다.
1. **공유 이미지 갤러리 만들기** 페이지에서 올바른 구독을 선택합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 이름으로 *myGalleryRG* 를 입력합니다.
1. **이름** 에서 갤러리 이름으로 *myGallery* 를 입력합니다.
1. **지역** 은 기본값 그대로 둡니다.
1. '테스트용 내 이미지 갤러리'와 같이 갤러리에 대한 간단한 설명을 입력할 수 있습니다. 그런 다음 **검토 + 만들기** 를 클릭합니다.
1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="cli"></a>[CLI](#tab/cli)

[az sig create](/cli/azure/sig#az_sig_create)를 사용하여 이미지 갤러리를 만드세요. 다음 예제에서는 *미국 동부* 에 *myGalleryRG* 라는 리소스 그룹 및 *myGallery* 라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

[New-AzGallery](/powershell/module/az.compute/new-azgallery)를 사용하여 이미지 갤러리를 만듭니다. 다음 예제에서는 *myGalleryRG* 리소스 그룹에 *myGallery* 라는 갤러리를 만듭니다.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


### <a name="rest"></a>[REST (영문)](#tab/rest)

[REST API](/rest/api/resources/resource-groups/create-or-update) 를 사용 하 여 리소스 그룹을 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

[REST API](/rest/api/compute/galleries/create-or-update) 를 사용 하 여 갤러리를 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>다음 단계

[이미지 정의와 이미지 버전](image-version.md)을 만듭니다.

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./windows/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)