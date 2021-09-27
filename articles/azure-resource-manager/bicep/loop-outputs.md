---
title: Bicep에서 출력 값의 여러 인스턴스 정의
description: Bicep 출력 루프를 사용하여 배포 값을 반복하고 반환합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 28aa16d5f65891df063db7b43211e466855dfeec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832052"
---
# <a name="output-iteration-in-bicep"></a>Bicep의 출력 반복

이 문서에서는 Bicep 파일에서 출력에 대한 값을 두 개 이상 만드는 방법을 보여 줍니다. 파일의 `output` 섹션에 루프를 추가하고, 배포하는 동안 여러 항목을 동적으로 반환할 수 있습니다.

[모듈](loop-modules.md), [리소스](loop-resources.md), [리소스의 속성](loop-properties.md), [변수](loop-variables.md)와 함께 루프를 사용할 수도 있습니다.

### <a name="microsoft-learn"></a>Microsoft Learn

루프 및 실습 지침에 대한 자세한 내용은 **Microsoft Learn** [조건 및 루프를 사용하여 유연한 Bicep 템플릿 빌드를 참조하세요.](/learn/modules/build-flexible-bicep-templates-conditions-loops/)

## <a name="syntax"></a>구문

다음을 통해 배포하는 동안 항목을 반환하려면 루프를 사용할 수 있습니다.

- 루프 인덱스 사용.

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  자세한 정보는 [루프 인덱스](#loop-index)를 참조하세요.

- 배열 반복.

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 배열 및 인덱스 반복.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

  자세한 정보는 [루프 배열 및 인덱스](#loop-array-and-index)를 참조하세요.

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. 

## <a name="loop-index"></a>루프 인덱스

다음 예제에서는 다양한 수의 스토리지 계정을 만들고 각 스토리지 계정에 대한 엔드포인트를 반환합니다.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): stg[i].properties.primaryEndpoints.blob]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

다음 예제에서는 새 스토리지 계정에서 세 가지 속성을 반환합니다.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: stg[i].id
  blobEndpoint: stg[i].properties.primaryEndpoints.blob
  status: stg[i].properties.statusOfPrimary
}]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="loop-array-and-index"></a>루프 배열 및 인덱스

이 예제에서는 배열의 요소와 인덱스를 모두 사용합니다.

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep의 리소스 반복](loop-resources.md)
  - [Bicep의 모듈 반복](loop-modules.md)
  - [Bicep의 속성 반복](loop-properties.md)
  - [Bicep의 변수 반복](loop-variables.md)
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.

