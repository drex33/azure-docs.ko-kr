---
title: Azure CLI를 사용하여 관리형 이미지를 이미지 버전으로 복제
description: Azure CLI를 사용하여 관리형 이미지를 Shared Image Gallery의 이미지 버전으로 복제하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8032524c01d64fa6100779401b725f0eb339457e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689937"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Azure CLI를 사용하여 관리형 이미지를 이미지 버전으로 복제

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

기존 관리형 이미지를 Shared Image Gallery로 복제하려는 경우, 관리형 이미지에서 직접 Shared Image Gallery 이미지를 만들 수 있습니다. 새 이미지 만들기를 테스트한 후에는 원본 관리형 이미지를 삭제할 수 있습니다. [PowerShell](image-version-managed-image-powershell.md)을 사용하여 관리형 이미지에서 Shared Image Gallery로 마이그레이션할 수도 있습니다.

이미지 갤러리의 이미지에는 두 가지 구성 요소가 있으며, 다음 예제에서는 해당 구성 요소를 생성합니다.
- **이미지 정의** 에는 이미지에 대한 정보 및 이미지 사용에 대한 요구 사항이 포함되어 있습니다. 여기에는 이미지가 Windows인지 Linux인지, 특수한 이미지인지 일반화된 이미지인지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 Shared Image Gallery를 사용할 때 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 생성할 때 이미지 버전은 VM의 새 디스크를 만드는 데 사용됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 [Shared Image Gallery](shared-images-cli.md)가 있어야 합니다. 

이 문서의 예제를 완료하려면 일반화된 VM의 기존 관리 이미지가 있어야 합니다. 자세한 내용은 [관리형 이미지 캡처](./linux/capture-image.md)를 참조하세요. 관리형 이미지에 데이터 디스크가 포함되어 있는 경우 데이터 디스크 크기는 1TB를 초과할 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.



## <a name="create-an-image-definition"></a>이미지 정의 만들기

관리형 이미지는 항상 일반화된 이미지이므로 일반화된 이미지에 대해 `--os-state generalized`를 사용하여 이미지 정의를 만듭니다.

이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의에 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create)를 사용하여 갤러리에서 이미지 정의를 만듭니다.

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [일반화된](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `--plan-name`, `--plan-product`, `--plan-publisher`를 추가하여 이미지 정의에 플랜 정보를 포함합니다.
>

## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 버전을 만듭니다. 이미지 버전을 만드는 기준선으로 사용할 관리되는 이미지의 ID를 전달해야 합니다. [az image list](/cli/azure/image?view#az_image_list)를 사용하여 이미지의 ID를 가져올 수 있습니다. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

해당 예제에서 이미지의 버전은 *1.0.0* 이며, 영역 중복 스토리지를 사용하여 ‘미국 중남부’ 지역에 복제본 1개, ‘미국 동부 2’ 지역에 복제본 1개를 만들 것입니다.  복제를 위해 대상 지역을 선택할 때 ‘원본’ 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다.

`--managed-image` 매개 변수에 관리형 이미지의 ID를 전달합니다.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type standard_zrs`를 추가하여 모든 이미지 버전 복제본을 [영역 중복 스토리지](../storage/common/storage-redundancy.md)에 저장할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

[일반화된 이미지 버전](vm-generalized-image-version-cli.md)에서 VM을 만듭니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
