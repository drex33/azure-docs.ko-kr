---
title: Azure CLI를 사용하여 일반화된 이미지에서 VM 만들기
description: Azure CLI를 사용하여 일반화된 이미지 버전에서 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9304f75c1de6a46da960187ef82951bc77df4d96
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688163"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Azure CLI를 사용하여 일반화된 이미지 버전에서 VM 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

Shared Image Gallery에 저장된 [일반화된 이미지 버전](./shared-image-galleries.md#generalized-and-specialized-images)에서 VM을 만듭니다. 전문화된 이미지로 VM을 만들려면 [전문화된 이미지에서 VM 만들기](vm-specialized-image-version-powershell.md)를 참조하세요. 


## <a name="get-the-image-id"></a>이미지 ID 가져오기

[az sig image-definition 목록](/cli/azure/sig/image-definition#az_sig_image_definition_list)으로 갤러리의 이미지 정의를 나열하여 정의의 이름과 ID를 확인합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>VM 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 최신 버전의 이미지를 사용하려면 `--image`를 이미지 정의의 ID로 설정합니다. 

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

`--image` 매개 변수에 대한 이미지 버전 ID를 사용하여 특정 버전을 사용할 수도 있습니다. 예를 들어 이미지 버전 *1.0.0* 을 사용하려면 다음을 입력합니다. `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

## <a name="next-steps"></a>다음 단계

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다.