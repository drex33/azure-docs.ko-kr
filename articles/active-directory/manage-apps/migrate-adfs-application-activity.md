---
title: 활동 보고서를 사용하여 AD FS 앱을 Azure Active Directory로 이동
description: AD FS(Active Directory Federation Services) 애플리케이션 활동 보고서를 사용하면 AD FS에서 Azure AD(Azure Active Directory)로 신속하게 애플리케이션을 마이그레이션할 수 있습니다. AD FS에 대한 이 마이그레이션 도구는 Azure AD와의 호환성을 식별하고 마이그레이션 지침을 제공합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: alamaral
ms.openlocfilehash: 38b7137c130925ef7c8e9c127830c33f507b7642
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046264"
---
# <a name="review-the-application-activity-report-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 작업 보고서를 검토 합니다.

많은 조직에서 AD FS(Active Directory Federation Services)를 사용하여 클라우드 애플리케이션에 Single Sign-On을 제공합니다. 인증을 위해 AD FS 애플리케이션을 Azure AD로 이동하면 비용 관리, 위험 관리, 생산성, 규정 준수, 거버넌스 측면에서 특히 상당한 이점이 있습니다. 그러나 Azure AD와 호환되는 애플리케이션을 이해하고 특정 마이그레이션 단계를 식별하는 것은 시간이 오래 걸릴 수 있습니다.

Azure Portal의 AD FS 애플리케이션 활동 보고서를 사용하여 Azure AD로 마이그레이션할 수 있는 애플리케이션을 신속하게 식별할 수 있습니다. Azure AD와의 호환성을 위해 모든 AD FS 애플리케이션을 평가하고, 문제를 확인하고, 개별 애플리케이션을 마이그레이션하기 위한 지침을 제공합니다. AD FS 애플리케이션 활동 보고서를 사용하여 다음을 수행할 수 있습니다.

* **AD FS 애플리케이션을 검색하고 마이그레이션 범위를 확인합니다.** AD FS 애플리케이션 활동 보고서에는 지난 30일 동안 활성 사용자 로그인이 있는 조직 내 모든 AD FS 애플리케이션이 나열됩니다. 이 보고서는 Azure AD로 마이그레이션하기 위한 앱 준비 상태를 나타냅니다. 이 보고서에는 Office 365와 같은 AD FS의 Microsoft 관련 신뢰 당사자가 표시되지 않습니다. 이름이 'urn:federation: MicrosoftOnline'인 신뢰 당사자를 예로 들 수 있습니다.

* **애플리케이션의 마이그레이션 우선 순위를 지정합니다.** 애플리케이션 마이그레이션의 중요도를 결정하는 데 도움이 되도록 지난 1, 7 또는 30일 동안 애플리케이션에 로그인한 고유 사용자 수를 가져옵니다.
* **마이그레이션 테스트를 실행하고 문제를 해결합니다.** 보고 서비스는 테스트를 자동으로 실행하여 애플리케이션을 마이그레이션할 준비가 되었는지 여부를 확인합니다. 결과는 AD FS 애플리케이션 활동 보고서에 마이그레이션 상태로 표시됩니다. AD FS 구성이 Azure AD 구성과 호환되지 않는 경우 Azure AD에서 구성 문제를 해결하는 방법에 대한 구체적인 지침을 얻을 수 있습니다.

전역 관리자, 보고서 읽기 권한자, 보안 읽기 권한자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자와 같은 관리 역할이 할당된 사용자는 AD FS 애플리케이션 작업 데이터를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 조직에서 애플리케이션에 액세스하려면 현재, AD FS를 사용해야 합니다.
* Azure AD 테넌트에서 Azure AD Connect Health를 사용하도록 설정해야 합니다.
* AD FS 에이전트에 대한 Azure AD Connect Health를 설치해야 합니다.
* [Azure AD Connect Health에 대한 자세한 정보](../hybrid/how-to-connect-health-adfs.md)
* [Azure AD Connect Health 설정 시작 및 AD FS 에이전트 설치](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT]
>Azure AD Connect Health를 설치한 후에 예상되는 애플리케이션 중 일부만 표시되는 몇 가지 이유가 있습니다. AD FS 애플리케이션 활동 보고서는 지난 30일 동안 사용자 로그인이 있는 AD FS 신뢰 당사자만 보여 줍니다. 이 보고서에는 Office 365와 같은 Microsoft 관련 신뢰 당사자가 표시되지 않습니다.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>마이그레이션할 수 있는 AD FS 애플리케이션 검색

AD FS 애플리케이션 작업 보고서는 Azure AD **사용량 & 인사이트** 보고의 Azure Portal에서 사용할 수 있습니다. AD FS 애플리케이션 활동 보고서는 각 AD FS 애플리케이션을 분석하여 있는 그대로 마이그레이션할 수 있는지 아니면 추가 검토가 필요한지를 결정합니다.

1. AD FS 애플리케이션 작업 데이터에 대한 액세스 권한이 있는 관리자 역할(전역 관리자, 보고서 읽기 권한자, 보안 읽기 권한자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자)을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 로 이동한 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.

3. **작업** 에서 **사용량 및 인사이트** 를 선택하고 **AD FS 애플리케이션 활동** 을 선택하여 조직의 모든 AD FS 애플리케이션 목록을 엽니다.

   ![AD FS 애플리케이션 활동](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 애플리케이션 AD FS 활동 목록의 각 애플리케이션에 대해 **마이그레이션 상태** 를 확인합니다.

   * **마이그레이션할 준비 완료** 는 AD FS 애플리케이션 구성이 Azure AD에서 완전히 지원되며 그대로 마이그레이션할 수 있음을 의미합니다.

   * **검토 필요** 는 애플리케이션의 설정 중 일부를 Azure AD로 마이그레이션할 수 있지만 그대로 마이그레이션할 수 없는 설정을 검토해야 한다는 것을 의미합니다.

   * **추가 단계 필요** 는 Azure AD가 애플리케이션의 설정 중 일부를 지원하지 않으므로 현재 상태에서 애플리케이션을 마이그레이션할 수 없음을 의미합니다.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>애플리케이션의 마이그레이션 준비 상태 평가

1. AD FS 애플리케이션 활동 목록에서 **마이그레이션 상태** 열의 상태를 클릭하여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 전달된 구성 테스트에 대한 요약이 표시됩니다.

   ![마이그레이션 세부 정보](media/migrate-adfs-application-activity/migration-details.png)

2. 추가 마이그레이션 규칙 세부 정보를 열려면 메시지를 클릭합니다. 테스트된 속성의 전체 목록은 아래 [AD FS 애플리케이션 구성 테스트](#ad-fs-application-configuration-tests) 표를 참조하세요.

   ![마이그레이션 규칙 세부 정보](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 애플리케이션 구성 테스트

다음 표에는 AD FS 애플리케이션에서 수행되는 모든 구성 테스트가 나열되어 있습니다.

|결과  |성공/경고/실패  |Description  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> AdditionalAuthentication에 대해 마이그레이션할 수 없는 하나 이상의 규칙이 검색되었습니다.       | 성공/경고          | 신뢰 당사자에게 MFA(Multi-Factor Authentication)를 요구하는 규칙이 있습니다. Azure AD로 이동하려면 이러한 규칙을 조건부 액세스 정책으로 변환합니다. 온-프레미스 MFA를 사용하는 경우 Azure AD MFA로 이동하는 것이 좋습니다. [조건부 액세스에 대해 자세히 알아봅니다](../authentication/concept-mfa-howitworks.md).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> 신뢰 당사자가 AdditionalWSFedEndpoint를 true로 설정했습니다.       | 성공/실패          | AD FS 신뢰 당사자는 여러 WS-Fed 어설션 엔드포인트를 허용합니다.현재, Azure AD는 하나만 지원합니다.이 결과가 마이그레이션을 차단하는 시나리오가 발생하는 경우 [저희에게 알려주세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> 신뢰 당사자가 AllowedAuthenticationClassReferences를 설정했습니다.       | 성공/실패          | AD FS에서 이 설정을 사용하면 애플리케이션이 특정 인증 유형만 허용하도록 구성되어 있는지 여부를 지정할 수 있습니다. 조건부 액세스를 사용하여 이 기능을 구현하는 것이 좋습니다. 이 결과가 마이그레이션을 차단하는 시나리오가 발생하는 경우 [저희에게 알려주세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).  [조건부 액세스에 대해 자세히 알아보기](../authentication/concept-mfa-howitworks.md).         |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 성공/실패          | AD FS에서 이 설정을 사용하면 애플리케이션이 SSO 쿠키를 무시하도록 구성되었는지 여부를 지정하고 **항상 인증 확인** 을 지정할 수 있습니다. Azure AD에서 조건부 액세스 정책을 통해 인증 세션을 관리하여 유사한 동작을 달성할 수 있습니다. [조건부 액세스를 사용하여 인증 세션 관리를 구성하는 방법에 대해 자세히 알아보세요](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Test-ADFSRPAutoUpdateEnabled <br> 신뢰 당사자가 AutoUpdateEnabled를 true로 설정했습니다.       | 성공/경고          | AD FS에서 이 설정을 사용하면 AD FS를 페더레이션 메타데이터 내의 변경 내용에 따라 애플리케이션을 자동으로 업데이트하도록 구성할지 여부를 지정할 수 있습니다. Azure AD는 현재 이러한 구성을 지원하지 않지만 애플리케이션을 Azure AD로 마이그레이션하는 것을 차단하면 안 됩니다.           |
|Test-ADFSRPClaimsProviderName <br> 신뢰 당사자가 여러 ClaimsProvider를 사용하도록 설정했습니다.       | 성공/실패          | 이 AD FS의 이 설정은 신뢰 당사자가 클레임을 수락하는 ID 공급자를 호출합니다. Azure AD에서는 Azure AD B2B를 사용하여 외부 공동 작업을 사용하도록 설정할 수 있습니다. [Azure AD B2B에 대해 자세히 알아봅니다](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | 성공/실패          | 애플리케이션에 사용자 지정 위임 권한 부여 규칙이 정의되어 있습니다. 이것은 Azure AD에서 OpenID Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용하여 지원하는 WS-Trust 개념입니다. [Microsoft ID 플랫폼에 대해 자세히 알아봅니다](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPImpersonationAuthorizationRules       | 성공/경고          | 애플리케이션에 사용자 지정 가장 규칙이 정의되어 있습니다.이것은 Azure AD에서 OpenID Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용하여 지원하는 WS-Trust 개념입니다. [Microsoft ID 플랫폼에 대해 자세히 알아봅니다](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> IssuanceAuthorization에 대해 마이그레이션할 수 없는 하나 이상의 규칙이 검색되었습니다.       | 성공/경고          | 애플리케이션에 AD FS에 정의된 사용자 지정 발급 권한 부여 규칙이 있습니다.Azure AD는 Azure AD 조건부 액세스와 함께 이 기능을 지원합니다. [조건부 액세스에 대해 자세히 알아봅니다](../conditional-access/overview.md). <br> 애플리케이션에 할당된 사용자 또는 그룹을 기준으로 애플리케이션에 대한 액세스를 제한할 수도 있습니다. [애플리케이션에 액세스할 사용자 및 그룹을 할당하는 방법에 대해 자세히 알아봅니다](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> IssuanceTransform에 대해 마이그레이션할 수 없는 하나 이상의 규칙이 검색되었습니다.       | 성공/경고          | 애플리케이션에 AD FS에 정의된 사용자 지정 발급 변환 규칙이 있습니다. Azure AD는 토큰에 발급된 클레임 사용자 지정을 지원합니다. 자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.           |
|Test-ADFSRPMonitoringEnabled <br> 신뢰 당사자가 MonitoringEnabled를 true로 설정했습니다.       | 성공/경고          | AD FS에서 이 설정을 사용하면 AD FS를 페더레이션 메타데이터 내의 변경 내용에 따라 애플리케이션을 자동으로 업데이트하도록 구성할지 여부를 지정할 수 있습니다. Azure AD는 현재 이러한 구성을 지원하지 않지만 애플리케이션을 Azure AD로 마이그레이션하는 것을 차단하면 안 됩니다.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 성공/경고          | AD FS는 SAML 토큰의 NotBefore 및 NotOnOrAfter 시간을 기준으로 시간차를 허용합니다. Azure AD에서는 기본적으로 이 기능이 처리됩니다.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> 신뢰 당사자가 RequestMFAFromClaimsProviders를 true로 설정했습니다.       | 성공/경고          | AD FS의 이 설정은 사용자가 다른 클레임 공급자에서 온 경우 MFA에 대한 동작을 결정합니다. Azure AD에서는 Azure AD B2B를 사용하여 외부 공동 작업을 사용하도록 설정할 수 있습니다. 그런 다음, 조건부 액세스 정책을 적용하여 게스트 액세스를 보호할 수 있습니다. [Azure AD B2B](../external-identities/what-is-b2b.md) 및 [조건부 액세스](../conditional-access/overview.md)에 대해 자세히 알아봅니다.          |
|Test-ADFSRPSignedSamlRequestsRequired <br> 신뢰 당사자가 SignedSamlRequestsRequired를 true로 설정했습니다.       | 성공/실패          | 애플리케이션은 SAML 요청에서 서명을 확인하도록 AD FS에서 구성됩니다. Azure AD는 서명된 SAML 요청을 수락합니다. 그러나 서명을 확인하지 않습니다. Azure AD에는 악의적인 호출로부터 보호하는 여러 방법이 있습니다. 예를 들어, Azure AD는 애플리케이션에 구성된 회신 URL을 사용하여 SAML 요청의 유효성을 검사합니다. Azure AD는 애플리케이션에 대해 구성된 회신 URL에만 토큰을 보냅니다. 이 결과가 마이그레이션을 차단하는 시나리오가 발생하는 경우 [저희에게 알려주세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 성공/경고         | 애플리케이션은 사용자 지정 토큰 수명에 대해 구성됩니다. AD FS 기본값은 1시간입니다.Azure AD는 조건부 액세스와 함께 이 기능을 지원합니다. 자세히 알아보려면 [조건부 액세스를 사용하여 인증 세션 관리 구성](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조하세요.          |
|신뢰 당사자가 클레임을 암호화하도록 설정되어 있습니다. Azure AD에서 지원됩니다.       | 합격          | Azure AD를 사용하여 애플리케이션에 전송된 토큰을 암호화할 수 있습니다. 자세히 알아보려면 [Azure AD SAML 토큰 암호화 구성](./howto-saml-token-encryption.md)을 참조하세요.          |
|EncryptedNameIdRequiredCheckResult      | 성공/실패          | 애플리케이션은 SAML 토큰의 nameID 클레임을 암호화하도록 구성됩니다.Azure AD를 사용하여 애플리케이션에 전송된 전체 토큰을 암호화할 수 있습니다.특정 클레임의 암호화는 아직 지원되지 않습니다. 자세히 알아보려면 [Azure AD SAML 토큰 암호화 구성](./howto-saml-token-encryption.md)을 참조하세요.         |

## <a name="check-the-results-of-claim-rule-tests"></a>클레임 규칙 테스트의 결과 확인

AD FS에서 애플리케이션에 대한 클레임 규칙을 구성한 경우 이 환경은 모든 클레임 규칙에 대한 세부적인 분석을 제공합니다. Azure AD로 이동할 수 있는 클레임 규칙과 추가 검토가 필요한 클레임 규칙을 확인할 수 있습니다.

1. AD FS 애플리케이션 활동 목록에서 **마이그레이션 상태** 열의 상태를 클릭하여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 전달된 구성 테스트에 대한 요약이 표시됩니다.

2. **마이그레이션 규칙 세부 정보** 페이지에서 결과를 확장하여 잠재적인 마이그레이션 문제에 대한 세부 정보를 표시하고 추가 지침을 가져옵니다. 테스트된 모든 클레임 규칙의 자세한 목록은 아래의 [클레임 규칙 테스트 결과 확인](#check-the-results-of-claim-rule-tests) 표를 참조하세요.

   아래 예제에서는 IssuanceTransform 규칙에 대한 마이그레이션 규칙 정보를 보여 줍니다. 애플리케이션을 Azure AD로 마이그레이션하기 전에 검토하고 해결해야 하는 클레임의 특정 부분을 나열합니다.

   ![마이그레이션 규칙 세부 정보 추가 지침](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>클레임 규칙 테스트

다음 표에는 AD FS 애플리케이션에서 수행되는 모든 클레임 규칙 테스트가 나열되어 있습니다.

|속성  |Description  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 조건문은 정규식을 사용하여 클레임이 특정 패턴과 일치하는지 여부를 평가합니다.Azure AD에서 유사한 기능을 얻기 위해 IfEmpty(), StartWith(), Contains() 등의 미리 정의된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.          |
|UNSUPPORTED_CONDITION_CLASS      | 조건문에는 발급 문을 실행하기 전에 평가해야 하는 여러 조건이 있습니다.Azure AD는 클레임의 변환 함수를 사용하여 이 기능을 지원합니다. 이 변환 함수에서는 여러 클레임 값을 평가할 수 있습니다.자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.          |
|UNSUPPORTED_RULE_TYPE      | 클레임 규칙을 인식할 수 없습니다. Azure AD에서 클레임을 구성하는 방법에 대한 자세한 내용은 [엔터프라이즈 애플리케이션에 대해 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 조건문이 Azure AD에서 지원되지 않는 발급자를 사용합니다.현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소의 클레임을 제공하지 않습니다. 이로 인해 애플리케이션을 Azure AD로 마이그레이션하지 못할 경우 [저희에게 알려주세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).         |
|UNSUPPORTED_CONDITION_FUNCTION      | 조건문은 집계 함수를 사용하여 일치하는 항목 수에 관계 없이 단일 클레임을 발급하거나 추가합니다.Azure AD에서는 사용자의 특성을 평가하여 IfEmpty(), StartWith(), Contains() 등의 함수를 사용하여 클레임에 사용할 값을 결정할 수 있습니다.자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.          |
|RESTRICTED_CLAIM_ISSUED      | 조건문은 Azure AD에서 제한된 클레임을 사용합니다. 제한된 클레임을 발급할 수 있지만 원본을 수정하거나 변환을 적용할 수 없습니다. 자세한 내용은 [Azure AD의 특정 앱에 대한 토큰에서 내보낸 클레임 사용자 지정](../develop/active-directory-claims-mapping.md)을 참조하세요.          |
|EXTERNAL_ATTRIBUTE_STORE      | 발급문에서 Active Directory와는 다른 특성 저장소를 사용합니다. 현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소의 클레임을 제공하지 않습니다. 이 결과로 인해 애플리케이션을 Azure AD로 마이그레이션하지 못할 경우 [저희에게 알려주세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).          |
|UNSUPPORTED_ISSUANCE_CLASS      | 발급문은 ADD를 사용하여 들어오는 클레임 세트에 클레임을 추가합니다. Azure AD에서는 여러 클레임 변환으로 구성될 수 있습니다.자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-claims-mapping.md)을 참조하세요.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 발급문은 정규식을 사용하여 내보낼 클레임 값을 변환합니다.Azure AD에서 유사한 기능을 얻기 위해 Extract(), Trim(), ToLower 등의 미리 정의된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.          |

## <a name="troubleshooting"></a>문제 해결

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>보고서에서 내 AD FS 애플리케이션을 일부만 볼 수 있음

 Azure AD Connect Health를 설치했지만 이를 설치하라는 메시지가 계속 표시되거나, 보고서에 AD FS 애플리케이션이 일부만 표시되는 경우 활성 AD FS 애플리케이션이 없거나 AD FS 애플리케이션이 Microsoft 애플리케이션일 수 있습니다.

 AD FS 애플리케이션 활동 보고서에는 지난 30일 동안 활성 사용자 로그인이 있는 조직 내 모든 AD FS 애플리케이션이 나열됩니다. 이 보고서에는 Office 365와 같은 AD FS의 Microsoft 관련 신뢰 당사자가 표시되지 않습니다. 예를 들어, 이름이 'urn:federation:MicrosoftOnline', 'microsoftonline', 'microsoft:winhello:cert:prov:server'인 신뢰 당사자가 목록에 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [비디오: AD FS 활동 보고서를 사용하여 애플리케이션을 마이그레이션하는 방법](https://www.youtube.com/watch?v=OThlTA239lU)
* [Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
* [앱에 대한 액세스 관리](what-is-access-management.md)
* [Azure AD Connect 페더레이션](../hybrid/how-to-connect-fed-whatis.md)
