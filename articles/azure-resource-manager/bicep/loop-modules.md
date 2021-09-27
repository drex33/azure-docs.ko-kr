---
title: Bicep에서 모듈의 여러 인스턴스 배포
description: Bicep 파일에서 루프 및 배열을 사용하여 모듈의 여러 인스턴스를 배포합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: f8893fa6716d1b106e54f7eb76002622ce5a4bc3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793439"
---
# <a name="module-iteration-in-bicep"></a>Bicep의 모듈 반복

이 문서에서는 Bicep 파일에서 [모듈](modules.md)의 인스턴스를 두 개 이상 만드는 방법을 설명합니다. `module` 선언에 루프를 추가하고 모듈을 배포할 시간 수를 동적으로 설정할 수 있습니다. Bicep 파일에서 구문이 반복하지 않도록 합니다.

[리소스](loop-resources.md), [속성](loop-properties.md), [변수](loop-variables.md), [출력](loop-outputs.md)이 있는 루프도 사용할 수 있습니다.

### <a name="microsoft-learn"></a>Microsoft Learn

루프에 대 한 자세한 내용 및 실습 지침은 **Microsoft Learn** 에서 [조건 및 루프를 사용 하 여 유연한 Bicep 템플릿 빌드](/learn/modules/build-flexible-bicep-templates-conditions-loops/) 를 참조 하세요.

## <a name="syntax"></a>Syntax

루프는 다음과 같이 여러 모듈을 선언하는 데 사용할 수 있습니다.

- 루프 인덱스 사용.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <index> in range(<start>, <stop>): {
    <module-properties>
  }]
  ```

  자세한 정보는 [루프 인덱스](#loop-index)를 참조하세요.

- 배열 반복.

  ```bicep
  module <module-symbolic-name> '<module-file>' = [for <item> in <collection>: {
    <module-properties>
  }]
  ```

  자세한 내용은 [루프 배열](#loop-array)을 참조하세요.

- 배열과 인덱스를 반복합니다.

  ```bicep
  module <module-symbolic-name> 'module-file' = [for (<item>, <index>) in <collection>: {
    <module-properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다.

## <a name="loop-index"></a>루프 인덱스

다음 예제에서는 `storageCount` 매개 변수에 지정된 횟수 만큼 모듈을 배포합니다. 모듈의 각 인스턴스는 스토리지 계정을 생성합니다.

```bicep
param location string = resourceGroup().location
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

인덱스 `i`는 스토리지 계정 리소스 이름을 만드는 데 사용됩니다. 스토리지 계정은 모듈에 매개 변수 값으로 전달됩니다.

## <a name="loop-array"></a>루프 배열

다음 예제에서는 `storageNames` 매개 변수에 제공된 각 이름에 대해 모듈을 배포합니다. 모듈은 스토리지 계정을 생성합니다

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

module stgModule './storageAccount.bicep' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  params: {
    storageName: name
    location: location
  }
}]

```

모듈 컬렉션 참조는 출력 루프에서 지원되지 않습니다. 컬렉션의 모듈에서 결과를 출력하려면 배열 인덱서를 식에 적용합니다. 자세한 내용은 [출력 반복](loop-outputs.md)을 참조하세요.

## <a name="module-iteration-with-condition"></a>조건을 사용한 모듈 반복

다음 예에서는 필터링된 모듈 루프가 있는 Bicep 파일을 보여 줍니다. 필터는 부울 값으로 계산되는 식이어야 합니다.

```bicep
param location string = resourceGroup().location
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}storage${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

위의 예제에서 모듈은 부울 값이 `true`인 경우에만 호출됩니다.

필터는 [리소스 루프](loop-resources.md)에서도 지원됩니다.

## <a name="deploy-in-batches"></a>일괄 배포

기본값으로 Resource Manager는 병렬로 리소스를 만듭니다. 루프를 사용하여 한 리소스 종류의 여러 인스턴스를 만드는 경우 해당 인스턴스는 모두 동시에 배포됩니다. 생성되는 순서는 정해져 있지 않습니다. Bicep 파일에서 총 리소스 제한이 800개라는 점을 제외하고 병렬로 배포되는 리소스의 수에는 제한이 없습니다.

한 리소스 종류의 모든 인스턴스를 동시에 업데이트하려고 하지는 않을 수 있습니다. 예를 들어 프로덕션 환경을 업데이트할 때 특정 수를 한 번에 업데이트하도록 업데이트를 늦추려고 할 수 있습니다. 일괄 처리할 인스턴스의 하위 집합을 지정하고 동시에 배포할 수 있습니다. 다른 인스턴스는 해당 일괄 처리가 완료되기를 기다립니다.

모듈의 인스턴스를 직렬로 배포하려면 [batchSize 데코레이터](./file.md#resource-and-module-decorators)를 추가합니다. 값을 동시에 배포할 인스턴스 수로 설정합니다. 루프에 이전 인스턴스에 대한 종속성이 생성됩니다. 따라서 이전 일괄 처리가 완료될 때까지 하나의 일괄 처리를 시작하지 않습니다.

```bicep
param location string = resourceGroup().location

@batchSize(2)
module stgModule './storageAccount.bicep' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

순차 배포의 경우, 일괄 처리 크기를 1로 설정합니다.

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep의 리소스 반복](loop-resources.md)
  - [Bicep의 속성 반복](loop-properties.md)
  - [Bicep의 변수 반복](loop-variables.md)
  - [Bicep의 출력 반복](loop-outputs.md)
- 모듈에 대한 자세한 내용은 [Use Bicep modules](modules.md)를 참조하세요.
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
