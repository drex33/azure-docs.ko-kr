---
title: Azure IoT Central 솔루션에서 속성 사용
description: Azure IoT Central 솔루션에서 읽기 전용 및 쓰기 가능한 속성을 사용하는 방법에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 09/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 07038a98b3be23295599febe0af16cb081facd5d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797331"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Azure IoT Central 솔루션에서 속성 사용

이 방법 가이드에서는 Azure IoT Central 애플리케이션의 디바이스 템플릿에 정의된 디바이스 속성을 사용하는 방법을 보여줍니다.

속성은 특정 시간 값을 나타냅니다. 예를 들어 디바이스에서 속성을 사용하여 도달하려는 목표 온도를 보고할 수 있습니다. 기본적으로 디바이스 속성은 IoT Central에서 읽기 전용입니다. 쓰기 가능한 속성을 사용하면 디바이스와 Azure IoT Central 애플리케이션 간의 상태를 동기화할 수 있습니다.

Azure IoT Central 애플리케이션에서 클라우드 속성을 정의할 수도 있습니다. 클라우드 속성 값은 디바이스와 교환되지 않으며 이 문서에서 다루는 주제의 범위를 벗어납니다.

## <a name="define-your-properties"></a>속성 정의

속성은 디바이스의 상태를 나타내는 데이터 필드입니다. 속성을 사용하여 디바이스의 켜기/끄기 상태와 같은 디바이스의 지속형 상태를 나타냅니다. 또한 속성은 디바이스의 소프트웨어 버전 같은 기본 디바이스 속성을 나타낼 수도 있습니다. 속성을 읽기 전용 또는 쓰기 가능으로 선언합니다.

다음 스크린샷은 Azure IoT Central 애플리케이션의 속성 정의를 보여 줍니다.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Azure IoT Central 애플리케이션의 속성 정의를 보여 주는 스크린샷":::

다음 표에는 속성 기능의 구성 설정이 나와 있습니다.

| 필드           | 설명                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 표시 이름    | 대시보드 타일 및 디바이스 양식에 사용되는 속성 값의 표시 이름입니다.                                                                                                                                                              |
| 속성            | 속성의 이름입니다. Azure IoT Central은 표시 이름에서 이 필드의 값을 생성하지만, 필요한 경우 사용자 고유의 값을 선택할 수 있습니다. 이 필드는 영숫자여야 합니다.  디바이스 코드는 이 **이름** 값을 사용합니다.           |
| 기능 유형 | 속성입니다.                                                                                                                                                                                                                          |
| 의미 체계 유형   | 속성의 의미 체계 유형입니다(예: 온도, 상태 또는 이벤트). 의미 체계 유형을 선택하면 다음 필드 중에서 사용 가능한 필드가 결정됩니다.                                                                       |
| 스키마          | 속성 데이터 형식입니다(예: double, string 또는 vector). 사용 가능한 선택 항목은 의미 체계 유형에 따라 결정됩니다. 이벤트 및 상태 의미 체계 유형에는 스키마를 사용할 수 없습니다.                                               |
| 쓰기 가능       | 속성이 쓰기 가능이 아닌 경우 디바이스에서 속성 값을 Azure IoT Central에 보고할 수 있습니다. 속성이 쓰기 가능인 경우 디바이스에서 속성 값을 Azure IoT Central에 보고할 수 있습니다. 그런 다음, Azure IoT Central에서 디바이스에 속성 업데이트를 보낼 수 있습니다. |
| 심각도        | 이벤트 의미 체계 유형에만 사용할 수 있습니다. 심각도는 **오류**, **정보** 또는 **경고** 입니다.                                                                                                                         |
| 상태 값    | 상태 의미 체계 유형에만 사용할 수 있습니다. 가능한 상태 값을 정의하며, 각각에는 표시 이름, 이름, 열거형 형식 및 값이 있습니다.                                                                                   |
| 단위            | 속성 값의 단위입니다(예: **mph**, **%** 또는 **&deg;C**).                                                                                                                                                              |
| 표시 단위    | 대시보드 타일 및 디바이스 양식에서 사용할 표시 단위입니다.                                                                                                                                                                                    |
| 의견         | 속성 기능에 대한 주석입니다.                                                                                                                                                                                        |
| Description     | 속성 기능에 대한 설명입니다.                                                                                                                                                                                          |

다음과 같이 디바이스 템플릿의 인터페이스에서 속성을 정의할 수도 있습니다.

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

이 예제에서는 두 개의 속성을 보여 줍니다. 이러한 속성은 UI의 속성 정의와 관련되어 있습니다.

* `@type`은 `Property` 기능의 유형을 지정합니다. 위의 예제에서는 두 속성에 대한 `Temperature` 의미 체계 형식을 보여 줍니다.
* 속성에 대한 `name`입니다.
* `schema`는 속성에 대한 데이터 형식을 지정합니다. 이 값은 double, integer, Boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식 및 맵도 지원됩니다.
* `writable` 기본적으로 속성은 읽기 전용입니다. 이 필드를 사용하여 속성을 쓰기 가능으로 표시할 수 있습니다.

표시 이름, 설명 등의 선택적 필드를 사용하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

속성을 만들 때 **개체**, **열거형** 등의 복합 스키마 형식을 지정할 수 있습니다.

![기능을 추가하는 방법을 보여 주는 스크린샷](./media/howto-use-properties/property.png)

**개체** 등의 복합 **스키마** 를 선택하는 경우에도 개체를 정의해야 합니다.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="개체를 정의하는 방법을 보여 주는 스크린샷":::

다음 코드에서는 개체 속성 형식의 정의를 보여 줍니다. 이 개체에는 string과 integer 형식의 두 필드가 있습니다.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
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

## <a name="implement-read-only-properties"></a>읽기 전용 속성 구현

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성을 사용하면 디바이스 보고서 속성 값이 Azure IoT Central 애플리케이션으로 업데이트됩니다. Azure IoT Central 애플리케이션에서는 읽기 전용 속성 값을 설정할 수 없습니다.

Azure IoT Central은 디바이스 쌍을 사용하여 디바이스와 Azure IoT Central 애플리케이션 간에 속성 값을 동기화합니다. 디바이스 속성 값은 디바이스 쌍 reported 속성을 사용합니다. 자세한 내용은 [디바이스 쌍](../../iot-hub/tutorial-device-twins.md)을 참조하세요.

디바이스 모델의 다음 코드 조각은 읽기 전용 속성 유형에 대한 정의를 보여 줍니다.

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

디바이스에서 속성 업데이트를 JSON 페이로드로 보냅니다. 자세한 내용은 [페이로드](./concepts-telemetry-properties-commands.md)를 참조하세요.

Azure IoT 디바이스 SDK를 사용하여 Azure IoT Central 애플리케이션에 속성 업데이트를 보낼 수 있습니다.

다음 함수를 사용하여 Azure IoT Central 애플리케이션에 디바이스 쌍 속성을 보낼 수 있습니다.

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

이 문서에서는 편의를 위해 Node.js를 사용합니다. 다른 언어 예제에 대해서는 [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md) 자습서를 참조하세요.

Azure IoT Central 애플리케이션의 다음 보기에서는 사용자가 확인할 수 있는 속성을 보여 줍니다. 이 보기에서는 **디바이스 모델** 속성을 _읽기 전용 디바이스 속성_ 으로 자동으로 만듭니다.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="읽기 전용 속성의 보기를 보여 주는 스크린샷":::

## <a name="implement-writable-properties"></a>쓰기 가능한 속성 구현

쓰기 가능한 속성은 Azure IoT Central 애플리케이션의 양식에서 운영자에 의해 설정됩니다. Azure IoT Central은 디바이스에 속성을 보냅니다. Azure IoT Central에는 디바이스의 승인이 필요합니다.

디바이스 모델의 다음 코드 조각은 쓰기 가능한 속성 유형에 대한 정의를 보여 줍니다.

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

디바이스에서 응답하는 쓰기 가능한 속성을 정의하고 처리하려면 다음 코드를 사용하면 됩니다.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

응답 메시지에는 `ac` 및 `av` 필드가 포함되어야 합니다. `ad` 필드는 선택 사항입니다. 예제는 다음 코드 조각을 참조하세요.

* `ac`는 다음 표의 값을 사용하는 숫자 필드입니다.
* `av`는 디바이스에 전송된 버전 번호입니다.
* `ad`는 옵션 문자열 설명입니다.

| 값 | 레이블 | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 속성 변경 작업이 성공적으로 완료되었습니다. |
| `'ac': 202` 또는 `'ac': 201` | Pending | 속성 변경 작업이 보류 중이거나 진행 중입니다. |
| `'ac': 4xx` | Error | 요청된 속성 변경이 올바르지 않거나 오류가 발생했습니다. |
| `'ac': 5xx` | Error | 디바이스에서 요청된 변경을 처리하는 동안 예기치 않은 오류가 발생했습니다. |

디바이스 쌍에 대한 자세한 내용은 [백 엔드 서비스에서 디바이스 구성](../../iot-hub/tutorial-device-twins.md)을 참조하세요.

운영자가 Azure IoT Central 애플리케이션에서 쓰기 가능한 속성을 설정하면 애플리케이션에서 디바이스 쌍 desired 속성을 사용하여 디바이스에 값을 보냅니다. 그런 다음, 디바이스가 디바이스 쌍 reported 속성을 사용하여 응답합니다. Azure IoT Central은 reported 속성 값을 받으면 속성 보기를 **수락됨** 상태로 업데이트합니다.

다음 보기는 쓰기 가능한 속성을 보여 줍니다. 값을 입력하고 **저장** 을 선택하면 초기 상태는 **보류 중** 입니다. 디바이스가 변경 내용을 수락하면 상태가 **수락됨** 으로 바뀝니다.

![보류 중 상태를 보여 주는 스크린샷](./media/howto-use-properties/status-pending.png)

![수락됨 속성을 보여 주는 스크린샷](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 속성을 사용하는 방법을 알아보았으니 이제 다음을 참조하세요.

* [Payloads](concepts-telemetry-properties-commands.md)
* [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)