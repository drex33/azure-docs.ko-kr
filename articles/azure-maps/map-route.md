---
title: 맵에 경로 방향 표시 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 두 위치 사이의 방향을 표시하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen
ms.openlocfilehash: c877abf1b00f978a9ad0bdce51234308a5fab651
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429715"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다.

그렇게 하려면 두 가지 방법이 있습니다. 첫 번째 방법은 서비스 모듈을 통해 [Azure Maps 경로 API](/rest/api/maps/route/getroutedirections)를 쿼리하는 것입니다. 두 번째 방법은 [Fetch API](https://fetch.spec.whatwg.org/)를 사용하여 [Azure Maps Route API](/rest/api/maps/route/getroutedirections)에 대한 검색 요청을 수행하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="query-the-route-via-service-module"></a>서비스 모듈을 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/RBZbep/'>맵에서 A에서 B로의 방향 표시(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 블록은 맵 개체를 생성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 액세스 토큰을 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `TokenCredential`을 만듭니다. 그런 다음, `TokenCredential`을 `atlas.service.MapsURL.newPipeline()`에 전달하고 [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록은 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 개체를 만든 후 맵에 추가합니다.

네 번째 코드 블록은 시작 및 끝 [points](/javascript/api/azure-maps-control/atlas.data.point) 개체를 만든 후 dataSource 개체에 추가합니다.

선은 LineString의 [기능](/javascript/api/azure-maps-control/atlas.data.feature) 중 하나입니다. [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)는 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)에서 선 계층의 속성을 참조하세요.

[기호 계층](/javascript/api/azure-maps-control/atlas.layer.symbollayer)은 텍스트 또는 아이콘을 사용하여 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 점 기반 데이터를 렌더링합니다. 텍스트 또는 아이콘은 맵에 기호로 렌더링됩니다. 다섯 번째 코드 블록은 기호 계층을 만들고 맵에 추가합니다.

여섯 번째 코드 블록은 [서비스 모듈](how-to-use-services-module.md)에 속하는 Azure Maps 라우팅 서비스를 쿼리합니다. RouteURL의 [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) 메서드는 시작 및 끝 지점 사이의 경로를 가져오는 데 사용됩니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가합니다. 그런 다음, 응답을 지도에 경로로 렌더링합니다. 지도에 선을 추가하는 방법에 대한 자세한 내용은 [지도에 선 추가](map-add-line-layer.md)를 참조하세요.

마지막 코드 블록은 맵의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 맵의 경계를 설정합니다.

경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 경계는 [이벤트 수신기](/javascript/api/azure-maps-control/atlas.map#events)내에 생성됩니다. 이 코드 구조는 맵이 완전히 로드된 후에만 결과가 표시되도록 합니다.

## <a name="query-the-route-via-fetch-api"></a>Fetch API를 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>A에서 B로의 방향 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 개체를 만든 후 지도에 추가합니다.

세 번째 코드 블록은 경로의 시작 및 끝 지점을 만듭니다. 그런 다음, 데이터 원본에 추가합니다. [addPins](/javascript/api/azure-maps-control/atlas.map) 사용 방법에 대한 지침은 [지도에 핀 추가](map-add-pin.md)를 참조하세요.

[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)는 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)에서 선 계층의 속성을 참조하세요.

[기호 계층](/javascript/api/azure-maps-control/atlas.layer.symbollayer)은 텍스트 또는 아이콘을 사용하여 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 다섯 번째 코드 블록은 기호 계층을 만들고 맵에 추가합니다. [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)에서 기호 계층의 속성을 참조하세요.

다음 코드 블록은 시작 및 대상 지점에서 `SouthWest` 및 `NorthEast` 지점을 만들고 Map의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 지도의 경계를 설정합니다.

마지막 코드 블록은 [Fetch API](https://fetch.spec.whatwg.org/)를 사용하여 [Azure Maps Route API](/rest/api/maps/route/getroutedirections)에 대한 검색 요청을 수행하는 것입니다. 그런 다음, 응답을 구문 분석합니다. 응답이 성공한 경우 위도 및 경도 정보를 사용하여 해당 점에 연결하여 배열을 만듭니다. 그런 다음, 데이터 원본에 선 데이터를 추가하여 맵에 경로를 렌더링합니다. 지침은 [맵에 선 추가](map-add-line-layer.md)를 참조하세요.

경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 경계는 [이벤트 수신기](/javascript/api/azure-maps-control/atlas.map#events)내에 생성됩니다. 역시 맵이 완전히 로드된 후 결과가 표시되도록 하려고 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [라우팅 서비스 사용 모범 사례](how-to-use-best-practices-for-search.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵에 트래픽 표시](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [맵 - 마우스 이벤트 상호 작용](./map-events.md)