---
title: Azure Healthcare API 월별 릴리스
description: 이 문서에서는 Azure Healthcare API 월별 기능 및 향상된 기능에 대한 세부 정보를 제공합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/01/2021
ms.author: cavoeg
ms.openlocfilehash: ba9bb7fcdef85ae93fdce593cdf416ca3e7387c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477556"
---
# <a name="release-notes-azure-healthcare-apis"></a>릴리스 정보: Azure Healthcare API

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다. 

Azure Healthcare API는 의료 산업의 개방형 표준 및 프레임워크를 기반으로 하는 관리되는 API 서비스 세트입니다. 이를 통해 PHI(보호된 의료 정보) 데이터 세트를 함께 가져오고 기계 학습, 분석 및 AI용 도구와 엔드투엔드 연결하여 확장 가능하고 안전한 의료 솔루션을 빌드할 수 있습니다. 이 문서에서는 서로 원활하게 작동하는 다양한 서비스 유형(FHIR 서비스, DICOM 서비스 및 IoT 커넥터)을 포함하여 Azure Healthcare API의 기능 및 향상된 기능에 대해 자세히 설명합니다.

## <a name="september-2021"></a>2021년 9월

### <a name="fhir-service"></a>FHIR 서비스

#### <a name="feature-enhancements"></a>**향상된 기능**

|개선 사항 | Description |
|:------------------- | -----------:|

|조건부 패치에 대한 지원 추가 | [조건부 패치](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -----------:|
|조건부 패치 | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|조건부 패치 감사 이벤트 추가 | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|번들에서 JSON 패치 허용 | [번들의 JSON 패치](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -----------:|
|패치 요청이 있는 검색 기록 번들을 허용합니다. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|이진 리소스를 사용하여 번들에서 JSON 패치 사용 |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|새 감사 이벤트 [OperationName 하위 형식이](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) 추가되었습니다.| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| 다시 인덱스 작업 실행 | [다시 인덱스 개선](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -----------:|
|[다시 인덱스](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) 매개 변수에 대한 경계 추가|[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|다시 인덱스 매개 변수 경계에 대한 오류 메시지 업데이트|[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|최종 다시 인덱스 수 검사를 추가합니다. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**버그 수정**

|해결된 패치 버그 | Description |
|:------------------- | -----------:|

| 패치를 적용하는 동안 예외에 대한 더 넓은 catch | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------:|
|STU3에서 PATCH를 통해 기록 수정 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|사용자 지정 검색 버그 |Description |
|:------------------- | -----------:|
|Custom Search 매개 변수를 통해 삭제 실패를 해결합니다. |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|검색 매개 변수를 삭제하는 동안 재시도 논리가 추가되었습니다. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager에서 검색 옵션의 최대 항목 수 설정 |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|검색 매개 변수에 잘못된 식이 있는 경우 더 나은 예외 |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|한 리소스가 실패하는 경우 일괄 처리 다시 인덱스 SQL 해결됨 |Description |
|:------------------- | -----------:|
|일괄 처리 다시 인덱스 재시도 논리를 SQL 업데이트 |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub 문제 해결 |Description |
|:------------------- | -----------:|
|ID가 없는 조건부 만들기에 대한 명확하지 않은 오류 메시지 |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM 서비스**

|버그 수정 | Description |
|:------------------- | -----------:|

|QAGING 페이징 순서 문제를 해결하기 위한 수정이 구현되었습니다. |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -----------:|

### <a name="iot-connector"></a>**IoT 커넥터**

|버그 수정 | Description |
|:------------------- | -----------:|
| IoT 커넥터는 상태 데이터 표준화를 지원하고 향상시키기 위해 계산을 통해 향상된 기능을 정규화했습니다. | 참조: [디바이스 매핑 및](./../healthcare-apis/iot/how-to-use-device-mapping-iot.md) [계산된 함수](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md) 사용  |

## <a name="next-steps"></a>다음 단계

Azure API for FHIR 기능 및 버그 수정에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[릴리스 정보: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)


