---
title: Event Grid 원본으로서의 Azure Blob Storage
description: Azure Event Grid를 사용하여 Blob Storage 이벤트에 제공되는 속성을 설명합니다.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: fcd8ff40e8a31c3329f6526a488a6a6a5261383e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797632"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Event Grid 원본으로서의 Azure Blob Storage

이 문서에서는 Blob Storage 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 이벤트 원본으로서 Azure Blob Storage를 사용하기 위한 빠른 시작 및 자습서의 목록을 제공합니다.


>[!NOTE]
> **StorageV2(범용 v2)** , **BlockBlobStorage**, **BlobStorage** 종류의 스토리지 계정만 이벤트 통합을 지원합니다. **스토리지(범용 v1)** 는 Event Grid와의 통합을 지원하지 *않습니다*.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

### <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API에 대한 이벤트 목록

해당 이벤트는 클라이언트가 Blob REST API를 호출하여 Blob을 만들거나 바꾸거나 또는 삭제할 때 실행됩니다.

> [!NOTE]
> `$logs`과 `$blobchangefeed` 컨테이너는 Event Grid와 통합되지 않으므로 해당 컨테이너의 작업은 이벤트를 생성하지 않습니다. 또한 *`(abfss://URI) `* 비계층 구조 네임스페이스 사용 계정에서 DFS 엔드포인트를 사용하면 이벤트가 생성되지 않지만, Blob 엔드포인트는 *`(wasb:// URI)`* 이벤트를 생성합니다.

 |이벤트 이름 |Description|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Blob 생성 또는 교체 시 트리거됩니다. <br>특히 이 이벤트는 클라이언트가 Blob REST API에서 사용할 수 있는 `PutBlob`, `PutBlockList` 또는 `CopyBlob` 작업을 사용할 때, **그리고** 블록 Blob이 완전히 커밋될 때 트리거됩니다. <br>**계층 구조 네임스페이스** 기능이 사용되는 계정에서 클라이언트가 `CopyBlob` 작업을 사용하는 경우, `CopyBlob` 작업은 약간 다르게 작동합니다. 이 경우 **Microsoft.Storage.BlobCreated** 이벤트는 블록 Blob이 완전히 커밋될 때가 아니라 `CopyBlob` 작업이 **시작** 될 때 트리거됩니다.   |
 |**Microsoft.Storage.BlobDeleted** |Blob 삭제 시 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `DeleteBlob` Blob REST API에서 사용할 수 있는 작업을 호출하는 경우 트리거됩니다. |
 |**Microsoft.Storage.BlobTierChanged** |Blob 액세스 계층이 변경될 때 트리거됩니다. 특히 클라이언트가 Blob REST API에서 사용할 수 있는 `Set Blob Tier` 작업을 호출하면 이 이벤트는 계층 변경이 완료된 후에 트리거됩니다. |
|**Microsoft. Storage. AsyncOperationInitiated** |보관 계층에서 핫 또는 쿨 계층으로 데이터를 이동하거나 복사하는 작업이 시작될 때 트리거됩니다. 특히 클라이언트가 `Set Blob Tier` API를 호출하여 Blob을 보관 계층에서 핫 또는 쿨 계층으로 이동하거나 클라이언트가 `Copy Blob` API를 호출하여 보관 계층의 Blob에서 핫 또는 쿨 계층의 Blob으로 데이터를 복사할 때 이 이벤트가 트리거됩니다.|

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST API에 대한 이벤트 목록

해당 이벤트는 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정하며, 클라이언트는 Azure Data Lake Storage Gen2 REST API 사용 시 실행됩니다. Azure Data Lake Storage Gen2에 대한 자세한 사항은 [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)를 참조하세요.

|이벤트 이름|Description|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Blob 생성 또는 교체 시 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 Azure Data Lake Storage Gen2 REST API에서 이용 가능한 `CreateFile`과 `FlushWithClose` 작업 사용 시 트리거됩니다. |
|**Microsoft.Storage.BlobDeleted** |Blob 삭제 시 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `DeleteFile` Azure Data Lake Storage Gen2 REST API에서 이용 가능한 작업을 호출하는 경우에도 트리거됩니다. |
|**Microsoft.Storage.BlobRenamed**|Blob의 이름을 바꾸면 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `RenameFile`Azure Data Lake Storage Gen2 REST API에서 이용 가능한 작업 사용 시 트리거됩니다.|
|**Microsoft.Storage.DirectoryCreated**|디렉터리를 만들면 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `CreateDirectory`Azure Data Lake Storage Gen2 REST API에서 이용 가능한 작업 사용 시 트리거됩니다.|
|**Microsoft.Storage.DirectoryRenamed**|디렉터리의 이름을 바꾸면 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `RenameDirectory`Azure Data Lake Storage Gen2 REST API에서 이용 가능한 작업 사용 시 트리거됩니다.|
|**Microsoft.Storage.DirectoryDeleted**|디렉터리를 삭제하면 트리거됩니다. <br>특히 해당 이벤트는 클라이언트가 `DeleteDirectory`Azure Data Lake Storage Gen2 REST API에서 이용 가능한 작업 사용 시 트리거됩니다.|

> [!NOTE]
> **Azure Data Lake Storage Gen2** 는 블록 Blob이 완전히 커밋되었을 때만 **Microsoft.Storage.BlobCreated** 이벤트가 트리거되는지 확인하려면 `FlushWithClose` REST API 호출에 대한 이벤트를 필터링합니다. 해당 API 호출은 데이터가 블록 Blob에 완전히 커밋된 후에만 **Microsoft.Storage.BlobCreated** 이벤트를 트리거합니다. 필터를 만드는 방법은 [Event Grid에 대한 필터 이벤트](./how-to-filter-events.md)를 참조하세요.

## <a name="example-event"></a>예제 이벤트
이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다. 이 섹션에는 각 Blob Storage 이벤트에서 데이터가 표시되는 방법에 대한 예제가 포함되어 있습니다.

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated 이벤트(Data Lake Storage Gen2)

Blob Storage 계정에 계층 구조 네임스페이스가 있는 경우, 데이터는 해당 변경 내용을 제외하고 이전 예제와 유사하게 표시됩니다.

* `dataVersion` 키가 `2` 값으로 설정됩니다.

* `data.api` 키가 `CreateFile` 또는 `FlushWithClose` 문자열로 설정됩니다.

* `contentOffset` 키는 해당 데이터 세트에 포함됩니다.

> [!NOTE]
> 애플리케이션에서 `PutBlockList` 작업을 사용하여 새 Blob을 계정에 업로드하는 경우, 데이터는 해당 변경 내용을 포함하지 않습니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted 이벤트(Data Lake Storage Gen2)

Blob Storage 계정에 계층 구조 네임스페이스가 있는 경우, 데이터는 해당 변경 내용을 제외하고 이전 예제와 유사하게 표시됩니다.

* `dataVersion` 키가 `2` 값으로 설정됩니다.

* `data.api` 키가 `DeleteFile` 문자열로 설정됩니다.

* `url` 키는 `dfs.core.windows.net` 경로를 포함합니다.

> [!NOTE]
> 애플리케이션이 `DeleteBlob` 작업을 사용하여 계정에서 Blob을 삭제할 경우, 데이터는 해당 변경 내용을 포함하지 않습니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobtierchanged-event"></a>Microsoft.Storage.BlobTierChanged 이벤트

```json
{
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
    "subject": "/blobServices/default/containers/testcontainer/blobs/Auto.jpg",
    "eventType": "Microsoft.Storage.BlobTierChanged",
    "id": "0fdefc06-b01e-0034-39f6-4016610696f6",
    "data": {
        "api": "SetBlobTier",
        "clientRequestId": "68be434c-1a0d-432f-9cd7-1db90bff83d7",
        "requestId": "0fdefc06-b01e-0034-39f6-401661000000",
        "contentType": "image/jpeg",
        "contentLength": 105891,
        "blobType": "BlockBlob",
        "url": "https://my-storage-account.blob.core.windows.net/testcontainer/Auto.jpg",
        "sequencer": "000000000000000000000000000089A4000000000018d6ea",
        "storageDiagnostics": {
            "batchId": "3418f7a9-7006-0014-00f6-406dc6000000"
        }
    },
    "dataVersion": "",
    "metadataVersion": "1",
    "eventTime": "2021-05-04T15:00:00.8350154Z"
}
```

### <a name="microsoftstorageasyncoperationinitiated-event"></a>Microsoft. Storage. AsyncOperationInitiated 이벤트

```json
{
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
    "subject": "/blobServices/default/containers/testcontainer/blobs/00000.avro",
    "eventType": "Microsoft.Storage.AsyncOperationInitiated",
    "id": "8ea4e3f2-101e-003d-5ff4-4053b2061016",
    "data": {
        "api": "SetBlobTier",
        "clientRequestId": "777fb4cd-f890-4c5b-b024-fb47300bae62",
        "requestId": "8ea4e3f2-101e-003d-5ff4-4053b2000000",
        "contentType": "application/octet-stream",
        "contentLength": 3660,
        "blobType": "BlockBlob",
        "url": "https://my-storage-account.blob.core.windows.net/testcontainer/00000.avro",
        "sequencer": "000000000000000000000000000089A4000000000018c6d7",
        "storageDiagnostics": {
            "batchId": "34128c8a-7006-0014-00f4-406dc6000000"
        }
    },
    "dataVersion": "",
    "metadataVersion": "1",
    "eventTime": "2021-05-04T14:44:59.3204652Z"
}
```


### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated 이벤트(Data Lake Storage Gen2)

Blob Storage 계정에 계층 구조 네임스페이스가 있는 경우, 데이터는 해당 변경 내용을 제외하고 이전 예제와 유사하게 표시됩니다.

* `data.api` 키가 `CreateFile` 또는 `FlushWithClose` 문자열로 설정됩니다.
* `contentOffset` 키는 해당 데이터 세트에 포함됩니다.

> [!NOTE]
> 애플리케이션에서 `PutBlockList` 작업을 사용하여 새 Blob을 계정에 업로드하는 경우, 데이터는 해당 변경 내용을 포함하지 않습니다.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "type": "Microsoft.Storage.BlobCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted 이벤트(Data Lake Storage Gen2)

Blob Storage 계정에 계층 구조 네임스페이스가 있는 경우, 데이터는 해당 변경 내용을 제외하고 이전 예제와 유사하게 표시됩니다.


* `data.api` 키가 `DeleteFile` 문자열로 설정됩니다.
* `url` 키는 `dfs.core.windows.net` 경로를 포함합니다.

> [!NOTE]
> 애플리케이션이 `DeleteBlob` 작업을 사용하여 계정에서 Blob을 삭제할 경우, 데이터는 해당 변경 내용을 포함하지 않습니다.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "type": "Microsoft.Storage.BlobDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "type": "Microsoft.Storage.BlobRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "type": "Microsoft.Storage.DirectoryCreated",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "type": "Microsoft.Storage.DirectoryRenamed",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted 이벤트

```json
[{
  "source": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "type": "Microsoft.Storage.DirectoryDeleted",
  "time": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "specversion": "1.0"
}]
```

---


## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `api` | 문자열 | 이벤트를 트리거하는 작업입니다. |
| `clientRequestId` | 문자열 | 스토리지 API 작업에 대한 클라이언트 제공 요청 ID입니다. 해당 ID는 로그의 ‘client-request-id’ 필드를 사용하여 Azure Storage 진단 로그와의 상관관계를 지정하는 데 사용할 수 있으며, ‘x-ms-client-request-id’ 헤더를 사용하여 클라이언트 요청에 제공할 수 있습니다. [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| `requestId` | 문자열 | 스토리지 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| `eTag` | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| `contentType` | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| `contentLength` | 정수 | Blob의 크기(바이트)입니다. |
| `blobType` | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| `contentOffset` | number | 이벤트 트리거 애플리케이션에서 파일에 쓰기를 완료한 시점에 수행된 쓰기 작업의 바이트 단위 오프셋입니다. <br>계층 구조 네임스페이스가 있는 Blob Storage 계정에서 활성화되는 이벤트에 대해서만 나타납니다.|
| `destinationUrl` |문자열 | 작업이 완료된 후 존재하는 파일의 URL입니다. 예를 들어, 파일의 이름을 바꾸면 `destinationUrl` 속성에 새 파일 이름의 URL이 포함됩니다. <br>계층 구조 네임스페이스가 있는 Blob Storage 계정에서 활성화되는 이벤트에 대해서만 나타납니다.|
| `sourceUrl` |문자열 | 작업을 완료하기 전에 존재하는 파일의 URL입니다. 예를 들어, 파일의 이름을 바꾸면 `sourceUrl`은 이름 바꾸기 작업 전 원래 파일 이름의 URL을 포함합니다. <br>계층 구조 네임스페이스가 있는 Blob Storage 계정에서 활성화되는 이벤트에 대해서만 나타납니다. |
| `url` | 문자열 | Blob에 대한 경로입니다. <br>클라이언트가 Blob REST API를 사용할 경우 URL의 구조는 다음과 같습니다`<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>`. <br>클라이언트가 Data Lake Storage REST API를 사용할 경우 URL의 구조는 다음과 같습니다`<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>`. |
| `recursive` | 문자열 | 모든 자식 디렉터리에서 작업을 실행하려면 `True`, 그렇지 않으면 `False`입니다. <br>계층 구조 네임스페이스가 있는 Blob Storage 계정에서 활성화되는 이벤트에 대해서만 나타납니다. |
| `sequencer` | 문자열 | 특정 Blob 이름에 대한 이벤트의 논리적 순서를 나타내는 불투명 문자열 값입니다.  사용자는 표준 문자열 비교를 사용하여 동일한 Blob 이름에 대한 두 이벤트의 상대적 순서를 이해할 수 있습니다. |
| `storageDiagnostics` | object | 경우에 따라 Azure Storage 서비스에 의해 포함되는 진단 데이터입니다. 포함될 경우, 이벤트 소비자는 무시해야 합니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |설명  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: PowerShell을 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure PowerShell을 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: Azure Portal을 사용하여 Blob Storage 이벤트 만들기 및 라우팅](blob-event-quickstart-portal.md) | 포털를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [Azure CLI: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-cli-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [PowerShell: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-powershell-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [Resource Manager 템플릿: Blob Storage 및 구독 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-subscription-and-storage) | Azure Blob Storage 계정을 배포하고 해당 스토리지 계정에 대한 이벤트를 구독합니다. WebHook에 이벤트를 보냅니다. |
| [개요: Blob Storage 이벤트에 대응](../storage/blobs/storage-blob-event-overview.md) | Event Grid와 Blob Storage 통합의 개요입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Blob Storage 이벤트를 사용하는 방법에 대한 소개는 [Blob Storage 이벤트 라우팅 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 참조하세요. 
