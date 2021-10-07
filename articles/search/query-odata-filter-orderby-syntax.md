---
title: OData 언어 개요
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 filter, select, order-by에 대한 OData 언어 개요입니다.
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 2225bd0012e5159467cba17ebf7b272ca8db0cb7
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613019"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Azure Cognitive Search의 `$filter`, `$orderby`, `$select`에 대한 OData 언어 개요

Azure Cognitive Search에서는 **$filter**, **$orderby**, **$select** 식에 대한 OData 식 구문의 하위 집합을 지원합니다. 필터 식은 쿼리 구문 분석 때 평가되고, 검색을 특정 필드로 제한하거나 인덱스 검색 때 사용되는 일치 조건을 추가합니다. order-by 식은 반환된 문서를 정렬하기 위해 결과 집합에 대한 사후 처리 단계로 적용됩니다. select 식은 결과 집합에 포함되는 문서 필드를 결정합니다. 필드 참조 구문에 겹치는 부분이 있기는 하지만 **search** 매개 변수에서 사용되는 [simple](query-simple-syntax.md) 또는 [full](query-lucene-syntax.md) 쿼리 구문과 위의 식 구문은 구분됩니다.

이 문서에서는 filter, order-by, select 식에서 사용되는 OData 식 언어에 대해 개괄적으로 설명합니다. 언어는 가장 기본적인 요소로 시작하고 확장하는 “상향식”으로 표시됩니다. 각 매개 변수의 최상위 구문은 다음 개별 문서에서 설명합니다.

- [$filter 구문](search-query-odata-filter.md)
- [$orderby 구문](search-query-odata-orderby.md)
- [$select 구문](search-query-odata-select.md)

OData 식은 간단한 식부터 매우 복잡한 식까지 다양하지만 모두 공통 요소를 공유합니다. Azure Cognitive Search에서 OData 식의 가장 기본적인 부분은 다음과 같습니다.

- **필드 경로** - 인덱스의 특정 필드를 참조합니다.
- **상수** - 특정 데이터 형식의 리터럴 값입니다.

> [!NOTE]
> Azure Cognitive Search 용어와 [OData 표준](https://www.odata.org/documentation/) 간에는 몇 가지 차이가 있습니다. Azure Cognitive Search의 **필드** 는 OData의 **속성** 에 해당하고 **필드 경로** 는 **속성 경로** 에 해당합니다. Azure Cognitive Search에서 **문서** 를 포함하는 **인덱스** 를 OData에서는 더 일반적으로 **엔터티** 를 포함하는 **엔터티 집합** 이라고 합니다. 이 참조에서는 전체적으로 Azure Cognitive Search 용어가 사용되었습니다.

## <a name="field-paths"></a>필드 경로

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 필드 경로의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

필드 경로는 슬래시로 구분된 하나 이상의 **식별자** 로 구성됩니다. 각 식별자는 ASCII 문자 또는 밑줄로 시작하고 ASCII 문자, 숫자 또는 밑줄만 포함해야 하는 문자 시퀀스입니다. 문자는 대문자나 소문자일 수 있습니다.

식별자는 필드 이름 또는 필터의 [컬렉션 식](search-query-odata-collection-operators.md) 컨텍스트에서 사용된 **범위 변수**(`any` 또는 `all`)를 참조할 수 있습니다. 범위 변수는 컬렉션의 현재 요소를 나타내는 루프 변수와 유사합니다. 복합 컬렉션에서 해당 변수는 개체를 나타내며, 필드 경로를 사용하여 변수의 하위 필드를 참조할 수 있는 이유는 이 때문입니다. 대부분의 프로그래밍 언어에서 사용되는 점 표기법과 유사합니다.

다음 표에는 필드 경로 예제가 나와 있습니다.

| 필드 경로 | 설명 |
| --- | --- |
| `HotelName` | 인덱스의 최상위 필드를 참조합니다. |
| `Address/City` | 인덱스의 복합 필드에서 `City` 하위 필드를 참조합니다. 예제에서 `Address`는 `Edm.ComplexType` 형식입니다. |
| `Rooms/Type` | 인덱스의 복합 컬렉션 필드에서 `Type` 하위 필드를 참조합니다. 예제에서 `Rooms`는 `Collection(Edm.ComplexType)` 형식입니다. |
| `Stores/Address/Country` | 인덱스의 복합 컬렉션 필드에서 `Address` 하위 필드의 `Country` 하위 필드를 참조합니다. 예제에서 `Stores`는 `Collection(Edm.ComplexType)` 형식이고 `Address`는 `Edm.ComplexType` 형식입니다. |
| `room/Type` | `room` 범위 변수의 `Type` 하위 필드를 참조합니다(예: 필터 식 `Rooms/any(room: room/Type eq 'deluxe')`). |
| `store/Address/Country` | `store` 범위 변수에서 `Address` 하위 필드의 `Country` 하위 필드를 참조합니다(예: 필터 식 `Stores/any(store: store/Address/Country eq 'Canada')`). |

필드 경로의 의미는 컨텍스트에 따라 달라집니다. 필터에서 필드 경로는 현재 문서에 있는 필드의 ‘단일 인스턴스’ 값을 나타냅니다. **$orderby**, **$select** 또는 [전체 Lucene 구문의 필드 지정 검색](query-lucene-syntax.md#bkmk_fields)과 같은 다른 컨텍스트에서 필드 경로는 필드 자체를 참조합니다. 이 차이는 필터에서 필드 경로를 사용하는 방법에 영향을 줍니다.

필드 경로 `Address/City`를 고려해 보세요. 필터에서 이 경로는 “샌프란시스코”와 같은 현재 문서의 단일 도시를 참조합니다. 반면, `Rooms/Type`은 여러 객실의 `Type` 하위 필드를 참조합니다(예: 첫 번째 객실은 “standard”, 두 번째 객실은 “deluxe”). `Rooms/Type`은 `Type` 하위 필드의 ‘단일 인스턴스’를 참조하지 않으므로 필터에서 직접 사용할 수 없습니다. 대신, 객실 유형을 필터링하려면 다음과 같이 범위 변수와 함께 [람다 식](search-query-odata-collection-operators.md)을 사용합니다.

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

예제에서는 범위 변수 `room`이 `room/Type` 필드 경로에 표시됩니다. 이런 방식으로 `room/Type`은 현재 문서의 현재 객실 유형을 참조합니다. `Type` 하위 필드의 단일 인스턴스이므로 필터에서 직접 사용할 수 있습니다.

### <a name="using-field-paths"></a>필드 경로 사용

필드 경로는 [Azure Cognitive Search REST API](/rest/api/searchservice/)의 많은 매개 변수에서 사용됩니다. 다음 표에는 필드 경로를 사용할 수 있는 모든 위치 및 사용 제한 사항이 나와 있습니다.

| API | 매개 변수 이름 | 제한 |
| --- | --- | --- |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `suggesters/sourceFields` | 없음 |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | **검색 가능한** 필드만 참조할 수 있습니다. |
| 인덱스 [만들기](/rest/api/searchservice/create-index) 또는 [업데이트](/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | **필터링 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `queryType`이 `full`인 경우 `search` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `facet` | **패싯 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `highlight` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) | `searchFields` | **검색 가능한** 필드만 참조할 수 있습니다. |
| [제안](/rest/api/searchservice/suggestions) 및 [자동 완성](/rest/api/searchservice/autocomplete) | `searchFields` | [제안기](index-add-suggesters.md)의 일부인 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents), [제안](/rest/api/searchservice/suggestions), [자동 완성](/rest/api/searchservice/autocomplete) | `$filter` | **필터링 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents) 및 [제안](/rest/api/searchservice/suggestions) | `$orderby` | **정렬 가능한** 필드만 참조할 수 있습니다. |
| [검색](/rest/api/searchservice/search-documents), [제안](/rest/api/searchservice/suggestions), [조회](/rest/api/searchservice/lookup-document) | `$select` | **검색 가능한** 필드만 참조할 수 있습니다. |

## <a name="constants"></a>상수

OData의 상수는 지정된 EDM([엔터티 데이터 모델](/dotnet/framework/data/adonet/entity-data-model)) 형식의 리터럴 값입니다. Azure Cognitive Search에서 지원되는 형식 목록은 [지원되는 데이터 형식](/rest/api/searchservice/supported-data-types)을 참조하세요. 컬렉션 형식의 상수는 지원되지 않습니다.

다음 표에는 Azure Cognitive Search에서 지원되는 각 데이터 형식의 상수 예제가 나와 있습니다.

| 데이터 형식 | 예제 상수 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>문자열 상수에서 특수 문자 이스케이프

OData의 문자열 상수는 작은따옴표로 구분됩니다. 작은따옴표가 포함될 수 있는 문자열 상수로 쿼리를 생성해야 하는 경우 포함된 따옴표를 두 개 사용하여 이스케이프할 수 있습니다.

예를 들어 “Alice's car”와 같이 서식이 지정되지 않은 아포스트로피를 사용하는 구는 OData에서 문자열 상수 `'Alice''s car'`로 표시됩니다.

> [!IMPORTANT]
> 프로그래밍 방식으로 필터를 생성하는 경우 사용자 입력에서 제공된 문자열 상수를 이스케이프해야 합니다. 이 작업은 특히 필터를 사용하여 [보안 트리밍](search-security-trimming-for-azure-search.md)을 구현할 때 [삽입 공격](https://wikipedia.org/wiki/SQL_injection)의 가능성을 완화하기 위한 것입니다.

### <a name="constants-syntax"></a>상수 구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 위의 표에 있는 대부분의 상수의 문법을 정의합니다. 지리 공간적 형식의 문법은 [Azure Cognitive Search의 OData 지리 공간적 함수](search-query-odata-geo-spatial-functions.md)에서 확인할 수 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

## <a name="building-expressions-from-field-paths-and-constants"></a>필드 경로 및 상수에서 식 작성

필드 경로와 상수는 OData 식의 가장 기본적인 부분이지만 이미 전체 식이기도 합니다. 실제로 Azure Cognitive Search의 **$select** 매개 변수는 쉼표로 구분된 필드 경로 목록일 뿐이고 **$orderby** 도 **$select** 보다 많이 복잡하지 않습니다. 인덱스에 `Edm.Boolean` 형식의 필드가 있다면 해당 필드의 경로인 필터를 작성할 수도 있습니다. 마찬가지로 `true` 및 `false` 상수도 유효한 필터입니다.

그러나 대체로 둘 이상의 필드와 상수를 참조하는 더 복잡한 식이 필요합니다. 해당 식은 매개 변수에 따라 다양한 방식으로 빌드됩니다.

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 **$filter**, **$orderby**, **$select** 매개 변수의 문법을 정의합니다. 세 매개 변수는 필드 경로와 상수를 참조하는 더 간단한 식에서 빌드되었습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

**$orderby** 및 **$select** 매개 변수는 더 간단한 식의 쉼표로 구분된 목록입니다. **$filter** 매개 변수는 더 간단한 하위 식으로 구성된 부울 식입니다. 하위 식은 논리 연산자(예: [`and`, `or`, `not`](search-query-odata-logical-operators.md)), 비교 연산자(예: [`eq`, `lt`, `gt`](search-query-odata-comparison-operators.md)), 컬렉션 연산자(예: [`any`, `all`](search-query-odata-collection-operators.md))를 사용하여 결합됩니다.

**$filter**, **$orderby**, **$select** 매개 변수는 다음 문서에서 자세히 설명합니다.

- [Azure Cognitive Search의 OData $filter 구문](search-query-odata-filter.md)
- [Azure Cognitive Search의 OData $orderby 구문](search-query-odata-orderby.md)
- [Azure Cognitive Search의 OData $select 구문](search-query-odata-select.md)

## <a name="see-also"></a>참고 항목  

- [Azure Cognitive Search의 패싯 탐색](search-faceted-navigation.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)