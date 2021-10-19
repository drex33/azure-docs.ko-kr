---
title: Azure API for FHIR 검색 예
description: 다른 검색 매개 변수, 한정자 및 기타 FHIR 검색 도구를 사용 하 여 검색 하는 방법
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 731b85d0486c3d9fa7ba9e3af4cc2f047912fcdc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436001"
---
# <a name="fhir-search-examples-for-azure-api-for-fhir"></a>Azure API 용 Azure API에 대 한 FHIR 검색 예제

다음은 검색 매개 변수 및 한정자, 체인 및 역방향 체인 검색, 복합 검색, 검색 결과에 대 한 다음 항목 집합 보기, 요청을 검색 하는 등의 몇 가지 예입니다 `POST` . 검색에 대 한 자세한 내용은 [FHIR 검색 개요](overview-of-search.md)를 참조 하세요.
   
## <a name="search-result-parameters"></a>검색 결과 매개 변수

### <a name="_include"></a>_include

`_include` 리소스에서 지정 된 매개 변수를 포함 하는 리소스를 검색 합니다. 예를 들어, `MedicationRequest` 특정 환자 (매개 변수)에 대 한 규정에 대 한 정보를 포함 하는 리소스를 찾을 수 있도록 리소스 전체를 검색할 수 있습니다 `reference` `patient` . 아래 예제에서는에서 `MedicationRequests` 참조 되는 모든 및 모든 환자를 가져옵니다 `MedicationRequests` .

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** 및 **_revinclude** 은 100 항목으로 제한 됩니다.

### <a name="_revinclude"></a>_revinclude

`_revinclude` 반대 방향으로를 검색할 수 있습니다 `_include` . 예를 들어 환자를 검색 한 다음 환자를 참조 하는 모든 발견을 역방향으로 포함할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` 불필요 한 데이터를 생략 하 여 응답 크기를 줄이려면 검색 결과를 필드의 하위 집합으로 축소 합니다. 매개 변수는 다음과 같이 기본 요소에 대 한 쉼표로 구분 된 목록을 허용 합니다.

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

이 요청에서 환자의 번들을 가져오지만 각 리소스는 식별자 및 환자의 활성 상태만 포함 합니다. 반환 된 응답의 리소스는 불완전 한 `meta.tag` `SUBSETTED` 결과 집합 임을 나타내기 위해 값을 포함 합니다.

## <a name="search-modifiers"></a>검색 한정자

### <a name="not"></a>: 아님

`:not` 특성이 true가 아닌 리소스를 찾을 수 있습니다. 예를 들어 성별이 암이 아닌 환자를 검색할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

반환 값으로, 빈 값 (성별 없이 지정 된 항목)을 포함 하 여 성별을이 아닌 모든 환자 항목을 가져옵니다. 이는 성별이 남성 인 환자을 검색 하는 것과는 다릅니다 .이는 특정 성별이 없는 항목은 포함 되지 않기 때문입니다.

### <a name="missing"></a>: 없음

`:missing` 값이 이면 지정 된 요소에 대 한 값이 없는 모든 리소스를 반환 하 `true` 고, 값이 인 경우 지정 된 요소를 포함 하는 모든 리소스를 반환 합니다 `false` . 단순 데이터 형식 요소의 경우 `:missing=true` 은 요소가 확장에 있지만 빈 값이 있는 모든 리소스에 대해 일치 합니다. 예를 들어 `Patient` 생년월일에 정보가 누락 된 모든 리소스를 찾으려는 경우 다음을 수행할 수 있습니다.

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>: 정확히
`:exact` 는 `string` 매개 변수에 사용 되 고는 대/소문자 구분 및 문자 연결과 같이 매개 변수와 정확히 일치 하는 결과를 반환 합니다.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

이 요청은 `Patient` 이름이와 정확히 동일한 리소스를 반환 `Jon` 합니다. 리소스가 또는와 같은 이름으로 환자 된 경우 `Jonathan` `joN` 검색은 지정 된 값과 정확 하 게 일치 하지 않으므로 리소스를 무시 하 고 건너뜁니다.

### <a name="contains"></a>: contains
`:contains` 는 `string` 매개 변수에 사용 되며 검색 중인 필드 내의 문자열에서 지정 된 값과 부분적으로 일치 하는 리소스를 검색 합니다. `contains` 는 대/소문자를 구분 하지 않으며 문자 연결을 허용 합니다. 예를 들어:

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

이 요청은 `Patient` `address` "목초지" 문자열이 포함 된 값이 있는 모든 리소스를 필드와 함께 반환 합니다. 즉, "Meadowers" 또는 "59 목초지 ST"와 같은 값을 포함 하는 주소가 검색 결과로 반환 될 수 있습니다.

## <a name="chained-search"></a>연결 된 검색 

여러 참조 매개 변수를 포함 하는 일련의 검색 작업을 수행 하기 위해 일련의 참조 매개 변수를 마침표를 사용 하 여 하나씩 서버 요청에 하나씩 추가 하 여 "체인" 할 수 있습니다 `.` . 예를 들어 `DiagnosticReport` `subject` `Patient` 특정을 포함 하는 리소스에 대 한 참조를 포함 하는 모든 리소스를 보려면 `name` 다음을 수행 합니다.  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

이 요청은 `DiagnosticReport` "Sarah" 라는 환자 주체가 있는 모든 리소스를 반환 합니다. `.`필드가 `Patient` 매개 변수의 참조 매개 변수에 연결 된 검색을 수행 하는 기간 `subject` 입니다.

일반 검색 (연결 된 검색 아님)의 또 다른 일반적인 용도는 특정 환자에 대 한 모든 것을 찾는 것입니다. `Patient`에는 제목에 하나 이상의가 포함 되는 경우가 많습니다 `Encounter` . 제공 된를 사용 하 여에 대 한 모든 리소스를 검색 하려면 `Encounter` `Patient` `id` :

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

연결 된 검색을 사용 하 여 `Encounter` 다음과 같은 특정 정보와 일치 하는 모든 리소스를 찾을 수 있습니다 `Patient` `birthdate` .

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

이렇게 하면 `Encounter` 단일 환자에 대 한 리소스를 검색 하는 것 뿐만 아니라 지정 된 생년월일 값을 가진 모든 환자에서 리소스를 검색할 수 있습니다. 

또한 한 요청에서 `&` 여러 조건을 검색 하는 데 사용할 수 있는 기호를 사용 하 여 한 요청에서 연결 된 검색을 두 번 이상 수행할 수 있습니다. 이러한 경우, 모든 조건을 한 번만 만족 하는 조건을 검색 하는 대신 연결 된 검색에서 각 매개 변수를 검색 합니다.

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

이는 "Sarah"이 포함 된 모든 리소스를 반환 하며,이 경우에는 `Patient` `generalPractitioner` `generalPractitioner` 주소가 WA 인 주소가 포함 되어 있습니다. 즉, 환자가 Sarah 상태에서 청구 되 고 환자의 시/도에서 청구 되는 경우이 `generalPractitioner` 반환 됩니다.

모든 조건을 그룹으로 포함 하는 및 작업으로 검색 해야 하는 시나리오의 경우 아래 **복합 검색** 예를 참조 하세요.

## <a name="reverse-chain-search"></a>역방향 체인 검색

체인 검색을 통해 참조 하는 리소스의 속성을 기준으로 리소스를 검색할 수 있습니다. 역방향 체인 검색을 사용 하 여 다른 방법으로이 작업을 수행할 수 있습니다. 매개 변수를 사용 하 여 참조 하는 리소스의 속성을 기준으로 리소스를 검색할 수 있습니다 `_has` . 예를 들어 `Observation` 리소스에는 `patient` 환자 리소스를 참조 하는 검색 매개 변수가 있습니다. 특정를 사용 하 여에서 참조 하는 모든 환자 리소스를 찾으려면 `Observation` `code` 다음을 수행 합니다.

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

이 요청은 코드에서 참조 하는 환자 리소스를 반환 `Observation` `527` 합니다. 

또한 역방향 체인 검색은 재귀 구조를 포함할 수 있습니다. 예를 들어 특정 사용자의 감사 이벤트를 관찰 하는 모든 환자를 검색 하려는 경우 `Observation` `janedoe` 다음을 수행할 수 있습니다.

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> fhir 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 fhir 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 100 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다.

## <a name="composite-search"></a>복합 검색

한 번에 여러 조건을 충족 하는 리소스를 검색 하려면 단일 매개 변수 값의 시퀀스를 기호와 조인 하는 복합 검색을 사용 `$` 합니다. 반환 된 결과는 조인 된 검색 매개 변수로 지정 된 모든 조건과 일치 하는 리소스의 교집합입니다. 이러한 검색 매개 변수를 복합 검색 매개 변수 라고 하며, 중첩 된 구조체의 여러 매개 변수를 결합 하는 새 매개 변수를 정의 합니다. 예를 `DiagnosticReport` 들어 `Observation` 9.2 보다 작거나 같은 potassium 값을 포함 하는 모든 리소스를 찾으려는 경우:

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

이 요청은 코드를 포함 하는 구성 요소를 지정 합니다 `2823-3` .이 경우에는 potassium입니다. 기호 뒤에는 `$` "작거나 같음"을 사용 하 여 구성 요소 값의 범위를 지정 하 `lt` 고 `9.2` potassium 값 범위에 대해를 지정 합니다. 

## <a name="search-the-next-entry-set"></a>다음 항목 집합 검색

단일 검색 쿼리 당 반환 될 수 있는 최대 항목 수는 1000입니다. 그러나 검색 쿼리와 일치 하는 항목이 1000 개 보다 많을 수 있으며, 반환 된 첫 번째 1000 항목 후에 다음 항목 집합을 볼 수 있습니다. 이 경우 `url` `searchset` 아래 결과와 같이에서 연속 토큰 값을 사용 합니다 `Bundle` .

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

필드 아래에서 제공 된 URL에 대 한 GET 요청을 수행 합니다 `relation: next` .

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

검색 결과에 대 한 다음 항목 집합을 반환 합니다. 는 검색 `searchset` 결과 항목의 전체 집합으로, 연속 토큰은 `url` 검색 쿼리에 대해 반환 되는 최대 항목 수에 대 한 제한 때문에 첫 번째 집합에 표시 되지 않는 항목을 검색 하기 위해 서버에서 제공 하는 링크입니다.

## <a name="search-using-post"></a>POST를 사용 하 여 검색

위에서 언급 한 모든 검색 예제에서는 요청을 사용 했습니다 `GET` . 다음을 사용 하 여 검색 작업을 수행할 수도 있습니다 `POST` `_search` .

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

이 요청은 값이 `Patient` 45 인 모든 리소스를 반환 `id` 합니다. GET 요청에서와 마찬가지로 서버는 해당 조건을 충족 하는 리소스 집합을 확인 하 고 HTTP 응답에서 번들 리소스를 반환 합니다.

쿼리 매개 변수를 양식 본문으로 제출 하는 게시물을 사용 하 여 검색 하는 또 다른 예는 다음과 같습니다.

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)