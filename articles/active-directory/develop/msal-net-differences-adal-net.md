---
title: ADAL.NET과 MSAL.NET 앱 간의 차이점 | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)과 ADAL.NET(.NET용 Azure AD 인증 라이브러리)의 차이점에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5dfc0c3c006c42f6e56e9f2f15311a2b99396fe4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424100"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>ADAL.NET과 MSAL.NET 앱 간의 차이점

애플리케이션을 ADAL 사용에서 MSAL 사용으로 마이그레이션하면 보안 및 복원력이 향상됩니다. 이 문서에서는 MSAL.NET 및 ADAL.NET 간의 차이점을 간략하게 설명합니다. 대부분의 경우 Microsoft 인증 라이브러리의 최신 세대인 MSAL.NET 및 Microsoft ID 플랫폼을 사용하려고 합니다. MSAL.NET을 사용하면 Azure AD(회사 및 학교 계정), MSA(Microsoft (개인) 계정) 또는 Azure AD B2C를 사용하여 애플리케이션에 로그인하는 사용자에 대한 토큰을 얻을 수 있습니다.

ADAL.NET 및 개발자용 Azure AD(v1.0) 엔드포인트에 이미 익숙한 경우 [Microsoft ID 플랫폼과의 차이점은?](../azuread-dev/azure-ad-endpoint-comparison.md)에 대해 알고 있을 것입니다. 애플리케이션이 이전 버전의 [ADFS(Active Directory Federation Services)](/windows-server/identity/active-directory-federation-services)를 사용하여 사용자를 로그인해야 하는 경우에는 여전히 ADAL.NET을 사용해야 합니다. 자세한 내용은 [ADFS 지원](https://aka.ms/msal-net-adfs-support)을 참조하세요.

|   | **ADAL NET**  | **MSAL NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **NuGet 패키지 및 네임스페이스**  |ADAL은 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 패키지에서 사용됩니다. 네임스페이스가 `Microsoft.IdentityModel.Clients.ActiveDirectory`인 경우.  | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 패키지를 추가하고 `Microsoft.Identity.Client` 네임스페이스를 사용합니다. 기밀 클라이언트 애플리케이션을 빌드하는 경우 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)을 확인하세요. |
| **범위 및 리소스**  | ADAL.NET은 *리소스* 에 대한 토큰을 획득합니다. |  MSAL.NET은 *범위* 에 대한 토큰을 획득합니다 다양한 MSAL.NET `AcquireTokenXXX` 재정의에는 범위(`IEnumerable<string> scopes`)라는 매개 변수가 필요합니다. 이 매개 변수는 요청된 권한 및 리소스를 선언하는 간단한 문자열 목록입니다. 잘 알려진 범위는 [Microsoft Graph의 범위](/graph/permissions-reference)입니다. [MSAL.NET을 사용하여 v1.0 리소스에 액세스](#scopes)할 수도 있습니다. |
| **핵심 클래스**  | ADAL.NET은 인증 기관을 통해 STS(보안 토큰 서비스) 또는 권한 부여 서버에 대한 연결을 나타내는 표현으로 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD)를 사용했습니다. | MSAL.NET은 [클라이언트 애플리케이션](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)을 중심으로 설계되었습니다. 이 서비스는 퍼블릭 클라이언트 애플리케이션에 대해 `IPublicClientApplication` 인터페이스를 정의하고 기밀 클라이언트 애플리케이션에 대해 `IConfidentialClientApplication` 인터페이스를 정의할 뿐만 아니라 두 유형의 애플리케이션에 공통된 계약에 대해 기본 인터페이스 `IClientApplicationBase`를 정의합니다.|
| **토큰 획득**  | 퍼블릭 클라이언트에서 ADAL은 인증 호출에 `AcquireTokenAsync` 및 `AcquireTokenSilentAsync`를 사용합니다. | 퍼블릭 클라이언트에서 MSAL은 동일한 인증 호출에 `AcquireTokenInteractive` 및 `AcquireTokenSilent`를 사용합니다. 이러한 매개 변수는 ADAL 매개 변수와 다릅니다. <br><br>기밀 클라이언트 애플리케이션에는 시나리오에 따라 명시적 이름이 지정된 [토큰 획득 방법](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens)이 있습니다. 또 다른 차이점은 MSAL.NET의 경우 AcquireTokenXX 호출마다 애플리케이션의 `ClientID`를 더 이상 전달할 필요가 없다는 것입니다. `ClientID`는 `IPublicClientApplication` 또는 `IConfidentialClientApplication`을 빌드할 때 한 번만 설정됩니다.|
|  **IAccount 및 IUser**  | ADAL은 IUser 인터페이스를 통해 사용자의 표기법을 정의합니다. 그러나 사용자는 사람 또는 소프트웨어 에이전트입니다. 따라서 사용자는 Microsoft ID 시스템에서 하나 이상의 계정(여러 개의 Azure AD 계정, Azure AD B2C, Microsoft 개인 계정)을 소유할 수 있습니다. 사용자는 하나 이상의 Microsoft ID 플랫폼 계정을 책임질 수도 있습니다. | MSAL.NET는 이제 IAccount 인터페이스를 통해 계정에 대한 개념을 정의합니다. IAccount 인터페이스는 단일 계정에 대한 정보를 나타냅니다. 동일한 사용자가 여러 다른 테넌트에서 여러 계정을 가질 수 있습니다. 홈 계정 정보가 제공되므로 MSAL.NET은 게스트 시나리오에서 더 나은 정보를 제공합니다. [IUser와 IAccount 간 차이점](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount)에 대해 자세히 알아보세요.|
|  **캐시 지속성**  | ADAL.NET을 사용하면 보안 스토리지(.NET Framework 및 .NET Core)가 없는 플랫폼에서 원하는 지속성 기능을 구현하기 위해 `BeforeAccess` 및 `BeforeWrite` 메서드를 사용하여 `TokenCache` 클래스를 확장할 수 있습니다. 자세한 내용은 [ADAL.NET의 토큰 캐시 직렬화](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)를 참조하세요. | MSAL.NET은 토큰 캐시를 sealed 클래스로 만들어 확장 기능을 제거합니다. 따라서 토큰 캐시 지속성의 구현은 sealed 토큰 캐시와 상호 작용하는 도우미 클래스 형식이어야 합니다. 이 상호 작용은 [MSAL.NET의 토큰 캐시 직렬화](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) 문서에서 설명하고 있습니다. 퍼블릭 클라이언트 애플리케이션([퍼블릭 클라이언트 애플리케이션의 경우 토큰 캐시](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application) 참조)에 대한 직렬화는 기밀 클라이언트 애플리케이션의 직렬화와 다릅니다([웹앱 또는 웹 API에 대한 토큰 캐시](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application) 참조). |
| **공동 인증 기관** | ADAL은 Azure AD v1.0을 사용합니다. Azure AD v1.0의 `https://login.microsoftonline.com/common` 기관(ADAL에서 사용)을 통해 사용자는 AAD 조직(직장 또는 학교) 계정을 사용하여 로그인할 수 있습니다. Azure AD v1.0에서는 Microsoft 개인 계정으로 로그인할 수 없습니다. 자세한 내용은 [ADAL.NET의 인증 기관 유효성 검사](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)를 참조하세요. | MSAL은 Azure AD v2.0을 사용합니다. Azure AD v2.0의 `https://login.microsoftonline.com/common` 기관(MSAL에서 사용)을 통해 사용자는 AAD 조직(직장 또는 학교) 계정 또는 Microsoft 개인 계정으로 로그인할 수 있습니다. MSAL에서 조직 계정(직장 또는 학교 계정)만 사용하도록 로그인을 제한하려면 `https://login.microsoftonline.com/organizations` 엔드포인트를 사용해야 합니다.  자세한 내용은 [퍼블릭 클라이언트 애플리케이션](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)의 `authority` 매개 변수를 참조하세요. |

## <a name="supported-grants"></a>지원되는 권한 부여

다음은 퍼블릭 및 기밀 클라이언트 애플리케이션 모두에 대한 MSAL.NET 및 ADAL.NET 지원 권한 부여를 비교해서 설명하는 요약입니다.

### <a name="public-client-applications"></a>퍼블릭 클라이언트 애플리케이션

다음 그림에서는 퍼블릭 클라이언트 애플리케이션에 대한 ADAL.NET 및 MSAL.NET 간 차이점 중 일부를 요약해서 보여 줍니다.

[![퍼블릭 클라이언트 애플리케이션에 대한 ADAL.NET 및 MSAL.NET 간 차이점을 보여 주는 스크린샷](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

데스크톱 및 모바일 애플리케이션용 ADAL.NET 및 MSAL.NET에서 지원되는 권한은 다음과 같습니다.

허용                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
대화형                       | [MSAL.NET에서 대화형으로 토큰 획득](scenario-desktop-acquire-token-interactive.md)    | [대화형 인증](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
Windows 통합 인증 | [Windows 통합 인증](scenario-desktop-acquire-token-integrated-windows-authentication.md)         | [Windows의 통합 인증(Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
사용자 이름/암호               | [사용자 이름 - 암호 인증](scenario-desktop-acquire-token-username-password.md)                      | [사용자 이름 및 암호를 사용하여 토큰 획득](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
디바이스 코드 흐름                  | [디바이스 코드 흐름](scenario-desktop-acquire-token-device-code-flow.md)                    | [웹 브라우저가 없는 디바이스에 대한 디바이스 프로필](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>기밀 클라이언트 애플리케이션

다음 이미지에서는 기밀 클라이언트 애플리케이션에 대한 ADAL.NET 및 MSAL.NET 간의 몇 가지 차이점을 요약해서 보여 줍니다.
 
[![기밀 클라이언트 애플리케이션에 대한 ADAL.NET 및 MSAL.NET 간 차이점을 보여 주는 스크린샷](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


웹 애플리케이션, 웹 API 및 디먼 애플리케이션용 ADAL.NET, MSAL.NET 및 Microsoft.Identity.Web에서 지원되는 권한은 다음과 같습니다.

앱 유형              | 허용              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
웹앱, 웹 API, 디먼 | 클라이언트 자격 증명 | [MSAL.NET의 클라이언트 자격 증명 흐름](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [ADAL.NET의 클라이언트 자격 증명 흐름](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
Web API                  | On-Behalf-Of       | [MSAL.NET의 On-Behalf-Of 흐름](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [사용자를 대신하여 ADAL.NET을 통해 서비스 간 호출](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
웹앱                  | 권한 부여 코드          | [MSAL.NET을 사용하여 웹앱에서 권한 부여 코드가 있는 토큰 획득](scenario-web-app-call-api-acquire-token.md)  | [ADAL.NET을 사용하여 웹앱에서 권한 부여 코드가 있는 토큰 획득](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>새로 고침 토큰을 사용하여 ADAL 2.x에서 마이그레이션

ADAL.NET v2.X에서는 새로 고침 토큰이 공개되어 토큰을 캐시하고 ADAL 2.x에서 제공하는 `AcquireTokenByRefreshToken` 메서드를 사용하여 이러한 토큰의 사용과 관련된 솔루션을 개발할 수 있었습니다.

이러한 솔루션 중 일부는 다음과 같은 시나리오에서 사용되었습니다.

- 사용자가 더 이상 앱에 연결/로그인되지 않은 상태에서 사용자를 위해 대시보드를 새로 고치는 등의 작업을 수행하는 장기 실행 서비스
- 클라이언트에서 새로 고침 토큰을 웹 서비스로 가져올 수 있게 하는 WebFarm 시나리오(클라이언트 쪽에서 캐싱 수행, 쿠키 암호화, 서버 쪽이 아님)

MSAL.NET은 보안상의 이유로 새로 고침 토큰을 공개하지 않습니다. MSAL은 사용자 대신 토큰 새로 고침을 처리합니다.

다행히 MSAL.NET에는 이전의 새로 고침 토큰(ADAL을 통해 획득)을 `IConfidentialClientApplication`으로 마이그레이션할 수 있는 API가 있습니다.

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

이 메서드를 사용하면 이전에 사용한 새로 고침 토큰을 원하는 범위(리소스)와 함께 제공할 수 있습니다. 새로 고침 토큰이 새 토큰으로 교환되고 애플리케이션에 캐시됩니다.

이 메서드는 일반적이지 않은 시나리오를 위한 것이므로 먼저 `IByRefreshToken`에 캐스팅해야 `IConfidentialClientApplication`에서 쉽게 액세스할 수 있습니다.

다음 코드 조각에서는 기밀 클라이언트 애플리케이션의 일부 마이그레이션 코드를 보여 줍니다.

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser`는 ADAL 2.x를 사용하는 데 사용된 이전 버전의 애플리케이션에서 일부 스토리지에 저장한 새로 고침 토큰을 검색합니다. `GetTokenCacheForSignedInUser`는 로그인한 사용자에 대한 캐시를 역직렬화합니다(기밀 클라이언트 애플리케이션에 사용자당 하나의 캐시가 있어야 하므로).

새 새로 고침 토큰이 캐시에 저장되는 동안 액세스 토큰 및 ID 토큰이 `AuthenticationResult` 값에 반환됩니다. 이 메서드는 새로 고침 토큰을 사용할 수 있는 다양한 통합 시나리오에 사용할 수도 있습니다.

## <a name="v10-and-v20-tokens"></a>v1.0 및 v2.0 토큰

토큰에는 v1.0 토큰과 v2.0 토큰의 두 가지 버전이 있습니다. v1.0 엔드포인트(ADAL에서 사용)는 v1.0 ID 토큰을 내보내지만, v2.0 엔드포인트(MSAL에서 사용)는 v2.0 ID 토큰을 내보냅니다. 그러나 두 엔드포인트 모두 웹 API에서 허용하는 토큰 버전의 액세스 토큰을 내보냅니다. Web API의 애플리케이션 매니페스트 속성을 사용하면 개발자가 허용할 토큰 버전을 선택할 수 있습니다. [애플리케이션 매니페스트](reference-app-manifest.md) 참조 설명서에서 `accessTokenAcceptedVersion`을 참조하세요.

v1.0 및 v2.0 액세스 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](access-tokens.md)을 참조하세요.

## <a name="exceptions"></a>예외

### <a name="interaction-required-exceptions"></a>상호 작용에 필요한 예외

MSAL.NET을 사용하면 [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)에서 설명한 대로 `MsalUiRequiredException`을 catch합니다.

```csharp
catch(MsalUiRequiredException exception)
{
 try {"try to authenticate interactively"}
}
```

자세한 내용은 [MSAL.NET에서 오류 및 예외 처리](msal-error-handling-dotnet.md)를 참조하세요.

ADAL.NET에는 덜 명시적인 예외가 있습니다. 예를 들어 ADAL에서 자동 인증이 실패하면 프로시저에서 예외를 catch하고 `user_interaction_required` 오류 코드를 찾습니다.

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

자세한 내용은 ADAL.NET을 사용하여 [퍼블릭 클라이언트 애플리케이션에서 토큰을 획득하기 위한 권장 패턴](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token)을 참조하세요.

### <a name="prompt-behavior"></a>프롬프트 동작

MSAL.NET의 프롬프트 동작은 ADAL.NET의 프롬프트 동작과 동일합니다.

|  ADAL.NET | MSAL.NET | Description |
| ----------- | ----------- | -------------|
| `PromptBehavior.Auto`| `NoPrompt`| Azure AD는 사용자가 계정 하나를 사용하여 로그인한 경우 자동으로 로그인하거나 여러 계정으로 로그인하는 경우 계정 선택기를 표시하여 최상의 동작을 선택합니다. |
| `PromptBehavior.Always`| `ForceLogin` | 로그인 상자를 다시 설정하고 사용자가 자격 증명을 다시 입력하도록 합니다. |
| `PromptBehavior.RefreshSession`| `Consent`| 사용자가 모든 사용 권한에 강제로 다시 동의하도록 합니다. |
| `PromptBehavior.Never`| `Never`| 사용하지 않습니다. 대신 [퍼블릭 클라이언트 앱에 권장되는 패턴](scenario-desktop-acquire-token.md?tabs=dotnet)을 사용합니다. |
| `PromptBehavior.SelectAccount`| `SelectAccount`| 계정 선택기를 표시하고 사용자에게 계정을 강제로 선택하도록 합니다. |

### <a name="handling-claim-challenge-exceptions"></a>클레임 챌린지 예외 처리

토큰을 획득할 때 리소스에 사용자의 더 많은 클레임이 필요한 경우(예: 2단계 인증) Azure AD에서 예외를 throw합니다.

MSAL.NET에서 클레임 챌린지 예외는 다음과 같은 방법으로 처리됩니다.

- `Claims`가 `MsalServiceException`에 표시됩니다.
- `AcquireTokenXXX` 작성기에 적용할 수 있는 `.WithClaim(claims)` 메서드가 있습니다.

자세한 내용은 [MsalUiRequiredException 처리](msal-error-handling-dotnet.md#msaluirequiredexception)를 참조하세요.

ADAL.NET에서 클레임 챌린지 예외는 다음과 같은 방법으로 처리됩니다.

- `AdalClaimChallengeException`는 예외입니다(`AdalServiceException`에서 파생). `Claims` 멤버에는 필요한 클레임이 포함된 일부 JSON 조각이 포함되어 있습니다. 
- 이 예외를 받는 퍼블릭 클라이언트 애플리케이션에서 claims 매개 변수가 있는 `AcquireTokenInteractive` 재정의를 호출해야 합니다. `AcquireTokenInteractive`의 이러한 재정의에는 캐시가 필요하지 않으므로 캐시 적중을 시도하지도 않습니다. 이는 캐시의 토큰에 적절한 클레임이 없기 때문입니다(그렇지 않으면 `AdalClaimChallengeException`이 throw되지 않음). 따라서 캐시를 확인할 필요가 없습니다. OBO를 수행하는 WebAPI에서 `ClaimChallengeException`을 받을 수 있지만, 이 웹 API를 호출하는 퍼블릭 클라이언트 애플리케이션에서는 `AcquireTokenInteractive`를 호출해야 합니다.

샘플을 포함한 자세한 내용은 [AdalClaimChallengeException 처리](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)를 참조하세요.


## <a name="scopes"></a>범위

그러나 ADAL은 `resourceId` 문자열이 있는 리소스 개념을 사용하지만, MSAL.NET은 범위를 사용합니다. Azure AD에서 사용하는 논리는 다음과 같습니다.

- v1.0 액세스 토큰을 사용하는 ADAL(v1.0) 엔드포인트의 경우(유일하게 가능한 경우) `aud=resource`
- MSAL(v2.0 엔드포인트)에서 v2.0 토큰을 허용하는 리소스에 대한 액세스 토큰을 요청하는 경우 `aud=resource.AppId`
- MSAL(v2.0 엔드포인트)에서 v1.0 액세스 토큰을 허용하는 리소스에 대한 액세스 토큰을 요청하는 경우 Azure AD는 요청된 범위에서 원하는 대상 그룹을 구문 분석합니다. 이 작업은 마지막 슬래시 앞의 모든 항목을 가져와 리소스 식별자로 사용하여 수행됩니다. 따라서 `https://database.windows.net`에서 `https://database.windows.net/` 대상 그룹이 필요한 경우 `https://database.windows.net//.default` 범위를 요청해야 합니다(./default 앞에 이중 슬래시 사용). 이 내용은 아래의 예제 1과 2에 설명되어 있습니다.

### <a name="example-1"></a>예제 1

v1.0 토큰을 수락하는 애플리케이션[예: Microsoft Graph API(`https://graph.microsoft.com`)]에 대한 토큰을 획득하려면 원하는 리소스 식별자를 해당 리소스에 대해 원하는 OAuth2 권한과 연결하여 `scopes`를 만들어야 합니다.

예를 들어 앱 ID URI가 `ResourceId`인 v1.0 웹 API를 사용자 이름으로 액세스하려면 다음을 사용해야 합니다.

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Microsoft Graph API(`https://graph.microsoft.com/`)를 사용하여 MSAL.NET Azure Active Directory에서 읽고 쓰려면 아래 코드 조각과 같은 범위 목록을 만듭니다.

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>예 2

resourceId가 '/'로 끝나는 경우 범위 값을 쓸 때 이중 '/'이 있어야 합니다. 예를 들어, Azure Resource Manager API(`https://management.core.windows.net/`)에 해당하는 범위를 작성하려면 다음 범위를 요청합니다(이중 슬래시에 주의).

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

이는 Resource Manager API에서 해당 대상 그룹 클레임(`aud`)에 슬래시를 예상한 다음, 범위에서 API 이름을 분리하는 슬래시가 있기 때문입니다.

v1.0 애플리케이션의 모든 정적 범위에 대한 토큰을 획득하려면 아래 코드 조각과 같이 범위 목록을 만듭니다.

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

클라이언트 자격 증명 흐름의 경우 전달할 범위도 `/.default`가 됩니다. 이 범위는 관리자가 애플리케이션 등록에서 동의한 모든 앱 수준 권한을 요청한다고 Azure AD에 알려줍니다.

## <a name="next-steps"></a>다음 단계

[ADAL에서 MSAL로 앱 마이그레이션](msal-net-migration.md)
[MSAL.NET을 사용하도록 ADAL.NET 기밀 클라이언트 앱 마이그레이션](msal-net-migration-confidential-client.md)
