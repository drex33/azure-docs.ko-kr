---
title: 테넌트 간에 갤러리 이미지 공유
description: 공유 이미지 갤러리를 통해 Azure 테넌트 간에 공유되는 이미지를 사용하여 확장 집합을 만드는 방법을 알아봅니다.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b1833fe7c692529649e078e18e17e9333d74bfa6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693205"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Shared Image Gallery를 사용하여 테넌트 간에 이미지 공유

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Azure CLI를 사용하여 크기 집합 만들기

appID, 앱 키 및 테넌트 1의 ID를 사용하여 테넌트 1에 대한 서비스 주체를 로그인합니다. 필요한 경우 `az account show --query "tenantId"`를 사용하여 테넌트 ID를 가져올 수 있습니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
appID, 앱 키 및 테넌트 2의 ID를 사용하여 테넌트 2에 대한 서비스 주체를 로그인합니다.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

확장 집합을 만듭니다. 예시로 사용된 정보를 사용자의 정보로 바꿉니다.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../virtual-machines/troubleshooting-shared-images.md)을 수행할 수 있습니다.
