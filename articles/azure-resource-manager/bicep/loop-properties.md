---
title: Bicep 속성의 여러 인스턴스 정의
description: 리소스 속성을 만들 때 반복하려면 Bicep 속성 루프를 사용합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 6e9d41136401b28cf330bc828947d35a67c69a43
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225953"
---
# <a name="property-iteration-in-bicep"></a>Bicep의 속성 반복

이 문서에서는 Bicep 파일에서 속성 인스턴스를 두 개 이상 만드는 방법을 설명합니다. 리소스의 `properties` 섹션에 루프를 추가하고 속성의 항목 수를 동적으로 설정할 수 있습니다. Bicep 파일에서 구문이 반복하지 않도록 합니다.

속성에 루프를 적용하는 경우에도 최상위 리소스가 있는 루프만 사용할 수 있습니다. 자식 리소스를 최상위 수준 리소스로 변경하는 방법에 대해 자세히 알아보려면 [자식 리소스에 대한 반복](loop-resources.md#iteration-for-a-child-resource)을 참조하세요.

[모듈](loop-modules.md), [리소스](loop-resources.md), [변수](loop-variables.md), [출력](loop-outputs.md)이 있는 루프도 사용할 수 있습니다.

## <a name="syntax"></a>구문

루프는 다음과 같은 방법으로 여러 속성을 선언하는 데 사용될 수 있습니다.

- 루프 인덱스 사용.

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

- 배열 반복.

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

  자세한 내용은 [루프 배열](#loop-array)을 참조하세요.

- 배열과 인덱스를 반복합니다.

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. 

## <a name="loop-array"></a>루프 배열

이 예제에서는 `subnets` 속성의 배열을 반복하여 가상 네트워크 내에서 서브넷을 두 개 만듭니다.

```bicep
param rgLocation string = resourceGroup().location

var subnets = [
  {
    name: 'api'
    subnetPrefix: '10.144.0.0/24'
  }
  {
    name: 'worker'
    subnetPrefix: '10.144.1.0/24'
  }
]

resource vnet 'Microsoft.Network/virtualNetworks@2020-07-01' = {
  name: 'vnet'
  location: rgLocation
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.144.0.0/20'
      ]
    }
    subnets: [for subnet in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.subnetPrefix
      }
    }]
  }
}
```

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep의 리소스 반복](loop-resources.md)
  - [Bicep의 모듈 반복](loop-modules.md)
  - [Bicep의 변수 반복](loop-variables.md)
  - [Bicep의 출력 반복](loop-outputs.md)
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
