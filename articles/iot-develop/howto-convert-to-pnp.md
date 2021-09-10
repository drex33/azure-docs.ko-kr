---
title: IoT 플러그 앤 플레이를 사용하도록 기존 디바이스 변환 | Microsoft Docs
description: 이 문서에서는 디바이스 모델을 만든 다음, 디바이스가 연결될 때 모델 ID를 보내 기존 디바이스 코드를 IoT 플러그 앤 플레이로 작동하도록 변환하는 방법을 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2021
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 225b999053824513e41d29d80a47a596fdedf991
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442572"
---
# <a name="how-to-convert-an-existing-device-to-be-an-iot-plug-and-play-device"></a>기존 디바이스를 IoT 플러그 앤 플레이 디바이스로 변환하는 방법

이 문서에서는 기존 디바이스를 IoT 플러그 앤 플레이 디바이스로 변환하기 위해 수행해야 하는 단계를 간략하게 설명합니다. 모든 IoT 플러그 앤 플레이 디바이스에 필요한 모델을 만드는 방법에 대해 설명하고 디바이스가 IoT 플러그 앤 플레이 디바이스로 작동할 수 있도록 하는 데 필요한 코드를 설명합니다.

코드 샘플의 경우 이 문서는 MQTT 라이브러리를 사용하여 IoT hub에 연결하는 C 코드를 보여 줍니다. 이 문서에서 설명하는 변경 내용을 다른 언어 및 SDK를 사용하여 구현된 디바이스에 적용할 수 있습니다.

기존 디바이스를 IoT 플러그 앤 플레이 디바이스로 변환하려면 다음을 수행합니다.

1. 디바이스 코드를 검토하여 구현하는 원격 분석, 속성 및 명령을 이해합니다.
1. 디바이스에서 구현하는 원격 분석, 속성 및 명령을 설명하는 모델을 만듭니다.
1. 서비스에 연결할 때 모델 ID를 알리도록 디바이스 코드를 수정합니다.

## <a name="review-your-device-code"></a>디바이스 코드 검토

디바이스에 대한 모델을 만들기 전에 디바이스의 기존 기능을 이해해야 합니다.

- 디바이스가 정기적으로 보내는 원격 분석
- 디바이스가 서비스와 동기화하는 읽기 전용 및 쓰기 가능한 속성
- 디바이스가 응답하는 서비스에서 호출되는 명령

예를 들어 다양한 디바이스 기능을 구현하는 다음 디바이스 코드 조각을 검토합니다. 이러한 예제는 이전에 제공된 [PnPMQTTWin32\](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32-Before)의 샘플을 기준으로 합니다.

다음 코드 조각에서는 온도 원격 분석을 보내는 디바이스를 보여 줍니다.

```c
#define TOPIC "devices/" DEVICEID "/messages/events/"

// ...

void Thermostat_SendCurrentTemperature()
{
  char msg[] = "{\"temperature\":25.6}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, TOPIC, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

원격 분석 필드의 이름은 `temperature`이고 해당 형식은 float 또는 double입니다. 이 원격 분석 형식에 대한 모델 정의는 다음 JSON과 같습니다. 자세히 알아보려면 아래의 [모델 디자인](#design-a-model)을 참조하세요.

```json
{
  "@type": [
    "Telemetry"
  ],
  "name": "temperature",
  "displayName": "Temperature",
  "description": "Temperature in degrees Celsius.",
  "schema": "double"
}
```

다음 코드 조각은 속성 값을 보고하는 디바이스를 보여 줍니다.

```c
#define DEVICETWIN_MESSAGE_PATCH "$iothub/twin/PATCH/properties/reported/?$rid=patch_temp"

static void SendMaxTemperatureSinceReboot()
{
  char msg[] = "{\"maxTempSinceLastReboot\": 42.500}";
  int msgId = rand();
  int rc = mosquitto_publish(mosq, &msgId, DEVICETWIN_MESSAGE_PATCH, sizeof(msg) - 1, msg, 1, true);
  if (rc != MOSQ_ERR_SUCCESS)
  {
    printf("Error: %s\r\n", mosquitto_strerror(rc));
  }
}
```

속성의 이름은 `maxTempSinceLastReboot`이고 해당 형식은 float 또는 double입니다. 이 속성은 디바이스에서 보고되며, 디바이스는 서비스에서 이 값에 대한 업데이트를 받지 못합니다. 이 속성에 대한 모델 정의는 다음 JSON처럼 보입니다. 자세히 알아보려면 아래의 [모델 디자인](#design-a-model)을 참조하세요.

```json
{
  "@type": [
    "Property"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

다음 코드 조각은 서비스의 메시지에 응답하는 디바이스를 보여 줍니다.

```c
void message_callback(struct mosquitto* mosq, void* obj, const struct mosquitto_message* message)
{
  printf("Message received: %s payload: %s \r\n", message->topic, (char*)message->payload);
  
  if (strncmp(message->topic, "$iothub/methods/POST/getMaxMinReport/?$rid=1",37) == 0)
  {
    char* pch;
    char* context;
    int msgId = 0;
    pch = strtok_s((char*)message->topic, "=",&context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char * pEnd;
        msgId = strtol(pch,&pEnd,16 );
      }
    }
    char topic[64];
    sprintf_s(topic, "$iothub/methods/res/200/?$rid=%d", msgId);
    char msg[] = "{\"maxTemp\":83.51,\"minTemp\":77.68}";
    int rc = mosquitto_publish(mosq, &msgId, topic, sizeof(msg) - 1, msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }

  if (strncmp(message->topic, "$iothub/twin/PATCH/properties/desired/?$version=1", 38) == 0)
  {
    char* pch;
    char* context;
    int version = 0; 
    pch = strtok_s((char*)message->topic, "=", &context);
    while (pch != NULL)
    {
      pch = strtok_s(NULL, "=", &context);
      if (pch != NULL) {
        char* pEnd;
        version = strtol(pch, &pEnd, 10);
      }
    }
    // To do: Parse payload and extract target value
    char msg[128];
    int value = 46;
    sprintf_s(msg, "{\"targetTemperature\":{\"value\":%d,\"ac\":200,\"av\":%d,\"ad\":\"success\"}}", value, version);
    int rc = mosquitto_publish(mosq, &version, DEVICETWIN_MESSAGE_PATCH, strlen(msg), msg, 1, true);
    if (rc != MOSQ_ERR_SUCCESS)
    {
      printf("Error: %s\r\n", mosquitto_strerror(rc));
    }
    delete pch;
  }
}
```

`$iothub/methods/POST/getMaxMinReport/` 토픽은 서비스에서 `getMaxMinReport`라는 명령에 대한 요청을 받으며, 이 요청에는 명령 매개 변수를 사용하는 페이로드가 포함될 수 있습니다. 디바이스는 `maxTemp` 및 `minTemp` 값을 포함하는 페이로드를 사용하여 응답을 보냅니다.

`$iothub/twin/PATCH/properties/desired/` 토픽은 서비스에서 속성 업데이트를 수신합니다. 이 예제에서는 속성 업데이트가 `targetTemperature` 속성에 대한 것이라고 가정합니다. `{\"targetTemperature\":{\"value\":46,\"ac\":200,\"av\":12,\"ad\":\"success\"}}`와 같은 승인으로 응답합니다.

요약하자면 이 샘플은 다음과 같은 기능을 구현합니다.

| Name                   | 기능 유형   | 세부 정보 |
| ---------------------- | ----------------- | ------- |
| 온도            | 원격 분석         | 데이터 형식이 double이라고 가정합니다. |
| maxTempSinceLastReboot | 속성          | 데이터 형식이 double이라고 가정합니다. |
| targetTemperature      | 쓰기 가능 속성 | 데이터 형식이 Integer입니다. |
| getMaxMinReport        | 명령           | double 형식의 `maxTemp` 및 `minTemp` 필드가 있는 JSON을 반환합니다. |

## <a name="design-a-model"></a>모델 디자인

모든 IoT 플러그 앤 플레이 디바이스에는 디바이스의 특징과 기능을 설명하는 모델이 있습니다. 이 모델은 [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)을 사용하여 디바이스 기능을 설명합니다.

디바이스의 기존 기능을 매핑하는 단순 모델의 경우 *원격 분석*, *속성* 및 *명령* DTDL 요소를 사용합니다.

이전 섹션에 설명된 샘플의 DTDL 모델은 다음 예제와 같습니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:ConvertSample;1",
  "@type": "Interface",
  "displayName": "Simple device",
  "description": "Example that shows model for simple device converted to act as an IoT Plug and Play device.",
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
      "description": "This command returns the max and min temperature.",
      "request": {
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
            }
          ]
        }
      }
    }
  ]
}
```

이 모델에서는 다음이 적용됩니다.

- `name` 및 `schema` 값은 디바이스에서 보내고 받는 데이터에 매핑됩니다.
- 모든 기능은 단일 인터페이스로 그룹화됩니다.
- `@type` 필드는 **속성** 및 **명령** 과 같은 DTDL 유형을 식별합니다.
- `unit`, `displayName` 및 `description`과 같은 필드는 사용할 서비스에 대한 추가 정보를 제공합니다. 예를 들어, IoT Central은 디바이스 대시보드에 데이터를 표시할 때 이러한 값을 사용합니다.

자세히 알아보려면 [ 플러그 앤 플레이 규칙](concepts-convention.md) 및 [ 플러그 앤 플레이 모델링 가이드](concepts-modeling-guide.md)를 참조하세요.

## <a name="update-the-code"></a>코드 업데이트

디바이스가 이미 IoT Hub 또는 IoT Central을 사용하고 있는 경우 원격 분석, 속성 및 명령 기능의 구현을 변경할 필요가 없습니다. 디바이스가 IoT 플러그 앤 플레이 규칙을 따르도록 하려면 생성된 모델의 ID를 알리도록 디바이스가 서비스에 연결하는 방식을 수정합니다. 그러면 서비스는 이 모델을 사용하여 디바이스 기능을 이해할 수 있습니다. 예를 들어, IoT Central은 모델 ID를 사용하여 리포지토리에서 모델을 자동으로 검색하고 디바이스에 대한 디바이스 템플릿을 생성할 수 있습니다.

IoT 디바이스는 DPS(Device Provisioning Service)를 통해 또는 연결 문자열을 사용하여 직접 IoT 서비스에 연결합니다.

디바이스에서 DPS를 사용하여 연결하는 경우 디바이스를 등록할 때 보내는 페이로드에 모델 ID를 포함합니다. 이전에 표시된 예제 모델의 경우 페이로드는 다음과 같습니다.

```json
{
  "modelId" : "dtmi:com:example:ConvertSample;1"
}
```

자세히 알아보려면 [런타임 등록 - 디바이스 등록](/rest/api/iot-dps/device/runtime-registration/register-device)을 참조하세요.

디바이스에서 DPS를 사용하여 연결 문자열에 직접 연결하거나 코드에서 IoT Hub에 연결할 때 모델 ID를 포함합니다. 예를 들면 다음과 같습니다.

```c
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:ConvertSample;1"

// ...

mosquitto_username_pw_set(mosq, USERNAME, PWD);

// ...

rc = mosquitto_connect(mosq, HOST, PORT, 10);
```

## <a name="next-steps"></a>다음 단계

지금까지 기존 디바이스를 IoT 플러그 앤 플레이 디바이스로 변환하는 방법을 배웠으므로 이제 권장되는 다음 단계는 [IoT 플러그 앤 플레이 모델링 가이드](concepts-modeling-guide.md)를 참조하세요.
