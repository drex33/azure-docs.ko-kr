---
title: 자습서 - Google Maps에서 웹 서비스 마이그레이션 | Microsoft Azure Maps
description: Google Maps에서 Microsoft Azure Maps로 웹 서비스를 마이그레이션하는 방법에 대한 자습서
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/23/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 33bb2f240c46ac7e2192c84dbfd46906a6cc88f4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429679"
---
# <a name="tutorial-migrate-web-service-from-google-maps"></a>자습서: Google Maps에서 웹 서비스 마이그레이션

Azure 및 Google Maps 둘 다 REST 웹 서비스를 통해 공간 API에 액세스할 수 있습니다. 이러한 플랫폼의 API 인터페이스는 비슷한 기능을 수행합니다. 하지만, 각각 다른 명명 규칙과 응답 개체를 사용합니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 정방향 및 역방향 지오코딩
> * 관심 지점 검색
> * 경로 및 방향 계산
> * 맵 이미지 검색
> * 거리 행렬 계산
> * 표준 시간대 세부 정보 가져오기

다음 내용도 알아봅니다.

> [!div class="checklist"]
> * Google Maps 웹 서비스에서 마이그레이션할 때의 Azure Maps REST 서비스
> * Azure Maps 서비스를 최대한 활용하는 방법에 대한 팁
> * 기타 관련 Azure Maps 서비스에 대한 인사이트

다음 표에는 나열된 Google Maps 서비스 API와 기능이 유사한 Azure Maps 서비스 API가 있습니다.

| Google Maps 서비스 API | Azure Maps 서비스 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 방향              | [Route](/rest/api/maps/route)                                     |                         
| 거리 행렬         | [경로 행렬](/rest/api/maps/route/postroutematrixpreview)       |                         
| 지오코딩               | [검색](/rest/api/maps/search)                                   |                         
| 장소 검색           | [검색](/rest/api/maps/search)                                   |                         
| 장소 자동 완성      | [검색](/rest/api/maps/search)                                   |                         
| 도로에 맞춤            | [경로 및 방향 계산](#calculate-routes-and-directions) 섹션을 참조하세요.  |        
| 속도 제한            | [좌표 역방향 지오코딩](#reverse-geocode-a-coordinate) 섹션을 참조하세요.         |         
| 정적 맵              | [Render](/rest/api/maps/render/getmapimage)                       |                         
| 표준 시간대               | [표준 시간대](/rest/api/maps/timezone)                              |                         
| 상승               | [권한 상승](/rest/api/maps/elevation)                   | 

다음 서비스 API는 현재 Azure Maps에서 사용할 수 없습니다.

- 지리적 위치 - Azure Maps에는 지리적 위치라는 서비스가 있어서 위치 정보에 IP 주소를 제공하지만, 현재 셀 타워 또는 WiFi 삼각 측정을 지원하지 않습니다.
- 위치 세부 정보 및 사진 - 전화 번호 및 웹 사이트 URL은 Azure Maps 검색 API에서 사용할 수 있습니다.
- 맵 URL
- 가장 가까운 도로 - [여기](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)에 표시된 것처럼 Web SDK를 사용하여 달성할 수 있지만 현재 서비스로는 사용할 수 없습니다.
- 정적 주소 보기

Azure Maps에는 다음과 같은 몇 가지 흥미로운 다른 REST 웹 서비스가 있습니다.

- [공간 작업](/rest/api/maps/spatial): 복잡한 공간 계산과 작업(예: 지오펜싱)을 서비스로 오프로드합니다.
- [트래픽](/rest/api/maps/traffic): 실시간 트래픽 흐름 및 인시던트 데이터에 액세스합니다.

## <a name="prerequisites"></a>필수 조건

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
2. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="geocoding-addresses"></a>주소 지오코딩

지오코딩은 주소를 좌표로 변환하는 프로세스입니다. 예를 들어, "1 Microsoft way, Redmond, WA"를 경도: -122.1298, 위도: 47.64005로 변환합니다. 그런 다음, 맵 가운데 표식을 배치하는 등의 다양한 목적으로 좌표를 사용할 수 있습니다.

Azure Maps는 다음과 같은 주소를 지오코딩하는 여러 가지 방법을 제공합니다.

- [**자유 형식 주소 지오코딩**](/rest/api/maps/search/getsearchaddress): 단일 주소 문자열을 지정하고 요청을 즉시 처리합니다. "1 Microsoft way, Redmond, WA"는 단일 주소 문자열의 예입니다. 이 API는 개별 주소를 신속하게 지오코딩해야 하는 경우 권장됩니다.
- [**정형 주소 지오코딩**](/rest/api/maps/search/getsearchaddressstructured): 도로명, 구/군/시, 국가/지역, 우편 번호 등의 단일 주소 부분을 지정하고 요청을 즉시 처리합니다. 이 API는 개별 주소를 신속하게 지오코딩하고 데이터를 개별 주소 부분으로 구문 분석해야 하는 경우에 권장됩니다.
- [**일괄 처리 주소 지오코딩**](/rest/api/maps/search/postsearchaddressbatchpreview): 최대 10,000개의 주소를 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 주소가 서버에서 병렬로 지오코딩되며, 코딩이 완료되면 전체 결과 세트를 다운로드할 수 있습니다. 대형 데이터 세트를 지오코딩할 때 추천하는 방법입니다.
- [**유사 항목 검색**](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 자유 형식 문자열을 사용합니다. 이 문자열은 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있습니다. 이 API는 거의 실시간으로 요청을 처리합니다. 이 API는 사용자가 동일한 텍스트 상자에서 주소 또는 관심 지점을 검색하는 애플리케이션에 권장됩니다.
- [**유사 항목 일괄 검색**](/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 10,000개의 주소, 장소, 랜드마크 또는 관심 지점을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 및 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 시<br/>`municipalitySubdivision` – 이웃 도시, 하위 도시/대도시권<br/>`countrySubdivision` - 도<br/>`countrySecondarySubdivision` - 군<br/>`countryTertiarySubdivision` - 구<br/>`countryCode` - 두 문자로 된 국가/지역 코드 |
| `key`                       | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                  | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `region`                    | `countrySet`                       |

검색 서비스를 사용하는 방법의 예제는 [여기](how-to-search-for-address.md)에 설명되어 있습니다. [검색 관련 모범 사례](how-to-use-best-practices-for-search.md)를 검토해야 합니다.

> [!TIP]
> 요청 URL에 `&typeahead=true`를 추가하여 자동 완성 모드에서 자유 형식 주소 지오코딩 및 유사 항목 검색 API를 사용할 수 있습니다. 이렇게 하면 입력 텍스트가 일부일 가능성이 높다는 사실을 서버에 알리고 검색이 예측 모드로 전환됩니다.

## <a name="reverse-geocode-a-coordinate"></a>좌표 역방향 지오코딩

역방향 지오코딩은 지리적 좌표를 대략적인 주소로 변환하는 프로세스입니다. "경도: -122.1298, 위도: 47.64005"인 좌표는 "1 Microsoft way, Redmond, WA"로 변환됩니다.

Azure Maps는 다음과 같은 여러 가지 역방향 지오코딩 방법을 제공합니다.

- [**주소 역방향 지오코딩**](/rest/api/maps/search/getsearchaddressreverse): 하나의 지리적 좌표를 지정하면 좌표에 해당하는 대략적인 주소를 가져옵니다. 요청을 거의 실시간으로 처리합니다.
- [**교차로 역방향 지오코딩**](/rest/api/maps/search/getsearchaddressreversecrossstreet): 하나의 지리적 좌표를 지정하면 근처 교차로 정보를 가져오고 요청을 즉시 처리합니다. 예를 들어, 다음과 같은 교차로 1st Ave 및 Main St를 받을 수 있습니다.
- [**일괄 주소 역방향 지오코딩**](/rest/api/maps/search/postsearchaddressreversebatchpreview): 최대 10,000개의 좌표를 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터는 서버에서 병렬로 처리됩니다. 요청이 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수   | 비슷한 Azure Maps API 매개 변수   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                  | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `latlng`                    | `query`  |
| `location_type`             | *해당 사항 없음*     |
| `result_type`               | `entityType`    |

[검색 관련 모범 사례](how-to-use-best-practices-for-search.md)를 검토하세요.

Azure Maps 역방향 지오코딩 API에는 Google Maps에서 사용할 수 없는 몇 가지 다른 기능이 있습니다. 이러한 기능은 앱을 마이그레이션 할 때 애플리케이션과 통합하는 데 유용할 수 있습니다.

* 속도 제한 데이터 검색
* 지방 도로, 간선 도로, 진입 금지, 경사로 등의 도로 사용 정보 검색
* 좌표가 있는 거리의 측면 검색

## <a name="search-for-points-of-interest"></a>관심 지점 검색

관심 지점 데이터는 Places Search API를 사용하여 Google Maps에서 검색할 수 있습니다. 이 API는 관심 지점을 검색하는 다음 세 가지 방법을 제공합니다.

* **텍스트에서 장소 찾기:** 이름, 주소 또는 전화 번호를 기반으로 관심 지점을 검색합니다.
* **주변 검색**: 한 위치에서 특정 거리 내에 있는 관심 지점을 검색합니다.
* **텍스트 검색:** 관심 지점 및 위치 정보가 포함된 자유 형식 텍스트를 사용하여 장소를 검색합니다. 예: "뉴욕의 피자 가게" 또는 "메인 스트리트 근처의 식당".

Azure Maps는 관심 지점에 대한 여러 검색 API를 제공합니다.

- [**POI 검색**](/rest/api/maps/search/getsearchpoi): 이름으로 관심 지점을 검색합니다. 예: "스타벅스".
- [**POI 범주 검색**](/rest/api/maps/search/getsearchpoicategory): 범주로 관심 지점을 검색합니다. 예: "식당".
- [**주변 검색**](/rest/api/maps/search/getsearchnearby): 한 위치에서 특정 거리 내에 있는 관심 지점을 검색합니다.
- [**유사 항목 검색**](/rest/api/maps/search/getsearchfuzzy): 이 API는 주소 지오코딩을 관심 지점 검색과 결합합니다. 이 API는 주소, 장소, 랜드마크, 관심 지점 또는 관심 지점 범주일 수 있는 자유 형식 문자열을 사용합니다. 요청을 거의 실시간으로 처리합니다. 이 API는 사용자가 동일한 텍스트 상자에서 주소 또는 관심 지점을 검색하는 애플리케이션에 권장됩니다.
- [**기하 도형 내에서 검색**](/rest/api/maps/search/postsearchinsidegeometry): 지정된 기하 도형 내에 있는 관심 지점을 검색합니다. 예를 들어 다각형 내에서 관심 지점을 검색합니다.
- [**경로를 따라 검색**](/rest/api/maps/search/postsearchalongroute): 지정된 경로를 따라 있는 관심 지점을 검색합니다.
- [**유사 항목 일괄 검색**](/rest/api/maps/search/postsearchfuzzybatchpreview): 최대 10,000개의 주소, 장소, 랜드마크 또는 관심 지점을 포함하는 요청을 만듭니다. 요청이 일정 기간 동안 처리됩니다. 모든 데이터는 서버에서 병렬로 처리됩니다. 요청 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

현재 Azure Maps에는 Google Maps의 Text Search API와 비슷한 API가 없습니다.

> [!TIP]
> POI 검색, POI 범주 검색 및 유사 항목 검색 API는 요청 URL에 `&typeahead=true`를 추가하여 자동 완성 모드에서 사용할 수 있습니다. 이렇게 하면 입력 텍스트가 일부일 가능성이 높다는 사실을 서버에 알립니다. API는 예측 모드에서 검색을 수행합니다.

[검색 관련 모범 사례](how-to-use-best-practices-for-search.md) 설명서를 검토하세요.

### <a name="find-place-from-text"></a>텍스트에서 장소 찾기

Azure Maps [POI 검색](/rest/api/maps/search/getsearchpoi) 및 [유사 항목 검색](/rest/api/maps/search/getsearchfuzzy)을 사용하면 이름이나 주소로 관심 지점을 검색할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 비슷한 Azure Maps API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수 |
|---------------------------|-------------------------------------|
| `fields`                  | *해당 사항 없음*                               |
| `input`                   | `query`                             |
| `inputtype`               | *해당 사항 없음*                               |
| `key`                     | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `locationbias`            | `lat`, `lon`및 `radius`<br/>`topLeft` 및 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>주변 검색

[주변 검색](/rest/api/maps/search/getsearchnearby) API를 사용하면 Azure Maps에서 주변 관심 지점을 검색할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 비슷한 Azure Maps API 매개 변수를 보여줍니다.

| Google Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `keyword`                   | `categorySet` 및 `brandSet`        |
| `language`                  | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `location`                  | `lat` 및 `lon`                     |
| `maxprice`                  | *해당 사항 없음*                               |
| `minprice`                  | *해당 사항 없음*                               |
| `name`                      | `categorySet` 및 `brandSet`        |
| `opennow`                   | *해당 사항 없음*                               |
| `pagetoken`                 | `ofs` 및 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *해당 사항 없음*                               |
| `type`                      | `categorySet –` [지원되는 검색 범주](supported-search-categories.md) 설명서를 참조하세요.   |

## <a name="calculate-routes-and-directions"></a>경로 및 방향 계산

Azure Maps를 사용하여 경로와 방향을 계산합니다. Azure Maps에는 다음과 같이 Google Maps 라우팅 서비스와 동일한 여러 기능이 있습니다.

* 도착 및 출발 시간
* 실시간 및 예측 기반 트래픽 경로
* 다양한 교통 수단. 운전, 걷기, 자전거 타기 등.

> [!NOTE]
> Azure Maps는 모든 중간 지점이 좌표여야 합니다. 주소는 먼저 지오코딩되어야 합니다.

Azure Maps 라우팅 서비스는 경로 계산을 위한 다음 API를 제공합니다.

- [**경로 계산**](/rest/api/maps/route/getroutedirections): 경로를 계산하고 요청을 즉시 처리합니다. 이 API는 GET 및 POST 요청을 모두 지원합니다. POST 요청은 많은 수의 중간 지점을 지정할 때 또는 URL 요청이 너무 길어서 문제가 발생하지 않도록 많은 경로 옵션을 사용할 때 권장되는 방법입니다. Azure Maps의 POST 경로 방향에는 수천 개의 [지원 요소](/rest/api/maps/route/postroutedirections#supportingpoints)를 사용할 수 있는 옵션이 있으며 이를 사용하여 둘 사이의 논리적 경로를 다시 만들 수 있습니다(도로에 맞춤). 
- [**일괄 처리 경로**](/rest/api/maps/route/postroutedirectionsbatchpreview): 최대 1,000개의 경로 요청을 포함하는 요청을 만들고 일정 기간 동안 처리합니다. 모든 데이터가 서버에서 병렬로 처리되며, 처리가 완료되면 전체 결과 세트를 다운로드할 수 있습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수    | 비슷한 Azure Maps API 매개 변수  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – `"lat0,lon0:lat1,lon1…."` 형식의 좌표  |
| `key`                          | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                     | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *해당 없음* - 이 기능은 지오코딩과 관련이 있습니다. Azure Maps 지오코딩 API를 사용하는 경우 *countrySet* 매개 변수를 사용합니다.  |
| `traffic_model`               | *해당 없음* – *traffic* 매개 변수에 트래픽 데이터를 사용해야 하는 경우에만 지정할 수 있습니다. |
| `units`                        | *해당 없음* – Azure Maps는 메트릭 시스템만 사용합니다.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 기본적으로 Azure Maps Route API는 요약만 반환합니다. 경로의 거리와 시간 및 좌표를 반환합니다. `instructionsType` 매개 변수를 사용하여 턴바이턴 지침을 검색합니다. `routeRepresentation` 매개 변수를 사용하여 요약 정보 및 경로를 필터링합니다.

Azure Maps 라우팅 API에는 Google Maps에서 사용할 수 없는 다른 기능이 있습니다. 앱을 마이그레이션할 때 이러한 기능을 사용하면 유용할 수 있습니다.

* 경로 유형 지원: 최단거리, 최단시간, 선회 및 최고연비.
* 기타 이동 모드 지원: 버스, 오토바이, 택시, 트럭, 승합차.
* 150개 중간 지점을 지원합니다.
* 단일 요청에서 여러 이동 시간(기록 트래픽, 실시간 트래픽, 트래픽 없음)을 계산합니다.
* 기타 도로 유형(카풀 도로, 비포장 도로, 이미 사용한 도로)을 회피합니다.
* 회피할 사용자 지정 영역을 지정합니다.
* 경로가 상승할 수 있는 고도를 제한합니다.
* 엔진 사양에 따른 경로. 엔진 사양과 남은 연료 또는 충전량을 기반으로 내연 기관 차량이나 전기차의 경로를 계산합니다.
* 상용 차량 경로 매개 변수를 지원합니다. 예: 차량 치수, 무게, 차축 수 및 화물 유형.
* 최대 차량 속도를 지정합니다.

추가적으로 Azure Maps의 경로 서비스는 [라우팅할 수 있는 범위 계산](/rest/api/maps/route/getrouterange)을 지원합니다. 라우팅 가능 범위를 계산하는 것을 동시선(isochrone)이라고도 합니다. 이를 위해서는 원점에서 어느 방향으로든 이동할 수 있는 영역을 포함하는 다각형을 생성해야 합니다. 모두 지정된 길이의 시간 또는 지정된 양의 연료 또는 충전량 하에서 고려해야 합니다.

[라우팅 모범 사례](how-to-use-best-practices-for-routing.md) 설명서를 검토하세요.

## <a name="retrieve-a-map-image"></a>맵 이미지 검색

Azure Maps는 데이터가 오버레이된 정적 맵 이미지를 렌더링하기 위한 API를 제공합니다. Azure Maps의 [맵 이미지 렌더링](/rest/api/maps/render/getmapimagerytile) API는 Google Maps의 정적 맵 API와 비슷합니다.

> [!NOTE]
> Azure Maps에서는 중심, 모든 표식 및 경로 위치가 "경도, 위도" 형식의 좌표여야 합니다. 반면, Google Maps는 "위도,경도" 형식을 사용합니다. 가장 먼저 주소를 지오코딩해야 합니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` - URL 경로의 일부로 지정됩니다. 현재는 PNG만 지원됩니다. |
| `key`                       | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                  | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `maptype`                   | `layer` 및 `style` – [지원되는 맵 스타일](supported-map-styles.md) 설명서를 참조하세요. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *해당 없음* - 지오코딩 관련 기능입니다. Azure Maps 지오코딩 API를 사용하는 경우 `countrySet` 매개 변수를 사용합니다.  |
| `scale`                     | *해당 사항 없음*                              |
| `size`                      | `width` 및 `height` – 최대 크기는 8192x8192입니다. |
| `style`                     | *해당 사항 없음*                              |
| `visible`                   | *해당 사항 없음*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps 타일 시스템에서, 타일은 Google Maps에 사용되는 지도 타일 크기의 두 배입니다. 따라서 Azure Maps의 확대/축소 수준 값은 Google Maps와 비교할 때 Azure Maps에서 한 수준 더 크게 표시됩니다. 이러한 차이를 보정하려면 마이그레이션하는 요청에서 확대/축소 수준을 낮춥니다.

자세한 내용은 [맵 이미지 렌더링 API에 대한 방법 가이드](how-to-render-custom-data.md)를 참조하세요.

정적 맵 이미지를 생성할 수 있다는 점 외에도, Azure Maps 렌더링 서비스는 래스터(PNG) 및 벡터 형식의 지도 타일에 직접 액세스할 수 있는 다음 기능을 제공합니다.

- [**지도 타일**](/rest/api/maps/render/getmaptile): 기본 맵(도로, 경계, 배경)의 래스터(PNG) 및 벡터 타일을 검색합니다.
- [**맵 이미지 타일**](/rest/api/maps/render/getmapimagerytile): 항공 및 위성 이미지 타일을 검색합니다.

> [!TIP]
> 많은 Google Maps 애플리케이션이 수년 전에 대화형 맵 환경에서 정적 맵 이미지로 전환되었습니다. 비용 절감 방법으로 수행되었습니다. Azure Maps에서는 웹 SDK에서 대화형 지도 컨트롤을 사용하는 것이 더 비용 효율적입니다. 대화형 지도 컨트롤은 타일 로드 수를 기반으로 요금이 부과됩니다. Azure Maps의 지도 타일은 큽니다. 정적 맵과 동일한 맵 보기를 재생성하는 데 몇 개의 타일만 필요한 경우가 많습니다. 지도 타일은 브라우저에 의해 자동으로 캐시됩니다. 따라서 대화형 지도 컨트롤은 정적 맵 보기를 다시 만들 때 트랜잭션의 일부를 생성하는 경우가 많습니다. 지동 및 확대/축소는 더 많은 타일을 로드하지만, 이 동작을 비활성화하는 옵션이 지도 컨트롤에 있습니다. 또한 대화형 지도 컨트롤은 정적 맵 서비스보다 훨씬 많은 시각화 옵션을 제공합니다.

### <a name="marker-url-parameter-format-comparison"></a>표식 URL 매개 변수 형식 비교

**이전: Google Maps**

URL에서 `markers` 매개 변수를 사용하여 표식을 추가합니다. `markers` 매개 변수는 아래와 같이 맵에 렌더링할 스타일 및 위치 목록을 가져옵니다.

```text
&markers=markerStyles|markerLocation1|markerLocation2|...
```

다른 스타일을 추가하려면 다른 스타일과 위치 세트로 URL에 `markers` 매개 변수를 사용합니다.

"위도,경도" 형식으로 표식 위치를 지정합니다.

`optionName:value` 형식으로 표식 스타일을 추가하며, "optionName1:value1\|optionName2:value2"처럼 여러 스타일은 파이프(\|) 문자로 구분됩니다. 옵션 이름 및 값은 콜론(:)으로 구분됩니다. 다음 스타일 옵션 이름을 사용하여 Google Maps의 표식 스타일을 지정합니다.

* `color` – 기본 표식 아이콘의 색입니다. 24비트 16진수 색(`0xrrggbb`)일 수도 있고 `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white` 값 중 하나일 수도 있습니다.
* `label` – 아이콘 위에 표시할 단일 대문자 영숫자입니다.
* `size` - 표식의 크기입니다. `tiny`, `mid` 또는 `small`일 수 있습니다.

Google Maps의 사용자 지정 아이콘에는 다음과 같은 스타일 옵션 이름을 사용합니다.

* `anchor` – 아이콘 이미지를 좌표에 맞추는 방법을 지정합니다. 픽셀 (x, y) 값일 수도 있고 `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`, `bottomright` 값 중 하나일 수도 있습니다.
* `icon` – 아이콘 이미지를 가리키는 URL입니다.

예를 들어, 경도: -110, 위도: 45 맵에 빨간색, 중간 크기의 표식을 추가하겠습니다.

```text
&markers=color:red|size:mid|45,-110
```

![Google Maps 표식](media/migrate-google-maps-web-services/google-maps-marker.png)

**이후: Azure Maps**

URL에 `pins` 매개 변수를 지정하여 정적 맵 이미지에 표식을 추가합니다. Google Maps와 마찬가지로 매개 변수에 스타일 및 위치 목록을 지정합니다. `pins` 매개 변수는 다양한 스타일의 표식을 지원하기 위해 여러 번 지정할 수 있습니다.

```text
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

다른 스타일을 사용하려면 다른 스타일과 위치 세트로 URL에 `pins` 매개 변수를 더 추가합니다.

Azure Maps에서 핀 위치는 "경도 위도" 형식이어야 합니다. Google Maps는 "위도,경도" 형식을 사용합니다. Azure Maps 형식에는 쉼표가 아닌 공백이 경도와 위도를 구분합니다.

`iconType`은 생성할 핀의 유형을 지정합니다 다음과 같은 값을 가질 수 있습니다.

* `default` – 기본 핀 아이콘입니다.
* `none` – 아이콘이 표시되지 않고 레이블만 렌더링됩니다.
* `custom` – 사용자 지정 아이콘을 사용하도록 지정합니다. 아이콘 이미지를 가리키는 URL은 핀 위치 정보 뒤의 `pins` 매개 변수 끝에 추가할 수 있습니다.
* `{udid}` – Azure Maps 데이터 스토리지 플랫폼에 저장되는 아이콘의 UDID(고유 데이터 ID)입니다.

`optionNameValue` 형식으로 핀 스타일을 추가합니다. 여러 스타일은 파이프(\|) 문자로 구분합니다. 예: `iconType|optionName1Value1|optionName2Value2` 옵션 이름과 값은 분리되지 않습니다. 다음 스타일 옵션 이름을 사용하여 표식 스타일을 지정합니다.

* `al` – 표식의 불투명도(알파)를 지정합니다. 0과 1 사이의 숫자를 선택합니다.
* `an` – 핀 앵커를 지정합니다. "x y"형식으로 X 및 y 픽셀 값을 지정합니다.
* `co` - 핀 색입니다. 24비트 16진수 색(`000000`~`FFFFFF`)을 지정합니다.
* `la` - 레이블 앵커를 지정합니다. "x y"형식으로 X 및 y 픽셀 값을 지정합니다.
* `lc` - 레이블 색입니다. 24비트 16진수 색(`000000`~`FFFFFF`)을 지정합니다.
* `ls` - 레이블의 크기(픽셀)입니다. 0보다 큰 숫자를 선택합니다.
* `ro` - 아이콘을 회전하는 각도입니다. -360과 360 사이의 숫자를 선택합니다.
* `sc` – 핀 아이콘의 배율 값입니다. 0보다 큰 숫자를 선택합니다.

각 핀 위치에 대한 레이블 값을 지정합니다. 이 방법이 위치 목록의 모든 표식에 단일 레이블 값을 적용하는 것보다 효율적입니다. 레이블 값은 여러 문자로 된 문자열일 수 있습니다. 문자열은 작은따옴표로 묶어서 스타일이나 위치 값으로 오인되지 않도록 합니다.

"Space Needle"이라는 레이블이 있는 빨간색(`FF0000`) 기본 아이콘을 아래(15 50)에 추가해보겠습니다. 아이콘은 경도: -122.349300, 위도: 47.620180에 있습니다.

```text
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Azure Maps 표식](media/migrate-google-maps-web-services/azure-maps-marker.png)

레이블 값이 '1', '2' 및 '3'인 세 개의 핀을 추가합니다.

```text
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Azure Maps 여러 표식](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>경로 URL 매개 변수 형식 비교

**이전: Google Maps**

URL에 `path` 매개 변수를 사용하여 정적 맵 이미지에 선과 다각형을 추가합니다. `path` 매개 변수는 아래와 같이 맵에 렌더링할 스타일과 위치 목록을 가져옵니다.

```text
&path=pathStyles|pathLocation1|pathLocation2|...
```

다른 스타일과 위치 세트를 사용하여 URL에 `path` 매개 변수를 더 추가하여 다른 스타일을 사용합니다.

경로 위치는 `latitude1,longitude1|latitude2,longitude2|…` 형식으로 지정됩니다. 경로는 인코딩할 수도 있고, 지점의 주소를 포함할 수도 있습니다.

`optionName:value` 형식으로 경로 스타일을 추가하고, 여러 스타일은 파이프(\|) 문자로 구분합니다. 그리고 옵션 이름과 값은 콜론(:)으로 구분합니다. 예: `optionName1:value1|optionName2:value2`. 다음 스타일 옵션 이름을 사용하여 Google Maps에서 스타일 경로를 지정할 수 있습니다.

* `color` – 경로 또는 다각형 윤곽선의 색입니다. 24비트 16진수 색(`0xrrggbb`), 32비트 16진수 색(`0xrrggbbbaa`) 또는 검은색, 갈색, 녹색, 보라색, 노란색, 파란색, 회색, 주황색, 빨간색, 흰색 중 하나일 수 있습니다.
* `fillColor` – 경로 영역을 채울 색입니다(다각형). 24비트 16진수 색(`0xrrggbb`), 32비트 16진수 색(`0xrrggbbbaa`) 또는 검은색, 갈색, 녹색, 보라색, 노란색, 파란색, 회색, 주황색, 빨간색, 흰색 중 하나일 수 있습니다.
* `geodesic` – 경로가 지구의 곡률을 따르는 선이어야 하는지 여부를 나타냅니다.
* `weight` - 경로 선의 두께(픽셀)입니다.

URL 매개 변수에서 좌표 사이의 맵에 빨간색 선 불투명도와 픽셀 두께를 추가합니다. 아래 예의 경우 선의 불투명도는 50%이고 두께는 4픽셀입니다. 좌표는 경도: -110, 위도: 45 및 경도: -100, 위도: 50.

```text
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google Maps 폴리라인](media/migrate-google-maps-web-services/google-maps-polyline.png)

**이후: Azure Maps**

URL에 `path` 매개 변수를 지정하여 정적 맵 이미지에 선과 다각형을 추가합니다. Google Maps와 마찬가지로 매개 변수에 스타일 및 위치 목록을 지정합니다. 스타일이 다른 여러 원, 선 및 다각형을 렌더링하려면 `path` 매개 변수를 여러 번 지정합니다.

```text
&path=pathStyles||pathLocation1|pathLocation2|...
```

경로 위치의 경우, Azure Maps에서는 좌표가 "경도 위도" 형식이어야 합니다. Google Maps는 "위도,경도" 형식을 사용합니다. Azure Maps 형식에는 쉼표가 아닌 공백이 경도와 위도를 구분합니다. Azure Maps는 지점에 대해 인코딩된 경로 또는 주소를 지원하지 않습니다. [여기](how-to-render-custom-data.md#upload-pins-and-path-data)에 설명된 대로 더 큰 데이터 세트는 Azure Maps 데이터 스토리지 API에 GeoJSON 파일로 업로드합니다.

`optionNameValue` 형식으로 경로 스타일을 추가합니다. 여러 스타일은 파이프(\|) 문자로 구분합니다(예: `optionName1Value1|optionName2Value2`). 옵션 이름과 값은 분리되지 않습니다. 다음 스타일 옵션 이름을 사용하여 Azure Maps에서 경로 스타일을 지정합니다.

* `fa` - 다각형을 렌더링할 때 사용되는 채우기 색 불투명도(알파)입니다. 0과 1 사이의 숫자를 선택합니다.
* `fc` - 다각형 영역을 렌더링하는 데 사용되는 채우기 색입니다.
* `la` – 선 및 다각형의 윤곽선을 렌더링할 때 사용되는 선 색 불투명도(알파)입니다. 0과 1 사이의 숫자를 선택합니다.
* `lc` – 선 및 다각형의 윤곽선을 렌더링하는 데 사용되는 선 색입니다.
* `lw` - 선의 너비(픽셀)입니다.
* `ra` – 원 반지름(미터)을 지정합니다.

URL 매개 변수에서 좌표 사이에 빨간색 선 불투명도와 픽셀 두께를 추가합니다. 아래 예의 경우 선의 불투명도는 50%이고 두께는 4픽셀입니다. 좌표의 값은 다음과 같습니다. 경도: -110, 위도 45 및 경도: -100, 위도: 50.

```text
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure Maps 폴리라인](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>거리 행렬 계산

Azure Maps는 거리 행렬 API를 제공합니다. 이 API를 사용하여, 거리 행렬로 위치 간의 거리와 이동 시간을 계산합니다. Google Maps의 Distance Matrix API와 비슷합니다.

- [**경로 행렬**](/rest/api/maps/route/postroutematrixpreview): 출발지 및 목적지 세트 간의 이동 시간 및 거리를 비동기적으로 계산합니다. 요청당 최대 700개의 셀을 지원합니다. 이것은 출발지 수에 목적지 수를 곱한 것입니다. 이 제약 조건을 고려할 때 구현 가능한 행렬 크기는 700x1, 50x10, 10x10, 28x25, 10x70입니다.

> [!NOTE]
> 거리 행렬 API에 대한 요청은 요청 본문에 출발지 및 목적지 정보가 포함된 POST 요청을 통해서만 만들 수 있습니다. 또한 Azure Maps는 모든 출발지 및 목적지가 좌표여야 합니다. 가장 먼저 주소를 지오코딩해야 합니다.

다음 표에서는 Google Maps API 매개 변수와 비슷한 Azure Maps API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수      | 비슷한 Azure Maps API 매개 변수  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – POST 요청 본문에서 GeoJSON으로 지정합니다. |
| `key`                          | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요. |
| `language`                     | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – POST 요청 본문에서 GeoJSON으로 지정합니다.  |
| `region`                       | *해당 없음* - 이 기능은 지오코딩과 관련이 있습니다. Azure Maps 지오코딩 API를 사용하는 경우 `countrySet` 매개 변수를 사용합니다. |
| `traffic_model`                | *해당 없음* – `traffic` 매개 변수에 트래픽 데이터를 사용해야 하는 경우에만 지정할 수 있습니다. |
| `transit_mode`                 | *해당 없음* - 대중 교통 기반 거리 행렬은 현재 지원되지 않습니다.  |
| `transit_routing_preference`   | *해당 없음* - 대중 교통 기반 거리 행렬은 현재 지원되지 않습니다.  |
| `units`                        | *해당 없음* – Azure Maps는 메트릭 시스템만 사용합니다. |

> [!TIP]
> Azure Maps 라우팅 API에서 사용 가능한 모든 고급 라우팅 옵션은 Azure Maps 거리 행렬 API에서 지원됩니다. 고급 라우팅 옵션에는 트럭 라우팅, 엔진 사양 등이 포함됩니다.

[라우팅 모범 사례](how-to-use-best-practices-for-routing.md) 설명서를 검토하세요.

## <a name="get-a-time-zone"></a>표준 시간대 가져오기

Azure Maps는 좌표의 표준 시간대를 검색할 수 있는 API를 제공합니다. Azure Maps 표준 시간대 API는 Google Maps의 표준 시간대 API와 비슷합니다.

- [**좌표 기준 표준 시간대**](/rest/api/maps/timezone/gettimezonebycoordinates): 좌표를 지정하고 좌표의 표준 시간대 정보를 받습니다.

다음 표에서는 Google Maps API 매개 변수와 Azure Maps의 비슷한 API 매개 변수를 상호 참조합니다.

| Google Maps API 매개 변수 | 비슷한 Azure Maps API 매개 변수   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – [Azure Maps로 인증](azure-maps-authentication.md) 설명서도 참조하세요.       |
| `language`                  | `language` - [지원되는 언어](supported-languages.md) 설명서를 참조하세요.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

이 API 외에도 Azure Maps는 많은 표준 시간대 API를 제공합니다. 이러한 API는 표준 시간대의 이름이나 ID를 기반으로 시간을 변환합니다.

- [**ID 기준 표준 시간대**](/rest/api/maps/timezone/gettimezonebyid): 지정된 IANA 표준 시간대 ID의 현재, 과거 및 미래 표준 시간대 정보를 반환합니다.
- [**표준 시간대 Enum IANA**](/rest/api/maps/timezone/gettimezoneenumiana): IANA 표준 시간대 ID 전체 목록을 반환합니다. IANA 서비스 업데이트는 하루 이내에 시스템에 반영됩니다.
- [**표준 시간대 Enum Windows**](/rest/api/maps/timezone/gettimezoneenumwindows): Windows 표준 시간대 ID 전체 목록을 반환합니다.
- [**표준 시간대 IANA 버전**](/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps에서 사용하는 현재 IANA 버전 번호를 반환합니다.
- [**표준 시간대 Windows-IANA**](/rest/api/maps/timezone/gettimezonewindowstoiana): 유효한 Windows 표준 시간대 ID를 고려하여 해당하는 IANA ID를 반환합니다. Windows ID 하나에 여러 IANA ID가 반환될 수 있습니다.

## <a name="client-libraries"></a>클라이언트 라이브러리

Azure Maps는 다음 프로그래밍 언어를 위한 클라이언트 라이브러리를 제공합니다.

* JavaScript, TypeScript, Node.js – [설명서](how-to-use-services-module.md) \| [NPM 패키지](https://www.npmjs.com/package/azure-maps-rest)

다음 오픈 소스 클라이언트 라이브러리는 다른 프로그래밍 언어를 위한 라이브러리입니다.

* .NET Standard 2.0 – [GitHub 프로젝트](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 패키지](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Maps REST 서비스에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [검색 관련 모범 사례](how-to-use-best-practices-for-search.md)
