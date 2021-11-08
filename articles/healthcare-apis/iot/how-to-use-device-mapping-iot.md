---
title: IoT Connector의 장치 매핑 템플릿-Azure 의료 api
description: 이 문서에서는 IoT Connector에서 장치 매핑 템플릿을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: f11770a05d2429c87647b65a828f6477f1b1b8cb
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988195"
---
# <a name="how-to-use-device-mappings"></a>장치 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

IoT 커넥터에는 두 가지 유형의 JSON 기반 매핑이 필요 합니다. 첫 번째 유형인 **장치 매핑은** `devicedata` Azure Event Hub 끝점으로 전송 되는 장치 페이로드를 매핑합니다. 유형, 장치 식별자, 측정 날짜 시간 및 측정 값을 추출 합니다. 

두 번째 유형인 **fhir&#174; (신속한 의료 상호 운용성 리소스) 대상 매핑은** fhir 리소스에 대 한 매핑을 제어 합니다. 이를 통해 관찰 기간의 길이, 값을 저장 하는 데 사용 되는 FHIR 데이터 형식 및 용어 코드를 구성할 수 있습니다. 

두 가지 유형의 매핑은 해당 형식에 따라 JSON 문서로 구성 됩니다. 그런 다음 이러한 JSON 문서를 Azure Portal 통해 IoT 커넥터에 추가 합니다. 장치 매핑 문서는 **대상** 페이지를 통해 **장치 매핑** 페이지 및 fhir 대상 매핑 문서를 통해 추가 됩니다.

> [!NOTE]
> 매핑은 기본 blob 저장소에 저장 되 고 계산 실행 당 blob에서 로드 됩니다. 업데이트 되 면 즉시 적용 됩니다. 

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

## <a name="device-mappings-overview"></a>장치 매핑 개요

장치 매핑은 추가 평가를 위해 장치 메시지 콘텐츠를 공용 형식으로 추출 하는 기능을 제공 합니다. 수신 된 각 장치 메시지는 모든 장치 매핑 템플릿에 대해 평가 됩니다. 

단일 인바운드 장치 메시지는 나중에 FHIR 서비스의 다른 관찰에 매핑되는 여러 아웃 바운드 메시지로 분리 될 수 있습니다. 

결과는 템플릿에서 구문 분석 된 값을 나타내는 정규화 된 데이터 개체입니다. 

정규화 된 데이터 모델에는 몇 가지 필수 속성을 찾아서 추출 해야 합니다.

|속성|설명|
|--------|-----------|
|**Type**|측정값을 분류 하는 이름/형식입니다. 이 값은 필요한 FHIR 대상 매핑에 바인딩하는 데 사용 됩니다. 여러 매핑을 동일한 형식으로 출력 하 여 여러 장치에 있는 다양 한 표현을 단일 공통 출력에 매핑할 수 있습니다.|
|**OccurenceTimeUtc**|측정이 발생 한 시간입니다.|
|**DeviceId**|장치의 식별자입니다. 이 값은 대상 FHIR 서비스에 있는 장치 리소스의 식별자와 일치 해야 합니다.|
|**속성**|만든 관찰 리소스에 값을 저장할 수 있도록 적어도 하나 이상의 속성을 추출 합니다. 속성은 정규화 중에 추출 된 키 값 쌍의 컬렉션입니다.|

> [!IMPORTANT]
> 전체 정규화 된 모델은 [Imeasurement](https://github.com/microsoft/iomt-fhir/blob/master/src/lib/Microsoft.Health.Fhir.Ingest.Schema/IMeasurement.cs) 인터페이스에 의해 정의 됩니다.

다음은 IoT 커넥터 내에서 표준화 및 변환 프로세스 중에 발생 하는 상황에 대 한 개념 예입니다.

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT 데이터 정규화 흐름 example1" lightbox="media/iot-data-normalization-high-level.png":::

:::image type="content" source="media/concepts-iot-mapping-templates/normalization-example.png" alt-text="IoT 데이터 정규화 흐름 example2" lightbox="media/concepts-iot-mapping-templates/normalization-example.png":::

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
## <a name="mapping-with-jsonpath"></a>JSONPath를 사용 하 여 매핑

현재 지원 되는 5 개의 장치 콘텐츠 매핑 형식은 JSONPath를 사용 하 여 필요한 매핑과 추출 된 값을 모두 사용 합니다. JSONPath에 대 한 자세한 내용은 [여기](https://goessner.net/articles/JsonPath/)를 참조 하세요. 5 가지 템플릿 형식은 모두 [JSON .net 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용 하 여 JSONPath 식을 확인 합니다.

장치 매핑 템플릿 내에서 하나 이상의 템플릿을 정의할 수 있습니다. 수신 된 각 이벤트 허브 장치 메시지는 모든 장치 매핑 템플릿에 대해 평가 됩니다. 

단일 인바운드 장치 메시지는 나중에 FHIR 서비스의 다른 관찰에 매핑되는 여러 아웃 바운드 메시지로 분리 될 수 있습니다. 

다양 한 템플릿 유형이 존재 하며 장치 매핑 파일을 빌드할 때 사용할 수 있습니다.

|이름                                                                     | 설명                                                                   |  
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------|
|[JsonPathContentTemplate](#jsonpathcontenttemplate)                      |JsonPath를 사용 하 여 식 작성을 지 원하는 템플릿입니다.                  
|[CollectionContentTemplate](#collectioncontenttemplate)                  |정규화 중에 사용 되는 템플릿 목록을 나타내는 데 사용 되는 템플릿입니다.                                                            |                                                           
|[CalculatedContentTemplate](#calculatedcontenttemplate)                  |여러 식 언어 중 하나를 사용 하 여 식 작성을 지 원하는 템플릿입니다. JmesPath 함수를 사용 하 여 데이터 변환을 지원 합니다.|
|[IotJsonPathContentTemplate](#iotjsonpathcontenttemplate)                |Azure iot Hub 또는 Azure Iot Central의 레거시 데이터 내보내기 기능에서 보낸 메시지를 지 원하는 템플릿입니다.                                        |
|[IotCentralJsonPathContentTemplate](#iotcentraljsonpathcontenttemplate)  |Azure Iot Central의 데이터 내보내기 기능을 통해 전송 되는 메시지를 지 원하는 템플릿입니다.|  

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate를 사용 하면 JSONPath를 사용 하 여 Azure 이벤트 허브 메시지에서 값을 비교 하 고 추출할 수 있습니다.

|속성|설명|예제|
|--------|-----------|-------|
|TypeName|템플릿과 일치 하는 측정값과 연결할 형식입니다.|`heartrate`|
|TypeMatchExpression|EventData 페이로드에 대해 평가 되는 JSONPath 식입니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 모든 이후 식은 여기에 일치 하는 추출 된 JToken에 대해 평가 됩니다.|`$..[?(@heartRate)]`|
|TimestampExpression|측정의 OccurrenceTimeUtc에 대 한 타임 스탬프 값을 추출 하는 JSONPath 식입니다.|`$.matchedToken.endDate`|
|DeviceIdExpression|장치 식별자를 추출 하는 JSONPath 식입니다.|`$.matchedToken.deviceId`|
|PatientIdExpression|IdentityResolution이 **만들기** 모드에 있을 때 *필요* 하며 IdentityResolution가 **조회** 모드일 때 *선택 사항* 입니다. 환자 식별자를 추출할 식입니다.|`$.matchedToken.patientId`|
|EncounterIdExpression|*선택 사항*: 발생 식별자를 추출 하는 식입니다.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*선택 사항*: 상관 관계 식별자를 추출 하는 식입니다. 이 출력을 사용 하 여 FHIR 대상 매핑의 단일 관찰으로 값을 그룹화 할 수 있습니다.|`$.matchedToken.correlationId`|
|값 []. ValueName|다음 식에 의해 추출 된 값과 연결할 이름입니다. FHIR 대상 매핑 템플릿에서 원하는 값/구성 요소를 바인딩하는 데 사용 됩니다.|`hr`|
|값 []. ValueExpression|원하는 값을 추출 하는 JSONPath 식입니다.|`$.matchedToken.heartRate`|
|값 []. 필수|에는 값이 페이로드에 있어야 합니다. 찾을 수 없는 경우에는 측정값이 생성 되지 않으며 InvalidOperationException 생성 됩니다.|`true`|

### <a name="examples"></a>예제

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

## <a name="collectioncontenttemplate"></a>CollectionContentTemplate

CollectionContentTemplate는 정규화 중에 사용 되는 템플릿 목록을 나타내는 데 사용할 수 있습니다.
                                                             
### <a name="example"></a>예제

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
  ]
}
```

## <a name="calculatedcontenttemplate"></a>CalculatedContentTemplate

IoT 커넥터는 원하는 템플릿과 일치 하 고 값을 추출 하는 식 기반 콘텐츠 템플릿을 제공 합니다. JSONPath 또는 JmesPath에서 **식을** 사용할 수 있습니다. 템플릿 내의 각 식은 자체 식 언어를 선택할 수 있습니다. 

> [!NOTE]
> 식 언어가 정의 되어 있지 않으면 템플릿에 대해 구성 된 기본 식 언어가 사용 됩니다. 기본값은 JSONPath 이지만 필요한 경우 덮어쓸 수 있습니다.

식은 다음과 같이 정의 됩니다.

```json
<name of expression> : {
        "value" : <the expression>,
        "language": <the expression language>
    }
```

아래 예제에서 *typeMatchExpression* 는 다음과 같이 정의 됩니다.

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": {
            "value" : "$..[?(@heartRate)]",
            "language": "JsonPath"
        },
        ...
    }
```
> [!TIP]
> 장치 매핑 템플릿에 사용할 기본 식 언어는 JsonPath입니다. JsonPath를 사용 하려는 경우 식만 제공 될 수 있습니다.

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

템플릿에 사용할 기본 식 언어는 매개 변수를 사용 하 여 명시적으로 설정할 수 있습니다 `defaultExpressionLanguage` .

```json
"templateType": "CalculatedContent",
    "template": {
        "typeName": "heartrate",
        "defaultExpressionLanguage": "JsonPath",
        "typeMatchExpression": "$..[?(@heartRate)]",
        ...
    }
```

CalculatedContentTemplate는 아래 정의 된 **식을** 사용 하 여 Azure 이벤트 허브 메시지의 값을 비교 하 고 추출할 수 있습니다.

|속성|설명|예제|
|--------|-----------|-------|
|TypeName|템플릿과 일치 하는 측정값과 연결할 형식입니다.|`heartrate`|
|TypeMatchExpression|EventData 페이로드에 대해 계산 되는 식입니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 모든 이후 식은 여기에 일치 하는 추출 된 JToken에 대해 평가 됩니다.|`$..[?(@heartRate)]`|
|TimestampExpression|측정 OccurrenceTimeUtc의 타임 스탬프 값을 추출 하는 식입니다.|`$.matchedToken.endDate`|
|DeviceIdExpression|장치 식별자를 추출 하는 식입니다.|`$.matchedToken.deviceId`|
|PatientIdExpression|IdentityResolution이 **만들기** 모드에 있을 때 *필요* 하며 IdentityResolution가 **조회** 모드일 때 *선택 사항* 입니다. 환자 식별자를 추출할 식입니다.|`$.matchedToken.patientId`|
|EncounterIdExpression|*선택 사항*: 발생 식별자를 추출 하는 식입니다.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*선택 사항*: 상관 관계 식별자를 추출 하는 식입니다. 이 출력을 사용 하 여 FHIR 대상 매핑의 단일 관찰으로 값을 그룹화 할 수 있습니다.|`$.matchedToken.correlationId`|
|값 []. ValueName|다음 식에 의해 추출 된 값과 연결할 이름입니다. FHIR 대상 매핑 템플릿에서 원하는 값/구성 요소를 바인딩하는 데 사용 됩니다.|`hr`|
|값 []. ValueExpression|원하는 값을 추출 하는 식입니다.|`$.matchedToken.heartRate`|
|값 []. 필수|에는 값이 페이로드에 있어야 합니다. 찾을 수 없는 경우에는 측정값이 생성 되지 않으며 InvalidOperationException 생성 됩니다.|`true`|

### <a name="expression-languages"></a>식 언어

식에 사용할 언어를 지정할 때는 다음 값이 유효 합니다.

| 식 언어 | 값        |
|---------------------|--------------|
| JSONPath            | **JsonPath** |
| JmesPath            | **JmesPath** |

>[!TIP]
>JSONPath에 대 한 자세한 내용은 [JSONPath](https://goessner.net/articles/JsonPath/)를 참조 하세요. [CalculatedContentTemplate](#calculatedcontenttemplate) 는 JSONPath 식을 확인 하는 데 [JSON .net 구현을](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 사용 합니다.
>
>JmesPath에 대 한 자세한 내용은 [JmesPath](https://jmespath.org/specification.html)를 참조 하세요. [CalculatedContentTemplate](#calculatedcontenttemplate) 는 JmesPath 식을 확인 하기 위해 [JmesPath .net 구현을](https://github.com/jdevillard/JmesPath.Net) 사용 합니다.

### <a name="custom-functions"></a>사용자 지정 함수

IoT 커넥터 사용자 지정 함수 집합도 사용할 수 있습니다. 이러한 사용자 지정 함수는 JmesPath 사양의 일부로 제공 되는 함수 외부에 있습니다. IoT 커넥터 사용자 지정 함수에 대 한 자세한 내용은 [iot connector customer 함수](./iot-connector-custom-functions.md)를 참조 하세요.

### <a name="matched-token"></a>일치 토큰

**TypeMatchExpression** 는 들어오는 EventData 페이로드에 대해 평가 됩니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 

모든 이후 식은 새 JToken에 대해 평가 됩니다. 이 새 JToken은 여기에 일치 하는 원래 EventData 페이로드 및 추출 된 JToken을 모두 포함 합니다. 

이러한 방식으로 원래 페이로드 및 일치 하는 개체는 이후의 각 식에서 사용할 수 있습니다. 추출 된 JToken은 **Matchedtoken** 속성으로 사용할 수 있습니다.

이 예제 메시지를 제공 합니다.

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*템플릿*

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
  ]
}
```

위의 식을 사용 하 여 두 개의 일치 항목을 추출 하 고 JTokens을 만드는 데 사용 합니다. 이후 식은 다음 JTokens을 사용 하 여 평가 됩니다.

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", 
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122",
        "diastolic": "82",
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "120",
      "diastolic": "80",
      "date": "2021-07-13T17:29:01.061144Z"
  }
}
```

and

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120",
        "diastolic": "80",
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", 
        "diastolic": "82", 
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {},
  "matchedToken" : {
      "systolic": "122",
      "diastolic": "82",
      "date": "2021-07-13T17:28:01.061122Z"
    }
  }
}
```

### <a name="examples"></a>예제

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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
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
        "systolic": "123", // Match
        "diastolic" : "87", // Match
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "bloodpressure",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**Project 단일 메시지의 여러 측정값**

*Message*

```json
{
    "Body": {
        "heartRate": "78", // Match (Template 1)
        "steps": "2", // Match (Template 2)
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    },
```

*템플릿 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.steps",
            "valueName": "steps"
          }
        ]
      }
    }
```

**Project 메시지의 배열에서 여러 측정값**

*Message*

```json
{
  "Body": [
    {
      "heartRate": "78", // Match
      "endDate": "2019-02-01T20:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "81", // Match
      "endDate": "2019-02-01T21:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "heartRate": "72", // Match
      "endDate": "2019-02-01T22:46:01.8750000Z",
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
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]", // Expression
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.heartRate",
            "valueName": "hr"
          }
        ]
      }
    }
```

**Project 일치 하는 토큰 및 원래 이벤트의 데이터**

*Message*

```json
{
  "Body": {
    "deviceId": "device123",
    "data": [
      {
        "systolic": "120", // Match
        "diastolic": "80", // Match 
        "date": "2021-07-13T17:29:01.061144Z"
      },
      {
        "systolic": "122", // Match
        "diastolic": "82", // Match
        "date": "2021-07-13T17:28:01.061122Z"
      }
    ]
  },
  "Properties": {},
  "SystemProperties": {}
}
```

*템플릿*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@systolic && @diastolic)]", // Expression
        "deviceIdExpression": "$.Body.deviceId", // This accesses the attribute 'deviceId' which belongs to the original event data
        "timestampExpression": "$.matchedToken.date", 
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.systolic",
            "valueName": "systolic"
          },
          {
            "required": "true",
            "valueExpression": "$.matchedToken.diastolic",
            "valueName": "diastolic"
          }
        ]
      }
    }
```

**들어오는 데이터 선택 및 변환**

아래 예제에서 높이 데이터는 인치 또는 미터로 도착합니다. 모든 정규화된 높이 데이터를 미터로 하기를 원합니다. 이 결과를 얻기 위해 인치의 높이 데이터만 대상으로 하고 미터로 변환하는 템플릿을 만듭니다. 또 다른 템플릿은 높이 데이터를 미터로 대상으로 지정하고 그대로 저장합니다.

*Message*

```json
{
  "Body": [
    {
      "height": "78",
      "unit": "inches", // Match (Template 1)
      "endDate": "2019-02-01T22:46:01.8750000Z",
      "deviceId": "device123"
    },
    {
      "height": "1.9304",
      "unit": "meters", // Match (Template 2)
      "endDate": "2019-02-01T23:46:01.8750000Z",
      "deviceId": "device123"
    }
  ],
  "Properties": {},
  "SystemProperties": {}
}
```

*템플릿 1*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'inches')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": {
              "value": "multiply(to_number(matchedToken.height), `0.0254`)", // Convert inches to meters. Notice we utilize JmesPath as that gives us access to transformation functions
              "language": "JmesPath"
            },
            "valueName": "height"
          }
        ]
      }
    }
```

*템플릿 2*

```json
    {
      "templateType": "CalculatedContent",
      "template": {
        "typeName": "heightInMeters",
        "typeMatchExpression": "$..[?(@unit == 'meters')]",
        "deviceIdExpression": "$.matchedToken.deviceId",
        "timestampExpression": "$.matchedToken.endDate",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.matchedToken.height", // Simply extract the height as it is already in meters
            "valueName": "height"
          }
        ]
      }
    }
```
## <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate은 및 를 제외하고 JsonPathContentTemplate과 `DeviceIdExpression` `TimestampExpression` 유사합니다.

이 템플릿을 사용하는 경우 평가 중인 메시지가 Azure IoT [Central의](../../iot-central/core/overview-iot-central.md) [Azure IoT Hub 디바이스 SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 또는 [데이터 내보내기(레거시)](../../iot-central/core/howto-export-data-legacy.md) 기능을 사용하여 전송되었다고 가정합니다. 

이러한 SDK를 사용하는 경우 메시지의 디바이스 ID 및 타임스탬프를 알 수 있습니다.

>[!IMPORTANT]
>대상 FHIR 서비스의 디바이스 리소스에 대한 식별자로 등록된 Azure Iot Hub 또는 Azure IoT Central의 디바이스 식별자를 사용하고 있는지 확인합니다.

Azure IoT Hub 디바이스 SDK를 사용하는 경우 디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문의 사용자 지정 속성을 사용한다고 가정할 때 JsonPathContentTemplate을 계속 사용할 수 있습니다.

> [!NOTE]
> 를 사용하는 경우 `IotJsonPathContentTemplate` 는 `TypeMatchExpression` 전체 메시지로 JToken으로 확인되어야 합니다. 자세한 내용은 다음 예제를 참조하세요.

### <a name="examples"></a>예제

**심박수**

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

**혈압**

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
## <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate에도 DeviceIdExpression 및 TimestampExpression이 필요하지 않습니다. 평가 중인 메시지가 [Azure IoT Central의](../../iot-central/core/overview-iot-central.md)데이터 [내보내기](../../iot-central/core/howto-export-data.md) 기능을 통해 전송될 때 사용됩니다. 

디바이스 ID 또는 측정 타임스탬프에 대한 메시지 본문에 Azure IoT Central의 데이터 내보내기 기능 및 사용자 지정 속성을 사용하는 경우에도 JsonPathContentTemplate을 사용할 수 있습니다.

> [!NOTE]
> 를 사용하는 경우 `IotCentralJsonPathContentTemplate` `TypeMatchExpression` 전체 메시지를 JToken으로 확인해야 합니다. 자세한 내용은 다음 예제를 참조하세요.
 
### <a name="examples"></a>예제

**심박수**

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
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
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

**혈압**

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
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
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

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 권한으로 사용됩니다.
