---
title: Azure AD B2C의 새로운 앱 등록 환경
description: Azure AD B2C의 새로운 앱 등록 환경에 대해 소개합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 262d9a123180588ed8eaee35a5faf17797cbbc07
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008107"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C의 새로운 앱 등록 환경

이제 Azure AD B2C(Azure Active Directory B2C)의 새로운 **[앱 등록](https://aka.ms/b2cappregistrations)** 환경을 일반적으로 사용할 수 있습니다. Azure AD B2C에 대한 애플리케이션을 등록하는 **애플리케이션** 환경(여기서는 “레거시 환경”이라고 함)에 익숙한 경우 이 가이드에서 새로운 환경을 사용하여 시작해 볼 수 있습니다.

## <a name="overview"></a>개요
이전에는 레거시 환경을 사용하여 앱의 나머지 부분과 별도로 Azure AD B2C 고객용 애플리케이션을 관리해야 했습니다. 즉, Azure의 여러 위치에서의 다양한 앱 만들기 환경을 의미합니다.

새 환경은 모든 Azure AD B2C 앱 등록 및 Azure AD 앱 등록을 한 곳에 표시하고 일관성 있는 관리 방법을 제공합니다. 고객용 앱을 만드는 것부터 리소스 관리를 위해 Microsoft Graph 권한을 사용하여 앱을 관리하는 것까지 한 가지 방법만 학습하면 됩니다.

Azure Portal의 **Azure AD B2C** 또는 **Azure Active Directory** 서비스 모두에서 Azure AD B2C 테넌트의 **앱 등록** 으로 이동하여 새 환경에 연결할 수 있습니다.

Azure AD B2C 앱 등록 환경은 Azure AD 테넌트용 일반 [앱 등록 환경](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)을 기반으로 하지만 Azure AD B2C 테넌트에 맞게 조정됩니다.

## <a name="whats-not-changing"></a>변경되지 않는 항목
- 애플리케이션 및 관련 구성은 새 환경에서 있는 그대로 찾을 수 있습니다. 애플리케이션을 다시 등록할 필요는 없으며, 애플리케이션의 사용자는 다시 로그인할 필요가 없습니다.

> [!NOTE]
> 이전에 만든 모든 애플리케이션을 보려면 **앱 등록** 블레이드로 이동하여 **모든 애플리케이션** 탭을 선택합니다. 그러면 레거시 환경, 새 환경 및 Azure AD 서비스에서 만든 앱이 표시됩니다.

## <a name="key-new-features"></a>주요 새로운 기능

-   **통합 앱 목록** 에는 Azure AD B2C 및 Azure AD로 인증하는 모든 애플리케이션이 편리한 위치에 표시됩니다. 또한 **만든 날짜**, **인증서 및 비밀** 상태, 검색 표시줄 등 Azure AD 애플리케이션에 이미 사용할 수 있는 기능을 활용할 수 있습니다.

-   **결합된 앱 등록** 을 사용하면 고객용 앱인지 또는 Microsoft Graph에 액세스할 수 있는 앱인지 여부와 관계없이 앱을 신속하게 등록할 수 있습니다.

- **엔드포인트** 창을 사용하면 OpenID 연결 구성, SAML 메타데이터, Microsoft Graph API 및 [OAuth 2.0 사용자 흐름 엔드포인트](tokens-overview.md#endpoints)를 포함하여 시나리오에 대한 관련 엔드포인트를 빠르게 식별할 수 있습니다.

- **API 권한** 및 **API 노출** 은 보다 광범위한 범위, 권한 및 동의 관리를 제공합니다. 이제 앱에 MS Graph 권한을 할당할 수도 있습니다.

-   이제 Azure AD B2C로 인증하는 앱에 **소유자** 및 **매니페스트** 를 사용할 수 있습니다. [매니페스트 편집기](../active-directory/develop/reference-app-manifest.md)를 사용하여 등록 소유자를 추가하고 애플리케이션 속성을 직접 편집할 수 있습니다.


## <a name="new-supported-account-types"></a>지원되는 새 계정 유형

새 환경의 경우 다음 중에서 지원 계정 유형을 선택합니다.
- 이 조직 디렉터리의 계정만
- 모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)
- 모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)

다양한 계정 유형을 이해하려면 만들기 경험에서 **선택 도움말** 을 선택합니다.

레거시 환경에서 앱은 항상 고객용 애플리케이션으로 생성되었습니다. 해당 앱의 경우 계정 유형이 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 으로 설정됩니다.
> [!NOTE]
> 이 옵션은 Azure AD B2C 사용자 흐름을 실행하여 이 애플리케이션에 대한 사용자를 인증할 수 있어야 합니다. [사용자 흐름에 사용할 애플리케이션을 등록하는 방법](tutorial-register-applications.md)을 알아봅니다.

Azure AD B2C를 SAML 서비스 공급자로 사용하려는 경우에도 이 옵션을 사용할 수 있습니다. [자세히 알아봅니다](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>DevOps 시나리오용 애플리케이션

다른 계정 유형을 사용하여 Microsoft Graph 사용하여 Identity Experience Framework 정책을 업로드하거나 사용자를 프로비저닝하는 등 DevOps 시나리오를 관리하는 앱을 만들 수 있습니다. [Microsoft Graph 애플리케이션을 등록하여 Azure AD B2C 리소스를 관리하는 방법](microsoft-graph-get-started.md)을 알아봅니다.

해당 권한은 대부분 Azure B2C 소비자 사용자에게 적용되지 않기 때문에 Microsoft Graph 권한이 모두 표시되지 않을 수 있습니다. [Microsoft Graph를 사용한 사용자 관리](microsoft-graph-operations.md)에 대해 자세히 알아보세요.

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>관리자 동의 및 offline_access+openid 범위
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

**openid** 범위는 Azure AD B2C 사용자가 앱에 로그인할 수 있도록 하는 데 필요합니다. **offline_access** 범위는 사용자에 대한 새로 고침 토큰을 발급하는 데 필요합니다. 해당 범위는 이전에 추가되었으며 기본적으로 관리자 동의가 부여되었습니다. 이제 **openid 및 offline_access 권한에 관리자 동의 부여** 옵션을 선택하여 만들기 프로세스 중에 해당 범위에 대한 권한을 쉽게 추가할 수 있습니다. 또는 기존 앱에 대한 **API 권한** 설정에서 관리자 동의를 사용하여 Microsoft Graph 권한을 추가할 수 있습니다.

[권한 및 동의](../active-directory/develop/v2-permissions-and-consent.md)에 대해 자세히 알아봅니다.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>플랫폼/인증: 회신 URL/리디렉션 URI
레거시 환경에서 다양한 플랫폼 형식은 **속성** 에서 웹앱/API에 대한 회신 URL 및 네이티브 클라이언트에 대한 리디렉션 URI로 관리되었습니다. “네이티브 클라이언트”는 “퍼블릭 클라이언트”라고도 하며 여기에는 iOS, macOS, Android, 기타 모바일 및 데스크톱 애플리케이션 유형에 대한 앱이 포함되어 있습니다.

새 환경에서 회신 URL 및 리디렉션 URI는 모두 리디렉션 URI라고 하며, 앱의 **인증** 섹션에서 찾을 수 있습니다. 앱 등록은 웹앱 또는 네이티브 애플리케이션으로 제한되지 않습니다. 각 리디렉션 URI를 등록하여 해당 모든 플랫폼 유형에 동일한 앱 등록을 사용할 수 있습니다.

리디렉션 URI는 웹 또는 퍼블릭(모바일 및 데스크톱) 앱 유형과 연결해야 합니다. [리디렉션 URI에 대해 자세히 알아보기](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**iOS/macOS** 및 **Android** 플랫폼은 퍼블릭 클라이언트의 한 유형입니다. MSAL과 함께 사용하기 위해 적절한 리디렉션 URI로 iOS/macOS 또는 Android 앱을 구성하는 간편한 방법을 제공합니다. [애플리케이션 구성 옵션](../active-directory/develop/msal-client-applications.md)에 대해 자세히 알아봅니다.


## <a name="application-certificates--secrets"></a>애플리케이션 인증서 및 비밀

새 환경에서는 **키** 대신 **인증서 및 비밀** 블레이드를 사용하여 인증서 및 비밀을 관리합니다. 인증서 및 비밀을 사용하면 애플리케이션은 HTTPS 체계를 사용하여 웹 주소 지정 가능한 위치에서 토큰을 받을 때 인증 서비스에서 자신을 식별할 수 있습니다. Azure AD에 대해 인증할 때 클라이언트 자격 증명 시나리오에 클라이언트 암호 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하여 Azure AD B2C에 대해 인증할 수 없습니다.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C 테넌트에 적용되지 않는 기능
다음 Azure AD 앱 등록 기능은 Azure AD B2C 테넌트에서 적용되지 않거나 사용할 수 없습니다.
- **역할 및 관리자** - 현재 Azure AD B2C에서 사용할 수 없습니다.
- **브랜딩** - UI/UX 사용자 지정은 **회사 브랜딩** 환경에서 또는 사용자 흐름의 일부로 구성됩니다. [Azure Active Directory B2C에서 사용자 인터페이스를 사용자 지정하는 방법](customize-ui-with-html.md)을 알아봅니다.
- **게시자 도메인 확인** - 앱이 확인된 도메인이 아닌 *.onmicrosoft.com* 에 등록되어 있습니다. 또한 게시자 도메인은 사용자 동의를 부여하는 데 주로 사용되며, 사용자 인증을 위해 Azure AD B2C 앱에는 적용되지 않습니다. [게시자 도메인에 대해 자세히 알아봅니다.](../active-directory/develop/howto-configure-publisher-domain.md)
- **토큰 구성** - 토큰은 앱이 아닌 사용자 흐름의 일부로 구성됩니다.
- 현재 Azure AD B2C 테넌트에는 **빠른 시작** 환경을 사용할 수 없습니다.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>제한 사항
새 환경에는 다음과 같은 제한 사항이 있습니다.
- 현재 Azure AD B2C는 암시적 흐름에 대한 액세스 또는 ID 토큰을 발급할 수 있는지를 구분하지 않습니다. **인증** 블레이드에서 **ID 토큰** 옵션을 선택한 경우 두 가지 유형의 토큰 모두 암시적 권한 부여 흐름에 사용할 수 있습니다.
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- 지원되는 계정에 대한 값 변경은 UI에서 지원되지 않습니다. Azure AD 단일 테넌트와 다중 테넌트 간에 전환하지 않는 한 앱 매니페스트를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

새 앱 등록 환경을 시작하려면 다음을 수행하세요.
* [웹 애플리케이션을 등록하는 방법](tutorial-register-applications.md)을 알아봅니다.
* [웹 API를 등록하는 방법](add-web-api-application.md)을 알아봅니다.
* [네이티브 클라이언트 애플리케이션을 등록하는 방법](add-native-application.md)을 알아봅니다.
* [Microsoft Graph 애플리케이션을 등록하여 Azure AD B2C 리소스를 관리하는 방법](microsoft-graph-get-started.md)을 알아봅니다.
* [Azure AD B2C를 SAML 서비스 공급자로 사용하는 방법](identity-provider-adfs.md)을 알아봅니다.
* [애플리케이션 프록시](application-types.md)에 대해 알아봅니다.
