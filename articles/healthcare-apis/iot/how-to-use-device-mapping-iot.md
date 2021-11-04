---
title: IoT Connector의 장치 매핑 템플릿-Azure 의료 api
description: 이 문서에서는 IoT Connector에서 장치 매핑 템플릿을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: jasteppe
ms.openlocfilehash: 3550bab190c2b5bbec5a03faa8131094003ee85b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446014"
---
# <a name="how-to-use-device-mapping"></a>장치 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

IoT 커넥터에는 두 가지 유형의 JSON 기반 매핑이 필요 합니다. 첫 번째 유형인 **장치 매핑은** `devicedata` Azure Event Hub 끝점으로 전송 되는 장치 페이로드를 매핑합니다. 유형, 장치 식별자, 측정 날짜 시간 및 측정 값을 추출 합니다. 

두 번째 유형인 **fhir&#174; (신속한 의료 상호 운용성 리소스) 대상 매핑은** fhir 리소스에 대 한 매핑을 제어 합니다. 이를 통해 관찰 기간의 길이, 값을 저장 하는 데 사용 되는 FHIR 데이터 형식 및 용어 코드를 구성할 수 있습니다. 

두 가지 유형의 매핑은 해당 형식에 따라 JSON 문서로 구성 됩니다. 그런 다음 이러한 JSON 문서를 Azure Portal 통해 IoT 커넥터에 추가 합니다. 장치 매핑 문서는 **대상** 페이지를 통해 **장치 매핑** 페이지 및 fhir 대상 매핑 문서를 통해 추가 됩니다.

> [!NOTE]
> 매핑은 기본 blob 저장소에 저장 되 고 계산 실행 당 blob에서 로드 됩니다. 업데이트 되 면 즉시 적용 됩니다. 

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

## <a name="device-mapping"></a>디바이스 매핑

장치 매핑은 추가 평가를 위해 장치 콘텐츠를 공용 형식으로 추출 하는 매핑 기능을 제공 합니다. 받은 각 메시지는 모든 템플릿에 대해 평가 됩니다. 이 접근 방식을 사용 하면 단일 인바운드 메시지를 여러 아웃 바운드 메시지에 프로젝션 할 수 있으며 나중에이를 FHIR의 다른 관찰에 매핑할 수 있습니다. 결과는 템플릿에서 구문 분석 된 값을 나타내는 정규화 된 데이터 개체입니다. 정규화 된 데이터 모델에는 몇 가지 필수 속성을 찾아서 추출 해야 합니다.

| 속성             | 설명                                                                                                                                                                                                                                                   |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**             | 측정값을 분류 하는 이름/형식입니다. 이 값은 필요한 FHIR 대상 매핑에 바인딩하는 데 사용 됩니다.  여러 매핑을 동일한 형식으로 출력 하 여 여러 장치에 있는 다양 한 표현을 단일 공통 출력에 매핑할 수 있습니다. |
| **OccurenceTimeUtc** | 측정이 발생 한 시간입니다.                                                                                                                                                                                                                            |
| **DeviceId**         | 장치의 식별자입니다. 이 값은 대상 FHIR 서비스에 있는 장치 리소스의 식별자와 일치 해야 합니다.                                                                                                                       |
| **속성**       | 만든 관찰 리소스에 값을 저장할 수 있도록 적어도 하나 이상의 속성을 추출 합니다.  속성은 정규화 중에 추출 된 키 값 쌍의 컬렉션입니다.                                                                                  |

다음은 정규화 중에 발생 하는 작업의 개념 예입니다.

![정규화 예제](media/concepts-iot-mapping-templates/normalization-example.png#lightbox)

콘텐츠 페이로드는 세 부분으로 구성 된 Azure 이벤트 허브 메시지입니다. 본문, 속성 및 SystemProperties입니다. 는 `Body` u t f-8로 인코딩된 문자열을 나타내는 바이트 배열입니다. 템플릿 평가 중에 바이트 배열이 자동으로 문자열 값으로 변환 됩니다. `Properties` 메시지 작성자가 사용할 키 값 컬렉션입니다. `SystemProperties` 는 Azure Event hubs 프레임 워크에서 자동으로 채워진 항목을 사용 하 여 예약 된 키 값 컬렉션 이기도 합니다.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2021-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```
### <a name="mapping-with-json-path"></a>JSON 경로를 사용 하 여 매핑

현재 지원 되는 세 가지 장치 콘텐츠 매핑 형식은 모두 JSON 경로를 사용 하 여 필수 매핑과 추출 된 값을 일치 시킵니다. JSON 경로에 대 한 자세한 내용은 [여기](https://goessner.net/articles/JsonPath/)를 참조 하세요. 세 가지 템플릿 형식은 모두 json [.net 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용 하 여 json 경로 식을 확인 합니다.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate를 사용 하면 JSON 경로를 사용 하 여 이벤트 허브 메시지에서 값을 비교 하 고 추출할 수 있습니다.

| 속성 | Description |예제 |
| --- | --- | --- |
|**TypeName**|템플릿과 일치 하는 측정값과 연결할 형식입니다.|`heartrate`
|**TypeMatchExpression**|이벤트 허브 페이로드에 대해 평가 되는 JSON 경로 식입니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 모든 후속 식은 여기에 일치 하는 추출 된 JToken에 대해 평가 됩니다.|`$..[?(@heartRate)]`
|**TimestampExpression**|측정 OccurenceTimeUtc의 타임 스탬프 값을 추출 하는 JSON 경로 식입니다.|`$.endDate`
|**DeviceIdExpression**|장치 식별자를 추출 하는 JSON 경로 식입니다.|`$.deviceId`
|**PatientIdExpression**|*선택 사항*: 환자 식별자를 추출 하는 JSON 경로 식입니다.|`$.patientId`
|**EncounterIdExpression**|*선택 사항*: 발생 식별자를 추출 하는 JSON 경로 식입니다.|`$.encounterId`
|**값 []. ValueName**|후속 식에 의해 추출 된 값과 연결할 이름입니다. FHIR 대상 매핑의 필수 값/구성 요소를 바인딩하는 데 사용 됩니다. |`hr`
|**값 []. ValueExpression**|필요한 값을 추출 하는 JSON 경로 식입니다.|`$.heartRate`
|**값 []. 필수**|에는 값이 페이로드에 있어야 합니다.  이러한 항목이 없으면 측정이 생성 되지 않고 InvalidOperationException이 throw 됩니다.|`true`

##### <a name="examples"></a>예제

**하트 요금**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
**블러드 압력**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
**단일 메시지에서 여러 측정값 Project**

*Message*

```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2021-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿 1*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

*템플릿 2*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
**메시지의 배열에서 여러 측정값 Project**

*Message*

```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2021-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2021-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2021-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*템플릿*

```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate는 DeviceIdExpression 및 TimestampExpression가 필요 하지 않은 경우를 제외 하 고 JsonPathContentTemplate와 비슷합니다.

이 템플릿을 사용할 때 평가 되는 메시지는 [Azure IoT 허브 장치 sdk](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)의 [데이터 내보내기 (레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용 하 여 전송 된 것입니다. 이러한 Sdk를 사용 하는 경우 장치 id (Azure Iot Hub/Central의 장치 식별자가 대상 FHIR 서비스의 장치 리소스에 대 한 식별자로 등록 된 것으로 가정) 및 메시지의 타임 스탬프를 알 수 있습니다. Azure IoT 허브 장치 sdk를 사용 중이지만 장치 id 또는 측정 타임 스탬프에 대 한 메시지 본문의 사용자 지정 속성을 사용 하는 경우에도 JsonPathContentTemplate를 사용할 수 있습니다.

> [!NOTE]
> 를 사용 하는 경우 `IotJsonPathContentTemplate` 는 `TypeMatchExpression` 전체 메시지를 jtoken로 확인 해야 합니다. 자세한 내용은 다음 예제를 참조 하세요.

##### <a name="examples"></a>예제

**하트 요금**

*Message*

```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*템플릿*

```json

    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**블러드 압력**

*Message*

```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2021-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*템플릿*

```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

또한 IotCentralJsonPathContentTemplate에는 DeviceIdExpression 및 TimestampExpression가 필요 하지 않습니다. 이는 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)의 [데이터 내보내기](../../iot-central/core/howto-export-data.md) 기능을 통해 평가할 메시지가 전송 될 때 사용 됩니다. 이 기능을 사용 하는 경우 장치 id (Azure Iot Central의 장치 식별자가 대상 FHIR 서버에서 장치 리소스에 대 한 식별자로 등록 됨) 및 메시지의 타임 스탬프를 알 수 있습니다. Azure IoT Central의 데이터 내보내기 기능을 사용 중이지만 장치 id 또는 측정 타임 스탬프에 대 한 메시지 본문의 사용자 지정 속성을 사용 하는 경우에도 JsonPathContentTemplate를 사용할 수 있습니다.

> [!NOTE]
> IotCentralJsonPathContentTemplate를 사용 하는 경우 TypeMatchExpression는 전체 메시지를 JToken로 확인 해야 합니다. 자세한 내용은 다음 예제를 참조 하세요.
 
##### <a name="examples"></a>예제

**하트 요금**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*템플릿*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

**블러드 압력**

*Message*

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2021-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*템플릿*

```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용 하는 방법](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.