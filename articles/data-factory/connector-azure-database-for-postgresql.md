---
title: Azure Database for PostgreSQL에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics를 사용 하 여 Azure Database for PostgreSQL에서 데이터를 복사 하 고 변환 하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/25/2021
ms.openlocfilehash: ea2b0acf7c10f942d77c70a574fb19f0c29ca336
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473227"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 Azure Database for PostgreSQL 데이터 복사 및 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인에서 복사 작업을 사용 하 여 Azure Database for PostgreSQL 간에 데이터를 복사 하 고 데이터 Flow를 사용 하 여 Azure Database for PostgreSQL에서 데이터를 변환 하는 방법을 설명 합니다. 자세히 알아보려면 [Azure Data Factory](introduction.md) 및 [Synapse Analytics](../synapse-analytics/overview-what-is.md)에 대 한 소개 문서를 참조 하세요.

이 커넥터는 [Azure Database for PostgreSQL 서비스](../postgresql/overview.md)를 전문으로 지원합니다. 온-프레미스 또는 클라우드에 있는 일반 PostgreSQL 데이터베이스에서 데이터를 복사하려면 [PostgreSQL 커넥터](connector-postgresql.md)를 사용하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Database for PostgreSQL 커넥터는 다음 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용하는 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

현재 데이터 흐름은 Azure database for PostgreSQL 단일 서버를 지원 하지만 유연한 서버 또는 Hyperscale (Citus)은 지원 하지 않습니다. Azure Synapse Analytics의 데이터 흐름은 모든 PostgreSQL 특색을 지원 합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-database-for-postgresql-using-ui"></a>UI를 사용하여 Azure Database for PostgreSQL에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Database for PostgreSQL에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. PostgreSQL을 검색하고 Azure Database for PostgreSQL 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-database-for-postgresql/azure-database-for-postgresql-connector.png" alt-text="Azure Database for PostgreSQL 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-database-for-postgresql/configure-azure-database-for-postgresql-linked-service.png" alt-text="Azure Database for PostgreSQL에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Database for PostgreSQL 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Azure Database for PostgreSQL에 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzurePostgreSql** 로 설정해야 합니다. | 예 |
| connectionString | Azure Database for PostgreSQL에 연결하는 ODBC 연결 문자열입니다.<br/>Azure Key Vault에 암호를 넣고 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. | 예 |
| connectVia | 이 속성은 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)을 나타냅니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

일반적인 연결 문자열은 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`입니다. 사례에 따라 설정할 수 있는 추가 속성은 다음과 같습니다.

| 속성 | Description | 옵션 | 필수 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod(EM)| 드라이버와 데이터베이스 서버 간에 전송되는 데이터를 암호화하기 위해 드라이버에서 사용하는 메서드입니다. 예: `EncryptionMethod=<0/1/6>;`| 0(암호화 없음)**(기본값)** / 1(SSL) / 6(RequestSSL) | 예 |
| ValidateServerCertificate(VSC) | SSL 암호화를 사용할 때(암호화 메서드=1) 데이터베이스 서버에서 보내는 인증서의 유효성을 드라이버가 검사하는지 여부를 결정합니다. 예: `ValidateServerCertificate=<0/1>;`| 0(사용 안 함)**(기본값)** / 1(사용) | 예 |

**예제**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**예제**:

***Azure Key Vault에 암호 저장***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 이 섹션에서는 데이터 세트에서 Azure Database for PostgreSQL이 지원하는 속성 목록을 제공합니다.

Azure Database for PostgreSQL에서 데이터를 복사하려면 데이터 세트의 type 속성을 **AzurePostgreSqlTable** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 **AzurePostgreSqlTable** 로 설정해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Pipelines 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 Azure Database for PostgreSQL 원본에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSQL을 원본으로

Azure Database for PostgreSQL에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzurePostgreSqlSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **AzurePostgreSqlSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예를 들어 `SELECT * FROM mytable` 또는 `SELECT * FROM "MyTable"`입니다. PostgreSQL에서 엔터티 이름은 따옴표로 묶지 않은 경우 대/소문자를 구분하지 않는 것으로 취급됩니다. | 아니요(데이터 세트에서 tableName 속성이 지정된 경우) |
| partitionOptions | Azure SQL Database에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우 (즉, `None`은 안 됨), Azure SQL Database에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| ***`partitionSettings` 아래에서:*** | | |
| partitionNames | 복사해야 하는 물리적 파티션 목록입니다. <br>파티션 옵션이 `PhysicalPartitionsOfTable`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfTabularPartitionName`를 후크합니다. 예제는 [Azure Database for PostgreSQL에서 병렬 복사](#parallel-copy-from-azure-database-for-postgresql) 섹션을 참조 하세요. | 예 |
| partitionColumnName |  `int` `smallint` `bigint` `date` `timestamp without time zone` `timestamp with time zone` `time without time zone` 병렬 복사를 위해 범위 분할에서 사용할 정수 또는 날짜/시간 형식 (,,,, 또는)으로 원본 열의 이름을 지정 합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionColumnName `를 후크합니다. 예제는 [Azure Database for PostgreSQL에서 병렬 복사](#parallel-copy-from-azure-database-for-postgresql) 섹션을 참조 하세요. | 예 |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최댓값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionUpbound`를 후크합니다. 예제는 [Azure Database for PostgreSQL에서 병렬 복사](#parallel-copy-from-azure-database-for-postgresql) 섹션을 참조 하세요. | 예 |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최솟값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionLowbound`를 후크합니다. 예제는 [Azure Database for PostgreSQL에서 병렬 복사](#parallel-copy-from-azure-database-for-postgresql) 섹션을 참조 하세요. | 예 |

**예제**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL을 싱크로

Azure Database for PostgreSQL에 데이터를 복사하기 위해 복사 작업 **싱크** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **AzurePostgreSQLSink** 로 설정해야 합니다. | 예 |
| preCopyScript | 각 실행 시 Azure Database for PostgreSQL에 데이터를 기록하기 전 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 예 |
| writeMethod | Azure Database for PostgreSQL에 데이터를 쓰기 위해 사용되는 메서드입니다.<br>허용되는 값: **CopyCommand**(기본값: 성능이 더 높음), **BulkInsert** | 예 |
| writeBatchSize | 일괄 처리당 Azure Database for PostgreSQL에 로드되는 행 수입니다.<br>허용되는 값은 행 수를 나타내는 정수입니다. | 아니요(기본값: 1,000,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br>허용되는 값은 시간 범위 문자열입니다. 예를 들어 "00:30:00"(30분)입니다. | 아니요(기본값: 00:00:30) |

**예제**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="parallel-copy-from-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 병렬 복사

복사 작업의 Azure Database for PostgreSQL 커넥터는 데이터를 병렬로 복사 하기 위한 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](.\media\connector-azure-database-for-postgresql/connector-postgresql-partition-options.png)

분할 된 복사를 사용 하도록 설정 하면 복사 작업은 Azure Database for PostgreSQL 원본에 대해 병렬 쿼리를 실행 하 여 파티션으로 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어를 `parallelCopies` 4로 설정 하는 경우 서비스는 지정 된 파티션 옵션과 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며, 각 쿼리는 Azure Database for PostgreSQL에서 데이터의 일부를 검색 합니다.

특히 Azure Database for PostgreSQL에서 많은 양의 데이터를 로드 하는 경우 데이터 분할으로 병렬 복사를 사용 하도록 설정 하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일로 쓰는 것이 좋습니다(폴더 이름만 지정). 이 경우 단일 파일에 쓰는 것보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. |
| 데이터 분할을 위해 물리적 파티션을 사용하지 않지만 정수 열을 사용하여 대형 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다. |
| 사용자 지정 쿼리를 사용하여 물리적 파티션과 함께 대량의 데이터를 로드합니다. | **파티션 옵션**: 테이블의 물리적 파티션.<br>**쿼리**:`SELECT * FROM ?AdfTabularPartitionName WHERE <your_additional_where_clause>`.<br>**파티션 이름**: 데이터를 복사할 파티션 이름을 지정합니다. 지정 하지 않으면 서비스는 PostgreSQL 데이터 집합에서 지정한 테이블의 실제 파티션을 자동으로 검색 합니다.<br><br>실행 중에 서비스는를 `?AdfTabularPartitionName` 실제 파티션 이름으로 바꾸고 Azure Database for PostgreSQL으로 보냅니다. |
| 물리적 파티션이 없는 사용자 지정 쿼리를 사용하여 대량의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM ?AdfTabularPartitionName WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 또는 날짜/날짜/시간 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하려는 하한과 상한 범위 사이에서만 데이터를 검색하도록 지정합니다.<br><br>실행 중에 서비스는 `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` 및를 `?AdfRangePartitionLowbound` 각 파티션에 대 한 실제 열 이름 및 값 범위로 바꾸고 Azure Database for PostgreSQL으로 보냅니다. <br>예를 들어 파티션 열 “ID”의 하한이 1로 설정되고 상한이 80으로 설정된 경우 병렬 복사를 4로 설정하면 서비스는 4개의 파티션으로 데이터를 검색합니다. 해당 ID는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

파티션 옵션을 사용하여 데이터를 로드하는 모범 사례:

1. 데이터 기울이기를 방지하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택합니다. 
2. 테이블에 내장 파티션이 있는 경우 파티션 옵션 "테이블의 물리적 파티션"을 사용하여 성능을 향상시킵니다.    
3. Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 대규모 "[DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)"(>4)를 설정하여 더 많은 컴퓨팅 리소스를 활용할 수 있습니다. 해당하는 시나리오를 확인합니다.
4. "[복사 병렬성 정도](copy-activity-performance-features.md#parallel-copy)"는 파티션 번호를 제어합니다. 이 번호를 너무 크게 설정하면 성능이 저하될 수 있습니다. 이 번호를(DIU 또는 자체 호스팅 IR 노드 수) * (2~4)로 설정하는 것이 좋습니다.

**예: 물리적 파티션이 있는 대규모 테이블에서 전체 로드**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "AzurePostgreSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환할 때 Azure Database for PostgreSQL에서 테이블을 읽고 쓸 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요. Azure Database for PostgreSQL 데이터 세트 또는 [인라인 데이터 세트](data-flow-source.md#inline-datasets)를 원본 및 싱크 형식으로 사용하도록 선택할 수 있습니다.

### <a name="source-transformation"></a>원본 변환

다음 표에서는 Azure Database for PostgreSQL 원본에서 지원되는 속성을 나열합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 테이블 | 테이블을 입력으로 선택하는 경우 데이터 흐름은 데이터 세트에 지정된 테이블에서 모든 데이터를 가져옵니다. | 예 | - |*(인라인 데이터 세트에만 해당)*<br>tableName |
| 쿼리 | 쿼리를 입력으로 선택하는 경우 원본에서 데이터를 가져올 SQL 쿼리를 지정하면 데이터 세트에서 지정한 테이블이 재정의됩니다. 쿼리를 사용하면 테스트 또는 조회를 위한 행을 줄일 수 있습니다.<br><br>**Order By** 절은 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 데이터 흐름에서 사용할 수 있는 테이블을 반환하는 SQL의 UDF입니다.<br>쿼리 예: `select * from mytable where customerId > 1000 and customerId < 2000` 또는 `select * from "MyTable"` PostgreSQL에서 엔터티 이름은 따옴표로 묶지 않은 경우 대/소문자를 구분하지 않는 것으로 취급됩니다.| 예 | String | Query |
| Batch 크기 | 일괄 처리 크기를 지정하여 대량 데이터를 일괄 처리로 청크합니다. | 예 | 정수 | batchSize |
| 격리 수준 | 다음 격리 수준 중 하나를 선택합니다.<br>- 커밋된 읽기<br>- 커밋되지 않은 읽기(기본값)<br>- 반복 읽기<br>- 직렬화 가능<br>- 없음(격리 수준 무시) | 예 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>직렬화 가능<br/>없음</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Azure Database for PostgreSQL 원본 스트립트 예

Azure Database for PostgreSQL을 원본 형식으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>싱크 변환

다음 표에서는 Azure Database for PostgreSQL 싱크에서 지원되는 속성을 나열합니다. 해당 속성은 **싱크 옵션** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 메서드 | 데이터베이스 대상에서 허용되는 작업을 지정합니다. 기본값은 삽입만 허용하는 것입니다.<br>행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 [행 변경 변환](data-flow-alter-row.md)이 필요합니다. | 예 | `true` 또는 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 키 열 | 업데이트, upsert, 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.<br>키로 선택한 열 이름은 후속 업데이트, upsert, 삭제의 일부로 사용됩니다. 따라서 싱크 매핑에 있는 열을 선택해야 합니다. | 예 | Array | 키 |
| 키 열 쓰기 건너뛰기 | 키 열에 값을 쓰지 않으려면 “키 열 작성 건너뛰기”를 선택합니다. | 예 | `true` 또는 `false` | skipKeyWrites |
| 테이블 작업 |쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.<br>- **None**: 테이블에 대한 작업이 수행되지 않습니다.<br>- **Recreate**: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.<br>- **Truncate**: 대상 테이블의 모든 행이 제거됩니다. | 아니요 | `true` 또는 `false` | recreate<br/>truncate |
| Batch 크기 | 각 일괄 처리에 작성되는 행 수를 지정합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다. | 예 | 정수 | batchSize |
| 사전 및 사후 SQL 스크립트 | 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 지정합니다. | 예 | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database for PostgreSQL 싱크 스크립트 예

Azure Database for PostgreSQL을 싱크 형식으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 관한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
