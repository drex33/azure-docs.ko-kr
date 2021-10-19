---
title: Bicep 함수 - 범위
description: Bicep 파일에서 배포 범위에 대한 값을 검색하는 데 사용할 함수를 설명합니다.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 809d3b2097e530f92370374be5f15c39bbd70734
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178344"
---
# <a name="scope-functions-for-bicep"></a>Bicep의 범위 함수

이 문서에서는 범위 값을 가져오는 Bicep 함수에 대해 설명 합니다.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(identifier)`

범위를 관리 그룹으로 설정하는 데 사용되는 개체를 반환합니다.

또는

현재 배포의 관리 그룹에 있는 속성을 사용 하 여 개체를 반환 합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

`managementGroup()`은 [관리 그룹 배포](deploy-to-management-group.md)에만 사용될 수 있습니다. 배포 작업에 대한 현재 관리 그룹을 반환합니다. 범위 개체를 가져오거나 현재 관리 그룹에 대 한 속성을 가져올 때 사용 합니다.

`managementGroup(identifier)` 는 범위 개체를 가져올 때만 모든 배포 범위에 사용할 수 있습니다. 관리 그룹에 대 한 속성을 검색 하려면 관리 그룹 식별자를 전달할 수 없습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| identifier |예 |문자열 |배포할 관리 그룹에 대한 고유 식별자입니다. 관리 그룹에 대한 표시 이름을 사용하지 마세요. 값을 입력하지 않으면 현재 관리 그룹이 반환됩니다. |

### <a name="return-value"></a>반환 값

[모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 `scope` 속성을 설정하는 데 사용되는 개체입니다. 또는 현재 관리 그룹에 대 한 속성을 포함 하는 개체입니다.

### <a name="management-group-example"></a>관리 그룹의 예

다음 예제에서는 모듈의 범위를 관리 그룹으로 설정합니다.

```bicep
param managementGroupIdentifier string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupIdentifier)
}
```

다음 예에서는 현재 관리 그룹에 대 한 속성을 반환 합니다.

```bicep
targetScope = 'managementGroup'

var mgInfo = managementGroup()

output mgResult object = mgInfo
```

그러면 다음을 반환합니다.

```json
"mgResult": {
  "type": "Object",
  "value": {
    "id": "/providers/Microsoft.Management/managementGroups/examplemg1",
    "name": "examplemg1",
    "properties": {
      "details": {
        "parent": {
          "displayName": "Tenant Root Group",
          "id": "/providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000",
          "name": "00000000-0000-0000-0000-000000000000"
        },
        "updatedBy": "00000000-0000-0000-0000-000000000000",
        "updatedTime": "2020-07-23T21:05:52.661306Z",
        "version": "1"
      },
      "displayName": "Example MG 1",
      "tenantId": "00000000-0000-0000-0000-000000000000"
    },
    "type": "/providers/Microsoft.Management/managementGroups"
  }
}
```

다음 예에서는 새 관리 그룹을 만들고이 함수를 사용 하 여 부모 관리 그룹을 설정 합니다.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

리소스 그룹에 대한 범위를 설정하는 데 사용되는 개체를 반환합니다.

또는

현재 리소스 그룹을 나타내는 개체를 반환합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

resourceGroup 함수에는 두 가지 고유한 용도가 있습니다. 한 가지 용도는 [모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 범위를 설정하는 것입니다. 다른 용도는 현재 리소스 그룹에 대한 세부 정보를 가져오는 것입니다. 함수의 배치가 용도를 결정합니다. `scope` 속성을 설정하는 데 사용된 경우 범위 개체를 반환합니다.

`resourceGroup()`은 범위를 설정하거나 리소스 그룹에 대한 세부 정보를 가져오는 데 사용될 수 있습니다.

`resourceGroup(resourceGroupName)`, `resourceGroup(subscriptionId, resourceGroupName)`는 범위를 설정하는 데에만 사용될 수 있습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceGroupName |예 |문자열 | 배포할 리소스 그룹의 이름입니다. 값을 입력하지 않으면 현재 리소스 그룹이 반환됩니다. |
| subscriptionId |예 |문자열 |배포할 구독의 고유 식별자입니다. 값을 입력하지 않으면 현재 구독이 반환됩니다. |

### <a name="return-value"></a>반환 값

범위를 설정하는 데 사용되는 경우 함수는 모듈 또는 확장 리소스 종류의 `scope` 속성에 유효한 개체를 반환합니다.

리소스 그룹에 대한 세부 정보를 가져오는 데 사용되는 경우 함수에서 다음 형식을 반환합니다.

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**managedBy** 속성은 다른 서비스에서 관리하는 리소스를 포함하는 리소스 그룹에 대해서만 반환됩니다. 관리형 애플리케이션, Databricks 및 AKS의 경우 속성 값은 관리하는 리소스의 리소스 ID입니다.

### <a name="resource-group-example"></a>리소스 그룹 예제

다음 예제에서는 리소스 그룹으로 모듈의 범위를 지정합니다.

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

그 다음 예제에서는 리소스 그룹의 속성을 반환합니다.

```bicep
output resourceGroupOutput object = resourceGroup()
```

아래와 같은 형식의 개체를 반환합니다.

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

resourceGroup 함수는 일반적으로 리소스 그룹과 동일한 위치에 리소스를 만드는 데 사용됩니다. 다음 예제에서는 기본 매개 변수 값에 대해 리소스 그룹 위치를 사용합니다.

```bicep
param location string = resourceGroup().location
```

또한 resourceGroup 함수를 사용하여 리소스 그룹의 태그를 리소스에 적용할 수 있습니다. 자세한 내용은 [리소스 그룹에서 태그 적용](../management/tag-resources.md#apply-tags-from-resource-group)을 참조하세요.

## <a name="subscription"></a>subscription

`subscription()`

`subscription(subscriptionId)`

범위를 구독으로 설정하는 데 사용되는 개체를 반환합니다.

또는

현재 배포에 대한 구독 관련 세부 정보를 반환합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

subscription 함수에는 두 가지 고유한 용도가 있습니다. 한 가지 용도는 [모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 범위를 설정하는 것입니다. 다른 용도는 현재 구독에 대한 세부 정보를 가져오는 것입니다. 함수의 배치가 용도를 결정합니다. `scope` 속성을 설정하는 데 사용된 경우 범위 개체를 반환합니다.

`subscription(subscriptionId)`은 범위를 설정하는 데에만 사용될 수 있습니다.

`subscription()`은 범위를 설정하거나 구독에 대한 세부 정보를 가져오는 데 사용될 수 있습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |예 |문자열 |배포할 구독의 고유 식별자입니다. 값을 입력하지 않으면 현재 구독이 반환됩니다. |

### <a name="return-value"></a>반환 값

범위를 설정하는 데 사용되는 경우 함수는 모듈 또는 확장 리소스 종류의 `scope` 속성에 유효한 개체를 반환합니다.

구독에 대한 세부 정보를 가져오는 데 사용되는 경우 함수에서 다음 형식을 반환합니다.

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>구독 예제

다음 예제에서는 구독으로 모듈의 범위를 지정합니다.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

그 다음 예제에서는 구독에 대한 세부 정보를 반환합니다.

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

범위를 테넌트로 설정하는 데 사용되는 개체를 반환합니다.

또는

현재 배포에 대 한 테 넌 트에 대 한 속성을 반환 합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

`tenant()`는 모든 배포 범위에서 사용될 수 있습니다. 항상 현재 테넌트를 반환합니다. 이 함수를 사용 하 여 리소스에 대 한 범위를 설정 하거나 현재 테 넌 트의 속성을 가져올 수 있습니다.

### <a name="return-value"></a>반환 값

[모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 `scope` 속성을 설정하는 데 사용되는 개체입니다. 또는 현재 테 넌 트에 대 한 속성을 포함 하는 개체입니다.

### <a name="tenant-example"></a>테넌트의 예

다음 예제에는 테넌트에 배포된 모듈이 나와 있습니다.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

다음 예에서는 테 넌 트의 속성을 반환 합니다.

```bicep
var tenantInfo = tenant()

output tenantResult object = tenantInfo
```

그러면 다음을 반환합니다.

```json
"tenantResult": {
  "type": "Object",
  "value": {
    "countryCode": "US",
    "displayName": "Contoso",
    "id": "/tenants/00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000"
  }
}
```

일부 리소스의 경우 속성에 대 한 테 넌 트 ID를 설정 해야 합니다. 테 넌 트 ID를 매개 변수로 전달 하는 대신 테 넌 트 함수를 사용 하 여 검색할 수 있습니다.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2021-06-01-preview' = {
  name: 'examplekeyvault'
  location: 'westus'
  properties: {
    tenantId: tenant().tenantId
    ...
  }
}
```

## <a name="next-steps"></a>다음 단계

배포 범위에 대해 자세히 알아보려면 다음을 참조하세요.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [구독 배포](deploy-to-subscription.md)
* [관리 그룹 배포](deploy-to-management-group.md)
* [테넌트 배포](deploy-to-tenant.md)
