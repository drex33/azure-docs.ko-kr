---
title: Microsoft ID 플랫폼 인증 및 권한 부여 코드 샘플
description: 여러 애플리케이션 유형, 개발 언어, 프레임워크에서의 인증 및 권한 부여를 보여 주고 Microsoft에서 유지 관리하는 코드 샘플의 인덱스입니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 08/27/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: fb8cf19f3b266d2cc7c3a1569f964acdc934ba7b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786772"
---
# <a name="microsoft-identity-platform-code-samples"></a>Microsoft ID 플랫폼 코드 샘플

Microsoft에서 빌드하고 유지 관리하는 이 코드 샘플은 여러 [애플리케이션 유형](v2-app-types.md), 개발 언어, 프레임워크에서 Azure AD와 Microsoft ID 플랫폼을 사용하여 인증 및 권한 부여를 보여 줍니다.

- 사용자를 웹 애플리케이션에 로그인하고 보호된 웹 API에 대한 권한 있는 액세스를 제공합니다.
- API 작업 수행 시 액세스 토큰을 요구하여 웹 API를 보호합니다.

각 코드 샘플에는 프로젝트를 빌드하고(해당하는 경우) 샘플 애플리케이션을 실행하는 방법을 설명하는 _README.md_ 파일이 포함되어 있습니다. 코드의 주석은 인증 라이브러리와 ID 플랫폼을 사용하여 인증 및 권한 부여를 구현하는 중요한 섹션을 이해하는 데 도움이 됩니다.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이 샘플에서는 Microsoft ID 플랫폼으로 보안이 설정된 단일 페이지 애플리케이션을 작성하는 방법을 보여 줍니다. 이 샘플은 MSAL.js의 특징 중 하나를 사용합니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/>GitHub | 인증<br/> 라이브러리 | 인증 흐름 |  
> | ------- | -------- | ------------- | -------------- |  
> | Angular | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [.NET Core 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [.NET Core 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP를 사용하여 .NET Core 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; PKCE를 적용한 권한 부여 코드<br/>&#8226; OBO(On-behalf-of) <br/>&#8226; PoP(소유 증명)|
> | Blazor WebAssembly | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | PKCE가 있는 권한 부여 코드 |
> | JavaScript | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Node.js 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [OBO 및 CA를 통해 Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; PKCE를 적용한 권한 부여 코드<br/>&#8226; OBO(On-behalf-of) <br/>&#8226; CA(조건부 액세스.) |
> | React | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Node.js 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP를 사용하여 Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Azure Static Web Apps에 배포](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL 반응  | &#8226; PKCE를 적용한 권한 부여 코드<br/>&#8226; OBO(On-behalf-of) <br/>&#8226; CA(조건부 액세스.)<br/>&#8226; PoP(소유 증명) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플<br/> GitHub | 인증<br/> 라이브러리 | 인증 흐름 |
> | ------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core| ASP.NET Core 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [토큰 캐시 사용자 지정](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [Graph 호출(다중 테넌트)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [Azure REST API 호출](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [웹 API 보호](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [웹 API 보호(B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [다중 테넌트 웹 API 보호](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; MSAL.NET<br/> &#8226; Microsoft.Identity.Web | &#8226; OpenID Connect <br/> &#8226; 권한 부여 코드 <br/> &#8226; OBO(On-Behalf-Of)|
> | Blazor | Blazor Server 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [웹 API 호출](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | PKCE가 있는 권한 부여 코드|
> | ASP.NET Core|[고급 토큰 캐시 시나리오](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | OBO(On-Behalf-Of) |
> | ASP.NET Core|[조건부 액세스 인증 컨텍스트를 사용하여 버전\- 업그레이드 인증 수행](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | 인증 코드 |
> | ASP.NET Core|[Active Directory FS에서 Azure AD로 마이그레이션](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | MSAL.NET | &#8226; SAML <br/> &#8226; OpenID Connect |
> | ASP.NET | &#8226; [Microsoft Graph 학습 샘플](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) <br/> &#8226; [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) <br/> &#8226; [관리자 제한 범위에서 사용자 로그인 Microsoft Graph 호출](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <br/> &#8226; [빠른 시작: 사용자 로그인](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | MSAL.NET | &#8226; OpenID Connect <br/> &#8226; 권한 부여 코드 |
> | Java </p> Spring |Azure AD Spring Boot Starter 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [액세스 제어를 위해 그룹 사용](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | &#8226; MSAL Java <br/> &#8226; Azure AD Boot Starter | 인증 코드 |
> | Java </p> 서블릿 | Spring 제외 서블릿 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | 인증 코드 |
> | Java | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-webapp)| MSAL Java | 인증 코드 |
> | Java </p> Spring| OBO를 통한 사용자 로그인 및 Microsoft Graph 호출 </p> &#8226; [웹 API](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | &#8226; 권한 부여 코드 <br/> &#8226; OBO(On-Behalf-Of) |
> | Node.js </p> Express | Express 웹앱 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) <br/> &#8226; [사용자 로그인 웹앱](https://github.com/Azure-Samples/ms-identity-node) | MSAL 노드 | 인증 코드 |
> | Python </p> Flask | Flask 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/>&#8226; [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-webapp) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | MSAL Python | 인증 코드 |
> | Python </p> Django | Django 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | 인증 코드 |
> | Ruby | Graph 학습 <br/> &#8226; [사용자 로그인 및 Microsoft Graph 호출](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | OmniAuth OAuth2 | 인증 코드 |

## <a name="web-api"></a>Web API

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 보호하는 방법과 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET | [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) | MSAL.NET | OBO(On-Behalf-Of) |
> | ASP.NET Core | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | OBO(On-Behalf-Of) |
> | Java | [사용자 로그인](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | OBO(On-Behalf-Of) |
> | Node.js | &#8226; [Node.js 웹 API 보호](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) <br/> &#8226; [Azure AD B2C를 사용하여 Node.js 웹 API 보안 유지](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | MSAL 노드 | 권한 부여 전달자 |

## <a name="desktop"></a>데스크톱

다음 샘플에서는 Microsoft Graph API에 액세스하거나, 사용자 이름의 자체 웹 API에 액세스하는 퍼블릭 클라이언트 데스크톱 애플리케이션을 보여 줍니다. _WAM(Workspace Application Manager)을 사용하는 데스크톱(콘솔)_ 샘플 외에도 이러한 클라이언트 애플리케이션은 모두 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub | 인증<br/> 라이브러리 | 인증 흐름 |  
> | ------- | -------- | ------------- | -------------- |
> | .NET Core | &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/1-Calling-MSGraph/1-1-AzureAD) <br/> &#8226; [토큰 캐시를 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/2-TokenCache) <br/> &#8226; [웹 UI HTML을 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-1-CustomHTML) <br/> &#8226; [웹 브라우저를 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-2-CustomBrowser) <br/> &#8226; [디바이스 코드 흐름을 통해 사용자 로그인](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/4-DeviceCodeFlow) | MSAL.NET |&#8226; PKCE를 적용한 권한 부여 코드 <br/> &#8226; 디바이스 코드 |
> | .NET | &#8226; [디먼 콘솔을 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [디먼 콘솔을 사용하여 웹 API 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md) | MSAL.NET | PKCE가 있는 권한 부여 코드 |
> | .NET | [Windows 통합 인증을 통해 보호된 API 호출](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) | MSAL.NET | Windows 통합 인증 |
> | Java | [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-desktop/) | MSAL Java | Windows 통합 인증 |
> | Node.js | [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) | MSAL 노드 | PKCE가 있는 권한 부여 코드 |
> | PowerShell | [사용자 이름/암호를 통한 사용자 로그인으로 Microsoft Graph 호출](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) | MSAL.NET | 리소스 소유자 암호 자격 증명 |
> | Python | [사용자 로그인](https://github.com/Azure-Samples/ms-identity-python-desktop) | MSAL Python | PKCE가 있는 권한 부여 코드 |
> | UWP(유니버설 Windows 플랫폼) | [Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | 웹 계정 관리자 |
> | WPF(Windows Presentation Foundation) | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | PKCE가 있는 권한 부여 코드 |
> | XAML | &#8226; [사용자 로그인 및 ASP.NET 핵심 웹 API 호출](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/1.%20Desktop%20app%20calls%20Web%20API) <br/> &#8226; [사용자 로그인 및 Microsoft Graph 호출](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | MSAL.NET | PKCE가 있는 권한 부여 코드 |

## <a name="mobile"></a>모바일

다음 샘플에서는 Microsoft Graph API에 액세스하거나, 사용자 이름의 자체 웹 API에 액세스하는 퍼블릭 클라이언트 모바일 애플리케이션을 보여 줍니다. 이러한 클라이언트 애플리케이션은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | iOS | &#8226; [Microsoft Graph 원시 호출](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc) <br/> &#8226; [Azure AD nxoauth를 사용하여 Microsoft Graph 호출](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) | MSAL iOS | PKCE가 있는 권한 부여 코드 |
> | Java | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-android-java) | MSAL Android | PKCE가 있는 권한 부여 코드 |
> | Kotlin | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-android-kotlin) | MSAL Android | PKCE가 있는 권한 부여 코드 |
> | Xamarin | &#8226; [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/1-Basic) <br/>&#8226; [broker를 통한 사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | PKCE가 있는 권한 부여 코드 |

## <a name="service--daemon"></a>서비스/디먼

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 애플리케이션을 보여 줍니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET| &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [웹 API 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)<br/> &#8226; [자체 웹 API 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/4-Call-OwnApi-Pop) <br/> &#8226; [관리 ID 및 Azure Key Vault 사용](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/3-Using-KeyVault) <br/> &#8226; [Microsoft ID 플랫폼 엔드포인트를 포함한 다중 테넌트](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp) | MSAL.NET | 클라이언트 자격 증명 부여|
> | Java | [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-daemon)| MSAL Java| 클라이언트 자격 증명 부여|
> | Node.js | [사용자 로그인 및 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) | MSAL 노드 | 클라이언트 자격 증명 부여 |
> | Python | &#8226; [비밀을 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/1-Call-MsGraph-WithSecret) <br/> &#8226; [인증서를 사용하여 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/2-Call-MsGraph-WithCertificate) | MSAL Python| 클라이언트 자격 증명 부여|

## <a name="azure-functions-as-web-apis"></a>Web API로의 Azure Functions

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 노출하고 HttpTrigger를 사용하여 Azure 함수를 보호하는 방법 및 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | .NET | [Azure AD로 보호되는 .NET Azure 함수 웹 API](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) | MSAL.NET | 인증 코드 |
> | Node.js | [Azure AD로 보호되는 Node.js Azure 함수 웹 API](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) | MSAL 노드 | 권한 부여 전달자 |
> | Node.js | [사용자를 대신하여 Microsoft Graph API 호출](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) | MSAL 노드| OBO(On-Behalf-Of)|
> | Python | [Azure AD로 보호되는 Python Azure 함수 웹 API](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) | MSAL Python | 인증 코드 |

## <a name="headless"></a>헤드리스

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구이거나 Linux나 Mac에서 실행되는 앱 또는 IoT 애플리케이션일 수 있습니다. 샘플에는 다른 디바이스(예: 휴대폰)에서 대화형으로 로그인하는 사용자 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 애플리케이션은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | .NET core | [텍스트 전용 디바이스에서, 보호되는 API 호출](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) | MSAL.NET | 디바이스 코드|
> | Java | [사용자 로그인 및 보호되는 API 호출](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) | MSAL Java | 디바이스 코드 |
> | Python | [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) | MSAL Python | 디바이스 코드 |

## <a name="multi-tenant-saas"></a>다중 테넌트 SaaS

다음 샘플에서는 모든 Azure AD(Azure Active Directory) 테넌트에서 로그인을 허용하도록 애플리케이션을 구성하는 방법을 보여 줍니다. _다중 테넌트_ 가 되도록 애플리케이션을 구성하면 **SaaS(Software as a Service)** 애플리케이션을 많은 조직에 제공할 수 있으므로 해당 동의한 후에 애플리케이션에 로그인할 수 있습니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 <br/> GitHub |인증<br/> 라이브러리 |인증 흐름 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET Core | [ASP.NET Core MVC 웹 애플리케이션의 Microsoft Graph API 호출](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) | MSAL.NET | OpenID Connect |
> | ASP.NET Core | [ASP.NET Core MVC 웹 애플리케이션 호출 ASP.NET Core API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) | MSAL.NET | 인증 코드 |

## <a name="next-steps"></a>다음 단계

더 많은 샘플 코드를 자세히 알아보려면 다음을 참조하세요.

- [Angular에서 사용자 로그인 Microsoft Graph API 호출](tutorial-v2-angular-auth-code.md)
- [Node.js 및 Express 웹앱에서 사용자 로그인](tutorial-v2-nodejs-webapp-msal.md)
- [UWP(유니버설 Windows 플랫폼)에서 Microsoft Graph API 호출](tutorial-v2-windows-uwp.md)
