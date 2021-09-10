---
title: 이벤트 알림
titleSuffix: Azure Digital Twins
description: 다양한 이벤트 유형 및 다양한 알림 메시지를 해석하는 방법을 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: d008888968f05641786cdfcb73afac1d540b7596
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769870"
---
# <a name="event-notifications"></a>이벤트 알림

Azure Digital Twins의 다양한 이벤트는 여러 가지 작업이 수행될 때 솔루션 백 엔드가 이를 인식할 수 있도록 하는 **알림** 을 생성합니다. 이 알림은 작업을 수행하는 데 이 정보를 사용할 수 있는 Azure Digital Twins 내부 및 외부의 여러 위치로 [라우팅됩니다](concepts-route-events.md).

생성할 수 있는 여러 유형의 알림이 있으며 알림 메시지는 생성된 이벤트 유형에 따라 다르게 보일 수 있습니다. 이 문서에서는 다양한 유형의 메시지에 관한 세부 정보와 표시되는 모양을 제공합니다.

이 차트에서는 다음과 같은 다양한 알림 유형을 보여 줍니다.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>알림 구조

일반적으로 알림은 헤더 및 본문의 두 부분으로 구성됩니다. 

### <a name="event-notification-headers"></a>이벤트 알림 헤더

알림 메시지 헤더는 키-값 쌍으로 표시됩니다. 사용된 프로토콜(MQTT, AMQP 또는 HTTP)에 따라 메시지 헤더가 다르게 직렬화됩니다. 이 섹션에서는 선택된 특정 프로토콜 및 serialization에 관계없이 알림 메시지의 일반 헤더 정보를 설명합니다.

일부 알림은 [CloudEvents](https://cloudevents.io/) 표준을 따릅니다. CloudEvents 규칙은 다음과 같습니다.
* 디바이스에서 내보낸 알림은 알림의 기존 사양을 계속 따릅니다.
* IoT Hub에서 처리하고 내보내는 알림은 IoT Hub에서 Event Grid 등을 통해 CloudEvents를 지원하기 위해 선택하는 경우를 제외하고는 알림의 기존 사양을 계속 따릅니다.
* [모델](concepts-models.md)을 사용하여 [Digital Twins](concepts-twins-graph.md)에서 내보낸 알림은 CloudEvents를 따릅니다.
* Azure Digital Twins에서 처리하고 내보내는 알림은 CloudEvents를 따릅니다.

서비스는 순서를 표시하기 위해 모든 알림에 시퀀스 번호를 추가하거나 다른 방식으로 고유한 순서를 유지 관리해야 합니다. 

Azure Digital Twins에서 Event Grid로 내보내는 알림은 Event Grid 항목에 정의된 스키마 유형에 따라 자동으로 CloudEvents 스키마 또는 EventGridEvent 스키마로 형식이 지정됩니다. 

헤더의 확장 특성은 페이로드의 Event Grid 스키마에 대한 속성으로 추가됩니다. 

### <a name="event-notification-bodies"></a>이벤트 알림 본문

알림 메시지 본문은 여기서 JSON으로 설명됩니다. 메시지 본문에 대해 원하는 serialization 형식(예: JSON, CBOR, Protobuf 등)에 따라 메시지 본문이 다르게 직렬화될 수 있습니다.

본문에 포함된 필드 세트는 알림 유형에 따라 달라집니다.

다음 섹션에서는 IoT Hub 및 Azure Digital Twins(또는 기타 Azure IoT 서비스)에서 내보내는 다양한 유형의 알림에 관해 자세히 살펴봅니다. 각 알림 유형을 트리거하는 항목과 각 유형의 알림 본문에 포함되는 필드 세트에 관해 알아봅니다.

## <a name="digital-twin-change-notifications"></a>디지털 트윈 변경 알림

**디지털 트윈 변경 알림** 은 다음과 같이 디지털 트윈이 업데이트되는 경우 생성됩니다.
* 속성 값 또는 메타데이터가 변경되는 경우
* 디지털 트윈 또는 구성 요소 메타데이터가 변경되는 경우 이 시나리오의 예제에서는 디지털 트윈의 모델을 변경합니다.

### <a name="properties"></a>속성

디지털 트윈 변경 알림 본문의 필드는 다음과 같습니다.

| Name    | 값 |
| --- | --- |
| `id` | 서비스에서 유지 관리되는 카운터 또는 UUID와 같은 알림의 식별자. `source` + `id`는 각 고유 이벤트에 대해 고유합니다. |
| `source` | IoT 허브 또는 Azure Digital Twins 인스턴스의 이름(예: *myhub.azure-devices.net* 또는 *mydigitaltwins.westus2.azuredigitaltwins.net*) |
| `data` | 트윈에 적용되는 업데이트를 설명하는 JSON 패치 문서. 자세한 내용은 아래 [본문 세부 정보](#body-details)를 참조하세요. |
| `specversion` | *1.0*<br>메시지는 이 버전의 [CloudEvents 사양](https://github.com/cloudevents/spec)을 따릅니다. |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | 디지털 트윈 ID |
| `time` | 디지털 트윈에서 작업이 수행된 시간의 타임스탬프 |
| `traceparent` | 이벤트의 W3C 추적 컨텍스트 |

### <a name="body-details"></a>본문 세부 정보

메시지 내에서 `data` 필드는 디지털 트윈 업데이트가 들어 있는 JSON 패치 문서를 포함합니다.

예를 들어, 디지털 트윈이 다음 패치를 사용하여 업데이트되었다고 가정합니다.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

디지털 트윈을 업데이트하는 Azure Digital Twins와 같이 서비스에서 동기적으로 실행하는 경우 해당 알림의 데이터에는 다음과 같은 본문이 포함됩니다.

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

이 데이터는 수명 주기 알림 메시지의 `data` 필드에 포함되는 정보입니다.

## <a name="digital-twin-lifecycle-notifications"></a>디지털 트윈 수명 주기 알림

[디지털 트윈](concepts-twins-graph.md)이 [Azure Digital Twins의 IoT Hub 디바이스](how-to-ingest-iot-hub-data.md)를 나타내는지 여부에 관계없이, 디지털 트윈은 모두 알림을 내보냅니다. 디지털 트윈 자체와 관련이 있는 **수명 주기 알림** 때문입니다.

수명 주기 알림은 다음 경우에 생성됩니다.
* 디지털 트윈이 생성되는 경우
* 디지털 트윈이 삭제되는 경우

### <a name="properties"></a>속성

수명 주기 알림 본문의 필드는 다음과 같습니다.

| Name | 값 |
| --- | --- |
| `id` | 서비스에서 유지 관리되는 카운터 또는 UUID와 같은 알림의 식별자. `source` + `id`는 각 고유 이벤트에 대해 고유합니다. |
| `source` | IoT 허브 또는 Azure Digital Twins 인스턴스의 이름(예: *myhub.azure-devices.net* 또는 *mydigitaltwins.westus2.azuredigitaltwins.net*) |
| `data` | 수명 주기 이벤트가 발생하는 트윈의 데이터. 자세한 내용은 아래 [본문 세부 정보](#body-details-1)를 참조하세요. |
| `specversion` | *1.0*<br>메시지는 이 버전의 [CloudEvents 사양](https://github.com/cloudevents/spec)을 따릅니다. |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 디지털 트윈 ID |
| `time` | 트윈에서 작업이 수행된 시간의 타임스탬프 |
| `traceparent` | 이벤트의 W3C 추적 컨텍스트 |

### <a name="body-details"></a>본문 세부 정보

수명 주기 알림 메시지의 예제는 다음과 같습니다. 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

메시지 내에서 `data` 필드에는 JSON 형식으로 표시된 영향을 받는 디지털 트윈 테이터가 포함됩니다. 이 `data` 필드의 스키마는 *Digital Twins Resource 7.1* 입니다.

생성 이벤트의 경우 `data` 페이로드는 리소스가 만들어진 후 트윈 상태를 반영하므로 `GET` 호출처럼 모든 시스템 생성 요소를 포함해야 합니다.

구성 요소가 있고 최상위 속성이 없는 [IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) 디바이스 관련 데이터의 예제는 다음과 같습니다. 디바이스에 적합하지 않은 속성(예: 보고된 속성)은 생략해야 합니다. 다음 JSON 개체는 수명 주기 알림 메시지의 `data` 필드에 포함되는 정보입니다.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

디지털 트윈 데이터의 또 다른 예제는 다음과 같습니다. 이 예제는 [모델](concepts-models.md)을 기반으로 하며 구성 요소를 지원하지 않습니다.

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>디지털 트윈 관계 변경 알림

**관계 변경 알림** 은 디지털 트윈의 관계가 생성, 업데이트 또는 삭제될 때 생성됩니다. 

### <a name="properties"></a>속성

관계 변경 알림 본문의 필드는 다음과 같습니다.

| Name    | 값 |
| --- | --- |
| `id` | 서비스에서 유지 관리되는 카운터 또는 UUID와 같은 알림의 식별자. `source` + `id`는 각 고유 이벤트에 대해 고유합니다. |
| `source` | Azure Digital Twins 인스턴스의 이름(예: *mydigitaltwins.westus2.azuredigitaltwins.net*) |
| `data` | 변경된 관계의 페이로드. 자세한 내용은 아래 [본문 세부 정보](#body-details-2)를 참조하세요. |
| `specversion` | *1.0*<br>메시지는 이 버전의 [CloudEvents 사양](https://github.com/cloudevents/spec)을 따릅니다. |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 관계 ID(예: `<twin-ID>/relationships/<relationshipID>`) |
| `time` | 관계에서 작업이 수행된 시간의 타임스탬프 |
| `traceparent` | 이벤트의 W3C 추적 컨텍스트 |

### <a name="body-details"></a>본문 세부 정보

메시지 내에서 `data` 필드는 JSON 형식으로 관계의 페이로드를 포함합니다. 이 필드는 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)를 통해 관계에 대한 `GET` 요청과 동일한 형식을 사용합니다. 

업데이트 관계 알림에 관한 데이터의 예제는 다음과 같습니다. “관계 업데이트”는 관계의 속성이 변경되었음을 의미하므로 데이터는 업데이트된 속성 및 새 값을 표시합니다. 다음 JSON 개체는 디지털 트윈 관계 알림 메시지의 `data` 필드에 포함되는 정보입니다.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

만들기 또는 삭제 관계 알림에 관한 데이터의 예제는 다음과 같습니다. `Relationship.Delete`의 경우 본문은 `GET` 요청과 같으며 삭제 전 최신 상태를 가져옵니다.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>디지털 트윈 원격 분석 메시지

**원격 분석 메시지** 는 측정값을 수집하고 전송하는 연결된 디바이스의 Azure Digital Twins에 수신됩니다.

### <a name="properties"></a>속성

원격 분석 메시지 본문의 필드는 다음과 같습니다.

| Name    | 값 |
| --- | --- |
| `id` | 원격 분석 API를 호출할 때 고객이 제공하는 알림 식별자 |
| `source` | 원격 분석 이벤트가 전송된 트윈의 정규화된 이름. `<your-Digital-Twin-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>` 형식을 사용합니다. |
| `specversion` | *1.0*<br>메시지는 이 버전의 [CloudEvents 사양](https://github.com/cloudevents/spec)을 따릅니다. |
| `type` | `microsoft.iot.telemetry` |
| `data` | 트윈에 전송된 원격 분석 메시지. 페이로드는 수정되지 않으며 원격 분석이 전송된 트윈의 스키마와 일치하지 않을 수 있습니다. |
| `dataschema` | 데이터 스키마는 원격 분석을 내보내는 구성 요소 또는 트윈의 모델 ID입니다. 예들 들어 `dtmi:example:com:floor4;2`입니다. |
| `datacontenttype` | `application/json` |
| `traceparent` | 이벤트의 W3C 추적 컨텍스트 |

### <a name="body-details"></a>본문 세부 정보

본문에는 디바이스에 관한 일부 컨텍스트 정보와 함께 원격 분석 측정값이 포함됩니다.

예제 원격 분석 메시지 본문은 다음과 같습니다. 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>다음 단계

엔드포인트와 경로를 사용하여 여러 대상에 이벤트를 전달하는 방법을 알아봅니다.
* [이벤트 경로](concepts-route-events.md)
