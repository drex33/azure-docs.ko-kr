---
title: Azure Active Directory 앱 갤러리에 앱 게시
description: Azure Active Directory 앱 갤러리에 Single Sign-On을 지원하는 애플리케이션을 나열하는 방법을 알아봅니다. 앱 갤러리에 게시하면 고객이 더 쉽게 앱을 찾고 테넌트에 추가할 수 있습니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev, contperf-fy21q4
ms.openlocfilehash: 7938e8ffbaca3f069016a445775d9c669f1ad144
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228071"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 앱 게시

Azure AD(Azure Active Directory) 앱 갤러리에 앱을 게시할 수 있습니다. 앱이 게시되면 [테넌트에 앱을 추가](../manage-apps/add-application-portal.md)할 때 고객에게 옵션으로 표시됩니다. 

Azure AD 앱 갤러리에 앱을 게시하는 단계는 다음과 같습니다.
1. 필수 구성 요소
1. 앱에 적합한 Single Sign-On 표준을 선택합니다.
1. 앱에서 Single Sign-On을 구현합니다.
1. 앱에서 SCIM 사용자 프로비저닝 구현(선택 사항)
1. Azure 테넌트를 만들고 앱을 테스트합니다.
1. 설명서를 만들고 게시합니다.
1. 앱을 제출합니다.
1. Microsoft 파트너 네트워크에 참가합니다.

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리란?

[Azure AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)는 SSO(Single Sign-On)와 자동화된 사용자 프로비저닝을 쉽게 배포하고 구성할 수 있는 수천 개의 앱 카탈로그입니다.

Azure AD 갤러리에 앱을 추가하면 다음과 같은 이점이 있습니다.

- 고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.
- 애플리케이션 구성이 간단하고 최소화됩니다.
- 갤러리에서 빠른 검색에 애플리케이션을 제공합니다.
- 무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.
- [SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(System for Cross-domain Identity Management)을 사용하는 고객은 동일한 앱에 대한 프로비저닝을 사용할 수 있습니다.

또한 고객이 앱의 ID 공급자로 Azure AD를 사용하는 경우 많은 이점이 있습니다. 그 중 일부는 다음과 같습니다.

- 사용자에게 Single Sign-On을 제공합니다. SSO를 사용할 수 있게 되면 고객이 Single Sign-On을 더 쉽게 사용할 수 있도록 하여 지원 비용을 절감할 수 있습니다. 한 번 클릭 SSO를 사용하도록 설정하면 고객의 IT 관리자가 조직에서 사용하도록 애플리케이션을 구성하는 방법을 배우지 않아도 됩니다. Single Sign-On에 대한 자세한 내용은 [Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
- Microsoft 365 앱 갤러리, Microsoft 365 앱 시작 관리자 및 Office.com의 Microsoft Search 내에서 앱을 검색할 수 있습니다. 
- 통합 앱 관리. Azure AD의 앱 관리에 대한 자세한 내용은 [애플리케이션 관리란?](../manage-apps/what-is-application-management.md)을 참조하세요.
- 앱은 [Graph API](/graph/)를 사용하여 Microsoft 에코시스템에서 사용자 생산성을 구동하는 데이터에 액세스할 수 있습니다.
- 상호 고객을 위해 Azure AD 팀과 공동으로 제작한 애플리케이션 관련 설명서는 채택하는 데 도움이 됩니다.
- 고객에게 직원 및 게스트 ID의 인증 및 권한 부여를 완전히 관리할 수 있는 기능을 제공합니다.
- 모든 계정 관리 및 규정 준수 책임은 해당 ID의 고객 소유자에게 있습니다.
- 특정 ID 공급자, 그룹 또는 사용자가 비즈니스 요구 사항을 충족하도록 SSO를 사용하거나 사용하지 않도록 설정하는 기능을 제공합니다.
- 시장 가능성과 채택 가능성을 높입니다. 많은 대규모 조직에서는 직원들이 모든 애플리케이션에서 원활한 SSO 환경을 갖출 것을 요구하거나 촉진합니다. SSO를 쉽게 만드는 것이 중요합니다.
- 최종 사용자 마찰을 줄이면 최종 사용자 사용량이 증가하고 수익이 증가할 수 있습니다.
- [SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(System for Cross-domain Identity Management)을 사용하는 고객은 동일한 앱에 대한 프로비저닝을 사용할 수 있습니다.
- 사용자가 Azure AD SSO를 사용하고 별도 자격 증명이 필요하지 않도록 함으로써 애플리케이션에 로그온하여 할 때 보안 및 편의를 추가적으로 제공합니다.

> [!TIP]
> 구매 또는 구독을 통해 다른 회사에서 사용할 애플리케이션을 제공하는 경우 자신의 Azure 테넌트 내에서 고객이 애플리케이션을 사용할 수 있도록 합니다. 이것을 다중 테넌트 애플리케이션이라고 합니다. 이 개념의 개요는 [Azure Active Directory의 테넌트](single-and-multi-tenant-apps.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
Azure AD 갤러리에 앱을 게시하려면 먼저 특정 [사용 약관](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)을 읽고 이에 동의해야 합니다.

두 명 이상의 사용자를 등록하여 테스트하기 위한 영구 계정이 필요합니다.

- 페더레이션된 애플리케이션(Open ID 및 SAML/WS-Fed)의 경우 애플리케이션은 Azure AD 앱 갤러리에 표시되려면 SaaS(Software-as-a-Service) 모델을 지원해야 합니다. 엔터프라이즈 갤러리 애플리케이션은 특정 고객이 아닌 여러 고객 구성을 지원해야 합니다.
- Open ID Connect의 경우 애플리케이션은 다중 테넌트화되어야 하며, [Azure AD 동의 프레임워크](../develop/consent-framework.md)는 애플리케이션에 대해 올바르게 구현되어야 합니다. 모든 고객이 애플리케이션에 동의를 제공할 수 있도록 사용자가 공통 엔드포인트에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.
- SAML 2.0/WS-Fed의 경우 SP 또는 IDP 모드에서 SAML/WS-Fed SSO 통합을 수행하는 기능이 애플리케이션에 있어야 합니다. 요청을 제출하기 전에 이 기능이 제대로 작동하는지 확인합니다.
- 암호 SSO의 경우 암호 보관을 수행하여 Single Sign-On이 예상대로 작동할 수 있도록 애플리케이션이 폼 인증을 지원하는지 확인하세요.
- 두 명 이상의 사용자를 등록하여 테스트하기 위한 영구 계정이 필요합니다.

모든 프리미엄 Azure AD 기능(90일 무료)이 있는 무료 테스트 계정을 얻을 수 있으며, 개발 작업을 수행하는 한, 확장할 수 있습니다. [Microsoft 365 개발자 프로그램에 참여하세요.](/office/developer-program/microsoft-365-developer-program)

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>1단계 - 앱에 적합한 Single Sign-On 표준 선택

Azure AD 앱 갤러리에 애플리케이션을 나열하려면 지원되는 Single Sign-On 옵션 중 하나 이상을 구현합니다. Single Sign-On 옵션과 고객이 Azure AD에서 이 옵션을 구성하는 방법을 이해하려면 [SSO 옵션](../manage-apps/sso-options.md)을 참조하세요.

다음 표에서는 주 표준인 OIDC(OpenID Connect)가 있는 OAuth 2.0(Open Authentication 2.0), SAML(Security Assertion Markup Language) 및 WS-Fed(Web Services Federation)를 비교해서 설명합니다.

| 기능| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| 웹 기반 Single Sign-On| √| √ |
| 웹 기반 Single Sign-out| √| √ |
| 모바일 기반 Single Sign-On| √| √* |
| 모바일 기반 Single Sign-Out| √| √* |
| 모바일 애플리케이션에 대한 조건부 액세스 정책| √| √* |
| 모바일 애플리케이션에 대한 원활한 MFA 환경| √| √* |
| SCIM 프로비저닝| √| √ |
| Microsoft Graph 액세스| √| X |

*가능하지만 Microsoft에서 샘플 또는 지침을 제공하지 않습니다.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 및 OpenID Connect
OAuth 2.0은 권한 부여를 위한 [업계 표준](https://oauth.net/2/) 프로토콜입니다. OIDC(OpenID Connect)는 OAuth 2.0 프로토콜을 기준으로 구축된 [업계 표준](https://openid.net/connect/) ID 인증 계층입니다. 

**OAuth/OIDC를 선택하는 이유**
- 이러한 프로토콜에 내재된 권한 부여를 사용하여 애플리케이션은 Microsoft Graph API를 통해 풍부한 사용자 및 조직 데이터에 액세스하고 통합할 수 있습니다.
- 애플리케이션에 SSO를 채택할 때 고객의 최종 사용자 환경을 간소화합니다. 필요한 권한 집합을 쉽게 정의할 수 있습니다. 그러면 이러한 권한 집합이 관리자 또는 최종 사용자 동의에 자동으로 표시됩니다.
- 이러한 프로토콜을 사용하면 고객이 조건부 액세스 및 MFA(Multi-Factor Authentication) 정책을 사용하여 애플리케이션에 대한 액세스를 제어할 수 있습니다. 
- Microsoft는 개발을 지원하기 위해 [여러 기술 플랫폼에서 라이브러리 및 코드 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)을 제공합니다.  

**고려할 사항**
- 애플리케이션에 대한 SAML 기반 Single Sign-On을 이미 구현한 경우 갤러리에서 앱을 가져오기 위해 새 표준을 구현하지 않으려고 할 수 있습니다.

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 또는 WS-Fed

SAML은 웹 애플리케이션을 위한 널리 채택되고 있는 완성된 [Single Sign-On 표준](https://www.oasis-open.org/standards#samlv2.0)입니다. Azure에서 SAML를 사용하는 방법에 대한 자세한 내용은 [Azure에서 SAML 프로토콜을 사용하는 방법](active-directory-saml-protocol-reference.md)을 참조하세요. 

WS-Fed(Web Services Federation)는 .NET 플랫폼을 사용하여 개발된 웹 애플리케이션에 일반적으로 사용되는 [업계 표준](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)입니다.

**SAML을 선택하는 이유**
- SAML 2.0은 완성된 표준이며 대부분의 기술 플랫폼은 SAML 2.0용 오픈 소스 라이브러리를 지원합니다. 
- SAML SSO를 구성하기 위해 고객에게 관리 인터페이스를 제공할 수 있습니다. Microsoft Azure AD용 SAML SSO와 SAML을 지원하는 다른 모든 ID 공급자를 구성할 수 있습니다.

**고려할 사항**
- 모바일 애플리케이션에 SAML 2.0 또는 WSFed 프로토콜을 사용하는 경우 MFA(Multi-factor Authentication)를 비롯한 특정 조건부 액세스 정책에는 성능 저하가 발생합니다.
- Microsoft Graph에 액세스하려는 경우 OAuth 2.0을 통해 권한 부여를 구현하여 필요한 토큰을 생성해야 합니다. 

### <a name="password-based"></a>암호 기반
암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>2단계 - 앱에서 Single Sign-On 구현
갤러리의 모든 앱은 지원되는 Single Sign-On 옵션 중 하나를 구현해야 합니다. 지원되는 옵션에 대 한 자세한 내용은 [SSO 옵션](../manage-apps/sso-options.md)을 참조하세요.

OAuth 및 OIDC의 경우 [인증 패턴 지침](v2-app-types.md) 및 [Azure Active Directory 코드 샘플](sample-v2-code.md)을 참조하세요.

SAML 및 WS-Fed의 경우 SP 또는 IDP 모드에서 SSO 통합을 수행하는 기능이 애플리케이션에 있어야 합니다. 요청을 제출하기 전에 이 기능이 제대로 작동하는지 확인합니다.

인증에 대해 자세히 알아보려면 [인증이란?](authentication-vs-authorization.md)을 참조하세요.

> [!IMPORTANT]
> 페더레이션된 애플리케이션(OpenID 및 SAML/WS-Fed)의 경우 앱에서 SaaS(Software as a Service) 모델을 지원해야 합니다. Azure AD 갤러리 애플리케이션은 여러 고객 구성을 지원해야 하며 단일 고객에만 국한되면 안 됩니다.

### <a name="implement-oauth-20-and-openid-connect"></a>OAuth 2.0 및 OpenID Connect 구현

OpenID Connect의 경우 애플리케이션은 다중 테넌트화되어야 하며, [Azure AD 동의 프레임워크](consent-framework.md)는 애플리케이션에 대해 올바르게 구현되어야 합니다. 모든 고객이 애플리케이션에 동의를 제공할 수 있도록 사용자가 공통 엔드포인트에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.

특정 예제를 검토하려면 [Microsoft ID 플랫폼 코드 샘플](sample-v2-code.md)을 참조하세요. 

모바일 관련 예제를 검토하려면 다음을 참조하세요. 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [유니버설 Windows 플랫폼](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>SAML 2.0 구현

앱이 SAML 2.0을 지원하는 경우 Azure AD 테넌트와 직접 통합할 수 있습니다. Azure AD를 사용한 SAML 구성에 대한 자세한 내용은 [SAML 기반 Single Sign-On 구성](../manage-apps/configure-saml-single-sign-on.md)을 참조하세요.

Microsoft는 SAML 구현을 위한 라이브러리를 제공하거나 권장하지 않습니다. 많은 오픈 소스 라이브러리를 사용할 수 있습니다.

### <a name="implement-ws-fed"></a>WS-Fed 구현
ASP.NET Core의 WS-Fed에 대한 자세한 내용은 [ASP.NET Core에서 WS-Federation을 사용하여 사용자 인증](/aspnet/core/security/authentication/ws-federation)을 참조하세요.

### <a name="implement-password-vaulting"></a>암호 보관 구현

HTML 로그인 페이지가 있는 웹 애플리케이션을 만듭니다. 암호 보관을 수행하여 Single Sign-On이 예상대로 작동할 수 있도록 애플리케이션이 폼 인증을 지원하는지 확인하세요.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>3단계 - 앱에서 SCIM 사용자 프로비저닝 구현
[SCIM](https://aka.ms/scimoverview) 프로비저닝 지원은 선택 사항이지만 애플리케이션을 빌드할 때 매우 권장되는 단계입니다. SCIM 표준은 쉽게 지원할 수 있으며, 고객은 CSV 파일 업로드와 같은 수동 프로세스에 의존하지 않고 앱에서 사용자 계정을 자동으로 만들고 업데이트할 수 있습니다. 또한 고객은 사용자 제거를 자동화하고, 그룹 멤버 자격을 동기화된 상태로 유지할 수 있습니다. SAML JIT와 같은 솔루션으로는 이러한 작업을 수행할 수 없습니다. 

### <a name="learn-about-scim"></a>SCIM에 대해 알아보기
고객을 위한 SCIM 표준 및 혜택에 대해 자세히 알아보려면 [SCIM으로 프로비저닝 - 시작](https://aka.ms/scimoverview)을 참조하세요.

### <a name="understand-the-azure-ad-scim-implementation"></a>Azure AD SCIM 구현 이해
Azure AD SCIM 구현에 대해 자세히 알아보려면 [Azure AD를 사용하여 SCIM 엔드포인트 빌드 및 사용자 프로비저닝 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 참조하세요.

### <a name="implement-scim"></a>SCIM 구현
Azure AD는 SCIM 엔드포인트를 빌드하는 데 도움이 되는 [참조 코드](https://aka.ms/scimoverview)를 제공합니다. GitHub에서 찾을 수 있는 많은 타사 라이브러리/참조도 있습니다.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>4단계 - Azure 테넌트 만들기 및 앱 테스트

앱을 테스트하려면 Azure AD 테넌트가 필요합니다. 개발 환경을 설정하려면 [빠른 시작: 테넌트 설정](quickstart-create-new-tenant.md)을 참조하세요.

또는 Azure AD 테넌트는 모든 Microsoft 365 구독에서 제공됩니다. 무료 Microsoft 365 개발 환경을 설정하려면 [Microsoft 365 개발자 프로그램 참여](/office/developer-program/microsoft-365-developer-program)를 참조하세요.

테넌트가 있으면 Single Sign-On 및 [프로비저닝](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)을 테스트합니다. 

**OIDC 또는 Oath 애플리케이션의 경우** [애플리케이션을 다중 테넌트 애플리케이션으로서 등록](quickstart-register-app.md)합니다. ‎지원되는 계정 유형에서 모든 조직 디렉터리의 계정 및 개인 Microsoft 계정 옵션을 선택합니다.

**SAML 및 WS-Fed 기반 애플리케이션의 경우** Azure AD에서 일반 SAML 템플릿을 사용하여 [SAML 기반 Single Sign-On](../manage-apps/configure-saml-single-sign-on.md) 애플리케이션을 구성합니다.

필요한 경우 [단일 테넌트 애플리케이션을 다중 테넌트로 변환](howto-convert-app-to-be-multi-tenant.md)할 수도 있습니다.


## <a name="step-5---create-and-publish-documentation"></a>5단계 - 설명서 만들기 및 게시

### <a name="documentation-on-your-site"></a>사이트에 대한 설명서

채택 용이성은 엔터프라이즈 소프트웨어 결정에 있어서 중요한 요소입니다. 쉽게 따라할 수 있는 명확한 설명서는 채택 과정에서 고객을 지원하고 지원 비용을 줄여줍니다. Microsoft는 수천 개의 소프트웨어 공급업체와 협력하면서 효과적인 방법을 확인했습니다.

사이트에 대한 설명서는 적어도 다음 항목을 포함하는 것이 좋습니다.

* SSO 기능 소개
  * 지원되는 프로토콜
  * 버전 및 SKU
  * 설명서 링크를 포함하는 지원되는 ID 공급자 목록
* 애플리케이션에 대한 라이선스 정보
* SSO 구성을 위한 역할 기반 액세스 제어
* SSO 구성 단계
  * 공급자가 기대하는 가치를 갖는 SAML의 UI 구성 요소
  * ID 공급자에게 전달할 서비스 공급자 정보
* OIDC/OAuth인 경우
  * 비즈니스 타당성을 포함하는 동의에 필요한 사용 권한 목록
* 파일럿 사용자를 위한 테스트 단계
* 오류 코드 및 메시지를 포함하는 문제 해결 정보
* 고객 지원 메커니즘
* 지원되는 리소스 및 특성을 비롯한 SCIM 엔드포인트에 대한 세부 정보

### <a name="documentation-on-the-microsoft-site"></a>Microsoft 사이트에 대한 설명서

Azure Active Directory 애플리케이션 갤러리에 애플리케이션을 나열하고 Azure Marketplace에 애플리케이션을 게시하는 경우 Microsoft는 상호 고객을 위해 단계별 프로세스를 설명하는 설명서를 생성합니다. [여기](../saas-apps/tutorial-list.md)에서 예제를 확인할 수 있습니다. 이 설명서는 갤러리에 대한 제출에 따라 만들어지며, GitHub 계정을 사용하여 애플리케이션을 변경하는 경우 쉽게 업데이트할 수 있습니다.


## <a name="step-6---submit-your-app"></a>6단계 - 앱 제출

애플리케이션 통합이 Azure AD에서 작동하는지 테스트한 후에 [Microsoft Application Network Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 애플리케이션 요청을 제출합니다.

처음으로 이 Portal에 로그인할 때 두 화면 중 하나가 표시됩니다. 

"작동하지 않음" 메시지가 표시되면 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의해야 합니다. 요청을 제출하는 데 사용할 메일 계정을 제공합니다. `name@yourbusiness.com`과 같은 비즈니스 메일 주소가 선호됩니다. 그러면 Azure AD 팀에서 Microsoft Application Network Portal에 계정을 추가합니다.

"액세스 요청" 페이지가 표시되면 비즈니스 타당성을 입력하고 **액세스 요청** 을 선택합니다.

계정이 추가된 후에는 홈페이지에서 **요청 제출(ISV)** 타일을 선택하여 Microsoft Application Network Portal에 로그인한 후 요청을 제출할 수 있습니다.

![홈페이지의 요청 제출(ISV) 타일](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>포털 로그인에 대한 이슈

로그인하는 동안 이 오류가 표시되는 경우 이슈에 대한 세부 정보와 해결 방법은 다음과 같습니다.

* 아래와 같이 로그인이 차단된 경우:

  ![갤러리의 애플리케이션 확인과 관련된 이슈](./media/howto-app-gallery-listing/blocked.png)

**현재 상황:**

게스트 사용자는 Azure AD이기도 한 홈 테넌트에 페더레이션됩니다. 게스트 사용자는 위험 수준이 높습니다. Microsoft는 높은 위험 수준의 사용자가 해당 리소스에 액세스할 수 없도록 합니다. 모든 위험 수준 사용자(직원 또는 게스트/공급업체)는 Microsoft 리소스에 액세스하기 위해 위험을 수정하거나 종결해야 합니다. 게스트 사용자의 경우 이 사용자 위험은 홈 테넌트에서 발생하고 정책은 리소스 테넌트(이 경우 Microsoft)에서 제공됩니다.
 
**보안 솔루션:**

* MFA 등록 게스트 사용자는 자신의 사용자 위험을 수정합니다. 게스트 사용자는 해당 홈 테넌트에서 보안 암호 변경 또는 재설정(https://aka.ms/sspr) )을 수행하여 이 작업을 수행할 수 있습니다(홈 테넌트에서 MFA 및 SSPR 필요). 보안 암호 변경 또는 재설정은 온-프레미스가 아닌 Azure AD에서 시작해야 합니다.

* 게스트 사용자는 관리자가 해당 위험을 해결하도록 합니다. 이 경우 관리자는 암호 재설정(임시 암호 생성)을 수행합니다. 이를 위해 ID 보호가 필요하지는 않습니다. 게스트 사용자의 관리자는 https://aka.ms/RiskyUsers 로 이동한 후 '암호 재설정'을 클릭할 수 있습니다.

* 게스트 사용자는 관리자에게 위험을 종결하거나 해제하도록 합니다. 마찬가지로 이를 위해서도 ID 보호는 필요하지 않습니다. 관리자는 https://aka.ms/RiskyUsers 로 이동한 후 '사용자 위험 해제'를 클릭할 수 있습니다. 그러나 관리자는 사용자 위험을 종결하기 전에 실사를 수행하여 가양성 위험 평가인지 확인해야 합니다. 그러지 않으면 조사 없이 위험 평가를 진행하지 않도록 함으로써 자기 자신과 Microsoft의 리소스를 위험에 노출하게 됩니다.

> [!NOTE]
> 액세스와 관련해서 이슈가 있는 경우 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

### <a name="implementation-specific-options"></a>구현 관련 옵션
OpenID Connect를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 표시된 것처럼 **OpenID Connect 및 OAuth 2.0** 을 선택합니다.

![갤러리에 OpenID Connect 애플리케이션 나열](./media/howto-app-gallery-listing/openid.png)

**SAML 2.0** 또는 **WS-Fed** 를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 표시된 것처럼 **SAMl 2.0/WS-Fed** 를 선택합니다.

![갤러리에 SAML 2.0 또는 WS-Fed 애플리케이션 나열](./media/howto-app-gallery-listing/saml.png)

암호 SSO를 사용하여 갤러리에 나열할 애플리케이션을 추가하려면 표시된 것처럼 **암호 SSO(사용자 이름 및 암호)** 를 선택합니다.

![갤러리에 암호 SSO 애플리케이션 나열](./media/howto-app-gallery-listing/passwordsso.png)

사용자 프로비저닝을 위해 [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0 엔드포인트를 구현하는 경우 표시된 대로 옵션을 선택합니다. 온보딩 요청에서 스키마를 제공하는 경우 [여기](../app-provisioning/export-import-provisioning-configuration.md)에 있는 지침에 따라 스키마를 다운로드하세요. 비갤러리 애플리케이션을 테스트할 때 구성한 스키마를 사용하여 갤러리 애플리케이션을 빌드합니다. 

   ![사용자 프로비저닝 요청](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>기존 목록 업데이트 또는 제거

[Microsoft Application Network Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 기존 갤러리 앱을 업데이트하거나 제거할 수 있습니다.

![갤러리에 SAML 애플리케이션 나열](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> 액세스와 관련된 이슈가 있는 경우 계정을 만드는 방법에 대한 이전 섹션을 검토합니다. 해당 방법으로 해결되지 않는 경우 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의하세요.

### <a name="timelines"></a>타임라인

갤러리에서 SAML 2.0 또는 WS-Fed 애플리케이션을 나열하는 프로세스의 타임라인은 영업일을 기준으로 7~10일입니다.

![갤러리에서 SAML 애플리케이션을 나열하기 위한 타임라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에서 OpenID Connect 애플리케이션을 나열하는 프로세스의 타임라인은 영업일을 기준으로 2~5일입니다.

![갤러리에 OpenID Connect 애플리케이션을 나열하기 위한 타임라인](./media/howto-app-gallery-listing/timeline2.png)

갤러리에 SCIM 프로비저닝 애플리케이션을 나열하는 프로세스의 타임라인은 가변적이며 다양한 요인에 따라 달라집니다. 

### <a name="escalations"></a>에스컬레이션

에스컬레이션은 [Azure AD SSO 통합 팀](mailto:SaaSApplicationIntegrations@service.microsoft.com)으로 메일을 보내주세요. 가능한 빨리 대응하겠습니다.


## <a name="step-7---join-the-microsoft-partner-network"></a>7단계 - Microsoft 파트너 네트워크에 참가
Microsoft 파트너 네트워크에서는 전용 리소스, 프로그램, 도구 및 연결에 대해 즉시 액세스할 수 있습니다. 네트워크에 참가하고 시장 출시 플랜을 만들려면 [Reach commercial customers](https://partner.microsoft.com/explore/commercial#gtm)(상업적 고객 유치)를 참조하세요.

## <a name="request-apps-by-sharing-isv-app-team-contact"></a>ISV 앱 팀 연락처를 공유하여 앱 요청

고객은 [여기](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/AppRequestsByCustomers.aspx)에서 애플리케이션 및 ISV 연락처 정보를 공유하여 애플리케이션을 요청할 수 있습니다.

![고객이 요청한 앱 타일을 표시합니다.](./media/howto-app-gallery-listing/customer-submit-request.png)

고객 요청 애플리케이션의 흐름은 다음과 같습니다.

![고객이 요청한 앱 흐름을 표시합니다.](./media/howto-app-gallery-listing/customer-request-2.png)

> [!Note]
> [액세스와 관련된 이슈](#issues-on-logging-into-portal)가 있는 경우 [Azure AD 앱 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 메일을 보내세요.

## <a name="next-steps"></a>다음 단계

* [SCIM 엔드포인트를 빌드하고 사용자 프로비저닝 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Azure AD 인증 시나리오](authentication-flows-app-scenarios.md)