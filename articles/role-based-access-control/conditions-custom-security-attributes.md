---
title: 태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용(미리 보기) - Azure ABAC
description: Azure 역할 할당 조건 및 Azure ABAC(Azure 특성 기반 액세스 제어)를 사용하여 태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스를 허용합니다.
services: active-directory
author: rolyon
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: 2a8ebab29b4ad2f5c59fc1cb65a4deb4c7bd4bd1
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133217783"
---
# <a name="allow-read-access-to-blobs-based-on-tags-and-custom-security-attributes-preview"></a>태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용(미리 보기)

> [!IMPORTANT]
> 사용자 지정 보안 특성은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 ABAC(특성 기반 액세스 제어) 조건을 사용하여 Blob 인덱스 태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스를 허용하는 방법을 알아봅니다. 이렇게 하면 Blob에 대한 액세스를 보다 쉽게 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자 지정 보안 특성을 할당하고 Azure AD 테넌트에서 역할 할당 조건을 추가하려면 다음이 필요합니다.

- Azure AD Premium P1 또는 P2 라이선스
- [특성 정의 관리자](../active-directory/roles/permissions-reference.md#attribute-definition-administrator) 및 [특성 할당 관리자](../active-directory/roles/permissions-reference.md#attribute-assignment-administrator)
- [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner)

> [!IMPORTANT]
> 기본적으로 [전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator) 및 기타 관리자 역할에는 사용자 지정 보안 특성을 읽거나 정의하거나 할당할 수 있는 권한이 없습니다. 이러한 필수 조건을 충족하지 않으면 조건 작성기에서 보안 주체/사용자 특성이 표시되지 않습니다.

## <a name="condition"></a>조건

이 문서에서는 사용자에게 Blob 인덱스 태그와 일치하는 사용자 지정 보안 특성이 있는 경우 Blob에 대한 읽기 액세스를 허용합니다. 이렇게 하려면 역할 할당에 조건을 추가합니다.
 
![조건이 있는 역할 할당의 다이어그램.](./media/conditions-custom-security-attributes/condition-principal-attribute.png)

예를 들어, 이 특성이 있는 경우 `Project=Baker` `Project=Baker` Blob 인덱스 태그가 있는 Blob만 읽을 수 있습니다. 마찬가지로 Chandra는 를 통해서만 Blob을 읽을 수 `Project=Cascade` 있습니다.

![태그 및 사용자 지정 보안 특성을 기반으로 Blob에 대한 읽기 액세스를 보여 주는 다이어그램](./media/conditions-custom-security-attributes/condition-principal-attribute-example.png)

코드에서 조건의 모양은 다음과 같습니다.

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})
 )
 OR 
 (
  @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project] StringEquals @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
 )
)
```

조건에 대한 자세한 내용은 [Azure ABAC(Azure 특성 기반 액세스 제어)란?을 참조하세요. (미리 보기)](conditions-overview.md).

## <a name="step-1-add-a-new-custom-security-attribute"></a>1단계: 새 사용자 지정 보안 특성 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1.   >  **사용자 지정 보안 특성 Azure Active Directory(미리 보기)** 를 클릭합니다.

1. 및 값을 가진 라는 특성을 `Project` `Baker` `Cascade` 추가합니다. 또는 기존 특성을 사용합니다. 자세한 내용은 [Azure AD에서 사용자 지정 보안 특성 추가 또는 비활성화를 참조하세요.](../active-directory/fundamentals/custom-security-attributes-add.md)

    ![사용자 지정 보안 특성을 추가하는 스크린샷.](./media/conditions-custom-security-attributes/project-attribute-add.png)

## <a name="step-2-assign-the-custom-security-attribute-to-a-user"></a>2단계: 사용자에게 사용자 지정 보안 특성 할당

1. Azure AD에서 보안 그룹을 만듭니다.

1. 사용자를 그룹의 구성원으로 추가합니다.

1. 값이 인 `Project` 특성을 `Cascade` 사용자에게 할당합니다. 자세한 내용은 [사용자에 대한 사용자 지정 보안 특성 할당 또는 제거를 참조하세요.](../active-directory/enterprise-users/users-custom-security-attributes.md) 

    ![사용자 지정 보안 특성을 할당하는 스크린샷.](./media/conditions-custom-security-attributes/project-users-attributes-assign.png)

1. **저장을** 클릭하여 할당을 저장해야 합니다.

## <a name="step-3-set-up-storage-and-blob-index-tags"></a>3단계: 스토리지 및 Blob 인덱스 태그 설정

1. Blob 인덱스 태그 기능과 호환되는 스토리지 계정을 만듭니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기](../storage/blobs/storage-manage-find-blobs.md)를 참조하세요.

1. 스토리지 계정 내에 새 컨테이너를 만들고 **공용 액세스 수준을** **프라이빗(익명 액세스 없음)으로** 설정합니다.

1. 인증 유형을 **Azure AD 사용자 계정** 으로 설정합니다.

1. 텍스트 파일을 컨테이너에 업로드 다음 Blob 인덱스 태그를 설정합니다.

    | 파일 | 키 | 값 |
    | --- | --- | --- |
    | 2016년 3월 2 | 프로젝트 | Baker |
    | 계단식 텍스트 파일 | 프로젝트 | 계단식 배열 |
 
## <a name="step-4-assign-storage-blob-data-reader-role-with-a-condition"></a>4단계: 조건을 사용하여 Storage Blob 데이터 판독기 역할 할당

1. 새 탭을 열고 [Azure Portal](https://portal.azure.com)로그인합니다.

1. 스토리지 계정이 있는 리소스 그룹을 엽니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가** 를 클릭합니다.
 
1. **역할** 탭에서 Storage [Blob 데이터 판독기](built-in-roles.md#storage-blob-data-reader) 역할을 선택합니다.
 
1. **멤버** 탭에서 이전에 만든 보안 그룹을 선택합니다.

1. (선택 사항) 사용자에게 **Blob 인덱스 태그와 일치하는 사용자 지정 보안 특성이** 있는 경우 **설명** 상자에 Blob에 대한 읽기 권한을 입력합니다.

1. **조건(옵션)** 탭에서 **조건 추가** 를 클릭합니다.

    역할 할당 조건 추가 페이지가 표시됩니다.
 
1. **작업 추가** 섹션에서 **작업 추가** 를 클릭합니다.

    작업 선택 창이 표시됩니다. 이 창은 조건 대상이 될 역할 할당을 기준으로 데이터 작업을 필터링한 목록입니다.
 
1. **태그 조건이 있는 Blob에서 콘텐츠 읽기를** 클릭한 다음 **선택을** 클릭합니다.

1. 빌드 **식** 섹션에서 **추가를** 클릭합니다.

1. 다음 설정을 입력합니다.

    | 설정 | 값 |
    | --- | --- |
    | 특성 원본 | 주 서버 |
    | attribute | &lt;attributeset &gt; _Project |
    | 연산자 | StringEquals |
    | 옵션 | attribute |
    | 특성 원본 | 리소스 |
    | attribute | Blob 인덱스 태그 [키의 값] |
    | Key | Project |

    > [!NOTE]
    > Principal이 특성 원본에 옵션으로 나열되지 않은 경우 [1단계: 새](#step-1-add-a-new-custom-security-attribute)사용자 지정 보안 특성 추가의 앞에서 설명한 대로 사용자 지정 보안 특성을 정의했는지 확인합니다.

    ![시각적 편집기에서 표시되는 보안 주체 특성을 사용하는 조건의 스크린샷.](./media/conditions-custom-security-attributes/condition-principal-visual.png)

1. **편집기 형식** 으로 스크롤하고 **코드** 를 클릭합니다.

    조건은 다음과 유사합니다.

    ```
    (
     (
      !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})
     )
     OR 
     (
      @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project] StringEquals @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
     )
    )
    ```

1. **저장을** 클릭하여 조건을 저장합니다.

1. 검토 + 할당 탭에서 **검토 + 할당을** 클릭하여 조건과 함께 Storage Blob 데이터 판독기 역할을 할당합니다.

## <a name="step-5-assign-reader-role"></a>5단계: 리더 역할 할당

- 이전 단계를 반복하여 리소스 그룹 범위에서 보안 그룹에 대한 [읽기](built-in-roles.md#reader) 역할을 할당합니다.

    > [!NOTE]
    > 일반적으로 리더 역할을 할당할 필요가 없습니다. 그러나 이 작업을 수행하면 Azure Portal을 사용하여 조건을 테스트할 수 있습니다.

## <a name="step-6-test-the-condition"></a>6단계: 조건 테스트

1. 새 창에서 [Azure Portal](https://portal.azure.com)을 엽니다.

1. 사용자 지정 보안 특성으로 만든 사용자로 `Project=Cascade` 로그인합니다.

1. 만든 스토리지 계정 및 컨테이너를 엽니다.

1. 인증 방법이 **액세스 키** 가 아닌 **Azure AD 사용자 계정** 으로 설정되어 있는지 확인합니다.

    ![테스트 파일이 포함된 스토리지 컨테이너의 스크린샷.](./media/conditions-custom-security-attributes/test-storage-container.png)

1. Baker 텍스트 파일을 클릭합니다.

    Blob을 보거나 다운로드할 수 **없으며** 권한 부여 실패 메시지가 표시되어야 합니다.
 
1. 계단식 텍스트 파일을 클릭합니다.

    Blob을 보고 다운로드할 수 있어야 합니다.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 사용하여 역할 할당 조건을 추가할 수도 있습니다. 다음 명령은 조건을 추가하는 방법을 보여줍니다. 자세한 내용은 [자습서: Azure PowerShell(미리 보기)를 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가를](../storage/common/storage-auth-abac-powershell.md)참조하세요.

### <a name="add-a-condition"></a>조건 추가

1. [커넥트-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하고 디렉터리에 사용자 액세스 관리자 또는 소유자로 로그인하는 것처럼 보이는 지침을 따릅니다.

    ```powershell
    Connect-AzAccount
    ```

1. [Get-AzRoleAssignment를](/powershell/module/az.resources/get-azroleassignment) 사용하여 보안 그룹에 할당한 역할 할당을 얻습니다.

    ```powershell
    $groupRoleAssignment = Get-AzRoleAssignment -ObjectId <groupObjectId> -Scope <scope>
    ```
    
1. 역할 `Condition` 할당 개체의 속성을 설정합니다. 특성 집합 이름을 사용해야 합니다.

    ```powershell
    $groupRoleAssignment.Condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions' })) OR (@Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project] StringEquals @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>]))"
    ```

1. 역할 `ConditionVersion` 할당 개체의 속성을 설정합니다.

    ```powershell
    $groupRoleAssignment.ConditionVersion = "2.0"
    ```

1. [Set-AzRoleAssignment를](/powershell/module/az.resources/set-azroleassignment) 사용하여 역할 할당을 업데이트합니다.

    ```powershell
    Set-AzRoleAssignment -InputObject $groupRoleAssignment
    ```

### <a name="test-the-condition"></a>조건 테스트

1. 새 PowerShell 창에서 [커넥트-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 보안 그룹의 구성원으로 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

1. [New-AzStorageContext를](/powershell/module/az.storage/new-azstoragecontext) 사용하여 스토리지 계정에 대한 컨텍스트를 설정합니다.

    ```powershell
    $bearerCtx = New-AzStorageContext -StorageAccountName <accountName>
    ```

1. [Get-AzStorageBlob을](/powershell/module/az.storage/get-azstorageblob) 사용하여 이 파일을 읽으려고 합니다.

    ```powershell
    Get-AzStorageBlob -Container <containerName> -Blob <blobNameBaker> -Context $bearerCtx
    ```

    Blob을 읽을 수 **없으며** 권한 부여 실패 메시지가 표시되어야 합니다.

    ```powershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ...
    ```

1. [Get-AzStorageBlob을](/powershell/module/az.storage/get-azstorageblob) 사용하여 Cascade 파일을 읽으려고 합니다.

    ```powershell
    Get-AzStorageBlob -Container <containerName> -Blob <blobNameCascade> -Context $bearerCtx
    You should be able to read the blob.
    AccountName: <storageAccountName>, ContainerName: <containerName>
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="azure-cli"></a>Azure CLI

Azure CLI 사용하여 역할 할당 조건을 추가할 수도 있습니다. 다음 명령은 조건을 추가하는 방법을 보여줍니다. 자세한 내용은 [자습서: Azure CLI(미리 보기)를 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가를 참조하세요.](../storage/common/storage-auth-abac-cli.md)

### <a name="add-a-condition"></a>조건 추가

1. [az login](/cli/azure/reference-index#az_login) 명령을 사용하고 디렉터리에 사용자 액세스 관리자 또는 소유자로 로그인하는 것처럼 보이는 지침을 따릅니다.

    ```azurecli
    az login
    ```

1. [az role assignment list를](/cli/azure/role/assignment#az_role_assignment_list) 사용하여 보안 그룹에 할당한 역할 할당을 얻습니다.

    ```azurecli
    az role assignment list --assignee <groupObjectId> --scope <scope>
    ```

1. 다음 형식으로 JSON 파일을 만듭니다.

    ```azurecli
    {
        "canDelegate": null,
        "condition": "",
        "conditionVersion": "",
        "description": "",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{groupObjectId}",
        "principalName": "{principalName}",
        "principalType": "Group",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. 속성을 `condition` 업데이트합니다. 특성 집합 이름을 사용해야 합니다.

    ```azurecli
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions' })) OR (@Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project] StringEquals @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]))",
    ```

1. 속성을 `conditionVersion` 업데이트합니다.

    ```azurecli
    "conditionVersion": "2.0",
    ```

1. [az role assignment update를](/cli/azure/role/assignment#az_role_assignment_update) 사용하여 역할 할당에 조건을 추가합니다.

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```

### <a name="test-the-condition"></a>조건 테스트

1. 새 명령 창에서 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 보안 그룹의 구성원으로 로그인합니다.

    ```azurecli
    az login
    ```

1. [az storage blob show를](/cli/azure/storage/blob#az_storage_blob_show) 사용하여 이 파일의 속성을 읽으려고 합니다.

    ```azurecli
    az storage blob show --account-name <storageAccountName> --container-name <containerName> --name <blobNameBaker> --auth-mode login
    ```

    Blob을 읽을 수 **없으며** 권한 부여 실패 메시지가 표시되어야 합니다.

    ```azurecli
    You do not have the required permissions needed to perform this operation.
    ...
    ```

1. [az storage blob show를](/cli/azure/storage/blob#az_storage_blob_show) 사용하여 Cascade 파일의 속성을 읽으려고 합니다.

    ```azurecli
    az storage blob show --account-name <storageAccountName> --container-name <containerName> --name <blobNameCascade> --auth-mode login
    You should be able to read the blob.
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
    ...
    }
    ```

## <a name="next-steps"></a>다음 단계

- [Azure AD의 사용자 지정 보안 특성이란? (미리 보기)](../active-directory/fundamentals/custom-security-attributes-overview.md)
- [Azure 역할 할당 조건 형식 및 구문(미리 보기)](conditions-format.md)
- [Azure 역할 할당 조건 예(미리 보기)](../storage/common/storage-auth-abac-examples.md?toc=/azure/role-based-access-control/toc.json)
