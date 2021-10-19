---
title: Azure API for FHIR 대한 FHIR Rest API 기능
description: 이 문서에서는 Azure API for FHIR 대한 RESTful 상호 작용 및 기능을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: c69c3838693c3e59aa7a024c0a67c28c6f1a5d21
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187727"
---
# <a name="fhir-rest-api-capabilities-for-azure-api-for-fhir"></a>Azure API for FHIR 대한 FHIR Rest API 기능

이 문서에서는 Azure API for FHIR RESTful 상호 작용의 미묘한 차이를 다룹니다.


## <a name="conditional-createupdate"></a>조건부 만들기/업데이트

Azure API for FHIR FHIR 사양에 정의된 대로 만들기, 조건부 만들기, 업데이트 및 조건부 업데이트를 지원합니다. 이러한 시나리오에서 유용한 헤더 중 하나는 [If-Match](https://www.hl7.org/fhir/http.html#concurrency) 헤더입니다. `If-Match`헤더가 사용되며 업데이트하기 전에 업데이트되는 버전의 유효성을 검사합니다. 가 `ETag` 예상과 일치하지 않으면 `ETag` 오류 메시지 *412 사전 조건 실패 을 생성합니다.* 

## <a name="delete"></a>삭제

FHIR 사양에 정의된 [삭제를](https://www.hl7.org/fhir/http.html#delete) 사용하려면 삭제 후 이후 버전이 아닌 특정 리소스 읽기에서 410 HTTP 상태 코드를 반환하고 검색을 통해 리소스를 더 이상 찾을 수 없게 해야 합니다. 또한 Azure API for FHIR 사용하면 리소스를 완전히 삭제(모든 기록 포함)할 수 있습니다. 리소스를 완전히 삭제하려면 매개 변수 설정을 true( )로 전달할 수 `hardDelete` `DELETE {server}/{resource}/{id}?hardDelete=true` 있습니다. 이 매개 변수를 전달하지 않거나 false로 설정하면 `hardDelete` 리소스의 기록 버전을 계속 사용할 수 있습니다.

> [!NOTE]
> 기록만 삭제하려는 경우 Azure API for FHIR 리소스에서 기록을 삭제할 수 있는 사용자 지정 작업 를 `$purge-history` 지원합니다. 

## <a name="conditional-delete"></a>조건부 삭제

삭제 외에도 Azure API for FHIR 조건부 삭제를 지원하여 검색 조건을 전달하여 리소스를 삭제할 수 있습니다. 기본적으로 조건부 삭제를 사용하면 한 번에 하나의 항목을 삭제할 수 있습니다. `_count`매개 변수를 지정하여 한 번에 최대 100개 항목을 삭제할 수도 있습니다. 다음은 조건부 삭제를 사용하는 몇 가지 예입니다.

조건부 삭제를 사용하여 단일 항목을 삭제하려면 단일 항목을 반환하는 검색 조건을 지정해야 합니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

동일한 검색을 수행할 수 있지만 hardDelete=true를 포함하면 모든 기록도 삭제할 수 있습니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

여러 리소스를 삭제하려는 경우 검색 `_count=100` 조건과 일치하는 최대 100개의 리소스를 삭제하는 를 포함할 수 있습니다.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>패치 및 조건부 패치

패치는 FHIR 리소스의 일부만 업데이트해야 하는 경우 중요한 RESTful 작업입니다. Patch를 사용하면 전체 레코드를 업데이트하지 않고도 리소스에서 업데이트할 요소를 지정할 수 있습니다. FHIR은 FHIR에서 리소스를 패치하는 세 가지 방법인 JSON 패치, XML 패치 및 FHIR 경로 패치를 정의합니다. FHIR 서비스는 JSON 패치 및 조건부 JSON 패치를 지원합니다(ID 대신 검색 조건에 따라 리소스를 패치할 수 있음). JSON 패치를 사용하는 몇 가지 예제를 진행하려면 샘플 [REST 파일](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http)를 참조하세요.

> [!NOTE]
> STU3에 대해 를 사용하는 `PATCH` 경우 및 기록 번들을 요청하는 경우 패치된 리소스의 `Bundle.entry.request.method` 가 에 `PUT` 매핑됩니다. 이는 STU3이 `PATCH` [HTTPVerb 값 집합](http://hl7.org/fhir/STU3/valueset-http-verb.html)에 동사에 대한 정의를 포함하지 않기 때문입니다.

### <a name="testing-patch"></a>테스트 패치

패치 내에는 패치를 수행하기 전에 조건이 true인지 확인할 수 있는 테스트 작업이 있습니다. 예를 들어 환자를 세울 경우 아직 병가로 표시되지 않은 경우에만 아래 예제를 사용할 수 있습니다. 

PATCH `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` Content-type: `application/json-patch+json`

```
[
    {
        “op”: “test”,
        “path”: “/deceasedBoolean”,
        “value”: false
    },
    {
        “op”: “replace”
        “path”: “/deceasedBoolean”,
        “value”: true
    }
]

```

### <a name="patch-in-bundles"></a>번들의 패치

기본적으로 JSON 패치는 번들 리소스에서 지원되지 않습니다. 번들은 FHIR 리소스만 지원하고 JSON 패치는 FHIR 리소스가 아니기 때문입니다. 이 해결을 위해 번들이 실행될 때 콘텐츠 형식이 인 이진 리소스를 `"application/json-patch+json"` JSON 문자열의 base64 인코딩으로 처리합니다. 이 해결 방법을 알아보려면 [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request)에 로그인합니다. 

아래 예제에서는 환자의 성별을 여성으로 변경하려고 합니다. JSON 패치를 가져와 `[{"op":"replace","path":"/gender","value":"female"}]` base64로 인코딩했습니다.

POST `https://{FHIR-SERVICE-NAME}/` content-type: `application/json`

```
{
    “resourceType”: “Bundle”
    “id”: “bundle-batch”,
    “type”: “batch”
    “entry”: [
        {
            “fullUrl”: “Patient/{PatientID}”,
            “resource”: {
                “resourceType”: “Binary”,
                “contentType”: “application/json-patch+json”,
                “data”: "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            “request”: { 
                “method”: “PATCH”,
                “url”: “Patient/{PatientID}”
            }
        }
    ]
}

```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR REST 기능 중 일부에 대해 배웠습니다. 다음으로 FHIR에서 리소스 검색의 주요 측면에 대해 자세히 알아볼 수 있습니다. 

>[!div class="nextstepaction"]
>[Azure API for FHIR 검색 개요](overview-of-search.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.