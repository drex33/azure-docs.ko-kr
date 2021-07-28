---
title: Azure SQL Database에서 데이터 복사 및 변환
description: Azure Data Factory를 사용하여 Azure SQL Database 간에 데이터를 복사하고 Azure SQL Database에서 데이터를 변환하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: e72b1c0edf110dc680d76c4451f45dec708467cb
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109487532"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory 버전을 선택하세요."]
>
> - [버전 1](v1/data-factory-azure-sql-connector.md)
> - [현재 버전](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database 간에 데이터를 복사하고 Data Flow를 사용하여 Azure SQL Database에서 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure SQL Database 커넥터는 다음과 같은 작업에서 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md) 테이블을 사용하여 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 작업의 경우, 이 Azure SQL Database 커넥터는 다음 함수를 지원합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 쿼리 또는 저장 프로시저를 사용하여 데이터 검색(원본) Azure SQL Database 원본에서 병렬 복사를 선택할 수도 있습니다. 자세한 내용은 [SQL Database에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요.
- 싱크로서 원본 스키마에 대상 테이블이 존재하지 않는 경우 자동으로 만듭니다. 복사 시 사용자 지정 논리를 사용하여 테이블에 데이터를 추가하거나 저장된 프로시저를 호출합니다.

서버가 일시 중지될 때 Azure SQL Database [서버리스 계층](../azure-sql/database/serverless-tier-overview.md)을 사용하는 경우 자동 다시 시작이 준비될 때까지 기다리는 대신 작업 실행이 실패합니다. 활동 다시 시도를 추가하거나 추가 활동을 연결하여 서버가 실제 실행 시 라이브 상태를 유지할 수 있습니다.

>[!NOTE]
> 현재 이 커넥터는 Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 지원하지 않습니다. 이 문제를 해결하려면 자체 호스팅 통합 런타임을 통해 [일반 ODBC 커넥터](connector-odbc.md)와 SQL Server ODBC 드라이버를 사용할 수 있습니다. 자세한 내용은 [Always Encrypted 사용](#using-always-encrypted) 섹션을 참조하세요. 

> [!IMPORTANT]
> Azure Integration Runtime을 사용하여 데이터를 복사하는 경우, Azure 서비스가 서버에 액세스할 수 있도록 [서버 수준 방화벽 규칙](../azure-sql/database/firewall-configure.md)을 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우 적절한 IP 범위를 허용하도록 방화벽을 구성합니다. 이 범위에는 Azure SQL Database에 연결하는 데 사용되는 머신 IP가 포함됩니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database 커넥터와 관련된 Azure Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureSqlDatabase** 로 설정해야 합니다. | 예 |
| connectionString | Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 **connectionString** 속성에 대해 지정합니다. <br/>Azure Key Vault에서 암호나 서비스 주체 키를 입력할 수도 있습니다. SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 다음에 나오는 JSON 예제를 참조하고 [Azure Key Vault에 로그인 정보를 저장](store-credentials-in-key-vault.md)합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 Azure Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant | 애플리케이션이 상주하는 테넌트 정보(예: 도메인 이름 또는 테넌트 ID)를 지정합니다. Azure 포털의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure AD 애플리케이션이 등록된 Azure 클라우드 환경의 유형을 지정합니다. <br/> 허용되는 값은 **AzurePublic**, **AzureChina**, **AzureUsGovernment**, **AzureGermany** 입니다. 기본적으로 데이터 팩터리의 클라우드 환경이 사용됩니다. | 예 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. | 예 |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- [Azure AD 애플리케이션 토큰 인증: 서비스 주체](#service-principal-authentication)
- [Azure AD 애플리케이션 토큰 인증: Azure 리소스용 관리 ID](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요. `Pooling=false`는 **SHIR(자체 호스팅 Integration Runtime)** 유형의 연결된 서비스 설정에도 권장됩니다. 풀링 및 기타 연결 매개 변수는 연결된 서비스 만들기 양식의 **추가 연결 속성** 섹션에서 새 매개 변수 이름 및 값으로 추가할 수 있습니다.

### <a name="sql-authentication"></a>SQL 인증

**예제: SQL 인증 사용**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault의 암호:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 기반의 Azure AD 애플리케이션 토큰 인증을 사용하려면 다음 단계를 따르세요.

1. Azure Portal에서 [Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 아직 수행하지 않은 경우 Azure Portal에서 서버에 대해 [Azure Active Directory 관리자를 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이어야 하지만 서비스 주체일 수는 없습니다. 이 단계가 수행되면, 이후 단계에서 Azure AD ID를 사용하여 서비스 주체에 대한 포함된 데이터베이스 사용자를 만들 수 있습니다.

3. 서비스 주체에 대한 [포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) . ALTER ANY USER 이상의 사용 권한을 가진 Azure AD ID를 통해 SQL Server Management Studio와 같은 도구를 사용하여 데이터를 복사하려는 데이터베이스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 서비스 주체에 필요한 권한을 부여합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)를 참조하세요.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Azure Data Factory에서 Azure SQL Database 연결된 서비스를 구성합니다.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. Azure SQL Database 인증에 관리 ID를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터베이스에 액세스하고 해당 데이터베이스에 대해 데이터를 복사할 수 있습니다.

관리 ID 인증을 사용하려면 다음 단계를 수행합니다.

1. 아직 수행하지 않은 경우 Azure Portal에서 서버에 대해 [Azure Active Directory 관리자를 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

2. Azure Data Factory 관리 ID용 [포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities). ALTER ANY USER 이상의 사용 권한을 가진 Azure AD ID를 통해 SQL Server Management Studio와 같은 도구를 사용하여 데이터를 복사하려는 데이터베이스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL 사용자 및 다른 사용자에 대해 일반적으로 수행하는 것처럼 Data Factory 관리 ID에 필요한 권한을 부여합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)를 참조하세요.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Azure Data Factory에서 Azure SQL Database 연결된 서비스를 구성합니다.

**예제**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](./concepts-datasets-linked-services.md)를 참조하세요.

Azure SQL Database 데이터 세트에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **AzureSqlTable** 로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database가 원본인 경우

>[!TIP]
>데이터 분할을 사용하여 Azure SQL Database에서 데이터를 효율적으로 로드하려면 [SQL Database에서 병렬 복사](#parallel-copy-from-sql-database) 섹션에서 자세히 알아보세요.

Azure SQL Database에서 데이터를 복사하기 위해 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성을 **AzureSqlSource** 로 설정해야 합니다. "SqlSource" 형식은 이전 버전과의 호환성을 위해 계속 지원됩니다. | 예 |
| SqlReaderQuery | 이 속성은 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. | 예 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 예 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예 |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 다음과 같습니다. **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. 지정하지 않으면 데이터베이스의 기본 격리 수준이 사용됩니다. 자세한 내용은 [이 문서](/dotnet/api/system.data.isolationlevel)를 참조하세요. | 예 |
| partitionOptions | Azure SQL Database에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우 (즉, `None`은 안 됨), Azure SQL Database에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| ***`partitionSettings` 아래에서:*** | | |
| partitionColumnName | 원본 열의 이름을 **정수 또는 날짜/날짜/시간 유형** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` 또는 `datetimeoffset`) 병렬 복사를 위해 범위 분할에 사용됩니다. 지정하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfDynamicRangePartitionCondition `를 후크합니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionUpperBound | 파티션 범위 분할에 대한 파티션 열의 최댓값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.  <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 파티션 범위 분할에 대한 파티션 열의 최솟값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-sql-database) 섹션을 참조하세요. | 예 |

**다음 사항에 유의하세요.**

- **sqlReaderQuery** 가 **AzureSqlSource** 에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL Database 원본에 대해 이 쿼리를 실행합니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- 원본에서 저장 프로시저를 사용하여 데이터를 검색할 때 저장 프로시저가 다른 매개 변수 값이 전달되면 다른 스키마를 반환하도록 설계된 경우, UI에서 스키마를 가져오거나 자동 테이블을 만들어 SQL 데이터베이스로 데이터를 복사하면 예기치 않은 결과가 발생할 수 있습니다.

#### <a name="sql-query-example"></a>SQL 쿼리 예제

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>저장 프로시저 예제

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>저장 프로시저 정의

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database가 싱크인 경우

> [!TIP]
> [Azure SQL Database로 데이터를 로드하는 모범 사례](#best-practice-for-loading-data-into-azure-sql-database)에서 지원되는 쓰기 동작, 구성, 모범 사례에 대해 자세히 알아보세요.

Azure SQL Database로 데이터를 복사하기 위해 복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 sink의 **type** 속성은 **AzureSqlSink** 로 설정해야 합니다. "SqlSink" 형식은 이전 버전과의 호환성을 위해 계속 지원됩니다. | 예 |
| preCopyScript | Azure SQL Database에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예 |
| tableOption | 원본 스키마에 따라 존재하지 않는 경우 [싱크 테이블을 자동으로 만들지](copy-activity-overview.md#auto-create-sink-tables) 여부를 지정합니다. <br>싱크가 저장 프로시저를 지정하는 경우 자동 테이블 만들기가 지원되지 않습니다. <br>허용되는 값은 `none`(기본값) 또는 `autoCreate`입니다. | 예 |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출* 됩니다. 한 번만 실행되며 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다.<br>예시는 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요. | 예 |
| storedProcedureTableTypeParameterName |저장 프로시저에 지정된 테이블 형식의 매개 변수 이름입니다.  |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름입니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예 |
| writeBatchSize | *일괄 처리당* SQL 테이블에 삽입할 행 수입니다.<br/> 허용되는 값은 **정수**(행 수)입니다. 기본적으로 Azure Data Factory는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. | 예 |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/> 허용되는 값은 **시간 범위** 입니다. 예를 들어 "00:30:00"(30분)입니다. | 예 |
| disableMetricsCollection | Data Factory는 복사 성능 최적화 및 권장 사항을 위해 Azure SQL Database DTU와 같은 메트릭을 수집하여 추가 마스터 DB 액세스를 도입합니다. 이 동작에 관심이 있는 경우 `true`를 지정하여 해제합니다. | 아니요(기본값: `false`) |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예제 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

복사 작업의 Azure SQL Database 커넥터는 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-sql-server/connector-sql-partition-options.png)

분할된 복사본을 사용하도록 설정하면 복사 작업이 Azure SQL Database 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 `parallelCopies`를 4로 설정하는 경우, Data Factory는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 Azure SQL Database에서 데이터의 일부를 검색합니다.

특히 Azure SQL Database에서 대량의 데이터를 로드하는 경우 특별히 데이터 분할로 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일로 쓰는 것이 좋습니다(폴더 이름만 지정). 이 경우 단일 파일에 쓰는 것보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행 중에 Data Factory는 물리적 파티션을 자동으로 검색하여 파티션별로 데이터를 복사합니다. <br><br/>테이블에 물리적 파티션이 있는지 확인하려면 [이 쿼리](#sample-query-to-check-physical-partition)를 참조하세요. |
| 데이터 분할을 위해 실제 파티션을 사용하지 않지만 정수 또는 날짜/시간 열을 사용하여 대규모 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**(선택 사항): 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 인덱스 또는 기본 키 열이 사용됩니다.<br/>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블의 행을 필터링하려는 것이 아니라 테이블의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>예를 들어 파티션 열 "ID"의 값 범위가 1에서 100이고 하한을 20으로 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 Data Factory는 4개의 파티션(ID 범위: <=20, [21, 50], [51, 80] 및 >=81)으로 데이터를 검색합니다. |
| 실제 파티션이 없는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 또는 날짜/시간 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다.<br>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블에서 행을 필터링하려는 것이 아니라 쿼리 결과의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>실행하는 동안 Data Factory는 `?AdfRangePartitionColumnName`을 각 파티션의 실제 열 이름과 값 범위로 바꾸고 Azure SQL Database로 보냅니다. <br>예를 들어 파티션 열 "ID"의 값 범위가 1에서 100이고 하한을 20으로 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 Data Factory는 4개의 파티션(ID 범위: <=20, [21, 50], [51, 80] 및 >=81)으로 데이터를 검색합니다. <br><br>다양한 시나리오에 대한 추가적인 샘플 쿼리는 다음과 같습니다.<br> 1. 전체 테이블 쿼리: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. 열을 선택하고 where 절 필터를 추가하여 테이블 쿼리: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. 하위 쿼리를 사용하여 쿼리: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. 하위 쿼리에 파티션을 사용하여 쿼리: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

파티션 옵션을 사용하여 데이터를 로드하는 모범 사례:

1. 데이터 기울이기를 방지하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택합니다. 
2. 테이블에 내장 파티션이 있는 경우 파티션 옵션 "테이블의 물리적 파티션"을 사용하여 성능을 향상시킵니다.    
3. Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 대규모 "[DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)"(>4)를 설정하여 더 많은 컴퓨팅 리소스를 활용할 수 있습니다. 해당하는 시나리오를 확인합니다.
4. "[복사 병렬성 정도](copy-activity-performance-features.md#parallel-copy)"는 파티션 번호를 제어합니다. 이 번호를 너무 크게 설정하면 성능이 저하될 수 있습니다. 이 번호를(DIU 또는 자체 호스팅 IR 노드 수) * (2~4)로 설정하는 것이 좋습니다.

**예: 물리적 파티션이 있는 대규모 테이블에서 전체 로드**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "AzureSqlSource",
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

![SQL 쿼리 결과](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL Database에 데이터를 로드하는 모범 사례

Azure SQL Database로 데이터를 복사하는 경우 다른 쓰기 동작이 필요할 수 있습니다.

- [추가](#append-data): 내 원본 데이터에 새 레코드만 있습니다.
- [Upsert](#upsert-data): 내 원본 데이터에 삽입과 업데이트가 모두 있습니다.
- [덮어쓰기](#overwrite-the-entire-table): 매번 전체 차원 테이블을 다시 로드합니다.
- [사용자 지정 논리를 사용하여 작성](#write-data-with-custom-logic): 대상 테이블에 최종 삽입하기 전에 추가 처리가 필요합니다.

Azure Data Factory에서 구성하는 방법 및 모범 사례는 각 섹션을 참조하세요.

### <a name="append-data"></a>데이터 추가

데이터 추가는 Azure SQL Database 싱크 커넥터의 기본 동작입니다. Azure Data Factory는 대량 삽입을 수행하여 테이블에 효율적으로 쓰기 작업을 수행합니다. 이에 따라 복사 작업에 원본 및 싱크를 구성할 수 있습니다.

### <a name="upsert-data"></a>데이터 Upsert

**옵션 1:** 많은 양의 데이터를 복사하는 경우 복사 작업을 사용하여 준비 테이블에 모든 레코드를 대량 로드한 다음 저장 프로시저 작업을 실행하여 한 번에 [MERGE](/sql/t-sql/statements/merge-transact-sql) 또는 INSERT/UPDATE 문을 적용할 수 있습니다. 

현재 복사 작업은 데이터를 데이터베이스 임시 테이블로 로드하는 것을 기본적으로 지원하지 않습니다. 여러 작업의 조합을 사용하여 이를 설정하는 고급 방식은 [Azure SQL Database 대량 Upsert 최적화 시나리오](https://github.com/scoriani/azuresqlbulkupsert)를 참조하세요. 다음은 영구 테이블을 준비 프로세스로 사용하는 샘플을 보여 줍니다.

예를 들어 Azure Data Factory에서 **저장 프로시저 작업** 과 연결된 **복사 작업** 을 사용하여 파이프라인을 만들 수 있습니다. 복사 작업은 원본 저장소에서 Azure SQL Database 준비 테이블(예: 데이터 세트의 테이블 이름 **UpsertStagingTable**)로 데이터를 복사합니다. 그런 다음 저장 프로시저 작업은 저장 프로시저를 호출하여 준비 테이블의 원본 데이터를 대상 테이블에 통합하고 준비 테이블을 정리합니다.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

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

**옵션 3:** 기본 제공 삽입/upsert/업데이트 방법을 제공하는 [매핑 데이터 흐름](#sink-transformation)을 사용할 수 있습니다.

### <a name="overwrite-the-entire-table"></a>전체 테이블 덮어쓰기

복사 작업 싱크에서 **preCopyScript** 속성을 구성할 수 있습니다. 이 경우, 실행되는 각 복사 작업에 대해 Azure Data Factory는 스크립트를 먼저 실행합니다. 그런 다음, 복사를 실행하여 데이터를 삽입합니다. 예를 들어 최신 데이터를 사용하여 전체 테이블을 덮어쓰려면 원본에서 새 데이터를 대량으로 로드하기 전에 먼저 스크립트를 지정하여 모든 레코드를 삭제합니다.

### <a name="write-data-with-custom-logic"></a>사용자 지정 논리를 사용하여 데이터 작성

사용자 지정 논리를 사용하여 데이터를 작성하는 단계는 [데이터 Upsert](#upsert-data) 섹션의 설명과 유사합니다. 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용해야 하는 경우, 준비 테이블에 로드한 다음 저장 프로시저 작업을 호출하거나 복사 작업 싱크에서 저장 프로시저를 호출하여 데이터를 적용하거나 매핑 데이터 흐름을 사용할 수 있습니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

Azure SQL Database로 데이터를 복사하는 경우, 원본 테이블의 각 일괄 처리에서 추가 매개 변수를 사용하여 사용자 지정 저장 프로시저를 구성하고 호출할 수도 있습니다. 저장 프로시저 기능은 [테이블 반환 매개 변수](/dotnet/framework/data/adonet/sql/table-valued-parameters)을 활용합니다.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용하려는 경우를 예로 들 수 있습니다. 추가 처리 예시로는 열을 병합하고, 추가 값을 조회하고, 둘 이상의 테이블에 삽입하려는 경우가 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database의 테이블에 upsert(업데이트/삽입)를 수행하는 방법을 보여 줍니다. 입력 데이터와 싱크 **Marketing** 테이블에 각기 **ProfileID**, **State** 및 **Category** 의 세 열이 있다고 가정합니다. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 “ProductA”라는 특정 카테고리에만 적용합니다.

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

3. Azure Data Factory에서 다음과 같이 복사 작업의 **SQL 싱크** 섹션을 정의합니다.

    ```json
    "sink": {
        "type": "AzureSqlSink",
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

매핑 데이터 흐름에서 데이터를 변환하는 경우 Azure SQL Database에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요.

### <a name="source-transformation"></a>원본 변환

Azure SQL Database에 특정한 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다.

**입력:** 원본을 테이블에 표시할지 선택하거나(```Select * from <table-name>```와 동일) 사용자 지정 SQL 쿼리를 입력합니다.

**쿼리**: 입력 필드에서 쿼리를 선택하는 경우에는 원본에 대한 SQL 쿼리를 입력합니다. 이렇게 설정하면 데이터 세트에서 선택한 모든 테이블이 재정의됩니다. **Order By** 절은 여기서 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 테이블을 반환하는 SQL의 UDF입니다. 이 쿼리는 데이터 흐름에서 사용할 수 있는 원본 테이블을 생성합니다. 쿼리를 사용하는 것은 테스트 또는 조회를 위해 행을 줄이는 좋은 방법이기도 합니다.

**저장 프로시저**: 원본 데이터베이스에서 실행되는 저장 프로시저에서 프로젝션 및 원본 데이터를 생성하려면 이 옵션을 선택합니다. 스키마, 프로시저 이름 및 매개 변수를 입력하거나, 새로 고침을 클릭하여 ADF에 스키마 및 프로시저 이름을 검색할 수 있습니다. 그런 다음, 가져오기를 클릭하여 ``@paraName`` 형식을 사용하여 모든 프로시저 매개 변수를 가져올 수 있습니다.

![저장 프로시저](media/data-flow/stored-procedure-2.png "저장 프로시저")

- SQL 예제: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- 매개 변수가 있는 SQL 예제: ``"select * from {$tablename} where orderyear > {$year}"``

**일괄 처리 크기**: 일괄 처리 크기를 입력하여 대량 데이터를 읽기로 청크합니다.

**격리 수준**: 매핑 데이터 흐름에서 SQL 원본의 기본값은 커밋되지 않은 읽기입니다. 여기에서 격리 수준을 다음 값 중 하나로 변경할 수 있습니다.

- 커밋된 읽기
- 커밋되지 않은 읽기
- 반복 가능한 읽기
- 직렬화 가능
- 없음(격리 수준 무시)

![격리 수준](media/data-flow/isolationlevel.png "격리 수준")

### <a name="sink-transformation"></a>싱크 변환

Azure SQL Database 관련 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 메서드:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 행 변경 변환이 필요합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.

![키 열](media/data-flow/keycolumn.png "키 열")

여기에서 키로 선택한 열 이름은 ADF에서 후속 업데이트, upsert, 삭제의 일부로 사용됩니다. 따라서 싱크 매핑에 있는 열을 선택해야 합니다. 키 열에 값을 쓰지 않으려면 “키 열 작성 건너뛰기”를 클릭합니다.

대상 Azure SQL Database 테이블을 업데이트하는 데 사용되는 키 열을 매개 변수화할 수 있습니다. 복합 키에 대한 열이 여러 개 있는 경우 "사용자 지정 식"을 클릭합니다. 그러면 복합 키에 대한 열 이름이 포함된 문자열 배열을 포함할 수 있는 ADF 데이터 흐름 식 언어를 사용하여 동적 콘텐츠를 추가할 수 있습니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.

- 없음: 테이블에 대한 작업이 수행되지 않습니다.
- 다시 만들기: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.
- 자르기: 대상 테이블의 모든 행이 제거됩니다.

**일괄 처리 크기**: 각 버킷에 작성되는 행 수를 제어합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다.

**TempDB 사용:** 기본적으로 Data Factory는 전역 임시 테이블을 사용하여 로드 프로세스의 일부로 데이터를 저장합니다. 또는 "TempDB 사용" 옵션을 선택 취소하고 대신 이 싱크에 사용되는 데이터베이스에 있는 사용자 데이터베이스에 임시 보관 테이블을 저장할 것을 Data Factory에 요청할 수 있습니다.

![임시 DB 사용](media/data-flow/tempdb.png "임시 DB 사용")

**사전 및 사후 SQL 스크립트**: 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 입력합니다.

![사전 및 사후 SQL 처리 스크립트](media/data-flow/prepost1.png "SQL 처리 스크립트")

### <a name="error-row-handling"></a>오류 행 처리

Azure SQL DB에 쓸 때 대상에서 설정된 제약 조건으로 인해 특정 데이터 행이 실패할 수 있습니다. 일반적인 오류는 다음과 같습니다.

*    테이블에서 문자열이나 이진 데이터는 잘립니다.
*    열에 NULL 값을 삽입할 수 없습니다.
*    INSERT 문이 CHECK 제약 조건과 충돌합니다.

기본적으로 발생하는 첫 번째 오류에서 데이터 흐름 실행이 실패합니다. 개별 행에 오류가 있는 경우에도 데이터 흐름이 완료될 수 있도록 **오류 발생 시 계속** 을 선택할 수 있습니다. Azure Data Factory는 이러한 오류 행을 처리하는 다양한 옵션을 제공합니다.

**트랜잭션 커밋:** 데이터를 단일 트랜잭션으로 쓸지 또는 일괄로 쓸지를 선택합니다. 단일 트랜잭션은 더 나쁜 성능을 제공하지만 트랜잭션이 완료될 때까지 기록된 데이터가 다른 사용자에게 표시되지 않습니다.  

**거부된 데이터 출력:** 사용하도록 설정된 경우 선택한 Azure Blob Storage 또는 선택한 Azure Data Lake Storage Gen2 계정의 csv 파일에 오류 행을 출력할 수 있습니다. 이렇게 하면 세 개의 추가 열, 즉 INSERT 또는 UPDATE와 같은 SQL 작업, 데이터 흐름 오류 코드 및 오류 메시지를 포함하는 오류 행이 기록됩니다.

**오류 발생 시 성공 보고:** 사용하도록 설정된 경우 오류 행이 있는 경우에도 데이터 흐름이 성공으로 표시됩니다. 

![오류 행 처리](media/data-flow/sql-error-row-handling.png "오류 행 처리")


## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

Azure SQL Database 간에 데이터를 복사하는 경우, Azure SQL Database 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 복사 작업은 현재 최대 28자리의 데이터를 지원합니다. 28보다 큰 자릿수의 데이터가 있는 경우 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요.

## <a name="using-always-encrypted"></a>Always Encrypted 사용

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 Azure SQL Database 간에 데이터를 복사하는 경우, 자체 호스팅 통합 런타임을 통해 [일반 ODBC 커넥터](connector-odbc.md) 및 SQL Server ODBC 드라이버를 사용합니다. 이 Azure SQL Database 커넥터는 현재 Always Encrypted를 지원하지 않습니다. 

더 구체적으로 살펴보면 다음과 같습니다.

1. 자체 호스팅 통합 런타임이 없는 경우에는 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.

2. [여기](/sql/connect/odbc/download-odbc-driver-for-sql-server)에서 SQL Server용 64비트 ODBC 드라이버를 다운로드하여 통합 런타임 머신에 설치합니다. 이 드라이버를 사용하는 방법에 대한 자세한 내용은 [SQL Server용 ODBC 드라이버를 통해 Always Encrypted 사용](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)을 참조하세요.

3. ODBC 유형을 사용하여 연결된 서비스를 만들어 SQL 데이터베이스에 연결하려면 다음 샘플을 참조하세요.

    - **SQL 인증** 을 사용하려면 아래와 같이 ODBC 연결 문자열을 지정하고 **기본** 인증을 선택하여 사용자 이름과 암호를 설정합니다.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Azure 가상 머신에서 자체 호스팅 통합 런타임을 실행하는 경우 Azure VM의 ID로 **관리 ID 인증** 을 사용할 수 있습니다.

        1. 동일한 [필수 구성 요소](#managed-identity)에 따라 관리 ID에 대한 데이터베이스 사용자를 만들고 데이터베이스에서 적절한 역할을 부여합니다.
        2. 연결된 서비스에서 아래와 같이 ODBC 연결 문자열을 지정하고, 연결 문자열 자체가 나타내는 **익명** 인증을 선택합니다 `Authentication=ActiveDirectoryMsi`.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. 그에 따라 ODBC 형식으로 데이터 세트 및 복사 작업을 만듭니다. 자세한 내용은 [ODBC 커넥터](connector-odbc.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
