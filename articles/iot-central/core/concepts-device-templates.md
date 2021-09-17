---
title: Azure IoT Central의 디바이스 템플릿이란? | Microsoft Docs
description: Azure IoT Central 디바이스 템플릿을 사용하면 애플리케이션에 연결된 디바이스의 동작을 지정할 수 있습니다. 디바이스 템플릿은 디바이스에서 구현해야 하는 원격 분석, 속성 및 명령을 지정합니다. 또한 디바이스 템플릿은 운영자가 사용하는 양식 및 보기와 같이 IoT Central의 디바이스에 대한 UI를 정의합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: d0e5f563e61cec96e67dd998e969a34a8da3615f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481239"
---
# <a name="what-are-device-templates"></a>디바이스 템플릿이란?

Azure IoT Central에서 디바이스 템플릿은 애플리케이션에 연결하는 디바이스 유형의 특징과 동작을 정의하는 청사진입니다. 예를 들어 디바이스 템플릿은 IoT Central이 올바른 단위와 데이터 형식을 사용하는 시각화를 만들 수 있도록 디바이스에서 보내는 원격 분석 데이터를 정의합니다.

솔루션 빌더는 IoT Central 애플리케이션에 디바이스 템플릿을 추가합니다. 디바이스 개발자는 디바이스 템플릿에 정의된 동작을 구현하는 디바이스 코드를 작성합니다.

디바이스 템플릿에는 다음과 같은 섹션이 포함됩니다.

- _디바이스 모델_. 디바이스 템플릿의 이 부분은 디바이스가 애플리케이션과 상호 작용하는 방법을 정의합니다. 디바이스 개발자는 모델에 정의된 동작을 구현합니다.
    - _루트 구성 요소_. 모든 디바이스 모델에는 루트 구성 요소가 있습니다. 루트 구성 요소의 인터페이스는 디바이스 모델과 관련된 기능을 설명합니다.
    - _구성 요소_. 디바이스 모델에는 디바이스 기능을 설명하는 루트 구성 요소 외의 구성 요소가 포함될 수 있습니다. 각 구성 요소에는 해당 구성 요소의 기능을 설명하는 인터페이스가 있습니다. 구성 요소 인터페이스는 다른 디바이스 모델에서 재사용할 수 있습니다. 예를 들어 여러 전화 디바이스 모델에서 동일한 카메라 인터페이스를 사용할 수 있습니다.
    - _상속된 인터페이스_. 디바이스 모델에는 루트 구성 요소의 기능을 확장하는 인터페이스가 하나 이상 포함되어 있습니다.
- _클라우드 속성_. 솔루션 개발자는 디바이스 템플릿의 이 부분을 통해 저장할 디바이스 메타데이터를 지정할 수 있습니다. 클라우드 속성은 디바이스와 동기화되지 않으며 애플리케이션에만 존재합니다. 클라우드 속성은 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 주지 않습니다.
- _사용자 지정_. 솔루션 개발자는 디바이스 템플릿의 이 부분을 통해 디바이스 모델의 일부 정의를 재정의할 수 있습니다. 사용자 지정은 솔루션 개발자가 속성의 표시 이름을 변경하거나 원격 분석 값을 표시하는 데 사용된 색상을 변경하는 등과 같이 애플리케이션에서 값을 처리하는 방법을 조정하려는 경우에 유용합니다. 사용자 지정은 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 주지 않습니다.
- _보기_. 솔루션 개발자는 디바이스 템플릿의 이 부분을 통해 디바이스에서 데이터를 보기 위한 시각화를 정의하고, 디바이스를 관리 및 제어하기 위한 양식을 정의할 수 있습니다. 보기에서는 디바이스 모델, 클라우드 속성 및 사용자 지정이 사용됩니다. 보기는 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 주지 않습니다.

## <a name="device-models"></a>디바이스 모델

디바이스 모델은 디바이스가 IoT Central 애플리케이션과 상호 작용하는 방식을 정의합니다. 디바이스 개발자는 IoT Central이 디바이스를 모니터링하고 관리할 수 있도록 디바이스 모델에 정의된 동작을 디바이스가 구현하는지 확인해야 합니다. 디바이스 모델은 하나 이상의 _인터페이스_ 로 구성되며 각 인터페이스는 _원격 분석_ 유형, _디바이스 속성_ 및 _명령_ 의 컬렉션을 정의할 수 있습니다. 솔루션 개발자는 디바이스 모델을 정의하는 JSON 파일을 디바이스 템플릿으로 가져오거나, IoT Central의 웹 UI를 사용하여 디바이스 모델을 만들거나 편집할 수 있습니다.

디바이스 모델 수정에 대한 자세한 내용은 [기존 디바이스 템플릿 편집](howto-edit-device-template.md)을 참조하세요.

또한 솔루션 개발자는 디바이스 모델을 포함하는 JSON 파일을 내보낼 수 있습니다. 디바이스 개발자는 이 JSON 문서를 사용하여 디바이스가 IoT Central 애플리케이션과 통신하는 방법을 이해할 수 있습니다.

디바이스 모델을 정의하는 JSON 파일은 [DTDL(디지털 트윈 정의 언어) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 사용합니다. IoT Central에는 별도의 파일이 아닌 인라인으로 인터페이스가 정의된 디바이스 모델을 포함하는 JSON 파일이 필요합니다. 자세히 알아보려면 [IoT 플러그 앤 플레이 모델링 가이드](../../iot-develop/concepts-modeling-guide.md)를 참조하세요.

일반적인 IoT 디바이스는 다음과 같이 구성됩니다.

- 디바이스를 고유하게 만드는 사용자 지정 파트
- 모든 디바이스에 공통적인 표준 파트

디바이스 모델에서는 이러한 파트를 _인터페이스_ 라고 합니다. 인터페이스는 디바이스가 구현하는 각 파트에 대한 세부 정보를 정의합니다. 인터페이스는 디바이스 모델에 전반에서 재사용할 수 있습니다. DTDL에서 구성 요소는 별도의 DTDL 파일이나 별도의 파일 섹션에서 정의될 수 있는 다른 인터페이스를 참조합니다.

다음 예제에서는 [온도 컨트롤러 디바이스](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json)에 대한 디바이스 모델의 개요를 보여 줍니다. 루트 구성 요소에는 `workingSet`, `serialNumber` 및 `reboot`에 대한 정의가 포함되어 있습니다. 또한 디바이스 모델에는 `thermostat` 구성 요소 두 개와 `deviceInformation` 구성 요소 하나도 포함되어 있습니다. 다음 세 가지 구성 요소의 내용은 간단하게 표시하기 위해 제거되었습니다.

```json
[
  {
    "@context": [
      "dtmi:iotcentral:context;2",
      "dtmi:dtdl:context;2"
    ],
    "@id": "dtmi:com:example:TemperatureController;2",
    "@type": "Interface",
    "contents": [
      {
        "@type": [
          "Telemetry",
          "DataSize"
        ],
        "description": {
          "en": "Current working set of the device memory in KiB."
        },
        "displayName": {
          "en": "Working Set"
        },
        "name": "workingSet",
        "schema": "double",
        "unit": "kibibit"
      },
      {
        "@type": "Property",
        "displayName": {
          "en": "Serial Number"
        },
        "name": "serialNumber",
        "schema": "string",
        "writable": false
      },
      {
        "@type": "Command",
        "commandType": "synchronous",
        "description": {
          "en": "Reboots the device after waiting the number of seconds specified."
        },
        "displayName": {
          "en": "Reboot"
        },
        "name": "reboot",
        "request": {
          "@type": "CommandPayload",
          "description": {
            "en": "Number of seconds to wait before rebooting the device."
          },
          "displayName": {
            "en": "Delay"
          },
          "name": "delay",
          "schema": "integer"
        }
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat1"
        },
        "name": "thermostat1",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat2"
        },
        "name": "thermostat2",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "DeviceInfo"
        },
        "name": "deviceInformation",
        "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1"
      }
    ],
    "displayName": {
      "en": "Temperature Controller"
    }
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;2",
    "@type": "Interface",
    "displayName": "Thermostat",
    "description": "Reports current temperature and provides desired temperature control.",
    "contents": [
      ...
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:azure:DeviceManagement:DeviceInformation;1",
    "@type": "Interface",
    "displayName": "Device Information",
    "contents": [
      ...
    ]
  }
]
```

인터페이스에는 다음과 같은 몇 가지 필수 필드가 있습니다.

- `@id`: 단순 URN(Uniform Resource Name) 형식의 고유한 ID입니다.
- `@type`: 이 개체가 인터페이스임을 선언합니다.
- `@context`: 인터페이스에 사용되는 DTDL 버전을 지정합니다.
- `contents`: 디바이스를 구성하는 속성, 원격 분석 및 명령을 나열합니다. 이 기능은 여러 인터페이스에서 정의될 수 있습니다.

표시 이름 및 설명과 같은 추가 정보를 기능 모델에 추가하는 데 사용할 수 있는 몇 가지 선택적 필드가 있습니다.

구현 섹션에서 인터페이스 목록의 각 항목에는 다음이 포함됩니다.

- `name`: 인터페이스의 프로그래밍 이름입니다.
- `schema`: 기능 모델이 구현하는 인터페이스입니다.

## <a name="interfaces"></a>인터페이스

DTDL을 사용하면 디바이스의 기능을 설명할 수 있습니다. 관련 기능은 인터페이스로 그룹화됩니다. 인터페이스는 디바이스의 일부가 구현하는 속성, 원격 분석 및 명령을 설명합니다.

- `Properties`. 속성은 디바이스의 상태를 나타내는 데이터 필드입니다. 속성을 사용하여 냉각수 펌프의 켜기 및 끄기 상태와 같은 디바이스의 내구성 상태를 나타냅니다. 또한 속성은 디바이스의 펌웨어 버전 같은 기본 디바이스 속성을 나타낼 수도 있습니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 디바이스만 읽기 전용 속성의 값을 업데이트할 수 있습니다. 운영자는 디바이스에 보낼 쓰기 가능 속성의 값을 설정할 수 있습니다.
- `Telemetry`. 원격 분석 필드는 센서의 측정값을 나타냅니다. 디바이스가 센서 측정값을 사용하는 경우에는 센서 데이터를 포함하는 원격 분석 이벤트를 전송해야 합니다.
- `Commands`. 명령은 디바이스 사용자가 디바이스에서 실행할 수 있는 메서드를 나타냅니다. 예를 들면 reset 명령 또는 팬을 켜고 끄는 명령입니다.

다음 예제에서는 자동 온도 조절기 인터페이스 정의를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
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

이 예제에서는 두 개의 속성(읽기 전용 및 쓰기 가능), 원격 분석 유형 및 명령을 보여 줍니다. 최소 필드 설명에는 다음이 포함됩니다.

- `Telemetry`, `Property` 또는 `Command` 기능 유형을 지정하는 `@type`.  일부 경우에는 IoT Central에서 값을 처리하는 방법에 대한 몇 가지 가정을 할 수 있도록 의미 체계 형식이 type에 포함됩니다.
- 원격 분석 값에 대한 `name`
- 원격 분석 또는 속성의 데이터 형식을 지정하는 `schema`. 이 값은 double, integer, boolean 또는 string과 같은 기본 형식일 수 있습니다. 복합 개체 형식 및 맵도 지원됩니다.

표시 이름, 설명 등의 선택적 필드를 사용하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

## <a name="properties"></a>속성

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 디바이스가 IoT Central 애플리케이션에 대한 속성 값 업데이트를 보고한다는 것을 의미합니다. IoT Central 애플리케이션에서는 읽기 전용 속성 값을 설정할 수 없습니다.

또한 인터페이스에서 속성을 쓰기 가능으로 표시할 수도 있습니다. 디바이스는 IoT Central 애플리케이션에서 쓰기 가능한 속성에 대한 업데이트를 수신할 수 있을 뿐만 아니라 애플리케이션에 대한 속성 값 업데이트를 보고할 수 도 있습니다.

속성 값을 설정하기 위해 디바이스를 연결할 필요가 없습니다. 업데이트된 값은 다음 번에 디바이스가 애플리케이션에 연결될 때 전송됩니다. 이 동작은 읽기 전용 및 쓰기 가능 속성 모두에 적용됩니다.

디바이스에서 원격 분석 데이터를 전송하기 위해 속성을 사용하지 마세요. 예를 들어 `temperatureSetting=80`과 같은 읽기 전용 속성은 디바이스 온도가 80으로 설정되어 있고 디바이스가 이 온도에 도달하거나 이 온도를 유지하려고 하는 것을 의미합니다.

쓰기 가능 속성의 경우 디바이스 애플리케이션은 속성 값을 수신하여 적용했는지 여부를 나타내는 데 필요한 상태 코드, 버전 및 설명을 반환합니다.

## <a name="telemetry"></a>원격 분석

IoT Central을 사용하면 디바이스 보기 및 차트에서 원격 분석 데이터를 볼 수 있으며 임계값에 도달하면 규칙을 사용하여 작업을 트리거할 수 있습니다. IoT Central은 데이터 형식, 단위 및 표시 이름과 같은 디바이스 모델의 정보를 사용하여 원격 분석 값을 표시하는 방법을 결정합니다. 애플리케이션 및 프라이빗 대시보드에 원격 분석 값을 표시할 수도 있습니다.

IoT Central 데이터 내보내기 기능을 사용하여 스토리지 또는 Event Hubs 같은 다른 대상으로 원격 분석 데이터를 스트리밍할 수 있습니다.

## <a name="commands"></a>명령

명령은 기본적으로 30초 이내에 실행해야 하며, 명령이 도착하면 디바이스를 연결해야 합니다. 디바이스가 시간 내에 응답하거나 디바이스가 연결되지 않은 경우 명령이 실패합니다.

명령에는 요청 매개 변수를 사용할 수 있으며 응답을 반환할 수 있습니다.

### <a name="offline-commands"></a>오프라인 명령

디바이스가 현재 오프라인 상태인 경우 디바이스 템플릿의 명령에 대해 **오프라인 상태인 경우 큐** 옵션을 사용하도록 설정하여 큐 명령을 선택할 수 있습니다.

오프라인 명령은 솔루션에서 디바이스로의 단방향 알림입니다. 오프라인 명령은 요청 매개 변수를 포함할 수 있지만 응답을 반환하지 않습니다.

> [!NOTE]
> 이 옵션은 IoT Central 웹 UI에서만 사용할 수 있습니다. 이 설정은 디바이스 템플릿에서 모델 또는 인터페이스를 내보내는 경우에는 포함되지 않습니다.

## <a name="cloud-properties"></a>클라우드 속성

클라우드 속성은 디바이스 템플릿의 일부이지만 디바이스 모델의 일부는 아닙니다. 클라우드 속성을 사용하면 솔루션 개발자가 IoT Central 애플리케이션에 저장할 디바이스 메타데이터를 지정할 수 있습니다. 클라우드 속성은 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 주지 않습니다.

솔루션 개발자는 디바이스 속성과 함께 디바이스 보기 및 양식에 클라우드 속성을 추가하여 운영자가 애플리케이션에 연결된 디바이스를 관리할 수 있도록 합니다. 또한 솔루션 개발자는 규칙 정의의 일부로 클라우드 속성을 사용하여 운영자가 임계값을 편집할 수 있도록 만들 수 있습니다.

## <a name="customizations"></a>사용자 지정

사용자 지정은 디바이스 템플릿의 일부이지만 디바이스 모델의 일부는 아닙니다. 사용자 지정을 사용하면 솔루션 개발자가 디바이스 모델의 정의 중 일부를 개선하거나 재정의할 수 있습니다. 예를 들어 솔루션 개발자는 원격 분석 유형 또는 속성의 표시 이름을 변경할 수 있습니다. 솔루션 개발자는 사용자 지정을 사용하여 문자열 디바이스 속성의 최소 또는 최대 길이와 같은 유효성 검사를 추가할 수도 있습니다.

사용자 지정은 디바이스 개발자가 디바이스 모델을 구현하기 위해 작성하는 코드에 영향을 줄 수 있습니다. 예를 들어 사용자 지정은 원격 분석 데이터의 최소 및 최대 문자열 길이 또는 최소 및 최대 숫자 값을 설정할 수 있습니다.

## <a name="views"></a>보기

솔루션 개발자는 운영자가 연결된 디바이스를 모니터링하고 관리할 수 있는 보기를 만듭니다. 보기는 디바이스 템플릿의 일부이므로 특정 디바이스 유형과 보기가 연결됩니다. 보기에는 다음이 포함될 수 있습니다.

- 원격 분석 데이터를 그리는 차트
- 읽기 전용 디바이스 속성을 표시하는 타일
- 운영자가 쓰기 가능한 디바이스 속성을 편집할 수 있는 타일
- 운영자가 클라우드 속성을 편집할 수 있는 타일
- 페이로드를 필요로 하는 명령을 포함하여 운영자가 명령을 호출할 수 있는 타일
- 레이블, 이미지 또는 markdown 텍스트를 표시하는 타일

보기에 추가할 수 있는 원격 분석 데이터, 속성 및 명령은 디바이스 모델, 클라우드 속성 및 디바이스 템플릿의 사용자 지정에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

이제 디바이스 템플릿에 대해 배웠으므로 제안된 다음 단계는 디바이스가 IoT Central과 교환하는 데이터에 대해 자세히 알아보기 위해 [원격 측정, 속성 및 명령 페이로드](./concepts-telemetry-properties-commands.md)를 읽는 것입니다.
