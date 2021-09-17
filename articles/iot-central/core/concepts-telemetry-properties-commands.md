---
title: Azure IoT Central에서 원격 분석, 속성 및 명령 페이로드 | Microsoft Docs
description: Azure IoT Central 디바이스 템플릿을 사용하면 디바이스에서 구현해야 하는 원격 분석, 속성 및 명령을 지정할 수 있습니다. 디바이스가 IoT Central과 교환할 수 있는 데이터의 형식을 이해합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 3c30662a9ca85265f43e78ad9dd3f33c70c167a5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479943"
---
# <a name="telemetry-property-and-command-payloads"></a>원격 분석, 속성 및 명령 페이로드

Azure IoT Central의 디바이스 템플릿은 다음을 정의하는 청사진입니다.

* 디바이스가 IoT Central로 전송하는 원격 분석.
* 디바이스가 IoT Central과 동기화하는 속성.
* IoT Central이 디바이스에서 호출하는 명령.

이 문서에서는 디바이스 템플릿에서 정의된 원격 분석, 속성 및 명령에 대해 디바이스에서 보내고 받는 JSON 페이로드를 설명합니다.

이 문서에서는 가능한 모든 유형의 원격 분석, 속성 및 명령 페이로드를 설명하지는 않지만, 예제에서는 모든 주요 유형을 설명합니다.

각 예제에서는 디바이스가 IoT Central 애플리케이션과 상호 작용하는 방법을 설명하기 위해 형식 및 예제 JSON 페이로드를 정의하는 디바이스 모델의 조각을 보여 줍니다.

> [!NOTE]
> IoT Central은 유효한 모든 JSON을 허용하지만 디바이스 모델의 정의와 일치하는 경우에만 시각화에 사용할 수 있습니다. 정의와 일치하지 않는 데이터를 내보낼 수 있습니다. [Azure에서 IoT 데이터를 대상으로 내보내기](howto-export-data.md)를 참조하세요.

디바이스 모델을 정의하는 JSON 파일은 [DTDL(디지털 트윈 정의 언어) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 사용합니다.

사용 중인 이러한 페이로드 중 일부를 보여 주는 샘플 디바이스 코드는 [클라이언트 애플리케이션 만들기 및 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md) 자습서를 참조하세요.

## <a name="view-raw-data"></a>원시 데이터 보기

IoT Central을 사용하여 디바이스가 애플리케이션에 전송하는 원시 데이터를 볼 수 있습니다. 이 보기는 디바이스에서 전송된 페이로드의 문제를 해결하는 데 유용합니다. 디바이스가 전송하는 원시 데이터를 보려면 다음을 수행합니다.

1. **디바이스** 페이지에서 디바이스로 이동합니다.

1. **원시 데이터** 탭을 선택합니다.

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="원시 데이터 보기":::

    이 보기에서 표시할 열을 선택하고 표시할 시간 범위를 설정할 수 있습니다. **모델링되지 않은 데이터** 열에는 디바이스 템플릿의 속성 또는 원격 분석 정의와 일치하지 않는 디바이스 데이터가 표시됩니다.

## <a name="telemetry"></a>원격 분석

### <a name="telemetry-in-components"></a>구성 요소의 원격 분석

원격 분석이 구성 요소에 정의된 경우 디바이스 모델에 정의된 대로 구성 요소 이름과 함께 `$.sub`라는 사용자 지정 메시지 속성을 추가합니다. 자세히 알아보려면 [자습서: 클라이언트 애플리케이션 만들기 및 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)을 참조하세요.

### <a name="primitive-types"></a>기본 형식

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로 스트리밍하는 기본 원격 분석 유형의 예제를 보여 줍니다.

디바이스 모델의 다음 코드 조각은 `boolean` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다.

```json
{ "BooleanTelemetry": true }
```

디바이스 모델의 다음 코드 조각은 `string` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다.

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

디바이스 모델의 다음 코드 조각은 `integer` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다.

```json
{ "IntegerTelemetry": 23 }
```

디바이스 모델의 다음 코드 조각은 `double` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다.

```json
{ "DoubleTelemetry": 56.78 }
```

디바이스 모델의 다음 코드 조각은 `dateTime` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다. `DateTime` 유형은 ISO 8061 형식이어야 합니다.

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

디바이스 모델의 다음 코드 조각은 `duration` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다. 기간은 ISO 8601 형식이어야 합니다.

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로 스트리밍하는 복잡한 원격 분석 유형의 예제를 보여 줍니다.

디바이스 모델의 다음 코드 조각은 `geopoint` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> **geopoint** 스키마 유형은 [디지털 쌍 정의 언어 사양](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)의 일부가 아닙니다. 현재 IoT Central은 이전 버전과의 호환성을 위해 **geopoint** 스키마 형식 및 **location** 의미 체계 형식을 지원합니다.

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다. IoT Central은 맵에 값을 pin으로 표시합니다.

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

디바이스 모델의 다음 코드 조각은 `Enum` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다. 가능한 값은 `0`, `1` 및 `2`이며 IoT Central에서 `Item1`, `Item2` 및 `Item3`으로 표시됩니다.

```json
{ "EnumTelemetry": 1 }
```

디바이스 모델의 다음 코드 조각은 `Object` 원격 분석 유형에 대한 정의를 보여 줍니다. 이 개체에는 `dateTime`, `integer` 및 `Enum` 형식의 세 필드가 있습니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다. `DateTime` 형식은 ISO 8061 규격이어야 합니다. `Property3`에 사용할 수 있는 값은 `0`, `1`이며 IoT Central에 `Item1`, `Item2` 및 `Item3`으로 표시됩니다.

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

디바이스 모델의 다음 코드 조각은 `vector` 원격 분석 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송해야 합니다.

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>이벤트 및 상태 유형

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로 보내는 원격 분석 이벤트 및 상태의 예제를 보여 줍니다.

디바이스 모델의 다음 코드 조각은 `integer` 이벤트 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 이벤트 데이터를 전송해야 합니다.

```json
{ "IntegerEvent": 74 }
```

디바이스 모델의 다음 코드 조각은 `integer` 상태 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

디바이스 클라이언트는 다음 예제와 같이 JSON으로 상태를 전송해야 합니다. 가능한 정수 상태 값은 `1`, `2` 또는 `3`입니다.

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>속성

> [!NOTE]
> 속성의 페이로드 형식은 2020년 7월 14일 이후 생성된 애플리케이션에 적용됩니다.

### <a name="properties-in-components"></a>구성 요소의 속성

속성이 구성 요소에 정의되어 있는 경우 구성 요소 이름에 속성을 래핑합니다. 다음 예제에서는 `thermostat2` 구성 요소에 `maxTempSinceLastReboot`를 설정합니다. 마커 `__t`는 다음 구성 요소를 나타냅니다.

```json
{
  "thermostat2" : {  
    "__t" : "c",  
    "maxTempSinceLastReboot" : 38.7
    } 
}
```

자세히 알아보려면 [자습서: 클라이언트 애플리케이션 만들기 및 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)을 참조하세요.

### <a name="primitive-types"></a>기본 형식

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로 보내는 기본 속성 유형의 예제를 보여 줍니다.

디바이스 모델의 다음 코드 조각은 `boolean` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{ "BooleanProperty": false }
```

디바이스 모델의 다음 코드 조각은 `long` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{ "LongProperty": 439 }
```

디바이스 모델의 다음 코드 조각은 `date` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다. `Date` 형식은 ISO 8061 규격이어야 합니다.

```json
{ "DateProperty": "2020-05-17" }
```

디바이스 모델의 다음 코드 조각은 `duration` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다. 기간은 ISO 8601 기간 규격이어야 합니다.

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

디바이스 모델의 다음 코드 조각은 `float` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{ "FloatProperty": 1.9 }
```

디바이스 모델의 다음 코드 조각은 `string` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로 보내는 복잡한 속성 유형의 예제를 보여 줍니다.

디바이스 모델의 다음 코드 조각은 `geopoint` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> **geopoint** 스키마 유형은 [디지털 쌍 정의 언어 사양](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)의 일부가 아닙니다. 현재 IoT Central은 이전 버전과의 호환성을 위해 **geopoint** 스키마 형식 및 **location** 의미 체계 형식을 지원합니다.

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

디바이스 모델의 다음 코드 조각은 `Enum` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다. 가능한 값은 `0`, `1`이며 IoT Central에서 `Item1`, `Item2` 및 `Item3`으로 표시됩니다.

```json
{ "EnumProperty": 1 }
```

디바이스 모델의 다음 코드 조각은 `Object` 속성 유형에 대한 정의를 보여 줍니다. 이 개체에는 `string` 및 `integer` 형식의 두 필드가 있습니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

디바이스 모델의 다음 코드 조각은 `vector` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

디바이스 클라이언트는 다음 예제와 같은 JSON 페이로드를 디바이스 쌍의 보고된 속성으로 전송해야 합니다.

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writable-property-types"></a>쓰기 가능한 속성 유형

이 섹션에서는 디바이스가 IoT Central 애플리케이션으로부터 받는 쓰기 가능한 속성 유형의 예제를 보여 줍니다.

쓰기 가능한 속성이 구성 요소에 정의되어 있으면 원하는 속성 메시지에 구성 요소 이름이 포함됩니다. 다음 예제에서는 `thermostat2` 구성 요소의 `targetTemperature`를 업데이트하기 위해 디바이스를 요청하는 메시지를 보여 줍니다. 마커 `__t`는 다음 구성 요소를 나타냅니다.

```json
{
  "thermostat2": {
    "targetTemperature": {
      "value": 57
    },
    "__t": "c"
  },
  "$version": 3
}
```

자세히 알아보려면 [자습서: 클라이언트 애플리케이션 만들기 및 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)을 참조하세요.

IoT Central 디바이스에서 쓰기 가능한 속성 업데이트에 대한 응답을 예상합니다. 응답 메시지에는 `ac` 및 `av` 필드가 포함되어야 합니다. `ad` 필드는 선택 사항입니다. 예제는 다음 코드 조각을 참조하세요.

`ac`는 다음 표의 값을 사용하는 숫자 필드입니다.

| 값 | 레이블 | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 속성 변경 작업이 성공적으로 완료되었습니다. |
| `'ac': 202` 또는 `'ac': 201` | 보류 중 | 속성 변경 작업이 보류 중이거나 진행 중입니다. |
| `'ac': 4xx` | Error | 요청된 속성 변경이 올바르지 않거나 오류가 발생했습니다. |
| `'ac': 5xx` | 오류 | 디바이스에서 요청된 변경을 처리하는 동안 예기치 않은 오류가 발생했습니다. |

`av`는 디바이스에 전송된 버전 번호입니다.

`ad`는 옵션 문자열 설명입니다.

디바이스 모델의 다음 코드 조각은 쓰기 가능한 `string` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

디바이스는 IoT Central에서 다음 페이로드를 수신합니다.

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

디바이스는 업데이트를 처리한 후 IoT Central에 다음 JSON 페이로드를 전송해야 합니다. 이 메시지는 IoT Central에서 받은 원래 업데이트의 버전 번호를 포함합니다. IoT Central이 이 메시지를 받으면 UI에서 속성을 **동기화된** 것으로 표시합니다.

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

디바이스 모델의 다음 코드 조각은 쓰기 가능한 `Enum` 속성 유형에 대한 정의를 보여 줍니다.

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

디바이스는 IoT Central에서 다음 페이로드를 수신합니다.

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

디바이스는 업데이트를 처리한 후 IoT Central에 다음 JSON 페이로드를 전송해야 합니다. 이 메시지는 IoT Central에서 받은 원래 업데이트의 버전 번호를 포함합니다. IoT Central이 이 메시지를 받으면 UI에서 속성을 **동기화된** 것으로 표시합니다.

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>명령

명령이 구성 요소에 정의되어 있으면 디바이스에서 수신하는 명령 이름에 구성 요소 이름이 포함됩니다. 예를 들어 명령이 `getMaxMinReport`이고, 구성 요소가 `thermostat2`이면 디바이스에서 `thermostat2*getMaxMinReport`라는 명령을 실행하라는 요청을 받습니다.

디바이스 모델의 다음 코드 조각은 매개 변수가 없고 디바이스에서 아무것도 반환하지 않는 명령의 정의를 보여 줍니다.

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

디바이스는 요청에서 빈 페이로드를 받고 성공 여부를 나타내는 `200` HTTP 응답 코드와 함께 응답에 빈 페이로드를 반환해야 합니다.

디바이스 모델의 다음 코드 조각은 정수 매개 변수가 있고 디바이스에서 정수 값을 반환하는 명령의 정의를 보여 줍니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

디바이스는 요청 페이로드로 정수 값을 받습니다. 디바이스는 성공 여부를 나타내는 `200` HTTP 응답 코드를 사용하여 응답 페이로드로 정수 값을 반환해야 합니다.

디바이스 모델의 다음 코드 조각은 개체 매개 변수가 있고 디바이스에서 개체를 반환하는 명령의 정의를 보여 줍니다. 이 예제에서는 두 개체 모두에 정수 및 문자열 필드가 있습니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

다음 코드 조각은 디바이스에 전송된 예제 요청 페이로드를 보여 줍니다.

```json
{ "Field1": 56, "Field2": "A string value" }
```

다음 코드 조각은 디바이스로부터 전송된 예제 응답 페이로드를 보여 줍니다. `200` HTTP 응답 코드를 사용하여 성공 여부를 표시합니다.

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>장기 실행 명령

디바이스 모델의 다음 코드 조각은 명령의 정의를 보여 줍니다. 명령에는 정수 매개 변수가 있으며 디바이스에서 정수 값을 반환해야 합니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

디바이스는 요청 페이로드로 정수 값을 받습니다. 디바이스에서 이 명령을 처리하는 데 시간이 필요한 경우 디바이스에서 처리 요청을 수락했음을 나타내는 `202` HTTP 응답 코드와 함께 빈 응답 페이로드를 반환해야 합니다.

디바이스에서 요청 처리가 완료되면 다음 예제와 같이 IoT Central에 속성을 보내야 합니다. 속성 이름은 명령 이름과 동일해야 합니다.

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>오프라인 명령

IoT Central 웹 UI에서 명령에 대한 **오프라인 상태인 경우 큐에 넣기** 옵션을 선택할 수 있습니다. 오프라인 명령은 디바이스가 연결되는 즉시 제공되는 솔루션에서 디바이스에 대한 단방향 알림입니다. 오프라인 명령은 요청 매개 변수를 포함할 수 있지만 응답을 반환하지 않습니다.

**오프라인 상태인 경우 큐에 넣기** 설정은 디바이스 템플릿에서 모델 또는 인터페이스를 내보내는 경우에는 포함되지 않습니다. 내보낸 모델 또는 인터페이스 JSON은 명령이 오프라인 명령임을 알 수 없습니다.

오프라인 명령은 [IoT Hub 클라우드-디바이스 메시지](../../iot-hub/iot-hub-devguide-messages-c2d.md)를 사용하여 명령 및 페이로드를 디바이스로 보냅니다.

디바이스가 수신하는 메시지의 페이로드는 매개 변수의 원시 값입니다. `method-name`이라는 사용자 지정 속성은 IoT Central 명령의 이름을 저장합니다. 다음 표에서는 몇 가지 페이로드 예를 보여줍니다.

| IoT Central 요청 스키마 | 디바이스에서 받은 페이로드 예 |
| -------------------------- | ---------------------------------- |
| 요청 매개 변수 없음       | `@`                                |
| Double                     | `1.23`                             |
| String                     | `sample string`                    |
| Object                     | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

디바이스 모델의 다음 코드 조각은 명령의 정의를 보여 줍니다. 명령에는 datetime 필드와 열거형이 포함된 개체 매개 변수가 있습니다.

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 3,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

이전 코드 조각의 명령에 대한 디바이스 템플릿 UI에서 **오프라인인 경우 큐에 넣기** 옵션을 사용하도록 설정하면 디바이스에서 수신하는 메시지에는 다음 속성이 포함됩니다.

| 속성 이름 | 예제 값 |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>다음 단계

이제 디바이스 템플릿에 대해 알아보았으므로 다음 단계는 [Azure IoT Central에 연결](./concepts-get-connected.md)을 읽고 IoT Central에 디바이스를 등록하는 방법 및 IoT Central이 디바이스 연결을 보호하는 방식에 대해 자세히 알아보는 것입니다.
