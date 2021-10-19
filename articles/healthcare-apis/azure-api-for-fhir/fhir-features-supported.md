---
title: Azure에서 지원 되는 FHIR 기능-FHIR 용 Azure API
description: 이 문서에서는 Azure API for FHIR에서 구현 되는 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 6/16/2021
ms.author: cavoeg
ms.openlocfilehash: 5db569349134bd63b0341cc7afb024cfad83b884
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046148"
---
# <a name="features"></a>기능

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에는 FHIR 서버의 주요 기능이 나열 되어 있습니다.

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
| 연결 된 검색                 | 예       | 예       | 아래 참고를 참조 하세요. |
| 역방향 연결 된 검색         | 예       | 예       | 아래 참고를 참조 하세요. |
| 일괄 처리                          | 예       | 예       |
| 트랜잭션                    | 아니요        | 예       |
| 페이징                         | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 아니요        | 아니요        |

> [!Note] 
> fhir 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 fhir 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 1000 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다.

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

| 검색 매개 변수 유형 | FHIR용 Azure API | 의료 API의 FHIR 서비스| 의견 |
|------------------------|-----------|-----------|---------|
| $export(전체 시스템) | 예       | 예       |         |
| 환자/$export        | 예       | 예       |         |
| 그룹/$export          | 예       | 예       |         |
| $convert-data          | 예       | 예       |         |
| $validate              | 예       | 예       |         |
| $member 일치          | 예       | 예       |         |
| $patient-everything    | 예       | 예       |         |
| $purge 기록         | 예       | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지속성 모듈이 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) 있습니다(참조).

현재 FHIR 서버 오픈 소스 코드에는 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 및 SQL Database 대한 [구현이](https://azure.microsoft.com/services/sql-database/)포함되어 있습니다.

Cosmos DB는 전역적으로 분산된 다중 모델(SQL API, MongoDB API 등) 데이터베이스입니다. 다양한 일관성 [수준](../../cosmos-db/consistency-levels.md)을 지원합니다. 기본 배포 템플릿은 일관성을 사용하여 FHIR 서버를 `Strong` 구성하지만 요청 헤더를 사용하여 요청에 따라 일관성 정책을 수정(일반적으로 완화)할 수 `x-ms-consistency-level` 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서버는 액세스 제어에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 사용합니다. 특히 구성 매개 변수가 로 `FhirServer:Security:Enabled` 설정되고 FHIR 서버에 대한 모든 `true` 요청(제외)에 요청 헤더가 로 설정된 경우 RBAC(역할 기반 액세스 `/metadata` 제어)가 `Authorization` 적용됩니다. `Bearer <TOKEN>` 토큰은 클레임에 정의된 역할을 하나 이상 포함해야 `roles` 합니다. 토큰에 지정된 리소스에 대해 지정된 작업을 허용하는 역할이 포함된 경우 요청이 허용됩니다.

현재 지정된 역할에 허용되는 작업은 API에 *전역적으로* 적용됩니다.

## <a name="service-limits"></a>서비스 제한

* [**요청 단위(RUS)**](../../cosmos-db/concepts-limits.md) - 포털에서 Azure API for FHIR 최대 10,000개까지 구성할 수 있습니다. 최소 400개 또는 40개 CPU/GB 중 더 큰 값이 필요합니다. 10,000개 이상의 RU가 필요한 경우 지원 티켓을 넣어서 RU를 늘릴 수 있습니다. 사용 가능한 최대값은 1,000,000입니다.

* **번들 크기** - 각 번들은 500개 항목으로 제한됩니다.

* **데이터 크기** - 데이터/문서는 각각 2MB 미만이어야 합니다.

* **구독 제한** - 기본적으로 각 구독은 최대 10개의 FHIR 서버 인스턴스로 제한됩니다. 구독당 더 많은 인스턴스가 필요한 경우 지원 티켓을 열고 요구 사항에 대한 세부 정보를 제공합니다.

* **동시 연결 및 인스턴스** - 기본적으로 클러스터의 두 인스턴스에 15개의 동시 연결이 있습니다(총 30개의 동시 요청). 더 많은 동시 요청이 필요한 경우 지원 티켓을 열고 요구 사항에 대한 세부 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR 지원되는 FHIR 기능에 대해 읽어보았습니다. 다음으로 Azure API for FHIR 배포합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
