---
title: Azure 의료 Api의 FHIR 서비스에서 프로필에 대 한 리소스를 $validate 합니다.
description: FHIR 서비스의 프로필에 대 한 리소스를 $validate 합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b52389b6007c436614840a9bad568a0e81cf7fa2
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779573"
---
# <a name="how-to-validate-fhir-resources-against-profiles"></a>프로필에 대 한 리소스 리소스의 유효성을 검사 하는 방법

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

HL7 FHIR은 의료 데이터를 저장 하 고 교환 하는 표준 및 상호 운용 가능한 방법을 정의 합니다. 기본 FHIR 사양 내 에서도 FHIR이 사용 되는 컨텍스트에 따라 추가 규칙 또는 확장을 정의 하는 것이 유용할 수 있습니다. FHIR의 이러한 컨텍스트별 사용을 위해 **fhir 프로필** 은 추가 사양 계층에 사용 됩니다.

[Fhir 프로필](https://www.hl7.org/fhir/profiling.html) 은로 표시 되는 리소스에 대 한 제약 조건 또는 확장과 같은 추가 컨텍스트를 설명 합니다 `StructureDefinition` . HL7 FHIR 표준은 기본 리소스 집합을 정의 하며 이러한 표준 기본 리소스에는 제네릭 정의가 있습니다. FHIR 프로필을 사용 하면 제약 조건 및 확장을 사용 하 여 리소스 정의를 축소 하 고 사용자 지정할 수 있습니다.

Azure 의료 Api의 FHIR 서비스 (FHIR 서비스 라고 함)는 프로필에 대 한 리소스의 유효성 검사를 허용 하 여 리소스가 프로필을 따르는지 확인 합니다. 이 문서에서는 FHIR 프로필의 기본 사항을 설명 하 고 `$validate` 리소스를 만들고 업데이트할 때 프로필에 대 한 리소스의 유효성을 검사 하는 데 사용 하는 방법을 안내 합니다.

## <a name="fhir-profile-the-basics"></a>FHIR 프로필: 기본 사항

프로필은 리소스에 대 한 추가 컨텍스트 (일반적으로 리소스로 표시 됨)를 설정 합니다 `StructureDefinition` . `StructureDefinition` 리소스의 콘텐츠 또는 데이터 형식에 대 한 규칙 집합을 정의 합니다. 예를 들어 리소스에 포함 된 필드와 이러한 필드에 사용할 수 있는 값을 정의 합니다. 예를 들어 프로필은 카디널리티를 제한 (예: 최대 카디널리티를 0으로 설정 하 여 요소를 규칙 아웃) 하거나, 요소의 내용을 단일 고정 값으로 제한 하거나, 리소스에 대해 필요한 확장을 정의할 수 있습니다. 또한 기존 프로필에 추가 제약 조건을 지정할 수 있습니다. 는 `StructureDefinition` 정식 URL로 식별 됩니다.

```rest
http://hl7.org/fhir/StructureDefinition/{profile}
```

`{profile}`필드에서 프로필의 이름을 지정 합니다.

예를 들어:

- `http://hl7.org/fhir/StructureDefinition/patient-birthPlace` 는 환자 생년월일의 등록 된 주소에 대 한 정보가 필요한 기본 프로필입니다.
- `http://hl7.org/fhir/StructureDefinition/bmi` 는 BMI (본문 Mass Index) 관찰을 나타내는 방법을 정의 하는 또 다른 기본 프로필입니다.
- `http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance` 는 환자와 연결 된 리소스에 대 한 최소 기대치를 설정 하 `AllergyIntolerance` 고 확장 및 값 집합과 같은 필수 필드를 식별 하는 미국 코어 프로필입니다.

리소스가 프로필을 준수 하는 경우 해당 프로필은 필드 내의 리소스에 지정 됩니다 `profile` .

```json
{
  "resourceType" : "Patient",
  "id" : "ExamplePatient1",
  "meta" : {
    "lastUpdated" : "2020-10-30T09:48:01.8512764-04:00",
    "source" : "Organization/PayerOrganizationExample1",
    "profile" : [
      "http://hl7.org/fhir/us/carin-bb/StructureDefinition/C4BB-Patient"
    ]
  },
```

### <a name="base-profile-and-custom-profile"></a>기본 프로필 및 사용자 지정 프로필

프로필에는 기본 프로필과 사용자 지정 프로필 이라는 두 가지 유형이 있습니다. 기본 프로필은 `StructureDefinition` 리소스가 준수 해야 하 고 또는와 같은 기본 리소스로 정의 된 기본 프로필입니다 `Patient` `Observation` . 예를 들어 본문 Mass Index (BMI) `Observation` 프로필은 다음과 같이 시작 됩니다.

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "bmi",
...
}
```

사용자 지정 프로필은 기본 프로필 위에 있는 추가 제약 조건의 집합으로, 기본 사양에 포함 되지 않은 리소스 매개 변수를 제한 하거나 추가 합니다. 사용자 지정 프로필은 기존 기본 리소스에서 제약 조건 및 확장을 지정 하 여 사용자가 직접 리소스 정의를 사용자 지정할 수 있기 때문에 유용 합니다. 예를 들어 성별을 기반으로 리소스 인스턴스를 표시 하는 프로필을 빌드할 수 있습니다 `AllergyIntolerance` `Patient` .이 경우 프로필을 사용 하 여 기존 프로필 위에 사용자 지정 프로필을 만듭니다 `Patient` `AllergyIntolerance` .

> [!NOTE]
> 사용자 지정 프로필은 기본 리소스 위에 빌드해야 하며 기본 리소스와 충돌 하지 않습니다. 예를 들어 요소에 1.. 1의 카디널리티가 있는 경우 사용자 지정 프로필은 선택적으로 만들 수 없습니다.

사용자 지정 프로필은 다양 한 구현 가이드에도 지정 됩니다. 몇 가지 일반적인 구현 가이드는 다음과 같습니다.

|Name |URL
|---- |----
Us 핵심 |<https://www.hl7.org/fhir/us/core/>
파랑 단추 |<http://hl7.org/fhir/us/carin-bb/>
Da 다빈치 지불자 데이터 Exchange |<http://hl7.org/fhir/us/davinci-pdex/>
Argonaut |<http://www.fhir.org/guides/argonaut/pd/>

## <a name="accessing-profiles-and-storing-profiles"></a>프로필 액세스 및 프로필 저장

### <a name="storing-profiles"></a>프로필 저장

서버에 프로필을 저장 하는 경우 요청을 수행할 수 있습니다 `POST` .

```rest
POST http://<your FHIR service base URL>/StructureDefinition
```

예를 들어 프로필을 저장 하려면 다음을 `us-core-allergyintolerance` 수행 합니다.

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance
```

미국 Core Allergy Intolerance 프로필이 저장 및 검색 되는 위치:

```json
{
    "resourceType" : "StructureDefinition",
    "id" : "us-core-allergyintolerance",
    "text" : {
        "status" : "extensions"
    },
    "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-allergyintolerance",
    "version" : "3.1.1",
    "name" : "USCoreAllergyIntolerance",
    "title" : "US  Core AllergyIntolerance Profile",
    "status" : "active",
    "experimental" : false,
    "date" : "2020-06-29",
        "publisher" : "HL7 US Realm Steering Committee",
    "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
    "description" : "Defines constraints and extensions on the AllergyIntolerance resource for the minimal set of data to query and retrieve allergy information.",

...
```

대부분의 프로필에는 리소스 형식이 `StructureDefinition` 있지만 `ValueSet` 용어 리소스 인 및 형식을 사용할 수도 있습니다 `CodeSystem` . [](http://hl7.org/fhir/terminologies.html) 예를 들어 `POST` `ValueSet` JSON 형식의 프로필을 사용 하는 경우 `id` 와 마찬가지로 서버는 프로필에 대해 할당 된로 저장 된 프로필을 반환 합니다 `StructureDefinition` . 다음은 조건/진단 심각도에 대 한 조건을 지정 하는 [조건 심각도](https://www.hl7.org/fhir/valueset-condition-severity.html) 프로필을 업로드할 때 얻을 수 있는 예입니다.

```json
{
    "resourceType": "ValueSet",
    "id": "35ab90e5-c75d-45ca-aa10-748fefaca7ee",
    "meta": {
        "versionId": "1",
        "lastUpdated": "2021-05-07T21:34:28.781+00:00",
        "profile": [
            "http://hl7.org/fhir/StructureDefinition/shareablevalueset"
        ]
    },
    "text": {
        "status": "generated"
    },
    "extension": [
        {
            "url": "http://hl7.org/fhir/StructureDefinition/structuredefinition-wg",
            "valueCode": "pc"
        }
    ],
    "url": "http://hl7.org/fhir/ValueSet/condition-severity",
    "identifier": [
        {
            "system": "urn:ietf:rfc:3986",
            "value": "urn:oid:2.16.840.1.113883.4.642.3.168"
        }
    ],
    "version": "4.0.1",
    "name": "Condition/DiagnosisSeverity",
    "title": "Condition/Diagnosis Severity",
    "status": "draft",
    "experimental": false,
    "date": "2019-11-01T09:29:23+11:00",
    "publisher": "FHIR Project team",
...
```

이 `resourceType` `ValueSet` 이 고, `url` 프로필에 대 한가 형식 임을 지정 하 `ValueSet` 는 것을 볼 수 있습니다 `"http://hl7.org/fhir/ValueSet/condition-severity"` .

### <a name="viewing-profiles"></a>프로필 보기

요청을 사용 하 여 서버에서 기존 사용자 지정 프로필에 액세스할 수 있습니다 `GET` . 구현 가이드에서 유효한 정식 Url을 사용 하는 프로필과 같은 모든 유효한 프로필에는 다음 쿼리를 통해 액세스할 수 있어야 합니다.

```rest
GET http://<your FHIR service base URL>/StructureDefinition?url={canonicalUrl} 
```

여기서 필드는 `{canonicalUrl}` 프로필의 정식 URL로 바뀝니다.

예를 들어 미국 코어 `Goal` 리소스 프로필을 보려면 다음을 수행 합니다.

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/StructureDefinition?url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal
```

이렇게 하면 `StructureDefinition` US Core 목표 프로필에 대 한 리소스가 반환 되 고 다음과 같이 시작 됩니다.

```json
{
  "resourceType" : "StructureDefinition",
  "id" : "us-core-goal",
  "url" : "http://hl7.org/fhir/us/core/StructureDefinition/us-core-goal",
  "version" : "3.1.1",
  "name" : "USCoreGoalProfile",
  "title" : "US Core Goal Profile",
  "status" : "active",
  "experimental" : false,
  "date" : "2020-07-21",
  "publisher" : "HL7 US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "url",
          "value" : "http://www.healthit.gov"
        }
      ]
    }
  ],
  "description" : "Defines constraints and extensions on the Goal resource for the minimal set of data to query and retrieve a patient's goal(s).",
...
```

FHIR 서비스는 `StructureDefinition` 기본 프로필에 대 한 인스턴스를 반환 하지 않지만 다음과 같이 HL7 웹 사이트에서 쉽게 찾을 수 있습니다.

- `http://hl7.org/fhir/Observation.profile.json.html`
- `http://hl7.org/fhir/Patient.profile.json.html`


### <a name="profiles-in-the-capability-statement"></a>기능 문의 프로필

에는 `Capability Statement` 구조 정의와 값 집합 등의 서버 기능 문으로 사용할 FHIR 서비스의 가능한 모든 동작이 나와 있습니다. FHIR 서비스는 다음 형식으로 업로드 및 저장 된 프로필에 대 한 정보로 기능 문을 업데이트 합니다.

- `CapabilityStatement.rest.resource.profile`
- `CapabilityStatement.rest.resource.supportedProfile`

여기에는 카디널리티, 바인딩, 확장 또는 기타 제한 사항에 대 한 제약 조건을 포함 하 여 리소스에 대 한 전반적인 지원을 설명 하는 프로필에 대 한 모든 사양이 표시 됩니다. 따라서 `POST` 의 형식으로 프로필을 `StructureDefinition` 만들고 `GET` 리소스 메타 데이터를 통해 전체 기능 설명을 볼 때 `supportedProfiles` 업로드 한 프로필에 대 한 모든 세부 정보를 매개 변수 옆에 표시 합니다.

예를 들어 `POST` 미국 코어 환자 프로필은 다음과 같이 시작 됩니다.

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient",
  "version": "3.1.1",
  "name": "USCorePatientProfile",
  "title": "US Core Patient Profile",
  "status": "active",
  "experimental": false,
  "date": "2020-06-27",
  "publisher": "HL7 US Realm Steering Committee",
...
```

다음 `GET` 에 대 한 요청을 보냅니다 `metadata` .

```rest
GET http://<your FHIR service base URL>/metadata
```

`CapabilityStatement`FHIR 서버에 업로드 한 미국 코어 환자 프로필에 대 한 다음 정보를 포함 하는이 반환 됩니다.

```json
...
{
    "type": "Patient",
    "profile": "http://hl7.org/fhir/StructureDefinition/Patient",
    "supportedProfile":[
        "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
    ],
...
```

## <a name="validating-resources-against-the-profiles"></a>프로필에 대 한 리소스 유효성 검사

또는와 같은 FHIR 리소스 `Patient` 는 `Observation` 특정 프로필에 대 한 준수를 표현할 수 있습니다. 이를 통해 FHIR 서비스는 연결 된 프로필 또는 지정 된 프로필에 대해 지정 된 리소스 **의 유효성을 검사할** 수 있습니다. 프로필에 대 한 리소스의 유효성을 검사 하는 것은 `Resource.meta.profile` 또는 구현 가이드에 나열 된 사양을 비롯 하 여 리소스가 프로필을 따르는지 확인 하는 것을 의미 합니다.

리소스의 유효성을 검사 하는 방법에는 두 가지가 있습니다. 먼저 `$validate` FHIR 서비스에 이미 있는 리소스에 대해 작업을 사용할 수 있습니다. 둘째, `POST` 리소스 또는 작업의 일부로 서버에 사용할 수 있습니다 `Update` `Create` . 두 경우 모두, 리소스가 원하는 프로필에 맞지 않는 경우에 수행할 작업을 FHIR 서비스 구성에서 결정할 수 있습니다.

### <a name="using-validate"></a>$validate 사용

`$validate`작업은 제공 된 프로필이 유효한 지 여부와 리소스가 지정 된 프로필을 따르는지 여부를 확인 합니다. [HL7 FHIR 사양](https://www.hl7.org/fhir/resource-operation-validate.html)에 설명 된 대로 및와 같은의을 지정할 수도 있습니다 `mode` `$validate` `create` `update` .

- `create`: 서버는 프로필 콘텐츠가 기존 리소스에서 고유한 지 확인 하 고 새 리소스로 만들 수 있는지 확인 합니다.
- `update`: 프로필이 지정한 기존 리소스에 대 한 업데이트 인지 확인 합니다 (예: 변경할 수 없는 필드는 변경 되지 않음).

서버는 항상 `OperationOutcome` 유효성 검사 결과로를 반환 합니다.

#### <a name="validating-an-existing-resource"></a>기존 리소스 유효성 검사

기존 리소스의 유효성을 검사 하려면 `$validate` 요청에서를 사용 합니다 `GET` .

```rest
GET http://<your FHIR service base URL>/{resource}/{resource ID}/$validate
```

예를 들어:

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/a6e11662-def8-4dde-9ebc-4429e68d130e/$validate
```

위의 예제에서 기존 리소스의 유효성을 검사 `Patient` `a6e11662-def8-4dde-9ebc-4429e68d130e` 합니다. 유효한 경우 다음과 같은을 얻게 됩니다 `OperationOutcome` .

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "information",
            "code": "informational",
            "diagnostics": "All OK"
        }
    ]
}
```

리소스가 유효 하지 않은 경우 오류 코드와 리소스가 잘못 된 이유에 대 한 세부 정보가 포함 된 오류 메시지가 표시 됩니다. `4xx`또는 `5xx` 오류는 유효성 검사 자체를 수행할 수 없고 리소스가 유효한 지 여부를 알 수 없음을 의미 합니다. `OperationOutcome`오류 메시지와 함께 반환 되는 예는 다음과 같습니다.

```json
{
    "resourceType": "OperationOutcome",
    "issue": [
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.identifier.value' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient.identifier[1]"
            ]
        },
        {
            "severity": "error",
            "code": "invalid",
            "details": {
                "coding": [
                    {
                        "system": "http://hl7.org/fhir/dotnet-api-operation-outcome",
                        "code": "1028"
                    }
                ],
                "text": "Instance count for 'Patient.gender' is 0, which is not within the specified cardinality of 1..1"
            },
            "location": [
                "Patient"
            ]
        }
    ]
}
```

위의이 예제에서 리소스는 `Patient` 환자 식별자 값 및 성별이 필요한 제공 된 프로필을 준수 하지 않았습니다.

프로필을 매개 변수로 지정 하려면의 HL7 기본 프로필에 대 한 다음 예제와 같이 유효성을 검사할 프로필의 정식 URL을 지정할 수 있습니다 `heartrate` .

```rest
GET https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Observation/12345678/$validate?profile=http://hl7.org/fhir/StructureDefinition/heartrate
```

#### <a name="validating-a-new-resource"></a>새 리소스의 유효성 검사

서버에 업로드할 새 리소스의 유효성을 검사 하려는 경우 다음 요청을 수행할 수 있습니다 `POST` .

```rest
POST http://<your FHIR service base URL>/{Resource}/$validate
```

예를 들어:

```rest
POST https://myworkspace-myfhirserver.fhir.azurehealthcareapis.com/Patient/$validate 
```

이 요청은 요청 페이로드에서 지정 하는 새 리소스 (JSON 또는 XML 형식)를 만들고 업로드 된 리소스의 유효성을 검사 합니다. 그런 다음 `OperationOutcome` 새 리소스에 대 한 유효성 검사의 결과로를 반환 합니다.

### <a name="validate-on-resource-create-or-resource-update"></a>리소스 만들기 또는 리소스 업데이트에 대 한 유효성 검사

리소스 만들기 또는 업데이트와 같은 리소스의 유효성을 검사 하려는 경우를 선택할 수 있습니다. 서버 구성 설정의 아래에서이를 지정할 수 있습니다 `CoreFeatures` .

```json
{
   "FhirServer": {
      "CoreFeatures": {
            "ProfileValidationOnCreate": true,
            "ProfileValidationOnUpdate": false
        }
}
```

리소스가 제공 된를 준수 하 `Resource.meta.profile` 고 프로필이 시스템에 있으면 서버는 위의 구성 설정에 따라 작동 합니다. 제공 된 프로필이 서버에 없는 경우 유효성 검사 요청이 무시 되 고에 남아 `Resource.meta.profile` 있습니다.
유효성 검사는 일반적으로 비용이 많이 드는 작업이므로 일반적으로 테스트 서버 또는 리소스의 작은 하위 집합에서만 실행됩니다. 따라서 이러한 방법으로 서버 쪽에서 유효성 검사 작업을 설정하거나 해제해야 합니다. 서버 구성에서 리소스 만들기/업데이트에 대한 유효성 검사를 옵트아웃하도록 지정하는 경우 사용자는 만들기/업데이트 요청의 에서 동작을 지정하여 동작을 재정의할 수 있습니다. `header`

```rest
x-ms-profile-validation: true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 프로필에 대해 알아보고 $validate 사용하여 프로필에 대해 리소스의 유효성을 검사하는 방법을 배웠습니다. FHIR 서비스의 다른 지원되는 기능에 대해 알아보려면 다음을 확인하세요.

>[!div class="nextstepaction"]
>[FHIR 지원 기능](fhir-features-supported.md)
