---
title: Bicep 함수 - 범위
description: Bicep 파일에서 배포 범위에 대한 값을 검색하는 데 사용할 함수를 설명합니다.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 6771bf52ce5e5427343eb3210f665760a894627f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067139"
---
# <a name="scope-functions-for-bicep"></a>Bicep의 범위 함수

이 문서에서는 범위 값을 가져오는 Bicep 함수에 대해 설명 합니다.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

범위를 관리 그룹으로 설정하는 데 사용되는 개체를 반환합니다.

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

`managementGroup()`은 [관리 그룹 배포](deploy-to-management-group.md)에만 사용될 수 있습니다. 배포 작업에 대한 현재 관리 그룹을 반환합니다.

`managementGroup(name)`은 배포 범위에 사용될 수 있습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| name |예 |문자열 |배포할 관리 그룹에 대한 고유 식별자입니다. 관리 그룹에 대한 표시 이름을 사용하지 마세요. 값을 입력하지 않으면 현재 관리 그룹이 반환됩니다. |

### <a name="return-value"></a>반환 값

[모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 `scope` 속성을 설정하는 데 사용되는 개체입니다.

### <a name="management-group-example"></a>관리 그룹의 예

다음 예제에서는 모듈의 범위를 관리 그룹으로 설정합니다.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
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

네임 스페이스: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>설명

`tenant()`는 모든 배포 범위에서 사용될 수 있습니다. 항상 현재 테넌트를 반환합니다.

### <a name="return-value"></a>반환 값

[모듈](modules.md#set-module-scope) 또는 [확장 리소스 종류](scope-extension-resources.md)의 `scope` 속성을 설정하는 데 사용되는 개체입니다.

### <a name="tenant-example"></a>테넌트의 예

다음 예제에는 테넌트에 배포된 모듈이 나와 있습니다.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>다음 단계

배포 범위에 대해 자세히 알아보려면 다음을 참조하세요.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [구독 배포](deploy-to-subscription.md)
* [관리 그룹 배포](deploy-to-management-group.md)
* [테넌트 배포](deploy-to-tenant.md)
