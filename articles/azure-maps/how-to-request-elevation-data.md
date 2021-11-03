---
title: Azure Maps Elevation Service를 사용한 상승 데이터 요청
description: Azure Maps Elevation Service를 사용하여 상승 데이터를 요청하는 방법을 알아보세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6562af274455297e12084353926478f1954c2407
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031407"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>Azure Maps Elevation Service를 사용한 상승 데이터 요청

Azure Maps [Elevation Service](/rest/api/maps/elevation)는 지구 표면의 모든 상승 데이터를 쿼리하는 API를 제공합니다. 정의된 경계 상자 또는 특정 좌표에서 경로를 따라 샘플링된 상승 데이터를 요청할 수 있습니다. 또한 [Render V2 - Get Map Tile API](/rest/api/maps/renderv2)를 사용하여 타일 형태의 상승 데이터를 검색할 수 있습니다. 타일은 GeoTIFF 래스터 형식으로 제공됩니다. 이 문서는 Azure Maps Elevation Service 및 Get Map Tile API를 사용하여 상승 데이터 요청 방법을 알려줍니다. 상승 데이터는 GeoJSON 및 GeoTiff 형식으로 요청할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Gen 1(S1) 또는 Gen 2 가격 책정 계층에서 Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Azure Maps의 인증에 대한 자세한 정보는 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="request-elevation-data-in-raster-tile-format"></a>래스터 타일 형식의 상승 데이터 요청

래스터 타일 형식의 상승 데이터를 요청하려면 [Render V2 - Get Map Tile API](/rest/api/maps/renderv2)를 사용하세요. 타일을 찾을 수 있는 경우 API는 타일을 GeoTIFF로 반환합니다. 그렇지 않은 경우 API는 0을 반환합니다. 모든 래스터 DEM 타일은 지오이드(해상 수준) 지구 모드를 사용합니다. 이 예에서는 산의 상승 데이터를 요청합니다. 에베레스트.

>[!TIP]
>세계 지도의 특정 영역에서 타일을 검색하려면 적절한 확대/축소 수준에서 올바른 타일을 찾아야 합니다. 또한 WorldDEM은 전체 대륙에 적용되지만 바다에는 적용되지 않는다는 점에 유의하십시오.  자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조하세요.

Postman 앱을 사용하여 래스터 타일 형식의 권한 상승 데이터를 요청하려면 다음을 수행합니다.

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름** 을 입력합니다.

4. **작성기** 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력하여 래스터 타일을 요청합니다.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

    >[!Important]
    >이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

5. **보내기** 단추를 선택합니다.

    상승 데이터가 포함된 래스터 타일을 GeoTIFF 형식으로 수신해야 합니다. 래스터 타일 원시 데이터 내의 각 픽셀은 `float` 유형입니다. 각 픽셀 값은 상승 높이(미터)를 나타냅니다.

## <a name="request-elevation-data-in-geojson-format"></a>GeoJSON 형식의 상승 데이터 요청

Elevation service API를 사용하여 GeoJSON 형식의 상승 데이터를 요청합니다. 이 섹션에서는 이러한 각 API에 대해 설명합니다.

* [포인트 데이터 가져오기](/rest/api/maps/elevation/getdataforpoints)
* [포인트 데이터 게시](/rest/api/maps/elevation/postdataforpoints)
* [폴리라인 데이터 가져오기](/rest/api/maps/elevation/getdataforpolyline)
* [폴리라인 데이터 게시](/rest/api/maps/elevation/postdataforpolyline)
* [경계 상자 데이터 가져오기](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> 데이터를 반환할 수 없는 경우 모든 API는 **0** 을 반환합니다.

### <a name="request-elevation-data-for-points"></a>포인트에 대한 상승 데이터 요청

이 예에서는 [포인트 데이터 가져오기 API](/rest/api/maps/elevation/getdataforpoints)를 사용하여 산의 상승 데이터를 요청합니다. 에베레스트 및 참랑 그런 다음, [포인트 데이터 게시 API](/rest/api/maps/elevation/postdataforpoints)를 사용하여 동일한 두 개의 포인트를 사용하는 상승 데이터를 요청합니다. URL의 위도 및 경도는 WGS84(World Geodetic System)의 십진수이어야 합니다.

 >[!IMPORTANT]
 >URL 문자 길이가 2048자로 제한되므로 URL GET 요청에서 100개 이상의 좌표를 파이프라인으로 구분된 문자열로 전달하는 것은 불가능합니다. 100개 이상의 좌표를 파이프라인으로 구분된 문자열로 전달하려는 경우 Points API용 Post Data를 사용하세요.

요청을 만들려면 다음을 수행합니다.

1. Postman 앱에서 **새로 만들기** 를 다시 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. 요청에 대한 **요청 이름** 을 입력합니다.

4. **작성기** 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다(키 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 대체).

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

5. **보내기** 단추를 선택합니다.  다음과 같은 JSON 응답이 표시됩니다.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

6. 이제 [포인트 데이터 게시 API](/rest/api/maps/elevation/postdataforpoints)를 호출하여 동일한 두 포인트의 상승 데이터를 가져옵니다. **작성기** 탭에서 **POST** HTTP 메서드를 선택하고 다음의 URL을 입력합니다(키 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 대체).

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

7. **POST** 요청의 **헤더** 필드에서 `Content-Type`을 `application/json`으로 설정합니다. 

8. **본문** 필드에서 다음의 좌표 포인트 정보를 작성하세요.

    ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

9. **보내기** 를 선택합니다.

### <a name="request-elevation-data-samples-along-a-polyline"></a>폴리라인을 따라 상승 데이터 샘플 요청

이 예에서는 [폴리라인 API로 데이터 가져오기](/rest/api/maps/elevation/getdataforpolyline)를 사용하여 산의 좌표 사이 직선을 따라 동일한 간격의 상승 데이터 샘플 5개를 요청합니다. 에베레스트 및 참랑 두 좌표는 모두 경도/위도 형식으로 정의되어야 합니다. `samples`매개 변수 값을 지정하지 않는 경우 샘플 수는 기본적으로 10개로 설정됩니다. 최대 샘플 수는 2,000개입니다.

그 후 폴리라인 API 데이터 가져오기를 통해 경로를 따라 동일한 간격의 상승 데이터 샘플 3개를 요청합니다. 3개의 경도/위도 좌표 쌍을 전달하여 샘플의 정확한 위치를 정의합니다.

마지막으로, [폴리라인 데이터 게시 API](/rest/api/maps/elevation/postdataforpolyline)를 사용하여 똑같이 동일한 간격의 샘플 3개에서 상승 데이터를 요청합니다.

URL의 위도 및 경도는 WGS84(World Geodetic System)의 십진수이어야 합니다.

 >[!IMPORTANT]
 >URL 문자 길이가 2048자로 제한되므로 URL GET 요청에서 100개 이상의 좌표를 파이프라인으로 구분된 문자열로 전달하는 것은 불가능합니다. 100개 이상의 좌표를 파이프라인으로 구분된 문자열로 전달하려는 경우 Points API용 Post Data를 사용하세요.

요청을 만들려면 다음을 수행합니다.

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. **요청 이름** 을 입력합니다.

4. **작성기** 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다(키 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 대체).

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

5. **보내기** 단추를 선택합니다.  다음과 같은 JSON 응답이 표시됩니다.

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

6. 이제 에베레스트, 참랑, 자누 산의 좌표 간 경로를 따라 상승 데이터 샘플 3개를 요청합니다. **Params** 필드에서 `lines` 쿼리 키 값에 대한 다음 좌표 배열을 입력합니다.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

7. `samples` 쿼리 키 값을 `3`로 변경합니다.  아래 이미지는 새로운 값을 보여줍니다.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="3개의 상승 데이터 샘플을 검색하세요.":::

8. **보내기** 를 선택합니다. 다음과 같은 JSON 응답이 표시됩니다.

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

9. 이제 [폴리라인 데이터 게시 API](/rest/api/maps/elevation/postdataforpolyline)를 호출하여 동일한 3개의 포인트에 대한 상승 데이터를 가져옵니다.  **작성기** 탭에서 **POST** HTTP 메서드를 선택하고 다음 URL을 입력합니다(기본 구독 키로 `{Azure-Maps-Primary-Subscription-key}` 대체).

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

10. **POST** 요청의 **헤더** 필드에서 `Content-Type`을 `application/json`으로 설정합니다. 

11. **본문** 필드에서 다음 좌표 포인트 정보를 작성하세요.

    ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

12. **보내기** 를 선택합니다.

### <a name="request-elevation-data-by-bounding-box"></a>경계 상자별 상승 데이터 요청

이제 [경계 상자 데이터 가져오기](/rest/api/maps/elevation/getdataforboundingbox)를 사용하여 산 근처의 상승 데이터를 요청합니다. 워싱턴 주 Rainier 상승 데이터는 경계 상자 내 동일한 간격의 위치에 반환됩니다. 경계 영역은 두 개의 경도/위도 좌표 세트(남위, 서경|북위, 동경)에 의해 정의되고 행과 열로 구분됩니다. 경계 상자의 가장자리는 두 개의 행과 두 개의 열을 차지합니다. 행과 열 교차점에 생성된 그리드 꼭짓점에 상승이 반환됩니다. 단일 요청으로 최대 2000개의 상승이 반환될 수 있습니다.

이 예에서는 rows=3 및 columns=6을 지정합니다. 응답은 18개의 상승값을 반환합니다. 다음 다이어그램에서 상승 값은 남서쪽 모퉁이에서 시작하여 서쪽에서 동쪽으로, 남쪽에서 북쪽으로 정렬됩니다.  상승 포인트는 반환되는 순서에 따라 번호가 매겨집니다.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="NE 및 SE 모퉁이의 경계 상자 좌표.":::

요청을 만들려면 다음을 수행합니다.

1. Postman 앱에서 **새로 만들기** 를 선택합니다.

2. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다.

3. **요청 이름** 을 입력합니다.

4. **작성기** 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력합니다(키 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 대체).

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

5. **보내기** 를 선택합니다.  응답은 18개의 상승 데이터 샘플을 그리드의 각 꼭짓점에 하나씩 반환합니다.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>샘플: Azure Maps Control에서 Elevation service API 사용

### <a name="get-elevation-data-by-coordinate-position"></a>좌표 위치별 상승 데이터 가져오기

다음 샘플 웹 페이지에서 지도 컨트롤을 사용하여 좌표 포인트에 상승 데이터를 표시하는 방법을 확인할 수 있습니다. 사용자가 마커를 끌어오면 상승 데이터가 지도에서 팝업 창에 표시됩니다.

<br/>

<iframe height="500" scrolling="no" title="위치에서 상승 가져오기" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 따라 펜 <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>위치에서 상승 가져오기</a>를 참조하세요.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>경계 상자별 상승 데이터 요청

다음 샘플 웹 페이지에는 지도 컨트롤을 사용하여 경계 상자에 포함된 상승 데이터를 표시하는 방법이 나와있습니다. 사용자는 왼쪽 위 모서리의 `square` 아이콘을 선택한 후 지도에 사각형을 그려 경계 상자를 정의합니다. 그런 다음 지도 컨트롤이 오른쪽 위 모서리에 있는 키에 지정된 색상에 따라 상승 데이터를 렌더링합니다.

<br/>

<iframe height="500" scrolling="no" title="경계 상자별 상승" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 따라 펜 <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>경계 상자별 상승</a>을 참조하세요.
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>폴리라인 경로별 상승 데이터 가져오기

다음 샘플 웹 페이지에는 지도 컨트롤을 사용하여 경로를 따라 상승 데이터를 표시하는 방법이 나와있습니다. 사용자는 왼쪽 위 모서리의 `Polyline` 아이콘을 선택한 후 지도에 폴리라인을 그려서 경계 상자를 정의합니다. 그런 다음 지도 컨트롤이 오른쪽 위 모서리에 있는 키에 지정된 색상으로 상승 데이터를 렌더링합니다.

<br/>

<iframe height="500" scrolling="no" title="상승 경로 그라데이션" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 따라 펜 <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>상승 경로 그라데이션</a>을 참조하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

Azure Maps Elevation API에 대해 자세히 살펴보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Elevation - Lat Long 좌표에 대한 데이터 가져오기](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Elevation - 경계 상자에 대한 데이터 가져오기](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Elevation - 폴리라인에 대한 데이터 가져오기](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [렌더링 V2 – 맵 타일 가져오기](/rest/api/maps/renderv2)

Azure Maps REST API의 전체 목록은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps REST API](/rest/api/maps/)
