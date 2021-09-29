---
title: 쿼리 유형
titleSuffix: Azure Cognitive Search
description: 자유 텍스트, 필터, 자동 완성 및 제안, 지역 검색, 시스템 쿼리 및 문서 조회 등 Cognitive Search에서 지원되는 쿼리 형식에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b9ca2a4ba836adacb81a82ad59a11c21ffd6c3fb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216881"
---
# <a name="querying-in-azure-cognitive-search"></a>Azure Cognitive Search에서 쿼리

Azure Cognitive Search는 자유 텍스트 검색에서 고도로 지정된 쿼리 패턴에 이르기까지, 광범위한 시나리오를 지원하는 풍부한 쿼리 언어를 제공합니다. 이 문서에서는 쿼리 요청과 만들 수 있는 쿼리의 종류에 대해 설명합니다.

Cognitive Search에서 쿼리는 쿼리 실행을 알리고 응답을 다시 형성하는 매개 변수가 포함된 왕복 **`search`** 작업에 지정되는 전체 내용입니다. 매개 변수 및 파서는 쿼리 요청의 유형을 결정합니다. 다음 쿼리 예제는 [hotels-sample-index](search-get-started-portal.md) 문서 컬렉션을 대상으로 하는 [검색 문서(REST API)](/rest/api/searchservice/search-documents)를 통해 부울 연산자를 사용하는 자유 텍스트 쿼리입니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

쿼리 실행 중에 사용되는 매개 변수는 다음과 같습니다.

+ **`queryType`** 은 [간단한 기본 쿼리 파서](search-query-simple-examples.md)(전체 텍스트 검색에 최적화됨) 또는 고급 쿼리 구문(예: 정규식, 근접 검색, 유사 항목 검색 및 와일드카드 검색)에 사용되는 [전체 Lucene 쿼리 파서](search-query-lucene-examples.md) 중 하나인 파서를 설정합니다.

+ **`searchMode`** 는 일치 항목이 식의 "모든" 조건을 기준으로 하는지, "임의" 조건을 기준으로 하는지를 지정합니다. 기본값은 임의입니다.

+ **`search`** 는 연산자를 포함하거나 포함하지 않는 일치 기준(일반적으로 전체 용어 또는 구문)을 제공합니다. 인덱스 스키마에서 *검색 가능* 으로 지정되는 필드는 이 매개 변수의 후보입니다.

+ **`searchFields`** 는 쿼리 실행을 검색 가능한 특정 필드로 제한합니다. 개발하는 동안 선택 및 검색에 동일한 필드 목록을 사용하는 것이 좋습니다. 그렇지 않으면 결과에 표시될 수 없는 필드 값을 기준으로 하여 문서가 반환된 이유가 불확실해질 수 있습니다.

응답을 형성하는 데 사용되는 매개 변수는 다음과 같습니다.

+ **`select`** 는 응답에 반환할 필드를 지정합니다. 인덱스에서 *검색 가능한* 상태로 표시되는 필드만 select 문에서 사용할 수 있습니다.

+ **`top`** 은 지정된 수의 가장 일치하는 문서를 반환합니다. 이 예제에서는 10개의 적중 사항만 반환됩니다. top과 skip(표시되지 않음)을 사용하여 결과를 페이징할 수 있습니다.

+ **`count`** 는 전체 인덱스에서 전반적으로 일치하는 문서의 개수를 알려 줍니다. 이는 반환되는 것보다 많을 수 있습니다. 

+ **`orderby`** 는 등급 또는 위치와 같은 값을 기준으로 결과를 정렬하려는 경우에 사용됩니다. 그렇지 않으면 기본값은 관련성 점수를 사용하여 결과의 ​​순위를 지정하는 것입니다. 이 매개 변수의 후보가 되려면 필드가 *정렬 가능한* 상태로 지정되어야 합니다.

위의 목록이 대표적이지만 완전한 것은 아닙니다. 쿼리 요청의 전체 매개 변수 목록을 보려면 [문서 검색(REST API)](/rest/api/searchservice/search-documents)을 참조하세요.

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>쿼리 유형

몇 가지 주목할 만한 예외를 제외하고 쿼리 요청은 빠르게 검사하도록 구조화되어 있는 반전된 인덱스를 반복합니다. 이러한 인덱스는 검색 문서의 수에 상관없이 잠재적으로 모든 필드에서 일치 항목을 찾을 수 있습니다. Cognitive Search에서 일치 항목을 찾기 위한 기본 방법은 전체 텍스트 검색 또는 필터 중 하나이지만 자동 완성, 지리적 위치 검색 등 그 외 잘 알려진 검색 환경도 구현할 수 있습니다. 이 문서의 나머지 부분에서는 Cognitive Search의 쿼리를 요약하고 추가 정보 및 예제 링크를 제공합니다.

## <a name="full-text-search"></a>전체 텍스트 검색

검색 앱에서 용어 입력을 수집하는 검색 상자를 포함하는 경우, 전체 텍스트 검색은 해당 환경을 지원하는 쿼리 작업일 것입니다. 전체 텍스트 검색은 인덱스에서 *검색 가능한* 모든 필드의 **`search`** 매개 변수에 전달된 용어 또는 구를 허용합니다. 쿼리 문자열의 선택적 부울 연산자는 포함 또는 제외 조건을 지정할 수 있습니다. 단순 파서와 전체 파서는 모두 전체 텍스트 검색을 지원합니다.

Cognitive Search에서 전체 텍스트 검색은 Apache Lucene 쿼리 엔진을 기반으로 합니다. 전체 텍스트 검색의 쿼리 문자열에서는 보다 효율적으로 검사하기 위해 어휘 분석을 수행합니다. 분석에는 모든 용어를 소문자로 처리하고, "the"와 같은 불용어를 제거하며, 용어를 원시 어근 형태로 줄이는 것이 포함됩니다. 기본 분석기는 표준 Lucene입니다.

일치하는 용어를 찾으면 쿼리 엔진은 문서 키 또는 ID를 사용해 일치 항목이 포함된 검색 문서를 다시 구성하여 필드 값을 조합하고, 관련성 순서로 문서 순위를 지정하며, 응답에 상위 50개(기본값) 또는 다른 수를 반환( **`top`** 을 지정한 경우)합니다.

전체 텍스트 검색을 구현하는 경우 콘텐츠가 토큰화되는 방법을 이해하면 모든 쿼리 변칙을 디버그하는 데 도움이 됩니다. 하이픈을 넣은 문자열이나 특수 문자를 쿼리하면 인덱스에 올바른 토큰이 포함되어 있는지 확인하기 위해 기본 표준 Lucene 이외의 분석기를 사용해야 할 수 있습니다. 어휘 분석을 수정하는 [언어 분석기](index-add-language-analyzers.md#language-analyzer-list) 또는 [특수 분석기](index-add-custom-analyzers.md#built-in-analyzers)로 기본값을 재정의할 수 있습니다. 일례로 필드의 전체 내용을 단일 토큰으로 취급하는 [키워드](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)를 들 수 있습니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다. 자세한 내용은 [특수 문자를 사용하는 부분 용어 검색 및 패턴](search-query-partial-matching.md)을 참조하세요.

큰 텍스트 블록(내용 필드 또는 긴 설명)이 포함된 인덱스에서 부울 연산자를 많이 사용할 것으로 예상되는 경우 **`searchMode=Any|All`** 매개 변수로 쿼리를 테스트하여 해당 설정이 부울 검색에 미치는 영향을 평가해야 합니다.

## <a name="autocomplete-and-suggested-queries"></a>자동 완성 및 제안된 쿼리

[자동 완성 또는 제안된 결과](search-add-autocomplete-suggestions.md)는 입력 시 검색 환경에서 부분 문자열 입력(각 문자 뒤)을 기준으로 연속 쿼리 요청을 실행하는 **`search`** 의 대안입니다. [이 자습서](tutorial-csharp-type-ahead-and-suggestions.md)에 설명된 대로 **`autocomplete`** 및 **`suggestions`** 매개 변수를 함께 또는 따로 사용할 수는 있지만, **`search`** 와 함께 사용할 수는 없습니다. 완료된 용어와 제안된 쿼리는 모두 인덱스 콘텐츠에서 파생됩니다. 엔진은 인덱스에 존재하지 않는 문자열 또는 제안을 반환하지 않습니다. 자세한 내용은 [자동 완성(REST API)](/rest/api/searchservice/autocomplete) 및 [제안(REST API)](/rest/api/searchservice/suggestions)을 참조하세요.

## <a name="filter-search"></a>필터 검색

필터는 Cognitive Search를 포함하는 앱에서 널리 사용됩니다. 애플리케이션 페이지에서 필터는 사용자가 직접 필터링할 수 있도록 링크 탐색 구조에서 패싯으로 시각화되는 경우가 많습니다. 또한 필터는 인덱싱된 콘텐츠의 조각을 노출하기 위해 내부적으로 사용됩니다. 예를 들어 제품 범주에 대한 필터를 사용하여 검색 페이지를 초기화하거나, 인덱스에 영어와 프랑스어 필드가 모두 포함된 경우 언어를 초기화할 수 있습니다.

다음 표에 설명된 바와 같이 특수 쿼리 양식을 호출하는 필터 필요할 수도 있습니다. 지정되지 않은 검색( **`search=*`** ) 또는 용어, 구, 연산자, 패턴이 포함된 쿼리 문자열을 사용하여 필터를 사용할 수 있습니다.

| 필터 시나리오 | Description |
|-----------------|-------------|
| 범위 필터 | Azure Cognitive Search에서 범위 쿼리는 필터 매개 변수를 사용하여 작성됩니다. 자세한 내용과 예제는 [범위 필터 예제](search-query-simple-examples.md#example-5-range-filters)를 참조하세요. |
| 지리적 위치 검색 | 검색 가능한 필드가 [Edm.GeographyPoint type](/rest/api/searchservice/supported-data-types)인 경우 "주변 찾기" 또는 맵 기반 검색 컨트롤에 대한 필터 식을 만들 수 있습니다. 지리적 검색을 유도하는 필드에는 좌표가 포함됩니다. 자세한 내용과 예제는 [지역 검색 예제](search-query-simple-examples.md#example-6-geo-search)를 참조하세요. |
| 패싯 탐색 | [사용자는 Faceted 탐색](search-faceted-navigation.md) 트리에서 각 클릭 시 결과를 좁혀서 선택할 수 있습니다. 각 측면은 더 이상 조건과 일치하지 않는 문서를 제외하는 필터를 통해 백업됩니다. |

> [!NOTE]
> 필터 식에 사용되는 텍스트는 쿼리를 처리하는 동안 분석되지 않습니다. 텍스트 입력은 일치에서 성공하거나 실패하는, 대/소문자 구분 문자 패턴으로 가정됩니다. 필터 식은 [OData 구문](query-odata-filter-orderby-syntax.md)을 사용하여 생성되고 인덱스의 *필터링 가능한* 모든 필터에 있는 **`filter`** 매개 변수로 전달됩니다. 자세한 내용은 [Azure Cognitive Search의 필터](search-filters.md)를 참조하세요.

## <a name="document-look-up"></a>문서 조회

앞에서 설명한 쿼리 양식과 달리 이 쿼리는 해당 인덱스 검색 또는 검사 없이 단일 [ID별 검색 문서](/rest/api/searchservice/lookup-document)를 검색합니다. 한 문서만 요청되고 반환됩니다. 사용자가 검색 결과에서 한 항목을 선택하는 경우 문서를 검색하고 필드를 사용하여 상세 정보 페이지를 채우는 것이 일반적인 응답입니다. 문서 조회는 이를 지원하는 작업입니다.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>고급 검색: 유사 항목, 와일드카드, 근접, 정규식

고급 쿼리 형태는 전체 Lucene 파서 및 특정 쿼리 동작을 트리거하는 연산자에 따라 달라집니다.

| 쿼리 유형 | 사용량 | 예제 및 자세한 정보 |
|------------|--------|------------------------------|
| [필드 지정 검색](query-lucene-syntax.md#bkmk_fields) | **`search`** 매개 변수, **`queryType=full`**  | 단일 필드를 대상으로 복합 쿼리 식을 작성합니다. <br/>[필드 지정 검색 예제](search-query-lucene-examples.md#example-1-fielded-search) |
| [유사 항목 검색](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** 매개 변수, **`queryType=full`** | 유사한 구조 또는 철자가 포함된 용어를 검색합니다. <br/>[유사 항목 검색 예제](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [근접 검색](query-lucene-syntax.md#bkmk_proximity) | **`search`** 매개 변수, **`queryType=full`** | 문서에서 서로 가까이 있는 용어를 찾습니다. <br/>[근접 검색 예제](search-query-lucene-examples.md#example-3-proximity-search) |
| [용어 상승](query-lucene-syntax.md#bkmk_termboost) | **`search`** 매개 변수, **`queryType=full`** | 승격된 용어가 포함된 문서는 그렇지 않은 다른 문서보다 상대적으로 높은 순위를 매깁니다. <br/>[용어 상승 예제](search-query-lucene-examples.md#example-4-term-boosting) |
| [정규식 검색](query-lucene-syntax.md#bkmk_regex) | **`search`** 매개 변수, **`queryType=full`** | 정규식의 콘텐츠를 기반으로 검색합니다. <br/>[정규식 예제](search-query-lucene-examples.md#example-5-regex) |
|  [와일드 카드 또는 접두사 검색](query-lucene-syntax.md#bkmk_wildcard) | **`search`** 매개 변수(**_`~`_* 또는 **`?`** , **`queryType=full`** 사용)| 접두사와 물결표(`~`) 또는 단일 문자(`?`)를 기반으로 검색합니다. <br/>[와일드카드 검색 예제](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>다음 단계

쿼리 구현에 대한 자세한 내용은 각 구문의 예제를 검토하세요. 전체 텍스트 검색을 처음 사용하는 경우 쿼리 엔진이 수행하는 작업을 자세히 살펴보는 것도 마찬가지로 좋은 선택일 수 있습니다.

+ [단순 쿼리 예제](search-query-simple-examples.md)
+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)git