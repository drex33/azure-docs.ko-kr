---
title: IoT 커넥터의 대상 매핑-Azure 의료 Api
description: 이 문서에서는 IoT 커넥터에서 FHIR 대상 매핑 템플릿을 사용 하는 방법을 설명 합니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 0839376e1f04acc525cd7b33cd281edd3e66ee56
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990360"
---
# <a name="how-to-use-the-fhir-destination-mappings"></a>FHIR 대상 매핑을 사용 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 신속한 의료 상호 운용성 리소스 (FHIR&#174;) 대상 매핑을 사용 하 여 IoT 커넥터를 구성 하는 방법을 설명 합니다.

> [!TIP]
> IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위해 [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) 도구를 확인 하세요. Azure Portal에서 IoT connector로 업로드 하기 위한 매핑을 내보내거나, IoT 커넥터의 [오픈 소스 버전과](https://github.com/microsoft/iomt-fhir) 함께 사용 합니다.

다음은 IoT 커넥터 내에서 표준화 및 변환 프로세스 중에 발생 하는 상황에 대 한 개념 예입니다.

:::image type="content" source="media/iot-data-normalization-high-level.png" alt-text="IoT 데이터 정규화 흐름 example1" lightbox="media/iot-data-normalization-high-level.png":::

## <a name="fhir-destination-mappings"></a>대상 매핑

장치 콘텐츠가 정규화 된 모델로 추출 되 면 장치 식별자, 측정 유형 및 기간에 따라 데이터가 수집 되 고 그룹화 됩니다. 이 그룹화의 출력은 FHIR 리소스 (현재[관찰](https://www.hl7.org/fhir/observation.html) )로 변환 하기 위해 전송 됩니다. FHIR 대상 매핑 템플릿은 데이터가 FHIR 관찰에 매핑되는 방식을 제어 합니다. 특정 시점 또는 특정 시간에 대 한 관찰을 만들어야 하나요? 관찰에 추가 해야 하는 코드는 무엇입니까? 값을 [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData) 또는 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)로 표시 해야 하나요? 이러한 데이터 형식은 모두 FHIR 대상 매핑 구성 컨트롤의 모든 옵션입니다.

> [!NOTE]
> 매핑은 기본 blob 저장소에 저장 되 고 계산 실행 당 blob에서 로드 됩니다. 업데이트 되 면 즉시 적용 됩니다. 

### <a name="codevaluefhirtemplate"></a>Code-efrtemplate

현재이 템플릿에서는 현재 유일한 템플릿 (현재는 FHIR 대상 매핑)이 지원 됩니다.  코드, 유효 기간 및 관찰 값을 정의할 수 있습니다. 여러 값 형식이 지원 됩니다. [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)및 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). 이러한 구성 가능한 값과 함께 관찰 리소스의 식별자와 적절 한 장치 및 환자 리소스에 대 한 링크가 자동으로 처리 됩니다.

| 속성 | 설명 
| --- | ---
|**T**| 이 템플릿이 바인딩될 측정 형식입니다. 이 형식을 출력 하는 장치 매핑 템플릿이 하나 이상 있어야 합니다.
|**PeriodInterval**|관찰을 만든 기간이 표시 되는 시간입니다. 지원 되는 값은 0 (인스턴스), 60 (1 시간), 1440 (일)입니다.
|**범주**|생성 되는 관찰 유형을 분류 하기 위한 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 수
|**코드가**|만든 관찰에 적용할 하나 이상의 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 입니다.
|**코드 []. Code**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 코드입니다.
|**코드 []. 컴퓨터**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)을 위한 시스템입니다.
|**코드 []. 표시가**|[코딩](http://hl7.org/fhir/datatypes-definitions.html#coding)에 대 한 표시입니다.
|**값**|관찰에서 추출 하 고 표시할 값입니다. 자세한 내용은 [값 형식 템플릿](#value-type-templates)을 참조 하세요.
|**Components**|*선택 사항:* 관찰 시 만들 하나 이상의 구성 요소입니다.
|**구성 요소 []. 코드가**|구성 요소에 적용할 하나 이상의 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 입니다.
|**구성 요소 []. 기본값**|구성 요소에서 추출 하 고 표시할 값입니다. 자세한 내용은 [값 형식 템플릿](#value-type-templates)을 참조 하세요.

### <a name="value-type-templates"></a>값 형식 템플릿

다음은 현재 지원 되는 값 형식 템플릿입니다.

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
|**Text**|일반 텍스트 표현입니다. 
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

**장치가 제거 됨-CodeableConcept**

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

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 대상 매핑을 사용 하는 방법을 알아보았습니다. 장치 매핑을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[장치 매핑을 사용 하는 방법](how-to-use-device-mapping-iot.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
