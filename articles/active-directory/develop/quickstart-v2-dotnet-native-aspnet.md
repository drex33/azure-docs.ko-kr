---
title: '빠른 시작: Microsoft ID 플랫폼으로 보호되는 ASP.NET 웹 API 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Windows 데스크톱(WPF) 애플리케이션에서 Microsoft ID 플랫폼으로 보호된 ASP.NET 웹 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/11/2022
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", mode-api
ms.openlocfilehash: 57bcd6562862210be21c17680842de44168c435b
ms.sourcegitcommit: 04420fb4695bd70408d9854ad5b2af8a9bbfbc64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2022
ms.locfileid: "136847815"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>빠른 시작: Microsoft ID 플랫폼으로 보호되는 ASP.NET 웹 API 호출

이 빠른 시작에서는 리소스에 대한 액세스를 인증된 계정으로만 제한하여 ASP.NET 웹 API를 보호하는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 이 샘플은 모든 Azure AD(Azure Active Directory) 조직에서 개인 Microsoft 계정 및 계정의 권한 부여를 지원합니다.

또한 이 문서에서는 WPF(Windows Presentation Foundation) 앱을 사용하여 웹 API에 액세스하기 위해 액세스 토큰을 요청하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 또는 2019 [체험용 Visual Studio](https://www.visualstudio.com/downloads/) 다운로드

## <a name="clone-or-download-the-sample"></a>샘플 복제 또는 다운로드

다음 두 가지 방법 중 하나로 샘플을 얻을 수 있습니다.

* 셸 또는 명령줄에서 복제합니다.

   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```

* [ZIP 파일로 다운로드합니다](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-the-web-api-todolistservice"></a>웹 API 등록(TodoListService)

Azure Portal의 **앱 등록** 에 웹 API를 등록합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트를 선택합니다.
1. **Azure Active Directory** 를 찾아서 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `AppModelv2-NativeClient-DotNet-TodoListService`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
1. **지원되는 계정 유형** 에는 **모든 조직 디렉터리의 계정** 을 선택합니다.
1. **등록** 을 선택하여 애플리케이션을 만듭니다.
1. 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾은 후 나중에 사용할 수 있도록 기록합니다. 이 프로젝트의 Visual Studio 구성 파일(즉, *TodoListService\Web.config* 파일의 `ClientId`)을 구성하는 데 필요합니다.
1. **관리** 에서 **API 표시** > **범위 추가** 를 선택합니다. **저장 및 계속** 을 선택하여 제안된 애플리케이션 ID URI(`api://{clientId}`)를 수락한 후, 다음 정보를 입력합니다.

    1. **범위 이름** 으로 `access_as_user`를 입력합니다.
    1. **동의할 수 있는 사람** 에 대해 **관리자 및 사용자** 옵션을 선택했는지 확인합니다.
    1. **관리자 동의 표시 이름** 상자에 `Access TodoListService as a user`를 입력합니다.
    1. **관리자 동의 설명** 상자에 `Accesses the TodoListService web API as a user`를 입력합니다.
    1. **사용자 동의 표시 이름** 상자에 `Access TodoListService as a user`를 입력합니다.
    1. **사용자 동의 설명** 상자에 `Accesses the TodoListService web API as a user`를 입력합니다.
    1. **상태** 는 **사용** 으로 유지합니다.
1. **범위 추가** 를 선택합니다.

### <a name="configure-the-service-project"></a>서비스 프로젝트 구성

등록된 웹 API와 일치하도록 서비스 프로젝트를 구성합니다.

1. Visual Studio에서 솔루션을 연 다음, TodoListService 프로젝트의 루트 아래에서 *Web.config* 파일을 엽니다.

1. `ida:ClientId` 매개 변수 값을 방금 **앱 등록** 포털에서 등록한 애플리케이션의 클라이언트 ID(애플리케이션 ID)로 바꿉니다.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>app.config 파일에 새 범위 추가

TodoListClient *app.config* 파일에 새 범위를 추가하려면 다음 단계를 수행합니다.

1. TodoListClient 프로젝트 루트 폴더에서 *app.config* 파일을 엽니다.

1. TodoListService 프로젝트에 대해 방금 등록한 애플리케이션의 애플리케이션 ID를 `TodoListServiceScope` 매개 변수에 붙여넣고 `{Enter the Application ID of your TodoListService from the app registration portal}` 문자열을 대체합니다.

  > [!NOTE]
  > 애플리케이션 ID가 `api://{TodoListService-Application-ID}/access_as_user` 형식을 사용하는지 확인합니다. 여기서 `{TodoListService-Application-ID}`는 TodoListService 앱의 애플리케이션 ID를 나타내는 GUID입니다.

## <a name="register-the-web-app-todolistclient"></a>웹앱 등록(TodoListClient)

Azure Portal의 **앱 등록** 에 TodoListClient 앱을 등록한 다음, TodoListClient 프로젝트에서 코드를 구성합니다. 클라이언트와 서버가 동일한 애플리케이션으로 간주되는 경우 2단계에서 등록한 애플리케이션을 재사용할 수 있습니다. 사용자가 개인 Microsoft 계정으로 로그인하도록 하려면 동일한 애플리케이션을 사용합니다.

### <a name="register-the-app"></a>앱 등록

TodoListClient 앱을 등록하려면 다음 단계를 수행합니다.

1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 포털로 이동합니다.
1. **새 등록** 을 선택합니다.
1. **애플리케이션 등록** 페이지가 열리면 애플리케이션의 등록 정보를 입력합니다.

    1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: **NativeClient-DotNet-TodoListClient**)을 입력합니다.
    1. **지원되는 계정 유형** 에는 **모든 조직 디렉터리의 계정** 을 선택합니다.
    1. **등록** 을 선택하여 애플리케이션을 만듭니다.

   > [!NOTE]
   > TodoListClient 프로젝트의 *app.config* 파일에서 `ida:Tenant`의 기본값은 `common`으로 설정됩니다. 가능한 값은 다음과 같습니다.
   >
   > - `common`: 회사 또는 학교 계정이나 개인 Microsoft 계정을 사용하여 로그인할 수 있습니다(이전 단계에서 모든 **조직 디렉터리의 계정** 을 선택했기 때문).
   > - `organizations`: 회사 또는 학교 계정을 사용하여 로그인할 수 있습니다.
   > - `consumers`: Microsoft 개인 계정을 통해서만 로그인할 수 있습니다.

1. 앱 **개요** 페이지에서 **인증** 을 선택한 후 다음 단계를 완료하여 플랫폼을 추가합니다.

    1. **플랫폼 구성** 에서 **플랫폼 추가** 단추를 선택합니다.
    1. **모바일 및 데스크톱 애플리케이션** 의 경우 **모바일 및 데스크톱 애플리케이션** 을 선택합니다.
    1. **리디렉션 URI** 의 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient` 확인란을 선택합니다.
    1. **구성** 을 선택합니다.

1. **API 권한** 을 선택한 후 다음 단계를 완료하여 권한을 추가합니다.

    1. **권한 추가** 를 선택합니다.
    1. **내 API** 탭을 선택합니다.
    1. API 목록에서 **AppModelv2-NativeClient-DotNet-TodoListService API** 또는 웹 API에 대해 입력한 이름을 선택합니다.
    1. **access_as_user** 권한 확인란을 아직 선택하지 않았으면 선택합니다. 필요한 경우 검색 상자를 사용합니다.
    1. **사용 권한 추가** 단추를 선택합니다.

### <a name="configure-your-project"></a>프로젝트 구성

애플리케이션 ID를 *app.config* 파일에 추가하여 TodoListClient 프로젝트를 구성합니다.

1. **앱 등록** 포털의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 복사합니다.

1. TodoListClient 프로젝트 루트 폴더에서 *app.config* 파일을 열고 `ida:ClientId` 매개 변수에 애플리케이션 ID 값을 붙여넣습니다.

## <a name="run-your-projects"></a>프로젝트를 실행합니다.

두 프로젝트를 시작합니다. Visual Studio를 사용하는 경우:

1. Visual Studio 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.

1. **공용 속성** 에서 **시작 프로젝트** 를 선택한 다음 **여러 시작 프로젝트** 를 선택합니다. 

1. 두 프로젝트 모두 작업으로 **시작** 을 선택합니다.

1. 위쪽 화살표를 사용하여 목록의 첫 번째 위치로 이동하여 TodoListService 서비스가 먼저 시작되는지 확인합니다.

TodoListClient 프로젝트를 실행하려면 로그인합니다.

1. F5 키를 눌러 프로젝트를 시작합니다. 서비스 페이지와 데스크톱 애플리케이션이 열립니다.

1. TodoListClient에서 오른쪽 위에 있는 **로그인** 을 선택한 다음, 애플리케이션을 등록하는 데 사용한 것과 동일한 자격 증명으로 로그인하거나, 동일한 디렉터리에서 사용자로 로그인합니다.

   처음으로 로그인하는 경우 TodoListService 웹 API에 동의하라는 메시지가 표시될 수 있습니다.

   TodoListService 웹 API에 액세스하고 *할 일* 목록을 조작하는 데 도움이 되도록, 로그인할 때 *access_as_user* 범위에 대한 액세스 토큰도 요청합니다.

## <a name="pre-authorize-your-client-application"></a>클라이언트 애플리케이션 미리 인증

웹 API에 액세스할 수 있도록 클라이언트 애플리케이션에 미리 권한을 부여하여 다른 디렉터리의 사용자가 웹 API에 액세스하도록 허용할 수 있습니다. 웹 API에 대해 미리 권한이 부여된 애플리케이션 목록에 클라이언트 앱의 애플리케이션 ID를 추가하면 됩니다. 미리 권한이 부여된 클라이언트를 추가하면 사용자가 동의하지 않고도 웹 API에 액세스할 수 있습니다.

1. **앱 등록** 포털에서 TodoListService 앱의 속성을 엽니다.
1. **API 표시** 섹션의 **권한 있는 클라이언트 애플리케이션** 아래에서 **클라이언트 애플리케이션 추가** 를 선택합니다.
1. **클라이언트 ID** 상자에 TodoListClient 앱의 애플리케이션 ID를 붙여넣습니다.
1. **권한 있는 범위** 섹션에서 `api://<Application ID>/access_as_user` 웹 API의 범위를 선택합니다.
1. **애플리케이션 추가** 를 선택합니다.

### <a name="run-your-project"></a>프로젝트 실행

1. <kbd>F5</kbd> 키를 눌러 프로젝트를 실행합니다. TodoListClient 앱이 열립니다.
1. 오른쪽 위에서 **로그인** 을 선택하고 개인 Microsoft 계정(예: *live.com* 또는 *hotmail.com* 계정)이나 회사 또는 학교 계정을 사용하여 로그인합니다.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>선택 사항: 특정 사용자로 로그인 액세스 제한

기본적으로 *outlook.com* 또는 *live.com* 계정과 같은 개인 계정이나 Azure AD와 통합된 조직의 회사 또는 학교 계정은 토큰을 요청하고 웹 API에 액세스할 수 있습니다.

애플리케이션에 로그인할 수 있는 사람을 지정하려면 다음 옵션 중 하나를 사용합니다.

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>옵션 1: 단일 조직(단일 테넌트)에 대한 액세스 제한

애플리케이션에 대한 로그인 액세스를 단일 Azure AD 테넌트에 있는 사용자 계정(테넌트의 게스트 계정 포함)으로 제한할 수 있습니다. 이 시나리오는 LOB(기간 업무) 애플리케이션에 공통적으로 적용됩니다.

1. *App_Start\Startup.Auth* 파일을 연 다음, `OpenIdConnectSecurityTokenProvider`로 전달되는 메타데이터 엔드포인트의 값을 `https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration`으로 변경합니다. `contoso.onmicrosoft.com`과 같은 테넌트 이름을 사용할 수도 있습니다.
1. 같은 파일에서 `TokenValidationParameters`의 `ValidIssuer` 속성을 `https://sts.windows.net/{Tenant ID}/`으로 설정하고 `ValidateIssuer` 인수를 `true`로 설정합니다.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>옵션 2: 사용자 지정 메서드를 사용하여 발급자 유효성 검사

`IssuerValidator` 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현할 수 있습니다. 이 매개 변수에 대한 자세한 내용은 [TokenValidationParameters 클래스](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters)를 참조하세요.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 지원하는 보호된 웹 API 시나리오에 대해 자세히 알아보세요.
> [!div class="nextstepaction"]
> [보호된 웹 API 시나리오](scenario-protected-web-api-overview.md)
