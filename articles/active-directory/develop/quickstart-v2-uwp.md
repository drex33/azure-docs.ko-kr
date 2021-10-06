---
title: '빠른 시작: 유니버설 Windows 플랫폼 앱에서 사용자 로그인 및 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 UWP(유니버설 Windows 플랫폼) 애플리케이션이 액세스 토큰을 가져오고, Microsoft ID 플랫폼으로 보호되는 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: e9353c12c20ea8b08164ac057f886ca47cc8c383
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552751"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>빠른 시작: 유니버설 Windows 플랫폼(UWP) 애플리케이션에서 Microsoft Graph API 호출

이 빠른 시작에서는 UWP(유니버설 Windows 플랫폼) 애플리케이션이 사용자를 로그인하고 Microsoft Graph API를 호출할 액세스 토큰을 가져오는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 

자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>필수 구성 요소
>
> * 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> [!div renderon="docs" class="sxs-lookup"]
> 빠른 시작 애플리케이션을 시작하는 옵션은 두 가지가 있습니다.
> * [기본] [옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [수동] [옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure Portal - 앱 등록</a> 빠른 시작 환경으로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록** 을 선택합니다.
> 1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 추가하려면 다음 단계를 따르세요.
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `UWP-App-calling-MsGraph`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
> 1. **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)** 을 선택합니다.
> 1. **등록** 을 선택하여 애플리케이션을 만든 다음, 이후 단계에서 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.
> 1. **관리** 에서 **인증** 을 선택합니다.
> 1. **플랫폼 추가** > **모바일 및 데스크톱 애플리케이션** 을 차례로 선택합니다.
> 1. **리디렉션 URI** 아래에서 `https://login.microsoftonline.com/common/oauth2/nativeclient`를 선택합니다.
> 1. **구성** 을 선택합니다.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>1단계: 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 `https://login.microsoftonline.com/common/oauth2/nativeclient`의 **리디렉션 URI** 를 추가합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-uwp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-visual-studio-project"></a>2단계: Visual Studio 프로젝트 다운로드

> [!div renderon="docs"]
> [Visual Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019를 사용하여 프로젝트를 실행합니다.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱 속성 값을 사용하여 프로젝트를 구성했고 실행할 준비가 되었습니다.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>3단계: Visual Studio 프로젝트 구성
>
> 1. .zip 보관 파일을 드라이브 루트에 가까운 로컬 폴더에 추출합니다. 예를 들어 **C:\Azure-Samples** 입니다.
> 1. Visual Studio에서 프로젝트를 엽니다. 메시지가 표시되면 **유니버설 Windows 플랫폼 개발** 워크로드 및 개별 SDK 구성 요소를 설치합니다.
> 1. *MainPage.Xaml.cs* 에서 `ClientId` 변수의 값을 이전에 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 로 변경합니다.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    **애플리케이션(클라이언트) ID** 는 Azure Portal(**Azure Active Directory** > **애플리케이션 등록** >  *{사용자의 앱 등록}* )에 있는 앱의 **개요** 창에서 찾을 수 있습니다.
> 1. 패키지에 대한 새 자체 서명된 테스트 인증서를 만든 다음, 이를 선택합니다.
>     1. **솔루션 탐색기** 에서 *Package.appxmanifest* 파일을 두 번 클릭합니다.
>     1. **패키징** > **인증서 선택...**  > **만들기...** 를 차례로 선택합니다.
>     1. 암호를 입력한 다음, **확인** 을 선택합니다. *Native_UWP_V2_TemporaryKey.pfx* 라는 인증서가 만들어집니다. 
>     1. **확인** 을 선택하여 **인증서 선택** 대화 상자를 해제한 다음, 솔루션 탐색기에 *Native_UWP_V2_TemporaryKey.pfx* 가 표시되는지 확인합니다.
>     1. **솔루션 탐색기** 에서 마우스 오른쪽 단추로 **Native_UWP_V2** 프로젝트를 클릭하고, **속성** 을 선택합니다.
>     1. **서명** 을 선택한 다음, **강력한 이름 키 파일 선택** 드롭다운에서 만든 .pfx를 선택합니다.

#### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

로컬 컴퓨터에서 샘플 애플리케이션을 실행합니다.

1. Visual Studio 도구 모음에서 올바른 플랫폼을 선택합니다(ARM이 아닌 **x64** 또는 **x86**). 대상 디바이스가 *디바이스* 에서 *로컬 컴퓨터* 로 변경됩니다.
1. **디버그** > **디버그하지 않고 시작** 을 선택합니다.
    
    이 작업을 수행하라는 메시지가 표시되면 먼저 **개발자 모드** 를 사용하도록 설정한 다음, **디버그하지 않고 시작** 을 다시 사용하도록 설정해야 할 수 있습니다.

앱 창이 표시되면 **Microsoft Graph API 호출** 단추를 선택하고, 자격 증명을 입력하고, 애플리케이션에서 요청한 권한에 동의할 수 있습니다. 성공하면 애플리케이션에서 Microsoft Graph API에 대한 호출에서 가져온 일부 토큰 정보 및 데이터를 표시합니다.

## <a name="how-the-sample-works"></a>샘플 작동 방법

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client))은 사용자를 로그인하고 보안 토큰을 요청하는 데 사용되는 라이브러리입니다. 보안 토큰은 Microsoft ID 플랫폼으로 보호되는 API에 액세스하는 데 사용됩니다. Visual Studio의 *패키지 관리자 콘솔* 에서 다음 명령을 실행하여 MSAL을 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```csharp
using Microsoft.Identity.Client;
```

그런 다음, 다음 코드를 사용하여 MSAL을 초기화합니다.

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

`ClientId`의 값은 Azure Portal에 등록한 앱의 **애플리케이션(클라이언트) ID** 입니다. 이 값은 Azure Portal에서 앱의 **개요** 페이지에 있습니다.

### <a name="requesting-tokens"></a>토큰 요청

MSAL에는 UWP 앱에서 토큰 획득을 위한 두 가지 메서드인 `AcquireTokenInteractive` 및 `AcquireTokenSilent`가 있습니다.

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

상황에 따라 사용자가 자격 증명의 유효성을 검사하거나 동의를 제공하기 위해 팝업 창을 통해 Microsoft ID 플랫폼과 상호 작용해야 합니다. 일부 사례:

- 사용자가 애플리케이션에 처음으로 로그인하는 경우
- 암호가 만료되어 사용자가 자격 증명을 다시 입력해야 할 경우
- 애플리케이션이 사용자 동의가 필요한 리소스에 액세스를 요청하고 있는 경우
- 2단계 인증이 필요한 경우

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes` 매개 변수에는 요청된 범위(예: Microsoft Graph의 경우 `{ "user.read" }` 또는 사용자 지정 웹 API의 경우 `{ "api://<Application ID>/access_as_user" }`)가 포함됩니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

초기 `AcquireTokenInteractive` 메서드 다음에 보호된 리소스에 액세스하는 토큰을 가져오려면 `AcquireTokenSilent` 메서드를 사용합니다. 사용자가 리소스에 액세스해야 할 때마다 자격 증명의 유효성을 검사할 필요가 없도록 하려고 합니다. 대부분의 경우 사용자 개입 없이 토큰 획득 및 갱신 처리

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes`에는 요청된 범위(예: Microsoft Graph의 경우 `{ "user.read" }` 또는 사용자 지정 웹 API의 경우 `{ "api://<Application ID>/access_as_user" }`)가 포함됩니다.
* `firstAccount`는 캐시의 첫 번째 사용자 계정을 지정합니다(MSAL은 단일 앱에서 여러 사용자를 지원함).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작의 전체 설명을 포함하여 애플리케이션 및 새로운 기능 빌드에 대한 완전한 단계별 가이드를 제공하는 Windows 데스크톱 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [UWP - Graph API 호출 자습서](tutorial-v2-windows-uwp.md)
