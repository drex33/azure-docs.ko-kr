---
title: 템플릿 함수 - 논리적
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 논리 값을 확인하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: c69e10b660d5b7cbf768ea31fda6678d07053224
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959628"
---
# <a name="logical-functions-for-arm-templates"></a>ARM 템플릿의 논리 함수

Resource Manager는 ARM 템플릿(Azure Resource Manager 템플릿)에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

## <a name="and"></a>및

`and(arg1, arg2, ...)`

모든 매개 변수 값이 True인지 확인합니다.

`and` 함수는 Bicep에서 지원되지 않습니다. 대신 [&& 연산자](../bicep/operators-logical.md#and-)를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |예 |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

모든 값이 True이면 **True** 를 반환하고 그렇지 않으면 **False** 를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부울로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 부울입니다.

### <a name="remarks"></a>설명

[true()](#true) 및 [false()](#false)를 사용하여 부울 값을 가져올 수도 있습니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)에서는 문자열 또는 정수에 bool을 사용하는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

false를 반환합니다.

`false` 함수는 Bicep에서 사용할 수 없습니다.  대신 `false` 키워드를 사용합니다.

### <a name="parameters"></a>매개 변수

false 함수는 매개 변수를 허용하지 않습니다.

### <a name="return-value"></a>반환 값

항상 false인 부울입니다.

### <a name="example"></a>예제

다음 예에서는 false 출력 값을 반환합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

조건이 true인지 아니면 false인지에 따라 값을 반환합니다.

Bicep에서 `if` 함수는 지원되지 않습니다. 대신 [?: 연산자](../bicep/operators-logical.md#conditional-expression--)를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| condition(조건) |예 |boolean |true인지 false인지 확인할 값입니다. |
| trueValue |예 | 문자열, 정수, 개체 또는 배열 |조건이 true이면 반환할 값입니다. |
| falseValue |예 | 문자열, 정수, 개체 또는 배열 |조건이 false이면 반환할 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 매개 변수가 **True** 이면 두 번째 매개 변수를 반환하고 그렇지 않으면 세 번째 매개 변수를 반환합니다.

### <a name="remarks"></a>설명

조건이 **true** 이면 true 값만 평가됩니다. 조건이 **false** 이면 false 값만 평가됩니다. `if` 함수를 사용하여 조건부로 유효한 식만 포함할 수 있습니다. 예를 들어, 한 조건에는 있지만 다른 조건에는 없는 리소스를 참조할 수 있습니다. 조건에 따라 식을 평가하는 예는 다음 섹션에 나와 있습니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)에서는 `if` 함수를 사용하는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| yesOutput | String | 예 |
| noOutput | String | 아니요 |
| objectOutput | Object | { "test": "value1" } |

다음 [예제 템플릿](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)에서는 조건부로 유효한 식에서만 이 함수를 사용하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

## <a name="not"></a>not

`not(arg1)`

부울 값을 반대 값으로 변환합니다.

`not` 함수는 Bicep에서 지원되지 않습니다. 대신 [! 연산자](../bicep/operators-logical.md#not-)를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |변환할 값입니다. |

### <a name="return-value"></a>반환 값

매개 변수가 **False** 이면 **True** 를 반환합니다. 매개 변수가 **True** 이면 **False** 를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)에서는 [equals](template-functions-comparison.md#equals)에 **not** 을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>또는

`or(arg1, arg2, ...)`

매개 변수 값 중 하나가 True인지 확인합니다.

`or` 함수는 Bicep에서 지원되지 않습니다. 대신 [|| 연산자](../bicep/operators-logical.md#or-)를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |예 |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

True인 값이 하나라도 있으면 **True** 를 반환하고 그렇지 않으면 **False** 를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

true를 반환합니다.

`true` 함수는 Bicep에서 사용할 수 없습니다.  대신 `true` 키워드를 사용합니다.

### <a name="parameters"></a>매개 변수

true 함수는 매개 변수를 허용하지 않습니다.

### <a name="return-value"></a>반환 값

항상 true인 부울입니다.

### <a name="example"></a>예제

다음 예에서는 true 출력 값을 반환합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.