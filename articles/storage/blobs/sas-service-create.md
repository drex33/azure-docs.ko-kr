---
title: 컨테이너 또는 Blob에 대한 서비스 SAS 만들기
titleSuffix: Azure Storage
description: Azure Blob Storage 클라이언트 라이브러리를 사용하여 컨테이너 또는 Blob에 대한 SAS(서비스 공유 액세스 서명)를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e2674a3e9227f5cf5cf2a6ebff0a317f4377f3a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606172"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>컨테이너 또는 Blob에 대한 서비스 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 스토리지 계정 키를 사용하여 Blob Storage에 대한 Azure Storage 클라이언트 라이브러리를 통해 컨테이너 또는 Blob에 대한 서비스 SAS를 만드는 방법을 보여 줍니다.

## <a name="create-a-service-sas-for-a-blob-container"></a>컨테이너 또는 Blob에 대한 서비스 SAS 만들기

다음 코드 예는 컨테이너에 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장된 액세스 정책이 제공되지 않는 경우 코드는 컨테이너에서 임시 SAS를 만듭니다.

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

서비스 SAS는 계정 액세스 키로 서명됩니다. [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 클래스를 사용하여 SAS에 서명하는 데 사용되는 자격 증명을 만듭니다. 그런 다음 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 개체를 만들고 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)를 호출하여 SAS 토큰 문자열을 가져옵니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

컨테이너에 대한 서비스 SAS를 만들려면 [CloudBlobContainer.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) 메서드를 호출합니다.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container,
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

서비스 SAS는 계정 액세스 키로 서명됩니다. [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) 클래스를 사용하여 SAS에 서명하는 데 사용되는 자격 증명을 만듭니다. 그런 다음 SAS 토큰 문자열을 가져오는 데 필요한 매개 변수를 제공하는 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 함수를 호출합니다.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>Blob에 서비스 SAS 만들기

다음 코드 예는 Blob에 SAS를 만듭니다. 기존에 저장된 액세스 정책의 이름을 제공하는 경우 해당 정책은 SAS와 연결됩니다. 저장된 액세스 정책이 제공되지 않는 경우 코드는 Blob에서 임시 SAS를 만듭니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

서비스 SAS는 계정 액세스 키로 서명됩니다. [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 클래스를 사용하여 SAS에 서명하는 데 사용되는 자격 증명을 만듭니다. 그런 다음 새 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 개체를 만들고 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)를 호출하여 SAS 토큰 문자열을 가져옵니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

Blob에 대한 서비스 SAS를 만들려면 [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 메서드를 호출합니다.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12-sdk"></a>[JavaScript v12 SDK](#tab/javascript)

Blob에 대한 서비스 SAS를 만들려면 [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) 메서드를 호출합니다.

Blob에 대한 서비스 SAS를 만들려면 필요한 매개 변수를 제공하는 [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) 함수를 호출합니다.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>디렉터리에 대한 서비스 SAS 만들기

계층적 네임스페이스를 사용하도록 설정된 스토리지 계정에서 디렉터리에 대한 서비스 SAS를 만들 수 있습니다. 서비스 SAS를 만들려면 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 패키지의 버전 12.5.0 이상을 설치했는지 확인합니다.

다음 예에서는 .NET용 v12 클라이언트 라이브러리를 사용하여 디렉터리에 대한 서비스 SAS를 만드는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)
- [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas)
