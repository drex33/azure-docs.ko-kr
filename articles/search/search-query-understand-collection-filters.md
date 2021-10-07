---
title: OData 컬렉션 필터 이해
titleSuffix: Azure Cognitive Search
description: 컬렉션에 고유한 제한 사항 및 동작을 포함하여 Azure Cognitive Search 쿼리에서 OData 컬렉션 필터가 작동하는 방식의 메커니즘을 알아봅니다.
author: bevloh
ms.author: beloh
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 9a73f5073d5388499a678b25db983046a50de717
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612088"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData 컬렉션 필터 이해

Azure Cognitive Search에서 컬렉션 필드를 [필터링](query-odata-filter-orderby-syntax.md)하려면 [ `any` 및 `all` 연산자](search-query-odata-collection-operators.md)를 **람다 식** 과 함께 사용합니다. 람다 식은 **범위 변수** 를 참조하는 부울 식입니다. `any` 및 `all` 연산자는 대부분의 프로그래밍 언어서 루프 변수의 역할을 수행하는 범위 변수와 루프 본문으로 람다 식을 포함하는 `for` 루프와 유사합니다. 범위 변수는 루프를 반복하는 동안 컬렉션의 “현재” 값을 사용합니다.

적어도 개념적으로는 이런 방식으로 작동합니다. 실제로 Azure Cognitive Search는 `for` 루프의 작동 방식과는 다른 방법으로 필터를 구현합니다. 이 차이가 사용자에게 표시되지 않는 것이 이상적이지만 특정 상황에서는 표시되기도 합니다. 최종 결과로, 람다 식을 작성할 때 따라야 하는 규칙이 있습니다.

이 문서에서는 Azure Cognitive Search에서 이 필터를 실행하는 방법을 살펴보고 컬렉션 필터의 규칙이 있는 이유를 설명합니다. 복잡한 람다 식을 사용하여 고급 필터를 작성하는 경우 이 문서가 필터에서 가능한 항목과 그 이유를 이해하는 데 도움이 될 수 있습니다.

예제를 포함하여 컬렉션 필터의 규칙에 대한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조하세요.

## <a name="why-collection-filters-are-limited"></a>컬렉션 필터가 제한되는 이유

모든 유형의 컬렉션에서 모든 필터 기능이 지원되지는 않으며, 다음 세 가지 기본 이유가 있습니다.

1. 특정 데이터 형식에서는 특정 연산자만 지원됩니다. 예를 들어 `lt`, `gt` 등을 사용하여 부울 값 `true` 및 `false`를 비교하는 것은 의미가 없습니다.
1. Azure Cognitive Search는 `Collection(Edm.ComplexType)` 형식의 필드에서 **상관 관계 지정 검색** 을 지원하지 않습니다.
1. Azure Cognitive Search는 반전 인덱스를 사용하여 컬렉션을 비롯한 모든 데이터 형식에서 필터를 실행합니다.

첫 번째 이유는 OData 언어와 EDM 형식 시스템이 정의된 방식의 결과일 뿐입니다. 마지막 두 이유는 이 문서의 나머지 부분에서 자세히 설명합니다.

## <a name="correlated-versus-uncorrelated-search"></a>상관 관계 지정 검색 및 상관 관계 미지정 검색

복합 개체 컬렉션에 여러 필터 조건을 적용하는 경우 조건이 ‘컬렉션의 각 개체’에 적용되므로 **상관 관계가 지정** 됩니다. 예를 들어 다음 필터는 요금이 100 미만인 deluxe 객실이 하나 이상 있는 호텔을 반환합니다.

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

‘상관 관계 미지정’ 필터링인 경우 위의 필터는 deluxe인 한 객실과 요금이 100 미만인 다른 객실이 있는 호텔을 반환할 수 있습니다. 람다 식의 두 절이 `room`이라는 같은 범위 변수에 적용되므로 이 결과는 의미가 없습니다. 필터에 상관 관계가 지정되는 이유는 이 때문입니다.

그러나 전체 텍스트 검색에서는 특정 범위 변수를 참조할 수 없습니다. 필드 지정 검색을 사용하여 다음과 같은 [전체 Lucene 쿼리](query-lucene-syntax.md)를 실행한다고 가정해 보세요.

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

이 경우 deluxe인 한 객실과 설명에 “city view”가 포함된 다른 객실이 있는 호텔이 반환될 수 있습니다. 예를 들어 `Id`가 `1`인 아래 문서는 쿼리와 일치합니다.

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

그 이유는 아래 표와 같이 `Rooms/Type`이 전체 문서의 `Rooms/Type` 필드에서 분석된 모든 용어를 참조하고 `Rooms/Description`도 마찬가지이기 때문입니다.

전체 텍스트 검색에서 `Rooms/Type`이 저장되는 방식:

| `Rooms/Type`의 용어 | 문서 ID |
| --- | --- |
| deluxe | 1, 2 |
| 표준 | 1 |

전체 텍스트 검색에서 `Rooms/Description`이 저장되는 방식:

| `Rooms/Description`의 용어 | 문서 ID |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| garden | 1 |
| 큰 | 1 |
| 모텔 | 2 |
| 객실 | 1, 2 |
| 표준 | 1 |
| suite | 1 |
| 뷰 | 1 |

따라서 기본적으로 “객실 `Type`이 ‘Deluxe Room’이고 **동일한 객실** 의 `BaseRate`가 100 미만인 문서와 일치”하는 위의 필터와 달리 검색 쿼리는 “`Rooms/Type`에 “deluxe”라는 용어가 있고 `Rooms/Description`에 “city view” 구가 있는 문서와 일치”를 지정합니다. 후자의 경우 필드 상관 관계를 지정할 수 있는 개별 객실의 개념이 없습니다.

## <a name="inverted-indexes-and-collections"></a>반전 인덱스 및 컬렉션

`Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)` 등의 단순 컬렉션보다 복합 컬렉션의 람다 식에 대한 제한 사항이 훨씬 더 적은 것을 발견했을 수 있습니다. Azure Cognitive Search에서 복합 컬렉션은 하위 문서의 실제 컬렉션으로 저장되는 반면 단순 컬렉션은 컬렉션으로 저장되지 않기 때문입니다.

예를 들어 온라인 판매점의 인덱스에 `seasons`와 같은 필터링 가능한 문자열 컬렉션 필드가 있다고 가정해 보세요. 이 인덱스에 업로드된 일부 문서는 다음과 같이 표시될 수 있습니다.

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

`seasons` 필드의 값은 다음과 같이 **반전 인덱스** 라는 구조체에 저장됩니다.

| 용어 | 문서 ID |
| --- | --- |
| spring | 1, 2 |
| summer | 1 |
| fall | 1, 2 |
| winter | 2, 3 |

이 데이터 구조는 지정된 용어가 어떤 문서에 표시되는가란 한 가지 질문에 신속하게 대답하도록 설계되었습니다. 이 질문에 대한 대답은 컬렉션 루프보다 일반적인 같음 검사처럼 작동합니다. 실제로 문자열 컬렉션의 경우 Azure Cognitive Search에서 `any`의 람다 식 내 비교 연산자로서만 `eq`가 허용되는 이유는 이 때문입니다.

같음부터 시작해서 다음에는 동일한 범위 변수에 대한 여러 개의 같음 검사를 `or`로 결합할 수 있는 방법을 살펴보겠습니다. 이 방법은 대수 및 [수량자의 분배 속성](https://en.wikipedia.org/wiki/Existential_quantification#Negation)을 통해 작동합니다. 다음 식을 가정해 보세요.

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

이는 다음과 동등합니다.

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

반전 인덱스를 사용하여 두 개의 `any` 하위 식을 각각 효율적으로 실행할 수도 있습니다. 또한 [수량자의 부정 법칙](https://en.wikipedia.org/wiki/Existential_quantification#Negation)을 통해 다음 식을 가정해 보세요.

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

이는 다음과 동등합니다.

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

`ne` 및 `and`와 함께 `all`을 사용할 수 있는 이유는 이 때문입니다.

> [!NOTE]
> 세부 정보는 이 문서에서 설명하지 않지만 동일한 원칙이 [지리 공간적 지점 컬렉션에 대한 거리 및 교차 테스트](search-query-odata-geo-spatial-functions.md)까지 확장됩니다. `any`에서 다음과 같이 제한되는 이유는 이 때문입니다.
>
> - `geo.intersects`를 부정할 수 없음
> - `lt` 또는 `le`를 사용하여 `geo.distance`를 비교해야 합니다.
> - `and`가 아니라 `or`로 식을 결합해야 합니다.
>
> `all`의 경우 반대 규칙이 적용됩니다.

`lt`, `gt`, `le`, `ge` 연산자를 지원하는 데이터 형식 컬렉션(예: `Collection(Edm.Int32)`)을 필터링하는 경우 더 광범위한 식이 허용됩니다. 특히 기본 비교 식이 `and`를 사용하여 **범위 비교** 로 결합된 다음 `or`을 사용하여 추가로 결합되기만 하면 `any`에서 `and` 및 `or`를 사용할 수 있습니다. 이 부울 식 구조를 [DNF(Disjunctive Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)라고 하며, 달리 “AND의 OR”라고도 합니다. 반대로 데이터 형식의 `all`에 대한 람다 식은 [CNF(Conjunctive Normal Form)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)에 있어야 하며, 달리 “OR의 AND”라고도 합니다. Azure Cognitive Search에서는 문자열에 대한 빠른 용어 조회와 마찬가지로 반전 인덱스를 사용하여 효율적으로 실행할 수 있으므로 범위 비교를 허용합니다.

요약하면, 람다 식에서 허용되는 항목은 대략적으로 다음과 같습니다.

- `any` 내에서 같음, 범위 비교, `geo.intersects`, `geo.distance`와 `lt` 또는 `le` 비교 등의 ‘양수 검사’는 항상 허용됩니다(거리 검사의 경우 “가장 가까움”을 같음으로 간주).
- `any` 내에서 `or`는 항상 허용됩니다. `and`는 DNF(AND의 OR)를 사용하는 경우에만, 그리고 범위 검사를 표현할 수 있는 데이터 형식에서만 사용할 수 있습니다.
- `all` 내에서는 규칙이 반대가 됩니다. ‘음수 검사’만 허용되고, `and`는 항상 사용할 수 있으며, `or`는 CNF(OR의 AND)로 표현된 범위 검사에서만 사용할 수 있습니다.

실제로 가장 많이 사용되는 필터 유형입니다. 그러나 가능한 항목의 경계를 알면 도움이 됩니다.

허용되는 필터 종류와 허용되지 않는 필터 종류의 구체적인 예는 [유효한 컬렉션 필터를 작성하는 방법](search-query-troubleshoot-collection-filters.md#bkmk_examples)을 참조하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 OData 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)
- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)