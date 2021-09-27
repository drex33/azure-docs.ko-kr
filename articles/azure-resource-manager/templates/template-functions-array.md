---
title: 템플릿 함수 - 배열
description: 배열 작업을 위해 ARM 템플릿(Azure Resource Manager 템플릿)에서 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 25fb249e1840a7538fa1dd14f3b1a930bbefdfee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755998"
---
# <a name="array-functions-for-arm-templates"></a>ARM 템플릿의 배열 함수

리소스 관리자는 ARM 템플릿(Azure Resource Manager 템플릿)에서 배열을 사용하기 위한 몇 가지 함수를 제공합니다.

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

값으로 구분되는 문자열 값의 배열을 가져오려면 [split](template-functions-string.md#split)을 참조하세요.

## <a name="array"></a>array

`array(convertToArray)`

값을 배열로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |예 |int, 문자열, 배열 또는 개체 |배열로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

배열입니다.

### <a name="example"></a>예제

다음 예제에서는 여러 다른 형식의 배열 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/array.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| intOutput | Array |  [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

여러 배열을 결합하고 연결된 배열을 반환하거나 여러 문자열 값을 결합하고 연결된 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |연결을 위한 첫 번째 배열 또는 문자열입니다. |
| 추가 인수 |예 |배열 또는 문자열 |연결에 대 한 순서 대로 배열이 나 문자열이 더 많이 있습니다. |

이 함수는 인수를 개수에 관계없이 사용할 수 있으며 매개 변수에 대한 문자열이나 배열 중 하나를 사용할 수 있습니다. 그러나 매개 변수에 대한 배열과 문자열을 둘 다 제공할 수는 없습니다. 배열은 다른 배열과만 연결됩니다.

### <a name="return-value"></a>반환 값

연결된 값의 문자열 또는 배열입니다.

### <a name="example"></a>예제

다음 예제에서는 두 개의 배열을 결합하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

다음 예제에서는 2개의 문자열 값을 결합하고 연결된 문자열을 반환하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

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

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

매개 변수에서 배열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| args |아니요 |문자열, 정수, 배열 또는 개체 |배열의 값입니다. |

### <a name="return-value"></a>반환 값

배열입니다. 제공된 매개 변수가 없는 경우 빈 배열을 반환합니다.

### <a name="example"></a>예제

다음 예제에서는 여러 다른 형식의 createArray를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/createarray.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

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

## <a name="first"></a>first

`first(arg1)`

배열의 첫 번째 요소 또는 문자열의 첫 번째 문자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |첫 번째 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 첫 번째 요소 또는 문자열의 첫 번째 문자에 대한 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 문자열에 첫 번째 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>교집합

`intersection(arg1, arg2, arg3, ...)`

매개 변수에서 공통 요소를 갖는 단일 배열 또는 개체를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 첫 번째 값입니다. |
| arg2 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 두 번째 값입니다. |
| 추가 인수 |예 |배열 또는 개체 |공통 요소를 찾는 데 사용할 추가 값입니다. |

### <a name="return-value"></a>반환 값

공통 요소가 있는 배열 또는 개체입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 개체에 교차를 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

배열의 마지막 요소 또는 문자열의 마지막 문자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |마지막 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 마지막 요소 또는 문자열의 마지막 문자에 대한 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 문자열에 마지막 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

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

다음 예제에서는 배열 및 문자열에 length를 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

배열과 함께 이 함수를 사용하면 리소스를 만들 때 반복 횟수를 지정할 수 있습니다. 다음 예제에서 매개 변수 **siteNames** 는 웹 사이트를 만들 때 사용할 이름 배열을 나타냅니다.

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

배열에서 이 함수를 사용하는 방법에 대한 자세한 내용은 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.

## <a name="max"></a>max

`max(arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최대값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최대값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

최대값을 나타내는 int입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 정수 목록과 함께 max를 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>분

`min(arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최소값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최소값을 가져올 컬렉션입니다. |

### <a name="return-value"></a>반환 값

최소값을 나타내는 int입니다.

### <a name="example"></a>예제

다음 예제에서는 배열 및 정수 목록과 함께 min을 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

시작 정수 및 항목의 수를 포함하는 정수 배열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| startIndex |예 |int |배열에서 첫 번째 정수입니다. startIndex 및 count의 합계는 2,147,483,647 이하여야 합니다. |
| count |예 |int |배열에 있는 정수의 수입니다. 음수가 아닌 정수(최대 10.000)여야 합니다. |

### <a name="return-value"></a>반환 값

정수 배열입니다.

### <a name="example"></a>예제

다음 예제에서는 range 함수를 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/range.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

배열에서 지정된 숫자 이후의 모든 요소를 포함하는 배열을 반환하거나 문자열에서 지정된 숫자 이후의 모든 숫자를 포함하는 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |건너뛰는 데 사용할 배열 또는 문자열입니다. |
| numberToSkip |예 |int |건너뛸 요소 또는 문자 수입니다. 이 값이 0 이하이면 값의 모든 요소 또는 문자가 반환됩니다. 이 값이 배열 또는 문자열의 길이보다 크면 빈 배열 또는 문자열이 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="example"></a>예제

다음 예제에서는 배열에서 지정된 요소 수 및 문자열에서 지정된 수의 문자를 건너뜁니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

배열 또는 문자열을 반환 합니다. 배열의 시작 부분부터 지정 된 수의 요소를 포함 합니다. 문자열은 문자열의 시작 부분부터 지정 된 수 만큼의 문자를 포함 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |요소를 가져올 배열 또는 문자열입니다. |
| numberToTake |예 |int |수락할 요소 또는 문자의 수입니다. 이 값이 0 이하이면 빈 배열 또는 문자열이 반환됩니다. 지정된 배열 또는 문자열의 길이보다 크면 배열 또는 문자열의 모든 요소가 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="example"></a>예제

다음 예제에서는 배열에서 지정된 수의 요소 및 문자열의 문자를 가져옵니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | On |

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

다음 예제에서는 배열 및 개체에 union을 사용 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
