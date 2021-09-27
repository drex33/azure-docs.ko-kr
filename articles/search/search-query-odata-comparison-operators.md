---
title: OData 비교 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 비교 연산자(eq, ne, gt, lt, ge, le)를 사용하기 위한 구문 및 참조 설명서입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 804e974196b62b6dfed9d4bd64b8dfe0cc107faf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591523"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure Cognitive Search의 OData 비교 연산자 - `eq`, `ne`, `gt`, `lt`, `ge`, `le`

Azure Cognitive Search의 [OData 필터 식](query-odata-filter-orderby-syntax.md)에서 가장 기본적인 작업은 필드를 지정된 값과 비교하는 것입니다. 비교에는 같음 비교와 범위 비교의 두 가지 유형이 있습니다. 다음 연산자를 사용하여 필드를 상수 값과 비교할 수 있습니다.

같음 연산자:

- `eq`: 필드가 상수 값 **과 같은지** 여부를 테스트합니다.
- `ne`: 필드가 상수 값 **과 같지 않은지** 여부를 테스트합니다.

범위 연산자:

- `gt`: 필드가 상수 값 **보다 큰지** 여부를 테스트합니다.
- `lt`: 필드가 상수 값 **보다 작은지** 여부를 테스트합니다.
- `ge`: 필드가 상수 값 **보다 크거나 같은지** 여부를 테스트합니다.
- `le`: 필드가 상수 값 **보다 작거나 같은지** 여부를 테스트합니다.

[논리 연산자](search-query-odata-logical-operators.md)와 함께 범위 연산자를 사용하여 필드가 특정 값 범위 내에 있는지 여부를 테스트할 수 있습니다. 이 문서의 뒷부분에 있는 [예제](#examples)를 참조하세요.

> [!NOTE]
> 원할 경우 연산자의 왼쪽에 상수 값을 입력하고 오른쪽에 필드 이름을 입력할 수 있습니다. 범위 연산자의 경우 비교의 의미가 반대로 바뀝니다. 예를 들어 상수 값이 왼쪽에 있으면 `gt`는 상수 값이 필드보다 큰지 여부를 테스트합니다. 비교 연산자를 사용하여 `geo.distance`와 같은 함수의 결과를 값과 비교할 수도 있습니다. `search.ismatch`와 같은 부울 함수의 경우 결과를 `true`에 비교할지 또는 `false`에 비교할지 선택할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 비교 연산자를 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

비교 식에는 두 가지 형식이 있습니다. 두 형식은 상수가 연산자의 왼쪽에 표시되는지 또는 오른쪽에 표시되는지의 차이만 있을 뿐입니다. 연산자의 반대쪽에 있는 식은 **변수** 또는 함수 호출이어야 합니다. 변수는 필드 이름이거나 [람다 식](search-query-odata-collection-operators.md)의 경우 범위 변수일 수 있습니다.

## <a name="data-types-for-comparisons"></a>비교할 데이터 형식

비교 연산자의 양쪽에 있는 데이터 형식이 호환되어야 합니다. 예를 들어 왼쪽이 `Edm.DateTimeOffset` 형식의 필드이면 오른쪽은 날짜/시간 상수여야 합니다. 숫자 데이터 형식은 더 유연합니다. 다음 표에 설명된 것처럼 몇 가지 제한 사항은 있지만 모든 숫자 형식의 변수와 함수를 다른 모든 숫자 형식의 상수와 비교할 수 있습니다.

| 변수 또는 함수 형식 | 상수 값 형식 | 제한 사항 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 비교에 [`NaN`에 대한 특수 규칙](#special-case-nan)이 적용됩니다. |
| `Edm.Double` | `Edm.Int64` | 상수가 `Edm.Double`로 변환되므로 큰 값의 경우 정밀도가 손실됩니다. |
| `Edm.Double` | `Edm.Int32` | 해당 없음 |
| `Edm.Int64` | `Edm.Double` | `NaN`, `-INF` 또는 `INF`와 비교할 수 없습니다. |
| `Edm.Int64` | `Edm.Int64` | 해당 없음 |
| `Edm.Int64` | `Edm.Int32` | 비교 전에 상수가 `Edm.Int64`로 변환됩니다. |
| `Edm.Int32` | `Edm.Double` | `NaN`, `-INF` 또는 `INF`와 비교할 수 없습니다. |
| `Edm.Int32` | `Edm.Int64` | 해당 없음 |
| `Edm.Int32` | `Edm.Int32` | 해당 없음 |

`Edm.Int64` 형식의 필드를 `NaN`과 비교하는 경우와 같이 허용되지 않는 비교의 경우 Azure Cognitive Search REST API에서 “HTTP 400: 잘못된 요청” 오류가 반환됩니다.

> [!IMPORTANT]
> 숫자 형식 비교의 경우 유연성이 있긴 하지만 상수 값이 비교할 변수나 함수와 동일한 데이터 형식이 되도록 필터의 비교를 작성하는 것이 좋습니다. 부동 소수점 값과 정수 값을 함께 사용하여 암시적 변환 시 정밀도가 손실될 수 있는 경우에는 특히 이 작업이 중요합니다.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` 및 `NaN`의 특수 사례

비교 연산자를 사용하는 경우 Azure Cognitive Search의 모든 비컬렉션 필드가 잠재적으로 `null`이 될 수 있다는 것에 유의해야 합니다. 다음 표에서는 둘 중 하나가 `null`이 될 수 있는 비교 식의 가능한 모든 결과를 보여 줍니다.

| 연산자 | 필드 또는 변수만 `null`인 경우의 결과 | 상수가 `null`인 경우의 결과 | 필드 또는 변수와 상수가 둘 다 `null`인 경우의 결과 |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `lt` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `ge` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `le` | `false` | HTTP 400: 잘못된 요청 오류 | HTTP 400: 잘못된 요청 오류 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

요약하면 `null`은 null과 같기만 하고 다른 값보다 작거나 크지 않습니다.

인덱스에 `Edm.Double` 형식의 필드가 있고 해당 필드에 `NaN` 값을 업로드하는 경우 필터를 작성할 때 이 사항을 고려해야 합니다. Azure Cognitive Search에서는 `NaN` 값을 처리하기 위한 IEEE 754 표준을 구현하며, 해당 값과 비교할 경우 다음 표와 같이 명확하지 않은 결과가 생성됩니다.

| 연산자 | 하나 이상의 피연산자가 `NaN`인 경우의 결과 |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

요약하면 `NaN`은 NaN을 비롯한 모든 값과 같지 않습니다.

### <a name="comparing-geo-spatial-data"></a>지리 공간 데이터 비교

`Edm.GeographyPoint` 형식의 필드를 상수 값과 직접 비교할 수는 없지만 `geo.distance` 함수를 사용할 수 있습니다. 이 함수는 `Edm.Double` 형식의 값을 반환하므로 숫자 상수와 비교하여 상수 지리 공간 좌표에서의 거리를 기준으로 필터링할 수 있습니다. 아래 [예제](#examples)를 참조하세요.

### <a name="comparing-string-data"></a>문자열 데이터 비교

`eq` 및 `ne` 연산자를 사용하여 필터에서 문자열을 비교하고 정확한 일치 항목을 찾을 수 있습니다. 이 비교는 대/소문자를 구분합니다.

## <a name="examples"></a>예

`Rating` 필드가 3~5(포함) 사이인 문서를 찾습니다.

```text
Rating ge 3 and Rating le 5
```

`Location` 필드가 지정된 위도 및 경도에서 2킬로미터 미만인 문서를 찾습니다.

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

`LastRenovationDate` 필드가 2015년 1월 1일 자정(UTC)보다 이후이거나 같은 문서를 찾습니다.

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

`Details/Sku` 필드가 `null`이 아닌 문서를 찾습니다.

```text
Details/Sku ne null
```

하나 이상의 객실이 “Deluxe Room” 유형이고 `Rooms/Type` 필드의 문자열이 필터와 정확히 일치하는 호텔의 문서를 찾습니다.

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)