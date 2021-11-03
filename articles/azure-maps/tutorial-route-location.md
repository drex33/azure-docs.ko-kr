---
title: '자습서: 위치에 대한 경로 찾기 | Microsoft Azure Maps'
description: 관심 지점까지의 경로를 찾는 방법에 대한 자습서. 주소 좌표를 설정하는 방법을 참조하고 Azure Maps Route 서비스를 쿼리하여 지점으로 가는 방향을 확인합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: cf5377c872c086675136f5b1a52ceb15c8b3b461
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039148"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>자습서: Azure Maps Route Service 및 지도 컨트롤을 사용하여 경로 방향을 표시하는 방법

이 자습서에서는 Azure Maps [Route Service API](/rest/api/maps/route) 및 [지도 컨트롤](./how-to-use-map-control.md)을 사용하여 시작점부터 도착점까지의 경로 방향을 표시하는 방법을 보여 줍니다. 이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 웹 페이지에서 지도 컨트롤을 만들고 표시합니다. 
> * [기호 계층](map-add-pin.md) 및 [선 계층](map-add-line-layer.md)을 정의하여 경로의 표시 렌더링을 정의합니다.
> * GeoJSON 개체를 만들고 맵에 추가하여 시작점과 도착점을 나타냅니다.
> * [경로 방향 API 가져오기](/rest/api/maps/route/getroutedirections)를 사용하여 시작점부터 도착점까지의 경로 방향을 가져옵니다.

[여기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)에서 샘플의 전체 소스 코드를 가져올 수 있습니다. 라이브 샘플은 [여기](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>지도 컨트롤 만들기 및 표시

다음 단계에서는 웹 페이지에서 지도 컨트롤을 만들고 표시하는 방법을 보여 줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapRoute.html** 로 지정합니다.
2. 다음 HTML 태그를 복사하여 파일에 붙여넣습니다.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    HTML 헤더에는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일이 포함됩니다. 본문의 `onload` 이벤트는 `GetMap` 함수를 호출합니다. 다음 단계에서는 지도 컨트롤 초기화 코드를 추가하겠습니다.

3. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. 문자열 `<Your Azure Maps Key>`를 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```javascript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. 파일을 저장하고 브라우저에서 엽니다. 간단하게 표시됩니다.

    :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="지도 컨트롤의 기본 지도 렌더링":::

## <a name="define-route-display-rendering"></a>경로 표시 렌더링 정의

이 자습서에서는 선 계층을 사용하여 경로를 렌더링하겠습니다. 시작점과 도착점은 기호 계층을 사용하여 렌더링됩니다. 선 계층을 추가하는 방법에 대한 자세한 내용은 [지도에 선 계층 추가](map-add-line-layer.md)를 참조하세요. 기호 계층에 대한 자세한 내용은 [지도에 기호 계층 추가](map-add-pin.md)를 참조하세요.

1. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. 이 코드는 지도 컨트롤의 `ready` 이벤트 처리기를 구현합니다. 이 자습서의 나머지 코드는 `ready` 이벤트 처리기 내에 배치됩니다.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```

    지도 컨트롤의 `ready` 이벤트 처리기에서 시작점부터 도착점까지의 경로를 저장하는 데이터 원본이 만들어집니다. 경로 선이 렌더링되는 방식을 정의하기 위해 선 계층을 만들어 데이터 원본에 연결합니다.  경로 선이 도로 레이블을 포함하지 않도록 하기 위해 두 번째 매개 변수를 `'labels'` 값으로 전달했습니다.

    다음으로, 기호 계층이 생성되어 데이터 원본에 연결됩니다. 이 레이어는 출발 지점 및 도착 지점이 렌더링되는 방법을 지정합니다. 각 지점 개체의 속성에서 아이콘 이미지 및 텍스트 레이블 정보를 검색하는 식이 추가되었습니다. 식에 대한 자세한 내용은 [데이터 기반 스타일 식](data-driven-style-expressions-web-sdk.md)을 참조하세요.

2. Microsoft를 시작점으로 설정하고, 시애틀의 주유소를 도착점으로 설정합니다.  지도 컨트롤의 `ready` 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    이 코드는 데이터 원본에 추가되는 출발점과 도착점을 나타낼 두 개의 [GeoJSON 지점 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. 

    마지막 코드 블록은 출발점과 도착점의 위도 및 경도를 사용하여 카메라 보기를 설정합니다. 시작 지점과 끝 지점이 데이터 원본에 추가됩니다. `atlas.data.BoundingBox.fromData` 함수를 사용하여 시작 및 끝 지점의 경계 상자가 계산됩니다. 이 경계 상자는 `map.setCamera` 함수를 사용하여 전체 경로에 대해 맵 카메라 보기를 설정하는 데 사용됩니다. 기호 아이콘의 픽셀 크기를 보정하기 위해 안쪽 여백이 추가됩니다. 지도 컨트롤의 setCamera 속성에 대한 자세한 내용은 [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)를 참조하세요.

3. **MapRoute.html** 을 저장하고, 브라우저를 새로 고칩니다. 이제 지도 중심이 시애틀로 이동됩니다. 물방울 모양 파란색 핀은 시작점을 표시합니다. 둥근 파란색 핀은 도착점을 표시합니다.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="맵에서 경로 시작점과 종료점 보기":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>경로 방향 가져오기

이 섹션에서는 Azure Maps 경로 방향 API를 사용하여 한 지점에서 다른 지점까지의 경로 방향 및 예상 도착 시간을 가져오는 방법을 보여줍니다.

>[!TIP]
>Azure Maps 경로 서비스는 거리, 트래픽 조건 및 사용되는 운송 모드를 기준으로 *가장 빠른*, *가장 짧은*, *에코*, *스릴* 경로와 같은 다양한 경로 유형을 기반으로 경로를 계획하는 API를 제공합니다. 서비스를 통해 사용자는 과거 트래픽 조건을 기준으로 향후 경로를 계획할 수도 있습니다. 사용자는 지정된 시간 동안 경로 기간의 예측을 확인할 수 있습니다. 자세한 내용은 [경로 방향 API 가져오기](/rest/api/maps/route/getroutedirections)를 참조하세요.

1. `GetMap` 함수에서 컨트롤의 `ready` 이벤트 처리기 내에 다음을 JavaScript 코드에 추가합니다.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`은 구독 키를 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `SubscriptionKeyCredentialPolicy`를 만듭니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

2. 자격 증명과 URL을 설정한 후에는 컨트롤의 `ready` 이벤트 처리기에 다음 코드를 추가합니다. 이 코드는 시작점부터 도착점까지의 경로를 생성합니다. `routeURL`은 Azure Maps Route Service API를 요청하여 경로 방향을 계산합니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가합니다.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. **MapRoute.html** 파일을 저장하고, 웹 브라우저를 새로 고칩니다. 이제 지도에 시작점부터 도착점까지의 경로가 표시됩니다.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Azure 지도 컨트롤 및 Route Service":::

[여기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)에서 샘플의 전체 소스 코드를 가져올 수 있습니다. 라이브 샘플은 [여기](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)에서 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

정리가 필요한 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

다음 자습서에서는 이동 모드 또는 화물 유형과 같은 제한을 적용하여 경로 쿼리를 만드는 방법을 보여 줍니다. 그런 다음, 여러 경로를 동일한 지도에 표시할 수 있습니다.

> [!div class="nextstepaction"]
> [여러 이동 모드에 대한 경로 찾기](./tutorial-prioritized-routes.md)