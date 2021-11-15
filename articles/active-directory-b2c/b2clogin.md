---
title: 애플리케이션 및 API를 b2clogin.com으로 마이그레이션
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 리디렉션 URL에 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 21f881dd0213c59c32f0acfe96b3c3cfc809fdcf
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039869"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C의 리디렉션 URL을 b2clogin.com으로 설정

Azure AD B2C(Azure Active Directory B2C) 애플리케이션의 가입 및 로그인을 위한 ID 공급자를 설정하는 경우 리디렉션 URL을 지정해야 합니다. Azure AD B2C를 통해 사용자를 인증하기 위해 애플리케이션의 애플리케이션 및 API에서 *login.microsoftonline.com* 을 참조하지 않아야 합니다. 대신 모든 새 애플리케이션에 *b2clogin.com* 을 사용하고 기존 애플리케이션을 *login.microsoftonline.com* 에서 *b2clogin.com* 으로 마이그레이션합니다.

## <a name="what-endpoints-does-this-apply-to"></a>해당하는 엔드포인트
b2clogin.com으로의 전환은 사용자 인증에 Azure AD B2C 정책(사용자 흐름 또는 사용자 지정 정책)을 사용하는 인증 엔드포인트에만 적용됩니다. 이러한 엔드포인트에는 Azure AD B2C가 사용해야 하는 정책을 지정하는 `<policy-name>` 매개 변수가 있습니다. [Azure AD B2C 정책에 대해 자세히 알아보세요](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

이러한 엔드포인트는 다음과 같습니다.
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

또는 `<policy-name>`이 쿼리 매개 변수로 전달될 수 있습니다.
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> ‘policy’ 매개 변수를 사용하는 엔드포인트와 [ID 공급자 리디렉션 URL](#change-identity-provider-redirect-urls)을 업데이트해야 합니다.

일부 Azure AD B2C 고객은 OAuth 2.0 클라이언트 자격 증명 부여 흐름과 같은 Azure AD 엔터프라이즈 테넌트의 공유 기능을 사용합니다. 이러한 기능에는 ‘정책 매개 변수를 포함하지 않는’ Azure AD의 login.microsoftonline.com 엔드포인트를 통해 액세스할 수 있습니다. __이러한 엔드포인트는 영향을 받지 않습니다__.

## <a name="benefits-of-b2clogincom"></a>b2clogin.com의 이점

리디렉션 URL로 *b2clogin.com* 을 사용하는 경우:

* 쿠키 헤더에서 Microsoft 서비스가 사용하는 공간이 줄어듭니다.
* URL에 더 이상 Microsoft에 대한 참조를 포함할 필요가 없습니다.
* [JavaScript 클라이언트 측 코드](javascript-and-page-layout.md)는 사용자 지정된 페이지에서 지원됩니다. 보안 제약 조건으로 인해 *login.microsoftonline.com* 을 사용하는 경우 JavaScript 코드 및 HTML 양식 요소가 사용자 지정 페이지에서 삭제됩니다.

## <a name="overview-of-required-changes"></a>필수 변경 사항 개요

애플리케이션을 *b2clogin.com* 으로 마이그레이션하려면 다음과 같이 몇 가지를 변경해야 합니다.

* ID 공급자의 애플리케이션의 리디렉션 URL이 *b2clogin.com* 을 참조하도록 변경합니다.
* 사용자 흐름 및 토큰 엔드포인트 참조에서 *b2clogin.com* 을 사용하도록 Azure AD B2C 애플리케이션을 업데이트합니다. 여기에는 MSAL(Microsoft 인증 라이브러리)과 같은 인증 라이브러리의 사용 업데이트가 포함될 수 있습니다.
* [사용자 인터페이스 사용자 지정](customize-ui-with-html.md)의 CORS 설정에서 정의한 **허용된 원본** 을 업데이트합니다.

이전 엔드포인트는 다음과 같을 수 있습니다.
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

해당하는 업데이트된 엔드포인트는 다음과 같습니다.
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>ID 공급자 리디렉션 URL 변경

애플리케이션을 만든 각 ID 공급자의 웹 사이트에서 모든 신뢰할 수 있는 URL을 *login.microsoftonline.com* 대신 `your-tenant-name.b2clogin.com`으로 리디렉션하도록 변경합니다.

b2clogin.com 리디렉션 URL에 사용할 수 있는 두 가지 형식이 있습니다. 첫 번째 형식에는 테넌트 도메인 이름 대신 테넌트 ID(GUID)를 사용하여 URL의 어디에도 “Microsoft”가 표시되지 않도록 하는 이점이 있습니다.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

두 번째 옵션은 `your-tenant-name.onmicrosoft.com` 형식의 테넌트 도메인 이름을 사용합니다. 예를 들면 다음과 같습니다.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

두 형식 모두:

* `{your-tenant-name}`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
* URL에 `/te`가 있는 경우 제거합니다.

## <a name="update-your-applications-and-apis"></a>애플리케이션 및 API 업데이트

Azure AD B2C 지원 애플리케이션 및 API의 코드는 여러 위치에서 `login.microsoftonline.com`을 참조할 수 있습니다. 예를 들어 코드에 사용자 흐름과 토큰 엔드포인트에 대한 참조가 있을 수 있습니다. 대신 `your-tenant-name.b2clogin.com`을 참조하도록 다음을 업데이트합니다.

* 권한 부여 엔드포인트
* 토큰 엔드포인트
* 토큰 발급자

예를 들어 Contoso의 가입/로그인 정책에 대한 권한 엔드포인트는 이제 다음과 같습니다.

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

OWIN 기반 웹 애플리케이션을 b2clogin.com으로 마이그레이션하는 방법은 [OWIN 기반 웹 API를 b2clogin.com으로 마이그레이션](multiple-token-endpoints.md)을 참조하세요.

Azure AD B2C로 보호되는 Azure API Management API를 마이그레이션하려면 [Azure AD B2C를 통한 Azure API Management API 보호](secure-api-management.md) 섹션의 [b2clogin.com으로 마이그레이션](secure-api-management.md#migrate-to-b2clogincom)을 참조하세요.

## <a name="microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority 속성

[MSAL.NET][msal-dotnet] v2 이하 버전을 사용하는 경우 클라이언트 인스턴스화에서 **ValidateAuthority** 속성을 `false`로 설정하여 *b2clogin.com* 으로 리디렉션을 허용합니다. MSAL.NET v3 이상 버전에서는 이 값을 `false`로 설정할 필요가 없습니다.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL for JavaScript validateAuthority 속성

[MSAL for JavaScript][msal-js] v1.2.2 이하 버전을 사용하는 경우 **validateAuthority** 속성을 `false`로 설정합니다.

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

MSAL.js 1.3.0 이상 버전(기본값)에서 `validateAuthority: true`를 설정하는 경우 `knownAuthorities`를 사용하여 유효한 토큰 발급자도 지정해야 합니다.

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>다음 단계

OWIN 기반 웹 애플리케이션을 b2clogin.com으로 마이그레이션하는 방법은 [OWIN 기반 웹 API를 b2clogin.com으로 마이그레이션](multiple-token-endpoints.md)을 참조하세요.

Azure AD B2C로 보호되는 Azure API Management API를 마이그레이션하려면 [Azure AD B2C를 통한 Azure API Management API 보호](secure-api-management.md) 섹션의 [b2clogin.com으로 마이그레이션](secure-api-management.md#migrate-to-b2clogincom)을 참조하세요.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
