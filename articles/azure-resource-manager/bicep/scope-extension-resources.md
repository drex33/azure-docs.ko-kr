---
title: 확장 리소스 종류에 대한 범위(Bicep)
description: Bicep으로 확장 리소스 종류를 배포할 때 범위 속성을 사용하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 2b1fb9960caf81a51c80dae312979d90f6eb5f4d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733420"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>Bicep에서 확장 리소스의 범위 설정

확장 리소스는 다른 리소스를 수정하는 리소스입니다. 예를 들어 리소스에 역할을 할당할 수 있습니다. 역할 할당은 확장 리소스 종류입니다.

확장 리소스 종류의 전체 목록은 [다른 리소스의 기능을 확장하는 리소스 종류](../management/extension-resource-types.md)를 참조하세요.

이 문서에서는 Bicep 파일을 사용하여 배포할 때 확장 리소스 종류에 범위를 설정하는 방법을 보여 줍니다. 확장 리소스를 리소스에 적용할 때 사용할 수 있는 scope 속성을 설명합니다.

> [!NOTE]
> scope 속성은 확장 리소스 종류에만 사용할 수 있습니다. 확장 형식이 아닌 리소스 종류에 대해 다른 범위를 지정하려면 [모듈](modules.md)을 사용합니다.

### <a name="microsoft-learn"></a>Microsoft Learn

확장 리소스에 대한 자세한 내용과 실습 지침은 **Microsoft Learn** [Bicep을 사용하여 자식 및 확장 리소스 배포를](/learn/modules/child-extension-bicep-templates) 참조하세요.

## <a name="apply-at-deployment-scope"></a>배포 범위에서 적용

대상 배포 범위에서 확장 리소스 종류를 적용하려면 다른 리소스 종류에서와 마찬가지로 템플릿에 리소스를 추가합니다. 사용 가능한 범위는 [리소스 그룹](deploy-to-resource-group.md), [구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md) 및 [테넌트](deploy-to-tenant.md)입니다. 배포 범위가 리소스 종류를 지원해야 합니다.

리소스 그룹에 배포된 경우 다음 템플릿은 해당 리소스 그룹에 잠금을 추가합니다.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

다음 예제는 배포된 구독에 역할을 할당합니다.

```bicep
targetScope = 'subscription'

@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}

resource roleAssignSub 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
}
```

## <a name="apply-to-resource"></a>리소스에 적용

리소스에 확장 리소스를 적용하려면 `scope` 속성을 사용합니다. 범위 속성에서 확장을 추가하는 리소스를 참조합니다. 리소스의 기호 이름을 제공하여 리소스를 참조합니다. Scope 속성은 확장 리소스 유형의 루트 속성입니다.

다음 예제에서는 스토리지 계정을 만들고 여기에 역할을 적용합니다.

```bicep
@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()
param location string = resourceGroup().location

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}
var uniqueStorageName = 'storage${uniqueString(resourceGroup().id)}'

resource demoStorageAcct 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource roleAssignStorage 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
  scope: demoStorageAcct
}
```

기존 리소스에 확장 리소스를 적용할 수 있습니다. 다음 예제에서는 기존 스토리지 계정에 잠금을 추가합니다.

```bicep
resource demoStorageAcct 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: 'examplestore'
}

resource createStorageLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'storeLock'
  scope: demoStorageAcct
  properties: {
    level: 'CanNotDelete'
    notes: 'Storage account should not be deleted.'
  }
}
```

## <a name="next-steps"></a>다음 단계

범위에 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [구독 배포](deploy-to-subscription.md)
* [관리 그룹 배포](deploy-to-management-group.md)
* [테넌트 배포](deploy-to-tenant.md)