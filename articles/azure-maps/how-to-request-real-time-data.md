---
title: Microsoft Azure Maps Mobility Services(미리 보기)를 사용하여 실시간 대중교통 데이터 요청
description: 정류장 도착과 같은 실시간 대중교통 데이터를 요청하는 방법을 알아봅니다. 이 목적을 위해 Azure Maps Mobility Services(미리 보기)를 사용하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 6e7358e76cdcf07f39a9b5a5fcab7ab0151ebc32
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529004"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Azure Maps Mobility Services(미리 보기)를 사용하여 실시간 대중교통 데이터 요청 

> [!IMPORTANT]
> Azure Maps Mobility Services 미리 보기는 사용 중지되었으며 2021년 10월 5일 이후에는 더 이상 사용할 수 없으며 지원되지 않습니다. 다른 모든 Azure Maps API 및 서비스는 이 만료 공지의 영향을 받지 않습니다.
> 자세한 내용은 [Azure Maps Mobility 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)를 참조하세요.


이 문서에서는 Azure Maps [Mobility Services](/rest/api/maps/mobility)를 사용하여 실시간 대중교통 데이터를 요청하는 방법을 보여 줍니다.

이 문서에서는 지정된 정류장에 도착하는 모든 노선에 대한 다음 실시간 도착 시간을 요청하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Maps 대중교통 API에 대한 호출을 수행하려면 먼저 Azure Maps 계정 및 구독 키가 있어야 합니다. Azure Maps 계정 구독을 만들려면 [계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)의 지침을 따릅니다. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 단계에 따라 계정에 대한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-real-time-arrivals-for-a-stop"></a>정류장에 대한 실시간 도착 시간 요청

특정 대중교통 정류장의 실시간 도착 데이터를 요청하려면 Azure Maps [Mobility Services(미리 보기)](/rest/api/maps/mobility)의 [실시간 도착 API](/rest/api/maps/mobility/getrealtimearrivalspreview)에 요청해야 합니다. 요청을 완료하려면 **metroID** 및 **stopid** 가 필요합니다. 이러한 매개 변수를 요청하는 방법에 대해 자세히 알아보려면 [대중교통 경로 요청](./how-to-request-transit-data.md) 방법에 대한 가이드를 참조하세요.

메트로 ID로 "522"을 사용하겠습니다. 이 ID는 "Seattle–Tacoma–Bellevue, WA" 영역에 대한 메트로 ID입니다. "522---2060603"을 정류장 ID로 사용합니다 .이 버스 정류장은 "Ne 24th St & 162nd Ave Ne, Bellevue WA"에 있습니다. 다음 5개의 실시간 도착 데이터를 요청하려면 이 정류장의 모든 다음 실시간 도착에 대해 다음 단계를 완료합니다.

1. Postman 앱을 엽니다. **새로 만들기** 를 선택하여 요청을 만듭니다. **새로 만들기** 창에서 **HTTP 요청** 을 선택합니다. 요청에 대한 **요청 이름** 을 입력합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력하여 GET 요청을 만듭니다. `{subscription-key}`를 Azure Maps 기본 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

3. 요청이 성공적으로 완료되면 다음 응답을 받게 됩니다.  'ScheduleType' 매개 변수는 예상 도착 시간이 실시간 데이터를 기준으로 하는지 또는 정적 데이터를 기준으로 하는지를 정의합니다.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

Mobility Services(미리 보기)를 사용하여 교통 데이터를 요청하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [교통 데이터를 요청하는 방법](how-to-request-transit-data.md)

Azure Maps Mobility Services(미리 보기) API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Services API 설명서](/rest/api/maps/mobility)