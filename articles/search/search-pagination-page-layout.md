---
title: 검색 결과 작업 방법
titleSuffix: Azure Cognitive Search
description: 검색 결과를 구조화 및 정렬하고 문서 수를 가져오고 Azure Cognitive Search의 검색 결과에 콘텐츠 탐색을 추가합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: e810a5d3a94ad4e356d0b44a422b757b5710a781
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530063"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 결과 작업 방법

이 문서에서는 Azure Cognitive Search에서 쿼리 응답을 작성하는 방법을 설명합니다. 응답의 구조는 REST API의 [검색 문서](/rest/api/searchservice/Search-Documents) 또는 .NET SDK의 [SearchResults 클래스](/dotnet/api/azure.search.documents.models.searchresults-1) 등 쿼리의 매개 변수에 따라 결정됩니다. 다음과 같은 방법으로 쿼리에 매개 변수를 사용하여 결과 집합을 구성할 수 있습니다.

+ 결과의 문서 수 제한 또는 일괄 처리(기본적으로 50개)
+ 결과에 포함할 필드 선택
+ 결과 정렬
+ 검색 결과의 본문에서 전체 또는 부분 일치 항목 강조 표시

## <a name="result-composition"></a>결과 구성

검색 문서는 대규모 필드로 구성될 수 있지만 보통 결과 집합의 각 문서를 표시하는 데는 몇 개면 됩니다. 쿼리 요청에서 `$select=<field list>`를 추가하여 응답에 표시되는 필드를 지정합니다. 필드는 결과에 포함될 인덱스에서 **검색할** 수 있도록 특성을 지정해야 합니다. 

가장 잘 작동하는 필드에는 문서를 대조 및 구분하여 사용자 쪽의 클릭 메시지 응답을 유도하기에 충분한 정보를 제공하는 필드가 포함됩니다. 전자상거래 사이트에는 제품 이름, 설명, 브랜드, 색, 크기, 가격 및 등급이 있을 수 있습니다. hotels-sample-index 기본 제공 샘플의 경우 다음 예제의 필드가 될 수 있습니다.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 제품 사진이나 로고처럼 결과에 이미지 파일을 포함하려는 경우, 이미지 파일은 Azure Cognitive Search 외부에 저장하지만 검색 문서에서 이미지 URL을 참조하는 필드를 인덱스에 포함합니다. 결과에서 이미지를 지원하는 샘플 인덱스에는 이 [빠른 시작](search-create-app-portal.md)의 **realestate-sample-us** 데모와 [뉴욕시 작업 데모 앱](https://aka.ms/azjobsdemo)이 포함됩니다.

### <a name="tips-for-unexpected-results"></a>예기치 않은 결과 대한 팁

경우에 따라, 결과의 구조가 아닌 본질을 예상할 수 없습니다. 쿼리 결과가 예상과 다른 경우 다음과 같이 쿼리를 수정하여 결과가 개선되는지 확인할 수 있습니다.

+ **`searchMode=any`** (기본값)를 **`searchMode=all`** 로 변경하면 임의의 조건이 아닌 모든 조건이 일치해야 합니다. 부울 연산자가 쿼리에 포함되는 경우 특히 이렇게 합니다.

+ 다른 어휘 분석기나 사용자 지정 분석기를 시험하여 쿼리 결과가 변경되는지 확인합니다. 기본 분석기는 하이픈이 포함된 단어를 나누며 단어를 어근 형태까지 줄여 일반적으로 쿼리 응답의 견고성을 높입니다. 그러나 하이픈을 그대로 두어야 하거나 문자열에 특수 문자가 포함되었다면, 인덱스가 올바른 형식의 토큰을 포함하도록 사용자 지정 분석기를 구성해야 할 수 있습니다. 자세한 내용은 [특수 문자(하이픈, 와일드카드, 정규식, 패턴)를 사용하는 부분 용어 검색 및 패턴](search-query-partial-matching.md)을 참조하세요.

## <a name="paging-results"></a>페이징 결과

기본적으로 검색 엔진은 쿼리가 전체 텍스트이거나 정확한 일치 쿼리에 대해 임의의 순서인 경우 검색 점수에 따라 결정된 대로 최대 50개의 최초 일치 항목을 반환합니다.

이와는 다른 수의 일치 문서를 반환하려면 `$top` 및 `$skip` 매개 변수를 쿼리 요청에 추가합니다. 다음 목록은 논리를 설명합니다.

+ `$count=true`를 추가하여 인덱스 내에 있는 총 일치 문서 수를 가져옵니다.

+ 일치하는 처음 15개 문서의 집합과 총 일치 항목 수를 더하여 반환합니다(`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`).

+ 두 번째 집합을 반환하며 처음 15개를 건너뛰고 다음 15개를 가져옵니다(`$top=15&$skip=15`). 세 번째 15개 집합에 대해 같은 작업을 수행합니다(`$top=15&$skip=30`).

기본 인덱스가 변경될 경우 페이지가 매겨진 쿼리의 결과가 안정적이지 않을 수 있습니다. 페이징은 각 페이지에 대해 `$skip` 값을 변경하지만, 각각의 쿼리는 독립적이며 쿼리 시점에서 인덱스에 존재하는 그대로 현재 데이터 보기에서 작동합니다(즉, 범용 데이터베이스에서와 같은 결과 캐싱이나 스냅샷 없음).
 
다음 예제는 중복 항목이 어떻게 얻어지는지 설명합니다. 4개의 문서에 대한 인덱스를 가정해 보겠습니다.

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
한 번에 2개 결과를 평가에 따라 정렬하여 반환하고자 합니다. 이 쿼리를 실행하여 결과의 첫 페이지를 가져오고(`$top=2&$skip=0&$orderby=rating desc`) 다음과 같은 결과가 생성됩니다.

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
서비스에서 쿼리 호출 사이에 5번째 문서가 인덱스에 추가되었습니다(`{ "id": "5", "rating": 4 }`).  잠시 후 두 번째 페이지를 페치하는 쿼리를 실행하고(`$top=2&$skip=2&$orderby=rating desc`) 다음 결과를 가져옵니다.

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
문서 2는 두 번 페치됩니다. 이것은 새 문서 5는 평가의 값이 더 크기 때문에 문서 2 앞에 정렬되고 첫 페이지에 도달하기 때문입니다. 이 동작은 예기치 않을 수 있지만 검색 엔진의 일반적인 동작 방식입니다.

## <a name="ordering-results"></a>결과 정렬

전체 텍스트 검색 쿼리의 경우 문서의 용어 빈도 및 근접성([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)에서 파생)에 따라 계산된 검색 점수에 따라 결과의 순위를 자동으로 지정하며, 검색어와 일치 항목이 더 많거나 일치 수준이 높을수록 문서에 더 높은 점수를 부여합니다. 

검색 점수는 동일한 결과 집합의 다른 문서를 기준으로 일치 수준을 반영하여 일반적인 관련성 정보를 전달합니다. 그러나 쿼리마다 점수가 항상 일치하는 것은 아니므로, 쿼리 작업에서 검색 문서의 순서가 지정되는 방법에서 약간 차이가 있을 수 있습니다. 이러한 상황이 발생하는 원인은 다음과 같이 몇 가지로 설명할 수 있습니다.

| 원인 | 설명 |
|-----------|-------------|
| 데이터 변동성 | 인덱스 콘텐츠에 따라 문서를 추가, 수정 또는 삭제할 수 있습니다. 시간 경과에 따라 인덱스 업데이트가 처리되면 용어 빈도가 변경되며 일치하는 문서의 검색 점수에 영향이 있습니다. |
| 여러 복제본 | 여러 복제본을 사용하는 서비스의 경우 각 복제본에 대해 병렬로 쿼리가 실행됩니다. 검색 점수를 계산하는 데 사용되는 인덱스 통계는 복제본별로 산출되며 쿼리 응답에서는 결과가 병합 및 정렬됩니다. 복제본은 대부분 서로를 반영하지만 약간의 상태 차이가 있으므로 통계가 다를 수 있습니다. 예를 들어, 한 복제본이 해당 통계에 영향을 주는 문서를 삭제했는데 이 문서가 다른 복제본에 병합되었을 수 있습니다. 일반적으로 복제본별 통계에서의 차이는 소규모 인덱스에서 더 두드러집니다. |
| 동일한 점수 | 여러 문서의 점수가 동일한 경우 그 중 아무거나 먼저 표시될 수 있습니다.  |

### <a name="how-to-get-consistent-ordering"></a>순서를 일관되게 지정하는 방법

일관된 순서 지정이 애플리케이션 요구 사항이라면 필드에서 [ **`$orderby`** 식](query-odata-filter-orderby-syntax.md)을 명시적으로 정의할 수 있습니다. **`sortable`** 로 인덱싱되는 필드만 결과 정렬에 사용할 수 있습니다. **`$orderby`** 에 일반적으로 사용되는 필드에는 평가 및 날짜와, **`orderby`** 매개 변수 값을 지리 공간적 값에 대한 필드 이름과 [ **`geo.distance()` 함수**](query-odata-filter-orderby-syntax.md) 호출을 포함하도록 지정한 경우 위치 필드 등이 있습니다.

일관성을 높이는 또 다른 방법은 [사용자 지정 점수 매기기 프로필](index-add-scoring-profiles.md)을 사용하는 것입니다. 점수 매기기 프로필을 사용하면 특정 필드에서의 일치 항목 찾기 기능을 더 강화하여 검색 결과의 항목 순위를 더 세밀하게 제어할 수 있습니다. 추가적인 점수 매기기 논리를 사용하면, 각 문서의 검색 점수가 서로 달라 발생하는 복제본 간의 미세한 차이를 무시할 수 있습니다. 이 방법에서는 [순위 지정 알고리즘](index-ranking-similarity.md)을 사용하는 것이 좋습니다.

## <a name="hit-highlighting"></a>적중 항목 강조 표시

적중 항목 강조 표시란 일치를 쉽게 찾을 수 있게 결과에서 일치하는 용어에 적용되는 텍스트 서식(예: 굵게, 노란색 강조 표시)을 말합니다. 적중 항목 강조 표시 지침은 [쿼리 요청](/rest/api/searchservice/search-documents)에서 제공합니다. 

적중 항목 강조 표시를 사용하려면 `highlight=[comma-delimited list of string fields]`를 추가하여 강조 표시를 사용할 필드를 지정합니다. 강조 표시는 설명 필드 등 일치 항목이 바로 드러나지 않는 긴 콘텐츠 필드에 유용합니다. **검색 가능** 특성이 지정된 필드 정의만 적중 항목 강조 표시를 적용할 수 있습니다.

기본적으로 Azure Cognitive Search는 필드당 최대 5개의 강조 표시를 반환합니다. 필드에 대시와 정수를 첨부하여 이 숫자를 조정할 수 있습니다. 예를 들어 `highlight=Description-10`은 설명 필드에서 일치하는 내용에 대해 최대 10개의 강조 표시를 반환합니다.

서식은 전체 용어 쿼리에 적용됩니다. 서식 지정 형식은 `highlightPreTag` 및 `highlightPostTag` 태그로 결정되며, 코드가 응답을 처리합니다(예: 굵은 글꼴 또는 노란색 배경 적용).

다음 예제에서는 설명 필드에서 찾은 "sandy", "sand", "beaches", "beach" 등의 단어를 강조 표시하도록 태그가 지정되었습니다. 유사 항목 및 와일드카드 검색 등 엔진에서 쿼리 확장을 트리거하는 쿼리에서는 적중 항목 강조 표시를 제한적으로 지원합니다.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>새 동작(7월 15일부터)

2020년 7월 15일 이후에 만들어진 서비스는 다른 강조 표시 환경을 제공합니다. 해당 날짜 이전에 만든 서비스는 강조 표시 동작에 변동이 없습니다. 

새 동작:

+ 전체 구 쿼리와 일치하는 구만 반환됩니다. 쿼리 구 "super bowl"는 다음과 같은 강조 표시를 반환합니다.

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  *super* 및 *bowl* 의 다른 인스턴스는 전체 구와 일치하지 않으므로 강조 표시되지 않습니다.

적중 항목 강조 표시를 구현하는 클라이언트 코드를 작성할 때는 이러한 변경에 유의합니다. 완전히 새로운 검색 서비스를 만들지 않는 한 영향은 없습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대한 검색 페이지를 신속히 생성하려면 다음 옵션을 고려합니다.

+ 포털에서 [애플리케이션 생성기](search-create-app-portal.md)는 검색 창 및 패싯 탐색과, 이미지가 있는 결과 영역을 포함하는 HTML 페이지를 만듭니다.
+ [C#에서 첫 번째 앱 만들기](tutorial-csharp-create-first-app.md)는 기능적인 클라이언트를 빌드하는 자습서입니다. 샘플 코드는 페이지가 매겨진 쿼리, 적중 항목 강조 표시 및 정렬을 보여줍니다.

[뉴욕시 작업 데모 앱](https://aka.ms/azjobsdemo) 또는 [라이브 데모 사이트를 사용하는 JavaScript 샘플 코드](https://github.com/liamca/azure-search-javascript-samples) 등 몇 가지 코드 샘플에서 웹 프런트 엔드 인터페이스를 확인할 수 있습니다.
