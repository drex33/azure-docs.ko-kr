---
title: B2B의 ID 공급자로 Google 추가 - Azure AD
description: 게스트 사용자가 자신의 Gmail 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 Google과 페더레이션합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 687e23c7267991eee171e205a537a45546da73b2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864581"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google을 사용하여 페더레이션을 설정하면 초대된 사용자가 Microsoft 계정을 만들지 않고도 자신의 Gmail 계정을 사용하여 공유 앱 및 리소스에 로그인하도록 허용할 수 있습니다. Google을 애플리케이션의 로그인 옵션 중 하나로 추가한 다음 **로그인** 페이지에서 사용자는 Google에 로그인하는 데 사용하는 Gmail 주소를 입력하기만 하면 됩니다.

![Google 사용자를 위한 로그인 옵션](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 페더레이션은 Gmail 사용자를 위해 특별히 설계되었습니다. Google Workspace 도메인과 페더레이션하려면 [SAML/WS-Fed ID 공급자 페더레이션](direct-federation.md)을 사용합니다.

> [!IMPORTANT]
>
> - **2021년 7월 12일부터**, Azure AD B2B 고객이 셀프 서비스 등록에 사용하거나 사용자 지정 또는 LOB(기간 업무) 애플리케이션에 외부 사용자를 초대하기 위해 새 Google 통합을 설정하는 경우 Gmail 사용자에 대한 인증이 차단될 수 있습니다(아래 [예상 결과](#what-to-expect)에 오류 화면이 표시됨). 이 문제는 2021년 7월 12일 이후 셀프 서비스 가입 사용자 흐름 또는 초대에 대한 Google 통합을 만들고 사용자 지정 또는 기간 업무 애플리케이션의 Gmail 인증이 시스템 웹 보기로 전환되지 않은 경우에만 발생합니다. 시스템 웹 보기는 기본적으로 사용하도록 설정되어 있으므로 대부분의 앱은 영향을 받지 않습니다. 이 문제를 방지하려면 셀프 서비스 가입을 위한 새로운 Google 통합을 만들기 전에 Gmail 인증을 시스템 브라우저로 전환하는 것이 좋습니다. [포함된 웹 보기에 필요한 작업](#action-needed-for-embedded-frameworks)을 참조하세요.
> - **2021년 9월 30일부터** Google은 [웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 외부 사용자 초대 또는 [셀프 서비스 등록](identity-providers.md)을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다. [자세한 정보를 알아보세요](#deprecation-of-web-view-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?

Google 사용자가 초대를 교환할 때 해당 환경은 Google에 이미 로그인했는지 여부에 따라 달라집니다.

- Google에 로그인하지 않은 게스트 사용자에게는 로그인하라는 메시지가 표시됩니다.
- 게스트 사용자가 Google에 이미 로그인한 경우에는 사용하려는 계정을 선택하라는 메시지가 표시됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

“헤더가 너무 깁니다” 오류가 표시되는 게스트 사용자는 쿠키를 지우거나 개인 또는 incognito 창을 열고 다시 로그인을 시도할 수 있습니다.

![Google 로그인 페이지를 보여주는 스크린샷.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>로그인 엔드포인트

Google 게스트 사용자는 이제 [공통 엔드포인트](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)(즉, 테넌트 컨텍스트를 포함하지 않는 일반 앱 URL)를 사용하여 다중 테넌트 또는 Microsoft 자사 앱에 로그인할 수 있습니다. 로그인 프로세스 중에 게스트 사용자는 **로그인 옵션** 을 선택한 다음, **조직에 로그인** 을 선택합니다. 그런 다음, 사용자는 조직의 이름을 입력하고 Google 자격 증명을 사용하여 계속 로그인합니다.

Google 게스트 사용자는 테넌트 정보가 포함된 애플리케이션 엔드포인트를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

테넌트 정보(예: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`)를 포함하여 Google 게스트 사용자에게 애플리케이션 또는 리소스에 대한 직접 링크를 제공할 수도 있습니다.

## <a name="deprecation-of-web-view-sign-in-support"></a>웹 보기 로그인 지원 중단

2021년 9월 30부터 Google은 [포함된 웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 [외부 사용자 초대](google-federation.md) 또는 [셀프 서비스 등록](identity-providers.md)을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다.

Gmail 사용자에게 영향을 주는 알려진 시나리오는 다음과 같습니다.
- Windows의 Microsoft 앱(예: Teams 및 Power Apps) 
- 인증에 [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview) 컨트롤, [WebView2](/microsoft-edge/webview2/) 또는 이전 WebBrowser 컨트롤을 사용하는 Windows 앱. 해당 앱은 WAM(웹 계정 관리자) 흐름을 사용하여 마이그레이션해야 합니다.
- WebView UI 요소를 사용하는 Android 애플리케이션 
- UIWebView/WKWebview를 사용하는 iOS 애플리케이션 
- [ADAL을 사용하는 앱](../develop/howto-get-list-of-all-active-directory-auth-library-apps.md)

해당 변경 내용은 다음에는 적용되지 않습니다.
- 웹앱
- 웹 사이트를 통해 액세스되는 Microsoft 365 서비스(예: SharePoint Online, Office 웹앱 및 Teams 웹앱)
- 인증에 시스템 웹 보기를 사용하는 모바일 앱(iOS의 경우 [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller), Android의 경우 [Custom Tabs](https://developer.chrome.com/docs/android/custom-tabs/overview/)).  
- Google Workspace ID(예: Google Workspace에서 [SAML 기반 페더레이션](direct-federation.md)을 사용하는 경우)

이 변경 내용이 다음에 영향을 주는지 여부를 Google을 통해 확인하고 있습니다.
- WAM(웹 계정 관리자) 또는 WAB(웹 인증 브로커)를 사용하는 Windows 앱.  

다양한 플랫폼과 시나리오를 계속해서 테스트하고 있으며, 이에 따라 이 문서를 업데이트합니다.

### <a name="action-needed-for-embedded-web-views"></a>포함된 웹 보기에 필요한 작업

로그인에 시스템 브라우저를 사용하도록 앱을 수정합니다. 자세한 내용은 MSAL.NET 설명서의 [포함된 UI와 시스템 웹 UI 비교](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)를 참조하세요. 모든 MSAL SDK는 기본적으로 시스템 웹 보기를 사용합니다.

### <a name="what-to-expect"></a>필요한 항목

Google이 해당 변경 내용을 2021년 9월 30일에 적용하기 전에 Microsoft에서는 여전히 포함된 웹 보기를 사용하는 앱에 대한 해결 방법을 배포하여 인증이 차단되지 않도록 할 예정입니다. 포함된 웹 보기에서 Gmail 계정으로 로그인하는 사용자는 로그인을 완료하기 위해 별도의 브라우저에 코드를 입력하라는 메시지가 표시됩니다.

또는 기존 및 신규 Gmail 사용자가 이메일 일회용 암호로 로그인하도록 할 수 있습니다. Gmail 사용자가 이메일 일회용 암호를 사용하도록 하려면 다음을 수행합니다.
1. [이메일 일회용 암호를 사용하도록 설정](one-time-passcode.md#enable-email-one-time-passcode)
2. [Google 페더레이션 제거](google-federation.md#how-do-i-remove-google-federation)
3. 앞으로 이메일 1회용 암호를 사용할 수 있도록 Gmail 사용자의 [사용 상태를 재설정](reset-redemption-status.md)합니다.

인증 가능한 웹 보기로 마이그레이션되는 애플리케이션은 영향을 받지 않으며, 사용자는 평소처럼 Google을 통해 인증할 수 있습니다.

애플리케이션이 인증 가능한 웹 보기로 마이그레이션되지 않은 경우 영향을 받는 Gmail 사용자에게는 다음 화면이 표시됩니다.

![앱이 시스템 브라우저로 마이그레이션되지 않은 경우 Google 로그인 오류 발생](media/google-federation/google-sign-in-error-ewv.png)

Google에서 날짜 및 추가 세부 정보 공유 시 이 문서를 업데이트할 예정입니다.

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>CEF/Electron과 포함된 웹 보기 구분

[포함된 웹 보기 및 프레임워크 로그인 지원 중단](#deprecation-of-web-view-sign-in-support) 외에도 Google은 [CEF(Chromium Embedded Framework) 기반 Gmail 인증도 중단](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)할 예정입니다. Electron 앱과 같이 CEF를 기반으로 하는 애플리케이션의 경우 Google은 2021년 6월 30일부터 인증을 지원하지 않습니다. 영향을 받는 애플리케이션은 Google로부터 직접 알림을 받았으므로 이 설명서에서는 다루지 않습니다.  이 문서는 위에서 설명한 포함된 웹 보기와 관련이 있으며, 이에 대해 Google은 2021년 9월 30일, 별도의 일정을 두고 제한할 예정입니다.

### <a name="action-needed-for-embedded-frameworks"></a>포함된 프레임워크에 필요한 작업

[Google의 지침](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)에 따라 앱이 영향을 받는지 확인합니다.

## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성

먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure AD(Azure Active Directory)에 추가할 수 있는 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 
1. https://console.developers.google.com 에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 해당 계정을 사용하라는 메시지가 표시되면 서비스 약관에 동의합니다.
3. 새 프로젝트 만들기: 페이지의 왼쪽 위 모서리에서 프로젝트 목록을 선택한 다음, **프로젝트 선택** 페이지에서 **새 프로젝트** 를 선택합니다.
4. **새 프로젝트** 페이지에서 프로젝트 이름(예: **Azure AD B2B**)을 지정한 다음, **만들기** 를 선택합니다. 
   
   ![새 프로젝트 페이지를 보여주는 스크린샷.](media/google-federation/google-new-project.png)

4. **API 및 서비스** 페이지의 새 프로젝트 아래에서 **보기** 를 선택합니다.

5. API 카드에서 **Go to APIs overview**(API 개요로 이동)를 선택합니다. **OAuth 동의 화면** 을 선택합니다.

6. **외부** 를 선택한 다음, **만들기** 를 선택합니다. 

7. **OAuth 동의 화면** 에서 **애플리케이션 이름** 을 입력합니다.

   ![Google OAuth 동의 화면을 보여주는 스크린샷.](media/google-federation/google-oauth-consent-screen.png)

8. **권한 있는 도메인** 섹션으로 스크롤하고 **microsoftonline.com** 을 입력합니다.

   ![권한 있는 도메인 섹션을 보여주는 스크린샷.](media/google-federation/google-oauth-authorized-domains.PNG)

9. **저장** 을 선택합니다.

10. **자격 증명** 을 선택합니다. **자격 증명 생성** 메뉴에서 **OAuth 클라이언트 ID** 를 선택합니다.

    ![Google API 자격 증명 생성 메뉴를 보여주는 스크린샷.](media/google-federation/google-api-credentials.png)

11. **애플리케이션 형식** 에서 **웹 애플리케이션** 을 선택합니다. **AZURE AD B2B** 와 같이 애플리케이션에 적합한 이름을 지정합니다. **권한 있는 URI** 에서 다음 URI를 입력합니다.
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(여기서 `<tenant ID>`는 테넌트 ID임)
    - `https://login.microsoftonline.com/te/<tenant name>.onmicrosoft.com/oauth2/authresp` <br>(여기서 `<tenant name>`은 테넌트 이름임)
   
    > [!NOTE]
    > 테넌트 ID를 찾으려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 에서 **속성** 을 선택하고 **테넌트 ID** 를 복사합니다.

    ![권한 있는 리디렉션 URI 섹션을 보여주는 스크린샷.](media/google-federation/google-create-oauth-client-id.png)

12. **만들기** 를 선택합니다. 클라이언트 ID 및 클라이언트 암호를 복사합니다. Azure Portal에서 ID 공급자를 추가할 때 복사한 정보를 사용합니다.

    ![OAuth 클라이언트 ID 및 클라이언트 암호를 보여주는 스크린샷.](media/google-federation/google-auth-client-id-secret.png)

13. 프로젝트 게시 상태를 **테스트 중** 으로 두고 OAuth 동의 화면에 테스트 사용자를 추가할 수 있습니다. 또는 OAuth 동의 화면에서 **앱 게시** 단추를 선택하여 Google 계정이 있는 모든 사용자가 앱을 사용할 수 있도록 할 수 있습니다.

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2단계: Azure AD에서 Google 페더레이션 구성 

이제 Google 클라이언트 ID와 클라이언트 암호를 설정합니다. Azure Portal 또는 PowerShell을 사용하여 설정할 수 있습니다. 자신을 초대하여 Google 페더레이션 구성을 테스트해야 합니다. Gmail 주소를 사용하고 초대된 Google 계정으로 초대를 사용해 보세요. 

**Azure Portal에서 Google 페더레이션을 구성하려면 다음을 수행합니다.** 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택한 다음 **Google** 단추를 선택합니다.
4. 미리 복사해 둔 클라이언트 ID와 클라이언트 암호를 입력합니다. **저장** 을 선택합니다. 

   ![Google ID 공급자 추가 페이지를 보여주는 스크린샷.](media/google-federation/google-identity-provider.png)

**PowerShell을 사용하여 Google 페더레이션을 구성하려면 다음을 수행합니다.**
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령을 실행합니다. `Connect-AzureAD`
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > “1단계: Google 개발자 프로젝트 구성”에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용합니다. 자세한 내용은 [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true)를 참조하세요. 

## <a name="how-do-i-remove-google-federation"></a>Google 페더레이션은 어떻게 제거하나요?

Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 삭제하면 이미 초대에 응한 Google 게스트 사용자는 로그인할 수 없습니다. 하지만 [해당 상환 상태를 재설정](reset-redemption-status.md)하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다.
 
**Azure AD 포털에서 Google 페더레이션을 삭제하려면 다음을 수행합니다.**
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. **Google** 줄에서 줄임표 단추( **...** )를 선택한 다음 **삭제** 를 선택합니다. 
   
   ![소셜 ID 공급자의 삭제 단추를 보여주는 스크린샷.](media/google-federation/google-social-identity-providers.png)

1. **예** 를 선택하여 삭제를 확인합니다. 

**PowerShell을 사용하여 Google 페더레이션을 삭제하려면 다음을 수행합니다.** 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리형 전역 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true)를 참조하세요.
