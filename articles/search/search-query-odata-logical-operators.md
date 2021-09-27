---
title: OData 논리 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 논리 연산자인 and, or, not을 사용하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 0cd42f2a8ba7b9d543909604184e3618da31e710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656553"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure Cognitive Search의 OData 논리 연산자 - `and`, `or`, `not`

Azure Cognitive Search의 [OData 필터 식](query-odata-filter-orderby-syntax.md)은 `true` 또는 `false`로 평가되는 부울 식입니다. 일련의 [단순 필터](search-query-odata-comparison-operators.md)를 작성하고 [부울 대수](https://en.wikipedia.org/wiki/Boolean_algebra)의 논리 연산자로 복합 필터를 구성하여 복합 필터를 작성할 수 있습니다.

- `and`: 왼쪽 및 오른쪽 하위 식이 모두 `true`로 평가되면 `true`로 평가되는 이항 연산자입니다.
- `or`: 왼쪽 및 오른쪽 하위 식 중 하나가 `true`로 평가되면 `true`로 평가되는 이항 연산자입니다.
- `not`: 하위 식이 `false`로 평가되면 `true`로 평가되고 그 반대의 경우도 마찬가지인 단항 연산자입니다.

[컬렉션 연산자 `any` 및 `all`](search-query-odata-collection-operators.md)과 함께 위의 연산자를 사용하면 매우 복잡한 검색 조건을 표현할 수 있는 필터를 생성할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 논리 연산자를 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

논리 식에는 두 개의 하위 식이 있는 이항(`and`/`or`)과 하위 식이 하나뿐인 단항(`not`)의 두 가지 형태가 있습니다. 하위 식은 다음을 포함하여 모든 종류의 부울 식일 수 있습니다.

- `Edm.Boolean` 형식의 필드 또는 범위 변수
- `Edm.Boolean` 형식의 값을 반환하는 함수(예: `geo.intersects` 또는 `search.ismatch`)
- [비교 식](search-query-odata-comparison-operators.md)(예: `rating gt 4`)
- [컬렉션 식](search-query-odata-collection-operators.md)(예: `Rooms/any(room: room/Type eq 'Deluxe Room')`)
- 부울 리터럴 `true` 또는 `false`
- `and`, `or`, `not`을 사용하여 생성된 다른 논리 식

> [!IMPORTANT]
> 특히 람다 식 내에서는 `and`/`or`과 함께 모든 종류의 하위 식을 사용할 수 없는 경우도 있습니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 연산자](search-query-odata-collection-operators.md#limitations)를 참조하세요.

### <a name="logical-operators-and-null"></a>논리 연산자 및 `null`

함수 및 비교와 같은 대부분의 부울 식은 `null` 값을 생성할 수 없으며 `null` 리터럴에 직접 논리 연산자를 적용할 수 없습니다(예: `x and null`은 허용되지 않음). 그러나 부울 필드는 `null`이 될 수 있으므로 Null이 있는 경우 `and`, `or`, `not` 연산자가 어떻게 동작하는지를 알고 있어야 합니다. 이 내용은 다음 표에 요약되어 있습니다. 여기서 `b`는 `Edm.Boolean` 형식의 필드입니다.

| 식 | `b`가 `null`인 경우의 결과 |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

부울 필드 `b`는 필터 식에 단독으로 표시되는 경우 `b eq true`로 작성된 것처럼 동작하므로 `b`가 `null`이면 식이 `false`로 평가됩니다. 마찬가지로, `not b`는 `not (b eq true)`처럼 동작하므로 `true`로 평가됩니다. 이런 방식으로 `null` 필드는 `false`와 동일하게 동작합니다. 이 동작은 위의 표와 같이 `and` 및 `or`를 사용하여 다른 식과 결합될 때의 동작과 일치합니다. 하지만 `false`와 직접 비교하는 경우(`b eq false`)에도 `false`로 평가됩니다. 즉, `null`은 부울 식에서 false처럼 동작하지만 `false`와 같지 않습니다.

## <a name="examples"></a>예

`rating` 필드가 3~5(포함) 사이인 문서를 찾습니다.

```odata-filter-expr
    rating ge 3 and rating le 5
```

`ratings` 필드의 모든 요소가 3보다 작거나 5보다 큰 문서를 찾습니다.

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

`location` 필드가 지정된 다각형 내에 있고 문서에 “public”이라는 용어가 포함되지 않은 문서를 찾습니다.

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

기본 요금이 160 미만인 deluxe 객실이 있는 캐나다 밴쿠버 호텔의 문서를 찾습니다.

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)