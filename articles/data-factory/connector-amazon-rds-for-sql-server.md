---
title: Amazon RDS for SQL Server에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 azure Synapse Analytics 파이프라인을 사용 하 여 온-프레미스 또는 azure VM에 있는 Amazon RDS for SQL Server 데이터베이스에서 데이터를 복사 하 고 변환 하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/26/2021
ms.openlocfilehash: 8c884262620784ef49a011e7f7398a0d44427b33
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084406"
---
# <a name="copy-and-transform-data-from-amazon-rds-for-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용 하 여 Amazon RDS for SQL Server에서 데이터 복사 및 변환

이 문서에서는 Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업을 사용 하 여 Amazon RDS for SQL Server 데이터베이스에서 데이터를 복사 하는 방법을 설명 합니다. 자세한 내용은 [Azure Data Factory](introduction.md) 또는 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)의 소개 문서를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Amazon RDS for SQL Server 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

Amazon RDS for SQL Server 데이터베이스에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

특히이 Amazon RDS for SQL Server 커넥터는 다음을 지원 합니다.

- SQL Server 버전 2005 이상
- SQL 또는 Windows 인증을 사용한 데이터 복사
- 쿼리 또는 저장 프로시저를 사용하여 데이터 검색(원본) SQL Server 원본에 대해 Amazon RDS의 병렬 복사를 선택할 수도 있습니다. 자세한 내용은 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조 하세요.

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb)는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-amazon-rds-for-sql-server-linked-service-using-ui"></a>UI를 사용 하 여 SQL Server 연결 된 서비스에 대 한 Amazon RDS 만들기

다음 단계를 사용 하 여 Azure Portal UI에서 Amazon RDS for SQL Server 연결 된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. amazon rds for SQL 서버를 검색 하 고 amazon rds for SQL Server 커넥터를 선택 합니다.

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/amazon-rds-for-sql-server-connector.png" alt-text="Amazon RDS for SQL Server 커넥터의 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/configure-amazon-rds-for-sql-server-linked-service.png" alt-text="SQL Server 연결 된 서비스에 대 한 Amazon RDS 구성 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Amazon RDS for SQL Server 데이터베이스 커넥터와 관련 된 Data Factory 및 Synapse 파이프라인 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon RDS for SQL Server 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **AmazonRdsForSqlServer** 로 설정 해야 합니다. | 예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용 하 여 Amazon RDS for SQL Server 데이터베이스에 연결 하는 데 필요한 **connectionString** 정보를 지정 합니다. 다음 샘플을 참조하세요.<br/>암호를 Azure Key Vault에 입력할 수도 있습니다. SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 다음에 나오는 JSON 예제를 참조하고 [Azure Key Vault에 로그인 정보를 저장](store-credentials-in-key-vault.md)합니다. |예 |
| userName |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. **domainname\\username** 을 예로 들 수 있습니다. |예 |
| password |username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장합니다. 또는 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수 있습니다. |예 |
| alwaysEncryptedSettings | 관리 id 또는 서비스 주체를 사용 하 여 SQL Server에 대해 Amazon RDS에 저장 된 중요 한 데이터를 보호 Always Encrypted를 사용 하도록 설정 하는 데 필요한 **alwaysencryptedsettings** 정보를 지정 합니다. 자세한 내용은 표 다음에 나오는 JSON 예제와 [Always Encrypted 사용](#using-always-encrypted) 섹션을 참조하세요. 지정하지 않으면 기본 Always Encrypted 설정이 사용하도록 설정되지 않습니다. |예 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

> [!NOTE]
> Amazon RDS for SQL Server [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true) 는 데이터 흐름에서 지원 되지 않습니다. 

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

**예제 1: SQL 인증 사용**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: Azure Key Vault의 암호를 통한 SQL 인증 사용**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**예제 3: Windows 인증 사용**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 4: Always Encrypted 사용**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "alwaysEncryptedSettings": {
            "alwaysEncryptedAkvAuthType": "ServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Amazon RDS for SQL Server 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

Amazon RDS for SQL Server 데이터베이스에서 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **AmazonRdsForSqlServerTable** 로 설정 해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |예 |
| 테이블 | 테이블/뷰의 이름입니다. |예 |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 예 |

**예제**

```json
{
    "name": "AmazonRdsForSQLServerDataset",
    "properties":
    {
        "type": "AmazonRdsForSqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Amazon RDS for SQL Server 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="amazon-rds-for-sql-server-as-a-source"></a>원본으로 SQL Server Amazon RDS

>[!TIP]
>데이터 분할을 사용 하 여 Amazon RDS for SQL Server에서 효율적으로 데이터를 로드 하려면 [SQL 데이터베이스의 병렬 복사](#parallel-copy-from-sql-database)에서 자세히 알아보세요.

Amazon RDS for SQL Server에서 데이터를 복사 하려면 복사 작업의 원본 형식을 **AmazonRdsForSqlServerSource** 로 설정 합니다. 복사 작업 source 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **AmazonRdsForSqlServerSource** 로 설정 해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. |예 |
| sqlReaderStoredProcedureName |이 속성은 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |예 |
| storedProcedureParameters |저장 프로시저용 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 다음과 같습니다. **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. 지정하지 않으면 데이터베이스의 기본 격리 수준이 사용됩니다. 자세한 내용은 [이 문서](/dotnet/api/system.data.isolationlevel)를 참조하세요. | 예 |
| partitionOptions | Amazon RDS for SQL Server에서 데이터를 로드 하는 데 사용 되는 데이터 분할 옵션을 지정 합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용 하도록 설정 하는 경우 (즉,이 아님 `None` ) Amazon RDS for SQL Server에서 데이터를 동시에 로드 하는 병렬 처리 수준이 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 복사 작업의 설정에 의해 제어 됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| ***`partitionSettings` 아래에서:*** | | |
| partitionColumnName | 원본 열의 이름을 **정수 또는 날짜/날짜/시간 유형** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` 또는 `datetimeoffset`) 병렬 복사를 위해 범위 분할에 사용됩니다. 지정하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfDynamicRangePartitionCondition `를 후크합니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionUpperBound | 파티션 범위 분할에 대한 파티션 열의 최댓값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.  <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 파티션 범위 분할에 대한 파티션 열의 최솟값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |

**다음 사항에 유의하세요.**

- **AmazonRdsForSqlServerSource** 에 대해 **sqlreaderquery** 를 지정 하는 경우 복사 작업은 데이터를 가져오기 위해 Amazon RDS for SQL Server 원본에 대해이 쿼리를 실행 합니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- 원본에서 저장 프로시저를 사용하여 데이터를 검색할 때 저장 프로시저가 다른 매개 변수 값이 전달되면 다른 스키마를 반환하도록 설계된 경우, UI에서 스키마를 가져오거나 자동 테이블을 만들어 SQL 데이터베이스로 데이터를 복사하면 예기치 않은 결과가 발생할 수 있습니다.

**예제: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
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
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예제: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
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
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**저장 프로시저 정의**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

## <a name="parallel-copy-from-sql-database"></a>SQL Database에서의 병렬 복사

copy 활동의 Amazon RDS for SQL Server 커넥터는 데이터를 병렬로 복사 하기 위한 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/connector-amazon-rds-for-sql-server/connector-amazon-rds-for-sql-partition-options.png" alt-text="파티션 옵션의 스크린샷":::

분할 된 복사를 사용 하도록 설정 하면 복사 작업이 Amazon RDS for SQL Server 원본에 대해 병렬 쿼리를 실행 하 여 파티션으로 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어를 `parallelCopies` 4로 설정 하는 경우 서비스는 지정 된 파티션 옵션과 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며, 각 쿼리는 AMAZON RDS for SQL Server에서 데이터의 일부를 검색 합니다.

특히 Amazon RDS for SQL Server에서 많은 양의 데이터를 로드 하는 경우 데이터 분할을 사용 하 여 병렬 복사를 사용 하도록 제안 하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일로 쓰는 것이 좋습니다(폴더 이름만 지정). 이 경우 단일 파일에 쓰는 것보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. <br><br/>테이블에 물리적 파티션이 있는지 확인하려면 [이 쿼리](#sample-query-to-check-physical-partition)를 참조하세요. |
| 데이터 분할을 위해 실제 파티션을 사용하지 않지만 정수 또는 날짜/시간 열을 사용하여 대규모 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**(선택 사항): 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다.<br/>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블의 행을 필터링하려는 것이 아니라 테이블의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색하고 MIN 및 MAX 값에 따라 시간이 오래 걸릴 수 있습니다. 상한 및 하한을 제공하는 것이 좋습니다. <br><br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. |
| 실제 파티션이 없는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 또는 날짜/시간 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다.<br>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블에서 행을 필터링하려는 것이 아니라 쿼리 결과의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>실행 중에 서비스는 `?AdfRangePartitionColumnName` 각 파티션에 대 한 실제 열 이름 및 값 범위를 대체 하 고 AMAZON RDS for SQL Server으로 보냅니다. <br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. <br><br>다양한 시나리오에 대한 추가적인 샘플 쿼리는 다음과 같습니다.<br> 1. 전체 테이블 쿼리: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. 열을 선택하고 where 절 필터를 추가하여 테이블 쿼리: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. 하위 쿼리를 사용하여 쿼리: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. 하위 쿼리에 파티션을 사용하여 쿼리: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

파티션 옵션을 사용하여 데이터를 로드하는 모범 사례:

1. 데이터 기울이기를 방지하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택합니다. 
2. 테이블에 내장 파티션이 있는 경우 파티션 옵션 "테이블의 물리적 파티션"을 사용하여 성능을 향상시킵니다.    
3. Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 대규모 "[DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)"(>4)를 설정하여 더 많은 컴퓨팅 리소스를 활용할 수 있습니다. 해당하는 시나리오를 확인합니다.
4. "[복사 병렬성 정도](copy-activity-performance-features.md#parallel-copy)"는 파티션 번호를 제어합니다. 이 번호를 너무 크게 설정하면 성능이 저하될 수 있습니다. 이 번호를(DIU 또는 자체 호스팅 IR 노드 수) * (2~4)로 설정하는 것이 좋습니다.

**예: 물리적 파티션이 있는 대규모 테이블에서 전체 로드**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>실제 파티션을 확인하는 샘플 쿼리

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

테이블에 물리적 파티션이 있는 경우 다음과 같이 "HasPartition"이 "예"로 표시됩니다.

:::image type="content" source="./media/connector-azure-sql-database/sql-query-result.png" alt-text="SQL 쿼리 결과":::

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요. 

## <a name="using-always-encrypted"></a>Always Encrypted 사용

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용 하 여 SQL Server에서 Amazon RDS로 데이터를 복사 하는 경우 아래 단계를 따르세요. 

1. [Azure Key Vault](../key-vault/general/overview.md)에 [CMK(열 마스터 키)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true)를 저장합니다. [Azure Key Vault를 사용하여 Always Encrypted를 구성하는 방법](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)을 자세히 알아보세요.

2. [CMK(열 마스터 키)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true)가 저장된 키 자격 증명 모음에 액세스할 수 있는지 확인합니다. 필요한 권한은 이 [문서](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults)를 참조하세요.

3. 연결된 서비스를 만들어 SQL 데이터베이스에 연결하고 관리 ID 또는 서비스 주체를 사용하여 ‘Always Encrypted’ 기능을 사용하도록 설정합니다. 

## <a name="troubleshoot-connection-issues"></a>연결 문제 해결

1. 원격 연결을 허용 하도록 Amazon RDS for SQL Server 인스턴스를 구성 합니다. **Amazon RDS for SQL Server Management Studio** 를 시작 하 고 **서버** 를 마우스 오른쪽 단추로 클릭 한 다음 **속성** 을 선택 합니다. 목록에서 **연결** 을 선택하고, **이 서버에 대한 원격 연결 허용** 확인란을 선택합니다.

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="원격 연결 사용":::

    자세한 단계는 [원격 액세스 서버 구성 옵션 구성](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option)을 참조하세요.

2. **SQL Server 구성 관리자에 대해 Amazon RDS를** 시작 합니다. 원하는 인스턴스에 대 한 **Amazon RDS for SQL Server 네트워크 구성을** 확장 하 고 **MSSQLSERVER에 대 한 프로토콜** 을 선택 합니다. 오른쪽 창에 프로토콜이 표시됩니다. 마우스 오른쪽 단추로 **TCP/IP** 를 클릭하고 **사용** 을 선택하여 TCP/IP를 사용하도록 설정합니다.

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="TCP/IP 사용":::

    자세한 내용 및 TCP/IP 프로토콜을 사용하도록 설정하는 다른 방법은 [서버 네트워크 프로토콜 설정 또는 해제](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)를 참조하세요.

3. 동일한 창에서 **TCP/IP** 를 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 표시합니다. **TCP 포트** 를 적어 둡니다. 기본값은 **1433** 입니다.
5. 컴퓨터에 **Windows 방화벽에 대한 규칙** 을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다. 
6. **연결 확인**: 정규화 된 이름을 사용 하 여 amazon rds for SQL Server에 연결 하려면 다른 컴퓨터의 SQL Server Management Studio amazon rds를 사용 합니다. 예제는 `"<machine>.<domain>.corp.<company>.com,1433"`입니다.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
