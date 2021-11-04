---
title: Azure AD B2C 배포
description: Azure Active Directory B2C 배포 가이드
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9935fb243b6c2824633e67ac1eed26a80b67bc9d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068135"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Azure Active Directory B2C 배포 계획

Azure Active Directory B2C는 확장 가능한 ID 및 액세스 관리 솔루션입니다. 비즈니스 기대치를 충족하는 높은 유연성과 기존 인프라와의 원활한 통합을 통해 더 많은 디지털화가 가능합니다.

조직에서 비즈니스 요구 사항을 이해하고 규정 준수 경계를 준수할 수 있도록 Azure AD(Azure Active Directory) B2C 배포 전체에서 단계별 접근 방식을 사용하는 것이 좋습니다.

| 기능 | Description |
|:-----|:------|
| [계획](#plan-an-azure-ad-b2c-deployment) | 배포할 Azure AD B2C 프로젝트를 준비합니다. 먼저 관련자를 식별하고, 나중에 프로젝트 타임라인을 정의합니다. |
| [구현](#implement-an-azure-ad-b2c-deployment) | 먼저 인증 및 권한 부여를 사용하도록 설정하고, 나중에 전체 애플리케이션 온보딩을 수행합니다. |
| [모니터](#monitor-an-azure-ad-b2c-solution) | Azure AD B2C 솔루션이 준비되면 로깅, 감사 및 보고를 사용하도록 설정합니다. |

## <a name="plan-an-azure-ad-b2c-deployment"></a>Azure AD B2C 배포 계획

이 단계에 포함되는 기능은 다음과 같습니다.

| 기능 | Description |
|:------------|:------------|
|[비즈니스 요구 사항 검토](#business-requirements-review) | 조직의 상태와 기대치를 평가합니다. |
| [관련자](#stakeholders) |프로젝트 팀을 구축합니다. |
|[통신](#communication) | 프로젝트에 대해 팀과 소통합니다. |
| [타임라인](#timeline) | 주요 프로젝트 마일스톤을 미리 알려줍니다.  |

### <a name="business-requirements-review"></a>비즈니스 요구 사항 검토

- 기존 시스템을 해제하고 [Azure AD B2C로 이동](../../active-directory-b2c/overview.md)해야 하는 주요 이유를 평가합니다.

- 새 애플리케이션의 경우 CIAM(고객 ID 액세스 관리) 시스템을 [계획 및 설계](../../active-directory-b2c/best-practices.md#planning-and-design)합니다.

- 고객의 위치를 식별하고 [해당 데이터 센터에서 테넌트를 만듭니다](../../active-directory-b2c/tutorial-create-tenant.md).

- 보유하고 있는 애플리케이션의 유형을 확인합니다.
  - 현재 지원되는 플랫폼([MSAL](../develop/msal-overview.md) 또는 [오픈 소스](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9))을 확인합니다.
  - 백 엔드 서비스의 경우 [클라이언트 자격 증명 흐름](../develop/msal-authentication-flows.md#client-credentials)을 사용합니다.

- 기존 ID 공급자(IdP)에서 마이그레이션하려는 경우

  - [원활한 마이그레이션 방법](../../active-directory-b2c/user-migration.md#seamless-migration)을 사용하는 것이 좋습니다.
  - [기존 애플리케이션을 마이그레이션하는 방법](https://github.com/azure-ad-b2c/user-migration)을 알아봅니다.
  - 여러 솔루션을 한 번에 공존하도록 보장합니다.

- 사용하려는 프로토콜을 결정합니다.

  - 현재 Kerberos, NTLM 및 WS-Fed를 사용하는 경우 [애플리케이션을 마이그레이션하고 리팩터링](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE)합니다. 애플리케이션이 마이그레이션되면 OAuth 2.0 및 OIDC(OpenID Connect)와 같은 최신 ID 프로토콜을 지원하여 ID 보호 및 보안을 추가로 구현할 수 있습니다.

### <a name="stakeholders"></a>관련자

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](./active-directory-deployment-plans.md#include-the-right-stakeholders)시키고 관련자가 자신의 역할을 이해하고 있는지 확인해야 합니다.

- 애플리케이션의 기본 설계자, 프로젝트 관리자 및 소유자를 식별합니다.

- DL(배포 목록)을 제공하는 것이 좋습니다. 이 DL을 사용하면 제품 문제를 Microsoft 계정 팀 또는 엔지니어링 팀에 전달할 수 있습니다. 질문을 하고 중요한 알림을 받을 수 있습니다.

- 사용자를 지원할 수 있는 조직 외부의 파트너 또는 리소스를 식별합니다.

### <a name="communication"></a>통신

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자에게 변경 내용을 사전에 알려줍니다. 환경에서 변경하는 방법과 시기 및 문제가 발생하는 경우 지원을 받는 방법에 대해 적시에 알려줍니다.

### <a name="timeline"></a>타임라인

명확한 기대치를 정의하고, 다음과 같은 주요 마일스톤을 충족하기 위한 후속 계획을 수립합니다.

- 예상 파일럿 날짜

- 예상 시작 날짜

- 프로젝트 배달 날짜에 영향을 줄 수 있는 날짜

## <a name="implement-an-azure-ad-b2c-deployment"></a>Azure AD B2C 배포 구현

이 단계에 포함되는 기능은 다음과 같습니다.

| 기능 | Description |
|:-------------|:--------------|
| [인증 및 권한 부여 배포](#deploy-authentication-and-authorization) | [인증 및 권한 부여](../develop/authentication-vs-authorization.md) 시나리오를 이해합니다. |
| [애플리케이션 및 사용자 ID 배포](#deploy-applications-and-user-identities) | 클라이언트 애플리케이션을 배포하고 사용자 ID를 마이그레이션하도록 계획합니다.  |
| [클라이언트 애플리케이션 온보딩 및 배달물](#client-application-onboarding-and-deliverables) | 클라이언트 애플리케이션을 온보딩하고 솔루션을 테스트합니다. |
| [보안](#security) | ID 솔루션의 보안을 강화합니다. |
|[호환성](#compliance) | 규정 요구 사항을 처리합니다. |
|[사용자 환경](#user-experience) | 사용자에게 친숙한 서비스를 사용하도록 설정합니다. |

### <a name="deploy-authentication-and-authorization"></a>인증 및 권한 부여 배포

- 먼저 [Azure AD B2C 테넌트를 설정](../../active-directory-b2c/tutorial-create-tenant.md)합니다.

- 비즈니스 기반 권한 부여의 경우 [Azure AD B2C IEF(Identity Experience Framework) 샘플 사용자 경험](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements)을 사용합니다.

- [Open Policy Agent](https://www.openpolicyagent.org/)를 사용해 봅니다.

[이 개발자 과정](https://aka.ms/learnaadb2c)에서 Azure AD B2C에 대해 자세히 알아보세요.

자세한 지침은 다음 샘플 검사 목록을 따릅니다.

- 애플리케이션에 액세스해야 하는 다른 가상 사용자를 식별합니다.  

- 현재 기존 시스템에서 권한 및 자격을 관리하는 방법과 향후 계획을 수립하는 방법을 정의합니다.

- 권한 저장소가 있는지와 디렉터리에 추가해야 하는 권한이 있는지를 확인합니다.

- 위임된 관리가 필요한 경우 이를 해결하는 방법을 정의합니다. 예를 들어 고객의 고객 관리가 있습니다.

- 애플리케이션에서 APIM(API 관리자)을 직접 호출하는지 확인합니다. 토큰을 애플리케이션에 발급하기 전에 IdP에서 호출해야 할 수도 있습니다.

### <a name="deploy-applications-and-user-identities"></a>애플리케이션 및 사용자 ID 배포

모든 Azure AD B2C 프로젝트는 비즈니스 목표가 다를 수 있는 하나 이상의 클라이언트 애플리케이션으로 시작합니다.

1. [클라이언트 애플리케이션을 만들거나 구성](../../active-directory-b2c/app-registrations-training-guide.md)합니다. 구현은 여기에 있는 [코드 샘플](../../active-directory-b2c/integrate-with-app-code-samples.md)을 참조하세요.

2. 다음으로 기본 제공 또는 사용자 지정 사용자 흐름에 따라 사용자 경험을 설정합니다. [사용자 흐름을 사용해야 하는 경우와 사용자 지정 정책을 사용해야 하는 경우를 알아보세요](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies).

3. 비즈니스 요구 사항에 따라 IdP를 설정합니다. [Azure Active Directory B2C를 IdP로 추가하는 방법에 대해 알아보세요](../../active-directory-b2c/add-identity-provider.md).

4. 사용자를 마이그레이션합니다. [사용자 마이그레이션 방법에 대해 알아보세요](../../active-directory-b2c/user-migration.md). 고급 시나리오는 [Azure AD B2C IEF 샘플 사용자 경험](https://github.com/azure-ad-b2c/samples)을 참조하세요.  

**애플리케이션을 배포** 할 때 다음 샘플 검사 목록을 고려합니다.

- CIAM 배포 범위에 있는 애플리케이션의 수를 확인합니다.

- 사용 중인 애플리케이션의 유형을 확인합니다. 예를 들어 기존 웹 애플리케이션, API, SPA(단일 페이지 앱) 또는 네이티브 모바일 애플리케이션이 있습니다.

- 사용 중인 인증의 종류를 확인합니다. 예를 들어 양식 기반, SAML과 페더레이션 또는 OIDC와 페더레이션이 있습니다.
  - OIDC인 경우 응답 형식(code 또는 id_token)을 확인합니다.

- 모든 프런트 엔드 및 백 엔드 애플리케이션이 온-프레미스, 클라우드 또는 하이브리드 클라우드에서 호스트되는지 확인합니다.

- 사용되는 플랫폼/언어(예: [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md), Java 및 Node.js)를 확인합니다.

- 현재 사용자 특성이 저장되는 위치를 확인합니다. LDAP(Lightweight Directory Access Protocol) 또는 데이터베이스일 수 있습니다.

**사용자 ID를 배포** 할 때 다음 샘플 검사 목록을 고려합니다.

- 애플리케이션에 액세스하는 사용자의 수를 확인합니다.

- 필요한 IdP 유형을 확인합니다. 예를 들어 Facebook, 로컬 계정 및 [AD FS(Active Directory Federation Services)](/windows-server/identity/active-directory-federation-services)가 있습니다.

- 해당하는 경우 애플리케이션, [Azure AD B2C](../../active-directory-b2c/claimsschema.md) 및 IdP에 필요한 클레임 스키마를 간략히 설명합니다.

- [로그인/가입 흐름](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow) 중에 캡처하는 데 필요한 정보를 간략히 설명합니다.

### <a name="client-application-onboarding-and-deliverables"></a>클라이언트 애플리케이션 온보딩 및 배달물

**애플리케이션을 온보딩** 하는 동안 다음 샘플 검사 목록을 고려합니다.

|  작업 | 설명 |
|:--------------------|:----------------------|
| 애플리케이션의 대상 그룹 정의 | 이 애플리케이션이 최종 고객 애플리케이션, 비즈니스 고객 애플리케이션 또는 디지털 서비스인지 확인합니다. 직원 로그인이 필요한지 확인합니다. |
| 애플리케이션 이면의 비즈니스 가치 식별 | 애플리케이션 이면의 전체 비즈니스 사례를 파악하여 Azure AD B2C 솔루션 및 추가 클라이언트 애플리케이션과의 통합에 가장 적합한 것을 찾습니다.|
| 보유하고 있는 ID 그룹 확인 | 최종 고객과 비즈니스 고객을 위한 **B2C(Business to Customer)** , 파트너와 공급업체를 위한 **B2B(Business to Business)** , 직원과 외부 직원을 위한 **B2E(Business to Employee)** , IoT 디바이스 로그인과 서비스 계정을 위한 **B2M(Business to Machine)** 과 같이 다양한 유형의 요구 사항이 있는 다양한 유형의 그룹에 대한 클러스터 ID입니다.|
| 비즈니스 요구 사항 및 프로세스에 필요한 IdP 확인 | Azure AD B2C는 [여러 유형의 IdP를 지원](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider)하며 사용 사례에 따라 올바른 IdP를 선택해야 합니다. 예를 들어 C2C(Customer to Customer) 모바일 애플리케이션의 경우 빠르고 쉬운 사용자 로그인이 필요합니다. 또 다른 사용 사례에서는 디지털 서비스를 사용하는 B2C(Business to Customer)의 경우 추가 규정 준수 요구 사항이 필요합니다. 사용자는 이메일 로그인과 같은 비즈니스 ID를 사용하여 로그인해야 할 수 있습니다. |
| 규정 제약 조건 확인 | 원격 프로필 또는 특정 개인 정보 보호 정책이 있어야 하는 이유가 있는지 확인합니다.  |
| 로그인 및 가입 흐름 설계 | 이메일 확인 또는 가입 시 이메일 확인이 필요한지 여부를 결정합니다. 먼저 Shop 시스템 또는 [Azure AD MFA(Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)와 같은 프로세스가 필요한지 여부를 확인합니다. [이 비디오](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4)를 시청하세요. |
| 사용되거나 구현될 애플리케이션 및 인증 프로토콜의 유형 확인 | 웹 애플리케이션, SPA 또는 네이티브 애플리케이션과 같은 클라이언트 애플리케이션의 구현에 대한 정보를 교환합니다. 클라이언트 애플리케이션 및 Azure AD B2C에 대한 인증 프로토콜은 OAuth, OIDC 및 SAML일 수 있습니다. [이 비디오](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9)를 시청하세요.|
| 사용자 마이그레이션 계획 | [Azure AD B2C를 통한 사용자 마이그레이션](../../active-directory-b2c/user-migration.md)의 가능성에 대해 설명합니다. JIT(Just-In-Time) 마이그레이션 및 대량 가져오기/내보내기와 같이 가능한 몇 가지 시나리오가 있습니다. [이 비디오](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2)를 시청하세요. [Microsoft Graph API](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3)를 사용자 마이그레이션에 사용하는 것도 고려할 수 있습니다.|

**배달** 하는 동안 다음 샘플 검사 목록을 고려합니다.

| 기능 | Description |
|:-----|:-------|
|프로토콜 정보| 두 변형의 기본 경로, 정책, 메타데이터 URL을 수집합니다. 클라이언트 애플리케이션에 따라 샘플 로그인, 클라이언트 애플리케이션 ID, 비밀 및 리디렉션과 같은 특성을 지정합니다.|
| 애플리케이션 샘플 | 여기에 제공된 [샘플 코드](../../active-directory-b2c/integrate-with-app-code-samples.md)를 참조하세요. |
|펜 테스트 | 펜 테스트를 수행하기 전에 펜 테스트에 대해 운영 팀에 알린 다음, OAuth 구현을 포함한 모든 사용자 흐름을 테스트합니다. [침투 테스트](../../security/fundamentals/pen-testing.md) 및 [Microsoft 클라우드 통합 침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement)에 대해 자세히 알아보세요.
| 단위 테스트  | [ROPC(리소스 소유자 암호 자격 증명) 흐름을 사용](../develop/v2-oauth-ropc.md)하여 단위 테스트를 수행하고 토큰을 생성합니다. Azure AD B2C 토큰 제한에 도달하면 [지원 팀에 문의](../../active-directory-b2c/support-options.md)합니다. 토큰을 다시 사용하여 인프라에 대한 조사 작업을 줄입니다. [ROPC 흐름을 설정](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga)합니다.|
| 부하 테스트 | Azure AD B2C [서비스 제한](../../active-directory-b2c/service-limits.md)에 도달해야 합니다. 사용하는 서비스에 대한 월별 예상 인증 수를 평가합니다. 월별 예상 평균 사용자 로그인 수를 평가합니다. 예상 고부하 트래픽 기간과 비즈니스 이유(예: 휴일, 마이그레이션 및 이벤트)를 평가합니다. 예상 최고 가입 속도(예: 초당 요청 수)를 평가합니다. MFA를 통한 예상 최고 트래픽 속도(예: 초당 요청 수)를 평가합니다. 예상 트래픽 지리적 분포 및 해당 최고 속도를 평가합니다.

### <a name="security"></a>보안

비즈니스 요구 사항에 따라 애플리케이션의 보안을 강화하려면 다음 샘플 검사 목록을 고려합니다.

- [MFA](../authentication/concept-mfa-howitworks.md)와 같은 강력한 인증 방법이 필요한지 확인합니다. 높은 가치의 트랜잭션 또는 기타 위험 이벤트를 트리거하는 사용자의 경우 MFA를 사용하는 것이 좋습니다. 예를 들어 은행 및 금융 애플리케이션의 경우 온라인 상점 - 첫 번째 체크 아웃 프로세스입니다.

- MFA가 필요한지 확인하고, SMS/전화, 이메일 및 타사 서비스와 같이 [MFA를 수행하는 데 사용할 수 있는 방법을 확인](../authentication/concept-authentication-methods.md)합니다.

- 현재 애플리케이션에서 봇 방지 메커니즘을 사용하고 있는지 확인합니다.

- 사기성 계정 및 로그인을 만들려는 시도의 위험을 평가합니다. [Microsoft Dynamics 365 Fraud Protection 평가](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md)를 사용하여 새 가짜 계정을 만들거나 기존 계정을 손상시키려는 의심스러운 시도를 차단하거나 이의를 제기합니다.  

- 애플리케이션을 사용하여 계정에 로그인하거나 가입하는 과정의 일부로 적용해야 하는 특별한 조건부 상태를 확인합니다.

>[!NOTE]
>[조건부 액세스 규칙](../conditional-access/overview.md)을 사용하여 비즈니스 목표에 따라 사용자 환경과 보안 간의 차이를 조정할 수 있습니다.

자세한 내용은 [Azure AD B2C의 ID 보호 및 조건부 액세스](../../active-directory-b2c/conditional-access-identity-protection-overview.md)를 참조하세요.

### <a name="compliance"></a>규정 준수

특정 규정 요구 사항을 충족하기 위해 vNet, IP 제한, WAF(Web Application Firewall) 및 유사한 서비스를 사용하여 백 엔드 시스템의 보안을 강화하는 것이 좋습니다.

기본 규정 준수 요구 사항을 처리하려면 다음을 고려합니다.

- 특정 규정 준수 요구 사항(예: 지원해야 하는 PCI-DSS)

- 데이터를 별도의 데이터베이스 저장소에 저장해야 하는지 확인합니다. 그렇다면 이 정보를 디렉터리에 쓰지 않아야 하는지 확인합니다.

### <a name="user-experience"></a>사용자 환경

UX(사용자 환경) 요구 사항을 정의하려면 다음 샘플 검사 목록을 고려합니다.

- [CIAM 기능을 확장하고 원활한 최종 사용자 환경을 구축](../../active-directory-b2c/partner-gallery.md)하는 데 필요한 통합을 식별합니다.

- 기존 애플리케이션에 대한 최종 사용자 환경을 보여 주는 스크린샷과 사용자 사례를 제공합니다. 예를 들어 로그인, 가입, 통합 SUSI(가입-로그인), 프로필 편집 및 암호 재설정에 대한 스크린샷을 제공합니다.

- 현재 CIAM 솔루션에서 queryString 매개 변수를 사용하여 전달되는 기존 힌트를 찾습니다.

- 픽셀-픽셀과 같은 높은 UX 사용자 지정이 필요한 경우 도움을 줄 수 있는 프런트 엔드 개발자의 도움이 필요할 수 있습니다.

## <a name="monitor-an-azure-ad-b2c-solution"></a>Azure AD B2C 솔루션 모니터링

이 단계에 포함되는 기능은 다음과 같습니다.

| 기능 | Description |
|:---------|:----------|
|  모니터링  |[Azure Monitor를 사용하여 Azure AD B2C를 모니터링](../../active-directory-b2c/azure-monitor.md)합니다. [이 비디오](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1)를 시청하세요.|
| 감사 및 로깅 | [감사 로그 액세스 및 검토](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>자세한 정보

Azure AD B2C 배포를 가속화하고 서비스를 규모에 맞게 모니터링하려면 다음 문서를 참조하세요.

- [Microsoft Graph로 Azure AD B2C 관리](../../active-directory-b2c/microsoft-graph-get-started.md)

- [Microsoft Graph로 Azure AD B2C 사용자 계정 관리](../../active-directory-b2c/microsoft-graph-operations.md)

- [Azure Pipelines를 사용하여 사용자 지정 정책 배포](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [Azure PowerShell을 사용하여 Azure AD B2C 사용자 지정 정책 관리](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [Azure Monitor를 사용한 Azure AD B2C 모니터링](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 모범 사례](../../active-directory-b2c/best-practices.md)

- [Azure AD B2C 서비스 제한 사항](../../active-directory-b2c/service-limits.md)
