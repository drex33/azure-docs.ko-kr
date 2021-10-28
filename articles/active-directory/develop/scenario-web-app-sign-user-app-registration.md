---
title: 사용자가 로그인하는 웹앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 사용자가 로그인하는 웹앱을 등록하는 방법 알아보기
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0a32bef26db276b8858614ae3c2b86586f040aad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630360"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>사용자가 로그인하는 웹앱: 앱 등록

이 문서에서는 사용자가 로그인하는 웹앱에 대한 앱 등록 단계를 설명합니다.

애플리케이션을 등록하려면 다음을 사용할 수 있습니다.

- [웹앱 빠른 시작](#register-an-app-by-using-the-quickstarts). 애플리케이션을 만들 때 뛰어난 첫 경험이 될 뿐만 아니라, Azure Portal의 빠른 시작에는 **변경 적용** 이라는 단추가 포함되어 있습니다. 이 단추를 사용하여 기존 앱에도 필요한 속성을 설정할 수 있습니다. 이러한 속성의 값을 사례에 맞게 조정합니다. 특히 앱의 웹 API URL은 제안된 기본값과 다를 수 있으며, 이는 로그아웃 URI에도 영향을 줍니다.
- [애플리케이션을 수동으로 등록](#register-an-app-by-using-the-azure-portal)하는 Azure Portal입니다.
- PowerShell 및 명령줄 도구.

## <a name="register-an-app-by-using-the-quickstarts"></a>빠른 시작을 사용하여 앱 등록

다음 링크를 사용해서 웹 애플리케이션을 부트스트랩할 수 있습니다.

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Azure Portal을 사용하여 앱 등록

> [!NOTE]
> 사용할 포털은 애플리케이션이 Microsoft Azure 퍼블릭 클라우드나 국가 또는 소버린 클라우드에서 실행되는지 여부에 따라 달라집니다. 자세한 내용은 [국가별 클라우드](./authentication-national-cloud.md#app-registration-endpoints)를 참조하세요.


1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다. 
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `AspNetCore-WebApp`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
   1. 애플리케이션에 지원되는 계정 유형을 선택합니다. ([지원되는 계정 유형](./v2-supported-account-types.md)을 참조하세요.)
   1. **리디렉션 URI** 의 경우 인증에 성공한 후 반환된 토큰 응답을 받을 애플리케이션의 형식 및 URI 대상을 추가합니다. 예를 들어 다음과 같이 입력합니다. `https://localhost:44321`
   1. **등록** 을 선택합니다.
1. **관리** 에서 **인증** 을 선택하고 다음 정보를 추가합니다.
   1. **웹** 섹션에서 `https://localhost:44321/signin-oidc`를 **리디렉션 URI** 로 추가합니다.
   1. **전면 채널 로그아웃 URL** 에 `https://localhost:44321/signout-oidc`을 입력합니다.
   1. **암시적 권한 부여 및 하이브리드 흐름** 에서 **ID 토큰** 을 선택합니다.
   1. **저장** 을 선택합니다.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `MailApp-openidconnect-v2`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
   1. 애플리케이션에 지원되는 계정 유형을 선택합니다. ([지원되는 계정 유형](./v2-supported-account-types.md)을 참조하세요.)
   1. **리디렉션 URI(선택 사항)** 섹션에서 콤보 상자의 **웹** 을 선택하고 `https://localhost:44326/`의 리디렉션 UR **를** 입력합니다.
   1. **등록** 을 선택하여 애플리케이션을 만듭니다.
1. **관리** 에서 **인증** 을 선택합니다.
1. **암시적 권한 부여 및 하이브리드 흐름** 섹션에서 **ID 토큰** 을 선택합니다. 이 샘플에서는 사용자를 로그인하기 위해 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다.
1. **저장** 을 선택합니다.

# <a name="java"></a>[Java](#tab/java)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다. 
    1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `java-webapp`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다. 
    1. **모든 조직 디렉터리와 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)에 속하는 계정** 을 선택합니다.
    1. **등록** 을 선택하여 애플리케이션을 등록합니다.
1. **관리** 에서 **인증** > **플랫폼 추가** 를 선택합니다.
1. **웹** 을 선택합니다.
1. **리디렉션 URI** 에 대해 동일한 호스트 및 포트 번호를 입력한 뒤 로그인 페이지용 `/msal4jsample/secure/aad`을 입력합니다. 
1. **구성** 을 선택합니다.
1. **웹** 섹션에서 호스트 및 포트 번호를 사용하고, 사용자 정보 페이지의 **리디렉션 URI** 에 `/msal4jsample/graph/me`을 사용합니다.
기본적으로 이 샘플은 다음을 사용합니다.
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. **저장** 을 선택합니다.
1. **관리** 에서 **인증서 및 암호** 를 선택합니다.
1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호** 를 선택합니다.

   1. 키 설명을 입력합니다.
   1. 키 유효 기간을 **1년** 으로 설정합니다.
   1. **추가** 를 선택합니다.
   1. 키 값이 표시되면 나중에 해당 키 값을 복사합니다. 이 값은 다시 표시되지 않으며 다른 방법으로 검색할 수도 없습니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `node-webapp`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
   1. **지원되는 계정 유형** 을 **모든 조직 디렉터리와 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)에 속하는 계정** 으로 변경합니다.
   1. **리디렉션 URI(선택 사항)** 섹션의 콤보 상자에서 **웹** 을 선택하고, `http://localhost:3000/redirect`의 리디렉션 URI를 입력합니다.
   1. **등록** 을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 구성 파일을 구성하는 데 필요합니다.
1. **관리** 에서 **인증서 및 암호** 를 선택합니다.
1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호** 를 선택합니다.
   1. 키 설명을 입력합니다.
   1. **1년 후** 키 기간을 선택합니다.
   1. **추가** 를 선택합니다.
   1. 키 값이 표시되면 값을 복사합니다. 나중에 필요합니다.

# <a name="python"></a>[Python](#tab/python)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `python-webapp`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
   1. **지원되는 계정 유형** 을 **모든 조직 디렉터리와 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)에 속하는 계정** 으로 변경합니다.
   1. **리디렉션 URI(선택 사항)** 섹션의 콤보 상자에서 **웹** 을 선택하고, `http://localhost:5000/getAToken`의 리디렉션 URI를 입력합니다.
   1. **등록** 을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 Visual Studio 구성 파일을 구성하는 데 필요합니다.
1. **관리** 에서 **인증서 및 암호** 를 선택합니다.
1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호** 를 선택합니다.
   1. 키 설명을 입력합니다.
   1. **1년 후** 키 기간을 선택합니다.
   1. **추가** 를 선택합니다.
   1. 키 값이 표시되면 값을 복사합니다. 나중에 필요합니다.
---

## <a name="register-an-app-by-using-powershell"></a>PowerShell을 사용한 앱 등록

> [!NOTE]
> 현재 Azure AD PowerShell은 다음과 같은 지원되는 계정 유형만 포함된 애플리케이션을 만듭니다.
>
> - MyOrg(이 조직 디렉터리의 계정에만 해당)
> - AnyOrg(모든 조직 디렉터리의 계정)
>
> 개인 Microsoft 계정(예: Skype, Xbox 또는 Outlook.com)을 사용하여 사용자가 로그인하는 애플리케이션을 만들 수 있습니다. 먼저 다중 테넌트 애플리케이션을 만듭니다. 지원되는 계정 유형은 모든 조직 디렉터리의 계정입니다. 그런 다음 Azure Portal의 [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) 속성을 **2** 로 바꾸고 [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) 속성을 [애플리케이션 매니페스트](./reference-app-manifest.md)의 `AzureADandPersonalMicrosoftAccount`로 변경합니다. 자세한 내용은 ASP.NET Core 자습서에서 [1.3 단계](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)를 참조하세요. 모든 언어로 된 웹앱에 단계를 일반적으로 적용 가능합니다.

## <a name="next-steps"></a>다음 단계

이 시나리오에서 다음 문서 [앱의 코드 구성](scenario-web-app-sign-user-app-configuration.md)으로 이동합니다.
