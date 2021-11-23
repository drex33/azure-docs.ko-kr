---
title: IoT 커넥터의 FHIR 대상 매핑 - Azure Healthcare API
description: 이 문서에서는 Azure Healthcare API IoT 커넥터에서 FHIR 대상 매핑을 구성하고 사용하는 방법을 설명합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 9f6374ad6ba021e6f67f1ea9780aa90d49a7f5a2
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940299"
---
# <a name="how-to-use-the-fhir-destination-mappings"></a>FHIR 대상 매핑을 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 전자 의료 기록 교환(FHIR&#174;) 대상 매핑을 사용하여 IoT 커넥터를 구성하는 방법을 설명합니다.

> [!TIP]
> [IoT 커넥터](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위해 IoMT 커넥터 데이터 매퍼 도구를 확인하세요. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 오픈 [소스 버전의](https://github.com/microsoft/iomt-fhir) IoT 커넥터와 함께 사용합니다.

다음은 IoT 커넥터 내에서 정규화 및 변환 프로세스 중에 발생하는 작업의 개념 예제입니다.

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT 데이터 정규화 흐름 예제1" lightbox="media/iot-data-normalization-high-level.png":::

## <a name="fhir-destination-mappings"></a>FHIR 대상 매핑

디바이스 콘텐츠가 정규화된 모델로 추출되면 데이터는 디바이스 식별자, 측정 유형 및 기간에 따라 수집되고 그룹화됩니다. 이 그룹화의 출력은 FHIR 리소스(현재[관찰)로](https://www.hl7.org/fhir/observation.html) 변환하기 위해 전송됩니다. FHIR 대상 매핑 템플릿은 데이터가 FHIR 관찰에 매핑되는 방법을 제어합니다. 시점 또는 1시간 동안 관찰을 만들어야 합니까? 관찰에 추가해야 하는 코드는 무엇인가요? 값을 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 또는 Quantity로 표시해야 [합니까?](https://www.hl7.org/fhir/datatypes.html#Quantity) 이러한 데이터 형식은 FHIR 대상 매핑 구성 컨트롤의 모든 옵션입니다.

> [!NOTE]
> 매핑은 기본 Blob Storage에 저장되고 컴퓨팅 실행당 Blob에서 로드됩니다. 업데이트되면 즉시 적용됩니다. 

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate

CodeValueFhirTemplate은 현재 FHIR 대상 매핑에서 지원되는 유일한 템플릿입니다.  이를 통해 코드, 유효 기간 및 관찰 값을 정의할 수 있습니다. [SampledData,](https://www.hl7.org/fhir/datatypes.html#SampledData) [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)및 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)등의 여러 값 형식이 지원됩니다. 이러한 구성 가능한 값과 함께 관찰 리소스의 식별자 및 적절한 디바이스 및 환자 리소스에 대한 연결이 자동으로 처리됩니다.

| 속성 | Description 
| --- | ---
|**TypeName**| 이 템플릿이 바인딩해야 하는 측정 형식입니다. 이 형식을 출력하는 디바이스 매핑 템플릿이 하나 이상 있어야 합니다.
|**PeriodInterval**|생성된 관찰이 나타내는 기간입니다. 지원되는 값은 0(인스턴스), 60(시간), 1440(일)입니다.
|**범주**|생성된 관찰 유형을 분류할 [수 있는 CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 수입니다.
|**코드**|생성된 [관찰에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**Codes[]. 코드**|코딩에 대한 [코드입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 시스템**|코딩에 대한 [시스템입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 표시**|코딩 에 대한 [표시입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**값**|관찰에서 추출하고 나타낼 값입니다. 자세한 내용은 [값 형식 템플릿을 참조하세요.](#value-type-templates)
|**Components**|*선택적:* 관찰에서 만들 하나 이상의 구성 요소입니다.
|**구성 요소[]. 코드**|구성 [요소에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**구성 요소[]. 값**|구성 요소에서 추출하고 나타낼 값입니다. 자세한 내용은 [값 형식 템플릿을 참조하세요.](#value-type-templates)

### <a name="value-type-templates"></a>값 형식 템플릿

다음은 현재 지원되는 값 형식 템플릿입니다.

#### <a name="sampleddata"></a>SampledData

[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 데이터 형식을 나타냅니다. 관찰 측정값은 지정 시간에 시작하여 정의된 기간을 사용하여 앞으로 증분하는 값 스트림에 기록됩니다. 값이 없으면 `E` 가 데이터 스트림에 기록됩니다. 마침표가 두 개 이상의 값이 데이터 스트림에서 동일한 위치를 차지하게 되면 최신 값이 사용됩니다. SampledData를 사용한 관찰이 업데이트되면 동일한 논리가 적용됩니다.

| 속성 | Description 
| --- | ---
|**DefaultPeriod**|사용할 기본 기간(밀리초)입니다. 
|**단위**|SampledData의 원점에서 설정할 단위입니다. 

#### <a name="quantity"></a>수량

Quantity FHIR 데이터 [형식을](http://hl7.org/fhir/datatypes.html#Quantity) 나타냅니다. 그룹화에 두 개 이상의 값이 있는 경우 첫 번째 값만 사용됩니다. 동일한 관찰에 매핑되는 새 값이 도착하면 이전 값을 덮어쓰게 됩니다.

| 속성 | Description 
| --- | --- 
|**단위**| 단위 표현입니다.
|**코드**| 단위의 코딩된 형식입니다.
|**System**| 코딩된 단위 폼을 정의하는 시스템입니다.

### <a name="codeableconcept"></a>CodeableConcept

[CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 데이터 형식을 나타냅니다. 실제 값은 사용되지 않습니다.

| 속성 | Description 
| --- | --- 
|**Text**|일반 텍스트 표현입니다. 
|**코드**|생성된 [관찰에](http://hl7.org/fhir/datatypes-definitions.html#coding) 적용할 하나 이상의 코딩입니다.
|**Codes[]. 코드**|코딩에 대한 [코드입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 시스템**|코딩에 대한 [시스템입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)
|**Codes[]. 표시**|코딩 에 대한 [표시입니다.](http://hl7.org/fhir/datatypes-definitions.html#coding)

### <a name="examples"></a>예

**심박수 - SampledData**

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

**단계 - SampledData**

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

**압력 - SampledData**

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

**압력 - 수량**

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

> [!TIP]
> 일반적인 오류 및 문제 해결에 대 한 지원은 IoT 커넥터 [문제 해결 가이드](./iot-troubleshoot-guide.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 대상 매핑을 사용 하는 방법을 알아보았습니다. 장치 매핑을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[장치 매핑을 사용 하는 방법](how-to-use-device-mappings.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
