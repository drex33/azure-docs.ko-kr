---
title: Azure Synapse 데이터 탐색기에 대 한 Event Grid 데이터 연결 (미리 보기)
description: 이 문서에서는 Event Grid에서 Azure Synapse 데이터 탐색기로 데이터를 수집 (로드) 하는 방법에 대 한 개요를 제공 합니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 14b5129a29e7d09e86ebfd8874654e55be6f1cb6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715837"
---
# <a name="event-grid-data-connection-preview"></a>Event Grid 데이터 연결 (미리 보기)

Event Grid 수집은 Azure 스토리지를 수신 대기하고 구독됨 이벤트가 발생하면 정보를 끌어오도록 Azure Data Explorer를 업데이트하는 파이프라인입니다. 데이터 탐색기는 blob 생성 또는 blob 이름 변경 알림을 위한 [Azure Event Grid](../../../event-grid/overview.md) 구독과 함께 Azure Storage (blob Storage 및 ADLSv2)에서 지속적인 수집을 제공 하 고 이벤트 허브를 통해 이러한 알림을 데이터 탐색기로 스트리밍합니다.

Event Grid 수집 파이프라인은 여러 단계를 거칩니다. 데이터 탐색기에서 [특정 형식의 데이터](#data-format) 를 수집 대상 테이블을 만듭니다. 그런 다음 데이터 탐색기에서 Event Grid 데이터 연결을 만듭니다. Event Grid 데이터 연결에는 데이터를 보낼 테이블 및 테이블 매핑과 같은 [이벤트 라우팅](#events-routing) 정보가 필요합니다. 수집할 데이터, 대상 테이블 및 매핑을 설명하는 [수집 속성](#ingestion-properties)도 지정합니다. 샘플 데이터를 생성하고, [Blob을 업로드](#upload-blobs)하거나 [Blob 이름을 변경](#rename-blobs)하여 연결을 테스트할 수 있습니다. 수집 후에는 [Blob을 삭제](#delete-blobs-using-storage-lifecycle)합니다. 이 프로세스는 [Azure Portal](data-explorer-ingest-event-grid-portal.md)를 통해 관리할 수 있습니다. <!-- , using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md). -->

<!-- For general information about data ingestion in Data Explorer, see [Data Explorer data ingestion overview](ingest-data-overview.md). -->

## <a name="data-format"></a>데이터 형식

- [지원되는 형식](data-explorer-ingest-data-supported-formats.md)을 참조하세요.
- [지원되는 압축](data-explorer-ingest-data-supported-formats.md#supported-data-compression-formats)을 참조하세요.
    - 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다. 그렇지 않으면 데이터 탐색기이 값을 예측 합니다. 파일당 압축되지 않은 수집 크기 제한은 4GB입니다.

> [!NOTE]
> Event Grid 알림 구독은 `BlobStorage`, `StorageV2` 또는 [Data Lake Storage Gen2](../../../storage/blobs/data-lake-storage-introduction.md)용 Azure Storage 계정에서 설정할 수 있습니다.

## <a name="ingestion-properties"></a>수집 속성

Blob 메타데이터를 통해 Blob 수집의 [수집 속성](data-explorer-ingest-data-properties.md)을 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

[!INCLUDE [ingestion-properties-event-grid](../includes/data-explorer-event-grid-ingestion-properties.md)]

## <a name="events-routing"></a>이벤트 라우팅

데이터 탐색기 클러스터에 대 한 blob 저장소 연결을 설정 하는 경우 대상 테이블 속성을 지정 합니다.

- 테이블 이름
- 데이터 형식
- 매핑

이 설정은 데이터에 대한 기본 라우팅이며, `static routing`이라고도 하는 경우가 있습니다.
또한 Blob 메타데이터를 사용하여 각 Blob에 대한 대상 테이블 속성을 지정할 수 있습니다. 데이터는 [수집 속성](#ingestion-properties)에서 지정한 대로 동적으로 라우팅됩니다.

다음 예제에서는 업로드하기 전에 Blob 메타데이터에 대한 수집 속성을 설정하는 방법을 보여 줍니다. Blob은 다른 테이블로 라우팅됩니다.

자세한 내용은 [Blob 업로드](#upload-blobs)를 참조하세요.

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Blob 업로드

로컬 파일에서 Blob을 만들고, 수집 속성을 Blob 메타데이터로 설정하고, 이를 업로드할 수 있습니다. 예제는 [Event Grid 알림을 구독 하 여 데이터 탐색기에 Blob 수집](data-explorer-ingest-event-grid-portal.md#generate-sample-data) 을 참조 하세요.

> [!NOTE]
> - `BlockBlob`을 사용하여 데이터를 생성합니다. `AppendBlob`은 지원되지 않습니다.
> - Azure Data Lake Gen2 스토리지 SDK를 사용하려면 파일을 업로드하는 `CreateFile`을 사용하고 close 매개 변수가 "true"로 설정된 `Flush`를 끝에 사용해야 합니다.
<!-- > For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk). -->
> - Event Hub 엔드포인트에서 이벤트 수신을 승인하지 않으면 Azure Event Grid에서 다시 시도 메커니즘을 활성화합니다. 이 다시 시도 배달이 실패하면 Event Grid에서 *배달 못 한 편지* 프로세스를 사용하여 배달 안 됨 이벤트를 스토리지 계정에 배달할 수 있습니다. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](../../../event-grid/delivery-and-retry.md)를 참조하세요.

## <a name="rename-blobs"></a>Blob 이름 바꾸기

ADLSv2를 사용 하는 경우 blob의 이름을 변경 하 여 blob 수집을 데이터 탐색기으로 트리거할 수 있습니다. 예를 들어 [Event Grid 알림을 구독 하 여 데이터 탐색기에 Blob 수집](data-explorer-ingest-event-grid-portal.md#generate-sample-data)을 참조 하세요.

> [!NOTE]
> - ADLSv2에서는 디렉터리 이름을 변경할 수 있지만 디렉터리 내에서 *Blob 이름 변경* 이벤트 및 Blob 수집을 트리거하지 않습니다. 이름을 바꾼 후 Blob을 수집하려면 원하는 Blob의 이름을 직접 바꿉니다.
> - [데이터 연결을 만드는](data-explorer-ingest-event-grid-portal.md#create-an-event-grid-data-connection)동안 특정 주제를 추적 하는 필터를 정의한 경우<!-- or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path. -->

## <a name="delete-blobs-using-storage-lifecycle"></a>스토리지 수명 주기를 사용하여 Blob 삭제

수집 후에는 blob을 삭제 하지 데이터 탐색기. [Azure Blob 스토리지 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)를 사용하여 Blob 삭제를 관리합니다. Blob은 3~5일 동안 유지하는 것이 좋습니다.

## <a name="known-event-grid-issues"></a>알려진 Event Grid 문제

- 데이터 탐색기를 사용 하 여 event grid 수집에 사용 되는 파일을 [내보내는](/azure/data-explorer/kusto/management/data-export/export-data-to-storage?context=/azure/synapse-analytics/context/context) 경우 다음을 참고 하십시오. 
    - 내보내기 명령에 제공된 연결 문자열 또는 [외부 테이블](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table?context=/azure/synapse-analytics/context/context)에 제공된 연결 문자열이 [ADLS Gen2 형식](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-data-lake-storage-gen2)의 연결 문자열(예: `abfss://filesystem@accountname.dfs.core.windows.net`)이지만 스토리지 계정이 계층 구조 네임스페이스에 사용하도록 설정되지 않은 경우 Event Grid 알림이 트리거되지 않습니다.
    - 계정이 계층 구조 네임스페이스에 사용하도록 설정되지 않은 경우 연결 문자열에서 [Blob Storage 형식](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-blob-storage)(예: `https://accountname.blob.core.windows.net`)을 사용해야 합니다. 내보내기는 ADLS Gen2 연결 문자열을 사용하는 경우에도 예상대로 작동하지만, 알림이 트리거되지 않고 Event Grid 수집이 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Event Grid 알림을 구독 하 여 데이터 탐색기 blob 수집](data-explorer-ingest-event-grid-portal.md)
<!-- - [Create an Event Grid data connection for Data Explorer by using C#](data-connection-event-grid-csharp.md)
- [Create an Event Grid data connection for Data Explorer by using Python](data-connection-event-grid-python.md)
- [Create an Event Grid data connection for Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
- [Use one-click ingestion to ingest CSV data from a container to a new table in Data Explorer](one-click-ingestion-new-table.md) -->
