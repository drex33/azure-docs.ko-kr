---
title: '자습서: Microsoft Graph 비즈니스 데이터에 액세스하는 다중 테넌트 디먼 빌드 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 Windows 데스크톱(WPF) 애플리케이션에서 Azure Active Directory를 통해 보호되는 ASP.NET 웹 API를 호출하는 방법을 알아봅니다. WPF 클라이언트는 사용자를 인증하고, 액세스 토큰을 요청하고, 웹 API를 호출합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, has-adal-ref
ms.openlocfilehash: 0deaa92659cbd022444c0e4d43389f39ebbaa51c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050359"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>자습서: Microsoft ID 플랫폼을 사용하는 다중 테넌트 디먼 빌드

이 자습서에서는 OAuth 2.0 클라이언트 자격 증명 부여를 사용하여 Microsoft Graph API를 호출하는 액세스 토큰을 가져오는 방법을 보여주는 ASP.NET 디먼 웹앱을 다운로드하고 실행합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 디먼 앱과 Microsoft ID 플랫폼 통합
> * 관리자가 앱에 직접 애플리케이션 권한 부여
> * Microsoft Graph API를 호출할 액세스 토큰 가져오기
> * Microsoft Graph API를 호출합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Visual Studio 2017 또는 2019](https://visualstudio.microsoft.com/downloads/)
- Azure AD 테넌트. 자세한 내용은 [Azure AD 테넌트를 가져오는 방법](quickstart-create-new-tenant.md)을 참조하세요.
- Azure AD 테넌트에 있는 하나 이상의 사용자 계정. 이 샘플은 Microsoft 계정에서 작동하지 않습니다. Microsoft 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하여 사용자 계정을 디렉터리에 만든 적이 없는 경우 지금 이 작업을 수행합니다.

## <a name="scenario"></a>시나리오

앱은 ASP.NET MVC 애플리케이션으로 빌드됩니다. OWIN OpenID Connect 미들웨어를 사용하여 사용자를 로그인합니다.

이 샘플의 "daemon" 구성 요소는 `SyncController.cs` API 컨트롤러입니다. 컨트롤러가 호출되면 Microsoft Graph에서 고객의 Azure AD(Azure Active Directory) 테넌트에 있는 사용자 목록을 가져옵니다. `SyncController.cs`는 웹 애플리케이션의 AJAX 호출을 통해 트리거되며, [.NET용 MSAL(Microsoft 인증 라이브러리)](msal-overview.md)을 사용하여 Microsoft Graph에 대한 액세스 토큰을 획득합니다.

이 앱은 Microsoft 비즈니스 고객을 위한 다중 테넌트 앱이므로 고객이 애플리케이션을 회사 데이터에 "가입"하거나 "연결"할 수 있는 방법을 제공해야 합니다. 연결 흐름 중에 전역 관리자는 먼저 로그인한 사용자가 없어도 비대화형 방식으로 회사 데이터에 액세스할 수 있도록 *애플리케이션 권한* 을 앱에 직접 부여합니다. 이 샘플의 논리 대부분에서는 ID 플랫폼의 [관리자 동의](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) 엔드포인트를 사용하여 이 연결 흐름을 수행하는 방법을 보여 줍니다.

![다이어그램은 Azure에 연결되는 세 개의 로컬 항목이 있는 UserSync 앱을 보여줍니다. Start dot Auth는 대화형으로 토큰을 획득하여 Azure AD에 연결하고, AccountController는 Azure AD에 연결하기 위한 관리자 동의를 가져오고, SyncController는 사용자를 읽어 Microsoft Graph에 연결합니다.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

이 샘플에 사용되는 개념에 대한 자세한 내용은 [ID 플랫폼의 클라이언트 자격 증명 프로토콜 설명서](v2-oauth2-client-creds-grant-flow.md)를 참조하세요.

## <a name="clone-or-download-this-repository"></a>리포지토리 복제 또는 다운로드

셸 또는 명령줄에서 다음 명령을 입력합니다.

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

또는 [샘플을 ZIP 파일로 다운로드](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)합니다.

## <a name="register-your-application"></a>애플리케이션 등록

이 샘플에는 하나의 프로젝트가 있습니다. Azure AD 테넌트에 애플리케이션을 등록하려면 다음 중 하나를 수행합니다.

- [Azure Active Directory 테넌트에 샘플 등록](#register-the-client-app-dotnet-web-daemon-v2) 및 [Azure AD 테넌트를 사용하도록 샘플 구성](#choose-the-azure-ad-tenant)의 단계를 수행합니다.
- 다음을 수행하는 PowerShell 스크립트를 사용합니다.
  - Azure AD 애플리케이션 및 관련 개체(암호, 권한, 종속성)를 *자동으로* 만듭니다.
  - Visual Studio 프로젝트의 구성 파일을 수정합니다.

자동화를 사용하려면 다음을 수행합니다.

1. Windows에서 PowerShell을 실행하고, 복제된 디렉터리의 루트로 이동합니다.
1. 다음 명령을 실행합니다.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 스크립트를 실행하여 Azure AD 애플리케이션을 만들고, 이에 따라 샘플 애플리케이션의 코드를 구성합니다.

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   스크립트를 실행하는 다른 방법은 [앱 만들기 스크립트](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)에서 설명하고 있습니다.

1. Visual Studio 솔루션을 열고, **시작** 을 선택하여 코드를 실행합니다.

자동화를 사용하지 않으려면 다음 섹션의 단계를 사용합니다.

### <a name="choose-the-azure-ad-tenant"></a>Azure AD 테넌트 선택

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>클라이언트 앱(dotnet-web-daemon-v2) 등록

1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `dotnet-web-daemon-v2`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
1. **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정** 을 선택합니다.
1. **리디렉션 URI(선택 사항)** 섹션의 콤보 상자에서 **웹** 을 선택하고 리디렉션 URI로 `https://localhost:44316/` 및 `https://localhost:44316/Account/GrantPermissions`를 입력합니다.

    셋 이상의 리디렉션 URI가 있는 경우 나중에 앱이 성공적으로 만들어지면 **인증** 탭에서 이러한 URI를 추가해야 합니다.
1. **등록** 을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 Visual Studio 구성 파일을 구성하는 데 필요합니다.
1. **관리** 에서 **인증** 을 선택합니다.
1. **프런트 채널 로그 아웃 URL** 을 `https://localhost:44316/Account/EndSession`으로 설정합니다.
1. **암시적 허용 및 하이브리드 흐름** 섹션에서 **액세스 토큰** 및 **ID 토큰** 을 선택합니다. 이 샘플에서는 사용자를 로그인하고 API를 호출하는 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다.
1. **저장** 을 선택합니다.
1. **관리** 에서 **인증서 및 암호** 를 선택합니다.
1. **클라이언트 비밀** 섹션에서 **새 클라이언트 비밀** 을 선택합니다. 
1. 키 설명(예: **앱 비밀**)을 입력합니다.
1. 키 기간을 **1년 후**, **2년 후** 또는 **만료 기한 제한 없음** 으로 선택합니다.
1. **추가** 를 선택합니다. 안전한 위치에 키 값을 기록합니다. 나중에 Visual Studio에서 프로젝트를 구성하려면 이 키가 필요합니다.
1. **관리** 에서 **API 권한** > **권한 추가** 를 선택합니다.
1. **일반적으로 사용되는 Microsoft API** 섹션에서 **Microsoft Graph** 를 선택합니다.
1. **애플리케이션 권한** 섹션에서 올바른 권한(**User.Read.All**)이 선택되었는지 확인합니다.
1. **권한 추가** 를 선택합니다.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Azure AD 테넌트를 사용하도록 샘플 구성

다음 단계에서 **ClientID** 는 "애플리케이션 ID" 또는 **AppId** 와 동일합니다.

Visual Studio에서 솔루션을 열어 프로젝트를 구성합니다.

### <a name="configure-the-client-project"></a>클라이언트 프로젝트 구성

설치 스크립트를 사용한 경우 변경 내용이 다음과 같이 적용됩니다.

1. **UserSync\Web.Config** 파일을 엽니다.
1. **ida:ClientId** 앱 키를 찾습니다. 기존 값을 Azure Portal에서 복사한 **dotnet-web-daemon-v2** 애플리케이션의 애플리케이션 ID로 바꿉니다.
1. **ida:ClientSecret** 앱 키를 찾습니다. 기존 값을 Azure Portal에서 **dotnet-web-daemon-v2** 앱을 만드는 동안 저장한 키로 바꿉니다.

## <a name="run-the-sample"></a>샘플 실행

솔루션을 정리하고, 솔루션을 다시 빌드하고, UserSync 애플리케이션을 실행한 다음, 관리자 권한으로 Azure AD 테넌트에 로그인합니다. 테스트할 Azure AD 테넌트가 없으면 [이 지침](quickstart-create-new-tenant.md)에 따라 가져올 수 있습니다.

로그인하면 앱에서 먼저 사용자에게 로그인하고 사용자 프로필을 읽을 수 있는 권한을 요청합니다. 이 동의를 통해 앱에서 사용자가 비즈니스 사용자임을 확인할 수 있습니다.

![사용자 승인](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

그러면 앱에서 Microsoft Graph를 통해 Azure AD 테넌트의 사용자 목록을 동기화하려고 합니다. 동기화할 수 없는 경우 테넌트 관리자에게 테넌트를 앱에 연결하도록 요청합니다.

그러면 앱에서 테넌트의 사용자 목록을 읽을 수 있는 권한을 요청합니다.

![관리자 동의](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

권한을 부여한 후에는 앱에서 로그아웃됩니다. 이 로그아웃을 통해 토큰 캐시에서 Microsoft Graph용 기존 액세스 토큰이 제거됩니다. 다시 로그인하면 획득한 새 토큰에 Microsoft Graph를 호출하는 데 필요한 권한이 부여됩니다.


권한이 부여되면 앱에서 언제든지 사용자를 쿼리할 수 있습니다. **사용자 동기화** 단추를 선택하고 사용자 목록을 새로 고쳐 이를 확인할 수 있습니다. 사용자를 추가하거나 제거하고 목록을 다시 동기화합니다. (그러나 앱은 사용자의 첫 페이지만 동기화합니다.)

## <a name="about-the-code"></a>코드 정보

이 샘플의 관련 코드는 다음 파일에 있습니다.

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: 초기 로그인입니다. 특히 컨트롤러의 작업에는 사용자가 강제로 로그인할 수 있도록 하는 **Authorize**(권한 부여) 특성이 있습니다. 애플리케이션에서 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 사용자를 로그인합니다.
- **Controllers\SyncController.cs**: 사용자 목록을 로컬 메모리 내 저장소에 동기화합니다.
- **Controllers\UserController.cs**: 로컬 메모리 내 저장소의 사용자 목록을 표시합니다.
- **Controllers\AccountController.cs**: 관리자 동의 엔드포인트를 사용하여 테넌트 관리자로부터 권한을 획득합니다.

## <a name="re-create-the-sample-app"></a>샘플 앱 다시 만들기

1. Visual Studio에서 새 **Visual C#** **ASP.NET 웹 애플리케이션(.NET Framework)** 프로젝트를 만듭니다.
1. 다음 화면에서 **MVC** 프로젝트 템플릿을 선택합니다. 나중에 웹 API 컨트롤러를 추가하므로 **Web API** 에 대한 폴더 및 코어 참조도 추가합니다. 선택된 프로젝트의 인증 모드를 기본값, 즉 **인증 없음** 으로 둡니다.
1. **솔루션 탐색기** 창에서 프로젝트, **F4** 키를 차례로 선택합니다.
1. 프로젝트 속성에서 **SSL 사용** 을 **True** 로 설정합니다. **SSL URL** 의 정보를 적어둡니다. 이는 Azure Portal에서 이 애플리케이션의 등록을 구성할 때 필요합니다.
1. 다음 ASP.NET OWIN 미들웨어 NuGet 패키지를 추가합니다.
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. **App_Start** 폴더에서 다음을 수행합니다.
   1. **Startup.Auth.cs** 라는 클래스를 만듭니다.
   1. 네임스페이스 이름에서 **.App_Start** 를 제거합니다.
   1. **Startup** 클래스의 코드를 샘플 앱의 동일한 파일에 있는 코드로 바꿉니다.
   클래스 정의 전체를 사용해야 합니다. 정의가 **퍼블릭 클래스 Startup** 에서 **퍼블릭 partial 클래스 Startup** 으로 변경됩니다.
1. **Startup.Auth.cs** 에서 Visual Studio IntelliSense에서 제안한 대로 **using** 문을 추가하여 누락된 참조를 확인합니다.
1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** 를 선택한 다음, **클래스** 를 선택합니다.
1. 검색 상자에서 **OWIN** 을 입력합니다. **OWIN Startup 클래스** 가 선택 항목으로 표시됩니다. 이를 선택한 다음, 클래스 이름을 **Startup.cs** 로 지정합니다.
1. **Startup.cs** 에서 **Startup** 클래스의 코드를 샘플 앱의 동일한 파일에 있는 코드로 바꿉니다. 다시 한 번 정의가 **퍼블릭 클래스 Startup** 에서 **퍼블릭 partial 클래스 Startup** 으로 변경됩니다.
1. **Models** 폴더에서 **MsGraphUser.cs** 라는 새 클래스를 추가합니다. 구현을 샘플에서 이름이 동일한 파일의 내용으로 바꿉니다.
1. **AccountController** 라는 새 **MVC 5 컨트롤러 - 비어 있음** 인스턴스를 추가합니다. 구현을 샘플에서 이름이 동일한 파일의 내용으로 바꿉니다.
1. **UserController** 라는 새 **MVC 5 컨트롤러 - 비어 있음** 인스턴스를 추가합니다. 구현을 샘플에서 이름이 동일한 파일의 내용으로 바꿉니다.
1. **SyncController** 라는 새 **Web API 2 컨트롤러 - 비어 있음** 인스턴스를 추가합니다. 구현을 샘플에서 이름이 동일한 파일의 내용으로 바꿉니다.
1. 사용자 인터페이스의 경우 **Views\Account** 폴더에서 **GrantPermissions**, **Index** 및 **UserMismatch** 라는 세 개의 **비어 있음(모델 없음) 보기** 인스턴스를 추가합니다. **Index** 라는 인스턴스를 **Views\User** 폴더에 추가합니다. 구현을 샘플에서 이름이 동일한 파일의 내용으로 바꿉니다.
1. 다양한 보기를 모두 올바르게 연결하도록 **Shared\_Layout.cshtml** 및 **Home\Index.cshtml** 을 업데이트합니다.

## <a name="deploy-the-sample-to-azure"></a>Azure에 샘플 배포

이 프로젝트에는 웹앱 및 웹 API 프로젝트가 있습니다. Azure 웹 사이트에 배포하려면 각 웹 사이트에 대해 다음 단계를 수행합니다.

1. Azure 웹 사이트를 만듭니다.
1. 웹앱 및 웹 API를 웹 사이트에 게시합니다.
1. IIS Express 대신 웹 사이트를 호출하도록 클라이언트를 업데이트합니다.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Azure 웹 사이트에 dotnet-web-daemon-v2 만들기 및 게시

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 왼쪽 위 구석에서 **리소스 만들기** 를 선택합니다.
1. **웹** > **웹앱** 을 차례로 선택한 다음, 웹 사이트 이름을 지정합니다. 예를 들어 이름을 **dotnet-web-daemon-v2-contoso.azurewebsites.net** 으로 지정합니다.
1. **구독**, **리소스 그룹** 및 **앱 서비스 계획 및 위치** 에 대한 정보를 선택합니다. **OS** 는 **Windows** 이고 **게시** 는 **코드** 입니다.
1. **만들기** 를 선택하고, 앱 서비스가 만들어질 때까지 기다립니다.
1. **배포 성공** 알림이 표시되면 **리소스로 이동** 을 선택하여 새로 만든 앱 서비스로 이동합니다.
1. 웹 사이트가 만들어지면 **대시보드** 에서 해당 웹 사이트를 찾아서 선택하여 앱 서비스의 **개요** 화면을 엽니다.
1. 앱 서비스의 **개요** 탭에서 **게시 프로필 가져오기** 링크를 선택하여 게시 프로필을 다운로드하고 저장합니다. 원본 제어에서 배포와 같은 다른 배포 메커니즘을 사용할 수 있습니다.
1. Visual Studio로 전환하고 다음을 수행합니다.
   1. **dotnet-web-daemon-v2** 프로젝트로 이동합니다.
   1. 솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭한 다음, **게시** 를 선택합니다.
   1. 아래쪽 표시줄에서 **프로필 가져오기** 를 선택하고, 이전에 다운로드한 게시 프로필을 가져옵니다.
1. **구성** 을 선택합니다.
1. **연결** 탭에서 "https"를 사용하도록 대상 URL을 업데이트합니다. 예를 들면 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`를 사용합니다. **다음** 을 선택합니다.
1. **설정** 탭에서 **조직 인증 사용** 이 선택 취소되어 있는지 확인합니다.
1. **저장** 을 선택합니다. 주 화면에서 **게시** 를 선택합니다.

Visual Studio에서 프로젝트를 게시하고, 브라우저를 프로젝트의 URL로 자동으로 엽니다. 프로젝트의 기본 웹 페이지가 표시되면 게시가 성공적으로 수행된 것입니다.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>dotnet-web-daemon-v2에 대한 Azure AD 테넌트 애플리케이션 등록 업데이트

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>로 이동합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스, **앱 등록** 을 차례로 선택합니다.
1. **dotnet-web-daemon-v2** 애플리케이션을 선택합니다.
1. 애플리케이션에 대한 **인증** 페이지에서 **프론트 채널 로그아웃 URL** 필드를 서비스 주소로 업데이트합니다. 예를 들면 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession`를 사용합니다.
1. **브랜딩** 메뉴에서 **홈 페이지 URL** 을 서비스 주소로 업데이트합니다. 예를 들면 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`를 사용합니다.
1. 구성을 저장합니다.
1. 동일한 URL을 **인증** > **리디렉션 URI** 메뉴의 값 목록에 추가합니다. 여러 개의 리디렉션 URL이 있는 경우 각 리디렉션 URL에 대해 앱 서비스의 URI를 사용하는 새 항목이 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 [애플리케이션 등록 단계](#register-your-application)에서 만든 애플리케이션 개체를 삭제합니다.  애플리케이션을 제거하려면 [사용자 또는 조직이 작성한 애플리케이션 제거](./howto-remove-app.md#remove-an-application-authored-by-you-or-your-organization)의 지침을 따르세요.

## <a name="get-help"></a>도움말 보기

[Microsoft Q&A](/answers/products/)를 사용하여 커뮤니티에서 지원을 받을 수 있습니다.
먼저 [Microsoft Q&A](/answers/products/)에 질문하고, 기존 문제를 검색하여 이전에 누군가가 질문했는지 확인합니다.
질문 또는 의견에 "azure-ad-adal-deprecation," "azure-ad-msal," 및 "dotnet-standard" 태그가 지정되어 있는지 확인합니다.

샘플에 버그가 있으면 해당 문제를 [GitHub 문제](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)에 제기하세요.

MSAL.NET에 버그가 있으면 해당 문제를 [MSAL.NET GitHub 문제](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)에 제기하세요.

추천 사항을 제공하려면 [사용자 의견 페이지](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)로 이동하세요.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼을 사용하여 보호된 웹 API에 액세스하는 디먼 앱을 빌드하는 방법에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [시나리오: 웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)