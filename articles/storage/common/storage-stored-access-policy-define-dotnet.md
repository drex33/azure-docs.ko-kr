---
title: .NET을 사용하여 저장된 액세스 정책 만들기
titleSuffix: Azure Storage
description: Azure Storage 및 .NET을 사용하여 저장된 액세스 정책을 만듭니다. 서버의 서비스 수준 공유 액세스 서명에 대한 제어 수준을 높입니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/12/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1be2b8200cd9e7b83fb3d113966a13c32e126d34
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589267"
---
# <a name="create-a-stored-access-policy-with-net"></a>.NET을 사용하여 저장된 액세스 정책 만들기

저장된 액세스 정책은 서버 쪽의 서비스 수준 SAS(공유 액세스 서명)에 대한 제어 수준을 높입니다. 저장된 액세스 정책을 정의하면 공유 액세스 서명을 그룹화하고 정책으로 제한된 공유 액세스 서명을 추가로 제한하는 데 도움이 됩니다. 저장된 액세스 정책을 사용하여 SAS의 시작 시간, 만료 시간 또는 사용 권한을 변경하거나, 발급된 SAS를 철회할 수 있습니다.

다음 Azure Storage 리소스는 저장된 액세스 정책을 지원합니다.

- Blob 컨테이너
- 파일 공유
- 큐
- 테이블

> [!NOTE]
> 컨테이너에 대한 저장된 액세스 정책은 컨테이너 자체 또는 컨테이너에 포함된 Blob에 대한 사용 권한을 부여하는 공유 액세스 서명과 연결될 수 있습니다. 마찬가지로, 파일 공유에 대한 저장된 액세스 정책은 공유 자체 또는 공유에 포함된 파일에 대한 사용 권한을 부여하는 공유 액세스 서명과 연결될 수 있습니다.  
>
> 저장된 액세스 정책은 서비스 SAS에만 지원됩니다. 계정 SAS 또는 사용자 위임 SAS의 경우 저장된 액세스 정책이 지원되지 않습니다.

저장된 액세스 정책에 대한 자세한 내용은 [저장된 액세스 정책 정의](/rest/api/storageservices/define-stored-access-policy)를 참조하세요.

## <a name="create-a-stored-access-policy"></a>저장된 액세스 정책 만들기

저장된 액세스 정책을 만드는 기본 REST 작업은 [컨테이너 ACL 설정](/rest/api/storageservices/set-container-acl)입니다. 연결 문자열에서 계정 액세스 키를 사용하여 공유 키를 통해 저장된 액세스 정책을 만드는 작업 권한을 부여해야 합니다. Azure AD 자격 증명을 사용하여 **컨테이너 ACL 설정** 작업 권한을 부여할 수는 없습니다. 자세한 내용은 [데이터 작업 호출 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)을 참조하세요.

다음 코드 예제에서는 컨테이너에 대한 저장된 액세스 정책을 만듭니다. 액세스 정책을 사용하여 컨테이너나 해당 Blob에 서비스 SAS에 대한 제약 조건을 지정할 수 있습니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Azure Storage용 .NET 클라이언트 라이브러리 버전 12를 사용하여 컨테이너에 대한 저장된 액세스 정책을 만들려면 다음 메서드 중 하나를 호출합니다.

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

다음 예제에서는 1일 동안 유효하며 읽기/쓰기 권한을 부여하는 저장된 액세스 정책을 만듭니다.

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Azure Storage용 .NET 클라이언트 라이브러리 버전 12를 사용하여 컨테이너에 대한 저장된 액세스 정책을 만들려면 다음 메서드 중 하나를 호출합니다.

- [CloudBlobContainer.SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer.SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

다음 예제에서는 1일 동안 유효하며 읽기, 쓰기, 나열 권한을 부여하는 저장된 액세스 정책을 만듭니다.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>참고 항목

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
- [저장된 액세스 정책 정의](/rest/api/storageservices/define-stored-access-policy)
- [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)
