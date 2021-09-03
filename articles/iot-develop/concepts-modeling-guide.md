---
title: IoT 플러그 앤 플레이 디바이스 모델 이해 | Microsoft Docs
description: IoT 플러그 앤 플레이 디바이스에 대한 DTDL(Digital Twins Definition Language) 모델링 언어를 이해합니다. 이 문서에서는 기본 및 복합 데이터 형식, 구성 요소 및 상속을 사용하는 재사용 패턴, 의미 체계 형식에 대해 설명합니다. 이 문서에는 모델을 작성하기 위한 디바이스 쌍 모델 식별자 및 도구 지원을 선택하는 방법에 대한 지침이 나와 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: b79c72f6711519c87cb671ea5b7d72eaffabe7e2
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406580"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT 플러그 앤 플레이 모델링 가이드

IoT 플러그 앤 플레이의 핵심에는 IoT 플러그 앤 플레이 사용 애플리케이션에 대한 디바이스의 기능을 설명하는 디바이스 _모델_ 이 있습니다. 이 모델은 다음을 정의하는 인터페이스 세트로 구성됩니다.

- 속성 - 디바이스 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능 상태를 나타냅니다. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 쓰기 가능 속성일 수 있습니다.
- _원격 분석_ 필드 - 데이터가 정기적인 센서 판독 스트림인지, 가끔 발생하는 오류인지, 아니면 정보 메시지인지 여부에 관계없이 디바이스에서 내보내는 데이터를 정의하는 필드입니다.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

IoT 플러그 앤 플레이에서 디바이스 모델을 사용하는 방법에 대한 자세한 내용은 [IoT 플러그 앤 플레이 디바이스 개발자 가이드](concepts-developer-guide-device.md) 및 [IoT 플러그 앤 플레이 서비스 개발자 가이드](concepts-developer-guide-service.md)를 참조하세요.

모델을 정의하려면 DTDL(Digital Twins Definition Language)을 사용합니다. DTDL은 [JSON-LD](https://json-ld.org/)라는 JSON 변형을 사용합니다. 다음 코드 조각에서는 다음과 같은 자동 온도 조절기 디바이스에 대한 모델을 보여 줍니다.

- 고유한 모델 ID(`dtmi:com:example:Thermostat;1`)가 있습니다.
- 온도 원격 분석을 보냅니다.
- 목표 온도를 설정하는 쓰기 가능한 속성이 있습니다.
- 마지막 다시 부팅 이후의 최대 온도를 보고하는 읽기 전용 속성이 있습니다.
- 일정 기간 동안의 최대, 최소 및 평균 온도를 요청하는 명령에 응답합니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

자동 온도 조절기 모델에는 단일 인터페이스가 있습니다. 이 문서의 뒷부분의 예에서는 구성 요소 및 상속을 사용하는 더 복잡한 모델을 보여 줍니다.

이 문서에서는 사용자 자신의 모델을 설계하고 제작하는 방법을 설명하고 데이터 형식, 모델 구조 및 도구와 같은 주제에 대해 설명합니다.

자세한 내용은 [Digital Twins Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) 사양을 참조하세요.

## <a name="model-structure"></a>모델 구조

속성, 원격 분석 및 명령은 인터페이스로 그룹화됩니다. 이 섹션에서는 인터페이스를 통해 구성 요소 및 상속을 사용하여 간단한 모델과 복잡한 모델을 설명하는 방법에 대해 설명합니다.

### <a name="model-ids"></a>모델 ID

모든 인터페이스에는 고유한 DTMI(디지털 트윈 모델 식별자)가 있습니다. 복잡한 모델은 DTMI를 사용하여 구성 요소를 식별합니다. 애플리케이션은 디바이스에서 보내는 DTMI를 사용하여 리포지토리에서 모델 정의를 찾을 수 있습니다.

DTMI는 [IoT 플러그 앤 플레이 모델 리포지토리](https://github.com/Azure/iot-plugandplay-models)에서 요구하는 명명 규칙을 따라야 합니다.

- DTMI 접두사는 `dtmi:`입니다.
- DTMI 접미사는 모델의 버전 번호(예: `;2`)입니다.
- DTMI의 본문은 모델이 저장된 모델 리포지토리의 폴더 및 파일에 매핑됩니다. 버전 번호는 파일 이름의 일부입니다.

예를 들어 `dtmi:com:Example:Thermostat;2` DTMI로 식별된 모델은 *dtmi/com/example/thermostat-2.json* 파일에 저장됩니다.

다음 코드 조각에서는 고유한 DTMI를 사용하는 인터페이스 정의에 대한 개요를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>구성 요소 없음

이전에 표시된 자동 온도 조절기와 같은 간단한 모델은 포함 또는 계단식 구성 요소를 사용하지 않습니다. 원격 분석, 속성 및 명령은 인터페이스의 `contents` 노드에서 정의됩니다.

다음 예제에서는 구성 요소를 사용하지 않는 간단한 모델의 일부를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Azure IoT 탐색기 및 IoT Central 디바이스 템플릿 디자이너와 같은 도구는 자동 온도 조절기와 같은 독립 실행형 인터페이스를 _기본 구성 요소_ 로 분류합니다.

다음 스크린샷에서는 Azure IoT 탐색기 도구에서 모델이 표시되는 방식을 보여 줍니다.

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Azure IoT 탐색기의 기본 구성 요소":::

다음 스크린샷에서는 IoT Central 디바이스 템플릿 디자이너에서 모델이 기본 구성 요소로 표시되는 방식을 보여 줍니다. **ID 보기** 를 선택하여 모델의 DTMI를 확인합니다.

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="IoT Central 디자이너에서 자동 온도 조절기 모델을 보여 주는 스크린샷":::

모델 ID는 다음 스크린샷과 같이 디바이스 쌍 속성에 저장됩니다.

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="디지털 트윈 속성의 모델 ID":::

구성 요소를 사용하지 않는 DTDL 모델은 원격 분석, 속성 및 명령의 단일 세트를 사용하는 디바이스 또는 IoT Edge 모듈에 유용하게 사용할 수 있습니다. 구성 요소를 사용하지 않는 모델을 사용하면 기존 디바이스 또는 모듈을 IoT 플러그 앤 플레이 디바이스 또는 모듈로 쉽게 마이그레이션할 수 있습니다. 구성 요소를 정의할 필요 없이 실제 디바이스 또는 모듈을 설명하는 DTDL 모델을 만듭니다.

> [!TIP]
> 모듈은 디바이스 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 또는 [IoT Edge 모듈](../iot-edge/about-iot-edge.md)일 수 있습니다.

### <a name="reuse"></a>재사용

인터페이스 정의를 다시 사용하는 방법에는 두 가지가 있습니다. 모델에서 여러 구성 요소를 사용하여 다른 인터페이스 정의를 참조합니다. 상속을 사용하여 기존 인터페이스 정의를 확장합니다.

### <a name="multiple-components"></a>여러 구성 요소

구성 요소를 사용하면 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다.

예를 들어 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 인터페이스는 모델로 정의됩니다. [온도 조절기 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)을 정의할 때 이 인터페이스를 하나 이상의 구성 요소로 통합할 수 있습니다. 다음 예제에서 이러한 구성 요소는 `thermostat1` 및 `thermostat2`라고 합니다.

여러 구성 요소가 포함된 DTDL 모델의 경우 둘 이상의 구성 요소 섹션이 있습니다. 각 섹션에는 `@type`이 `Component`로 설정되어 있으며, 다음 코드 조각에서 표시한 대로 스키마를 명시적으로 참조합니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

이 모델에는 contents 섹션에 정의된 세 가지 구성 요소(두 개의 `Thermostat` 구성 요소 밑 하나의 `DeviceInformation` 구성 요소)가 있습니다. contents 섹션에는 속성, 원격 분석 및 명령 정의도 포함됩니다.

다음 스크린샷에서는 이 모델이 IoT Central에 표시되는 방식을 보여 줍니다. 온도 조절기의 속성, 원격 분석 및 명령 정의는 최상위 **기본 구성 요소** 에 나타납니다. 각 자동 온도 조절기에 대한 속성, 원격 분석 및 명령 정의는 구성 요소 정의에 나타납니다.

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="IoT Central의 온도 조절기 디바이스 템플릿을 보여 주는 스크린샷":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="IoT Central의 온도 조절기 디바이스 템플릿에 있는 자동 온도 조절기 구성 요소를 보여 주는 스크린샷":::

구성 요소와 상호 작용하는 디바이스 코드를 작성하는 방법에 대한 자세한 내용은 [IoT 플러그 앤 플레이 디바이스 개발자 가이드](concepts-developer-guide-device.md)를 참조하세요.

디바이스의 구성 요소와 상호 작용하는 서비스 코드를 작성하는 방법은 [IoT 플러그 앤 플레이 서비스 개발자 가이드](concepts-developer-guide-service.md)를 참조하세요.

### <a name="inheritance"></a>상속

상속을 사용하면 기본 인터페이스의 기능을 다시 사용하여 인터페이스의 기능을 확장할 수 있습니다. 예를 들어 여러 디바이스 모델에서 일련 번호와 같은 일반적인 기능을 공유할 수 있습니다.

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="기본 인터페이스의 기능을 공유하는 자동 온도 조절기 및 유량 제어기를 보여 주는 디바이스 모델의 상속 예" border="false":::

다음 코드 조각에서는 `extends` 키워드를 사용하여 이전 다이어그램에서 보여 주는 상속 관계를 정의하는 DTML 모델을 보여 줍니다.

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

다음 스크린샷에서는 IoT Central 디바이스 템플릿 환경에서 이 모델을 보여 줍니다.

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="IoT Central의 인터페이스 상속을 보여 주는 스크린샷":::

디바이스 또는 서비스 쪽 코드를 작성하는 경우 코드는 상속된 인터페이스를 처리하기 위해 특별한 작업을 수행할 필요가 없습니다. 이 섹션에서 보여 주는 예제에서 디바이스 코드는 일련 번호를 자동 온도 조절기 인터페이스의 일부인 것처럼 보고합니다.

### <a name="tips"></a>팁

모델을 만들 때 구성 요소와 상속을 결합할 수 있습니다. 다음 다이어그램에서는 `baseDevice` 인터페이스에서 상속되는 `thermostat` 모델을 보여 줍니다. `baseDevice` 인터페이스에는 다른 인터페이스에서 상속되는 구성 요소가 있습니다.

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="구성 요소와 상속을 모두 사용하는 모델을 보여 주는 다이어그램" border="false":::

다음 코드 조각에서는 `extends` 및 `component` 키워드를 사용하여 이전 다이어그램에서 보여 준 상속 관계 및 구성 요소 사용을 정의하는 DTML 모델을 보여 줍니다.

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>데이터 형식

데이터 형식을 사용하여 원격 분석, 속성 및 명령 매개 변수를 정의합니다. 데이터 형식은 기본 또는 복합 형식일 수 있습니다. 복합 데이터 형식은 기본 형식 또는 다른 복합 형식을 사용합니다. 복합 형식의 최대 깊이는 5개 수준입니다.

### <a name="primitive-types"></a>기본 형식

다음 표에서는 사용할 수 있는 기본 형식 세트를 보여 줍니다.

| 기본 형식 | 설명 |
| --- | --- |
| `boolean` | 부울 값 |
| `date` | [RFC 3339의 5.6 섹션](https://tools.ietf.org/html/rfc3339#section-5.6)에 정의된 전체 날짜 |
| `dateTime` | [RFC 3339](https://tools.ietf.org/html/rfc3339)에 정의된 날짜/시간 |
| `double` | IEEE 8바이트 부동 소수점 |
| `duration` | ISO 8601 형식의 기간 |
| `float` | IEEE 4바이트 부동 소수점 |
| `integer` | 부호 있는 4바이트 정수 |
| `long` | 부호 있는 8바이트 정수 |
| `string` | UTF8 문자열 |
| `time` | [RFC 3339의 5.6 섹션](https://tools.ietf.org/html/rfc3339#section-5.6)에 정의된 전체 시간 |

다음 코드 조각에서는 `schema` 필드에서 `double` 형식을 사용하는 원격 분석 정의의 예제를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>복합 데이터 형식

복합 데이터 형식은 *배열*, *열거형*, *맵*, *개체* 또는 지리 공간적 형식 중 하나입니다.

#### <a name="arrays"></a>배열

배열은 모든 요소가 동일한 형식인 인덱싱 가능한 데이터 형식입니다. 요소 형식은 기본 또는 복합 형식일 수 있습니다.

다음 코드 조각에서는 `schema` 필드에서 `Array` 형식을 사용하는 원격 분석 정의의 예제를 보여 줍니다. 배열의 요소는 부울입니다.

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>열거형

열거형은 값에 매핑되는 명명된 레이블 세트가 있는 형식을 설명합니다. 값은 정수 또는 문자열일 수 있지만, 레이블은 항상 문자열입니다.

다음 코드 조각에서는 `schema` 필드에서 `Enum` 형식을 사용하는 원격 분석 정의의 예제를 보여 줍니다. 열거형의 값은 정수입니다.

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

맵은 모든 값의 형식이 동일한 키-값 쌍 형식입니다. 맵의 키는 문자열이어야 합니다. 맵의 값은 다른 복합 형식을 포함하여 모든 형식일 수 있습니다.

다음 코드 조각에서는 `schema` 필드에서 `Map` 형식을 사용하는 속성 정의의 예제를 보여 줍니다. 맵의 값은 문자열입니다.

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>개체

개체 형식은 명명된 필드로 구성됩니다. 개체 맵의 필드 형식은 기본 또는 복합 형식일 수 있습니다.

다음 코드 조각에서는 `schema` 필드에서 `Object` 형식을 사용하는 원격 분석 정의의 예제를 보여 줍니다. 개체의 필드는 `dateTime`, `duration` 및 `string` 형식입니다.

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>지리 공간적 형식

DTDL은 지리 데이터 구조를 모델링하기 위해 [GeoJSON](https://geojson.org/)을 기반으로 하는 일단의 지리 공간적 형식(`point`, `multiPoint`, `lineString`, `multiLineString`, `polygon` 및 `multiPolygon`)을 제공합니다. 이러한 형식은 배열, 개체 및 열거형의 미리 정의된 중첩 구조입니다.

다음 코드 조각에서는 `schema` 필드에서 `point` 형식을 사용하는 원격 분석 정의의 예제를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

지리 공간적 형식은 배열 기반이므로 현재 속성 정의에서 사용할 수 없습니다.

## <a name="semantic-types"></a>의미 체계 형식

속성 또는 원격 분석 정의의 데이터 형식은 디바이스에서 서비스와 교환하는 데이터의 형식을 지정합니다. 의미 체계 형식은 애플리케이션에서 값을 처리하거나 표시하는 방법을 결정하는 데 사용할 수 있는 원격 분석 및 속성에 대한 정보를 제공합니다. 각 의미 체계 형식에는 하나 이상의 연결된 단위가 있습니다. 예를 들어 섭씨 및 화씨는 온도 의미 체계 형식의 단위입니다. IoT Central 대시보드 및 분석은 의미 체계 형식 정보를 사용하여 원격 분석 또는 속성 값을 그리고 단위를 표시하는 방법을 결정할 수 있습니다. 모델 파서를 사용하여 의미 체계 형식을 읽는 방법에 대한 자세한 내용은 [디지털 트윈 모델 파서 이해](concepts-model-parser.md)를 참조하세요.

다음 코드 조각에서는 의미 체계 형식 정보가 포함된 원격 분석 정의의 예제를 보여 줍니다. `Temperature` 의미 체계 형식이 `@type` 배열에 추가되고, `unit` 값인 `degreeCelsius`는 의미 체계 형식에 유효한 단위 중 하나입니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>지역화

IoT Central과 같은 애플리케이션은 모델의 정보를 사용하여 IoT 플러그 앤 플레이 디바이스와 교환되는 데이터를 중심으로 UI를 동적으로 빌드합니다. 예를 들어 대시보드의 타일은 원격 분석, 속성 및 명령에 대한 이름과 설명을 표시할 수 있습니다.

모델의 선택적 `description` 및 `displayName` 이름 필드에는 UI에서 사용하기 위한 문자열이 포함됩니다. 이러한 필드에는 애플리케이션에서 지역화된 UI를 렌더링하는 데 사용할 수 있는 지역화된 문자열이 포함될 수 있습니다.

다음 코드 조각에서는 지역화된 문자열이 포함된 온도 원격 분석 정의의 예제를 보여 줍니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

지역화된 문자열을 추가하는 것은 선택 사항입니다. 다음 예제에는 하나의 기본 언어만 있습니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>수명 주기 및 도구

디바이스 모델의 4가지 수명 주기 단계는 *작성자*, *게시*, *사용* 및 *버전* 입니다.

### <a name="author"></a>작성자

DTML 디바이스 모델은 텍스트 편집기에서 만들 수 있는 JSON 문서입니다. 그러나 IoT Central에서는 디바이스 템플릿 GUI 환경을 사용하여 DTML 모델을 만들 수 있습니다. IoT Central에서 수행할 수 있는 작업은 다음과 같습니다.

- 속성, 원격 분석 및 명령을 정의하는 인터페이스를 만듭니다.
- 구성 요소를 사용하여 여러 인터페이스를 함께 어셈블합니다.
- 인터페이스 간의 상속 관계를 정의합니다.
- DTML 모델 파일을 가져오고 내보냅니다.

자세한 내용은 [Azure IoT Central 애플리케이션에서 새 IoT 디바이스 유형 정의](../iot-central/core/howto-set-up-template.md)를 참조하세요.

VS Code 및 Visual Studio 2019 모두에 대한 DTDL 제작 확장이 있습니다.

VS Code용 DTDL 확장을 설치하려면 [Visual Studio Code 용 DTDL 편집기](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)로 이동합니다. VS Code의 **확장** 뷰에서 **DTDL** 을 검색할 수도 있습니다.

이 확장을 설치한 경우 확장을 사용하면 VS Code에서 DTDL 모델 파일을 작성하는 데 도움이 됩니다.

- 이 확장은 DTDL 모델 파일에서 구문 유효성 검사를 제공하고 다음 스크린샷에 보듯이 오류를 강조 표시합니다.

    :::image type="content" source="media/concepts-modeling-guide/model-validation.png" alt-text="VS Code의 모델 유효성 검사":::

- DTDL 모델을 편집하는 경우 intellisense와 자동 완성을 사용합니다.

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense.png" alt-text="VS Code에서 DTDL 모델에 intellisense 사용":::

- 새 DTDL 인터페이스를 만듭니다. **DTDL: 인터페이스 만들기** 명령은 새 인터페이스를 사용하여 JSON 파일을 만듭니다. 인터페이스에는 원격 분석, 속성 및 명령 정의 예가 포함되어 있습니다.

Visual Studio 2019용 DTDL 확장을 설치하려면 [VS 2019에 대한 DTDL 언어 지원](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)으로 이동하세요. Visual Studio의 **확장 관리** 에서 **DTDL** 을 검색할 수도 있습니다.

이 확장을 설치한 경우 확장을 사용하면 Visual Studio에서 DTDL 모델 파일을 작성하는 데 도움이 됩니다.

- 이 확장은 DTDL 모델 파일에서 구문 유효성 검사를 제공하고 다음 스크린샷에 보듯이 오류를 강조 표시합니다.

    :::image type="content" source="media/concepts-modeling-guide/model-validation-2.png" alt-text="Visual Studio의 모델 유효성 검사":::

- DTDL 모델을 편집하는 경우 intellisense와 자동 완성을 사용합니다.

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense-2.png" alt-text="Visual Studio에서 DTDL 모델에 intellisense 사용":::

### <a name="publish"></a>게시

DTML 모델을 공유 및 검색 가능하게 하려면 해당 모델을 디바이스 모델 리포지토리에 게시합니다.

모델을 퍼블릭 리포지토리에 게시하기 전에 `dmr-client` 도구를 사용하여 모델의 유효성을 검사할 수 있습니다.

자세한 내용은 [디바이스 모델 리포지토리](concepts-model-repository.md)를 참조하세요.

### <a name="use"></a>Windows Server Update Services와 함께

IoT Central과 같은 애플리케이션은 디바이스 모델을 사용합니다. IoT Central에서 모델은 디바이스의 기능을 설명하는 디바이스 템플릿의 일부입니다. IoT Central은 디바이스 템플릿을 사용하여 대시보드 및 분석을 포함하여 디바이스에 대한 UI를 동적으로 빌드합니다.

사용자 지정 솔루션은 [디지털 트윈 모델 파서](concepts-model-parser.md)를 사용하여 모델을 구현하는 디바이스의 기능을 인식할 수 있습니다. 자세한 내용은 [IoT 솔루션에서 IoT 플러그 앤 플레이 모델 사용](concepts-model-discovery.md)을 참조하세요.

### <a name="version"></a>버전

모델을 사용하는 디바이스 및 서버 쪽 솔루션이 계속 작동하도록 하기 위해 게시된 모델은 변경할 수 없습니다.

DTMI에는 여러 버전의 모델을 만드는 데 사용할 수 있는 버전 번호가 포함되어 있습니다. 디바이스 및 서버 쪽 솔루션은 사용하도록 설계된 특정 버전을 사용할 수 있습니다.

IoT Central은 디바이스 모델에 대한 더 많은 버전 관리 규칙을 구현합니다. IoT Central에서 디바이스 템플릿 및 해당 모델의 버전을 관리하는 경우 디바이스를 이전 버전에서 이후 버전으로 마이그레이션할 수 있습니다. 그러나 마이그레이션된 디바이스는 펌웨어를 업그레이드한 후에 새로운 기능을 사용할 수 있습니다. 자세한 내용은 [디바이스 템플릿 수정](../iot-central/core/howto-edit-device-template.md)을 참조하세요.

## <a name="limits-and-constraints"></a>제한 및 제약 조건

다음 목록에는 모델에 대한 몇 가지 주요 제한 및 제약 조건이 요약되어 있습니다.

- 현재 배열, 맵 및 개체의 최대 깊이는 5개 수준입니다.
- 배열은 속성 정의에서 사용할 수 없습니다.
- 인터페이스는 10개 수준의 깊이로 확장할 수 있습니다.
- 인터페이스는 최대 두 개의 다른 인터페이스로 확장할 수 있습니다.
- 구성 요소는 다른 구성 요소를 포함할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이제 디바이스 모델링에 대해 알아보았으므로 다음과 같은 추가 리소스가 있습니다.

- [DTDL(Digital Twins Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl)
- [모델 리포지토리](./concepts-model-repository.md)
