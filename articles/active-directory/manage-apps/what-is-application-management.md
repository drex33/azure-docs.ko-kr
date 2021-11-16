---
title: 애플리케이션 관리란?
description: Azure Active Directory에서 애플리케이션의 수명 주기를 관리하는 방법에 대한 개요입니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/22/2021
ms.author: davidmu
ms.reviewer: sureshja, napuri
ms.openlocfilehash: 3b1e814def005283fb08ab7eeb3c23a8a38e3ee2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257401"
---
# <a name="what-is-application-management-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션 관리란?

Azure AD(Azure Active Directory)의 애플리케이션 관리는 클라우드에서 애플리케이션을 만들고, 구성하고, 관리하고, 모니터링하는 프로세스입니다. [애플리케이션](../develop/app-objects-and-service-principals.md)을 Azure AD 테넌트에 등록하는 경우 해당 애플리케이션에 할당된 사용자는 애플리케이션에 안전하게 액세스할 수 있습니다. 여러 유형의 애플리케이션을 Azure AD에 등록할 수 있습니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../develop/v2-app-types.md)을 참조하세요.

이 문서에서는 애플리케이션의 수명 주기를 관리하는 다음과 같은 중요한 측면에 대해 알아봅니다.

- **개발, 추가 또는 연결** – 사용자 애플리케이션을 개발하는지, 미리 통합된 애플리케이션을 사용하는지 또는 온-프레미스 애플리케이션에 연결하는지에 따라 다른 경로를 따릅니다.
- **액세스 관리** – 액세스는 SSO(Single Sign-On)를 사용하고, 리소스를 할당하고, 액세스 권한을 부여하고 동의하는 방식을 정의하고, 자동화된 프로비저닝을 사용하여 관리할 수 있습니다.
- **속성 구성** – 애플리케이션에 로그인하기 위한 요구 사항과 애플리케이션이 사용자 포털에 표시되는 방식을 구성합니다.
- **애플리케이션 보안** – 권한 구성, MFA(다단계 인증), 조건부 액세스, 토큰 및 인증서를 관리합니다.
- **통제 및 모니터링** – 권한 관리, 보고 및 모니터링 리소스를 사용하여 상호 작용을 관리하고 작업을 검토합니다.
- **정리** – 애플리케이션이 더 이상 필요하지 않은 경우 해당 애플리케이션에 대한 액세스를 제거하고 애플리케이션을 삭제하여 테넌트를 정리합니다.

## <a name="develop-add-or-connect"></a>개발, 추가 또는 연결

Azure AD에서 애플리케이션을 관리하는 방법에는 여러 가지가 있습니다. 애플리케이션 관리를 시작하는 가장 쉬운 방법은 Azure AD 갤러리에서 사전 통합된 애플리케이션을 사용하는 것입니다. 사용자 고유의 애플리케이션을 개발하고 Azure AD에 등록하는 것은 한 가지 옵션이며, 온-프레미스 애플리케이션을 계속 사용할 수도 있습니다.

다음 이미지는 이러한 애플리케이션이 Azure AD와 상호 작용하는 방법을 보여 줍니다.

:::image type="content" source="media/what-is-application-management/app-management-overview.png" alt-text="사용자가 직접 개발한 앱, 사전 통합된 앱 및 온-프레미스 앱을 엔터프라이즈 앱으로 사용할 수 있는 방법을 보여 주는 다이어그램":::

### <a name="pre-integrated-applications"></a>사전 통합된 애플리케이션

많은 애플리케이션은 이미 사전 통합되어 있으며(위의 이미지에 "클라우드 애플리케이션"으로 표시됨) 최소한의 노력으로 설정할 수 있습니다. Azure AD 갤러리의 각 애플리케이션에 대해 [애플리케이션을 구성](../saas-apps/tutorial-list.md)하는 데 필요한 단계를 보여 주는 문서를 사용할 수 있습니다. 갤러리에서 Azure AD 테넌트에 애플리케이션을 추가하는 방법에 대한 간단한 예제는 [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md)를 참조하세요.

### <a name="your-own-applications"></a>사용자 고유의 애플리케이션

고유한 비즈니스 애플리케이션을 개발하는 경우 Azure AD에 등록하여 테넌트가 제공하는 보안 기능을 활용할 수 있습니다. **앱 등록** 에서 애플리케이션을 등록하거나 **엔터프라이즈 애플리케이션** 에서 새 애플리케이션을 추가할 때 **사용자 고유의 애플리케이션 만들기** 링크를 사용하여 등록할 수 있습니다. Azure AD와의 통합을 위해 애플리케이션에서 [인증](../develop/authentication-vs-authorization.md)을 구현하는 방법을 고려합니다. 

갤러리를 통해 애플리케이션을 사용할 수 있게 하려면 [요청을 제출하여 추가](../develop/v2-howto-app-gallery-listing.md)할 수 있습니다.


### <a name="on-premises-applications"></a>온-프레미스 애플리케이션

온-프레미스 애플리케이션을 계속 사용하지만 Azure AD에서 제공하는 기능을 활용하려는 경우 [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy.md)를 사용하여 Azure AD에 연결합니다. 외부에서 온-프레미스 애플리케이션을 게시하려는 경우 애플리케이션 프록시를 구현할 수 있습니다. 이렇게 하면 내부 애플리케이션에 액세스해야 하는 원격 사용자가 안전하게 액세스할 수 있습니다.

## <a name="manage-access"></a>액세스 관리

애플리케이션에 대한 [액세스를 관리](what-is-access-management.md)하려면 다음 질문에 대답합니다.

- 애플리케이션에 대해 액세스 권한을 부여하고 동의하는 방법은 무엇인가요?
- 애플리케이션이 SSO를 지원하나요?
- 애플리케이션에 어떤 사용자, 그룹 및 소유자를 할당해야 하나요? 
- 애플리케이션을 지원하는 다른 ID 공급자가 있나요?
- 사용자 ID 및 역할 프로비저닝을 자동화하는 데 도움이 되나요?

### <a name="access-and-consent"></a>액세스 및 동의

[사용자 동의 설정을 관리](configure-user-consent.md)하여 사용자가 애플리케이션 또는 서비스에서 사용자 프로필 및 조직 데이터에 액세스할 수 있도록 허용할지 여부를 선택할 수 있습니다. 애플리케이션에 액세스 권한이 부여되면 사용자는 Azure AD와 통합된 애플리케이션에 로그인할 수 있으며, 애플리케이션은 조직의 데이터에 액세스하여 풍부한 데이터 기반 환경을 제공할 수 있습니다.

사용자는 애플리케이션이 요청하는 사용 권한에 동의할 수 없는 경우가 많습니다. 사용자가 근거를 제공하고 관리자의 애플리케이션 검토 및 승인을 요청할 수 있도록 [관리자 동의 워크플로](configure-admin-consent-workflow.md)를 구성합니다.

관리자는 [테넌트 전체 애플리케이션 사용 권한 관리자 동의를 부여](grant-admin-consent.md)할 수 있습니다. 일반 사용자에게 허용되지 않는 권한이 애플리케이션에 필요하고 조직에서 자체 검토 프로세스를 구현할 수 있도록 하려면 테넌트 전체 관리자 동의가 필요합니다. 동의를 부여하기 전에 애플리케이션에서 요청하는 권한을 항상 신중하게 검토합니다. 애플리케이션에 테넌트 전체 관리자 동의가 부여되면 사용자 할당을 요구하도록 구성되지 않은 한 모든 사용자가 애플리케이션에 로그인할 수 있습니다.

### <a name="single-sign-on"></a>Single Sign-On

애플리케이션에서 SSO를 구현하는 것이 좋습니다. SSO에 대한 대부분의 애플리케이션을 수동으로 구성할 수 있습니다. Azure AD에서 가장 자주 사용되는 옵션은 [SAML 기반 SSO 및 OpenID Connect 기반 SSO](../develop/active-directory-v2-protocols.md)입니다. 시작하기 전에 SSO에 대한 요구 사항 및 [배포 계획](plan-sso-deployment.md) 방법을 이해해야 합니다. Azure AD 테넌트에서 엔터프라이즈 애플리케이션에 대해 SAML 기반 SSO를 구성하는 방법에 대한 간단한 예제는 [빠른 시작: 엔터프라이즈 애플리케이션에 Single Sign-On 사용](add-application-portal-setup-sso.md)을 참조하세요.

### <a name="user-group-and-owner-assignment"></a>사용자, 그룹 및 소유자 할당

기본적으로 모든 사용자는 엔터프라이즈 애플리케이션에 할당되지 않아도 해당 애플리케이션에 액세스할 수 있습니다. 그러나 사용자 집합에 애플리케이션을 할당하려는 경우 애플리케이션에 사용자 할당이 필요합니다. 사용자 계정을 만들고 애플리케이션에 할당하는 방법에 대한 간단한 예제는 [빠른 시작: 사용자 계정 만들기 및 할당](add-application-portal-assign-users.md)을 참조하세요. 

구독에 포함된 경우 그룹 소유자에게 지속적 액세스 관리를 위임할 수 있도록 [애플리케이션에 그룹을 할당](assign-user-or-group-access-portal.md)합니다. 

[소유자 할당](assign-app-owners.md)은 애플리케이션에 대한 Azure AD 구성의 모든 측면을 관리하는 기능을 부여하는 간단한 방법입니다. 소유자는 애플리케이션의 조직별 구성을 관리할 수 있습니다.

### <a name="automate-provisioning"></a>프로비저닝 자동화

Azure AD에서 [애플리케이션 프로비저닝](../app-provisioning/user-provisioning.md)은 사용자가 액세스해야 하는 애플리케이션에서 사용자 ID와 역할을 자동으로 만드는 것을 의미합니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다.

### <a name="identity-providers"></a>ID 공급자

Azure AD와 상호 작용하려는 ID 공급자가 있나요? [홈 영역 검색](home-realm-discovery-policy.md)은 Azure AD에서 사용자가 로그인할 때 사용자가 인증을 받아야 하는 ID 공급자를 결정하는 데 사용할 수 있는 구성을 제공합니다.

### <a name="user-portals"></a>사용자 포털

Azure AD는 조직의 사용자에게 애플리케이션을 배포하는 사용자 지정 가능한 방법을 제공합니다. 예를 들어 [내 앱 포털 또는 Microsoft 365 애플리케이션 시작 관리자](end-user-experiences.md)가 있습니다. 내 앱을 사용하면 사용자가 작업을 시작하는 공간과 액세스 권한이 있는 모든 애플리케이션을 찾을 수 있습니다. 애플리케이션의 관리자는 [조직의 사용자가 내 앱을 사용하는 방식을 계획](my-apps-deployment-plan.md)해야 합니다.

## <a name="configure-properties"></a>속성 구성

Azure AD 테넌트에 애플리케이션을 추가하는 경우 사용자가 로그인할 수 있는 방법에 영향을 주는 [속성을 구성](add-application-portal-configure.md)할 수 있습니다. 로그인할 수 있는 기능을 사용하거나 사용하지 않도록 설정할 수 있으며 사용자 할당이 필요할 수 있습니다. 애플리케이션의 표시 여부, 애플리케이션을 나타내는 로고 및 애플리케이션에 대한 정보를 확인할 수도 있습니다.

## <a name="secure-the-application"></a>애플리케이션 보안

엔터프라이즈 애플리케이션의 보안을 유지하는 데 도움이 되는 몇 가지 방법이 있습니다. 예를 들어, [테넌트 액세스를 제한](tenant-restrictions.md)하고, [표시 유형, 데이터 및 분석을 관리](cloud-app-security.md)하고, [하이브리드 액세스](secure-hybrid-access.md)를 제공할 수 있습니다. 엔터프라이즈 애플리케이션을 안전하게 유지하려면 권한, MFA, 조건부 액세스, 토큰 및 인증서의 구성을 관리해야 합니다.

### <a name="permissions"></a>권한

주기적으로 [애플리케이션 또는 서비스에 부여된 사용 권한을 검토](manage-application-permissions.md)하고 필요한 경우 관리하는 것이 중요합니다. 의심스러운 활동이 있는지 주기적으로 평가하여 애플리케이션에 대해 적절한 액세스만 허용해야 합니다.

[권한 분류](configure-permission-classifications.md)를 사용하면 조직의 정책 및 위험 평가에 따라 서로 다른 권한에 적용되는 영향을 식별할 수 있습니다. 예를 들어 동의 정책에서 권한 분류를 사용하여 사용자가 동의할 수 있는 권한 세트를 식별할 수 있습니다.

### <a name="multifactor-authentication-and-conditional-access"></a>다단계 인증 및 조건부 액세스

Azure AD MFA는 데이터와 애플리케이션에 대한 액세스를 보호하고, 두 번째 형태의 인증을 사용하여 다른 보안 계층을 제공하는 데 도움이 됩니다. 2단계 인증에 사용할 수 있는 여러 가지 방법이 있습니다. 시작하기 전에 조직의 [애플리케이션에 대한 MFA 배포를 계획](../authentication/howto-mfa-getstarted.md)합니다.

조직에서는 [조건부 액세스](../conditional-access/overview.md)와 MFA를 사용하여 솔루션을 특정 요구 사항에 맞게 만들 수 있습니다. 관리자는 조건부 액세스 정책을 사용하여 특정 [애플리케이션, 작업 또는 인증 컨텍스트](../conditional-access/concept-conditional-access-cloud-apps.md)에 제어를 할당할 수 있습니다.

### <a name="tokens-and-certificates"></a>토큰 및 인증서

사용되는 프로토콜에 따라 Azure AD의 인증 흐름에서 다양한 유형의 보안 토큰을 사용합니다. 예를 들어 [SAML 토큰](../develop/reference-saml-tokens.md)은 SAML 프로토콜에 사용되고 [ID 토큰](../develop/id-tokens.md) 및 [액세스 토큰](../develop/access-tokens.md)은 OpenID Connect 프로토콜에 사용됩니다. 토큰은 Azure AD 및 특정 표준 알고리즘에서 생성된 고유한 인증서로 서명됩니다. 

[토큰을 암호화](howto-saml-token-encryption.md)하여 보다 강력한 보안을 제공할 수 있습니다. 애플리케이션에 [허용되는 역할](../develop/howto-add-app-roles-in-azure-ad-apps.md)을 포함하는 정보를 토큰에 관리할 수도 있습니다.

Azure AD에서는 기본적으로 [SHA-256 알고리즘](certificate-signing-options.md)을 사용하여 SAML 응답에 서명합니다. 애플리케이션에 SHA-1이 필요하지 않으면 SHA-256를 사용합니다. [인증서의 수명을 관리](manage-certificates-for-federated-single-sign-on.md)하는 프로세스를 설정합니다. 서명 인증서의 최대 수명은 3년입니다. 인증서 만료에 따른 중단 문제를 예방하거나 최소화하려면 역할 및 이메일 배포 목록을 사용하여 인증서 관련 변경 알림이 면밀히 모니터링되는지 확인합니다. 

## <a name="govern-and-monitor"></a>통제 및 모니터링

Azure AD의 [권한 관리](../governance/entitlement-management-scenarios.md)를 통해 애플리케이션 및 관리자, 카탈로그 소유자, 액세스 패키지 관리자, 승인자 및 요청자 간의 상호 작용을 관리할 수 있습니다.

Azure AD 보고 및 모니터링 솔루션은 법적, 보안 및 운영 요구 사항과 기존 환경 및 프로세스에 따라 달라집니다. Azure AD에서 유지 관리되는 여러 로그가 있으며, 애플리케이션에 대한 최상의 환경을 유지하기 위해 [배포 보고 및 모니터링을 계획](../reports-monitoring/plan-monitoring-and-reporting.md)해야 합니다.

## <a name="clean-up"></a>정리

애플리케이션에 대한 액세스 권한을 정리할 수 있습니다. 예를 들어, [사용자 액세스 권한을 제거](methods-for-removing-user-access.md)할 수 있습니다. [사용자가 로그인하는 방법을 사용하지 않도록 설정](disable-user-sign-in-portal.md)할 수도 있습니다. 마지막으로, 조직에 더 이상 필요하지 않은 애플리케이션을 삭제할 수 있습니다. Azure AD 테넌트에서 엔터프라이즈 애플리케이션을 삭제하는 방법에 대한 간단한 예제는 [빠른 시작: 엔터프라이즈 애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 먼저 [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md)를 사용하여 첫 번째 엔터프라이즈 애플리케이션을 추가하세요.