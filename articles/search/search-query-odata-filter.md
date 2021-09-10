---
title: OData 필터 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만드는 데 사용되는 OData 언어 참조 및 전체 구문입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: be51077c5aad6056cf4e28656c0552bc052a7ead
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829913"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData $filter 구문

Azure Cognitive Search에서는 [OData 필터 식](query-odata-filter-orderby-syntax.md)을 사용하여 전체 텍스트 검색어 외의 추가 조건을 검색 쿼리에 적용합니다. 이 문서에서는 필터 구문에 대해 자세히 설명합니다. 필터 개념 및 필터를 사용하여 특정 쿼리 시나리오를 실현하는 방법에 대한 일반적인 내용은 [Azure Cognitive Search의 필터](search-filters.md)를 참조하세요.

## <a name="syntax"></a>구문

OData 언어의 필터는 부울 식이며, 다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에 표시된 여러 형식의 식 중 하나일 수 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

부울 식 형식에는 다음이 포함됩니다.

- `any` 또는 `all`을 사용하는 컬렉션 필터 식. 이 식은 컬렉션 필드에 필터 조건을 적용합니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md)를 참조하세요.
- `and`, `or`, `not` 연산자를 사용하여 다른 부울 식을 결합하는 논리 식. 자세한 내용은 [Azure Cognitive Search의 OData 논리 연산자](search-query-odata-logical-operators.md)를 참조하세요.
- `eq`, `ne`, `gt`, `lt`, `ge`, `le` 연산자를 사용하여 필드 또는 범위 변수를 상수 값과 비교하는 비교 식. 자세한 내용은 [Azure Cognitive Search의 OData 비교 연산자](search-query-odata-comparison-operators.md)를 참조하세요. 비교 식은 `geo.distance` 함수를 사용하여 지리 공간 좌표 사이의 거리를 비교하는 데에도 사용됩니다. 자세한 내용은 [Azure Cognitive Search의 OData 지리 공간적 함수](search-query-odata-geo-spatial-functions.md)를 참조하세요.
- 부울 리터럴 `true` 및 `false`. 이 상수는 프로그래밍 방식으로 필터를 생성하는 경우에 가끔 유용할 수 있지만 그렇지 않은 경우에는 실제로 사용되지 않습니다.
- 다음을 비롯한 부울 함수 호출
  - `geo.intersects` - 지정된 점이 지정된 다각형 내에 있는지 여부를 테스트합니다. 자세한 내용은 [Azure Cognitive Search의 OData 지리 공간적 함수](search-query-odata-geo-spatial-functions.md)를 참조하세요.
  - `search.in` - 필드 또는 범위 변수와 값 목록의 각 값을 비교합니다. 자세한 내용은 [Azure Cognitive Search의 OData `search.in` 함수](search-query-odata-search-in-function.md)를 참조하세요.
  - `search.ismatch` 및 `search.ismatchscoring` - 필터 컨텍스트에서 전체 텍스트 검색 작업을 실행합니다. 자세한 내용은 [Azure Cognitive Search의 OData 전체 텍스트 검색 함수](search-query-odata-full-text-search-functions.md)를 참조하세요.
- `Edm.Boolean` 형식의 필드 경로 또는 범위 변수. 예를 들어 인덱스에 `IsEnabled`라는 부울 필드가 있고 이 필드가 `true`인 모든 문서를 반환하려는 경우 필터 식으로 이름 `IsEnabled`만 사용해도 됩니다.
- 괄호 안의 부울 식. 괄호를 사용하면 필터의 작업 순서를 명시적으로 결정하는 데 도움이 됩니다. OData 연산자의 기본 우선 순위에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="operator-precedence-in-filters"></a>필터의 연산자 우선 순위

하위 식 주위에 괄호를 사용하지 않고 필터 식을 작성하면 Azure Cognitive Search에서 연산자 우선 순위 규칙 집합에 따라 식을 계산합니다. 이 규칙은 하위 식을 결합하는 데 사용되는 연산자를 기반으로 합니다. 다음 표에는 연산자 그룹이 가장 높은 우선 순위부터 가장 낮은 우선 순위 순으로 나와 있습니다.

| 그룹 | 연산자 |
| --- | --- |
| 논리 연산자 | `not` |
| 비교 연산자 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 논리 연산자 | `and` |
| 논리 연산자 | `or` |

위의 표에서 우선 순위가 높은 연산자는 다른 연산자보다 해당 피연산자에 “더 긴밀하게 바인딩”됩니다. 예를 들어 `and`는 `or`보다 우선 순위가 높고 비교 연산자는 둘 중 하나보다 우선 순위가 높기 때문에 다음 두 식은 동일합니다.

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not` 연산자는 모든 연산자 중에서 우선 순위가 가장 높으며 비교 연산자보다도 높습니다. 다음과 같은 필터를 작성한다고 가정해 보세요.

```odata-filter-expr
    not Rating gt 5
```

위와 같은 이유로 다음 오류 메시지가 표시됩니다.

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

이 오류는 연산자가 전체 비교 식이 아니라 `Edm.Int32` 형식의 `Rating` 필드에만 연결되기 때문에 발생합니다. 문제를 해결하려면 `not`의 피연산자를 괄호 안에 넣습니다.

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>필터 크기 제한

Azure Cognitive Search로 보낼 수 있는 필터 식의 크기 및 복잡성은 제한됩니다. 이러한 제한은 대략적으로 필터 식의 절 수와 관련이 있습니다. 적절한 지침은 수백 개의 절이 있을 경우 한도에 도달할 위험이 있다는 것입니다. 제한 없는 크기의 필터를 생성하지 않도록 애플리케이션을 설계하는 것이 좋습니다.

> [!TIP]
> 함수 호출이 단일 절로 계산되기 때문에 같음 비교의 긴 논리합 대신 [`search.in` 함수](search-query-odata-search-in-function.md)를 사용하면 필터 절 한도를 피할 수 있습니다.

## <a name="examples"></a>예

등급이 4 이상이고 기본 요금이 $200 미만인 객실이 하나 이상 있는 호텔을 모두 찾습니다.

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

2010년 이후에 리모델링된 호텔 중에서 “Sea View Motel”을 제외한 호텔을 모두 찾습니다.

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

2010년 이후에 리모델링된 호텔을 모두 찾습니다. 날짜/시간 리터럴에는 태평양 표준시의 표준 시간대 정보가 포함됩니다.  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

주차장이 있고 모든 객실이 비흡연실인 호텔을 모두 찾습니다.

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- 또는 -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

럭셔리 호텔이거나 주차장이 포함되어 있고 등급이 5인 모든 호텔을 찾습니다.  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

하나 이상의 객실에 “wifi” 태그가 있는 호텔을 모두 찾습니다(각 객실에는 `Collection(Edm.String)` 필드에 저장된 태그가 있음).  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

객실이 있는 호텔을 모두 찾습니다.  

```odata-filter-expr
    $filter=Rooms/any()
```

객실이 없는 호텔을 모두 찾습니다.

```odata-filter-expr
    $filter=not Rooms/any()
```

지정된 참조 지점에서 10km 이내에 있는 호텔을 모두 찾습니다(`Location`은 `Edm.GeographyPoint` 형식의 필드임).

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

다각형으로 설명된 지정된 뷰포트 내의 호텔을 모두 찾습니다(`Location`은 Edm.GeographyPoint 형식의 필드임). 다각형은 닫혀 있어야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. 또한 [점이 시계 반대 방향으로 나열되어야 합니다](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

“Description” 필드가 Null인 호텔을 모두 찾습니다. 설정되지 않았거나 명시적으로 Null로 설정된 필드는 Null이 됩니다.  

```odata-filter-expr
    $filter=Description eq null
```

이름이 ‘Sea View motel’이거나 ‘Budget hotel’인 호텔을 모두 찾습니다. 해당 구에는 공백이 포함되고 공백이 기본 구분 기호입니다. 대체 구분 기호를 작은따옴표로 묶어 세 번째 문자열 매개 변수로 지정할 수 있습니다.  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

이름이 '|'로 구분된 'Sea View motel'이거나 'Budget hotel'인 호텔을 모두 찾습니다.  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

모든 객실에 ‘wifi’ 또는 ‘tub’ 태그가 있는 호텔을 모두 찾습니다.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

태그에서 ‘heated towel racks’ 또는 ‘hairdryer included’ 등의 컬렉션 내 구와 일치하는 항목을 찾습니다.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](/rest/api/searchservice/search-documents)과 동일합니다.

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 `or`를 사용하여 필터 작업과 전체 텍스트 검색을 결합하므로 `search.ismatchscoring` 함수 없이 표현할 수 없습니다.

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

단어 "luxury"가 없는 문서를 찾습니다.

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Description`에 대해서만 실행됩니다. 논리합의 두 번째 절에만 일치하는 문서(`Rating`이 5인 호텔)도 반환됩니다. 점수를 매긴 식의 어떤 부분과도 일치하지 않았음을 명확하게 나타내기 위해 해당 문서는 점수가 0으로 반환됩니다.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

“hotel” 및 “airport” 용어가 설명에서 서로 5개 단어 이내에 있고 모든 객실이 비흡연실인 호텔을 찾습니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

설명 필드에서 문자 "lux"로 시작하는 단어가 있는 문서를 찾습니다. 이 쿼리는 `search.ismatch`와 함께 [접두사 검색](query-simple-syntax.md#prefix-queries)을 사용합니다.

```odata-filter-expr
    $filter=search.ismatch('lux*', 'Description')
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)