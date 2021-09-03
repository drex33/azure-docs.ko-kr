---
title: 유사성 및 점수 매기기 개요
titleSuffix: Azure Cognitive Search
description: 유사성 및 점수 매기기의 개념과 개발자가 점수 매기기 결과를 사용자 지정하기 위해 수행할 수 있는 작업을 설명합니다.
author: puneet-hariharan-MSFT
ms.author: puhariharan
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cacf6097234a5a2c7ec5261623fe653e509b8d9f
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112981433"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure Cognitive Search의 유사성 및 점수 매기기

이 문서에서는 쿼리와 가장 관련성이 높은 일치하는 문서를 확인하기 위해 Azure Cognitive Search에서 사용하는 두 가지 유사성 순위 알고리즘을 설명합니다. 또한 이 문서에서는 관련된 두 가지 기능인 ‘점수 매기기 프로필’(검색 점수 조정 기준)과 *featuresMode* 매개 변수(추가 세부 정보 표시를 위해 검색 점수의 압축 풀기)를 소개합니다.

> [!NOTE]
> [의미 체계 순위 재지정 알고리즘](semantic-ranking.md) 세 가지 중 하나는 현재 퍼블릭 미리 보기 상태입니다. 자세한 내용은 [의미 체계 검색 개요](semantic-search-overview.md)부터 확인하세요.

## <a name="similarity-ranking-algorithms"></a>유사성 순위 알고리즘

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘을 지원합니다.

| 알고리즘 | 점수 | 가용성 |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | 2020년 7월 15일까지 모든 검색 서비스에서 사용합니다. |
| BM25Similarity | @search.score | 7월 15일 이후 만들어진 모든 검색 서비스에서 사용합니다. 클래식을 기본으로 사용하는 이전 서비스는 [BM25로 옵트인](index-ranking-similarity.md)할 수 있습니다. |

클래식과 BM25는 모두 TF-IDF와 유사한 검색 함수로, 각각의 문서-쿼리 쌍에 대한 관련성 점수를 계산하는 데 TF(용어 빈도)와 IDF(역 문서 빈도)를 변수로 사용하며, 이후 해당 쌍은 순위 지정에 사용됩니다. BM25는 개념적으로 클래식과 유사하지만 확률적 정보 검색을 기반으로 개선됩니다. BM25는 사용자가 일치하는 용어의 용어 빈도를 통해 관련성 점수가 확장되는 방식을 결정할 수 있도록 하는 등의 고급 사용자 지정 옵션을 함께 제공합니다.

다음 비디오 세그먼트는 Azure Cognitive Search에서 사용되는 일반적으로 활용할 수 있는 순위 알고리즘에 대한 설명을 빠르게 제공합니다. 자세한 배경 정보는 전체 비디오를 시청하여 확인할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>관련성 채점

점수 매기기는 전체 텍스트 검색 쿼리에 대해 검색 결과에 반환된 모든 항목의 검색 점수를 계산하는 것입니다. 점수는 현재 쿼리의 컨텍스트에서 항목의 관련성을 나타내는 표시기입니다. 점수가 높을수록 항목의 관련성도 높습니다. 검색 결과에서 항목은 각 항목에 대해 계산된 검색 점수를 기준으로 높은 순위부터 낮은 순위로 정렬됩니다. 점수는 모든 문서에서 "@search.score" 응답으로 반환됩니다.

기본적으로 응답에 상위 50개 항목이 반환되지만 **$top** 매개 변수를 사용하여 반환되는 항목 수를 늘리거나 줄일 수 있으며(최대 1,000개 항목) **$skip** 매개 변수를 사용하여 다음 결과 세트를 가져올 수 있습니다.

검색 점수는 쿼리와 데이터의 통계 속성에 따라 계산됩니다. Azure Cognitive Search에서는 검색 용어와 일치하는 문서를 찾은 다음([searchMode](/rest/api/searchservice/search-documents#query-parameters)에 따라 일부 또는 전체), 검색 용어가 많이 포함된 문서에 높은 점수를 할당합니다. 해당 용어가 데이터 인덱스에는 거의 없지만 해당 문서 내에서는 자주 나오는 경우 검색 점수가 더 높아집니다. 이와 같은 관련성 계산 방식의 기준을 *TF-IDF*, 즉 용어 빈도와 문서 빈도 반비례라고 합니다.

전체 결과 집합에서 검색 점수 값이 반복될 수 있습니다. 여러 적중 항목의 검색 점수가 같은 경우 동일 점수의 항목 순서는 정의되지 않았으므로 항목이 안정적으로 정렬되지 않습니다. 쿼리를 다시 실행하면, 특히 무료 서비스를 사용하거나 여러 복제본이 청구 가능한 서비스를 사용하는 경우 항목 이동 위치가 표시될 수 있습니다. 즉, 두 항목의 점수가 같은 경우 어떤 항목이 먼저 표시되는지 보장되지 않습니다.

반복 점수 간에 연결을 끊으려는 경우 **$orderby** 절을 먼저 점수를 기준으로 정렬한 다음, 정렬 가능한 다른 필드를 기준으로 정렬합니다(예: `$orderby=search.score() desc,Rating desc`). 자세한 내용은 [$orderby](search-query-odata-orderby.md)를 참조하세요.

> [!NOTE]
> `@search.score = 1.00`은 점수가 매겨지지 않거나 순위가 지정되지 않은 결과 세트를 나타냅니다. 점수는 모든 결과에서 균일합니다. 쿼리 양식이 유사 항목 검색, 와일드카드 또는 regex 쿼리, **$filter** 식인 경우 점수가 없는 결과가 나타납니다.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>통계 및 고정 세션 점수 매기기

스케일링 성능을 위해 Azure Cognitive Search는 분할 프로세스를 통해 각 인덱스를 수평으로 분산합니다. 즉, [인덱스 부분이 물리적으로 분리됩니다](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

기본적으로 문서 점수는 *분할된 데이터베이스 내* 데이터의 통계 속성에 따라 계산됩니다. 이 방법은 일반적으로 많은 양의 데이터 모음이 있을 때는 문제가 되지 않으며, 모든 분할된 데이터베이스의 정보를 기준으로 점수를 계산해야 하는 경우보다 더 나은 성능을 제공합니다. 즉, 이러한 성능 최적화를 사용하면 매우 유사한 두 개의 문서(또는 동일한 문서)가 서로 다른 분할된 데이터베이스에 속하게 될 때 서로 다른 관련성 점수를 갖게 될 수 있습니다.

모든 분할된 데이터베이스의 통계 속성에 따라 점수를 계산하려면 [쿼리 매개 변수](/rest/api/searchservice/search-documents)로 *scoringStatistics=global* 을 추가하여(또는 [쿼리 요청](/rest/api/searchservice/search-documents)의 본문 매개 변수로 *"scoringStatistics": "global"* 추가) 이렇게 할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

scoringStatistics를 사용하면 동일한 복제본의 모든 분할된 데이터베이스가 동일한 결과를 제공합니다. 즉, 항상 인덱스에 대한 최신 변경 내용으로 업데이트되므로 복제본마다 서로 약간씩 다를 수 있습니다. 일부 시나리오에서는 사용자가 "쿼리 세션" 중에 보다 일관된 결과를 얻게 하려고 할 수 있습니다. 이러한 시나리오에서는 쿼리의 일부로 `sessionId`를 제공할 수 있습니다. `sessionId`는 고유한 사용자 세션을 참조하기 위해 만드는 고유 문자열입니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

동일한 `sessionId`를 사용한다면 동일한 복제본을 대상으로 최상의 시도를 진행하여 사용자에게 표시되는 결과의 일관성이 높일 수 있습니다. 

> [!NOTE]
> 동일한 `sessionId` 값을 반복해서 다시 사용하면 복제본 간에 요청 부하가 분산되지 못하며 검색 서비스의 성능에 부정적인 영향을 미칠 수 있습니다. sessionId로 사용된 값은 '_' 문자로 시작할 수 없습니다.

## <a name="scoring-profiles"></a>점수 매기기 프로필

*채점 프로필* 을 정의하여 다양한 필드의 순위 지정 방식을 사용자 지정할 수 있습니다. 점수 매기기 프로필을 사용하면 검색 결과에서 항목의 순위를 보다 강력하게 제어할 수 있습니다. 예를 들어 잠재 수익을 기준으로 하여 특정 항목을 상승시키거나, 새 항목을 프로모션하거나, 너무 오랫동안 재고에 포함되어 있던 항목을 상승시킬 수 있습니다. 

점수 매기기 프로필은 가중 필드, 함수 및 매개 변수로 구성된 인덱스 정의의 일부입니다. 정의 방법에 대한 자세한 내용은 [점수 매기기 프로필](index-add-scoring-profiles.md)을 참조하세요.

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode 매개 변수(미리 보기)

[문서 검색](/rest/api/searchservice/preview-api/search-documents)요청에는 필드 수준의 관련성에 관해 추가적인 세부 정보를 제공할 새 [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) 매개 변수가 있습니다. `@searchScore`가 전체 문서에 대하여 계산되는 반면(해당 쿼리의 컨텍스트에서의 문서 관련성 정도), featuresMode를 통해서는 `@search.features` 구조에서 설명한 바와 같이 개별 필드에 대한 정보를 얻을 수 있습니다. 해당 구조에는 쿼리에서 사용한 모든 필드가 들어갑니다(쿼리 내의 **searchFields** 를 통한 특정 필드 또는 인덱스 내에서 **searchable** 라는 특성을 갖는 모든 필드). 각 필드에 대해 다음 값을 얻습니다.

+ 필드에서 찾은 고유한 토큰의 수
+ 유사성 점수 또는 쿼리 용어와 관련해 해당 필드의 콘텐츠가 얼마나 유사한지에 대한 측정값
+ 용어 빈도 또는 필드 내에서 해당 쿼리 용어가 발견된 횟수

"설명" 및 "제목" 필드를 대상으로 하는 쿼리에 대해서는 `@search.features`를 포함하는 응답이 다음과 같을 수 있습니다.

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

이러한 데이터 요소를 [사용자 지정 채점 솔루션](https://github.com/Azure-Samples/search-ranking-tutorial)에서 사용하거나 검색 관련성 문제를 디버그하는 데 정보를 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목

+ [점수 매기기 프로필](index-add-scoring-profiles.md)
+ [REST API 참조](/rest/api/searchservice/)
+ [문서 검색 API](/rest/api/searchservice/search-documents)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)