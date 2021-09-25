---
title: .NET을 사용하여 Blob에 대한 속성 및 메타데이터 관리 - Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정의 Blob에 시스템 속성을 설정 및 검색하고 사용자 지정 메타데이터를 저장하는 방법을 알아봅니다.
services: storage
author: normesta
ms.author: normesta
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e88c3bebe5cccaac2a750c4f5bd0b301226d0ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630758"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET을 사용하여 Blob 속성 및 메타데이터 관리

Blob은 포함된 데이터 외에도 시스템 속성 및 사용자 정의 메타데이터를 지원합니다. 이 문서에서는 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 사용하여 관리 시스템 속성 및 사용자 정의 메타데이터를 관리하는 방법을 보여줍니다.

## <a name="about-properties-and-metadata"></a>속성 및 메타데이터 정보

- **시스템 속성**: 각 Blob Storage 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. .NET 용 Azure Storage 클라이언트 라이브러리는 이러한 속성을 유지 관리합니다.

- **사용자 정의 메타데이터**: 사용자 정의 메타데이터는 Blob Storage 리소스에 지정하는 하나 이상의 이름-값 쌍으로 구성됩니다. 메타데이터를 사용하여 리소스와 함께 추가 값을 저장할 수 있습니다. 메타데이터 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

> [!NOTE]
> 또한 Blob 인덱스 태그는 Azure Blob Storage 리소스와 함께 임의의 사용자 정의 키/값 특성을 저장하는 기능을 제공합니다. 메타데이터와 비슷하지만 Blob 인덱스 태그만 자동으로 인덱싱되고 네이티브 Blob Service에서 검색할 수 있습니다. Azure Search와 같은 별도의 서비스를 활용하지 않으면 메타데이터를 인덱싱하고 쿼리할 수 없습니다.
>
> 이 기능에 대한 자세한 내용은 [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기(미리 보기)](storage-manage-find-blobs.md)를 참조하세요.

## <a name="set-and-retrieve-properties"></a>속성 설정 및 검색

다음 코드 예제는 `ContentType` 및 `ContentLanguage` 시스템 속성을 Blob에 설정합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Blob에서 속성을 설정하려면 [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) 또는 [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync)를 호출합니다. 명시적으로 설정되지 않은 속성은 모두 지워진 것입니다. 다음 코드 예제에서는 먼저 Blob의 기존 속성을 가져오고 이를 사용하여 업데이트되지 않는 헤더를 채웁니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

다음 코드 예제에서는 Blob의 시스템 속성을 가져오고 일부 값을 표시합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Blob Storage 리소스에 대한 메타데이터 및 속성 값 검색은 2단계 과정입니다. 이 값을 읽으려면 먼저 `FetchAttributes` 또는 `FetchAttributesAsync` 메서드를 호출하여 명시적으로 가져와야 합니다. 이 규칙의 예외는 `Exists` 및 `ExistsAsync` 메서드가 내부적으로 해당하는 `FetchAttributes` 메서드를 호출하는 것입니다. 이러한 메서드 중 하나를 호출하는 경우에도 `FetchAttributes`를 호출할 필요가 없습니다.

> [!IMPORTANT]
> 스토리지 리소스의 속성 또는 메타데이터 값이 채워지지 않은 경우에는 코드가 `FetchAttributes` 또는 `FetchAttributesAsync` 메서드를 호출하는지 확인합니다.

Blob 속성을 검색하려면 `FetchAttributes` 또는 `FetchAttributesAsync` 메서드를 Blob에 호출하여 `Properties` 속성을 채웁니다.

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

## <a name="set-and-retrieve-metadata"></a>메타데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타데이터를 설정하려면 리소스의 `Metadata` 컬렉션에 이름과 값의 쌍을 추가합니다. 그 후 다음 메서드 중 하나를 호출하여 값을 작성합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

메타데이터 이름/값 쌍은 유효한 HTTP 헤더이므로 HTTP 헤더와 관련된 모든 제한 사항을 준수해야 합니다. 메타데이터 이름은 유효한 HTTP 헤더 이름과 유효한 C# 식별자여야 하며, ASCII 문자만 포함할 수 있고, 대/소문자를 구분하지 않는 것으로 간주해야 합니다. [Base64-encode](/dotnet/api/system.convert.tobase64string) 또는 [URL-encode](/dotnet/api/system.web.httputility.urlencode) 메타데이터 값은 ASCII가 아닌 문자를 포함합니다.

메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다. 메타데이터 이름은 만들 때 사용되는 대/소문자를 유지하지만 설정하거나 읽을 때 대/소문자를 구분하지 않습니다. 동일한 이름을 사용하는 둘 이상의 메타데이터 헤더가 리소스에 제출되는 경우 Azure Blob Storage는 HTTP 오류 코드 400(잘못된 요청)을 반환합니다.

다음 코드 예제에서는 Blob에서 메타데이터를 설정합니다. 하나의 값은 컬렉션의 `Add` 메서드를 사용하여 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

다음 코드 예제에서는 Blob의 메타데이터를 읽습니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

메타데이터를 검색하려면 아래 예제에 나온 것처럼 Blob 또는 컨테이너에서 [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) 또는 [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) 메서드를 호출하여 [Metadata](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) 컬렉션을 채운 후에 값을 읽습니다. 이 **GetProperties** 메서드는 단일 호출에서 Blob 속성 및 메타데이터를 검색합니다. 이는 [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties) 및 [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)에 대한 별도의 호출이 필요한 REST API와 다릅니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

메타데이터를 검색하려면 아래 예제에 나온 것처럼 Blob 또는 컨테이너에서 `FetchAttributes` 또는 `FetchAttributesAsync` 메서드를 호출하여 `Metadata` 컬렉션을 채운 다음, 값을 읽습니다.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [Blob 속성 작업 설정](/rest/api/storageservices/set-blob-properties)
- [Blob 속성 작업 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 작업 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 메타데이터 작업 가져오기](/rest/api/storageservices/get-blob-metadata)
