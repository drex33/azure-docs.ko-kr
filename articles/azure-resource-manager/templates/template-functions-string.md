---
title: 템플릿 함수 - 문자열
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 문자열 작업을 수행하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: bfb80a03012f5a1a9194789a82efd5cccd1eb18d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806998"
---
# <a name="string-functions-for-arm-templates"></a>ARM 템플릿의 문자열 함수

Resource Manager는 ARM 템플릿(Azure Resource Manager 템플릿)에서 문자열 작업을 수행하는 데 사용할 수 있도록 아래의 함수를 제공합니다.

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [json](#json)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

입력 문자열의 base64 표현을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |예 |문자열 |base64 표현으로 반환할 값입니다. |

### <a name="return-value"></a>반환 값

Base64 표현을 포함하는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 `base64` 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {“one”: “a”, “two”: “b”} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

base64 표현을 JSON 개체로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |예 |문자열 |JSON 개체로 변환할 base64 표현입니다. |

### <a name="return-value"></a>반환 값

JSON 개체입니다.

### <a name="examples"></a>예

다음 예제에서는 `base64ToJson` 함수를 사용하여 base64 값을 변환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {“one”: “a”, “two”: “b”} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

base64 표현을 문자열로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |예 |문자열 |문자열로 변환할 base64 표현입니다. |

### <a name="return-value"></a>반환 값

변환된 base64 값의 문자열입니다.

### <a name="examples"></a>예

다음 예에서는 함수를 사용 하 여 `base64ToString` base64 값을 변환 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/base64.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {“one”: “a”, “two”: “b”} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

여러 문자열 값을 결합하고 연결된 문자열을 반환하거나 여러 배열을 결합하고 연결된 배열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 배열 |연결할 첫 번째 문자열 또는 배열입니다. |
| 추가 인수 |예 |문자열 또는 배열 |연결에 대 한 순서에 따라 더 많은 문자열 또는 배열 |

이 함수는 인수를 개수에 관계없이 사용할 수 있으며 매개 변수에 대한 문자열이나 배열 중 하나를 사용할 수 있습니다. 그러나 매개 변수에 대한 배열과 문자열을 둘 다 제공할 수는 없습니다. 문자열은 다른 문자열과만 연결됩니다.

### <a name="return-value"></a>반환 값

연결된 값의 문자열 또는 배열입니다.

### <a name="examples"></a>예

다음 예제에서는 2개의 문자열 값을 결합하고 연결된 문자열을 반환하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

다음 예제에서는 두 개의 배열을 결합하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>포함

`contains (container, itemToFind)`

배열에 값이 포함되는지, 개체에 키가 포함되는지 또는 문자열에 하위 문자열이 포함되는지를 확인합니다. 문자열 비교에서는 대/소문자를 구분합니다. 그러나 개체에 키가 포함되어 있는지를 테스트할 때는 비교에서 대/소문자를 구분하지 않습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| container |예 |배열, 개체 또는 문자열 |찾을 값을 포함하는 값입니다. |
| itemToFind |예 |문자열 또는 int |찾을 값입니다. |

### <a name="return-value"></a>반환 값

항목이 있으면 **True** 이고, 항목이 없으면 **False** 입니다.

### <a name="examples"></a>예

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

값을 데이터 URI로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |예 |문자열 |데이터 URI로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

데이터 URI로 형식이 지정된 문자열입니다.

### <a name="examples"></a>예

다음 예에서는 값을 데이터 URI로 변환 하 고 데이터 URI를 문자열로 변환 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/datauri.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

데이터 URI로 형식이 지정된 값을 문자열로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |예 |문자열 |변환할 데이터 URI 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값을 포함하는 문자열입니다.

### <a name="examples"></a>예

다음 예제 템플릿에서는 값을 데이터 URI로 변환 하 고 데이터 URI를 문자열로 변환 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/datauri.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>비어 있음

`empty(itemToTest)`

배열, 개체 또는 문자열이 비어 있는지를 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| itemToTest |예 |배열, 개체 또는 문자열 |비어 있는지 확인할 값입니다. |

### <a name="return-value"></a>반환 값

값이 비어 있으면 **True** 를 반환하고 비어 있지 않으면 **False** 를 반환합니다.

### <a name="examples"></a>예

다음 예제에서는 배열, 개체 및 문자열이 비어 있는지 여부를 확인합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

문자열이 값으로 끝나는지 여부를 결정합니다. 비교는 대/소문자를 구분합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |예 |문자열 |찾을 값을 포함하는 값입니다. |
| stringToFind |예 |문자열 |찾을 값입니다. |

### <a name="return-value"></a>반환 값

마지막 문자 또는 문자열의 문자가 값과 일치하면 **True** 이고, 일치하지 않으면 **False** 입니다.

### <a name="examples"></a>예

다음 예제에서는 및 함수를 사용 하는 방법을 보여 줍니다 `startsWith` `endsWith` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/startsendswith.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

문자열의 첫 번째 문자 또는 배열의 첫 번째 요소를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |첫 번째 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 첫 번째 문자의 문자열 또는 첫 번째 요소의 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="examples"></a>예

다음 예제에서는 배열 및 문자열에 첫 번째 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

입력 값에서 형식이 지정된 문자열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| formatString | 예 | 문자열 | 합성 형식 문자열입니다. |
| arg1 | 예 | 문자열, 정수 또는 부울 | 서식이 지정된 문자열에 포함할 값입니다. |
| 추가 인수 | 예 | 문자열, 정수 또는 부울 | 형식이 지정 된 문자열에 포함할 값이 더 있습니다. |

### <a name="remarks"></a>설명

템플릿에서 문자열의 서식을 지정하려면 이 함수를 사용합니다. .NET의 [System.String.Format](/dotnet/api/system.string.format) 메서드와 동일한 서식 옵션을 사용합니다.

### <a name="examples"></a>예

다음 예제에서는 서식 함수를 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/format.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| formatTest | String | Hello, User. 형식이 지정된 숫자: 8,175,133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

매개 변수로 제공된 값을 기반으로 고유 식별자 형식의 값을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |예 |문자열 |GUID를 만들기 위해 해시 함수에 사용되는 값입니다. |
| 필요에 따라 더 많은 매개 변수 |예 |문자열 |고유성 수준을 지정하는 값을 만들기 위해 필요한 만큼 문자열을 추가할 수 있습니다. |

### <a name="remarks"></a>설명

이 함수는 고유 식별자 형식의 값을 만들어야 할 때 유용합니다. 결과의 고유성 범위를 제한하는 매개 변수 값을 제공합니다. 구독, 리소스 그룹 또는 배포까지 해당 이름이 고유한지 여부를 지정할 수 있습니다.

반환된 값은 임의 문자열이 아닌 매개 변수의 해시 함수 결과입니다. 반환된 값은 36자입니다. 전역적으로 고유하지 않습니다. 매개 변수의 해당 해시 값을 기준으로 하지 않는 새 GUID를 만들려면 [newGuid](#newguid) 함수를 사용합니다.

다음 예제에서는 guid를 사용하여 일반적으로 사용하는 수준에 대해 고유한 값을 만드는 방법을 보여 줍니다.

구독에 범위가 지정된 고유함

```json
"[guid(subscription().subscriptionId)]"
```

리소스 그룹에 범위가 지정된 고유함

```json
"[guid(resourceGroup().id)]"
```

리소스 그룹의 배포에 범위가 지정된 고유함

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>반환 값

고유 식별자 형식의 문자 36자를 포함하고 있는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 에서 결과를 `guid` 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/guid.json":::

## <a name="indexof"></a>indexof

`indexOf(stringToSearch, stringToFind)`

문자열 내 값의 첫 번째 위치를 반환합니다. 비교는 대/소문자를 구분합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |예 |문자열 |찾을 값을 포함하는 값입니다. |
| stringToFind |예 |문자열 |찾을 값입니다. |

### <a name="return-value"></a>반환 값

찾을 항목의 위치를 나타내는 정수입니다. 값은 0부터 시작합니다. 항목을 찾을 수 없으면 -1이 반환됩니다.

### <a name="examples"></a>예

다음 예제에서는 및 함수를 사용하는 방법을 `indexOf` `lastIndexOf` 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/indexof.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json :

`json(arg1)`

유효한 JSON 문자열을 JSON 데이터 형식으로 변환합니다. 자세한 내용은 [json 함수](template-functions-object.md#json)를 참조하세요.

## <a name="last"></a>last

`last (arg1)`

문자열의 마지막 문자 또는 배열의 마지막 요소를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열 또는 문자열 |마지막 요소 또는 문자를 검색할 값입니다. |

### <a name="return-value"></a>반환 값

배열의 마지막 문자의 문자열 또는 마지막 요소의 형식(문자열, int, 배열 또는 개체)입니다.

### <a name="examples"></a>예

다음 예제에서는 배열 및 문자열과 함께 함수를 사용하는 방법을 보여 `last` 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastindexof

`lastIndexOf(stringToSearch, stringToFind)`

문자열 내 값의 마지막 위치를 반환합니다. 비교는 대/소문자를 구분합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |예 |문자열 |찾을 값을 포함하는 값입니다. |
| stringToFind |예 |문자열 |찾을 값입니다. |

### <a name="return-value"></a>반환 값

찾을 항목의 마지막 위치를 나타내는 정수입니다. 값은 0부터 시작합니다. 항목을 찾을 수 없으면 -1이 반환됩니다.

### <a name="examples"></a>예

다음 예제에서는 및 함수를 사용하는 방법을 `indexOf` `lastIndexOf` 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/indexof.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

문자열의 문자 수, 배열의 요소 수 또는 개체의 루트 수준 속성 수를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |배열, 문자열 또는 개체 |요소의 수를 가져오는 데 사용할 배열, 문자 수를 가져오는 데 사용할 문자열 또는 루트 수준의 속성 수를 가져오려는 데 사용할 개체입니다. |

### <a name="return-value"></a>반환 값

int입니다.

### <a name="examples"></a>예

다음 예제에서는 `length` 배열 및 문자열과 함께 함수를 사용하는 방법을 보여 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

전역적으로 고유한 식별자의 형식으로 값을 반환합니다. **이 함수는 매개 변수의 기본값에만 사용할 수 있습니다.**

### <a name="remarks"></a>설명

이 함수는 매개 변수의 기본값에 대한 언어 식 내에서만 사용할 수 있습니다. 템플릿의 다른 위치에서 사용하면 오류가 반환됩니다. 호출될 때마다 다른 값을 반환하는 함수이므로 템플릿의 다른 부분에서는 허용되지 않습니다. 동일한 매개 변수를 사용하여 동일한 템플릿을 배포하면 동일한 결과가 안정적으로 생성되지 않습니다.

newGuid 함수는 매개 변수를 사용하지 않으므로 [guid](#guid) 함수와 다릅니다. 동일한 매개 변수를 사용하여 guid를 호출하면 매번 동일한 식별자를 반환합니다. 특정 환경에 대해 동일한 GUID를 안정적으로 생성해야 하는 경우 guid를 사용합니다. 테스트 환경에 리소스를 배포하는 것과 같이 매번 다른 식별자가 필요한 경우 newGuid를 사용합니다.

newGuid 함수는 .NET Framework의 [Guid 구조](/dotnet/api/system.guid)를 사용하여 전역적으로 고유한 식별자를 생성합니다.

[이전에 성공한 배포를 다시 배포하는 옵션](rollback-on-error.md)을 사용하고 이전 배포에 newGuid를 사용하는 매개 변수가 포함된 경우 해당 매개 변수는 재평가되지 않습니다. 대신 이전 배포의 매개 변수 값이 롤백 배포에서 자동으로 다시 사용됩니다.

테스트 환경에서는 짧은 시간 동안만 지속되는 리소스를 반복적으로 배포해야 할 수 있습니다. 고유 이름을 생성하는 대신 [uniqueString](#uniquestring)과 함께 newGuid를 사용하여 고유한 이름을 만들 수 있습니다.

기본값에 대한 newGuid 함수에 의존하는 템플릿은 주의를 기울여 재배포해야 합니다. 재배포할 때 매개 변수 값을 제공하지 않으면 함수는 재평가됩니다. 새 리소스를 만드는 대신 기존 리소스를 업데이트하려면 이전 배포의 매개 변수 값을 제공해야 합니다.

### <a name="return-value"></a>반환 값

고유 식별자 형식의 문자 36자를 포함하고 있는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 새 식별자를 포함하는 매개 변수를 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/newguid.json":::

이전 예제의 출력은 각 배포마다 다르지만 다음과 유사합니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| guidOutput | 문자열 | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

다음 예제에서는 `newGuid` 함수를 사용하여 스토리지 계정에 대한 고유한 이름을 만듭니다. 이 템플릿은 스토리지 계정이 짧은 시간 동안 존재하고 다시 배포되지 않는 테스트 환경에 사용할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/newguid-storageaccount.json":::

이전 예제의 출력은 각 배포마다 다르지만 다음과 유사합니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| nameOutput | 문자열 | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

지정된 총 길이에 도달할 때까지 왼쪽에 문자를 추가하여 오른쪽 맞추어진 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |예 |문자열 또는 int |오른쪽으로 맞출 값입니다. |
| totalLength |예 |int |반환된 문자열에서 문자의 총수입니다. |
| paddingCharacter |예 |단일 문자 |총 길이에 도달할 때까지 왼쪽 여백에 사용되는 문자입니다. 기본값은 공백입니다. |

원래 문자열이 채울 문자 수보다 긴 경우 문자가 추가되지 않습니다.

### <a name="return-value"></a>반환 값

최소한 지정된 문자의 수를 포함하는 문자열입니다.

### <a name="examples"></a>예

다음 예제는 문자열이 총 문자 수에 도달할 때까지 0 문자를 추가하여 사용자가 제공한 매개 변수 값을 채우는 방법을 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/padleft.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

다른 문자열로 대체한 어떤 문자열의 인스턴스를 포함한 새 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| originalString |예 |문자열 |다른 문자열로 대체한 어떤 문자열의 인스턴스를 포함하는 값입니다. |
| oldString |예 |문자열 |원래 문자열에서 제거할 문자열입니다. |
| newString |예 |문자열 |제거된 문자열 대신 추가할 문자열입니다. |

### <a name="return-value"></a>반환 값

문자가 대체된 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 사용자가 제공한 문자열에서 모든 대시를 제거하는 방법 및 문자열의 일부를 다른 문자열로 대체하는 방법을 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/replace.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

지정된 문자 수 이후의 모든 문자를 포함하는 문자열 또는 지정된 요소 수 이후의 모든 요소를 포함하는 배열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |건너뛰는 데 사용할 배열 또는 문자열입니다. |
| numberToSkip |예 |int |건너뛸 요소 또는 문자 수입니다. 이 값이 0 이하이면 값의 모든 요소 또는 문자가 반환됩니다. 이 값이 배열 또는 문자열의 길이보다 크면 빈 배열 또는 문자열이 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 배열에서 지정된 요소 수 및 문자열에서 지정된 수의 문자를 건너뜁니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>분할

`split(inputString, delimiter)`

지정된 구분 기호로 구분되는 입력 문자열의 부분 문자열을 포함하는 문자열의 배열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |예 |문자열 |분할할 문자열입니다. |
| 구분 기호 |예 |문자열 또는 문자열 배열 |문자열 분할에 사용할 구분 기호입니다. |

### <a name="return-value"></a>반환 값

문자열 배열입니다.

### <a name="examples"></a>예

다음 예에서는 쉼표와 쉼표 또는 세미콜론을 사용 하 여 입력 문자열을 분할 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/split.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| firstOutput | Array | [“one”, “two”, “three”] |
| secondOutput | Array | [“one”, “two”, “three”] |

## <a name="startswith"></a>startswith

`startsWith(stringToSearch, stringToFind)`

문자열이 값으로 시작하는지 여부를 결정합니다. 비교는 대/소문자를 구분합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |예 |문자열 |찾을 값을 포함하는 값입니다. |
| stringToFind |예 |문자열 |찾을 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 문자 또는 문자열의 문자가 값과 일치하면 **True** 이고, 일치하지 않으면 **False** 입니다.

### <a name="examples"></a>예

다음 예제에서는 및 함수를 사용 하는 방법을 보여 줍니다 `startsWith` `endsWith` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/startsendswith.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>문자열

`string(valueToConvert)`

지정한 값을 문자열로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 | 모두 |문자열로 변환할 값입니다. 개체 및 배열을 비롯하여 모든 값 형식을 변환할 수 있습니다. |

### <a name="return-value"></a>반환 값

변환된 값의 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 다양 한 형식의 값을 문자열로 변환 하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/string.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| objectOutput | String | {“valueA”:10,“valueB”:“Example Text”} |
| arrayOutput | String | [“a”,“b”,“c”] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

지정된 문자 위치에서 시작하고 지정한 개수의 문자를 포함하는 부분 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToParse |예 |문자열 |부분 문자열을 추출할 원래 문자열입니다. |
| startIndex |예 |int |부분 문자열의 0부터 시작하는 문자 위치입니다. |
| length |예 |int |부분 문자열에 대한 문자 수입니다. 문자열 내 위치를 참조해야 합니다. 0 이상이어야 합니다. |

### <a name="return-value"></a>반환 값

하위 문자열입니다. 또는 길이가 0인 경우 빈 문자열입니다.

### <a name="remarks"></a>설명

함수는 부분 문자열이 문자열의 끝을 넘어 확장되거나 길이가 0보다 작은 경우 실패합니다. 다음 예제는 "인덱스 및 길이 매개 변수는 문자열 내 위치를 참조해야 합니다. 인덱스 매개 변수: '0', 길이 매개 변수: '11', 문자열 매개 변수의 길이: '10'." 오류와 함께 실패합니다.

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>예

다음 예제에서는 매개 변수에서 하위 문자열을 추출합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/substring.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

배열 또는 문자열을 반환합니다. 배열에는 배열의 시작부터 지정된 개수의 요소가 있습니다. 문자열에는 문자열의 시작부터 지정된 수의 문자가 있습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |예 |배열 또는 문자열 |요소를 가져올 배열 또는 문자열입니다. |
| numberToTake |예 |int |수락할 요소 또는 문자의 수입니다. 이 값이 0 이하이면 빈 배열 또는 문자열이 반환됩니다. 지정된 배열 또는 문자열의 길이보다 크면 배열 또는 문자열의 모든 요소가 반환됩니다. |

### <a name="return-value"></a>반환 값

배열 또는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 배열에서 지정된 수의 요소 및 문자열의 문자를 가져옵니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | On |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

지정된 문자열을 소문자로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |예 |문자열 |소문자로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

소문자로 변환된 문자열입니다.

### <a name="examples"></a>예

다음 예제는 매개 변수 값을 소문자 및 대문자로 변환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/tolower.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

지정된 문자열을 대문자로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |예 |문자열 |대문자로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

대문자로 변환된 문자열입니다.

### <a name="examples"></a>예

다음 예제는 매개 변수 값을 소문자 및 대문자로 변환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/tolower.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

지정된 문자열에서 모든 선행 및 후행 공백 문자를 제거합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |예 |문자열 |자를 값입니다. |

### <a name="return-value"></a>반환 값

선행 및 후행 공백 문자가 없는 문자열입니다.

### <a name="examples"></a>예

다음은 매개 변수에서 공백 문자를 자르는 예입니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/trim.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

매개 변수로 제공된 값을 기반으로 결정 해시 문자열을 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |예 |문자열 |고유한 문자열을 만들기 위해 해시 함수에서 사용되는 값입니다. |
| 필요에 따라 더 많은 매개 변수 |예 |문자열 |고유성 수준을 지정하는 값을 만들기 위해 필요한 만큼 문자열을 추가할 수 있습니다. |

### <a name="remarks"></a>설명

이 함수는 리소스의 고유한 이름을 만들어야 할 때 유용합니다. 결과의 고유성 범위를 제한하는 매개 변수 값을 제공합니다. 구독, 리소스 그룹 또는 배포까지 해당 이름이 고유한지 여부를 지정할 수 있습니다.

반환된 값은 임의 문자열이 아닌 해시 함수의 결과입니다. 반환된 값은 13자입니다. 전역적으로 고유하지 않습니다. 의미있는 이름을 만들기 위해 해당 값과 명명 규칙의 접두사를 결합할 수도 있습니다. 다음 예제에서는 반환된 값의 형식을 보여 줍니다. 실제 값은 제공된 매개 변수에 따라 달라집니다.

`tcvhiyu5h2o5o`

다음 예제에서는 `uniqueString`을 사용하여 일반적으로 사용하는 수준에 대해 고유한 값을 만드는 방법을 보여줍니다.

구독에 범위가 지정된 고유함

```json
"[uniqueString(subscription().subscriptionId)]"
```

리소스 그룹에 범위가 지정된 고유함

```json
"[uniqueString(resourceGroup().id)]"
```

리소스 그룹의 배포에 범위가 지정된 고유함

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

다음 예제에서는 리소스 그룹에 따라 스토리지 계정에 고유한 이름을 만드는 방법을 보여 줍니다. 리소스 그룹의 내부에서 같은 방식으로 생성된 경우 이름은 고유하지 않습니다.

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

템플릿을 배포할 때마다 고유한 이름을 새로 만들어야 하고 리소스를 업데이트 하지 않으려는 경우 [에서 ](template-functions-date.md#utcnow)utcNow`uniqueString` 함수를 사용할 수 있습니다. 이 방법은 테스트 환경에서 사용할 수 있습니다. 예제는 [utcNow](template-functions-date.md#utcnow)를 참조하세요.

### <a name="return-value"></a>반환 값

13개의 문자를 포함하는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 에서 결과를 `uniquestring` 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uniquestring.json":::

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

baseUri와 relativeUri 문자열을 결합하여 절대 URI를 만듭니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| baseUri |예 |문자열 |기본 uri 문자열입니다. 이 표에 설명된 대로 후행 슬래시()의 처리에 대한 동작을 `/` 관찰합니다.  |
| relativeUri |예 |문자열 |기본 uri 문자열에 추가할 상대 uri 문자열입니다. |

* **baseUri가** 후행 슬래시로 끝나는 경우 결과는 **baseUri** 다음에 **relativeUri** 가 입니다.

* **baseUri가** 후행 슬래시로 끝나지 않으면 두 가지 중 하나가 발생합니다.

   * **baseUri에** 슬래시가 전혀 없는 `//` 경우(앞부분에 있는 것 외에) 결과는 baseUri 다음에 **relativeUri** 가 입니다. 

   * **baseUri** 에 슬래시가 있지만 슬래시로 끝나지 않는 경우 마지막 슬래시부터 나오는 모든 항목은 **baseuri** 에서 제거되고 결과로 **baseUri** 다음에 **relativeUri** 가 나옵니다.

다음은 몇 가지 예입니다.

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```

전체 세부 정보를 보려면 [RFC 3986, 섹션 5](https://tools.ietf.org/html/rfc3986#section-5)에 지정된 대로 **baseUri** 및 **relativeUri** 매개 변수를 확인합니다.

### <a name="return-value"></a>반환 값

기본 및 상대 값에 대한 절대 URI를 나타내는 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 부모 템플릿의 값을 기반으로 중첩된 템플릿에 대한 링크를 생성하는 방법을 보여 줍니다.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

다음 예제 템플릿에서는 , 및 를 사용하는 방법을 `uri` `uriComponent` `uriComponentToString` 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI를 인코딩합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |예 |문자열 |인코딩할 값입니다. |

### <a name="return-value"></a>반환 값

URI로 인코딩된 값의 문자열입니다.

### <a name="examples"></a>예

다음 예제 템플릿에서는 , 및 를 사용하는 방법을 `uri` `uriComponent` `uriComponentToString` 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

URI로 인코딩된 값의 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |예 |문자열 |문자열로 변환할 URI 인코딩 값입니다. |

### <a name="return-value"></a>반환 값

URI로 인코딩된 값의 디코딩된 문자열입니다.

### <a name="examples"></a>예

다음 예제에서는 , 및 를 사용하는 방법을 `uri` `uriComponent` `uriComponentToString` 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/uri.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
* 여러 템플릿을 병합하려면 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](linked-templates.md)을 참조하세요.
* 리소스 형식을 만들 때 지정된 횟수만큼 반복하려면 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
