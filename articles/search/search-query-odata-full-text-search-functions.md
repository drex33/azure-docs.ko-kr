---
title: OData 전체 텍스트 검색 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 OData 전체 텍스트 검색 함수인 search.ismatch 및 search.ismatchscoring
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
ms.openlocfilehash: 8a3c563c25283c300942f1fb58072e8c61847ba1
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831452"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Azure Cognitive Search의 OData 전체 텍스트 검색 함수 - `search.ismatch` 및 `search.ismatchscoring`

Azure Cognitive Search는 `search.ismatch` 및 `search.ismatchscoring` 함수를 통해 [OData 필터 식](query-odata-filter-orderby-syntax.md)의 컨텍스트에서 전체 텍스트 검색을 지원합니다. 두 함수를 사용하면 [검색 API](/rest/api/searchservice/search-documents)의 최상위 `search` 매개 변수만으로는 불가능한 방식으로 엄격한 부울 필터링과 전체 텍스트 검색을 결합할 수 있습니다.

> [!NOTE]
> `search.ismatch` 및 `search.ismatchscoring` 함수는 [검색 API](/rest/api/searchservice/search-documents)의 필터에서만 지원됩니다. [제안](/rest/api/searchservice/suggestions) 또는 [자동 완성](/rest/api/searchservice/autocomplete) API에서는 지원되지 않습니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 `search.ismatch` 및 `search.ismatchscoring` 함수의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

### <a name="searchismatch"></a>search.ismatch

`search.ismatch` 함수는 전체 텍스트 검색 쿼리를 필터 식의 일부로 평가합니다. 검색 쿼리와 일치하는 문서가 결과 집합에 반환됩니다. 이 함수의 다음 오버로드를 사용할 수 있습니다.

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

매개 변수는 다음 표에 정의되어 있습니다.

| 매개 변수 이름 | 유형 | 설명 |
| --- | --- | --- |
| `search` | `Edm.String` | 검색 쿼리([simple](query-simple-syntax.md) 또는 [full](query-lucene-syntax.md) Lucene 쿼리 구문)입니다. |
| `searchFields` | `Edm.String` | 검색할 검색 가능 필드의 쉼표로 구분된 목록으로, 기본값은 인덱스의 모든 검색 가능 필드입니다. `search` 매개 변수에 [필드 지정 검색](query-lucene-syntax.md#bkmk_fields)을 사용하는 경우 Lucene 쿼리의 필드 지정자가 이 매개 변수에 지정된 모든 필드를 재정의합니다. |
| `queryType` | `Edm.String` | `'simple'` 또는 `'full'`이며 기본값은 `'simple'`입니다. `search` 매개 변수에 사용된 쿼리 언어를 지정합니다. |
| `searchMode` | `Edm.String` | `'any'` 또는 `'all'`이며 기본값은 `'any'`입니다. 문서가 일치 항목으로 계산되기 위해 `search` 매개 변수의 검색어 중 하나 또는 모두와 일치해야 하는지를 나타냅니다. `search` 매개 변수에 [Lucene 부울 연산자](query-lucene-syntax.md#bkmk_boolean)를 사용하는 경우 이 매개 변수보다 우선 적용됩니다. |

위의 모든 매개 변수는 [검색 API의 해당 검색 요청 매개 변수](/rest/api/searchservice/search-documents)와 같습니다.

`search.ismatch` 함수는 `Edm.Boolean` 형식의 값을 반환하므로 부울 [논리 연산자](search-query-odata-logical-operators.md)를 사용하여 다른 필터 하위 식으로 값을 작성할 수 있습니다.

> [!NOTE]
> Azure Cognitive Search에서는 람다 식에 `search.ismatch` 또는 `search.ismatchscoring`을 사용할 수 없습니다. 즉, 동일한 개체에 대한 엄격한 필터 일치 항목과 전체 텍스트 검색 일치 항목을 상호 연결할 수 있는 개체 컬렉션 필터를 작성할 수 없습니다. 이 제한 사항에 대한 자세한 내용과 예제는 [Azure Cognitive Search에서 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조하세요. 제한 사항이 있는 이유에 대한 자세한 내용은 [Azure Cognitive Search의 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatch` 함수와 마찬가지로 `search.ismatchscoring` 함수는 매개 변수로 전달된 전체 텍스트 검색 쿼리와 일치하는 문서에 대해 `true`를 반환합니다. 이 두 함수 간의 차이점은 `search.ismatchscoring` 쿼리와 일치하는 문서의 관련성 점수가 전반적인 문서 점수에 기여하지만, `search.ismatch`의 경우 문서 점수가 변경되지 않는다는 것입니다. 이 함수의 다음 오버로드는 `search.ismatch`의 매개 변수와 동일한 매개 변수에서 사용할 수 있습니다.

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` 및 `search.ismatchscoring` 함수를 동일한 필터 식에서 모두 사용할 수 있습니다.

## <a name="examples"></a>예

"waterfront" 단어를 포함하는 문서를 찾습니다. 이 필터 쿼리는 `search=waterfront`를 사용한 [검색 요청](/rest/api/searchservice/search-documents)과 동일합니다.

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

단어 "hostel"을 포함하고 등급이 4 이상인 문서 또는 단어 "motel"을 포함하고 등급이 5인 문서를 찾습니다. 이 요청은 `search.ismatchscoring` 함수를 사용해야만 표시할 수 있습니다.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

단어 "luxury"가 없는 문서를 찾습니다.

```odata-filter-expr
    not search.ismatch('luxury')
```

구 "ocean view"를 포함하거나 등급이 5인 문서를 찾습니다. `search.ismatchscoring` 쿼리는 필드 `HotelName` 및 `Rooms/Description`에 대해서만 실행됩니다.

논리합의 두 번째 절에만 일치하는 문서(`Rating`이 5인 호텔)도 반환됩니다. 점수를 매긴 식의 어떤 부분과도 일치하지 않았음을 명확하게 나타내기 위해 해당 문서는 점수가 0으로 반환됩니다.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

“hotel” 및 “airport” 용어가 호텔 설명에서 서로 5개 단어 이내에 있고 적어도 일부 객실이 비흡연실인 문서를 찾습니다. 이 쿼리는 [전체 Lucene 쿼리 언어](query-lucene-syntax.md)를 사용합니다.

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

설명 필드에서 "lux"로 시작하는 단어가 있는 문서를 찾습니다. 이 쿼리는 `search.ismatch`와 함께 [접두사 검색](query-simple-syntax.md#prefix-queries)을 사용합니다.

```odata-filter-expr
    search.ismatch('lux*', 'Description')
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)