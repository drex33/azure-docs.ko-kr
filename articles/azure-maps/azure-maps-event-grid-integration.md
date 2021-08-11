---
title: Event Grid를 사용하여 Azure Maps 이벤트에 대응
description: 지오펜스와 관련된 Azure Maps 이벤트에 대응하는 방법을 알아봅니다. 이벤트를 수신 대기하는 방법 및 Event Grid를 사용하여 이벤트를 이벤트 처리기로 라우팅하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92889769"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid를 사용하여 Azure Maps 이벤트에 대응

Azure Maps는 사용자가 이벤트 알림을 기타 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합됩니다. 이 문서의 목적은 Azure Maps 이벤트를 수신 대기하도록 비즈니스 애플리케이션을 구성하는 데 도움이 되는 것입니다. 이를 통해 안정적이고, 확장성 있으며, 안전한 방식으로 위험 이벤트에 빠르게 대응할 수 있습니다. 예를 들어, 사용자는 디바이스가 지오펜스에 들어갈 때마다 데이터베이스를 업데이트하고, 티켓을 생성하고, 이메일 알림을 전달하는 애플리케이션을 빌드할 수 있습니다.

Azure Event Grid는 게시-구독 모델을 사용하는 완전 관리형 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../azure-functions/functions-overview.md)와 같은 Azure 서비스에 대한 지원이 기본적으로 제공됩니다. 웹후크를 사용하여 비 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.


![Azure Event Grid 기능 모델](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 이벤트 형식

Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure Maps 계정은 다음과 같은 이벤트 유형을 내보냅니다. 

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 수신된 좌표가 지정된 지오펜스 외부에서 내부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceExited | 수신된 좌표가 지정된 지오펜스 내부에서 외부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceResult | 상태와 관계없이 지오펜싱 쿼리에서 결과가 반환될 때마다 발생합니다. |

## <a name="event-schema"></a>이벤트 스키마

다음 예제에서는 GeofenceResult의 스키마를 보여줍니다.

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>이벤트 사용 팁

Azure Maps 지오펜스 이벤트를 처리하는 애플리케이션은 다음과 같은 몇 가지 권장 지침을 따라야 합니다.

* 여러 구독을 구성하여 이벤트를 동일한 이벤트 처리기로 라우팅합니다. 이벤트가 특정 원본에서 제공되었다고 가정하지 않는 것이 중요합니다. 항상 메시지 토픽을 확인하여 예상한 원본에서 보낸 메시지인지 확인합니다.
* 응답 헤더의 `X-Correlation-id` 필드를 사용하여 개체 정보가 최신 상태인지 파악합니다. 메시지가 잘못된 순서로 도착하거나 지연 후에 도착할 수 있습니다.
* 모드 매개 변수를 `EnterAndExit`로 설정하여 지오펜스 API에서 GET이나 POST 요청을 호출하는 경우, 상태가 이전 지오펜스 API 호출과 다르게 변경된 지오펜스의 각 기하 도형에 대해 Enter 또는 Exit 이벤트가 생성됩니다.

## <a name="next-steps"></a>다음 단계

지오펜싱을 사용하여 공사 현장의 작업을 제어하는 방법을 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"] 
> [Azure Maps를 사용하여 지오펜스 설정](tutorial-geofence.md)