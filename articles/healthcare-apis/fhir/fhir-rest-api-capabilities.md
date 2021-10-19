---
title: Azure 의료 Api에 대 한 FHIR Rest API 기능 서비스
description: 이 문서에서는 Azure 의료 Api fRESTful r 서비스에 대 한 상호 작용 및 기능을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: bca8eb3714a685c9477f512a38406cb77f1c6886
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187743"
---
# <a name="fhir-rest-api-capabilities-for-azure-healthcare-apis-fhir-service"></a>Azure 의료 Api에 대 한 FHIR Rest API 기능 서비스

이 문서에서는 Azure 의료 Api FHIR 서비스 (미묘한 차이)의 RESTful 상호 작용에 대해 설명 합니다.


## <a name="conditional-createupdate"></a>조건부 만들기/업데이트

FHIR 서비스는 FHIR 사양에 정의 된 대로 만들기, 조건부 만들기, 업데이트 및 조건부 업데이트를 지원 합니다. 이러한 시나리오에서 유용한 헤더 중 하나는 [일치](https://www.hl7.org/fhir/http.html#concurrency) 하는 헤더입니다. `If-Match`헤더가 사용 되 고 업데이트를 수행 하기 전에 업데이트 되는 버전의 유효성을 검사 합니다. `ETag`이 예상한와 일치 하지 않으면 `ETag` 오류 메시지 *412 전제 조건 실패* 를 생성 합니다. 

## <a name="delete"></a>삭제

FHIR 사양에 정의 된 [삭제](https://www.hl7.org/fhir/http.html#delete) 를 수행 하려면 삭제 후에 다음 버전의 특정 리소스 읽기가 410 HTTP 상태 코드를 반환 하 고 검색을 통해 리소스를 더 이상 찾을 수 없습니다. 또한 FHIR 서비스를 사용 하면 리소스의 모든 기록을 포함 하 여 전체를 삭제할 수 있습니다. 리소스를 완전히 삭제 하려면 매개 변수 설정을 `hardDelete` true ()로 전달할 수 있습니다 `DELETE {server}/{resource}/{id}?hardDelete=true` . 이 매개 변수를 전달 하거나 false로 설정 하지 않으면 `hardDelete` 리소스의 기록 버전을 계속 사용할 수 있습니다.

> [!NOTE]
> 기록만 삭제 하려는 경우 FHIR 서비스는 사용자 지정 작업을 지원 하며,이를 `$purge-history` 통해 리소스에서 기록을 삭제할 수 있습니다. 

## <a name="conditional-delete"></a>조건부 삭제

Delete 외에도 FHIR 서비스는 조건부 삭제를 지원 하 여 리소스를 삭제 하는 검색 조건을 전달할 수 있습니다. 기본적으로 조건부 삭제를 사용 하면 한 번에 하나의 항목을 삭제할 수 있습니다. `_count`매개 변수를 지정 하 여 한 번에 최대 100 개의 항목을 삭제할 수도 있습니다. 다음은 조건부 삭제를 사용 하는 몇 가지 예입니다.

조건부 삭제를 사용 하 여 단일 항목을 삭제 하려면 단일 항목을 반환 하는 검색 조건을 지정 해야 합니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

동일한 검색을 수행할 수 있지만, 모든 기록을 삭제 하려면 하드 Delete = true를 포함 합니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

여러 리소스를 삭제 하려는 경우 `_count=100` 검색 조건과 일치 하는 최대 100 개의 리소스를 삭제 하는를 포함할 수 있습니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>패치 및 조건부 패치

패치는 FHIR 리소스의 일부만 업데이트 해야 하는 경우에 중요 한 RESTful 작업입니다. Patch를 사용 하면 전체 레코드를 업데이트 하지 않고도 리소스에서 업데이트 하려는 요소를 지정할 수 있습니다. FHIR은 FHIR: JSON 패치, XML 패치 및 FHIR 경로 패치에서 리소스를 패치 하는 세 가지 유형의 방법을 정의 합니다. FHIR 서비스는 JSON 패치 및 조건부 JSON 패치 (ID 대신 검색 조건에 따라 리소스를 패치할 수 있음)를 지원 합니다. JSON 패치를 사용 하는 몇 가지 예제를 살펴보려면 샘플 [REST 파일](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http)을 참조 하세요.

> [!NOTE]
> `PATCH`STU3에 대해를 사용 하는 경우 및 기록 번들을 요청 하는 경우 패치 된 리소스의 `Bundle.entry.request.method` 이에 매핑됩니다 `PUT` . 이는 STU3가 `PATCH` [httpverb 값 집합](http://hl7.org/fhir/STU3/valueset-http-verb.html)의 동사에 대 한 정의를 포함 하지 않기 때문입니다.

### <a name="testing-patch"></a>테스트 패치

패치 내에는 패치를 수행 하기 전에 조건이 true 인지 확인 하는 데 사용할 수 있는 테스트 작업이 있습니다. 예를 들어 사망한로 환자를 설정 하려는 경우 (아직 사망한로 표시 되지 않은 경우에만) 아래 예제를 사용할 수 있습니다. 

패치 `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` 콘텐츠 형식: `application/json-patch+json`

```
[
    {
        "op": "test",
        "path": "/deceasedBoolean",
        "value": false
    },
    {
        "op": "replace",
        "path": "/deceasedBoolean",
        "value": true
    }
]

```

### <a name="patch-in-bundles"></a>번들로 패치

기본적으로 JSON 패치는 번들 리소스에서 지원 되지 않습니다. 번들은 FHIR 리소스만 지원 하 고 JSON 패치는 FHIR 리소스가 아니기 때문입니다. 이 문제를 해결 하기 위해 `"application/json-patch+json"` 번들이 실행 될 때 JSON 문자열의 콘텐츠 형식으로 이진 리소스를 처리 합니다. 이 해결 방법에 대 한 자세한 내용은 [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)에 로그인 하세요. 

아래 예제에서는 환자의 성별을 여성로 변경 하려고 합니다. JSON 패치를 수행 `[{"op":"replace","path":"/gender","value":"female"}]` 하 고 base64로 인코드 했습니다.

사후 `https://{FHIR-SERVICE-NAME}/` 콘텐츠 형식: `application/json`

```
{
    "resourceType": "Bundle",
    "id": "bundle-batch",
    "type": "batch",
    "entry": [
        {
            "fullUrl": "Patient/{PatientID}",
            "resource": {
                "resourceType": "Binary",
                "contentType": "application/json-patch+json",
                "data": "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            "request": { 
                "method": "PATCH",
                "url": "Patient/{PatientID}"
            }
        }
    ]
}

```

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 서비스의 몇 가지 REST 기능에 대해 알아보았습니다. 다음으로 FHIR 서비스에서 리소스를 검색 하는 주요 측면에 대해 자세히 알아볼 수 있습니다. 

>[!div class="nextstepaction"]
>[FHIR 검색 개요](overview-of-search.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.


