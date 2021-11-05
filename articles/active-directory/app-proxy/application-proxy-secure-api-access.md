---
title: Azure Active Directory 애플리케이션 프록시를 사용하여 온-프레미스 API에 액세스
description: Azure Active Directory의 애플리케이션 프록시를 사용하면 네이티브 앱이 온-프레미스 또는 클라우드 VM에서 호스트하는 API 및 비즈니스 논리에 안전하게 액세스할 수 있습니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.custom: has-adal-ref
ms.openlocfilehash: eb2036976e9e73e7ad466974f9885b9035152851
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059493"
---
# <a name="secure-access-to-on-premises-apis-with-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 온-프레미스 API에 안전하게 액세스

온-프레미스에서 실행되거나 클라우드의 가상 머신에서 호스트되는 비즈니스 논리 API가 있을 수 있습니다. 네이티브 Android, iOS, Mac 또는 Windows 앱은 데이터를 사용하거나 사용자 상호 작용을 제공하기 위해 API 엔드포인트와 상호 작용해야 합니다. Azure AD 애플리케이션 프록시 및 [MSAL(Microsoft 인증 라이브러리)](../azuread-dev/active-directory-authentication-libraries.md)을 사용하면 네이티브 앱에서 온-프레미스 API에 안전하게 액세스할 수 있습니다. Azure Active Directory 애플리케이션 프록시는 방화벽 포트를 열고 앱 계층에서 인증 및 권한 부여를 제어하는 것보다 더 빠르고 안전한 솔루션입니다.

이 문서에서는 네이티브 앱에서 액세스할 수 있는 웹 API 서비스를 호스트하기 위한 Azure AD 애플리케이션 프록시 솔루션을 설정하는 과정을 안내합니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 온-프레미스 API를 게시하는 기존의 방법을 보여 줍니다. 이 방법을 사용하려면 80 및 443 들어오는 포트를 열어야 합니다.

![기존 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

다음 다이어그램에서는 Azure AD 애플리케이션 프록시를 사용하여 들어오는 포트를 열지 않고 API를 안전하게 게시하는 방법을 보여 줍니다.

![Azure AD 애플리케이션 프록시 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 애플리케이션 프록시는 API 액세스를 위한 퍼블릭 엔드포인트로 작동하고 인증 및 권한 부여를 제공하는 솔루션의 백본을 형성합니다. [MSAL(Microsoft 인증 라이브러리)](../azuread-dev/active-directory-authentication-libraries.md) 라이브러리를 사용하여 다양한 플랫폼에서 API에 액세스할 수 있습니다.

Azure AD 애플리케이션 프록시 인증 및 권한 부여는 Azure AD를 기반으로 하므로 Azure AD 조건부 액세스를 사용하여 신뢰할 수 있는 디바이스만 애플리케이션 프록시를 통해 게시된 API에 액세스할 수 있도록 할 수 있습니다. 데스크톱에는 Azure AD 조인 또는 Azure AD 하이브리드 조인을 사용하고, 디바이스에는 Intune 관리를 사용합니다. 또한 Azure AD Multi-Factor Authentication과 같은 Azure Active Directory Premium 기능과 [Azure Identity Protection](../identity-protection/overview-identity-protection.md)의 기계 학습 기반 보안을 활용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 연습을 수행하려면 다음이 필요합니다.

- Azure 디렉터리에 대한 관리자 액세스 권한(앱을 만들고 등록할 수 있는 계정 사용)
- [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)의 샘플 웹 API 및 네이티브 클라이언트 앱

## <a name="publish-the-api-through-application-proxy"></a>애플리케이션 프록시를 통해 API 게시

애플리케이션 프록시를 통해 API를 인트라넷 외부에 게시하려면 웹앱 게시와 동일한 패턴을 따릅니다. 자세한 내용은 [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)를 참조하세요.

애플리케이션 프록시를 통해 SecretAPI 웹 API를 게시하려면 다음을 수행합니다.

1. 샘플 SecretAPI 프로젝트를 빌드하고, ASP.NET 웹앱으로 로컬 컴퓨터 또는 인트라넷에 게시합니다. 웹앱에 로컬로 액세스할 수 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다. 그런 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.

1. **엔터프라이즈 애플리케이션 - 모든 애플리케이션** 페이지의 위쪽에서 **새 애플리케이션** 을 선택합니다.

1. **애플리케이션 추가** 페이지에서 **온-프레미스 애플리케이션** 을 선택합니다. **사용자 고유의 온-프레미스 애플리케이션 추가** 페이지가 나타납니다.

1. 애플리케이션 프록시 커넥터가 설치되지 않은 경우 설치하라는 메시지가 표시됩니다. **애플리케이션 프록시 커넥터 다운로드** 를 선택하여 커넥터를 다운로드하고 설치합니다.

1. 애플리케이션 프록시 커넥터가 설치되면 **사용자 고유의 온-프레미스 애플리케이션 추가** 페이지에서 다음을 수행합니다.

   1. **이름** 옆에 *SecretAPI* 를 입력합니다.

   1. **내부 URL** 옆에 인트라넷 내에서 API에 액세스하는 데 사용하는 URL을 입력합니다.

   1. **사전 인증** 이 **Azure Active Directory** 로 설정되어 있는지 확인합니다.

   1. 페이지 위쪽에서 **추가** 를 선택하고, 앱이 만들어질 때까지 기다립니다.

   ![API 앱 추가](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. **엔터프라이즈 애플리케이션 - 모든 애플리케이션** 페이지에서 **SecretAPI** 앱을 선택합니다.

1. **SecretAPI - 개요** 페이지의 왼쪽 탐색 영역에서 **속성** 을 선택합니다.

1. **MyApps** 패널에서 최종 사용자가 API를 사용할 수 없도록 하려면 **속성** 페이지의 아래쪽에서 **사용자가 볼 수 있습니까?** 를 **아니요** 로 설정한 다음, **저장** 을 선택합니다.

   ![사용자가 볼 수 없음](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Azure AD 애플리케이션 프록시를 통해 웹 API를 게시했습니다. 이제 앱에 액세스할 수 있는 사용자를 추가합니다.

1. **SecretAPI - 개요** 페이지의 왼쪽 탐색 영역에서 **사용자 및 그룹** 을 선택합니다.

1. **사용자 및 그룹** 페이지에서 **사용자 추가** 를 선택합니다.

1. **할당 추가** 페이지에서 **사용자 및 그룹** 을 선택합니다.

1. **사용자 및 그룹** 페이지에서 적어도 자신을 포함하여 앱에 액세스할 수 있는 사용자를 검색하여 선택합니다. 모든 사용자를 선택한 후 **선택** 을 선택합니다.

   ![사용자 선택 및 할당](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. **할당 추가** 페이지로 돌아가서 **할당** 을 선택합니다.

> [!NOTE]
> Windows 통합 인증을 사용하는 API에는 [추가적인 단계](./application-proxy-configure-single-sign-on-with-kcd.md)가 필요할 수 있습니다.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>네이티브 앱을 등록하고 API에 대한 액세스 권한 부여

네이티브 앱은 특정 플랫폼 또는 디바이스에서 사용하도록 개발된 프로그램입니다. 네이티브 앱에서 API에 연결하고 액세스하려면 먼저 Azure AD에 등록해야 합니다. 다음 단계에서는 네이티브 앱을 등록하고 애플리케이션 프록시를 통해 게시한 웹 API에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.

AppProxyNativeAppSample 네이티브 앱을 등록하려면 다음을 수행합니다.

1. Azure Active Directory **개요** 페이지에서 **앱 등록** 을 선택하고, **앱 등록** 창의 위쪽에서 **새 등록** 을 선택합니다.

1. **애플리케이션 등록** 페이지에서 다음을 수행합니다.

   1. **이름** 아래에서 *AppProxyNativeAppSample* 을 입력합니다.

   1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정** 을 선택합니다.

   1. **리디렉션 URL** 아래에서 **퍼블릭 클라이언트(모바일 및 데스크톱)** 를 드롭다운하여 선택한 다음, *https://login.microsoftonline.com/common/oauth2/nativeclient* 를 입력합니다.

   1. **등록** 을 선택하고 앱이 성공적으로 등록될 때까지 기다립니다.

      ![새 애플리케이션 등록](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

이제 AppProxyNativeAppSample 앱을 Azure Active Directory에 등록했습니다. SecretAPI 웹 API에 대한 액세스 권한을 네이티브 앱에 부여하려면 다음을 수행합니다.

1. Azure Active Directory **개요** > **앱 등록** 페이지에서 **AppProxyNativeAppSample** 앱을 선택합니다.

1. **AppProxyNativeAppSample** 페이지의 왼쪽 탐색 영역에서 **API 권한** 을 선택합니다.

1. **API 권한** 페이지에서 **권한 추가** 를 선택합니다.

1. 첫 번째 **API 권한 요청** 페이지에서 **내 조직에서 사용하는 API** 탭을 선택한 다음, **SecretAPI** 를 검색하여 선택합니다.

1. 다음 **API 권한 요청** 페이지에서 **user_impersonation** 옆에 있는 확인란을 선택한 다음, **권한 추가** 를 선택합니다.

    ![API 선택](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. **API 권한** 페이지로 돌아가서 **Contoso에 대한 관리자 동의 허용** 을 선택하여 다른 사용자가 앱에 개별적으로 동의하지 않도록 방지할 수 있습니다.

## <a name="configure-the-native-app-code"></a>네이티브 앱 코드 구성

마지막 단계는 네이티브 앱을 구성하는 것입니다. NativeClient 샘플 앱의 *Form1.cs* 파일에서 다음 코드 조각을 사용하면 MSAL 라이브러리에서 API 호출을 요청하기 위한 토큰을 획득하고 이를 전달자로 앱 헤더에 연결합니다.

```csharp
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
    .Create(<App ID of the Native app>)
    .WithDefaultRedirectUri() // Will automatically use the default Uri for native app
    .WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
    .Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
{
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
}

if (authResult != null)
{
    // Use the Access Token to access the Proxy Application

    HttpClient httpClient = new HttpClient();
    HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
}
```

Azure Active Directory에 연결하고 API 앱 프록시를 호출하도록 네이티브 앱을 구성하려면 NativeClient 샘플 앱의 *App.config* 파일에 있는 자리 표시자 값을 Azure AD의 값으로 업데이트합니다.

- **디렉터리(테넌트) ID** 를 `<add key="ida:Tenant" value="" />` 필드에 붙여넣습니다. 두 앱 중 하나의 **개요** 페이지에서 이 값(GUID)을 찾아서 복사할 수 있습니다.

- AppProxyNativeAppSample **애플리케이션(클라이언트) ID** 를 `<add key="ida:ClientId" value="" />` 필드에 붙여넣습니다. AppProxyNativeAppSample **개요** 페이지에서 이 값(GUID)을 찾아서 복사할 수 있습니다.

- AppProxyNativeAppSample **리디렉션 URI** 를 `<add key="ida:RedirectUri" value="" />` 필드에 붙여넣습니다. AppProxyNativeAppSample **인증** 페이지에서 이 값(URI)을 찾아서 복사할 수 있습니다.

- SecretAPI **애플리케이션 ID URI** 를 `<add key="todo:TodoListResourceId" value="" />` 필드에 붙여넣습니다. SecretAPI **API 표시** 페이지에서 이 값(URI)을 찾아서 복사할 수 있습니다.

- SecretAPI **홈 페이지 URL** 을 `<add key="todo:TodoListBaseAddress" value="" />` 필드에 붙여넣습니다. SecretAPI **브랜딩** 페이지에서 이 값(URL)을 찾아서 복사할 수 있습니다.

매개 변수가 구성되면 네이티브 앱을 빌드하고 실행합니다. **로그인** 단추를 선택하면 앱에서 로그인할 수 있습니다. 그러면 SecretAPI에 성공적으로 연결되었음을 확인할 수 있는 성공 화면이 표시됩니다.

![성공한 SecretAPI 메시지와 확인 단추를 보여 주는 스크린샷](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)
- [빠른 시작: 웹 API에 액세스하는 클라이언트 애플리케이션 구성](../develop/quickstart-configure-app-access-web-apis.md)
- [네이티브 클라이언트 앱이 프록시 애플리케이션과 상호 작용할 수 있도록 설정하는 방법](application-proxy-configure-native-client-application.md)
