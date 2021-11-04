---
title: Microsoft Azure Maps의 Azure Maps Route Service에 대한 모범 사례
description: Microsoft Azure Maps의 Route Service를 사용하여 차량을 라우팅하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 327747b731bfb26192f22631e23b4f3f7dc49cf3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447173"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure Maps Route Service에 대한 모범 사례

Azure Maps [Route Service](/rest/api/maps/route)의 경로 방향 및 경로 매트릭스 API를 사용하여 요청된 각 경로에 대한 ETA(예상 도착 시간)를 계산할 수 있습니다. 경로 API는 요청된 요일 및 시간의 일반적인 도로 통행 속도와 같은 실시간 교통 정보, 과거의 교통 데이터 같은 요소를 고려합니다. 이 API는 시간 또는 거리를 기준으로 차례대로 또는 최적화된 순서대로 한 번에 여러 대상에서 사용할 수 있는 최단 경로 또는 가장 빠른 경로를 반환합니다. 또한 사용자가 도보 이동자, 자전거 이용자 및 트럭 같은 상업용 차량의 특수 경로 및 세부 정보를 요청할 수 있습니다. 이 문서에서는 Azure Maps [Route Service](/rest/api/maps/route)를 호출하는 모범 사례를 공유하고 다음 작업 방법을 설명합니다.

 * 경로 방향 API와 매트릭스 라우팅 API 중에서 선택
 * 실시간 및 과거 트래픽 데이터를 기반으로 기록 및 예측된 이동 시간 요청
 * 전체 경로 및 경로의 각 지선에 대한 시간 및 거리와 같은 경로 세부 정보 요청
 * 트럭과 같은 상업용 차량에 대한 경로 요청
 * 경로와 함께 정체 및 톨게이트 요금 정보와 같은 교통 정보 요청
 * 하나 이상의 경유지(중간 지점)로 구성된 경로 요청
 * 각 경유지(이동 경로)를 방문할 최적의 순서를 가져오기 위해 하나 이상의 중지점 경로 최적화
 * 지원 지점을 사용하여 대체 경로를 최적화합니다. 예를 들어 전기 차량 충전소를 통과하는 대체 경로를 제공합니다.
 * Azure Maps 웹 SDK에서 [Route Service](/rest/api/maps/route) 사용

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Route Service의 적용 범위에 대한 자세한 내용은 [라우팅 적용 범위](routing-coverage.md)를 참조하세요.

이 문서에서는 [Postman 앱](https://www.postman.com/downloads/)을 사용하여 REST 호출을 빌드하지만 모든 API 개발 환경을 선택할 수 있습니다.

## <a name="choose-between-route-directions-and-matrix-routing"></a>경로 방향 및 매트릭스 라우팅 중에서 선택

경로 방향 API는 경로에 대한 좌표 및 이동 시간을 포함한 지침을 반환합니다. 경로 매트릭스 API를 사용하면 출발지 위치와 목적지 위치로 정의된 경로 세트에 대한 이동 시간과 거리를 계산할 수 있습니다. 매트릭스 API는 지정된 모든 출발지에 대해 해당 출발지부터 지정된 모든 목적지까지의 라우팅 비용(이동 시간 및 거리)을 계산합니다. 이러한 모든 API를 사용하여 원하는 출발 시간, 도착 시간 및 차량 유형(예: 자동차 또는 트럭)과 같은 매개 변수를 지정할 수 있습니다. 매개 변수는 모두 그에 따라 실시간 또는 예측 교통 데이터를 사용하여 가장 최적의 경로를 반환합니다.

다음과 같은 시나리오에서는 경로 방향 API를 호출하는 것이 좋습니다.

* 배달 차량의 정확한 도착 시간을 확인하려면 둘 이상의 알려진 위치 사이의 가장 짧거나 가장 빠른 주행 경로를 요청합니다.
* 경로 기하 도형을 비롯한 자세한 경로 안내를 요청하여 지도에 경로를 시각화합니다.
* 고객 위치 목록이 제공된 경우 각 고객 위치를 방문하고 출발지로 돌아갈 수 있는 가장 짧은 경로를 계산합니다. 이 시나리오를 일반적으로 여행하는 외판원 문제라고 합니다. 한 요청에서 최대 150개의 중간 지점(경유지)을 통과할 수 있습니다.
* 단일 API 호출을 사용하여 경로 방향 일괄 처리 API에 쿼리 일괄 처리를 보냅니다.

다음과 같은 시나리오에서는 매트릭스 라우팅 API를 호출하는 것이 좋습니다.

* 출발지와 목적지 세트 간 이동 시간 및 거리를 계산합니다. 예를 들어 운전자가 12명이고, 식당에서 배달 음식을 픽업할 수 있는 가장 가까운 운전자를 찾아야 합니다.
* 실제 이동 거리 또는 시간별로 가능한 경로를 정렬합니다. 매트릭스 API는 각 출발지와 목적지 조합에 대한 이동 시간과 거리만 반환합니다.
* 이동 시간 또는 거리를 기준으로 데이터를 클러스터링합니다. 예를 들어 회사에 근무하는 50명의 직원 중 사무실에서 드라이브 시간으로 20분 이내에 거주하는 직원을 모두 찾습니다.

다음은 경로 방향 및 매트릭스 API의 몇 가지 기능을 비교하여 보여 준 것입니다.

| Azure Maps API | 요청의 최대 쿼리 수 | 지역 피하기 | 트럭 및 전기 차량 라우팅 | 중간 지점 및 여행하는 외판원 최적화 | 지원 지점 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 경로 방향 가져오기 | 1 | | ✔ | ✔ | |
| 경로 방향 게시 | 1 | ✔ | ✔ | ✔ | ✔ |
| 사후 경로 방향 일괄 처리 | 700 | | ✔ | ✔ | |
| 경로 매트릭스 게시 | 700 | | ✔ | | |

전기 차량 라우팅 기능에 대해 자세히 알아보려면 [Azure Notebooks를 사용하여 전기 차량 라우팅(Python)](tutorial-ev-routing.md) 방법에 대한 자습서를 참조하세요.

## <a name="request-historic-and-real-time-data"></a>기록 및 실시간 데이터 요청

기본적으로 Route Service는 이동 수단이 자동차이며 출발 시간이 현재라고 가정합니다. 경로 계산 요청에서 달리 지정하지 않는 한 실시간 교통 상황에 따라 경로를 반환합니다. '오후 4시부터 오후 6시까지 좌회전 불가'와 같은 고정된 시간 종속 교통 제한이 캡처된 경우 라우팅 엔진에서 이를 고려합니다. 현재 실시간 교통을 무시하는 경로를 구체적으로 요청하지 않을 경우 도로 공사와 같은 도로 폐쇄가 고려됩니다. 현재 교통을 무시하려면 API 요청에서 `traffic`을 `false`로 설정합니다.

경로 계산 **travelTimeInSeconds** 값에 교통으로 인한 지연 시간이 포함됩니다. 출발 시간이 현재로 설정된 경우 현재 및 기록 이동 시간 데이터를 활용하여 생성됩니다. 출발 시간이 미래로 설정된 경우 API는 기록 데이터를 기반으로 예측된 이동 시간을 반환합니다.

요청에 **computeTravelTimeFor=all** 매개 변수를 포함하는 경우 응답의 요약 요소에 과거의 교통 상황을 포함하여 다음과 같은 추가 필드가 포함됩니다.

| 요소 | Description|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 정체 등의 교통 상황으로 인해 경로에 지연이 발생하지 않는 것처럼 계산된 예상 이동 시간 |
| historicTrafficTravelTimeInSeconds | 시간 종속 기록 교통 데이터를 사용하여 계산된 예상 이동 시간 |
| liveTrafficIncidentsTravelTimeInSeconds | 실시간 속도 데이터를 사용하여 계산된 예상 이동 시간 |

다음 섹션에서는 설명한 매개 변수를 사용하여 경로 API를 호출하는 방법을 보여 줍니다.

### <a name="sample-query"></a>샘플 쿼리

아래의 첫 번째 예제에서 출발 시간은 작성할 때 미래로 설정됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

응답에는 아래와 같은 요약 요소가 포함되어 있습니다. 출발 시간이 미래로 설정되므로 **trafficDelayInSeconds** 값은 0입니다. **travelTimeInSeconds** 값은 시간 종속 기록 교통 데이터를 사용하여 계산됩니다. 따라서 이 경우 **travelTimeInSeconds** 값은 **historicTrafficTravelTimeInSeconds** 값과 동일합니다.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>샘플 쿼리

아래 두 번째 예제에서는 출발 시간이 현재인 실시간 라우팅 요청이 있습니다. 현재 출발 시간이 기본값이므로 URL에 명시적으로 지정되지 않았습니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

응답에는 아래와 같이 요약이 포함됩니다. 정체로 인해 **trafficDelaysInSeconds** 값이 0보다 큽니다. 또한 **historicTrafficTravelTimeInSeconds** 값보다 큽니다.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>경로 및 지선 세부 정보 요청

기본적으로 Route Service는 좌표 배열을 반환합니다. 응답에는 `points`라고 하는 경로를 구성하는 좌표가 목록에 포함됩니다. 또한 경로 응답에는 경로의 출발 위치부터의 거리와 예상 경과 시간이 포함됩니다. 이러한 값을 사용하여 전체 경로의 평균 속도를 계산할 수 있습니다.

다음 이미지에서는 `points` 요소를 보여 줍니다.

![Points 요소](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

경로에 대한 좌표 목록을 보려면 `point` 요소를 확장합니다.

![확장된 points 요소](media/how-to-use-best-practices-for-routing/points-list-img.png)

경로 방향 API는 **instructionsType** 매개 변수를 지정하여 사용할 수 있는 다양한 명령 형식을 지원합니다. 간단한 컴퓨터 처리를 위한 명령 형식을 지정하려면 **instructionsType=coded** 를 사용합니다. **instructionsType=tagged** 를 사용하여 텍스트로 명령을 표시합니다. 또한 명령의 일부 요소가 표시되는 텍스트로 명령 형식을 지정할 수 있고, 명령이 특수 형식으로 제공됩니다. 자세한 내용은 [지원되는 명령 유형 목록](/rest/api/maps/route/postroutedirections#routeinstructionstype)을 참조하세요.

명령을 요청하면 응답은 `guidance`라는 새 요소를 반환합니다. `guidance` 요소는 턴바이턴 안내와 요약된 명령이라는 두 가지 정보를 포함합니다.

![명령 유형](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions` 요소는 이동에 대한 턴바이턴 안내를 포함하고 `instructionGroups`는 요약된 명령을 포함합니다. 각 명령 요약은 여러 도로에 적용될 수 있는 이동 세그먼트를 포함합니다. 이 API는 교통 혼잡에 대한 좌표 범위 또는 현재 차량 속도 등 경로의 섹션에 대한 세부 정보를 반환할 수 있습니다.

![턴바이턴 명령](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![요약된 명령](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>상업용 차량에 대한 경로 요청

Azure Maps 라우팅 API는 상업용 트럭 라우팅을 포함한 상업용 차량 라우팅을 지원합니다. 이 API는 차량의 높이와 무게 및 차량이 위험한 화물을 운반하는지 여부 등 지정된 제한 사항을 고려합니다. 예를 들어 차량이 인화성 물질을 운반하는 경우 라우팅 엔진은 주택 지역 근처에 있는 특정 터널을 피합니다.

### <a name="sample-query"></a>샘플 쿼리

아래 샘플 요청은 상업용 트럭에 대한 경로를 쿼리합니다. 트럭이 1등급 유해 폐기물을 운반합니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

경로 API가 트럭의 크기 및 유해 폐기물을 수용하는 길 찾기를 반환합니다. `guidance` 요소를 확장하여 경로 지침을 읽을 수 있습니다.

![1등급 유해 폐기물을 운반하는 트럭](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>샘플 쿼리

위의 쿼리에서 미국 유해 물질 등급을 변경하면 이러한 변경 내용을 수용할 다른 경로가 생성됩니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

아래 응답은 9등급 유해 물질을 운반하는 트럭에 대한 것으로, 1등급 유해 물질보다 위험 수준이 낮습니다. `guidance` 요소를 확장하여 지침을 읽으면 지침이 동일하지 않다는 것을 알 수 있습니다. 1등급 유해 물질을 운반하는 트럭에 대한 경로 지침이 더 많이 있습니다.



![9등급 유해 물질을 운반하는 트럭](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>경로를 따라 교통 정보 요청

Azure Maps 경로 방향 API를 사용하면 개발자가 요청에 `sectionType` 매개 변수를 포함하여 각 섹션 유형에 대한 세부 정보를 요청할 수 있습니다. 예를 들어 각 교통 혼잡 세그먼트에 대한 속도 정보를 요청할 수 있습니다. 요청할 수 있는 다양한 세부 정보를 알아보려면 [list of values for the sectionType key(sectionType 키에 대한 값 목록)](/rest/api/maps/route/getroutedirections#sectiontype)를 참조하세요.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 `sectionType`을 `traffic`으로 설정합니다. 이 쿼리는 시애틀부터 샌디에고까지의 교통 정보가 포함된 섹션을 요청합니다.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

응답에는 지정된 좌표를 따라 교통에 적합한 섹션이 포함되어 있습니다.

![교통 섹션](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

이 옵션은 아래 이미지와 같이 지도를 렌더링할 때 섹션에 색을 지정하는 데 사용할 수 있습니다. 

![지도에 렌더링된 색이 있는 섹션](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>다중 경유 경로 계산 및 최적화

Azure Maps는 현재 다음 두 가지 형태의 경로 최적화를 제공합니다.

* 중간 지점의 순서를 변경하지 않고 요청된 경로 유형을 기반으로 최적화. [여기서 지원되는 경로 유형](/rest/api/maps/route/postroutedirections#routetype)을 찾을 수 있습니다.

* 각 경유지를 방문할 최적의 순서를 얻기 위해 중간 지점의 순서를 변경하는 여행하는 외판원 최적화

다중 경유 라우팅의 경우 단일 경로 요청에 최대 150개의 중간 지점을 지정할 수 있습니다. 왕복의 경우와 마찬가지로 시작 및 끝 좌표 위치는 동일할 수 있습니다. 그러나 경로를 계산하려면 적어도 하나의 추가 중간 지점을 제공해야 합니다. 중간 지점은 출발지 좌표와 목적지 좌표 사이의 쿼리에 추가할 수 있습니다.

지정된 중간 지점을 방문하는 최적의 순서를 최적화하려면 **computeBestOrder=true** 를 지정해야 합니다. 이 시나리오를 여행하는 외판원 최적화 문제라고도 합니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 `computeBestOrder` 매개 변수를 `false`로 설정하여 6개의 중간 지점에 대한 경로를 요청합니다. 이는 `computeBestOrder` 매개 변수의 기본값이기도 합니다.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 경로 길이가 140,851미터이며 해당 경로를 이동하는 데 9,991초가 소요된다고 설명합니다.

![최적화되지 않은 응답](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

아래 이미지는 이 쿼리에서 생성된 결과 경로를 보여줍니다. 이 경로는 가능한 1개 경로입니다. 시간이나 거리를 기반으로 한 최적의 경로가 아닙니다.

![최적화되지 않은 이미지](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

이 경로의 중간 지점 순서는 0, 1, 2, 3, 4, 5, 6입니다.

### <a name="sample-query"></a>샘플 쿼리

다음 쿼리는 위의 샘플처럼 동일한 6개의 중간 지점에 대한 경로를 요청합니다. 이번에는 `computeBestOrder` 매개 변수를 `true`로 설정했습니다(여행하는 외판원 최적화).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

응답은 경로 길이가 91,814미터이며 해당 경로를 이동하는 데 7,797초가 소요된다고 설명합니다. API가 최적화된 경로를 반환했으므로 이동 거리와 이동 시간은 둘 다 여기가 더 낮습니다.

![최적화된 응답](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

아래 이미지는 이 쿼리에서 생성된 결과 경로를 보여줍니다.

![최적화된 이미지](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

최적 경로의 중간 지점 순서는 0, 5, 1, 2, 4, 3, 6입니다.

>[!TIP]
> 라우팅 서비스에서 최적화된 중간 지점 순서 정보는 인덱스 집합을 제공합니다. 이러한 인덱스는 출발지와 목적지 인덱스를 제외합니다. 출발지를 고려하려면 이러한 값을 1씩 늘려야 합니다. 그런 다음 목적지를 끝에 추가하여 전체 순서가 지정된 중간 지점 목록을 얻습니다.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>지원 지점을 사용하여 대체 경로 계산 및 보정

참조 경로에 대한 0개 이상의 대체 경로를 계산하기 위해 경로를 다시 구성하는 상황이 발생할 수 있습니다. 예를 들어 소매점을 통과하는 대체 경로를 고객에게 보여 줄 수 있습니다. 이 경우 지원 지점을 사용하여 위치를 보정해야 합니다. 위치를 보정하는 단계는 다음과 같습니다.

1. 경로를 있는 그대로 계산하고 경로 응답에서 경로 얻기
2. 경로를 사용하여 경로를 따라 또는 경로 주위에서 원하는 위치를 찾습니다. 예를 들어 Azure Maps [Point of Interest API(관심 지점 AP)I](/rest/api/maps/search/getsearchpoi)를 사용하거나 데이터베이스에서 자신의 데이터를 쿼리할 수 있습니다.  
3. 경로 시작부터의 거리를 기준으로 위치 순서를 정합니다.
4. 이러한 위치를 새 경로 요청의 지원 지점으로 [Post Route Directions API(사후 경로 방향 API)](/rest/api/maps/route/postroutedirections)에 추가합니다. 지원 지점에 대한 자세한 내용은 [Post Route Directions API documentation(사후 경로 방향 API 설명서)](/rest/api/maps/route/postroutedirections#supportingpoints)을 참조하세요. 

[Post Route Directions API(사후 경로 방향 API)](/rest/api/maps/route/postroutedirections)를 호출할 때 지원 지점과 함께 최소 편차 시간 또는 거리 제약 조건을 설정할 수 있습니다. 대체 경로를 제공하되 이동 시간도 제한하려는 경우에는 이러한 매개 변수를 사용합니다. 이러한 제약 조건을 사용하는 경우 대체 경로는 지정된 시간 또는 거리에 대해 출발 지점부터 참조 경로를 따릅니다. 즉, 다른 경로는 지정된 제약 조건에 따라 참조 경로에서 분기됩니다.

아래 이미지는 시간 및 거리에 대해 편차 제한이 지정된 대체 경로를 렌더링하는 예제입니다.

![대체 경로](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>웹앱에서 라우팅 서비스 사용

Azure Maps 웹 SDK는 [Service module(서비스 모듈)](/javascript/api/azure-maps-rest/)을 제공합니다. 이 모듈은 JavaScript 또는 TypeScript를 사용하여 웹 또는 Node.js 애플리케이션에서 Azure Maps REST API를 쉽게 사용할 수 있도록 지원하는 도우미 라이브러리입니다. 서비스 모듈을 사용하여 반환된 경로를 지도에 렌더링할 수 있습니다. 이 모듈은 GET 및 POST 요청과 함께 사용할 API를 자동으로 결정합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps Route Service](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [서비스 모듈을 사용하는 방법](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Show route on the map(지도에 경로 표시)](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM Package(Azure Maps NPM 패키지)](https://www.npmjs.com/package/azure-maps-rest  )
