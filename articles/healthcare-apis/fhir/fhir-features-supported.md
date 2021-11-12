---
title: FHIR 서비스에서 지원되는 FHIR 기능
description: 이 문서에서는 의료 API에서 구현되는 FHIR 사양의 기능을 설명합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: 4c1b9efce8ad29eeb9ebca6285849760f20fbc17
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399904"
---
# <a name="supported-fhir-features"></a>지원되는 FHIR 기능

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

Azure Healthcare API의 FHIR &reg; 서비스(여기서는 FHIR 서비스라고 함)는 [오픈 소스 FHIR 서버의](https://github.com/microsoft/fhir-server) 완전 관리형 배포를 제공하며 [FHIR](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 FHIR 서비스의 주요 기능을 나열합니다.

## <a name="fhir-version"></a>FHIR 버전

지원되는 최신 버전: `4.0.1`

또한 현재 지원되는 이전 버전은 다음과 같습니다. `3.0.2`

## <a name="rest-api"></a>REST API

다음은 지원되는 RESTful 기능에 대한 요약입니다. 이러한 기능의 구현에 대한 자세한 내용은 [FHIR REST API 기능을 참조하세요.](fhir-rest-api-capabilities.md) 

| API    | FHIR용 Azure API | 의료 API의 FHIR 서비스 | 의견 |
|--------|--------------------|---------------------------------|---------|
| 읽기   | 예                | 예                             |         |
| vread  | 예                | 예                             |         |
| update | 예                | 예                             |         | 
| 낙관적 잠금을 통해 업데이트 | 예       | 예       |
| update(조건부)           | 예       | 예       |
| 패치                          | 예       | 예       | [JSON 패치만](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch) 지원합니다. |
| patch(조건부)            | 예       | 예       |
| history                        | 예       | 예       |
| create                         | 예       | 예       | POST/PUT 모두 지원 |
| create(조건부)           | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조하세요. |
| 연결된 검색                 | 예       | 예       | |
| 역방향 체인 검색         | 예       | 예       | |
| 일괄 처리                          | 예       | 예       |
| 트랜잭션                    | 예        | 예       |
| 페이징                         | Partial   | Partial   | `self` 및 `next` 가 지원됩니다.                     |
| 중개자                 | 예        | 예        |

## <a name="extended-operations"></a>확장 작업

REST API 확장하는 모든 작업이 지원됩니다.

| 검색 매개 변수 유형 | FHIR용 Azure API | 의료 API의 FHIR 서비스| 의견 |
|------------------------|-----------|-----------|---------|
| [$export(전체](../../healthcare-apis/data-transformation/export-data.md) 시스템) | 예       | 예       | 시스템, 그룹 및 환자를 지원합니다. |
| [$convert-data](../../healthcare-apis/data-transformation/convert-data.md)          | 예       | 예       |         |
| [$validate](validation-against-profiles.md)              | 예       | 예       |         |
| [$member 일치](tutorial-member-match.md)          | 예       | 예       |         |
| [$patient-everything](patient-everything.md)    | 예       | 예       |         |
| $purge 기록         | 예       | 예       |         |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서비스는 액세스 제어에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 사용합니다. 

## <a name="service-limits"></a>서비스 제한

* **번들 크기** -각 번들은 500 항목으로 제한 됩니다.

* **구독 제한** -기본적으로 각 구독은 최대 10 개의 FHIR 서비스로 제한 됩니다. 한도는 하나 이상의 작업 영역에서 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 서비스에서 지원 되는 FHIR 기능에 대해 읽었습니다. FHIR 서비스를 배포 하는 방법에 대 한 자세한 내용은을 참조 하세요.
 
>[!div class="nextstepaction"]
>[FHIR 서비스 배포](fhir-portal-quickstart.md)
