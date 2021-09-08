---
title: Bicep에서 여러 변수 인스턴스 정의
description: Bicep 변수 루프를 사용하여 변수를 만들 때 반복합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: bf182379c9cc10db11e451f908df552a16520b45
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225205"
---
# <a name="variable-iteration-in-bicep"></a>Bicep의 변수 반복

이 문서에서는 Bicep 파일에서 변수에 대한 값을 두 개 이상 만드는 방법을 보여 줍니다. `variables` 선언에 루프를 추가하고 변수의 항목 수를 동적으로 설정할 수 있습니다. Bicep 파일에서 구문이 반복하지 않도록 합니다.

[모듈](loop-modules.md), [리소스](loop-resources.md), [리소스의 속성](loop-properties.md) 및 [출력](loop-outputs.md)과 함께 복사를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

루프는 다음과 같이 여러 변수를 선언하는 데 사용할 수 있습니다.

- 루프 인덱스 사용.

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

  자세한 내용은 [루프 인덱스](#loop-index)를 참조하세요.

- 배열 반복.

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

  자세한 내용은 [루프 배열](#loop-array)를 참조하세요.

- 배열과 인덱스를 반복합니다.

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. 

## <a name="loop-index"></a>루프 인덱스

다음 예제에서는 문자열 값의 배열을 만드는 방법을 보여줍니다.

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

다음 예제에서는 세 가지 속성(`name`, `diskSizeGB` 및 `diskIndex`)이 있는 개체 배열을 만드는 방법을 보여줍니다.

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="loop-array"></a>루프 배열

다음 예에서는 매개 변수로 전달된 배열을 반복합니다. 변수는 매개 변수에서 필요한 형식으로 개체를 구성합니다.

```bicep
@description('An array that contains objects with properties for the security rules.')
param securityRules array = [
  {
    name: 'RDPAllow'
    description: 'allow RDP connections'
    direction: 'Inbound'
    priority: 100
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.0.0/24'
    sourcePortRange: '*'
    destinationPortRange: '3389'
    access: 'Allow'
    protocol: 'Tcp'
  }
  {
    name: 'HTTPAllow'
    description: 'allow HTTP connections'
    direction: 'Inbound'
    priority: 200
    sourceAddressPrefix: '*'
    destinationAddressPrefix: '10.0.1.0/24'
    sourcePortRange: '*'
    destinationPortRange: '80'
    access: 'Allow'
    protocol: 'Tcp'
  }
]


var securityRulesVar = [for rule in securityRules: {
  name: rule.name
  properties: {
    description: rule.description
    priority: rule.priority
    protocol: rule.protocol
    sourcePortRange: rule.sourcePortRange
    destinationPortRange: rule.destinationPortRange
    sourceAddressPrefix: rule.sourceAddressPrefix
    destinationAddressPrefix: rule.destinationAddressPrefix
    access: rule.access
    direction: rule.direction
  }
}]

resource netSG 'Microsoft.Network/networkSecurityGroups@2020-11-01' = {
  name: 'NSG1'
  location: resourceGroup().location
  properties: {
    securityRules: securityRulesVar
  }
}
```

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep의 리소스 반복](loop-resources.md)
  - [Bicep의 모듈 반복](loop-modules.md)
  - [Bicep의 속성 반복](loop-properties.md)
  - [Bicep의 출력 반복](loop-outputs.md)
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
