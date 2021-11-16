---
title: Azure API 월간 릴리스
description: 이 문서에서는 Azure API for 월간 기능 및 향상 된 기능에 대 한 세부 정보를 제공 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: cavoeg
ms.openlocfilehash: 0acbf2efdb65ff5376da9be918eaead2169cd5d6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547915"
---
# <a name="release-notes-azure-api-for-fhir"></a>릴리스 정보: Azure API for FHIR

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 FHIR 용 Azure API의 기능 및 향상 된 기능에 대 한 세부 정보를 제공 합니다.


## <a name="october-2021"></a>2021년 10월

### <a name="bug-fixes"></a>**버그 수정**

| 무한 루프 버그 | 관련 정보          |
| ----------------- | ----------------------------: |
|[조건부 삭제가](./././../azure-api-for-fhir/fhir-rest-api-capabilities.md#conditional-delete) 무한 루프를 발생 시킬 수 있는 문제를 해결 했습니다. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |

## <a name="september-2021"></a>2021년 9월 

### <a name="features-and-enhancements"></a>**기능 및 향상 된 기능**

|된 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |관련 정보 |
|------------------- | --------------- |

|조건부 패치에 대 한 지원 추가 | [조건부 패치](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| ----------------------------------- | ------: |
|조건부 패치 |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|조건부 패치 감사 이벤트를 추가 했습니다. |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|번들에서 JSON 패치 허용 | [번들의 JSON 패치](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| ----------------------------------- | ------: |
|패치 요청이 포함 된 검색 기록 번들을 허용 합니다. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|이진 리소스를 사용 하 여 번들에서 JSON 패치를 사용 하도록 설정 했습니다. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|새 감사 이벤트 하위 유형 |관련 정보 |
| ----------------------------------- | ---------------: |
|새 감사 [OperationName 하위 유형이](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)추가 되었습니다.| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[향상 된 인덱스 재 인덱싱](how-to-run-a-reindex.md) |관련 정보 |
| ----------------------------------- | ---------------: |
|[인덱스를 재 인덱싱 하기 위한 경계를](how-to-run-a-reindex.md) 추가 했습니다. |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|매개 변수 경계를 재 인덱싱 하기 위한 오류 메시지를 업데이트 합니다. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|최종 인덱스 인덱스 검사를 추가 했습니다. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**버그 수정**

|해결 된 패치 버그 |관련 정보 |
| :----------------------------------- | ---------------: |
|패치 적용 시 예외에 대 한 더 광범위 한 catch. |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|STU3의 패치를 사용 하 여 기록을 수정 합니다.| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|사용자 지정 검색 버그 |관련 정보 |
| ----------------------------------- | ---------------: |
|사용자 지정 검색 매개 변수를 사용 하 여 삭제 실패를 해결 합니다. | [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|검색 매개 변수를 삭제 하는 동안 다시 시도 논리를 추가 했습니다. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager의 검색 옵션에서 최대 항목 수를 설정 합니다. | [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|검색 매개 변수에 잘못 된 식이 있는 경우 더 나은 예외를 제공 합니다. |[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|해결 된 다시 시도 503 오류 |관련 정보 |
| ----------------------------------- | ---------------: |
|Cosmos DB에서 503 오류를 다시 시도 합니다. |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|작업 프로시저에서 429s 처리를 수정 합니다. |[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub 닫힌 문제 |관련 정보 |
| ----------------------------------- | ---------------: |
|CarePlan 의료 장치에 대 한 사용자 지정 검색 매개 변수를 만들 수 없습니다. |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|ID가 없는 조건부 만들기에 대 한 오류 메시지가 명확 하지 않습니다. | [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>FHIR 용 IoT 커넥터 (미리 보기)

|버그 수정 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|관련 정보 |
| ----------------------------------- | ---------------: |
|끊어진 링크를 수정 했습니다.| Azure API for FHIR 포털에서 IoT connector Azure 설명서에 대 한 링크를 업데이트 했습니다. |

## <a name="next-steps"></a>다음 단계

Azure 의료 Api (FHIR 서비스, DICOM service 및 IoT 커넥터)의 기능 및 버그 수정에 대 한 자세한 내용은을 참조 하세요.

>[!div class="nextstepaction"]
>[릴리스 정보: Azure 의료 Api](../release-notes.md)
