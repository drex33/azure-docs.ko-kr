---
title: CLI를 사용하여 다른 갤러리에서 이미지 버전 복사
description: Azure CLI를 사용하여 다른 갤러리에서 이미지 버전을 복사합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 904700344900d73b700bdfac01af2022425c7c69
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689022"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Azure CLI를 사용하여 다른 갤러리에서 이미지 복사

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

조직에 여러 갤러리가 있는 경우 다른 갤러리에 저장된 기존 이미지 버전에서 이미지 버전을 만들 수도 있습니다. 예를 들어 새 이미지를 만들고 테스트하기 위한 개발 및 테스트 갤러리가 있을 수 있습니다. 프로덕션 환경에서 사용할 준비가 되면 이 예제를 사용하여 프로덕션 갤러리에 복사할 수 있습니다. [Azure PowerShell](image-version-another-gallery-powershell.md)을 사용하여 다른 갤러리의 이미지에서 이미지를 만들 수도 있습니다.



## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 원본 갤러리, 이미지 정의, 이미지 버전이 있어야 합니다. 대상 갤러리도 있어야 합니다. 

원본 이미지 버전은 대상 갤러리가 있는 지역에 복제되어야 합니다. 

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.



## <a name="get-information-from-the-source-gallery"></a>원본 갤러리에서 정보 가져오기

새 갤러리에서 복사본을 만들 수 있으려면 원본 이미지 정의의 정보가 필요합니다.

[az sig list](/cli/azure/sig#az_sig_list)를 사용하여 사용 가능한 이미지 갤러리에 대한 정보를 나열하고 원본 갤러리에 대한 정보를 찾습니다.

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)를 사용하여 갤러리의 이미지 정의를 나열합니다. 이 예제에서는 *myGalleryRG* 리소스 그룹에 있는 *myGallery* 라는 갤러리에서 이미지 정의를 검색합니다.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)를 사용하여 갤러리의 이미지 버전을 나열하고 새 갤러리에 복사하려는 이미지 버전을 찾습니다. 이 예제에서는 *myImageDefinition* 이미지 정의에 속하는 모든 이미지 버전을 찾습니다.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

필요한 정보가 모두 있으면 [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show)를 사용하여 원본 이미지 버전의 ID를 가져올 수 있습니다.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>이미지 정의 만들기 

운영 체제, 운영 체제 상태 및 원본 이미지 버전이 포함된 이미지 정의의 Hyper-V 만들기와 일치하는 이미지 정의를 만들어야 합니다. [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show)를 사용하여 새 갤러리에 이미지 정의를 다시 만드는 데 필요한 모든 정보를 표시할 수 있습니다.

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

위의 출력 정보를 사용하여 새 갤러리에 새 이미지 정의를 만듭니다.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `--plan-name`, `--plan-product` 및 `--plan-publisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>


## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [az image list](/cli/azure/image?view#az_image_list)를 사용하여 리소스 그룹에 있는 이미지에 대한 정보를 가져올 수 있습니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

해당 예제에서 이미지의 버전은 *1.0.0* 이며, 영역 중복 스토리지를 사용하여 ‘미국 중남부’ 지역에 복제본 1개, ‘미국 동부’ 지역에 복제본 1개를 만들 것입니다. 


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type  premium_lrs`를 추가하여 프리미엄 스토리지에 추가하거나 `--storage-account-type  standard_zrs`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

[일반화](vm-generalized-image-version-cli.md)된 이미지 버전 또는 [특화](vm-specialized-image-version-cli.md)된 이미지 버전에서 VM을 만듭니다.

[Azure Image Builder](./image-builder-overview.md)는 이미지 버전 생성 자동화에 도움이 되며, [기존 이미지 버전에서 새 이미지 버전을 만들고](./linux/image-builder-gallery-update-image-version.md) 업데이트하는 데에도 사용할 수 있습니다. 

구매 계획 정보를 제공하는 방법에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
