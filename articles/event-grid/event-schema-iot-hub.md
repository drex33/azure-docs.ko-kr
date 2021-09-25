---
title: Event Grid 원본으로서의 Azure IoT Hub
description: 이 아티클에서는 Azure IoT Hub 이벤트에 대한 속성 및 스키마를 제공합니다. 사용 가능한 이벤트 유형, 예제 이벤트 및 이벤트 속성을 나열합니다.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: ac3cac72cc9998d4fb78ed0459e5e07df1125df0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606326"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Event Grid 원본으로서의 Azure IoT Hub
이 아티클에서는 Azure IoT Hub 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure IoT Hub는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | IoT 허브에 디바이스를 등록하는 경우 게시합니다. |
| Microsoft.Devices.DeviceDeleted | IoT 허브에서 디바이스를 삭제하는 경우 게시합니다. | 
| Microsoft.Devices.DeviceConnected | IoT Hub에 디바이스가 연결되는 경우 게시합니다. |
| Microsoft.Devices.DeviceDisconnected | IoT Hub와 디바이스의 연결이 해제되는 경우 게시합니다. | 
| Microsoft.Devices.DeviceTelemetry | IoT 허브로 원격 분석 메시지를 보내는 경우 게시합니다. |

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

DeviceConnected 및 DeviceDisconnected 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 연결될 때 발생하는 이벤트의 스키마를 보여 줍니다.

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
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry 이벤트는 IoT Hub로 원격 분석 이벤트가 전송될 때 발생합니다. 해당 이벤트에 대한 샘플 스키마는 다음과 같습니다.

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

DeviceCreated 및 DeviceDeleted 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 등록될 때 발생하는 이벤트의 스키마를 보여줍니다.

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
      "deviceEtag": "null",
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

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

DeviceConnected 및 DeviceDisconnected 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 연결될 때 발생하는 이벤트의 스키마를 보여 줍니다.

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

DeviceTelemetry 이벤트는 IoT Hub로 원격 분석 이벤트가 전송될 때 발생합니다. 해당 이벤트에 대한 샘플 스키마는 다음과 같습니다.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
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
  "specversion": "1.0"
}]
```

DeviceCreated 및 DeviceDeleted 이벤트에 대한 스키마는 구조가 동일합니다. 이 샘플 이벤트는 디바이스가 IoT 허브에 등록될 때 발생하는 이벤트의 스키마를 보여줍니다.

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
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
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

모든 이벤트에는 동일한 최상위 수준 데이터가 포함됩니다. 

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `data` | object | IoT Hub 이벤트 데이터입니다.  |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

모든 이벤트에는 동일한 최상위 수준 데이터가 포함됩니다. 


| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `data` | object | IoT Hub 이벤트 데이터입니다.  |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

모든 IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `hubName` | 문자열 | 디바이스가 만들어지거나 삭제된 IoT Hub의 이름입니다. |
| `deviceId` | 문자열 | 디바이스의 고유 식별자입니다. 이 대/소문자 구분 문자열은 최대 128자까지 가능하며, ASCII 7 비트 영숫자 문자 + 다음 특수 문자 `- : . + % _ # * ? ! ( ) , = @ ; $ '`을 지원합니다. |

데이터 개체의 내용은 각 이벤트 게시자에 따라 다릅니다. 

**Device Connected** 및 **Device Disconnected** IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `moduleId` | 문자열 | 모듈의 고유 식별자입니다. 이 필드는 모듈 디바이스에 대해서만 출력됩니다. 이 대/소문자 구분 문자열은 최대 128자까지 가능하며, ASCII 7 비트 영숫자 문자 + 다음 특수 문자 `- : . + % _ # * ? ! ( ) , = @ ; $ '`을 지원합니다. |
| `deviceConnectionStateEventInfo` | object | 디바이스 연결 상태 이벤트 정보
| `sequenceNumber` | 문자열 | 연결된 디바이스 또는 디바이스 분리 이벤트의 순서를 나타내는 데 도움이 되는 숫자입니다. 최신 이벤트는 이전 이벤트보다 시퀀스 번호가 높습니다. 이 숫자는 1을 초과하여 변경될 수 있지만 엄격하게 증가합니다. [시퀀스 번호 사용 방법](../iot-hub/iot-hub-how-to-order-connection-state-events.md)을 참조하세요. |

**디바이스 원격 분석** IoT Hub 이벤트의 경우, 데이터 개체는 [IoT Hub 메시지 형식](../iot-hub/iot-hub-devguide-messages-construct.md)의 Device-to-Cloud 메시지를 포함하며, 다음과 같은 속성을 가집니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `body` | 문자열 | 디바이스의 메시지 내용입니다. |
| `properties` | 문자열 | 애플리케이션 속성은 메시지에 추가할 수 있는 사용자 정의 문자열입니다. 이러한 필드는 선택 사항입니다. |
| `system properties` | 문자열 | [시스템 속성](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)을 사용하면 메시지의 콘텐츠 및 소스를 식별할 수 있습니다. 디바이스 원격 분석 메시지는 메시지 시스템 속성에서 contentType이 JSON으로 설정되고 contentEncoding은 UTF-8로 설정된 유효한 JSON 형식이어야 합니다. 해당 설정을 지정하지 않으면 IoT Hub는 Base 64 인코딩 형식으로 메시지를 작성합니다.  |

**디바이스가 생성됨** 및 **디바이스가 삭제됨** IoT Hub 이벤트의 경우 데이터 개체에 다음 속성이 포함됩니다.

| 속성 | 유형 | 설명 |
| -------- | ---- | ----------- |
| `twin` | object | 애플리케이션 디바이스 메타데이터의 클라우드 표현인 디바이스 쌍에 관한 정보입니다. | 
| `deviceID` | 문자열 | 디바이스 쌍의 고유 식별자입니다. | 
| `etag` | 문자열 | 디바이스 쌍에 대한 업데이트의 일관성을 확인하는 유효성 검사기입니다. 각 etag은 디바이스 쌍마다 고유합니다. |  
| `deviceEtag` | 문자열 | 디바이스 레지스트리에 대한 업데이트의 일관성을 확인하는 유효성 검사기입니다. 각 deviceEtag는 디바이스 레지스트리마다 고유해야 합니다. |
| `status` | 문자열 | 디바이스 쌍의 활성화 여부를 나타냅니다. | 
| `statusUpdateTime` | 문자열 | 마지막 디바이스 쌍 상태 업데이트의 ISO8601 타임 스탬프입니다. |
| `connectionState` | 문자열 | 디바이스의 연결 여부를 나타냅니다. | 
| `lastActivityTime` | 문자열 | 마지막 활동의 ISO8601 타임스탬프입니다. | 
| `cloudToDeviceMessageCount` | 정수 | 이 디바이스에 전송된 클라우드-디바이스 메시지 횟수입니다. | 
| `authenticationType` | 문자열 | 이 디바이스에 사용되는 인증 유형은 `SAS`, `SelfSigned` 또는 `CertificateAuthority`입니다. |
| `x509Thumbprint` | 문자열 | 지문은 x509 인증서에 대한 고유값으로, 인증서 저장소에서 특정 인증서를 찾는 데 주로 사용됩니다. 지문은 SHA1 알고리즘을 사용하여 동적으로 생성되며, 인증서에 실제로 존재하지는 않습니다. | 
| `primaryThumbprint` | 문자열 | x509 인증서에 대한 주된 지문입니다. |
| `secondaryThumbprint` | 문자열 | x509 인증서에 대한 보조 지문입니다. | 
| `version` | 정수 | 디바이스 쌍이 업데이트될 때마다 하나씩 증가하는 정수입니다. |
| `desired` | object | 애플리케이션 백 엔드에서만 작성할 수 있고 디바이스에서 읽을 수 있는 속성의 일부입니다. | 
| `reported` | object | 디바이스에서만 작성할 수 있고 애플리케이션 백 엔드에서 읽을 수 있는 속성의 일부입니다. |
| `lastUpdated` | 문자열 | 마지막 디바이스 쌍 속성 업데이트의 ISO8601 타임 스탬프입니다. | 

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |설명  |
|---------|---------|
| [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](publish-iot-hub-events-to-logic-apps.md) | 논리 앱은 사용자의 IoT Hub에 디바이스가 추가될 때마다 알림 이메일을 보냅니다. |
| [작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md) | Event Grid와 IoT Hub 통합의 개요입니다. |
| [디바이스 연결 및 디바이스 연결 해제 이벤트 정렬](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | 디바이스 연결 상태 이벤트 정렬하는 방법을 보여 줍니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* IoT Hub 및 Event Grid와 함께 작동하는 방법에 대해 알아보려면 [동작을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md)을 참조하세요.