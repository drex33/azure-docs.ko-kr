---
title: Blob Storage에서 동시성 관리
titleSuffix: Azure Storage
description: 애플리케이션에 낙관적 또는 비관적 동시성을 구현하여 Blob의 여러 작성자를 관리하는 방법에 대해 알아봅니다. 낙관적 동시성은 Blob의 ETag 값을 확인하여 제공된 ETag와 비교합니다. 비관적 동시성은 배타적 임대를 사용하여 다른 작성자에게는 Blob을 잠급니다.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 20f6e4b02e75686d98456a97490ef261ee929a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600307"
---
# <a name="managing-concurrency-in-blob-storage"></a>Blob Storage에서 동시성 관리

최신 애플리케이션에서는 여러 사용자가 데이터를 동시에 보고 업데이트하는 경우가 종종 있습니다. 애플리케이션 개발자는 최종 사용자에게 예측 가능한 환경을 제공하는 방법, 특히 여러 사용자가 같은 데이터를 업데이트할 수 있는 시나리오를 신중하게 고려해야 합니다. 개발자가 일반적으로 고려하는 주요 데이터 동시성 전략에는 다음의 세 가지가 있습니다.

- **낙관적 동시성**: 업데이트를 수행하는 애플리케이션이 업데이트의 일환으로 애플리케이션이 데이터를 마지막으로 읽은 이후 해당 데이터가 변경되었는지를 판단합니다. 예를 들어 wiki 페이지를 보는 두 사용자가 해당 페이지를 업데이트하면 wiki 플랫폼은 두 번째 업데이트가 첫 번째 업데이트를 덮어쓰지 않도록 해야 합니다. 또한 두 사용자가 업데이트의 성공 여부를 파악하도록 해야 합니다. 이 전략은 웹 애플리케이션에서 가장 흔히 사용됩니다.

- **비관적 동시성**: 업데이트를 수행하려는 애플리케이션이 개체를 잠가서 다른 사용자가 잠금이 해제될 때까지 데이터를 업데이트하지 못하도록 합니다. 예를 들어 기본만 업데이트를 수행하는 기본/보조 데이터 복제 시나리오에서는 다른 사람이 데이터를 업데이트할 수 없도록 대개 기본이 장시간 데이터에 대해 배타적 잠금을 적용합니다.

- **마지막 작성자의 업데이트 적용**: 데이터를 읽은 후 다른 애플리케이션의 데이터 업데이트 여부를 먼저 판단하지 않고 업데이트 작업을 진행하도록 허용하는 방식입니다. 이 방법은 일반적으로 여러 사용자가 동시에 동일한 데이터에 액세스하지 않는 방식으로 데이터가 분할될 때 사용됩니다. 일시적인 데이터 스트림을 처리하는 경우에도 이 전략이 유용할 수 있습니다.

Azure Storage는 낙관적 및 비관적 동시성을 완벽하게 지원하는 기능이 특징이지만 세 가지 전략을 모두 지원합니다. Azure Storage는 서비스가 삽입 또는 업데이트 작업을 수행하고 나면 후속 읽기 작업에서 최신 업데이트를 반환하도록 보장하는 강력한 일관성 모델을 수용하도록 설계되었습니다.

개발자는 적절한 동시성 전략을 선택해야 할 뿐 아니라 스토리지 플랫폼이 변경 내용, 특히 여러 트랜잭션 간의 같은 개체에 대한 변경 내용을 격리하는 방법도 파악해야 합니다. Azure Storage 서비스는 스냅샷 격리를 사용하여 단일 파티션 내에서 읽기 작업과 쓰기 작업을 동시에 허용합니다. 스냅샷 격리를 통해 모든 읽기 작업은 업데이트가 발생하는 동안에도 데이터의 일관된 스냅샷을 반환합니다.

낙관적 동시성 모델이나 비관적 동시성 모델을 사용하여 Blob 및 컨테이너에 대한 액세스를 관리할 수 있습니다. 전략을 명시적으로 지정하지 않으면 기본적으로 마지막 작성자의 업데이트가 적용됩니다.

## <a name="optimistic-concurrency"></a>낙관적 동시성

Azure Storage는 저장되는 모든 개체에 식별자를 할당합니다. 이 식별자는 개체에서 쓰기 작업이 실행될 때마다 업데이트됩니다. 식별자는 HTTP 프로토콜에서 정의하는 ETag 헤더에 포함된 HTTP GET 응답의 일환으로 클라이언트로 반환됩니다.

업데이트를 수행하는 클라이언트는 조건부 헤더와 함께 본래 ETag를 전송하여 특정 조건이 충족된 경우에만 업데이트가 발생하도록 할 수 있습니다. 예를 들어, **If-Match** 헤더를 지정하는 경우 Azure Storage는 업데이트 요청에 지정된 ETag의 값이 업데이트되는 개체의 ETag와 동일한지 확인합니다. 조건부 헤더에 대한 자세한 내용은 [Blob 서비스 작업의 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)을 참조하세요.

이 프로세스는 대략적으로 다음과 같이 수행됩니다.

1. Azure Storage에서 Blob을 검색합니다. 응답에는 개체의 현재 버전을 식별하는 HTTP ETag 헤더 값이 포함됩니다.
1. Blob를 업데이트할 때는 작성자 요청의 **If-Match** 조건부 헤더에 1단계에서 받은 ETag 값을 포함합니다. Azure Storage가 요청의 ETag 값을 Blob의 현재 ETag 값과 비교합니다.
1. Blob의 현재 ETag 값이 요청에 제공된 **If-Match** 조건부 헤더에 지정된 ETag 값과 다른 경우 Azure Storage는 HTTP 상태 코드 412(전제 조건 실패)를 반환합니다. 클라이언트는 이 오류를 통해 Blob를 처음 검색한 후 다른 프로세스에서 Blob를 업데이트했음을 확인할 수 있습니다.
1. Blob의 현재 ETag 값이 요청의 **If-Match** 조건부 헤더에 포함된 ETag와 같은 버전이면 Azure Storage는 요청된 작업을 수행하고 Blob의 현재 ETag 값을 업데이트합니다.

다음 코드 예제에서는 Blob의 ETag 값을 확인하는 쓰기 요청에서 **If-Match** 조건을 생성하는 방법을 보여 줍니다. Azure Storage는 Blob의 현재 ETag가 요청에 제공된 ETag와 동일한지 여부를 평가하고 두 ETag 값이 일치하는 경우에만 쓰기 작업을 수행합니다. 그 사이 다른 프로세스가 Blob을 업데이트한 경우 Azure Storage는 HTTP 412(전제 조건 실패) 상태 메시지를 반환합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage는 **If-Modified-Since**, **If-Unmodified-Since**, **If-None-Match** 등 다른 조건부 헤더도 지원합니다. 자세한 내용은 [Blob 서비스 작업의 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)을 참조하세요.

## <a name="pessimistic-concurrency-for-blobs"></a>Blob에 대한 비관적 동시성

단독 사용을 위해 Blob를 잠그려는 경우 Blob에 대한 임대를 획득할 수 있습니다. 임대를 획득하는 경우 임대 기간을 지정합니다. 유한 임대는 15 ~ 60 초 범위에서 유효합니다. 무제한 임대도 가능하며, 이는 배타적 잠금에 해당합니다. 유한 임대는 갱신하여 연장할 수 있으며 완료된 임대는 해제할 수 있습니다. Azure Storage는 만료된 유한 임대를 자동으로 해제합니다.

임대를 사용하면 배타적 쓰기/공유 읽기 작업, 배타적 쓰기/배타적 읽기 작업, 공유 쓰기/배타적 읽기 작업 등의 다양한 동기화 전략을 지원할 수 있습니다. 임대가 있으면 Azure Storage는 임대 소유자에 대하여 쓰기 작업에 대한 배타적 액세스를 적용합니다. 그러나 읽기 작업에 대해 독점성을 보장하는 경우 개발자는 모든 클라이언트 애플리케이션에서 임대 ID를 사용하고 한 번에 한 클라이언트만 유효한 임대 ID를 사용하도록 해야 합니다. 임대 ID를 포함하지 않는 읽기 작업에서는 공유 읽기가 수행됩니다.

다음 코드 예제에서는 Blob에 대한 배타적 임대를 획득하고 임대 ID를 제공하여 Blob의 내용을 업데이트한 다음 임대를 해제하는 방법을 보여 줍니다. 임대가 활성 상태이고 임대 ID가 쓰기 요청에서 제공되지 않으면 오류 코드 412(전제 조건 실패)가 발생하면서 쓰기 작업이 실패합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>컨테이너에 대한 비관적 동시성

컨테이너에 대한 임대를 사용하면 배타적 쓰기/공유 읽기, 배타적 쓰기/배타적 읽기, 공유 쓰기/배타적 읽기 등 Blob에 지원되는 동일한 동기화 전략을 사용할 수 있습니다. 그러나 컨테이너의 경우 배타적 잠금은 삭제 작업에만 적용됩니다. 임대가 활성 상태인 컨테이너를 삭제하려면 클라이언트가 삭제 요청에 활성 임대 ID를 포함해야 합니다. 다른 모든 컨테이너 작업은 임대 ID가 없는 임대된 컨테이너에서 성공합니다.

## <a name="next-steps"></a>다음 단계

- [Blob 서비스 작업의 조건부 헤더 지정](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
- [컨테이너 임대](/rest/api/storageservices/lease-container)
- [Blob 임대](/rest/api/storageservices/lease-blob)
