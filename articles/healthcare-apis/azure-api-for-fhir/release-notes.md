---
title: Azure API for FHIR 월별 릴리스
description: 이 문서에서는 Azure API for FHIR 월별 기능 및 향상된 기능에 대해 자세히 설명합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: 201de8f7bbae01c708757ee2290f6005979410c4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477540"
---
# <a name="release-notes-azure-api-for-fhir"></a>릴리스 정보: Azure API for FHIR

Azure API for FHIR Azure용 Microsoft FHIR 서버의 완전 관리형 배포를 제공합니다. 서버는 [FHIR](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 Azure API for FHIR 기능 및 향상된 기능에 대해 자세히 설명합니다.

## <a name="september-2021"></a>2021년 9월 

### <a name="features-and-enhancements"></a>**기능 및 향상된 기능**

|개선 사항 | Description |
|:------------------- | -----------:|

|조건부 패치에 대한 지원 추가 | [조건부 패치](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
| :----------------------------------- | ------: |
|조건부 패치 |[#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|조건부 패치 감사 이벤트 추가 |[#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|번들에서 JSON 패치 허용 | [번들의 JSON 패치](././../azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
| :----------------------------------- | ------: |
|패치 요청이 있는 검색 기록 번들 허용 |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|이진 리소스를 사용하여 번들에서 JSON 패치 사용 |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |

|새 감사 이벤트 하위 유형| Description|
| :----------------------------------- | ------: |
|새 감사 [OperationName 하위 유형이 추가되었습니다.](././../azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details)| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

|[다시 인덱스 개선 사항:](how-to-run-a-reindex.md) | Description|
| :----------------------------------- | ------: |
|[다시 인덱스](how-to-run-a-reindex.md) 매개 변수에 대한 경계 추가|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|다시 인덱스 매개 변수 경계에 대한 오류 메시지 업데이트|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|최종 다시 인덱스 수 검사를 추가합니다. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|


### <a name="bug-fixes"></a>**버그 수정**

|해결된 패치 버그| Description|
| :----------------------------------- | ------: |
|패치를 적용하는 동안 예외에 대한 더 넓은 catch |[#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|STU3에서 PATCH를 통해 기록 수정| [#2177](https://github.com/microsoft/fhir-server/pull/2177)|

|사용자 지정 검색 버그| Description|
| :----------------------------------- | ------: |
|Custom Search 매개 변수를 통해 삭제 실패를 해결합니다.| [#2133](https://github.com/microsoft/fhir-server/pull/2133)|
|검색 매개 변수를 삭제하는 동안 재시도 논리 추가| [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager에서 검색 옵션의 최대 항목 수 설정| [#2141](https://github.com/microsoft/fhir-server/pull/2141)|
|검색 매개 변수에 잘못된 식이 있는 경우 더 나은 예외|[#2157](https://github.com/microsoft/fhir-server/pull/2157)|

|해결된 다시 시도 503 오류| Description|
| :----------------------------------- | ------: |
|Cosmos DB에서 503 다시 시도 오류 |[#2106](https://github.com/microsoft/fhir-server/pull/2106)|
|StoreProcedures에서 429s 처리를 수정합니다.|[#2165](https://github.com/microsoft/fhir-server/pull/2165)|

|GitHub 문제 해결| Description|
| :----------------------------------- | ------: |
|CarePlan 의료 디바이스에 대한 사용자 지정 검색 매개 변수를 만들 수 없음 |[#2146](https://github.com/microsoft/fhir-server/issues/2146) |
|ID가 없는 조건부 만들기에 대한 명확하지 않은 오류 메시지| [#2168](https://github.com/microsoft/fhir-server/issues/2168)|

### <a name="iot-connector-for-fhir-preview"></a>IoT Connector for FHIR(미리 보기)

|버그 수정| Description|
| :-----------------------------------| ------: |
|IoT 커넥터 Azure 설명서에 대한 링크가 수정됨|Azure API for FHIR 포털의 끊어진 링크 |

## <a name="next-steps"></a>다음 단계

Azure Healthcare API(FHIR 서비스, DICOM 서비스 및 IoT 커넥터)의 기능 및 버그 수정에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[릴리스 정보: Azure Healthcare API](../release-notes.md)
