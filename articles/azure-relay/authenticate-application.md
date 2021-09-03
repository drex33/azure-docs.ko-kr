---
title: 애플리케이션에서 인증 - Azure Relay(미리 보기)
description: 이 문서에서는 Azure Relay 리소스에 액세스하는 Azure Active Directory를 사용하는 애플리케이션 인증에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654207"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>Azure Relay 엔터티(미리 보기)에 액세스하기 위해 Azure Active Directory를 사용하여 애플리케이션 인증 및 권한 부여
Azure Relay는 Azure AD(Azure Active Directory)를 사용하여 Azure Relay 엔터티(하이브리드 연결, WCF Relay)에 대한 요청에 권한을 부여할 수 있습니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대한 자세한 내용은 [다양한 역할 이해](../role-based-access-control/overview.md)를 참조하세요.   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>앱에서 인증
Azure Relay에서 Azure AD를 사용할 경우의 주요 이점은 사용자의 자격 증명을 코드에 저장할 필요가 없다는 것입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹, 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure Relay에 대한 요청에 권한을 부여할 수 있습니다.

다음 섹션에서는 Microsoft ID 플랫폼 2.0에서 인증하도록 콘솔 애플리케이션을 구성하는 방법을 보여 줍니다. 자세한 내용은 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 Azure Relay 엔터티에 권한을 부여하는 첫 번째 단계는 Azure Portal에서 Azure AD 테넌트에 클라이언트 애플리케이션을 등록하는 것입니다. 클라이언트 애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(애플리케이션 ID라고도 함)를 제공합니다. 

Azure AD에 애플리케이션을 등록하는 단계별 지침은 [빠른 시작: Azure AD에 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md#register-an-application)을 참조하세요.

> [!IMPORTANT]
> **디렉터리(테넌트) ID** 와 **애플리케이션(클라이언트) ID** 를 적어 둡니다. 샘플 애플리케이션을 실행하려면 이러한 값이 필요합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 위에 링크된 동일한 문서에서 [클라이언트 비밀 추가](../active-directory/develop/quickstart-register-app.md#add-a-client-secret) 섹션을 참조하여 클라이언트 비밀을 만듭니다. 

> [!IMPORTANT]
> **클라이언트 비밀** 을 적어 둡니다. 샘플 애플리케이션을 실행하려면 클라이언트 비밀이 필요합니다.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당
원하는 범위(Relay 엔터티, 네임스페이스, 리소스 그룹, 구독)에서 애플리케이션 서비스 주체에 Azure Relay 역할 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="run-the-sample"></a>샘플 실행

1. [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol)에서 콘솔 애플리케이션 샘플을 다운로드합니다.
1. [추가 정보 문서](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)의 지침에 따라 컴퓨터에서 로컬로 애플리케이션을 실행합니다.

    > [!NOTE]
    > 위와 동일한 단계를 수행하여 [WCF Relay용 샘플 콘솔 애플리케이션](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)을 실행합니다. 

#### <a name="highlighted-code-from-the-sample"></a>샘플에서 강조 표시된 코드
Azure AD 인증을 사용하여 Azure Relay 서비스에 연결하는 방법을 보여 주는 샘플 코드는 다음과 같습니다.  

1. `TokenProvider.CreateAzureActiveDirectoryTokenProvider` 메서드를 사용하여 [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) 개체를 만듭니다. 

    앱 등록을 아직 만들지 않은 경우 [Azure AD에 애플리케이션 등록](#register-your-application-with-an-azure-ad-tenant) 섹션을 참조하여 등록을 만든 후, [클라이언트 비밀 만들기](#create-a-client-secret) 섹션에 설명된 대로 클라이언트 비밀을 만듭니다.

    기존 앱 등록을 사용하려는 경우 다음 지침에 따라 **애플리케이션(클라이언트) ID** 와 **디렉터리(테넌트) ID** 를 가져옵니다. 

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    1. 맨 위에 있는 검색 창을 사용하여 **Azure Active Directory** 를 검색하고 선택합니다.
    1. **Azure Active Directory** 페이지 왼쪽 메뉴의 **관리** 섹션에서 **앱 등록** 을 선택합니다. 
    1. 앱 등록 선택 
    1. 앱 등록 페이지에 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 값이 표시됩니다. 
    
    **클라이언트 비밀** 을 가져오려면 다음 단계를 수행합니다.
    1. 앱 등록 페이지의 왼쪽 메뉴에서 **인증서 및 비밀** 을 선택합니다. 
    1. **클라이언트 비밀** 섹션의 비밀 **값** 열에 있는 복사 단추를 사용합니다. 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. 이전 단계에서 만든 하이브리드 연결 URI와 토큰 공급자를 전달하여 [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) 또는 [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) 개체를 만듭니다.

    **수신기:**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **송신기:**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>다음 단계
- Azure RBAC에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음 문서를 참조하세요.
    - [Azure PowerShell을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-template.md)

Azure 릴레이에 대한 자세한 내용은 다음 항목을 참조하세요.
- [릴레이란?](relay-what-is-it.md)
- [Azure Relay 하이브리드 연결 WebSockets 시작](relay-hybrid-connections-dotnet-get-started.md)
- [Azure Relay 하이브리드 연결 HTTP 요청 시작](relay-hybrid-connections-http-requests-dotnet-get-started.md)








