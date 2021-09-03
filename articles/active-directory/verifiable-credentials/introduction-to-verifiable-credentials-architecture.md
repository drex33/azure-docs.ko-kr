---
title: Azure Active Directory 아키텍처 개요(미리 보기)
description: 솔루션을 계획하고 디자인하는 기본 정보 알아보기
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.openlocfilehash: 45dd0be664474b22a24354903233e5729479f095
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535507"
---
# <a name="azure-ad-verifiable-credentials-architecture-overview-preview"></a>Azure AD 확인 가능한 자격 증명 아키텍처 개요(미리 보기)

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

자격 증명을 발급 및/또는 검증하는 것 외에도 솔루션이 아키텍처와 비즈니스에 미치는 영향을 완전히 볼 수 있도록 확인 가능한 자격 증명 솔루션을 계획하는 것이 중요합니다. 아직 검토하지 않았다면 [Azure Active Directory 확인 가능한 자격 증명 소개](decentralized-identifier-overview.md) 및 [FAQ](verifiable-credentials-faq.md)를 검토한 다음 [시작하기](get-started-verifiable-credentials.md) 자습서를 완료하는 것이 좋습니다. 

이 아키텍처 개요에서는 Azure Active Directory 확인 가능한 자격 증명 서비스의 기능과 구성 요소를 소개합니다. 발급 및 유효성 검사에 대한 자세한 내용은 다음을 참조하세요. 

* [발행 솔루션 계획](plan-issuance-solution.md)

* [확인 솔루션 계획](plan-verification-solution.md)

## <a name="approaches-to-identity"></a>ID 접근 방법

오늘날 대부분의 조직에서는 중앙 집중식 ID 시스템을 사용하여 직원 자격 증명을 제공합니다. 또한 다양한 방법을 사용하여 고객, 파트너, 공급업체 및 신뢰 당사자를 조직의 신뢰 경계로 가져옵니다. 해당 방법에는 페더레이션, Azure AD B2B와 같은 시스템을 사용하여 게스트 계정 만들기 및 관리, 신뢰 당사자와의 명시적 신뢰 생성이 포함됩니다. 대부분의 비즈니스 관계에는 디지털 구성 요소가 있으므로 조직 간에 특정 형태의 신뢰를 사용하려면 상당한 노력이 필요합니다. 

### <a name="centralized-identity-systems"></a>중앙 집중식 ID 시스템

중앙 집중식 접근 방식은 애플리케이션, 서비스, 디바이스가 도메인 또는 신뢰 경계 내에서 사용되는 신뢰 메커니즘에 의존하는 경우와 같이 대부분의 경우 여전히 제대로 작동합니다. 

중앙 집중식 ID 시스템에서 IDP(ID 공급자)가 자격 증명의 수명 주기와 사용을 제어합니다. 

![중앙 집중식 ID 시스템의 예](./media/introduction-to-verifiable-credentials-architecture/centralized-identity-architecture.png)


그러나 확인 가능한 자격 증명이 있는 탈중앙화 아키텍처를 사용하면 다음과 같은 주요 시나리오를 보강하여 가치를 제공할 수 있는 시나리오가 있습니다.

* 원격 시나리오를 포함하여 직원 ID와 다른 ID를 안전하게 온보딩.

* 특정 조건에 따라 조직 신뢰 경계 내의 리소스에 액세스.

* 조직에서 발급한 이식 가능 자격 증명을 사용하여 파트너 리소스에 액세스하는 등 신뢰 경계 외부의 리소스에 액세스.

 

### <a name="decentralized-identity-systems"></a>탈중앙화 ID 시스템

탈중앙화 ID 시스템에서 자격 증명의 수명 주기와 사용 제어는 자격 증명을 사용하는 발급자, 보유자, 신뢰 당사자 간에 공유됩니다.

전자상거래 웹 사이트인 Proseware에서 Woodgrove 직원에게 회사 할인을 제공하려고 하는 아래 다이어그램의 시나리오를 고려하세요. 

 ![탈중앙화 ID 시스템의 예](media/introduction-to-verifiable-credentials-architecture/decentralized-architecture.png)


VC(확인 가능한 자격 증명)에 관해 익숙지 않은 경우 VC라는 용어가 혼동될 수 있습니다. 다음 정의는 [확인 가능한 자격 증명 데이터 모델 1.0](https://www.w3.org/TR/vc-data-model/) 용어 섹션에서 가져온 것입니다. 각 용어를 정의한 다음에 위 다이어그램에 있는 엔터티와 연관 지어 설명합니다.

 “발급자는 엔터티가 하나 이상의 주제에 대한 클레임을 주장하고, 해당 클레임에서 확인 가능한 자격 증명을 만들고, 보유자에게 확인 가능한 자격 증명을 전송하여 수행할 수 있는 역할입니다.”

* 위의 다이어그램에서 Woodgrove는 직원에게 확인 가능한 자격 증명을 발급하는 발급자입니다. 

 “보유자는 엔터티가 확인 가능한 자격 증명을 하나 이상 소유하고 이러한 자격 증명에서 프레젠테이션을 생성하여 수행할 수도 있는 역할입니다. 항상 그렇지는 않지만 주로 보유자는 소유하고 있는 확인 가능한 자격 증명의 주체입니다. 보유자는 자격 증명 리포지토리에 자격 증명을 저장합니다.” 

* 위의 다이어그램에서 Alice는 Woodgrove 직원입니다. Woodgrove 발급자로부터 확인 가능한 자격 증명을 획득했으며 해당 자격 증명의 보유자입니다.

 “검증자는 엔터티가 선택적으로 처리를 위해 확인 가능한 프레젠테이션 내부에서 하나 이상의 확인 가능한 자격 증명을 수신하여 수행하는 역할입니다. 다른 사양에서는 이 개념을 신뢰 당사자로 나타낼 수도 있습니다.”

* 위의 다이어그램에서 Proseware는 Woodgrove에서 발급한 자격 증명의 검증자입니다. 

“자격 증명은 발급자가 제출한 하나 이상의 클레임 세트입니다. 확인 가능한 자격 증명은 암호로 검증할 수 있는 저작권이 있는 변조 방지 자격 증명입니다. 확인 가능한 자격 증명은 확인 가능한 프레젠테이션을 작성하는 데 사용할 수 있습니다. 이 프레젠테이션도 암호로 검증할 수 있습니다. 자격 증명의 클레임은 다른 주체에 대한 정보일 수 있습니다.”

 “탈중앙화 ID는 엔터티와 연결된 DID라고도 하는 이식 가능한 URI 기반 식별자입니다. 해당 식별자는 확인 가능한 자격 증명에 대개 사용되며 주체, 발급자, 검증자와 연결됩니다.”

* 위의 다이어그램에서 행위자 DID의 퍼블릭 키는 탈중앙화 ID 문서의 ION(탈중앙화 원장)에 저장된 것으로 표시됩니다.

 “‘탈중앙화 ID 문서’(‘DID 문서’라고도 함)는 확인 가능한 데이터 레지스트리를 사용하여 액세스할 수 있고 연관된 리포지토리, 퍼블릭 키 정보 등의 특정 탈중앙화 ID에 관련된 정보를 포함하는 문서입니다.” 

* 위의 시나리오에서 발급자와 검증자 모두에 DID와 DID 문서가 있습니다. DID 문서에는 퍼블릭 키 및 DID와 연결된 DNS 웹 도메인(연결된 도메인이라고도 함) 목록이 포함되어 있습니다.

* Woodgrove(발급자)는 퍼블릭 키로 직원의 VC에 서명합니다. 마찬가지로 Proseware(검증자)는 DID와도 연결된 키를 사용하여 VC를 표시하라는 요청에 서명합니다.

 “분산 원장은 이벤트를 기록하는 데 사용하는 탈중앙화된 시스템입니다. 해당 시스템은 참가자가 운영 결정을 내리는 데 다른 사용자가 기록한 데이터에 의존할 수 있도록 충분한 신뢰를 구축합니다. 일반적으로 서로 다른 노드에서 합의 프로토콜을 사용하여 암호화 방식으로 서명된 트랜잭션의 순서를 확인하는 분산 데이터베이스를 사용합니다. 시간이 지남에 따라 디지털 방식으로 서명된 트랜잭션을 연결하면 원장의 기록이 효과적으로 변경되지 않는 경우가 많습니다.”

* Microsoft 솔루션에서는 ION(ID 오버레이 네트워크)을 사용하여 탈중앙화 PKI(퍼블릭 키 인프라) 기능을 제공합니다.

 

### <a name="combining-centralized-and-decentralized-identity-architectures"></a>중앙 집중식 ID 아키텍처와 탈중앙화 ID 아키텍처 결합

확인 가능한 자격 증명 솔루션을 검토할 때 탈중앙화 ID 아키텍처를 중앙 집중식 ID 아키텍처와 결합하여 위험을 줄이며 상당한 운영상의 이점이 있는 솔루션을 제공하는 방법을 이해하면 도움이 됩니다.

## <a name="the-user-journey"></a>사용자 경험

이 아키텍처 개요에서는 조직에 지원하고 고용을 수락하는 구직자와 직원의 경험을 살펴봅니다. 그런 다음, 확인 가능한 자격 증명이 중앙 집중식 프로세스를 강화할 수 있는 변경 내용을 통해 직원과 조직을 살펴봅니다.

 

### <a name="actors-in-these-use-cases"></a>사용 사례의 행위자

* **Alice** 는 Woodgrove, Inc.에 입사 지원하여 고용 제안을 수락하는 사람입니다.

* **Woodgrove**, Inc는 가상의 회사입니다.

* **Adatum** 은 Woodgrove의 가상 ID 검증 파트너입니다.

* **Proseware** 는 Woodgrove의 가상 파트너 조직입니다.

Woodgrove에서는 중앙 집중식 ID 아키텍처와 탈중앙화 ID 아키텍처를 모두 사용합니다.

### <a name="steps-in-the-user-journey"></a>사용자 경험의 단계

* Alice는 Woodgrove, Inc.에 입사 지원하고 고용 제안을 수락한 후에 온보딩을 거칩니다.

* Woodgrove의 신뢰 경계 내에 있는 디지털 리소스에 액세스합니다.

* Woodgrove 또는 파트너의 신뢰 경계를 확장하지 않고 Woodgrove의 신뢰 경계 외부에 있는 디지털 리소스에 액세스합니다.

Woodgrove는 계속 비즈니스를 운영하므로 ID를 지속적으로 관리해야 합니다. 이 참고 자료의 사용 사례는 Alice가 셀프 서비스 기능을 사용하여 ID를 얻고 유지 관리하는 방법과 Woodgrove가 다양한 신뢰 요구 사항이 있는 B2B 관계를 추가, 수정, 종료할 수 있는 방법을 설명합니다. 

해당 사용 사례는 중앙 집중식 ID와 탈중앙화 ID를 결합하여 더 강력하고 효율적인 ID와 신뢰 전략 및 수명 주기를 제공할 수 있는 방법을 보여 줍니다. 


## <a name="user-journey-onboarding-to-woodgrove"></a>사용자 경험: Woodgrove에 온보딩

![Woodgrove에 온보딩하는 사용자 경험](media/introduction-to-verifiable-credentials-architecture/onboarding-journey.png)

 **인식**: Alice는 Woodgrove, Inc.에 입사하려고 Woodgrove의 채용 웹 사이트를 방문합니다. 

**활성화**: Woodgrove 사이트에서는 Alice에게 신뢰할 수 있는 신원 확인 파트너인 Adatum을 방문하도록 QR 코드 또는 딥 링크를 표시하여 신원을 증명하는 방법을 제공합니다.

**요청 및 업로드**: Adatum은 Alice에게 신원 증명을 요청합니다. Alice는 셀카 사진과 면허증 사진을 찍어 Adatum에 업로드합니다. 

**발급**: Adatum은 Alice의 신원을 확인한 후에 Alice에게 신원을 증명하는 VC(확인 가능한 자격 증명)를 발급합니다.

**프레젠테이션**: Alice(자격 증명의 보유자이자 주체)는 Woodgrove 채용 포털에 액세스하여 지원 프로세스를 완료할 수 있습니다. Alice가 VC를 사용하여 포털에 액세스하면 Woodgrove는 Adatum의 증명을 신뢰하는 검증자 및 신뢰 당사자의 역할을 수행합니다.


### <a name="distributing-initial-credentials"></a>초기 자격 증명 배포

Alice는 Woodgrove의 고용 제안을 수락합니다. 온보딩 프로세스의 일부로 Alice가 Woodgrove 신뢰 경계 내에서 사용할 수 있는 Azure AD(Active Directory) 계정이 생성됩니다. Alice의 관리자는 원격으로 근무하는 Alice가 안전한 방법으로 초기 로그인 정보를 수신할 수 있는 방법을 찾아야 합니다. 과거에는 IT 부서에서 해당 자격 증명을 관리자에게 제공하여 관리자가 이를 인쇄하여 Alice에게 전달했었을 수 있습니다. 이 방식은 원격 직원에게는 적용되지 않습니다.

VC는 자격 증명 배포 프로세스를 강화하여 중앙 집중식 시스템에 가치를 더할 수 있습니다. 관리자가 자격 증명을 제공할 필요 없이 Alice는 VC를 신원 증명으로 사용하여 중앙 집중식 시스템 액세스를 위한 초기 사용자 이름과 자격 증명을 받을 수 있습니다. Alice는 온보딩 프로세스의 일부로 자신의 전자지갑에 추가된 ID 증명을 표시합니다. 

 

온보딩 사용 사례에서 신뢰 관계 역할은 발급자, 검증자, 보유자 간에 분산됩니다. 

* 발급자는 발급하는 VC의 일부인 클레임의 유효성을 검사해야 합니다. Adatum은 VC를 발급하기 위해 Alice ID의 유효성을 검사합니다. 이 경우 검증자나 신뢰 당사자를 고려하지 않고 VC가 발급됩니다.

* 보유자는 VC를 소유하고 확인을 위해 VC 제시를 시작합니다. Alice만 보유하고 있는 VC를 제시할 수 있습니다.

* 검증자는 확인 가능한 자격 증명 데이터 모델에 설명된 탈중앙화 원장 기능을 사용하여 신뢰하는 발급자의 VC에 있는 클레임을 수락하고 VC의 유효성을 검사합니다. Woodgrove는 Alice의 ID에 대한 Adatum의 클레임을 신뢰합니다.

 

온보딩을 위해 중앙 집중식 ID 아키텍처와 탈중앙화 ID 아키텍처를 결합함으로써, 정부 ID 번호와 같이 ID 검증에 필요한 Alice에 대한 권한 정보는 Woodgrove가 저장하지 않아도 됩니다. ID 확인 파트너(Adatum)가 발급한 Alice의 VC가 자신의 신원을 확인할 수 있다고 믿기 때문입니다. 작업 중복을 최소화하고 초기 온보딩 작업에 대한 프로그래밍 방식과 예측 가능한 접근 방식을 구현할 수 있습니다. 

 

## <a name="user-journey-accessing-resources-inside-the-trust-boundary"></a>사용자 경험: 신뢰 경계 내의 리소스에 액세스

![신뢰 경계 내의 리소스에 액세스](media/introduction-to-verifiable-credentials-architecture/inside-trust-boundary.png)

직원인 Alice는 Woodgrove의 신뢰 경계 내에서 작업합니다. Woodgrove는 IDP(ID 공급자) 역할을 하며 Alice가 Woodgrove 신뢰 경계 내에서 상호 작용하는 데 사용하는 앱의 ID 및 구성을 완전히 제어합니다. Azure AD 신뢰 경계에서 리소스를 사용하기 위해 Alice는 잠재적으로 여러 형식의 식별 증명을 제공하여 Woodgrove의 신뢰 경계에 로그온하고 Woodgrove 기술 환경 내의 리소스에 액세스합니다. 중앙 집중식 ID 아키텍처를 사용하여 올바르게 제공되는 일반적인 시나리오입니다. 

* Woodgrove는 신뢰 경계를 관리하고 우수한 보안 방법을 사용하여 수행된 작업을 기반으로 Alice에 대한 최소 수준의 액세스 권한을 제공합니다. 강력한 보안 상태를 유지하고 잠재적으로 규정 준수를 위해 Woodgrove는 직원의 권한 및 리소스 액세스를 추적할 수 있어야 하며 고용이 종료될 때 권한을 취소할 수 있어야 합니다.

* Alice는 Woodgrove가 유지 관리하는 자격 증명만 사용하여 Woodgrove 리소스에 액세스합니다. 자격 증명이 Woodgrove에서 관리되고 Woodgrove 리소스에서만 사용되므로 Alice는 자격 증명이 사용되는 시기를 추적할 필요가 없습니다. ID는 Woodgrove 리소스에 대한 액세스가 필요한 경우에만 Woodgrove 트러스트 경계 내에서만 유효하므로 Alice는 자격 증명을 소유할 필요가 없습니다. 

### <a name="using-vcs-inside-the-trust-boundary"></a>신뢰 경계 내에서 VC 사용

개별 직원은 ID 요구 사항을 변경하고, VC는 중앙 집중식 시스템을 강화하여 해당 변경 내용을 관리할 수 있습니다. 

* Woodgrove에 고용된 동안 Alice는 특정 요구 사항을 충족하기 위해 리소스에 대한 추가 액세스 권한이 필요할 수 있습니다. 예를 들어 Alice가 개인 정보 보호 교육을 완료하면 해당 클레임이 포함된 새 직원 VC가 발급될 수 있으며 해당 VC는 제한된 리소스에 액세스하는 데 사용할 수 있습니다.

* 계정 복구를 위해 신뢰 경계 내에서 VC를 사용할 수 있습니다. 예를 들어 직원이 휴대폰과 컴퓨터를 분실한 경우 Woodgrove가 신뢰하는 ID 검증 서비스에서 새 VC를 얻어 액세스 권한을 다시 얻은 다음, VC를 사용하여 새 자격 증명을 얻을 수 있습니다. 

 ## <a name="user-journey-accessing-external-resources"></a>사용자 경험: 외부 리소스에 액세스

Woodgrove는 Proseware와 제품 구매 할인을 협상합니다. 모든 Woodgrove 직원은 할인을 받을 수 있습니다. Woodgrove는 Alice에게 Proseware의 웹 사이트에 액세스하고 구매한 제품에 대한 할인을 받을 수 있는 방법을 제공하려고 합니다. Woodgrove가 중앙 집중식 ID 아키텍처를 사용하는 경우 Alice에게 할인을 제공하는 두 가지 접근 방식이 있습니다. 

* Alice는 Proseware를 통해 계정을 만들기 위해 개인 정보를 제공할 수 있으며, Proseware는 Alice가 Woodgrove에 고용되었는지 확인해야 합니다.

* Woodgrove는 신뢰 당사자로 Proseware를 포함하도록 신뢰 경계를 확장할 수 있으며 Alice는 확장된 신뢰 경계를 사용하여 할인을 받을 수 있습니다. 

Woodgrove는 탈중앙화 ID를 사용하여 Alice가 Proseware의 웹 사이트 및 기타 외부 리소스에 액세스하는 데 사용할 수 있는 VC(확인 가능한 자격 증명)를 Alice에게 제공할 수 있습니다. 

![신뢰 경계 외부의 리소스에 액세스](media/introduction-to-verifiable-credentials-architecture/external-resources.png)

 

Woodgrove는 Alice에게 VC를 제공하여 Alice가 직원임을 증명합니다. Woodgrove는 Proseware의 유효성 검사 솔루션에서 신뢰할 수 있는 VC 발급자입니다. Woodgrove의 발급 프로세스에 대한 이 신뢰 덕분에 Proseware는 Alice가 Woodgrove 직원이라는 증거로 VC를 전자적으로 수락하고 Alice에게 할인을 제공할 수 있습니다. Proseware는 Alice가 제공하는 VC 유효성 검사의 일부로 분산 원장을 사용하여 VC의 유효성을 검사합니다. 이 솔루션의 내용은 다음과 같습니다.

* Woodgrove를 사용하면 Woodgrove가 신뢰 경계를 확장할 필요 없이 Alice가 Proseware 고용 증명을 제공할 수 있습니다. 

* Proseware는 Alice가 Woodgrove의 직원인지 확인하기 위해 신뢰 경계를 확장할 필요가 없습니다. Proseware는 Woodgrove에서 제공하는 VC를 대신 사용할 수 있습니다. 신뢰 경계는 확장되지 않으므로 신뢰 관계를 관리하기가 쉬워지며 Proseware는 VC를 더 이상 허용하지 않아 관계를 쉽게 종료할 수 있습니다.

* Alice는 메일과 같은 Proseware 개인 정보를 제공할 필요가 없습니다. Alice는 개인 디바이스의 전자지갑 애플리케이션에서 VC를 유지 관리합니다. VC를 사용할 수 있는 유일한 사용자는 Alice이며 Alice가 자격 증명 사용을 시작해야 합니다. VC의 각 사용량은 전자지갑 애플리케이션에서 기록하므로 Alice는 VC가 사용되는 시기와 위치에 대한 레코드를 보유하고 있습니다. 

 

신뢰 경계 내부 및 외부에서 작동하기 위해 중앙 집중식 ID 아키텍처와 탈중앙화 ID 아키텍처를 결합하면 복잡성과 위험을 줄이고 제한된 관계를 쉽게 관리할 수 있습니다. 

### <a name="changes-over-time"></a>시간에 따른 변경 내용

Woodgrove는 다른 조직과의 비즈니스 관계를 추가하고 종료하며 중앙 집중식 및 탈중앙화 ID 아키텍처가 사용되는 시기를 결정해야 합니다.

중앙 집중식 ID 아키텍처와 탈중앙화 ID 아키텍처를 결합하면 ID 및 ID 증명과 관련된 책임과 노력이 분산되고, 위험이 감소하며, 사용자는 개인 정보를 알 수 없는 검증자에게 또는 자주 릴리스할 위험이 없습니다. 특히 다음에 대해 주의하세요.

* 중앙 집중식 ID 아키텍처에서 IDP는 자격 증명을 발급하고 발급된 자격 증명의 확인을 수행합니다. 모든 ID에 대한 정보는 IDP에서 처리하여 디렉토리에 저장하거나 디렉토리에서 검색합니다. IDP는 소셜 로그인 또는 비즈니스 파트너와 같은 다른 IDP 시스템의 보안 토큰을 동적으로 수락할 수도 있습니다. 신뢰 당사자가 IDP 신뢰 경계에서 ID를 사용하려면 IDP에서 발급한 토큰을 수락하도록 구성해야 합니다.

## <a name="how-decentralized-identity-systems-work"></a>탈중앙화 ID 시스템의 작동 방식

탈중앙화 ID 아키텍처에서 발급자, 사용자 및 RP(신뢰 당사자)는 각각 서로의 자격 증명을 지속적으로 신뢰할 수 있는 교환을 설정하고 보장하는 역할을 합니다. 행위자 DID의 퍼블릭 키는 ION에서 확인할 수 있으므로 서명 유효성 검사를 허용합니다. 따라서 확인 가능한 자격 증명을 비롯한 아티팩트를 신뢰할 수 있습니다. 신뢰 당사자는 발급자와 신뢰 관계를 설정하지 않고도 확인 가능한 자격 증명을 사용할 수 있습니다. 대신 발급자는 신뢰 당사자에게 증명으로 제시할 자격 증명을 주체에 제공합니다. 행위자 간 모든 메시지는 행위자의 DID로 서명됩니다. 발급자 및 검증자의 DID도 요청을 생성한 DNS 도메인을 소유해야 합니다. 

예: VC 보유자가 리소스에 액세스해야 하는 경우 해당 신뢰 당사자에게 VC를 제공해야 합니다. 이렇게 하려면 전자지갑 애플리케이션을 사용하여 VC를 제공하는 RP의 요청을 읽습니다. 요청을 읽는 과정에서 전자지갑 애플리케이션은 ION을 사용하여 RP의 퍼블릭 키를 찾는 데 RP의 DID를 사용하며, VC를 제시하라는 요청이 변조되지 않았는지 확인합니다. 이 전자지갑은 DID가 RP의 DNS 도메인에서 호스팅되는 메타데이터 문서에서 참조되어 도메인 소유권을 증명하는지도 확인합니다. 

 

![탈중앙화 ID 시스템의 작동 방식](media/introduction-to-verifiable-credentials-architecture/how-decentralized-works.png)

### <a name="flow-1-verifiable-credential-issuance"></a>흐름 1: 확인 가능한 자격 증명 발급

이 흐름에서 자격 증명 보유자는 발급자와 상호 작용하여 다음 다이어그램에 설명된 대로 확인 가능한 자격 증명을 요청합니다.

![확인 가능한 자격 증명 발급](media/introduction-to-verifiable-credentials-architecture/issuance.png)

1. 보유자는 브라우저나 기본 애플리케이션을 통해 발급자의 웹 프런트 엔드에 액세스하여 흐름을 시작합니다. 이 경우 발급자 웹 사이트는 사용자가 데이터를 수집하도록 유도하고 발급자별 논리를 실행하여 자격 증명의 발급 가능 여부와 해당 콘텐츠를 결정합니다.) 

1. 발급자 웹 프런트 엔드는 Azure AD VC 서비스를 호출하여 VC 발급 요청을 생성합니다. 

1. 웹 프런트 엔드는 요청에 대한 링크를 QR 코드 또는 디바이스별 딥 링크(디바이스에 따라 다름)로 렌더링합니다.

1. 보유자는 Microsoft Authenticator와 같은 전자지갑 앱을 사용하여 3단계의 QR 코드 또는 딥 링크를 검색합니다.

1. 전자지갑은 링크에서 요청을 다운로드합니다. 요청에 다음이 포함되어 있습니다.

   * 발급자의 DID입니다. 전자지갑 앱에서 퍼블릭 키와 연결된 도메인을 찾기 위해 ION에서 확인하는 데 사용됩니다.

   * VC를 발급하기 위한 계약 요구 사항을 지정하는 VC 매니페스트가 있는 URL입니다. 여기에는 id_token, 제공해야 하는 자체 증명된 속성 또는 다른 VC의 표시가 포함될 수 있습니다. 

   * VC의 모양과 느낌(로고 파일, 색상 등의 URL)입니다.

1. 전자지갑은 발급 요청의 유효성을 검사하고 계약 요구 사항을 처리합니다.

   1. 발급 요청 메시지가 ION에서 해결된 DID 문서에 있는 발급자의 키로 서명되었는지 확인합니다. 그러면 메시지가 변조되지 않습니다.

   1. 발급자의 DID 문서에서 참조된 DNS 도메인이 발급자 소유인지 확인합니다. 

   1. VC 계약 요구 사항에 따라 전자지갑은 보유자가 추가 정보를 수집하도록 요구할 수 있습니다. 예를 들어 자체 발급 속성을 요청하거나 id_token을 얻기 위해 OIDC 흐름을 탐색합니다.

1. 계약에 필요한 아티팩트를 Azure AD VC 서비스에 제출합니다. Azure AD VC 서비스는 발급자의 DID 키로 서명된 VC를 반환하고, 전자지갑에서 VC를 안전하게 저장합니다.

발급 솔루션을 구축하는 방법 및 아키텍처 고려 사항에 대한 자세한 내용은 [Azure Active Directory 확인 가능한 자격 증명 발급 솔루션 계획](plan-issuance-solution.md)을 참조하세요.

### <a name="flow-2-verifiable-credential-presentation"></a>흐름 2: 확인 가능한 자격 증명 프레젠테이션

![확인 가능한 자격 증명 프레젠테이션](media/introduction-to-verifiable-credentials-architecture/presentation.png)

이 흐름에서 보유자는 RP(신뢰 당사자)와 상호 작용하여 VC를 권한 부여 요구 사항의 일부로 제공합니다.

1. 보유자는 브라우저나 기본 애플리케이션을 통해 신뢰 당사자의 웹 프런트 엔드에 액세스하여 흐름을 시작합니다.

1. 웹 프런트 엔드는 Azure AD VC 서비스를 호출하여 VC 프레젠테이션 요청을 생성합니다.

1. 웹 프런트 엔드는 요청에 대한 링크를 QR 코드 또는 디바이스별 딥 링크(디바이스에 따라 다름)로 렌더링합니다.

1. 보유자는 Microsoft Authenticator와 같은 전자지갑 앱을 사용하여 3단계의 QR 코드 또는 딥 링크를 검색합니다.

1. 전자지갑은 링크에서 요청을 다운로드합니다. 요청에 다음이 포함되어 있습니다.

   * 스키마 또는 자격 증명 형식의 [자격 증명에 대한 표준 기반 요청](https://identity.foundation/presentation-exchange/) 

   * 전자지갑이 ION에서 찾는 RP의 DID입니다.


1. 이 전자지갑은 프레젠테이션 요청의 유효성을 검사하고 요청을 충족하는 저장된 VC를 찾습니다. 필요한 VC에 따라 전자지갑은 주체에게 VC를 선택하고 동의하도록 안내합니다.

   * 주체가 VC를 사용하도록 동의하면 전자지갑은 주체와 RP 간에 고유한 DID를 쌍으로 생성합니다. 

   그런 다음 전자지갑은 주체가 서명한 Azure AD VC 서비스에 프레젠테이션 응답 페이로드를 보냅니다. 다음과 같이 구성됩니다. 

   * 주체가 동의하는 VC입니다.

   * 페이로드의 “주체”로 생성된 쌍별 DID입니다.

   * 페이로드의 “대상”인 RP DID입니다.

1. Azure AD VC 서비스는 전자지갑에서 보낸 응답의 유효성을 검사합니다. 2단계에서 원래 프레젠테이션 요청이 생성된 방식에 따라 이 유효성 검사에는 취소와 같은 경우를 위해 VC 발급자와 함께 제시된 VC의 상태를 확인하는 것이 포함될 수 있습니다.

1. 유효성 검사가 수행되면 Azure AD VC 서비스는 결과를 사용하여 RP를 다시 호출합니다. 

유효성 검사 솔루션을 구축하는 방법 및 아키텍처 고려 사항에 대한 자세한 내용은 [Azure Active Directory 확인 가능한 자격 증명 확인 솔루션 계획](plan-verification-solution.md)을 참조하세요.

## <a name="key-takeaways"></a>핵심 내용

탈중앙화 아키텍처는 기존 솔루션을 개선하고 새로운 기능을 제공하는 데 사용할 수 있습니다. 

DIF([Decentralized Identity Foundation](https://identity.foundation/)) 및 W3C [디자인 목표](https://www.w3.org/TR/did-core/)의 목표를 달성하려면 확인 가능한 자격 증명 솔루션을 생성할 때 다음 사항을 고려해야 합니다.

* 시스템의 행위자 간에는 신뢰를 확립할 수 있는 중앙 지점이 없습니다. 즉, 행위자가 특정 VC를 신뢰하므로 신뢰 경계가 페더레이션을 통해 확장되지 않습니다.

   * ION을 사용하면 모든 행위자의 DID(탈중앙화 식별자)를 발견할 수 있습니다.

   * 이 솔루션을 통해 검증자는 모든 발급자로부터 VC(확인 가능한 자격 증명)의 유효성을 검사할 수 있습니다.

   * 이 솔루션을 사용하면 발급자가 주체 또는 검증자(신뢰 당사자)의 권한을 제어할 수 없습니다.

* 행위자는 분리된 방식으로 작동하며 각각은 자신의 역할에 대한 작업을 완료할 수 있습니다.

   * 발급자가 모든 VC 요청을 처리하고 서비스된 요청을 차별하지 않습니다.

   * 주체는 발급된 VC를 소유하고 모든 검증자에게 VC를 제시할 수 있습니다.

   * 검증자는 모든 주체나 발급자로부터 VC의 유효성을 검사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

확인 가능한 자격 증명의 아키텍처에 대해 자세히 알아보기

* [발행 솔루션 계획](plan-issuance-solution.md)

* [확인 솔루션 계획](plan-verification-solution.md)

* [Azure Active Directory 확인 가능한 자격 증명 시작하기](get-started-verifiable-credentials.md)
