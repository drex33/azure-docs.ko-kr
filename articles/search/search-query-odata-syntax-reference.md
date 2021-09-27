---
title: OData 식 구문 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리 내 OData 식의 공식 문법 및 구문 사양입니다.
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
ms.openlocfilehash: 8e8cc8522a4e41c389043f394ec0de5e26f51c53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620296"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Azure Cognitive Search의 OData 식 구문 참조

Azure Cognitive Search는 API 전체에서 [OData 식](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html)을 매개 변수로 사용합니다. 대체로 OData 식은 `$orderby` 및 `$filter` 매개 변수에 사용됩니다. 이 식은 여러 절, 함수, 연산자를 포함하여 복잡할 수 있습니다. 그러나 속성 경로와 같은 간단한 OData 식도 Azure Cognitive Search REST API의 여러 부분에서 사용됩니다. 예를 들어 경로 식은 [제안기](index-add-suggesters.md), [채점 함수](index-add-scoring-profiles.md), `$select` 매개 변수 또는 [Lucene 쿼리의 필드 지정 검색](query-lucene-syntax.md)에서 하위 필드를 나열하는 경우와 같이 API 전체에서 복합 필드의 하위 필드를 참조하는 데 사용됩니다.

이 문서에서는 공식 문법을 사용하여 모든 형식의 OData 식에 대해 설명합니다. 문법을 시각적으로 살펴볼 수 있도록 [대화형 다이어그램](#syntax-diagram)도 제공됩니다.

## <a name="formal-grammar"></a>공식 문법

EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 문법을 사용하여 Azure Cognitive Search에서 지원되는 OData 언어 하위 집합을 설명할 수 있습니다. 규칙은 가장 복잡한 식부터 시작해서 보다 단순한 식으로 분석하여 “하향식”으로 나열됩니다. 맨 위에는 Azure Cognitive Search REST API의 특정 매개 변수에 해당하는 문법 규칙이 있습니다.

- [`$filter`](search-query-odata-filter.md) - `filter_expression` 규칙으로 정의됩니다.
- [`$orderby`](search-query-odata-orderby.md) - `order_by_expression` 규칙으로 정의됩니다.
- [`$select`](search-query-odata-select.md) - `select_expression` 규칙으로 정의됩니다.
- 필드 경로 - `field_path` 규칙으로 정의됩니다. 필드 경로는 API 전체에서 사용됩니다. 인덱스의 최상위 필드 또는 하나 이상의 [복합 필드](search-howto-complex-data-types.md) 상위 항목이 있는 하위 필드를 참조할 수 있습니다.

EBNF 뒤에는 문법과 해당 규칙 간 관계를 대화형으로 살펴볼 수 있는 검색 가능한 [구문 다이어그램](https://en.wikipedia.org/wiki/Syntax_diagram)이 있습니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>구문 다이어그램

Azure Cognitive Search에서 지원되는 OData 언어 문법을 시각적으로 살펴보려면 대화형 구문 다이어그램을 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>참고 항목  

- [Azure Cognitive Search의 필터](search-filters.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene 쿼리 구문](query-lucene-syntax.md)
- [Azure Search의 단순 쿼리 구문](query-simple-syntax.md)