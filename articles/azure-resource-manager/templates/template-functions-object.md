---
title: 템플릿 함수 - 개체
description: 개체 작업을 위해 ARM 템플릿(Azure Resource Manager 템플릿)에서 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820438"
---
# <a name="object-functions-for-arm-templates"></a>ARM 템플릿의 개체 함수

리소스 관리자는 ARM 템플릿(Azure Resource Manager 템플릿)에서 개체를 사용하기 위한 몇 가지 함수를 제공합니다.

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

## <a name="contains"></a>포함

`contains(container, itemToFind)`

배열에 값이 포함되는지, 개체에 키가 포함되는지 또는 문자열에 하위 문자열이 포함되는지를 확인합니다. 문자열 비교에서는 대/소문자를 구분합니다. 그러나 개체에 키가 포함되어 있는지를 테스트할 때는 비교에서 대/소문자를 구분하지 않습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| container |예 |배열, 개체 또는 문자열 |찾을 값을 포함하는 값입니다. |
| itemToFind |예 |문자열 또는 int |찾을 값입니다. |

### <a name="return-value"></a>반환 값

항목이 있으면 **True** 이고, 항목이 없으면 **False** 입니다.

### <a name="example"></a>예제

다음 예제에서는 여러 다른 형식의 contains를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

키와 값에서 개체를 만듭니다.

`createObject`함수는 Bicep에서 지원 되지 않습니다.  `{}`를 사용하여 개체를 생성합니다. [개체](../bicep/data-types.md#objects)를 참조하세요.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| key1 |예 |문자열 |키의 이름입니다. |
| value1 |예 |int, 부울, 문자열, 개체 또는 배열 |키의 값입니다. |
| 추가 키 |예 |문자열 |키의 추가 이름입니다. |
| 더 많은 값 |예 |int, 부울, 문자열, 개체 또는 배열 |키에 대 한 추가 값입니다. |

함수는 짝수의 매개 변수만 사용할 수 있습니다. 각 키는 일치 값이 있어야 합니다.

### <a name="return-value"></a>반환 값

각 키와 값 쌍을 포함하는 개체입니다.

### <a name="example"></a>예제

다음 예에서는 다양한 값 형식에서 개체를 만듭니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

기본값을 사용하는 이전 예제의 출력은 다음과 같은 값을 가진 `newObject`라는 개체입니다.

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>비어 있음

`empty(itemToTest)`

배열, 개체 또는 문자열이 비어 있는지를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| itemToTest |예 |배열, 개체 또는 문자열 |비어 있는지 확인할 값입니다. |

### <a name="return-value"></a>반환 값

값이 비어 있으면 **True** 를 반환하고 비어 있지 않으면 **False** 를 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 배열, 개체 및 문자열이 비어 있는지 여부를 확인합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>교집합

`intersection(arg1, arg2, arg3, ...)`

매개 변수에서 공통 요소를 갖는 단일 배열 또는 개체를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 첫 번째 값입니다. |
| arg2 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 두 번째 값입니다. |
| more 인수 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 값이 더 많습니다. |

### <a name="return-value"></a>반환 값

공통 요소가 있는 배열 또는 개체입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 개체와의 교집합을 사용하는 방법을 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

<a id="json"></a>

## <a name="json"></a>json :

`json(arg1)`

유효한 JSON 문자열을 JSON 데이터 형식으로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 |JSON으로 변환할 값입니다. 문자열은 올바른 형식의 JSON 문자열이어야 합니다. |

### <a name="return-value"></a>반환 값

지정된 문자열의 JSON 데이터 형식이거나, **null** 을 지정한 경우 빈 개체입니다.

### <a name="remarks"></a>설명

JSON 개체에 매개 변수 값이나 변수를 포함해야 하는 경우 [concat](template-functions-string.md#concat) 함수를 사용하여 함수로 전달할 문자열을 작성합니다.

[null()](#null)을 사용하여 Null 값을 가져올 수도 있습니다.

### <a name="example"></a>예제

다음 예제에서는 `json` 함수를 사용하는 방법을 보여 줍니다. 빈 개체에 대해 를 전달할 수 `null` 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| emptyObjectOutput | 부울 | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | test |
| booleanOutput | 부울 | True |
| intOutput | 정수 | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | { "a": "demo value" } |

## <a name="length"></a>length

`length(arg1)`

배열의 요소 수, 문자열의 문자 수 또는 개체의 루트 수준 속성 수를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열, 문자열 또는 개체 |요소의 수를 가져오는 데 사용할 배열, 문자 수를 가져오는 데 사용할 문자열 또는 루트 수준의 속성 수를 가져오려는 데 사용할 개체입니다. |

### <a name="return-value"></a>반환 값

int입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 문자열에 length를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

null을 반환합니다.

`null`함수는 Bicep에서 사용할 수 없습니다. 대신 `null` 키워드를 사용합니다.

### <a name="parameters"></a>매개 변수

Null 함수는 매개 변수를 허용하지 않습니다.

### <a name="return-value"></a>반환 값

항상 Null인 값입니다.

### <a name="example"></a>예제

다음 예제에서는 Null 함수를 사용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

위 예제의 출력은 다음과 같습니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

매개 변수의 모든 요소를 포함하는 단일 배열 또는 개체를 반환합니다. 중복된 값 또는 키는 한 번만 포함됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 개체 |요소를 조인하는 데 사용할 첫 번째 값입니다. |
| arg2 |예 |배열 또는 개체 |요소를 조인하는 데 사용할 두 번째 값입니다. |
| 추가 인수 |예 |배열 또는 개체 |요소를 조인 하는 데 사용할 추가 값입니다. |

### <a name="return-value"></a>반환 값

배열 또는 개체입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 개체에 union을 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
