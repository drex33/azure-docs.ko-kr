---
title: Azure 역할 정의 이해 - Azure RBAC
description: Azure 리소스의 세분화된 액세스 관리에 사용되는 Azure RBAC(Azure 역할 기반 액세스 제어)의 Azure 역할 정의에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/28/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: c68849dcb3c0c5683bfc160a0b4a1cb03ae0e13a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356050"
---
# <a name="understand-azure-role-definitions"></a>Azure 역할 정의 이해

Azure 역할이 작동하는 방식을 이해하려거나 사용자 고유의 [Azure 사용자 지정 역할](custom-roles.md)을 만드는 경우 역할을 정의하는 방법을 이해하는 데 도움이 됩니다. 이 문서에서는 역할 정의에 대한 자세한 내용을 설명하고 몇 가지 예제를 제공합니다.

## <a name="role-definition"></a>역할 정의

*역할 정의* 는 권한 컬렉션입니다. 때로는 *역할* 이라고 합니다. 역할 정의에는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업이 나열 됩니다. 또한 허용 되는 작업 또는 기본 데이터와 관련 된 작업에서 제외 된 작업을 나열할 수 있습니다.

다음은 Azure PowerShell를 사용하여 표시할 경우 역할 정의의 속성 예를 보여 줍니다.

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

다음은 Azure Portal, Azure CLI 또는 REST API를 사용하여 표시할 경우 역할 정의의 속성 예를 보여 줍니다.

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

다음 표에서는 역할 속성의 의미에 대해 설명합니다.

| 속성 | Description |
| --- | --- |
| `Name`</br>`roleName` | 역할의 표시 이름입니다. |
| `Id`</br>`name` | 역할의 고유 ID입니다. 기본 제공 역할은 여러 클라우드에서 동일한 역할 ID를 가집니다. |
| `IsCustom`</br>`roleType` | 사용자 지정 역할인지 여부를 나타냅니다. 사용자 지정 역할인 경우 `true` 또는 `CustomRole`로 설정합니다. 기본 제공 역할인 경우 `false` 또는 `BuiltInRole`로 설정합니다. |
| `Description`</br>`description` | 역할에 대한 설명입니다. |
| `Actions`</br>`actions` | 역할에서 수행할 수 있는 제어 평면 작업을 지정 하는 문자열의 배열입니다. |
| `NotActions`</br>`notActions` | 허용 된에서 제외 되는 제어 평면 작업을 지정 하는 문자열 배열입니다 `Actions` . |
| `DataActions`</br>`dataActions` | 해당 개체 내의 데이터에 대해 역할이 수행할 수 있도록 허용 하는 데이터 평면 작업을 지정 하는 문자열의 배열입니다. |
| `NotDataActions`</br>`notDataActions` | 허용 된에서 제외 되는 데이터 평면 작업을 지정 하는 문자열 배열입니다 `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | 할당에 사용할 수 있는 역할에 대한 범위를 지정하는 문자열 배열입니다. |

### <a name="actions-format"></a>작업 형식

작업은 다음과 같은 형식의 문자열로 지정 됩니다.

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}`작업 문자열의 부분은 리소스 유형에 대해 수행할 수 있는 동작의 유형을 지정 합니다. 예를 들어 `{action}`에 표시되는 부분 문자열은 다음과 같습니다.

| 작업 부분 문자열    | Description         |
| ------------------- | ------------------- |
| `*` | 와일드 카드 문자는 문자열과 일치 하는 모든 작업에 대 한 액세스 권한을 부여 합니다. |
| `read` | 읽기 작업 (GET)을 사용 하도록 설정 합니다. |
| `write` | 쓰기 작업 (PUT 또는 PATCH)을 사용 하도록 설정 합니다. |
| `action` | 가상 컴퓨터 다시 시작 (POST)과 같은 사용자 지정 작업을 사용 하도록 설정 합니다. |
| `delete` | 삭제 작업을 사용 합니다 (DELETE). |

### <a name="role-definition-example"></a>역할 정의 예제

Azure PowerShell 및 Azure CLI에 표시되는 [기여자](built-in-roles.md#contributor) 역할 정의는 다음과 같습니다. 아래의 와일드 카드 ( `*` ) 작업은 `Actions` 이 역할에 할당 된 보안 주체가 모든 작업을 수행할 수 있음을 나타냅니다. 즉, 모든 항목을 관리할 수 있습니다. 여기에는 나중에 Azure에서 새 리소스 종류를 추가할 때 정의되는 작업도 포함됩니다. 아래의 작업을 `NotActions` 에서 뺍니다 `Actions` . [기여자](built-in-roles.md#contributor) 역할의 경우 `NotActions`는 리소스에 대한 액세스를 관리하고 Azure Blueprint 할당을 관리하는 이 역할의 기능을 제거합니다.

Azure PowerShell에 표시되는 기여자 역할:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI에 표시되는 기여자 역할:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="control-and-data-actions"></a>컨트롤 및 데이터 작업

제어 평면 작업에 대 한 역할 기반 액세스 제어는 `Actions` `NotActions` 역할 정의의 및 속성에서 지정 됩니다. Azure의 제어 평면 작업에 대 한 몇 가지 예는 다음과 같습니다.

- 스토리지 계정에 대한 액세스 관리
- Blob 컨테이너 만들기, 업데이트 또는 삭제
- 리소스 그룹 및 해당하는 모든 리소스 삭제

컨테이너 인증 방법이 "액세스 키"가 아닌 "Azure AD 사용자 계정"으로 설정 된 경우 제어 평면 액세스는 데이터 평면에 상속 되지 않습니다. 이렇게 분리하면 와일드카드(`*`)가 있는 역할에서 데이터에 무제한으로 액세스할 수 없게 됩니다. 예를 들어 구독에 [읽기 권한자](built-in-roles.md#reader) 역할이 있는 사용자는 스토리지 계정을 볼 수 있지만 기본적으로 기본 데이터는 볼 수 없습니다.

이전에는 역할 기반 액세스 제어가 데이터 작업에 사용 되지 않았습니다. 데이터 작업에 대 한 권한 부여는 리소스 공급자 간에 다양 합니다. 제어 평면 작업에 사용 되는 것과 동일한 역할 기반 액세스 제어 권한 부여 모델이 데이터 평면 작업으로 확장 되었습니다.

데이터 평면 작업을 지원 하기 위해 새 데이터 속성이 역할 정의에 추가 되었습니다. 데이터 평면 동작은 및 속성에서 지정 `DataActions` `NotDataActions` 합니다. 이러한 데이터 속성을 추가 하 여 제어 평면과 데이터 평면 간의 분리를 유지 합니다. 이렇게 하면 와일드카드(`*`)를 사용한 현재 역할 할당이 갑자기 데이터에 액세스할 수 있게 되는 경우를 방지할 수 있습니다. 다음은 및에서 지정할 수 있는 몇 가지 데이터 평면 동작입니다 `DataActions` `NotDataActions` .

- 컨테이너의 Blob 목록 읽기
- 컨테이너에서 Storage Blob 작성
- 큐의 메시지 삭제

및 속성의 작업을 포함 하는 [Storage Blob 데이터 판독기](built-in-roles.md#storage-blob-data-reader) 역할 정의는 다음과 `Actions` 같습니다 `DataActions` . 이 역할을 사용하면 Blob 컨테이너 및 기본 Blob 데이터를 읽을 수 있습니다.

Azure PowerShell에 표시되는 Storage Blob 데이터 읽기 권한자 역할:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI에 표시되는 Storage Blob 데이터 읽기 권한자 역할:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

데이터 평면 작업만 및 속성에 추가할 수 있습니다 `DataActions` `NotDataActions` . 리소스 공급자는 속성을로 설정 하 여 데이터 작업을 수행 하는 작업을 식별 `isDataAction` `true` 합니다. 가 인 작업 목록을 보려면 `isDataAction` `true` [리소스 공급자 작업](resource-provider-operations.md)을 참조 하세요. 데이터 동작이 없는 역할은 `DataActions` 역할 정의 내에 및 속성을 포함할 필요가 없습니다 `NotDataActions` .

모든 제어 평면 API 호출에 대 한 권한 부여는 Azure Resource Manager에 의해 처리 됩니다. 데이터 평면 API 호출에 대 한 권한 부여는 리소스 공급자나 Azure Resource Manager에 의해 처리 됩니다.

### <a name="data-actions-example"></a>데이터 작업 예

제어 평면과 데이터 평면 동작의 작동 방식을 더 잘 이해 하기 위해 특정 예제를 살펴보겠습니다. Alice는 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당 받았습니다. Bob에게는 스토리지 계정 범위에서 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) 역할이 할당되었습니다. 다음 다이어그램은 이 예제를 보여 줍니다.

![역할 기반 액세스 제어가 제어 평면과 데이터 평면 작업을 모두 지원 하도록 확장 되었습니다.](./media/role-definitions/rbac-data-plane.png)

Alice의 [소유자](built-in-roles.md#owner) 역할과 Bob의 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) 역할은 다음 작업을 수행합니다.

소유자

&nbsp;&nbsp;&nbsp;&nbsp;작업<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob 데이터 기여자

&nbsp;&nbsp;&nbsp;&nbsp;작업<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Alice는 구독 범위에서 와일드 카드 () 작업을 포함 하므로 `*` 해당 권한은 모든 제어 평면 작업을 수행할 수 있도록 상속 됩니다. Alice는 컨테이너를 읽고 쓰며 삭제할 수 있습니다. 그러나 Alice는 추가 단계를 수행 하지 않고 데이터 평면 작업을 수행할 수 없습니다. 예를 들어 Alice는 기본적으로 컨테이너 내부의 Blob을 읽을 수 없습니다. Blob을 읽으려면 스토리지 액세스 키를 검색하고 사용하여 Blob에 액세스해야 합니다.

Bob의 권한은 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) 역할에 지정된 `Actions` 및 `DataActions`로만 제한됩니다. Bob은 역할에 따라 제어 평면과 데이터 평면 작업을 모두 수행할 수 있습니다. 예를 들어 Bob은 지정된 스토리지 계정에서 컨테이너를 읽고, 쓰고, 삭제할 수 있으며 Blob을 읽고, 쓰고, 삭제할 수도 있습니다.

저장소에 대 한 제어 및 데이터 평면 보안에 대 한 자세한 내용은 [Azure Storage 보안 가이드](../storage/blobs/security-recommendations.md)를 참조 하세요.

### <a name="what-tools-support-using-azure-roles-for-data-actions"></a>Azure 역할을 사용 하 여 데이터 작업을 지 원하는 도구

데이터 작업을 보고 작업 하려면 올바른 버전의 도구 또는 Sdk가 있어야 합니다.

| 도구  | 버전  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 이상 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 이상 |
| [.NET용 Azure](/dotnet/azure/) | 2.8.0-미리 보기 이상 버전 |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 이상 |
| [Java용 Azure](/java/azure/) | 1.9.0 이상 |
| [Python용 Azure](/azure/python/) | 0.40.0 이상 |
| [Ruby용 Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 이상 |

REST API 데이터 작업을 보고 사용하려면 **api-version** 매개 변수를 다음 버전 이상으로 설정해야 합니다.

- 2018-07-01

## <a name="actions"></a>동작

`Actions`사용 권한은 역할이 수행할 수 있는 컨트롤 플레인 동작을 지정합니다. Azure 리소스 공급자의 보안 가능한 작업을 식별하는 문자열 컬렉션입니다. 다음은 에서 사용할 수 있는 컨트롤 플레인 작업의 몇 가지 `Actions` 예입니다.

> [!div class="mx-tableFixed"]
> | 작업 문자열    | Description         |
> | ------------------- | ------------------- |
> | `*/read` | 모든 Azure 리소스 공급자의 모든 리소스 종류에 대한 읽기 작업에 대한 액세스 권한을 부여합니다.|
> | `Microsoft.Compute/*` | Microsoft.Compute 리소스 공급자의 모든 리소스 종류에 대한 모든 작업에 대한 액세스 권한을 부여합니다.|
> | `Microsoft.Network/*/read` | Microsoft.Network 리소스 공급자의 모든 리소스 종류에 대한 읽기 작업에 대한 액세스 권한을 부여합니다.|
> | `Microsoft.Compute/virtualMachines/*` | 가상 머신 및 해당 자식 리소스 유형의 모든 작업에 대한 액세스 권한을 부여합니다.|
> | `microsoft.web/sites/restart/Action` | 웹앱을 다시 시작하기 위한 액세스 권한을 부여합니다.|

## <a name="notactions"></a>NotActions

`NotActions`사용 권한은 와일드카드( )가 있는 허용된 에서 빼거나 제외하는 컨트롤 `Actions` 플레인 동작을 지정합니다. `*` `NotActions`허용하려는 작업 집합이 와일드카드( )가 있는 에서 를 빼서 보다 쉽게 정의할 수 있는 경우 사용 권한을 `Actions` `*` 사용합니다. 역할(유효 권한)에서 부여된 액세스 권한은 작업에서 작업을 빼서 `NotActions` `Actions` 계산됩니다.

`Actions - NotActions = Effective control plane permissions`

다음 표에서는 [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement) 와일드카드 작업에 대한 유효 컨트롤 플레인 권한의 두 가지 예를 보여줍니다.

> [!div class="mx-tableFixed"]
> | Actions | NotActions | 효과적인 컨트롤 플레인 권한 |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *없음* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> 사용자에게 에서 작업을 제외하는 역할이 `NotActions` 할당되고 동일한 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 사용자는 해당 작업을 수행할 수 있습니다. `NotActions` 는 거부 규칙이 아닙니다. 특정 작업을 제외해야 하는 경우 허용된 작업 집합을 만드는 편리한 방법입니다.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>NotActions와 deny 할당 간의 차이점

`NotActions` 및 deny 할당은 같지 않으며 서로 다른 용도를 제공합니다. `NotActions` 는 와일드카드( ) 작업에서 특정 작업을 빼는 편리한 `*` 방법입니다.

거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 작업을 사용자가 수행할 수 없도록 차단합니다. 자세한 내용은 [Azure 거부 할당 이해](deny-assignments.md)를 참조하세요.

## <a name="dataactions"></a>DataActions

`DataActions`사용 권한은 역할이 해당 개체 내의 데이터에 대해 수행할 수 있는 데이터 평면 동작을 지정합니다. 예를 들어 사용자가 스토리지 계정에 대한 Blob 데이터 읽기 액세스 권한이 있는 경우 해당 스토리지 계정 내의 Blob을 읽을 수 있습니다. 다음은 에서 사용할 수 있는 데이터 작업의 몇 가지 `DataActions` 예입니다.

> [!div class="mx-tableFixed"]
> | 데이터 작업 문자열    | Description         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Blob 또는 Blob 목록 반환 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Blob 쓰기 결과 반환 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | 메시지 반환 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | 메시지 또는 메시지 작성/삭제 결과를 반환합니다. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`사용 권한은 와일드카드( )가 있는 허용된 에서 빼거나 제외하는 데이터 평면 `DataActions` 동작을 지정합니다. `*` `NotDataActions`허용하려는 작업 집합이 와일드카드( )가 있는 에서 를 빼서 보다 쉽게 정의할 수 있는 경우 사용 권한을 `DataActions` `*` 사용합니다. 역할(유효 권한)에서 부여된 액세스 권한은 작업에서 작업을 빼서 `NotDataActions` `DataActions` 계산됩니다. 각 리소스 공급자는 데이터 작업을 수행하기 위해 각각의 API 집합을 제공합니다.

`DataActions - NotDataActions = Effective data plane permissions`

다음 표에서는 [Microsoft.Storage](resource-provider-operations.md#microsoftstorage) 와일드카드 작업에 대한 유효 날짜 평면 사용 권한의 두 가지 예를 보여줍니다.

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | 유효 데이터 평면 권한 |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *없음* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> 사용자에게 에서 데이터 작업을 제외하는 역할이 할당되고 동일한 데이터 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 `NotDataActions` 사용자는 해당 데이터 작업을 수행할 수 있습니다. `NotDataActions` 는 거부 규칙이 아닙니다. 특정 데이터 작업을 제외해야 할 때 허용되는 데이터 작업 집합을 만드는 편리한 방법입니다.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`속성은 이 역할 정의를 할당할 수 있는 범위(관리 그룹, 구독 또는 리소스 그룹)를 지정합니다. 역할이 필요한 관리 그룹, 구독 또는 리소스 그룹에만 역할을 할당할 수 있습니다. 하나 이상의 관리 그룹, 구독 또는 리소스 그룹을 사용해야 합니다.

기본 제공 역할에는 루트 범위(`"/"`)로 설정된 `AssignableScopes`가 있습니다. 루트 범위는 모든 범위에서 역할을 할당에 사용할 수 있음을 나타냅니다. 유효한 할당 가능한 범위의 예는 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 역할을 할당할 수 있습니다. | 예제 |
> |----------|---------|
> | 구독 하나 사용 | `"/subscriptions/{subscriptionId1}"` |
> | 두 구독 | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | 네트워크 리소스 그룹 | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | 하나의 관리 그룹 | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | 관리 그룹 및 구독 | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | 모든 범위(기본 제공 역할에만 적용됨) | `"/"` |

사용자 지정 역할에 대한 내용은 `AssignableScopes`[Azure 사용자 지정 역할](custom-roles.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 기본 제공 역할](built-in-roles.md)
* [Azure 사용자 지정 역할](custom-roles.md)
* [Azure 리소스 공급자 작업](resource-provider-operations.md)
