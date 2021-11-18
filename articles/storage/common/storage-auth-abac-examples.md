---
title: Azure 역할 할당 조건 예(미리 보기) - Azure RBAC
titleSuffix: Azure Storage
description: Azure ABAC(Azure 특성 기반 액세스 제어)에 대한 Azure 역할 할당 조건의 예입니다.
services: storage
author: rolyon
ms.service: storage
ms.topic: conceptual
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 11/16/2021
ms.openlocfilehash: fa290d972877c213f3d24e57b497e05d931d7b3d
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751355"
---
# <a name="example-azure-role-assignment-conditions-preview"></a>Azure 역할 할당 조건 예(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에는 역할 할당 조건의 몇 가지 예가 나와 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](../../role-based-access-control/conditions-prerequisites.md)을 참조하세요.

## <a name="example-1-read-access-to-blobs-with-a-tag"></a>예제 1: 태그가 있는 Blob에 대한 읽기 권한

이 조건을 통해 사용자는 Blob 인덱스 태그 키가 Project이고 태그 값이 Cascade인 Blob을 읽을 수 있습니다. 이 키-값 태그가 없는 Blob에 액세스하려는 시도는 허용되지 않습니다.

> [!TIP]
> Blob은 임의의 사용자 정의 키- 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

![태그가 있는 일부 Blob에 대한 읽기 권한을 보여주는 예제 1 조건의 다이어그램](./media/storage-auth-abac-examples/example-1.png)

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

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | 태그 조건이 있는 Blob에서 콘텐츠 읽기 |
| 특성 원본 | 리소스 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | {keyName} |
| 연산자 | StringEquals |
| 값 | {keyValue} |

![Azure Portal의 예제 1 조건 편집기 스크린샷](./media/storage-auth-abac-examples/example-1-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
Get-AzStorageBlob -Container <containerName> -Blob <blobName> -Context $bearerCtx 
```

## <a name="example-2-new-blobs-must-include-a-tag"></a>예제 2: 새 Blob에는 태그가 포함되어야 함

이 조건에서는 새 Blob이 Blob 인덱스 태그 키 Project와 태그 값 Cascade를 포함해야 합니다.

> [!TIP]
> Blob은 임의의 사용자 정의 키- 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

새 Blob을 만들 수 있는 두 가지 권한이 있으므로 둘 다 대상으로 지정해야 합니다. 다음 권한 중 하나를 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/write(만들기 또는 업데이트)
- /blobs/add/action(만들기)

![새 Blob에는 태그가 포함되어야 한다는 것을 보여주는 예제 2 조건의 다이어그램](./media/storage-auth-abac-examples/example-2.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob 인덱스 태그를 사용하여 Blob에 쓰기<br/>Blob 인덱스 태그를 사용하여 Blob에 콘텐츠 쓰기 |
| 특성 원본 | 요청 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | {keyName} |
| 연산자 | StringEquals |
| 값 | {keyValue} |

![Azure Portal의 예제 2 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-2-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Project'='Baker'}
$grantedTag = @{'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-3-existing-blobs-must-have-tag-keys"></a>예제 3: 기존 Blob에는 태그 키가 있어야 함

이 조건을 사용하려면 기존 Blob에 허용되는 Blob 인덱스 태그 키(Project 또는 Program) 중 하나 이상으로 태그를 지정해야 합니다. 이 조건은 기존 Blob에 거버넌스를 추가하는 데 유용합니다.

> [!TIP]
> Blob은 임의의 사용자 정의 키- 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

기존 Blob에 대한 태그를 업데이트할 수 있는 두 가지 권한이 있으므로 둘 다 대상으로 지정해야 합니다. 다음 권한 중 하나를 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/write(업데이트 또는 만들기, 만들기는 제외할 수 없음)
- /blobs/tags/write

![기존 Blob에는 태그 키가 있어야 한다는 것을 보여주는 예제 3 조건의 다이어그램](./media/storage-auth-abac-examples/example-3.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob 인덱스 태그를 사용하여 Blob에 쓰기<br/>Blob 인덱스 태그 쓰기 |
| 특성 원본 | 요청 |
| attribute | Blob 인덱스 태그 [키] |
| 연산자 | ForAllOfAnyValues:StringEquals |
| 값 | {keyName1}<br/>{keyName2} |

![Azure Portal의 예제 3 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-3-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Mode'='Baker'}
$grantedTag = @{'Program'='Alpine';'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-4-existing-blobs-must-have-a-tag-key-and-values"></a>예제 4: 기존 Blob에는 태그 키와 값이 있어야 함

이 조건에서는 기존 Blob에 Blob인덱스 태그 키 Project와 태그 값 Cascade, Baker 또는 Skagit이 있어야 합니다. 이 조건은 기존 Blob에 거버넌스를 추가하는 데 유용합니다.

> [!TIP]
> Blob은 임의의 사용자 정의 키- 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

기존 Blob에 대한 태그를 업데이트할 수 있는 두 가지 권한이 있으므로 둘 다 대상으로 지정해야 합니다. 다음 권한 중 하나를 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/write(업데이트 또는 만들기, 만들기는 제외할 수 없음)
- /blobs/tags/write

![기존 Blob에는 태그 키와 값이 있어야 한다는 것을 보여주는 예제 4 조건의 다이어그램](./media/storage-auth-abac-examples/example-4.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAnyOfAnyValues:StringEquals {'Project'}
  AND
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob 인덱스 태그를 사용하여 Blob에 쓰기<br/>Blob 인덱스 태그 쓰기 |
| 특성 원본 | 요청 |
| attribute | Blob 인덱스 태그 [키] |
| 연산자 | ForAnyOfAnyValues:StringEquals |
| 값 | {keyName} |
| 연산자 | And |
| **식 2** |  |
| 특성 원본 | 요청 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | {keyName} |
| 연산자 | ForAllOfAnyValues:StringEquals |
| 값 | {keyValue1}<br/>{keyValue2}<br/>{keyValue3} |

![Azure Portal의 예제 4 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-4-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAnyOfAnyValues:StringEquals {'Project'} AND @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$localSrcFile = <pathToLocalFile>
$ungrantedTag = @{'Project'='Alpine'}
$grantedTag1 = @{'Project'='Cascade'}
$grantedTag2 = @{'Project'='Baker'}
$grantedTag3 = @{'Project'='Skagit'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag1 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag2 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag3 -Context $bearerCtx
```

## <a name="example-5-read-write-or-delete-blobs-in-named-containers"></a>예제 5: 명명된 컨테이너에서 Blob 읽기, 쓰기 또는 삭제

이 조건을 통해 사용자는 blob-example-container라는 스토리지 컨테이너에서 Blob을 읽거나 쓰거나 삭제할 수 있습니다. 이 조건은 특정 스토리지 컨테이너를 구독의 다른 사용자와 공유하는 데 유용합니다.

기존 Blob에 대한 읽기, 쓰기 및 삭제에 대한 네 가지 권한이 있으므로 모든 권한을 대상으로 지정해야 합니다. 다음 권한 중 하나를 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/delete
- /blobs/read
- /blobs/write (업데이트 또는 만들기)
- /blobs/add/action(만들기)

이 조건에서는 하위 작업이 사용되지 않습니다. 조건이 태그를 기반으로 작성되는 경우에만 subOperation이 필요하기 때문입니다.

![명명된 컨테이너에서 Blob 읽기, 쓰기 또는 삭제를 보여주는 예제 5 조건의 다이어그램](./media/storage-auth-abac-examples/example-5.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob 삭제<br/>Blob 읽기<br/>Blob에 쓰기<br/>Blob 또는 스냅샷을 만들거나 데이터를 추가합니다. |
| 특성 원본 | 리소스 |
| attribute | 컨테이너 이름 |
| 연산자 | StringEquals |
| 값 | {containerName} |

![Azure Portal의 예제 5 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-5-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$localSrcFile = <pathToLocalFile>
$grantedContainer = "blobs-example-container"
$ungrantedContainer = "ungranted"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Ungranted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
# Granted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
```

## <a name="example-6-read-access-to-blobs-in-named-containers-with-a-path"></a>예제 6: 경로가 있는 명명된 컨테이너의 Blob에 대한 읽기 권한

이 조건은 Blob 경로가 readonly/*인 blob-example-container라는 스토리지 컨테이너에 대한 읽기 권한을 허용합니다. 이 조건은 구독의 다른 사용자와 읽기 권한을 위해 스토리지 컨테이너의 특정 부분을 공유하는 데 유용합니다.

다음 권한을 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/read

![경로가 있는 명명된 컨테이너의 Blob에 대한 읽기 권한을 보여주는 예제 6 조건의 다이어그램](./media/storage-auth-abac-examples/example-6.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
        AND
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'
    )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob 읽기 |
| 특성 원본 | 리소스 |
| attribute | 컨테이너 이름 |
| 연산자 | StringEquals |
| 값 | {containerName} |
| **식 2** |  |
| 연산자 | And |
| 특성 원본 | 리소스 |
| attribute | Blob 경로 |
| 연산자 | StringLike |
| 값 | {pathString} |

![Azure Portal의 예제 6 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-6-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$grantedContainer = "blobs-example-container"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Ungranted.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "readonly/Example6.txt" -Context $bearerCtx
```

## <a name="example-7-write-access-to-blobs-in-named-containers-with-a-path"></a>예제 7: 경로가 있는 명명된 컨테이너의 Blob에 대한 쓰기 권한

이 조건을 사용하면 파트너(Azure AD 게스트 사용자)가 경로가 uploads/contoso/*인 Contosocorp라는 스토리지 컨테이너에 파일을 넣을 수 있습니다. 이 조건은 다른 사용자가 스토리지 컨테이너에 데이터를 넣을 수 있도록 하는 데 유용합니다.

다음 권한을 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/write(만들기 또는 업데이트)
- /blobs/add/action(만들기)

![경로가 있는 명명된 컨테이너의 Blob에 대한 쓰기 권한을 보여주는 예제 7 조건의 다이어그램](./media/storage-auth-abac-examples/example-7.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp'
  AND
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | Blob에 쓰기<br/>Blob 또는 스냅샷을 만들거나 데이터를 추가합니다. |
| 특성 원본 | 리소스 |
| attribute | 컨테이너 이름 |
| 연산자 | StringEquals |
| 값 | {containerName} |
| **식 2** |  |
| 연산자 | And |
| 특성 원본 | 리소스 |
| attribute | Blob 경로 |
| 연산자 | StringLike |
| 값 | {pathString} |

![Azure Portal의 예제 7 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-7-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$grantedContainer = "contosocorp"
$localSrcFile = <pathToLocalFile>
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to set ungranted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "Example7.txt" -Context $bearerCtx -File $localSrcFile
# Try to set granted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "uploads/contoso/Example7.txt" -Context $bearerCtx -File $localSrcFile
```

## <a name="example-8-read-access-to-blobs-with-a-tag-and-a-path"></a>예제 8: 태그 및 경로가 있는 Blob에 대한 읽기 권한

이 조건에서는 Blob 인덱스 태그 키가 Program이고 태그 값이 Alpine이고 Blob 경로가 logs *인 Blob을 사용자가 읽을 수 있습니다. Blob 경로 logs* 에는 Blob 이름도 포함됩니다.

> [!TIP]
> Blob은 임의의 사용자 정의 키- 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

다음 권한을 포함하는 모든 역할 할당에 이 조건을 추가해야 합니다.

- /blobs/read

![태그 및 경로가 있는 Blob에 대한 읽기 권한을 보여주는 예제 8 조건의 다이어그램](./media/storage-auth-abac-examples/example-8.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        SubOperationMatches{'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<$key_case_sensitive$>] StringEquals 'Alpine'
    )
)
AND
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'
    )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | 태그 조건이 있는 Blob에서 콘텐츠 읽기 |
| 특성 원본 | 리소스 |
| attribute | Blob 인덱스 탭 [키의 값] |
| 키 | {keyName} |
| 연산자 | StringEquals |
| 값 | {keyValue} |

![Azure Portal의 예제 8 조건 1 편집기 스크린샷](./media/storage-auth-abac-examples/example-8-condition-1-portal.png)

| 조건 #2 | 설정 |
| --- | --- |
| 동작 | Blob 읽기 |
| 특성 원본 | 리소스 |
| attribute | Blob 경로 |
| 연산자 | StringLike |
| 값 | {pathString} |

![Azure Portal의 예제 8 조건 2 편집기 스크린샷](./media/storage-auth-abac-examples/example-8-condition-2-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 이 조건을 추가하는 방법은 다음과 같습니다.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<`$key_case_sensitive`$>] StringEquals 'Alpine')) AND ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

이 조건을 테스트하는 방법은 다음과 같습니다.

```azurepowershell
$grantedContainer = "contosocorp"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blobs
# Wrong name but right tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "AlpineFile.txt" -Context $bearerCtx
# Right name but wrong tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logsAlpine.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logs/AlpineFile.txt" -Context $bearerCtx
```

## <a name="example-9-allow-read-and-write-access-to-blobs-based-on-tags-and-custom-security-attributes"></a>예제 9: 태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 및 쓰기 액세스 허용

이 조건은 사용자에게 Blob 인덱스 태그와 일치하는 [사용자 지정 보안 특성이](../../active-directory/fundamentals/custom-security-attributes-overview.md) 있는 경우 Blob에 대한 읽기 및 쓰기 액세스를 허용합니다.
 
예를 들어, 이 특성이 있는 경우 `Project=Baker` `Project=Baker` Blob 인덱스 태그를 가진 Blob만 읽고 쓸 수 있습니다. 마찬가지로 Chandra는 를 통해서만 Blob을 읽고 쓸 수 `Project=Cascade` 있습니다.

자세한 내용은 [태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용을 참조하세요.](../../role-based-access-control/conditions-custom-security-attributes.md)

![태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 및 쓰기 액세스를 보여 주는 예제 9 조건 다이어그램](./media/storage-auth-abac-examples/condition-principal-attribute-example.png)

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
AND
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project] StringEquals @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | 태그 조건이 있는 Blob에서 콘텐츠 읽기 |
| 특성 원본 | 주 서버 |
| attribute | &lt;attributeset &gt; _ &lt; key&gt; |
| 연산자 | StringEquals |
| 옵션 | attribute |
| 특성 원본 | 리소스 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | &lt;key&gt; |

| 조건 #2 | 설정 |
| --- | --- |
| 동작 | Blob 인덱스 태그를 사용하여 Blob에 쓰기<br/>Blob 인덱스 태그를 사용하여 Blob에 쓰기 |
| 특성 원본 | 주 서버 |
| attribute | &lt;attributeset &gt; _ &lt; key&gt; |
| 연산자 | StringEquals |
| 옵션 | attribute |
| 특성 원본 | 요청 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | &lt;key&gt; |

## <a name="example-10-allow-read-access-to-blobs-based-on-tags-and-multi-value-custom-security-attributes"></a>예제 10: 태그 및 다중 값 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용

이 조건에서는 사용자에게 Blob 인덱스 태그와 일치하는 값이 있는 [사용자 지정 보안 특성이](../../active-directory/fundamentals/custom-security-attributes-overview.md) 있는 경우 Blob에 대한 읽기 액세스를 허용합니다.
 
예를 들어 Chandra에 값이인 Project 특성이 있는 경우 또는 `Project=Baker` `Project=Cascade` Blob 인덱스 태그가 있는 Blob만 읽을 수 있습니다.

자세한 내용은 [태그 및 사용자 지정 보안 특성에 따라 Blob에 대한 읽기 액세스 허용을 참조하세요.](../../role-based-access-control/conditions-custom-security-attributes.md)

![태그 및 다중 값 사용자 지정 보안 특성을 기반으로 Blob에 대한 읽기 액세스를 보여 주는 예제 10 조건 다이어그램](./media/storage-auth-abac-examples/condition-principal-attribute-multi-value-example.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAnyOfAnyValues:StringEquals @Principal[Microsoft.Directory/CustomSecurityAttributes/Id:Engineering_Project]
 )
)
```

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 이 조건을 추가하는 설정은 다음과 같습니다.

| 조건 #1 | 설정 |
| --- | --- |
| 동작 | 태그 조건이 있는 Blob에서 콘텐츠 읽기 |
| 특성 원본 | 리소스 |
| attribute | Blob 인덱스 태그 [키의 값] |
| 키 | &lt;key&gt; |
| 연산자 | ForAnyOfAnyValues:StringEquals |
| 옵션 | attribute |
| 특성 원본 | 주 서버 |
| attribute | &lt;attributeset &gt; _ &lt; key&gt; |

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](storage-auth-abac-portal.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure 역할 할당 조건 형식 및 구문(미리 보기)](../../role-based-access-control/conditions-format.md)
