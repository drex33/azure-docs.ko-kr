---
title: 네이티브 응용 프로그램 또는 웹 응용 프로그램에서 blob 또는 큐 데이터에 대 한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory를 사용하여 클라이언트 애플리케이션 내에서 인증하고, OAuth 2.0 토큰을 획득하며, Azure Blob Storage 및 Queue Storage에 대한 요청을 승인합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 0eb9c51f112af25356023ea0483440cea1831e3e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856452"
---
# <a name="authorize-access-to-blob-or-queue-data-from-a-native-or-web-application"></a>네이티브 응용 프로그램 또는 웹 응용 프로그램에서 blob 또는 큐 데이터에 대 한 액세스 권한 부여

Azure Blob Storage 또는 Queue Storage에서 Azure AD(Azure Active Directory)를 사용하는 주요 이점은 사용자의 자격 증명을 코드에 저장할 필요가 없다는 점입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 애플리케이션을 실행하는 보안 주체(사용자, 그룹 또는 서비스 주체)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure Blob Storage 또는 Queue Storage에 대한 요청을 승인할 수 있습니다.

이 문서에서는 다운로드할 수 있는 샘플 애플리케이션을 사용하여 Microsoft ID 플랫폼 인증을 위해 네이티브 애플리케이션 또는 웹 애플리케이션을 구성하는 방법을 보여 줍니다. 샘플 애플리케이션에서는 .NET을 사용하지만 다른 언어는 비슷한 접근 방식을 사용합니다. Microsoft ID 플랫폼에 대한 자세한 내용은 [Microsoft ID 플랫폼 개요](../../active-directory/develop/v2-overview.md)를 참조하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

## <a name="about-the-sample-application"></a>샘플 애플리케이션 정보

샘플 애플리케이션은 로컬 개발 환경에서 Azure AD를 사용한 인증을 위해 샘플 웹 애플리케이션을 구성하는 방법을 보여 주는 종단 간 경험을 제공합니다. 샘플 애플리케이션을 보고 실행하려면 먼저 [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)에서 복제하거나 다운로드합니다. 그런 다음 문서에 설명된 단계에 따라 Azure 앱 등록을 구성하고 사용자 환경에 맞게 애플리케이션을 업데이트합니다.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD 보안 주체에 역할 할당

Azure Storage 애플리케이션에서 보안 주체를 인증하려면 먼저 해당 보안 주체에 대해 Azure RBAC(Azure 역할 기반 액세스 제어) 설정을 구성합니다. Azure Storage에서는 컨테이너 및 큐에 대한 사용 권한을 포함하는 기본 제공 역할을 정의합니다. Azure 역할이 보안 주체에게 할당되면 해당 보안 주체는 해당 리소스에 대한 액세스 권한이 부여됩니다. 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록

Azure AD를 사용하여 스토리지 리소스에 대한 액세스 권한을 부여하는 첫 번째 단계는 [Azure Portal](https://portal.azure.com)에서 Azure AD 테넌트에 클라이언트 애플리케이션을 등록하는 것입니다. 클라이언트 애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(*애플리케이션 ID* 라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. Azure Storage 애플리케이션을 등록하려면 [빠른 시작: Microsoft id 플랫폼에 애플리케이션 등록](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)에 표시된 단계를 따르세요.

다음 이미지는 웹 애플리케이션 등록을 위한 일반 설정을 보여 줍니다. 이 예제에서 리디렉션 URI는 개발 환경에서 샘플 애플리케이션을 테스트하기 위해 `http://localhost:5000/signin-oidc`(으)로 설정됩니다. 나중에 Azure Portal에서 등록된 애플리케이션의 **인증** 설정에서 이 설정을 수정할 수 있습니다.

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Azure AD에서 스토리지 애플리케이션을 등록하는 방법을 보여 주는 스크린샷":::

> [!NOTE]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 **리디렉션 URI** 에 유효한 URI를 지정할 수 있습니다. 네이티브 애플리케이션의 경우 이 값은 실제 URL일 필요가 없습니다. 웹 애플리케이션의 경우 리디렉션 URI는 토큰이 제공되는 URL을 지정하므로 유효한 URI여야 합니다.

애플리케이션을 등록한 후에 **설정** 아래에 애플리케이션 ID(또는 클라이언트 ID)가 표시됩니다.

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="클라이언트 ID를 보여주는 스크린샷":::

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-register-app.md)을 참조하세요.

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Azure Storage에 등록된 앱 사용 권한 부여

다음으로 Azure Storage API를 호출하여 애플리케이션 사용 권한을 부여합니다. 이 단계에서는 애플리케이션에서 Azure AD를 사용하여 Azure Storage에 대한 요청을 승인할 수 있습니다.

1. 등록된 애플리케이션에 대한 **API 권한** 페이지에서 **사용 권한 추가** 를 선택합니다.
1. **Microsoft API** 탭에서 **Azure Storage** 를 선택합니다.
1. **API 권한 요청** 창의 **애플리케이션에 필요한 사용 권한 유형** 아래에서 사용 가능한 권한 유형이 **위임된 권한** 인지 확인합니다. 이 옵션은 기본적으로 선택되어 있습니다.
1. **사용 권한** 아래에서 **user_impersonation** 옆의 확인란을 선택하여 **사용 권한 추가** 단추를 선택합니다.

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="스토리지 API에 대한 사용 권한을 보여주는 스크린샷":::

1. 그런 다음 **기본 디렉터리에 대한 관리자 동의 부여** 를 클릭하여 이러한 권한에 대한 관리자 동의를 부여합니다.

이제 **API 권한** 창에 등록된 Azure AD 애플리케이션이 Microsoft Graph 및 Azure Storage API 모두에 액세스할 수 있으며 기본 디렉터리에 대한 동의가 부여되었음을 보여 줍니다. Azure AD에 앱을 처음 등록할 때 자동으로 Microsoft Graph에 대한 사용 권한이 부여됩니다.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="등록된 앱에 대한 API 권한을 보여 주는 스크린샷":::

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 보안상의 이유로 Microsoft는 24개월보다 긴 클라이언트 암호 생성을 제한하며 12개월 미만의 값으로 설정하는 것이 좋습니다. 클라이언트 암호를 추가하려면 다음 단계를 수행합니다.

1. Azure Portal에서 앱 등록으로 이동합니다.
1. **인증서 및 암호** 설정을 선택합니다.
1. **클라이언트 암호** 아래에서 **새 클라이언트 암호** 를 클릭하여 새 암호를 생성합니다.
1. 암호에 대한 설명을 제공하고 원하는 만료 간격을 선택합니다.
1. 새 암호의 값을 안전한 위치에 즉시 복사합니다. 전체 값은 한 번만 표시됩니다.

    ![클라이언트 암호를 보여 주는 스크린샷](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>암시적 권한 부여 흐름 활성화

다음으로 애플리케이션에 대한 암시적 권한 부여 흐름을 구성합니다. 다음 단계를 수행합니다.

1. Azure Portal에서 앱 등록으로 이동합니다.
1. **관리** 섹션에서 **인증** 설정을 선택합니다.
1. **암시적 권한 부여** 섹션에서 다음 이미지와 같이 ID 토큰을 사용하도록 설정하는 확인란을 선택합니다.

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="암시적 권한 부여 흐름에 대한 설정을 활성화하는 방법을 보여 주는 스크린샷":::

## <a name="client-libraries-for-token-acquisition"></a>토큰 획득을 위한 클라이언트 라이브러리

애플리케이션을 등록하고 이 애플리케이션에 Azure Blob Storage 및 Queue Storage에서 데이터를 액세스할 수 있는 권한을 부여하면 보안 주체를 인증하고 OAuth 2.0 토큰을 획득하는 코드를 애플리케이션에 추가할 수 있습니다. 토큰을 인증하고 얻으려면 [Microsoft ID 플랫폼 인증 라이브러리](../../active-directory/develop/reference-v2-libraries.md) 또는 OpenID Connect 1.0을 지원하는 다른 오픈 소스 라이브러리 중 하나를 사용할 수 있습니다. 그러면 애플리케이션에서는 액세스 토큰을 사용하여 Azure Blob Storage 및 Queue Storage에 대한 요청에 권한을 부여할 수 있습니다.

토큰 획득이 지원되는 시나리오 목록은 [MSAL(Microsoft 인증 라이브러리)](../../active-directory/develop/msal-overview.md) 설명서의 [인증 흐름](../../active-directory/develop/msal-authentication-flows.md) 섹션을 참조하세요.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD를 사용하여 인증하기 위해 잘 알려진 값

Azure AD를 사용하여 보안 주체를 인증하려면 코드에서 잘 알려진 몇 가지 값을 포함해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Microsoft 퍼블릭 클라우드의 경우 기본 Azure AD 권한은 다음과 같습니다. 여기서 *tenant-id* 는 Active Directory 테넌트 ID(또는 디렉터리 ID)입니다.

`https://login.microsoftonline.com/<tenant-id>/`

테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 이를 디렉터리 ID라고도 합니다. 테넌트 ID를 검색하려면 Azure Portal에서 앱 등록을 위한 **개요** 페이지로 이동하고 여기에서 값을 복사합니다.

### <a name="azure-storage-resource-id"></a>Azure Storage 리소스 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 Azure AD에서 액세스 토큰을 가져오는 방법을 보여줍니다. 지정한 사용자를 인증한 다음, 블록 Blob을 만드는 요청에 권한을 부여하는 데 액세스 토큰이 사용됩니다. 이 샘플을 사용하려면 먼저 이전 섹션에 설명된 단계를 따릅니다.

토큰을 요청하려면 앱 등록에서 다음 값이 필요합니다.

- Azure AD 도메인의 이름. Azure Active Directory의 **개요** 페이지에서 이 값을 검색합니다.
- 테넌트(또는 디렉터리) ID. 앱 등록의 **개요** 페이지에서이 값을 검색합니다.
- 클라이언트(또는 애플리케이션) ID. 앱 등록의 **개요** 페이지에서 이 값을 검색합니다.
- 클라이언트 리디렉션 URI. 앱 등록을 위한 **인증** 설정에서 이 값을 검색합니다.
- 클라이언트 암호의 값. 이전에 복사한 위치에서 이 값을 검색합니다.

### <a name="create-a-storage-account-and-container"></a>스토리지 계정 및 컨테이너 만들기

코드 샘플을 실행하려면 Azure Active Directory와 동일한 구독 내에 스토리지 계정을 만듭니다. 그런 다음 해당 스토리지 계정 내에 컨테이너를 만듭니다. 샘플 코드는 이 컨테이너에 블록 Blob을 만듭니다.

그런 다음, 샘플 코드를 실행할 사용자 계정에 **Storage Blob 데이터 기여자** 역할을 명시적으로 할당합니다. Azure Portal에서 이 역할을 할당하는 방법은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> Azure Portal에서 데이터 액세스를 위한 계정 키를 사용할 수도 있으므로 사용자가 데이터 액세스 역할을 자신에게 할당하기 전에 Azure Portal을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md)을 참조하세요.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD를 사용하여 Blob Storage에 대한 액세스 권한을 부여하는 웹 애플리케이션 만들기

애플리케이션이 Azure Storage에 액세스하는 경우 사용자를 대신하여 이를 수행합니다. 즉, 로그인한 사용자의 권한을 사용하여 Blob 또는 큐 리소스에 액세스합니다. 이 코드 예제를 사용하려면 사용자에게 Azure AD ID를 사용하여 로그인하라는 메시지를 표시하는 웹 애플리케이션이 필요합니다. 직접 프로그램을 만들거나 Microsoft에서 제공하는 샘플 애플리케이션을 사용할 수 있습니다.

토큰을 획득하고 이를 사용하여 Azure Storage에서 Blob을 만드는 완성된 샘플 웹 애플리케이션은 [GitHub](https://aka.ms/aadstorage)에서 사용할 수 있습니다. 완성된 샘플을 검토하고 실행하면 코드 예제를 해석하는 데 도움이 될 수 있습니다. 완성된 샘플을 실행하는 방법에 대한 지침은 [완료된 샘플 보기 및 실행](#view-and-run-the-completed-sample) 섹션을 참조하세요.

#### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용

Visual Studio에서 Azure Storage Client 클라이언트 라이브러리를 설치합니다. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔** 을 차례로 선택합니다. 콘솔 창에 다음 명령을 입력하여 .NET용 Azure Storage 클라이언트 라이브러리에서 필요한 패키지를 설치합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

그런 다음 문을 사용하여 HomeController.cs 파일에 다음을 추가합니다.

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

그런 다음 문을 사용하여 HomeController.cs 파일에 다음을 추가합니다.

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>블록 Blob 만들기

다음 코드 조각을 추가하여 블록 Blob을 만듭니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> OAuth 2.0 토큰을 사용하여 Blob 및 큐 작업에 권한을 부여하려면 HTTPS를 사용해야 합니다.

위의 예제에서 .NET 클라이언트 라이브러리는 블록 Blob 만들기 요청의 권한 부여를 처리합니다. 다른 언어를 위한 Azure Storage 클라이언트 라이브러리도 요청에 대한 권한 부여를 처리합니다. 그러나 REST API를 사용하여 OAuth 토큰을 통해 Azure Storage 작업을 호출하는 경우 OAuth 토큰을 사용하여 **권한 부여** 헤더를 구성해야 합니다.

OAuth 액세스 토큰을 사용하여 Blob 및 큐 서비스 작업을 호출하려면, **Bearer**(전달자) 체계를 사용하여 **Authorization**(권한 부여) 헤더에 액세스 토큰을 전달하고 2017-11-09 이상의 서비스 버전을 지정합니다.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Azure AD에서 액세스 토큰 가져오기

다음으로, 사용자를 대신하여 Azure AD에서 토큰을 요청하는 메서드를 추가합니다. 이 메서드는 권한이 부여되는 범위를 정의합니다. 권한 및 범위에 대한 자세한 내용은 [Microsoft ID 플랫폼 엔드포인트의 권한 및 동의](../../active-directory/develop/v2-permissions-and-consent.md)를 참조하세요.

리소스 ID를 사용하여 토큰을 가져올 범위를 구성합니다. 이 예제는 기본 제공 `user_impersonation` 범위와 함께 리소스 ID를 사용하여 범위를 구성합니다. 이는 사용자를 대신하여 토큰이 요청되고 있음을 나타냅니다.

사용자가 토큰을 대신 요청할 수 있는 인터페이스를 사용자에게 제공해야 할 수도 있습니다.

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

동의란 사용자가 자신을 대신해 보호되는 리소스에 액세스하기 위해 애플리케이션에 권한을 부여하는 프로세스를 말합니다. Microsoft ID 플랫폼은 증분 동의를 지원합니다. 즉, 보안 주체는 초기에 최소 권한 집합을 요청할 수 있으며 시간이 지남에 따라 필요할 때 권한을 추가할 수 있습니다. 코드에서 액세스 토큰을 요청할 때 앱에 필요한 권한 범위를 지정합니다. 증분 동의에 대한 자세한 내용은 [증분 및 동적 동의](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)를 참조하세요.

## <a name="view-and-run-the-completed-sample"></a>완성된 샘플 보기 및 실행

샘플 애플리케이션을 실행하려면 먼저 [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)에서 복제하거나 다운로드합니다. 그런 다음, 다음 섹션에 설명된 대로 애플리케이션을 업데이트합니다.

### <a name="provide-values-in-the-settings-file"></a>설정 파일에 값 제공

다음과 같이 고유한 값으로 *appsettings.json* 파일을 업데이트합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>",
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>스토리지 계정 및 컨테이너 이름 업데이트

*HomeController.cs* 파일에서 블록 Blob을 참조하는 URI를 업데이트하여 스토리지 계정 및 컨테이너의 이름을 사용하고 꺾쇠 괄호의 값을 고유한 값으로 바꿉니다.

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>다음 단계

- [Microsoft ID 플랫폼](../../active-directory/develop/index.yml)
- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)
- [Azure 리소스에 대한 관리 ID를 사용하여 Blob 데이터에 대한 액세스 권한 부여](../blobs/authorize-managed-identity.md)
