---
title: 검색 결과에 대한 필터링
titleSuffix: Azure Cognitive Search
description: Microsoft Azure의 호스트된 클라우드 검색 서비스인 Azure Cognitive Search에서 쿼리에 대한 검색 결과를 줄이려면 사용자 보안 ID, 언어, 지리적 위치 또는 숫자 값별로 필터링합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f052325b2c73fa5fdcaff8f60de6547d5a7d9e57
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359649"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure Cognitive Search의 필터 

*필터* 는 쿼리에 사용되는 문서를 선택하기 위한 조건을 제공합니다. 필터는 단일 값 또는 OData [필터 식](search-query-odata-filter.md)일 수 있습니다. 전체 텍스트 검색과 달리 필터 값 또는 식만 엄격한 일치를 반환합니다.

[패싯 탐색](search-faceted-navigation.md)과 같은 일부 검색 환경은 구현 부분에서 필터에 따라 달라 지지만, 쿼리 범위를 특정 값으로 지정할 때는 언제든지 필터를 사용할 수 있습니다. 대신 특정 필드에 대한 쿼리의 범위를 지정하는 것이 목표인 경우 아래에 설명된 대체 방법이 있습니다.

## <a name="when-to-use-a-filter"></a>필터를 사용하는 경우

필터는 "내 주변 찾기", 패싯 탐색, 사용자가 볼 수 있는 문서만 표시하는 보안 필터를 비롯한 여러 검색 환경의 기초입니다. 이러한 환경 중 하나를 구현하는 경우 필터가 필요합니다. 지리적 위치 좌표, 사용자가 선택한 패싯 범주 또는 요청자의 보안 ID를 제공하는 검색 쿼리에 연결된 필터입니다.

다음은 공통 시나리오입니다.

+ 인덱스에서 내용에 따라 검색 결과를 조각화합니다. 호텔 위치, 카테고리 및 편의 시설을 포함하는 스키마가 제공되면, 조건(시애틀에서, 물가에서, 뷰가 있는)에서 명시적으로 일치하는 필터를 만들 수 있습니다. 

+ 검색 환경 구현에는 다음과 같은 필터 요구 사항이 수반됩니다.

  + [패싯 탐색](search-faceted-navigation.md)은 사용자가 선택한 패싯 범주를 다시 전달하는 데 필터를 사용합니다.
  + 지리적 검색은 "내 주변 찾기" 앱에서 현재 위치의 좌표를 전달하는 데 필터를 사용합니다. 
  + [보안 필터](search-security-trimming-for-azure-search.md)는 보안 식별자를 필터 조건으로 전달하고, 여기서 인덱스의 일치 항목은 문서에 액세스할 수 있는 권한을 위한 프록시 역할을 합니다.

+ "숫자 검색"을 수행합니다. 숫자 필드는 문서에서 검색할 수 있고 검색 결과에 표시되기도 하지만, 개별적으로 검색할 수는 없습니다(전체 텍스트 검색의 대상). 숫자 데이터를 기반으로 하는 선택 조건이 필요한 경우 필터를 사용합니다.

### <a name="alternative-methods-for-reducing-scope"></a>범위를 줄이기 위한 대체 방법

검색 결과를 효과적으로 줄이고 싶을 때 필터 외에 다른 방법도 있습니다. 목적에 따라 이러한 대안이 더 나을 수 있습니다.

+ `searchFields` 쿼리 매개 변수는 검색을 특정 필드로 제한합니다. 예를 들어, 인덱스에서 영어와 스페인어 설명을 위한 별도의 필드를 제공하는 경우 searchFields를 사용하여 전체 텍스트 검색에 사용할 대상 필드를 지정할 수 있습니다. 

+ `$select` 매개 변수는 결과 집합에 포함할 필드를 지정하는 데 사용되며 호출 애플리케이션에 보내기 전에 효과적으로 응답을 조정합니다. 이 매개 변수는 쿼리를 구체화하거나 문서 컬렉션을 줄이지 않지만, 세부적인 응답이 목표인 경우 이 매개 변수는 고려해야 할 옵션입니다. 

두 매개 변수에 대한 자세한 내용은 [문서 검색 > 요청 > 쿼리 매개 변수](/rest/api/searchservice/search-documents#query-parameters)를 참조하세요.

## <a name="how-filters-are-executed"></a>필터 실행 방법

쿼리 시 필터 파서는 조건을 입력으로 받아 표현식을 트리로 표현되는 원자성 부울 식으로 변환하고, 그러고 나서 인덱스의 필터 가능한 필드에 대해 필터 트리를 평가합니다.

필터링은 검색과 함께 수행되며, 문서 검색 및 관련성 평가를 위해 다운스트림 처리에 포함할 문서를 선별합니다. 필터가 검색 문자열과 함께 사용되면 필터는 후속 검색 작업의 호출 세트를 효과적으로 줄입니다. 단독으로 사용되면(예를 들어 `search=*` 같이 쿼리 문자열이 비어 있는 경우) 필터 조건이 유일한 입력입니다. 

## <a name="defining-filters"></a>필터 정의

필터는 OData 식이며, Cognitive Search에서 지원하는 [필터 구문](search-query-odata-filter.md)에 더 잘 표현됩니다.

**검색** 작업마다 하나의 필터를 지정할 수 있지만 필터 자체에는 여러 필드와 여러 조건을 포함할 수 있으며, **ismatch** 함수를 사용하면 여러 표현식을 사용할 수도 있습니다. 여러 부분으로 구성된 필터 식에서 (연산자 우선 순위 규칙에 따라) 조건자를 순서에 관계없이 지정할 수 있습니다. 특정한 순서로 조건자를 다시 정렬해도 성능에 별다른 도움은 되지 않습니다.

필터 식에 대한 한도는 요청에 대한 최대 크기 제한입니다. 필터를 포함한 전체 요청은 POST의 경우 최대 16MB, GET의 경우 최대 8KB가 될 수 있습니다. 필터 식의 절 개수에도 제한이 있습니다. 경험에 따르면 수백 개의 절이 있는 경우 제한에 도달할 위험이 있습니다. 애플리케이션을 설계할 때 필터의 크기를 무제한으로 생성하지 않는 것이 좋습니다.

다음 예제에서는 여러 API의 정형 필터 정의를 나타냅니다.

```http
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-patterns"></a>필터 패턴

다음 예제에는 필터 시나리오에 대한 여러 디자인 패턴이 나와 있습니다. 자세한 내용은 [OData 식 구문 > 예제](./search-query-odata-filter.md#examples)를 참조하세요.

+ 독립 실행형 **$filter** 는 쿼리 문자열 없이, 필터 식이 관심 있는 문서를 정규화할 수 있을 때 유용합니다. 쿼리 문자열이 없으면 어휘 또는 언어 분석, 점수 매기기 및 순위 지정 등이 없으며 검색 문자열은 "모든 문서 일치"를 의미하는 별표입니다.

  ```json
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ 쿼리 문자열과 **$filter** 의 조합에서 필터가 하위 집합을 만들면 쿼리 문자열이 필터링된 하위 집합에 대해 전체 텍스트 검색에 용어 입력을 제공합니다. 용어를 추가(이동 거리 극장) 하면 결과에 검색 점수가 도입되고, 여기에서 용어와 가장 일치하는 문서는 더 높은 순위를 갖습니다. 쿼리 문자열과 함께 필터를 사용하는 것이 가장 일반적인 코드 패턴입니다.

  ```json
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```http
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  을 통해 전체 텍스트 검색을 대신 를 사용하는 필터와 결합할 수도 `search.ismatchscoring` `and` 있지만 이는 검색 `or` 요청에서 및 매개 변수를 사용하는 것과 기능적으로 `search` `$filter` 동일합니다. 예를 들어 다음 두 쿼리는 동일한 결과를 생성합니다.

  ```http
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

## <a name="field-requirements-for-filtering"></a>필터링을 위한 필드 요구 사항

REST API 필터링 가능은 단순 필드에 대해 기본적으로 *설정됩니다.* 필터링 가능 필드는 인덱스 크기가 늘어나기 때문에 필터에서 실제로 사용하지 않는 필드에 대해서는 `"filterable": false`로 설정합니다. 필드 정의 설정에 대한 자세한 내용은 [Create Index](/rest/api/searchservice/create-index)(인덱스 만들기)를 참조하세요.

.NET SDK에서는 필터링 가능이 기본적으로 *해제* 되어 있습니다. 해당 [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) 개체의 [IsFilterable 속성을](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 로 설정하여 필드를 필터링 가능하게 만들 수 `true` 있습니다. 아래 예제에서 특성은 `BaseRate` 인덱스 정의에 매핑되는 모델 클래스의 속성에 설정됩니다.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>필터링 가능한 기존 필드 만들기

필터링 가능하도록 기존 필드를 수정할 수 없습니다. 대신 새 필드를 추가하거나 인덱스를 다시 작성해야 합니다. 인덱스 또는 재생 필드를 다시 작성하는 방법에 대한 자세한 내용은 [Azure Cognitive Search 인덱스를 다시 작성하는 방법](search-howto-reindex.md)을 참조하세요.

## <a name="text-filter-fundamentals"></a>텍스트 필터 기본 사항

텍스트 필터는 필터에 제공하는 리터럴 문자열과 문자열 필드를 일치시킵니다.: `$filter=Category eq 'Resort and Spa'`

문자열로 구성된 텍스트 필터의 경우와 달리 어휘 분석 또는 단어 분리가 없으므로, 정확한 일치 항목만 비교합니다. 예를 들어, 필드 *f* 에 "sunny day"가 포함되어 있다고 가정하고, `$filter=f eq 'Sunny'`은 일치하지 않지만 `$filter=f eq 'sunny day'`는 일치합니다. 

텍스트 문자열은 대/소문자를 구분합니다. 대문자 단어에 대해 소문자를 검색하지 않습니다.: `$filter=f eq 'Sunny day'`은 “sunny day”를 찾지 않습니다.

### <a name="approaches-for-filtering-on-text"></a>텍스트 필터링 접근 방법

| 접근 방식 | Description | 사용 시기 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 구분된 문자열 목록에 대해 필드를 일치시키는 함수입니다. | 문자열 필드와 일치해야 하는 원시 텍스트 값이 많은 필터 및 [보안 필터](search-security-trimming-for-azure-search.md)에 권장됩니다. **search.in** 함수는 속도를 위해 디자인되었으며, `eq` 및 `or`를 사용하여 각 문자열과 필드를 명시적으로 비교하는 것 보다 훨씬 빠릅니다. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 동일한 필터 식에서 전체 텍스트 검색 작업과 엄격한 부울 필터 작업을 혼합할 수 있게 해주는 함수입니다. | 하나의 요청에서 여러 검색 필터 조합을 사용하려면 **search.ismatch** (또는 해당하는 해당 점수, **search.ismatchscoring**)를 사용합니다. 크기가 더 큰 문자열 내에서 부분적으로 문자열을 필터링하기 위해 *contains* 필터에 대해 이를 사용할 수도 있습니다. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 필드, 연산자 및 값으로 구성된 사용자 정의 식입니다. | 문자열 필드와 문자열 값 사이에 정확히 일치하는 항목을 찾으려면 이를 사용합니다. |

## <a name="numeric-filter-fundamentals"></a>숫자 필터 기본 사항

숫자 필드는 전체 텍스트 검색의 컨텍스트에서 `searchable`이 아닙니다. 문자열만 전체 텍스트 검색이 가능합니다. 예를 들어, 검색어로 99.99를 입력하면 $99.99로 책정된 항목이 반환되지 않고 문서의 문자열 필드에 숫자 99가 있는 항목이 표시됩니다. 따라서 숫자 데이터가 있는 경우 범위, 패싯, 그룹 등을 포함하여 필터에 사용한다고 가정합니다. 

숫자 필드(가격, 크기, SKU, ID)를 포함하는 문서는 필드가 `retrievable`로 표시되면 검색 결과에서 해당 값을 제공합니다. 여기서 핵심은 전체 텍스트 검색 자체가 숫자 필드 유형에는 적용되지 않는다는 것입니다.

## <a name="next-steps"></a>다음 단계

먼저 포털의 **검색 탐색기** 에서 **$filter** 매개 변수를 사용하여 쿼리를 제출합니다. 필터링된 다음 쿼리를 검색 창에 붙여넣으면 [부동산 샘플 인덱스](search-get-started-portal.md)에서 해당 쿼리에 대한 흥미로운 결과를 제공합니다.

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

더 많은 예제를 사용하려면 [OData 필터 식 구문 > 예제](./search-query-odata-filter.md#examples)를 참조하세요.

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 검색 REST API](/rest/api/searchservice/search-documents)
+ [단순 쿼리 구문](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 쿼리 구문](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [지원되는 데이터 형식](/rest/api/searchservice/supported-data-types)