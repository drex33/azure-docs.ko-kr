---
title: Bicep의 변수
description: Bicep에서 변수를 정의하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 35175cff08b1470725da87015a3faef9f5bc3ed4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494789"
---
# <a name="variables-in-bicep"></a>Bicep의 변수

이 문서에서는 Bicep 파일에서 변수를 정의하고 사용하는 방법을 설명합니다. 변수를 사용하여 Bicep 파일 개발을 간소화할 수 있습니다. Bicep 파일 전체에서 복잡한 식을 반복하는 대신 복잡한 식을 포함하는 변수를 정의합니다. 그런 다음 Bicep 파일 전체에서 필요에 따라 해당 변수를 사용합니다.

Resource Manager는 배포 작업을 시작하기 전에 변수를 확인합니다. Bicep 파일에서 변수를 사용할 때마다 Resource Manager는 변수를 확인된 값으로 바꿉니다.

## <a name="define-variable"></a>변수 정의

변수를 정의하는 구문은 다음과 같습니다.

```bicep
var <variable-name> = <variable-value>
```

변수는 매개 변수, 모듈 또는 리소스와 동일한 이름을 가질 수 없습니다.

변수의 [데이터 형식은](data-types.md) 지정하지 않습니다. 형식은 값에서 유추됩니다. 다음 예제에서는 변수를 문자열로 설정합니다.

```bicep
var stringVar = 'example value'
```

변수를 생성할 때 매개 변수 또는 다른 변수의 값을 사용할 수 있습니다.

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'preset variable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'

output addToVar string = concatToVar
output addToParam string = concatToParam
```

앞의 예제는 다음을 반환합니다.

```json
{
  "addToParam": {
    "type": "String",
    "value": "deployment parameterAddToParam"
  },
  "addToVar": {
    "type": "String",
    "value": "preset variableAddToVar"
  }
}
```

[Bicep 함수](bicep-functions.md)를 사용하여 변수 값을 생성할 수 있습니다. 다음 예제에서는 Bicep 함수를 사용하여 스토리지 계정 이름에 대한 문자열 값을 만듭니다.

```bicep
param storageNamePrefix string = 'stg'
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

output uniqueStorageName string = storageName
```

앞의 예제에서는 다음과 같은 값을 반환합니다.

```json
"uniqueStorageName": {
  "type": "String",
  "value": "stghzuunrvapn6sw"
}
```

변수를 정의할 때 반복 루프를 사용할 수 있습니다. 다음 예제에서는 세 가지 속성을 가진 개체의 배열을 만듭니다.

```bicep
param itemCount int = 3

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
  }
]
```

변수와 함께 사용할 수 있는 루프 형식에 대한 자세한 내용은 [Bicep의 반복 루프를](loops.md)참조하세요.

## <a name="use-variable"></a>변수 사용

다음 예제에서는 리소스 속성에 대한 변수를 사용하는 방법을 설명합니다. 변수의 이름(`storageName`)을 제공하여 변수의 값을 참조합니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variableswithfunction.bicep" highlight="4,7,15" :::

스토리지 계정 이름은 소문자를 사용해야 하므로 `storageName` 변수는 `toLower` 함수를 사용하여 `storageNamePrefix` 값을 소문자로 만듭니다. `uniqueString` 함수는 리소스 그룹 ID에서 고유한 값을 만듭니다. 값은 문자열에 연결됩니다.

## <a name="configuration-variables"></a>구성 변수

환경을 구성하기 위한 관련된 값을 포함하는 변수를 정의할 수 있습니다. 값이 있는 개체로 변수를 정의합니다. 다음 예제에서는 **test** 와 **prod** 의 두 환경에 대한 값을 보유하는 개체를 보여 줍니다. 배포하는 동안 해당 값 중 하나를 전달합니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>다음 단계

- 변수에 사용할 수 있는 속성에 대한 자세한 내용은 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 루프 구문을 사용하는 방법에 대한 자세한 내용은 [Bicep의 반복 루프를](loops.md)참조하세요.