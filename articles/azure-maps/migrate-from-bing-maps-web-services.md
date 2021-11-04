---
title: '자습서: 웹 서비스를 Bing Maps에서 Microsoft Azure Maps로 마이그레이션'
description: Bing Maps에서 Microsoft Azure Maps로 웹 서비스를 마이그레이션하는 방법에 대한 자습서.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 244305dcc454287f2c68ec9b9c71145fb9a28641
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473987"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>자습서: Bing Maps에서 웹 서비스 마이그레이션

Azure 및 Bing Maps 둘 다 REST 웹 서비스를 통해 공간 API에 액세스할 수 있습니다. 이러한 플랫폼의 API 인터페이스는 비슷한 기능을 수행하지만 서로 다른 명명 규칙과 응답 개체를 사용합니다. 이 자습서에서는 다음 작업 방법을 배웁니다.

> * 정방향 및 역방향 지오코딩
> * 관심 지점 검색
> * 경로 및 방향 계산
> * 맵 이미지 검색
> * 거리 행렬 계산
> * 표준 시간대 세부 정보 가져오기

다음 표에는 나열된 Bing Maps 서비스 API와 유사한 기능을 제공하는 Azure Maps 서비스 API가 정리되어 있습니다.

| Bing Maps 서비스 API                 | Azure Maps 서비스 API      |
|---------------------------------------|-----------------------------|
| 자동 제안                           | [검색](/rest/api/maps/search)     |
| 방향(트럭 포함)          | [경로 방향](/rest/api/maps/route/getroutedirections)                          |
| 거리 행렬                       | [경로 행렬](/rest/api/maps/route/postroutematrixpreview)                          |
| 이미지 - 정적 맵                  | [렌더링](/rest/api/maps/render/getmapimage)                                   |
| 등시성                            | [경로 범위](/rest/api/maps/route/getrouterange)                                    |
| 로컬 인사이트                        | [검색](/rest/api/maps/search) + [경로 범위](/rest/api/maps/route/getrouterange)    |
| 로컬 검색                          | [검색](/rest/api/maps/search)     |
| 위치 인식(POI)           | [검색](/rest/api/maps/search)     |
| 위치(정방향/역방향 지오코딩) | [검색](/rest/api/maps/search)                                               |
| 도로에 맞춤                          | [경로 방향 게시](/rest/api/maps/route/postroutedirections)                         |
| SDS(Spatial Data Services)           | [검색](/rest/api/maps/search) + [경로](/rest/api/maps/route) + 기타 Azure 서비스 |
| 표준 시간대                             | [표준 시간대](/rest/api/maps/timezone)  |
| 교통 사고                     | [트래픽 인시던트 세부 정보](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| 상승                             | [권한 상승](/rest/api/maps/elevation)

다음 서비스 API는 현재 Azure Maps에서 사용할 수 없습니다.

* 최적화된 일정표 경로 - 계획됨. Azure Maps 경로 API는 단일 차량에 대한 외판원 최적화를 지원합니다.
* 이미지 메타데이터 – 주로 Bing Maps에서 타일 URL을 가져오는 데 사용됩니다. Azure Maps에는 지도 타일에 직접 액세스하기 위한 독립 실행형 서비스가 있습니다.

Azure Maps에는 다음과 같은 몇 가지 흥미로운 추가 REST 웹 서비스가 있습니다.

* [Azure Maps Creator](./creator-indoor-maps.md) – 건물과 공간의 사용자 지정 프라이빗 디지털 쌍을 만듭니다.
* [공간 작업](/rest/api/maps/spatial) - 복잡한 공간 계산과 작업(예: 지오펜싱)을 서비스로 오프로드합니다.
* [지도 타일](/rest/api/maps/render/getmaptile) – 래스터 및 벡터 타일로 Azure Maps의 도로 및 이미지 타일에 액세스합니다.
* [일괄 처리 라우팅](/rest/api/maps/route/postroutedirectionsbatchpreview) – 일정 기간 동안 단일 일괄 처리에서 최대 1,000개의 경로 요청을 만들 수 있습니다. 처리 속도를 높이기 위해 서버에서 경로가 병렬로 계산됩니다.
* [트래픽](/rest/api/maps/traffic) 흐름 – 래스터 및 벡터 타일로 실시간 트래픽 흐름 데이터에 액세스합니다.
* [지리적 위치 API(미리 보기)](/rest/api/maps/geolocation/get-ip-to-location) - IP 주소의 위치를 가져옵니다.
* [날씨 서비스](/rest/api/maps/weather) – 실시간 및 예측 날씨 데이터에 대한 액세스 권한을 얻습니다.

다음 모범 사례 가이드도 꼭 검토하세요.

* [검색 관련 모범 사례](./how-to-use-best-practices-for-search.md)
* [라우팅 모범 사례](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="geocoding-addresses"></a>주소 지오코딩

지오코딩은 주소(예: `"1 Microsoft way, Redmond, WA"`)를 좌표(예: 경도: -122.1298, 위도: 47.64005)로 변환하는 프로세스입니다. 그 후 주로 좌표를 사용하여 맵에서 압정의 위치를 지정하거나 지도를 가운데에 맞춥니다.

Azure Maps는 다음과 같은 주소를 지오코딩하는 여러 가지 방법을 제공합니다.

* [자유 형식 주소 지오코딩](/rest/api/maps/search/getsearchaddress): 단일 주소 문자열(예: `"1 Microsoft way, Redmond, WA"`)을 지정하고 요청을 즉시 처리합니다. 이 서비스는 개별 주소를 신속하게 지오코딩해야 하는 경우에 추천합니다.
* [정형 주소 지오코딩](/rest/api/maps/search/getsearchaddressstructured): 도로명, 구/군/시, 국가, 우편 번호 등의 단일 주소 부분을 지정하고 요청을 즉시 처리합니다. 이 서비스는 개별 주소를 신속하게 지오코딩하고 데이터를 개별 주소 부분으로 구문 분석해야 하는 경우에 추천합니다.
* [일괄 처리 주소 지오코딩](/rest/api/maps/search/postsearchaddressbatchpreview): 최대 10,000개의 주소를 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 주소가 서버에서 병렬로 지오코딩되며, 코딩이 완료되면 전체 결과 세트를 다운로드할 수 있습니다. 이 서비스는 대형 데이터 세트를 지오코딩할 때 추천합니다.
* [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있는 자유 형식 문자열을 사용하여 요청을 즉시 처리합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 애플리케이션에 추천하는 방법입니다.
* [유사 항목 일괄 검색](/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 10,000개의 주소, 장소, 랜드마크 또는 관심 지점을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Bing Maps API 매개 변수를 정형 자유 형식 주소 지오코딩에 사용되는 Azure Maps의 비슷한 API 매개 변수와 상호 참조합니다.

**주소 기준 위치(정형 주소)**

| Bing Maps API 매개 변수              | 비슷한 Azure Maps API 매개 변수                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` 또는 `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` 및 `countryCode`                     |
| `locality`                         | `municipality` 또는 `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | 해당 없음 – 항상 Azure Maps에서 반환됩니다(사용 가능한 경우).   |
| `include` (`incl`)               | 해당 없음 – 국가 ISO2 코드는 항상 Azure Maps에서 반환됩니다. |
| `key`                              | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)                  | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요. |
| `userRegion` (`ur`)              | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

Azure Maps는 다음 기능도 지원합니다.

* `countrySecondarySubdivision` – 구/군, 구역
* `countryTertiarySubdivision` - 명명된 영역, 자치 구역, 주, 최소 행정 구역
* `ofs` - `maxResults` 매개 변수와 함께 결과를 살펴봅니다.

**쿼리별 위치(자유 형식 주소 문자열)**

| Bing Maps API 매개 변수              | 비슷한 Azure Maps API 매개 변수      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | 해당 없음 – 항상 Azure Maps에서 반환됩니다(사용 가능한 경우).  |
| `include` (`incl`)               | 해당 없음 – 국가 ISO2 코드는 항상 Azure Maps에서 반환됩니다.  |
| `key`                              | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)                  | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.  |
| `userRegion` (`ur`)              | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

Azure Maps는 다음 기능도 지원합니다.

* `typeahead` - 쿼리가 부분 입력으로 해석되고 검색이 예측 모드(자동 제안/자동 완성)로 진입하는 조건을 지정합니다.
* `countrySet` – 검색을 제한할 ISO2 국가 코드를 쉼표로 구분한 목록입니다.
* `lat`/`lon`, `topLeft`/`btmRight`, `radius` – 로컬과 관련성이 깊은 결과를 얻으려면 사용자 위치와 영역을 지정합니다.
* `ofs` - `maxResults` 매개 변수와 함께 결과를 살펴봅니다.

검색 서비스를 사용하는 방법의 예제는 [여기](./how-to-search-for-address.md)에 설명되어 있습니다. [검색 모범 사례](./how-to-use-best-practices-for-search.md) 설명서를 검토하세요.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>좌표 역방향 지오코딩(지점으로 위치 찾기)

역방향 지오코딩은 지리적 좌표(예: 경도: -122.1298, 위도: 47.64005)를 대략적인 주소(예: `"1 Microsoft way, Redmond, WA"`)로 변환하는 프로세스입니다.

Azure Maps는 다음과 같은 여러 가지 역방향 지오코딩 방법을 제공합니다.

* [주소 역방향 지오코딩](/rest/api/maps/search/getsearchaddressreverse): 단일 지리적 좌표를 지정하여 대략적인 주소를 가져오고 요청을 즉시 처리합니다.
* [교차로 역방향 지오코딩](/rest/api/maps/search/getsearchaddressreversecrossstreet): 지리적 좌표 하나를 지정하여 인근의 교차로 정보(예: 1번가 및 메인 스트리트)를 가져오고 요청을 즉시 처리합니다.
* [일괄 주소 역방향 지오코딩](/rest/api/maps/search/postsearchaddressreversebatchpreview): 최대 10,000개의 좌표를 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수              | 비슷한 Azure Maps API 매개 변수       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – 아래의 엔터티 형식 비교 표를 참조하세요.    |
| `includeNeighborhood` (`inclnb`)     | 해당 없음 – 항상 Azure Maps에서 반환됩니다(사용 가능한 경우).         |
| `include` (`incl`)                   | 해당 없음 – 국가 ISO2 코드는 항상 Azure Maps에서 반환됩니다.    |
| `key`                                | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)                      | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.   |
| `userRegion` (`ur`)                  | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

[검색 모범 사례](./how-to-use-best-practices-for-search.md) 설명서를 검토하세요.

Azure Maps 역방향 지오코딩 API에는 Bing Maps에 없는 다음과 같은 추가 기능이 있으며, 이러한 기능은 앱을 마이그레이션할 때 통합에 유용하게 사용할 수 있습니다.

* 속도 제한 데이터 검색
* 지방 도로, 간선 도로, 진입 금지, 나들목 등의 도로 사용 정보 검색
* 좌표가 속한 거리의 측면

**엔터티 형식 비교 표**

다음 표에서는 Bing Maps 엔터티 형식 값을 그에 상응하는 Azure Maps의 속성 이름과 상호 참조합니다.

| Bing Maps 엔터티 형식 | 비슷한 Azure Maps 엔터티 형식               | 설명                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *주소*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *이웃 도시*                             |
| `PopulatedPlace`      | `Municipality` 또는 `MunicipalitySubdivision`     | *도시*, *마을/하위 도시* 또는 *대도시권*     |
| `Postcode1`           | `PostalCodeArea`                                | *우편 번호* 또는 *우편 번호*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *시/도* 또는 *시/군/구*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *구/군* 또는 *구역*                    |
| `CountryRegion`       | `Country`                                       | *국가 이름*                             |
|                       | `CountryTertiarySubdivision`                    | *자치 구역*, *주*, *최소 행정 구역*          |

## <a name="get-location-suggestions-autosuggest"></a>위치 제안 가져오기(Autosuggest)

여러 Azure Maps 검색 API는 자동 제안 시나리오에 사용할 수 있는 예측 모드를 지원합니다. Azure Maps [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy) API는 Bing Maps Autosuggest API와 가장 비슷합니다. 다음 API도 예측 모드를 지원하며, 쿼리에 `&typeahead=true`를 추가합니다.

* [자유 형식 주소 지오코딩](/rest/api/maps/search/getsearchaddress): 단일 주소 문자열(예: `"1 Microsoft way, Redmond, WA"`)을 지정하고 요청을 즉시 처리합니다. 이 서비스는 개별 주소를 신속하게 지오코딩해야 하는 경우에 추천합니다.
* [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있는 자유 형식 문자열을 사용하여 요청을 즉시 처리합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 애플리케이션에 추천하는 방법입니다.
* [POI 검색](/rest/api/maps/search/getsearchpoi): 이름으로 관심 지점을 검색합니다. 예: `"starbucks"`.
* [POI 범주 검색](/rest/api/maps/search/getsearchpoicategory): 범주로 관심 지점을 검색합니다. 예: "식당".

## <a name="calculate-routes-and-directions"></a>경로 및 방향 계산

Azure Maps를 사용하여 경로 및 방향을 계산할 수 있습니다. Azure Maps에는 다음과 같이 Bing Maps 라우팅 서비스와 동일한 여러 기능이 있습니다.

* 도착 및 출발 시간
* 실시간 및 예측 기반 트래픽 경로
* 다양한 교통 모드(자동차, 도보, 트럭)
* 중간 지점 주문 최적화(외판원)

> [!NOTE]
> Azure Maps는 모든 중간 지점이 좌표여야 합니다. 가장 먼저 주소를 지오코딩해야 합니다.

Azure Maps 라우팅 서비스는 경로 계산을 위한 다음 API를 제공합니다.

* [경로 계산](/rest/api/maps/route/getroutedirections): 경로를 계산하고 요청을 즉시 처리합니다. 이 API는 GET 및 POST 요청을 모두 지원합니다. POST 요청은 많은 수의 중간 지점을 지정할 때 또는 URL 요청이 너무 길어서 문제가 발생하지 않도록 많은 경로 옵션을 사용할 때 추천하는 방법입니다.
* [일괄 처리 경로](/rest/api/maps/route/postroutedirectionsbatchpreview): 최대 1,000개의 경로 요청을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수                                    | 비슷한 Azure Maps API 매개 변수               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` 또는 `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | 해당 없음                                               |
| `distanceUnit` (`du`)                                      | 해당 없음 – Azure Maps는 메트릭 시스템만 사용합니다.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType`, `minDeviationDistance` 및 `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` 및 `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` 또는 `arriveAt`                          |
| `tolerances` (`tl`)                                        | 해당 없음                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n`(`wp.n`) 또는 `viaWaypoint.n`(`vwp.n`)         | `query` – `lat0,lon0:lat1,lon1….` 형식의 좌표   |
| `key`                                                      | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)                                            | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요. |
| `userRegion` (`ur`)                                        | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

Azure Maps 라우팅 API는 동일한 API 내에서 트럭 라우팅도 지원합니다. 다음 표에서는 추가적인 Bing Maps 트럭 라우팅 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수                  | 비슷한 Azure Maps API 매개 변수        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | 해당 없음 – 미터 단위의 크기만 지원됩니다. |
| `weightUnit` (`wu`)                      | 해당 없음 – 킬로그램 단위의 무게만 지원됩니다. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **해당 사항 없음**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **해당 사항 없음**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **해당 사항 없음**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **해당 사항 없음**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **해당 사항 없음**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> 기본적으로 Azure Maps 경로 API는 요약 정보(거리 및 시간)와 경로의 좌표만 반환합니다. `instructionsType` 매개 변수를 사용하여 턴바이턴 지침을 검색합니다. `routeRepresentation` 매개 변수를 사용하여 요약 정보 및 경로를 필터링할 수 있습니다.

[라우팅 모범 사례](./how-to-use-best-practices-for-routing.md) 설명서도 꼭 검토하세요.

Azure Maps 경로 API에는 Bing Maps에 없는 다음과 같은 여러 추가 기능이 있으며, 이러한 기능은 앱을 마이그레이션할 때 통합에 유용하게 사용할 수 있습니다.

* 경로 유형 지원: 최단거리, 최단시간, 선회 및 최고연비.
* 추가 이동 모드 지원: 자전거, 버스, 오토바이, 택시, 트럭, 승합차.
* 150개 중간 지점을 지원합니다.
* 단일 요청에서 여러 이동 시간(기록 트래픽, 실시간 트래픽, 트래픽 없음)을 계산합니다.
* 회피할 도로 유형(카풀 도로, 비포장 도로, 이미 사용한 도로)을 추가합니다.
* 엔진 사양에 따라 경로를 지정합니다. 연료/배터리 잔량 및 엔진 사양에 따라 내연 기관 차량 또는 전기차의 경로를 계산합니다.
* 최대 차량 속도를 지정합니다.

## <a name="snap-coordinates-to-road"></a>좌표를 도로에 맞추기

Azure Maps에서 여러 가지 방법으로 좌표를 도로에 맞출 수 있습니다.

* 경로 방향 API를 사용하여 도로 네트워크를 따라 논리적 경로에 좌표를 맞춥니다.
* Azure Maps 웹 SDK를 사용하여 개별 좌표를 벡터 타일의 가장 가까운 도로에 맞춥니다.
* Azure Maps 벡터 타일을 사용하여 개별 좌표를 직접 맞춥니다.

**경로 방향 API를 사용하여 좌표 맞추기**

Azure Maps는 [경로 방향](/rest/api/maps/route/postroutedirections) API를 사용하여 좌표를 도로에 맞출 수 있습니다. 이 서비스는 좌표 세트 간의 논리적 경로를 재구성하는 데 사용할 수 있으며 Bing Maps Snap to Road API와 비슷합니다.

경로 방향 API를 사용하여 좌표를 도로에 맞추는 두 가지 방법이 있습니다.

* 좌표가 150개 이하인 경우 GET route directions API에서 좌표를 중간 지점으로 통과할 수 있습니다. 이 방법을 사용하면 두 가지 유형의 맞춰진 데이터를 검색할 수 있습니다. 라우팅 지침에는 맞춰진 개별 중간 지점이 포함되고, 라우팅 경로에는 좌표 사이의 전체 경로를 채우게 될 보간된 좌표 세트가 포함됩니다.
* 좌표가 150개를 초과하는 경우 POST route directions API를 사용할 수 있습니다. 좌표 시작 및 끝 좌표는 쿼리 매개 변수로 전달되어야 하지만, 모든 좌표를 POST 요청 본문의 `supportingPoints` 매개 변수에 전달하고 지점의 GeoJSON 기하 도형 컬렉션 형식으로 지정할 수 있습니다. 이 방법을 선택할 때 사용 가능한 유일한 맞춰진 데이터는 좌표 사이의 전체 경로를 채우게 될 보간된 좌표 세트인 라우팅 경로입니다. 다음은 Azure Maps 웹 SDK의 서비스 모듈을 사용하는 이 방법의 [예제](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path)입니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수    | 비슷한 Azure Maps API 매개 변수                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – 이 지점을 POST 요청의 본문에 전달합니다.  |
| `interpolate`              | 해당 없음                                                                 |
| `includeSpeedLimit`        | N/A                                                                 |
| `includeTruckSpeedLimit`   | N/A                                                                 |
| `speedUnit`                | 해당 없음                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)            | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.   |
| `userRegion` (`ur`)        | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요.   |

또한 Azure Maps 라우팅 API는 논리적 경로를 계산할 수 있도록 동일한 API 내에서 트럭 라우팅 매개 변수를 지원합니다. 다음 표에서는 추가적인 Bing Maps 트럭 라우팅 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수                 | 비슷한 Azure Maps API 매개 변수        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | 해당 없음 – 미터 단위의 크기만 지원됩니다. |
| `weightUnit` (`wu`)                     | 해당 없음 – 킬로그램 단위의 무게만 지원됩니다. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **해당 사항 없음**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **해당 사항 없음**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **해당 사항 없음**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **해당 사항 없음**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **해당 사항 없음**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

이 접근 방식에서는 라우팅 방향 API를 사용하므로 해당 서비스의 전체 옵션 세트를 사용하여 좌표를 도로에 맞추는 데 사용되는 논리를 사용자 지정할 수 있습니다. 예를 들어 출발 시간을 지정하면 과거의 트래픽 데이터를 고려합니다.

Azure Maps 라우팅 방향 API는 현재 속도 제한 데이터를 반환하지 않지만, Azure Maps 역방향 지오코딩 API를 사용하여 검색할 수 있습니다.

**웹 SDK를 사용하여 좌표 맞추기**

Azure Maps 웹 SDK는 벡터 타일을 사용하여 맵을 렌더링합니다. 이러한 벡터 타일은 원시 도로 기하 도형 정보를 포함하며, 좌표와 가장 가까운 도로를 계산하는 데 사용하여 개별 좌표를 간단하게 맞출 수 있습니다. 좌표를 도로 위에 시각적으로 표시하기를 원하고 이미 Azure Maps 웹 SDK를 사용하여 데이터를 시각화하고 있는 경우에 유용한 방법입니다.

그러나 이 방법은 맵 보기 내에 로드된 도로 세그먼트에만 맞춰집니다. 국가 수준에서 맵을 축소할 경우 도로 데이터가 없어서 맞추기가 불가능할 수도 있지만, 이와 같은 확대/축소 수준에서는 단일 픽셀이 여러 도시 블록 영역을 나타낼 수 있으므로 맞추기가 필요 없습니다. 맵의 이동이 완료될 때마다 맞추기 논리를 적용하면 이 문제를 해결할 수 있습니다. 이 방법을 보여주는 [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)을 살펴보세요.

**Azure Maps 벡터 타일을 사용하여 직접 좌표 맞추기**

Azure Maps 벡터 타일은 좌표와 가장 가까운 도로의 지점을 계산하여 개별 좌표의 기본 맞추기를 수행할 수 있는 원시 도로 기하 도형 정보를 포함하고 있습니다. 모든 도로 세그먼트는 확대/축소 수준 15의 섹터에 표시되므로 여기서 타일을 검색합니다. 그런 다음, [쿼드트리 타일 피라미드 수학](./zoom-levels-and-tile-grid.md)을 사용하여 타일이 필요한지 확인하고 타일을 기하 도형으로 변환할 수 있습니다. 여기서 [turf js](http://turfjs.org/) 또는 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) 같은 공간 수학 라이브러리를 사용하여 가장 가까운 선 세그먼트를 계산할 수 있습니다.

## <a name="retrieve-a-map-image-static-map"></a>맵 이미지 검색(정적 맵)

Azure Maps는 데이터가 오버레이된 정적 맵 이미지를 렌더링하기 위한 API를 제공합니다. Azure Maps [맵 이미지 렌더링](/rest/api/maps/render/getmapimagerytile) API는 Bing Maps의 정적 맵 API와 비슷합니다.

> [!NOTE]
> Azure Maps는 가운데, 모든 압정 및 경로 위치가 `longitude,latitude` 형식의 좌표여야 하는 반면, Bing Maps는 `latitude,longitude` 형식을 사용합니다.</p>
<p>가장 먼저 주소를 지오코딩해야 합니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수  | 비슷한 Azure Maps API 매개 변수            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` - URL 경로의 일부로 지정됩니다. 현재는 PNG만 지원됩니다.  |
| `heading`                | N/A – Streetside가 지원되지 않습니다.                |
| `imagerySet`             | `layer` 및 `style` – [지원되는 맵 스타일](./supported-map-styles.md) 설명서를 참조하세요.   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | 해당 없음                                            |
| `mapSize` (`ms`)         | `width` 및 `height` – 최대 크기는 8192x8192입니다. |
| `declutterPins` (`dcl`)  | 해당 없음                                            |
| `dpi`                    | N/A                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | 해당 없음                                            |
| `pitch`                  | N/A – Streetside가 지원되지 않습니다.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | 해당 없음 – 가운데 또는 경계 상자를 사용해야 합니다.     |
| `highlightEntity` (`he`) | 해당 없음                                            |
| `style`                  | 해당 없음                                            |
| 경로 매개 변수         | 해당 없음                                            |
| `key`                    | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)          | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.   |
| `userRegion` (`ur`)      | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

> [!NOTE]
> Azure Maps는 타일 크기가 Bing Maps에 사용되는 지도 타일의 두 배인 타일 시스템을 사용합니다. 따라서 Azure Maps의 확대/축소 수준 값은 Bing Maps와 비교할 때 Azure Maps에서 한 수준 더 크게 표시됩니다. 이를 보정하려면 마이그레이션하는 요청에서 확대/축소 수준을 1단계 낮춥니다.

자세한 내용은 [맵 이미지 렌더링 API에 대한 방법 가이드](./how-to-render-custom-data.md)를 참조하세요.

정적 맵 이미지를 생성할 수 있다는 점 외에도, Azure Maps 렌더링 서비스는 래스터(PNG) 및 벡터 형식의 지도 타일에 직접 액세스할 수 있는 다음 기능을 제공합니다.

* [지도 타일](/rest/api/maps/render/getmaptile) - 기본 맵(도로, 경계, 배경)의 래스터(PNG) 및 벡터 타일을 검색합니다.
* [맵 이미지 타일](/rest/api/maps/render/getmapimagerytile) - 항공 및 위성 이미지 타일을 검색합니다.

### <a name="pushpin-url-parameter-format-comparison"></a>압정 URL 매개 변수 형식 비교

**이전: Bing Maps**

Bing Maps에서는 URL에 `pushpin` 매개 변수를 사용하여 정적 맵 이미지에 압정을 추가할 수 있습니다. `pushpin` 매개 변수는 아래와 같이 `latitude,longitude` 형식의 위치, 아이콘 스타일 및 텍스트 레이블(최대 3자)을 사용합니다.

> `&pushpin=latitude,longitude;iconStyle;label`

다른 값을 URL에 `pushpin` 매개 변수를 추가하는 방법으로 압정을 추가할 수 있습니다. 압정 아이콘 스타일은 Bing Maps API에서 제공하는 미리 정의된 스타일 중 하나로 제한됩니다.

예를 들어 Bing Maps에서 다음 URL 매개 변수를 사용하여 "AB"라는 레이블이 있는 빨간색 압정을 맵의 좌표(경도: -110, 위도: 45)에 추가할 수 있습니다.

> `&pushpin=45,-110;7;AB`

![Bing Maps 정적 맵 핀](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**이후: Azure Maps**

Azure Maps에서는 URL에 `pins` 매개 변수를 지정하여 정적 맵 이미지에 압정을 추가할 수 있습니다. Azure Maps의 압정은 해당 아이콘 스타일을 사용하는 아이콘 스타일과 위치 목록을 지정하여 정의됩니다. 그 후 이 정보는 `pins` 매개 변수에 전달되며, 이 매개 변수는 다양한 스타일의 표식을 지원하도록 여러 번 지정할 수 있습니다.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

다른 스타일 및 위치 세트를 사용하여 URL에 `pins` 매개 변수를 추가하는 방법으로 추가 스타일을 사용할 수 있습니다.

핀 위치와 관련하여 Azure Maps에서는 좌표가 `longitude latitude` 형식이어야 하는 반면, Bing Maps에서는 `latitude,longitude` 형식을 사용합니다. 또한 Azure Maps에서는 **쉼표로 분리된 경도 및 위도가 아니라 공백** 이 있습니다.

`iconType` 값은 만들려는 핀의 유형을 지정하며 가능한 값은 다음과 같습니다.

* `default` – 기본 핀 아이콘입니다.
* `none` – 아이콘이 표시되지 않고 레이블만 렌더링됩니다.
* `custom` – 사용자 지정 아이콘을 사용하도록 지정합니다. 아이콘 이미지를 가리키는 URL은 핀 위치 정보 뒤의 `pins` 매개 변수 끝에 추가할 수 있습니다.
* `{udid}` – Azure Maps 데이터 스토리지 플랫폼에 저장되는 아이콘의 UDID(고유 데이터 ID)입니다.

Azure Maps의 핀 스타일은 `optionNameValue` 형식으로 추가되며, `iconType|optionName1Value1|optionName2Value2`처럼 여러 스타일이 파이프(`|`) 문자로 구분됩니다. 옵션 이름과 값은 구분되지 않습니다. 다음 스타일 옵션 이름을 사용하여 Azure Maps에서 압정 스타일을 지정할 수 있습니다.

* `al` – 압정의 불투명도(알파)를 지정합니다. 0~1 사이의 숫자입니다.
* `an` – 핀 앵커를 지정합니다. `x y` 형식으로 지정된 x 및 y 픽셀 값입니다.
* `co` - 핀 색입니다. 24비트 16진수 색(`000000`~`FFFFFF`)이어야 합니다.
* `la` - 레이블 앵커를 지정합니다. `x y` 형식으로 지정된 x 및 y 픽셀 값입니다.
* `lc` - 레이블 색입니다. 24비트 16진수 색(`000000`~`FFFFFF`)이어야 합니다.
* `ls` - 레이블의 크기(픽셀)입니다. 0보다 큰 숫자여야 합니다.
* `ro` - 아이콘을 회전하는 각도입니다. -360~360 사이의 숫자입니다.
* `sc` – 핀 아이콘의 배율 값입니다. 0보다 큰 숫자여야 합니다.

위치 목록의 모든 압정에 적용되는 단일 레이블 값을 갖는 것이 아니라 각 핀의 레이블 값이 지정됩니다. 레이블 값은 여러 문자로 된 문자열일 수 있으며, 스타일 또는 위치 값으로 잘못 읽지 않도록 작은따옴표로 래핑됩니다.

예를 들어 Azure Maps에서는 다음 URL 매개 변수를 사용하여 "Space Needle"이라는 레이블이 지정된 빨간색(`FF0000`) 기본 아이콘을 좌표(경도: -122.349300, 위도: 47.620180)의 아이콘 아래에(15 50) 추가할 수 있습니다.

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Azure Maps 정적 맵 핀](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

다음 예제에서는 레이블 값이 '1', '2', '3'인 핀 3개를 추가합니다.

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Azure Maps 정적 맵 여러 핀](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>곡선 그리기 URL 매개 변수 형식 비교

**이전: Bing Maps**

Bing Maps에서는 URL에 `drawCurve` 매개 변수를 사용하여 정적 맵 이미지에 선 및 다각형을 추가할 수 있습니다. `drawCurve` 매개 변수는 아래와 같이 맵에 렌더링할 셰이프 유형, 스타일 유형 및 위치 목록을 가져옵니다.

> `&drawCurve=shapeType,styleType,location1,location2...`

다른 스타일 및 위치 세트를 사용하여 URL에 `drawCurve` 매개 변수를 추가하는 방법으로 추가 스타일을 사용할 수 있습니다.

Bing Maps의 위치는 `latitude1,longitude1_latitude2,longitude2_…` 형식으로 지정됩니다. 위치도 인코딩될 수 있습니다.

Bing Maps의 셰이프 유형에는 선, 다각형, 원 및 곡선이 있습니다. 스타일 유형에는 선 색, 선 두께, 윤곽선 색, 채우기 색, 윤곽선 두께 및 원 반지름이 있습니다.

예를 들어 Bing Maps에서 불투명도가 50%이고 두께가 4픽셀인 파란색 선은 다음 URL 매개 변수를 사용하여 좌표(경도: -110, 위도: 45 및 경도: -100, 위도: 50) 사이의 맵에 추가할 수 있습니다.

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Bing Maps 정적 맵 선](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**이후: Azure Maps**

Azure Maps에서는 URL에 *path* 매개 변수를 지정하여 정적 맵 이미지에 선과 다각형을 추가할 수 있습니다. Bing Maps와 마찬가지로, 이 매개 변수에서는 스타일 및 위치 목록을 지정할 수 있으며, *path* 매개 변수를 여러 번 지정하여 여러 가지 스타일로 여러 원, 선 및 다각형을 렌더링할 수 있습니다.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

경로 위치와 관련하여 Azure Maps에서는 좌표가 `longitude latitude` 형식이어야 하는 반면, Bing Maps에서는 `latitude,longitude` 형식을 사용합니다. 또한 Azure Maps에서는 **쉼표로 분리된 경도 및 위도가 아니라 공백** 이 있습니다. Azure Maps는 현재 인코딩된 경로를 지원하지 않습니다. [여기](./how-to-render-custom-data.md#upload-pins-and-path-data)에 설명된 것처럼 큰 데이터 세트를 Azure Maps 데이터 스토리지 API에 GeoJSON 채우기로 업로드할 수 있습니다.

Azure Maps의 경로 스타일은 `optionNameValue` 형식으로 추가되며, `optionName1Value1|optionName2Value2`처럼 여러 스타일이 파이프(`|`) 문자로 구분됩니다. 옵션 이름과 값은 구분되지 않습니다. 다음 스타일 옵션 이름을 사용하여 Azure Maps에서 스타일 경로를 지정할 수 있습니다.

* `fa` - 다각형을 렌더링할 때 사용되는 채우기 색 불투명도(알파)입니다. 0~1 사이의 숫자입니다.
* `fc` - 다각형 영역을 렌더링하는 데 사용되는 채우기 색입니다.
* `la` – 선 및 다각형의 윤곽선을 렌더링할 때 사용되는 선 색 불투명도(알파)입니다. 0~1 사이의 숫자입니다.
* `lc` – 선 및 다각형의 윤곽선을 렌더링하는 데 사용되는 선 색입니다.
* `lw` - 선의 너비(픽셀)입니다.
* `ra` – 원 반지름(미터)을 지정합니다.

예를 들어 Azure Maps에서 불투명도가 50%이고 두께가 4픽셀인 파란색 선은 다음 URL 매개 변수를 사용하여 좌표(경도: -110, 위도: 45 및 경도: -100, 위도: 50) 사이의 맵에 추가할 수 있습니다.

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Azure Maps 정적 맵 선](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>거리 행렬 계산

Azure Maps는 위치 세트 간의 이동 시간 및 거리를 거리 행렬로 계산할 수 있는 API를 제공합니다. Azure Maps 거리 행렬 API는 Bing Maps의 거리 행렬 API와 유사합니다.

* [경로 행렬](/rest/api/maps/route/postroutematrixpreview): 출발지 및 목적지 세트 간의 이동 시간 및 거리를 비동기적으로 계산합니다. 요청당 최대 700개 셀을 지원합니다(출발지 수 * 목적지 수). 이 제약 조건을 고려할 때 구현 가능한 행렬 크기는 `700x1`, `50x10`, `10x10`, `28x25`, `10x70`입니다.

> [!NOTE]
> 거리 행렬 API에 대한 요청은 요청 본문에 출발지 및 목적지 정보가 포함된 POST 요청을 통해서만 만들 수 있습니다.</p>
<p>또한 Azure Maps는 모든 출발지 및 목적지가 좌표여야 합니다. 가장 먼저 주소를 지오코딩해야 합니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – POST 요청 본문에서 GeoJSON으로 지정합니다.    |
| `destinations`          | `destination` – POST 요청 본문에서 GeoJSON으로 지정합니다. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | 해당 없음                                                         |
| `distanceUnit`          | 해당 없음 - 모든 거리는 미터 단위입니다.                              |
| `timeUnit`              | 해당 없음 – 모든 시간은 초 단위입니다.                                 |
| `key`                   | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)         | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.  |
| `userRegion` (`ur`)     | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요.     |

> [!TIP]
> Azure Maps 라우팅 API에서 사용 가능한 모든 고급 라우팅 옵션(트럭 라우팅, 엔진 사양, 회피 등)은 Azure Maps 거리 행렬 API에서 지원됩니다.

## <a name="calculate-an-isochrone"></a>등시선 계산

Azure Maps는 지정된 시간 또는 연료/요금 내에 원점에서 모든 방향으로 이동할 수 있는 영역을 포괄하는 다각형인 등시선을 계산할 수 있는 API를 제공합니다. Azure Maps 경로 범위 API는 Bing Maps의 등시선 API와 비슷합니다.

* [경로](/rest/api/maps/route/getrouterange) 범위**: 지정된 시간, 거리 또는 사용 가능한 연료/요금 내에 원점에서 모든 방향으로 이동할 수 있는 영역을 포괄하는 다각형을 계산합니다.

> [!NOTE]
> Azure Maps에서는 쿼리 원점이 좌표여야 합니다. 가장 먼저 주소를 지오코딩해야 합니다.</p>
<p>또한 Bing Maps는 시간 또는 거리를 기준으로 등시선을 계산할 수 있는 반면, Azure Maps는 시간, 거리 또는 사용 가능한 연료/요금에 따라 등시선을 계산할 수 있습니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수      | 비슷한 Azure Maps API 매개 변수            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | 해당 없음 – 모든 시간은 초 단위입니다.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | 해당 없음 - 모든 거리는 미터 단위입니다.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)              | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.  |
| `userRegion` (`ur`)          | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

> [!TIP]
> Azure Maps 라우팅 API에서 사용 가능한 모든 고급 라우팅 옵션(트럭 라우팅, 엔진 사양, 회피 등)은 Azure Maps 등시선 API에서 지원됩니다.

## <a name="search-for-points-of-interest"></a>관심 지점 검색

Bing Maps에서는 다음 API를 사용하여 관심 지점 데이터를 검색할 수 있습니다.

* **로컬 검색:** 이름 또는 엔터티 형식(범주)을 기준으로 근처(방사형 검색)의 관심 지점을 검색합니다. 이 API와 가장 비슷한 것은 Azure Maps [POI 검색](/rest/api/maps/search/getsearchpoi) 및 [POI 범주 검색](/rest/api/maps/search/getsearchpoicategory) API입니다.
* **위치 인식**: 한 위치에서 특정 거리 내에 있는 관심 지점을 검색합니다. 이 API와 가장 비슷한 것은 Azure Maps [근처 검색](/rest/api/maps/search/getsearchnearby) API입니다.
* **로컬 인사이트:** 특정 좌표로부터 지정된 최대 주행 시간 또는 거리 안에 있는 관심 지점을 검색합니다. 이렇게 하려면 등시선을 계산하여 [기하 도형 내에서 검색](/rest/api/maps/search/postsearchinsidegeometry) API로 전달해야 합니다.

Azure Maps는 관심 지점에 대한 여러 검색 API를 제공합니다.

* [POI 검색](/rest/api/maps/search/getsearchpoi): 이름으로 관심 지점을 검색합니다. 예: `"starbucks"`.
* [POI 범주 검색](/rest/api/maps/search/getsearchpoicategory): 범주로 관심 지점을 검색합니다. 예: "식당".
* [주변 검색](/rest/api/maps/search/getsearchnearby): 한 위치에서 특정 거리 내에 있는 관심 지점을 검색합니다.
* [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있는 자유 형식 문자열을 사용하여 요청을 즉시 처리합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 애플리케이션에 추천하는 방법입니다.
* [기하 도형 내에서 검색](/rest/api/maps/search/postsearchinsidegeometry): 지정된 기하 도형(다각형) 내에 있는 관심 지점을 검색합니다.
* [경로를 따라 검색](/rest/api/maps/search/postsearchalongroute): 지정된 경로를 따라 있는 관심 지점을 검색합니다.
* [유사 항목 일괄 검색](/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 10,000개의 주소, 장소, 랜드마크 또는 관심 지점을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

[검색 모범 사례](./how-to-use-best-practices-for-search.md) 설명서를 검토하세요.

## <a name="get-traffic-incidents"></a>트래픽 인시던트 가져오기

Azure Maps는 트래픽 데이터를 검색하는 몇 가지 API를 제공합니다. 다음과 같은 두 가지 유형의 트래픽 데이터를 사용할 수 있습니다.

* **흐름 데이터** - 도로 구획의 트래픽 흐름에 대한 메트릭을 제공합니다. 도로를 색으로 구분하는 데 자주 사용됩니다. 데이터는 2분마다 업데이트됩니다.
* **인시던트 데이터** – 트래픽에 영향을 줄 수 있는 공사, 도로 폐쇄, 사고 및 기타 인시던트에 대한 데이터를 제공합니다. 데이터는 1분마다 업데이트됩니다.

Bing Maps는 대화형 지도 컨트롤을 통해 트래픽 흐름 및 인시던트 데이터를 제공하며, 인시던트 데이터를 서비스로 제공합니다.

트래픽 데이터는 Azure Maps 대화형 지도 컨트롤에도 통합되어 있습니다. Azure Maps는 다음과 같은 트래픽 서비스 API도 제공합니다.

* [트래픽 흐름 세그먼트](/rest/api/maps/traffic/gettrafficflowsegment): 지정된 좌표와 가장 가까운 도로 조각의 속도와 이동 시간에 대한 정보를 제공합니다.
* [트래픽 흐름 타일](/rest/api/maps/traffic/gettrafficflowtile): 트래픽 흐름 데이터를 포함하는 래스터 및 벡터 타일을 제공합니다. Azure Maps 컨트롤 또는 Leaflet 같은 타사 지도 컨트롤에서 사용할 수 있습니다. 벡터 타일은 고급 데이터 분석에도 사용할 수 있습니다.
* [트래픽 인시던트 세부 정보](/rest/api/maps/traffic/gettrafficincidentdetail): 경계 상자, 확대/축소 수준 및 트래픽 모델 내에 있는 트래픽 인시던트 세부 정보를 제공합니다.
* [트래픽 인시던트 타일](/rest/api/maps/traffic/gettrafficincidenttile): 트래픽 인시던트 데이터를 포함하는 래스터 및 벡터 타일을 제공합니다.
* [트래픽 인시던트 뷰포트](/rest/api/maps/traffic/gettrafficincidentviewport): 트래픽 모델 ID처럼 요청에 설명된 뷰포트의 법률 및 기술 정보를 검색합니다.

다음 표에서는 Bing Maps 트래픽 API 매개 변수와 Azure Maps의 비슷한 트래픽 인시던트 세부 정보 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수  | 비슷한 Azure Maps API 매개 변수   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` 및 `boundingZoom`      |
| `includeLocationCodes`   | 해당 없음                                   |
| `severity` (`s`)         | 해당 없음 - 모든 데이터가 반환됨               |
| `type` (`t`)             | 해당 없음 - 모든 데이터가 반환됨               |
| `key`                    | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)          | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요. |
| `userRegion` (`ur`)      | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요. |

## <a name="get-a-time-zone"></a>표준 시간대 가져오기

Azure Maps는 좌표가 속한 표준 시간대를 검색할 수 있는 API를 제공합니다. Azure Maps 표준 시간대 API는 Bing Maps의 표준 시간대 API와 비슷합니다.

* [좌표 기준 표준 시간대](/rest/api/maps/timezone/gettimezonebycoordinates): 좌표를 지정하고 좌표가 속한 표준 시간대의 세부 정보를 가져옵니다.

다음 표에서는 Bing Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Bing Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | 해당 없음 - 위치부터 지오코딩해야 합니다.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | 해당 없음 – Azure Maps에서 항상 응답에 포함시킵니다. |
| `key`                   | `subscription-key` – [Azure Maps로 인증](./azure-maps-authentication.md) 설명서도 참조하세요. |
| `culture` (`c`)         | `language` - [지원되는 언어](./supported-languages.md) 설명서를 참조하세요.  |
| `userRegion` (`ur`)     | `view` - [지원되는 보기](./supported-languages.md#azure-maps-supported-views) 설명서를 참조하세요.  |

그 외에도 Azure Maps 플랫폼은 표준 시간대 이름 및 ID로 변환할 수 있는 다음과 같은 여러 표준 시간대 API를 추가로 제공합니다.

* [ID 기준 표준 시간대](/rest/api/maps/timezone/gettimezonebyid): 지정된 IANA 표준 시간대 ID의 현재, 과거 및 미래 표준 시간대 정보를 반환합니다.
* [표준 시간대 Enum IANA](/rest/api/maps/timezone/gettimezoneenumiana): IANA 표준 시간대 ID 전체 목록을 반환합니다. IANA 서비스 업데이트는 하루 이내에 시스템에 반영됩니다. 
* [표준 시간대 Enum Windows](/rest/api/maps/timezone/gettimezoneenumwindows): Windows 표준 시간대 ID 전체 목록을 반환합니다.
* [표준 시간대 IANA 버전](/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps에서 사용하는 현재 IANA 버전 번호를 반환합니다. 
* [표준 시간대 Windows-IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): 유효한 Windows 표준 시간대 ID를 고려하여 해당하는 IANA ID를 반환합니다. Windows ID 하나에 여러 IANA ID가 반환될 수 있습니다.

## <a name="spatial-data-services-sds"></a>SDS(Spatial Data Services)

Bing Maps의 공간 데이터 서비스는 다음과 같은 세 가지 주요 기능을 제공합니다.

* 일괄 처리 지오코딩 – 단일 요청으로 일괄 처리 주소 지오코딩을 처리합니다.
* 관리 경계 데이터 검색 – 좌표를 사용하고 지정된 엔터티 형식의 교차 경계를 가져옵니다.
* 공간 비즈니스 데이터 호스트 및 쿼리 – 간단한 2D 데이터 테이블을 업로드하고 몇 가지 간단한 공간 쿼리를 사용하여 액세스합니다.

### <a name="batch-geocode-data"></a>일괄 처리 지오코딩 데이터

일괄 처리 지오코딩은 대량의 주소 또는 장소를 가져와서 단일 요청으로 서비스에 전달한 다음, 모두 병렬로 지오코딩하고 그 결과를 단일 응답으로 반환하는 프로세스입니다.

Bing Maps는 일괄 처리 지오코딩 요청 하나로 최대 200,000개의 주소를 전달할 수 있습니다. 이 요청은 큐에 들어간 후 일반적으로 데이터 세트의 크기와 서비스의 부하에 따라 몇 분에서 몇 시간까지 일정 시간 동안 처리합니다. 요청의 각 주소에서 트랜잭션을 생성했습니다.

Azure Maps에는 일괄 처리 지오코딩 서비스가 있지만, 단일 요청으로 최대 10,000개의 주소를 전달할 수 있으며 데이터 세트의 크기와 서비스의 부하에 따라 몇 초에서 몇 분 동안 처리됩니다. 요청의 각 주소에서 트랜잭션을 생성했습니다. Azure Maps에서 일괄 처리 지오코딩 서비스는 Gen 2 또는 S1 가격 책정 계층만 사용할 수 있습니다. 가격 책정 계층에 대한 자세한 내용은 [Azure Maps에서 적절한 가격 책정 계층 선택](choose-pricing-tier.md)을 참조하세요.

Azure Maps를 사용하여 대량의 주소를 지오코딩하는 또 다른 옵션은 표준 검색 API에 대한 병렬 요청을 만드는 것입니다. 이러한 서비스는 요청당 하나의 주소만 허용하지만, 무료 사용량 제한을 제공하는 S0 계층에도 사용할 수 있습니다. S0 계층은 단일 계정에서 Azure Maps 플랫폼으로 초당 최대 50개 요청을 보낼 수 있습니다. 따라서 이러한 제한 내에서만 처리할 경우 시간당 최대 180,000개 주소를 지오코딩할 수 있습니다. Gen 2 또는 S1 가격 책정 계층은 계정에서 수행할 수 있는 초당 쿼리 수에 대한 문서화된 제한이 없으므로 이 가격 책정 계층을 사용하면 훨씬 많은 데이터를 더 빠르게 처리할 수 있습니다. 그러나 일괄 처리 지오코딩 서비스를 사용하면 전송되는 총 데이터 양을 줄이고 네트워크 트래픽을 크게 줄일 수 있습니다.

* [자유 형식 주소 지오코딩](/rest/api/maps/search/getsearchaddress): 단일 주소 문자열(예: `"1 Microsoft way, Redmond, WA"`)을 지정하고 요청을 즉시 처리합니다. 이 서비스는 개별 주소를 신속하게 지오코딩해야 하는 경우에 추천합니다.
* [정형 주소 지오코딩](/rest/api/maps/search/getsearchaddressstructured): 도로명, 구/군/시, 국가, 우편 번호 등의 단일 주소 부분을 지정하고 요청을 즉시 처리합니다. 이 서비스는 개별 주소를 신속하게 지오코딩하고 데이터를 개별 주소 부분으로 구문 분석해야 하는 경우에 추천합니다.
* [일괄 처리 주소 지오코딩](/rest/api/maps/search/postsearchaddressbatchpreview): 최대 10,000개의 주소를 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 주소가 서버에서 병렬로 지오코딩되며, 코딩이 완료되면 전체 결과 세트를 다운로드할 수 있습니다. 이 서비스는 대형 데이터 세트를 지오코딩할 때 추천합니다.
* [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있는 자유 형식 문자열을 사용하여 요청을 즉시 처리합니다. 이 API는 사용자가 같은 텍스트 상자에서 주소 또는 관심 지점을 검색할 수 있는 애플리케이션에 추천하는 방법입니다.
* [유사 항목 일괄 검색](/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 10,000개의 주소, 장소, 랜드마크 또는 관심 지점을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

### <a name="get-administrative-boundary-data"></a>관리 경계 데이터 가져오기

Bing Maps에서 국가, 도/시, 시/군, 시 및 우편 번호의 관리 경계는 Geodata API를 통해 제공됩니다. 이 API는 지오코딩에 좌표 또는 쿼리를 사용합니다. 쿼리가 전달되면 쿼리가 지오코딩되고 첫 번째 결과의 좌표가 사용됩니다. 이 API는 좌표를 사용하고 좌표와 교차하는 지정된 엔터티 형식의 경계를 검색합니다. 이 API는 전달된 쿼리의 경계를 반드시 반환하는 것은 아닙니다. `"Seattle, WA"`에 대한 쿼리가 전달되었지만 엔터티 형식 값이 국가 지역으로 설정된 경우 USA의 경계가 반환됩니다.

Azure Maps는 관리 경계(국가, 도/시, 시/군, 시 및 우편 번호)에 대한 액세스도 제공합니다. 경계를 검색하려면 원하는 경계(예: `Seattle, WA`)에 대한 검색 API 중 하나를 쿼리해야 합니다. 검색 결과와 연결된 경계가 있는 경우 기하 도형 ID가 결과 응답에 제공됩니다. 그 후 검색 다각형 API를 사용하여 하나 이상의 기하 도형 ID에 대한 정확한 경계를 검색할 수 있습니다. 이는 Bing Maps와 약간 다른데, Azure Maps는 검색된 항목에 대한 경계를 반환하는 반면, Bing Maps는 지정된 좌표에서 지정된 엔터티 형식에 대한 경계를 반환합니다. 또한 Azure Maps에서 반환된 경계 데이터는 GeoJSON 형식입니다.

요약하면 다음과 같습니다.

1. 받고 싶은 경계에 대한 쿼리를 다음 검색 API 중 하나에 전달합니다.

   * [자유 형식 주소 지오코딩](/rest/api/maps/search/getsearchaddress)
   * [정형 주소 지오코딩](/rest/api/maps/search/getsearchaddressstructured)
   * [일괄 처리 주소 지오코딩](/rest/api/maps/search/postsearchaddressbatchpreview)
   * [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy)
   * [유사 항목 일괄 검색](/rest/api/maps/search/postsearchfuzzybatchpreview)

1. 원하는 결과의 기하 도형 ID가 있는 경우 [검색 다각형 API](/rest/api/maps/search/getsearchpolygon)에 전달합니다.

### <a name="host-and-query-spatial-business-data"></a>공간 비즈니스 데이터 호스트 및 쿼리

Bing Maps의 공간 데이터 서비스는 비즈니스 데이터를 호스트하고 공간 REST 서비스로 공개하기 위한 간단한 공간 데이터 스토리지 솔루션을 제공합니다. 이 서비스는 속성으로 찾기, 주변 찾기, 경계 상자에서 찾기, 1마일 경로로 찾기의 네 가지 주요 쿼리를 제공합니다. 이 서비스를 사용하는 여러 회사는 이미 어딘가에 있는 데이터베이스에 비즈니스 데이터가 저장되어 있으며, 저장소 로케이터 같은 강력한 애플리케이션을 구동하기 위해 데이터 하위 집합을 이 서비스로 업로드하는 경우가 많습니다. 키 기반 인증은 기본 보안을 제공하므로 이 서비스를 공용 데이터에만 사용하는 것이 좋습니다.

대부분의 비즈니스 위치 데이터는 데이터베이스에서 시작됩니다. 따라서 Azure SQL 또는 Azure PostgreSQL(PostGIS 플러그 인 포함) 같은 기존 Azure 스토리지 솔루션을 사용하는 것이 좋습니다. 이러한 스토리지 솔루션은 공간 데이터를 지원하며 풍부한 공간 쿼리 기능 세트를 제공합니다. 데이터가 적절한 스토리지 솔루션에 있는 경우 사용자 지정 웹 서비스를 만들거나 ASP.NET 또는 Entity Framework 같은 프레임워크를 사용하여 데이터를 애플리케이션에 통합할 수 있습니다. 이 방법을 사용하면 더 많은 쿼리 기능과 훨씬 강력한 옵션을 사용할 수 있습니다.

또한 Azure Cosmos DB는 시나리오에 따라 충분할 수도 있고 충분하지 않을 수도 있는 제한된 공간 기능 세트를 제공합니다.

다음은 Azure에서 공간 데이터를 호스트하고 쿼리하는 방법에 대한 유용한 리소스입니다.

* [Azure SQL 공간 데이터 형식 개요](/sql/relational-databases/spatial/spatial-data-types-overview)
* [Azure SQL 공간 – 가장 인접한 항목 쿼리](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
* [Azure Cosmos DB 지리 공간적 기능 개요](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Maps는 다음 프로그래밍 언어를 위한 클라이언트 라이브러리를 제공합니다.

* JavaScript, TypeScript, Node.js – [설명서](./how-to-use-services-module.md) \| [NPM 패키지](https://www.npmjs.com/package/azure-maps-rest)

다른 프로그래밍 언어를 위한 오픈 소스 클라이언트 라이브러리:

* .NET Standard 2.0 – [GitHub 프로젝트](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 패키지](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Maps REST 서비스에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [검색 서비스 사용 모범 사례](how-to-use-best-practices-for-search.md)
