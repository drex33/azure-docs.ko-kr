---
title: .NET-Azure Storage를 사용하여 Blob Storage에 대한 요청에 고객이 제공한 키를 지정합니다.
description: .NET을 사용하여 Blob Storage에 대한 요청에 고객이 제공한 키를 지정하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b6f7001b9eabd34774930378d0656b4ad7ef3c25
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857800"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET을 사용하여 Blob Storage에 대한 요청에 고객이 제공한 키를 지정합니다.

Azure Blob Storage에 대한 요청을 수행하는 클라이언트에는 개별 요청에 대한 AES-256 암호화 키를 제공하는 옵션이 있습니다. 요청에 암호화 키를 포함하면 Blob Storage 작업에 대한 암호화 설정의 세부적인 제어 기능이 제공됩니다. 고객이 제공한 키는 Azure Key Vault 또는 다른 키 스토리지에 저장할 수 있습니다.

이 문서에서는 .NET을 사용하여 요청 시 고객이 제공한 키를 지정하는 방법을 보여 줍니다.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure ID 클라이언트 라이브러리로 인증하는 방법에 대한 자세한 내용은 [.NET용 Azure ID 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)를 참조하세요.

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>고객이 제공한 키를 사용하여 Blob에 쓰기

다음 예에서는 Blob Storage용 v12 클라이언트 라이브러리를 사용하여 Blob을 업로드할 때 AES-256 키를 제공합니다. 이 예에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 개체를 사용하여 Azure AD에서 쓰기 요청에 권한을 부여하지만 공유 키 자격 증명으로 요청에 권한을 부여할 수도 있습니다. DefaultAzureCredential 클래스를 사용 하 여 Azure Storage에 대 한 액세스 권한을 관리 되는 id에 부여 하는 방법에 대 한 자세한 내용은 [.net 용 Azure id 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme)를 참조 하세요.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Blob Storage에 요청에 대한 암호화 키 제공](encryption-customer-provided-keys.md)
- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
