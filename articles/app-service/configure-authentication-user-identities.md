---
title: AuthN/AuthZ의 사용자 ID
description: App Service에서 기본 제공 인증 및 권한 부여를 사용할 때 사용자 ID에 액세스하는 방법을 알아봅니다.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230435"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>Azure App Service 인증에서 사용자 ID 사용

이 문서에서는 기본 제공되는 [App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 사용할 때 사용자 ID로 작업하는 방법을 보여줍니다. 

## <a name="access-user-claims-in-app-code"></a>앱 코드에서 사용자 클레임에 액세스

모든 언어 프레임워크를 위해 App Service는 들어오는 토큰의 클레임(인증된 최종 사용자 또는 클라이언트 애플리케이션이든 관계없이)을 요청 헤더에 삽입하여 코드에서 사용할 수 있도록 합니다. 외부 요청은 이러한 헤더를 설정하도록 허용되지 않았으므로 App Service에서 설정한 경우에만 표시됩니다. 다음은 이러한 헤더의 예입니다.

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

모든 언어로 작성된 코드 또는 프레임워크는 이러한 헤더에서 필요한 정보를 가져올 수 있습니다. 

> [!NOTE]
> 다른 언어 프레임워크는 이러한 헤더를 소문자 또는 제목 대/소문자와 같은 다양한 형식으로 앱 코드에 표시할 수 있습니다.

ASP.NET 4.6 응용 프로그램의 경우 App Service는 인증된 사용자의 클레임을 사용하여 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)를 채우기 때문에 `[Authorize]` 특성을 비롯한 표준 .NET 코드 패턴을 따를 수 있습니다. 마찬가지로 PHP 앱의 경우, App Service는 `_SERVER['REMOTE_USER']` 변수를 채웁니다. Java 앱의 경우 [Tomcat 서블릿에서 클레임에 액세스할 수 ](configure-language-java.md#authenticate-users-easy-auth)있습니다.

[Azure Functions](../azure-functions/functions-overview.md)의 경우, .NET 코드를 위해 `ClaimsPrincipal.Current`가 채워지지 않지만, 요청 헤더에서 사용자 클레임을 찾거나 요청 컨텍스트에서 또는 바인딩 매개 변수를 통해 `ClaimsPrincipal` 개체를 가져올 수 있습니다. 자세한 내용은 [Azure Functions에서 클라이언트 ID 사용](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)을 참조하세요.

.NET Core의 경우 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/)은 App Service 인증으로 현재 사용자 채우기를 지원합니다. 자세한 내용은 [Microsoft.Identity.Web wiki](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb)를 참조하거나 [Microsoft Graph에 액세스하는 웹앱에 대해 이 자습서](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages)에서 시연된 내용을 참조하세요.

## <a name="access-user-claims-using-the-api"></a>API를 사용하여 사용자 클레임에 액세스

앱에서 [토큰 저장소](overview-authentication-authorization.md#token-store)를 사용하도록 설정한 경우 `/.auth/me`를 호출하여 인증된 사용자에 관한 추가 세부 정보를 가져올 수도 있습니다. Mobile Apps 서버 SDK는 이 데이터를 사용하기 위한 도우미 메서드를 제공합니다. 자세한 내용은 [Azure Mobile Apps Node.js SDK를 사용하는 방법](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) 및 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)