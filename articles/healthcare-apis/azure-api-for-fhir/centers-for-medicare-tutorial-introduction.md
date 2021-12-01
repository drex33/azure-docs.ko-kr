---
title: 자습서-Medicare 및 Medicaid Services에 대 한 센터 소개 소개-FHIR 용 Azure API
description: 이 개요에서는 Medicare and Medicaid Services (CMS) 상호 운용성 및 환자 액세스 규칙에 대 한 센터와 관련 된 일련의 자습서를 소개 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 11/29/2021
ms.openlocfilehash: 1390ed2316c4891a23684223644bb976ff230fbc
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359667"
---
# <a name="centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule-introduction"></a>Medicare 및 Medicaid Services (CMS) 상호 운용성 및 환자 액세스 규칙 소개 센터

이 자습서 시리즈에서는 CMS (Medicare and Medicaid Services) 상호 운용성 및 환자 액세스 규칙에 대 한 중앙의 개략적인 요약 및이 규칙에 설명 된 기술 요구 사항에 대해 설명 합니다. 이 규칙에 대해 참조 된 다양 한 구현 가이드를 살펴보겠습니다. 또한 이러한 구현 가이드를 지원 하기 위해 Azure API를 구성 하는 방법에 대 한 세부 정보를 제공 합니다.


## <a name="rule-overview"></a>규칙 개요

5 월 1 일, 2020에 대 한 [상호 운용성 및 환자 액세스 규칙](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) 을 해제 한 CMS입니다. 이 규칙을 사용 하려면 환자 (환자, 공급자 및 payers)에 참여 하는 모든 당사자 간에 안전 하 고 안전한 데이터 흐름이 필요 합니다. 상호 운용성은 의료 업계를 수십 년 동안 겪었던, 사일로 데이터를 사용 하 여 더 높거나 예측할 수 없는 비용으로 부정적 상태 결과를 초래 합니다. CMS는 인증 기관을 사용 하 여이 규칙을 적용 하기 위해 Medicare 혜택 (MA), Medicaid, 자녀의 칩 칩 (건강 보험 프로그램) 및 QHP (정규화 된 상태 계획) 발급자를 규정 하 여이 규칙을 적용 합니다. 

8 월 2020, CMS에서 조직의 요구 사항을 충족 하는 방법에 대해 자세히 설명 합니다. 데이터를 안전 하 고 표준화 된 방식으로 교환할 수 있도록 CMS는 데이터 교환에 필요한 기본 표준으로 FHIR 버전 릴리스 4 (4-4)를 식별 합니다. 

상호 운용성 및 환자 액세스 가로선에는 세 가지 주요 부분이 있습니다.

* **환자 액세스 API (필수 7 월 1 2021 일 필수)** – CMS 규정 payers (위에서 정의 된 대로)는 보안 표준 기반 API를 구현 하 고 유지 관리 하는 데 필요 합니다 .이 api를 사용 하 여 비용을 비롯 하 여 해당 클레임에 대 한 정보를 쉽게 액세스 하 고, 선택 된 타사 응용 프로그램을 통해 해당 임상 정보에 정의 된 하위 집합을 포함할 수  

* **공급자 디렉터리 API (7 월 1 2021 일 필수)** – CMS 규정 payers는 표준 기반 API를 통해 공급자 디렉터리 정보를 공개적으로 사용할 수 있도록 하는 규칙의이 부분에 필요 합니다. 타사 응용 프로그램 개발자는이 정보를 사용할 수 있도록 하 여 특정 주의 요구에 대 한 공급자를 찾기 환자 하는 데 도움이 되는 서비스를 만들 수 있습니다.  

* **지불자 지불자 data Exchange (2022 1 월 1 일에 필수)** – 다른 payers를 사용 하 여 환자 요청에서 특정 환자 임상 데이터를 교환 하려면 CMS 규정 payers가 필요 합니다. 모든 종류의 표준을 준수 해야 하는 것은 아니지만이 데이터를 교환 하는 데 FHIR을 적용 하는 것이 좋습니다. 

## <a name="key-fhir-concepts"></a>핵심 개념

위에서 설명한 것 처럼이 요구 사항을 충족 하려면 FHIR 4가 필요 합니다. 또한 규칙에 대 한 지침을 제공 하는 몇 가지 구현 가이드를 개발 했습니다. [구현 가이드](https://www.hl7.org/fhir/implementationguide.html) 는 기본 FHIR 사양 위에 추가 컨텍스트를 제공 합니다. 여기에는 추가 검색 매개 변수, 프로필, 확장, 작업, 값 집합 및 코드 시스템을 정의 하는 작업이 포함 됩니다.

FHIR 용 Azure API는 다양 한 구현 가이드를 위해 데이터베이스를 구성 하는 데 도움이 되는 다음과 같은 기능을 제공 합니다.

* [RESTful 상호 작용에 대 한 지원](fhir-features-supported.md)
* [프로필 저장 및 유효성 검사](validation-against-profiles.md)
* [사용자 지정 검색 매개 변수 정의 및 인덱싱](how-to-do-custom-search.md)
* [데이터 변환](convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>환자 액세스 API 구현 가이드

환자 액세스 API는 4 개의 FHIR 구현 가이드에 대 한 준수를 설명 합니다.

* Payers [For Blue button®](http://hl7.org/fhir/us/carin-bb/STU1/index.html): 환자의 클레임을 만들고 C4BB Ig (Blue Button 구현 가이드)의 carin에 따라 데이터를 제공 해야 합니다. C4BB IG는 payers가 FHIR API를 통해 소비자에 게 표시할 수 있는 리소스 집합을 제공 하 고 상호 운용성 및 환자 액세스 API의 클레임 데이터에 필요한 세부 정보를 포함 합니다. 이 구현 가이드에서는 기본 리소스로 ExplanationOfBenefit (EOB) 리소스를 사용 하 여 참조 되는 다른 리소스를 풀링 합니다.
* [HL7 fhir Da 다빈치 PDex](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)(지불자 Data Exchange 구현 가이드): payers에서 환자 액세스 API에 대 한 요구 사항을 충족 하기 위해 모든 관련 환자 임상 데이터를 제공 하는지 확인 하는 데 중점을 두었습니다. 이는 4 번째 리소스에 US 코어 프로필을 사용 하 고 최소한의 발생, 공급자, 조직, 위치, 서비스 날짜, 진단, 절차 및 관찰을 포함 합니다. 이 데이터는 FHIR 형식으로 사용할 수 있지만 클레임 데이터, HL7 V2 메시지 및 C CDA 문서 형식의 다른 시스템 에서도 가져올 수 있습니다.
* 미국 코어 [HL7](https://www.hl7.org/fhir/us/core/toc.html): HL7 Us 핵심 구현 가이드 (미국 코어)는 위에서 설명한 PDex IG의 백본입니다. PDex IG는 미국 코어를 초과 하는 경우를 비롯 하 여 일부 리소스를 제한 하지만 많은 리소스가 미국 코어의 표준에 따릅니다.

* [HL7 FHIR Da 다빈치-PDEX US 약품 FORMULARY IG](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html): 파트 D Medicare의 이점 요금제는 환자 API를 통해 Formulary 정보를 사용할 수 있도록 해야 합니다. 이러한 작업은 USDF (PDex US Formulary 구현 가이드)를 사용 하 여 수행 합니다. USDF는 health insurer의 마약 formulary 정보에 대 한 FHIR 인터페이스를 정의 합니다 .이 정보는 health insurer에서 지불에 동의한 브랜드 이름 및 일반 prescription 마약 목록입니다. 이에 대 한 주요 사용 사례는 환자에서 해당 하는 대체 약품을 사용할 수 있는지 여부를 이해 하 고 마약 비용을 비교 하는 것입니다.

## <a name="provider-directory-api-implementation-guide"></a>공급자 디렉터리 API 구현 가이드

공급자 디렉터리 API는 다음과 같은 구현 가이드에 대 한 준수를 설명 합니다.

* [HL7 Da 다빈치 PDex 계획 네트워크](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)성능:이 구현 가이드는 상태 insurer의 보험 계획, 연결 된 네트워크 및 이러한 네트워크에 참여 하는 조직 및 공급자에 대 한 FHIR 인터페이스를 정의 합니다.

## <a name="touchstone"></a>Touchstone

다양 한 구현 가이드에 대 한 준수를 테스트 하기 위해 [Touchstone](https://touchstone.aegis.net/touchstone/) 는 훌륭한 리소스입니다. 예정 된 자습서 전체에서 다양 한 Touchstone 테스트를 성공적으로 통과 하도록 FHIR 용 Azure API가 구성 되었는지 확인 하는 데 초점을 둡니다. Touchstone 사이트는 준비 하 고 실행 하는 데 도움이 되는 많은 유용한 설명서를 제공 합니다.

## <a name="next-steps"></a>다음 단계

이제 상호 운용성 및 환자 액세스 규칙, 구현 가이드 및 사용 가능한 테스트 도구 (Touchstone)에 대 한 기본적인 이해를 했으므로 파랑 용 CARIN에 대 한 Azure API를 설정 하는 과정을 안내 합니다. 

>[!div class="nextstepaction"]
>[파란색 단추에 대한 CARIN 구현 가이드](carin-implementation-guide-blue-button-tutorial.md)  