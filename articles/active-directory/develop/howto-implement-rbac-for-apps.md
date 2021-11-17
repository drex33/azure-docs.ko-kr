---
title: 앱에서 역할 기반 액세스 제어 구현
titleSuffix: Microsoft identity platform
description: 애플리케이션에서 역할 기반 액세스 제어를 구현하는 방법을 알아봅니다.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/17/2021
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: ccbdbde261914e95470e37b0e2046a95e1fe5898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629487"
---
# <a name="implement-role-based-access-control-in-apps"></a>앱에서 역할 기반 액세스 제어 구현

RBAC(역할 기반 액세스 제어)를 사용하면 사용자 또는 그룹이 어떤 리소스에 액세스할 수 있는지, 해당 리소스로 무엇을 할 수 있는지, 누가 어떤 리소스를 관리하는지에 대한 권한을 구체적으로 지정할 수 있습니다.

일반적으로 리소스를 보호하기 위해 RBAC를 구현하는 방법을 설명하는 경우 웹 애플리케이션, SPA(단일 페이지 애플리케이션) 또는 API를 보호하려고 합니다.  이는 전체 애플리케이션이나 API, 특정 영역, 기능 또는 API 메서드에 대한 것일 수 있습니다.

이 문서에서는 애플리케이션별 역할 기반 액세스 제어를 구현하는 방법에 대해 설명합니다.  권한 부여 기본 사항에 대한 자세한 내용은 [권한 부여 기본 사항](./authorization-basics.md)을 참조하세요.

## <a name="implementing-rbac-using-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼을 사용하여 RBAC 구현

[애플리케이션 개발자를 위한 역할 기반 액세스 제어](./custom-rbac-for-developers.md)에서 설명한 대로 Microsoft ID 플랫폼을 사용하여 RBAC를 구현하는 세 가지 방법이 있습니다.

- **앱 역할** – 애플리케이션 내에서 논리와 함께 사용하여 [애플리케이션 등록의 앱 역할 기능](./howto-add-app-roles-in-azure-ad-apps.md#declare-roles-for-an-application)을 들어오는 앱 역할 할당을 해석합니다.
- **그룹** – 애플리케이션 내에서 논리와 함께 들어오는 ID의 그룹 할당을 사용하여 그룹 할당을 해석합니다. 
- **사용자 지정 데이터 저장소** – 애플리케이션 내에서 논리를 사용하여 역할 할당을 검색하고 해석합니다.

가장 쉽게 구현할 수 있는 *앱 역할* 을 사용하는 것이 좋습니다. 이 방법은 Microsoft ID 플랫폼을 활용하여 앱을 빌드하는 데 사용되는 SDK에서 직접 지원합니다. 방법을 선택하는 방법에 대한 자세한 내용은 [방법 선택](./custom-rbac-for-developers.md#choosing-an-approach)을 참조하세요.

이 문서의 나머지 부분에서는 앱 역할을 정의하고, 앱 역할을 사용하여 애플리케이션 내에서 RBAC를 구현하는 방법을 보여 줍니다.

## <a name="defining-roles-for-your-application"></a>애플리케이션에 대한 역할 정의

애플리케이션에 대한 RBAC를 구현하는 첫 번째 단계는 애플리케이션에 필요한 역할을 정의하고 사용자 또는 그룹을 해당 역할에 할당하는 것입니다.  이 프로세스는 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신](./howto-add-app-roles-in-azure-ad-apps.md)에서 설명하고 있습니다. 역할을 정의하고 사용자 또는 그룹을 할당한 후에는 애플리케이션에 들어오는 토큰의 역할 할당에 액세스하고 이에 따라 해당 작업을 수행할 수 있습니다.

## <a name="implementing-rbac-in-aspnet-core"></a>ASP.NET Core에서 RBAC 구현 

ASP.NET Core는 RBAC를 ASP.NET Core 웹 애플리케이션 또는 웹 API에 추가하도록 지원합니다.  이렇게 하면 ASP.NET Core *Authorize* 특성과 함께 [역할 검사](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)를 사용하여 RBAC를 쉽게 구현할 수 있습니다. 또한 ASP.NET Core에서 지원하는 [정책 기반 역할 검사](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#policy-based-role-checks)를 사용할 수 있습니다.

### <a name="aspnet-core-mvc-web-application"></a>ASP.NET Core MVC 웹 애플리케이션 

ASP.NET Core MVC 웹 애플리케이션에서 RBAC를 구현하는 것은 간단합니다.  이렇게 하려면 주로 *Authorize* 특성을 사용하여 특정 컨트롤러 또는 컨트롤러의 작업에 액세스할 수 있는 역할을 지정해야 합니다. 다음 단계에 따라 ASP.NET Core MVC 애플리케이션에서 RBAC를 구현합니다.
1. 위의 *애플리케이션에 대한 역할 정의* 에서 설명한 대로 앱 역할 및 할당을 사용하여 앱 등록을 만듭니다.
1. 다음 단계 중 하나를 수행합니다.
    - **dotnet cli** 를 사용하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다.  *--auth* 플래그는 단일 테넌트 인증의 경우 *SingleOrg* 를 사용하고 다중 테넌트 인증의 경우 *MultiOrg* 를 사용하여 지정합니다. *--client-id* 플래그는 앱 등록의 경우 클라이언트를 사용하여 지정하고, *--tenant-id* 플래그는 Azure AD 테넌트의 경우 테넌트를 사용하여 지정합니다.
 
      ```bash 
    
      dotnet new mvc --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID>  
    
      ```
      
    - Microsoft.Identity.Web 및 Microsoft.Identity.Web.UI 라이브러리를 기존 ASP.NET Core MVC 프로젝트에 추가합니다.
 
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Microsoft.Identity.Web.UI 

      ```

   그런 다음, [빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)에서 지정한 지침에 따라 인증을 애플리케이션에 추가합니다.
1. [역할 검사 추가](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)에서 설명한 대로 컨트롤러 작업에 대한 역할 검사를 추가합니다.
1. 보호된 MVC 경로 중 하나에 액세스하여 애플리케이션을 테스트합니다.

### <a name="aspnet-core-web-api"></a>ASP.NET Core 웹 API

ASP.NET Core 웹 API에서 RBAC를 구현하려면 주로 *Authorize* 특성을 활용하여 특정 컨트롤러 또는 컨트롤러의 작업에 액세스할 수 있는 역할을 지정해야 합니다. 다음 단계에 따라 ASP.NET Core 웹 API에서 RBAC를 구현합니다.
1. 위의 *애플리케이션에 대한 역할 정의* 에서 설명한 대로 앱 역할 및 할당을 사용하여 앱 등록을 만듭니다.
1. 다음 단계 중 하나를 수행합니다.
    - **dotnet cli** 를 사용하여 새 ASP.NET Core MVC 웹 API 프로젝트를 만듭니다.  *--auth* 플래그는 단일 테넌트 인증의 경우 *SingleOrg* 를 사용하고 다중 테넌트 인증의 경우 *MultiOrg* 를 사용하여 지정합니다. *--client-id* 플래그는 앱 등록의 경우 클라이언트를 사용하여 지정하고, *--tenant-id* 플래그는 Azure AD 테넌트의 경우 테넌트를 사용하여 지정합니다.

      ```bash 
    
      dotnet new webapi --auth SingleOrg --client-id <YOUR-APPLICATION-CLIENT-ID> --tenant-id <YOUR-TENANT-ID> 
    
      ```

    - Microsoft.Identity.Web 및 Swashbuckle.AspNetCore 라이브러리를 기존 ASP.NET Core 웹 API 프로젝트에 추가합니다.
      
      ```bash 

      dotnet add package Microsoft.Identity.Web 

      dotnet add package Swashbuckle.AspNetCore 

      ```
    
   그런 다음, [빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가](./quickstart-v2-aspnet-core-webapp.md?view=aspnetcore-5.0&preserve-view=true)에서 지정한 지침에 따라 인증을 애플리케이션에 추가합니다.
1. [역할 검사 추가](/aspnet/core/security/authorization/roles?view=aspnetcore-5.0&preserve-view=true#adding-role-checks)에서 설명한 대로 컨트롤러 작업에 대한 역할 검사를 추가합니다.
1. 클라이언트 앱에서 API를 호출합니다.  엔드투엔드 샘플은 [.NET Core 웹 API를 호출하고 앱 역할을 사용하여 역할 기반 액세스 제어를 구현하는 Angular 단일 페이지 애플리케이션](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles)을 참조하세요.


## <a name="implementing-rbac-in-other-platforms"></a>다른 플랫폼에서 RBAC 구현

### <a name="angular-spa-using-msalguard"></a>MsalGuard를 사용하는 Angular SPA
Angular SPA에서 RBAC를 구현하려면 [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)를 사용하여 애플리케이션 내에 포함된 Angular 경로에 대한 액세스 권한을 부여해야 합니다.  이는 [Angular 단일 페이지 애플리케이션에서 Microsoft ID 플랫폼을 통해 사용자를 로그인하고 API를 호출하도록 설정](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial#chapter-5-control-access-to-your-protected-api-using-app-roles-and-security-groups) 샘플에 나와 있습니다.

> [!NOTE]
> 권한 없는 애플리케이션에서 중요한 리소스에 액세스하지 못하도록 방지하기 위해 클라이언트 쪽 RBAC 구현을 서버 쪽 RBAC와 쌍으로 연결해야 합니다.

### <a name="nodejs-with-express-application"></a>Express 애플리케이션을 사용하는 Node.js
Express 애플리케이션을 사용하여 Node.js에서 RBAC를 구현하려면 MSAL을 사용하여 애플리케이션 내에 포함된 Express 경로에 대한 액세스 권한을 부여해야 합니다.  이는 [Node.js 웹앱에서 Microsoft ID 플랫폼을 통해 사용자를 로그인하고 API를 호출하도록 설정](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial#chapter-4-control-access-to-your-app-using-app-roles-and-security-groups) 샘플에 나와 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft ID 플랫폼의 권한 및 동의](./v2-permissions-and-consent.md)에 대해 자세히 알아봅니다.
- [애플리케이션 개발자를 위한 역할 기반 액세스 제어](./custom-rbac-for-developers.md)에 대해 자세히 알아봅니다.
