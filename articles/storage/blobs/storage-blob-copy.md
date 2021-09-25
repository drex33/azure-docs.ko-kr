---
title: Azure Storage API를 사용하여 Blob 복사
description: Azure Storage 클라이언트 라이브러리를 사용하여 Blob을 복사하는 방법을 알아봅니다.
services: storage
author: normesta
ms.author: normesta
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 39969e5e1767bbd2a84a27bb5cbbea6d51c9a4eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674569"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Azure Storage 클라이언트 라이브러리를 사용하여 Blob 복사

이 문서에서는 Azure Storage 계정에서 Blob을 복사하는 방법을 보여줍니다. 비동기 복사 작업을 중단하는 방법도 보여줍니다. 예제 코드는 Azure Storage 클라이언트 라이브러리를 사용합니다.

## <a name="about-copying-blobs"></a>Blob 복사 정보

동일한 스토리지 계정 내에서 Blob을 복사하는 것은 동기 작업입니다. 계정 간에 복사하는 것은 비동기 작업입니다.

복사 작업의 원본 Blob은 블록 Blob 또는 추가 Blob, 페이지 Blob 또는 스냅샷일 수 있습니다. 대상 blob가 이미 있으면 원본 blob와 동일한 blob 유형이어야 합니다. 기존 대상 Blob을 덮어씁니다.

복사 작업이 진행 중일 때는 대상 Blob를 수정할 수 없습니다. 대상 Blob은 처리되지 않은 복사 작업을 하나만 포함할 수 있습니다. 즉, Blob은 보류 중인 여러 복사 작업의 대상일 수 없습니다.

전체 원본 Blob 또는 파일이 항상 복사됩니다. 바이트 범위 또는 블록 세트 복사는 지원되지 않습니다.

Blob을 복사하면 해당 시스템 속성이 동일 값으로 대상 Blob에 복사됩니다.

복사 작업은 다음 형식 중 하나를 사용할 수 있습니다.

- 다른 이름을 사용해서 원본 Blob을 대상 Blob에 복사합니다. 대상 Blob은 동일한 Blob 유형(블록, 추가 또는 페이지)의 기존 Blob이거나 복사 작업으로 생성된 새 Blob일 수 있습니다.
- 동일한 이름을 사용해 원본 Blob을 대상 Blob으로 복사하여 대상 Blob을 효과적으로 바꿀 수 있습니다. 이러한 복사 작업은 커밋되지 않은 블록을 제거하고 대상 Blob의 메타데이터를 덮어씁니다.
- Azure 파일 서비스의 원본 파일을 대상 Blob에 복사합니다. 대상 blob은 기존 블록 Blob이거나 복사 작업을 통해 만든 새 블록 Blob일 수 있습니다. 파일에서 페이지 Blob 또는 추가 Blob으로 복사하는 것은 지원되지 않습니다.
- 기본 Blob에 대해 스냅샷을 복사합니다. 스냅샷의 수준을 기본 Blob 위치로 올리면 이전 Blob 버전을 복원할 수 있습니다.
- 스냅샷을 다른 이름으로 대상 Blob에 복사합니다. 복사된 대상 Blob는 쓰기 가능한 Blob이고 스냅숏이 아닙니다.

## <a name="copy-a-blob"></a>blob 복사

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Blob을 복사하려면 다음 메서드 중 하나를 호출합니다.

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri` 및 `StartCopyFromUriAsync` 메서드는 복사 작업에 대한 정보가 포함된 [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) 개체를 반환합니다.

다음 코드 예제에서는 이전에 만든 Blob을 나타내는 [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)를 가져와 동일한 컨테이너의 새 Blob에 복사합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Blob을 복사하려면 다음 메서드 중 하나를 호출합니다.

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy` 및 `StartCopyAsync` 메서드는 상태를 확인하거나 복사 작업을 중단하는 데 사용되는 복사 ID 값을 반환합니다.

다음 코드 예제에서는 이전에 만든 Blob에 대한 참조를 가져와 동일한 컨테이너의 새 Blob에 복사합니다.

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

Blob을 복사하려면 [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) 메서드를 호출합니다. `start_copy_from_url` 메서드는 복사 작업에 대한 정보가 포함된 사전을 반환합니다.

다음 코드 예제에서는 이전에 만든 Blob을 나타내는 [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient)를 가져와 동일한 컨테이너의 새 Blob에 복사합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>복사 작업 중단

복사 작업을 중단하면 대상 Blob의 길이가 0이 됩니다. 하지만 대상 Blob의 메타데이터는 원본 Blob에서 복사되었거나 복사 작업 중에 명시적으로 설정된 새 값을 포함합니다. 복사 전의 원본 메타데이터를 보존하려면 복사 메서드 중 하나를 호출하기 전에 대상 Blob의 스냅샷을 만듭니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

대상 Blob의 [BlobProperties.CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) 속성을 확인하여 복사 작업의 상태를 가져옵니다. 복사가 완료되면 최종 blob가 커밋됩니다.

복사 작업을 중단하면 대상 Blob의 복사 상태가 [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus)로 설정됩니다.

[AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) 및 [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) 메서드는 진행 중인 복사 작업을 취소합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

대상 Blob의 [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) 속성을 확인하여 복사 작업의 상태를 가져옵니다. 복사가 완료되면 최종 blob가 커밋됩니다.

복사 작업을 중단하면 대상 Blob의 복사 상태가 [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus)로 설정됩니다.

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) 및 [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) 메서드는 진행 중인 복사 작업을 취소합니다.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) 메서드에서 반환한 [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) 사전의 "status" 항목을 확인하여 복사 작업의 상태를 가져옵니다. 복사가 완료되면 최종 blob가 커밋됩니다.

복사 작업을 중단하면 [상태](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties)가 "중단됨"으로 설정됩니다.

[abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) 메서드는 진행 중인 복사 작업을 취소합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK

Azure SDK에 대한 자세한 정보를 참조하세요.

- [Azure SDK for .NET](https://github.com/azure/azure-sdk-for-net)
- [Java용 Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Python용 Azure SDK](https://github.com/azure/azure-sdk-for-python)
- [JavaScript용 Azure SDK](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>다음 단계

다음 항목에서는 Blob을 복사하고 Azure REST API를 사용하여 진행 중인 복사 작업을 중단하는 방법에 대해 설명합니다.

- [Blob 복사](/rest/api/storageservices/copy-blob)
- [Blob 복사 중단](/rest/api/storageservices/abort-copy-blob)
