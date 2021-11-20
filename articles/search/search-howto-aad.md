---
title: Azure AD를 사용 하 여 검색 앱 요청 권한 부여
titleSuffix: Azure Cognitive Search
description: Azure AD에서 토큰을 획득 하 여 Azure Cognitive Search에서 빌드된 앱에 대 한 검색 요청에 권한을 부여 합니다.
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/19/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 0ed871f0a6834077d598d8757b69bebf4611c31d
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871740"
---
# <a name="authorize-access-to-a-search-apps-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 검색 앱에 대 한 액세스 권한 부여

> [!IMPORTANT]
> 인덱스 만들기 또는 인덱스 쿼리와 같은 데이터 평면 작업에 대 한 역할 기반 액세스 제어는 현재 공개 미리 보기 상태 이며 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 사용할 수 있습니다. 이 기능은 공용 클라우드 지역 에서만 사용할 수 있으며 기능이 미리 보기 상태인 동안 작업의 대기 시간에 영향을 줄 수 있습니다. 미리 보기 제한 사항에 대 한 자세한 내용은 [RBAC 미리 보기 제한 사항](search-security-rbac.md#preview-limitations)을 참조 하세요.

이제 azure Cognitive Search에서 빌드된 검색 응용 프로그램에서 인증 및 권한 있는 액세스를 위해 azure AD (Azure Active Directory) 및 azure RBAC (역할 기반 액세스)를 사용할 수 있습니다. Azure AD를 사용 하는 경우의 주요 이점은 자격 증명 및 API 키를 코드에 더 이상 저장할 필요가 없다는 점입니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Cognitive Search에 대 한 요청에 권한을 부여할 수 있습니다. 응용 프로그램에서 Azure AD를 사용 하는 이점에 대 한 자세한 내용은 [Azure Active Directory 통합](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)을 참조 하세요.

이 문서에서는 [Microsoft ID 플랫폼](../active-directory/develop/v2-overview.md)를 사용 하 여 인증을 위해 응용 프로그램을 구성 하는 방법을 보여 줍니다. Azure AD에서 사용 하는 oauth 2.0 코드 부여 흐름에 대 한 자세한 내용은 [oauth 2.0 코드 부여 흐름을 사용 하 여 Azure Active Directory 웹 응용 프로그램에 대 한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조 하세요.

## <a name="prepare-your-search-service"></a>검색 서비스 준비

첫 번째 단계로 [search 서비스를 만들고](search-create-service-portal.md) Azure 역할 기반 액세스 제어 (RBAC)를 사용 하도록 구성 합니다.

### <a name="sign-up-for-the-preview"></a>미리 보기 등록

Search 서비스를 쿼리 하는 데 Azure AD를 사용 하는 데 필요한 Azure Cognitive Search의 RBAC 기능 부분은 미리 보기 상태입니다. 이러한 기능을 사용 하려면 Azure 구독에 미리 보기 기능을 추가 해야 합니다.

1. [Azure Portal](https://portal.azure.com/) 를 열고 검색 서비스를 찾습니다.

1. 왼쪽 탐색 창에서 **키** 를 선택 합니다.

1. 미리 보기를 언급 하는 파란색 배너에서 **등록** 을 선택 하 여 구독에 기능을 추가 합니다.

   :::image type="content" source="media/search-howto-aad/rbac-signup-portal.png" alt-text="포털에서 rbac 미리 보기에 등록 하는 방법의 스크린샷" border="true" :::

Azure 기능 노출 제어 (AFEC)를 사용 하 여 미리 보기에 등록 하 고 *Search Service (미리 보기)에 대 한 Access Control 역할을 기준으로* 검색할 수도 있습니다. 미리 보기 기능을 추가 하는 방법에 대 한 자세한 내용은 [Azure 구독에서 미리 보기 기능 설정](../azure-resource-manager/management/preview-features.md?tabs=azure-portal)을 참조 하세요.

> [!NOTE]
> 구독에 미리 보기를 추가 하면 구독에 있는 모든 서비스가 미리 보기에 영구적으로 등록 됩니다. 지정 된 서비스에서 RBAC를 원하지 않는 경우 이후 단계에서와 같이 데이터 평면 작업에 대해 RBAC를 사용 하지 않도록 설정할 수 있습니다.

### <a name="enable-rbac-for-data-plane-operations"></a>데이터 평면 작업에 RBAC 사용

구독이 미리 보기에 추가 되 면 Azure AD 인증을 사용할 수 있도록 데이터 평면 작업에 대해 RBAC를 사용 하도록 설정 해야 합니다. 기본적으로 Azure Cognitive Search는 데이터 평면 작업에 대해 키 기반 인증을 사용 하지만 역할 기반 액세스 제어를 허용 하도록 설정을 변경할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에서 검색 서비스로 이동 합니다.

1. 왼쪽 탐색 창에서 **키** 를 선택 합니다.

1. 키 기반 액세스 제어와 역할 기반 액세스 제어를 모두 허용 하 시겠습니까, 아니면 역할 기반 액세스 제어만 허용 하는지 결정 합니다.

   :::image type="content" source="media/search-howto-aad/portal-api-access-control.png" alt-text="포털에서 azure 인지 검색에 대 한 인증 옵션의 스크린샷" border="true" :::

[Azure COGNITIVE SEARCH RBAC 설명서](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-2-preview-configuration)에 설명 된 대로 이러한 설정을 프로그래밍 방식으로 변경할 수도 있습니다.

## <a name="register-an-application-with-azure-ad"></a>Azure AD에 애플리케이션을 등록합니다.

인증을 위해 Azure AD를 사용 하는 다음 단계는 [Microsoft ID 플랫폼](../active-directory/develop/quickstart-register-app.md)에 응용 프로그램을 등록 하는 것입니다. 응용 프로그램을 등록 하는 데 문제가 있는 경우 [필요한 권한이](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)있는지 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정에 로그인 합니다.

1. **Azure Active Directory** 검색

1. **앱 등록** 을 선택합니다.

1. **+ 새 등록** 을 선택합니다.

1. 응용 프로그램에 이름을 지정 하 고, 응용 프로그램을 사용할 수 있는 사용자를 결정 하는 지원 되는 계정 유형을 선택 합니다. 그런 다음 **등록** 을 선택합니다.

   :::image type="content" source="media/search-howto-aad/register-app.png" alt-text="응용 프로그램 등록 마법사의 스크린샷" border="true" :::

이 시점에서 Azure AD 응용 프로그램 및 서비스 주체를 만들었습니다. 앱 등록의 개요 페이지에서 테 넌 트 (또는 디렉터리) ID와 클라이언트 또는 응용 프로그램 ID를 적어 둡니다. 이후 단계에서 이러한 값이 필요 합니다.

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램에는 토큰을 요청할 때 해당 id를 증명 하기 위한 클라이언트 암호 또는 인증서도 필요 합니다. 

1. 만든 앱 등록으로 이동 합니다.

1. **인증서 및 암호를** 선택 합니다.

1. **클라이언트 암호** 아래에서 **새 클라이언트 암호** 를 선택합니다.

1. 비밀에 대 한 설명을 제공 하 고 원하는 만료 간격을 선택 합니다.

   :::image type="content" source="media/search-howto-aad/create-secret.png" alt-text="클라이언트 암호 만들기 마법사 스크린샷" border="true" :::

값에 다시 액세스할 수 없으므로 보안 위치에 비밀 값을 저장 해야 합니다. 

## <a name="assign-a-role-to-the-application"></a>애플리케이션에 역할 할당

다음으로, Azure AD 응용 프로그램에 검색 서비스에 대 한 액세스 권한을 부여 해야 합니다. Azure Cognitive Search에는 다양 한 [기본 제공 역할이](search-security-rbac.md#built-in-roles-used-in-search)있습니다. [사용자 지정 역할](search-security-rbac.md#create-a-custom-role)을 만들 수도 있습니다.

일반적으로 응용 프로그램에 필요한 액세스만 제공 하는 것이 가장 좋습니다. 예를 들어 응용 프로그램에서 검색 인덱스를 쿼리할 수 있어야 하는 경우 [검색 인덱스 데이터 판독기 (미리 보기)](../role-based-access-control/built-in-roles.md#search-index-data-reader) 역할에 해당 인덱스를 부여할 수 있습니다. 또는 검색 인덱스를 읽고 쓸 수 있어야 하는 경우 [검색 인덱스 데이터 참가자 (미리 보기)](../role-based-access-control/built-in-roles.md#search-index-data-contributor) 역할을 사용할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com)을 엽니다.

1. 검색 서비스로 이동합니다.

1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택합니다.

   ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. 해당 하는 역할을 선택 합니다.

   + 소유자
   + 참가자
   + 판독기
   + Search 서비스 참가자
   + 인덱스 데이터 참여자 검색 (미리 보기)
   + 검색 인덱스 데이터 판독기 (미리 보기)

1. **구성원** 탭에서 응용 프로그램이 실행 되는 Azure AD 사용자 또는 그룹 id를 선택 합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.

[PowerShell을 사용 하 여 역할을 할당할](./search-security-rbac.md?tabs=config-svc-rest%2croles-powershell%2ctest-rest#step-3-assign-roles)수도 있습니다.

## <a name="set-up-azure-ad-authentication-in-your-client"></a>클라이언트에서 Azure AD 인증 설정

Azure AD 응용 프로그램이 생성 되 고 검색 서비스에 액세스할 수 있는 권한이 부여 되 면 응용 프로그램에 코드를 추가 하 여 보안 주체를 인증 하 고 OAuth 2.0 토큰을 획득할 수 있습니다.

[Java](https://search.maven.org/artifact/com.azure/azure-search-documents/11.5.0-beta.3/jar), [Python](https://pypi.org/project/azure-search-documents/11.3.0b3/)및 [JavaScript](https://www.npmjs.com/package/@azure/search-documents/v/11.3.0-beta.3)용 미리 보기 sdk 에서도 Azure AD 인증을 지원 합니다.

### <a name="net-sdk"></a>[**.NET SDK**](#tab/aad-dotnet)

Azure Sdk를 사용 하면 Azure AD와 쉽게 통합할 수 있습니다. 버전 [11.4.0-beta. 2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) 이상 .net SDK는 Azure AD 인증을 지원 합니다.

다음 지침에서는 기존 c # 샘플을 참조 하 여 코드 변경 내용을 보여 줍니다.

1. 시작 지점으로 [c # 빠른](search-get-started-dotnet.md)시작에 대 한 [소스 코드](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) 를 복제 합니다.

   이 샘플에서는 현재 키 기반 인증을 사용 하 여 `SearchClient` 를 `SearchIndexClient` 만들지만 작은 변경을 수행 하 여 역할 기반 인증으로 전환할 수 있습니다. Program .cs의 시작 부분에서를 사용 하는 대신 `AzureKeyCredential` `Main()` 다음을 사용 [합니다](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v11/AzureSearchQuickstart-v11/Program.cs). 

   ```csharp
   AzureKeyCredential credential = new AzureKeyCredential(apiKey);
     
   // Create a SearchIndexClient to send create/delete index commands
   SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);
   // Create a SearchClient to load and query documents
   SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
   ```

1. `ClientSecretCredential`검색 서비스를 사용 하 여 인증 하려면를 사용 합니다. 먼저, 사용할 [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/) 라이브러리를 가져옵니다 `ClientSecretCredential` .

1. 다음 문자열을 제공 해야 합니다.

   + 테넌트(또는 디렉터리) ID. 앱 등록의 개요 페이지에서 검색할 수 있습니다.
   + 클라이언트(또는 애플리케이션) ID. 앱 등록의 개요 페이지에서 검색할 수 있습니다.
   + 미리 보기 단계에서 복사한 클라이언트 암호의 값입니다.

   ```csharp
   var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
   SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, tokenCredential);
   ```

또한 azure. Identity 설명서에는 azure [SDK for .net과 함께 AZURE AD 인증](/dotnet/api/overview/azure/identity-readme)을 사용 하는 방법에 대 한 추가 정보가 포함 되어 있습니다.

### <a name="rest-api"></a>[**REST API**](#tab/aad-rest)

Azure SDK를 사용 하면 OAuth 2.0 흐름이 간소화 되지만 응용 프로그램에서 프로토콜에 대해 직접 프로그래밍할 수도 있습니다. 전체 세부 정보는 [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)에서 사용할 수 있습니다.

1. 먼저 Microsoft ID 플랫폼에서 [토큰을 가져옵니다](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token) .

   ```http
   POST /[tenant id]/oauth2/v2.0/token HTTP/1.1
   Host: login.microsoftonline.com
   Content-Type: application/x-www-form-urlencoded

   client_id=[client id]
   &scope=https%3A%2F%2Fsearch.azure.com%2F.default
   &client_secret=[client secret]
   &grant_type=client_credentials
   ```

   필요한 범위는 " https://search.azure.com/.default "입니다. 

1. 이제 토큰이 생겼으므로 검색 서비스에 요청을 발급할 준비가 되었습니다. 

   ```http
   GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
   Content-Type: application/json   
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
   ```

---

## <a name="see-also"></a>추가 정보

+ [Azure Cognitive Search Azure 역할 기반 액세스 제어 사용](search-security-rbac.md)
+ [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)
+ [Azure Active Directory와 통합](../active-directory/develop/active-directory-how-to-integrate.md#benefits-of-integration)
+ [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)