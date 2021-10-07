---
title: '자습서: Bing Entity Search 단일 페이지 웹앱'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 단일 페이지 웹 애플리케이션에서 Bing Entity Search API를 사용하는 방법을 보여줍니다.
author: aahill
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 49ac0daf08e2b279396b12fa5fee74e8cdbbed7e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363535"
---
# <a name="tutorial-single-page-web-app"></a>자습서: 단일 페이지 웹앱

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Entity Search API를 사용하면 *엔터티* 및 *장소* 에 대한 정보를 웹에서 검색할 수 있습니다. 지정된 쿼리에서 두 종류의 결과 중 하나 또는 둘 다를 요청할 수 있습니다. 아래에는 장소 및 엔터티에 대한 정의가 나와 있습니다.

| 결과 | Description |
|-|-|
|엔터티|이름으로 찾을 수 있는 잘 알려진 사람, 장소 및 사물|
|장소|이름  *또는* 유형으로 찾을 수 있는 식당, 호텔, 기타 현지 기업|

이 자습서에서는 Bing Entity Search API를 사용하여 이 페이지에 검색 결과를 표시하는 단일 페이지 웹 애플리케이션을 빌드합니다. 애플리케이션에는 HTML, CSS 및 JavaScript 구성 요소가 포함됩니다.

API를 통해 위치별로 결과의 우선 순위를 지정할 수 있습니다. 모바일 앱에서는 디바이스에 해당 위치를 요청할 수 있습니다. 웹앱에서는 `getPosition()` 함수를 사용할 수 있습니다. 그러나 이 호출은 보안 컨텍스트에서만 작동하며, 정확한 위치를 제공하지 않을 수 있습니다. 사용자가 해당 위치가 아닌 위치의 주변 엔터티를 검색할 수도 있습니다.

따라서 앱은 Bing 지도 서비스를 사용하여 사용자가 입력한 위치에서 위도와 경도를 얻습니다. 사용자가 랜드마크 이름(“스페이스 니들”) 또는 전체 또는 부분 주소("뉴욕시")를 입력하면 Bing 지도 API가 좌표를 제공합니다.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> 클릭하면 페이지 맨 아래의 JSON 및 HTTP 제목에 JSON 응답 및 HTTP 요청 정보가 표시됩니다. 이러한 세부 정보는 서비스를 탐색할 때 유용합니다.

자습서 앱은 다음 방법을 보여 줍니다.

> [!div class="checklist"]
> * JavaScript에서 Bing Entity Search API 호출 수행
> * JavaScript에서 Bing 지도 `locationQuery` API 호출 수행
> * API 호출에 검색 옵션 전달
> * 검색 결과 표시
> * Bing 클라이언트 ID와 API 구독 키 처리
> * 발생할 수 있는 오류 처리

자습서 페이지는 완전히 독립적입니다. 즉, 외부 프레임워크, 스타일시트 또는 이미지 파일을 사용하지 않습니다. 이 페이지는 광범위하게 지원되는 JavaScript 언어 기능만 사용하며, 모든 주요 웹 브라우저의 현재 버전에서 작동합니다.

이 자습서에서는 소스 코드의 선택된 부분에 대해서만 설명합니다. 전체 소스 코드는 [별도 페이지에서]() 확인할 수 있습니다. 텍스트 편집기에 이 코드를 복사 및 붙여넣고 `bing.html`로 저장합니다.

> [!NOTE]
> 이 자습서는 [단일 페이지 Bing Web Search 앱 자습서](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)와 대체로 비슷하지만, 엔터티 검색 결과만 처리합니다.

## <a name="prerequisites"></a>필수 구성 요소

자습서를 따르려면 Bing Search API 및 Bing Maps API에 대한 구독 키가 필요합니다. 

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 다음을 수행합니다.
  * Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기 </a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
  * Azure Portal에서 <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 만들기 </a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.

## <a name="app-components"></a>앱 구성 요소

단일 페이지 웹앱과 마찬가지로, 자습서 애플리케이션은 다음 세 부분으로 구성되어 있습니다.

> [!div class="checklist"]
> * HTML - 페이지의 구조와 콘텐츠를 정의합니다.
> * CSS - 페이지의 모양을 정의합니다.
> * JavaScript - 페이지의 동작을 정의합니다.

이 자습서에서는 대부분의 HTML 또는 CSS를 자세히 다루지 않습니다. 간단하기 때문입니다.

HTML에는 사용자가 쿼리를 입력하고 검색 옵션을 선택하는 검색 양식이 포함되어 있습니다. 이 양식은 실제로 `<form>` 태그의 `onsubmit` 특성에 의해 검색을 수행하는 JavaScript에 연결됩니다.

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit` 처리기는 `false`를 반환하여 양식이 서버에 제출되지 못하게 합니다. JavaScript 코드는 실제로 양식에서 필요한 정보를 수집하고 검색을 수행합니다.

검색은 두 단계로 수행됩니다. 먼저 사용자가 위치 제한을 입력한 경우 좌표로 변환하기 위해 Bing 지도 쿼리가 수행됩니다. 이 쿼리에 대한 콜백이 Bing Entity Search 쿼리를 시작합니다.

또한 HTML에는 검색 결과가 나타나는 부분(HTML `<div>` 태그)이 포함됩니다.

## <a name="managing-subscription-keys"></a>구독 키 관리

> [!NOTE]
> 이 앱을 사용하려면 Bing Search API와 Bing Maps API 둘 다의 구독 키가 필요합니다.

Bing Search 및 Bing 지도 API 구독 키를 코드에 포함할 필요가 없도록, 여기서는 브라우저의 영구적 스토리지를 사용하여 키를 저장합니다. 키가 저장되지 않은 경우 확인 메시지를 표시하고, 나중에 사용할 수 있도록 저장합니다. 키가 나중에 API에서 거부될 경우 저장된 키를 무효화하므로, 사용자가 다음에 검색할 때 키가 요청됩니다.

`localStorage` 개체(브라우저가 지원하는 경우) 또는 쿠키를 사용하는 `storeValue` 및 `retrieveValue` 함수를 정의합니다. `getSubscriptionKey()` 함수는 이러한 함수를 사용하여 사용자 키를 저장하고 검색합니다. 아래의 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML `<body>` 태그에는 페이지 로드가 완료될 때 `getSearchSubscriptionKey()` 및 `getMapsSubscriptionKey()`를 호출하는 `onload` 특성이 포함됩니다. 이러한 호출은 사용자가 아직 키를 입력하지 않은 경우 즉시 사용자에게 키를 확인하는 역할을 합니다.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>검색 옵션 선택

![[Bing Entity Search 양식]](media/entity-search-spa-form.png)

HTML 양식에는 다음과 같은 컨트롤이 포함됩니다.

| 제어 | Description |
|-|-|
|`where`|검색에 사용되는 지역/국가(위치 및 언어)를 선택하기 위한 드롭다운 메뉴입니다.|
|`query`|검색어를 입력할 텍스트 필드입니다.|
|`safe`|유해 정보 차단이 켜져 있는지 여부를 나타내는 확인란입니다(“성인” 결과 제한).|
|`what`|엔터티, 장소 또는 둘 다를 검색하도록 선택하기 위한 메뉴입니다.|
|`mapquery`|Bing Entity Search에서 보다 관련된 결과를 반환할 수 있도록 사용자가 전체 또는 부분 주소, 랜드마크 등을 입력하는 텍스트 필드입니다.|

> [!NOTE]
> 장소 결과는 현재 미국에서만 사용할 수 있습니다. `where` 및 `what` 메뉴에는 이 제한을 적용하는 코드가 있습니다. `what` 메뉴에서 장소가 선택된 상태로 미국 외 지역/국가를 선택하면 `what`이 Anything으로 바뀝니다. `where` 메뉴에서 미국 외 지역/국가가 선택된 상태로 장소를 선택하면 `where`가 US로 바뀝니다.

JavaScript 함수 `bingSearchOptions()`는 이러한 필드를 Bing Search API용 부분 쿼리 문자열로 변환합니다.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

예를 들어 유해 정보 차단 기능은 `moderate`를 기본값으로 사용하는 `strict`, `moderate` 또는 `off`일 수 있습니다. 그러나 양식에서는 두 개의 상태만 있는 확인란을 사용합니다. JavaScript 코드는 이 설정을 `strict` 또는 `off`로 변환합니다(`moderate`를 사용하지 않음).

`mapquery` 필드는 Bing Entity Search가 아니라 Bing 지도 위치 쿼리에 사용되므로 `bingSearchOptions()`에서 처리되지 않습니다.

## <a name="obtaining-a-location"></a>위치 가져오기

Bing 지도 API는 사용자가 입력한 위치의 위도와 경도를 찾는 데 사용되는 [`locationQuery` 메서드](//msdn.microsoft.com/library/ff701711.aspx)를 제공합니다. 이 좌표는 사용자 요청과 함께 Bing Entity Search API로 전달됩니다. 검색 결과에서는 지정된 위치에 가까운 엔터티와 장소에 우선 순위가 지정됩니다.

서비스에서 원본 간 쿼리를 지원하지 않기 때문에 웹앱에서 일반 `XMLHttpRequest` 쿼리를 사용하여 Bing 지도 API에 액세스할 수 없습니다. 다행히, JSONP(“P”는 “padded(채워짐)을 나타냄)가 지원됩니다. JSONP 응답은 함수 호출에 래핑된 일반 JSON 응답입니다. `<script>` 태그 사용을 문서에 삽입하여 요청이 수행됩니다. 스크립트 로드에는 브라우저 보안 정책이 적용되지 않습니다.

`bingMapsLocate()` 함수는 쿼리에 대한 `<script>` 태그를 만들고 삽입합니다. 쿼리 문자열의 `jsonp=bingMapsCallback` 세그먼트는 응답과 함께 호출할 함수의 이름을 지정합니다.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Bing 지도 API가 응답하지 않으면 `bingMapsCallBack()` 함수는 호출되지 않습니다. 일반적으로, 이 경우 `bingEntitySearch()`가 호출되지 않고 엔터티 검색 결과가 나타나지 않습니다. 이 시나리오를 방지하기 위해 `bingMapsLocate()`는 5초 후에 `bingEntitySearch()`를 호출하도록 타이머도 설정합니다. 콜백 함수에는 엔터티 검색이 두 번 수행되지 않도록 방지하는 논리가 있습니다.

쿼리가 완료되면 요청된 대로 `bingMapsCallback()` 함수가 호출됩니다.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Bing Entity Search 쿼리에는 위도 및 경도와 함께, 위치 정보의 정밀도를 나타내는 반경가 필요합니다. Bing 지도 응답에 제공된 경계 상자를 사용하여 반경을 계산합니다. 경계 상자는 전체 위치를 둘러싸는 사각형입니다. 예를 들어 사용자가 `NYC`를 입력하는 경우 결과에는 뉴욕시의 대략적인 중심 좌표와 도시를 둘러싸는 경계 상자가 포함됩니다. 

먼저 `haversineDistance()` 함수(표시되지 않음)를 사용하여 기본 좌표에서 경계 상자의 네 모서리까지의 거리를 각각 계산합니다. 이 네 개의 거리 중 가장 큰 값을 반경으로 사용합니다. 최소 반경은 1킬로미터입니다. 이 값은 응답에 경계 상자가 제공되지 않은 경우 기본값으로도 사용됩니다.

좌표와 반경을 구했으면 `bingEntitySearch()`를 호출하여 실제 검색을 수행합니다.

## <a name="performing-the-search"></a>검색 수행

쿼리, 위치, 옵션 문자열 및 API 키가 제공되면 `BingEntitySearch()` 함수에서 Bing Entity Search 요청을 수행합니다.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");

    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 요청이 성공적으로 완료되면 JavaScript는 `load` 이벤트 처리기인 `handleBingResponse()` 함수를 호출하여 API에 대한 성공적인 HTTP GET 요청을 처리합니다. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> 성공적인 HTTP 요청이 반드시 검색 자체가 성공했음을 의미하는 것은 *아닙니다*. 검색 작업에서 오류가 발생할 경우 Bing Entity Search API는 200 이외의 HTTP 상태 코드를 반환하고, JSON 응답에 오류 정보를 포함합니다. 또한 요청의 속도가 제한된 경우 API에서 빈 응답을 반환합니다.

위의 두 함수에 포함된 코드는 대부분 오류 처리에만 사용됩니다. 다음 단계에서 오류가 발생할 수 있습니다.

|단계|잠재적 오류|처리 방법|
|-|-|-|
|JavaScript 요청 개체 빌드|잘못된 URL|`try`/`catch` 블록|
|요청하기|네트워크 오류, 중단된 연결|`error` 및 `abort` 이벤트 처리기|
|검색 수행|잘못된 요청, 잘못된 JSON, 속도 제한|`load` 이벤트 처리기에서 테스트|

오류는 오류에 대한 알려진 정보로 `renderErrorMessage()`를 호출하여 처리됩니다. 응답이 전체 오류 테스트 단계를 통과하면 `renderSearchResults()`를 호출하여 페이지에 검색 결과를 표시합니다.

## <a name="displaying-search-results"></a>검색 결과 표시

Bing Entity Search API[에서는 지정된 순서로 결과를 표시해야 합니다](../bing-web-search/use-display-requirements.md). API가 두 가지 종류의 응답을 반환할 수 있으므로 JSON 응답에서 최상위 `Entities` 또는 `Places` 컬렉션을 반복하고 해당 결과를 표시하는 것으로는 충분하지 않습니다. 한 가지 유형의 결과만 표시하려면 `responseFilter` 쿼리 매개 변수를 사용합니다.

대신, 검색 결과에서 `rankingResponse` 컬렉션을 사용하여 표시할 결과를 정렬합니다. 이 개체는 `Entitiess` 및/또는 `Places` 컬렉션의 항목을 나타냅니다.

`rankingResponse`에는 `pole`, `mainline` 및 `sidebar`로 지정된 검색 결과 컬렉션이 최대 세 개까지 포함될 수 있습니다. 

있는 경우 `pole`이 가장 관련된 검색 결과이며, 잘 보이게 표시되어야 합니다. `mainline`은 대량 검색 결과를 나타냅니다. 기본 줄 결과는 `pole` 바로 뒤에(또는 `pole`이 없는 경우 첫 번째로) 표시되어야 합니다. 

마지막으로, `sidebar`는 보조 검색 결과를 나타냅니다. 실제 사이드바에 표시되거나, 단순히 기본 줄 결과 다음에 표시될 수 있습니다. 자습서 앱에서는 후자를 선택했습니다.

`rankingResponse` 컬렉션의 각 항목은 서로 다르지만 동등한 두 가지 방식으로 실제 검색 결과 항목을 나타냅니다.

| 항목 | Description |
|-|-|
|`id`|`id`는 URL처럼 보이지만 링크에 사용하면 안 됩니다. 순위 결과의 `id` 형식은 답변 컬렉션의 검색 결과 항목 또는 전체 답변 컬렉션(예: `Entities`)의 `id`와 일치합니다.
|`answerType`<br>`resultIndex`|`answerType`은 결과가 포함된 최상위 답변 컬렉션(예: `Entities`)을 나타냅니다. `resultIndex`는 해당 컬렉션 내의 결과 인덱스를 나타냅니다. `resultIndex`가 생략된 경우 순위 결과는 전체 컬렉션을 나타냅니다.

> [!NOTE]
> 검색 응답에서 이 부분에 대한 자세한 내용은 [결과에 순위 지정](rank-results.md)을 참조하세요.

참조된 검색 결과 항목을 찾는 메서드 중 애플리케이션에 가장 편리한 메서드를 사용할 수 있습니다. 자습서 코드에서는 `answerType` 및 `resultIndex`를 사용하여 각 검색 결과를 찾습니다.

마지막으로, `renderSearchResults()` 함수를 확인합니다. 이 함수는 검색 결과의 세 가지 섹션을 나타내는 세 개의 `rankingResponse` 컬렉션을 반복합니다. 각 섹션에서 `renderResultsItems()`를 호출하여 해당 섹션에 대한 결과를 렌더링합니다.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>결과 항목 렌더링

JavaScript 코드에는 각 종류의 검색 결과에 대해 HTML을 생성하는 렌더러 함수가 포함된 `searchItemRenderers` 개체가 있습니다.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

렌더러 함수는 다음 매개 변수를 사용할 수 있습니다.

| 매개 변수 | Description |
|-|-|
|`item`|URL 및 해당 설명과 같은 항목의 속성을 포함하는 JavaScript 개체입니다.|
|`index`|해당 컬렉션 내에서 결과 항목의 인덱스입니다.|
|`count`|검색 결과 항목 컬렉션에 있는 항목 수입니다.|

`index` 및 `count` 매개 변수는 결과에 번호를 지정하고, 컬렉션 시작 또는 끝에 대한 특수 HTML을 생성하고, 특정 개수의 항목 다음에 줄 바꿈을 삽입하는 등의 작업에 사용될 수 있습니다. 렌더러에 이 기능이 필요하지 않은 경우 이러한 두 매개 변수를 사용하지 않아도 됩니다. 실제로 자습서 앱용 렌더러에서는 사용하지 않습니다.

`entities` 렌더러를 좀 더 자세히 살펴보겠습니다.

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

엔터티 렌더러는 다음과 같이 작동합니다.

> [!div class="checklist"]
> * 이미지 썸네일(있는 경우)을 표시할 HTML `<img>` 태그를 빌드합니다. 
> * 이미지가 포함된 페이지로 연결되는 HTML `<a>` 태그를 빌드합니다.
> * 이미지와 해당 이미지가 있는 사이트에 대한 정보를 표시하는 설명을 빌드합니다.
> * 표시 힌트(있는 경우)를 사용하여 엔터티의 분류를 통합합니다.
> * 엔터티에 대한 자세한 정보를 얻기 위해 Bing 검색에 대한 링크를 포함합니다.
> * 데이터 원본에 필요한 라이선스 또는 특성 정보를 표시합니다.

## <a name="persisting-client-id"></a>클라이언트 ID 유지

Bing Search API의 응답에는 후속 요청과 함께 API로 다시 전송되어야 하는 `X-MSEdge-ClientID` 헤더가 포함될 수 있습니다. 여러 개의 Bing Search API를 사용하는 경우 모두 동일한 클라이언트 ID를 사용해야 합니다(가능한 경우).

`X-MSEdge-ClientID` 헤더를 제공하면 Bing API가 모든 사용자 검색을 연결할 수 있습니다. 이렇게 하면 두 가지 중요한 이점이 있습니다.

첫째, Bing 검색 엔진이 이전 컨텍스트를 검색에 적용하여 사용자에게 보다 만족스러운 결과를 찾을 수 있습니다. 예를 들어 사용자가 이전에 항해와 관련된 용어를 검색한 경우 나중에 “docks”를 검색하면 보트를 정박할 수 있는 장소에 대한 정보가 우선적으로 반환될 수 있습니다.

둘째, Bing을 통해 임의로 선택된 사용자가 새로운 기능이 폭넓게 사용되기 전에 이러한 새 기능을 경험해볼 수 있습니다. 각 요청에 동일한 클라이언트 ID를 제공하면 기능을 볼 수 있도록 선택된 사용자에게 항상 기능이 표시됩니다. 클라이언트 ID가 없으면 사용자의 검색 결과에서 기능이 임의로 나타나거나 사라지는 것처럼 보일 수 있습니다.

브라우저 보안 정책(CORS) 때문에 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용하지 못할 수 있습니다. 이러한 제한은 검색 응답의 원본이 해당 응답을 요청한 페이지와 다른 경우에 발생합니다. 프로덕션 환경에서는 웹 페이지와 동일한 도메인에 대해 API 호출을 수행하는 서버 쪽 스크립트를 호스트하여 이 정책 문제를 해결해야 합니다. 스크립트의 원본은 웹 페이지와 동일하므로 JavaScript에서 `X-MSEdge-ClientID` 헤더를 사용할 수 있습니다.

> [!NOTE]
> 그래도 프로덕션 웹 애플리케이션의 경우 서버 쪽에서 요청을 수행해야 합니다. 그렇지 않은 경우 Bing Search API 키를 웹 페이지에 포함해야만 원본을 보는 누구나 사용할 수 있게 됩니다. 권한 없는 사람이 수행한 요청을 포함하여 API 구독 키를 통한 모든 사용량에 요금이 청구되므로, 키를 노출하지 않는 것이 중요합니다.

개발 목적으로 CORS 프록시를 통해 Bing Web Search API 요청을 수행할 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 나열하고 JavaScript에 응답 헤더를 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 포함됩니다.

자습서 앱이 클라이언트 ID 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 창에서 다음 명령을 실행합니다.

```console
npm install -g cors-proxy-server
```

다음으로, HTML 파일에서 Bing Web Search 엔드포인트를 다음 항목으로 변경합니다.
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

```console
cors-proxy-server
```

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Entity Search API 참조](/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing 지도 API 문서](/bingmaps/)