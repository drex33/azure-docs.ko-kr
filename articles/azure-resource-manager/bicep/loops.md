---
title: Bicep의 반복적인 루프
description: 루프를 사용 하 여 Bicep의 컬렉션을 반복 합니다.
ms.topic: conceptual
ms.date: 12/02/2021
ms.openlocfilehash: 2b31c3b6ed55f1f03d9a5420491a41f0cd4efb3f
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133518149"
---
# <a name="iterative-loops-in-bicep"></a>Bicep의 반복적인 루프

이 아티클에서는 구문을 사용 하 여 컬렉션의 항목을 반복 하는 방법을 보여 줍니다 `for` . 루프를 사용 하 여 리소스, 모듈, 변수, 속성 또는 출력의 여러 복사본을 정의할 수 있습니다. 루프를 사용 하 여 Bicep 파일의 반복 구문을 방지 하 고 배포 중에 만들 복사본 수를 동적으로 설정 합니다.

### <a name="microsoft-learn"></a>Microsoft Learn

루프에 대 한 자세한 내용 및 실습 지침은 **Microsoft Learn** 에서 [조건 및 루프를 사용 하 여 유연한 Bicep 템플릿 빌드](/learn/modules/build-flexible-bicep-templates-conditions-loops/) 를 참조 하세요.

## <a name="loop-syntax"></a>루프 구문

루프는 다음을 통해 선언할 수 있습니다.

- **정수 인덱스** 사용. 이 옵션은 "이 많은 인스턴스를 만들려고 합니다." 라는 시나리오에서 작동 합니다. [Range 함수](bicep-functions-array.md#range) 는 시작 인덱스에서 시작 하 고 지정 된 요소 수를 포함 하는 정수 배열을 만듭니다. 루프 내에서 정수 인덱스를 사용 하 여 값을 수정할 수 있습니다. 자세한 내용은 [정수 인덱스](#integer-index)를 참조 하십시오.

  ```bicep
  [for <index> in range(<startIndex>, <numberOfElements>): {
    ...
  }]
  ```

- **배열의 항목** 사용 이 옵션은 시나리오 "배열의 각 요소에 대 한 인스턴스를 만들려고 합니다."와 같은 경우에 작동 합니다. 루프 내에서 현재 배열 요소의 값을 사용 하 여 값을 수정할 수 있습니다. 자세한 내용은 [배열 요소](#array-elements)를 참조 하세요.

  ```bicep
  [for <item> in <collection>: {
    ...
  }]
  ```

- **사전 개체의 항목** 을 사용 합니다. 이 옵션은 시나리오 "개체의 각 항목에 대 한 인스턴스를 만들려고 합니다."와 같은 경우에 작동 합니다. [Items 함수](bicep-functions-array.md#items) 는 개체를 배열로 변환 합니다. 루프 내에서 개체의 속성을 사용 하 여 값을 만들 수 있습니다. 자세한 내용은 [Dictionary 개체](#dictionary-object)를 참조 하세요.

  ```bicep
  [for <item> in items(<object>): {
    ...
  }]
  ```

- **정수 인덱스 및 배열의 항목** 사용 이 옵션은 시나리오에 따라 다음과 같이 작동 합니다. "배열의 각 요소에 대 한 인스턴스를 만들려고 하지만 다른 값을 만들려면 현재 인덱스가 필요 합니다." 자세한 정보는 [루프 배열 및 인덱스](#array-and-index)를 참조하세요.

  ```bicep
  [for (<item>, <index>) in <collection>: {
    ...
  }]
  ```

- **조건부 배포** 추가 이 옵션은 시나리오에서 "여러 인스턴스를 만들려고 하는데, 조건이 true 인 경우에만 배포 하려는 각 인스턴스에 대해 수행 합니다."와 같은 경우에 작동 합니다. 자세한 내용은 [조건과 함께 루프](#loop-with-condition)를 참조 하세요.

  ```bicep
  [for <item> in <collection>: if(<condition>) {
    ...
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep에서 루프를 사용 하는 경우 다음과 같은 제한 사항이 있습니다.

- 루프 반복은 음수 이거나 800 반복을 초과할 수 없습니다.
- 중첩 된 자식 리소스를 사용 하 여 리소스를 반복할 수 없습니다. 자식 리소스를 최상위 리소스로 변경 합니다.  [자식 리소스에 대 한 반복](#iteration-for-a-child-resource)을 참조 하세요.
- 여러 수준의 속성을 반복할 수 없습니다.

## <a name="integer-index"></a>정수 인덱스

인덱스를 사용 하는 간단한 예는 문자열 배열을 포함 하는 **변수** 를 만듭니다.

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

다음 예제에서는 매개 변수에 지정 된 저장소 계정의 수를 만듭니다 `storageCount` . 각 저장소 계정에 대해 3 개의 속성을 반환 합니다.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-06-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: storageAcct[i].id
  blobEndpoint: storageAcct[i].properties.primaryEndpoints.blob
  status: storageAcct[i].properties.statusOfPrimary
}]
```

인덱스 `i`는 스토리지 계정 리소스 이름을 만드는 데 사용됩니다.

다음 예제에서는 모듈을 여러 번 배포 합니다.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

## <a name="array-elements"></a>배열 요소

다음 예제에서는 `storageNames` 매개 변수에 제공된 각 이름에 대해 하나의 스토리지 계정을 만듭니다.

```bicep
param location string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-06-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

다음 예제에서는 배열을 반복 하 여 속성을 정의 합니다. 가상 네트워크 내에 두 개의 서브넷을 만듭니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/loops/loopproperty.bicep" highlight="23-28" :::

## <a name="array-and-index"></a>배열 및 인덱스

다음 예에서는 스토리지 계정을 정의할 때 배열 요소와 인덱스 값을 모두 사용합니다.

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-06-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  sku: {
    name: config.sku
  }
  kind: 'StorageV2'
}]
```

다음 예제에서는 배열의 요소와 인덱스를 모두 사용 하 여 새 리소스에 대 한 정보를 출력 합니다.

```bicep
param location string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: location
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

## <a name="dictionary-object"></a>사전 개체

사전 개체의 요소를 반복 하려면 개체를 배열로 변환 하는 [items 함수](bicep-functions-array.md#items)를 사용 합니다. 속성을 사용 `value` 하 여 개체에 대 한 속성을 가져옵니다.

```bicep
param nsgValues object = {
  nsg1: {
    name: 'nsg-westus1'
    location: 'westus'
  }
  nsg2: {
    name: 'nsg-east1'
    location: 'eastus'
  }
}

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for nsg in items(nsgValues): {
  name: nsg.value.name
  location: nsg.value.location
}]
```

## <a name="loop-with-condition"></a>조건을 사용한 루프

**리소스 및 모듈** 의 경우 `if` 루프 구문을 사용 하 여 식을 추가 하 여 조건에 따라 컬렉션을 배포할 수 있습니다.

다음 예에서는 condition 문과 함께 사용 되는 루프를 보여 줍니다. 이 예에서는 모듈의 모든 인스턴스에 단일 조건이 적용 됩니다.

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

다음 예제에서는 배열에서 현재 요소에 해당 하는 조건을 적용 하는 방법을 보여 줍니다.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

## <a name="deploy-in-batches"></a>일괄 배포

기본적으로 Azure 리소스는 동시에 배포 됩니다. 루프를 사용하여 한 리소스 종류의 여러 인스턴스를 만드는 경우 해당 인스턴스는 모두 동시에 배포됩니다. 생성되는 순서는 정해져 있지 않습니다. Bicep 파일에서 총 리소스 제한이 800개라는 점을 제외하고 병렬로 배포되는 리소스의 수에는 제한이 없습니다.

한 리소스 종류의 모든 인스턴스를 동시에 업데이트하려고 하지는 않을 수 있습니다. 예를 들어 프로덕션 환경을 업데이트할 때 특정 수를 한 번에 업데이트하도록 업데이트를 늦추려고 할 수 있습니다. 일괄 처리할 인스턴스의 하위 집합을 지정하고 동시에 배포할 수 있습니다. 다른 인스턴스는 해당 일괄 처리가 완료되기를 기다립니다.

리소스 인스턴스를 직렬로 배포하려면 [batchSize 데코레이터](./file.md#resource-and-module-decorators)를 추가합니다. 값을 동시에 배포할 인스턴스 수로 설정합니다. 루프에 이전 인스턴스에 대한 종속성이 생성됩니다. 따라서 이전 일괄 처리가 완료될 때까지 하나의 일괄 처리를 시작하지 않습니다.

```bicep
param location string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-06-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

순차 배포의 경우 일괄 처리 크기를 1로 설정 합니다.

`batchSize`데코레이터는 [sys 네임 스페이스](bicep-functions.md#namespaces-for-functions)에 있습니다. 이 데코레이터를 같은 이름의 다른 항목과 구분 해야 하는 경우에는 데코레이터 앞에 **sys** 를 사용 합니다. `@sys.batchSize(2)`

## <a name="iteration-for-a-child-resource"></a>자식 리소스에 대한 반복

중첩된 자식 리소스에 대해 루프를 사용할 수 없습니다. 자식 리소스의 인스턴스를 두 개 이상 만들려면 자식 리소스를 최상위 리소스로 변경합니다.

예를 들어, 일반적으로 파일 서비스 및 파일 공유를 스토리지 계정에 대한 중첩 리소스로 정의한다고 가정합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

파일 공유를 두 개 이상 만들려면 스토리지 계정 외부로 이동합니다. `parent` 속성을 통해 부모 리소스와의 관계를 정의합니다.

다음 예제에서는 스토리지 계정, 파일 서비스 및 두 개 이상의 파일 공유를 만드는 방법을 보여 줍니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-06-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-06-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>다음 단계

- 루프에서 만들어진 리소스에 대 한 종속성을 설정 하려면 [리소스 종속성](./resource-declaration.md#dependencies)을 참조 하세요.
