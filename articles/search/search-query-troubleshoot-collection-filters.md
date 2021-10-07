---
title: OData 컬렉션 필터 문제 해결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 컬렉션 필터 오류를 해결하기 방법에 대해 알아봅니다.
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: da5769f2d9562676b9a3f1c0494395295737ae94
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620187"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData 컬렉션 필터 문제 해결

Azure Cognitive Search에서 컬렉션 필드를 [필터링](query-odata-filter-orderby-syntax.md)하려면 [`any` 및 `all` 연산자](search-query-odata-collection-operators.md)를 **람다 식** 과 함께 사용합니다. 람다 식은 컬렉션의 각 요소에 적용되는 하위 필터입니다.

필터 식의 모든 기능을 람다 식에서 사용할 수 있는 것은 아닙니다. 사용할 수 있는 기능은 필터링하려는 컬렉션 필드의 데이터 형식에 따라 다릅니다. 해당 컨텍스트에서 지원되지 않는 람다 식의 기능을 사용하려고 하면 오류가 발생할 수 있습니다. 컬렉션 필드에 복잡한 필터를 작성하는 동안 이러한 오류가 발생하는 경우 이 문서가 문제 해결에 도움이 될 것입니다.

## <a name="common-collection-filter-errors"></a>일반 컬렉션 필터 오류

다음 표에서는 컬렉션 필터를 실행하려 할 때 발생할 수 있는 오류를 나열합니다. 해당 오류는 람다 식에서 지원되지 않는 필터 식 기능을 사용하는 경우에 발생합니다. 각 오류는 오류를 방지하기 위해 필터를 다시 작성하는 방법에 대한 지침을 제공합니다. 이 표에는 오류를 방지하는 방법에 대한 이 문서의 관련 섹션 링크도 포함되어 있습니다.

| 오류 메시지 | 상황 | 자세한 내용은 다음을 참조하세요. |
| --- | --- | --- |
| 'ismatch' 함수에는 범위 변수 's'에 바인딩된 매개 변수가 없습니다. 람다 식('any' 또는 'all') 내에서는 바인딩된 필드 참조만 지원됩니다. 'ismatch' 함수가 람다 식 외부에 있도록 필터를 변경하고 다시 시도하세요. | 람다 식 내에서 `search.ismatch` 또는 `search.ismatchscoring` 사용 | [복합 컬렉션 필터링 규칙](#bkmk_complex) |
| 잘못된 람다 식입니다. Collection(Edm.String) 형식의 필드를 반복하는 람다 식에서 반대되는 같음 또는 같지 않음에 대한 테스트를 발견했습니다. 'any'의 경우 'x eq y'또는 'search.in(...)' 형식의 식을 사용하세요. 'all'의 경우 'x ne y', 'not (x eq y)', 또는 'not search.in(...)' 형식의 식을 사용하세요. | `Collection(Edm.String)` 형식 필드에 대한 필터링 | [문자열 컬렉션을 필터링 규칙](#bkmk_strings) |
| 잘못된 람다 식입니다. 지원되지 않는 형식의 복합 부울 식을 찾았습니다. 'any'의 경우 DNF(Disjunctive Nomal Form)로 알려진 'ORs of ANDs' 식을 사용하세요. 예: '(a 및 b) 또는 (c 및 d)' 여기서 a, b, c 및 d는 비교 또는 같음의 하위 식입니다. 'all'의 경우 CNF(Conjunctive Normal Form)로 알려진 'ANDs of ORs' 식을 사용하세요. 예: '(a 또는 b) 및 (c 또는 d)' 여기서 a, b, c 및 d는 비교 또는 같지 않음의 하위 식입니다. 비교 식의 예: 'x gt 5', 'x le 2'. 같음 식의 예: 'x eq 5'. 같지 않음 식의 예: 'x ne 5'. | `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)` 또는 `Collection(Edm.Int64)` 형식 필드 필터링 | [비교 가능한 컬렉션 필터링 규칙](#bkmk_comparables) |
| 잘못된 람다 식입니다. Collection(Edm.GeographyPoint) 형식의 필드를 반복하는 람다 식에서 지원되지 않는 geo.distance() 또는 geo.intersects() 사용을 발견했습니다. 'any'의 경우 'lt'또는 'le' 연산자를 사용하여 geo.distance()를 비교하고 geo.intersects() 사용이 부정되지 않았는지 확인합니다. 'all'의 경우 'gt'또는 'ge' 연산자를 사용하여 geo.distance()를 비교하고 geo.intersects() 사용이 부정되었는지 확인합니다. | `Collection(Edm.GeographyPoint)` 형식 필드에 대한 필터링 | [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 잘못된 람다 식입니다. Collection(Edm.GeographyPoint) 형식의 필드를 반복하는 람다 식에서는 복합 부울식이 지원되지 않습니다. 'any'의 경우 하위 식을 'or'과 조인하세요. 'and'는 지원되지 않습니다. 'all'의 경우 하위 식을 'and'와 조인하세요. 'or'은 지원되지 않습니다. | `Collection(Edm.String)` 또는 `Collection(Edm.GeographyPoint)` 형식 필드 필터링 | [문자열 컬렉션을 필터링 규칙](#bkmk_strings) <br/><br/> [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints) |
| 잘못된 람다 식입니다. 비교 연산자 ('lt', 'le', 'gt', 또는 'ge' 중 하나)를 찾았습니다. Collection(Edm.String) 형식의 필드를 반복하는 람다 식에는 같음 연산자만 허용됩니다. 'any'의 경우 'x eq y'형식의 식을 사용하세요. 'all'의 경우 'x ne y' 또는 'not (x eq y)' 형식의 식을 사용하세요. | `Collection(Edm.String)` 형식 필드에 대한 필터링 | [문자열 컬렉션을 필터링 규칙](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>유효 컬렉션 필터 작성 방법

유효 컬렉션 필터 작성 규칙은 각 데이터 형식마다 다릅니다. 다음 섹션에서는 지원되는 필터 기능과 지원되지 않는 기능의 예를 표시하여 규칙을 설명합니다:

- [문자열 컬렉션을 필터링 규칙](#bkmk_strings)
- [부울 컬렉션 필터링 규칙](#bkmk_bools)
- [GeographyPoint 컬렉션 필터링 규칙](#bkmk_geopoints)
- [비교 가능한 컬렉션 필터링 규칙](#bkmk_comparables)
- [복합 컬렉션 필터링 규칙](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>문자열 컬렉션 필터링 규칙

문자열 컬렉션에 대한 람다 식 내에서 사용할 수 있는 유일한 비교 연산자는 `eq` 및 `ne`입니다.

> [!NOTE]
> Azure Cognitive Search는 람다 식 내외부와 관계없이 문자열에 대해 `lt`/`le`/`gt`/`ge` 연산자를 지원하지 않습니다.

`any` 본문은 같음만 테스트할 수 있고 `all` 본문은 같지 않음만 테스트할 수 있습니다.

`any` 본문의 `or`및 `all` 본문의 `and`을 통해 여러 식을 결합할 수도 있습니다. `search.in` 함수는 같음 검사를 `or`와 결합하는 것과 동일하므로 `any` 본문에서도 허용됩니다. 반대로 `all` 본문에는 `not search.in`이 허용됩니다.

예를 들어 다음의 식이 허용됩니다:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

이러한 식은 허용되지 않습니다:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>부울 컬렉션 필터링 규칙

`Edm.Boolean` 유형은 `eq` 및 `ne` 연산자만 지원합니다. 따라서 동일한 범위 변수를 `and`/`or`과 함께 검사하는 해당 절을 결합하는 것은 항상 Tautology나 Contradiction으로 이어질 수 있기 때문에 그다지 의미가 없습니다.

다음은 허용되는 부울 컬렉션에 대한 필터의 몇 가지 예입니다:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

문자열 컬렉션과 달리 부울 컬렉션에는 람다 식에 사용할 수 있는 연산자에 대한 제한이 없습니다. `eq` 및 `ne`는 모두 `any` 또는 `all`의 본문에서 사용할 수 있습니다.

다음과 같은 식은 부울 컬렉션에 허용되지 않습니다:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>GeographyPoint 컬렉션 필터링 규칙

컬렉션의 형식 `Edm.GeographyPoint` 값은 서로 직접 비교할 수 없습니다. 대신 `geo.distance` 및 `geo.intersects` 함수에 대한 매개 변수로 사용해야합니다. `geo.distance` 함수는 차례로 비교 연산자 `lt`, `le`, `gt` 또는 `ge` 중 하나를 사용하여 거리 값과 비교해야 합니다. 해당 규칙은 컬렉션이 아닌 Edm.GeographyPoint 필드에도 적용됩니다.

문자열 컬렉션과 마찬가지로 `Edm.GeographyPoint` 컬렉션에는 다양한 형식의 람다 식에서 지리 공간 함수를 사용하고 결합하는 방법에 대한 몇 가지 규칙이 있습니다:

- `geo.distance` 함수와 함께 사용할 수 있는 비교 연산자는 람다 식의 형식에 따라 달라집니다. `any`의 경우 `lt` 또는 `le`만 사용할 수 있습니다. `all`의 경우 `gt` 또는 `ge`만 사용할 수 있습니다. `geo.distance`과 관련된 식을 부정할 수 있지만 비교 연산자(`geo.distance(...) lt x`는 `not (geo.distance(...) ge x)`으로, `geo.distance(...) le x`는 `not (geo.distance(...) gt x)`로)를 변경해야 합니다.
- `all`의 본문에서 `geo.intersects` 함수는 부정해야 합니다. 반대로 `any`의 본문에서 `geo.intersects` 함수는 부정해서는 안됩니다.
- `any`의 본문에서 지리 공간 식은 `or`를 사용하여 결합할 수 있습니다. `all`의 본문에서 이러한 식은 `and`를 사용하여 결합할 수 있습니다.

위의 제한 사항은 문자열 컬렉션의 같음/같지 않음 제한 사항과 유사한 이유로 존재합니다. 해당 이유를 자세히 살펴보기 위해 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.

다음은 허용되는 `Edm.GeographyPoint` 컬렉션에 대한 필터의 몇 가지 예입니다:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

`Edm.GeographyPoint` 컬렉션에는 다음과 같은 식이 허용되지 않습니다:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>비교 가능한 컬렉션 필터링 규칙

이 섹션은 다음 데이터 형식 모두에 적용됩니다:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

`Edm.Int32` 및 `Edm.DateTimeOffset`와 같은 형식은 `eq`, `ne`, `lt`, `le`, `gt` 및 `ge`의 여섯 가지 비교 연산자를 모두 지원합니다. 이러한 형식의 컬렉션에 대한 람다 식에는 해당 연산자를 사용하는 간단한 식을 포함할 수 있습니다. 이는 `any` 및 `all` 모두에 적용됩니다. 예를 들어 다음과 같은 필터가 허용됩니다:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

그러나 이러한 비교 식을 람다 식 내에서 더 복잡한 식으로 결합하는 방법에는 다음과 같은 제한 사항이 있습니다:

- `any`에 대한 규칙:
  - 단순한 같지 않음 식은 다른 어떤 식과도 유용하게 결합할 수 없습니다. 예를 들어 다음과 같은 식이 허용됩니다:
    - `ratings/any(r: r ne 5)`

    그러나 다음과 같은 식은 허용되지 않습니다:
    - `ratings/any(r: r ne 5 and r gt 2)`

    이 식은 허용되지만 조건이 겹치므로 유용하지 않습니다:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - `eq`, `lt`, `le`, `gt` 또는 `ge`와 관련된 간단한 비교 식을 `and`/`or`과 결합할 수 있습니다. 예를 들면 다음과 같습니다.
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - `and`(결합)과 결합된 비교 식은 `or`를 사용하여 추가로 결합할 수 있습니다. 이 양식은 부울 논리에서"[DNF](https://en.wikipedia.org/wiki/Disjunctive_normal_form)(Disjunctive Normal Form)"로 알려져 있습니다. 예를 들면 다음과 같습니다.
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- `all`에 대한 규칙:
  - 단순한 같음 식은 다른 어떤 식과도 유용하게 결합할 수 없습니다. 예를 들어 다음과 같은 식이 허용됩니다:
    - `ratings/all(r: r eq 5)`

    그러나 다음과 같은 식은 허용되지 않습니다:
    - `ratings/all(r: r eq 5 or r le 2)`

    이 식은 허용되지만 조건이 겹치므로 유용하지 않습니다:
    - `ratings/all(r: r eq 5 and r le 7)`
  - `ne`, `lt`, `le`, `gt` 또는 `ge`와 관련된 간단한 비교 식을 `and`/`or`과 결합할 수 있습니다. 예를 들면 다음과 같습니다.
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - `or`(분리)과 결합된 비교 식은 `and`를 사용하여 추가로 결합할 수 있습니다. 이 양식은 부울 논리에서"[CNF](https://en.wikipedia.org/wiki/Conjunctive_normal_form)(Conjunctive Normal Form)"로 알려져 있습니다. 예를 들면 다음과 같습니다.
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>복합 컬렉션 필터링 규칙

복합 컬렉션에 대한 람다 식은 기본 형식 컬렉션에 대한 람다 식보다 훨씬 더 유연한 구문을 지원합니다. 두 가지 예외만 제외하고 외부에서 사용할 수 있는 이러한 람다 식에 모든 필터 구문을 사용할 수 있습니다.

먼저 `search.ismatch` 및 `search.ismatchscoring` 함수는 람다 식 내에서 지원되지 않습니다. 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.

두 번째로, 범위 변수 (*무료 변수*)에 *바인딩* 되지 않은 참조 필드는 허용되지 않습니다. 예를 들어 다음과 같은 두 개의 동일한 OData 필터 식을 고려합니다:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

첫 번째 식은 허용되지만 두 번째 양식은 `details/margin`이 범위 변수 `s`에 바인딩되지 않기 때문에 거부됩니다.

또한 이 규칙은 외부 범위에 바인딩된 변수가 있는 식으로 확장됩니다. 해당 변수는 표시되는 범위에 대하여 무료입니다. 예를 들어 첫 번째 식은 허용되지만 두 번째 해당 식은 `s/name`이 범위 변수 `a`의 범위에 대해 무료이기 때문에 허용되지 않습니다:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

람다 식에 바인딩된 변수만 포함되도록 필터를 구성하는 것이 항상 가능하기 때문에 이러한 제한 사항은 실제로는 문제가 되지 않습니다.

## <a name="cheat-sheet-for-collection-filter-rules"></a>컬렉션 필터 규칙용 참고 자료

다음 표에서는 각 컬렉션 데이터 형식에 대해 유효한 필터를 구성하기 위한 규칙을 요약해서 보여줍니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

각 사례에 대해 유효한 필터를 구성하는 방법에 대한 예제는 [유효한 컬렉션 필터를 작성하는 방법](#bkmk_examples)을 참조하세요.

필터를 자주 작성하고 첫 번째 원칙의 규칙을 이해하는 것이 단순히 기억하는 것 이상으로 도움이 되는 경우 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)