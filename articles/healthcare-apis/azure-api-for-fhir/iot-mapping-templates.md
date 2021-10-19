---
title: '개념: fhir 용 Azure API의 fhir (미리 보기) 기능에 대 한 Azure IoT 커넥터에서 템플릿 매핑'
description: fhir (미리 보기)에 대 한 Azure IoT 커넥터에서 두 가지 유형의 매핑 템플릿을 만드는 방법을 알아봅니다. 디바이스 매핑 템플릿은 디바이스 데이터를 정규화된 스키마로 변환합니다. FHIR 매핑 템플릿은 정규화된 메시지를 FHIR 기반 관찰 리소스로 변환합니다.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: rabhaiya
ms.openlocfilehash: 4b32bf7a505fb7ca3958d8add69e3deb25cee0a1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748750"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Azure IoT Connector for FHIR(미리 보기) 매핑 템플릿
이 문서에서는 매핑 템플릿을 사용 하 여 신속한 의료 상호 운용성 리소스 (fhir&#174;) *에 대 한 Azure IoT 커넥터를 구성 하는 방법을 자세히 설명 합니다.

fhir 용 Azure IoT 커넥터에는 두 가지 유형의 JSON 기반 매핑 템플릿이 필요 합니다. 첫 번째 유형인 **장치 매핑은** `devicedata` Azure Event Hub 끝점으로 전송 되는 장치 페이로드를 매핑합니다. 유형, 장치 식별자, 측정 날짜 시간 및 측정 값을 추출 합니다. 두 번째 유형인 **fhir 매핑은** fhir 리소스에 대 한 매핑을 제어 합니다. 이를 통해 관찰 기간의 길이, 값을 저장 하는 데 사용 되는 FHIR 데이터 형식 및 용어 코드를 구성할 수 있습니다. 

매핑 템플릿은 해당 형식에 따라 JSON 문서로 구성 됩니다. 그런 다음 이러한 JSON 문서는 Azure Portal를 통해 fhir 용 Azure IoT 커넥터에 추가 됩니다. 장치 매핑 문서는 **장치 매핑 구성** 페이지 및 Fhir 매핑 **구성** 페이지를 통해 추가 됩니다.

> [!NOTE]
> 매핑 템플릿은 기본 blob 저장소에 저장 되 고 계산 실행 당 blob에서 로드 됩니다. 업데이트 되 면 즉시 적용 됩니다. 

## <a name="device-mapping"></a>디바이스 매핑
장치 매핑은 추가 평가를 위해 장치 콘텐츠를 공용 형식으로 추출 하는 매핑 기능을 제공 합니다. 받은 각 메시지는 모든 템플릿에 대해 평가 됩니다. 이 접근 방식을 사용 하면 단일 인바운드 메시지를 여러 아웃 바운드 메시지에 프로젝션 할 수 있으며 나중에이를 FHIR의 다른 관찰에 매핑할 수 있습니다. 결과는 템플릿에서 구문 분석 된 값을 나타내는 정규화 된 데이터 개체입니다. 정규화 된 데이터 모델에는 몇 가지 필수 속성을 찾아서 추출 해야 합니다.

| 속성 | 설명 |
| - | - |
|**Type**|측정값을 분류 하는 이름/형식입니다. 이 값은 필요한 FHIR 매핑 템플릿에 바인딩하는 데 사용 됩니다.  여러 개의 템플릿이 동일한 형식으로 출력 될 수 있으므로 여러 장치에서 서로 다른 표현을 단일 공통 출력으로 매핑할 수 있습니다.|
|**OccurenceTimeUtc**|측정이 발생 한 시간입니다.|
|**DeviceId**|장치의 식별자입니다. 이 값은 대상 FHIR 서버에 있는 장치 리소스의 식별자와 일치 해야 합니다.|
 |**속성**|만든 관찰 리소스에 값을 저장할 수 있도록 적어도 하나 이상의 속성을 추출 합니다.  속성은 정규화 중에 추출 된 키 값 쌍의 컬렉션입니다.|

다음은 정규화 중에 발생 하는 작업의 개념 예입니다.

![정규화 예제](media/concepts-iot-mapping-templates/normalization-example.png)

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
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>JSON 경로를 사용 하 여 매핑
현재 지원 되는 세 가지 장치 콘텐츠 템플릿 형식은 모두 JSON 경로를 사용 하 여 필수 템플릿과 추출 된 값을 일치 시킵니다. JSON 경로에 대 한 자세한 내용은 [여기](https://goessner.net/articles/JsonPath/)를 참조 하세요. 세 가지 템플릿 형식은 모두 json [.net 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용 하 여 json 경로 식을 확인 합니다.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate를 사용 하면 JSON 경로를 사용 하 여 이벤트 허브 메시지에서 값을 비교 하 고 추출할 수 있습니다.

| 속성 | 설명 |예제 |
| --- | --- | --- |
|**TypeName**|템플릿과 일치 하는 측정값과 연결할 형식입니다.|`heartrate`
|**TypeMatchExpression**|이벤트 허브 페이로드에 대해 평가 되는 JSON 경로 식입니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 모든 후속 식은 여기에 일치 하는 추출 된 JToken에 대해 평가 됩니다.|`$..[?(@heartRate)]`
|**TimestampExpression**|측정 OccurenceTimeUtc의 타임 스탬프 값을 추출 하는 JSON 경로 식입니다.|`$.endDate`
|**DeviceIdExpression**|장치 식별자를 추출 하는 JSON 경로 식입니다.|`$.deviceId`
|**PatientIdExpression**|*선택 사항*: 환자 식별자를 추출 하는 JSON 경로 식입니다.|`$.patientId`
|**EncounterIdExpression**|*선택 사항*: 발생 식별자를 추출 하는 JSON 경로 식입니다.|`$.encounterId`
|**값 []. ValueName**|후속 식에 의해 추출 된 값과 연결할 이름입니다. FHIR 매핑 템플릿의 필수 값/구성 요소를 바인딩하는 데 사용 됩니다. |`hr`
|**값 []. ValueExpression**|필요한 값을 추출 하는 JSON 경로 식입니다.|`$.heartRate`
|**값 []. 필수**|에는 값이 페이로드에 있어야 합니다.  이러한 항목이 없으면 측정이 생성 되지 않고 InvalidOperationException이 throw 됩니다.|`true`

##### <a name="examples"></a>예제
---
**하트 요금**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---
**블러드 압력**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---

**단일 메시지에서 여러 측정값 Project**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
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
---

**메시지의 배열에서 여러 측정값 Project**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
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

이 템플릿을 사용할 때 평가 되는 메시지는 [Azure IoT 허브 장치 sdk](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)의 [데이터 내보내기 (레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용 하 여 전송 된 것입니다. 이러한 Sdk를 사용 하는 경우 장치 id (Azure Iot Hub/Central의 장치 식별자가 대상 FHIR 서버에서 장치 리소스에 대 한 식별자로 등록 됨) 및 메시지의 타임 스탬프를 알 수 있습니다. Azure IoT 허브 장치 sdk를 사용 중이지만 장치 id 또는 측정 타임 스탬프에 대 한 메시지 본문의 사용자 지정 속성을 사용 하는 경우에도 JsonPathContentTemplate를 사용할 수 있습니다.

*참고: IotJsonPathContentTemplate를 사용 하는 경우 TypeMatchExpression는 전체 메시지를 JToken로 확인 해야 합니다. 아래 예제를 참조 하세요.* 
##### <a name="examples"></a>예제
---
**하트 요금**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*템플릿*
```json
{
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
---
**블러드 압력**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
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

또한 IotCentralJsonPathContentTemplate에는 DeviceIdExpression 및 TimestampExpression가 필요 하지 않으며 평가할 메시지가 [Azure IoT Central](../../iot-central/core/overview-iot-central.md)의 [데이터 내보내기](../../iot-central/core/howto-export-data.md) 기능을 통해 전송 될 때 사용 됩니다. 이 기능을 사용 하는 경우 장치 id (Azure Iot Central의 장치 식별자가 대상 FHIR 서버에서 장치 리소스에 대 한 식별자로 등록 됨) 및 메시지의 타임 스탬프를 알 수 있습니다. Azure IoT Central의 데이터 내보내기 기능을 사용 중이지만 장치 id 또는 측정 타임 스탬프에 대 한 메시지 본문의 사용자 지정 속성을 사용 하는 경우에도 JsonPathContentTemplate를 사용할 수 있습니다.

*참고: IotCentralJsonPathContentTemplate를 사용 하는 경우 TypeMatchExpression는 전체 메시지를 JToken로 확인 해야 합니다. 아래 예제를 참조 하세요.* 
##### <a name="examples"></a>예제
---
**하트 요금**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
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
---
**블러드 압력**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
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

## <a name="fhir-mapping"></a>FHIR 매핑
장치 콘텐츠가 정규화 된 모델로 추출 되 면 장치 식별자, 측정 유형 및 기간에 따라 데이터가 수집 되 고 그룹화 됩니다. 이 그룹화의 출력은 FHIR 리소스 (현재[관찰](https://www.hl7.org/fhir/observation.html) )로 변환 하기 위해 전송 됩니다. 여기서 FHIR 매핑 템플릿은 데이터가 FHIR 관찰에 매핑되는 방식을 제어 합니다. 특정 시점 또는 특정 시간에 대 한 관찰을 만들어야 하나요? 관찰에 추가 해야 하는 코드는 무엇입니까? 값은 [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData) 또는 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)로 표시 되어야 하나요? 이러한 데이터 형식은 모두 FHIR 매핑 구성 컨트롤의 모든 옵션입니다.

### <a name="codevaluefhirtemplate"></a>Code-efrtemplate
현재이 템플릿은 현재 FHIR 매핑에서 지원 되는 유일한 템플릿입니다.  코드, 유효 기간 및 관찰 값을 정의할 수 있습니다. 여러 값 형식이 지원 됩니다. [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)및 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). 이러한 구성 가능한 값과 함께 관찰 리소스의 식별자와 적절 한 장치 및 환자 리소스에 대 한 링크가 자동으로 처리 됩니다.

| 속성 | 설명 
| --- | ---
|**TypeName**| 이 템플릿이 바인딩될 측정 형식입니다. 이 형식을 출력 하는 장치 매핑 템플릿이 하나 이상 있어야 합니다.
|**PeriodInterval**|관찰을 만든 기간이 표시 되는 시간입니다. 지원 되는 값은 0 (인스턴스), 60 (1 시간), 1440 (일)입니다.
|**범주**|생성 되는 관찰 유형을 분류 하기 위한 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 수
|**코드가**|만든 관찰에 적용할 하나 이상의 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 입니다.
|**코드 []. Code**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 코드입니다.
|**코드 []. 컴퓨터**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)을 위한 시스템입니다.
|**코드 []. 표시가**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 표시입니다.
|**값**|관찰에서 추출 하 고 표시할 값입니다. 자세한 내용은 [값 형식 템플릿](#valuetypes)을 참조 하세요.
|**Components**|*선택 사항:* 관찰 시 만들 하나 이상의 구성 요소입니다.
|**구성 요소 []. 코드가**|구성 요소에 적용할 하나 이상의 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 입니다.
|**구성 요소 []. 기본값**|구성 요소에서 추출 하 고 표시할 값입니다. 자세한 내용은 [값 형식 템플릿](#valuetypes)을 참조 하세요.

### <a name="value-type-templates"></a>값 형식 템플릿 <a name="valuetypes"></a>
다음은 현재 지원 되는 값 형식 템플릿입니다. 나중에 추가 템플릿이 추가 될 수 있습니다.
#### <a name="sampleddata"></a>SampledData
[Sampleddata](http://hl7.org/fhir/datatypes.html#SampledData) fhir 데이터 형식을 나타냅니다. 관찰 측정은 특정 시점에서 시작 하 고 정의 된 기간을 사용 하 여 전달 하는 값 스트림에 기록 됩니다. 값이 없는 경우이 `E` 데이터 스트림에 기록 됩니다. 두 개 이상의 값이 데이터 스트림의 동일한 위치를 차지 하는 기간이 면 최신 값이 사용 됩니다. SampledData를 사용 하는 관찰이 업데이트 되는 경우에도 동일한 논리가 적용 됩니다.

| 속성 | 설명 
| --- | ---
|**DefaultPeriod**|사용할 기본 기간 (밀리초)입니다. 
|**단위**|SampledData의 원점에 설정할 단위입니다. 

#### <a name="quantity"></a>수량
[수량](http://hl7.org/fhir/datatypes.html#Quantity) fhir 데이터 형식을 나타냅니다. 그룹화에 두 개 이상의 값이 있는 경우 첫 번째 값만 사용 됩니다. 동일한 관찰에 매핑되는 새 값이 도착 하면 이전 값을 덮어씁니다.

| 속성 | 설명 
| --- | --- 
|**단위**| 단위 표현입니다.
|**코드**| 단위의 코딩 된 형태입니다.
|**System**| 코딩 된 단위 형식을 정의 하는 시스템입니다.

### <a name="codeableconcept"></a>CodeableConcept
[CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) fhir 데이터 형식을 나타냅니다. 실제 값은 사용 되지 않습니다.

| 속성 | 설명 
| --- | --- 
|**텍스트**|일반 텍스트 표현입니다. 
|**코드가**|만든 관찰에 적용할 하나 이상의 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 입니다.
|**코드 []. Code**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 코드입니다.
|**코드 []. 컴퓨터**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)을 위한 시스템입니다.
|**코드 []. 표시가**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 표시입니다.

### <a name="examples"></a>예제
**하트 비트 전송률-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**단계-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**블러드 압력-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**블러드 압력-수량**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**디바이스 제거 - CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>다음 단계

Azure IoT Connector for FHIR(미리 보기)에 대한 질문과 대답을 확인하세요.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR FAQ](fhir-faq.yml)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.