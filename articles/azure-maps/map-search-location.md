---
title: 맵에 검색 결과 표시 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 검색 요청을 수행하고 맵에 결과를 표시하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen
ms.openlocfilehash: be6b1a133af6ee93aa67d4fb4571dbde1c659278
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439581"
---
# <a name="show-search-results-on-the-map"></a>지도에 검색 결과 표시

이 문서에서는 관심 있는 위치를 검색하고 지도에 검색 결과를 표시하는 방법을 보여줍니다.

원하는 위치를 검색하는 방법은 두 가지가 있습니다. 한 방법은 서비스 모듈을 사용하여 검색 요청을 하는 것입니다. 다른 방법은 [Fetch API](https://fetch.spec.whatwg.org/)를 통해 [Azure Maps Fuzzy search API](/rest/api/maps/search/getsearchfuzzy)에 검색 요청을 하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="make-a-search-request-via-service-module"></a>서비스 모듈을 통해 검색 요청

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>지도에 검색 결과 표시</a>(서비스 모듈)를 참조하세요.
</iframe>

위의 코드에서 첫 번째 블록은 맵 개체를 생성하고 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 액세스 토큰을 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `TokenCredential`을 만듭니다. 그런 다음, `TokenCredential`을 `atlas.service.MapsURL.newPipeline()`에 전달하고 [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) 인스턴스를 만듭니다. `searchURL`은 Azure Maps [검색](/rest/api/maps/search) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록에서는[DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 클래스를 사용하여 데이터 원본 개체를 만들고 검색 결과를 추가합니다. [기호 계층](/javascript/api/azure-maps-control/atlas.layer.symbollayer)은 텍스트 또는 아이콘을 사용하여 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다.  그러면 기호 계층이 만들어집니다. 데이터 원본이 기호 계층에 추가된 다음, 맵에 추가됩니다.

네 번째 코드 블록은 [서비스 모듈](how-to-use-services-module.md)에서 [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) 메서드를 사용합니다. 이를 사용하면 [Get Search Fuzzy rest API](/rest/api/maps/search/getsearchfuzzy)를 통해 자유 형식 텍스트 검색을 수행하여 관심 지점을 검색할 수 있습니다. Search Fuzzy API에 대한 Get 요청은 모든 조합의 유사 항목 입력을 처리할 수 있습니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가하면, 기호 레이어를 통해 데이터가 맵에 자동으로 렌더링됩니다.

마지막 코드 블록은 맵의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 맵에 대한 카메라 범위를 조정합니다.

검색 요청, 데이터 원본, 기호 계층 및 카메라 범위는 맵의 [이벤트 수신기](/javascript/api/azure-maps-control/atlas.map#events) 내에 있습니다. 맵이 완전히 로드된 후 결과가 표시되도록 하려고 합니다.


## <a name="make-a-search-request-via-fetch-api"></a>Fetch API를 통해 검색 요청 만들기

<iframe height='500' scrolling='no' title='지도에 검색 결과 표시' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>지도에 검색 결과 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 액세스 토큰을 사용하도록 인증 메커니즘을 설정합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 검색 요청을 수행할 URL을 만듭니다. 또한 검색 결과에 대한 범위 및 핀을 저장할 두 배열을 만듭니다.

세 번째 코드 블록은 [Fetch API](https://fetch.spec.whatwg.org/)를 사용합니다. [Fetch API](https://fetch.spec.whatwg.org/)를 사용하여 관심 지점을 검색하는 [Azure Maps Fuzzy search API](/rest/api/maps/search/getsearchfuzzy)에 대한 요청을 수행합니다. 유사 항목 검색 API는 모든 조합의 유사 항목 입력을 처리할 수 있습니다. 그런 다음, 검색 응답을 처리하고 구문 분석하여 searchPins 배열에 결과 핀을 추가합니다.

네 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 클래스를 사용하여 생성됩니다. 코드에서 원본 개체에 검색 결과를 추가합니다. [기호 계층](/javascript/api/azure-maps-control/atlas.layer.symbollayer)은 텍스트 또는 아이콘을 사용하여 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 그러면 기호 계층이 만들어집니다. 데이터 원본이 기호 계층에 추가된 다음, 맵에 추가됩니다.

마지막 코드 블록은 [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) 개체를 만듭니다. 결과 배열을 사용한 다음, 맵의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)를 사용하여 맵의 카메라 경계를 조정합니다. 그런 다음, 결과 핀을 렌더링합니다.

맵이 완전히 로드된 후 결과가 표시되도록 검색 요청, 데이터 원본, 기호 계층 및 카메라 범위가 맵의 [이벤트 수신기](/javascript/api/azure-maps-control/atlas.map#events) 내에 설정됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [검색 서비스 사용 모범 사례](how-to-use-best-practices-for-search.md)

**유상 항목 검색** 에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps 유사 항목 검색 API](/rest/api/maps/search/getsearchfuzzy)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](map-route.md)