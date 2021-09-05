---
title: Azure CLI를 사용하여 VM에서 이미지 만들기
description: Azure의 VM에서 Shared Image Gallery에 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 75ce18b89975aaf0f45620291ccd106db69656ec
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205101"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure의 VM에서 이미지 버전 만들기

기존 VM으로 동일한 VM을 여러 개 만들려는 경우, Azure CLI를 사용하여 Shared Image Gallery에 이미지를 만드는 데 해당 VM을 사용할 수 있습니다. 또한 [Azure PowerShell](image-version-vm-powershell.md)을 사용하여 VM에서 이미지를 만들 수 있습니다.

**이미지 버전** 은 Shared Image Gallery를 사용하는 중에 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 이미지 버전을 사용하여 VM을 만들 때는 이미지 버전으로 새 VM의 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 Shared Image Gallery가 있어야 합니다. 

또한 Azure의 기존 VM이 갤러리와 동일한 영역에 있어야 합니다. 

VM에 데이터 디스크가 연결된 경우 데이터 디스크 크기는 1TB를 초과할 수 없습니다.

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.

## <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

[az vm list](/cli/azure/vm#az_vm_list)를 사용하여 사용할 수 있는 VM 목록을 볼 수 있습니다. 

```azurecli-interactive
az vm list --output table
```

VM 이름과 해당 VM이 속한 리소스 그룹을 알고 있으면 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)를 사용하여 VM ID를 가져옵니다. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 

이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의가 올바른 형식인지 확인합니다. VM을 일반화한 경우(Windows용 Sysprep 사용 또는 Linux용 waagent -프로비전 해제) `--os-state generalized`를 사용하여 일반화된 이미지 정의를 만들어야 합니다. 기존 사용자 계정을 제거하지 않고 VM을 사용하려는 경우 `--os-state specialized`를 사용하여 특수화된 이미지 정의를 만듭니다.

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create)를 사용하여 갤러리에서 이미지 정의를 만듭니다.

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [특수](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `--plan-name`, `--plan-product` 및 `--plan-publisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>

## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 VM에서 이미지 버전을 만듭니다.  

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch* 입니다.

다음 예제에서는 이미지 버전이 *1.0.0* 이며, 영역 중복 스토리지를 사용하여 두 복제본을 *미국 중서부* 지역에, 하나의 복제본을 각각 *미국 중남부* 지역 및 *미국 동부 2* 지역에 만듭니다. 복제 지역에는 원본 VM이 있는 지역이 포함되어야 합니다.

이 예제의 `--managed-image` 값을 이전 단계의 VM ID로 바꿉니다.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type  premium_lrs`를 추가하여 프리미엄 스토리지에 추가하거나 `--storage-account-type  standard_zrs`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 [일반화된 이미지](vm-generalized-image-version-cli.md)에서 VM을 생성합니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
