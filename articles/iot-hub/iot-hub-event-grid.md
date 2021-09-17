---
title: Azure IoT Hub 및 Azure Event Grid | Microsoft Docs
description: IoT Hub에서 발생하는 작업을 기반으로 프로세스를 트리거하려면 Azure Event Grid를 사용합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: f8d1d3cf8553c768bb2bee015be2f5995214fe62
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478719"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응

Azure Maps 이벤트 알림을 다른 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure IoT Hub가 Azure Event Grid와 통합됩니다. 안정적이고 확장 가능하고 안전한 방식으로 중요 이벤트에 대응할 수 있도록 비즈니스 애플리케이션에서 IoT Hub 이벤트를 수신하도록 구성합니다. 예를 들어 데이터베이스를 업데이트하고, 작업 티켓을 만들고, 새 IoT 디바이스가 IoT 허브에 등록될 때마다 이메일 알림을 전달하는 애플리케이션을 빌드합니다.

[Azure Event Grid](../event-grid/overview.md)는 게시-구독 모델을 사용하여 완전히 관리되는 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

![Azure Event Grid 아키텍처](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>국가별 가용성

Event Grid가지 원되는 지역의 IoT Hub에 대해 Event Grid 통합을 사용할 수 있습니다. 최신 지역 목록을 보려면 [Azure Event Grid 소개](../event-grid/overview.md)를 참조하세요.

## <a name="event-types"></a>일정 유형

IoT Hub는 다음과 같은 이벤트 유형을 게시합니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 디바이스를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 디바이스를 삭제하는 경우 게시합니다. |
| Microsoft.Devices.DeviceConnected | IoT Hub에 디바이스가 연결되는 경우 게시합니다. |
| Microsoft.Devices.DeviceDisconnected | IoT Hub와 디바이스의 연결이 해제되는 경우 게시합니다. |
| Microsoft.Devices.DeviceTelemetry | 디바이스 원격 분석 메시지를 IoT 허브로 보낼 때 게시됩니다. |

Azure Portal 또는 Azure 명령줄 인터페이스를 사용하여 각 IoT 허브에서 어떤 이벤트를 게시할지 구성할 수 있습니다. 한 예로 자습서 [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](../event-grid/publish-iot-hub-events-to-logic-apps.md)를 시도해 봅니다.

## <a name="event-schema"></a>이벤트 스키마

IoT Hub 이벤트에는 디바이스 수명 주기 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. eventType 속성이 **Microsoft.Devices** 로 시작하는지 확인하여 IoT Hub 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성을 사용하는 방법에 대한 자세한 내용은 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.

### <a name="device-connected-schema"></a>디바이스 연결됨 스키마

다음 예제는 디바이스 연결됨 이벤트의 스키마를 보여 줍니다.

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```



### <a name="device-telemetry-schema"></a>디바이스 원격 분석 스키마

디바이스 원격 분석 메시지는 메시지 [시스템 속성](iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType이 **application/json** 으로 설정되고 contentEncoding은 **UTF-8** 로 설정된 유효한 JSON 형식이어야 합니다. 두 속성은 모두 대/소문자를 구분하지 않습니다. 콘텐츠 인코딩을 지정하지 않으면 IoT Hub는 base 64로 인코드된 형식으로 메시지를 작성합니다.

엔드포인트를 Event Grid로 선택하여 Event Grid에 게시되기 전에 디바이스 원격 분석 이벤트를 보강할 수 있습니다. 자세한 내용은 [메시지 보강 개요](iot-hub-message-enrichments-overview.md)를 참조하세요.

다음 예제에서는 디바이스 원격 분석 이벤트의 스키마를 보여 줍니다.

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>스키마를 생성한 디바이스

다음 예제는 디바이스가 생성된 이벤트의 스키마를 보여줍니다.

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```


> [!WARNING]
> 디바이스 만들기 이벤트와 연결된 ‘트윈 데이터’는 기본 구성 이며, 새로 만든 디바이스의 실제 `authenticationType` 및 기타 디바이스 속성에 사용해서는 ‘안 됩니다’.  새로 만든 디바이스의 `authenticationType` 및 기타 디바이스 속성에는 Azure IoT SDK에 제공된 등록 관리자 API를 사용합니다.

각 속성에 대한 자세한 설명은 [IoT Hub에 대한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-iot-hub.md)를 참조하세요.

## <a name="filter-events"></a>이벤트 필터링

IoT Hub 이벤트 구독은 이벤트 유형, 데이터 콘텐츠, 주체(디바이스 이름)를 기준으로 이벤트를 필터링할 수 있습니다.

Event Grid를 사용하면 이벤트 유형, 주체, 데이터 콘텐츠를 [필터링](../event-grid/event-filtering.md)할 수 있습니다. Event Grid 구독을 만드는 동안 선택한 IoT 이벤트를 구독하도록 선택할 수 있습니다. Event Grid의 제목 필터는 **시작 문자**(접두사) 및 **끝 문자**(접미사) 일치를 기반으로 작동합니다. 필터는 `AND` 연산자를 사용하므로 접두사와 접미사가 둘 다 일치하는 제목의 이벤트가 구독자에게 전달됩니다.

IoT 이벤트의 제목은 다음 형식을 사용합니다.

```json
devices/{deviceId}
```

Event Grid를 사용하여 데이터 콘텐츠를 비롯한 각 이벤트의 특성을 필터링할 수도 있습니다. 이렇게 하면 원격 분석 메시지의 내용에 따라 전달되는 이벤트를 선택할 수 있습니다. 예제를 보려면 [고급 필터링](../event-grid/event-filtering.md#advanced-filtering)을 참조하세요. 원격 분석 메시지 본문을 필터링하려면 메시지 [시스템 속성](./iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType을 **application/json** 으로 설정하고 contentEncoding을 **UTF-8** 로 설정해야 합니다. 두 속성은 모두 대/소문자를 구분하지 않습니다.

DeviceConnected, DeviceDisconnected, DeviceCreated, DeviceDeleted와 같은 비원격 분석 이벤트의 경우 구독을 만들 때 Event Grid 필터링을 사용할 수 있습니다. 원격 분석 이벤트의 경우 사용자는 Event Grid 필터링 외에도 메시지 라우팅 쿼리를 통해 디바이스 쌍, 메시지 속성, 본문을 필터링할 수도 있습니다. 

Event Grid를 통해 원격 분석 이벤트를 구독하면 IoT Hub에서 기본 메시지 경로를 만들어 데이터 원본 형식 디바이스 메시지를 Event Grid로 보냅니다. 메시지 라우팅에 대한 자세한 내용은 [IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 참조하세요. 이 경로는 포털의 IoT Hub > 메시지 라우팅에서 볼 수 있습니다. 원격 분석 이벤트에 대해 생성된 EG 구독 수에 관계없이 Event Grid 경로는 한 개만 생성됩니다. 따라서 각기 다른 필터를 사용하는 여러 구독이 필요한 경우 동일한 경로에 대한 해당 쿼리에서 OR 연산자를 사용할 수 있습니다. 경로 생성 및 삭제는 Event Grid를 통해 원격 분석 이벤트를 구독하여 제어합니다. IoT Hub 메시지 라우팅을 사용하여 Event Grid 경로를 만들거나 삭제할 수는 없습니다.

원격 분석 데이터를 보내기 전에 메시지를 필터링하려면 [라우팅 쿼리](iot-hub-devguide-routing-query-syntax.md)를 업데이트합니다. 본문이 JSON인 경우에만 메시지 본문에 라우팅 쿼리를 적용할 수 있습니다. 또한 메시지 [시스템 속성](./iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType을 **application/json** 으로 설정하고 contentEncoding을 **UTF-8** 로 설정해야 합니다.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>디바이스 연결됨 및 디바이스 연결 끊김 이벤트에 대한 제한

디바이스 연결 상태 이벤트를 수신하려면 디바이스가 IoT Hub를 사용하여 *디바이스-클라우드 전송 원격 분석* 또는 *클라우드-디바이스 수신 메시지* 작업을 호출해야 합니다. 그러나 디바이스가 AMQP 프로토콜을 사용하여 IoT Hub에 연결하는 경우 디바이스가 *클라우드-디바이스 수신 메시지* 작업을 호출하는 것이 좋습니다. 그러지 않으면 연결 상태 알림이 몇 분 지연될 수 있습니다. 디바이스가 MQTT 프로토콜로 연결되는 경우 IoT Hub는 클라우드-디바이스 링크를 열린 상태로 유지합니다. AMQP에 대한 클라우드-디바이스 링크를 열려면 [수신 비동기 API](/rest/api/iothub/device/receivedeviceboundnotification)를 호출합니다.

디바이스-클라우드 링크는 디바이스에서 원격 분석을 전송하는 동안 열린 상태로 유지됩니다.

디바이스가 자주 연결되고 연결이 끊어지는 경우 IoT Hub 모든 단일 연결 상태를 보내지 않지만 60초의 주기적인 스냅샷에서 수행된 현재 연결 상태를 게시합니다. 동일한 연결 상태 이벤트를 여러 시퀀스 번호로 수신하거나 여러 연결 상태 이벤트를 수신하는 경우 디바이스 연결 상태가 변경된 것을 의미합니다.

## <a name="tips-for-consuming-events"></a>이벤트 사용 팁

IoT Hub 이벤트를 처리하는 애플리케이션은 다음과 같은 권장 지침을 따라야 합니다.

* 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 따라서 이벤트가 특정 원본에서 제공되었다고 가정하지 마세요. 항상 메시지 항목을 확인하여 예상한 IoT Hub에서 제공된 것인지 확인합니다.

* 수신하는 모든 이벤트가 예상한 유형일 것이라고 가정하지 마세요. 메시지를 처리하기 전에 eventType을 항상 확인합니다.

* 메시지가 잘못된 순서로 도착하거나 지연 후에 도착할 수 있습니다. etag 필드를 사용하여 디바이스 생성됨 또는 디바이스 삭제됨 이벤트의 개체 정보가 최신 상태인지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [IoT Hub 이벤트 자습서를 시도해 봅니다.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [디바이스 연결 및 연결 해제 이벤트를 정렬하는 방법](iot-hub-how-to-order-connection-state-events.md)에 대해 알아봅니다.

* [Event Hubs에 대한 자세한 정보](../event-grid/overview.md)

* [IoT Hub 이벤트 라우팅 및 메시지 간의 차이점 비교](iot-hub-event-grid-routing-comparison.md)

* [IoT 원격 분석 이벤트를 사용하여 Azure Maps를 통해 IoT 공간 분석을 구현하는 방법에 대한 자세한 정보](../azure-maps/tutorial-iot-hub-maps.md)
