---
title: '방법: Azure AD의 OpenID Connect 메타데이터 새로 고침에 탄력적으로 대응하는 서비스 빌드 | Azure'
titleSuffix: Microsoft identity platform
description: 웹앱 또는 웹 API가 Azure AD의 OpenID Connect 메타데이터 새로 고침에 탄력적으로 대응하는지 확인하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: jmprieur
ms.reviewer: marsma, shermanouko
ms.custom: aaddev
ms.openlocfilehash: 2b698f351198daeceddc6b254eb62ddb5f58a008
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988903"
---
# <a name="build-services-that-are-resilient-to-azure-ads-openid-connect-metadata-refresh"></a>Azure AD의 OpenID Connect 메타데이터 새로 고침에 탄력적으로 대응하는 서비스 빌드

보호된 웹 API는 액세스 토큰의 유효성을 검사해야 합니다. 웹앱도 ID 토큰의 유효성을 검사합니다. 토큰 유효성 검사는 토큰이 애플리케이션에 속하는지, 토큰을 IDP(신뢰할 수 있는 ID 공급자)에서 발급했는지, 토큰에 여전히 범위 내에 있고 변조되지 않은 수명이 있는지에 대한 검사로 구성됩니다. 특별한 유효성 검사를 수행할 수도 있습니다. 예를 들어 앱이 서명의 유효성을 검사하고 서명 키(토큰에 포함된 경우)를 신뢰할 수 있는지 확인하고 토큰이 재생되고 있지 않은지 확인해야 합니다. 서명 키가 토큰에 포함되지 않은 경우 ID 공급자(검색 또는 메타데이터)에서 인출해야 합니다. 런타임에 동적으로 키를 가져와야 하는 경우도 있습니다.

웹앱 및 웹 API는 복원력을 보장하기 위해 오래된 OpenID Connect 메타데이터를 새로 고쳐야 합니다. 이 문서는 복원력 있는 앱을 구현하는 방법을 안내합니다. ASP.NET Core, ASP.NET 클래식, Microsoft.IdentityModel에 적용됩니다.

## <a name="aspnet-core"></a>ASP.NET Core

[Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)의 최신 버전을 사용하고 수동으로 아래 지침을 따릅니다.

Startup.cs의 `ConfigureServices` 메서드에서 `JwtBearerOptions.RefreshOnIssuerKeyNotFound`가 true로 설정되어 있고 최신 Microsoft.IdentityModel.* 라이브러리를 사용하고 있는지 확인합니다. 이 속성은 기본적으로 사용하도록 설정되어 있습니다.

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    …
    // shouldn’t be necessary as it’s true by default
    options.RefreshOnIssuerKeyNotFound = true;
    …
};
```

## <a name="aspnet-owin"></a>ASP.NET/ OWIN

ASP.NET에서 개발이 중지되었으므로 ASP.NET Core로 이동하는 것이 좋습니다.

ASP.NET 클래식을 사용하는 경우 최신 [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)을 사용합니다.

OWIN에는 `OpenIdConnectConfiguration`에 대한 자동 24시간 새로 고침 간격이 있습니다. 이 새로 고침은 24시간 범위가 전달된 후 요청을 수신한 경우에만 트리거됩니다. 현재 애플리케이션을 다시 시작하는 것 외에는 이 값을 변경하거나 조기에 새로 고침을 트리거할 수 있는 방법은 없습니다.

## <a name="microsoftidentitymodel"></a>Microsoft.IdentityModel

토큰의 유효성을 직접 검사하는 경우, 예를 들어 Azure 함수에서 최신 버전의 [Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)을 사용하고 아래 코드 조각에 나와 있는 메타데이터 지침을 따릅니다.

```csharp
var configManager =
  new ConfigurationManager<OpenIdConnectConfiguration>(
    "http://someaddress.com",
    new OpenIdConnectConfigurationRetriever());

var config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
var validationParameters = new TokenValidationParameters()
{
  …
  IssuerSigningKeys = config.SigningKeys;
  …
}

var tokenHandler = new JsonWebTokenHandler();
result = Handler.ValidateToken(jwtToken, validationParameters);
if (result.Exception != null && result.Exception is SecurityTokenSignatureKeyNotFoundException)
{
  configManager.RequestRefresh();
  config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
  validationParameters = new TokenValidationParameters()
  {
    …
    IssuerSigningKeys = config.SigningKeys,
    …
  };

  // attempt to validate token again after refresh
  result = Handler.ValidateToken(jwtToken, validationParameters);
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [보호된 웹 API에서 토큰 유효성 검사](scenario-protected-web-api-app-configuration.md#token-validation)를 참조하세요.
