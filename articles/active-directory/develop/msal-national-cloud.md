---
title: 국가별 클라우드 앱에서 MSAL 사용 | Azure
titleSuffix: Microsoft identity platform
description: 애플리케이션 개발자는 MSAL(Microsoft 인증 라이브러리)을 통해 보안 웹 API를 호출하기 위한 토큰을 획득할 수 있습니다. 해당 웹 API에는 Microsoft Graph, 기타 Microsoft API, 파트너 웹 API 또는 사용자의 웹 API가 있습니다. MSAL은 다수의 애플리케이션 아키텍처와 플랫폼을 지원합니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8b8ae31aa2af84a6f8dfd4f93c90a09e58805f73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598803"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>국가별 클라우드 환경에서 MSAL 사용

소버린 클라우드라고도 하는 [국가별 클라우드](authentication-national-cloud.md)는 물리적으로 격리된 Azure 인스턴스입니다. 해당 Azure 지역은 지리적 경계 내에서 데이터 보존, 주권, 준수 요구 사항을 적용하는 데 도움이 됩니다.

Microsoft의 전 세계 클라우드 외에도 MSAL(Microsoft 인증 라이브러리)을 사용하면 국가별 클라우드의 애플리케이션 개발자가 보안 웹 API를 인증하고 호출하기 위해 토큰을 획득할 수 있습니다. 그러한 웹 API에는 Microsoft Graph 또는 기타 Microsoft API가 있습니다.

글로벌 Azure 클라우드를 포함하여 Azure AD(Azure Active Directory)는 다음 국가별 클라우드에 배포됩니다. 

- Azure Government
- Azure 중국 21Vianet
- Azure 독일([2021년 10월 29일에 종료](https://www.microsoft.com/cloud-platform/germany-cloud-regions))

이 가이드에서는 [Azure Government 클라우드](https://azure.microsoft.com/global-infrastructure/government/) 환경에서 회사 및 학교 계정에 로그인하고, 액세스 토큰을 가져오고, Microsoft Graph API를 호출하는 방법을 보여 줍니다.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure 독일(Microsoft Cloud Deutschland)

> [!WARNING]
> Azure 독일(Microsoft Cloud Deutschland)은 [2021년 10월 29일에 종료](https://www.microsoft.com/cloud-platform/germany-cloud-regions)됩니다. 해당 날짜 이전에 글로벌 Azure의 지역으로 마이그레이션하지 _않도록_ 선택한 서비스 및 애플리케이션에 액세스할 수 없습니다.

Azure 독일에서 애플리케이션을 마이그레이션하지 않은 경우 [Azure 독일에서 마이그레이션에 대한 Azure Active Directory 정보](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad)에 따라 시작하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 해당 필수 구성 요소를 충족하는지 확인합니다.

### <a name="choose-the-appropriate-identities"></a>적절한 ID 선택

[Azure Government](../../azure-government/index.yml) 애플리케이션은 Azure AD Government ID 및 Azure AD 퍼블릭 ID를 사용하여 사용자를 인증할 수 있습니다. 해당 ID 중 하나를 사용할 수 있으므로 사용자의 시나리오를 위해 선택해야 하는 인증 기관 엔드포인트를 결정합니다.

- Azure AD 퍼블릭: 조직에 Microsoft 365(퍼블릭 또는 GCC) 또는 다른 애플리케이션을 지원하기 위한 Azure AD 퍼블릭 테넌트가 이미 있는 경우 일반적으로 사용됩니다.
- Azure AD Government: 조직에 이미 Office 365(GCC High 또는 DoD)를 지원하기 위한 Azure AD Government 테넌트가 있는 경우 또는 Azure AD Government에서 새 테넌트를 만드는 경우 일반적으로 사용됩니다.

결정한 후에는 앱 등록을 어디서 수행할지 특별히 고려해야 합니다. Azure Government 애플리케이션에 대해 Azure AD 퍼블릭 ID를 선택하는 경우 Azure AD 퍼블릭 테넌트에서 애플리케이션을 등록해야 합니다.

### <a name="get-an-azure-government-subscription"></a>Azure Government에서 구독 가져오기

Azure Government 구독을 가져오려면 [Azure Government에서 구독 관리 및 연결](../../azure-government/compare-azure-government-global-azure.md)을 참조하세요.

Azure Government 구독이 없는 경우, 시작하기 전에 [체험 계정](https://azure.microsoft.com/global-infrastructure/government/request/)을 만듭니다.

특정 프로그래밍 언어를 통해 국가별 클라우드를 사용하는 방법에 대한 자세한 내용을 보려면 다음에서 해당 언어와 일치하는 탭을 선택합니다.

## <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET를 사용하여 로그인하고, 토큰을 획득하고, 국가별 클라우드에서 Microsoft Graph API를 호출할 수 있습니다.

다음 자습서는 .NET Core 2.2 MVC 웹앱을 빌드하는 방법을 보여 줍니다. 앱은 OpenID Connect를 사용하여 국가별 클라우드에 속한 조직의 회사 및 학교 계정으로 로그인합니다.

- 사용자를 로그인하고 토큰을 획득하려면 [Microsoft ID 플랫폼을 사용하여 소버린 클라우드에서 ASP.NET Core 웹앱 로그인 사용자 빌드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform) 자습서를 따르세요.
- Microsoft Graph API를 호출하려면 다음 자습서를 수행합니다. [Microsoft 국가별 클라우드에서 회사 및 학교 계정을 사용해서 로그인하는 사용자를 대신하여 ASP.NET Core 2.x 웹앱에서 Microsoft Graph API를 호출하는 Microsoft ID 플랫폼 사용](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

소버린 클라우드에 대해 MSAL.js 애플리케이션을 사용하도록 설정하려면 다음을 수행합니다.

- 클라우드에 따라 특정 포털에 애플리케이션을 등록합니다. 포털을 선택하는 방법에 대한 자세한 내용은 [앱 등록 엔드포인트](authentication-national-cloud.md#app-registration-endpoints)를 참조하세요.
- 다음에 설명된 클라우드에 따라 구성에 대한 몇 가지 변경 내용이 포함된 리포지토리의 [샘플](https://github.com/Azure-Samples/ms-identity-javascript-tutorial)을 사용합니다.
- 애플리케이션을 등록한 클라우드에 따라 특정 권한을 사용합니다. 다른 클라우드의 인증 기관에 대한 자세한 내용은 [Azure AD 인증 엔드포인트](authentication-national-cloud.md#azure-ad-authentication-endpoints)를 참조하세요.
- Microsoft Graph API를 호출하려면 사용 중인 클라우드와 관련된 엔드포인트 URL이 필요합니다. 모든 국가별 클라우드의 Microsoft Graph 엔드포인트를 찾으려면 [Microsoft Graph 및 Graph 탐색기 서비스 루트 엔드포인트](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)를 참조하세요.

다음은 인증 기관의 예입니다.

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

다음은 범위가 있는 Microsoft Graph 엔드포인트의 예입니다.

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

소버린 클라우드로 사용자를 인증하고 Microsoft Graph를 호출하기 위한 최소 코드는 다음과 같습니다.

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        redirectUri: "/",
    }
};

// Initialize MSAL
const msalObj = new PublicClientApplication(msalConfig);

// Get token using popup experience
try {
    const graphToken = await msalObj.acquireTokenPopup({
        scopes: ["User.Read"]
    });
} catch(error) {
    console.log(error)
}

// Call the Graph API
const headers = new Headers();
const bearer = `Bearer ${graphToken}`;

headers.append("Authorization", bearer);

fetch("https://graph.microsoft.us/v1.0/me", {
    method: "GET",
    headers: headers
})
```


## <a name="python"></a>[Python](#tab/python)

소버린 클라우드용 MSAL Python 애플리케이션을 사용하도록 설정하려면 다음을 수행하세요.

- 클라우드에 따라 특정 포털에 애플리케이션을 등록합니다. 포털을 선택하는 방법에 대한 자세한 내용은 [앱 등록 엔드포인트](authentication-national-cloud.md#app-registration-endpoints)를 참조하세요.
- 다음에 설명된 클라우드에 따라 구성에 대한 몇 가지 변경 내용이 포함된 리포지토리의 [샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample)을 사용합니다.
- 애플리케이션을 등록한 클라우드에 따라 특정 권한을 사용합니다. 다른 클라우드의 인증 기관에 대한 자세한 내용은 [Azure AD 인증 엔드포인트](authentication-national-cloud.md#azure-ad-authentication-endpoints)를 참조하세요.

    다음은 인증 기관의 예입니다.

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Microsoft Graph API를 호출하려면 사용 중인 클라우드와 관련된 엔드포인트 URL이 필요합니다. 모든 국가별 클라우드의 Microsoft Graph 엔드포인트를 찾으려면 [Microsoft Graph 및 Graph 탐색기 서비스 루트 엔드포인트](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)를 참조하세요.

    다음은 범위가 있는 Microsoft Graph 엔드포인트의 예입니다.

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

소버린 클라우드용 Java 애플리케이션에 MSAL을 사용하도록 설정하려면 다음을 수행합니다.

- 클라우드에 따라 특정 포털에 애플리케이션을 등록합니다. 포털을 선택하는 방법에 대한 자세한 내용은 [앱 등록 엔드포인트](authentication-national-cloud.md#app-registration-endpoints)를 참조하세요.
- 다음에 언급된 클라우드에 따라 구성에 대한 몇 가지 변경 내용이 포함된 리포지토리의 [샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)을 사용합니다.
- 애플리케이션을 등록한 클라우드에 따라 특정 권한을 사용합니다. 다른 클라우드의 인증 기관에 대한 자세한 내용은 [Azure AD 인증 엔드포인트](authentication-national-cloud.md#azure-ad-authentication-endpoints)를 참조하세요.

다음은 인증 기관의 예입니다.

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Microsoft Graph API를 호출하려면 사용 중인 클라우드와 관련된 엔드포인트 URL이 필요합니다. 모든 국가별 클라우드의 Microsoft Graph 엔드포인트를 찾으려면 [Microsoft Graph 및 Graph 탐색기 서비스 루트 엔드포인트](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)를 참조하세요.

다음은 범위를 포함하는 그래프 엔드포인트의 예입니다.

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

iOS 및 macOS용 MSAL을 사용하여 국가별 클라우드에서 토큰을 획득할 수 있지만 만들 때 추가 구성이 필요합니다`MSALPublicClientApplication`.

예를 들어 애플리케이션을 국가별 클라우드의 다중 테넌트 애플리케이션(여기서는 미국 정부)으로 만들려면 다음을 작성할 수 있습니다.

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

iOS 및 macOS용 MSAL을 사용하여 국가별 클라우드에서 토큰을 획득할 수 있지만 만들 때 추가 구성이 필요합니다`MSALPublicClientApplication`.

예를 들어 애플리케이션을 국가별 클라우드의 다중 테넌트 애플리케이션(여기서는 미국 정부)으로 만들려면 다음을 작성할 수 있습니다.

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>다음 단계

각 클라우드의 Azure Portal URL 및 토큰 엔드포인트 목록은 [국가별 클라우드 인증 엔드포인트](authentication-national-cloud.md)를 참조하세요.

국가별 클라우드 설명서:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure 독일(2021년 10월 29일에 종료)](../../germany/index.yml)