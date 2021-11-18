---
title: IoT 커넥터 디바이스 매핑의 JsonPathContentTemplate 매핑 - Azure Healthcare API
description: 이 문서에서는 IoT 커넥터 디바이스 매핑 템플릿을 사용하여 JsonPathContentTemplate 매핑을 사용하는 방법을 설명합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 7def9d932f36a7720d0da7e19a29cf72f2ef0b98
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733488"
---
# <a name="how-to-use-jsonpathcontenttemplate-mappings"></a>JsonPathContentTemplate 매핑을 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

> [!TIP]
> [IoT 커넥터](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위해 IoMT 커넥터 데이터 매퍼 도구를 확인하세요. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 오픈 [소스 버전의](https://github.com/microsoft/iomt-fhir) IoT 커넥터와 함께 사용합니다.

이 문서에서는 IoT 커넥터 디바이스 매핑 템플릿을 사용하여 JsonPathContentTemplate 매핑을 사용하는 방법을 설명합니다.

## <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate

JsonPathContentTemplate을 사용하면 JSONPath를 사용하여 Azure Event Hub 메시지에서 값을 일치시키고 추출할 수 있습니다.

|속성|설명|예제|
|--------|-----------|-------|
|TypeName|템플릿과 일치하는 측정값과 연결할 형식입니다.|`heartrate`|
|TypeMatchExpression|EventData 페이로드에 대해 계산되는 JSONPath 식입니다. 일치하는 JToken이 발견되면 템플릿이 일치하는 것으로 간주됩니다. 이후의 모든 식은 여기에서 일치하는 추출된 JToken에 대해 평가됩니다.|`$..[?(@heartRate)]`|
|TimestampExpression|측정값의 OccurrenceTimeUtc에 대한 타임스탬프 값을 추출하는 JSONPath 식입니다.|`$.matchedToken.endDate`|
|DeviceIdExpression|디바이스 식별자를 추출하는 JSONPath 식입니다.|`$.matchedToken.deviceId`|
|PatientIdExpression|IdentityResolution이 **만들기** 모드인 경우 *필수이고* IdentityResolution이 **조회** 모드인 경우 *선택 사항입니다.* 환자 식별자를 추출할 식입니다.|`$.matchedToken.patientId`|
|EncounterIdExpression|*선택 사항:* 발생 식별자를 추출하는 식입니다.|`$.matchedToken.encounterId`|
|CorrelationIdExpression|*선택 사항:* 상관 관계 식별자를 추출하는 식입니다. 이 출력을 사용하여 FHIR 대상 매핑에서 값을 단일 관찰로 그룹화할 수 있습니다.|`$.matchedToken.correlationId`|
|값[]. ValueName|다음 식에서 추출한 값과 연결할 이름입니다. FHIR 대상 매핑 템플릿에서 원하는 값/구성 요소를 바인딩하는 데 사용됩니다.|`hr`|
|값[]. ValueExpression|원하는 값을 추출하는 JSONPath 식입니다.|`$.matchedToken.heartRate`|
|값[]. 필수|페이로드에 값이 있어야 합니다. 찾을 수 없으면 측정값이 생성되지 않고 InvalidOperationException이 생성됩니다.|`true`|

### <a name="examples"></a>예제

**심박수**

*메시지*

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
**혈압**

*메시지*

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

*메시지*

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

*메시지*

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

## <a name="next-steps"></a>다음 단계

이 문서에서는 디바이스 매핑을 사용하는 방법을 배웠습니다. FHIR 대상 매핑을 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
