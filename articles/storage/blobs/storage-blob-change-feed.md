---
title: Azure Blob Storage의 변경 피드 | Microsoft Docs
description: Azure Blob Storage의 변경 피드 로그 및 이를 사용하는 방법에 대해 알아봅니다.
author: tamram
ms.author: tamram
ms.date: 05/17/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0d06e40fc33a713904fb171a3a44ba8e977a254f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467649"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Azure Blob Storage의 변경 피드 지원

변경 피드는 스토리지 계정의 Blob 및 Blob 메타데이터에 발생하는 모든 변경 내용에 대한 트랜잭션 로그를 제공하기 위한 것입니다. 변경 피드는 이러한 변경 내용에 대해 **순서가 지정** 되고, **보장** 되며, **내구성** 이 있고, **변경할 수 없는**, **읽기 전용** 로그를 제공합니다. 클라이언트 애플리케이션은 언제든지 이러한 로그를 스트리밍 또는 일괄 처리 모드에서 읽을 수 있습니다. 변경 피드를 사용하면 Blob Storage 계정에서 발생하는 변경 이벤트를 저렴한 비용으로 처리하는 효율적이고 확장 가능한 솔루션을 빌드할 수 있습니다.

## <a name="how-the-change-feed-works"></a>변경 피드의 작동 방식

변경 피드는 표준 [Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 비용으로 스토리지 계정의 특수 컨테이너에 [Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)으로 저장됩니다. 요구 사항에 따라 이러한 파일의 보존 기간을 제어할 수 있습니다(현재 릴리스의 [조건](#conditions) 참조). 변경 이벤트는 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양의 레코드로 변경 피드에 추가됩니다. 이 형식은 인라인 스키마를 사용하여 풍부한 데이터 구조를 제공하는 간단하고 빠른 이진 형식입니다. 이 형식은 Hadoop 에코시스템, Stream Analytics 및 Azure Data Factory에서 널리 사용됩니다.

이러한 로그는 비동기, 증분 또는 전체 방식으로 처리할 수 있습니다. 많은 클라이언트 애플리케이션에서 자체의 고유한 속도로 변경 피드를 독립적으로 병렬로 읽을 수 있습니다. [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) 또는 [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html)와 같은 분석 애플리케이션은 로그를 Avro 파일로 직접 사용할 수 있으므로 사용자 지정 애플리케이션을 작성하지 않고도 높은 대역폭을 사용하여 저렴한 비용으로 로그를 처리할 수 있습니다.

다음 다이어그램에서는 레코드를 변경 피드에 추가하는 방법을 보여 줍니다.

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Blob에 대해 순서가 지정된 로그를 제공하기 위해 변경 피드가 작동하는 방식을 보여 주는 다이어그램":::

변경 피드 지원은 변경된 개체를 기반으로 하여 데이터를 처리하는 시나리오에 적합합니다. 예를 들어 애플리케이션에서 다음을 수행할 수 있습니다.

- 보조 인덱스를 업데이트하고, 캐시, 검색 엔진 또는 기타 콘텐츠 관리 시나리오와 동기화합니다.
- 스트리밍 방식 또는 일괄 처리 모드로 개체에 발생하는 변경 내용을 기반으로 비즈니스 분석 정보와 메트릭을 추출합니다.
- 엔터프라이즈 데이터 관리를 위한 보안, 규정 준수 또는 인텔리전스를 위해 일정 기간 동안 개체에 대한 변경 내용을 저장, 감사, 분석합니다.
- 재해 관리 또는 규정 준수를 위해 계정에서 개체 상태를 백업, 미러링 또는 복제하는 솔루션을 빌드합니다.
- 변경 이벤트에 대응하거나 만들어지거나 변경된 개체를 기반으로 하여 실행을 예약하는 연결된 애플리케이션 파이프라인을 빌드합니다.

변경 피드는 블록 Blob에 대한 [개체 복제](object-replication-overview.md) 및 [특정 시점 복원](point-in-time-restore-overview.md)에 대한 필수 기능입니다.

> [!NOTE]
> 변경 피드는 Blob에 발생하는 변경 내용에 대한 지속적이고 순서가 지정된 로그 모델을 제공합니다. 변경 내용은 변경 후 몇 분 내에 변경 피드 로그에 기록되고 사용할 수 있습니다. 애플리케이션에서 이보다 훨씬 더 빠르게 이벤트에 대응해야 하는 경우 [Blob Storage 이벤트](storage-blob-event-overview.md)를 대신 사용하는 것이 좋습니다. [Blob Storage 이벤트](storage-blob-event-overview.md)는 Azure Functions 또는 애플리케이션에서 Blob에 발생하는 변경에 빠르게 대응할 수 있도록 하는 실시간 일회성 이벤트를 제공합니다. 

## <a name="enable-and-disable-the-change-feed"></a>변경 피드를 사용하거나 사용하지 않도록 설정

변경 내용 캡처 및 기록을 시작하려면 스토리지 계정에서 변경 피드를 사용하도록 설정해야 합니다. 변경 내용 캡처를 중지하려면 변경 피드를 사용하지 않도록 설정합니다. 포털 또는 PowerShell에서 Azure Resource Manager 템플릿을 사용하여 변경 내용을 사용하거나 사용하지 않도록 설정할 수 있습니다.

변경 피드를 사용하도록 설정하는 경우 주의해야 할 몇 가지 사항은 다음과 같습니다.

- 각 스토리지 계정에는 Blob 서비스에 대한 하나의 변경 피드만 있으며 **$blobchangefeed** 컨테이너에 저장됩니다.

- 변경 내용 만들기, 업데이트 및 삭제는 Blob 서비스 수준에서만 캡처됩니다.

- 변경 피드는 계정에서 발생하는 모든 사용 가능한 이벤트에 대한 *모든* 변경 내용을 캡처합니다. 클라이언트 애플리케이션은 필요에 따라 이벤트 유형을 필터링할 수 있습니다. (현재 릴리스의 [조건](#conditions)을 참조하세요.)

- 범용 v2 및 Blob Storage 계정만 변경 피드를 사용하도록 설정할 수 있습니다. 프리미엄 블록 Blob 계정 및 계층 구조 네임스페이스 사용 계정은 현재 지원되지 않습니다. 범용 v1 스토리지 계정은 지원되지 않지만 가동 중지 시간 없이 범용 v2로 업그레이드할 수 있습니다. 자세한 내용은 [GPv2 스토리지 계정으로 업그레이드](../common/storage-account-upgrade.md)를 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정에서 변경 피드를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다.
1. **데이터 관리** 에서 **데이터 보호** 옵션으로 이동합니다.
1. **추적** 에서 **Blob 변경 피드 사용** 을 선택합니다.
1. **저장** 단추를 선택하여 데이터 보호 설정을 확인합니다.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Azure Portal에서 변경 피드를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 변경 피드를 사용하도록 설정합니다.

1. 최신 PowershellGet을 설치합니다.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. PowerShell 콘솔을 닫고 다시 엽니다.

3. **Az.Storage** 모듈 버전 2.5.0 이상을 설치합니다.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

5. 스토리지 계정에 대한 변경 피드를 사용하도록 설정합니다.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[템플릿](#tab/template)
Azure Resource Manager 템플릿을 사용하여 Azure Portal을 통해 기존 스토리지 계정에서 변경 피드를 사용하도록 설정합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.

2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.

3. **[사용자 지정 템플릿 배포](https://portal.azure.com/#create/Microsoft.Template)** 를 선택한 다음, **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

4. 템플릿 편집기에서 다음 json을 붙여넣습니다. `<accountName>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```

5. **저장** 단추를 선택하고, 계정의 리소스 그룹을 지정한 다음, **구매** 단추를 선택하여 템플릿을 배포하고 변경 피드를 사용하도록 설정합니다.

---

## <a name="consume-the-change-feed"></a>변경 피드 사용

변경 피드는 여러 메타데이터 및 로그 파일을 생성합니다. 이러한 파일은 스토리지 계정의 **$blobchangefeed** 컨테이너에 있습니다.

> [!NOTE]
> 현재 릴리스에서 $blobchangefeed 컨테이너는 Azure Portal에만 표시되고, Azure Storage Explorer에는 표시되지 않습니다. ListContainers API를 호출하는 경우 현재 $blobchangefeed 컨테이너가 표시되지 않지만, 컨테이너에서 ListBlobs API를 직접 호출하여 Blob을 표시할 수 있습니다.

클라이언트 애플리케이션은 변경 피드 프로세서 SDK와 함께 제공되는 Blob 변경 피드 프로세서 라이브러리를 사용하여 변경 피드를 사용할 수 있습니다.

[Azure Blob Storage에서 변경 피드 로그 처리](storage-blob-change-feed-how-to.md)를 참조하세요.

## <a name="understand-change-feed-organization"></a>변경 피드 구성 이해

<a id="segment-index"></a>

### <a name="segments"></a>세그먼트

변경 피드는 **시간별** *세그먼트* 로 구성되지만, 몇 분마다 추가되고 업데이트되는 변경 내용에 대한 로그입니다. 이러한 세그먼트는 해당 시간에 발생하는 Blob 변경 이벤트가 있는 경우에만 만들어집니다. 이렇게 하면 클라이언트 애플리케이션에서 전체 로그를 검색할 필요 없이 특정 시간 범위 내에서 발생하는 변경 내용을 사용할 수 있습니다. 자세한 내용은 [사양](#specifications)을 참조하세요.

변경 피드의 사용 가능한 시간별 세그먼트는 해당 세그먼트에 대한 변경 피드 파일의 경로를 지정하는 매니페스트 파일에 설명되어 있습니다. `$blobchangefeed/idx/segments/` 가상 디렉터리의 목록에는 시간별로 정렬된 세그먼트가 표시됩니다. 세그먼트 경로는 세그먼트에서 나타내는 시간별 시간 범위의 시작을 설명합니다. 이 목록을 사용하여 관심 있는 로그 세그먼트를 필터링할 수 있습니다.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`은 변경 피드를 사용하도록 설정하면 자동으로 만들어집니다. 이 파일은 무시해도 됩니다. 항상 비어 있는 초기화 파일입니다. 

세그먼트 매니페스트 파일(`meta.json`)은 `chunkFilePaths` 속성에서 해당 세그먼트에 대한 변경 피드 파일의 경로를 표시합니다. 세그먼트 매니페스트 파일의 예제는 다음과 같습니다.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> `$blobchangefeed` 컨테이너는 계정에서 변경 피드 기능을 사용하도록 설정한 후에만 표시됩니다. Blob을 컨테이너에 나열하려면 먼저 변경 피드를 사용하도록 설정한 후 몇 분 정도 기다려야 합니다. 

<a id="log-files"></a>

### <a name="change-event-records"></a>이벤트 레코드 변경

변경 피드 파일에는 일련의 변경 이벤트 레코드가 포함되어 있습니다. 각 변경 이벤트 레코드는 개별 Blob에 대한 하나의 변경 내용에 해당합니다. 레코드는 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용하여 직렬화되고 파일에 기록됩니다. 레코드는 Avro 파일 형식 사양을 사용하여 읽을 수 있습니다. 이러한 형식의 파일을 처리하는 데 사용할 수 있는 여러 라이브러리가 있습니다.

변경 피드 파일은 [추가 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)으로 `$blobchangefeed/log/` 가상 디렉터리에 저장됩니다. 각 경로 아래의 첫 번째 변경 피드 파일에는 `00000`이 파일 이름에 포함됩니다(예: `00000.avro`). 해당 경로에 추가되는 각 후속 로그 파일의 이름은 1씩 증가합니다(예: `00001.avro`).

변경 피드 레코드에 캡처되는 이벤트 유형은 다음과 같습니다.
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

변경 피드 파일에서 Json으로 변환된 변경 이벤트 레코드의 예제는 다음과 같습니다.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

각 속성에 대한 설명은 [Blob Storage에 대한 Azure Event Grid 이벤트 스키마](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)를 참조하세요. BlobPropertiesUpdated 및 BlobSnapshotCreated 이벤트는 현재 변경 피드 전용이며, Blob Storage 이벤트에는 아직 지원되지 않습니다.

> [!NOTE]
> 세그먼트에 대한 변경 피드 파일은 세그먼트를 만드는 즉시 표시되지 않습니다. 지연 시간은 변경 후 몇 분 이내인 변경 피드의 게시 대기 시간에 대한 일반적인 간격 내에 있습니다.

<a id="specifications"></a>

## <a name="specifications"></a>사양

- 변경 이벤트 레코드는 변경 피드에만 추가됩니다. 이러한 레코드가 추가되면 변경할 수 없으며 레코드 위치는 안정적입니다. 클라이언트 애플리케이션은 변경 피드의 읽기 위치에서 자체 검사점을 유지 관리할 수 있습니다.

- 변경 이벤트 레코드는 변경 후 몇 분 이내에 추가됩니다. 클라이언트 애플리케이션은 스트리밍 액세스를 위해 추가되거나 다른 시간에 대량으로 추가된 레코드를 사용하도록 선택할 수 있습니다.

- 변경 이벤트 레코드는 **Blob별** 수정 순서에 따라 정렬됩니다. Blob 간의 변경 순서는 Azure Blob Storage에서 정의되지 않습니다. 이전 세그먼트의 모든 변경은 후속 세그먼트의 변경 이전입니다.

- 변경 이벤트 레코드는 [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용하여 로그 파일에 직렬화됩니다.

- `eventType`에 `Control` 값이 있는 변경 이벤트 레코드는 내부 시스템 레코드이며, 계정의 개체에 대한 변경 내용을 반영하지 않습니다. 이러한 레코드는 무시해도 됩니다.

- `storageDiagnostics` 속성 모음의 값은 내부 전용이며, 애플리케이션에서 사용하도록 설계되지 않았습니다. 애플리케이션에는 해당 데이터에 대한 계약 종속성이 없어야 합니다. 이러한 속성은 무시해도 됩니다.

- 세그먼트에서 나타내는 시간은 15분 범위의 **근사치** 입니다. 따라서 모든 레코드를 지정된 시간 내에 사용하도록 보장하려면 연속된 이전 및 다음 시간 세그먼트를 사용합니다.

- 각 세그먼트에는 게시 처리량을 관리하기 위한 로그 스트림의 내부 분할로 인해 서로 다른 수의 `chunkFilePaths`가 있을 수 있습니다. 각 `chunkFilePath`의 로그 파일은 상호 배타적인 Blob을 포함하도록 보장되며, 반복하는 동안 Blob별 수정 순서를 위반하지 않고 병렬로 사용하고 처리할 수 있습니다.

- 세그먼트는 `Publishing` 상태에서 시작됩니다. 레코드를 세그먼트에 추가하는 작업이 완료되면 `Finalized`가 됩니다. `$blobchangefeed/meta/Segments.json` 파일에 있는 `LastConsumable` 속성의 날짜 이후의 날짜가 지정된 세그먼트의 로그 파일은 애플리케이션에서 사용하지 않아야 합니다. `$blobchangefeed/meta/Segments.json` 파일의 `LastConsumable` 속성의 예제는 다음과 같습니다.

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>조건 및 알려진 문제

이 섹션에서는 변경 피드의 현재 릴리스에서 알려진 문제 및 조건에 대해 설명합니다. 

- 단일 변경에 대한 이벤트 레코드 변경 내용이 변경 피드에 두 번 이상 나타날 수 있습니다.
- 변경 피드 로그 파일의 수명은 아직 해당 파일에 대한 시간 기반 보존 정책을 설정하여 관리할 수 없으며, Blob은 삭제할 수 없습니다.
- 로그 파일의 `url` 속성은 현재 항상 비어 있습니다.
- segments.json 파일의 `LastConsumable` 속성은 변경 피드에서 완료하는 첫 번째 세그먼트를 나열하지 않습니다. 이 문제는 첫 번째 세그먼트가 완료된 후에만 발생합니다. 첫 번째 시간 이후의 모든 후속 세그먼트는 `LastConsumable` 속성에서 정확하게 캡처됩니다.
- ListContainers API를 호출하는 경우 현재 **$blobchangefeed** 컨테이너가 표시되지 않으며, Azure Portal 또는 Storage Explorer에도 해당 컨테이너가 표시되지 않습니다. 콘텐츠는 $blobchangefeed 컨테이너에서 ListBlobs API를 직접 호출하여 볼 수 있습니다.
- 이전에 [계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md)를 시작한 스토리지 계정에 로그 파일이 표시되지 않는 문제가 있을 수 있습니다. 향후의 계정 장애 조치(failover)도 로그 파일에 영향을 줄 수 있습니다.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다. 

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/no-icon.png)              | ![예](../media/icons/no-icon.png) | 
| Premium 블록 Blob          | ![예](../media/icons/no-icon.png)|![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-the-change-feed-and-storage-analytics-logging"></a>변경 피드와 스토리지 분석 로깅의 차이점은 무엇인가요?

분석 로그에는 모든 작업에서 성공한 요청 및 실패한 요청을 포함하여 모든 읽기, 쓰기, 나열 및 삭제 작업에 대한 레코드가 있습니다. 분석 로그는 최선의 작업이며 순서가 보장되지 않습니다.

변경 피드는 Blob 생성, 수정 및 삭제와 같은 성공적인 변형 또는 계정 변경에 대한 트랜잭션 로그를 제공하는 솔루션입니다. 변경 피드는 모든 이벤트가 Blob당 성공적인 변경 순서로 기록되고 표시되도록 보장하므로 대량의 읽기 작업이나 실패한 요청에서 노이즈를 필터링할 필요가 없습니다. 변경 피드는 특정 보장이 필요한 애플리케이션 개발을 위해 기본적으로 설계되고 최적화되었습니다.

### <a name="should-i-use-the-change-feed-or-storage-events"></a>변경 피드 또는 스토리지 이벤트를 사용해야 하나요?

변경 피드로 두 기능을 모두 활용할 수 있으며 [Blob Storage 이벤트](storage-blob-event-overview.md)는 동일한 전달 안정성을 보장하여 동일한 정보를 제공하며 주요 차이점은 이벤트 레코드의 지연, 순서 지정 및 스토리지입니다. 변경 피드는 변경 후 몇 분 이내에 로그에 레코드를 게시하고 Blob당 변경 작업 순서도 보장합니다. 스토리지 이벤트는 실시간으로 푸시되며, 순서가 지정되지 않을 수 있습니다. 변경 피드 이벤트는 사용자가 직접 정의한 보존 기간이 있는 안정적인 읽기 전용 로그로 스토리지 계정 내에 지속적으로 저장되지만, 스토리지 이벤트는 명시적으로 저장하지 않는 한 이벤트 처리기에서 일시적으로 사용됩니다. 변경 피드를 사용하면 여러 애플리케이션에서 Blob API 또는 SDK를 사용하여 로그를 자체의 편리한 방식으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- ,NET 클라이언트 애플리케이션을 사용하여 변경 피드를 읽는 방법에 대한 예제를 참조합니다. [Azure Blob Storage에서 변경 피드 로그 처리](storage-blob-change-feed-how-to.md)를 참조하세요.
- 실시간으로 이벤트에 대응하는 방법을 알아봅니다. [Blob Storage 이벤트에 대응](storage-blob-event-overview.md)을 참조하세요.
- 모든 요청의 성공한 작업 및 실패한 작업 모두에 대한 자세한 로깅 정보에 대해 자세히 알아봅니다. [Azure 스토리지 분석 로깅](../common/storage-analytics-logging.md)을 참조하세요.
