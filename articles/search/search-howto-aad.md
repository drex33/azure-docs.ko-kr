---
title: Azure AD를 사용하여 검색 요청 권한 부여
titleSuffix: Azure Cognitive Search
description: Azure AD에서 토큰을 획득하여 검색 요청 권한 부여
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: c948bb3cebfef6df79e4d2349aac31248650c0de
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075855"
---
# <a name="authorize-search-requests-using-azure-ad-preview"></a>Azure AD를 사용하여 검색 요청 권한 부여(미리 보기)

> [!IMPORTANT]
> 인덱스 만들기 또는 인덱스 쿼리와 같은 데이터 평면 작업에 대한 역할 기반 액세스 제어는 현재 공개 미리 보기로 제공되며 [추가 사용 약관에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)따라 사용할 수 있습니다. 이 기능은 퍼블릭 클라우드에서만 사용할 수 있으며 기능이 미리 보기 상태인 동안 작업 대기에 영향을 미칠 수 있습니다. 

azure AD(Azure Active Directory)를 사용하면 RBAC(역할 기반 액세스 제어)를 사용하여 Azure Cognitive Search 서비스에 대한 액세스 권한을 부여할 수 있습니다. Azure AD를 사용하는 주요 이점은 자격 증명을 더 이상 코드에 저장할 필요가 없다는 것입니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증에 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고 애플리케이션은 액세스 토큰을 사용하여 Azure Cognitive Search 요청에 권한을 부여할 수 있습니다. 애플리케이션에서 Azure AD를 사용하는 이점에 대한 자세한 내용은 [Azure Active Directory 통합을](/azure/active-directory/develop/active-directory-how-to-integrate#benefits-of-integration)참조하세요.

이 문서에서는 Microsoft ID 플랫폼 인증을 위해 애플리케이션을 구성하는 방법을 보여 줍니다. Microsoft 식별 플랫폼에 대한 자세한 내용은 [Microsoft ID 플랫폼 개요를 참조하세요.](/azure/active-directory/develop/v2-overview) Azure AD에서 사용하는 OAuth 2.0 코드 부여 흐름에 대한 자세한 내용은 [OAuth 2.0 코드 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여를](/azure/active-directory/develop/v2-oauth2-auth-code-flow)참조하세요.

## <a name="prepare-your-search-service"></a>검색 서비스 준비

첫 번째 단계로 [검색 서비스를 만들고](search-create-service-portal.md) RBAC(역할 기반 액세스 제어)를 사용하도록 구성합니다.

### <a name="sign-up-for-the-preview"></a>미리 보기 등록

검색 서비스를 쿼리하는 데 Azure AD를 사용하는 데 필요한 Azure Cognitive Search RBAC 기능 부분은 여전히 게이트된 미리 보기 상태입니다. 

미리 보기에 등록하는 경우 [이 양식을 작성합니다.](https://aka.ms/azure-cognitive-search/rbac-preview)

요청을 처리하는 데 영업일이 몇 일 정도 걸릴 수 있습니다. 

### <a name="enable-rbac-for-data-plane-operations"></a>데이터 평면 작업에 RBAC 사용

구독이 미리 보기에 온보딩된 후에도 Azure AD 인증을 사용할 수 있도록 데이터 평면 작업에 RBAC를 사용하도록 설정해야 합니다. 기본적으로 Azure Cognitive Search 데이터 평면 작업에 키 기반 인증을 사용하지만 역할 기반 액세스 제어를 허용하도록 설정을 변경할 수 있습니다. 

역할 기반 액세스 제어를 사용하도록 설정하려면 다음을 수행합니다.

1. 이 미리 보기 링크를 사용하여 Azure Portal [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 이동합니다. 
1. 왼쪽 탐색 창에서 **키를** 선택합니다.
1. 키 기반 액세스 제어와 역할 기반 액세스 제어를 모두 허용할지 또는 역할 기반 액세스 제어만 허용할지 결정합니다.

![포털에서 Azure Cognitive Search에 대한 인증 옵션](media/search-howto-Azure AD/portal-api-access-control.png)

[Azure Cognitive Search RBAC 설명서에](/azure/search/search-security-rbac?tabs=config-svc-rest%2Croles-powershell%2Ctest-rest#step-2-preview-configuration)설명된 대로 이러한 설정을 프로그래밍 방식으로 변경할 수도 있습니다.

## <a name="register-an-application-with-azure-ad"></a>Azure AD에 애플리케이션을 등록합니다.

인증에 Azure AD를 사용하는 다음 단계는 [Microsoft ID 플랫폼](/azure/active-directory/develop/quickstart-register-app)애플리케이션을 등록하는 것입니다. 애플리케이션을 만드는 데 문제가 있는 경우 애플리케이션을 [등록하는 데 필요한 권한이 있는지 확인합니다.](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app)

Azure AD에 애플리케이션을 등록하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)Azure 계정에 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **앱 등록** 을 선택합니다.
1. **새 등록** 을 선택합니다.
1. 애플리케이션에 이름을 지정하고 애플리케이션을 사용할 수 있는 사람을 결정하는 지원되는 계정 유형을 선택합니다. 그런 다음 **등록** 을 선택합니다.

![애플리케이션 등록 마법사](media/search-howto-aad/register-app.png)

이 시점에서 Azure AD 애플리케이션 및 서비스 주체를 만들었습니다. 앱 등록의 개요 페이지에서 테넌트(또는 디렉터리) ID 및 클라이언트(또는 애플리케이션) ID를 기록해 둡다. 이러한 값은 향후 단계에서 필요합니다.

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

또한 애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호 또는 인증서가 필요합니다. 이 문서에서는 클라이언트 비밀을 사용하는 방법을 보여 드리겠습니다.

1. 방금 만든 앱 등록으로 이동합니다.
1. **인증서 및 비밀을** 선택합니다.
1. **클라이언트 비밀** 아래에서 **새 클라이언트 비밀** 을 클릭합니다.
1. 비밀에 대한 설명을 제공하고 원하는 만료 간격을 선택합니다.

![클라이언트 암호 만들기 마법사](media/search-howto-aad/create-secret.png)

값에 다시 액세스할 수 없도록 비밀 값을 안전한 위치에 저장해야 합니다. 

## <a name="grant-your-application-permissions-to-azure-cognitive-search"></a>애플리케이션에 Azure Cognitive Search 권한 부여

다음으로, 검색 서비스에 대한 Azure AD 애플리케이션 액세스 권한을 부여해야 합니다. Azure Cognitive Search [애플리케이션에](/azure/search/search-security-rbac?tabs=config-svc-portal%2Croles-portal%2Ctest-portal#built-in-roles-used-in-search) 필요한 액세스에 따라 사용할 수 있는 다양한 기본 제공 역할이 있습니다.

일반적으로 애플리케이션에 필요한 액세스 권한만 제공하는 것이 가장 좋습니다. 예를 들어 애플리케이션에서 검색 인덱스만 쿼리할 수 있어야 하는 경우 [인덱스 데이터 판독기 검색(미리 보기)](/azure/role-based-access-control/built-in-roles#search-index-data-reader) 역할을 부여할 수 있습니다. 또는 검색 인덱스 읽기 및 쓰기가 필요한 경우 검색 [인덱스 데이터 기여자(미리 보기)](/azure/role-based-access-control/built-in-roles#search-index-data-contributor) 역할을 사용할 수 있습니다.

앱 등록에 역할을 할당하려면 다음을 수행합니다.

1. Azure Portal 열고 검색 서비스로 이동합니다.
1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. 오른쪽에서 이 **리소스에 대한 액세스 권한 부여** 아래에서 역할 할당 **추가를** 선택합니다.
1. 사용하려는 역할을 선택하고 **다음을** 클릭합니다.
1. 다음 페이지에서 멤버 **선택을** 클릭하고 이전에 만든 애플리케이션을 찾습니다. 
1. 마지막으로 검토 **+ 할당을** 클릭합니다.

![Azure Portal에서 역할 할당 추가](media/search-howto-aad/role-assignment.png)

[PowerShell 을 사용하여 역할을 할당할](/azure/search/search-security-rbac?tabs=config-svc-rest%2Croles-powershell%2Ctest-rest#step-3-assign-roles)수도 있습니다.

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

기본 제공 [역할을](/azure/search/search-security-rbac?tabs=config-svc-portal%2Croles-portal%2Ctest-portal#built-in-roles-used-in-search)사용하는 것 외에도 사용자 [지정 역할을](/azure/role-based-access-control/custom-roles) 만들어 애플리케이션에서 수행할 수 있는 작업을 정확하게 정의할 수 있습니다.

예를 들어 인덱스를 만들고 인덱스에서 데이터를 읽는 기능을 포함하여 인덱스를 완전히 관리할 수 있는 역할을 원하는 경우 아래와 같은 역할을 정의할 수 있습니다.

```json
{
  "Name": "Search Index Manager",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can manage search indexes and read or write to them",
  "Actions": [
    "Microsoft.Search/searchServices/indexes/*",
    
  ],
  "NotActions": [],
  "DataActions": [
      "Microsoft.Search/searchServices/indexes/documents/*"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}"
  ]
}
```

[Azure Portal](/azure/role-based-access-control/custom-roles-portal), [Azure PowerShell](/azure/role-based-access-control/custom-roles-powershell), [Azure CLI](/azure/role-based-access-control/custom-roles-cli) 또는 [REST API](/azure/role-based-access-control/custom-roles-rest)를 사용하여 사용자 지정 역할을 만들 수 있습니다. 위의 JSON은 PowerShell을 통해 사용자 지정 역할을 만들기 위한 구문을 보여줍니다.

사용 가능한 작업의 전체 목록은 [Microsoft.Search 리소스 공급자 작업을 참조하세요.](/azure/role-based-access-control/resource-provider-operations#microsoftsearch)


### <a name="grant-access-to-only-a-single-index"></a>단일 인덱스만 액세스 권한 부여

일부 시나리오에서는 인덱스와 같은 단일 리소스에 대한 애플리케이션의 액세스 범위를 축소할 수 있습니다. 

포털은 현재 단일 인덱스만 액세스 권한 부여를 지원하지 않지만 [PowerShell](../role-based-access-control/role-assignments-powershell.md) 또는 [Azure CLI](../role-based-access-control/role-assignments-cli.md)사용하여 수행할 수 있습니다.

PowerShell에서는 [New-AzRoleAssignment를](/powershell/module/az.resources/new-azroleassignment)사용하여 Azure 사용자 또는 그룹 이름과 할당 범위를 제공합니다.

시작하기 전에 Azure 및 AzureAD 모듈을 로드하고 Azure 계정에 연결해야 합니다.

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

범위가 지정된 역할 할당을 개별 인덱스로 추가하려면 다음 명령을 실행합니다.

```powershell
New-AzRoleAssignment -ObjectId <objectId> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

## <a name="set-up-azure-ad-authentication-in-your-client"></a>클라이언트에서 Azure AD 인증 설정

Azure AD 애플리케이션을 만들고 검색 서비스에 액세스할 수 있는 권한을 부여했으면 애플리케이션에 코드를 추가하여 보안 주체를 인증하고 OAuth 2.0 토큰을 획득할 준비가 된 것입니다.

### <a name="azure-ad-authentication-with-the-net-sdk"></a>.NET SDK를 사용하는 Azure AD 인증

Azure SDK를 통해 Azure AD와 쉽게 통합할 수 있습니다. 버전 [11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) 이상은 Azure AD 인증을 지원합니다. Azure AD 인증은 [Java,](https://search.maven.org/artifact/com.azure/azure-search-documents/11.5.0-beta.3/jar) [Python](https://pypi.org/project/azure-search-documents/11.3.0b3/)및 [JavaScript용](https://www.npmjs.com/package/@azure/search-documents/v/11.3.0-beta.3)미리 보기 SDK에서도 지원됩니다.

시작점으로 C# 빠른 시작 에 대한 [소스 코드를](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) [복제합니다.](search-get-started-dotnet.md)  빠른 시작에서는 현재 키 기반 인증을 사용하여 `SearchClient` 를 `SearchIndexClient` 만들지만 약간 변경하여 역할 기반 인증으로 전환할 수 있습니다. `AzureKeyCredential` `Main()` [Program.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v11/AzureSearchQuickstart-v11/Program.cs)의 시작 부분에서 를 사용하는 대신 

```dotnet
AzureKeyCredential credential = new AzureKeyCredential(apiKey);

// Create a SearchIndexClient to send create/delete index commands
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);
// Create a SearchClient to load and query documents
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
```

를 사용하여 검색 서비스를 인증할 수 `ClientSecretCredential` 있습니다.

이 경우 다음이 필요합니다.
+ 테넌트(또는 디렉터리) ID. 앱 등록의 개요 페이지에서 검색할 수 있습니다.
+ 클라이언트(또는 애플리케이션) ID. 앱 등록의 개요 페이지에서 검색할 수 있습니다.
+ 미리 보기 단계에서 복사한 클라이언트 암호의 값입니다.

```dotnet
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, tokenCredential);
```

를 사용하려면 [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/) 라이브러리를 가져와야 `ClientSecretCredential` 합니다.

Azure.Identity 설명서에는 [.NET용 Azure SDK에서 Azure AD 인증을](/dotnet/api/overview/azure/identity-readme)사용하는 데 대한 추가 세부 정보도 있습니다.

### <a name="azure-ad-authentication-with-the-rest-api"></a>REST API Azure AD 인증

Azure SDK를 사용하면 OAuth 2.0 흐름이 간소화되지만 애플리케이션의 프로토콜에 대해 직접 프로그래밍할 수도 있습니다. 전체 세부 정보는 [Microsoft ID 플랫폼 OAuth 2.0 클라이언트 자격 증명 흐름에서](/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)사용할 수 있습니다.

#### <a name="get-a-token"></a>토큰 가져오기

먼저 Microsoft ID 플랫폼에서 [토큰을 가져옵니다](/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#get-a-token) .

```
POST /[tenant id]/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=[client id]
&scope=https%3A%2F%2Fsearch.azure.com%2F.default
&client_secret=[client secret]
&grant_type=client_credentials
```

필요한 범위는 " https://search.azure.com/.default "입니다. 

#### <a name="use-a-token"></a>토큰 사용

이제 토큰이 있으므로 검색 서비스에 대 한 요청을 실행할 준비가 되었습니다. 

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
Content-Type: application/json   
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search에서 역할 기반 권한 부여 사용](search-security-rbac.md)
+ [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](/azure/active-directory/develop/v2-oauth2-auth-code-flow)
+ [Azure Active Directory와 통합](/azure/active-directory/develop/active-directory-how-to-integrate#benefits-of-integration)
+ [Azure 사용자 지정 역할](/azure/role-based-access-control/custom-roles)

