---
title: Azure 의료 Api 월간 릴리스
description: 이 문서에서는 Azure 의료 Api 월간 기능 및 향상 된 기능에 대 한 세부 정보를 제공 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/22/2021
ms.author: cavoeg
ms.openlocfilehash: 304fdc3ceea4c26e861ba54e2dd90f98fd7bc225
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936527"
---
# <a name="release-notes-azure-healthcare-apis"></a>릴리스 정보: Azure 의료 Api

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다. 

Azure 의료 Api는 의료 업계의 오픈 표준 및 프레임 워크를 기반으로 하는 관리 되는 API 서비스 집합입니다. 이 기능을 사용 하면 사용이 금지 된 데이터 집합을 결합 하 고 machine learning, 분석 및 AI 용 도구를 사용 하 여 종단 간 연결을 통해 확장 가능 하 고 안전한 의료 솔루션을 빌드할 수 있습니다. 이 문서에서는 서로 원활 하 게 작동 하는 다양 한 서비스 유형 (FHIR 서비스, DICOM 서비스 및 IoT 커넥터)을 포함 하 여 Azure 의료 Api에 적용 되는 기능 및 향상 된 기능에 대해 자세히 설명 합니다.

## <a name="october-2021"></a>2021년 10월

### <a name="azure-healthcare-apis-feature-enhancements"></a>Azure 의료 Api 기능 향상

| 된 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 관련 정보           |
|------------- | -----------------------------: |
|테스트 데이터 생성기 도구 |microsoft는 sya 데이터를 사용 하 여 [테스트 데이터 생성기 도구](https://github.com/microsoft/healthcare-apis-samples/blob/main/docs/HowToRunPerformanceTest.md) 를 포함 하도록 의료 api GitHub 샘플 리포지토리를 업데이트 했습니다. 이 도구는 성능 테스트를 위해 Azure AKS에 배포할 수 있는 Apache JMeter에 따라 오픈 소스 [공용 테스트 프로젝트](https://github.com/ShadowPic/PublicTestProjects)에 대 한 향상 된 기능입니다. |

### <a name="fhir-service"></a>FHIR 서비스

#### <a name="feature-enhancements"></a>**향상된 기능**

|개선 사항 | 관련 정보 |
|------------------------ | -------------------------------: |
|문자열과 날짜/시간에 대 한 [_sort](././../healthcare-apis/fhir/overview-of-search.md#search-result-parameters) 에 대 한 지원이 추가 되었습니다. |[#2169](https://github.com/microsoft/fhir-server/pull/2169)  |

#### <a name="bug-fixes"></a>**버그 수정**

|버그 수정 | 관련 정보 |
|------------------------ | -------------------------------: |
|[조건부 삭제가](././../healthcare-apis/fhir/fhir-rest-api-capabilities.md#conditional-delete) 무한 루프를 발생 시킬 수 있는 문제를 해결 했습니다. | [#2269](https://github.com/microsoft/fhir-server/pull/2269) |
|번들 게시물의 잘못 된 트랜잭션 본문으로 인해 해결 된 500 오류가 발생 했을 수 있습니다. URL이 [트랜잭션 번들](././..//healthcare-apis/fhir/fhir-features-supported.md#rest-api) 요청에서 채워지는지 확인을 추가 했습니다. | [#2255](https://github.com/microsoft/fhir-server/pull/2255) |

### <a name="dicom-service"></a>**DICOM 서비스**

|지원 추가 | 관련 정보 |
|------------------------ | -------------------------------: |
|영역 | 남부 브라질 및 중부 캐나다. Azure 지역 및 가용성 영역에 대 한 자세한 내용은 [가용성 영역을 지 원하는 azure 서비스](./../availability-zones/az-region.md)를 참조 하세요. |
|확장 쿼리 태그 |DateTime (DT) 및 Time (TM) Value 표시 (VR) 형식 |

|버그 수정 | 관련 정보 |
|------------------------ | -------------------------------: |
|작업 영역 이름에 대 한 수정을 구현 했습니다. |이름이 문자로 시작 하는 작업 영역에서 작업 하도록 DICOM 서비스를 사용 하도록 설정 했습니다. |

## <a name="september-2021"></a>2021년 9월

### <a name="fhir-service"></a>FHIR 서비스

#### <a name="feature-enhancements"></a>**향상된 기능**

|개선 사항 | 관련 정보 |
|:------------------- | -------------------------------: |

|조건부 패치에 대 한 지원 추가 | [조건부 패치](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch)|
|:------------------- | -------------------------------:|
|조건부 패치 | [#2163](https://github.com/microsoft/fhir-server/pull/2163) |
|조건부 패치 감사 이벤트를 추가 했습니다. | [#2213](https://github.com/microsoft/fhir-server/pull/2213) |

|번들에서 JSON 패치 허용 | [번들의 JSON 패치](./././azure-api-for-fhir/fhir-rest-api-capabilities.md#patch-in-bundles)|
|:------------------- | -------------------------------:|
|패치 요청이 포함 된 검색 기록 번들을 허용 합니다. |[#2156](https://github.com/microsoft/fhir-server/pull/2156) | 
|이진 리소스를 사용 하 여 번들에서 JSON 패치를 사용 하도록 설정 했습니다. |[#2143](https://github.com/microsoft/fhir-server/pull/2143) |
|새 감사 이벤트 [OperationName 하위 유형을](./././azure-api-for-fhir/enable-diagnostic-logging.md#audit-log-details) 추가 했습니다.| [#2170](https://github.com/microsoft/fhir-server/pull/2170) |

| 인덱스 작업 실행 | [향상 된 인덱스 재 인덱싱](./././fhir/how-to-run-a-reindex.md)|
|:------------------- | -------------------------------:|
|[인덱스를 재 인덱싱 하기 위한 경계를](./././azure-api-for-fhir/how-to-run-a-reindex.md#performance-considerations) 추가 했습니다. |[#2103](https://github.com/microsoft/fhir-server/pull/2103)|
|매개 변수 경계를 재 인덱싱 하기 위해 업데이트 된 오류 메시지입니다. |[#2109](https://github.com/microsoft/fhir-server/pull/2109)|
|최종 인덱스 인덱스 검사를 추가 했습니다. |[#2099](https://github.com/microsoft/fhir-server/pull/2099)|

#### <a name="bug-fixes"></a>**버그 수정**

|해결 된 패치 버그 | 관련 정보 |
|:------------------- | --------------------------------: |

| 패치를 적용 하는 동안 예외에 대 한 더 광범위 한 catch | [#2192](https://github.com/microsoft/fhir-server/pull/2192)|
|:------------------- | -----------: |
|STU3에서 패치를 사용 하 여 기록 수정 |[#2177](https://github.com/microsoft/fhir-server/pull/2177) |

|사용자 지정 검색 버그 | 관련 정보 |
|:------------------- | -------------------------------: |
|사용자 지정 검색 매개 변수를 사용 하 여 삭제 실패 해결 |[#2133](https://github.com/microsoft/fhir-server/pull/2133) |
|검색 매개 변수를 삭제 하는 동안 다시 시도 논리를 추가 했습니다. | [#2121](https://github.com/microsoft/fhir-server/pull/2121)|
|SearchParameterDefinitionManager의 검색 옵션에서 최대 항목 수 설정 |[#2141](https://github.com/microsoft/fhir-server/pull/2141) |
|검색 매개 변수에 잘못 된 식이 있는 경우 더 나은 예외 |[#2157](https://github.com/microsoft/fhir-server/pull/2157) |

|한 리소스가 실패 하는 경우 일괄 처리 일괄 처리 SQL 확인 됨 | 관련 정보 |
|:------------------- | -------------------------------: |
|일괄 처리 다시 시도 논리 SQL 업데이트 |[#2118](https://github.com/microsoft/fhir-server/pull/2118) |

|GitHub 닫힌 문제 | 관련 정보 |
|:------------------- | -------------------------------: |
|ID 없이 조건부 만들기에 대 한 오류 메시지가 명확 하지 않음 |[#2168](https://github.com/microsoft/fhir-server/issues/2168) |

### <a name="dicom-service"></a>**DICOM 서비스**

|버그 수정 | 관련 정보 |
|:------------------- | -------------------------------: |

|QIDO 페이징 순서 문제를 해결 하는 수정이 구현 되었습니다. |  [#989](https://github.com/microsoft/dicom-server/pull/989) |
|:------------------- | -------------------------------: |

### <a name="iot-connector"></a>**IoT 커넥터**

|버그 수정 | 관련 정보 |
|:------------------- | -------------------------------: |
| IoT 커넥터는 상태 데이터 표준화를 지원하고 향상시키기 위해 계산을 통해 향상된 기능을 정규화했습니다. | 참조: [디바이스 매핑 및](./../healthcare-apis/iot/how-to-use-device-mappings.md) 계산 [함수](./../healthcare-apis/iot/how-to-use-calculated-functions-mappings.md) 사용  |

## <a name="next-steps"></a>다음 단계

Azure API for FHIR 기능 및 버그 수정에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[릴리스 정보: Azure API for FHIR](./azure-api-for-fhir/release-notes.md)
