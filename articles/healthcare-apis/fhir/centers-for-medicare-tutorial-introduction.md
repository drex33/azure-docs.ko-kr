---
title: 자습서 - CmS(히어 및 히어 서비스) 소개 센터 - FHIR 서비스
description: CMS(Center for Toaid Services) 상호 운용성 및 환자 액세스 규칙과 관련된 일련의 자습서를 소개합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/05/2021
ms.openlocfilehash: 0f1c14976aae6b0335d968a2967e48b89b997f28
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778340"
---
# <a name="introduction-centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule"></a>소개: CMS(율리치 센터 및 퇴사 서비스) 상호 운용성 및 환자 액세스 규칙

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서 시리즈에서는 CMS(Center for The Center for Edaid Services) 상호 운용성 및 환자 액세스 규칙과 이 규칙에 설명된 기술 요구 사항에 대한 간략한 요약을 다룹니다. 이 규칙에 대해 참조된 다양한 구현 가이드를 살펴보겠습니다. 또한 이러한 구현 가이드를 지원하도록 Azure Healthcare API(여기서는 FHIR 서비스라고 함)에서 FHIR 서비스를 구성하는 방법에 대한 세부 정보도 제공합니다.


## <a name="rule-overview"></a>규칙 개요

CMS는 2020년 5월 1일에 [상호 운용성 및 환자 액세스 규칙을](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) 릴리스했습니다. 이 규칙은 환자 치료(환자, 공급자 및 지불자)와 관련된 모든 당사자 간에 무료이고 안전한 데이터 흐름을 요구하여 환자가 필요할 때 건강 정보에 액세스할 수 있도록 합니다. 상호 운용성은 수십 년 동안 의료 업계를 혁신해 왔으며, 이로 인해 부정적인 의료 결과를 초래하는 사일로화된 데이터가 발생했으며, 이로 인해 의료 비용이 더 높고 예측할 수 없습니다. CMS는 이 규칙을 적용하기 위해 해당 기관을 사용하여 FFE(Federally Facilitated Exchange)에서 MA(쿼리 이점), Toaid, CHIP(Children's Health Insurance Program) 및 QHP(정규 의료 플랜) 발급자를 규제하고 있습니다. 

2020년 8월에 CMS는 조직이 위임을 충족하는 방법을 자세히 설명했습니다. 데이터를 표준화된 방식으로 안전하게 교환할 수 있도록 하기 위해 CMS는 FHIR 버전 릴리스 4(R4)를 데이터 교환에 필요한 기본 표준으로 식별했습니다. 

상호 운용성 및 환자 액세스 에 대한 세 가지 주요 부분이 있습니다.

* **환자 액세스 API(2021년 7월 1일 필요)** – CMS 규제 지급자는(위에서 정의한 대로) 환자가 자신의 클레임에 쉽게 액세스하고 비용을 포함한 정보와 선택한 타사 애플리케이션을 통해 임상 정보의 정의된 하위 집합을 확인할 수 있는 안전한 표준 기반 API를 구현하고 유지 관리해야 합니다.  

* **공급자 디렉터리 API(2021년 7월 1일 필요)** – CMS 규제 지불자는 표준 기반 API를 통해 공급자 디렉터리 정보를 공개적으로 사용할 수 있도록 규칙의 이 부분에 필요합니다. 이 정보를 사용할 수 있게 함으로써 타사 애플리케이션 개발자는 환자가 특정 의료 요구 사항에 대한 공급자를 찾고 임상의가 의료 조정을 위해 다른 공급자를 찾는 데 도움이 되는 서비스를 만들 수 있습니다.  

* **종량제 데이터 Exchange(2022년 1월 1일 필요)** – CMS 규제 지불자는 환자의 요청에 따라 특정 환자 임상 데이터를 다른 지급자와 교환해야 합니다. 어떤 종류의 표준을 따라야 할 필요는 없지만 FHIR을 적용하여 이 데이터를 교환하는 것이 좋습니다. 

## <a name="key-fhir-concepts"></a>주요 FHIR 개념

위에서 설명한 대로 FHIR R4는 이 위임을 충족해야 합니다. 또한 규칙에 대한 지침을 제공하는 몇 가지 구현 가이드가 개발되었습니다. [구현 가이드는](https://www.hl7.org/fhir/implementationguide.html) 기본 FHIR 사양을 기반으로 추가 컨텍스트를 제공합니다. 여기에는 추가 검색 매개 변수, 프로필, 확장, 작업, 값 집합 및 코드 시스템 정의가 포함됩니다.

FHIR 서비스에는 다양한 구현 가이드에 맞게 데이터베이스를 구성하는 데 도움이 되는 다음과 같은 기능이 있습니다.

* [RESTful 상호 작용 지원](fhir-features-supported.md)
* [프로필 저장 및 유효성 검사](validation-against-profiles.md)
* [사용자 지정 검색 매개 변수 정의 및 인덱싱](how-to-do-custom-search.md)
* [데이터 변환](../data-transformation/convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>환자 액세스 API 구현 가이드

환자 액세스 API는 다음 네 가지 FHIR 구현 가이드의 준수를 설명합니다.

* [CARIN IG for Blue Button®:](http://hl7.org/fhir/us/carin-bb/STU1/index.html)지급자는 환자의 클레임을 만드는 데 필요하며 C4BB IG(Blue Button Implementation Guide)용 CARIN IG에 따라 데이터를 사용할 수 있습니다. C4BB IG는 FHIR API를 통해 지불자가 소비자에게 표시할 수 있는 리소스 집합을 제공하며 상호 운용성 및 환자 액세스 API의 클레임 데이터에 필요한 세부 정보를 포함합니다. 이 구현 가이드에서는 EOB(ExplanationOfBenefit) 리소스를 주 리소스로 사용하여 참조되는 다른 리소스를 끌어오고 있습니다.
* [HL7 FHIR Da Vinci PDex IG:](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)PDex IG(Payer Data Exchange Implementation Guide)는 지급자가 환자 액세스 API에 대한 요구 사항을 충족하기 위해 모든 관련 환자 임상 데이터를 제공하도록 하는 데 중점을 두고 있습니다. R4 리소스에서 US Core 프로필을 사용하며 최소한 발생, 공급자, 조직, 위치, 서비스 날짜, 진단, 절차 및 관찰을 포함합니다. 이 데이터는 FHIR 형식으로 사용할 수 있지만 클레임 데이터, HL7 V2 메시지 및 C-CDA 문서 형식의 다른 시스템에서도 제공될 수 있습니다.
* [HL7 US Core IG:](https://www.hl7.org/fhir/us/core/toc.html)HL7 US Core 구현 가이드(US Core IG)는 위에서 설명한 PDex IG의 백본입니다. PDex IG는 US Core IG보다 더 많은 리소스를 제한하지만 많은 리소스가 US Core IG의 표준을 따릅니다.

* [HL7 FHIR Da Vinci - PDex US 약품 수식 IG:](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html)1부 D -Advantage 계획은 환자 API를 통해 수식 정보를 사용할 수 있도록 해야 합니다. PDex US 약품 수식 구현 가이드(USDF IG)를 사용하여 이 작업을 수행합니다. USDF IG는 건강 보험 회사의 약품 수식 정보에 대한 FHIR 인터페이스를 정의합니다. 이 정보는 건강 보험업체가 지불하는 데 동의한 브랜드 이름 및 일반 약품 목록입니다. 이 경우의 주요 사용 사례는 환자가 약품에 대해 사용 가능한 대체 약품이 있는지 파악하고 약 비용을 비교할 수 있도록 하는 것입니다.

## <a name="provider-directory-api-implementation-guide"></a>공급자 디렉터리 API 구현 가이드

공급자 디렉터리 API는 한 가지 구현 가이드의 준수를 설명합니다.

* [HL7 Da Vinci PDex Plan Network IG:](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)이 구현 가이드에서는 의료 보험 플랜, 관련 네트워크 및 이러한 네트워크에 참여하는 조직 및 공급자에 대한 FHIR 인터페이스를 정의합니다.

## <a name="touchstone"></a>시금석

다양한 구현 가이드의 준수를 테스트하기 위해 [Touchstone은](https://touchstone.aegis.net/touchstone/) 훌륭한 리소스입니다. 예정된 자습서 전체에서 FHIR 서비스가 다양한 Touchstone 테스트를 성공적으로 통과하도록 구성되었는지 확인하는 데 집중합니다. Touchstone 사이트에는 시작하고 실행하는 데 도움이 되는 많은 훌륭한 설명서가 있습니다.

## <a name="next-steps"></a>다음 단계

이제 상호 운용성 및 환자 액세스 규칙, 구현 가이드 및 사용 가능한 테스트 도구(Touchstone)를 기본적으로 이해했으므로 Blue Button용 CARIN IG에 대한 FHIR 서비스를 설정하는 것을 살펴보겠습니다. 

>[!div class="nextstepaction"]
>[파란색 단추에 대한 CARIN 구현 가이드](carin-implementation-guide-blue-button-tutorial.md)  
