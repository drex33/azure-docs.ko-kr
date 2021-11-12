---
title: Azure에서 지원 되는 FHIR 기능-FHIR 용 Azure API
description: 이 문서에서는 Azure API for FHIR에서 구현 되는 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: b6c170a7b2f46adeb4b287424601455bb078aba7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397303"
---
# <a name="features"></a>기능

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에는 FHIR 서버의 주요 기능이 나열 되어 있습니다.

## <a name="fhir-version"></a>FHIR 버전

지원 되는 최신 버전: `4.0.1`

이전 버전은 현재 다음과 같이 지원 됩니다. `3.0.2`

## <a name="rest-api"></a>REST API

다음은 지원 되는 RESTful 기능에 대 한 요약입니다. 이러한 기능을 구현 하는 방법에 대 한 자세한 내용은 [Fhir REST API 기능](fhir-rest-api-capabilities.md)을 참조 하세요.

| API    | FHIR용 Azure API | 의료 Api의 FHIR 서비스 | 의견 |
|--------|--------------------|---------------------------------|---------|
| 읽기   | 예                | 예                             |         |
| vread  | 예                | 예                             |         |
| update | 예                | 예                             |         | 
| 낙관적 잠금으로 업데이트 | 예       | 예       |
| update (조건부)           | 예       | 예       |
| 패치                          | 예       | 예       | [JSON Patch](https://www.hl7.org/fhir/http.html#patch) 에만 지원 됩니다. [이 PR](https://github.com/microsoft/fhir-server/pull/2143)의 번들에 JSON 패치를 사용 하는 방법에 대 한 해결 방법이 포함 되어 있습니다.|
| 패치 (조건부)            | 예       | 예       |
| history                        | 예       | 예       |
| create                         | 예       | 예       | POST/PUT 모두 지원 |
| create (조건부)           | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조 하세요. |
| 연결 된 검색                 | 예       | 예       | 아래 참고를 참조 하세요. |
| 역방향 연결 된 검색         | 예       | 예       | 아래 참고를 참조 하세요. |
| 일괄 처리                          | 예       | 예       |
| 트랜잭션                    | 예        | 예       |
| 페이징                         | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 예        | 예        |

> [!Note] 
> fhir 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 fhir 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 1000 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다.

## <a name="extended-operations"></a>확장 된 작업

REST API를 확장 하는 모든 지원 되는 작업입니다.

| 검색 매개 변수 유형 | FHIR용 Azure API | 의료 Api의 FHIR 서비스| 의견 |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (전체 시스템) | 예       | 예       | 시스템, 그룹 및 환자를 지원 합니다.   |
| [$convert-데이터](convert-data.md)          | 예       | 예       |         |
| [$validate](validation-against-profiles.md)              | 예       | 예       |         |
| [$member-일치](tutorial-member-match.md)          | 예       | 예       |         |
| [$patient-모든 항목](patient-everything.md)    | 예       | 예       |         |
| $purge-기록         | 예       | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지 속성 모듈이 있습니다 (참조 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

현재 fhir 서버 오픈 소스 코드는 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)에 대 한 구현을 포함 합니다.

Cosmos DB는 전역으로 분산 된 다중 모델 (SQL api, MongoDB api 등) 데이터베이스입니다. 서로 다른 [일관성 수준을](../../cosmos-db/consistency-levels.md)지원 합니다. 기본 배포 템플릿은 일관성을 사용 하 여 FHIR 서버를 구성 `Strong` 하지만 요청 헤더를 사용 하 여 요청 별로 요청에서 일관성 정책을 수정할 수 있습니다 (일반적으로 완화 됨) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

fhir 서버는 액세스 제어를 위해 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 를 사용 합니다. 특히 구성 매개 변수가로 설정 되어 있는 경우 RBAC (역할 기반 액세스 제어)가 적용 되 `FhirServer:Security:Enabled` `true` 고 `/metadata` Fhir 서버에 대 한 모든 요청 (제외)에는 `Authorization` 요청 헤더가로 설정 되어 있어야 합니다 `Bearer <TOKEN>` . 토큰은 클레임에 정의 된 하나 이상의 역할을 포함 해야 합니다 `roles` . 지정 된 리소스에 대해 지정 된 작업을 허용 하는 역할이 토큰에 포함 되어 있으면 요청이 허용 됩니다.

현재 지정 된 역할에 대해 허용 되는 작업은 API에 *전역적* 으로 적용 됩니다.

## <a name="service-limits"></a>서비스 제한

* [**Rus (요청 단위)**](../../cosmos-db/concepts-limits.md) -Azure API for FHIR에 대 한 포털에서 최대 1만 RUs를 구성할 수 있습니다. 최소 400 RUs 또는 40 RUs/GB 중 더 큰 값이 필요 합니다. RUs를 1만 개 이상 필요로 하는 경우 지원 티켓에 추가 하 여 RUs를 늘릴 수 있습니다. 사용 가능한 최대값은 100만입니다.

* **번들 크기** -각 번들은 500 항목으로 제한 됩니다.

* **데이터 크기** -데이터/문서는 각각 2mb 보다 약간 작아야 합니다.

* **구독 제한** -기본적으로 각 구독은 최대 10 개의 FHIR 서버 인스턴스로 제한 됩니다. 구독 당 더 많은 인스턴스가 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

* **동시 연결 및 인스턴스** -기본적으로 클러스터의 두 인스턴스에 대 한 동시 연결 수가 15 개 있습니다 (총 30 개 동시 요청). 더 많은 동시 요청이 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에서 지원 되는 FHIR 기능에 대해 읽었습니다. FHIR 용 Azure API 배포에 대 한 자세한 내용은을 참조 하세요.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
