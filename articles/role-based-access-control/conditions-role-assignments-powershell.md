---
title: Azure PowerShell(미리 보기)을 사용하여 Azure 역할 할당 조건 추가 또는 편집 - Azure RBAC
description: Azure PowerShell 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 역할 할당에서 ABAC(특성 기반 액세스 제어) 조건을 추가, 편집, 나열 또는 삭제하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: eaa9d010e79fae54629f359b3b2270dca7856de0
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755054"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-powershell-preview"></a>Azure PowerShell(미리 보기)을 사용하여 Azure 역할 할당 조건 추가 또는 편집

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 역할 할당 조건](conditions-overview.md)은 더 세분화된 액세스 제어를 제공하기 위해 선택적으로 역할 할당에 추가할 수 있는 추가 검사입니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 이 문서에서는 Azure PowerShell을 사용하여 역할 할당에 대한 조건을 추가, 편집, 나열 또는 삭제하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](conditions-prerequisites.md)을 참조하세요.

## <a name="add-a-condition"></a>조건 추가

역할 할당 조건을 추가하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용합니다. [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 명령에는 조건과 관련된 다음 매개 변수가 포함됩니다.

| 매개 변수 | 형식 | Description |
| --- | --- | --- |
| `Condition` | String | 사용자에게 사용 권한을 부여할 수 있는 조건입니다. |
| `ConditionVersion` | String | 조건 구문의 버전입니다. 2\.0으로 설정해야 합니다. `Condition`이 지정된 경우 `ConditionVersion`도 지정해야 합니다. |

다음 예제에서는 조건으로 변수를 초기화하여 [스토리지 Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader) 역할을 할당하는 방법을 보여줍니다. 조건은 컨테이너 이름이 'blobs-example-container'와 같은지 여부를 확인합니다.

```azurepowershell
$subscriptionId = "<subscriptionId>"
$resourceGroup = "<resourceGroup>"
$roleDefinitionName = "Storage Blob Data Reader"
$roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
$userObjectId = "<userObjectId>"
$scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
$description = "Read access if container name equals blobs-example-container"
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$conditionVersion = "2.0"
```

조건과 함께 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 역할을 할당합니다.

```azurepowershell
New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
```

다음은 출력의 예입니다.

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))
```

PowerShell에서 조건에 달러 기호($)가 포함된 경우에는 앞에 백틱(\`)을 붙여야 합니다. 예를 들어 다음 조건은 달러 기호를 사용하여 태그 키 이름을 표시합니다. PowerShell의 따옴표 규칙에 대한 자세한 내용은 [따옴표 규칙 정보](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)를 참조하세요.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="edit-a-condition"></a>조건 편집

기존 역할 할당 조건을 편집하려면 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)를 사용합니다. `Condition`, `ConditionVersion` 및 `Description` 속성만 편집할 수 있습니다. `-PassThru` 매개 변수는 [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)가 업데이트된 역할 할당을 반환하도록 하여 추가 사용을 위해 변수에 시각화 또는 저장을 허용합니다.

두 가지 방법으로 조건을 편집할 수 있습니다. `PSRoleAssignment` 개체 또는 JSON 파일을 사용할 수 있습니다.

### <a name="edit-a-condition-using-the-psroleassignment-object"></a>PSRoleAssignment 개체를 사용하여 조건 편집

1. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용하여 조건을 `PSRoleAssignment` 개체로 포함하는 기존 역할 할당을 가져옵니다.

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. 조건을 편집합니다.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))"
    ```

1. 조건과 설명을 초기화합니다.

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access if container name equals blobs-example-container or blobs-example-container2"
    ```

1. [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)를 사용하여 역할 할당에 대한 조건을 업데이트합니다.

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    다음은 출력의 예입니다.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : User1
    SignInName         : user1@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access if container name equals blobs-example-container or blobs-example-container2
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
    ```

### <a name="edit-a-condition-using-a-json-file"></a>JSON 파일을 사용하여 조건 편집

조건을 편집하기 위해 JSON 파일을 입력으로 제공할 수도 있습니다. 다음은 `Condition` 및 `Description`이 업데이트되는 예제 JSON 파일을 보여줍니다. 조건을 업데이트하려면 JSON 파일의 모든 속성을 지정해야 합니다.

```json
{
    "RoleDefinitionId": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "ObjectId": "<userObjectId>",
    "ObjectType": "User",
    "Scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>",
    "Condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "ConditionVersion": "2.0",
    "CanDelegate": false,
    "Description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "RoleAssignmentId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>"
}
```

[Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment)를 사용하여 역할 할당에 대한 조건을 업데이트합니다.

```azurepowershell
Set-AzRoleAssignment -InputFile "C:\path\roleassignment.json" -PassThru
```

다음은 출력의 예입니다.

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container or blobs-example-container2
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
```

## <a name="list-a-condition"></a>조건 나열

역할 할당 조건을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다. 자세한 내용은 [Azure PowerShell을 사용하여 Azure 역할 할당 나열](role-assignments-list-powershell.md)을 참조하세요.

## <a name="delete-a-condition"></a>조건 삭제

역할 할당 조건을 삭제하려면 역할 할당 조건을 편집하고 `Condition` 및 `ConditionVersion` 속성을 빈 문자열(`""`) 또는 `$null`로 설정합니다.

또는 역할 할당과 조건을 모두 삭제하려는 경우 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 명령을 사용합니다. 자세한 내용은 [Azure 역할 할당 제거](role-assignments-remove.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [자습서: Azure PowerShell을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](../storage/common/storage-auth-abac-powershell.md)
- [Azure 역할 할당 조건 문제 해결(미리 보기)](conditions-troubleshoot.md)
