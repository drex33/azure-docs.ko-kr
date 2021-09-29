---
title: '자습서: Azure CLI를 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure CLI 및 Azure ABAC(Azure 특성 기반 액세스 제어)를 통해 역할 할당 조건을 추가하여 Blob에 대한 액세스를 제한합니다.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 4050d9452a8253bbfe468b180a8eaa701e2a3684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587464"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-cli-preview"></a>자습서: Azure CLI를 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

대부분의 경우 역할 할당은 Azure 리소스에 필요한 사용 권한을 부여합니다. 그러나 경우에 따라 역할 할당 조건을 추가하여 보다 세분화된 액세스 제어를 제공하는 것이 좋습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - 역할 할당에 조건 추가
> - BLOB 인덱스 태그를 기반으로 BLOB에 대한 액세스 제한

## <a name="prerequisites"></a>사전 요구 사항

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](../../role-based-access-control/conditions-prerequisites.md)을 참조하세요.

## <a name="condition"></a>조건

이 자습서에서는 특정 태그를 사용하여 BLOB에 대한 액세스를 제한합니다. 예를 들어 Chandra에서 Project=Cascade 태그가 있는 파일만 읽을 수 있도록 역할 할당에 조건을 추가합니다.

![조건이 있는 역할 할당의 다이어그램.](./media/shared/condition-role-assignment-rg.png)

Chandra가 Project=Cascade 태그 없이 BLOB을 읽으려고 하면 액세스가 허용되지 않습니다.

![Project=Cascade 태그를 사용한 BLOB의 읽기 권한을 보여주는 다이어그램](./media/shared/condition-access.png)

코드에서 조건은 다음과 같습니다.

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

## <a name="step-1-sign-in-to-azure"></a>1단계: Azure에 로그인

1. [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../../role-based-access-control/built-in-roles.md#owner)로 디렉터리에 로그인하기 위해 표시되는 지침을 따릅니다.

    ```azurecli
    az login
    ```

1. [az account show](/cli/azure/account#az_account_show)를 사용하여 구독 ID를 가져옵니다.

    ```azurecli
    az account show
    ```

1. 구독 ID를 확인하고 변수를 초기화합니다.

    ```azurecli
    subscriptionId="<subscriptionId>"
    ```

## <a name="step-2-create-a-user"></a>2단계: 사용자 만들기

1. [az ad user create](/cli/azure/ad/user#az_ad_user_create)를 사용하여 사용자를 만들거나 기존 사용자를 찾습니다. 이 자습서에서는 Chandra를 예제로 사용합니다.

1. 사용자의 개체 ID에 대한 변수를 초기화합니다.

    ```azurecli
    userObjectId="<userObjectId>"
    ```

## <a name="step-3-set-up-storage"></a>3단계: 스토리지 설정

Azure AD 자격 증명을 사용하거나 스토리지 계정 액세스 키를 사용하여 Azure CLI에서 Blob 스토리지에 대한 액세스 권한을 부여할 수 있습니다. 이 문서에서는 Azure AD를 사용하여 Blob 스토리지 작업에 권한을 부여하는 방법을 보여 줍니다. Azure Storage에 대한 자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Blob 만들기, 다운로드, 나열하기](../blobs/storage-quickstart-blobs-cli.md)를 참조하세요.

1. [az storage account](/cli/azure/storage/account)를 사용하여 Blob 인덱스 기능과 호환되는 스토리지 계정을 만듭니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)를 참조하세요.

1. [az storage container](/cli/azure/storage/container)를 사용하여 스토리지 계정에서 새 Blob 컨테이너를 만들고 공용 액세스 수준을 **비공개(익명 액세스 차단)** 로 설정합니다.

1. [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload)를 사용하여 텍스트 파일을 컨테이너에 업로드합니다.

1. 텍스트 파일에 다음 Blob 인덱스 태그를 추가합니다. 자세한 내용은 [Blob 인덱스 태그(미리 보기)를 사용하여 Azure Blob Storage 데이터 관리 및 찾기를 참조하세요](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > BLOB은 임의의 사용자 정의 키 값 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다.

    | 키 | 값 |
    | --- | --- |
    | 프로젝트  | 계단식 배열 |

1. 두 번째 텍스트 파일을 컨테이너에 업로드합니다.

1. 다음 Blob 인덱스 태그를 두 번째 텍스트 파일에 추가합니다.

    | 키 | 값 |
    | --- | --- |
    | 프로젝트  | Baker |

1. 사용한 이름으로 다음 변수를 초기화합니다.

    ```azurecli
    resourceGroup="<resourceGroup>"
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameCascade="<blobNameCascade>"
    blobNameBaker="<blobNameBaker>"
    ```

## <a name="step-4-assign-a-role-with-a-condition"></a>4단계: 조건이 있는 역할 할당

1. [Storage Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할 변수를 초기화합니다.

    ```azurecli
    roleDefinitionName="Storage Blob Data Reader"
    roleDefinitionId="2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. 리소스 그룹의 범위를 초기화합니다.

    ```azurecli
    scope="/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 조건을 초기화합니다.

    ```azurecli
    condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
    ```

    Bash에서 기록 확장을 사용하는 경우 느낌표(!)로 인해 메시지 `bash: !: event not found`가 표시될 수 있습니다. 이 경우 명령 `set +H`를 통해 기록 확장을 사용하지 않도록 설정할 수 있습니다. 기록 확장을 다시 사용하도록 설정하려면 `set -H`를 사용합니다.

    Bash에서 달러 기호($)는 확장에 특별한 의미가 있습니다. 조건에 달러 기호($)가 포함된 경우 백슬래시(\\)를 접두사로 사용해야 할 수 있습니다. 예를 들어 이 조건은 달러 기호를 사용하여 태그 키 이름을 표시합니다. Bash의 따옴표 규칙에 대한 자세한 내용은 [큰따옴표](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)를 참조하세요.

1. 조건 버전 및 설명을 초기화합니다.

    ```azurecli
    conditionVersion="2.0"
    description="Read access to blobs with the tag Project=Cascade"
    ```

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 리소스 그룹 범위에서 사용자에게 조건과 함께 [Storage Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할을 할당합니다.

    ```azurecli
    az role assignment create --assignee-object-id $userObjectId --scope $scope --role $roleDefinitionId --description "$description" --condition "$condition" --condition-version $conditionVersion
    ```

    다음은 출력의 예입니다.

    ```azurecli
    {
      "canDelegate": null,
      "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
      "conditionVersion": "2.0",
      "description": "Read access to blobs with the tag Project=Cascade",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "name": "{roleAssignmentId}",
      "principalId": "{userObjectId}",
      "principalType": "User",
      "resourceGroup": "{resourceGroup}",
      "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

## <a name="step-5-optional-view-the-condition-in-the-azure-portal"></a>5단계: (선택 사항)Azure Portal에서 조건 보기

1. Azure Portal에서 리소스 그룹을 엽니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. 역할 할당 탭에서 역할 할당을 찾습니다.

1. **조건** 열에서 **보기/편집** 을 클릭하여 조건을 확인합니다.

    ![Azure Portal에서 역할 할당 조건을 추가하는 스크린샷](./media/shared/condition-view.png)

## <a name="step-6-test-the-condition"></a>6단계: 조건 테스트

1. 새 명령 창을 엽니다.

1. [az login](/cli/azure/reference-index#az_login)을 사용하여 Chandra로 로그인합니다.

    ```azurecli
    az login
    ```

1. 사용한 이름으로 다음 변수를 초기화합니다.

    ```azurecli
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameBaker="<blobNameBaker>"
    blobNameCascade="<blobNameCascade>"
    ```

1. [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show)를 사용하여 Baker 프로젝트에 대한 파일의 속성을 읽으려고 합니다.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameBaker --auth-mode login
    ```

    다음은 출력의 예입니다. 추가한 조건으로 인해 파일을 읽을 **수 없습니다.**

    ```azurecli
    You do not have the required permissions needed to perform this operation.
    Depending on your operation, you may need to be assigned one of the following roles:
        "Storage Blob Data Contributor"
        "Storage Blob Data Reader"
        "Storage Queue Data Contributor"
        "Storage Queue Data Reader"

    If you want to use the old authentication method and allow querying for the right account key, please use the "--auth-mode" parameter and "key" value.
    ```

1. Cascade 프로젝트에 대한 파일의 속성을 읽습니다.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameCascade --auth-mode login 
    ```

    다음은 출력의 예입니다. Project=Cascade 태그가 있으므로 파일의 속성을 읽을 수 있습니다.

    ```azurecli
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
      "isAppendBlobSealed": null,
      "isCurrentVersion": null,
      "lastAccessedOn": null,
      "metadata": {},
      "name": "<blobNameCascade>",
      "objectReplicationDestinationPolicy": null,
      "objectReplicationSourceProperties": [],
      "properties": {
        "appendBlobCommittedBlockCount": null,
        "blobTier": "Hot",
        "blobTierChangeTime": null,
        "blobTierInferred": true,
        "blobType": "BlockBlob",
        "contentLength": 7,
        "contentRange": null,

      ...

    }
    ```

## <a name="step-7-optional-edit-the-condition"></a>7단계: (선택 사항)조건 편집

1. 다른 명령 창에서 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)를 사용하여 추가한 역할 할당을 가져옵니다.

    ```azurecli
    az role assignment list --assignee $userObjectId --resource-group $resourceGroup
    ```

    다음과 유사하게 출력됩니다.

    ```azurecli
    [
      {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ]
    ```

1. 다음 형식으로 JSON 파일을 만들고 `condition` 및 `description` 속성을 업데이트합니다.

    ```json
    {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Baker'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade or Project=Baker",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update)를 사용하여 역할 할당에 대한 조건을 업데이트합니다.

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```

## <a name="step-8-clean-up-resources"></a>8단계: 리소스 정리

1. [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete)를 사용하여 추가한 역할 할당 및 조건을 제거합니다.

    ```azurecli
    az role assignment delete --assignee $userObjectId --role "$roleDefinitionName" --resource-group $resourceGroup
    ```

1. 만든 스토리지 계정을 삭제합니다.

1. 만든 사용자를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제](storage-auth-abac-examples.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure 역할 할당 조건의 형식 및 구문](../../role-based-access-control/conditions-format.md)
