---
title: IoT 플러그 앤 플레이 디지털 트윈 이해
description: IoT 플러그 앤 플레이 디지털 트윈 이해
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 5623192cd713f06be24ce6c3f78b91756868db86
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406563"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>IoT 플러그 앤 플레이 디지털 트윈 이해

IoT 플러그 앤 플레이 장치는 [DTDL(디지털 트윈 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 스키마에서 설명하는 모델을 구현합니다. 모델은 특정 장치에 포함될 수 있는 구성 요소, 속성, 명령 및 원격 분석 메시지 집합을 설명합니다.

IoT 플러그 앤 플레이는 DTDL 버전 2를 사용합니다. 이 버전에 대한 자세한 내용은 GitHub의 [DTDL(디지털 트윈 정의 언어)-버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 사양을 참조하십시오.

> [!NOTE]
> DTDL은 IoT 플러그 앤 플레이에만 국한되지 않습니다. [Azure Digital Twins](../digital-twins/overview.md)와 같은 다른 IoT 서비스는 이를 사용하여 건물 및 에너지 네트워크와 같은 전체 환경을 나타냅니다.

Azure IoT 서비스 Sdk에는 서비스에서 장치의 디지털 트윈을 상호작용할 수 있도록 하는 Api가 포함되어 있습니다. 예를 들어, 서비스는 디지털 트윈에서 장치 속성을 읽거나 디지털 트윈을 사용하여 장치에서 명령을 호출할 수 있습니다. 자세히 알아보려면 [IoT Hub 디지털 트윈 예시](concepts-developer-guide-service.md#iot-hub-digital-twin-examples)를 참조하세요.

이 문서의 IoT 플러그 앤 플레이 장치 예시에서는 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 구성 요소를 포함하는 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)을 구현합니다.

## <a name="device-twins-and-digital-twins"></a>장치 쌍 및 디지털 트윈

또한 Azure IoT Hub 디지털 트윈 뿐만 아니라 연결된 모든 장치에 대한 *장치 쌍* 을 유지 관리합니다. 장치 쌍은 장치 속성을 표현한다는 점에서 디지털 트윈과 비슷합니다. Azure IoT 서비스 Sdk에는 장치 쌍과 상호작용하기 위한 Api가 포함되어 있습니다.

Iot hub는 IoT 플러그 앤 플레이 장치가 처음으로 연결할 때 디지털 트윈 및 장치 쌍을 초기화합니다.

디바이스 쌍은 메타데이터, 구성, 조건 등 디바이스 상태 정보가 저장된 JSON 문서입니다. 자세히 알아보려면 [IoT Hub 서비스 클라이언트 예시](concepts-developer-guide-service.md#iot-hub-service-client-examples)를 참조하세요. 장치 및 솔루션 빌더는 동일한 장치 쌍 Api 및 Sdk 집합을 통해 IoT 플러그 앤 플레이 규칙을 사용하여 장치 및 솔루션을 구현할 수 있습니다.

디지털 트윈 Api는 구성 요소, 속성 및 명령과 같은 고급 DTDL 구문에 대해 작동합니다. 디지털 트윈 API를 사용하면 솔루션 빌더에서 IoT 플러그 앤 플레이 솔루션을 쉽게 생성할 수 있습니다.

장치 쌍에서 쓰기 가능한 속성의 상태는 *원하는 속성* 및 *보고된 속성* 섹션으로 나뉩니다. 모든 읽기 전용 속성은 보고된 속성 섹션 내에서 사용할 수 있습니다.

디지털 트윈에는 속성의 현재 및 원하는 상태에 대한 통합 보기가 있습니다. 지정된 속성의 동기화 상태는 해당 기본 구성 요소 `$metadata` 섹션에 저장됩니다.

### <a name="device-twin-json-example"></a>장치 쌍 JSON 예시

다음 조각은 JSON 개체로 형식이 지정된 IoT 플러그 앤 플레이 장치 쌍을 보여줍니다.

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>디지털 트윈 예시

다음 조각은 JSON 개체로 형식이 지정된 디지털 트윈을 보여줍니다.

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

다음 표에서는 디지털 트윈 JSON 개체의 필드에 대해 설명합니다.

| 필드 이름 | Description |
| --- | --- |
| `$dtId` | 장치 디지털 트윈의 ID를 나타내는 사용자 제공 문자열 |
| `{propertyName}` | JSON의 속성 값 |
| `$metadata.$model` | [선택 사항] 이 디지털 트윈의 특징을 나타내는 모델 인터페이스의 ID |
| `$metadata.{propertyName}.desiredValue` | [쓰기 가능한 속성에만 해당] 지정된 속성의 원하는 값 |
| `$metadata.{propertyName}.desiredVersion` | [쓰기 가능한 속성에만 해당] IoT Hub에 의해 관리되는 원하는 값의 버전|
| `$metadata.{propertyName}.ackVersion` | [필수, 쓰기 가능한 속성에만 해당] 디지털 트윈을 구현하는 장치에서 승인한 버전으로, 원하는 버전보다 이상이어야 합니다 |
| `$metadata.{propertyName}.ackCode` | [필수, 쓰기 가능한 속성에만 해당] 디지털 트윈을 구현하는 장치 앱에서 반환된 `ack` 코드 |
| `$metadata.{propertyName}.ackDescription` | [선택 사항, 쓰기 가능한 속성에만 해당] 디지털 트윈을 구현하는 장치 앱에서 반환된 `ack` 설명 |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub는 장치에서 속성의 마지막 업데이트 타임스탬프를 유지 관리합니다. 타임스탬프는 UTC 형식이며 ISO8601 형식 YYYY-MM-DDTHH:MM:SS.mmmZ로 인코딩됩니다 |
| `{componentName}` | 구성 요소의 속성 값과 메타데이터를 포함하는 JSON 개체입니다. |
| `{componentName}.{propertyName}` | JSON에서 구성 요소의 속성 값 |
| `{componentName}.$metadata` | 구성 요소에 대한 메타데이터 정보. |

### <a name="properties"></a>속성

속성은 엔터티의 상태를 나타내는 데이터 필드입니다(예: 많은 개체 지향 프로그래밍 언어의 속성).

#### <a name="read-only-property"></a>읽기 전용 속성

DTDL 스키마:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

이 예시에서 `alwinexlepaho8329`는 장치에서 보고한 `serialNumber` 읽기 전용 속성의 현재 값입니다.
다음 조각은 `serialNumber` 속성의 나란한 JSON 표현을 보여줍니다.

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>쓰기 가능 속성

다음 예에서는 기본 구성 요소의 쓰기 가능 속성을 보여줍니다.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

이 예시에서 `3.0` 는 장치에서 보고하는 `fanSpeed` 속성의 현재 값입니다. `2.0`는 솔루션에 의해 설정되는 원하는 값입니다. 루트 수준 속성의 원하는 값 및 동기화 상태는 디지털 트윈의 루트 수준 `$metadata` 내에서 설정됩니다. 장치가 온라인 상태가 되면 이 업데이트를 적용하고 업데이트된 값을 다시 보고할 수 있습니다.

### <a name="components"></a>구성 요소

구성 요소를 사용하여 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다.
예를 들어 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 인터페이스를 구성 요소 `thermostat1` 및 `thermostat2`를 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) 모델에 통합할 수 있습니다.

장치 쌍에서 구성 요소는 `{ "__t": "c"}` 표식으로 식별됩니다. 디지털 트윈에서 `$metadata`이 있으면 구성 요소가 표시됩니다.

이 예시에서 `thermostat1`은 다음 두 개의 속성을 포함하는 구성 요소입니다.

- `maxTempSinceLastReboot` 읽기 전용 속성이입니다.
- `targetTemperature`은 장치에서 성공적으로 동기화된 쓰기 가능한 속성입니다. 이러한 속성의 원하는 값과 동기화 상태는 구성 요소의 `$metadata`에 있습니다.

다음 조각은 `thermostat1` 구성요소의 나란한 JSON 표현을 보여줍니다.

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>디지털 트윈 API

디지털 트윈 Api에는 디지털 트윈 **가져오기**, **디지털 트윈 업데이트**, **구성 요소 호출 명령** 디지털 트윈 관리를 위한 **명령 작업 호출** 등이 포함됩니다. [REST api](/rest/api/iothub/service/digitaltwin)를 직접 사용하거나 [서비스 SDK](../iot-develop/libraries-sdks.md)를 통해 사용할 수 있습니다.

## <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

디지털 트윈 변경 이벤트를 사용하도록 설정하면 현재 또는 원하는 구성 요소나 속성 값이 변경될 때마다 이벤트가 트리거됩니다. 디지털 트윈 변경 이벤트는 [JSON 패치](http://jsonpatch.com/) 형식으로 생성됩니다. 쌍 변경 이벤트를 사용하도록 설정하면 장치 쌍 형식으로 해당 이벤트가 생성됩니다.

장치 및 디지털 트윈 이벤트에 대해 라우팅을 사용하도록 설정하는 방법에 대한 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 여러 엔드포인트로 장치-클라우드 메시지 보내기](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요. 메시지 형식을 이해하려면 [IoT Hub 메시지 생성하기 및 읽기](../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

예를 들어 다음 디지털 트윈 변경 이벤트는 `targetTemperature`가 솔루션에 의해 설정된 경우에 트리거됩니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

다음 디지털 트윈 변경 이벤트는 장치가 위의 원하는 변경 내용이 적용되었음을 보고할 때 트리거됩니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> 장치와 디지털 트윈 변경 알림에서 모두 설정되면 쌍 변경 알림 메시지가 두 배가 됩니다.

## <a name="next-steps"></a>다음 단계

이제 디지털 트윈에 대해 알아보았으므로 다음과 같은 추가 리소스가 있습니다.

- [IoT 플러그 앤 플레이 디지털 트윈 API를 사용하는 방법](howto-manage-digital-twin.md)
- [솔루션에서 디바이스와 상호 작용](tutorial-service.md)
- [IoT 디지털 트윈 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](../iot-fundamentals/howto-use-iot-explorer.md)