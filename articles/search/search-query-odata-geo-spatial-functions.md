---
title: OData 지리 공간적 함수 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 OData 지리 공간적 함수인 geo.distance 및 geo.intersects를 사용하기 위한 구문 및 참조 설명서입니다.
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
ms.openlocfilehash: 246c8fa5eaf092f4641da643471af9447be29fba
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133265862"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure Cognitive Search의 OData 지리 공간적 함수 - `geo.distance` 및 `geo.intersects`

Azure Cognitive Search는 `geo.distance` 및 `geo.intersects` 함수를 통해 [OData 필터 식](query-odata-filter-orderby-syntax.md)에서 지리 공간적 쿼리를 지원합니다. `geo.distance` 함수는 두 지점, 즉 필드 또는 범위 변수인 한 지점과 필터의 일부로 전달되는 상수인 다른 지점 사이의 거리(킬로미터)를 반환합니다. `geo.intersects` 함수는 지정된 지점이 지정된 다각형 내에 있는 경우 `true`를 반환합니다. 여기서 지점은 필드 또는 범위 변수이고 다각형은 필터의 일부로 전달되는 상수로 지정됩니다.

[ **$orderby** 매개 변수](search-query-odata-orderby.md)에서 `geo.distance` 함수를 사용하여 지정된 지점에서의 거리를 기준으로 검색 결과를 정렬할 수도 있습니다. **$orderby** 에서 `geo.distance`에 대한 구문은 **$filter** 에 있을 때와 같습니다. **$orderby** 에서 `geo.distance`를 사용하는 경우 적용되는 필드는 `Edm.GeographyPoint` 형식이어야 하고 **정렬 가능** 해야 합니다.

> [!NOTE]
> **$orderby** 매개 변수에서 `geo.distance`를 사용하는 경우 함수에 전달하는 필드는 단일 지리 지점만 포함해야 합니다. 즉, `Collection(Edm.GeographyPoint)` 형식이 아닌 `Edm.GeographyPoint` 형식이어야 합니다. Azure Cognitive Search에서는 컬렉션 필드를 기준으로 정렬할 수 없습니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))에서는 `geo.distance` 및 `geo.intersects` 함수의 문법과 두 함수가 적용되는 지리 공간적 값을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

### <a name="geodistance"></a>geo.distance

`geo.distance` 함수는 `Edm.GeographyPoint` 형식의 두 매개 변수를 사용하고 두 매개 변수 사이의 거리(킬로미터)인 `Edm.Double` 값을 반환합니다. 이 서비스는 OData 지리 공간적 작업을 지원하고 일반적으로 미터 단위의 거리를 반환하는 다른 서비스와는 다릅니다.

`geo.distance`의 매개 변수 중 하나는 지리 지점 상수여야 하고, 다른 매개 변수는 필드 경로(또는 `Collection(Edm.GeographyPoint)` 형식의 필드를 반복하는 필터의 경우 범위 변수)여야 합니다. 매개 변수의 순서는 중요하지 않습니다.

지리 지점 상수는 `geography'POINT(<longitude> <latitude>)'` 형식입니다. 여기서 longitude와 latitude는 숫자 상수입니다.

> [!NOTE]
> 필터에서 `geo.distance`를 사용하는 경우 `lt`, `le`, `gt` 또는 `ge`를 사용하여 함수에서 반환된 거리와 상수를 비교해야 합니다. 연산자 `eq` 및 `ne`는 거리를 비교할 때 사용할 수 없습니다. 예를 들어 `geo.distance`를 올바르게 사용하는 방법은 `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`입니다.

### <a name="geointersects"></a>geo.intersects

`geo.intersects` 함수는 `Edm.GeographyPoint` 형식의 변수와 `Edm.GeographyPolygon` 상수를 사용하며 점이 다각형 경계 내에 있는 경우 `Edm.Boolean` -- `true`를 반환하고 그렇지 않으면 `false`를 반환합니다.

다각형은 바인딩된 링을 정의하는 점 시퀀스로 저장된 2차원 표면입니다(아래의 [예제](#examples) 참조). 다각형은 닫혀야 합니다. 즉, 첫 번째 점과 마지막 점 세트가 같아야 합니다. [다각형의 점은 시계 반대 방향 순서여야 합니다](/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>경도 180도에 걸쳐 있는 지리 공간적 쿼리 및 다각형

많은 지리 공간적 쿼리 라이브러리의 경우 경도 180도(날짜 변경선 근처)를 포함하는 쿼리를 작성하는 것이 금지되거나 다각형을 자오선 양쪽에 하나씩 2개로 분할하는 것과 같은 대체 방법이 필요합니다.

Azure Cognitive Search에서 180도 경도를 포함하는 지리 공간적 쿼리는 쿼리 모양이 사각형이고 좌표가 경도 및 위도를 따라 그리드 레이아웃에 맞춰 정렬되는 경우 예상대로 작동합니다(예: `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). 그렇지 않은 경우 사각형이 아닌 도형이나 정렬되지 않은 도형의 경우 분할된 다각형 접근 방법을 고려해야 합니다.  

### <a name="geo-spatial-functions-and-null"></a>지리 공간적 함수 및 `null`

Azure Cognitive Search의 다른 모든 비컬렉션 필드와 마찬가지로 `Edm.GeographyPoint` 형식의 필드에는 `null` 값이 포함될 수 있습니다. Azure Cognitive Search에서 `null`인 필드의 `geo.intersects`를 평가할 때 결과는 항상 `false`입니다. 이 경우 `geo.distance`의 동작은 컨텍스트에 따라 달라집니다.

- 필터에서 `null` 필드의 `geo.distance`는 `null`이 됩니다. 즉, Null이 아닌 값과 `null`을 비교하면 `false`로 평가되기 때문에 문서가 일치하지 않습니다.
- **$orderby** 를 사용하여 결과를 정렬하는 경우 `null` 필드의 `geo.distance`는 가능한 최대 거리가 됩니다. 해당 필드가 있는 문서는 정렬 방향 `asc`를 사용하는 경우(기본값) 다른 모든 문서보다 아래에 정렬되고, 방향이 `desc`인 경우 다른 모든 문서보다 위에 정렬됩니다.

## <a name="examples"></a>예

### <a name="filter-examples"></a>필터 예시

지정된 참조 지점에서 10km 이내에 있는 호텔을 모두 찾습니다(location은 `Edm.GeographyPoint` 형식의 필드임).

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

다각형으로 설명된 지정된 뷰포트 내의 호텔을 모두 찾습니다(location은 `Edm.GeographyPoint` 형식의 필드임). 다각형은 닫혀 있으며(첫 번째 점과 마지막 점 세트가 같음) [점은 시계 반대 방향 순서로 나열되어야 합니다](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Order-by 예제

호텔을 `rating` 내림차순으로 정렬한 다음, 지정된 좌표에서의 거리 오름차순으로 정렬합니다.

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

호텔을 `search.score` 및 `rating` 내림차순으로 정렬한 다음, 지정된 좌표에서의 거리 오름차순으로 정렬하여 등급이 같은 두 호텔 중에서 가장 가까운 호텔이 먼저 나열되도록 합니다.

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)