---
title: Event Grid Azure Synapse 데이터 탐색기 데이터 수집(미리 보기)
description: Event Grid Azure Synapse 데이터 탐색기 데이터를 수집(로드)하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: cb275db1c34fa8e479a10a3ac5495160855c6add
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720362"
---
# <a name="ingest-blobs-into-azure-synapse-data-explorer-by-subscribing-to-event-grid-notifications-preview"></a>Event Grid 알림을 구독하여 blob을 Azure Synapse 데이터 탐색기(미리 보기)

<!-- > [!div class="op_single_selector"]
> * [One-click](one-click-ingestion-new-table.md)
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager template](data-connection-event-grid-resource-manager.md) -->

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

이 문서에서는 Event Grid 데이터 연결을 사용하여 스토리지 계정에서 Azure Synapse 데이터 탐색기 Blob을 수집합니다. [Azure Event Grid](../../../event-grid/overview.md) 구독을 설정하는 Event Grid 데이터 연결을 만듭니다. Event Grid 구독은 Azure Event Hub를 통해 스토리지 계정에서 데이터 탐색기 이벤트를 라우팅합니다. 그런 다음 시스템 전체에 걸친 데이터 흐름의 예를 볼 수 있습니다.

Event Grid 데이터 탐색기 커넥트 Event Grid. [](data-explorer-ingest-event-grid-overview.md)<!-- To create resources manually in the Azure portal, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md). -->

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Event Hubs 데이터를 보낼 대상 테이블 만들기
    1. Synapse Studio의 왼쪽 창에서 **개발** 을 선택합니다.
    1. **KQL 스크립트** 에서 **&plus;** (새 리소스 추가) > **KQL 스크립트** 를 선택합니다. 오른쪽 창에서 스크립트 이름을 지정할 수 있습니다.
    1. **연결 대상** 메뉴에서 *contosodataexplorer* 를 선택합니다.
    1. **데이터베이스 사용** 메뉴에서 *TestDatabase* 를 선택합니다.
    1. 다음 명령을 붙여넣고 **실행** 을 선택하여 테이블을 만듭니다.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
        ```

        > [!TIP]
        > 테이블이 성공적으로 만들어졌는지 확인합니다. 왼쪽 창에서 **데이터** 를 선택하고 *contosodataexplorer* 추가 메뉴를 선택한 다음, **새로 고침** 을 선택합니다. *contosodataexplorer* 아래에서 **테이블을** 확장하고 *TestTable* 테이블이 목록에 표시되는지 확인합니다.

    1. 다음 명령을 창에 복사하고, **실행** 을 선택하여 들어오는 JSON 데이터를 테이블(TestTable)의 열 이름과 데이터 형식에 매핑합니다.

        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
        ```

* [스토리지 계정](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)을 만듭니다.
* Event Grid 알림 구독은 `BlobStorage`, `StorageV2` 또는 [Data Lake Storage Gen2](../../../storage/blobs/data-lake-storage-introduction.md)용 Azure Storage 계정에서 설정할 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-event-grid-data-connection"></a>Event Grid 데이터 연결 만들기

이제 스토리지 계정을 데이터 탐색기 연결하여 스토리지로 흐르는 데이터가 테스트 테이블로 스트리밍되도록 합니다. 이 연결은 데이터 탐색기 아래의 Azure Portal 만들 수 있습니다.

1. 만든 데이터 탐색기 풀 아래에서 **데이터베이스**  >  **TestDatabase를** 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/select-test-database.png" alt-text="테스트 데이터베이스 선택.":::

1. **데이터 연결 및 데이터 연결 추가를** 선택합니다. 

    :::image type="content" source="../media/ingest-data-event-grid/event-hub-connection.png" alt-text="데이터 수집 및 데이터 연결 추가를 선택합니다.":::

#### <a name="data-connection---basics-tab"></a>데이터 연결 - 기본 탭

1. 연결 형식(**Blob Storage**)을 선택합니다.

1. 다음 정보로 양식을 작성합니다.

    :::image type="content" source="../media/ingest-data-event-grid/data-connection-basics.png" alt-text="연결 기본 사항으로 Event Grid 양식을 작성합니다.":::

    |**설정** | **제안 값** | **필드 설명**|
    |---|---|---|
    | 데이터 연결 이름 | *test-grid-connection* | 데이터 탐색기 만들려는 연결의 이름입니다.|
    | 스토리지 계정 구독 | 구독 ID | 스토리지 계정이 있는 구독 ID입니다.|
    | 스토리지 계정 | *gridteststorage1* | 이전에 만든 스토리지 계정의 이름입니다.|
    | 이벤트 유형 | *Blob이 만들어짐* 또는 *Blob 이름이 바뀜* | 수집을 트리거하는 이벤트 유형입니다. *Blob 이름이 바뀜* 은 ADLSv2 스토리지에만 지원됩니다. 지원되는 유형은 Microsoft.Storage.BlobCreated 또는 Microsoft.Storage.BlobRenamed입니다. |
    | 리소스 만들기 | *자동* | 데이터 탐색기 Event Grid 구독, 이벤트 허브 네임스페이스 및 이벤트 허브를 만들 것인지 여부를 정의합니다. <!-- To create resources manually, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md) -->|

1. 특정 주제를 추적하려면 **필터 설정** 을 선택합니다. 알림에 대한 필터를 다음과 같이 설정합니다.
    * **접두사** 필드는 제목의 *리터럴* 접두사입니다. 적용된 패턴이 *startswith* 이므로 여러 컨테이너, 폴더 또는 blob을 포함할 수 있습니다. 와일드카드는 허용되지 않습니다.
        * Blob 컨테이너에서 필터를 정의하려면 필드를 *반드시* 다음과 같이 설정해야 합니다. *`/blobServices/default/containers/[container prefix]`* .
        * Blob 접두사(또는 Azure Data Lake Gen2의 폴더)에 대한 필터를 정의하려면 필드를 *반드시* 다음과 같이 설정해야 합니다. *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * **접미사** 필드는 Blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다.
    * **대/소문자 구분** 필드는 접두사 및 접미사 필터가 대/소문자를 구분하는지 여부를 나타냅니다.
    * 이벤트 필터링에 대한 자세한 내용은 [Blob Storage 이벤트](../../../storage/blobs/storage-blob-event-overview.md#filtering-events)를 참조하세요.

    :::image type="content" source="../media/ingest-data-event-grid/filter-settings.png" alt-text="필터 설정 Event Grid.":::

1. **다음: 수집 속성** 을 선택합니다.

> [!NOTE]
> 클러스터에 옵션을 사용할 수 있게 되는 즉시 관리 ID를 사용하여 스토리지 계정에 액세스하도록 데이터 연결을 업데이트하는 것이 좋습니다.

#### <a name="data-connection---ingest-properties-tab"></a>데이터 연결 - 수집 속성 탭

1. 다음 정보로 양식을 작성합니다. 테이블 및 매핑 이름의 대/소문자를 구분합니다.

   :::image type="content" source="../media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="테이블 및 매핑 수집 속성을 검토하고 만듭니다.":::

    수집 속성:

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 테이블 이름 | *TestTable* | **TestDatabase** 에 만든 테이블입니다. |
    | 데이터 형식 | *JSON* | 지원되는 형식은 Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW 및 W3CLOG입니다. 지원되는 압축 옵션은 Zip 및 Gzip입니다. |
    | 매핑 | *TestMapping* | **TestDatabase** 에서 생성된 것으로, 들어오는 JSON 데이터를 **TestTable** 의 열 이름 및 데이터 형식에 매핑.|
    | 고급 설정 | *내 데이터에 머리글 표시* | 헤더를 무시합니다. *SV 형식 파일에 대해 지원됩니다.|

   > [!NOTE]
   > 모든 **기본 라우팅 설정** 을 지정할 필요는 없습니다. 부분 설정도 허용됩니다.
1. **다음: 검토 + 만들기** 를 선택합니다.

#### <a name="data-connection---review--create-tab"></a>데이터 연결 - 검토 + 만들기 탭

1. 자동으로 만들어진 리소스를 검토하고 **만들기** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Event Grid 대한 데이터 연결을 검토하고 만듭니다.":::

### <a name="deployment"></a>배포

배포가 완료될 때까지 기다립니다. 배포에 실패한 경우 실패한 단계 옆에 있는 **작업 세부 정보** 를 선택하여 실패 이유에 대한 자세한 정보를 확인합니다. 리소스를 다시 배포하려면 **다시 배포** 를 선택합니다. 배포하기 전에 매개 변수를 변경할 수 있습니다.

:::image type="content" source="../media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Event Grid 리소스를 배포합니다.":::

## <a name="generate-sample-data"></a>샘플 데이터 생성

이제 데이터 탐색기 스토리지 계정이 연결되면 샘플 데이터를 만들 수 있습니다.

### <a name="upload-blob-to-the-storage-container"></a>스토리지 컨테이너에 Blob 업로드

Azure Storage 리소스와 상호 작용하는 몇 가지 기본 Azure CLI 명령을 발급하는 작은 셸 스크립트를 사용합니다. 이 스크립트에서 수행하는 작업은 다음과 같습니다.

1. 스토리지 계정에 새 컨테이너를 만듭니다.
1. 해당 컨테이너에 기존 파일을 Blob으로 업로드합니다.
1. 컨테이너의 Blob을 나열합니다.

[Azure Cloud Shell](../../../cloud-shell/overview.md)을 사용하여 포털에서 직접 스크립트를 실행할 수 있습니다.

파일에 데이터를 저장하고 이 스크립트를 사용하여 업로드합니다.

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
    #!/bin/bash
    ### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> 최상의 수집 성능을 얻으려면 수집을 위해 제출된 압축 Blob의 *압축되지 않은* 크기를 전달해야 합니다. Event Grid 알림에는 기본 세부 정보만 포함되므로 크기 정보를 명시적으로 전달해야 합니다. 압축되지 않은 크기 정보는 Blob 메타데이터의 `rawSizeBytes` 속성을 *압축되지 않은* 데이터 크기(바이트)로 설정하여 제공할 수 있습니다.

### <a name="rename-blob"></a>Blob 이름 바꾸기

ADLSv2 스토리지에서 데이터를 수집하고 데이터 연결에 대한 이벤트 유형으로 *Blob 이름이 바뀜* 을 정의한 경우 Blob 수집 트리거는 Blob 이름 바꾸기입니다. Blob의 이름을 바꾸려면 Azure Portal에서 Blob으로 이동하고, 마우스 오른쪽 단추로 Blob을 클릭하고, **이름 바꾸기** 를 선택합니다.

   :::image type="content" source="../media/ingest-data-event-grid/rename-blob-in-the-portal.png" alt-text="Azure Portal에서 Blob의 이름을 바꿉니다.":::

### <a name="ingestion-properties"></a>수집 속성

Blob 메타데이터를 통해 Blob 수집의 [수집 속성](data-explorer-ingest-event-grid-overview.md#ingestion-properties)을 지정할 수 있습니다.

> [!NOTE]
> 데이터 탐색기 Blob 사후에는 삭제되지 않습니다.
> 3~5일 동안 Blob을 유지합니다.
> [Azure Blob Storage 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)를 사용하여 Blob 삭제를 관리합니다.

## <a name="review-the-data-flow"></a>데이터 흐름 검토

> [!NOTE]
> 데이터 탐색기 수집 프로세스를 최적화하도록 설계된 데이터 수집에 대한 집계(일괄 처리) 정책이 있습니다.
> 기본적으로 정책은 5 분으로 구성됩니다.
> 정책은 나중에 필요에 따라 변경할 수 있습니다. 이 문서에서는 몇 분의 대기 시간이 발생할 수 있습니다.

1. Azure Portal Event Grid 앱이 실행되는 동안 활동이 급증합니다.

    :::image type="content" source="../media/ingest-data-event-grid/event-grid-graph.png" alt-text="Event Grid 대한 활동 그래프입니다.":::

1. 현재까지 데이터베이스로 전송된 메시지의 수를 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```kusto
    TestTable
    | count
    ```

1. 메시지 내용을 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```kusto
    TestTable
    ```

    결과 집합은 다음 이미지와 같이 표시됩니다.

    :::image type="content" source="../media/ingest-data-event-grid/table-result.png" alt-text="Event Grid에 대한 메시지 결과 세트입니다.":::

## <a name="clean-up-resources"></a>리소스 정리

Event Grid 다시 사용하지 않으려면 비용이 발생하지 않도록 자동으로 구성된 Event Grid 구독, 이벤트 허브 네임스페이스 및 이벤트 허브를 정리합니다.

1. Azure Portal에서 왼쪽 메뉴로 이동하여 **모든 리소스** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Event Grid 정리를 위한 리소스를 Select all.":::

1. Event Hub 네임스페이스를 검색하고 **삭제** 를 선택하여 삭제합니다.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-find-event-hub-namespace-delete.png" alt-text="Event Hub 네임스페이스를 정리합니다.":::

1. 리소스 삭제 양식에서 삭제를 확인하여 Event Hub 네임스페이스 및 Event Hub 리소스를 삭제합니다.

1. 스토리지 계정으로 이동합니다. 왼쪽 메뉴에서 **이벤트** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Event Grid에 대해 정리할 이벤트를 선택합니다.":::

1. 그래프 아래에서 Event Grid 구독을 선택한 다음 **삭제** 를 선택하여 삭제합니다.

    :::image type="content" source="../media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Event Grid 구독을 삭제합니다.":::

1. Event Grid 데이터 연결을 삭제하려면 데이터 탐색기 클러스터로 이동합니다. 왼쪽 메뉴에서 **데이터베이스** 를 선택합니다.

1. 데이터베이스 **TestDatabase** 를 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="리소스를 정리할 데이터베이스를 선택합니다.":::

1. 왼쪽 메뉴에서 **데이터 수집** 을 선택합니다.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="리소스를 정리할 데이터 수집을 선택합니다.":::

1. 데이터 연결 *test-grid-connection* 을 선택한 다음 **삭제** 를 선택하여 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Data Explorer로 분석](../../get-started-analyze-data-explorer.md)
- [Data Explorer 풀 모니터링](../data-explorer-monitor-pools.md)