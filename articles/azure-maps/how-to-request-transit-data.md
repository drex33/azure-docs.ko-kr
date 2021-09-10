---
title: Microsoft Azure Maps Mobility Services(미리 보기)를 사용하여 대중교통 데이터 요청
description: Azure Maps Mobility services(미리 보기)를 사용하여 대도시 지역 ID, 대중교통 정류장, 경로, 경로 일정 같은 대중교통 데이터를 요청하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 1a3a2ea2843b9a21c67f886b49001ebaa17fc868
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529179"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Azure Maps Mobility Services(미리 보기)를 사용하여 대중교통 데이터 요청 

> [!IMPORTANT]
> Azure Maps Mobility Services 미리 보기는 사용 중지되었으며 2021년 10월 5일 이후에는 더 이상 사용할 수 없으며 지원되지 않습니다. 다른 모든 Azure Maps API 및 서비스는 이 만료 공지의 영향을 받지 않습니다.
> 자세한 내용은 [Azure Maps Mobility 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)를 참조하세요.


이 문서에서는 Azure Maps [Mobility Services](/rest/api/maps/mobility)를 사용하여 대중교통 데이터를 요청하는 방법을 보여 줍니다. 대중교통 데이터에는 대중교통 정류장, 경로 정보, 예상 이동 시간 등이 포함됩니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* [대도시 지역 가져오기 API](/rest/api/maps/mobility/getmetroareapreview)를 사용하여 대도시 지역 ID 가져오기
* [주변 대중교통 가져오기](/rest/api/maps/mobility/getnearbytransitpreview) 서비스를 사용하여 가까운 대중교통 정류장을 요청합니다.
* [대중교통 경로 가져오기 API](/rest/api/maps/mobility/gettransitroutepreview)를 쿼리하여 대중교통 이용 경로를 계획합니다.
* [대중교통 일정 가져오기 API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)를 사용하여 대중교통 경로 기하 구조와 상세 경로 일정을 요청합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="get-a-metro-area-id"></a>대도시 지역 ID 가져오기

특정 대도시 지역의 대중교통 기관 및 지원되는 대중교통 형식에 대한 세부 정보를 요청하려면 해당 지역의 `metroId`가 필요합니다. [대도시 지역 가져오기 API](/rest/api/maps/mobility/getmetroareapreview)를 사용하면 Azure Maps Mobility Services를 사용할 수 있는 대도시 지역을 요청할 수 있습니다. 응답에는 `metroId`, `metroName`, GeoJSON 형식으로 표시된 대도시 지역 기하 구조 등 세부 정보가 포함됩니다.

시애틀-터코마 대도시 지역 ID에 대한 대도시 지역을 가져오기 위한 요청을 수행하겠습니다. 대도시 지역의 ID를 요청하려면 다음 단계를 완료합니다.

1. Postman 앱을 엽니다. **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.
  
2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력하여 GET 요청을 만듭니다. `{subscription-key}`를 Azure Maps 기본 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

3. 요청이 성공적으로 완료되면 다음 응답을 받게 됩니다.

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>가까운 대중교통 정류장 요청

Azure Maps [주변 대중교통 가져오기](/rest/api/maps/mobility/getnearbytransitpreview) 서비스를 통해 대중교통 개체를 검색할 수 있습니다. API는 지정된 위치 주변의 대중교통 정류장, 공유 자전거 같은 대중교통 개체 세부 정보를 반환합니다. 다음으로 지정된 위치 주변 300미터 반경 내의 가까운 대중교통 정류장을 검색할 것을 서비스에 요청하겠습니다.

[주변 대중교통 가져오기](/rest/api/maps/mobility/getnearbytransitpreview)에 요청을 하려면 다음 단계를 수행합니다.

1. Postman 앱을 엽니다. **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 다음과 같이 입력한 뒤 **보내기** 를 클릭합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 요청이 성공적으로 완료되면 응답 구조는 아래와 같이 표시됩니다.

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

응답 구조를 자세히 관찰하면 각 대중교통 개체에 대한 매개 변수가 포함된 것을 알 수 있습니다. 각 대중교통 개체에는 `id`, `type`,`stopName`, `mainTransitType`, `mainAgencyName`, 개체의 위치(좌표) 등과 같은 매개 변수가 있습니다.

학습을 위해 버스 정류장의 `id`를 다음 섹션에 있는 경로의 원본으로 사용하겠습니다.  

## <a name="request-a-transit-route"></a>대중교통 경로 요청

Azure Maps [대중교통 경로 가져오기 API](/rest/api/maps/mobility/gettransitroutepreview)를 통해 이동을 계획할 수 있습니다. 이 서비스는 원본에서 대상으로 가장 적합한 경로 옵션을 반환합니다. 도보, 자전거, 대중교통 등 다양한 이동 모드가 제공됩니다. 다음으로 시애틀의 스페이스 니들 타워와 가장 가까운 버스 정류장에서 출발하는 경로를 검색합니다.

### <a name="get-location-coordinates-for-destination"></a>대상의 위치 좌표 가져오기

스페이스 니들 타워의 위치 좌표를 가져오기 위해 Azure Maps [유사 항목 검색 서비스](/rest/api/maps/search/getsearchfuzzy)를 사용하겠습니다.

유사 항목 검색 서비스로 요청을 하려면 다음 단계를 수행합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 요청 URL을 다음과 같이 입력한 뒤 **보내기** 를 클릭합니다.

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. 응답을 자세히 살펴보면 스페이스 니들 검색 결과에 여러 위치가 포함되어 있습니다. 결과마다 **position** 아래에 위치 좌표가 있습니다. 첫 번째 결과의 **position** 아래에서 `lat`과 `lon`을 복사합니다.

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>경로 요청

경로를 요청하려면 다음 단계를 완료합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 다음과 같이 입력한 뒤 **보내기** 를 클릭합니다.

    `modeType` 및 `transitType` 매개 변수를 지정하여 버스의 대중교통 경로를 요청하겠습니다. 요청 URL에는 이전 섹션에서 검색한 위치가 포함되어 있습니다. `originType`의 경우, **stopId** 가 있습니다. `destionationType`의 경우에는 **position** 이 있습니다.

    [대중교통 경로 가져오기 API](/rest/api/maps/mobility/gettransitroutepreview)에 대한 요청에서 사용할 수 있는 [URI 매개 변수 목록](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters)을 참조하세요.
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 요청이 성공적으로 완료되면 응답 구조는 아래와 같이 표시됩니다.

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. 자세히 살펴보면 응답에 여러 개의 **버스** 경로가 있습니다. 각 경로에는 고유한 **일정 ID**, 경로의 각 구간을 설명하는 요약 및 버스표의 항목별 가격과 총 가격을 모두 제공하는 `itineraryFare`가 있습니다. 경로 구간이란 두 개의 정류장 중간 지점 사이에 있는 경로의 일부입니다. 그런 다음 응답에서 `itineraryId`를 사용하여 가장 빠른 경로에 대한 세부 정보를 요청해 보겠습니다.

## <a name="request-fastest-route-itinerary"></a>최단 경로 일정 요청

Azure Maps [대중교통 일정 가져오기](/rest/api/maps/mobility/gettransititinerarypreview) 서비스를 사용하면 [대중교통 경로 가져오기 API](/rest/api/maps/mobility/gettransitroutepreview) 서비스에서 반환된 경로의 **일정 ID** 를 사용하여 특정 경로에 대한 데이터를 요청할 수 있습니다. 경로를 요청하려면 다음 단계를 완료합니다.

1. Postman 앱에서 **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택합니다. API 엔드포인트에 대한 요청 URL을 다음과 같이 입력하고 **보내기** 를 클릭합니다.

    `detailType` 매개 변수를 **기하 구조** 로 설정하여 응답에 대중교통 정류장 정보와 경로의 도보 및 자전거 구간에 대한 턴바이턴 내비게이션이 포함되도록 하겠습니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. 요청이 성공적으로 완료되면 응답 구조는 아래와 같이 표시됩니다.  JSON 응답을 관찰하면 각 버스 구간마다 `legfare` 요소가 포함되어 있는 것을 확인할 수 있습니다. `legfare` 요소에는 별도 구매한 각 버스 경로별 요금(센트 단위)이 포함되어 있습니다. 응답의 끝에는 전체 경로의 요금(센트 단위)이 포함된 `itineraryFare` 요소가 있습니다. 이 예제에는 각 `$2.75`인 네 개의 버스 경로가 있습니다. 그러나 전체 경로에 대해 표를 하나면 구매한다면 비용은 `$5.50`입니다.

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>다음 단계

Mobility Services(미리 보기)를 사용하여 실시간 데이터를 요청하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청하는 방법](how-to-request-real-time-data.md)

Azure Maps Mobility Services(미리 보기) API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Service 설명서](/rest/api/maps/mobility)