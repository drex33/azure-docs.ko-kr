---
title: .NET에서 Blob 스냅샷 만들기 및 관리
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Blob의 읽기 전용 스냅샷을 만들어 특정 시점에 Blob 데이터를 백업하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a9a7c822d6a88d9108a3d933ac3b8321eb856f05
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855406"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>.NET에서 Blob 스냅샷 만들기 및 관리

스냅샷은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다. 이 문서에서는 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 사용하여 Blob 스냅샷을 만들고 관리하는 방법을 보여 줍니다.

Azure Storage의 Blob 스냅샷에 대한 자세한 내용은 [Blob 스냅샷](snapshots-overview.md)을 참조하세요.

## <a name="create-a-snapshot"></a>스냅샷 만들기

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

.NET용 Azure Storage 클라이언트 라이브러리 버전 12.x를 사용하여 블록 Blob의 스냅샷을 만들려면 다음 메서드 중 하나를 사용합니다.

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

다음 코드 예에서는 버전 12.x에서 스냅샷을 만드는 방법을 보여 줍니다. Azure AD 자격 증명을 사용하여 서비스에 대한 요청을 승인하려면 [Azure.Identity](https://www.nuget.org/packages/azure.identity) 라이브러리에 대한 참조를 포함합니다. [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스를 사용하여 관리 ID에 Azure Storage 액세스 권한을 부여하는 자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리를](/dotnet/api/overview/azure/identity-readme)참조하세요.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

.NET용 Azure Storage 클라이언트 라이브러리 버전 11.x를 사용하여 블록 Blob의 스냅샷을 만들려면 다음 메서드 중 하나를 사용합니다.

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

다음 코드 예에서는 버전 11.x에서 스냅샷을 만드는 방법을 보여 줍니다. 이 예제에서는 만들 때 스냅샷에 대한 추가 메타데이터를 지정합니다.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>스냅샷 삭제

Blob을 삭제하려면 먼저 해당 Blob의 스냅샷을 삭제해야 합니다. 스냅샷을 개별적으로 삭제하거나 원본 Blob를 삭제할 때 모든 스냅샷을 삭제되도록 지정할 수 있습니다. 스냅샷이 있는 Blob을 삭제하려고 하면 오류가 발생합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

.NET용 Azure Storage 클라이언트 라이브러리 버전 12.x를 사용하여 Blob 및 해당 스냅샷을 삭제하려면 다음 메서드 중 하나를 사용하고 [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) 열거형을 포함합니다.

- [삭제](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

다음 코드 예는 .NET에서 Blob 및 해당 스냅샷을 삭제하는 방법을 보여 줍니다. 여기서 `blobClient`는 [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) 유형의 개체입니다.

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

.NET용 Azure Storage 클라이언트 라이브러리 버전 11.x를 사용하여 Blob 및 해당 스냅샷을 삭제하려면 다음 Blob 삭제 메서드 중 하나를 사용하고 [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) 열거형을 포함합니다.

- [삭제](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

다음 코드 예에서는 .NET에서 Blob 및 해당 스냅샷을 삭제하는 방법을 보여 줍니다. 여기서 `blockBlob`은 [CloudBlockBlob][dotnet_CloudBlockBlob] 형식의 개체입니다.

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>다음 단계

- [Blob 스냅샷](snapshots-overview.md)
- [Blob 버전](versioning-overview.md)
- [Blob에 대한 일시 삭제](./soft-delete-blob-overview.md)