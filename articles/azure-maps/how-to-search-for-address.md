---
title: Azure Maps Search Service를 사용하여 위치 검색
description: Azure Maps Search Service에 대해 알아봅니다. 지오코딩, 역방향 지오코딩, 유사 항목 검색, 역방향 교차로 검색에 해당 API 집합을 사용하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ec92bb8bbfdc0f1598148b674f8e5de17e69afe3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031435"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure Maps Search Service를 사용하여 위치 검색

[Azure 지도 Search Service는](/rest/api/maps/search) 개발자가 이름, 범주 및 기타 지리적 정보를 기준으로 주소, 장소 및 비즈니스 목록을 검색할 수 있도록 설계된 RESTful API 세트입니다. 관련 서비스는 기존의 지오코딩을 지원할 뿐만 아니라 위도와 경도를 바탕으로 주소와 교차로를 역으로 지오코딩할 수 있습니다. 검색에서 얻은 위도와 경도 값을 [Route](/rest/api/maps/route) 및 [Weather](/rest/api/maps/weather)와 같은 다른 Azure Maps 서비스에서 매개 변수로 사용할 수 있습니다.


이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* [Search Address API](/rest/api/maps/search/getsearchaddress)를 사용하여 주소의 위도와 경도 좌표(지역 코드 주소 위치)를 요청합니다.
* [Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)를 사용하여 주소나 POI(관심 지점)를 검색합니다.
* [역 주소 검색](/rest/api/maps/search/getsearchaddressreverse)을 수행하여 좌표 위치를 주소로 변환합니다.
* [Search Address Reverse Cross Street API](/rest/api/maps/search/getsearchaddressreversecrossstreet)를 사용하여 좌표 위치를 사람이 이해할 수 있는 교차로로 변환합니다.  대부분의 경우 이는 디바이스나 자산에서 GPS 피드를 수신하고 좌표의 위치를 알고자 하는 추적 애플리케이션에 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>주소의 위도와 경도 요청(지오코딩)

이번 예제에서는 Azure Maps [Get Search Address API](/rest/api/maps/search/getsearchaddress)를 사용하여 주소를 위도와 경도 좌표로 변환합니다. 이 프로세스를 ‘지오코딩’이라고도 합니다. 응답은 좌표뿐만 아니라 거리, 우편 번호, 시, 국가/지역 정보와 같은 자세한 주소 속성까지 반환합니다.

>[!TIP]
>지오코딩할 주소 집합이 있는 경우 [Post Search Address Batch API](/rest/api/maps/search/postsearchaddressbatch)를 사용하여 한 번의 API 호출로 쿼리를 일괄 전송할 수 있습니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이번 요청에서는 특정 주소 `400 Braod St, Seattle, WA 98109`를 검색합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

3. 파란색 **보내기** 단추를 클릭합니다. 응답 본문에는 단일 위치의 데이터가 포함됩니다.

4. 이제 위치가 두 곳 이상일 수 있는 주소를 검색합니다. **Params** 섹션에서 `query` 키를 `400 Broad, Seattle`로 변경합니다. 파란색 **보내기** 단추를 클릭합니다.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="주소 검색":::

5. 그런 다음, `query` 키를 `400 Broa`로 설정해 봅니다.

6. **보내기** 단추를 클릭합니다. 이번 응답에는 여러 국가의 응답이 포함되어 있는 것을 확인할 수 있습니다. 결과를 사용자와 관련 있는 영역에 지오바이어싱하려면 항상 가능한 한 많은 위치 정보를 요청에 추가합니다.

## <a name="using-fuzzy-search-api"></a>Fuzzy Search API 사용

Azure Maps [Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)는 표준 단일 줄과 자유 형식 검색을 지원합니다. 검색 요청의 사용자 입력 유형을 모르는 경우 Azure Maps Search Fuzzy API를 사용하는 것이 좋습니다.  쿼리 입력은 전체 또는 부분 주소가 될 수 있습니다. POI(관심 지점) 토큰(예: POI 이름, POI 범주, 브랜드 이름)이 될 수도 있습니다. 또한 검색 결과의 관련성을 향상하기 위해 좌표 위치와 반경을 사용하거나 경계 상자를 정의함으로써 쿼리 결과를 제한할 수 있습니다.

>[!TIP]
>대부분의 검색 쿼리는 성능을 향상하고 비정상적인 결과를 줄이기 위해 기본적으로 maxFuzzyLevel = 1로 설정됩니다. `maxFuzzyLevel` 또는 `minFuzzyLevel` 매개 변수를 사용하여 유사 정도를 조정할 수 있습니다. `maxFuzzyLevel` 및 선택적 매개 변수의 전체 목록에 대한 자세한 내용은 [유사 항목 검색 URI 매개 변수](/rest/api/maps/search/getsearchfuzzy#uri-parameters)를 참조하세요.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

이번 예제에서는 유사 항목 검색을 사용하여 전 세계의 `pizza`를 검색합니다.  그다음, 검색을 특정 국가로 한정하는 방법을 보여 줍니다. 마지막으로, 좌표 위치와 반경을 사용하여 검색 범위를 특정 영역으로 한정하고 반환된 결과 수를 제한하는 방법을 보여 줍니다.

>[!IMPORTANT]
>결과를 사용자와 관련 있는 영역에 지오바이어싱하려면 항상 최대한 많은 위치 정보를 추가합니다. 자세한 내용은 [검색 모범 사례](how-to-use-best-practices-for-search.md#geobiased-search-results)를 참조하세요.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL 경로의 _json_ 특성은 응답 형식을 결정합니다. 이 문서에서는 사용 편의성과 가독성을 위해 json을 사용합니다. 지원되는 기타 응답 형식을 찾으려면 [URI 매개 변수 참조 설명서](/rest/api/maps/search/getsearchfuzzy#uri-parameters)에서 `format` 매개 변수 정의를 참조하세요.

3. **보내기** 를 클릭하고 응답 본문을 검토합니다.

    “pizza”라는 모호한 쿼리 문자열을 사용하자 “pizza”(피자) 범주와 “restaurant”(음식점) 범주에 모두 속하는 [POI(관심 지점) 결과](/rest/api/maps/search/getsearchpoi#searchpoiresponse) 10개가 반환되었습니다. 각 결과는 해당 위치에 대한 주소, 위도와 경도 값, 뷰포트, 진입점과 같은 세부 정보를 포함합니다. 해당 쿼리는 이제 다양한 결과를 반환하며, 결과는 어떠한 참조 위치에도 매여 있지 않습니다.
  
    다음 단계에서는 `countrySet` 매개 변수를 사용하여 애플리케이션의 검색 범위를 특정한 국가/지역으로 한정합니다. 지원되는 국가/지역의 전체 목록은 [검색 범위](./geocoding-coverage.md)를 참조하세요.

4. 기본 동작은 전 세계를 검색하여 불필요할 수 있는 결과까지 반환하는 것입니다. 다음으로, 미국에서만 피자를 검색합니다. **Params** 섹션에 `countrySet` 키를 추가하고 해당 값을 `US`로 설정합니다. `countrySet` 키를 `US`로 설정하면 결과가 미국에 바인딩됩니다.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="미국에 있는 피자 검색":::

    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.

5. 훨씬 더 많은 대상을 검색하려면 위도/경도 범위를 검색할 수 있습니다. 좌표 쌍입니다. 이번 예제에서는 시애틀 스페이스 니들의 위도/경도를 사용합니다. 400미터 반경 안에 있는 결과만 반환하고자 하기 때문에 `radius` 매개 변수를 추가합니다. 또한 `limit` 매개 변수를 추가하여 가장 가까운 5개의 피자 가게로 결과를 제한합니다.

    **Params** 섹션에서 다음 키/값 쌍을 추가합니다.

     | 키 | 값 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. **보내기** 를 클릭합니다. 응답에는 시애틀 스페이스 니들 근처의 피자 식당에 대한 결과가 포함됩니다.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>역방향 주소 검색을 사용하여 주소 검색

Azure Maps [Get Search Address Reverse API](/rest/api/maps/search/getsearchaddressreverse)는 좌표를 사람이 읽을 수 있는 주소로 변환합니다. GPS 피드를 사용하여 특정 좌표점에 있는 주소를 검색하려는 애플리케이션에서 해당 API가 자주 사용됩니다.

>[!IMPORTANT]
>결과를 사용자와 관련 있는 영역에 지오바이어싱하려면 항상 최대한 많은 위치 정보를 추가합니다. 자세한 내용은 [검색 모범 사례](how-to-use-best-practices-for-search.md#geobiased-search-results)를 참조하세요.

>[!TIP]
>역방향 지오코딩할 좌표 위치 집합이 있는 경우 [Post Search Address Reverse Batch API](/rest/api/maps/search/postsearchaddressreversebatch)를 사용하여 한 번의 API 호출로 쿼리를 일괄 전송할 수 있습니다.

이번 예제에서는 사용할 수 있는 몇 가지 선택적 매개 변수를 사용하여 역방향 검색을 수행합니다. 선택적 매개 변수의 전체 목록은 [ 역방향 검색 매개 변수](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)를 참조하세요.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. **보내기** 를 클릭하고 응답 본문을 검토합니다. 하나의 쿼리 결과가 표시됩니다. 응답에는 Safeco Field에 대한 주요 주소 정보가 포함됩니다.
  
4. 이제 **Params** 섹션에 다음 키/값 쌍을 추가합니다.

    | 키 | 값 | 반환
    |-----|------------|------|
    | number | 1 |응답은 도로의 측면(왼쪽/오른쪽) 및 해당 번호에 대한 오프셋 위치를 포함할 수 있습니다.|
    | returnSpeedLimit | true | 해당 주소에서의 제한 속도를 반환합니다.|
    | returnRoadUse | true | 해당 주소에서의 도로 용도 유형을 반환합니다. 가능한 도로 용도 유형을 모두 확인하려면 [도로 용도 유형](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)을 참조하세요.|
    | returnMatchType | true| 일치 유형을 반환합니다. 가능한 값을 모두 확인하려면 [역방향 주소 검색 결과](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)를 참조하세요.

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="역방향으로 검색합니다.":::

5. **보내기** 를 클릭하고 응답 본문을 검토합니다.

6. 다음으로, `entityType` 키를 추가하고 해당 값을 `Municipality`로 설정합니다. `entityType` 키가 이전 단계의 `returnMatchType` 키를 재정의합니다. 시에 대한 정보를 요청하고 있으므로 `returnSpeedLimit` 및 `returnRoadUse`도 제거해야 합니다.  가능한 엔터티 형식을 모두 확인하려면 [엔터티 형식](/rest/api/maps/search/getsearchaddressreverse#entitytype)을 참조하세요.

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="역방향 entityType 검색":::

7. **보내기** 를 클릭합니다. 해당 결과를 5단계에서 반환된 결과와 비교합니다.  이번에 요청된 엔터티 형식은 `municipality`이므로 응답에 주소 정보가 포함되어 있지 않습니다. 또한 반환된 `geometryId`를 사용하여 Azure Maps [Get Search Polygon API](/rest/api/maps/search/getsearchpolygon)를 통해 경계 다각형을 요청할 수 있습니다.

>[!TIP]
>해당 매개 변수와 기타 매개 변수에 대한 자세한 내용은 [역방향 검색 매개 변수 섹션](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)을 참조하세요.

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>역방향 주소 교차로 검색을 사용하여 교차로 검색

이번 예제에서는 주소의 좌표를 기준으로 교차로를 검색합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다. 요청은 다음 URL과 같습니다.
  
    ```http
    https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="교차로 검색":::
  
3. **보내기** 를 클릭하고 응답 본문을 검토합니다. `South Atlantic Street`의 `crossStreet` 값이 응답에 포함되어 있음을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service 모범 사례](how-to-use-best-practices-for-search.md)