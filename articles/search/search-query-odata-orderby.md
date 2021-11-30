---
title: OData order-by 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 order-by를 사용하기 위한 구문 및 언어 참조 설명서입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: reference
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
ms.openlocfilehash: f87c3f04fe8ef7c18a1b87c6b10474c05a1692b5
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266277"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData $orderby 구문

 [OData **$orderby** 매개 변수](query-odata-filter-orderby-syntax.md)를 사용하여 Azure Cognitive Search에서 검색 결과에 대해 사용자 지정 정렬 순서를 적용할 수 있습니다. 이 문서에서는 **$orderby** 구문에 대해 자세히 설명합니다. 검색 결과를 표시할 때 **$orderby** 를 사용하는 방법에 대한 일반적인 내용은 [Azure Cognitive Search에서 검색 결과 작업 방법](search-pagination-page-layout.md)을 참조하세요.

## <a name="syntax"></a>구문

**$orderby** 매개 변수는 최대 32개의 **order-by 절** 을 포함하는 쉼표로 구분된 목록을 수락합니다. order-by 절의 구문은 다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에서 설명합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

각 절에는 정렬 기준이 있으며, 필요에 따라 뒤에 정렬 방향(오름차순인 `asc` 또는 내림차순인 `desc`)이 표시됩니다. 방향을 지정하지 않는 경우 기본값은 오름차순입니다. 필드에 Null 값이 있는 경우 정렬이 `asc`이면 Null 값이 첫 번째로 표시되고 정렬이 `desc`이면 마지막에 표시됩니다.

정렬 기준은 `sortable` 필드의 경로이거나 [`geo.distance`](search-query-odata-geo-spatial-functions.md) 또는 [`search.score`](search-query-odata-search-score-function.md) 함수의 호출일 수 있습니다.

여러 문서에서 동일한 정렬 기준을 사용하고 `search.score` 함수가 사용되지 않는 경우(예: 숫자 `Rating` 필드로 정렬하고 3개 문서의 등급이 모두 4인 경우) 문서 점수 내림차순으로 동률을 구분합니다. 문서 점수가 같으면(예: 요청에 전체 텍스트 검색 쿼리가 지정되지 않은 경우) 동률 문서의 상대 순서가 결정되지 않습니다.

여러 정렬 기준을 지정할 수 있습니다. 식의 순서는 최종 정렬 순서를 결정합니다. 예를 들어 점수 내림차순으로 정렬한 다음, 등급 내림차순으로 정렬하려는 경우 구문은 `$orderby=search.score() desc,Rating desc`가 됩니다.

**$orderby** 에서 `geo.distance`에 대한 구문은 **$filter** 에 있을 때와 같습니다. **$orderby** 에서 `geo.distance`를 사용하는 경우 적용되는 필드는 `Edm.GeographyPoint` 형식이어야 하고 `sortable`이어야 합니다.

**$orderby** 에서 `search.score`에 대한 구문은 `search.score()`입니다. `search.score` 함수는 매개 변수를 사용하지 않습니다.

## <a name="examples"></a>예

호텔을 기본 요금의 오름차순으로 정렬합니다.

```odata-filter-expr
    $orderby=BaseRate asc
```

호텔을 등급의 내림차순으로 정렬한 후 기본 요금의 오름차순으로 정렬합니다(오름차순이 기본값임).

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

호텔을 등급 내림차순으로 정렬한 다음, 지정된 좌표에서의 거리 오름차순으로 정렬합니다.

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

호텔을 search.score 및 등급 내림차순으로 정렬한 다음, 지정된 좌표에서의 거리 오름차순으로 정렬합니다. 관련성 점수와 등급이 같은 두 호텔 중에서 가장 가까운 호텔이 먼저 나열됩니다.

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search에서 검색 결과 작업 방법](search-pagination-page-layout.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)