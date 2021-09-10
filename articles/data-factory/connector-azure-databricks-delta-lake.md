---
title: Azure Databricks Delta Lake에/에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인의 복사 작업을 사용하여 Azure Databricks Delta Lake 간에 데이터를 복사하는 방법을 알아봅니다.
ms.author: susabat
author: ssabat
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: afd401e71f7f369987ca165c66f5a5b33e55cb24
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314026"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Azure Databricks Delta Lake 간에 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 복사 작업을 사용하여 Azure Databricks Delta Lake에/에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업](copy-activity-overview.md) 문서를 기준으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Databricks Delta Lake 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md) 테이블을 사용하는 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

일반적으로 이 서비스는 다양한 요구 사항을 충족하기 위해 다음과 같은 기능으로 Delta Lake를 지원합니다.

- 복사 작업은 지원되는 모든 원본 데이터 저장소에서 Azure Databricks Delta Lake 테이블로 데이터를 복사하고 Delta Lake 테이블에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사하기 위해 Azure Databricks Delta Lake 커넥터를 지원합니다. Databricks 클러스터를 활용하여 데이터 이동을 수행하고 [필수 구성 요소 섹션](#prerequisites)의 세부 정보를 참조하세요.
- [매핑 데이터 흐름](concepts-data-flow-overview.md)은 Azure Storage의 일반 [델타 형식](format-delta.md)을 소스 및 싱크로 지원하여 코드 없는 ETL용 델타 파일을 읽고 쓸 수 있으며, 관리형 Azure Integration Runtime에서 실행됩니다.
- [Databricks 작업](transform-data-databricks-notebook.md)은 Delta Lake 위에서 코드 중심 ETL 또는 기계 학습 워크로드를 오케스트레이션하도록 지원합니다.

## <a name="prerequisites"></a>필수 조건

이 Azure Databricks Delta Lake 커넥터를 사용하려면 Azure Databricks에서 클러스터를 설정해야 합니다.

- Delta Lake에 데이터를 복사하기 위해 복사 작업에서는 Azure Databricks 클러스터를 호출하여 Azure Storage에서 데이터를 읽습니다. Azure Storage는 원래 원본이거나 서비스가 기본 제공 준비 복사본을 통해 원본 데이터를 처음 쓰는 준비 영역입니다. [싱크로서의 Delta Lake](#delta-lake-as-sink)에서 자세히 알아보세요.
- 마찬가지로, Delta Lake에서 데이터를 복사하기 위해 복사 작업에서는 Azure Databricks 클러스터를 호출하여 Azure Storage에 데이터를 씁니다. Azure Storage는 원본 싱크이거나 서비스가 기본 제공 준비 복사본을 통해 마지막 싱크에 계속 데이터를 쓰는 준비 영역입니다. [소스로서의 Delta Lake](#delta-lake-as-source)에서 자세히 알아보세요.

Databricks 클러스터에는 Azure Blob 또는 Azure Data Lake Storage Gen2 계정, 원본/싱크/준비에 사용되는 스토리지 컨테이너/파일 시스템과 Delta Lake 테이블을 작성하려는 컨테이너/파일 시스템에 대한 액세스 권한이 있어야 합니다.

- **Azure Data Lake Storage Gen2** 를 사용하려면 Apache Spark 구성의 일부로 Databricks 클러스터에서 **서비스 주체** 를 구성할 수 있습니다. [서비스 주체를 사용하여 직접 액세스](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20)의 단계를 따릅니다.

- **Azure Blob Storage** 를 사용하려면 Apache Spark 구성의 일부로 Databricks 클러스터에서 **스토리지 계정 액세스 키** 또는 **SAS 토큰** 을 구성할 수 있습니다. [RDD API를 사용하여 Azure Blob Storage에 액세스](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)의 단계를 따릅니다.

복사 작업을 실행하는 동안 구성한 클러스터가 종료되면 서비스에서 자동으로 시작합니다. 작성 UI를 사용하여 파이프라인을 작성하는 경우 데이터 미리 보기 등의 작업을 위해 라이브 클러스터가 필요하며 서비스에서 사용자 대신 클러스터를 시작하지 않습니다.

#### <a name="specify-the-cluster-configuration"></a>클러스터 구성 지정

1. **클러스터 모드** 드롭다운에서 **표준** 을 선택합니다.

2. **Databricks Runtime 버전** 드롭다운에서 Databricks 런타임 버전을 선택합니다.

3. [Spark 구성](/azure/databricks/clusters/configure#spark-config)에 다음 속성을 추가하여 [자동 최적화](/azure/databricks/delta/optimizations/auto-optimize)를 켭니다.

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. 통합 및 스케일링 요구 사항에 따라 클러스터를 구성합니다.

클러스터 구성에 관한 자세한 내용은 [클러스터 구성](/azure/databricks/clusters/configure)을 참조하세요.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-databricks-delta-lake-using-ui"></a>UI를 사용하여 Azure Databricks Delta Lake에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Databricks Delta Lake에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. delta를 검색하고 Azure Databricks Delta Lake 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-databricks-delta-lake/azure-databricks-delta-lake-connector.png" alt-text="Azure Databricks Delta Lake 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-databricks-delta-lake/configure-azure-databricks-delta-lake-linked-service.png" alt-text="Azure Databricks Delta Lake 연결된 서비스의 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Databricks Delta Lake 커넥터에 고유한 엔터티를 정의하는 속성에 관해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Databricks Delta Lake 연결된 서비스에 다음 속성이 지원됩니다.

| 속성    | 설명                                                  | 필수 |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | type 속성은 **AzureDatabricksDeltaLake** 로 설정해야 합니다. | 예      |
| 도메인      | Azure Databricks 작업 영역 URL(예: `https://adb-xxxxxxxxx.xx.azuredatabricks.net`)을 지정합니다. |          |
| clusterId   | 기존 클러스터의 클러스터 ID를 지정합니다. 이미 만든 대화형 클러스터여야 합니다. <br>Databricks 작업 영역 -> 대화형 클러스터 이름 -> 구성 -> 태그에서 대화형 클러스터의 클러스터 ID를 찾을 수 있습니다. [자세히 알아봅니다](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | 서비스가 Azure Databricks에서 인증을 받으려면 액세스 토큰이 필요합니다. 액세스 토큰은 Databricks 작업 영역에서 생성해야 합니다. 액세스 토큰을 찾는 더 자세한 단계는 [여기](/azure/databricks/dev-tools/api/latest/authentication#generate-token)에서 확인할 수 있습니다. |          |
| connectVia  | 데이터 저장소에 연결하는 데 사용되는 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임을 사용합니다. | 예       |

**예:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

Azure Databricks Delta Lake 데이터 세트에 다음 속성이 지원됩니다.

| 속성  | 설명                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 세트의 type 속성을 **AzureDatabricksDeltaLakeDataset** 로 설정해야 합니다. | 예                         |
| 데이터베이스 | 데이터베이스의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다.  |
| 테이블 | 델타 테이블의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다.  |

**예:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Databricks Delta Lake 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="delta-lake-as-source"></a>원본으로서의 Delta Lake

Azure Databricks Delta Lake에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성                     | 설명                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 type 속성을 **AzureDatabricksDeltaLakeSource** 로 설정해야 합니다. | 예      |
| Query          | 데이터를 읽는 SQL 쿼리를 지정합니다. 시간 이동 컨트롤의 경우 다음 패턴을 따릅니다.<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | 예       |
| exportSettings | 델타 테이블에서 데이터를 검색하는 데 사용되는 고급 설정입니다. | 예       |
| ***`exportSettings` 아래에서:*** |  |  |
| type | 내보내기 명령의 형식은 **AzureDatabricksDeltaLakeExportCommand** 로 설정합니다. | 예 |
| dateFormat | 날짜 형식을 날짜 형식의 문자열로 지정합니다. 사용자 지정 날짜 형식은 [날짜 시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정하지 않으면 기본값 `yyyy-MM-dd`을 사용합니다. | 예 |
| timestampFormat | 타임스탬프 형식을 타임스탬프 형식의 문자열로 지정합니다. 사용자 지정 날짜 형식은 [날짜 시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정하지 않으면 기본값 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]`을 사용합니다. | 예 |

#### <a name="direct-copy-from-delta-lake"></a>Delta Lake에서 직접 복사

싱크 데이터 저장소와 형식이 이 섹션에 설명된 조건을 충족하는 경우 복사 작업을 사용하여 Azure Databricks Delta 테이블에서 싱크로 직접 복사할 수 있습니다. 이 서비스는 설정을 확인하고 다음 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

- **싱크 연결된 서비스** 는 [Azure Blob Storage](connector-azure-blob-storage.md) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)입니다. 계정 자격 증명은 Azure Databricks 클러스터 구성에서 사전 구성되어야 합니다. [사전 요구 사항](#prerequisites)에서 자세히 알아보세요.

- **싱크 데이터 형식** 은 다음과 같이 구성된 **Parquet**, **구분된 텍스트** 또는 **Avro** 이며 파일이 아니라 폴더를 가리킵니다.

    - **Parquet** 형식의 경우 압축 코덱은 **none**, **snappy** 또는 **gzip** 입니다.
    - **구분된 텍스트** 형식:
        - `rowDelimiter`는 단일 문자입니다.
        - `compression`은 **none**, **bzip2**, **gzip** 일 수 있습니다.
        - `encodingName` UTF-7은 지원되지 않습니다.
    - **Avro** 형식의 경우 압축 코덱은 **none**, **deflate** 또는 **snappy** 입니다.

- 복사 작업 원본에서 `additionalColumns`가 지정되지 않았습니다.
- 복사 작업 싱크에서 데이터를 구분된 텍스트로 복사하는 경우 `fileExtension` ".csv"여야 합니다.
- 복사 작업 매핑에서 형식 변환이 사용되지 않습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Delta Lake에서 스테이징된 복사

싱크 데이터 저장소 또는 형식이 마지막 섹션에서 언급된 대로 직접 복사 기준과 일치하지 않는 경우 중간 Azure Storage 인스턴스를 사용하여 기본 제공 스테이징된 복사를 사용하도록 설정합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 이 서비스는 Azure Databricks Delta Lake의 데이터를 준비 스토리지로 내보낸 다음, 데이터를 싱크에 복사하고 마지막으로 준비 스토리지에서 임시 데이터를 정리합니다. 스테이징을 사용하는 데이터 복사에 관한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 [Azure Blob Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 스토리지 계정을 임시 준비로 참조하는 [Azure Data Lake Storage Gen2 연결된 서비스](connector-azure-data-lake-storage.md#linked-service-properties)를 만듭니다. 그런 다음 복사 작업에서 `enableStaging`과 `stagingSettings` 속성을 지정합니다.

>[!NOTE]
>준비 스토리지 계정 자격 증명은 Azure Databricks 클러스터 구성에서 사전 구성되어야 합니다. [사전 요구 사항](#prerequisites)에서 자세히 알아보세요.

**예:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>싱크로서의 Delta Lake

Azure Databricks Delta Lake에 데이터를 복사하기 위해 복사 작업 **싱크** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | 복사 작업 싱크의 type 속성을 **AzureDatabricksDeltaLakeSink** 로 설정합니다. | 예      |
| preCopyScript | 각 실행 시 Databricks 델타 테이블에 데이터를 쓰기 전에 실행할 복사 작업의 SQL 쿼리를 지정합니다. 예: `VACUUM eventsTable DRY RUN` 이 속성을 사용하여 미리 로드된 데이터를 정리하거나 자르기 테이블 또는 Vacuum문을 추가할 수 있습니다. | 예       |
| importSettings | 델타 테이블에 데이터를 쓰는 데 사용되는 고급 설정입니다. | 예 |
| ***`importSettings` 아래에서:*** |                                                              |  |
| type | 가져오기 명령의 형식은 **AzureDatabricksDeltaLakeImportCommand** 입니다. | 예 |
| dateFormat | 문자열의 형식을 날짜 형식의 날짜 형식으로 지정합니다. 사용자 지정 날짜 형식은 [날짜 시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정하지 않으면 기본값 `yyyy-MM-dd`을 사용합니다. | 예 |
| timestampFormat | 문자열의 형식을 타임스탬프 형식의 타임스탬프 형식으로 지정합니다. 사용자 지정 날짜 형식은 [날짜 시간 패턴](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)의 형식을 따릅니다. 지정하지 않으면 기본값 `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]`을 사용합니다. | 예 |

#### <a name="direct-copy-to-delta-lake"></a>Delta Lake로 직접 복사

원본 데이터 저장소와 형식이 이 섹션에 설명된 조건을 충족하는 경우 복사 작업을 사용하여 원본에서 Azure Databricks Delta Lake로 직접 복사할 수 있습니다. 이 서비스는 설정을 확인하고 다음 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

- **원본 연결된 서비스** 는 [Azure Blob Storage](connector-azure-blob-storage.md) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)입니다. 계정 자격 증명은 Azure Databricks 클러스터 구성에서 사전 구성되어야 합니다. [사전 요구 사항](#prerequisites)에서 자세히 알아보세요.

- **원본 데이터 형식** 은 다음과 같이 구성된 **Parquet**, **구분된 텍스트** 또는 **Avro** 이며 파일이 아니라 폴더를 가리킵니다.

    - **Parquet** 형식의 경우 압축 코덱은 **none**, **snappy** 또는 **gzip** 입니다.
    - **구분된 텍스트** 형식:
        - `rowDelimiter`는 기본값 또는 임의의 문자 하나입니다.
        - `compression`은 **none**, **bzip2**, **gzip** 일 수 있습니다.
        - `encodingName` UTF-7은 지원되지 않습니다.
    - **Avro** 형식의 경우 압축 코덱은 **none**, **deflate** 또는 **snappy** 입니다.

- 복사 작업 원본에서는 다음이 해당됩니다. 

    - `wildcardFileName`은 `?`가 아니라 와일드카드 `*`만 포함하며, `wildcardFolderName`은 지정되어 있지 않습니다.
    - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` 및 `enablePartitionDiscovery`는 지정되지 않습니다.
    - `additionalColumns`을(를) 지정하지 않았습니다.

- 복사 작업 매핑에서 형식 변환이 사용되지 않습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink",
                "sqlReadrQuery": "VACUUM eventsTable DRY RUN"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>delta lake로 스테이징된 복사

원본 데이터 저장소 또는 형식이 마지막 섹션에서 언급된 대로 직접 복사 기준과 일치하지 않는 경우 중간 Azure 스토리지 인스턴스를 사용하여 기본 제공 스테이징된 복사를 사용하도록 설정합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 이 서비스는 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 스테이징 스토리지로 변환한 다음, 데이터를 Delta Lake에 로드합니다. 마지막으로 스토리지에서 임시 데이터를 정리합니다. 스테이징을 사용하는 데이터 복사에 관한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 [Azure Blob Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 스토리지 계정을 임시 준비로 참조하는 [Azure Data Lake Storage Gen2 연결된 서비스](connector-azure-data-lake-storage.md#linked-service-properties)를 만듭니다. 그런 다음 복사 작업에서 `enableStaging`과 `stagingSettings` 속성을 지정합니다.

>[!NOTE]
>준비 스토리지 계정 자격 증명은 Azure Databricks 클러스터 구성에서 사전 구성되어야 합니다. [사전 요구 사항](#prerequisites)에서 자세히 알아보세요.

**예:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>모니터링

다른 커넥터의 경우와 동일한 [복사 작업 모니터링 환경](copy-activity-monitoring.md)이 제공됩니다. 또한 Delta Lake에서/에 데이터를 로드하는 작업이 Azure Databricks 클러스터에서 실행되므로 [자세한 클러스터 로그를 확인](/azure/databricks/clusters/clusters-manage#--view-cluster-logs)하고 [성능을 모니터링](/azure/databricks/clusters/clusters-manage#--monitor-performance)할 수 있습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 관한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
