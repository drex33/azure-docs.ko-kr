---
title: 변수의 여러 인스턴스 정의
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 복사 작업을 사용하여 변수를 만들 때 여러 번 반복합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: bf04a93d8ec6626bfbb2083789dbd6db08e2af51
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452180"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM 템플릿의 변수 반복

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 변수에 대한 값을 두 개 이상 만드는 방법을 보여줍니다. 템플릿의 변수 섹션에 `copy` 요소를 추가하면 배포 중에 변수에 대한 항목 수를 동적으로 설정할 수 있습니다. 템플릿 구문을 반복하지 않아도 됩니다.

[리소스](copy-resources.md), [리소스의 속성](copy-properties.md) 및 [출력](copy-outputs.md)과 함께 복사를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

복사 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

`name` 속성은 루프를 식별하는 모든 값입니다. `count` 속성은 변수에 대해 원하는 반복 횟수를 지정합니다.

`input` 속성은 반복할 속성을 지정합니다. `input` 속성의 값에서 생성된 요소 배열을 만듭니다. 단일 속성(예: 문자열) 또는 여러 속성이 있는 개체일 수 있습니다.

## <a name="copy-limits"></a>복사 제한

개수는 800개를 초과할 수 없습니다.

개수는 음수가 될 수 없습니다. 최신 버전의 Azure CLI, PowerShell 또는 REST API를 사용하여 템플릿을 배포하는 경우에는 0이 될 수 있습니다. 특히 다음을 사용해야 합니다.

- Azure PowerShell **2.6** 이상
- Azure CLI **2.0.74** 이상
- REST API 버전 **2019-05-10** 이상
- [연결된 배포](linked-templates.md)는 배포 리소스 유형에 API 버전 **2019-05-10** 이상을 사용해야 합니다.

이전 버전의 PowerShell, CLI 및 REST API는 개수에 0을 지원하지 않습니다.

## <a name="variable-iteration"></a>변수 반복

다음 예제에서는 문자열 값의 배열을 만드는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

이전 템플릿은 다음 값을 가진 배열을 반환합니다.

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

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "objectArray",
        "count": "[parameters('itemCount')]",
        "input": {
          "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('objectArray')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('objectArray')]"
    }
  }
}
```

앞의 예제에서는 다음 값을 가진 배열을 반환합니다.

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

> [!NOTE]
> 변수 반복은 오프셋 인수를 지원합니다. 오프셋은 `copyIndex('diskNames', 1)`과 같은 반복 이름 뒤에 와야 합니다. 오프셋 값을 제공하지 않으면 첫 번째 인스턴스의 기본값은 0입니다.
>

변수 내에서 `copy` 요소를 사용할 수도 있습니다. 다음 예제에서는 해당 값 중 하나로 배열이 있는 개체를 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "topLevelObject": {
      "sampleProperty": "sampleValue",
      "copy": [
        {
          "name": "disks",
          "count": "[parameters('itemCount')]",
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        }
      ]
    }
  },
  "resources": [],
  "outputs": {
    "objectResult": {
      "type": "object",
      "value": "[variables('topLevelObject')]"
    }
  }
}
```

앞의 예제에서는 다음 값을 가진 개체를 반환합니다.

```json
{
  "sampleProperty": "sampleValue",
  "disks": [
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
}
```

다음 예제에서는 변수와 함께 `copy`를 사용할 수 있는 다양한 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 변수에 대해 둘 이상의 값을 만드는 일반적인 시나리오를 보여줍니다.

|템플릿  |Description  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |변수를 반복하는 다양한 방법을 보여 줍니다. |
|[다중 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |네트워크 보안 그룹에 여러 보안 규칙을 배포합니다. 매개 변수에서 보안 규칙을 구성합니다. 매개 변수는 [여러 NSG 매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)을 참조합니다. |
|[변수를 사용 하 여 저장소 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewithvariables.json) | 변수를 반복 하 고 여러 저장소 계정을 만드는 방법에 대 한 예입니다. |

## <a name="next-steps"></a>다음 단계

- 자습서를 살펴보려면 [자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
- 복사 요소의 다른 용도는 다음을 참조하세요.
  - [ARM 템플릿의 리소스 반복](copy-resources.md)
  - [ARM 템플릿의 속성 반복](copy-properties.md)
  - [ARM 템플릿의 출력 반복](copy-outputs.md)
- 템플릿의 섹션에 대해 알아보려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
- 템플릿을 배포하는 방법을 자세히 알아보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
