---
title: FHIR 서비스에 대한 검색 예제
description: FHIR에 대한 다른 검색 매개 변수, 한정자 및 기타 검색 도구를 사용하여 검색하는 방법
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: 17f2ae17f2dd4677734a71fdf395ac7cdab13b8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722190"
---
# <a name="fhir-search-examples"></a>FHIR 검색 예제

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

다음은 검색 매개 변수 및 한정자, 체인 및 역방향 체인 검색, 복합 검색, 검색 결과에 대한 다음 항목 집합 보기 및 요청으로 검색을 포함하여 FHIR 검색 작업을 사용하는 몇 가지 `POST` 예입니다. 검색에 대한 자세한 내용은 [FHIR 검색 개요를](overview-of-search.md)참조하세요.
   
## <a name="search-result-parameters"></a>검색 결과 매개 변수

### <a name="_include"></a>_include

`_include` 는 리소스의 지정된 매개 변수를 포함하는 리소스를 검색합니다. 예를 들어 `MedicationRequest` 리소스를 검색하여 매개 변수인 특정 환자의개체에 대한 정보가 포함된 리소스만 찾을 수 `reference` `patient` 있습니다. 아래 예제에서는 에서 `MedicationRequests` 참조되는 모든 환자와 모든 환자를 끌어오게 됩니다. `MedicationRequests`

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** 및 **_revinclude** 100개 항목으로 제한됩니다.

### <a name="_revinclude"></a>_revinclude

`_revinclude` 를 사용하면 반대 방향을 로 검색할 수 `_include` 있습니다. 예를 들어 환자를 검색한 다음, 환자를 참조하는 모든 발생을 역방향으로 포함할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` 는 불필요한 데이터를 생략하여 응답 크기를 줄이기 위해 검색 결과를 필드의 하위 집합으로 좁혀 줍니다. 매개 변수는 쉼표로 구분된 기본 요소 목록을 허용합니다.

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

이 요청에서는 환자 번들을 다시 받게 되지만 각 리소스에는 식별자와 환자의 활성 상태만 포함됩니다. 이 반환된 응답의 리소스에는 `meta.tag` 불완전한 결과 집합임을 나타내는 값이 `SUBSETTED` 포함됩니다.

## <a name="search-modifiers"></a>검색 한정자

### <a name="not"></a>:not

`:not` 를 사용하면 특성이 true가 아닌 리소스를 찾을 수 있습니다. 예를 들어 성별이 성별이 아닌 환자를 검색할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

반환 값으로 빈 값(성별 없이 지정된 항목)을 포함하여 성별이 여성이 아닌 모든 환자 항목을 얻게 됩니다. 특정 성별이 없는 항목은 포함되지 않기 때문에 성별이 남성인 환자를 검색하는 것과는 다릅니다.

### <a name="missing"></a>:missing

`:missing` 는 값이 일 때 지정된 요소에 대한 값이 없는 모든 `true` 리소스를 반환하고 값이 일 때 지정된 요소를 포함하는 모든 리소스를 `false` 반환합니다. 단순 데이터 형식 요소의 경우 는 `:missing=true` 요소가 확장과 함께 있지만 빈 값이 있는 모든 리소스에서 일치합니다. 예를 들어 `Patient` 생년월일에 정보가 누락된 모든 리소스를 찾으려면 다음을 수행할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` 는 매개 변수에 사용되며 대/소문자 구분 `string` 및 문자 결합과 같이 매개 변수와 정확하게 일치하는 결과를 반환합니다.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

이 `Patient` 요청은 이름과 정확히 동일한 리소스를 `Jon` 반환합니다. 리소스에 또는 와 같은 이름의 환자가 있는 경우 `Jonathan` `joN` 지정된 값과 정확히 일치하지 않으므로 검색에서 리소스를 무시하고 건너뜁니다.

### <a name="contains"></a>:contains
`:contains``string`는 매개 변수에 사용되며 검색되는 필드 내의 문자열에서 지정된 값의 부분 일치 항목이 있는 리소스를 검색합니다. `contains` 는 대/소문자를 구분하지 않으며 문자 간을 허용합니다. 예를 들어:

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

이 요청은 `Patient` `address` 문자열 "표시"를 포함하는 값이 있는 필드가 있는 모든 리소스를 반환합니다. 즉, 검색 결과로 반환된 "표시자" 또는 "59개의 표시 ST"와 같은 값을 포함하는 주소가 있을 수 있습니다.

## <a name="chained-search"></a>연결된 검색 

여러 참조 매개 변수를 포함하는 일련의 검색 작업을 수행하려면 마침표 를 사용하여 일련의 참조 매개 변수를 서버 요청에 하나씩 추가하여 "연결"할 수 `.` 있습니다. 예를 들어 특정 를 `DiagnosticReport` 포함하는 리소스에 `subject` 대한 참조를 사용하여 모든 `Patient` 리소스를 보려는 경우 `name`  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

이 요청은 `DiagnosticReport` 환자 주제가 "Sarah"인 모든 리소스를 반환합니다. `.`필드가 매개 `Patient` 변수의 참조 매개 변수에 대해 연결된 검색을 수행한 후의 `subject` 기간입니다.

일반적인 검색(체인 검색이 아닌)의 또 다른 일반적인 용도는 특정 환자의 모든 발견을 찾는 것입니다. `Patient`s에는 제목이 있는 가 하나 이상 `Encounter` 있는 경우가 많습니다. 제공된 를 사용하여 에 대한 모든 `Encounter` `Patient` 리소스를 검색하려면: `id`

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

연결된 검색을 사용하여 와 같은 특정 정보와 일치하는 모든 `Encounter` `Patient` 리소스를 찾을 수 있습니다. `birthdate`

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

이렇게 하면 단일 `Encounter` 환자뿐만 아니라 지정된 생년월일 값이 있는 모든 환자에서 리소스를 검색할 수 있습니다. 

또한 하나의 요청에서 여러 조건을 검색할 수 있는 기호 를 사용하여 연결된 검색을 한 요청에서 두 번 이상 수행할 수 `&` 있습니다. 이러한 경우 연결된 검색은 모든 조건만 한 번에 충족하는 조건을 검색하는 대신 각 매개 변수를 "독립적으로" 검색합니다.

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

이렇게 하면 `Patient` "Sarah"가 로 있고 `generalPractitioner` `generalPractitioner` WA 상태의 주소가 있는 가 있는 모든 리소스가 반환됩니다. 즉, 환자의 로 참조된 WA 주의 NY 및 Bill 상태의 Sarah가 있는 환자의 경우 `generalPractitioner` 이 반환됩니다.

검색이 모든 조건을 그룹으로 포함하는 AND 작업이어야 하는 시나리오는 **아래의 복합 검색** 예제를 참조하세요.

## <a name="reverse-chain-search"></a>역방향 체인 검색

체인 검색을 사용하면 참조하는 리소스의 속성을 기반으로 리소스를 검색할 수 있습니다. 역방향 체인 검색을 사용하면 다른 방법으로 수행할 수 있습니다. 매개 변수를 사용하여 참조하는 리소스의 속성을 기반으로 리소스를 검색할 수 `_has` 있습니다. 예를 들어 `Observation` 리소스에는 환자 리소스를 참조하는 검색 매개 `patient` 변수가 있습니다. 특정 를 사용하여 에 의해 참조되는 모든 환자 리소스를 찾으려면 `Observation` `code`

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

이 요청은 코드와 함께 에서 참조하는 환자 `Observation` 리소스를 `527` 반환합니다. 

또한 역방향 체인 검색에는 재귀 구조가 있을 수 있습니다. 예를 들어 관찰에 특정 사용자의 감사 이벤트가 있는 모든 환자를 검색하려는 경우 `Observation` 다음을 `janedoe` 수행할 수 있습니다.

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

## <a name="composite-search"></a>복합 검색

한 번에 여러 조건을 충족하는 리소스를 검색하려면 단일 매개 변수 값 시퀀스를 기호 와 조인하는 복합 검색을 `$` 사용합니다. 반환되는 결과는 조인된 검색 매개 변수로 지정된 모든 조건과 일치하는 리소스의 교집합입니다. 이러한 검색 매개 변수를 복합 검색 매개 변수라고 하며 중첩된 구조에서 여러 매개 변수를 결합하는 새 매개 변수를 정의합니다. 예를 들어, `DiagnosticReport` `Observation` 9.2보다 크거나 작은 포트할당 값이 포함된 모든 리소스를 찾으려면 다음을 수행합니다.

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

이 요청은 코드가 포함된 구성 요소를 지정합니다. `2823-3` 이 경우 포트할당이 됩니다. 기호 다음에는 `$` `lt` "보다 낮거나 같음"에 를 사용하고, 포트할당 값 범위에 를 사용하여 구성 `9.2` 요소의 값 범위를 지정합니다. 

복합 검색 매개 변수를 사용하여 OR로 여러 구성 요소 코드 값 수량을 필터링할 수도 있습니다. 예를 들어 쿼리를 표현하여 90 이상 또는 140보다 큰 systolic 기압을 찾습니다.

```rest
GET [your-fhir-server]/Observation?component-code-value-quantity=http://loinc.org|8462-4$gt90,http://loinc.org|8480-6$gt140
``` 

## <a name="search-the-next-entry-set"></a>다음 항목 집합 검색

단일 검색 쿼리당 반환할 수 있는 최대 항목 수는 1000개입니다. 그러나 검색 쿼리와 일치하는 항목이 1000개 이상 있을 수 있으며, 반환된 처음 1000개 항목 이후의 다음 항목 집합을 볼 수 있습니다. 이러한 경우 `url` `searchset` 아래 결과와 같이 에서 연속 토큰 값을 사용합니다. `Bundle`

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

그리고 필드 아래에 제공된 URL에 대한 GET 요청을 수행합니다. `relation: next`

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

그러면 검색 결과에 대한 다음 항목 집합이 반환됩니다. 는 `searchset` 검색 결과 항목의 전체 집합이며 연속 `url` 토큰은 검색 쿼리에 대해 반환되는 최대 항목 수에 대한 제한 때문에 첫 번째 집합에 표시되지 않는 항목을 검색하기 위해 서버에서 제공하는 링크입니다.

## <a name="search-using-post"></a>POST를 사용하여 검색

위에서 언급한 모든 검색 예제에서 `GET` 요청을 사용했습니다. 를 사용하여 요청을 사용하여 검색 작업을 수행할 수도 `POST` 있습니다. `_search`

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

이 요청은 `Patient` 값이 45인 모든 리소스를 `id` 반환합니다. GET 요청과 마찬가지로 서버는 조건을 충족하는 리소스 집합을 결정하고 HTTP 응답에 번들 리소스를 반환합니다.

쿼리 매개 변수가 양식 본문으로 제출되는 POST를 사용하여 검색하는 또 다른 예는 다음과 같습니다.

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)
