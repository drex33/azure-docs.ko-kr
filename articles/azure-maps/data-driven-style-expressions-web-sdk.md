---
title: Azure Maps 웹 SDK의 데이터 기반 스타일 식 | Microsoft Azure Maps
description: 데이터 기반 스타일 식에 대해 알아봅니다. Azure Maps 웹 SDK에서 이러한 식을 사용하여 지도의 스타일을 조정하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 7875184456d03e08abb6168793fc9021bd953a0d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438519"
---
# <a name="data-driven-style-expressions-web-sdk"></a>데이터 기반 스타일 식(웹 SDK)

식을 사용하면 데이터 원본의 각 도형에 정의된 속성을 관찰하는 스타일 지정 옵션에 비즈니스 논리를 적용할 수 있습니다. 식은 데이터 원본 또는 계층의 데이터를 필터링할 수 있습니다. 식은 if문과 같은 조건부 논리로 구성될 수 있습니다. 또한 문자열 연산자, 논리 연산자, 수학 연산자를 사용하여 데이터를 조작하는 데 사용할 수 있습니다.

데이터 기반 스타일은 스타일 지정에 대한 비즈니스 논리를 구현하는 데 필요한 코드의 양을 줄여 줍니다. 계층과 사용되는 경우 별도의 스레드에서 렌더링 시 식이 평가됩니다. 이 기능은 UI 스레드에서 비즈니스 논리를 평가하는 것과 비교하여 향상된 성능을 제공합니다.

이 비디오는 Azure Maps 웹 SDK의 데이터 기반 스타일 지정에 대한 개요를 제공합니다.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

식은 JSON 배열로 표시됩니다. 배열에서 식의 첫 번째 요소는 식 연산자의 이름을 지정하는 문자열입니다. 예를 들면 "+" 또는 "case"입니다. 다음 요소(있는 경우)는 식에 대한 인수입니다. 각 인수는 리터럴 값(문자열, 숫자, 부울 또는 `null`)이거나 다른 식 배열입니다. 다음 의사 코드에서는 식의 기본 구조를 정의합니다.

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps 웹 SDK는 다양한 형식의 식을 지원합니다. 식은 자체적으로 사용되거나 다른 식과 함께 사용될 수 있습니다.

| 식의 형식 | Description |
|---------------------|-------------|
| [집계 식](#aggregate-expression) | 데이터 세트에 대해 처리되고 `DataSource`의 옵션 `clusterProperties`와 함께 사용할 수 있는 계산을 정의하는 식입니다. |
| [부울 식](#boolean-expressions) | 부울 식은 부울 비교를 평가하기 위한 부울 연산자 식 집합을 제공합니다. |
| [색 식](#color-expressions) | 색 식을 사용하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다. |
| [조건부 식](#conditional-expressions) | 조건부 식은 if 문과 같은 논리 연산을 제공합니다. |
| [데이터 식](#data-expressions) | 기능의 속성 데이터에 대한 액세스를 제공합니다. |
| [보간 및 단계 식](#interpolate-and-step-expressions) | 보간 및 단계 식은 보간 곡선이나 단계 함수를 따라 값을 계산하는 데 사용할 수 있습니다. |
| [계층별 식](#layer-specific-expressions) | 단일 계층에만 적용되는 특수 식입니다. |
| [수학 식](#math-expressions) | 식 프레임워크 내에서 데이터 기반 계산을 수행하는 수치 연산자를 제공합니다. |
| [문자열 연산자 식](#string-operator-expressions) | 문자열 연산자 식은 사례를 연결하고 변환하는 등의 문자열에 대해 변환 작업을 수행합니다. |
| [Type 식](#type-expressions) | 형식 식은 문자열, 숫자, 부울 값 등의 다양한 데이터 형식을 테스트하고 변환하는 도구를 제공합니다. |
| [변수 바인딩 식](#variable-binding-expressions) | 변수 바인딩 식은 계산 결과를 변수에 저장하고 저장된 값을 다시 계산할 필요 없이 식의 다른 위치에서 여러 번 참조합니다. |
| [확대/축소 식](#zoom-expression) | 렌더링 시 지도의 현재 확대/축소 수준을 검색합니다. |

이 문서의 모든 예에서는 다음 기능을 사용하여 다양한 형식의 식을 사용할 수 있는 여러 가지 방법을 보여 줍니다.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>데이터 식

데이터 식은 기능의 속성 데이터에 대한 액세스를 제공합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['at', number, array]` | 값 | 배열에서 항목을 검색합니다. |
| `['geometry-type']` | 문자열 | 기능의 기하 도형 유형인 Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon을 가져옵니다. |
| `['get', string]` | 값 | 현재 기능의 속성에서 속성 값을 가져옵니다. 요청된 속성이 없는 경우 null을 반환합니다. |
| `['get', string, object]` | 값 | 제공된 개체의 속성에서 속성 값을 가져옵니다. 요청된 속성이 없는 경우 null을 반환합니다. |
| `['has', string]` | boolean | 기능의 속성에 지정된 속성이 있는지 여부를 확인합니다. |
| `['has', string, object]` | boolean | 개체의 속성에 지정된 속성이 있는지 여부를 확인합니다. |
| `['id']` | 값 | 기능 ID가 있는 경우 해당 ID를 가져옵니다. |
| `['in', boolean | string | number, array]` | boolean | 항목이 배열에 있는지 여부를 확인합니다. |
| `['in', substring, string]` | boolean | 문자열에 substring이 있는지 여부를 확인합니다. |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | number | 배열에서 항목을 찾을 수 있는 첫 번째 위치를 반환하거나 문자열에서 substring을 찾을 수 있는 위치를 반환하고, 입력을 찾을 수 없는 경우 `-1`을 반환합니다. 검색을 시작할 위치에서 선택적 인덱스를 허용합니다. |
| `['length', string | array]` | number | 문자열이나 배열의 길이를 가져옵니다. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | 문자열 \| 배열 | 지정된 시작 인덱스의 문자열에서 배열이나 substring의 항목을 반환하거나 설정된 경우 시작 인덱스와 마지막 인덱스 사이에서 항목을 반환합니다. 반환 값은 시작 인덱스는 포함하지만 끝 인덱스는 포함하지 않습니다. |

**예제**

`get` 식을 사용하여 식에서 직접 기능의 속성에 액세스할 수 있습니다. 이 예에서는 기능의 `zoneColor` 값을 사용하여 거품형 계층의 색 속성을 지정합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

모든 point 기능에 `zoneColor` 속성이 있다면 위의 예는 제대로 작동합니다. 그렇지 않으면 색이 "black"으로 대체될 가능성이 높습니다. 대체 색을 수정하려면 `case` 식을 `has` 식과 함께 사용하여 속성이 있는지 확인합니다. 속성이 없으면 대체 색을 반환합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

거품 및 기호 계층은 기본적으로 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 이 동작은 다각형의 꼭짓점 또는 선을 강조 표시할 수 있습니다. 계층의 `filter` 옵션을 사용하면 부울 식 내에서 `['geometry-type']` 식을 사용하여 렌더링하는 기능의 기하 도형 유형을 제한할 수 있습니다. 다음 예에서는 `Point` 기능만 렌더링되도록 거품형 계층을 제한합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

다음 예에서는 `Point` 및 `MultiPoint` 기능을 모두 렌더링할 수 있습니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

마찬가지로 다각형의 윤곽선이 선 계층에서 렌더링됩니다. 선 계층에서 이 동작을 사용하지 않도록 설정하려면 `LineString` 및 `MultiLineString` 기능만 허용하는 필터를 추가합니다.  

다음은 데이터 식을 사용하는 방법에 대한 몇 가지 추가 예입니다.

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>수학 식

수학 식은 식 프레임워크 내에서 데이터 기반 계산을 수행하는 수치 연산자를 제공합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 지정된 숫자의 총량을 계산합니다. |
| `['-', number]` | number | 0에서 지정된 숫자만큼 뺍니다. |
| `['-', number, number]` | number | 첫 번째 숫자에서 두 번째 숫자만큼 뺍니다. |
| `['*', number, number, …]` | number | 지정된 숫자를 서로 곱합니다. |
| `['/', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눕니다. |
| `['%', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눈 나머지를 계산합니다. |
| `['^', number, number]` | number | 두 번째 숫자로 거듭제곱한 첫 번째 값을 계산합니다. |
| `['abs', number]` | number | 지정된 숫자의 절대 값을 계산합니다. |
| `['acos', number]` | number | 지정된 숫자의 아크코사인을 계산합니다. |
| `['asin', number]` | number | 지정된 숫자의 아크사인을 계산합니다. |
| `['atan', number]` | number | 지정된 숫자의 아크탄젠트를 계산합니다. |
| `['ceil', number]` | number | 숫자를 다음 정수로 반올림합니다. |
| `['cos', number]` | number | 지정된 숫자의 코사인을 계산합니다. |
| `['e']` | number | 산술 상수 `e`를 반환합니다. |
| `['floor', number]` | number | 숫자를 이전 정수로 내림합니다. |
| `['ln', number]` | number | 지정된 숫자의 자연 로그를 계산합니다. |
| `['ln2']` | number | 산술 상수 `ln(2)`를 반환합니다. |
| `['log10', number]` | number | 지정된 숫자의 밑이 10인 로그를 계산합니다. |
| `['log2', number]` | number | 지정된 숫자의 밑이 2인 로그를 계산합니다. |
| `['max', number, number, …]` | number | 지정된 숫자 집합의 최대 수를 계산합니다. |
| `['min', number, number, …]` | number | 지정된 숫자 집합의 최소 수를 계산합니다. |
| `['pi']` | number | 산술 상수 `PI`를 반환합니다. |
| `['round', number]` | number | 가장 가까운 정수로 반올림합니다. 중간값은 0에서 멀어지는 쪽으로 반올림됩니다. 예를 들어 `['round', -1.5]`는 `-2`로 계산됩니다. |
| `['sin', number]` | number | 지정된 숫자의 사인을 계산합니다. |
| `['sqrt', number]` | number | 지정된 숫자의 제곱근을 계산합니다. |
| `['tan', number]` | number | 지정된 숫자의 탄젠트를 계산합니다. |

## <a name="aggregate-expression"></a>집계 식

집계 식은 데이터 세트에 대해 처리되고 `DataSource`의 옵션 `clusterProperties`와 함께 사용할 수 있는 계산을 정의합니다. 이러한 식의 출력은 숫자 또는 부울이어야 합니다.

집계 식은 데이터의 각 기능에서 속성을 검색하여 집계 연산을 적용할 수 있는 세 가지 값(연산자 값, 초기 값 및 식)을 사용합니다. 이 식은 다음 형식을 갖습니다.

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 연산자: 클러스터의 각 지점에 대해 `mapExpression`이 계산하는 모든 값에 대해 적용되는 식 함수입니다. 지원되는 연산자:
  - 숫자: `+`, `*`, `max`, `min`
  - 부울: `all`, `any`
- initialValue: 첫 번째 계산된 값이 집계되는 초기 값입니다.
- mapExpression: 데이터 세트의 각 지점에 대해 적용되는 식입니다.

**예제**

데이터 세트의 모든 기능에 숫자로 된 `revenue` 속성이 있는 경우 데이터 세트에서 만들어진 클러스터의 모든 점에 대한 총 수익을 계산할 수 있습니다. 이 계산은 다음 집계 식을 사용하여 수행됩니다: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>누적 식

`accumulated` 식은 지금까지 누적된 클러스터 속성 값을 가져옵니다. 클러스터형 `DataSource` 원본의 옵션 `clusterProperties`에서만 사용할 수 있습니다.

**사용 현황**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>부울 식

부울 식은 부울 비교를 평가하기 위한 부울 연산자 식 집합을 제공합니다.

값을 비교할 때 비교는 엄격하게 형식화됩니다. 다른 형식의 값은 항상 동일하지 않은 것으로 간주됩니다. 구문 분석 시 형식이 다른 것으로 알려진 사례는 잘못된 것으로 간주되며 구문 분석 오류를 생성합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | 논리 부정. 입력이 `false`이면 `true`를 반환하고, 입력이 `true`이면 `false`를 반환합니다. |
| `['!=', value, value]` | boolean | 입력 값이 같지 않으면 `true`이고, 그렇지 않으면 `false`를 반환합니다. |
| `['<', value, value]` | boolean | 첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 작으면 `true`를 반환하고, 그렇지 않으면 `false`를 반환합니다. 인수는 둘 다 문자열이거나 둘 다 숫자여야 합니다. |
| `['<=', value, value]` | boolean | 첫 번째 매개 변수가 두 번째 매개 변수보다 작거나 동일하면 `true`를 반환하고, 그렇지 않으면 `false`를 반환합니다. 인수는 둘 다 문자열이거나 둘 다 숫자여야 합니다. |
| `['==', value, value]` | boolean | 입력 값이 같으면 `true`이고, 그렇지 않으면 `false`를 반환합니다. 인수는 둘 다 문자열이거나 둘 다 숫자여야 합니다. |
| `['>', value, value]` | boolean | 첫 번째 매개 변수가 두 번째 매개 변수보다 엄격하게 크면 `true`를 반환하고, 그렇지 않으면 `false`를 반환합니다. 인수는 둘 다 문자열이거나 둘 다 숫자여야 합니다. |
| `['>=' value, value]` | boolean | 첫 번째 매개 변수가 두 번째 매개 변수보다 크거나 동일하면 `true`를 반환하고, 그렇지 않으면 `false`를 반환합니다. 인수는 둘 다 문자열이거나 둘 다 숫자여야 합니다. |
| `['all', boolean, boolean, …]` | boolean | 모든 입력이 `true`이면 `true`을 반환하고 그렇지 않으면 `false`를 반환합니다. |
| `['any', boolean, boolean, …]` | boolean | 입력이 `true`이면 `true`을 반환하고 그렇지 않으면 `false`를 반환합니다. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | 계산된 기능이 입력 기하 도형의 경계 내에 완전히 포함되면 `true`를 반환하고 그렇지 않으면 false를 반환합니다. 입력 값은 `Polygon`, `MultiPolygon`, `Feature` 또는 `FeatureCollection` 형식의 유효한 GeoJSON일 수 있습니다. 평가를 위해 지원되는 기능:<br/><br/>- Point: 포인트가 경계에 있거나 경계를 벗어나면 `false`를 반환합니다.<br/>- LineString: 선의 일부가 경계를 벗어나거나 선이 경계를 교차하거나 선의 엔드포인트가 경계에 있는 경우 `false`를 반환합니다. |

## <a name="conditional-expressions"></a>조건부 식

조건부 식은 if 문과 같은 논리 연산을 제공합니다.

다음 식은 입력 데이터에 대해 조건부 논리 연산을 수행합니다. 예를 들어 `case` 식은 "if/then/else" 논리를 제공하며 `match` 식은 "switch 문"과 유사합니다.

### <a name="case-expression"></a>Case 식

`case` 식은 "if/then/else" 논리를 제공하는 조건식의 유형입니다. 이 유형의 식은 부울 조건 목록을 통해 단계를 진행합니다. True로 평가할 첫 번째 부울 조건의 출력 값을 반환합니다.

다음 의사 코드에서는 `case` 식의 구조를 정의합니다.

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**예제**

다음 예에서는 `true`로 평가되고 연결된 값을 반환하는 부울 조건을 찾을 때까지 다양한 부울 조건을 단계별로 실행합니다. 부울 조건이 `true`로 계산되지 않으면 대체 값이 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Match 식

`match` 식은 논리와 같은 switch 문을 제공하는 조건식의 유형입니다. 입력은 문자열이나 숫자를 반환하는 `['get', 'entityType']` 등의 모든 식일 수 있습니다. 각 레이블은 값이 모든 문자열 또는 모든 숫자여야 하는 단일 리터럴 값 또는 리터럴 값의 배열이어야 합니다. 배열의 값이 일치하는 경우 입력이 일치합니다. 각 레이블은 고유해야 합니다. 입력 유형이 레이블의 유형과 일치하지 않는 경우 결과는 대체 값이 됩니다.

다음 의사 코드에서는 `match` 식의 구조를 정의합니다.

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**예제**

다음 예에서는 거품형 계층에서 Point 기능의 `entityType` 속성을 검색하여 일치 항목을 찾습니다. 일치 항목이 발견되면 지정된 값이 반환되거나 대체 값이 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

다음 예에서는 배열을 사용하여 모두 동일한 값을 반환해야 하는 레이블 집합을 나열합니다. 이 방법은 각 레이블을 개별적으로 나열하는 것보다 훨씬 효율적입니다. 이 경우 `entityType` 속성이 "restaurant" 또는 "grocery_store"이면 색 "red"가 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Coalesce 식

`coalesce` 식에서는 null이 아닌 첫 번째 값을 가져와 값을 반환할 때까지 식 집합을 단계별로 실행합니다.

다음 의사 코드에서는 `coalesce` 식의 구조를 정의합니다.

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**예제**

다음 예에서는 `coalesce` 식을 사용하여 기호 계층의 `textField` 옵션을 설정합니다. `title` 속성이 기능에서 누락되었거나 `null`로 설정된 경우에는 식이 `subTitle` 속성을 찾으려고 시도하고, 누락되었거나 `null`인 경우에는 빈 문자열로 대체합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

다음 예에서는 `coalesce` 식을 사용하여 지정된 이미지 이름 목록에서 지도 스프라이트에 사용할 수 있는 첫 번째 사용 가능한 이미지 아이콘을 검색합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Type 식

형식 식은 문자열, 숫자, 부울 값 등의 다양한 데이터 형식을 테스트하고 변환하는 도구를 제공합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Object[] | 입력이 배열임을 어설션합니다. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | 입력 값이 부울임을 어설션합니다. 여러 값이 제공되는 경우 각 값은 부울을 가져올 때까지 순서대로 평가됩니다. 부울 입력이 없으면 식에 오류가 발생합니다. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | 병합기 | 로캘 종속적인 비교 작업에 사용할 병합기를 반환합니다. 대/소문자 구분 및 분음 부호 구분 옵션은 기본적으로 false로 설정됩니다. 로캘 인수는 사용할 로캘의 IETF 언어 태그를 지정합니다. 제공되지 않으면 기본 로캘이 사용됩니다. 요청한 로캘을 사용할 수 없는 경우 병합기는 시스템 정의 대체 로캘을 사용합니다. 해결된 로캘을 사용하여 로캘 대체 동작의 결과를 테스트합니다. |
| `['literal', array]`<br/><br/>`['literal', object]` | 배열 \| 개체 | 리터럴 배열 또는 개체 값을 반환합니다. 배열이나 개체가 식으로 계산되지 않도록 하려면 이 식을 사용합니다. 배열 또는 개체를 식에서 반환해야 하는 경우 이 작업이 필요합니다. |
| `['image', string]` | 문자열 | 지정된 이미지 ID가 지도 이미지 스프라이트에 로드되는지 확인합니다. 로드된 경우 ID가 반환되고, 그렇지 않으면 null이 반환됩니다. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | number | 입력 값이 숫자임을 어설션합니다. 여러 값이 제공되는 경우 각 값은 숫자를 가져올 때까지 순서대로 평가됩니다. 숫자 입력이 없으면 식에 오류가 발생합니다. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Object | 입력 값이 개체임을 어설션합니다.  여러 값이 제공되는 경우 각 값은 개체를 가져올 때까지 순서대로 평가됩니다. 개체 입력이 없으면 식에 오류가 발생합니다. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | 문자열 | 입력 값이 문자열임을 어설션합니다. 여러 값이 제공되는 경우 각 값은 문자열을 가져올 때까지 순서대로 평가됩니다. 문자열 입력이 없으면 식에 오류가 발생합니다. |
| `['to-boolean', value]` | boolean | 입력 값을 부울로 변환합니다. 입력이 빈 문자열, `0`, `false`, `null` 또는 `NaN`인 경우 결과는 `false`이고, 그렇지 않으면 `true`입니다. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | 색 | 입력 값을 색으로 변환합니다. 여러 값이 제공되는 경우 각 값은 변환이 성공적으로 수행될 때까지 순서대로 평가됩니다. 입력을 하나도 변환할 수 없는 경우 식에 오류가 발생합니다. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 가능한 경우 입력 값을 숫자로 변환합니다. 입력이 `null` 또는 `false`이면 결과는 0입니다. 입력이 `true`이면 결과는 1입니다. 입력이 문자열이면 ECMAScript 언어 사양의 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) 문자열 함수를 사용하여 숫자로 변환됩니다. 여러 값이 제공되는 경우 각 값은 변환이 성공적으로 수행될 때까지 순서대로 평가됩니다. 입력을 하나도 변환할 수 없는 경우 식에 오류가 발생합니다. |
| `['to-string', value]` | 문자열 | 입력 값을 문자열로 변환합니다. 입력이 `null`이면 결과는 `""`입니다. 입력이 부울이면 결과는 `"true"` 또는 `"false"`입니다. 입력이 숫자이면 ECMAScript 언어 사양의 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) 숫자 함수를 사용하여 문자열로 변환됩니다. 입력이 색이면 CSS RGBA 색 문자열 `"rgba(r,g,b,a)"`로 변환됩니다. 그렇지 않으면 ECMAScript 언어 사양의 [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 함수를 사용하여 입력이 문자열로 변환됩니다. |
| `['typeof', value]` | 문자열 | 지정된 값의 형식을 설명하는 문자열을 반환합니다. |

> [!TIP]
> `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`과 유사한 오류 메시지가 브라우저 콘솔에 표시되는 경우 코드에 첫 번째 값에 대한 문자열이 없는 배열이 있는 식이 있음을 의미합니다. 식이 배열을 반환하도록 하려면 `literal` 식을 사용하여 배열을 래핑합니다. 다음 예에서는 `match` 식을 사용하여 point 기능의 `entityType` 속성 값을 기반으로 두 오프셋 값 중에서 선택하여 두 개의 숫자를 포함하는 배열이어야 하는 기호 계층의 `offset` 아이콘 옵션을 설정합니다.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>색 식

색 식을 사용하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | 색 | `0`과 `255` 사이 및 알파 구성 요소 `1`의 *빨강*, *녹색* 및 *파랑* 구성 요소에서 색 값을 만듭니다. 구성 요소가 범위를 벗어난 경우 식에 오류가 발생합니다. |
| `['rgba', number, number, number, number]` | 색 | `0`과 `255` 사이 및 `0`과 `1` 사이의 알파 구성 요소의 *빨간색*, *녹색* 및 *파란색* 구성 요소에서 색 값을 만듭니다. 구성 요소가 범위를 벗어난 경우 식에 오류가 발생합니다. |
| `['to-rgba']` | \[number, number, number, number\] | 입력 색의 *빨간색*, *녹색*, *파란색* 및 *알파* 구성 요소를 포함하는 4개 요소 배열을 해당 순서대로 반환합니다. |

**예제**

다음 예에서는 `255`의 *빨간색* 값과 `2.5`에 `temperature` 속성 값을 곱하여 계산되는 *녹색* 및 *파란색* 값을 가진 RGB 색 값을 만듭니다. 온도가 변경됨에 따라 색은 *빨간색* 의 다른 색조로 변경됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>문자열 연산자 식

문자열 연산자 식은 사례를 연결하고 변환하는 등의 문자열에 대해 변환 작업을 수행합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 문자열 | 여러 문자열을 한 문자열로 연결합니다. 각 값은 문자열이어야 합니다. 필요한 경우 `to-string` 형식 식을 사용하여 다른 값 형식을 문자열로 변환합니다. |
| `['downcase', string]` | 문자열 | 지정된 문자열을 소문자로 변환합니다. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | 입력 문자열이 현재 글꼴 스택에서 지원하는 문자 집합을 사용하는지 여부를 확인합니다. 예: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | 문자열 | 제공된 병합기에서 사용하는 로캘의 IETF 언어 태그를 반환합니다. 이는 기본 시스템 로캘을 확인하거나 요청된 로캘이 성공적으로 로드되었는지 여부를 확인하는 데 사용할 수 있습니다. |
| `['upcase', string]` | 문자열 | 지정된 문자열을 대문자로 변환합니다. |

**예제**

다음 예에서는 point 기능의 `temperature` 속성을 문자열로 변환하고 끝에 "°F"를 연결합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

위의 식은 아래 이미지에 표시된 것처럼 "64°F"라는 텍스트 맨 위에 겹쳐서 표시된 상태로 지도에 핀을 렌더링합니다.

![문자열 연산자 식 예](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>보간 및 단계 식

보간 및 단계 식은 보간 곡선이나 단계 함수를 따라 값을 계산하는 데 사용할 수 있습니다. 이러한 식은 숫자 값을 입력으로 반환하는 식(예: `['get',  'temperature']`)을 사용합니다. 입력 값은 입력 및 출력 값 쌍에 대해 평가되어 보간된 곡선이나 단계 함수에 가장 적합한 값을 결정합니다. 출력 값은 "stops"라고 합니다. 각 중지의 입력 값은 숫자여야 하며 오름차순이어야 합니다. 출력 값은 숫자, 숫자 배열 또는 색이어야 합니다.

### <a name="interpolate-expression"></a>Interpolate 식

`interpolate` 식은 중지 값 사이를 보간하여 연속적이고 부드러운 값 세트를 계산하는 데 사용할 수 있습니다. 색 값을 반환하는 `interpolate` 식은 결과 값이 선택되는 색 그라데이션을 생성합니다.

`interpolate` 식에 사용할 수 있는 보간 방법에는 다음 세 가지 유형이 있습니다.

- `['linear']` - stops 쌍 사이를 선형으로 보간합니다.
- `['exponential', base]` - stops 사이를 지수로 보간합니다. `base` 값은 출력이 늘어나는 속도를 제어합니다. 값이 높을수록 출력은 범위의 높은 쪽 끝에서 증가합니다. 1에 가까운 `base` 값은 보다 선형적으로 증가하는 출력을 생성합니다.
- `['cubic-bezier', x1, y1, x2, y2]` - 지정된 제어점에서 정의하는 [입방형 3차원 곡선](https://developer.mozilla.org/docs/Web/CSS/timing-function)을 사용하여 보간합니다.

다음은 이러한 여러 유형의 보간의 예입니다.

| 선형  | 지수 | 입방형 3차원 |
|---------|-------------|--------------|
| ![선형 보간 그래프](media/how-to-expressions/linear-interpolation.png) | ![지수 보간 그래프](media/how-to-expressions/exponential-interpolation.png) | ![입방형 3차원 보간 그래프](media/how-to-expressions/bezier-curve-interpolation.png) |

다음 의사 코드에서는 `interpolate` 식의 구조를 정의합니다.

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**예제**

다음 예에서는 `linear interpolate` 식을 사용하여 point 기능의 `temperature` 속성을 기반으로 거품형 계층의 `color` 속성을 설정합니다. `temperature` 값이 60보다 작은 경우 "blue"가 반환됩니다. 60에서 70 미만 사이의 값인 경우 "yellow"가 반환됩니다. 70에서 80 미만 사이의 값인 경우 "orange"가 반환됩니다. 80 이상인 경우 "red"가 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

다음 이미지는 위의 식에서 색을 선택하는 방법을 보여 줍니다.
 
![Interpolate 식 예](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Step 식

`step` 식은 stops로 정의된 [조각별 상수 함수](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)를 평가하여 불연속의 단계별 결과 값을 계산하는 데 사용할 수 있습니다.

다음 의사 코드에서는 `step` 식의 구조를 정의합니다.

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Step 식은 입력 값 바로 앞에 있는 stop의 출력 값 또는 입력이 첫 번째 stop보다 작은 경우 첫 번째 입력 값을 반환합니다.

**예제**

다음 예에서는 `step` 식을 사용하여 point 기능의 `temperature` 속성을 기반으로 거품형 계층의 `color` 속성을 설정합니다. `temperature` 값이 60보다 작은 경우 "blue"가 반환됩니다. 60에서 70 미만 사이의 값인 경우 "yellow"가 반환됩니다. 70에서 80 미만 사이의 값인 경우 "orange"가 반환됩니다. 80 이상인 경우 "red"가 반환됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

다음 이미지는 위의 식에서 색을 선택하는 방법을 보여 줍니다.

![Step 식 예](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>계층별 식

특정 계층에만 적용되는 특수 식입니다.

### <a name="heat-map-density-expression"></a>열 지도 밀도 식

열 지도 밀도 식은 열 지도 계층의 각 픽셀에 대한 열 지도 밀도 값을 검색하고 `['heatmap-density']`로 정의됩니다. 이 값은 `0`에서 `1` 사이의 숫자입니다. `interpolation` 또는 `step` 식과 함께 사용되어 열 지도의 색을 지정하는 데 사용되는 색 그라데이션을 정의합니다. 이 식은 열 지도 계층의 [색 옵션](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color)에만 사용할 수 있습니다.

> [!TIP]
> 보간 식에서 인덱스 0의 색상 또는 단계 색의 기본 색은 데이터가 없는 영역의 색을 정의합니다. 인덱스 0에 있는 색은 배경색을 정의하는 데 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

**예제**

이 예에서는 선형 보간 식을 사용하여 열 지도를 렌더링하기 위한 부드러운 색 그라데이션을 만듭니다.

```javascript
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

부드러운 그라데이션을 사용하여 열 지도를 색상화하는 것 외에도 `step` 식을 사용하여 범위 집합 내에서 색을 지정할 수 있습니다. 열 지도를 색상화하는 `step` 식을 사용하여 윤곽선 또는 레이더 스타일 지도와 유사한 범위로 밀도를 시각적으로 나눕니다.  

```javascript
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

자세한 내용은 [열 지도 계층 추가](map-add-heat-map-layer.md) 설명서를 참조하세요.

### <a name="line-progress-expression"></a>선 진행률 식

선 진행률 식은 선 계층에서 그라데이션 선을 따라 진행률을 검색하고 `['line-progress']`로 정의됩니다. 이 값은 0에서 1 사이의 숫자입니다. `interpolation` 또는 `step` 식과 함께 사용됩니다. 이 식은 선 계층의 [strokeGradient 옵션](/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient)에만 사용할 수 있습니다.

> [!NOTE]
> 선 계층의 `strokeGradient` 옵션을 사용하려면 데이터 원본의 `lineMetrics` 옵션을 `true`로 설정해야 합니다.

**예제**

이 예에서는 `['line-progress']` 식을 사용하여 선의 스트로크에 색 그라데이션을 적용합니다.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[라이브 예 참조](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>텍스트 필드 서식 식

텍스트 필드 서식 식은 기호 계층 `textOptions` 속성의 `textField` 옵션과 함께 사용하여 혼합 텍스트 서식을 제공할 수 있습니다. 이 식을 사용하면 입력 문자열과 서식 옵션의 집합을 지정할 수 있습니다. 이 식의 각 입력 문자열에 대해 다음과 같은 옵션을 지정할 수 있습니다.

- `'font-scale'` - 글꼴 크기의 크기 조정 인수를 지정합니다. 지정된 경우 이 값은 개별 문자열에 대해 `textOptions`의 `size` 속성을 재정의합니다.
- `'text-font'` - 이 문자열에 사용해야 하는 글꼴 패밀리를 하나 이상 지정합니다. 지정된 경우 이 값은 개별 문자열에 대해 `textOptions`의 `font` 속성을 재정의합니다.

다음 의사 코드에서는 텍스트 필드 서식 식의 구조를 정의합니다.

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**예제**

다음 예에서는 굵은 글꼴을 추가하고 기능 `title` 속성의 글꼴 크기를 확장하여 텍스트 필드의 서식을 지정합니다. 또한 이 예에서는 줄 바꿈에서 축소된 글꼴 크기를 사용하여 기능의 `subTitle` 속성을 추가합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

이 계층은 아래 이미지와 같이 point 기능을 렌더링합니다.

![서식이 지정된 텍스트 필드가 있는 Point 기능 이미지](media/how-to-expressions/text-field-format-expression.png)

### <a name="number-format-expression"></a>숫자 서식 식

`number-format` 식은 기호 계층의 `textField` 옵션과 함께만 사용할 수 있습니다. 이 식은 제공된 숫자를 서식이 지정된 문자열로 변환합니다. 이 식은 JavaScript의 [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수를 래핑하고 다음 옵션 집합을 지원합니다.

- `locale` - 지정된 언어로 정렬되는 방식으로 숫자를 문자열로 변환하려면 이 옵션을 지정합니다. 이 옵션에 [BCP 47 언어 태그](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)를 전달합니다.
- `currency` - 숫자를 통화를 나타내는 문자열로 변환합니다. 가능한 값은 미국 달러의 경우 "USD", 유로화의 경우 "EUR", 중국어 RMB의 경우 "CNY"인 [ISO 4217 통화 코드](https://en.wikipedia.org/wiki/ISO_4217)입니다.
- `'min-fraction-digits'` - 숫자의 문자열 버전에 포함할 최소 소수 자릿수를 지정합니다.
- `'max-fraction-digits'` - 숫자의 문자열 버전에 포함할 최대 소수 자릿수를 지정합니다.

다음 의사 코드에서는 텍스트 필드 서식 식의 구조를 정의합니다.

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**예제**

다음 예에서는 `number-format` 식을 사용하여 US 달러 값으로 표시되도록 기호 계층의 `textField` 옵션에서 point 기능의 `revenue` 속성을 렌더링하는 방법을 수정합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

이 계층은 아래 이미지와 같이 point 기능을 렌더링합니다.

![숫자 서식 식 예](media/how-to-expressions/number-format-expression.png)

### <a name="image-expression"></a>이미지 식

이미지 식은 기호 계층의 `image` 및 `textField` 옵션과 다각형 계층의 `fillPattern` 옵션에 사용할 수 있습니다. 이 식은 요청한 이미지가 스타일에 있는지 확인하고 이미지가 현재 스타일에 있는지 여부에 따라 확인된 이미지 이름 또는 `null`을 반환합니다. 이 유효성 검사 프로세스는 동기식이며 이미지 인수에 이미지를 요청하기 전에 이미지를 스타일에 추가된 상태여야 합니다.

**예제**

다음 예에서는 `image` 식을 사용하여 기호 계층에 텍스트가 있는 아이콘 인라인을 추가합니다.

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));
    
    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

이 계층은 아래 이미지에 표시된 것처럼 기호 계층에서 텍스트 필드를 렌더링합니다.

![이미지 식 예](media/how-to-expressions/image-expression.png)

## <a name="zoom-expression"></a>확대/축소 식

`zoom` 식은 렌더링 시 지도의 현재 확대/축소 수준을 검색하는 데 사용되고 `['zoom']`으로 정의됩니다. 이 식은 지도의 최소 및 최대 확대/축소 수준 범위 사이의 숫자를 반환합니다. 웹 및 Android용 Azure Maps 대화형 지도 컨트롤은 25개의 확대/축소 수준(0-24)을 지원합니다. `zoom` 식을 사용하면 지도의 확대/축소 수준의 변경에 따라 스타일을 동적으로 수정할 수 있습니다. `zoom` 식과는 `interpolate` 및 `step` 식만 사용할 수 있습니다.

**예제**

기본적으로 열 지도 계층에 렌더링되는 데이터 포인트의 반경에는 모든 확대/축소 수준에 대한 고정 픽셀 반경이 있습니다. 지도의 확대/축소 수준이 변경되면 데이터가 함께 집계되고 열 지도 계층이 다르게 보입니다. 각 데이터 포인트가 지도의 동일한 실제 영역을 포함하도록 각 확대/축소 수준에 대한 반경의 크기를 조정하는 데 `zoom` 식을 사용할 수 있습니다. 열 지도 계층을 더 정적이고 일관되게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 비교했을 때 가로 및 세로로 두 배의 픽셀이 있습니다. 각 확대/축소 수준마다 두 배가 되도록 반경 크기를 조정하면 모든 확대/축소 수준에서 일관되게 보이는 열 지도를 만듭니다. `base 2 exponential interpolation` 식과 함께 `zoom` 식 사용하여 이를 수행할 수 있습니다. 최소 확대/축소 수준에 대해 픽셀 반경이 설정되고 아래와 같이 `2 * Math.pow(2, minZoom - maxZoom)`처럼 계산된 최대 확대/축소 수준에 대해 배율이 조정된 반경이 사용됩니다.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[라이브 예 참조](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>변수 바인딩 식

변수 바인딩 식은 계산 결과를 변수에 저장합니다. 따라서 계산 결과를 식의 다른 위치에서 여러 번 참조할 수 있습니다. 많은 계산을 포함하는 식에 대한 유용한 최적화입니다.

| 식 | 반환 형식 | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 결과를 반환하는 자식 식에서 `var` 식에 사용할 변수로 하나 이상의 값을 저장합니다. |
| `['var', name: string]` | any | `let` 식을 사용하여 만든 변수를 참조합니다. |

**예제**

이 예에서는 온도 비율을 기준으로 수익을 계산하는 식을 사용한 다음 `case` 식을 사용하여 이 값에 대한 다양한 부울 연산을 계산합니다. `let` 식은 한 번만 계산하면 되는 온도 비율을 기준으로 수익을 저장하는 데 사용됩니다. `var` 식은 변수를 다시 계산하지 않고도 필요한 만큼 자주 참조합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>다음 단계

식을 구현하는 더 많은 코드 샘플은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](map-add-heat-map-layer.md)

식을 지원하는 계층 옵션에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)