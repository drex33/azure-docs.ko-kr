---
title: Azure Database for MySQL에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory를 사용하여 Azure Database for MySQL에서 데이터를 복사하고 변환하는 방법을 알아봅니다.
ms.author: susabat
author: ssabat
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 03/10/2021
ms.openlocfilehash: 338405fd04617d3b1d563b7ac95d7233d23a8678
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642415"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Database for MySQL에서 데이터 복사 및 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Database for MySQL 간에 데이터를 복사하고 Data Flow를 사용하여 Azure Database for MySQL에서 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

이 커넥터는 [Azure Database for MySQL 서비스](../mysql/overview.md)에 대해 특수화되어 있습니다. 온-프레미스 또는 클라우드에 있는 일반 MySQL 데이터베이스에서 데이터를 복사하려면 [MySQL 커넥터](connector-mysql.md)를 사용합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Database for MySQL 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Database for MySQL 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Database for MySQL 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureMySql** 로 설정되어야 합니다. | 예 |
| connectionString | Azure Database for MySQL 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/> Azure Key Vault에 암호를 넣고, 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

일반적인 연결 문자열은 `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`입니다. 사례에 따라 다음과 같은 더 많은 속성을 설정할 수 있습니다.

| 속성 | Description | 옵션 | 필수 |
|:--- |:--- |:--- |:--- |
| SSLMode | 이 옵션은 MySQL에 연결할 때 드라이버에서 TLS 암호화 및 확인을 사용하는지 여부를 지정합니다. 예: `SSLMode=<0/1/2/3/4>`| 사용 안 함(0) / 기본 설정(1) **(기본값)** / 필요(2) / VERIFY_CA(3) / VERIFY_IDENTITY(4) | 예 |
| UseSystemTrustStore | 이 옵션은 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 예를 들어 `UseSystemTrustStore=<0/1>;`| 사용(1) / 사용 안 함(0) **(기본값)** | 예 |

**예:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Database for MySQL 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Azure Database for MySQL에서 데이터를 복사하려면 데이터 세트의 형식 속성을 **AzureMySqlTable** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **AzureMySqlTable** 로 설정해야 합니다. | 예 |
| tableName | MySQL 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Database for MySQL 원본 및 싱크에서 지원하는 속성의 목록을 제공합니다.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL을 원본으로

Azure Database for MySQL에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원하는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **AzureMySqlSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |
| queryCommandTimeout | 쿼리 요청 시간이 초과되기 전의 대기 시간입니다. 기본값은 120분(02:00:00)입니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL을 싱크로

Azure Database for MySQL로 데이터를 복사하기 위해 복사 작업 **싱크** 섹션에서 지원하는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 형식 속성은 **AzureMySqlSink** 로 설정해야 합니다. | 예 |
| preCopyScript | 각 실행 시 Azure Database for MySQL에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 예 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하면 Azure Database for MySQL 테이블에 데이터를 삽입합니다.<br>허용되는 값은 행 수를 나타내는 정수입니다. | 아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 아니요(기본값: 00:00:30) |

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 Azure Database for MySQL에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요. Azure Database for MySQL 데이터 세트 또는 [인라인 데이터 세트](data-flow-source.md#inline-datasets)를 원본 및 싱크 형식으로 사용하도록 선택할 수 있습니다.

### <a name="source-transformation"></a>원본 변환

다음 표에서는 Azure Database for MySQL 원본에서 지원하는 속성을 나열합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 테이블 | 테이블을 입력으로 선택하는 경우 데이터 흐름은 데이터 세트에 지정된 테이블에서 모든 데이터를 가져옵니다. | 예 | - |*(인라인 데이터 세트에만 해당)*<br>tableName |
| 쿼리 | 쿼리를 입력으로 선택하는 경우 원본에서 데이터를 가져올 SQL 쿼리를 지정하면 데이터 세트에서 지정한 테이블이 재정의됩니다. 쿼리를 사용하면 테스트 또는 조회를 위한 행을 줄일 수 있습니다.<br><br>**Order By** 절은 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 데이터 흐름에서 사용할 수 있는 테이블을 반환하는 SQL의 UDF입니다.<br>쿼리 예: `select * from mytable where customerId > 1000 and customerId < 2000` 또는 `select * from "MyTable"`| 예 | String | Query |
| Batch 크기 | 일괄 처리 크기를 지정하여 대량 데이터를 일괄 처리로 청크합니다. | 예 | 정수 | batchSize |
| 격리 수준 | 다음 격리 수준 중 하나를 선택합니다.<br>- 커밋된 읽기<br>- 커밋되지 않은 읽기(기본값)<br>- 반복 읽기<br>- 직렬화 가능<br>- 없음(격리 수준 무시) | 예 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>직렬화 가능<br/>없음</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Azure Database for MySQL 원본 스크립트 예

Azure Database for MySQL을 원본 형식으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>싱크 변환

다음 표에서는 Azure Database for MySQL 싱크에서 지원하는 속성을 나열합니다. 해당 속성은 **싱크 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 메서드 | 데이터베이스 대상에서 허용되는 작업을 지정합니다. 기본값은 삽입만 허용하는 것입니다.<br>행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 [행 변경 변환](data-flow-alter-row.md)이 필요합니다. | 예 | `true` 또는 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 키 열 | 업데이트, upsert, 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.<br>키로 선택한 열 이름은 후속 업데이트, upsert, 삭제의 일부로 사용됩니다. 따라서 싱크 매핑에 있는 열을 선택해야 합니다. | 예 | Array | 키 |
| 키 열 쓰기 건너뛰기 | 키 열에 값을 쓰지 않으려면 “키 열 작성 건너뛰기”를 선택합니다. | 예 | `true` 또는 `false` | skipKeyWrites |
| 테이블 작업 |쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.<br>- **None**: 테이블에 대한 작업이 수행되지 않습니다.<br>- **Recreate**: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.<br>- **Truncate**: 대상 테이블의 모든 행이 제거됩니다. | 아니요 | `true` 또는 `false` | recreate<br/>truncate |
| Batch 크기 | 각 일괄 처리에 작성되는 행 수를 지정합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다. | 예 | 정수 | batchSize |
| 사전 및 사후 SQL 스크립트 | 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 지정합니다. | 예 | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Azure Database for MySQL 싱크 스크립트 예

Azure Database for MySQL을 싱크 형식으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

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
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL의 데이터 형식 매핑

Azure Database for MySQL에서 데이터를 복사하는 경우 MySQL 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure Database for MySQL 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
