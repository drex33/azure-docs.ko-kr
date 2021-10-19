---
title: Azure 의료 Api의 FHIR 서비스에 대 한 Faq
description: FHIR Api 및 버전 지원에 대 한 데이터의 저장소 위치와 같은 FHIR 서비스에 대 한 질문과 대답을 확인 하세요.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 7e36e91125e721d8ce5ed4dbc2603109838ca531
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780014"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>FHIR 서비스에 대 한 질문과 대답

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 섹션에서는 Azure 의료 Api FHIR 서비스 (FHIR 서비스 라고 함)에 대 한 몇 가지 질문과 대답을 다룹니다.

## <a name="fhir-service-the-basics"></a>FHIR 서비스: 기본 사항

### <a name="what-is-fhir"></a>FHIR 이란?

신속한 전자 의료 기록 교환(FHIR-"fire"로 발음됨)은 서로 다른 상태 시스템 간의 의료 데이터 교환을 가능하게 하는 상호 운용성 표준입니다. 이 표준은 HL7 조직에서 개발 되었으며 전 세계 의료 기관에서 채택 하 고 있습니다. 최신 버전의 FHIR은 4, 4 (릴리스 4)로 제공 됩니다. FHIR 서비스는 4 ~ 4를 지원 하 고 이전 버전 STU3 (평가판 사용에 대 한 표준 3)도 지원 합니다. FHIR에 대 한 자세한 내용은 [HL7.org](http://hl7.org/fhir/summary.html)를 참조 하세요.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Azure에 저장 된 FHIR Api의 데이터 인지 여부

예, 데이터는 Azure의 관리 되는 데이터베이스에 저장 됩니다. Azure 의료 Api의 FHIR 서비스는 기본 데이터 저장소에 대 한 직접 액세스를 제공 하지 않습니다.

### <a name="what-identity-provider-do-you-support"></a>지원 되는 id 공급자는 무엇 인가요?

현재 id 공급자로 Microsoft Azure Active Directory을 지원 합니다.

### <a name="what-fhir-version-do-you-support"></a>어떤 FHIR 버전을 지원 하나요?

4.0.0 및 3.0.1 버전을 지원 합니다.

자세한 내용은 [지원 되는 기능](fhir-features-supported.md)을 참조 하세요. [HL7 FHIR의 버전 기록](https://hl7.org/fhir/R4/history.html)에서 fhir 버전 (즉, STU3 to 4) 사이에 변경 된 내용에 대해 알아보세요.

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>의료 Api의 Azure API와 fhir 서비스 간의 차이점은 무엇 인가요?

FHIR 서비스는 단일 작업 영역 내에서 FHIR 서비스 및 DICOM service를 사용할 수 있도록 하는 Azure 의료 Api에 있는 FHIR 사양의 구현입니다. FHIR 용 Azure API는 초기 GA 제품으로 서, 계속 독립 실행형 제품으로 사용할 수 있습니다. 주요 기능 차이점은 다음과 같습니다.

* FHIR 서비스는 4TB 제한이 있으며, FHIR 용 Azure API는 4TB 보다 많은 것을 지원 하 고 GA입니다.
* FHIR 서비스는 [트랜잭션 번들](https://www.hl7.org/fhir/http.html#transaction)을 지원 합니다.
* FHIR 용 Azure API에는 Azure 의료 Api의 FHIR 서비스에서 아직 사용할 수 없는 추가 플랫폼 기능 (예: 개인 링크, 고객 관리 키 및 로깅)이 있습니다. 자세한 내용은 GA의 이러한 기능을 참조 하세요.

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>Azure 의료 Api의 ' FHIR 서비스 ' 및 오픈 소스 ' FHIR 서버 '의 차이점은 무엇 인가요?

Azure 의료 Api의 FHIR 서비스는 호스트 되 고 관리 되는 버전의 Azure 용 오픈 소스 Microsoft FHIR 서버입니다. 관리 서비스에서 Microsoft는 모든 유지 관리 및 업데이트를 제공 합니다.

Azure 용 FHIR 서버를 실행 하면 기본 서비스에 직접 액세스할 수 있지만, 사용 하지 않는 데이터를 저장 하는 경우 서버를 유지 관리 하 고 업데이트 하는 데 필요한 모든 준수 작업을 담당 합니다.

### <a name="in-which-regions-is-the-fhir-service-available"></a>FHIR 서비스를 사용할 수 있는 지역은 어디 인가요?

Microsoft는 고객의 요구에 따라 의료 Api의 글로벌 차지를 지속적으로 확장 하 고 있습니다. FHIR 서비스는 현재 오스트레일리아 동부, 브라질 남부 *, 캐나다 중부, 인도 중부*, 동아시아 *, 미국 동부 2, 미국 동부, 독일 북부 **, 독일 중서부, 독일 북부**, 일본 동부, 일본 서 부*, 대한민국 중부 *, 미국 중 북부, 북부 유럽, 남아프리카 공화국 북부, 미국 중 북부, 동남 아시아, 스위스 북부 등의 25 개 지역에서 사용할 수 있습니다.  영국 남부, 영국 서부, 미국 서 부, 유럽 서부, 미국 서 부 2, 미국 정부 버지니아, 미국 정부 애리조나.

* 비공개 미리 보기 영역을 나타냅니다. * *는 재해 복구 지역만을 나타냅니다.

자세한 내용은 [지리적 지역 지원](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all)을 참조 하세요.

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>어디에서 FHIR 서비스에 릴리스를 확인할 수 있나요?

FHIR 서비스에 릴리스 되는 항목을 확인 하려면 오픈 소스 FHIR 서버 [릴리스](https://github.com/microsoft/fhir-server/releases) 를 참조 하세요. 항목을 관리 되는 서비스로 릴리스하고 일반적으로 2 주 후에는 항목을 FHIR-Service로 태그를 설정 해야 합니다. 사용자 환경에서 테스트 하려는 경우 [여기](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 에서 빌드를 테스트 하는 방법에 대 한 지침도 포함 되어 있습니다. 관리 되는 추가 서비스 업데이트를 가장 잘 공유 하는 방법을 평가 하 고 있습니다.

### <a name="what-is-smart-on-fhir"></a>FHIR의 SMART 란?

FHIR의 SMART (대체 가능한 의료 응용 프로그램 및 재사용 가능한 기술)는 파트너 응용 프로그램을 FHIR 서버와 통합 하는 개방형 사양 집합으로, 전자적 상태 레코드 및 상태 정보 교환 등의 기타 상태 IT 시스템입니다. SMART on FHIR 응용 프로그램을 만들어 응용 프로그램을 서로 다른 시스템의 다양 한에서 액세스 하 고 활용할 수 있는지 확인할 수 있습니다.
SMART에 대해 자세히 알아보려면 [스마트 상태](https://smarthealthit.org/)를 참조 하세요.

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>데이터베이스에서 실행 되는 FHIR 버전은 어디에서 찾을 수 있나요?

기능 문에 표시 되는 정확한 FHIR 버전은 "Fserversion" 속성 (FHIR URL/metadata)에서 찾을 수 있습니다.

## <a name="fhir-implementations-and-specifications"></a>구현 및 사양

### <a name="can-i-create-a-custom-fhir-resource"></a>사용자 지정 FHIR 리소스를 만들 수 있나요?

사용자 지정 FHIR 리소스를 허용 하지 않습니다. 사용자 지정 FHIR 리소스가 필요한 경우에는 확장을 사용 하 여 [기본 리소스](http://www.hl7.org/fhir/basic.html) 위에 사용자 지정 리소스를 빌드할 수 있습니다. 

### <a name="are-extensions-supported-on-the-fhir-service"></a>FHIR 서비스에서 [확장이](https://www.hl7.org/fhir/extensibility.html) 지원 되나요?

모든 유효한 FHIR JSON 데이터를 서버에 로드할 수 있습니다. 확장을 정의 하는 구조 정의를 저장 하려는 경우이를 구조체 정의 리소스로 저장할 수 있습니다. 확장을 검색 하려면 [고유한 검색 매개 변수를 정의](how-to-do-custom-search.md)해야 합니다. 

### <a name="what-is-the-limit-on-_count"></a>_Count에 대 한 제한은 무엇 인가요?

_Count에 대 한 현재 제한은 1000입니다. _Count를 1000 이상으로 설정 하면 1000 레코드만 표시 된다는 경고가 번들에 표시 됩니다.

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>번들을 FHIR 서비스에 게시할 수 있나요?

현재는 FHIR 서비스에서 [일괄 처리 번들](https://www.hl7.org/fhir/valueset-bundle-type.html) 게시 및 [트랜잭션 번들](https://www.hl7.org/fhir/http.html#transaction) 게시를 지원 합니다.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>FHIR 서비스에서 단일 환자에 대 한 모든 리소스를 얻으려면 어떻게 해야 하나요?

단일 환자와 관련 된 모든 데이터를 가져오는 [$patient 모든 작업](patient-everything.md) 을 지원 합니다. 

## <a name="using-the-fhir-service"></a>FHIR 서비스 사용

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>워크플로 내에서 FHIR 서비스를 사용 하는 몇 가지 예는 어디에서 확인할 수 있나요?

[상태 아키텍처 GitHub 페이지](https://github.com/microsoft/health-architectures)에서 사용할 수 있는 참조 아키텍처 컬렉션이 있습니다.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>웹 응용 프로그램을 FHIR 서비스에 연결 하는 예제는 어디에서 확인할 수 있나요?

예제 응용 프로그램 및 시나리오를 포함 하는 [상태 아키텍처 GitHub 페이지가](https://aka.ms/health-architectures) 있습니다. 웹 응용 프로그램을 FHIR 서비스에 연결 하는 방법을 보여 줍니다.
