---
title: Bicep의 변수
description: Bicep에서 변수를 정의하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 040e40d20fe81bb72493f087c9d0583a911b1ee7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733340"
---
# <a name="variables-in-bicep"></a>Bicep의 변수

이 문서에서는 Bicep 파일에서 변수를 정의하고 사용하는 방법을 설명합니다. 변수를 사용하여 Bicep 파일 개발을 간소화할 수 있습니다. Bicep 파일 전체에서 복잡한 식을 반복하는 대신 복잡한 식을 포함하는 변수를 정의합니다. 그런 다음 Bicep 파일 전체에서 필요에 따라 해당 변수를 사용합니다.

Resource Manager는 배포 작업을 시작하기 전에 변수를 확인합니다. Bicep 파일에서 변수를 사용할 때마다 Resource Manager는 변수를 확인된 값으로 바꿉니다.

## <a name="define-variable"></a>변수 정의

변수를 정의할 때 변수에 대한 [데이터 형식](data-types.md)을 지정하지 않습니다. 대신 값 또는 템플릿 식을 제공합니다. 변수 형식은 확인된 값에서 유추됩니다. 다음 예제에서는 변수를 문자열로 설정합니다.

```bicep
var stringVar = 'example value'
```

변수를 생성할 때 매개 변수 또는 다른 변수의 값을 사용할 수 있습니다.

```bicep
param inputValue string = 'deployment Parameter'

var stringVar = 'myVariable'

var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'
```

[Bicep 함수](bicep-functions.md)를 사용하여 변수 값을 생성할 수 있습니다. [reference](bicep-functions-resource.md#reference) 및 [list](bicep-functions-resource.md#list) 함수는 변수를 선언할 때 유효합니다.

다음 예제에서는 스토리지 계정 이름에 대한 문자열 값을 만듭니다. 여러 Bicep 함수를 사용하여 매개 변수 값을 가져오고 이를 고유한 문자열에 연결합니다.

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

다음 예제에서는 리소스를 배포하지 않습니다. 다양한 형식의 변수를 선언하는 방법을 보여줍니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variables.bicep":::

루프를 사용하여 동적 개수의 요소가 있는 배열 변수를 선언할 수 있습니다. 자세한 내용은 [Bicep의 변수 반복을 참조하세요.](loop-variables.md)

## <a name="use-variable"></a>변수 사용

다음 예제에서는 리소스 속성에 대한 변수를 사용하는 방법을 설명합니다. 변수의 이름(`storageName`)을 제공하여 변수의 값을 참조합니다.

```bicep
param rgLocation string = resourceGroup().location
param storageNamePrefix string = 'STG'

var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}

output stgOutput string = storageName
```

스토리지 계정 이름은 소문자를 사용해야 하므로 `storageName` 변수는 `toLower` 함수를 사용하여 `storageNamePrefix` 값을 소문자로 만듭니다. `uniqueString` 함수는 리소스 그룹 ID에서 고유한 값을 만듭니다. 값은 문자열에 연결됩니다.

## <a name="configuration-variables"></a>구성 변수

환경을 구성하기 위한 관련된 값을 포함하는 변수를 정의할 수 있습니다. 값이 있는 개체로 변수를 정의합니다. 다음 예제에서는 **test** 와 **prod** 의 두 환경에 대한 값을 보유하는 개체를 보여 줍니다. 배포하는 동안 해당 값 중 하나를 전달합니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>다음 단계

- 변수에 사용할 수 있는 속성에 대한 자세한 내용은 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 변수 선언과 함께 루프를 사용하는 방법에 대한 자세한 내용은 [Bicep의 변수 반복을](loop-variables.md)참조하세요.
