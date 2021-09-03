---
title: REST API를 사용하여 Azure IoT Central에서 디바이스 관리
description: IoT Central REST API를 사용하여 애플리케이션에서 디바이스를 제어하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 06/01/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2e808ee2d36237a830877e7f043656ce9189bd89
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461282"
---
# <a name="how-to-use-the-iot-central-rest-api-to-control-devices"></a>IoT Central REST API를 사용하여 디바이스를 제어하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용하여 IoT Central 애플리케이션에서 디바이스를 제어할 수 있습니다. REST API를 사용하면 다음을 수행할 수 있습니다.

- 디바이스에서 마지막으로 알려진 원격 분석 값을 읽습니다.
- 디바이스에서 속성 값을 읽습니다.
- 디바이스에서 쓰기 가능 속성을 설정합니다.
- 디바이스에서 명령을 호출합니다.

이 문서에서는 `/devices/{device_id}` API를 사용하여 개별 디바이스를 제어하는 방법을 설명합니다. 작업을 사용하여 디바이스를 대량으로 제어할 수도 있습니다.

디바이스는 지원하는 속성, 원격 분석 및 명령을 _구성 요소_ 및 _모듈_ 로 그룹화할 수 있습니다.

모든 IoT Central REST API 호출에는 권한 부여 헤더가 필요합니다. 자세히 알아보려면 [IoT Central REST API 호출을 인증하고 권한을 부여하는 방법](howto-authorize-rest-api.md)을 참조하세요.

IoT Central REST API에 대한 참조 설명서는 [Azure IoT Central REST API 참조](/rest/api/iotcentral/)를 참조하세요.

## <a name="components-and-modules"></a>구성 요소 및 모듈

구성 요소를 사용하면 디바이스 기능을 그룹화하고 다시 사용할 수 있습니다. 구성 요소 및 디바이스 모델에 대해 자세히 알아보려면 [IoT 플러그 앤 플레이 모델링 가이드](../../iot-develop/concepts-modeling-guide.md)를 참조하세요.

모든 디바이스 템플릿에서 구성 요소를 사용하는 것은 아닙니다. 다음 스크린샷은 모든 기능이 **루트 구성 요소** 라는 단일 인터페이스에 정의된 간단한 [자동 온도 조절기](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-2.json)의 디바이스 템플릿을 보여 줍니다.

:::image type="content" source="media/howto-control-devices-with-rest-api/thermostat-device.png" alt-text="구성 요소가 없는 간단한 자동 온도 조절기 디바이스를 보여 주는 스크린샷":::

다음 스크린샷은 구성 요소를 사용하는 [온도 컨트롤러](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json) 디바이스 템플릿을 보여 줍니다. 온도 컨트롤러에는 두 개의 자동 온도 조절기 구성 요소와 한 개의 디바이스 정보 구성 요소가 있습니다.

:::image type="content" source="media/howto-control-devices-with-rest-api/temperature-controller-device.png" alt-text="두 개의 자동 온도 조절기 구성 요소와 한 개의 디바이스 정보 구성 요소가 있는 온도 컨트롤러 디바이스를 보여 주는 스크린샷":::

IoT Central에서 모듈은 연결된 IoT Edge 디바이스에서 실행되는 IoT Edge 모듈을 나타냅니다. 모듈에는 구성 요소를 사용하지 않는 자동 온도 조절기와 같은 간단한 모델이 있을 수 있습니다. 모듈은 구성 요소를 사용하여 더 복잡한 기능 세트를 구성할 수도 있습니다. 다음 스크린샷은 모듈을 사용하는 디바이스 템플릿의 예제를 보여 줍니다. 환경 센서 디바이스에는 `SimulatedTemperatureSensor`라는 모듈과 `management`라는 상속된 인터페이스가 있습니다.

:::image type="content" source="media/howto-control-devices-with-rest-api/environmental-sensor-device.png" alt-text="모듈이 있는 환경 센서 디바이스를 보여 주는 스크린샷":::

## <a name="get-a-device-component"></a>디바이스 구성 요소 가져오기

`temperature-controller-01`이라는 디바이스에서 구성 요소를 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다. `value` 배열에는 각 디바이스 구성 요소의 세부 정보가 포함됩니다.

```json
{
  "value": [
    {
      "@type": "Component",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type": "Component",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

## <a name="get-a-device-module"></a>디바이스 모듈 가져오기

`environmental-sensor-01`이라는 연결된 IoT Edge 디바이스에서 실행되는 모듈 목록을 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다. 모듈 배열에는 기본 제공 `$edgeAgent` 및 `$edgeHub` 모듈이 아닌 IoT Edge 디바이스에서 실행되는 사용자 지정 모듈만 포함됩니다.

```json
{
  "value": [
    {
      "@type": [
        "Relationship",
        "EdgeModule"
      ],
      "name": "SimulatedTemperatureSensor",
      "displayName": "SimulatedTemperatureSensor"
    }
  ]
}
```

`SimulatedTemperatureSensor`라는 모듈의 구성 요소 목록을 검색하려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules?api-version=1.0
```

## <a name="read-telemetry"></a>원격 분석 읽기

구성 요소를 사용하지 않는 디바이스에서 마지막으로 알려진 원격 분석 값을 검색하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `thermostat-01`이라고 하고 원격 분석을 `temperature`라고 합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/telemetry/temperature?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "timestamp": "2021-03-24T12:33:15.223Z",
  "value": 40.10993804456927
}
```

구성 요소를 사용하는 디바이스에서 마지막으로 알려진 원격 분석 값을 검색하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `temperature-controller-01`, 구성 요소를 `thermostat2`, 원격 분석을 `temperature`라고 합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/telemetry/temperature?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "timestamp": "2021-03-24T12:43:44.968Z",
  "value": 70.29168040339141
}
```

디바이스가 IoT Edge 디바이스인 경우 다음 요청을 사용하여 모듈에서 마지막으로 알려진 원격 분석 값을 검색합니다. 이 예제에서는 `environmental-sensor-01`이라는 디바이스를 `SimulatedTemperatureSensor`라는 모듈과 `ambient`라는 원격 분석과 함께 사용합니다. `ambient` 원격 분석 유형에는 온도 값과 습도 값이 있습니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/telemetry/ambient?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "timestamp": "2021-03-25T15:44:34.955Z",
  "value": {
    "temperature": 21.18032378129676,
    "humidity": 25
  }
}
```

> [!TIP]
> 모듈의 구성 요소에서 원격 분석에 액세스하려면 `/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/telemetry/{telemetryName}`을 사용합니다.

## <a name="read-properties"></a>속성 읽기

구성 요소를 사용하지 않는 디바이스에서 속성 값을 검색하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `thermostat-01`이라고 합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다. 디바이스가 단일 속성 값을 보고하는 것을 보여 줍니다.

```json
{
  "maxTempSinceLastReboot": 93.95907131817654,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T12:47:46.7571438Z"
    }
  }
}
```

모든 구성 요소에서 속성 값을 검색하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `temperature-controller-01`이라고 합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/properties?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "serialNumber": "Explicabo animi nihil qui facere sit explicabo nisi.",
  "$metadata": {
    "serialNumber": {
      "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
    }
  },
  "thermostat1": {
    "maxTempSinceLastReboot": 79.7290121339184,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "thermostat2": {
    "maxTempSinceLastReboot": 54.214860556320424,
    "$metadata": {
      "maxTempSinceLastReboot": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    }
  },
  "deviceInformation": {
    "manufacturer": "Eveniet culpa sed sit omnis.",
    "$metadata": {
      "manufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "model": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "swVersion": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "osName": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorArchitecture": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "processorManufacturer": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalStorage": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      },
      "totalMemory": {
        "lastUpdateTime": "2021-03-24T13:58:52.5999859Z"
      }
    },
    "model": "Necessitatibus id ab dolores vel eligendi fuga.",
    "swVersion": "Ut minus ipsum ut omnis est asperiores harum.",
    "osName": "Atque sit omnis eum sapiente eum tenetur est dolor.",
    "processorArchitecture": "Ratione enim dolor iste iure.",
    "processorManufacturer": "Aliquam eligendi sit ipsa.",
    "totalStorage": 36.02825898541592,
    "totalMemory": 55.442695395750505
  }
}
```

개별 구성 요소에서 속성 값을 검색하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `temperature-controller-01`이라고 하고 구성 요소를 `thermostat2`라고 합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "maxTempSinceLastReboot": 24.445128131004935,
  "$metadata": {
    "maxTempSinceLastReboot": {
      "lastUpdateTime": "2021-03-24T14:03:53.787491Z"
    }
  }
}
```

디바이스가 IoT Edge 디바이스인 경우 다음 요청을 사용하여 모듈에서 속성 값을 검색합니다. 이 예제에서는 `environmental-sensor-01`이라는 디바이스를 `SimulatedTemperatureSensor`라는 모듈과 함께 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "$metadata": {
    "SendData": {
      "desiredValue": true,
      "desiredVersion": 1
    },
    "SendInterval": {
      "desiredValue": 10,
      "desiredVersion": 1
    }
  }
}
```

> [!TIP]
> 모듈의 구성 요소에서 속성에 액세스하려면 `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties`를 사용합니다.

## <a name="write-properties"></a>속성 쓰기

일부 속성은 쓰기가 가능합니다. 예를 들어 자동 온도 조절기 모델에서 `targetTemperature` 속성은 쓰기 가능한 속성입니다.

구성 요소를 사용하지 않는 디바이스에 개별 속성 값을 쓰려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `thermostat-01`이라고 합니다.

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/properties?api-version=1.0
```

요청 본문은 다음 예제와 같습니다.

```json
{
  "targetTemperature": 65.5
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> 디바이스의 모든 속성을 업데이트하려면 `PATCH` 대신 `PUT`를 사용합니다.

구성 요소를 사용하는 디바이스에 개별 속성 값을 쓰려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `temperature-controller-01`이라고 하고 구성 요소를 `thermostat2`라고 합니다.

```http
PATCH https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/properties?api-version=1.0
```

요청 본문은 다음 예제와 같습니다.

```json
{
  "targetTemperature": 65.5
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 65.5
    }
  }
}
```

> [!TIP]
> 구성 요소의 모든 속성을 업데이트하려면 `PATCH` 대신 `PUT`를 사용합니다.

디바이스가 IoT Edge 디바이스인 경우 다음 요청을 사용하여 개별 속성 값을 모듈에 씁니다. 이 예제에서는 `environmental-sensor-01`이라는 디바이스를 `SimulatedTemperatureSensor`라는 모듈과 `SendInterval`이라는 속성과 함께 사용합니다.

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/devices/environmental-sensor-01/modules/SimulatedTemperatureSensor/properties?api-version=1.0
```

요청 본문은 다음 예제와 같습니다.

```json
{
  "SendInterval": 20
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "$metadata": {
    "SendInterval": {
      "desiredValue": 20
    }
  }
}
```

> [!TIP]
> 모듈의 모든 속성을 업데이트하려면 `PATCH` 대신 `PUT`를 사용합니다.

### <a name="update-module-properties"></a>모듈 속성 업데이트

IoT Edge 디바이스를 사용하는 경우 다음 요청을 사용하여 모듈에서 속성 값을 검색합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/properties?api-version=1.0
```

IoT Edge 디바이스를 사용하는 경우 다음 요청을 사용하여 모듈의 구성 요소에서 속성 값을 검색합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/{deviceId}/modules/{moduleName}/components/{componentName}/properties?api-version=1.0
```

## <a name="call-commands"></a>명령 호출

REST API 사용하여 디바이스 명령을 호출하고 디바이스 기록을 검색할 수 있습니다.

구성 요소를 사용하지 않는 디바이스에서 명령을 호출하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `thermostat-01`이라고 하고 명령을 `getMaxMinReport`라고 합니다.

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

요청 본문은 다음 예제와 같습니다.

```json
{
  "since": "2021-03-24T12:55:20.789Z"
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "response": {
    "maxTemp": 21.002000799562367,
    "minTemp": 73.09674605264892,
    "avgTemp": 59.54553991653756,
    "startTime": "2022-02-28T15:02:56.789Z",
    "endTime": "2021-05-05T03:50:56.412Z"
  },
  "responseCode": 200
}
```

이 명령에 대한 기록을 보려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/thermostat-01/commands/getMaxMinReport?api-version=1.0
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "value": [
    {
      "response": {
        "maxTemp": 71.43744908819954,
        "minTemp": 51.29986610160005,
        "avgTemp": 39.577384387771744,
        "startTime": "2021-06-20T00:38:17.620Z",
        "endTime": "2022-01-07T22:30:41.104Z"
      },
      "responseCode": 200
    }
  ]
}
```

구성 요소를 사용하는 디바이스에서 명령을 호출하려면 다음 요청을 사용합니다. 이 예제에서는 디바이스를 `temperature-controller-01`, 구성 요소를 `thermostat2`, 명령을 `getMaxMinReport`라고 합니다.

```http
POST https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

요청 페이로드 및 응답의 형식은 구성 요소를 사용하지 않는 디바이스의 형식과 동일합니다.

이 명령에 대한 기록을 보려면 다음 요청을 사용합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/devices/temperature-controller-01/components/thermostat2/commands/getMaxMinReport?api-version=1.0
```

> [!TIP]
> 모듈의 구성 요소에서 명령을 호출하려면 `/devices/{deviceId}/modules/{moduleName}/components/{componentName}/commands/{commandName}`를 사용합니다.

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용하여 디바이스를 제어하는 방법을 알아봤으므로, 다음 단계에서는 [IoT Central REST API를 사용하여 작업을 만들고 관리하는 방법](howto-manage-jobs-with-rest-api.md)을 알아보는 것이 좋습니다.
