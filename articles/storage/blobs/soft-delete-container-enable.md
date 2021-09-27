---
title: 컨테이너에 대한 일시 삭제 사용 및 관리
titleSuffix: Azure Storage
description: 컨테이너 일시 삭제를 사용하여 데이터를 잘못 수정하거나 삭제할 때 더 쉽게 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 208f4ff6b43a722e14a788d052350117aeac56dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562498"
---
# <a name="enable-and-manage-soft-delete-for-containers"></a>컨테이너에 대한 일시 삭제 사용 및 관리

컨테이너 일시 삭제는 데이터가 실수로 또는 잘못하여 수정되거나 삭제되지 않도록 보호합니다. 스토리지 계정에 대해 컨테이너 일시 삭제를 사용하면 지정한 보존 기간 내에 컨테이너 및 해당 콘텐츠가 삭제된 후 복구될 수 있습니다. 컨테이너 일시 삭제에 대한 자세한 내용은 [컨테이너에 대한 일시 삭제](soft-delete-container-overview.md)를 참조하세요.

엔드투엔드 데이터 보호의 경우 Blob 및 Blob 버전에 대해 일시 삭제를 사용하는 것이 좋습니다. 또한 Blob에 대해 일시 삭제를 사용하는 방법을 알아보려면 [Blob에 대한 일시 삭제 사용 및 관리](soft-delete-blob-enable.md)를 참조하세요. Blob 버전 관리를 사용하는 방법을 알아보려면 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

## <a name="enable-container-soft-delete"></a>컨테이너 일시 삭제 사용

Azure Portal, PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 언제든지 스토리지 계정에 대해 컨테이너 일시 삭제를 사용하거나 사용하지 않을 수 있습니다. 컨테이너 일시 삭제의 보존 기간을 최소 7일로 설정하는 것이 좋습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정에 대해 컨테이너 일시 삭제를 사용하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.
1. **데이터 관리** 에서 **데이터 보호** 설정을 찾습니다.
1. **컨테이너에 대해 일시 삭제 사용** 을 선택합니다.
1. 1~365일 사이의 보존 기간을 지정합니다.
1. 변경 내용을 저장합니다.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Azure Portal에서 컨테이너 일시 삭제를 사용하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 컨테이너 일시 삭제를 사용하려면 먼저 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈, 버전 3.9.0 이상을 설치합니다. 다음으로 **Enable-AzStorageContainerDeleteRetentionPolicy** 명령을 호출하고 보존 기간에 대한 기간(일)을 지정합니다. 꺾쇠 괄호로 묶인 값을 다음과 같이 고유한 값으로 바꿔야 합니다.

```azurepowershell-interactive
Enable-AzStorageContainerDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

컨테이너 일시 삭제를 사용하지 않으려면 **Disable-AzStorageContainerDeleteRetentionPolicy** 명령을 호출합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 컨테이너 일시 삭제를 사용하려면 먼저 Azure CLI, 버전 2.26.0 이상을 설치합니다. 다음으로 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출하고 보존 기간에 대한 기간(일)을 지정합니다. 꺾쇠 괄호로 묶인 값을 다음과 같이 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account blob-service-properties update \
    --enable-container-delete-retention true \
    --container-delete-retention-days 7 \
    --account-name <storage-account> \
    --resource-group <resource_group>
```

컨테이너 일시 삭제를 사용하지 않으려면 `--enable-container-delete-retention` 매개 변수에 대해 `false`를 지정합니다.

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿에서 컨테이너 일시 삭제를 사용하려면 **containerDeleteRetentionPolicy** 속성을 설정하는 템플릿을 만듭니다. 다음 단계에서는 Azure Portal에서 템플릿을 만드는 방법을 설명합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.
1. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
1. **템플릿 배포** 를 선택하고 **만들기** 를 선택한 다음 **편집기에서 고유의 템플릿 빌드** 를 선택합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣습니다. `<account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

1. 보존 기간을 지정합니다. 기본값은 7입니다.
1. 템플릿을 저장하는 경우
1. 해당 계정의 리소스 그룹을 지정한 다음, **검토 + 만들기** 단추를 선택하여 템플릿을 배포하고 컨테이너 일시 삭제를 사용합니다.

---

## <a name="view-soft-deleted-containers"></a>일시 삭제된 컨테이너 보기

일시 삭제를 사용하면 Azure Portal에서 일시 삭제된 컨테이너를 볼 수 있습니다. 일시 삭제된 컨테이너는 지정된 보존 기간 동안 표시됩니다. 보존 기간이 만료되면 일시 삭제된 컨테이너가 영구적으로 삭제되고 더 이상 표시되지 않습니다.

Azure Portal에서 일시 삭제된 컨테이너를 보려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동하여 컨테이너 목록을 확인합니다.
1. 삭제된 컨테이너 표시 스위치를 전환하여 삭제된 컨테이너를 목록에 포함합니다.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Azure Portal에서 일시 삭제된 컨테이너를 보는 방법을 보여 주는 스크린샷":::

## <a name="restore-a-soft-deleted-container"></a>일시 삭제된 컨테이너 복원

일시 삭제된 컨테이너와 해당 콘텐츠를 보존 기간 내에 복원할 수 있습니다. Azure Portal에서 일시 삭제된 컨테이너를 복원하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동하여 컨테이너 목록을 확인합니다.
1. 복원하려는 컨테이너에 대한 바로 가기 메뉴를 표시하고 메뉴에서 **삭제 취소** 를 선택합니다.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Azure Portal에서 일시 삭제된 컨테이너를 복원하는 방법을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [컨테이너에 대한 일시 삭제](soft-delete-container-overview.md)
- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [Blob 버전 관리](versioning-overview.md)
