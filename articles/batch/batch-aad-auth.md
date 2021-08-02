---
title: Azure Active Directory를 사용하여 Azure Batch 서비스 인증
description: Batch는 Batch 서비스의 인증을 위해 Azure AD를 지원합니다. 두 가지 방법 중 하나로 인증하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 05/13/2021
ms.custom: has-adal-ref, subject-rbac-steps
ms.openlocfilehash: 52e34bb94ab151afcfcdc14d2de55e608d82a139
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066757"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Active Directory를 사용하여 Batch 서비스 솔루션 인증

Azure Batch는 Azure AD([Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) 인증을 지원합니다. Azure AD는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure에서는 Azure AD를 자체적으로 사용하여 고객, 서비스 관리자 및 조직 사용자를 인증합니다.

Azure Batch와 함께 Azure AD 인증을 사용할 때 다음 두 가지 방법 중 하나로 인증할 수 있습니다.

- **통합 인증** 을 사용하여 애플리케이션과 상호 작용하는 사용자를 인증합니다. 통합 인증을 사용하는 애플리케이션에서는 사용자의 자격 증명을 수집하고 해당 자격 증명을 사용하여 Batch 리소스에 대한 액세스를 인증합니다.
- **서비스 주체** 를 사용하여 무인으로 실행되는 애플리케이션을 인증합니다. 서비스 주체는 런타임에 리소스에 액세스할 때 애플리케이션을 나타내기 위해 애플리케이션에 대한 정책과 권한을 정의합니다.

Azure AD에 대한 자세한 내용은 [Azure Active Directory 설명서](../active-directory/index.yml)를 참조하세요.

## <a name="endpoints-for-authentication"></a>인증에 대한 엔드포인트

Azure AD로 Batch 애플리케이션을 인증하려면 코드에서 잘 알려진 몇 가지 엔드포인트를 포함해야 합니다.

### <a name="azure-ad-endpoint"></a>Azure AD 엔드포인트

기본 Azure AD 인증 기관 엔드포인트는 다음과 같습니다.

`https://login.microsoftonline.com/`

Azure AD로 인증하려면 이 엔드포인트를 테넌트 ID(디렉터리 ID)와 함께 사용합니다. 테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 테넌트 ID를 검색하려면 [Azure Active Directory에 대한 테넌트 ID 가져오기](#get-the-tenant-id-for-your-active-directory)에서 설명하는 단계를 따릅니다.

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> 테넌트별 엔드포인트는 서비스 주체를 사용하여 인증할 때 필요합니다.
>
> 통합 인증을 사용하여 인증할 때 테넌트별 엔드포인트는 선택 사항이지만 권장됩니다. 그러나 Azure AD 공통 엔드포인트도 사용할 수 있습니다. 공통 엔드포인트는 특정 테넌트를 제공하지 않을 때 일반 자격 증명 수집 인터페이스를 제공합니다. 공통 엔드포인트는 `https://login.microsoftonline.com/common`입니다.

Azure AD 엔드포인트에 대한 자세한 내용은 [인증 및 권한 부여](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.

### <a name="batch-resource-endpoint"></a>Batch 리소스 엔드포인트

**Azure Batch 리소스 엔드포인트** 를 사용하여 Batch 서비스에 대한 요청을 인증하는 토큰을 얻습니다.

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>테넌트에 애플리케이션 등록

Azure AD를 사용하여 인증하는 첫 번째 단계는 Azure AD 테넌트에 애플리케이션을 등록하는 것입니다. 애플리케이션을 등록하면 코드에서 ADAL(Azure [Active Directory 인증 라이브러리](../active-directory/azuread-dev/active-directory-authentication-libraries.md))을 호출할 수 있습니다. ADAL은 애플리케이션에서 Azure AD로 인증하는 API를 제공합니다. 통합 인증 또는 서비스 주체를 사용하려면 애플리케이션을 등록해야 합니다.

애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그런 다음, Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 애플리케이션 ID(*클라이언트 ID* 라고도 함)를 제공합니다. 애플리케이션 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

Batch 애플리케이션을 등록하려면 [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](../active-directory/develop/quickstart-register-app.md)의 **애플리케이션 등록** 섹션에 있는 단계를 따르세요. 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 **리디렉션 URI** 에 유효한 URI를 지정할 수 있습니다. 실제 엔드포인트일 필요는 없습니다.

애플리케이션을 등록하면 애플리케이션 ID가 표시됩니다.

![Azure Portal에 표시된 애플리케이션 ID의 스크린샷](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Active Directory에 대한 테넌트 ID 가져오기

테넌트 ID는 애플리케이션에 인증 서비스를 제공하는 Azure AD 테넌트를 식별합니다. 테넌트 ID를 얻으려면 다음 단계를 수행합니다.

1. Azure Portal에서 Active Directory를 선택합니다.
1. **속성** 을 선택합니다.
1. **디렉터리 ID** 에 제공된 GUID 값을 복사합니다. 이 값은 테넌트 ID라고도 합니다.

![Azure Portal에 있는 디렉터리 ID의 스크린샷](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>통합 인증 사용

통합 인증으로 인증하려면 Batch 서비스 API에 연결하기 위한 권한을 애플리케이션에 부여해야 합니다. 이 단계에서는 애플리케이션에서 Azure AD를 사용하여 Batch 서비스 API에 대한 호출을 인증할 수 있습니다.

애플리케이션을 등록했으면 Azure Portal에서 다음 단계에 따라 Batch 서비스에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **모든 서비스** 를 선택한 다음 **앱 등록** 을 선택합니다.
1. 앱 등록 목록에서 애플리케이션의 이름을 검색합니다.
1. 애플리케이션을 선택하고 **API 사용 권한** 을 선택합니다.
1. **API 권한** 섹션에서 **권한 추가** 를 선택합니다.
1. **API 선택** 에서 일괄 처리 API를 검색합니다. API를 찾을 때까지 다음 문자열 각각을 검색합니다.
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** 는 Batch API에 대한 ID입니다.
1. Batch API를 찾으면 이를 선택한 다음 **선택** 을 선택합니다.
1. **권한 선택** 에서 **Azure Batch 서비스에 액세스** 옆의 확인란을 선택하고 **권한 추가** 를 선택합니다.

이제 **API 사용 권한** 섹션에 Azure AD 애플리케이션이 Microsoft Graph 및 Batch 서비스 API 모두에 액세스할 수 있다고 표시됩니다. Azure AD에 앱을 처음 등록할 때 자동으로 Microsoft Graph에 대한 사용 권한이 부여됩니다.

## <a name="use-a-service-principal"></a>서비스 주체 사용

무인으로 실행되는 애플리케이션을 인증하려면 서비스 사용자를 사용합니다. 애플리케이션을 등록했으면 Azure Portal에서 다음 단계에 따라 서비스 주체를 구성합니다.

1. 애플리케이션에 대한 비밀을 요청합니다.
1. Azure RBAC(역할 기반 액세스 제어)를 애플리케이션에 할당합니다.

### <a name="request-a-secret-for-your-application"></a>애플리케이션에 대한 비밀 요청

애플리케이션에서 서비스 주체를 사용하여 인증하는 경우 Azure AD에 애플리케이션 ID와 비밀을 모두 보냅니다. 코드에서 사용할 비밀 키를 만들고 복사해야 합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal에서 **모든 서비스** 를 선택합니다. **앱 등록** 을 선택합니다.
1. 앱 등록 목록에서 애플리케이션을 선택합니다.
1. 애플리케이션을 선택한 다음 **인증서 & 비밀** 을 선택합니다. **클라이언트 비밀** 섹션에서 **새 클라이언트 비밀** 을 선택합니다.
1. 비밀을 만들려면 비밀에 대한 설명을 입력합니다. 그런 다음 1년, 2년 또는 만료 없음 중에서 비밀에 대한 만료를 선택합니다.
1. **추가** 를 선택하여 비밀을 만들고 표시합니다. 페이지를 닫은 후에 다시 액세스할 수 없으므로 비밀 값을 안전한 곳에 복사해 둡니다.

### <a name="assign-azure-rbac-to-your-application"></a>애플리케이션에 Azure RBAC 할당

서비스 주체로 인증하려면 애플리케이션에 Azure RBAC를 할당해야 합니다. 다음 단계를 수행하세요.

1. Azure Portal에서 애플리케이션에서 사용되는 Batch 계정으로 이동합니다.
1. Batch 계정의 **설정** 섹션에서 **Access Control(IAM)** 을 선택합니다.
1. 애플리케이션에 [기여자](../role-based-access-control/built-in-roles.md#contributor) 또는 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

이제 액세스 제어 설정에서 Azure 역할이 할당된 애플리케이션이 표시됩니다.

### <a name="assign-a-custom-role"></a>사용자 지정 역할 할당

사용자 지정 역할은 사용자에게 작업, 태스크 등을 제출하기 위한 세부적인 사용 권한을 부여합니다. 이를 통해 사용자가 풀 만들기 또는 노드 수정과 같이 비용에 영향을 주는 작업을 수행할 수 없도록 할 수 있습니다.

사용자 지정 역할을 사용하여 다음 Azure RBAC 작업에 대해 Azure AD 사용자, 그룹 또는 서비스 주체에 권한을 부여할 수 있습니다.

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read(읽기 작업의 경우)
- Microsoft.Batch/batchAccounts/listKeys/action(모든 작업)

사용자 지정 역할은 Batch 계정 자격 증명(공유 키)이 아닌 Azure AD에서 인증한 사용자를 위한 것입니다. Batch 계정 자격 증명은 Batch 계정에 대한 모든 사용 권한을 부여합니다. 또한 [자동 풀](nodes-and-pools.md#autopools)을 사용하는 작업에는 풀 수준 권한이 필요합니다.

> [!NOTE]
> 특정 역할 할당은 작업 필드에 지정해야 하지만 다른 역할 할당은 DataAction 필드에 지정해야 합니다. 자세한 내용은 [Azure 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftbatch)을 참조하세요.

다음은 사용자 지정 역할 정의에 대한 예입니다.

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Azure Active Directory에 대한 테넌트 ID 가져오기

테넌트 ID는 애플리케이션에 인증 서비스를 제공하는 Azure AD 테넌트를 식별합니다. 테넌트 ID를 얻으려면 다음 단계를 수행합니다.

1. Azure Portal에서 Active Directory를 선택합니다.
1. **속성** 을 선택합니다.
1. **디렉터리 ID** 에 제공된 GUID 값을 복사합니다. 이 값은 테넌트 ID라고도 합니다.

![디렉터리 ID 복사](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>코드 예제

이 섹션의 코드 예제에서는 Azure AD에서 통합 인증과 서비스 주체를 사용하여 인증하는 방법을 보여 줍니다. 이러한 코드 예제 대부분은 .NET을 사용하지만, 개념은 다른 언어와 비슷합니다.

> [!NOTE]
> Azure AD 인증 토큰은 1시간 후 만료됩니다. 수명이 긴 **BatchClient** 개체를 사용하는 경우 항상 유효한 토큰을 갖도록 모든 요청에 대해 ADAL에서 토큰을 검색하는 것이 좋습니다.
>
> 이를 위해 .NET에서 Azure AD에서 토큰을 검색하는 메서드를 작성하고 해당 메서드를 대리자로 **BatchTokenCredentials** 개체에 전달합니다. 대리자 메서드는 유효한 토큰이 제공되었는지 확인하도록 Batch 서비스에 대한 모든 요청에서 호출됩니다. 기본적으로 ADAL은 토큰을 캐시하므로 필요한 경우 Azure AD에서 새 토큰이 검색됩니다. Azure AD의 토큰에 대한 자세한 내용은 [보안 토큰](../active-directory/develop/security-tokens.md)을 참조하세요.

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>코드 예제: Batch .NET에서 Azure AD 통합 인증 사용

Batch .NET의 통합 인증으로 인증하려면 [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 패키지 및 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 패키지를 참조합니다.

코드에 다음 `using` 문을 포함시킵니다.

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

코드에서 테넌트 ID를 포함하여 Azure AD 엔드포인트를 참조합니다. 테넌트 ID를 검색하려면 [Azure Active Directory에 대한 테넌트 ID 가져오기](#get-the-tenant-id-for-your-active-directory)에서 설명하는 단계를 따릅니다.

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch 서비스 리소스 엔드포인트 참조:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch 계정 참조:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

애플리케이션에 대한 애플리케이션 ID(클라이언트 ID)를 지정합니다. 애플리케이션 ID는 Azure Portal의 앱 등록에서 사용할 수 있습니다.

```csharp
private const string ClientId = "<application-id>";
```

또한 네이티브 애플리케이션으로 애플리케이션을 등록하는 경우 사용자가 지정한 리디렉션 URI를 복사합니다. 코드에 지정된 리디렉션 URI는 애플리케이션을 등록할 때 제공한 리디렉션 URI와 일치해야 합니다.

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Azure AD에서 인증 토큰을 획득하는 콜백 메서드를 작성합니다. 여기에 표시된 **GetAuthenticationTokenAsync** 콜백 메서드는 ADAL을 호출하여 애플리케이션과 상호 작용하는 사용자를 인증합니다. ADAL에서 제공하는 **AcquireTokenAsync** 메서드는 사용자에게 자격 증명을 묻는 메시지를 표시하고, 사용자가 자격 증명을 제공하면 애플리케이션이 계속 진행됩니다(이미 캐시된 자격 증명이 있는 경우 제외).

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

매개 변수로 대리자를 사용자는 **BatchTokenCredentials** 개체를 생성합니다. 이러한 자격 증명을 사용하여 **BatchClient** 개체를 엽니다. Batch 서비스에 대한 후속 작업에 대해 해당 **BatchClient** 개체를 사용할 수 있습니다.

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>코드 예제: Batch .NET에서 Azure AD 서비스 주체 사용

Batch .NET의 서비스 주체로 인증하려면 [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) 패키지 및 [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 패키지를 참조합니다.

코드에 다음 `using` 문을 포함시킵니다.

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

코드에서 테넌트 ID를 포함하여 Azure AD 엔드포인트를 참조합니다. 서비스 주체를 사용하는 경우 테넌트별 엔드포인트를 제공해야 합니다. 테넌트 ID를 검색하려면 [Azure Active Directory에 대한 테넌트 ID 가져오기](#get-the-tenant-id-for-your-active-directory)에서 설명하는 단계를 따릅니다.

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch 서비스 리소스 엔드포인트 참조:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch 계정 참조:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

애플리케이션에 대한 애플리케이션 ID(클라이언트 ID)를 지정합니다. 애플리케이션 ID는 Azure Portal의 앱 등록에서 사용할 수 있습니다.

```csharp
private const string ClientId = "<application-id>";
```

Azure Portal에서 복사한 비밀 키를 지정합니다.

```csharp
private const string ClientKey = "<secret-key>";
```

Azure AD에서 인증 토큰을 획득하는 콜백 메서드를 작성합니다. 여기에 표시된 **GetAuthenticationTokenAsync** 콜백 메서드는 무인 인증을 위해 ADAL을 호출합니다.

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

매개 변수로 대리자를 사용자는 **BatchTokenCredentials** 개체를 생성합니다. 이러한 자격 증명을 사용하여 **BatchClient** 개체를 엽니다. 그런 다음, Batch 서비스에 대한 후속 작업에 해당 **BatchClient** 개체를 사용합니다.

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>코드 예제: Batch Python에서 Azure AD 서비스 주체 사용

Batch Python의 서비스 주체로 인증하려면 [azure-batch](https://pypi.org/project/azure-batch/) 및 [azure-common](https://pypi.org/project/azure-common/) 모듈을 설치 및 참조합니다.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

서비스 주체를 사용하는 경우 테넌트 ID를 제공해야 합니다. 테넌트 ID를 검색하려면 [Azure Active Directory에 대한 테넌트 ID 가져오기](#get-the-tenant-id-for-your-active-directory)에서 설명하는 단계를 따릅니다.

```python
TENANT_ID = "<tenant-id>"
```

Batch 서비스 리소스 엔드포인트 참조:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Batch 계정 참조:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

애플리케이션에 대한 애플리케이션 ID(클라이언트 ID)를 지정합니다. 애플리케이션 ID는 Azure Portal의 앱 등록에서 사용할 수 있습니다.

```python
CLIENT_ID = "<application-id>"
```

Azure Portal에서 복사한 비밀 키를 지정합니다.

```python
SECRET = "<secret-key>"
```

**ServicePrincipalCredentials** 개체를 만듭니다.

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

서비스 주체 자격 증명을 사용하여 **BatchServiceClient** 개체를 엽니다. 그런 다음, Batch 서비스에 대한 후속 작업에 해당 **BatchServiceClient** 개체를 사용합니다.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 문서](../active-directory/index.yml)를 검토합니다. ADAL을 사용하는 방법을 보여 주는 자세한 예제는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory) 라이브러리에서 사용할 수 있습니다.
- [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)와 [리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체를 만드는 방법](../active-directory/develop/howto-create-service-principal-portal.md)에 대해 알아봅니다.
- [Active Directory로 일괄 관리 솔루션 인증](batch-aad-auth-management.md)에 대해 알아봅니다.
- Azure AD 토큰을 사용하여 인증된 Batch 클라이언트를 만드는 방법의 Python 예제의 경우 [Python 스크립트를 사용하여 Azure Batch 사용자 지정 이미지 배포](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) 샘플을 참조하세요.
