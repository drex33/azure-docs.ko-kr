---
title: .NET을 사용하여 컨테이너, 디렉터리 또는 Blob에 대한 사용자 위임 SAS 만들기
titleSuffix: Azure Storage
description: Azure Storage용 .NET 클라이언트 라이브러리를 사용하여 Azure Active Directory 자격 증명으로 사용자 위임 SAS를 만드는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: ebd9ba6beabe1a0bd3e6f158b90ca90510ea1b66
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857837"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>.NET을 사용하여 컨테이너, 디렉터리 또는 Blob에 대한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD(Azure Active Directory) 자격 증명을 사용하여 .NET 버전 12용 Azure Storage 클라이언트 라이브러리를 통해 컨테이너 또는 Blob에 대한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>데이터 액세스를 위한 Azure 역할 할당

Azure AD 보안 주체가 Blob 데이터에 액세스하려는 경우 해당 보안 주체에게 리소스에 대한 권한이 있어야 합니다. 보안 주체가 Azure의 관리 ID 또는 개발 환경에서 코드를 실행하는 Azure AD 사용자 계정이면 보안 주체는 Azure Storage의 Blob 데이터에 대한 액세스 권한을 부여하는 Azure 역할을 할당받아야 합니다. Azure RBAC를 통해 권한을 할당하는 방법에 관한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

Azure Storage에서 azure id 클라이언트 라이브러리를 사용 하 여 토큰을 가져오는 방법에 대 한 자세한 내용은 [azure id 라이브러리를 사용 하 여 권한 부여에 대 한 액세스 토큰 가져오기](../common/identity-library-acquire-token.md)를 참조 하세요.

## <a name="get-an-authenticated-token-credential"></a>인증된 토큰 자격 증명 가져오기

코드에서 Azure Storage 요청에 권한을 부여하는 데 사용할 수 있는 토큰 자격 증명을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. DefaultAzureCredential 클래스를 사용 하 여 Azure Storage에 대 한 액세스 권한을 관리 되는 id에 부여 하는 방법에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)를 참조 하세요.

다음 코드 조각에서는 인증된 토큰 자격 증명을 가져와서 이를 사용하여 Blob Storage용 서비스 클라이언트를 만드는 방법을 보여 줍니다.

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>사용자 위임 키 가져오기

모든 SAS는 키로 서명됩니다. 사용자 위임 SAS를 만들려면 먼저 SAS에 서명하는 데 사용되는 사용자 위임 키를 요청해야 합니다. 사용자 위임 키는 Azure AD 자격 증명을 사용한다는 점을 제외하고 서비스 SAS 또는 계정 SAS에 서명하는 데 사용되는 계정 키와 유사합니다. 클라이언트가 OAuth 2.0 토큰을 사용하여 사용자 위임 키를 요청하면 Azure Storage가 사용자를 대신하여 사용자 위임 키를 반환합니다.

사용자 위임 키를 받은 후에는 해당 키를 사용하여 키의 수명이 끝날 때까지 사용자 위임 공유 액세스 서명을 개수 제한 없이 만들 수 있습니다. 사용자 위임 키는 해당 키를 획득하는 데 사용된 OAuth 2.0 토큰과 별개이므로 키가 여전히 유효하다면 토큰을 갱신할 필요가 없습니다. 키의 유효 기간은 최대 7일로 지정할 수 있습니다.

다음 방법 중 하나를 사용하여 사용자 위임 키를 요청합니다.

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

다음 코드 조각은 사용자 위임 키를 가져오고 해당 속성을 작성합니다.

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Blob에 대한 사용자 위임 SAS 가져오기

다음 코드 예제에서는 보안 주체를 인증하고 Blob에 대한 사용자 위임 SAS를 만들기 위한 전체 코드를 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

다음 예제에서는 이전 예제에서 만든 사용자 위임 SAS를 시뮬레이션된 클라이언트 애플리케이션에서 테스트합니다. SAS가 유효한 경우 클라이언트 애플리케이션에서 Blob의 콘텐츠를 읽을 수 있습니다. SAS가 유효하지 않은 경우(예: 만료된 경우) Azure Storage에서 오류 코드 403(사용할 수 없음)을 반환합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>컨테이너에 대한 사용자 위임 SAS 가져오기

다음 코드 예제에서는 컨테이너에 대한 사용자 위임 SAS를 생성하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

다음 예제에서는 이전 예제에서 만든 사용자 위임 SAS를 시뮬레이션된 클라이언트 애플리케이션에서 테스트합니다. SAS가 유효한 경우 클라이언트 애플리케이션에서 Blob의 콘텐츠를 읽을 수 있습니다. SAS가 유효하지 않은 경우(예: 만료된 경우) Azure Storage에서 오류 코드 403(사용할 수 없음)을 반환합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>디렉터리에 대한 사용자 위임 SAS 가져오기

다음 코드 예제에서는 스토리지 계정에 계층 구조 네임스페이스가 사용되는 경우 디렉터리에 대한 사용자 위임 SAS를 생성하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

다음 예제에서는 이전 예제에서 만든 사용자 위임 SAS를 시뮬레이션된 클라이언트 애플리케이션에서 테스트합니다. SAS가 유효한 경우 클라이언트 애플리케이션에서 해당 디렉터리의 파일 경로를 나열할 수 있습니다. SAS가 유효하지 않은 경우(예: 만료된 경우) Azure Storage에서 오류 코드 403(사용할 수 없음)을 반환합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
- [사용자 위임 SAS 만들기(REST API)](/rest/api/storageservices/create-user-delegation-sas)
