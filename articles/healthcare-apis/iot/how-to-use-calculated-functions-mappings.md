---
title: IoT Connector 장치 매핑의 CalculatedContentTemplate 매핑-Azure 의료 api
description: 이 문서에서는 IoT 커넥터 장치 매핑 템플릿과 함께 CalculatedContentTemplate 매핑을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 3e1ccf989bcd67e5e45d381ae681287730cad1d2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733502"
---
# <a name="how-to-use-calculatedcontenttemplate-mappings"></a>CalculatedContentTemplate 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

이 문서에서는 IoT 커넥터 장치 매핑 템플릿과 함께 CalculatedContentTemplate 매핑을 사용 하는 방법을 설명 합니다.

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

IoT 커넥터 사용자 지정 함수 집합도 사용할 수 있습니다. 이러한 사용자 지정 함수는 JmesPath 사양의 일부로 제공 되는 함수 외부에 있습니다. 사용자 지정 함수에 대 한 자세한 내용은 [IoT Connector 사용자 지정 함수](./how-to-use-custom-functions.md)를 참조 하세요.

### <a name="matched-token"></a>일치 토큰

**TypeMatchExpression** 는 들어오는 EventData 페이로드에 대해 평가 됩니다. 일치 하는 JToken이 있으면 템플릿이 일치 하는 것으로 간주 됩니다. 

모든 이후 식은 새 JToken에 대해 평가 됩니다. 이 새 JToken은 여기에 일치 하는 원래 EventData 페이로드 및 추출 된 JToken을 모두 포함 합니다. 

이러한 방식으로 원래 페이로드 및 일치 하는 개체는 이후의 각 식에서 사용할 수 있습니다. 추출 된 JToken은 **Matchedtoken** 속성으로 사용할 수 있습니다.

이 예제 메시지를 제공 합니다.

*메시지*

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

And

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

*메시지*

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

*메시지*

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

**단일 메시지에서 여러 측정값 Project**

*메시지*

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

**메시지의 배열에서 여러 측정값 Project**

*메시지*

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

**일치 하는 토큰 및 원래 이벤트의 데이터 Project**

*메시지*

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

아래 예제에서 높이 데이터는 인치 또는 미터 단위로 도착 합니다. 정규화 된 모든 높이 데이터를 미터 단위로 원합니다. 이 결과를 얻기 위해 인치 단위의 높이 데이터만 대상으로 하는 템플릿을 만들고이를 미터 단위로 변환 합니다. 다른 템플릿에서는 높이 데이터를 미터 단위로 대상으로 지정 하 고 단순히 있는 그대로 저장 합니다.

*메시지*

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

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
