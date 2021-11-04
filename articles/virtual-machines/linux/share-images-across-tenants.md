---
title: 테넌트 간에 갤러리 이미지 공유
description: Azure Compute 갤러리와 Azure CLI를 사용 하 여 Azure 테 넌 트 간에 VM 이미지를 공유 하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 525e1f51629fe698d1c9f2c3cb9454ddd0a5d183
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448902"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 테넌트 간 갤러리 VM 이미지 공유

Azure 계산 갤러리를 사용 하면 Azure RBAC를 사용 하 여 이미지를 공유할 수 있습니다. Azure RBAC를 사용하여 테넌트 내에서 이미지를 공유하고 테넌트 외부의 개인에게도 이미지를 공유할 수 있습니다. 이 간단한 공유 옵션에 대한 자세한 내용은 [갤러리 공유](./shared-images-portal.md#share-the-gallery)를 참조하세요.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 포털을 사용하여 이미지의 VM을 다른 Azure 테넌트에 배포할 수 없습니다. 테넌트 간에 공유된 이미지에서 VM을 생성하려면 Azure CLI 또는 [PowerShell](../windows/share-images-across-tenants.md)을 사용해야 합니다.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI를 사용하여 VM 만들기

appID, 앱 키, 테넌트 1의 ID를 사용하여 테넌트 1에 대한 서비스 주체를 로그인합니다. 필요한 경우 `az account show --query "tenantId"`를 사용하여 테넌트 ID를 가져올 수 있습니다.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
appID, 앱 키, 테넌트 2의 ID를 사용하여 테넌트 2에 대한 서비스 주체를 로그인합니다.

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

VM을 만듭니다. 예시로 사용된 정보를 사용자의 정보로 바꿉니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>다음 단계

문제가 발생 하는 경우 [갤러리 문제를 해결할](../troubleshooting-shared-images.md)수 있습니다.
