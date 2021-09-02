---
title: Azure Cosmos DB 첨부 파일
description: 이 문서에서는 Azure Cosmos DB 첨부 파일의 개요를 제공합니다.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: d4fd96c9112d3ae9d1e2590d1ca99275c7eead07
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102497"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB 첨부 파일
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 첨부 파일은 외부 Blob 또는 미디어 파일과 연관된 메타데이터 및 참조서를 포함하는 특수 항목입니다.

Azure Cosmos DB는 두 가지 유형의 첨부 파일을 지원합니다.

* **관리되지 않는 첨부 파일** 은 외부 서비스(예: Azure Storage, OneDrive 등)에 저장된 Blob에 대한 URI 참조를 둘러싸는 래퍼입니다. 이 방법은 표준 Azure Cosmos DB 항목에 URI 속성을 저장하는 방법과 비슷합니다.
* **관리되는 첨부 파일** 은 Azure Cosmos DB에서 내부적으로 관리 및 저장되며 시스템 생성 mediaLink를 통해 노출되는 Blob입니다.


> [!NOTE]
> 첨부 파일은 레거시 기능입니다. 이 기능을 이미 사용 중인 경우 해당 기능은 지원 범위 내에서 계속 기능을 제공합니다.
> 
> 첨부 파일을 사용하는 대신 Blob 데이터를 저장하는 데 사용되는 용도의 Blob Storage 서비스로 Azure Blob Storage를 사용하는 것이 좋습니다. Blob과 관련된 메타데이터를 참조 URI 링크와 함께 Azure Cosmos DB에 항목 속성으로 계속 저장할 수 있습니다. Azure Cosmos DB에 이 데이터를 저장하면 Azure Blob Storage에 저장된 Blob에 대한 메타데이터 및 링크를 쿼리할 수 있습니다.
> 
> Microsoft는 첨부 파일을 완전히 사용 중단하기 전에 최소 36개월 전 통지를 제공하려고 최선을 다하고 있으며, 날짜는 추후에 발표할 예정입니다.

## <a name="known-limitations"></a>알려진 제한 사항

Azure Cosmos DB의 관리 첨부 파일은 표준 항목에 대한 지원과 다릅니다. 이는 무제한 확장성, 전역 배포 및 다른 Azure 서비스와의 통합을 제공하기 때문입니다.

- 첨부 파일은 Azure Cosmos DB SDK 모든 버전에서 지원되지 않습니다.
- 관리 첨부 파일은 데이터베이스 계정마다 2GB의 스토리지로 제한됩니다.
- 관리 첨부 파일은 Azure Cosmos DB의 전역 배포와 호환되지 않으며 지역 간에 복제되지 않습니다.

> [!NOTE]
> MongoDB 버전 3.2에 대한 Azure Cosmos DB API는 GridFS용 관리 첨부 파일을 활용하며 동일한 제한 사항이 적용됩니다.
>
> MongoDB GridFS 기능 집합을 사용하여 MongoDB 버전 3.6 이상의 API Azure Cosmos DB로 업그레이드하는 것이 좋습니다.업그레이드 시 첨부 파일에서 분리되며 더 나은 환경을 제공합니다. 또는 MongoDB GridFS 기능 집합을 사용하는 개발자는 Blob 콘텐츠 저장 용도로 만들어졌으며 GridFS에 비해 저렴한 비용으로 확장 기능을 제공하는 Azure Blob Storage를 사용하는 것을 고려해 봐도 좋습니다.

## <a name="migrating-attachments-to-azure-blob-storage"></a>첨부 파일을 Azure Blob Storage로 마이그레이션

다음 단계를 수행하여 Azure Cosmos DB 첨부 파일을 Azure Blob Storage로 마이그레이션하는 것이 좋습니다.

1. 원본 Azure Cosmos DB 컨테이너의 첨부 파일 데이터를 대상 Azure Blob Storage 컨테이너로 복사합니다.
2. 대상 Azure Blob Storage 컨테이너에서 업로드된 Blob 데이터의 유효성을 검사합니다.
3. 해당하는 경우 URI 참조를 Azure Cosmos DB 데이터 세트 내의 문자열 속성으로 Azure Blob Storage에 포함된 Blob에 추가합니다.
4. 새 Azure Blob Storage 컨테이너에서 Blob을 읽고 쓸 수 있도록 애플리케이션 코드를 리팩터링합니다.

다음 dotnet 코드 샘플은 Azure Cosmos DB의 .NET SDK v2 및 Azure Blob Storage .NET SDK v12를 사용하여 마이그레이션 흐름의 일부로 첨부 파일을 Azure Cosmos DB에서 Azure Blob Storage로 복사하는 방법을 보여 줍니다. 원본 Azure Cosmos DB 계정 및 대상 Azure Blob Storage 컨테이너를 위해 `<placeholder values>`을(를) 대체해야 합니다.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>다음 단계

- [Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md) 시작
- [Azure Cosmos DB의 .NET SDK v2](/dotnet/api/microsoft.azure.documents.attachment)를 통해 첨부 파일 사용에 대한 참조 가져오기
- [Azure Cosmos DB의 Java SDK v2](/java/api/com.microsoft.azure.documentdb.attachment)를 통해 첨부 파일 사용에 대한 참조 가져오기
- [Azure Cosmos DB의 REST API](/rest/api/cosmos-db/attachments)를 통해 첨부 파일 사용에 대한 참조 가져오기
