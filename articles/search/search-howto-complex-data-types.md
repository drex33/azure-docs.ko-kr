---
title: 복합 데이터 형식을 모델링하는 방법
titleSuffix: Azure Cognitive Search
description: ComplexType 및 컬렉션 데이터 형식을 사용하여 Azure Cognitive Search 인덱스에서 중첩 또는 계층적 데이터 구조를 모델링할 수 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: bcd0819ce2720597c6e9f2435d37fe9276d595cd
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222276"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure Cognitive Search에서 복합 데이터 형식을 모델링하는 방법

Azure Cognitive Search 인덱스를 채우는 데 사용되는 외부 데이터 세트는 많은 셰이프로 제공될 수 있습니다. 계층적 또는 중첩된 하위 구조체를 포함하는 경우도 있습니다. 예제에는 한 고객에 여러 주소, 단일 SKU에 여러 색과 크기, 한 권의 책에 여러 저자 등이 포함될 수 있습니다. 모델링 용어에서 *complex*, *compound*, *composite* 또는 *aggregate* 데이터 형식이라고 하는 구조를 볼 수 있습니다. 이 개념에 대해 Azure Cognitive Search에서 사용하는 용어는 **복합(complex) 형식** 입니다. Azure Cognitive Search에서 복합 형식은 **복합 필드** 를 사용하여 모델링됩니다. 복합 필드는 다른 복합 형식을 포함하여 모든 데이터 형식일 수 있는 자식(하위 필드)을 포함하는 필드입니다. 이는 프로그래밍 언어에서 구조화된 데이터 형식과 비슷한 방식으로 작동합니다.

복합 필드는 데이터 형식에 따라 문서의 단일 개체 또는 개체의 배열을 나타냅니다. `Edm.ComplexType` 형식의 필드는 단일 개체를 나타내고, `Collection(Edm.ComplexType)` 형식의 필드는 개체의 배열을 나타냅니다.

Azure Cognitive Search는 기본적으로 복합 형식 및 컬렉션을 지원합니다. 이러한 형식을 사용하여 Azure Cognitive Search 인덱스에서 거의 모든 JSON 구조를 모델링할 수 있습니다. 이전 버전의 Azure Cognitive Search API에서는 일반 행 집합만 가져올 수 있었습니다. 최신 버전에서는 이제 인덱스가 원본 데이터와 더 밀접하게 일치합니다. 즉, 원본 데이터에 복합 형식이 있는 경우 인덱스에도 복합 형식이 있을 수 있습니다.

시작하려면 Azure Portal의 **데이터 가져오기** 마법사에서 로드할 수 있는 [호텔 데이터 세트](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)를 권장합니다. 마법사는 원본의 복합 형식을 검색하고 검색된 구조를 기반으로 인덱스 스키마를 제안합니다.

> [!Note]
> 복합 형식에 대한 지원은 일반적으로부터 `api-version=2019-05-06`부터 사용할 수 있습니다. 
>
> 검색 솔루션이 컬렉션의 일반 데이터 세트에 대한 이전 해결 방법을 기반으로 하는 경우 최신 API 버전에서 지원되는 복합 형식을 포함하도록 인덱스를 변경해야 합니다. API 버전을 업그레이드하는 방법에 대한 자세한 내용은 [최신 REST API 버전으로 업그레이드](search-api-migration.md) 또는 [최신 .NET SDK 버전으로 업그레이드](search-dotnet-sdk-migration-version-9.md)를 참조하세요.

## <a name="example-of-a-complex-structure"></a>복합 구조의 예제

다음 JSON 문서는 간단한 필드와 복합 필드로 구성됩니다. `Address` 및 `Rooms`와 같은 복합 필드에는 하위 필드가 있습니다. `Address`에는 문서의 단일 개체이므로 해당 하위 필드에 대한 단일 값 세트가 있습니다. 반면, `Rooms`에는 컬렉션의 각 개체에 대해 하나씩, 하위 필드에 대한 값 세트가 여러 개 있습니다.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"],
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

## <a name="indexing-complex-types"></a>복합 형식 인덱싱

인덱싱 중에 단일 문서 내의 모든 복합 컬렉션에서 최대 3000개의 요소를 가질 수 있습니다. 복합 컬렉션의 요소는 해당 컬렉션의 멤버이므로 객실의 경우(호텔 예제의 유일한 복합 컬렉션) 각 객실은 요소입니다. 위의 예제에서 “Secret Point Motel”에 500개의 객실이 있으면 호텔 문서에는 500개 객실 요소가 있습니다. 중첩된 복합 컬렉션의 경우 외부(부모) 요소 외에도 각 중첩된 요소가 계산됩니다.

이 제한은 복합 형식(예: 주소) 또는 문자열 컬렉션(예: 태그)이 아닌 복합 컬렉션에만 적용됩니다.

## <a name="creating-complex-fields"></a>복합 필드 만들기

모든 인덱스 정의와 마찬가지로, 포털, [REST API](/rest/api/searchservice/create-index) 또는 [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindex)를 사용하여 복합 형식을 포함하는 스키마를 만들 수 있습니다. 

다음 예제에서는 간단한 필드, 컬렉션 및 복합 형식을 사용하는 JSON 인덱스 스키마를 보여 줍니다. 복합 형식 내에서 각 하위 필드는 형식을 가지며 최상위 필드와 마찬가지로 특성을 가질 수도 있습니다. 스키마는 위의 예제 데이터에 해당합니다. `Address`는 컬렉션이 아닌 복합 필드입니다(호텔에는 주소가 하나임). `Rooms`는 복합 컬렉션 필드입니다(호텔에는 많은 객실이 있음).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>복합 필드 업데이트

일반적으로 필드에 적용되는 모든 [다시 인덱스 규칙](search-howto-reindex.md)은 복합 필드에도 적용됩니다. 여기에서 몇 가지 주요 규칙을 재작성하고, 복합 형식에 필드를 추가하는 경우 인덱스를 다시 빌드할 필요가 없지만 대부분의 수정 작업이 수행됩니다.

### <a name="structural-updates-to-the-definition"></a>정의에 대한 구조적 업데이트

인덱스를 다시 빌드할 필요 없이 언제든지 복합 필드에 새 하위 필드를 추가할 수 있습니다. 예를 들어 인덱스에 최상위 필드를 추가하는 것처럼 `Address`에 “ZipCode”를 또는 `Rooms`에 “Amenities”를 추가하는 것이 허용됩니다. 기존 문서에는 데이터를 업데이트하여 명시적으로 필드를 채울 때까지 새 필드에 대한 null 값이 있습니다.

복합 형식 내에서 각 하위 필드는 형식을 가지며 최상위 필드와 마찬가지로 특성을 가질 수도 있습니다.

### <a name="data-updates"></a>데이터 업데이트

`upload` 작업을 사용하여 인덱스에서 기존 문서를 업데이트하는 작업은 복합 필드와 단순 필드에 대해 동일한 방식으로 작동합니다. 즉, 모든 필드가 바뀝니다. 그러나 `merge`(또는 기존 문서에 적용되는 경우 `mergeOrUpload`)는 모든 필드에서 동일하게 작동하지 않습니다. 특히 `merge`는 컬렉션 내에서 요소를 병합하는 것을 지원하지 않습니다. 이 제한 사항은 기본 형식의 컬렉션 및 복합 컬렉션에 대해 존재합니다. 컬렉션을 업데이트하려면 전체 컬렉션 값을 검색하고 변경한 다음, 인덱스 API 요청에 새 컬렉션을 포함해야 합니다.

## <a name="searching-complex-fields"></a>복합 필드 검색

자유 형식 검색 식은 복합 형식에서 예상대로 작동합니다. 문서에서 검색 가능한 필드 또는 하위 필드가 일치하는 경우 문서 자체는 일치 항목입니다.

[Lucene 구문](query-lucene-syntax.md)에서 가능한 한 여러 용어와 연산자가 있고 일부 용어에 필드 이름이 지정된 경우 쿼리에 더 많은 미묘한 차이가 발생합니다. 예를 들어 이 쿼리는 Address 필드의 두 하위 필드에 대해 두 개의 용어 “Portland” 및 “OR”를 일치시키려고 합니다.

> `search=Address/City:Portland AND Address/State:OR`

이와 같은 쿼리는 필터와 달리 전체 텍스트 검색과는 *상관 관계가 없습니다*. 필터에서 복합 컬렉션의 하위 필드에 대한 쿼리는 [`any` 또는 `all`](search-query-odata-collection-operators.md)의 범위 변수를 사용하여 상호 관련됩니다. 위의 Lucene 쿼리는 오리건의 다른 도시와 함께 “Portland, Maine(메인 주 포틀랜드)”와 “Portland, Oregon(오리건 주 포틀랜드)”를 모두 포함하는 문서를 반환합니다. 이는 각 절이 전체 문서에 있는 해당 필드의 모든 값에 적용되므로 발생합니다. 따라서 “현재 하위 문서”의 개념이 없다는 것입니다. 이에 대한 자세한 내용은 [Azure Cognitive Search의 OData 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.

## <a name="selecting-complex-fields"></a>복합 필드 선택

`$select` 매개 변수는 검색 결과에서 반환되는 필드를 선택하는 데 사용됩니다. 이 매개 변수를 사용하여 복합 필드의 특정 하위 필드를 선택하려면 부모 필드와 하위 필드를 슬래시(`/`)로 구분하여 포함합니다.

> `$select=HotelName, Address/City, Rooms/BaseRate`

필드는 검색 결과에서 원하는 경우 인덱스에서 조회 가능으로 표시되어야 합니다. 조회 가능으로 표시된 필드만 `$select` 문에서 사용할 수 있습니다.

## <a name="filter-facet-and-sort-complex-fields"></a>필터, 패싯 및 정렬 복합 필드

필터링 및 필드 지정 검색에 사용되는 것과 동일한 [OData 경로 구문](query-odata-filter-orderby-syntax.md)도 검색 요청에서 필드를 패싯, 정렬 및 선택하는 데 사용할 수 있습니다. 복합 형식의 경우 정렬 가능 또는 패싯 가능으로 표시할 수 있는 하위 필드를 제어하는 규칙이 적용됩니다. 이러한 규칙에 대한 자세한 내용은 [인덱스 API 참조 만들기](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="faceting-sub-fields"></a>패싯 하위 필드

`Edm.GeographyPoint` 또는 `Collection(Edm.GeographyPoint)` 형식이 아닌 경우에는 모든 하위 필드를 패싯 가능으로 표시할 수 있습니다.

패싯 결과에서 반환되는 문서 개수는 부모 문서(호텔)에 대해 계산되며, 이는 복합 컬렉션(객실)의 하위 문서가 아닙니다. 예를 들어 호텔에 “suite” 형식의 객실이 20개 있다고 가정해 보겠습니다. 이 패싯 매개 변수 `facet=Rooms/Type`을 지정하는 경우 패싯 수는 객실에 대한 20이 아닌 호텔에 대한 1이 됩니다.

### <a name="sorting-complex-fields"></a>복합 필드 정렬

정렬 작업은 문서(호텔)에 적용되고 하위 문서(객실)에는 적용되지 않습니다. 객실과 같은 복합 형식 컬렉션을 사용할 경우에는 객실을 정렬할 수 없다는 점을 알아야 합니다. 실제로 컬렉션을 정렬할 수 없습니다.

정렬 작업은 필드가 단순 필드이든 복합 형식의 하위 필드이든 상관없이 필드에 문서당 단일 값이 있으면 작동합니다. 예를 들어, `Address/City`는 호텔당 주소가 하나뿐이므로 정렬 가능으로 허용됩니다. 따라서 `$orderby=Address/City`는 도시별로 호텔을 정렬합니다.

### <a name="filtering-on-complex-fields"></a>복합 필드에서 필터링

필터 식에서 복합 필드의 하위 필드를 참조할 수 있습니다. 패싯, 정렬 및 필드 선택에 사용되는 것과 동일한 [OData 경로 구문](query-odata-filter-orderby-syntax.md)을 사용하면 됩니다. 예를 들어 다음 필터는 캐나다에 있는 모든 호텔을 반환합니다.

> `$filter=Address/Country eq 'Canada'`

복합 컬렉션 필드를 필터링하기 위해 [`any` 및 `all` 연산자](search-query-odata-collection-operators.md)에 **람다 식** 을 사용할 수 있습니다. 이 경우 람다 식의 **범위 변수** 는 하위 필드가 있는 개체입니다. 표준 OData 경로 구문을 사용하여 이러한 하위 필드를 참조할 수 있습니다. 예를 들어 다음 필터는 하나 이상의 디럭스 객실 및 모든 금연 객실을 포함하는 모든 호텔을 반환합니다.

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

최상위 단순 필드와 마찬가지로, 인덱스 정의에서 **필터링 가능** 특성이 `true`로 설정된 경우에만 복합 필드의 단순 하위 필드를 필터에 포함할 수 있습니다. 자세한 내용은 [인덱스 API 참조 만들기](/rest/api/searchservice/create-index)를 참조하세요.

## <a name="next-steps"></a>다음 단계

**데이터 가져오기** 마법사에서 [호텔 데이터 세트](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)를 사용해 봅니다. 데이터에 액세스하려면 추가 정보에 제공된 Cosmos DB 연결 정보가 필요합니다.

이 정보를 사용한 마법사의 첫 번째 단계는 새 Azure Cosmos DB 데이터 원본을 만드는 것입니다. 마법사에서는 더 나아가 대상 인덱스 페이지에 가서 복합 형식이 포함된 인덱스가 표시됩니다. 이 인덱스를 만들고 로드한 다음, 쿼리를 실행하여 새 구조를 파악합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 가져오기, 인덱싱 및 쿼리를 위한 포털 마법사](search-get-started-portal.md)
