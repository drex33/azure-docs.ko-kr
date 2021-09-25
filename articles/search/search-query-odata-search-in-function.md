---
title: OData search.in 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 search.in 함수를 사용하는 방법에 대한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 3196c16e8223fe814a38bfa5556b9db5d68b2699
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567153"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData `search.in` 함수

[OData 필터 식](query-odata-filter-orderby-syntax.md)의 일반적인 시나리오는 각 문서의 단일 필드가 가능한 많은 값 중 하나와 동일한지 확인하는 것입니다. 예를 들어, 일부 애플리케이션이 쿼리를 발행하는 사용자를 나타내는 주체 ID 목록에 비해 하나 이상의 주체 ID가 포함된 필드를 확인하여 [보안 조정](search-security-trimming-for-azure-search.md)을 구현하는 방법입니다. 다음과 같이 쿼리를 작성하는 한 가지 방법은 [`eq`](search-query-odata-comparison-operators.md) 및 [`or`](search-query-odata-logical-operators.md) 연산자를 사용하는 것입니다.

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

하지만 `search.in` 함수를 사용하여 이를 작성하는 더 간단한 방법이 있습니다.

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> `search.in`을 사용하면 더 짧고 읽기 쉬울 뿐만 아니라 [성능상의 이점](#bkmk_performance)을 제공하며 필터에 포함할 값이 수백 또는 수천 개일 때에도 [필터의 특정 크기 제한](search-query-odata-filter.md#bkmk_limits)을 피할 수 있습니다. 이러한 이유로 동등 식의 더 복잡한 분리 대신 `search.in`을 사용하는 것이 좋습니다.

> [!NOTE]
> 최근 OData 표준의 버전 4.01에는 [`in` 연산자](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)가 도입되었으며, 이 연산자는 Azure Cognitive Search의 `search.in` 함수와 유사한 동작이 있습니다. 하지만 Azure Cognitive Search는 이 연산자를 지원하지 않기 때문에 대신 `search.in` 함수를 사용해야 합니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus–Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 `search.in` 함수의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

`search.in` 함수는 지정된 문자열 필드 또는 범위 변수가 지정된 값 목록 중 하나와 동일한지 테스트합니다. 이 목록에 있는 변수 및 각 값 사이의 동등함은 `eq` 연산자의 경우와 같이 대소문자 구분 방식으로 결정됩니다. 따라서 `search.in(myfield, 'a, b, c')`과 같은 식은 `search.in`이 훨씬 더 나은 성능을 제공한다는 점을 제외하고 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`와 같습니다.

다음과 같이 `search.in` 함수에는 두 가지 오버로드가 있습니다.

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

매개 변수는 다음 표에 정의되어 있습니다.

| 매개 변수 이름 | 유형 | 설명 |
| --- | --- | --- |
| `variable` | `Edm.String` | 문자열 필드 참조(또는 `search.in`이 `any` 또는 `all` 식 내에서 사용되는 경우 문자열 컬렉션 필드 위의 범위 변수). |
| `valueList` | `Edm.String` | `variable` 매개 변수와 비교할 구분된 목록 값을 포함하는 문자열입니다. `delimiters` 매개 변수가 지정되지 않은 경우 기본 구분 기호는 공백 및 쉼표입니다. |
| `delimiters` | `Edm.String` | `valueList` 매개 변수를 구문 분석할 때 각 문자가 구분 기호로 처리되는 문자열입니다. 이 매개 변수의 기본 값은 `' ,'`이며, 공백 및/또는 쉼표가 있는 값은 구분된다는 뜻입니다. 값에 공백 및 쉼표가 포함되므로 이 외의 다른 구분 기호를 사용해야 하는 경우 이 매개 변수에 `'|'` 등의 대체 구분 기호를 지정할 수 있습니다. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in`의 성능

`search.in`을 사용하는 경우 두 번째 매개 변수에 수십만 개 값 목록이 포함되어 있을 때 1초 미만의 응답 시간을 기대할 수 있습니다. 최대 요청 크기는 여전히 제한되지만 `search.in`에 전달할 수 있는 항목 수는 명시적으로 제한되지 않습니다. 그러나 값 수가 증가함에 따라 대기 시간도 증가합니다.

## <a name="examples"></a>예

이름이 ‘Sea View motel’이거나 ‘Budget hotel’인 호텔을 모두 찾습니다. 구에는 기본 구분 기호인 공백이 포함됩니다. 대체 구분 기호를 작은따옴표로 묶어 세 번째 문자열 매개 변수로 지정할 수 있습니다.  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

다음과 같이 이름이 '|'로 구분된 'Sea View motel' 또는 'Budget hotel'인 모든 호텔을 찾습니다.

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

다음과 같이 'wifi' 또는 'tub' 태그가 있는 방이 있는 모든 호텔을 찾습니다.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

태그에서 'heated towel racks' 또는 'hairdryer included' 등의 컬렉션 내 구와 일치하는 항목을 찾습니다.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

다음과 같이 'motel' 또는 'cabin' 태그가 없는 모든 호텔을 찾습니다.

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)