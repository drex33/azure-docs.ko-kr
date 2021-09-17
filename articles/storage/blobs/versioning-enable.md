---
title: Blob 버전 관리 설정 및 관리
titleSuffix: Azure Storage
description: Azure Portal에서 또는 Azure Resource Manager 템플릿을 사용하여 BLOB 버전 관리를 사용하는 방법 알아보기.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/07/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: bcf69aba20a53e101cf85cf8ad9e249ca72d3a93
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470493"
---
# <a name="enable-and-manage-blob-versioning"></a>Blob 버전 관리 설정 및 관리

Blob 스토리지 버전 관리를 사용하여 Blob이 수정되거나 삭제될 때 Blob의 이전 버전을 자동으로 유지할 수 있습니다. Blob 버전 관리를 사용하는 경우 데이터를 잘못 수정하거나 삭제한 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다.

이 문서는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 스토리지 계정에 대한 Blob 버전 관리를 사용하거나 사용하지 않는 방법을 보여 줍니다. Blob 버전 관리에 대한 자세한 정보는 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

## <a name="enable-blob-versioning"></a>BLOB 버전 관리 사용

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정에 대한 Blob 버전 관리를 사용하려면 다음을 수행합니다.

1. 포털에서 스토리지 계정으로 이동합니다.
1. **Blob service** 에서 **데이터 보호** 를 선택합니다.
1. **버전 관리** 섹션에서 **사용** 을 선택합니다.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Azure Portal에서 Blob 버전 관리 사용 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 대한 Blob 버전 관리를 사용하려면 먼저 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈 버전 2.3.0 이상을 설치합니다. 그런 다음, 다음 예제와 같이 [업데이트-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) 명령을 호출하여 버전 관리를 사용합니다. 꺾쇠 괄호로 묶인 값을 다음과 같이 고유한 값으로 바꿔야 합니다.

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 Blob 버전 관리를 사용하려면, 먼저 Azure CLI 버전 2.2.0 이상을 설치합니다. 그런 다음, 다음 예제와 같이 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출하여 버전 관리를 사용합니다. 꺾쇠 괄호로 묶인 값을 다음과 같이 고유한 값으로 바꿔야 합니다.

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[템플릿](#tab/template)

템플릿에서 Blob 버전 관리를 사용하도록 설정하려면 **IsVersioningEnabled** 속성을 **true** 로 설정하여 템플릿을 만듭니다. 다음 단계는 Azure Portal에서 템플릿을 만드는 방법을 보여 줍니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.
1. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
1. **템플릿 배포** 를 선택하고 **만들기** 를 선택한 다음 **편집기에서 고유의 템플릿 빌드** 를 선택합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣습니다. `<accountName>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.
1. 템플릿을 저장하는 경우
1. 해당 계정의 리소스 그룹을 지정한 다음 **구매** 단추를 선택하여 템플릿을 배포하고 Blob 버전 관리를 사용하도록 설정합니다.

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
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Azure Portal 템플릿을 사용하여 리소스를 배포하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 리소스 배포](../../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>새 버전을 트리거하기 위해 Blob 수정

다음 코드 예제에서는 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 이상 버전의 .NET에 대한 Azure Storage 클라이언트 라이브러리를 사용하여 새 버전 만들기를 트리거하는 방법을 보여 줍니다. 예제를 실행하기 전에 스토리지 계정에 대한 버전 관리를 사용하도록 설정했는지 확인합니다.

예제에서는 블록 Blob을 만든 다음 Blob의 메타데이터를 업데이트합니다. Blob의 메타데이터를 업데이트하면 새 버전 생성이 트리거됩니다. 예제에서는 초기 버전 및 현재 버전을 검색하고 현재 버전에만 메타데이터가 포함되어 있음을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Blob 버전 목록

.NET v12 클라이언트 라이브러리를 사용하여 Blob 버전 또는 스냅샷을 나열하려면 **버전** 필드를 사용하여 [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) 매개 변수를 지정합니다.

다음 코드 예제에서는 버전 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 이상의 .NET에 대한 Azure Storage 클라이언트 라이브러리를 사용하여 BLOB 버전을 나열하는 방법을 보여 줍니다. 예제를 실행하기 전에 스토리지 계정에 대한 버전 관리를 사용하도록 설정했는지 확인합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>다음 단계

- [Blob 버전 관리](versioning-overview.md)
- [Azure Storage Blob에 대한 일시 삭제](./soft-delete-blob-overview.md)
