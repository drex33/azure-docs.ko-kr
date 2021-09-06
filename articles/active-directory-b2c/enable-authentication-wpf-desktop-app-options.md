---
title: Azure Active Directory B2C를 사용하여 WPF 데스크톱 애플리케이션 옵션 활성화
description: 여러 가지 방법을 사용하여 WPF 데스크톱 애플리케이션 옵션의 사용을 활성화합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: bc3881f60ba77569ae0d2ae775ad124d0587fa82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528142"
---
# <a name="configure-authentication-options-in-a-wpf-desktop-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 WPF 데스크톱 애플리케이션에서 인증 옵션 구성 

이 문서에서는 WPF 데스크톱 애플리케이션에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용자 지정하고 개선할 수 있는 방법을 설명합니다. 시작하기 전에 [샘플 WPF 데스크톱 애플리케이션에서 인증 구성](configure-authentication-sample-wpf-desktop-app.md) 문서를 숙지합니다.


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우, [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. MSAL 구성 개체를 찾고 로그인 힌트를 사용하여 `withLoginHint()` 메서드를 추가합니다.

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. 기존 `Dictionary` 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 도메인 이름이 포함된 `domain_hint` 매개 변수를 사전에 추가합니다. `facebook.com`)을 입력합니다.
1. 추가 쿼리 매개 변수 개체를 MSAL 구성 개체의 `WithExtraQueryParameters` 메서드에 전달합니다.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성합니다](language-customization.md).
1. 기존 `Dictionary` 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 언어 코드를 포함하는 `ui_locales` 매개 변수를 사전에 추가합니다. `en-us`)을 입력합니다.
1. 추가 쿼리 매개 변수 개체를 MSAL 구성 개체의 `WithExtraQueryParameters` 메서드에 전달합니다.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. 기존 `Dictionary` 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. `campaignId`와 같은 사용자 지정 쿼리 문자열 매개 변수를 추가합니다. 매개 변수 값을 설정합니다. `germany-promotion`)을 입력합니다.
1. 추가 쿼리 매개 변수 개체를 MSAL 구성 개체의 `WithExtraQueryParameters` 메서드에 전달합니다.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. 코드에서 ID 토큰을 생성하거나 획득하고 토큰을 변수로 설정합니다. `idToken`)을 입력합니다. 
1. 기존 `Dictionary` 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. ID 토큰을 저장하는 해당 변수를 사용하여 `id_token_hint` 매개 변수를 추가합니다.
1. 추가 쿼리 매개 변수 개체를 MSAL 구성 개체의 `extraQueryParameters` 특성에 전달합니다.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


다음 코드 조각에서는 MSAL 로깅을 구성하는 방법을 보여 줍니다.

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-redirect-uri"></a>리디렉션 URI 구성

[데스크톱 앱 등록](configure-authentication-sample-wpf-desktop-app.md#23-register-the-desktop-app)에서 리디렉션 URI를 선택하는 경우 다음과 같은 중요한 고려 사항이 있습니다.

* **개발**: 개발 용도 및 **데스크톱 앱** 의 경우 리디렉션 URI를 `http://localhost`로 설정할 수 있으며 Azure AD B2C는 요청의 모든 포트를 준수합니다. 등록된 URI에 포트가 포함된 경우 Azure AD B2C는 해당 포트만 사용합니다. 예를 들어 등록된 리디렉션 URI가 `http://localhost`인 경우 요청의 리디렉션 URI는 `http://localhost:<randomport>`일 수 있습니다. 등록된 리디렉션 URI가 `http://localhost:8080`인 경우 요청의 리디렉션 URI는 `http://localhost:8080`이여야 합니다.
* **고유**: 리디렉션 URI의 구성표는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`에서 `com.onmicrosoft.contosob2c.exampleapp`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못 선택하면 로그인이 실패합니다.
* **전체**: 리디렉션 URI에는 구성표 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//oauth/`는 실행되고 `//oauth`는 실행되지 않습니다. URI에 밑줄과 같은 특수 문자를 사용하지 마세요.

## <a name="next-steps"></a>다음 단계

- 자세한 정보: [.NET, UWP, NetCore 및 Xamarin 구성 옵션에 대한 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
