---
title: FHIR 서비스에서 사용자 지정 검색을 수행 하는 방법
description: 이 문서에서는 데이터베이스에서 사용할 사용자 지정 검색 매개 변수를 정의 하는 방법을 설명 합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: b299a7a60e65ede867bf9501f99b76294011cc93
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779024"
---
# <a name="defining-custom-search-parameters"></a>사용자 지정 검색 매개 변수 정의

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

FHIR 사양은 리소스와 관련 된 모든 리소스 및 검색 매개 변수에 대 한 검색 매개 변수 집합을 정의 합니다. 그러나 FHIR 사양에 의해 정의 되지 않은 리소스의 요소를 표준 검색 매개 변수로 검색 하려는 경우가 있습니다. 이 문서에서는 Azure 의료 Api의 FHIR 서비스에서 사용할 고유한 [검색 매개 변수](https://www.hl7.org/fhir/searchparameter.html) 를 정의 하는 방법을 설명 합니다 (fhir 서비스 라고 함).

> [!NOTE]
> 검색 매개 변수를 생성, 업데이트 또는 삭제할 때마다 검색 매개 변수를 프로덕션에서 사용할 수 있도록 [인덱스 작업](how-to-run-a-reindex.md) 을 실행 해야 합니다. 아래에서는 전체 FHIR 서비스를 인덱스를 만들기 전에 검색 매개 변수를 테스트 하는 방법을 간략하게 설명 합니다.

## <a name="create-new-search-parameter"></a>새 검색 매개 변수 만들기

새 검색 매개 변수를 만들려면 `POST` `SearchParameter` 데이터베이스에 대 한 리소스를 만듭니다. 아래 코드 예제에서는 [미국 코어 경합 SearchParameter](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) 를 리소스에 추가 하는 방법을 보여 줍니다 `Patient` .

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> 데이터베이스에 검색 매개 변수를 게시 **하 고** 데이터베이스의 인덱스를 다시 만들면 새 검색 매개 변수가 FHIR 서비스의 기능 설명에 표시 됩니다. `SearchParameter`검색 매개 변수가 FHIR 서비스에서 지원 되는지 여부를 확인 하는 유일한 방법은 기능 설명의를 보는 것입니다. 검색 매개 변수를 검색 하 여 검색 매개 변수를 찾을 수 있지만 기능 문에서이를 볼 수 없는 경우에도 검색 매개 변수를 인덱싱합니다. 여러 검색 매개 변수를 게시 한 후에는 인덱스 작업을 트리거할 수 있습니다.

의 중요 한 요소 `SearchParameter` :

* **url**: 검색 매개 변수를 설명 하는 고유 키입니다. HL7와 같은 대부분의 조직에서는 위에서 설명한 대로 미국 코어 경합 검색 매개 변수에서 정의한 검색 매개 변수에 표준 URL 형식을 사용 합니다.

* **code**: **코드** 에 저장 된 값은 검색할 때 사용 하는 값입니다. 위의 예에서는로 검색 `GET {FHIR_URL}/Patient?race=<code>` 하 여 특정 레이스의 모든 환자를 가져옵니다. 코드는 검색 매개 변수가 적용 되는 리소스에 대해 고유 해야 합니다.

* **base**: 검색 매개 변수가 적용 되는 리소스를 설명 합니다. 검색 매개 변수가 모든 리소스에 적용 되는 경우를 사용할 수 있습니다. `Resource` 그렇지 않으면 관련 된 모든 리소스를 나열할 수 있습니다.
 
* **유형**: 검색 매개 변수의 데이터 형식을 설명 합니다. 유형은 FHIR 서비스에 대 한 지원에 의해 제한 됩니다. 즉, 특수 형식의 검색 매개 변수를 정의 하거나 지원 되는 조합이 아니면 [복합 검색 매개 변수](overview-of-search.md) 를 정의할 수 없습니다.

* **식**: 검색 값을 계산 하는 방법을 설명 합니다. 검색 매개 변수를 설명 하는 경우 사양에 필요 하지 않은 경우에도 식을 포함 해야 합니다. 이는 식이나 xpath 구문이 필요 하 고 FHIR 서비스가 xpath 구문을 무시 하기 때문입니다.

## <a name="test-search-parameters"></a>테스트 검색 매개 변수

인덱스 다시 만들기 작업을 실행할 때까지 프로덕션에서 검색 매개 변수를 사용할 수는 없지만 전체 데이터베이스를 다시 인덱싱 전에 검색 매개 변수를 테스트 하는 방법에는 몇 가지가 있습니다. 

먼저 새 검색 매개 변수를 테스트 하 여 반환 되는 값을 확인할 수 있습니다. 해당 ID를 입력 하 여 특정 리소스 인스턴스에 대해 아래 명령을 실행 하면 검색 매개 변수 이름 및 저장 된 값을 사용 하 여 값 쌍의 목록을 다시 가져올 수 있습니다. 여기에는 리소스에 대 한 모든 검색 매개 변수가 포함 되며 사용자가 만든 검색 매개 변수를 찾기 위해 스크롤할 수 있습니다. 이 명령을 실행 하면 FHIR 서비스의 동작이 변경 되지 않습니다. 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
예를 들어 환자에 대 한 모든 검색 매개 변수를 찾으려면 다음을 수행 합니다.

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

결과는 다음과 같습니다.

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
검색 매개 변수가 예상 대로 표시 되는 것을 확인 한 후에는 요소를 사용 하 여 검색을 테스트 하기 위해 단일 리소스의 인덱스를 만들 수 있습니다. 먼저 단일 리소스를 인덱스 합니다.

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

이를 실행 하 여 해당 리소스 유형에 대해 정의한 특정 리소스에 대 한 검색 매개 변수의 인덱스를 설정 합니다. 그러면 FHIR 서비스에 대 한 업데이트가 수행 됩니다. 이제 partial 인덱스 사용 헤더를 검색 하 고 true로 설정할 수 있습니다. 즉, 해당 형식의 모든 리소스가 인덱싱되지 않은 경우에도 검색 매개 변수가 인덱싱되는 결과가 반환 됩니다. 

위의 예를 계속 진행 하면 미국 코어 레이스를 사용 하기 위해 1 환자를 인덱싱할 수 있습니다 `SearchParameter` .

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

그런 다음 특정 레이스를 포함 하는 환자를 검색 합니다.

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

테스트를 수행 하 고 검색 매개 변수가 예상 대로 작동 하는 것이 충족 되 면 작업을 실행 또는 예약 하 여 프로덕션 사용 사례에 대 한 FHIR 서비스에서 검색 매개 변수를 사용할 수 있도록 합니다.

## <a name="update-a-search-parameter"></a>검색 매개 변수 업데이트

검색 매개 변수를 업데이트 하려면를 사용 `PUT` 하 여 새 버전의 검색 매개 변수를 만듭니다. `SearchParameter ID` `id` 요청 본문의 요소 `PUT` 와 호출에를 포함 해야 합니다 `PUT` .

> [!NOTE]
> 검색 매개 변수의 ID를 모르는 경우 검색할 수 있습니다. 를 사용 하면 `GET {{FHIR_URL}}/SearchParameter` 사용자 지정 검색 매개 변수가 모두 반환 되 고 검색 매개 변수를 스크롤하여 필요한 검색 매개 변수를 찾을 수 있습니다. 검색을 이름으로 제한할 수도 있습니다. 아래 예제에서는를 사용 하 여 이름을 검색할 수 있습니다 `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace` .

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

결과가 업데이트 되 `SearchParameter` 고 버전이 증가 합니다.

> [!Warning]
> 데이터베이스에 이미 인덱싱되는 SearchParameters를 업데이트할 때는 주의 해야 합니다. 기존 SearchParameter의 동작을 변경 하면 예상 되는 동작에 영향을 줄 수 있습니다. 인덱스 작업을 즉시 실행 하는 것이 좋습니다.

## <a name="delete-a-search-parameter"></a>검색 매개 변수 삭제

검색 매개 변수를 삭제 해야 하는 경우 다음을 사용 합니다.

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> 데이터베이스에 이미 인덱싱되는 SearchParameters를 삭제할 때는 주의 해야 합니다. 기존 SearchParameter의 동작을 변경 하면 예상 되는 동작에 영향을 줄 수 있습니다. 인덱스 작업을 즉시 실행 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 검색 매개 변수를 만드는 방법을 알아보았습니다. 다음으로 FHIR 서비스를 다시 인덱싱 하는 방법을 배울 수 있습니다.

>[!div class="nextstepaction"]
>[재색인 작업 실행 방법](how-to-run-a-reindex.md)
