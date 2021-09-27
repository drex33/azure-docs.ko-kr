---
title: OData search.score 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 search.score 함수를 사용하는 방법에 대한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 27cd6b6a9fb827d2ce2e4f3489cf51db797d0a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595582"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData `search.score` 함수

[ **$orderby** 매개 변수](search-query-odata-orderby.md)없이 Azure Cognitive Search 쿼리를 보내면, 다시 제공되는 결과가 관련성 점수별로 내림차순으로 정렬됩니다. **$orderby** 를 사용하는 경우에도 관련성 점수는 기본적으로 동점을 끊는 데 사용됩니다. 그러나 가끔 관련성 점수를 초기 정렬 조건으로 사용하고, 다른 조건을 동률 분리기로 사용하는 것이 유용한 경우도 있습니다. `search.score` 함수를 사용하면 이 작업을 수행할 수 있습니다.

## <a name="syntax"></a>구문

**$orderby** 에서 `search.score`에 대한 구문은 `search.score()`입니다. 함수 `search.score`는 매개 변수를 사용하지 않습니다. **$orderby** 매개 변수의 다른 절과 마찬가지로 `asc` 또는 `desc` 정렬 순서 지정자와 함께 사용할 수 있습니다. 정렬 조건 목록의 어디서나 나타날 수 있습니다.

## <a name="example"></a>예제

호텔을 `search.score` 및 `rating` 내림차순으로 정렬한 다음, 지정된 좌표에서의 거리로 오름차순 정렬하여 등급이 같은 두 호텔 중에서 가장 가까운 호텔이 먼저 나열되도록 합니다.

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search에서 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색 &#40;Azure Cognitive Search EST API&#41;](/rest/api/searchservice/Search-Documents)