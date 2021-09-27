---
title: SQL Server 간 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인을 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스 간에 데이터를 복사하고 변환하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 42040653f432577457cea6e5325fe686878e9da3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820152"
---
# <a name="copy-and-transform-data-to-and-from-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 SQL Server 간 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory 버전을 선택하세요."]
> * [버전 1](v1/data-factory-sqlserver-connector.md)
> * [현재 버전](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업을 사용하여 SQL Server 데이터베이스 간에 데이터를 복사하고 Data Flow를 사용하여 SQL Server 데이터베이스에서 데이터를 변환하는 방법을 설명합니다.  자세한 내용은 [Azure Data Factory](introduction.md) 또는 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)의 소개 문서를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SQL Server 커넥터에서 지원하는 작업은 다음과 같습니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

데이터를 SQL Server 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 복사할 수 있습니다. 또는 데이터를 지원되는 모든 원본 데이터 저장소에서 SQL Server 데이터베이스로 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SQL Server 커넥터는 다음을 지원합니다.

- SQL Server 버전 2005 이상
- SQL 또는 Windows 인증을 사용한 데이터 복사
- 쿼리 또는 저장 프로시저를 사용하여 데이터 검색(원본) SQL Server 원본에서 병렬 복사를 선택할 수도 있습니다. 자세한 내용은 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요.
- 싱크 - 원본 스키마에 따라 대상 테이블을 자동으로 만듭니다(없는 경우). 복사하는 동안 사용자 지정 논리를 사용하여 데이터를 테이블에 추가하거나 저장된 프로시저를 호출합니다. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb)는 지원되지 않습니다.


## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-sql-server-linked-service-using-ui"></a>UI를 사용하여 SQL Server 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 SQL Server 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. SQL을 검색하고 SQL Server 커넥터를 선택합니다.

    :::image type="content" source="media/connector-sql-server/sql-server-connector.png" alt-text="SQL Server 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-sql-server/configure-sql-server-linked-service.png" alt-text="SQL Server 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 SQL Server 데이터베이스 커넥터와 관련된 Data Factory 및 Synapse 파이프라인 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SQL Server 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SqlServer** 로 설정해야 합니다. | 예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 **connectionString** 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>암호를 Azure Key Vault에 입력할 수도 있습니다. SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 다음에 나오는 JSON 예제를 참조하고 [Azure Key Vault에 로그인 정보를 저장](store-credentials-in-key-vault.md)합니다. |예 |
| userName |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. **domainname\\username** 을 예로 들 수 있습니다. |예 |
| password |username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장합니다. 또는 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수 있습니다. |예 |
| alwaysEncryptedSettings | 관리 ID 또는 서비스 주체를 사용하여 SQL Server에 저장된 중요한 데이터를 보호하기 위해 Always Encrypted를 사용하도록 설정하는 데 필요한 **alwaysencryptedsettings** 정보를 지정합니다. 자세한 내용은 표 다음에 나오는 JSON 예제와 [Always Encrypted 사용](#using-always-encrypted) 섹션을 참조하세요. 지정하지 않으면 기본 Always Encrypted 설정이 사용하도록 설정되지 않습니다. |예 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

> [!NOTE]
> SQL Server [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true)는 데이터 흐름에서 지원되지 않습니다. 

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

**예제 1: SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 데이터 세트에서 지원하는 속성 목록을 제공합니다.

SQL Server 데이터베이스 간에 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **SqlServerTable** 로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="sql-server-as-a-source"></a>원본으로 SQL Server 사용

>[!TIP]
>데이터 분할을 사용하여 SQL Server에서 데이터를 효율적으로 로드하려면 [SQL Database에서 병렬 복사](#parallel-copy-from-sql-database)에서 자세히 알아보세요.

SQL Server의 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource** 로 설정합니다. 복사 작업 source 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **SqlSource** 로 설정해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. |예 |
| sqlReaderStoredProcedureName |이 속성은 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |예 |
| storedProcedureParameters |저장 프로시저용 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 다음과 같습니다. **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. 지정하지 않으면 데이터베이스의 기본 격리 수준이 사용됩니다. 자세한 내용은 [이 문서](/dotnet/api/system.data.isolationlevel)를 참조하세요. | 예 |
| partitionOptions | SQL Server에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우(즉, `None`이 아님) SQL Server에서 데이터를 동시에 로드하는 병렬 처리 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정으로 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| ***`partitionSettings` 아래에서:*** | | |
| partitionColumnName | 원본 열의 이름을 **정수 또는 날짜/날짜/시간 유형** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` 또는 `datetimeoffset`) 병렬 복사를 위해 범위 분할에 사용됩니다. 지정하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfDynamicRangePartitionCondition `를 후크합니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionUpperBound | 파티션 범위 분할에 대한 파티션 열의 최댓값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.  <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 파티션 범위 분할에 대한 파티션 열의 최솟값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |

**다음 사항에 유의하세요.**

- **SqlSource** 에 대해 **sqlReaderQuery** 가 지정되면 복사 작업에서 SQL Server 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- 원본에서 저장 프로시저를 사용하여 데이터를 검색할 때 저장 프로시저가 다른 매개 변수 값이 전달되면 다른 스키마를 반환하도록 설계된 경우, UI에서 스키마를 가져오거나 자동 테이블을 만들어 SQL 데이터베이스로 데이터를 복사하면 예기치 않은 결과가 발생할 수 있습니다.

**예제: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

### <a name="sql-server-as-a-sink"></a>싱크로 SQL Server 사용

> [!TIP]
> [데이터를 SQL Server에 로드하는 모범 사례](#best-practice-for-loading-data-into-sql-server)에서 지원되는 쓰기 동작, 구성, 모범 사례에 대해 자세히 알아보세요.

SQL Server에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink** 로 설정합니다. 복사 작업 sink 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성을 **SqlSink** 로 설정해야 합니다. | 예 |
| preCopyScript |이 속성은 데이터를 SQL Server에 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |예 |
| tableOption | 원본 스키마에 따라 존재하지 않는 경우 [싱크 테이블을 자동으로 만들지](copy-activity-overview.md#auto-create-sink-tables) 여부를 지정합니다. 싱크가 저장 프로시저를 지정하는 경우 자동 테이블 만들기가 지원되지 않습니다. 허용되는 값은 `none`(기본값) 또는 `autoCreate`입니다. |예 |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출* 됩니다. 한 번만 실행되며 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다.<br>예시는 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요. | 예 |
| storedProcedureTableTypeParameterName |저장 프로시저에 지정된 테이블 형식의 매개 변수 이름입니다.  |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름입니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예 |
| writeBatchSize |*일괄 처리당* SQL 테이블에 삽입할 행 수입니다.<br/>허용되는 값은 행 수에 해당하는 정수입니다. 기본적으로 서비스는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. |예 |
| writeBatchTimeout |이 속성은 시간이 초과되기 전에 완료하려는 배치 삽입 작업의 대기 시간을 지정합니다.<br/>허용되는 값은 timespan입니다. 예를 들어 30분인 경우 "00:30:00"입니다. 값을 지정하지 않으면 시간 제한은 기본적으로 "02:00:00"으로 설정됩니다. |예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예제 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**예제 2: 복사 중에 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>SQL Database에서의 병렬 복사

복사 작업의 SQL Server 커넥터는 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/connector-sql-server/connector-sql-partition-options.png" alt-text="파티션 옵션의 스크린샷":::

분할된 복사본을 사용하도록 설정하면 복사 작업에서 SQL Server 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 `parallelCopies`의 값을 4로 설정하면 서비스는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 SQL Server에서 데이터의 일부를 검색합니다.

특히 SQL Server에서 대량의 데이터를 로드하는 경우 데이터 분할을 통해 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일로 쓰는 것이 좋습니다(폴더 이름만 지정). 이 경우 단일 파일에 쓰는 것보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. <br><br/>테이블에 물리적 파티션이 있는지 확인하려면 [이 쿼리](#sample-query-to-check-physical-partition)를 참조하세요. |
| 데이터 분할을 위해 실제 파티션을 사용하지 않지만 정수 또는 날짜/시간 열을 사용하여 대규모 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**(선택 사항): 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다.<br/>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블의 행을 필터링하려는 것이 아니라 테이블의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색하고 MIN 및 MAX 값에 따라 시간이 오래 걸릴 수 있습니다. 상한 및 하한을 제공하는 것이 좋습니다. <br><br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. |
| 실제 파티션이 없는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 또는 날짜/시간 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다.<br>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블에서 행을 필터링하려는 것이 아니라 쿼리 결과의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>실행하는 동안 서비스에서 `?AdfRangePartitionColumnName`을 각 파티션의 실제 열 이름과 값 범위로 바꾸고 SQL Server에 보냅니다. <br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. <br><br>다양한 시나리오에 대한 추가적인 샘플 쿼리는 다음과 같습니다.<br> 1. 전체 테이블 쿼리: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. 열을 선택하고 where 절 필터를 추가하여 테이블 쿼리: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. 하위 쿼리를 사용하여 쿼리: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. 하위 쿼리에 파티션을 사용하여 쿼리: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

파티션 옵션을 사용하여 데이터를 로드하는 모범 사례:

1. 데이터 기울이기를 방지하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택합니다. 
2. 테이블에 내장 파티션이 있는 경우 파티션 옵션 "테이블의 물리적 파티션"을 사용하여 성능을 향상시킵니다.    
3. Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 대규모 "[DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)"(>4)를 설정하여 더 많은 컴퓨팅 리소스를 활용할 수 있습니다. 해당하는 시나리오를 확인합니다.
4. "[복사 병렬성 정도](copy-activity-performance-features.md#parallel-copy)"는 파티션 번호를 제어합니다. 이 번호를 너무 크게 설정하면 성능이 저하될 수 있습니다. 이 번호를(DIU 또는 자체 호스팅 IR 노드 수) * (2~4)로 설정하는 것이 좋습니다.

**예: 물리적 파티션이 있는 대규모 테이블에서 전체 로드**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "SqlSource",
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

## <a name="best-practice-for-loading-data-into-sql-server"></a>데이터를 SQL Server에 로드하는 모범 사례

데이터를 SQL Server에 복사하는 경우 다른 쓰기 동작이 필요할 수 있습니다.

- [추가](#append-data): 내 원본 데이터에 새 레코드만 있습니다.
- [Upsert](#upsert-data): 내 원본 데이터에 삽입과 업데이트가 모두 있습니다.
- [덮어쓰기](#overwrite-the-entire-table): 매번 전체 차원 테이블을 다시 로드하려고 합니다.
- [사용자 지정 논리를 사용하여 작성](#write-data-with-custom-logic): 대상 테이블에 최종 삽입하기 전에 추가 처리가 필요합니다.

구성하는 방법 및 모범 사례는 각 섹션을 참조하세요.

### <a name="append-data"></a>데이터 추가

데이터 추가는 SQL Server 싱크 커넥터의 기본 동작입니다. 서비스는 테이블에 효율적으로 쓰기 위해 일괄 삽입을 수행합니다. 이에 따라 복사 작업에 원본 및 싱크를 구성할 수 있습니다.

### <a name="upsert-data"></a>데이터 Upsert

**옵션 1:** 많은 양의 데이터를 복사하는 경우 복사 작업을 사용하여 준비 테이블에 모든 레코드를 대량 로드한 다음 저장 프로시저 작업을 실행하여 한 번에 [MERGE](/sql/t-sql/statements/merge-transact-sql) 또는 INSERT/UPDATE 문을 적용할 수 있습니다. 

현재 복사 작업은 데이터를 데이터베이스 임시 테이블로 로드하는 것을 기본적으로 지원하지 않습니다. 여러 작업의 조합을 사용하여 이를 설정하는 고급 방법은 [SQL Database 대량 Upsert 최적화 시나리오](https://github.com/scoriani/azuresqlbulkupsert)를 참조하세요. 다음은 영구 테이블을 준비 프로세스로 사용하는 샘플을 보여 줍니다.

예를 들어 **저장 프로시저 작업** 과 연결된 **복사 작업** 이 있는 파이프라인을 만들 수 있습니다. 복사 작업은 원본 저장소의 데이터를 SQL Server 준비 테이블(예: **UpsertStagingTable**)을 데이터 세트의 테이블 이름으로 복사합니다. 그런 다음 저장 프로시저 작업은 저장 프로시저를 호출하여 준비 테이블의 원본 데이터를 대상 테이블에 통합하고 준비 테이블을 정리합니다.

:::image type="content" source="./media/connector-azure-sql-database/azure-sql-database-upsert.png" alt-text="Upsert":::

데이터베이스에서 이전 저장 프로시저 작업에서 가리키는 다음 예와 같이 병합 논리를 사용하여 저장 프로시저를 정의합니다. 대상이 **프로필 ID**, **상태**, **카테고리** 라는 세 개의 열이 있는 **마케팅** 테이블이라고 가정합니다. **프로필 ID** 열에 따라 upsert를 수행합니다.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**옵션 2:** [복사 작업 내에서 저장 프로시저를 호출](#invoke-a-stored-procedure-from-a-sql-sink)하도록 선택할 수 있습니다. 이 방법은 복사 작업에서 기본 방식으로 일괄 삽입을 사용하는 대신 원본 테이블에서 각 일괄 처리(`writeBatchSize` 속성에 의해 제어됨)를 실행합니다.

### <a name="overwrite-the-entire-table"></a>전체 테이블 덮어쓰기

**preCopyScript** 속성은 복사 작업 싱크에서 구성할 수 있습니다. 이 경우 서비스는 실행되는 각 복사 작업에 대해 스크립트를 먼저 실행합니다. 그런 다음, 복사를 실행하여 데이터를 삽입합니다. 예를 들어 최신 데이터를 사용하여 전체 테이블을 덮어쓰려면 원본에서 새 데이터를 대량으로 로드하기 전에 먼저 스크립트를 지정하여 모든 레코드를 삭제합니다.

### <a name="write-data-with-custom-logic"></a>사용자 지정 논리를 사용하여 데이터 작성

사용자 지정 논리를 사용하여 데이터를 작성하는 단계는 [데이터 Upsert](#upsert-data) 섹션의 설명과 유사합니다. 최종적으로 원본 데이터를 대상 테이블에 삽입하기 전에 추가 처리를 적용해야 하는 경우 준비 테이블에 로드한 다음, 저장 프로시저 작업을 호출하거나 복사 작업 싱크에서 저장 프로시저를 호출하여 데이터를 적용할 수 있습니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

데이터를 SQL Server에 복사하는 경우 원본 테이블의 각 일괄 처리에 대한 추가 매개 변수를 사용하여 사용자 지정 저장 프로시저를 구성하고 호출할 수도 있습니다. 저장 프로시저 기능은 [테이블 반환 매개 변수](/dotnet/framework/data/adonet/sql/table-valued-parameters)을 활용합니다.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용하려는 경우를 예로 들 수 있습니다. 추가 처리 예시로는 열을 병합하고, 추가 값을 조회하고, 둘 이상의 테이블에 삽입하려는 경우가 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 입력 데이터와 싱크 **Marketing** 테이블에 각기 **ProfileID**, **State** 및 **Category** 의 세 열이 있다고 가정합니다. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 “ProductA”라는 특정 카테고리에만 적용합니다.

1. 데이터베이스에서 **sqlWriterTableType** 과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마는 입력 데이터에서 반환된 스키마와 같아야 합니다.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 데이터베이스에서 **SqlWriterStoredProcedureName** 과 동일한 이름으로 저장 프로시저를 정의합니다. 지정된 원본의 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저에서 테이블 형식의 매개 변수 이름은 데이터 세트에 정의된 **tableName** 과 같아야 합니다.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. 복사 작업의 **SQL 싱크** 섹션을 다음과 같이 정의합니다.

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 SQL Server 데이터베이스에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요.

> [!NOTE]
> 온-프레미스 SQL Server에 액세스하려면 프라이빗 엔드포인트를 사용하여 Azure Data Factory 또는 Synapse 작업 영역 [관리되는 가상 네트워크](managed-virtual-network-private-endpoint.md)를 사용해야 합니다. 자세한 단계는 이 [자습서](tutorial-managed-virtual-network-on-premise-sql-server.md)를 참조하세요.

### <a name="source-transformation"></a>원본 변환

다음 표에는 SQL Server 원본에서 지원하는 속성이 나와 있습니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 테이블 | 테이블을 입력으로 선택하는 경우 데이터 흐름은 데이터 세트에 지정된 테이블에서 모든 데이터를 가져옵니다. | 예 | - |- |
| 쿼리 | 쿼리를 입력으로 선택하는 경우 원본에서 데이터를 가져올 SQL 쿼리를 지정하면 데이터 세트에서 지정한 테이블이 재정의됩니다. 쿼리를 사용하면 테스트 또는 조회를 위한 행을 줄일 수 있습니다.<br><br>**Order By** 절은 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 데이터 흐름에서 사용할 수 있는 테이블을 반환하는 SQL의 UDF입니다.<br>쿼리 예: `Select * from MyTable where customerId > 1000 and customerId < 2000`| 예 | String | Query |
| Batch 크기 | 일괄 처리 크기를 지정하여 대량 데이터를 읽기로 청크 처리합니다. | 예 | 정수 | batchSize |
| 격리 수준 | 다음 격리 수준 중 하나를 선택합니다.<br>- 커밋된 읽기<br>- 커밋되지 않은 읽기(기본값)<br>- 반복 읽기<br>- 직렬화 가능<br>- 없음(격리 수준 무시) | 예 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>직렬화 가능<br/>없음</small> |isolationLevel |

#### <a name="sql-server-source-script-example"></a>SQL Server 원본 스크립트 예제

SQL Server를 원본 유형으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLSource
```

### <a name="sink-transformation"></a>싱크 변환

다음 표에는 SQL Server 싱크에서 지원하는 속성이 나와 있습니다. 해당 속성은 **싱크 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 메서드 | 데이터베이스 대상에서 허용되는 작업을 지정합니다. 기본값은 삽입만 허용하는 것입니다.<br>행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 [행 변경 변환](data-flow-alter-row.md)이 필요합니다. | 예 | `true` 또는 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 키 열 | 업데이트, upsert, 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.<br>키로 선택한 열 이름은 후속 업데이트, upsert, 삭제의 일부로 사용됩니다. 따라서 싱크 매핑에 있는 열을 선택해야 합니다. | 예 | Array | 키 |
| 키 열 쓰기 건너뛰기 | 키 열에 값을 쓰지 않으려면 “키 열 작성 건너뛰기”를 선택합니다. | 예 | `true` 또는 `false` | skipKeyWrites |
| 테이블 작업 |쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.<br>- **None**: 테이블에 대한 작업이 수행되지 않습니다.<br>- **Recreate**: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.<br>- **Truncate**: 대상 테이블의 모든 행이 제거됩니다. | 예 | `true` 또는 `false` | recreate<br/>truncate |
| Batch 크기 | 각 일괄 처리에 작성되는 행 수를 지정합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다. | 예 | 정수 | batchSize |
| 사전 및 사후 SQL 스크립트 | 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 지정합니다. | 예 | String | preSQLs<br>postSQLs |

#### <a name="sql-server-sink-script-example"></a>SQL Server 싱크 스크립트 예제

SQL Server를 싱크 유형으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

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
    skipDuplicateMapOutputs: true) ~> SQLSink
```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server에 대한 데이터 형식 매핑

데이터를 SQL Server 간에 복사하는 경우 다음과 같은 SQL Server 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 매핑이 사용됩니다. Data Factory를 구현하는 Synapse 파이프라인은 동일한 매핑을 사용합니다.  복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SQL Server 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |부울 |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 복사 작업은 현재 최대 28자리의 데이터를 지원합니다. 자릿수가 28자리를 초과하는 데이터가 있으면 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요. 

## <a name="using-always-encrypted"></a>Always Encrypted 사용

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 SQL Server 간에 데이터를 복사하는 경우 다음 단계를 수행합니다. 

1. [Azure Key Vault](../key-vault/general/overview.md)에 [CMK(열 마스터 키)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true)를 저장합니다. [Azure Key Vault를 사용하여 Always Encrypted를 구성하는 방법](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)을 자세히 알아보세요.

2. [CMK(열 마스터 키)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true)가 저장된 키 자격 증명 모음에 액세스할 수 있는지 확인합니다. 필요한 권한은 이 [문서](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults)를 참조하세요.

3. 연결된 서비스를 만들어 SQL 데이터베이스에 연결하고 관리 ID 또는 서비스 주체를 사용하여 ‘Always Encrypted’ 기능을 사용하도록 설정합니다. 


>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)는 아래 시나리오를 지원합니다. 
>1. 원본 또는 싱크 데이터 저장소에서 관리 ID 또는 서비스 주체를 키 공급자 인증 유형으로 사용하고 있습니다.
>2. 원본 및 싱크 데이터 저장소 모두 관리 ID를 키 공급자 인증 유형으로 사용하고 있습니다.
>3. 원본 및 싱크 데이터 저장소 모두 키 공급자 인증 유형과 동일한 서비스 주체를 사용합니다.

## <a name="troubleshoot-connection-issues"></a>연결 문제 해결

1. 원격 연결을 허용하도록 SQL Server 인스턴스를 구성합니다. **SQL Server Management Studio** 를 시작하고, 마우스 오른쪽 단추로 **서버** 를 클릭하고, **속성** 을 선택합니다. 목록에서 **연결** 을 선택하고, **이 서버에 대한 원격 연결 허용** 확인란을 선택합니다.

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="원격 연결 사용":::

    자세한 단계는 [원격 액세스 서버 구성 옵션 구성](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option)을 참조하세요.

2. **SQL Server 구성 관리자** 를 시작합니다. 사용하려는 인스턴스에 대한 **SQL Server 네트워크 구성** 을 확장하고 **MSSQLSERVER용 프로토콜** 을 선택합니다. 오른쪽 창에 프로토콜이 표시됩니다. 마우스 오른쪽 단추로 **TCP/IP** 를 클릭하고 **사용** 을 선택하여 TCP/IP를 사용하도록 설정합니다.

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="TCP/IP 사용":::

    자세한 내용 및 TCP/IP 프로토콜을 사용하도록 설정하는 다른 방법은 [서버 네트워크 프로토콜 설정 또는 해제](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)를 참조하세요.

3. 동일한 창에서 **TCP/IP** 를 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 표시합니다. **TCP 포트** 를 적어 둡니다. 기본값은 **1433** 입니다.
5. 컴퓨터에 **Windows 방화벽에 대한 규칙** 을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다. 
6. **연결 확인**: 정규화된 이름을 사용하여 SQL Server에 연결하려면 다른 컴퓨터의 SQL Server Management Studio를 사용합니다. 예제는 `"<machine>.<domain>.corp.<company>.com,1433"`입니다.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
