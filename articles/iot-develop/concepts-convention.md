---
title: IoT 플러그 앤 플레이 규칙 | Microsoft Docs
description: IoT 플러그 앤 플레이는 디바이스가 원격 분석 및 속성을 보내고 명령 및 속성 업데이트를 처리할 때 사용할 것으로 예상하는 규칙에 대한 설명입니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: fc8992e8e602f4a92d870328b6da14dde06af087
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670826"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 디바이스는 IoT Hub와 메시지를 교환할 때 규칙 집합을 따라야 합니다. IoT 플러그 앤 플레이 디바이스는 MQTT 프로토콜을 사용하여 IoT Hub와 통신합니다.

디바이스는 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 포함하거나 IoT Edge 런타임에서 호스팅하는 [IoT Edge 모듈](../iot-edge/about-iot-edge.md)에서 구현될 수 있습니다.

IoT 플러그 앤 플레이 디바이스가 [DTDL(Digital Twins Definition Language)v2](https://github.com/Azure/opendigitaltwins-dtdl) _모델_ 을 사용하여 구현하는 원격 분석, 속성 및 명령을 설명합니다. 이 문서에서 참조하는 모델에는 두 가지 형식이 있습니다.

- **구성 요소 없음** - 구성 요소가 없는 모델입니다. 모델은 원격 분석, 속성 및 명령을 기본 인터페이스의 콘텐츠 섹션에서 최상위 속성으로 선언합니다. Azure IoT 탐색기 도구에서 이 모델은 단일 _기본 구성 요소_ 로 표시됩니다.
- **다중 구성 요소** - 두 개 이상의 인터페이스로 구성된 모델입니다. 원격 분석, 속성 및 명령을 사용하여 _기본 구성 요소_ 로 표시되는 기본 인터페이스입니다. 추가 원격 분석, 속성 및 명령을 사용하여 구성 요소로 선언된 하나 이상의 인터페이스입니다.

자세한 내용은 [IoT 플러그 앤 플레이 모델링 가이드](concepts-modeling-guide.md)를 참조하세요.

## <a name="identify-the-model"></a>모델 식별

구현하는 모델을 알리기 위해 IoT 플러그 앤 플레이 디바이스 또는 모듈은 `USERNAME` 필드에 `model-id`을 추가하여 MQTT 연결 패킷에 모델 ID가 포함합니다.

디바이스 또는 모듈에서 구현하는 모델을 식별하기 위해 서비스는 다음에서 모델 ID를 가져올 수 있습니다.

- 디바이스 쌍 `modelId` 필드입니다.
- 디지털 트윈 `$metadata.$model` 필드입니다.
- 디지털 트윈 변경 알림입니다.

## <a name="telemetry"></a>원격 분석

구성 요소가 없는 디바이스에서 전송된 원격 분석에는 추가 메타데이터가 필요하지 않습니다. 시스템이 `dt-dataschema` 특성을 추가합니다.

다중 구성 요소 디바이스에서 전송된 원격 분석은 `$.sub` 메시지 속성으로 추가해야 합니다. 시스템은 `dt-subject` 및 `dt-dataschema` 속성을 추가합니다.

## <a name="read-only-properties"></a>읽기 전용 속성

### <a name="sample-no-component-read-only-property"></a>구성 요소가 없는 읽기 전용 속성 샘플

디바이스 또는 모듈은 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

reported 속성 페이로드 샘플:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>다중 구성 요소 읽기 전용 속성 샘플

디바이스 또는 모듈은 요소가 구성 요소를 참조함을 표시하기 위해 `{"__t": "c"}` 마커를 추가해야 합니다.

구성 요소를 참조하는 DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

구성 요소를 정의하는 DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

reported 속성 페이로드 샘플:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>쓰기 가능한 속성

디바이스 또는 모듈은 reported 속성을 전송하여 속성을 받았는지 확인해야 합니다. reported 속성에는 다음이 포함되어야 합니다.

- `value` - 속성의 실제 값(일반적으로 수신된 값이지만 디바이스가 다른 값을 보고하도록 결정할 수 있음).
- `ac` - HTTP 상태 코드를 사용하는 승인 코드입니다.
- `av` - desired 속성의 `$version`을 참조하는 승인 버전입니다. 이 값은 desired 속성 JSON 페이로드에서 찾을 수 있습니다.
- `ad` - 선택적 승인 설명입니다.

디바이스가 시작되면 디바이스 쌍을 요청하고 쓰기 가능한 속성 업데이트를 확인해야 합니다. 디바이스가 오프라인일 때 쓰기 가능한 속성의 버전이 증가하면 디바이스는 reported 속성 응답을 보내 업데이트를 받았음을 확인해야 합니다.

디바이스를 처음 시작하는 경우 허브에서 초기 desired 속성을 받지 못하는 경우 reported 속성에 대한 초기 값을 보낼 수 있습니다. 이 경우 디바이스는 `av`를 `1`로 설정해야 합니다. 예를 들면 다음과 같습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

디바이스는 reported 속성을 사용하여 허브에 다른 정보를 제공할 수 있습니다. 예를 들어 디바이스는 다음과 같은 일련의 진행 중인 메시지를 사용하여 응답할 수 있습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

디바이스가 대상 온도에 도달하면 다음 메시지를 보냅니다.

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

디바이스는 다음과 같은 오류를 보고할 수 있습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="object-type"></a>개체 유형

쓰기 가능한 속성이 개체로 정의된 경우 서비스는 전체 개체를 디바이스에 보내야 합니다. 디바이스가 업데이트에 대해 작동한 방식을 이해하기 위해 서비스에 충분한 정보를 다시 전송하여 업데이트를 승인해야 합니다. 이 응답에는 다음이 포함될 수 있습니다.

- 전체 개체입니다.
- 디바이스가 업데이트한 필드만
- 필드의 하위 집합입니다.

큰 개체의 경우 승인에 포함하는 개체의 크기를 최소화하는 것이 좋습니다.

다음 예제에서는 4개의 필드가 있는 으로 정의된 쓰기 가능한 속성을 보여줍니다. `Object`

DTDL:

```json
{
  "@type": "Property",
  "name": "samplingRange",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "startTime",
        "schema": "dateTime"
      },
      {
        "name": "lastTime",
        "schema": "dateTime"
      },
      {
        "name": "count",
        "schema": "integer"
      },
      {
        "name": "errorCount",
        "schema": "integer"
      }
    ]
  },
  "displayName": "Sampling range"
  "writable": true
}
```

이 쓰기 가능 속성을 업데이트하려면 서비스에서 다음과 같은 전체 개체를 보냅니다.

```json
{
  "samplingRange": {
    "startTime": "2021-08-17T12:53:00.000Z",
    "lastTime": "2021-08-17T14:54:00.000Z",
    "count": 100,
    "errorCount": 5
  }
}
```

디바이스는 다음과 같은 승인으로 응답합니다.

```json
{
  "samplingRange": {
    "ac": 200,
    "av": 5,
    "ad": "Weighing status updated",
    "value": {
      "startTime": "2021-08-17T12:53:00.000Z",
      "lastTime": "2021-08-17T14:54:00.000Z",
      "count": 100,
      "errorCount": 5
    }
  }
}
```

### <a name="sample-no-component-writable-property"></a>구성 요소가 없는 쓰기 가능한 속성 샘플

디바이스가 단일 페이로드에서 여러 desired 속성을 수신하는 경우 여러 페이로드에서 reported 속성 응답을 보내거나 응답을 단일 페이로드로 결합할 수 있습니다.

디바이스 또는 모듈은 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "targetHumidity",
      "schema": "double",
      "writable": true
    }
  ]
}
```

desired 속성 페이로드 샘플:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80,
  "$version" : 3
}
```

reported 속성 첫 번째 페이로드 샘플:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

reported 속성 두 번째 페이로드 샘플:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

> [!NOTE]
> 이러한 두 개의 보고된 속성 페이로드를 단일 페이로드로 결합하도록 선택할 수 있습니다.

### <a name="sample-multiple-components-writable-property"></a>다중 구성 요소 쓰기 가능 속성 샘플

디바이스 또는 모듈은 요소가 구성 요소를 참조함을 표시하기 위해 `{"__t": "c"}` 마커를 추가해야 합니다.

마커는 구성 요소에 정의된 속성에 대한 업데이트에 대해서만 전송됩니다. 기본 구성 요소에 정의된 속성 업데이트에는 마커가 포함되지 않습니다. [구성 요소가 없는 쓰기 가능한 속성 샘플](#sample-no-component-writable-property)을 참조하세요.

디바이스가 단일 페이로드에서 여러 reported 속성을 수신하는 경우 여러 페이로드에서 reported 속성 응답을 보내거나 응답을 단일 페이로드로 결합할 수 있습니다.

디바이스 또는 모듈은 reported 속성을 전송하여 속성을 받았는지 확인해야 합니다.

구성 요소를 참조하는 DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

구성 요소를 정의하는 DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

desired 속성 페이로드 샘플:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80,
    "$version" : 3
  }
}
```

reported 속성 첫 번째 페이로드 샘플:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

reported 속성 두 번째 페이로드 샘플:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

> [!NOTE]
> 이러한 두 개의 보고된 속성 페이로드를 단일 페이로드로 결합하도록 선택할 수 있습니다.

## <a name="commands"></a>명령

접두사 없이 명령 이름을 사용하는 구성 요소 인터페이스는 없습니다.

디바이스 또는 모듈에서 다중 구성 요소 인터페이스는 `componentName*commandName` 형식의 명령 이름을 사용합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 규칙에 대해 살펴보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT 플러그 앤 플레이 모델링 가이드](concepts-modeling-guide.md)
