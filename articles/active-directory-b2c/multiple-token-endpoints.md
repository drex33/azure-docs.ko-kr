---
title: OWIN 기반 웹 API를 b2clogin.com 또는 사용자 지정 도메인으로 마이그레이션
titleSuffix: Azure AD B2C
description: 애플리케이션을 b2clogin.com로 마이그레이션하는 동안 .NET 웹 API를 사용하여 여러 토큰 발급자가 발급한 토큰을 지원하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d11ce0d3f3890cfb2aa2ea7f957370d6739b6fe3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036363"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>OWIN 기반 웹 API를 b2clogin.com 또는 사용자 지정 도메인으로 마이그레이션

이 문서에서는 [OWIN(.NET용 오픈 웹 인터페이스)](http://owin.org/)을 구현하는 웹 API에서 여러 토큰 발급자를 지원할 수 있도록 하는 기법을 알아봅니다. 여러 토큰 엔드포인트를 지원하는 것은 Azure AD B2C(Azure Active Directory B2C) API 및 해당 애플리케이션을 한 도메인에서 다른 도메인으로 마이그레이션할 때 유용합니다. 예를 들어 *login.microsoftonline.com* 에서 *b2clogin.com* 또는 [사용자 지정 도메인](custom-domain.md)으로 마이그레이션하는 경우가 있습니다.

login.microsoftonline.com에서 발급한 토큰에 대한 지원 기능을 API에서 제거하기 전에 b2clogin.com, login.microsoftonline.com 또는 사용자 지정 도메인에서 발급한 토큰을 수락하는 지원 기능을 API에 추가하여 웹 애플리케이션을 단계적으로 마이그레이션할 수 있습니다.

다음 섹션에서는 [Microsoft OWIN][katana] 미들웨어 구성 요소(Katana)를 사용하는 웹 API에서 여러 발급자를 사용하도록 설정하는 방법에 대한 예제를 제공합니다. 코드 예제는 Microsoft OWIN 미들웨어와 관련이 있지만 일반적인 기법은 다른 OWIN 라이브러리에 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 진행하려면 다음과 같은 Azure AD B2C 리소스가 있어야 합니다.

* 테넌트에서 만든 [사용자 흐름](tutorial-create-user-flows.md?pivots=b2c-user-flow) 또는 [사용자 지정 정책](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="get-token-issuer-endpoints"></a>토큰 발급자 엔드포인트 가져오기

먼저 API에서 지원하려는 각 발급자의 토큰 발급자 엔드포인트 URI를 가져와야 합니다. Azure AD B2C 테넌트에서 지원하는 *b2clogin.com* 및 *login.microsoftonline.com* 엔드포인트를 가져오려면 Azure Portal에서 다음 절차를 사용합니다.

기존 사용자 흐름 중 하나를 선택하여 시작합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트로 이동합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 기존 정책(예: *B2C_1_signupsignin1*)을 선택하고 **사용자 흐름 실행** 을 선택합니다.
1. 페이지 상단의 **사용자 흐름 실행** 제목 아래에서 하이퍼링크를 선택하여 해당 사용자 흐름의 OpenID Connect 검색 엔드포인트로 이동합니다.

    ![Azure Portal의 지금 실행 페이지에 제공되는 잘 알려진 URI 하이퍼링크](media/multi-token-endpoints/portal-01-policy-link.png)

1. 브라우저에서 열리는 페이지에서 `issuer` 값을 적어 둡니다. 예를 들면 다음과 같습니다.

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. **도메인 선택** 드롭다운을 사용하여 다른 도메인을 선택한 다음, 이전의 두 단계를 다시 한 번 수행하고 `issuer` 값을 기록합니다.

이제 다음과 유사한 두 개의 URI가 기록되어 있을 것입니다.

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>사용자 지정 정책

사용자 흐름이 아닌 사용자 지정 정책을 사용하는 경우 유사한 프로세스를 사용하여 발급자 URI를 가져올 수 있습니다.

1. Azure AD B2C 테넌트로 이동합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. 신뢰 당사자 정책 중 하나를 선택합니다(예: *B2C_1A_signup_signin*)
1. **도메인 선택** 드롭다운을 사용하여 도메인(예: *yourtenant.b2clogin.com*)을 선택합니다.
1. **OpenID Connect 검색 엔드포인트** 아래에 표시되는 하이퍼링크를 선택합니다.
1. `issuer` 값을 기록합니다.
1. 다른 도메인(예: *login.microsoftonline.com*)에 대해 4~6단계를 수행합니다.

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

이제 두 토큰 엔드포인트 URI가 모두 있으므로 두 엔드포인트가 모두 유효한 발급자임을 명시하도록 코드를 업데이트해야 합니다. 예제를 살펴보려면 샘플 애플리케이션을 다운로드하거나 복제한 다음, 두 엔드포인트를 모두 유효한 발급자로 지원하도록 샘플을 업데이트합니다.

보관 파일 다운로드: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>웹 API에서 여러 발급자 사용

이 섹션에서는 두 토큰 발급자 엔드포인트가 유효함을 명시하도록 코드를 업데이트합니다.

1. Visual Studio에서 **B2C-WebAPI-DotNet.sln** 솔루션을 엽니다.
1. **TaskService** 프로젝트에서 *TaskService\\App_Start\\**Startup.Auth.cs*** 파일을 편집기에 엽니다.
1. 다음 `using` 지시문을 파일의 맨 위에 추가합니다.

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] 속성을 [`TokenValidationParameters`][tokenvalidationparameters] 정의에 추가하고 이전 섹션에서 기록한 두 URI를 지정합니다.

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`는 MSAL.NET이 제공하며, *Startup.Auth.cs* 에서 코드의 다음 섹션에 있는 OWIN 미들웨어가 사용합니다. 유효한 발급자를 여러 개 지정하면 OWIN 애플리케이션 파이프라인에서 두 토큰 엔드포인트가 모두 유효한 발급자임을 인식합니다.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

앞서 언급했듯이, 다른 OWIN 라이브러리는 일반적으로 여러 발급자를 지원하기 위해 유사한 기능을 제공합니다. 모든 라이브러리에 대한 예제를 제공하는 것은 이 문서의 범위를 벗어나지만 대부분의 라이브러리에 유사한 기법을 사용할 수 있습니다.

## <a name="switch-endpoints-in-web-app"></a>웹앱에서 엔드포인트 전환

이제 웹 API에서 두 URI를 모두 지원하므로 b2clogin.com 엔드포인트에서 토큰을 검색하도록 웹 애플리케이션을 업데이트해야 합니다.

예를 들어 *_TaskWebApp** 프로젝트의 TaskWebApp\\**Web.config** _* 파일에서 `ida:AadInstance` 값을 수정하여 새 엔드포인트를 사용하도록 샘플 웹 애플리케이션을 구성할 수 있습니다.

`login.microsoftonline.com` 대신 `{your-b2c-tenant-name}.b2clogin.com`을 참조하도록 TaskWebApp의 *Web.config* 에서 `ida:AadInstance` 값을 변경합니다.

이전:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

이후(`{your-b2c-tenant}`는 B2C 테넌트 이름으로 바꾸세요):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

웹앱을 실행하는 동안 엔드포인트 문자열이 생성되면 토큰을 요청할 때 b2clogin.com 기반 엔드포인트가 사용됩니다.

사용자 지정 도메인을 사용하는 경우:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 발급자 엔드포인트의 토큰을 허용하도록 Microsoft OWIN 미들웨어(Katana)를 구현하는 웹 API를 구성하는 방법을 제공했습니다. 눈치채셨을지 모르겠지만, 사용자 고유의 테넌트에 대해 이러한 프로젝트를 빌드하고 실행하려는 경우 TaskService 및 TaskWebApp 프로젝트의 *Web.Config* 파일에서 몇 가지 다른 문자열을 변경해야 합니다. 프로젝트의 작동 모습을 확인하려면 프로젝트를 적절히 수정해도 되지만 이 작업을 수행하는 방법에 대한 자세한 안내는 이 문서에서 다루지 않습니다.

Azure AD B2C에서 내보내는 다양한 형식의 보안 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](tokens-overview.md)를 참조하세요.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
