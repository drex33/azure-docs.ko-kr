---
title: 템플릿 함수 - 비교
description: Azure Resource Manager 템플릿(ARM 템플릿)에서 값을 비교하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 0f9243cdc61ad5e7710663328eb4f85c9471fda5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812358"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM 템플릿의 비교 함수

Resource Manager는 ARM 템플릿(Azure Resource Manager 템플릿)에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

매개 변수에서 첫 번째 null이 아닌 값을 반환합니다. 빈 문자열, 빈 배열 및 빈 개체는 null이 아닙니다.

Bicep에서는 대신 `??` 연산자를 사용합니다. [Coalesce ??](../bicep/operators-logical.md#coalesce-)를 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int, 문자열, 배열 또는 개체 |null인지 테스트할 첫 번째 값입니다. |
| 더 많은 args |예 |int, 문자열, 배열 또는 개체 | null을 테스트할 추가 값입니다. |

### <a name="return-value"></a>반환 값

문자열, int, 배열 또는 개체일 수 있는 첫 번째 null이 아닌 매개 변수의 값입니다. 모든 매개 변수가 null이면 null입니다.

### <a name="example"></a>예제

다음 예제 템플릿에서는 병합을 다르게 사용하여 출력된 결과를 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/coalesce.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | Int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array |  [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

두 값이 서로 일치하는지 여부를 확인합니다.

Bicep에서는 대신 `==` 연산자를 사용합니다. [같음 ==](../bicep/operators-comparison.md#equals-)을 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int, 문자열, 배열 또는 개체 |같은지 확인할 첫 번째 값입니다. |
| arg2 |예 |int, 문자열, 배열 또는 개체 |같은지 확인할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

값이 같으면 **True** 를 반환하고 같지 않으면 **False** 를 반환합니다.

### <a name="remarks"></a>설명

equals 함수는 종종 `condition` 요소와 함께 리소스 배포 여부를 테스트하는 데 사용됩니다.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

### <a name="example"></a>예제

다음 예제에서는 서로 다른 형식의 값이 같은지 확인합니다. 모든 기본값은 True를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/equals.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

다음 예제 템플릿에서는 **equals** 에 [not](template-functions-logical.md#not)을 사용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

위 예제의 출력은 다음과 같습니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

첫 번째 값이 두 번째 값보다 큰지 여부를 확인합니다.

Bicep에서는 대신 `>` 연산자를 사용합니다. [보다 큼 >](../bicep/operators-comparison.md#greater-than-)을 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |greater 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |greater 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크면 **True** 를 반환하고 크지 않으면 **False** 를 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 한 값이 다른 값 보다 큰지 여부를 확인 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greater.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

첫 번째 값이 두 번째 값보다 크거나 같은지 여부를 확인합니다.

Bicep에서는 대신 `>=` 연산자를 사용합니다. [크거나 같음 >=](../bicep/operators-comparison.md#greater-than-or-equal-)을 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |greater 또는 equal 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |greater 또는 equal 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크거나 같으면 **True** 를 반환하고 작으면 **False** 를 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 한 값이 다른 값 보다 크거나 같은지 여부를 확인 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greaterorequals.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

첫 번째 값이 두 번째 값보다 작은지 여부를 확인합니다.

Bicep에서는 대신 `<` 연산자를 사용합니다. [보다 작음 <](../bicep/operators-comparison.md#less-than-)을 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |less 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |less 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작으면 **True** 를 반환하고 작지 않으면 **False** 를 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 한 값이 다른 값 보다 적은지 여부를 확인 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/less.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

첫 번째 값이 두 번째 값보다 작거나 같은지 여부를 확인합니다.

Bicep에서는 대신 `<=` 연산자를 사용합니다. [작거나 같음 <=](../bicep/operators-comparison.md#less-than-or-equal-)을 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |int 또는 문자열 |less 또는 equals 비교에 사용할 첫 번째 값입니다. |
| arg2 |예 |int 또는 문자열 |less 또는 equals 비교에 사용할 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작거나 같으면 **True** 를 반환하고 크면 **False** 를 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 한 값이 다른 값 보다 작거나 같은지 여부를 확인 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/lessorequals.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
