---
title: Azure CLI로 정규화된 이미지에서 확장 집합 만들기
description: Azure CLI를 사용 하 여 Azure Compute 갤러리에서 일반화 된 이미지를 사용 하 여 확장 집합을 만듭니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: 3512a5e59cfa7f0f0bd9562498b4644454da7390
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459941"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Azure CLI로 정규화된 이미지에서 확장 집합 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

Azure CLI를 사용 하 여 [Azure Compute 갤러리](../virtual-machines/shared-image-galleries.md) 에 저장 된 일반화 된 이미지 버전에서 확장 집합을 만듭니다. 특수 이미지 버전을 사용하여 확장 집합을 만들려면 [특수 이미지에서 확장 집합 인스턴스 만들기](instance-specialized-image-version-cli.md)를 참조하세요.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.4.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)를 사용하여 갤러리에서 이미지 정의를 나열하면 해당 이름과 정의의 ID를 볼 수 있습니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

[`az vmss create`](/cli/azure/vmss#az_vmss_create)를 사용하여 확장 집합을 만듭니다. 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 확장 집합 인스턴스를 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 확장 집합 인스턴스를 만들 수 있습니다. 특정 이미지가 지역에서 삭제 또는 제거되었기 때문에 이용할 수 없는 경우, 해당 이미지 버전을 사용하면 자동화에 실패할 수 있다는 점에 유의하세요. 특정 이미지 버전이 필요하지 않는 한 새 VM을 만들기 위해 이미지 정의 ID를 사용하는 것이 좋습니다.

다음 예제에서는 *myImageDefinition* 이미지의 최신 버전에서 인스턴스를 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계
[Azure Image Builder(미리 보기)](../virtual-machines/image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](../virtual-machines/linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

템플릿을 사용 하 여 Azure Compute 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [Azure 계산 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [Azure 계산 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Azure 계산 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](../virtual-machines/shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../virtual-machines/troubleshooting-shared-images.md)을 참조하세요.