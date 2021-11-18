---
title: '자습서: Azure PowerShell을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure PowerShell 및 Azure ABAC(Azure 특성 기반 액세스 제어)를 통해 역할 할당 조건을 추가하여 Blob에 대한 액세스를 제한합니다.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 11/16/2021
ms.openlocfilehash: 51a37a5cf9010c3a76db99d2874a862dcf908130
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751254"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-powershell-preview"></a>자습서: Azure PowerShell을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)

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
        SubOperationMatches{'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

## <a name="step-1-install-prerequisites"></a>1단계: 필수 구성 요소 설치

1. PowerShell 창을 엽니다.

1. [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule)를 사용하여 설치된 모듈의 버전을 확인합니다.

    ```azurepowershell
    Get-InstalledModule -Name Az
    Get-InstalledModule -Name Az.Resources
    Get-InstalledModule -Name Az.Storage
    ```

1. 필요한 경우 [Install-Module](/powershell/module/powershellget/install-module)을 사용하여 [Az](https://www.powershellgallery.com/packages/Az/), [Az.Resources](https://www.powershellgallery.com/packages/Az.Resources/) 및 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) 모듈에 필요한 버전을 설치합니다.

    ```azurepowershell
    Install-Module -Name Az -RequiredVersion 5.5.0
    Install-Module -Name Az.Resources -RequiredVersion 3.2.1
    Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
    ```

1. PowerShell을 닫았다가 다시 열어 세션을 새로 고칩니다.

## <a name="step-2-sign-in-to-azure"></a>2단계: Azure에 로그인

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하고 표시되는 지침에 따라 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../../role-based-access-control/built-in-roles.md#owner)로 디렉터리에 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)을 사용하여 모든 구독을 나열합니다.

    ```azurepowershell
    Get-AzSubscription
    ```

1. 구독 ID를 확인하고 변수를 초기화합니다.

    ```azurepowershell
    $subscriptionId = "<subscriptionId>"
    ```

1. 구독을 활성 구독으로 설정합니다.

    ```azurepowershell
    $context = Get-AzSubscription -SubscriptionId $subscriptionId
    Set-AzContext $context
    ```

## <a name="step-3-create-a-user"></a>3단계: 사용자 만들기

1. [New-AzureADUser](/powershell/module/azuread/new-azureaduser)를 사용하여 사용자를 만들거나 기존 사용자를 찾습니다. 이 자습서에서는 Chandra를 예제로 사용합니다.

1. 사용자의 개체 ID에 대한 변수를 초기화합니다.

    ```azurepowershell
    $userObjectId = "<userObjectId>"
    ```

## <a name="step-4-set-up-storage"></a>4단계: 스토리지 설정

1. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 사용하여 Blob 인덱스 기능과 호환되는 스토리지 계정을 만듭니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)를 참조하세요.

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)를 사용하여 스토리지 계정에서 새 Blob 컨테이너를 만들고 공용 액세스 수준을 **비공개(익명 액세스 차단)** 로 설정합니다.

1. [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent)를 사용하여 텍스트 파일을 컨테이너에 업로드합니다.

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

    ```azurepowershell
    $resourceGroup = "<resourceGroup>"
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameCascade = "<blobNameCascade>"
    $blobNameBaker = "<blobNameBaker>"
    ```

## <a name="step-5-assign-a-role-with-a-condition"></a>5단계: 조건이 있는 역할 할당

1. [Storage Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할 변수를 초기화합니다.

    ```azurepowershell
    $roleDefinitionName = "Storage Blob Data Reader"
    $roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. 리소스 그룹의 범위를 초기화합니다.

    ```azurepowershell
    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. 조건을 초기화합니다.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
    ```

    PowerShell에서 조건에 달러 기호($)가 포함된 경우에는 앞에 백틱(\`)을 붙여야 합니다. 예를 들어 이 조건은 달러 기호를 사용하여 태그 키 이름을 표시합니다.

1. 조건 버전 및 설명을 초기화합니다.

    ```azurepowershell
    $conditionVersion = "2.0"
    $description = "Read access to blobs with the tag Project=Cascade"
    ```

1. [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 리소스 그룹 범위에서 사용자에게 조건부 [Storage Blob Data Reader](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할을 할당합니다.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
    ```

    다음은 출력의 예입니다.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         SubOperationMatches{'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))
    ```

## <a name="step-6-optional-view-the-condition-in-the-azure-portal"></a>6단계: (선택 사항) Azure Portal에서 조건 보기

1. Azure Portal에서 리소스 그룹을 엽니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. 역할 할당 탭에서 역할 할당을 찾습니다.

1. **조건** 열에서 **보기/편집** 을 클릭하여 조건을 확인합니다.

    ![Azure Portal에서 역할 할당 조건을 추가하는 스크린샷](./media/shared/condition-view.png)

## <a name="step-7-test-the-condition"></a>7단계: 조건 테스트

1. 새 PowerShell 창을 엽니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 사용하여 Chandra로 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. 사용한 이름으로 다음 변수를 초기화합니다.

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 사용하여 스토리지 계정에 보다 쉽게 액세스할 수 있는 특정 컨텍스트를 만듭니다.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)을 사용하여 Baker 프로젝트의 파일을 읽습니다.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    다음은 출력 예제입니다. 추가한 조건으로 인해 파일을 읽을 수 **없습니다.**

    ```azurepowershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sat, 24 Apr 2021 13:26:25 GMT
    At line:1 char:1
    + Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Con ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzStorageBlob], StorageException
        + FullyQualifiedErrorId : StorageException,Microsoft.WindowsAzure.Commands.Storage.Blob.Cmdlet.GetAzureStorageBlob
       Command
    ```

1. Cascade 프로젝트의 파일을 읽습니다.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    다음은 출력 예제입니다. Project=Cascade 태그가 있으므로 파일을 읽을 수 있습니다.

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-8-optional-edit-the-condition"></a>8단계: (선택 사항) 조건 편집

1. 다른 PowerShell 창에서 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용하여 추가한 역할 할당을 가져옵니다.

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 조건을 편집합니다.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Baker'))"
    ```

1. 조건과 설명을 초기화합니다.

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access to blobs with the tag Project=Cascade or Project=Baker"
    ```

1. [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)를 사용하여 역할 할당에 대한 조건을 업데이트합니다.

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    다음은 출력의 예입니다.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade or Project=Baker
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         SubOperationMatches{'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.S
                         torage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
                         StringEquals 'Baker'))
    ```

## <a name="step-9-clean-up-resources"></a>9단계: 리소스 정리

1. [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)를 사용하여 추가한 역할 할당 및 조건을 제거합니다.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $userObjectId -RoleDefinitionName $roleDefinitionName -ResourceGroupName $resourceGroup
    ```

1. 만든 스토리지 계정을 삭제합니다.

1. 만든 사용자를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제](storage-auth-abac-examples.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure 역할 할당 조건의 형식 및 구문](../../role-based-access-control/conditions-format.md)
