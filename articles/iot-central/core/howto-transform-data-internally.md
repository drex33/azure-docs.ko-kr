---
title: Azure IoT Central | 내의 데이터 변환 Microsoft Docs
description: IoT 디바이스는 변환해야 할 수 있는 다양한 형식으로 데이터를 전송합니다. 이 문서에서는 내보내기 전에 IoT Central 데이터를 변환하는 방법을 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 10/28/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c886fd71399f84f04e7ac83ff24059dd658f0a4b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579519"
---
# <a name="transform-data-inside-your-iot-central-application-for-export"></a>내보내기 위해 IoT Central 애플리케이션 내의 데이터 변환

:::image type="content" source="media/howto-transform-data-internally/transformations.png" alt-text="데이터 내보내기 변환 옵션을 보여 주는 다이어그램" border="false":::

IoT 디바이스는 다양한 형식으로 데이터를 전송합니다. IoT 솔루션에서 디바이스 데이터를 사용하려면 디바이스 데이터를 다른 서비스로 내보내기 전에 변환해야 할 수 있습니다.

이 문서에서는 IoT Central 애플리케이션에서 데이터 내보내기 정의의 일부로 디바이스 데이터를 변환하는 방법을 보여줍니다.

IoT Central 데이터 내보내기 정의의 변환을 사용하면 대상으로 내보내기 전에 디바이스 데이터의 형식과 구조를 조작할 수 있습니다. 내보내기 정의에서 각 대상에 대한 변환을 지정할 수 있습니다. 각 메시지는 변환을 통해 전달되어 대상으로 내보내지는 출력 레코드를 만듭니다.

변환을 사용하여 JSON 페이로드를 재구성하고, 필드 이름을 바꾸고, 필드를 필터링하고, 원격 분석 값에 대한 간단한 계산을 실행합니다. 예를 들어 변환을 사용하여 메시지를 대상의 스키마(예: Azure Data Explorer 테이블)와 일치하는 테이블 형식으로 변환합니다.

## <a name="add-a-transformation"></a>변환 추가

데이터 내보내기에서 대상에 대한 변환을 추가하려면 다음 스크린샷과 같이 **+ 변환을** 선택합니다.

:::image type="content" source="media/howto-transform-data-internally/add-transformation.png" alt-text="대상에 변환을 추가하는 방법을 보여 주는 스크린샷":::

**데이터 변환** 패널에서 변환을 지정할 수 있습니다. **1에서 입력 메시지 섹션을 추가하면** 변환을 통해 전달할 샘플 메시지를 입력할 수 있습니다. 디바이스 템플릿을 선택하여 샘플 메시지를 생성할 수도 있습니다. **2. 변환 쿼리 작성** 섹션에서 입력 메시지를 변환하는 쿼리를 입력할 수 있습니다. **3입니다. 출력 메시지 미리 보기** 섹션에는 변환 결과가 표시됩니다.

:::image type="content" source="media/howto-transform-data-internally/transformation-editor.png" alt-text="IoT Central 변환 편집기의 스크린샷.":::

> [!TIP]
> 입력 메시지의 형식을 모르는 경우 를 `.` 쿼리로 사용하여 메시지를 있는 그대로 Webhook와 같은 대상으로 내보냅니다. 그런 다음, webhook에서 받은 메시지를 ***1에 붙여넣습니다. 입력 메시지 를 추가합니다.** 그런 다음 변환 쿼리를 작성하여 이 메시지를 필요한 출력 형식으로 처리합니다.

## <a name="build-a-transformation-query"></a>변환 쿼리 작성

변환 엔진은 오픈 소스 [JQ JSON](https://stedolan.github.io/jq/) 프로세서를 사용하여 JSON 페이로드를 재구성하고 형식을 지정합니다. 변환을 지정하려면 JQ의 기본 제공 필터, 함수 및 기능을 사용할 수 있는 JQ 쿼리를 작성합니다. 일부 쿼리 예제는 [예제 변환 쿼리 를 참조하세요.](#example-transformation-queries) JQ 쿼리 작성에 대한 자세한 내용은 [JQ 설명서를 참조하세요.](https://stedolan.github.io/jq/manual/)

## <a name="pre-transformation-message-structure"></a>변환 전 메시지 구조

원격 분석, 속성 변경, 디바이스 연결 이벤트, 디바이스 수명 주기 이벤트 및 디바이스 템플릿 수명 주기 이벤트와 같은 IoT Central 데이터 스트림을 내보낼 수 있습니다. 각 데이터 형식에는 원격 분석 값, 애플리케이션 정보, 디바이스 메타데이터 및 속성 값과 같은 정보를 포함하는 특정 구조가 있습니다.

다음 예제에서는 원격 분석 메시지의 모양을 보여줍니다. 이 모든 데이터를 변환에 사용할 수 있습니다. 메시지의 구조는 다른 메시지 유형과 유사하지만 일부 형식별 필드가 있습니다. 입력 메시지 **추가** 기능을 사용하여 애플리케이션의 디바이스 템플릿을 기반으로 샘플 메시지를 생성할 수 있습니다.

```json
{
  "applicationId": "93d68c98-9a22-4b28-94d1-06625d4c3d0f",
  "device": {
    "id": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "name": "Scripted Device - 31edabe6-e0b9-4c83-b0df-d12e95745b9f",
    "cloudProperties": [],
    "properties": {
      "reported": [
        {
          "id": "urn:smartKneeBrace:Smart_Vitals_Patch_wr:FirmwareVersion:1",
          "name": "FirmwareVersion",
          "value": 1.0
        }
      ]
    },
    "templateId": "urn:sbq3croo:modelDefinition:nf7st1wn3",
    "templateName": "Smart Knee Brace"
  },
  "telemetry": [
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
        "name": "Acceleration",
        "value": {
          "x": 19.212770659918583,
          "y": 20.596296675217335,
          "z": 54.04859440697045
        }
      },
      {
        "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
        "name": "RangeOfMotion",
        "value": 110
      }
  ],
  "enqueuedTime": "2021-03-23T19:55:56.971Z",
  "enrichments": {
      "your-enrichment-key": "enrichment-value"
  },
  "messageProperties": {
      "prop1": "prop-value"
  },
  "messageSource": "telemetry"
}
```

> [!TIP]
> IoT Central 애플리케이션 UI에서 입력 메시지 추가 기능을 사용하여 속성 변경과 같은 다른 데이터 내보내기 형식에 대한 샘플 메시지 구조를 **확인합니다.**

## <a name="example-transformation-queries"></a>변환 쿼리 예제

다음 쿼리 예제에서는 이전 섹션에 표시된 원격 분석 메시지를 사용합니다.

**예제 1:** 다음 JQ 쿼리는 입력 메시지의 각 원격 분석을 별도의 출력 메시지로 출력합니다.

```jq
.telemetry[]
```

JSON 출력:

```json
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:Acceleration:1",
  "name": "Acceleration",
  "value": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  }
},
{
  "id": "urn:continuousPatientMonitoringTemplate:Smart_Knee_Brace_6wm:RangeOfMotion:1",
  "name": "RangeOfMotion",
  "value": 110
}
```

> [!TIP]
> 원격 분석 형식의 배열을 사용하여 출력을 단일 메시지로 변경하려면 쿼리를 `.telemetry` 사용합니다.

**예제 2:** 다음 JQ 쿼리는 입력 원격 분석 배열을 키로 원격 분석 이름을 가진 개체로 변환합니다.

```jq
.telemetry | map({ key: .name, value: .value }) | from_entries
```

JSON 출력:

```json
{
  "Acceleration": {
    "x": 19.212770659918583,
    "y": 20.596296675217335,
    "z": 54.04859440697045
  },
  "RangeOfMotion": 110
}
```

**예제 3:** 다음 JQ 쿼리는 **RangeOfMotion** 원격 분석 값을 찾아 수식을 사용하여 도에서 라디안으로 변환합니다. `rad = degree * pi / 180` 이 쿼리는 모듈을 가져오고 사용하는 방법도 보여줍니다. `iotc`

```jq
import "iotc" as iotc;
{
  rangeOfMotion: (
    .telemetry
    | iotc::find(.name == "RangeOfMotion").value
    | . * 3.14159265358979323846 / 180
  )
}
```

JSON 출력:

```json
{
  "rangeOfMotion": 1.9198621771937625
}
```

**예제 4:** 입력 메시지를 테이블 형식으로 조작하려면 내보낸 각 메시지를 하나 이상의 행에 매핑할 수 *있습니다.* 행 출력은 논리적으로 열 이름이 키이고 열 값이 값인 JSON 개체로 표시됩니다.

```json
{
    "<column 1 name>": "<column 1 value>",
    "<column 2 name>": "<column 2 value>",
    ...
}
```

> [!TIP]
> Azure Data Explorer 내보낼 때 테이블 형식을 사용합니다.

다음 JQ 쿼리는 여러 디바이스에서 **rangeOfMotion** 원격 분석을 저장하는 테이블에 행을 씁니다. 이 쿼리는 디바이스 ID, 인큐에 추가된 시간 및 동작 범위를 다음 열이 있는 테이블에 매핑합니다.

```jq
import "iotc" as iotc;
{
    deviceId: .deviceId,
    timestamp: .enqueuedTime,
    rangeOfMotion: .telemetry | iotc::find(.name == "RangeOfMotion").value
}
```

JSON 형식의 출력:

```json
{
  "deviceId": "31edabe6-e0b9-4c83-b0df-d12e95745b9f",
  "timestamp": "2021-03-23T19:55:56.971Z",
  "rangeOfMotion": 110
}
```

## <a name="iot-central-module"></a>IoT Central 모듈

JQ 모듈은 사용자 지정 함수의 컬렉션입니다. 변환 쿼리의 일부로 함수가 포함된 기본 제공 IoT Central 특정 모듈을 가져와 쿼리를 더 쉽게 작성할 수 있습니다. IoT Central 모듈을 가져오려면 다음 지시문을 사용합니다.

```jq
import "iotc" as iotc;
```

IoT Central 모듈에는 다음 함수가 포함되어 있습니다.

`find(expression)`: `find` 함수를 사용하면 페이로드에서 원격 분석 값 또는 속성 항목과 같은 특정 배열 요소를 찾을 수 있습니다. 함수 입력은 배열이고 매개 변수는 배열의 각 요소에 대해 실행할 JQ 필터를 정의합니다. 함수는 필터가 true로 평가되는 각 배열 요소를 반환합니다.

예를 들어 라는 특정 원격 분석 값을 `RangeOfMotion` 찾으려면:

```jq
.telemetry | iotc::find(.name == "RangeOfMotion")
```

## <a name="scenarios"></a>시나리오

다음 시나리오에서는 변환 기능을 사용하여 특정 대상에 대한 디바이스 데이터 형식을 사용자 지정합니다.

### <a name="scenario-1-export-device-data-to-azure-data-explorer"></a>시나리오 1: 디바이스 데이터를 Azure Data Explorer 내보내기

이 시나리오에서는 각 원격 분석 값이 테이블의 열로 표시되고 각 행이 단일 메시지를 나타내는 Azure Data Explorer 고정 스키마와 일치하도록 디바이스 데이터를 변환합니다. 예:

| DeviceId | 타임스탬프 | T1 | T2 |T3 |
| :------------- | :---------- | :----------- | :---------- | :----------- |
| "31edabe6-e0b9-4c83-b0df-d12e95745b9f" |"2021-03-23T19:55:56.971Z | 1.18898 | 1.434709 | 2.97008 |

이 테이블과 호환되는 데이터를 내보내려면 내보낸 각 메시지가 다음 개체와 같아야 합니다. 개체는 키가 열 이름이고 값이 각 열에 배치할 값인 단일 행을 나타냅니다.

```json
{
    "Timestamp": <value-of-Timestamp>,
    "DeviceId": <value-of-deviceId>,
    "T1": <value-of-T1>,
    "T2": <value-of-T2>,
    "T3": <value-of-T3>,
}
```

이 시나리오에서 디바이스는 `t1` 다음 `t2` 예제와 같은 입력 메시지에서 , 및 `t3` 원격 분석 값을 보냅니다.

```json
{
  "applicationId": "c57fe8d9-d15d-4659-9814-d3cc38ca9e1b",
  "enqueuedTime": "1933-01-26T03:10:44.480001324Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t1;1",
      "name": "t1",
      "value": 1.1889838348731093e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t2;1",
      "name": "t2",
      "value": 1.4347093391531383e+308
    },
    {
      "id": "dtmi:temperaturesensor288:sensors1lr:t3;1",
      "name": "t3",
      "value": 2.9700885230380616e+307
    }
  ],
  "device": {
    "id": "oozrnl1zs857",
    "name": "haptic alarm",
    "templateId": "dtmi:modelDefinition:nhhbjotee:qytxnp8hi",
    "templateName": "hapticsensors",
    "properties": {
      "reported": []
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": false,
    "blocked": false,
    "provisioned": true
  }
}
```

다음 JQ 쿼리는 `T1` , 및 원격 분석 값, 및 를 `T2` Azure Data Explorer 테이블 `T3` `Timestamp` `deviceId` 스키마와 일치하는 키-값 쌍이 있는 메시지로 출력합니다.

```jq
import "iotc" as iotc;
{
  deviceId: .device.id,
  Timestamp: .enqueuedTime,
  T1: .telemetry | iotc::find(.name == "t1").value,
  T2: .telemetry | iotc::find(.name == "t2").value,
  T3: .telemetry | iotc::find(.name == "t3").value,
}
```

JSON 출력:

```json
{
  "T1": 1.1889838348731093e+308,
  "T2": 1.4347093391531383e+308,
  "T3": 2.9700885230380616e+307,
  "Timestamp": "1933-01-26T03:10:44.480001324Z",
  "deviceId": "oozrnl1zs857"
}
```

Azure Data Explorer 클러스터 및 데이터베이스를 내보내기 대상으로 추가하는 방법에 대한 자세한 내용은 [Azure Data Explorer 대상 만들기를](howto-export-data.md#create-an-azure-data-explorer-destination)참조하세요.

### <a name="scenario-2-breaking-apart-a-telemetry-array"></a>시나리오 2: 원격 분석 배열 분리

이 시나리오에서 디바이스는 다음 원격 분석 배열을 하나의 메시지로 보냅니다.

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:sample1:data;1",
      "name": "data",
      "value": [
        {
          "id": "subdevice1",
          "values": {
              "running": true,
              "cycleCount": 2315
          }
        },
        {
          "id": "subdevice2",
          "values": {
              "running": false,
              "cycleCount": 824567
          }
        }
      ]
    },
    {
      "id": "dtmi:sample1:parentStatus;1",
      "name": "parentStatus",
      "value": "healthy"
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:sample1:prop;1",
          "name": "Connectivity",
          "value": "Tenetur ut quasi minus ratione voluptatem."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

다음 테이블 스키마와 일치하도록 이 디바이스 데이터를 변환하려고 합니다.

| cycleCount | deviceId | enqueuedTime | parentStatus | 실행 중 | subdeviceId |
| :--------- | :------- | :----------- | :----------- | :------ | :---------- |
| 2315   | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042Z" | "정상" | true | "subdevice1" |
| 824567 | "9xwhr7khkfri" | "1909-10-10T07:11:56.078161042 Z" | 정상 | false | "subdevice2" |

다음 JQ 쿼리는 메시지의 각 하위 장치 항목에 대 한 별도의 출력 메시지를 만들고 기본 메시지 및 부모 장치에서 제공 하는 몇 가지 일반적인 정보를 포함 합니다. 이 쿼리는 출력을 평면화 하 고 단일 메시지로 도착 한 데이터의 논리적 분할을 분리 합니다.

```jq
import "iotc" as iotc;
{
    enqueuedTime: .enqueuedTime,
    deviceId: .device.id,
    parentStatus: .telemetry | iotc::find(.name == "parentStatus").value
} + (
    .telemetry
    | iotc::find(.name == "data").value[]
    | {
        subdeviceId: .id,
        running: .values.running,
        cycleCount: .values.cycleCount
    }
)
```

JSON 출력:

```json
{
    "cycleCount": 2315,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": true,
    "subdeviceId": "subdevice1"
},
{
    "cycleCount": 824567,
    "deviceId": "9xwhr7khkfri",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "parentStatus": "healthy",
    "running": false,
    "subdeviceId": "subdevice2"
}
```

### <a name="scenario-3-power-bi-streaming"></a>시나리오 3: Power BI 스트리밍

Power BI 실시간 스트리밍 기능을 사용 하 여 실시간으로 짧은 대기 시간으로 업데이트 된 대시보드의 데이터를 볼 수 있습니다. 자세히 알아보려면 [Power BI의 실시간 스트리밍](/power-bi/connect-data/service-real-time-streaming)을 참조 하세요.

Power BI 스트림과 함께 IoT Central를 사용 하려면 특정 형식으로 요청 본문을 보내는 웹 후크 내보내기를 설정 합니다. 이 예에서는 다음 스키마를 사용 하는 Power BI 스트리밍 데이터 집합이 있다고 가정 합니다.

```json
[
  {
    "bloodPressureDiastolic": 161438124,
    "bloodPressureSystolic": -966387879,
    "deviceId": "9xwhr7khkfri",
    "deviceName": "wireless port",
    "heartRate": -633994413,
    "heartRateVariability": -37514094,
    "respiratoryRate": 1582211310,
    "timestamp": "1909-10-10T07:11:56.078161042Z"
  }
]
```

webhook 내보내기 대상을 만들려면 Power BI 스트리밍 데이터 집합에 대 한 REST API URL 끝점이 필요 합니다.

이 시나리오에서 장치는 다음 예제와 같은 원격 분석 메시지를 보냅니다.

```json
{
  "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "messageSource": "telemetry",
  "telemetry": [
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
      "name": "HeartRate",
      "value": -633994413
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
      "name": "RespiratoryRate",
      "value": 1582211310
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
      "name": "HeartRateVariability",
      "value": -37514094
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
      "name": "BodyTemperature",
      "value": 5.323322666478241e+307
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
      "name": "FallDetection",
      "value": "Earum est nobis at voluptas id qui."
    },
    {
      "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
      "name": "BloodPressure",
      "value": {
        "Diastolic": 161438124,
        "Systolic": -966387879
      }
    }
  ],
  "device": {
    "id": "9xwhr7khkfri",
    "name": "wireless port",
    "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
    "templateName": "Smart Vitals Patch",
    "properties": {
      "reported": [
        {
          "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
          "name": "DeviceStatus",
          "value": "Id optio iste vero et neque sit."
        }
      ]
    },
    "cloudProperties": [],
    "simulated": true,
    "approved": true,
    "blocked": false,
    "provisioned": false
  }
}
```

다음 jq 쿼리는 입력 메시지를 webhook에서 Power BI 스트리밍 데이터 집합으로 전송 하는 데 적합 한 형식으로 변환 합니다. 이 예에는 특정 장치 템플릿에 대 한 메시지만 출력 하는 필터 조건이 포함 되어 있습니다. 데이터 내보내기 필터 기능을 사용 하 여 장치 템플릿을 기준으로 필터링 할 수 있습니다.

```jq
import "iotc" as iotc;
if .device.templateId == "dtmi:hpzy1kfcbt2:umua7dplmbd" then 
    [{
        deviceId: .device.id,
        timestamp: .enqueuedTime,
        deviceName: .device.name,
        bloodPressureSystolic: .telemetry | iotc::find(.name == "BloodPressure").value.Systolic,
        bloodPressureDiastolic: .telemetry | iotc::find(.name == "BloodPressure").value.Diastolic,
        heartRate: .telemetry | iotc::find(.name == "HeartRate").value,
        heartRateVariability: .telemetry | iotc::find(.name == "HeartRateVariability").value,
        respiratoryRate: .telemetry | iotc::find(.name == "RespiratoryRate").value
    }]
else
    empty
end
```

JSON 출력:

```json
{
  "bloodPressureDiastolic": 161438124,
  "bloodPressureSystolic": -966387879,
  "deviceId": "9xwhr7khkfri",
  "deviceName": "wireless port",
  "heartRate": -633994413,
  "heartRateVariability": -37514094,
  "respiratoryRate": 1582211310,
  "timestamp": "1909-10-10T07:11:56.078161042Z"
}
```

### <a name="scenario-4-export-data-to-azure-data-explorer-and-visualize-it-in-power-bi"></a>시나리오 4: 데이터를 Azure 데이터 탐색기로 내보내고에서 시각화 Power BI

이 시나리오에서는 데이터를 Azure 데이터 탐색기로 내보낸 다음 커넥터를 사용 하 여 Power BI 데이터를 시각화 합니다. Azure 데이터 탐색기 클러스터와 데이터베이스를 내보내기 대상으로 추가 하는 방법에 대해 자세히 알아보려면 [azure 데이터 탐색기 대상 만들기](howto-export-data.md#create-an-azure-data-explorer-destination)를 참조 하세요.

이 시나리오에서는 다음 스키마를 사용 하 여 Azure 데이터 탐색기 테이블을 사용 합니다.

``` kusto
.create table smartvitalspatch (
  EnqueuedTime:datetime,
  Message:string,
  Application:string,
  Device:string,
  Simulated:boolean,
  Template:string,
  Module:string,
  Component:string,
  Capability:string,
  Value:dynamic
)
```

이 시나리오에서 장치는 다음 예제와 같은 원격 분석 메시지를 보냅니다.

```json
{
    "applicationId": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
    "enqueuedTime": "1909-10-10T07:11:56.078161042Z",
    "messageSource": "telemetry",
    "telemetry": [
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRate;1",
            "name": "HeartRate",
            "value": -633994413
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:RespiratoryRate;1",
            "name": "RespiratoryRate",
            "value": 1582211310
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:HeartRateVariability;1",
            "name": "HeartRateVariability",
            "value": -37514094
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BodyTemperature;1",
            "name": "BodyTemperature",
            "value": 5.323322666478241e+307
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:FallDetection;1",
            "name": "FallDetection",
            "value": "Earum est nobis at voluptas id qui."
        },
        {
            "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_37p:BloodPressure;1",
            "name": "BloodPressure",
            "value": {
                "Diastolic": 161438124,
                "Systolic": -966387879
            }
        }
    ],
    "device": {
        "id": "9xwhr7khkfri",
        "name": "wireless port",
        "templateId": "dtmi:hpzy1kfcbt2:umua7dplmbd",
        "templateName": "Smart Vitals Patch",
        "properties": {
            "reported": [
                {
                    "id": "dtmi:smartVitalsPatch:Smart_Vitals_Patch_wr:DeviceStatus;1",
                    "name": "DeviceStatus",
                    "value": "Id optio iste vero et neque sit."
                }
            ]
        },
        "cloudProperties": [],
        "simulated": true,
        "approved": true,
        "blocked": false,
        "provisioned": false
    }
}
```

다음 JQ 쿼리는 입력 메시지를 각 원격 분석 값에 대 한 별도의 출력 메시지로 변환 합니다. 이 변환은 Azure 데이터 탐색기 테이블 스키마와 일치 하는 출력을 생성 합니다. 변환에서는 각 행이 단일 원격 분석 값을 보유 하 고 원격 분석 이름이 동일한 행의 개별 열에 있는 값인 엔터티-특성 값 스키마를 사용 합니다.

```jq
. as $in | .telemetry[] | {
  EnqueuedTime: $in.enqueuedTime,
  Message: $in.messageId,
  Application: $in.applicationId,
  Device: $in.device.id,
  Simulated: $in.device.simulated,
  Template: ($in.device.templateName // ""),
  Module: ($in.module // ""),
  Component: ($in.component // ""),
  Capability: .name,
  Value: .value
}
```

JSON 출력:

```json
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -633994413
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "RespiratoryRate",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 1582211310
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "HeartRateVariability",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": -37514094
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BodyTemperature",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": 5.323322666478241e+307
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "FallDetection",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": "Earum est nobis at voluptas id qui."
},
{
  "Application": "570c2d7b-d72e-4ad1-aaf4-ad9b727daa47",
  "Capability": "BloodPressure",
  "Component": "",
  "Device": "9xwhr7khkfri",
  "EnqueuedTime": "1909-10-10T07:11:56.078161042Z",
  "Message": null,
  "Module": "",
  "Simulated": true,
  "Template": "Smart Vitals Patch",
  "Value": {
      "Diastolic": 161438124,
      "Systolic": -966387879
  }
}
```

출력 데이터는 Azure 데이터 탐색기 클러스터로 내보내집니다. Power BI에서 내보낸 데이터를 시각화 하려면 다음 단계를 완료 합니다.

1. Power BI 응용 프로그램을 설치 합니다. [Power BI Desktop를 사용 하 여 데이터에서 정보로 이동에서 정보로 이동](https://powerbi.microsoft.com/desktop/)에서 데스크톱 Power BI 응용 프로그램을 다운로드할 수 있습니다.
1. GitHub에서 Power BI desktop [IoT Central adx 커넥터. pbit](https://github.com/Azure-Samples/iot-central-docs-samples/raw/master/azure-data-explorer-power-bi/IoT%20Central%20ADX%20Connector.pbit) 파일을 다운로드 합니다.
1. Power BI Desktop 앱을 사용 하 여 이전 단계에서 다운로드 한 *IoT Central adx Connector. pbit* 파일을 엽니다. 메시지가 표시 되 면 이전에 기록해 둔 Azure 데이터 탐색기 클러스터, 데이터베이스 및 테이블 정보를 입력 합니다.

이제 Power BI에서 데이터를 시각화할 수 있습니다.

:::image type="content" source="media/howto-transform-data-internally/powerbi-report.png" alt-text="IoT Central의 데이터를 표시 하는 Power BI 보고서의 스크린샷" border="false":::

## <a name="next-steps"></a>다음 단계

이제 IoT Central에서 데이터를 변환 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central에서 분석을 사용 하는 방법을](./howto-create-analytics.md)배우는 것입니다.
