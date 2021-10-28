---
title: ADAL에서 MSAL로 마이그레이션 가이드(MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: ADAL(Azure Active Directory 인증 라이브러리) Java 앱을 MSAL(Microsoft 인증 라이브러리)로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: marsma
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev, devx-track-java, has-adal-ref
ms.openlocfilehash: c70f2d1495f827c391fab86fc6ea1dea4301fc29
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624618"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL에서 MSAL로 마이그레이션 가이드(Java)

이 문서에서는 ADAL(Azure Active Directory 인증 라이브러리)을 사용하는 앱을 MSAL(Microsoft 인증 라이브러리)을 사용하도록 마이그레이션하기 위해 변경해야 하는 사항을 설명합니다.

Azure AD 엔터티를 인증하고 Azure AD에서 토큰을 요청하는 데 MSAL4J(Java용 Microsoft 인증 라이브러리) 및 ADAL4J(Java용 Azure AD 인증 라이브러리)가 모두 사용됩니다. 지금까지 대부분의 개발자는 ADAL(Azure AD 인증 라이브러리)을 사용하는 토큰을 요청하여 Azure AD ID(회사 및 학교 계정)를 인증하기 위해 개발자 플랫폼(v1.0)에서 Azure AD를 사용했습니다.

MSAL은 다음과 같은 이점을 제공합니다.

- 최신 Microsoft ID 플랫폼을 사용하기 때문에 Azure AD ID, Microsoft 계정, 소셜 및 로컬 계정과 같은 광범위한 Microsoft ID 집합을 Azure AD B2C(Business to Consumer)를 통해 인증할 수 있습니다.
- 사용자가 최상의 Single Sign-On 환경을 이용할 수 있습니다.
- 애플리케이션에서 증분 동의를 설정할 수 있으며 조건부 액세스를 지원하기 더 쉽습니다.

Java용 MSAL은 Microsoft ID 플랫폼과 함께 사용하도록 권장하는 인증 라이브러리입니다. ADAL4J에는 새로운 기능이 구현되지 않습니다. 앞으로 진행되는 모든 작업은 MSAL을 개선하는 데 중점을 두고 있습니다.

MSAL에 대해 자세히 알아보고 [Microsoft 인증 라이브러리 개요](msal-overview.md)를 시작할 수 있습니다.

## <a name="differences"></a>차이점

개발자용 Azure AD(v1.0) 엔드포인트(및 ADAL4J)를 이미 사용 중인 경우 [Microsoft ID 플랫폼과의 차이점은?](../azuread-dev/azure-ad-endpoint-comparison.md)을 참조하는 것이 좋습니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL4J는 리소스에 대한 토큰을 획득하는 반면, Java용 MSAL은 범위에 대한 토큰을 획득합니다. Java 클래스에 대한 여러 MSAL에는 범위 매개 변수가 필요합니다. 이 매개 변수는 원하는 권한 및 요청된 리소스를 선언하는 문자열 목록입니다. 예제 범위를 보려면 [Microsoft Graph의 범위](/graph/permissions-reference)를 참조하세요.

앱을 ADAL에서 MSAL으로 마이그레이션하는 데 도움이 되도록 리소스에 `/.default` 범위 접미사를 추가할 수 있습니다. 예를 들어 `https://graph.microsoft.com`의 리소스 값에 해당하는 범위 값은 `https://graph.microsoft.com/.default`입니다.  리소스가 URL 형식이 아닌 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` 형식의 리소스 ID인 경우에서 범위 값을 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`로 계속 사용할 수 있습니다.

다양한 범위 형식에 대한 자세한 내용은 [Microsoft ID 플랫폼의 사용 권한 및 동의](./v2-permissions-and-consent.md) 및 [v1.0 토큰을 허용하는 Web API에 대한 범위](./msal-v1-app-scopes.md)를 참조하세요.

## <a name="core-classes"></a>핵심 클래스

ADAL4J에서는 `AuthenticationContext` 클래스가 인증 기관을 통한 STS(보안 토큰 서비스) 또는 권한 부여 서버 연결을 나타냅니다. 그러나 Java용 MSAL은 클라이언트 애플리케이션을 중심으로 설계되어 클라이언트 애플리케이션을 나타내는 두 개의 별도 클래스 `PublicClientApplication` 및 `ConfidentialClientApplication`을 제공합니다.  `ConfidentialClientApplication`은 디먼 앱에 대한 애플리케이션 식별자와 같은 비밀을 안전하게 유지하기 위해 디자인된 애플리케이션을 나타냅니다.

다음 표에서는 ADAL4J 함수가 새로운 Java용 MSAL에 매핑되는 방식을 보여 줍니다.

| ADAL4J 메서드| MSAL4J 메서드|
|------|-------|
|acquireToken(String resource, ClientCredential credential, AuthenticationCallback callback) | acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, ClientAssertion assertion, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, AsymmetricKeyCredential credential, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String username, String password, AuthenticationCallback callback)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String username, String password=null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(String resource, UserAssertion userAssertion, ClientCredential credential, AuthenticationCallback callback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() and acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IUser 대신 IAccount

ADAL4J는 사용자를 조작했습니다. 사용자는 개인 또는 소프트웨어 에이전트를 나타내므로 Microsoft ID 시스템에서 하나 이상의 계정을 가질 수 있습니다. 예를 들어 동일한 사용자에게 여러 Azure AD, Azure AD B2C 또는 Microsoft 개인 계정이 있을 수 있습니다.

Java용 MSAL은 `IAccount` 인터페이스를 통해 계정의 개념을 정의합니다. 이는 ADAL4J와의 호환성이 손상되는 변경이지만 동일한 사용자가 여러 계정을 가질 수 있고 계정이 심지어 여러 Azure AD 디렉터리에 있을 수 있다는 점을 고려하므로 좋은 방법입니다. 또한 홈 계정 정보가 제공되므로 Java용 MSAL은 게스트 시나리오에서 더 나은 정보를 제공합니다.

## <a name="cache-persistence"></a>캐시 지속성

ADAL4J는 토큰 캐시를 지원하지 않았습니다.
Java용 MSAL은 가능할 경우 만료된 토큰을 자동으로 새로 고쳐 토큰 수명 관리를 간소화하고 가능할 경우 사용자에게 자격 증명을 요구하는 불필요한 프롬프트를 방지하기 위해 [토큰 캐시](msal-acquire-cache-tokens.md)를 추가합니다.

## <a name="common-authority"></a>공동 인증 기관

v1.0에서는 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 모든 조직의 AAD(Azure Active Directory) 계정을 사용하여 로그인할 수 있습니다.

v2.0에서는 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 모든 AAD 조직, 심지어 MSA(Microsoft 개인 계정)를 사용하여 로그인할 수 있습니다. Java용 MSAL에서는 로그인을 AAD 계정으로 제한하려면 `https://login.microsoftonline.com/organizations` 인증 기관을 사용합니다(ADAL4J와 동일한 동작). 인증 기관을 지정하려면 `PublicClientApplication` 클래스를 만들 때 [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) 메서드에서 `authority` 매개 변수를 설정합니다.

## <a name="v10-and-v20-tokens"></a>v1.0 및 v2.0 토큰

v1.0 엔드포인트(ADAL에서 사용)는 v1.0 토큰만 내보냅니다.

v2.0 엔드포인트(MSAL에서 사용)는 v1.0 및 v2.0 토큰을 내보낼 수 있습니다. Web API의 애플리케이션 매니페스트 속성을 사용하면 개발자가 허용할 토큰 버전을 선택할 수 있습니다. [애플리케이션 매니페스트](./reference-app-manifest.md) 참조 설명서에서 `accessTokenAcceptedVersion`을 참조하세요.

v1.0 및 v2.0 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](./access-tokens.md)을 참조하세요.

## <a name="adal-to-msal-migration"></a>ADAL에서 MSAL로 마이그레이션

ADAL4J에서는 새로 고침 토큰이 노출되었으며 개발자가 이를 캐시할 수 있었습니다. 그런 다음 `AcquireTokenByRefreshToken()`을 사용하여 사용자가 더 이상 연결되어 있지 않을 때 사용자를 대신하여 대시보드를 새로 고치는 장기 실행 서비스 등의 솔루션을 구현했습니다.

Java용 MSAL은 보안상의 이유로 새로 고침 토큰을 노출하지 않습니다. 대신, MSAL은 사용자 대신 토큰 새로 고침을 처리합니다.

Java용 MSAL에는 ADAL4j에서 얻은 새로 고침 토큰을 ClientApplication으로 마이그레이션할 수 있는 API [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)이 있습니다. 이 메서드를 사용하면 이전에 사용한 새로 고침 토큰을 원하는 범위(리소스)와 함께 제공할 수 있습니다. 새로 고침 토큰이 새 토큰으로 교환되고 애플리케이션에서 사용할 수 있도록 캐시됩니다.

다음 코드 조각에서는 기밀 클라이언트 애플리케이션의 마이그레이션 코드를 일부 보여 줍니다.

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`는 액세스 토큰 및 ID 토큰을 반환하고 새 새로 고침 토큰은 캐시에 저장됩니다.
애플리케이션에는 이제 IAccount도 포함됩니다.

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

현재 캐시에 있는 토큰을 사용하려면 다음을 호출합니다.

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
