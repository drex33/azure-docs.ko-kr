---
title: Azure Synapse Data Explorer 풀에서 스트리밍 수집 구성
description: Azure Synapse Data Explorer 풀을 구성하고 스트리밍 수집으로 데이터 로드를 시작하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: mode-other
ms.openlocfilehash: 3b7c261328741b6594f2ca768bd2eb5035eebfb0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055336"
---
# <a name="configure-streaming-ingestion-on-your-azure-synapse-data-explorer-pool-preview"></a>Azure Synapse Data Explorer 풀에서 스트리밍 수집 구성(미리 보기)

스트리밍 수집은 수집과 쿼리 사이에 짧은 대기 시간이 필요할 때 데이터를 로드하는 데 유용합니다. 다음 시나리오에서 스트리밍 수집을 사용하는 것이 좋습니다.

- 1초 미만의 대기 시간이 필요합니다.
- 각 테이블로의 데이터 스트림이 비교적 작지만(초당 몇 개의 레코드) 전체 데이터 수집 볼륨은 높은(초당 수천 개의 레코드) 많은 테이블의 운영 처리를 최적화합니다.

각 테이블로의 데이터 스트림이 높은 경우(시간당 4GB 이상) [일괄 수집](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context)을 사용하는 것이 좋습니다.

수집 방법에 대한 자세한 내용은 [데이터 수집 개요](data-explorer-ingest-data-overview.md)을 참조하세요.

## <a name="choose-the-appropriate-streaming-ingestion-type"></a>적절한 스트리밍 수집 유형 선택

두 가지 스트리밍 수집 유형이 지원됩니다.

| 수집 유형 | Description |
| -- | -- |
| **Event Hub** 또는 **IoT Hub** | 허브는 테이블 스트리밍 데이터 원본으로 구성됩니다.<br />이러한 설정에 대한 내용은 [**Event Hub**](data-explorer-ingest-event-hub-overview.md)를 참조하세요. <!--  or [**IoT Hub**](ingest-data-iot-hub.md) data ingestion methods --> |
| **사용자 지정 수집** | 사용자 지정 수집을 사용하려면 Azure Synapse Data Explorer [클라이언트 라이브러리](/azure/data-explorer/kusto/api/client-libraries?context=/azure/synapse-analytics/context/context) 중 하나를 사용하는 애플리케이션을 작성해야 합니다.<br />이 항목의 정보를 사용하여 사용자 지정 수집을 구성합니다. [C# 스트리밍 수집 샘플 애플리케이션](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)이 유용할 수도 있습니다. |

다음 표를 사용하여 환경에 적합한 수집 유형을 선택합니다.

|조건|Event Hub/IoT Hub|사용자 지정 수집|
|---------|---------|---------|
|수집 시작과 쿼리에 사용할 수 있는 데이터 사이의 데이터 지연 | 더 긴 지연 | 더 짧은 지연 |
|개발 오버헤드 | 빠르고 쉬운 설정, 개발 오버헤드 없음 | 데이터를 수집하고, 오류를 처리하고, 데이터 일관성을 보장하는 애플리케이션을 만들기 위한 개발 오버헤드 높음 |

> [!NOTE]
> Synapse 작업 영역에서 데이터 반출 보호가 사용된 관리형 가상 네트워크를 사용하는 경우 Event Hub에서 데이터 탐색기 풀로의 데이터 수집이 작동하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- 쿼리 및 데이터 수집 엔드포인트를 가져옵니다.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="performance-and-operational-considerations"></a>성능 및 운영 고려 사항

스트리밍 수집에 영향을 줄 수 있는 주요 요소는 다음과 같습니다.

- **컴퓨팅 사양**: 데이터 탐색기 풀 크기가 증가함에 따라 스트리밍 수집 성능 및 용량이 확장됩니다. 동시 수집 요청 수는 코어당 6개로 제한됩니다. 예를 들어 컴퓨팅 최적화(대형) 및 스토리지 최적화(대형)와 같은 16코어 워크로드 유형의 경우 지원되는 최대 로드는 96개의 동시 수집 요청입니다. 컴퓨팅 최적화(초소형)와 같은 2코어 워크로드 유형의 경우 지원되는 최대 로드는 12개의 동시 수집 요청입니다.
- **데이터 크기 제한**: 스트리밍 수집 요청에 대한 데이터 크기 제한은 4MB입니다.
- **스키마 업데이트**: 테이블 만들기/수정 및 수집 매핑과 같은 스키마 업데이트에는 스트리밍 수집 서비스의 경우 최대 5분이 걸릴 수 있습니다. 자세한 내용은 [스트리밍 수집 및 스키마 변경](/azure/data-explorer/kusto/management/data-ingestion/streaming-ingestion-schema-changes?context=/azure/synapse-analytics/context/context)을 참조하세요.
- **SSD 용량**: Data Explorer 풀에서 스트리밍 수집을 사용하도록 설정하면 스트리밍을 통해 데이터가 수집되지 않는 경우에도 Data Explorer 풀 컴퓨터의 로컬 SSD 디스크 일부를 수집 데이터 스트리밍에 사용하고 핫 캐시에 사용할 수 있는 스토리지를 줄입니다.

## <a name="enable-streaming-ingestion-on-your-data-explorer-pool"></a>데이터 탐색기 풀에서 스트리밍 수집 사용

스트리밍 수집을 사용하려면 먼저 Data Explorer 풀에서 기능을 사용하도록 설정하고 [스트리밍 수집 정책](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)을 정의해야 합니다. [Data Explorer 풀을 만들](#enable-streaming-ingestion-while-creating-a-new-data-explorer-pool) 때 기능을 사용하도록 설정하거나 [기존 Data Explorer 풀에 추가](#enable-streaming-ingestion-on-an-existing-data-explorer-pool)할 수 있습니다.

> [!WARNING]
> 스트리밍 수집을 사용하도록 설정하기 전에 [제한 사항](#limitations)을 검토하세요.

### <a name="enable-streaming-ingestion-while-creating-a-new-data-explorer-pool"></a>새 Data Explorer 풀을 만드는 동안 스트리밍 수집 사용

Azure Synapse Studio 또는 Azure Portal를 사용하여 새 Data Explorer 풀을 만드는 동안 스트리밍 수집을 사용하도록 설정할 수 있습니다.

#### <a name="studio"></a>[스튜디오](#tab/azure-studio)

[Synapse Studio를 사용하여 Data Explorer 풀 만들기](../data-explorer-create-pool-studio.md#create-a-new-data-explorer-pool)의 단계를 통해 Data Explorer 풀을 만드는 동안 **추가 설정** 탭에서 **스트리밍 수집** > **사용** 을 선택합니다.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Azure Synapse Data Explorer에서 Data Explorer 풀을 만드는 동안 스트리밍 수집을 사용합니다.":::

#### <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal을 사용하여 Data Explorer 풀 만들기](../data-explorer-create-pool-portal.md#create-a-new-data-explorer-pool)의 단계를 통해 Data Explorer 풀을 만드는 동안 **추가 설정** 탭에서 **스트리밍 수집** > **사용** 을 선택합니다.

:::image type="content" source="../media/ingest-data-streaming/create-data-explorer-pool-advanced-settings-portal.png" alt-text="Azure Synapse Data Explorer에서 Data Explorer 풀을 만드는 동안 스트리밍 수집을 사용합니다.":::

---

### <a name="enable-streaming-ingestion-on-an-existing-data-explorer-pool"></a>기존 Data Explorer 풀에서 스트리밍 수집 사용

기존 Data Explorer 풀이 있는 경우 Azure Portal를 사용하여 스트리밍 수집을 사용하도록 설정할 수 있습니다.

1. Azure Portal에서 Data Explorer 풀로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **구성** 창에서 **켜기** 를 선택하여 **스트리밍 수집** 을 사용하도록 설정합니다.
1. **저장** 을 선택합니다.

### <a name="create-a-target-table-and-define-the-policy"></a>대상 테이블 만들기 및 정책 정의

스트리밍 수집 데이터를 수신하는 테이블을 만들고 Azure Synapse Studio 또는 Azure Portal을 사용하여 관련 정책을 정의합니다.

#### <a name="studio"></a>[스튜디오](#tab/azure-studio)

1. Synapse Studio의 왼쪽 창에서 **개발** 을 선택합니다.
1. **KQL 스크립트** 에서 **&plus;** (새 리소스 추가) > **KQL 스크립트** 를 선택합니다. 오른쪽 창에서 스크립트 이름을 지정할 수 있습니다.
1. **연결 대상** 메뉴에서 *contosodataexplorer* 를 선택합니다.
1. **데이터베이스 사용** 메뉴에서 *TestDatabase* 를 선택합니다.
1. 다음 명령을 붙여넣고 **실행** 을 선택하여 테이블을 만듭니다.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. 다음 명령 중 하나를 **쿼리 창** 에 복사하고 **실행** 을 선택합니다. 여기서 만든 테이블 또는 테이블이 포함된 데이터베이스에서 [스트리밍 수집 정책](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)을 정의합니다.

    > [!TIP]
    > 데이터베이스 수준에서 정의된 정책은 데이터베이스의 모든 기존 테이블 및 미래 테이블에 적용됩니다.

    - 만든 테이블에 대한 정책을 정의하려면 다음을 사용합니다.

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - 만든 테이블이 포함된 데이터베이스에 대한 정책을 정의하려면 다음을 사용합니다.

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Synapse Analytics 포털에서 Data Explorer 풀로 이동합니다.
1. **쿼리** 를 선택합니다.
1. 스트리밍 수집을 통해 데이터를 수신할 테이블을 만들려면 다음 명령을 **쿼리 창** 에 복사하고 **실행** 을 선택합니다.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

1. 다음 명령 중 하나를 **쿼리 창** 에 복사하고 **실행** 을 선택합니다. 여기서 만든 테이블 또는 테이블이 포함된 데이터베이스에서 [스트리밍 수집 정책](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)을 정의합니다.

    > [!TIP]
    > 데이터베이스 수준에서 정의된 정책은 데이터베이스의 모든 기존 테이블 및 미래 테이블에 적용됩니다.

    - 만든 테이블에 대한 정책을 정의하려면 다음을 사용합니다.

        ```kusto
        .alter table TestTable policy streamingingestion enable
        ```

    - 만든 테이블이 포함된 데이터베이스에 대한 정책을 정의하려면 다음을 사용합니다.

        ```kusto
        .alter database StreamingTestDb policy streamingingestion enable
        ```

---

## <a name="create-a-streaming-ingestion-application-to-ingest-data-to-your-data-explorer-pool"></a>데이터를 Data Explorer 풀에 수집하는 스트리밍 수집 애플리케이션 만들기

기본 설정 언어를 사용하여 데이터를 Data Explorer 풀에 수집하는 애플리케이션을 만듭니다. *poolPath* 변수의 경우 [사전 요구 사항](#prerequisites)에 기록해둔 쿼리 엔드포인트를 사용합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Kusto.Data;
using Kusto.Ingest;
using System.IO;
using Kusto.Data.Common;

namespace StreamingIngestion
{
    class Program
    {
        static void Main(string[] args)
        {
            string poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
            string appId = "<appId>";
            string appKey = "<appKey>";
            string appTenant = "<appTenant>";
            string dbName = "<dbName>";
            string tableName = "<tableName>";

            // Create Kusto connection string with App Authentication
            var csb =
                new KustoConnectionStringBuilder(poolPath)
                    .WithAadApplicationKeyAuthentication(
                        applicationClientId: appId,
                        applicationKey: appKey,
                        authority: appTenant
                    );

            // Create a disposable client that will execute the ingestion
            using (IKustoIngestClient client = KustoIngestFactory.CreateStreamingIngestClient(csb))
            {
                // Initialize client properties
                var ingestionProperties =
                    new KustoIngestionProperties(
                        databaseName: dbName,
                        tableName: tableName
                    );

                // Ingest from a compressed file
                var fileStream = File.Open("MyFile.gz", FileMode.Open);
                // Create source options
                var sourceOptions = new StreamSourceOptions()
                {
                    CompressionType = DataSourceCompressionType.GZip,
                };
                // Ingest from stream
                var status = client.IngestFromStreamAsync(fileStream, ingestionProperties, sourceOptions).GetAwaiter().GetResult();
            }
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
from Azure Synapse Analytics.kusto.data import KustoConnectionStringBuilder

from Azure Synapse Analytics.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient
)

poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
appId = "<appId>"
appKey = "<appKey>"
appTenant = "<appTenant>"
dbName = "<dbName>"
tableName = "<tableName>"

csb = KustoConnectionStringBuilder.with_aad_application_key_authentication(
    poolPath,
    appId,
    appKey,
    appTenant
)
client = KustoStreamingIngestClient(csb)

ingestionProperties = IngestionProperties(
    database=dbName,
    table=tableName,
    data_format=DataFormat.CSV
)

# Ingest from file
# Automatically detects gz format
client.ingest_from_file("MyFile.gz", ingestion_properties=ingestionProperties) 
```

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Load modules using ES6 import statements:
import { DataFormat, IngestionProperties, StreamingIngestClient } from "azure-kusto-ingest";
import { KustoConnectionStringBuilder } from "azure-kusto-data";

// For earlier version, load modules using require statements:
// const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
// const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
// const {DataFormat} = require("azure-kusto-ingest").IngestionPropertiesEnums;
// const StreamingIngestClient = require("azure-kusto-ingest").StreamingIngestClient;

const poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
const appId = "<appId>";
const appKey = "<appKey>";
const appTenant = "<appTenant>";
const dbName = "<dbName>";
const tableName = "<tableName>";
const mappingName = "<mappingName>"; // Required for JSON formatted files

const ingestionProperties = new IngestionProperties({
    database: dbName, // Your database
    table: tableName, // Your table
    format: DataFormat.JSON,
    ingestionMappingReference: mappingName
});

// Initialize client with engine endpoint
const client = new StreamingIngestClient(
    KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
        poolPath,
        appId,
        appKey,
        appTenant
    ),
    ingestionProperties
);

// Automatically detects gz format
await client.ingestFromFile("MyFile.gz", ingestionProperties);
```

### <a name="go"></a>[Go](#tab/go)

```go
import (
    "context"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto"
    "github.com/Azure Synapse Analytics/azure-kusto-go/kusto/ingest"
    "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
)

func ingest() {
    poolPath := "https://<Poolname>.<WorkspaceName>.kusto.windows.net"
    appId := "<appId>"
    appKey := "<appKey>"
    appTenant := "<appTenant>"
    dbName := "<dbName>"
    tableName := "<tableName>"
    mappingName := "<mappingName>" // Optional, can be nil

    // Creates a Kusto Authorizer using your client identity, secret, and tenant identity.
    // You may also uses other forms of authorization, see GoDoc > Authorization type.
    // auth package is: "github.com/Azure Synapse Analytics/go-autorest/autorest/Azure Synapse Analytics/auth"
    authorizer := kusto.Authorization{
        Config: auth.NewClientCredentialsConfig(appId, appKey, appTenant),
    }

    // Create a client
    client, err := kusto.New(poolPath, authorizer)
    if err != nil {
        panic("add error handling")
    }

    // Create an ingestion instance
    // Pass the client, the name of the database, and the name of table you wish to ingest into.
    in, err := ingest.New(client, dbName, tableName)
    if err != nil {
        panic("add error handling")
    }

    // Go currently only supports streaming from a byte array with a maximum size of 4 MB.
    jsonEncodedData := []byte("{\"a\":  1, \"b\":  10}\n{\"a\":  2, \"b\":  20}")

    // Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
    if err := in.Stream(context.Background(), jsonEncodedData, ingest.JSON, mappingName); err != nil {
        panic("add error handling")
    }
}
```

### <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.Azure Synapse Analytics.kusto.data.auth.ConnectionStringBuilder;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClient;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestClientFactory;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.IngestionProperties;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.result.OperationStatus;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.CompressionType;
import com.microsoft.Azure Synapse Analytics.kusto.ingest.source.StreamSourceInfo;
import java.io.FileInputStream;
import java.io.InputStream;

public class FileIngestion {
    public static void main(String[] args) throws Exception {
        String poolPath = "https://<Poolname>.<WorkspaceName>.kusto.windows.net";
        String appId = "<appId>";
        String appKey = "<appKey>";
        String appTenant = "<appTenant>";
        String dbName = "<dbName>";
        String tableName = "<tableName>";

        // Build connection string and initialize
        ConnectionStringBuilder csb =
            ConnectionStringBuilder.createWithAadApplicationCredentials(
                poolPath,
                appId,
                appKey,
                appTenant
            );

        // Initialize client and its properties
        IngestClient client = IngestClientFactory.createClient(csb);
        IngestionProperties ingestionProperties =
            new IngestionProperties(
                dbName,
                tableName
            );

        // Ingest from a compressed file
        // Create Source info
        InputStream zipInputStream = new FileInputStream("MyFile.gz");
        StreamSourceInfo zipStreamSourceInfo = new StreamSourceInfo(zipInputStream);
        // If the data is compressed
        zipStreamSourceInfo.setCompressionType(CompressionType.gz);
        // Ingest from stream
        OperationStatus status = client.ingestFromStream(zipStreamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
    }
}
```

---

## <a name="disable-streaming-ingestion-on-your-data-explorer-pool"></a>Data Explorer 풀에서 스트리밍 수집 사용 안 함

> [!WARNING]
> 스트리밍 수집을 사용하지 않도록 설정하는 데 몇 시간이 걸릴 수 있습니다.

Data Explorer 풀에서 스트리밍 수집을 사용하지 않도록 설정하기 전에 모든 관련 테이블과 데이터베이스에서 [스트리밍 수집 정책](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)을 삭제합니다. 스트리밍 수집 정책을 제거하면 Data Explorer 풀 내에서 데이터 다시 정렬이 트리거됩니다. 스트리밍 수집 데이터는 초기 스토리지에서 열 저장소(익스텐트 또는 분할)의 영구 스토리지로 이동됩니다. 이 프로세스는 초기 스토리지의 데이터 양에 따라 몇 초에서 몇 시간까지 걸릴 수 있습니다.

### <a name="drop-the-streaming-ingestion-policy"></a>스트리밍 수집 정책 삭제

Azure Synapse Studio 또는 Azure Portal을 사용하여 스트리밍 수집 정책을 삭제할 수 있습니다.

#### <a name="studio"></a>[스튜디오](#tab/azure-studio)

1. Synapse Studio의 왼쪽 창에서 **개발** 을 선택합니다.
1. **KQL 스크립트** 에서 **&plus;** (새 리소스 추가) > **KQL 스크립트** 를 선택합니다. 오른쪽 창에서 스크립트 이름을 지정할 수 있습니다.
1. **연결 대상** 메뉴에서 *contosodataexplorer* 를 선택합니다.
1. **데이터베이스 사용** 메뉴에서 *TestDatabase* 를 선택합니다.
1. 다음 명령을 붙여넣고 **실행** 을 선택하여 테이블을 만듭니다.

    ```kusto
    .delete table TestTable policy streamingingestion
    ```

1. Azure Portal에서 Data Explorer 풀로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **구성** 창에서 **켜기** 를 선택하여 **스트리밍 수집** 을 사용하도록 설정합니다.
1. **저장** 을 선택합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 Data Explorer 풀로 이동하여 **쿼리** 를 선택합니다.
1. 테이블에서 스트리밍 수집 정책을 삭제하려면 다음 명령을 **쿼리 창** 에 복사하고 **실행** 을 선택합니다.

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

1. **설정** 에서 **구성** 을 선택합니다.
1. **구성** 창에서 **끄기** 를 선택하여 **스트리밍 수집** 을 사용하지 않도록 설정합니다.
1. **저장** 을 선택합니다.

---

## <a name="limitations"></a>제한 사항

- 데이터베이스 자체나 해당 테이블에 [스트리밍 수집 정책](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context)이 정의되고 사용하도록 설정된 경우 데이터베이스에서 [데이터베이스 커서](/azure/data-explorer/kusto/management/databasecursor?context=/azure/synapse-analytics/context/context)가 지원되지 않습니다.
- 스트리밍 수집에 사용하려면 [데이터 매핑](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)을 [미리 만들어야](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) 합니다. 개별 스트리밍 수집 요청은 인라인 데이터 매핑을 수용하지 않습니다.
- 스트리밍 수집 데이터에는 [익스텐트 태그](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging)를 설정할 수 없습니다.
- [업데이트 정책](/azure/data-explorer/kusto/management/updatepolicy?context=/azure/synapse-analytics/context/context) 업데이트 정책은 원본 테이블에서 새로 수집된 데이터만 참조할 수 있으며 데이터베이스의 다른 데이터나 테이블은 참조할 수 없습니다.
- 스트리밍 수집이 데이터베이스의 테이블에서 사용되는 경우 이 데이터베이스는 팔로워 데이터베이스의 리더로 사용되거나 Azure Synapse Analytics Data Share의 데이터 공급자로 사용될 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Data Explorer로 분석](../../get-started-analyze-data-explorer.md)
- [Data Explorer 풀 모니터링](../data-explorer-monitor-pools.md)
