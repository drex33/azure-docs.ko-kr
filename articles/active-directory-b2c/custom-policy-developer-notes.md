---
title: 사용자 흐름 및 사용자 지정 정책에 대한 개발자 정보
titleSuffix: Azure AD B2C
description: 사용자 흐름 및 사용자 지정 정책으로 Azure AD B2C를 구성 및 유지 관리하는 개발자를 위한 정보입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 33389224bcc4abf05ffbb261e23409eb95896781
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220863"
---
# <a name="developer-notes-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대한 개발자 정보

Azure Active Directory B2C [사용자 흐름 및 사용자 지정 정책](user-flow-overview.md)이 일반 공급됩니다. Azure AD B2C 기능은 지속적으로 개발 중이므로 대부분의 기능이 일반 제공되더라도 일부 기능은 소프트웨어 릴리스 주기에서 서로 다른 단계에 있습니다. 이 문서에서는 Azure AD B2C의 누적 개선 사항에 대해 설명하고 기능 가용성을 지정합니다.

## <a name="terms-for-features-in-public-preview"></a>공개 미리 보기 기능에 대한 사용 조건

- 공개 미리 보기 기능은 평가 목적으로만 사용하는 것이 좋습니다.
- [서비스 수준 계약(SLA)](https://azure.microsoft.com/support/legal/sla/active-directory-b2c)은 공개 미리 보기 기능에 적용되지 않습니다.
- 공개 미리 보기 기능에 대한 지원 요청은 정식 지원 채널을 통해 제출할 수 있습니다.

## <a name="user-flows"></a>사용자 흐름

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
| 이메일 및 암호로 [등록 및 로그인](add-sign-up-and-sign-in-policy.md)합니다. | GA | GA| |
| 사용자 이름 및 암호로 [등록 및 로그인](add-sign-up-and-sign-in-policy.md)합니다.| GA | GA | |
| [프로필 편집 흐름](add-profile-editing-policy.md) | GA | GA | |
| [셀프 서비스 암호 재설정](add-password-reset-policy.md) | GA| GA| |
| [암호 재설정 강제 적용](force-password-reset.md) | GA | 해당 없음 | |
| [전화 등록 및 로그인](phone-authentication-user-flows.md) | GA | GA | |
| [조건부 액세스 ID 보호](conditional-access-user-flow.md) | GA | GA | SAML 애플리케이션에는 사용할 수 없음 |

## <a name="oauth-20-application-authorization-flows"></a>OAuth 2.0 애플리케이션 권한 부여 흐름

다음 표에는 Azure AD B2C와 통합할 수 있는 OAuth 2.0 및 OpenId Connect 애플리케이션 인증 흐름이 요약되어 있습니다.

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
[인증 코드](authorization-code-flow.md) | GA | GA | 사용자가 웹 애플리케이션에 로그인할 수 있습니다. 웹 애플리케이션은 권한 부여 코드를 받습니다. 인증 코드는 웹 API를 호출하는 토큰을 얻는 데 사용됩니다.|
[PKCE가 있는 권한 부여 코드](authorization-code-flow.md)| GA | GA | 사용자가 모바일 및 단일 페이지 애플리케이션에 로그인할 수 있습니다. 애플리케이션은 PKCE(코드 교환용 증명 키)를 사용하여 인증 코드를 받습니다. 인증 코드는 웹 API를 호출하는 토큰을 얻는 데 사용됩니다.  |
[클라이언트 자격 증명 부여](https://tools.ietf.org/html/rfc6749#section-4.4)| GA | GA | 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다.  <br />  <br />  Azure AD B2C 테넌트에서 이 기능을 사용하려면 Azure AD B2C 테넌트의 Azure AD 엔드포인트를 사용합니다. 자세한 내용은 [OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 참조하세요. 이 흐름은 Azure AD B2C [사용자 흐름 또는 사용자 지정 정책](user-flow-overview.md) 설정을 사용하지 않습니다. |
[디바이스 권한 부여](https://tools.ietf.org/html/rfc8628)| 해당 없음 | 해당 없음 | 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력이 제한된 디바이스에 로그인할 수 있습니다.  |
[암시적 흐름](implicit-flow-single-page-application.md) | GA | GA |  사용자가 단일 페이지 애플리케이션에 로그인할 수 있습니다. 앱은 백 엔드 서버 자격 증명 교환을 수행하지 않고 토큰을 직접 가져옵니다.|
[On-behalf-of](../active-directory/develop/v2-oauth2-on-behalf-of-flow.md)| 해당 없음 | 해당 없음 | 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 합니다. <br />  <br /> 중간 계층 서비스가 다운스트림 서비스에 인증된 요청을 수행하려면 권한 부여 헤더에 *클라이언트 자격 증명* 토큰을 전달합니다. 필요에 따라 Azure AD B2C 사용자의 토큰에 사용자 지정 헤더를 포함할 수 있습니다.  |
[OpenId Connect](openid-connect.md) | GA | GA | OpenID Connect는 클라이언트가 사용자 ID를 확인할 수 있게 하는 보안 토큰인 ID 토큰의 개념을 소개합니다. |
[OpenId Connect 하이브리드 흐름](openid-connect.md) | GA | GA | 웹 애플리케이션이 권한 부여 코드와 함께 권한 부여 요청에서 ID 토큰을 검색할 수 있도록 허용합니다.  |
[ROPC(리소스 소유자 암호 자격 증명)](add-ropc-policy.md) | 미리 보기 | 미리 보기 | 모바일 애플리케이션이 해당 암호를 직접 처리하여 사용자가 로그인할 수 있도록 허용합니다. |

### <a name="oauth-20-options"></a>OAuth 2.0 옵션

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
| [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider) | GA | GA | 쿼리 문자열 매개 변수 `domain_hint`. |
| [로그인 이름 미리 채우기](direct-signin.md#prepopulate-the-sign-in-name) | GA | GA | 쿼리 문자열 매개 변수 `login_hint`. |
| `client_assertion`을 통해 사용자 경험에 JSON 삽입| 해당 없음| 사용되지 않음 |  |
| 사용자 경험에 [id_token_hint](id-token-hint.md)로 JSON 삽입 | 해당 없음 | GA | |
| [애플리케이션에 ID 공급자 토큰 전달](idp-pass-through-user-flow.md)| 미리 보기| 미리 보기| 예: Facebook에서 앱으로 전달 |

## <a name="saml2-application-authentication-flows"></a>SAML2 애플리케이션 인증 흐름

다음 표에는 Azure AD B2C와 통합할 수 있는 SAML(Security Assertion Markup Language) 애플리케이션 인증 흐름이 요약되어 있습니다.

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
[SP 시작](saml-service-provider.md) | 해당 없음 | GA | POST 바인딩 및 리디렉션 바인딩 |
[IDP 시작](saml-service-provider-options.md#configure-idp-initiated-flow) | 해당 없음 | GA | 여기서 시작 ID 공급자는 Azure AD B2C입니다.  |

## <a name="user-experience-customization"></a>사용자 환경 사용자 지정

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
| [다중 언어 지원](localization.md)| GA | GA | |
| [사용자 지정 이메일 확인](custom-email-mailjet.md) | 해당 없음 | GA| |
| [기본 제공 템플릿을 사용하여 사용자 인터페이스 사용자 지정](customize-ui.md) | GA| GA| |
| [사용자 지정 템플릿을 사용하여 사용자 인터페이스 사용자 지정](customize-ui-with-html.md) | GA| GA| HTML 템플릿을 사용합니다. |
| [페이지 레이아웃 버전](page-layout.md) | GA | GA | |
| [JavaScript](javascript-and-page-layout.md) | GA | GA | |
| [포함된 로그인 환경](embedded-login.md) | 해당 없음 |  미리 보기| 인라인 프레임 요소 `<iframe>`을 사용합니다. |
| [암호 복잡도](password-complexity.md) | GA | GA | |
| [메일 확인을 사용하지 않도록 설정](disable-email-verification.md) | GA|  GA| 프로덕션 환경에서는 사용하지 않는 것이 좋습니다. 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. |



## <a name="identity-providers"></a>ID 공급자

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
|[AD FS](identity-provider-adfs.md) | 해당 없음 | GA | |
|[Amazon](identity-provider-amazon.md) | GA | GA | |
|[Apple](identity-provider-apple-id.md) | 미리 보기 | 미리 보기 | |
|[Azure AD(단일 테넌트)](identity-provider-azure-ad-single-tenant.md) | GA | GA | |
|[Azure AD(다중 테넌트)](identity-provider-azure-ad-multi-tenant.md) | 해당 없음  | GA | |
|[Azure AD B2C](identity-provider-azure-ad-b2c.md) | GA | GA | |
|[eBay](identity-provider-ebay.md) | 해당 없음 | 미리 보기 | |
|[Facebook](identity-provider-facebook.md) | GA | GA | |
|[GitHub](identity-provider-github.md) | GA | GA | |
|[Google](identity-provider-google.md) | GA | GA | |
|[ID.me](identity-provider-id-me.md) | GA | GA | |
|[LinkedIn](identity-provider-linkedin.md) | GA | GA | |
|[Microsoft 계정](identity-provider-microsoft-account.md) | GA | GA | |
|[QQ](identity-provider-qq.md) | 미리 보기 | GA | |
|[Salesforce](identity-provider-salesforce.md) | GA | GA | |
|[Salesforce(SAML 프로토콜)](identity-provider-salesforce-saml.md) | 해당 없음 | GA | |
|[Twitter](identity-provider-twitter.md) | GA | GA | |
|[WeChat](identity-provider-wechat.md) | 미리 보기 | GA | |
|[Weibo](identity-provider-weibo.md) | 미리 보기 | GA | |

## <a name="generic-identity-providers"></a>일반 ID 공급자

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
|[OAuth2](oauth2-technical-profile.md) | 해당 없음 | GA | 예를 들어 [Google](identity-provider-google.md), [GitHub](identity-provider-github.md) 및 [Facebook](identity-provider-facebook.md)이 있습니다.|
|[OAuth1](oauth1-technical-profile.md) | 해당 없음 | GA | 예: [Twitter](identity-provider-twitter.md) |
|[OpenID Connect](openid-connect-technical-profile.md) | GA | GA | 예를 들어 [Azure AD](identity-provider-azure-ad-single-tenant.md)가 있습니다.  |
|[SAML2](identity-provider-generic-saml.md) | 해당 없음 | GA | 예를 들어, [Salesforce](identity-provider-salesforce-saml.md), [AD-FS](identity-provider-adfs.md)가 있습니다. |
| WSFED | 해당 없음 | 해당 없음 | |

### <a name="api-connectors"></a>API 커넥터

|기능  |사용자 흐름  |사용자 지정 정책  |메모  |
|---------|:---------:|:---------:|---------|
|[API 커넥터](api-connectors-overview.md) | 미리 보기 | GA | |
|[기본 인증으로 보안](secure-rest-api.md#http-basic-authentication) | 미리 보기 | GA | |
|[클라이언트 인증서 인증으로 보안](secure-rest-api.md#https-client-certificate-authentication) | 미리 보기 | GA | |
|[OAuth2 전달자 인증으로 보안](secure-rest-api.md#oauth2-bearer-authentication) | 해당 없음 | GA | |
|[보안 API 키 인증](secure-rest-api.md#api-key-authentication) | 해당 없음 | GA | |


## <a name="custom-policy-features"></a>사용자 지정 정책 기능

### <a name="session-management"></a>세션 관리

| 기능 |  사용자 지정 정책 | 메모 |
| ------- | :--: | ----- |
| [기본 SSO 세션 공급자](custom-policy-reference-sso.md#defaultssosessionprovider) | GA |  |
| [외부 로그인 세션 공급자](custom-policy-reference-sso.md#externalloginssosessionprovider) | GA |  |
| [SAML SSO 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) | GA |  |
| [OAuth SSO 세션 공급자](custom-policy-reference-sso.md#oauthssosessionprovider)  | GA|  |
| [Single Sign-Out](session-behavior.md#sign-out)  |  미리 보기 |  |

### <a name="components"></a>구성 요소

| 기능 | 사용자 지정 정책 | 메모 |
| ------- | :--: | ----- |
| [Phone 요소 인증](phone-factor-technical-profile.md) | GA |  |
| [Azure AD MFA 인증](multi-factor-auth-technical-profile.md) | 미리 보기 |  |
| [일회용 암호](one-time-password-technical-profile.md) | GA |  |
| 로컬 디렉터리로서의 [Azure Active Directory](active-directory-technical-profile.md) | GA |  |
| [조건자 유효성 검사](predicates.md) | GA | 예: 암호 복잡성 |
| [표시 컨트롤](display-controls.md) | GA |  |

### <a name="developer-interface"></a>개발자 인터페이스

| 기능 | 사용자 지정 정책 | 메모 |
| ------- | :--: | ----- |
| Azure Portal | GA |   |
| [Application Insights 사용자 경험 로그](troubleshoot-with-application-insights.md) | 미리 보기 | 개발 중 문제를 해결하는 데 사용됨  |
| [Application Insights 이벤트 로그](analytics-with-application-insights.md) | 미리 보기 | 프로덕션에서 사용자 흐름을 모니터링하는 데 사용됨 |

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임

수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대해 낮은 수준의 액세스 권한을 부여하여 고유한 보안 프레임워크를 생성합니다. 다각적인 사용자 지정 ID 공급자, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로를 사용하려면 설계 및 구성에 대한 체계적인 접근 방식이 필요합니다.

사용자 지정 정책 기능 집합을 사용하는 개발자는 다음 지침을 준수해야 합니다.

- 사용자 지정 정책 및 키/비밀 관리의 구성 언어에 익숙해집니다. 자세한 내용은 [TrustFrameworkPolicy](trustframeworkpolicy.md)를 참조하세요.
- 시나리오 및 사용자 지정 통합을 직접 소유합니다. 작업을 문서화하고 라이브 사이트 조직에 알립니다.
- 체계적인 시나리오 테스트를 수행합니다.
- 소프트웨어 개발 및 준비 모범 사례를 따릅니다. 최소한 하나 이상의 개발 및 테스트 환경을 권장합니다.
- 사용자와 통합된 ID 공급자 및 서비스에 대한 새로운 개발 정보를 바로 입수합니다. 예를 들어 기밀 변경 내용과 예정되었거나 갑작스럽게 진행되는 서비스 변경 내용을 추적합니다.
- 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링합니다. Application Insights와 통합하는 방법에 대한 자세한 내용은 [Azure Active Directory B2C: 로그 수집](analytics-with-application-insights.md)을 참조하세요.
- Azure 구독에서 연락처 전자 메일 주소를 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 전자 메일에 즉시 응답합니다.
- Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치를 취합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에 사용할 수 있는 Microsoft Graph 작업](microsoft-graph-operations.md)을 확인합니다.
- [사용자 지정 정책 및 사용자 흐름과의 차이점](custom-policy-overview.md)에 대해 자세히 알아보기
