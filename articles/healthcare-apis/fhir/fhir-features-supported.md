---
title: FHIR 서비스에서 지원 되는 FHIR 기능
description: 이 문서에서는 의료 Api에 구현 된 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045846"
---
# <a name="supported-fhir-features"></a>지원 되는 FHIR 기능

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

&reg;Azure 의료 api의 fhir 서비스 (FHIR 서비스 라고 함)는 [오픈 소스 Fhir 서버](https://github.com/microsoft/fhir-server) 를 완전히 관리 하는 배포를 제공 하며, [fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 FHIR 서비스의 주요 기능을 보여 줍니다.

## <a name="fhir-version"></a>FHIR 버전

지원 되는 최신 버전: `4.0.1`

이전 버전은 현재 다음과 같이 지원 됩니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API    | FHIR용 Azure API | 의료 Api의 FHIR 서비스 | 의견 |
|--------|--------------------|---------------------------------|---------|
| 읽기   | 예                | 예                             |         |
| vread  | 예                | 예                             |         |
| update | 예                | 예                             |         | 
| 낙관적 잠금으로 업데이트 | 예       | 예       |
| update (조건부)           | 예       | 예       |
| 패치                          | 예       | 예       | [JSON Patch](https://www.hl7.org/fhir/http.html#patch) 에만 지원 됩니다. [이 PR](https://github.com/microsoft/fhir-server/pull/2143)의 번들에 JSON 패치를 사용 하는 방법에 대 한 해결 방법이 포함 되어 있습니다.|
| 패치 (조건부)            | 예       | 예       |
| delete                         | 예       | 예       | 아래의 삭제 섹션에서 세부 정보를 참조 하세요. |
| delete (조건부)           | 예       | 예       | 아래의 삭제 섹션에서 세부 정보를 참조 하세요. |
| history                        | 예       | 예       |
| create                         | 예       | 예       | POST/PUT 모두 지원 |
| create (조건부)           | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조 하세요. |
| 연결 된 검색                 | 예       | 예       | |
| 역방향 연결 된 검색         | 예       | 예       | |
| 일괄 처리                          | 예       | 예       |
| 트랜잭션                    | 아니요        | 예       |
| 페이징                         | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 아니요        | 아니요        |

### <a name="delete-and-conditional-delete"></a>삭제 및 조건부 삭제

FHIR 사양에 정의 된 삭제를 수행 하려면 삭제 후에 다음 버전의 특정 리소스 읽기가 410 HTTP 상태 코드를 반환 하 고 검색을 통해 리소스를 더 이상 찾을 수 없습니다. FHIR 및 FHIR 서비스에 대 한 Azure API를 사용 하 여 리소스의 모든 기록을 포함 하 여 전체 삭제를 수행할 수도 있습니다. 리소스를 완전히 삭제 하려면 매개 변수 설정을 `hardDelete` true ()로 전달할 수 있습니다 `DELETE {server}/{resource}/{id}?hardDelete=true` . 이 매개 변수를 전달 하지 않거나 `hardDelete` 을 false로 설정 하면 리소스의 기록 버전을 계속 사용할 수 있습니다.

Delete 외에도 FHIR 용 Azure API 및 FHIR 서비스는 조건부 삭제를 지원 하 여 리소스를 삭제 하는 검색 조건을 전달할 수 있습니다. 기본적으로 조건부 삭제를 사용 하면 한 번에 하나의 항목을 삭제할 수 있습니다. `_count`매개 변수를 지정 하 여 한 번에 최대 100 개의 항목을 삭제할 수도 있습니다. 다음은 조건부 삭제를 사용 하는 몇 가지 예입니다.

조건부 삭제를 사용 하 여 단일 항목을 삭제 하려면 단일 항목을 반환 하는 검색 조건을 지정 해야 합니다.
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

동일한 검색을 수행할 수 있지만, 모든 기록을 삭제 하려면 하드 Delete = true를 포함 합니다.
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

여러 리소스를 삭제 하려는 경우 `_count=100` 검색 조건과 일치 하는 최대 100 개의 리소스를 삭제 하는를 포함할 수 있습니다. 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>확장 된 작업

REST API를 확장 하는 모든 지원 되는 작업입니다.

| 검색 매개 변수 유형 | FHIR용 Azure API | 의료 Api의 FHIR 서비스| 의견 |
|------------------------|-----------|-----------|---------|
| $export (전체 시스템) | 예       | 예       |         |
| 환자/$export        | 예       | 예       |         |
| 그룹/$export          | 예       | 예       |         |
| $convert-data          | 예       | 예       |         |
| $validate              | 예       | 예       |         |
| $member 일치          | 예       | 예       |         |
| $patient-everything    | 예       | 예       |         |
| $purge 기록         | 예       | 예       |         |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서비스는 액세스 제어에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 사용합니다. 

## <a name="service-limits"></a>서비스 제한

* **번들 크기** - 각 번들은 500개 항목으로 제한됩니다.

* **구독 제한** - 기본적으로 각 구독은 최대 10개의 FHIR 서비스로 제한됩니다. 이 제한은 하나 또는 여러 작업 영역에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 서비스에서 지원되는 FHIR 기능에 대해 읽어보았습니다. 다음으로 FHIR 서비스를 배포합니다.
 
>[!div class="nextstepaction"]
>[FHIR 서비스 배포](fhir-portal-quickstart.md)
