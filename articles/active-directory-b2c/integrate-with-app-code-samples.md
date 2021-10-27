---
title: Azure Active Directory B2C와 앱 통합 샘플
description: Azure AD B2C와 모바일, 데스크톱, 웹 및 단일 페이지 애플리케이션을 통합하기 위한 코드 샘플입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42b59977ffd1ed09dbaecf01fbc955f7817356b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038862"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C 코드 샘플

다음 표에서는 iOS, Android, .NET, Node.js를 포함한 애플리케이션용 샘플의 링크를 제공합니다.

## <a name="mobile-and-desktop-apps"></a>모바일 및 데스크톱 앱

| 샘플 | Description |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | OAuth 2.0을 사용하여 Azure AD B2C 사용자를 인증하고 API를 호출하는 Swift의 iOS 샘플 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | MSAL을 사용하여 Azure Active Directory B2C를 통해 사용자를 인증하고 결과 토큰을 사용하여 Web API에 액세스하는 방법을 보여주는 간단한 Android 앱입니다. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | 타사 라이브러리를 사용하여 Objective-C로 Microsoft ID 사용자를 Azure AD B2C ID 서비스에 인증하는 iOS 애플리케이션을 빌드하는 방법을 보여주는 샘플입니다. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | 타사 라이브러리를 사용하여 Microsoft ID 사용자를 B2C ID 서비스에 인증하고 OAuth 2.0 액세스 토큰을 사용하여 Web API를 호출하는 Android 애플리케이션을 빌드하는 방법을 보여주는 샘플입니다. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Azure AD B2C를 사용하여 Windows 데스크톱 .NET(WPF) 애플리케이션으로 사용자를 로그인하고, MSAL.NET을 사용하여 액세스 토큰을 가져오고, API를 호출하는 방법을 보여주는 샘플입니다. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | MSAL을 사용하여 Azure Active Directory B2C를 통해 사용자를 인증하고 결과 토큰을 사용하여 Web API에 액세스하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다. |

## <a name="web-apps-and-apis"></a>웹앱 및 API

| 샘플 | Description |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | .NET Web API를 호출하는 .NET 웹 애플리케이션을 위한 결합 샘플로, 둘 다 Azure AD B2C를 사용하여 보호됩니다. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | OpenID Connect를 사용하여 Azure AD B2C에서 사용자를 로그인하는 ASP.NET Core 웹 애플리케이션입니다. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Azure AD B2C를 사용하여 사용자를 로그인하고, MSAL.NET을 사용하여 액세스 토큰을 가져오고, API를 호출하는 방법을 보여주는 ASP.NET Core 웹 애플리케이션입니다. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | OpenID Connect를 사용하여 Express를 통해 웹 애플리케이션을 쉽고 빠르게 설치하는 방법을 제공하는 Node.js 애플리케이션입니다. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | passport.js를 사용하여 web api를 보호하고 B2C 액세스 토큰에 액세스하는 방법을 보여주는 작은 Azure AD B2C용 node.js Web API입니다. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Microsoft ID 플랫폼의 B2C를 Python 웹 애플리케이션과 통합하는 방법을 보여줍니다.  |

## <a name="single-page-apps"></a>단일 페이지 앱

| 샘플 | Description |
|--------| ----------- |
| [ms-identity-javascript-angular-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | 웹 API를 호출하는 Angular SPA(단일 페이지 애플리케이션)입니다. 인증은 MSAL Angular를 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 PKCE와 함께 권한 부여 코드 흐름을 사용합니다. |
| [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | 웹 API를 호출하는 React SPA(단일 페이지 애플리케이션)입니다. 인증은 MSAL React를 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 PKCE와 함께 권한 부여 코드 흐름을 사용합니다. |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | 웹 API를 호출하는 VanillaJS SPA(단일 페이지 애플리케이션)입니다. 인증은 MSAL.js를 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 PKCE와 함께 권한 부여 코드 흐름을 사용합니다. |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Microsoft Graph를 호출하여 B2C 디렉터리의 사용자를 관리하는 VanillaJS SPA(단일 페이지 애플리케이션)입니다. 인증은 MSAL.js를 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 PKCE와 함께 권한 부여 코드 흐름을 사용합니다.|

## <a name="consoledaemon-apps"></a>콘솔/디먼 앱

| 샘플 | Description |
|--------| ----------- |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | B2C 디렉터리에서 사용자를 관리하기 위해 자체 ID를 사용하여 Microsoft Graph를 호출하는 Node.js 및 express 콘솔 데몬 애플리케이션입니다. 인증은 MSAL 노드를 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 권한 부여 코드 흐름을 사용합니다.|
| [dotnetcore-b2c-account-management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | B2C 디렉터리에서 사용자를 관리하기 위해 자체 ID를 사용하여 Microsoft Graph를 호출하는 .NET Core 콘솔 애플리케이션입니다. 인증은 MSAL.NET을 사용하여 Azure AD B2C로 수행됩니다. 이 샘플에서는 권한 부여 코드 흐름을 사용합니다.|

## <a name="saml-test-application"></a>SAML 테스트 애플리케이션

| 샘플 | Description |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Saml ID 공급자로 작동하도록 구성된 Azure AD B2C를 테스트하기 위한 SAML 테스트 애플리케이션입니다. |

## <a name="api-connectors"></a>API 커넥터

다음 표에서는 [API 커넥터](api-connectors-overview.md)를 사용하여 사용자 흐름에서 웹 API를 활용하기 위한 코드 샘플에 대한 링크를 제공합니다.

### <a name="azure-function-quickstarts"></a>Azure 함수 빠른 시작

| 샘플                                                                                                                          | Description                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 이 .NET Core Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다. |
| [Node.JS](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 이 Node.js Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 이 Python Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.    |


### <a name="automated-fraud-protection-services--captcha"></a>자동화된 사기 방지 서비스 및 CAPTCHA
| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs 사기 행위 및 남용 방지](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | 이 샘플에서는 Arkose Labs 사기 행위 및 남용 방지 서비스를 사용하여 사용자 등록을 보호하는 방법을 보여줍니다. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | 이 샘플에서는 자동화된 남용을 방지하기 위해 reCAPTCHA 챌린지를 사용하여 사용자 등록을 보호하는 방법을 보여줍니다. |


### <a name="identity-verification"></a>ID 확인

| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 이 샘플에서는 API 커넥터를 사용하여 IDology와 통합함으로써 등록 흐름의 일부로 사용자 ID를 확인하는 방법을 보여줍니다. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 이 샘플에서는 API 커넥터를 사용하여 Experian과 통합함으로써 등록 흐름의 일부로 사용자 ID를 확인하는 방법을 보여줍니다. |


### <a name="other"></a>기타

| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [초대 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | 이 샘플에서는 초대 코드를 사용하여 특정 대상으로 등록을 제한하는 방법을 보여줍니다.|
| [API 커넥터 커뮤니티 샘플](https://github.com/azure-ad-b2c/api-connector-samples) | 이 리포지토리에는 API 커넥터에 의해 활성화된 시나리오의 커뮤니티에서 유지 관리되는 샘플이 있습니다.|
