---
title: REST API 버전 업그레이드
titleSuffix: Azure Cognitive Search
description: API 버전의 차이를 검토하고 기존 코드를 최신 Azure Cognitive Search 서비스 REST API 버전으로 마이그레이션하는 데 필요한 작업을 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: d925b4542a5d03340558f302a2251b4c191de2e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601295"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Azure Cognitive Search에서 최신 REST API로 업그레이드

[**Search REST API**](/rest/api/searchservice/)의 이전 버전을 사용하는 경우 이 문서를 통해 최신 일반 공급 API 버전 **2020-06-30** 으로 애플리케이션을 업그레이드할 수 있습니다.

버전 2020-06-30에는 중요한 새 기능([지식 저장소](knowledge-store-concept-intro.md))이 포함되어 있으며 몇 가지 사소한 동작 변경 내용이 도입되었습니다. 따라서 이전 버전(2019-05-06)에서 업그레이드하는 경우, 이 버전이 대부분 이전 버전과 호환되므로 코드 변경을 최소화해야 합니다.

> [!NOTE]
> 검색 서비스는 이전 버전을 포함한 다양한 REST API 버전을 지원합니다. 이러한 API 버전을 계속 사용할 수 있지만 새 기능에 액세스할 수 있도록 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다. 시간이 지남에 따라 REST API의 가장 오래된 버전은 사용되지 않으며 [더 이상 지원되지 않습니다](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>업그레이드하는 방법

새 버전으로 업그레이드하는 경우 버전 번호를 제외하고 코드를 변경하지 않아도 됩니다. 코드를 변경해야 하는 유일한 경우는 다음과 같습니다.

* 인식할 수 없는 속성이 API 응답에 반환되는 경우 코드가 실패합니다. 기본적으로 애플리케이션은 이해하지 못하는 속성을 무시합니다.

* 코드는 API 요청을 보관하고 새 API 버전으로 다시 전송하려 합니다. 예를 들어 애플리케이션이 검색 API에서 반환된 연속 토큰을 보관하는 경우 이런 현상이 발생할 수 있습니다(자세한 내용은 [검색 API 참조](/rest/api/searchservice/Search-Documents)의 `@search.nextPageParameters`를 참조).

* 코드는 API 2019-05-06 이전 버전을 참조하며 해당 릴리스의 호환성이 손상되는 변경 내용 중 하나 이상을 적용받습니다. [2019-05-06으로 업그레이드](#upgrade-to-2019-05-06) 섹션에서 자세한 정보를 제공합니다. 

이러한 경우에 해당한다면 코드를 적절하게 변경해야 합니다. 새 버전에 추가된 기능을 사용하기 시작할 수 있지만 변경 사항은 필요하지 않습니다.

## <a name="upgrade-to-2020-06-30"></a>2020-06-30으로 업그레이드

2020-06-30 버전은 REST API의 새로운 일반 공급 릴리스입니다. 한 가지 호환성이 손상되는 변경 내용과 여러 가지 동작 차이가 있습니다. 

현재 이 API 버전에서 일반 공급되는 기능은 다음과 같습니다.

* 기술 세트를 통해 생성되고, 다운스트림 분석 및 다른 애플리케이션을 통한 처리를 위해 생성된 보강 콘텐츠의 영구 스토리지인 [지식 저장소](knowledge-store-concept-intro.md). 이 기능을 사용할 경우 인덱서 기반 AI 보강 파이프라인이 검색 인덱스 외에 지식 저장소도 채울 수 있습니다. 이 기능의 미리 보기 버전은 일반 공급되는 버전과 동일합니다. 유일하게 필요한 코드 변경은 api-version을 수정하는 것입니다.

### <a name="breaking-change"></a>주요 변경 내용

이전 API 버전에 대해 작성된 기존 코드는 다음 기능이 포함된 경우 api-version=2020-06-30 이상에서 중단됩니다.

* 필터 식에서 Edm.Date 리터럴(`2020-12-12`와 같이 연-월-일로 구성된 날짜)은 Edm.DateTimeOffset 형식(`2020-12-12T00:00:00Z`)을 따라야 합니다. 이 변경은 표준 시간대 차이로 인해 발생하는 잘못되었거나 예기치 않은 쿼리 결과를 처리하는 데 필요했습니다.

### <a name="behavior-changes"></a>동작 변경

* [BM25 순위 지정 알고리즘](index-ranking-similarity.md)은 이전 순위 지정 알고리즘을 최신 기술로 대체합니다. 새 서비스에서 자동으로 이 알고리즘을 사용합니다. 기존 서비스의 경우 새 알고리즘을 사용하도록 매개 변수를 설정해야 합니다.

* 이 버전에서는 null 값에 대해 정렬된 결과가 변경되었고, 정렬이 `asc`이면 null 값이 처음에 표시되고 정렬이 `desc`이면 마지막에 표시됩니다. null 값을 정렬하는 방법을 처리하는 코드를 작성한 경우 이 변경 내용을 알고 있어야 합니다.

## <a name="upgrade-to-2019-05-06"></a>2019-05-06으로 업그레이드

2019-05-06 버전은 REST API의 이전 일반 공급 릴리스입니다. 이 API 버전에서 일반 공급되는 기능은 다음과 같습니다.

* [자동 완성](index-add-suggesters.md) - 부분적으로 지정된 용어 입력을 완성하는 미리 입력 기능입니다.
* [복합 형식](search-howto-complex-data-types.md) - 검색 인덱스의 구조화된 개체 데이터에 대한 네이티브 지원을 제공합니다.
* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드](search-howto-index-json-blobs.md)는 JSON 엔터티마다 하나의 검색 문서를 만들고, 이 문서는 새로운 행으로 구분됩니다.
* [AI 보강](cognitive-search-concept-intro.md)은 Cognitive Services의 AI 보강 엔진을 활용하는 인덱싱을 제공합니다.

### <a name="breaking-changes"></a>호환성이 손상되는 변경

이전 API 버전에 대해 작성된 기존 코드는 다음 기능이 포함된 경우 api-version=2019-05-06 이상에서 중단됩니다.

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB - datasource의 인덱서는 이제 "type": "cosmosdb"

[Cosmos DB 인덱서](search-howto-index-cosmosdb.md )를 사용 중인 경우 `"type": "documentdb"`를 `"type": "cosmosdb"`로 변경해야 합니다.

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>인덱서 실행 결과 오류에 더 이상 상태가 표시되지 않음

이전에는 인덱서 실행에 대한 오류 구조에 `status` 요소가 있었습니다. 이 요소는 유용한 정보를 제공하지 않기 때문에 제거되었습니다.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>인덱서 데이터 원본 API가 더 이상 연결 문자열을 반환하지 않음

API 버전 2019-05-06 및 2019-05-06-Preview부터 데이터 원본 API가 더 이상 REST 작업에 대한 응답에 연결 문자열을 반환하지 않습니다. 이전 API 버전에서는 POST를 사용하여 만든 데이터 원본에 대해 Azure Cognitive Search가 **201** 을 반환한 다음, OData 응답을 반환했습니다. 여기에는 일반 텍스트 형식의 연결 문자열이 포함되어 있었습니다.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>명명된 엔터티 인식의 인식 기술이 이제 사용되지 않음

코드에서 [이름 엔터티 인식](cognitive-search-skill-named-entity-recognition.md) 기술을 호출하면 호출이 실패합니다. 대체 기능은 [엔터티 인식 기술(V3)](cognitive-search-skill-entity-recognition-v3.md)입니다. [사용되지 않는 인지 검색 기술](cognitive-search-skill-deprecated.md)의 권장 사항에 따라 지원되는 기술로 마이그레이션하세요.

### <a name="upgrading-complex-types"></a>복합 형식 업그레이드

API 버전 2019-05-06에 복합 형식에 대한 공식 지원이 추가되었습니다. 코드에서 2017-11-11-Preview 또는 2016-09-01-Preview와 같은 복합 형식에 대한 이전 권장 사항을 구현한 경우, 2019-05-06 버전부터 도입된 새로운 제한 사항과 변경된 제한 사항에 주의해야 합니다.

+ 하위 필드 수준 및 인덱스당 복합 컬렉션 수에 대한 제한 사항이 감소했습니다. 미리 보기 API 버전을 사용하여 이러한 제한 사항을 초과하는 인덱스를 만든 경우, API 버전 2019-05-06을 사용하여 해당 인덱스를 업데이트하거나 다시 만들려고 하면 실패합니다. 이 경우 새 제한 사항에 맞게 스키마를 다시 설계한 다음, 인덱스를 다시 작성해야 합니다.

+ API 버전 2019-05-06부터 문서당 복합 컬렉션의 요소 수에 대한 새로운 제한 사항이 있습니다. 미리 보기 API 버전을 사용하여 이러한 제한 사항을 초과하는 인덱스를 문서에 만든 경우, API 버전 2019-05-06을 사용하여 해당 데이터의 인덱스를 다시 작성하려고 하면 실패합니다. 이 경우 데이터의 인덱스를 다시 작성하기 전에 문서당 복합 컬렉션 요소 수를 줄여야 합니다.

자세한 내용은 [Azure Cognitive Search의 서비스 제한 사항](search-limits-quotas-capacity.md)을 참조하세요.

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>이전 복합 형식 구조를 업그레이드하는 방법

코드가 이전 미리 보기 API 버전 중 하나에서 복합 형식을 사용 하는 경우 다음과 같은 인덱스 정의 형식을 사용할 수 있습니다.

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

인덱스 필드를 정의하는 트리와 유사한 최신 형식은 API 버전 2017-11-11-Preview에서 도입되었습니다. 새 형식에서 각 복합 필드에는 하위 필드가 정의된 필드 컬렉션이 있습니다. API 버전 2019-05-06에서 이 새 형식은 배타적으로 사용되며, 이전 형식을 사용하여 인덱스를 만들거나 업데이트하려는 시도가 실패합니다. 이전 형식을 사용하여 생성된 인덱스가 있는 경우 API 버전 2017-11-11-Preview를 사용하여 새 형식으로 업데이트해야 API 버전 2019-05-06을 사용하여 관리할 수 있습니다.

API 버전 2017-11-11-Preview로 다음 단계를 수행하여 "플랫" 인덱스를 새 형식으로 업데이트할 수 있습니다.

1. GET 요청을 수행하여 인덱스를 검색합니다. 이미 새 형식이면 완료된 것입니다.

2. 인덱스를 "플랫" 형식에서 새 형식으로 변환합니다. 이 문서의 작성 시점을 기준으로 현재 사용할 수 있는 샘플 코드는 없으므로 이에 대한 코드를 작성해야 합니다.

3. PUT 요청을 수행하여 인덱스를 새 형식으로 업데이트합니다. 인덱스 업데이트 API에서 허용되지 않기 때문에 필드의 검색 가능성/필터링 가능성 같은 인덱스의 다른 세부 정보는 변경하지 않도록 해야 합니다.

> [!NOTE]
> Azure Portal에서 이전 "플랫" 형식을 사용하여 생성된 인덱스는 관리할 수 없습니다. 가급적 빨리 "플랫" 표현에서 "트리" 표현으로 인덱스를 업그레이드하세요.

## <a name="next-steps"></a>다음 단계

검색 REST API 참조 설명서를 검토합니다. 문제가 발생하는 경우 [Stack Overflow](https://stackoverflow.com/)에서 도움을 요청하거나 [고객 지원팀](https://azure.microsoft.com/support/community/?product=search)에 문의하세요.

> [!div class="nextstepaction"]
> [Search Service REST API 참조](/rest/api/searchservice/)
