---
title: 패싯 탐색 범주 계층 구조 추가
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search와 통합 되는 응용 프로그램에서 자체 지향 필터링을 위한 패싯 탐색을 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 55473848a8c8e8e7f9c6072566eb73d823feaba2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561312"
---
# <a name="add-faceted-navigation-to-a-search-app"></a>검색 앱에 패싯 탐색 추가

패싯 탐색은 검색 앱에서 쿼리 결과에 대 한 자체 지향 드릴 다운 필터링에 사용 됩니다. 여기서 응용 프로그램은 문서 그룹 (예: 범주 또는 브랜드)에 범위를 지정 하기 위한 양식 컨트롤을 제공 하 고 Azure Cognitive Search는 환경을 지 원하는 데이터 구조와 필터를 제공 합니다. 

이 문서에서는 Azure Cognitive Search에서 패싯 탐색 구조를 만드는 기본 단계에 대해 알아봅니다.

> [!div class="checklist"]
> * 인덱스의 필드 특성 설정
> * 요청 및 응답 구조
> * 프레젠테이션 계층에서 탐색 컨트롤 및 필터 추가

프레젠테이션 계층의 코드는 패싯 탐색 환경에서 많은 작업을 수행 합니다. 이 문서의 끝에 나열 된 데모와 샘플은 모든 항목을 함께 가져오는 방법을 보여 주는 작업 코드를 제공 합니다.

## <a name="faceted-navigation-in-a-search-page"></a>검색 페이지의 패싯 탐색

패싯은 동적이며 쿼리에서 반환됩니다. 검색 응답에는 결과의 문서를 탐색 하는 데 사용 되는 모든 패싯 범주가 표시 됩니다. 쿼리가 먼저 실행 된 다음 패싯이 현재 결과에서 끌어오고 패싯 탐색 구조로 어셈블됩니다.

Cognitive Search에서 패싯은 한 계층에 해당 하며 계층적 일 수 없습니다. 패싯 탐색에 대해 잘 모르는 경우 다음 예제에서는 왼쪽에 있는 항목을 보여 줍니다. 개수는 각 패싯에 대 한 일치 항목 수를 표시 합니다. 동일한 문서를 여러 패싯으로 나타낼 수 있습니다.

:::image source="media/tutorial-csharp-create-first-app/azure-search-facet-nav.png" alt-text="패싯 검색 결과":::

패싯을 사용하면 원하는 항목을 쉽게 찾을 수 있으며 항상 결과를 얻을 수 있습니다. 개발자는 패싯을 통해 검색 인덱스를 탐색하는 데 가장 유용한 검색 조건을 노출할 수 있습니다.

## <a name="enable-facets-in-the-index"></a>인덱스에서 패싯 사용

패싯는 "패싯 가능" 특성을 true로 설정할 때 인덱스 정의에서 필드 단위로 설정 됩니다.

반드시 필요한 것은 아니지만, 검색 응용 프로그램에서 패싯 탐색 환경을 지 원하는 데 필요한 필터를 작성할 수 있도록 "필터링 가능한" 특성도 설정 해야 합니다.

"호텔" 샘플 인덱스의 다음 예에서는 단일 값 또는 짧은 구 ("Category", "Tags", "등급")를 포함 하는 낮은 카디널리티 필드에 "패싯 가능" 및 "필터링 가능"을 보여 줍니다.

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "Description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "HotelName", "type": "Edm.String", "facetable": false },
    { "name": "Category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint" }
  ]
}
```

### <a name="choosing-fields"></a>필드 선택

패싯은 단일 값 필드 및 컬렉션에 대해 계산할 수 있습니다. 패싯 탐색에서 가장 잘 작동 하는 필드는 다음과 같은 특징이 있습니다.

* 낮은 카디널리티 (검색 모음 문서 전체에서 반복 되는 작은 고유 값)

* 탐색 트리에서 깔끔하게 렌더링 되는 간단한 설명 값 (하나 또는 두 개의 단어)

필드 자체가 아니라 필드의 내용이 패싯 탐색 구조에서 패싯을 생성 합니다. 패싯이 문자열 필드 *색* 이면 패싯이 파랑, 녹색 및 해당 필드에 대 한 다른 모든 값이 됩니다.

가장 좋은 방법은 null 값, 맞춤법 오류 또는 대/소문자 불일치, 동일한 단어의 단일 및 복수 버전에 대 한 필드를 선택 하는 것입니다. 필터 및 패싯이 어휘 분석 또는 [맞춤법 검사](speller-how-to-add.md)를 거치지 않습니다. 즉, 단어가 한 문자씩 다른 경우에도 "패싯 가능" 필드의 모든 값이 잠재적 패싯이 될 수 있습니다.

### <a name="defaults-in-rest-and-azure-sdks"></a>REST 및 Azure Sdk의 기본값

Azure Sdk 중 하나를 사용 하는 경우 코드에서 모든 필드 특성을 지정 해야 합니다. 이와 달리 REST API에는 [데이터 형식을](/rest/api/searchservice/supported-data-types)기반으로 하는 필드 특성에 대 한 기본값이 있습니다. 다음 데이터 형식은 기본적으로 "필터링 가능" 및 "패싯 가능"입니다.

* `Edm.String`
* `Edm.DateTimeOffset`
* `Edm.Boolean`
* `Edm.Int32`, `Edm.Int64`, `Edm.Double`
* 위 형식의 컬렉션 (예: `Collection(Edm.String)` 또는) `Collection(Edm.Double)`

`Edm.GeographyPoint` 또는 `Collection(Edm.GeographyPoint)` 필드는 패싯 탐색에서 사용할 수 없습니다. 카디널리티가 낮은 필드에서 패싯 성능이 가장 뛰어납니다. 지리적 좌표를 확인 했기 때문에 지정 된 데이터 집합에서 두 좌표 집합이 동일 하지는 않습니다. 따라서 지리적 좌표에는 패싯이 지원되지 않습니다. 위치별로 패싯을 만들려면 도시 또는 지역 필드가 필요합니다.

> [!Tip]
> 성능 및 스토리지 최적화를 위한 최고의 방법으로, 패싯으로 사용하지 말아야 하는 필드에 대해 패싯을 해제합니다. 특히 ID 또는 제품 이름과 같은 고유한 값의 문자열 필드는 패싯 탐색에서 실수로(그리고 비효과적으로) 사용되지 않도록 `"facetable": false`로 설정해야 합니다. 기본적으로 필터 및 패싯을 사용 하도록 설정 하는 REST API에 특히 그렇습니다.

## <a name="facet-request-and-response"></a>패싯 요청 및 응답

쿼리에서 패싯이 지정 되 고 패싯 탐색 구조가 응답의 맨 위에 반환 됩니다. 요청 및 응답의 구조는 매우 간단 합니다. 실제로 패싯 탐색의 실제 작업은 이후 섹션에서 설명 하는 프레젠테이션 계층에 있습니다. 

다음 REST 예제는 전체 인덱스로 범위가 한정 되는 정규화 되지 않은 쿼리 ( `"search": "*"` )입니다 ( [기본 제공 호텔 샘플](search-get-started-portal.md)참조). 패싯은 일반적으로 필드 목록 이지만,이 쿼리는 아래에서 보다 읽기 쉬운 응답을 위해 하나만 표시 합니다.

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "queryType": "simple",
    "select": "",
    "searchFields": "",
    "filter": "",
    "facets": [ "Category"], 
    "orderby": "",
    "count": true
}
```

패싯 탐색 구조를 완전히 채우도록 열려 있는 쿼리를 사용 하 여 검색 페이지를 초기화 하는 것이 유용 합니다. 요청에서 쿼리 용어를 전달 하는 즉시 패싯 탐색 구조는 전체 인덱스가 아닌 결과의 일치 항목만으로 범위가 지정 됩니다.

위의 예제에 대 한 응답은 위쪽에 패싯 탐색 구조를 포함 합니다. 구조는 "Category" 값 및 각 값에 대 한 호텔 수로 구성 됩니다. 여기에는 간단한 검색 결과가 나와 있습니다. 이 예제는 여러 가지 이유로 제대로 작동 합니다. 이 필드에 대 한 패싯 수는 제한 (기본값: 10) 이므로 모든 항목이 표시 되 고 50 호텔 인덱스의 모든 호텔은 정확히 이러한 범주 중 하나에 표시 됩니다.

```json
{
    "@odata.context": "https://demo-search-svc.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "@odata.count": 50,
    "@search.facets": {
        "Category": [
            {
                "count": 13,
                "value": "Budget"
            },
            {
                "count": 12,
                "value": "Resort and Spa"
            },
            {
                "count": 9,
                "value": "Luxury"
            },
            {
                "count": 7,
                "value": "Boutique"
            },
            {
                "count": 5,
                "value": "Suite"
            },
            {
                "count": 4,
                "value": "Extended-Stay"
            }
        ]
    },
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Category": "Boutique",
            "Tags": [
                "pool",
                "air conditioning",
                "concierge"
            ],
            "ParkingIncluded": false,
  . . .
```

## <a name="facets-syntax"></a>패싯 구문

패싯 쿼리 매개 변수는 쉼표로 구분 된 "패싯 가능" 필드 목록으로 설정 되 고 데이터 형식에 따라 개수, 정렬 순서 및 범위를 설정 하는 추가 매개 변수화 될 수 있습니다 (  `count:<integer>` , `sort:<>` , `interval:<integer>` 및 `values:<list>` ). 패싯 매개 변수에 대 한 자세한 내용은 [REST API의 "쿼리 매개 변수"](/rest/api/searchservice/search-documents#query-parameters)를 참조 하십시오.

```http
POST https://{{service_name}}.search.windows.net/indexes/hotels/docs/search?api-version={{api_version}}
{
    "search": "*",
    "facets": [ "Category", "Tags,count:5", "Rating,values:1|2|3|4|5"],
    "count": true
}
```

각 패싯 탐색 트리는 기본 제한 값이 10 개로 제한 됩니다. 이 기본값은 값 목록을 관리하기 쉬운 크기로 유지하므로 탐색 구조에 유용합니다. "Count"에 값을 할당 하 여 기본값을 재정의할 수 있습니다. 예를 들어는 `"Tags,count:5"` 태그 섹션 아래에 있는 태그의 수를 상위 5 개로 줄입니다.

Numeric 및 DateTime 값에만 해당 하는 경우 패싯 필드의 값을 명시적으로 설정 (예: `facet=Rating,values:1|2|3|4|5` ) 하 여 결과를 연속 범위 (숫자 값 또는 기간을 기준으로 하는 범위)로 구분할 수 있습니다. 또는에서와 같이 "interval:을 추가할 수 있습니다 `facet=Rating,interval:1` . 

각 범위는 0부터 작성되며, 목록의 값을 엔드포인트로 사용하고 이전 범위를 잘라 불연속 간격을 만듭니다.

### <a name="discrepancies-in-facet-counts"></a>패싯 카운트의 불일치

경우에 따라 패싯 수가 결과 집합과 일치하지 않을 수 있습니다[[Azure Cognitive Search의 패싯 탐색(Microsoft Q&A 질문 페이지)](/answers/topics/azure-cognitive-search.html)참조].

패싯 수는 분할 아키텍처로 인해 부정확할 수 있습니다. 모든 검색 인덱스에는 여러 개의 분할된 데이터베이스가 있으며, 각 분할된 데이터베이스는 문서 수에 따라 상위 N개의 패싯을 보고합니다. 이 값이 단일 결과로 통합됩니다. 분할된 데이터베이스 중에 일치하는 값이 많은 것과 적은 것이 있는 경우 결과에서 일부 패싯 값이 누락되거나 적은 개수로 나타날 수 있습니다.

이 동작은 언제든 변경될 수 있지만 이 동작이 발생한 경우 count:\<number>를 큰 값으로 인위적으로 늘려 각 분할된 데이터베이스에서 전체 보고하도록 적용할 수 있습니다. count: 값이 필드의 고유 값 수보다 크거나 같으면 정확한 결과가 반환됩니다. 그러나 문서 수가 많은 경우에는 성능이 저하되므로 이 옵션을 신중하게 사용해야 합니다.

## <a name="presentation-layer"></a>프레젠테이션 계층

애플리케이션 코드에서 패턴은 패싯 쿼리 매개 변수를 사용하여 패싯 결과 및 $filter 식과 함께 패싯 탐색 구조를 반환합니다.  필터 식은 클릭 이벤트를 처리 하 고 패싯 선택에 따라 검색 결과의 범위를 좁힙니다.

### <a name="facet-and-filter-combination"></a>패싯 및 필터 조합

`JobsSearch.cs`NYCJobs demo에서 파일의 다음 코드 조각은 비즈니스 제목 패싯에서 값을 선택 하는 경우 선택한 비즈니스 타이틀을 필터에 추가 합니다.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

호텔 샘플의 또 다른 예는 다음과 같습니다. 다음 코드 조각은 사용자가 category 패싯에 서 값을 선택 하는 경우 필터에 categorFacet를 추가 합니다.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

### <a name="html-for-faceted-navigation"></a>패싯 탐색을 위한 HTML

`index.cshtml`NYCJobs 샘플 응용 프로그램의 파일에서 가져온 다음 예제는 검색 결과 페이지에서 패싯 탐색을 표시 하는 정적 HTML 구조를 보여 줍니다. 검색 용어를 제출하거나 패킷을 선택 또는 선택 취소하면 자동으로 패싯 목록이 작성되거나 다시 작성됩니다.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

### <a name="build-html-dynamically"></a>동적으로 HTML 빌드

`index.cshtml`NYCJobs demo ()의 다음 코드 조각은 HTML을 동적으로 작성 하 여 첫 번째 패싯, 비즈니스 타이틀을 표시 합니다. 그와 유사한 기능은 다른 패싯에 대한 HTML을 동적으로 빌드합니다. 각 패싯에는 해당 패싯 결과에 대해 발견된 항목 수를 표시하는 레이블과 수가 있습니다.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

## <a name="tips-for-working-with-facets"></a>패싯 작업을 위한 팁

이 섹션은 유용 하 게 사용할 수 있는 팁과 해결 방법 모음입니다.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>필터링된 결과와 비동기적으로 패싯 탐색 구조 유지

Azure Cognitive Search에서 패싯 탐색의 문제 중 하나는 패싯이 현재 결과에 대해서만 존재 한다는 것입니다. 실제로 사용자가 역순으로 탐색하고 검색 콘텐츠를 통해 대체 경로를 탐색하는 단계를 거슬러 올라갈 수 있도록 정적 패싯 집합을 유지하는 것이 일반적입니다. 

일반적인 사용 사례 이지만 패싯 탐색 구조에서 현재 기본적으로 제공 되는 것은 아닙니다. 정적 패싯을 원하는 개발자는 일반적으로 두 개의 필터링된 쿼리를 실행하여 제한 사항을 해결합니다. 하나는 결과에 적용되고 다른 하나는 탐색 용도로 패싯의 정적 목록을 만드는 데 사용됩니다.

### <a name="clear-facets"></a>패싯 지우기

검색 결과 페이지를 디자인할 때 패싯을 지우는 메커니즘을 추가해야 합니다. 확인란을 추가하는 경우 필터를 지우는 방법을 쉽게 확인할 수 있습니다. 다른 레이아웃에는 이동 경로 탐색 패턴 또는 다른 창의적인 방법이 필요할 수 있습니다. 호텔 c # 샘플에서는 빈 검색을 보내 페이지를 다시 설정할 수 있습니다. 이와 대조적으로 NYCJobs 샘플 응용 프로그램은 `[X]` 사용자에 게 더 강력한 시각적 큐 인 패싯을 지우는 선택 된 패싯 뒤에 클릭 가능한를 제공 합니다.

### <a name="trim-facet-results-with-more-filters"></a>추가 필터로 패싯 결과 자르기

패싯 결과는 패싯 용어와 일치하는 검색 결과에서 발견된 문서입니다. 다음 예제의 *cloud computing* 에 대한 검색 결과에서 254개 항목은 콘텐츠 형식이 *internal specification* 입니다. 항목은 상호 배타적이지 않을 수 있습니다. 하나의 항목이 두 필터 조건을 모두 충족하는 경우에는 각각 하나로 계산됩니다. 이 중복은 주로 문서 태깅을 구현하는 데 사용되는 `Collection(Edm.String)` 필드를 패싯할 때 발생합니다.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

일반적으로 패싯 결과가 지속적으로 너무 큰 경우에는 필터를 더 추가하여 사용자에게 검색 범위를 좁힐 수 있는 추가 옵션을 제공하는 것이 좋습니다.

### <a name="a-facet-only-search-experience"></a>패싯 전용 검색 환경

응용 프로그램에서 패싯 탐색을 독점적으로 사용 하는 경우 (즉, 검색 상자 없음) 필드를,로 표시 `searchable=false` `filterable=true` 하 여 `facetable=true` 더 간결한 인덱스를 생성할 수 있습니다. 인덱스는 반전 인덱스를 포함 하지 않으며 텍스트 분석 또는 토큰화가 없습니다. 필터는 문자 수준에서 정확히 일치 하는 항목에 적용 됩니다.

### <a name="validate-inputs-at-query-time"></a>쿼리 시간에 입력 유효성 검사

신뢰할 수 없는 사용자 입력을 기반으로 패싯 목록을 동적으로 빌드하는 경우 패싯 필드의 이름이 올바른지 확인합니다. 또는 .NET에서 `Uri.EscapeDataString()`을 사용하거나 본인이 선택한 플랫폼에서 그에 상응하는 옵션을 사용하여 URL을 빌드할 때 이름을 이스케이프합니다.

## <a name="demos-and-samples"></a>데모 및 샘플

여러 샘플에는 패싯 탐색이 포함 됩니다. 이 섹션에는 샘플에 대 한 링크가 있으며 각 클라이언트 라이브러리 및 언어에 사용 되는 언어에 대해서도 설명 합니다.

### <a name="create-your-first-app-in-c-razor"></a>C #에서 첫 번째 앱 만들기 (Razor)

C #의이 자습서 및 샘플 시리즈에는 [패싯 탐색에 초점을 맞춘 단원](tutorial-csharp-facets.md)이 포함 되어 있습니다. 이 솔루션은 ASP.NET MVC 앱 이며 프레젠테이션 계층은 Razor 클라이언트 라이브러리를 사용 합니다.

### <a name="add-search-to-web-apps-react"></a>웹 앱에 검색 추가 (React)

[C #](tutorial-csharp-overview.md), [Python](tutorial-python-overview.md)및 [JavaScript](tutorial-javascript-overview.md) 의 자습서 및 샘플에는 패싯 탐색 뿐만 아니라 필터, 제안 및 자동 완성이 포함 됩니다. 이러한 샘플에서는 프레젠테이션 계층에 대 한 React를 사용 합니다.

### <a name="nycjobs-sample-code-and-demo-ajax"></a>NYCJobs 샘플 코드 및 데모 (Ajax)

NYCJobs 샘플은 프레젠테이션 계층에서 Ajax를 사용 하는 ASP.NET MVC 응용 프로그램입니다. [GitHub의 Azure 샘플 리포지토리](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)에서 [라이브 데모 앱](https://aka.ms/azjobsdemo) 및 원본 코드로 사용할 수 있습니다.
