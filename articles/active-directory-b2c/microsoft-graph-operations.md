---
title: Microsoft Graph를 사용하여 리소스 관리
titleSuffix: Azure AD B2C
description: Microsoft Graph API를 호출하고 프로세스를 자동화하는 애플리케이션 ID를 사용하여 Azure AD B2C 테넌트의 리소스를 관리하는 방법입니다.
services: B2C
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/08/2021
ms.custom: project-no-code, ignite-fall-2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 19a13d9c2941d9a98cce4415b9266d67a9bb8e53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028104"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph로 Azure AD B2C 관리

Microsoft Graph를 사용하여 Azure AD B2C 디렉터리에서 리소스를 관리할 수 있습니다. Microsoft Graph API 작업은 사용자, ID 공급자, 사용자 흐름, 사용자 지정 정책 및 정책 키를 포함한 Azure AD B2C 리소스의 관리를 지원합니다. 다음 섹션의 각 링크는 해당 작업에 대한 Microsoft Graph API 참조 내의 해당 페이지를 대상으로 합니다. 

> [!NOTE]
> Azure 구독에 연결된 해당 Azure 리소스와 함께 Azure AD B2C 디렉터리 자체를 프로그래밍 방식으로 만들 수도 있습니다. 이 기능은 Microsoft Graph API를 통해서가 아니라 Azure REST API를 통해 제공됩니다. 자세한 내용은 [B2C 테넌트 - 만들기](/rest/api/activedirectory/b2c-tenants/create)를 참조하세요.

이 비디오를 시청하여 Microsoft Graph API를 사용해 Azure AD B2C 사용자 마이그레이션에 대해 알아보세요.

>[!Video https://www.youtube.com/embed/9BRXBtkBzL4]

## <a name="prerequisites"></a>사전 요구 사항

MS Graph API를 사용하고 Azure AD B2C 테넌트의 리소스와 상호 작용하려면 그렇게 하도록 권한을 부여하는 애플리케이션 등록이 필요합니다. 관리 애플리케이션에서 사용할 수 있는 애플리케이션 등록을 만들려면 [Microsoft Graph를 사용하여 Azure AD B2C 관리](microsoft-graph-get-started.md) 문서의 단계를 수행합니다. 

## <a name="user-management"></a>사용자 관리

- [사용자 나열](/graph/api/user-list)
- [소비자 사용자 만들기](/graph/api/user-post-users)
- [사용자 한 명 가져오기](/graph/api/user-get)
- [사용자 업데이트](/graph/api/user-update)
- [사용자 삭제](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>사용자 전화 번호 관리(베타)

사용자가 [SMS 또는 음성 통화](sign-in-options.md#phone-sign-in)를 또는 [다단계 인증](multi-factor-authentication.md)을 사용하여 로그인하는 데 사용할 수 있는 전화 번호입니다. 자세한 내용은 [Azure AD 인증 방법 API](/graph/api/resources/phoneauthenticationmethod)를 참조하세요.

- [추가](/graph/api/authentication-post-phonemethods)
- [목록](/graph/api/authentication-list-phonemethods)
- [가져오기](/graph/api/phoneauthenticationmethod-get)
- [업데이트](/graph/api/phoneauthenticationmethod-update)
- [삭제](/graph/api/phoneauthenticationmethod-delete)

[목록](/graph/api/authentication-list-phonemethods) 작업은 사용하도록 설정된 전화 번호만 반환함을 참고하여 주십시오. 목록 작업과 함께 사용하려면 다음 전화 번호를 사용하도록 설정해야 합니다. 

![휴대폰 로그인 활성화](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> 현재 베타 버전에서 이 API는 전화번호가 국가 코드와 전화번호 사이에 공백으로 저장된 경우에만 작동합니다. Azure AD B2C 서비스는 현재 이 공간을 기본적으로 추가하지 않습니다.

## <a name="self-service-password-reset-email-address-beta"></a>셀프 서비스 암호 재설정 이메일 주소(베타)

[사용자 이름 로그인 계정](sign-in-options.md#username-sign-in)에서 암호를 재설정하는 데 사용할 수 있는 이메일 주소입니다. 자세한 내용은 [Azure AD 인증 방법 API](/graph/api/resources/emailauthenticationmethod)를 참조하세요.

- [추가](/graph/api/emailauthenticationmethod-post)
- [목록](/graph/api/emailauthenticationmethod-list)
- [가져오기](/graph/api/emailauthenticationmethod-get)
- [업데이트](/graph/api/emailauthenticationmethod-update)
- [삭제](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>ID 공급자

Azure AD B2C 테넌트에서 사용자 흐름에서 사용할 수 있는 [ID 공급자](add-identity-provider.md)를 관리합니다.

- [Azure AD B2C 테넌트에 등록된 ID 공급자 목록](/graph/api/identityprovider-list)
- [ID 공급자를 만들기](/graph/api/identityprovider-post-identityproviders)
- [ID 공급자 가져오기](/graph/api/identityprovider-get)
- [ID 공급자 업데이트](/graph/api/identityprovider-update)
- [ID 공급자 삭제](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>사용자 흐름

등록, 로그인, 결합된 등록 및 로그인, 암호 재설정 및 프로필 업데이트에 대해 미리 작성된 정책을 구성합니다.

- [사용자 흐름 나열하기](/graph/api/identitycontainer-list-b2cuserflows)
- [사용자 흐름 만들기](/graph/api/identitycontainer-post-b2cuserflows)
- [사용자 흐름 가져오기](/graph/api/b2cidentityuserflow-get)
- [사용자 흐름 삭제](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>사용자 흐름 인증 방법(베타)

사용자가 로컬 계정을 통해 등록할 수 있도록 허용하는 메커니즘을 선택합니다. 로컬 계정은 Azure AD가 ID 어설션을 수행하는 계정입니다. 자세한 내용은, [b2cAuthenticationMethodsPolicy 리소스 종류](/graph/api/resources/b2cauthenticationmethodspolicy)를 참조하세요.

- [가져오기](/graph/api/b2cauthenticationmethodspolicy-get)
- [업데이트](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>사용자 지정 정책

다음 작업을 사용하여 [사용자 지정 정책](custom-policy-overview.md)이라고 하는 Azure AD B2C 보안 프레임 워크 정책을 관리할 수 있습니다.

- [테넌트에서 구성된 모든 보안 프레임 워크 정책을 나열합니다](/graph/api/trustframework-list-trustframeworkpolicies)
- [보안 프레임워크 정책](/graph/api/trustframework-post-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책의 속성을 읽습니다](/graph/api/trustframeworkpolicy-get)
- [보안 프레임워크 정책을 업데이트 또는 만듭니다.](/graph/api/trustframework-put-trustframeworkpolicy)
- [기존 보안 프레임 워크 정책을 삭제합니다](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>정책 키

Identity Experience Framework는 사용자 지정 정책에서 참조되는 암호를 저장하여 구성 요소 간에 트러스트를 설정합니다. 이러한 비밀은 대칭 또는 비대칭 키/값이 될 수 있습니다. Azure Portal에서 이러한 엔터티는 **정책 키** 로 표시됩니다.

Microsoft Graph API의 정책 키에 대한 최상위 리소스는 [신뢰할 수 있는 프레임 워크 키 집합](/graph/api/resources/trustframeworkkeyset)입니다. 각각의 **키 집합** 에는 하나 이상의 **키** 가 포함되어 있습니다. 키를 만들려면 먼저 빈 키 집합을 만든 다음 키 집합에 키를 생성합니다. 수동 암호를 만들거나 인증서 또는 PKCS12 키를 업로드할 수 있습니다. 키는 생성된 암호, 문자열(예: Facebook 애플리케이션 암호) 또는 업로드한 인증서일 수 있습니다. 키 집합에 여러 키가 있는 경우 키 중 하나만 활성화됩니다.

### <a name="trust-framework-policy-keyset"></a>보안 프레임워크 정책 키 집합

- [보안 프레임 워크 키 집합 나열](/graph/api/trustframework-list-keysets)
- [보안 프레임 워크 키 집합 만들기](/graph/api/trustframework-post-keysets)
- [키 집합 가져오기](/graph/api/trustframeworkkeyset-get)
- [보안 프레임 워크 키 집합 업데이트](/graph/api/trustframeworkkeyset-update)
- [보안 프레임 워크 키 집합 삭제하기](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>보안 프레임워크 정책 키

- [키 집합에서 현재 활성 키 가져오기](/graph/api/trustframeworkkeyset-getactivekey)
- [키 집합에서 키 생성](/graph/api/trustframeworkkeyset-generatekey)
- [문자열 기반 비밀 업로드](/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 인증서 업로드](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 형식 인증서 업로드](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>애플리케이션

- [애플리케이션 나열](/graph/api/application-list)
- [애플리케이션 만들기](/graph/api/resources/application)
- [애플리케이션 업데이트](/graph/api/application-update)
- [ServicePrincipal 만들기](/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant 만들기](/graph/api/resources/oauth2permissiongrant)
- [애플리케이션 삭제](/graph/api/application-delete)

## <a name="application-extension-properties"></a>애플리케이션 확장 속성

- [확장 속성 나열](/graph/api/application-list-extensionproperty)
- [확장 속성 삭제](/graph/api/application-delete-extensionproperty)

Azure AD B2C는 사용자당 100개의 사용자 지정 특성을 보유할 수 있는 디렉터리를 제공합니다. 사용자 흐름의 경우 이러한 확장 속성은 [Azure Portal를 사용하여 관리됩니다](user-flow-custom-attributes.md). 사용자 지정 정책의 경우 정책에서 확장 속성에 값을 처음으로 쓸 때 Azure AD B2C에서 속성을 만듭니다.

## <a name="audit-logs"></a>감사 로그

- [감사 로그 나열](/graph/api/directoryaudit-list)

Azure AD B2C 감사 로그에 액세스하는 방법에 대한 자세한 내용은 [감사 로그 Azure AD B2C 액세스](view-audit-logs.md)를 참조하세요.

## <a name="conditional-access"></a>조건부 액세스

- [모든 조건부 액세스 정책을 나열합니다](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [조건부 액세스 정책의 속성 및 관계 읽기](/graph/api/conditionalaccesspolicy-get)
- [새 조건부 액세스 정책 만들기](/graph/api/resources/application)
- [조건부 액세스 정책 업데이트](/graph/api/conditionalaccesspolicy-update)
- [조건부 액세스 정책 삭제](/graph/api/conditionalaccesspolicy-delete)

## <a name="how-to-programmatically-manage-microsoft-graph"></a>프로그래밍 방식으로 Microsoft Graph를 관리하는 방법

Microsoft Graph를 관리하려는 경우 애플리케이션 권한을 사용하여 애플리케이션으로 수행하거나 위임된 권한을 사용할 수 있습니다. 위임된 권한을 사용하는 경우 사용자 또는 관리자가 앱이 요청하는 권한에 동의합니다. 이 앱은 대상 리소스를 호출할 때 로그인한 사용자 역할을 할 수 있는 권한을 위임받습니다. 애플리케이션 권한은 로그인한 사용자가 없어서 애플리케이션 권한이 필요한 앱에서 사용됩니다. 따라서 관리자만 애플리케이션 권한에 동의할 수 있습니다.

> [!NOTE]
> 사용자 흐름 또는 사용자 지정 정책을 통해 로그인하는 사용자의 위임된 권한은 Microsoft Graph의 위임된 권한에 대해 사용할 수 없습니다.
## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>코드 샘플: 프로그래밍 방식으로 사용자 계정을 관리하는 방법

이 코드 샘플은 [Microsoft Graph SDK](/graph/sdks/sdks-overview)를 사용하여 Microsoft Graph API와 상호 작용하는 .Net Core 콘솔 애플리케이션입니다. 이 코드에서는 API를 호출하여 Azure AD B2C 테넌트에서 사용자를 프로그래밍 방식으로 관리하는 방법을 보여줍니다.
(* .zip)인 [샘플 아카이브를 다운로드](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)하거나 GitHub에서 [리포지토리를 찾아보거나](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

코드 샘플을 가져온 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. [Visual Studio](https://visualstudio.microsoft.com) 또는 [Visual Studio Code](https://code.visualstudio.com)에서 프로젝트를 엽니다.
1. `src/appsettings.json`를 엽니다.
1. 섹션 `appSettings`에서 `your-b2c-tenant`를 테넌트의 이름으로 바꾸고, `Application (client) ID` 및 `Client secret`를 관리 애플리케이션 등록 값으로 바꿉니다. 자세한 내용은 [Microsoft Graph 애플리케이션 등록](microsoft-graph-get-started.md)을 참조하세요.
1. 리포지토리의 로컬 클론 내에서 콘솔 창을 열고 `src` 디렉터리로 전환한 다음 프로젝트를 빌드합니다.

    ```console
    cd src
    dotnet build
    ```
    
1. `dotnet` 명령을 사용하여 애플리케이션을 실행합니다.

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

애플리케이션은 실행할 수 있는 명령 목록을 표시합니다. 예를 들어 모든 사용자를 가져오고, 단일 사용자를 가져오고, 사용자를 삭제하고, 사용자 암호를 업데이트하고, 대량으로 가져오기를 합니다.

> [!NOTE]
> 애플리케이션에서 사용자 계정 암호를 업데이트하려면 애플리케이션에 [사용자 관리자 역할을 부여](microsoft-graph-get-started.md#optional-grant-user-administrator-role)해야 합니다.
 
### <a name="code-discussion"></a>코드 토론

샘플 코드는 [Microsoft Graph SDK](/graph/sdks/sdks-overview)를 사용하며, 이는 Microsoft Graph에 액세스하는 고품질의 효율적이고 복원력 있는 애플리케이션 구축을 간소화하도록 설계되었습니다.

Microsoft Graph API에 대한 요청은 인증을 위한 액세스 토큰이 필요합니다. 이 솔루션은 Microsoft Graph SDK에 사용할 MSAL(Microsoft 인증 라이브러리)의 인증 시나리오 기반 래퍼를 제공하는 [Microsoft Graph. Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet 패키지를 사용합니다.

_Program .cs_ 파일의 `RunAsync` 메서드는 다음과 같습니다.

1. _appsettings.js_ 파일에서 애플리케이션 설정을 읽습니다
1. [OAuth 2.0 클라이언트 자격 증명 부여](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 흐름을 사용하여 인증 공급자를 초기화합니다. 클라이언트 자격 증명 부여 흐름을 사용하여 앱은 Microsoft Graph API를 호출하는 액세스 토큰을 가져올 수 있습니다.
1. 인증 공급자를 사용하여 Microsoft Graph 서비스 클라이언트를 설정합니다.

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

그런 다음 초기화된 *GraphServiceClient* 이 사용자 관리 작업 수행을 위해 _UserService. cs_ 에서 사용됩니다. 예를 들어 테넌트의 사용자 계정 목록을 가져오는 것입니다.

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph SDK를 사용하여 API 호출을 수행하면](/graph/sdks/create-requests) Microsoft Graph에서 정보를 읽고 쓰는 방법, `$select`반환된 속성을 제어하기 위해 사용하는 방법, 사용자 지정 쿼리 매개 변수를 제공하고 사용하는 방법, `$filter` 및 `$orderBy` 쿼리 매개 변수를 사용하는 방법에 대한 정보가 포함됩니다.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: /graph/api/resources/user
