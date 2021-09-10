---
title: Azure Synapse Analytics에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory를 사용하여 Azure Synapse Analytics로/에서 데이터를 복사하고 Azure Synapse Analytics에서 데이터를 변환하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 2131e74935ee831925dbe307a79c26909078e575
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313989"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory-or-synapse-pipelines"></a>Azure Data Factory 또는 Synapse 파이프라인을 사용하여 Azure Synapse Analytics에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [현재 버전](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 또는 Synapse 파이프라인의 복사 작업을 사용하여 Azure Synapse Analytics 간에 데이터를 복사하고 Data Flow를 사용하여 Azure Data Lake Storage Gen2에서 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Synapse Analytics 커넥터는 다음과 같은 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md) 테이블을 사용하여 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 작업의 경우 이 Azure Synapse Analytics 커넥터는 다음과 같은 기능을 지원합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다. 또한 Azure Synapse Analytics 원본에서 병렬 복사를 선택할 수도 있습니다. 자세한 내용은 [Azure Synapse Analytics의 병렬 복사](#parallel-copy-from-azure-synapse-analytics) 섹션을 참조하세요.
- 싱크로 [COPY 문](#use-copy-statement), [PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics) 또는 대량 삽입을 사용하여 데이터를 로드합니다. 더 나은 복사 성능을 위해 COPY 문 또는 PolyBase를 권장합니다. 커넥터는 원본 스키마를 기준으로 하지 않는 경우 DISTRIBUTION = ROUND_ROBIN을 사용하여 대상 테이블을 자동으로 만드는 것을 지원합니다.

> [!IMPORTANT]
> Azure Integration Runtime을 사용하여 데이터를 복사하는 경우, Azure 서비스가 [논리 SQL Server](../azure-sql/database/logical-servers.md)에 액세스할 수 있도록 [서버 수준 방화벽 규칙](../azure-sql/database/firewall-configure.md)을 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우 적절한 IP 범위를 허용하도록 방화벽을 구성합니다. 이 범위에는 Azure Synapse Analytics에 연결하는 데 사용되는 머신 IP가 포함됩니다.
## <a name="get-started"></a>시작하기

> [!TIP]
> 최상의 성능을 얻으려면 PolyBase 또는 COPY 문을 사용하여 Azure Synapse Analytics에 데이터를 로드합니다. [PolyBase를 사용하여 Azure Synapse Analytics에 데이터 로드](#use-polybase-to-load-data-into-azure-synapse-analytics) 및 [COPY 문을 사용하여 Azure Synapse Analytics에 데이터 로드](#use-copy-statement) 섹션에 자세한 내용이 있습니다. 사용 사례가 있는 연습은 [Azure Data Factory를 통해 Azure Synapse Analytics에 15분 이내 1TB 로드](load-azure-sql-data-warehouse.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-synapse-analytics-linked-service-using-ui"></a>UI를 사용하여 Azure Synapse Analytics 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Synapse Analytics 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Synapse를 검색하고 Azure Synapse Analytics 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-sql-data-warehouse/azure-sql-data-warehouse-connector.png" alt-text="Azure Synapse Analytics 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-sql-data-warehouse/configure-azure-sql-data-warehouse-linked-service.png" alt-text="Azure Synapse Analytics 연결된 서비스의 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Synapse Analytics 커넥터와 관련된 Data Factory 및 Synapse 파이프라인 엔터티를 정의하는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Synapse Analytics 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 속성            | Description                                                  | 필수                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | type 속성은 **AzureSqlDW** 로 설정해야 합니다.             | 예                                                          |
| connectionString    | **connectionString** 속성에 대해 Azure Synapse Analytics 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 안전하게 저장합니다. 암호/서비스 주체 키를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예                                                          |
| servicePrincipalId  | 애플리케이션의 클라이언트 ID를 지정합니다.                         | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure AD 애플리케이션이 등록된 Azure 클라우드 환경의 유형을 지정합니다. <br/> 허용되는 값은 `AzurePublic`, `AzureChina`, `AzureUsGovernment` 및 `AzureGermany`입니다. 기본적으로 데이터 팩터리 또는 Synapse 파이프라인의 클라우드 환경이 사용됩니다. | 예 |
| connectVia          | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예                                                           |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- Azure AD 애플리케이션 토큰 인증: [서비스 주체](#service-principal-authentication)
- Azure AD 애플리케이션 토큰 인증: [Azure 리소스에 대한 관리 ID](#managed-identity)

>[!TIP]
>UI에서 Azure Synapse **서버리스** SQL 풀에 대한 연결된 서비스를 만들 때 구독에서 검색하는 대신 "수동으로 입력"을 선택합니다.

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault의 암호:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. Azure Portal에서 **[Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

   - 애플리케이션 UI
   - 애플리케이션 키
   - 테넌트 ID

2. 아직 수행하지 않은 경우 Azure Portal에서 서버에 대해 **[Azure Active Directory 관리자를 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

3. 서비스 주체에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your_application_name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여** 합니다. 다음 코드를 실행하거나 [여기](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)서 추가 옵션을 참조합니다. PolyBase를 사용하여 데이터를 로드하려는 경우 [필요한 데이터베이스 권한](#required-database-permission)을 알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Azure Data Factory 또는 Synapse 작업 영역에서 **Azure Synapse Analytics 연결된 서비스를 구성** 합니다.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

리소스를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리 또는 Synapse 작업 영역을 연결할 수 있습니다. Azure Synapse Analytics 인증에 이 관리 ID를 사용할 수 있습니다. 지정된 리소스는 이 ID를 사용하여 데이터 웨어하우스의 데이터에 액세스하고 복사할 수 있습니다.

관리 ID 인증을 사용하려면 다음 단계를 수행합니다.

1. 아직 수행하지 않은 경우 Azure Portal에서 서버에 대해 **[Azure Active Directory 관리자를 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

2. 관리 ID에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your_resource_name] FROM EXTERNAL PROVIDER;
    ```

3. SQL 사용자 및 다른 사용자에 대해 일반적으로 수행하는 것처럼 **관리 ID에 필요한 권한을 부여** 합니다. 다음 코드를 실행하거나 [여기](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)서 추가 옵션을 참조합니다. PolyBase를 사용하여 데이터를 로드하려는 경우 [필요한 데이터베이스 권한](#required-database-permission)을 알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your_resource_name];
    ```

4. **Azure Synapse Analytics 연결된 서비스를 구성** 합니다.

**예:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요.

Azure Synapse Analytics 데이터 세트에 대해 지원되는 속성은 다음과 같습니다.

| 속성  | Description                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 세트의 **type** 속성을 **AzureSqlDWTable** 로 설정해야 합니다. | 예                         |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Synapse Analytics 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-synapse-analytics-as-the-source"></a>원본으로 Azure Synapse Analytics

>[!TIP]
>데이터 분할을 사용하여 Azure Synapse Analytics에서 데이터를 효율적으로 로드하려면 [Azure Synapse Analytics의 병렬 복사](#parallel-copy-from-azure-synapse-analytics)에서 자세히 알아보세요.

Azure Synapse Analytics에서 데이터를 복사하려면 복사 작업 원본의 **type** 속성을 **SqlDWSource** 로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성                     | Description                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 **type** 속성을 **SqlDWSource** 로 설정해야 합니다. | 예      |
| SqlReaderQuery               | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. | 예       |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 예       |
| storedProcedureParameters    | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예       |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 다음과 같습니다. **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. 지정하지 않으면 데이터베이스의 기본 격리 수준이 사용됩니다. 자세한 내용은 [system.data.isolationlevel](/dotnet/api/system.data.isolationlevel)을 참조하세요. | 예 |
| partitionOptions | Azure Synapse Analytics에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우 (즉, `None`은 안 됨), Azure Synapse Analytics에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| ***`partitionSettings` 아래에서:*** | | |
| partitionColumnName | 원본 열의 이름을 **정수 또는 날짜/날짜/시간 유형** (`int`, `smallint`, `bigint`, `date`, `smalldatetime`, `datetime`, `datetime2` 또는 `datetimeoffset`) 병렬 복사를 위해 범위 분할에 사용됩니다. 지정하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfDynamicRangePartitionCondition `를 후크합니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-azure-synapse-analytics) 섹션을 참조하세요. | 예 |
| partitionUpperBound | 파티션 범위 분할에 대한 파티션 열의 최댓값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.  <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-azure-synapse-analytics) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 파티션 범위 분할에 대한 파티션 열의 최솟값입니다. 이 값은 테이블의 행을 필터링하는 것이 아니라 파티션 stride를 결정하는 데 사용됩니다. 테이블 또는 쿼리 결과의 모든 행이 분할되고 복사됩니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예는 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-azure-synapse-analytics) 섹션을 참조하세요. | 예 |

**다음 사항에 유의하세요.**

- 원본에서 저장 프로시저를 사용하여 데이터를 검색할 때 저장 프로시저가 다른 매개 변수 값이 전달되면 다른 스키마를 반환하도록 설계된 경우, UI에서 스키마를 가져오거나 자동 테이블을 만들어 SQL 데이터베이스로 데이터를 복사하면 예기치 않은 결과가 발생할 수 있습니다.

#### <a name="example-using-sql-query"></a>예: SQL 쿼리 사용

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="example-using-stored-procedure"></a>예: 저장 프로시저 사용

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
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

#### <a name="sample-stored-procedure"></a>샘플 저장 프로시저:

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> 싱크로 Azure Synapse Analytics

Azure Data Factory 및 Synapse 파이프라인은 Azure Synapse Analytics에 데이터를 로드하는 세 가지 방법을 지원합니다.

- [COPY 문 사용](#use-copy-statement)
- [PolyBase 사용](#use-polybase-to-load-data-into-azure-synapse-analytics)
- 대량 삽입 사용

데이터를 로드하는 가장 빠르고 확장성 있는 방법은 [COPY 문](/sql/t-sql/statements/copy-into-transact-sql) 또는 [PolyBase](/sql/relational-databases/polybase/polybase-guide)을 통하는 것입니다.

데이터를 Azure Synapse Analytics에 복사하려면 복사 작업의 싱크 유형을 **SqlDWSink** 로 설정합니다. 복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 작업 싱크의 **type** 속성은 **SqlDWSink** 로 설정해야 합니다. | 예                                           |
| allowPolyBase     | PolyBase를 사용하여 Azure Synapse Analytics에 데이터를 로드할지 여부를 나타냅니다. `allowCopyCommand` 및 `allowPolyBase` 모두 true일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure Synapse Analytics로 데이터 로드](#use-polybase-to-load-data-into-azure-synapse-analytics) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아니요.<br/>PolyBase를 사용하는 경우 적용합니다.     |
| polyBaseSettings  | `allowPolybase` 속성이 **true** 로 설정된 경우 지정될 수 있는 속성의 그룹입니다. | 아니요.<br/>PolyBase를 사용하는 경우 적용합니다. |
| allowCopyCommand | [COPY 문](/sql/t-sql/statements/copy-into-transact-sql)을 사용하여 Azure Synapse Analytics로 데이터를 로드할지 여부를 나타냅니다. `allowCopyCommand` 및 `allowPolyBase` 모두 true일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [COPY 문을 사용하여 Azure Synapse Analytics로 데이터 로드](#use-copy-statement) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아니요.<br>COPY를 사용하는 경우 적용합니다. |
| copyCommandSettings | `allowCopyCommand` 속성이 TRUE로 설정된 경우 지정될 수 있는 속성의 그룹입니다. | 아니요.<br/>COPY를 사용하는 경우 적용합니다. |
| writeBatchSize    | **일괄 처리당** SQL 테이블에 삽입할 행 수입니다.<br/><br/>허용되는 값은 **정수**(행 수)입니다. 기본적으로 서비스는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. | 아니요.<br/>대량 삽입을 사용하는 경우 적용합니다.     |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/><br/>허용되는 값은 **시간 범위** 입니다. 예제: "00:30:00"(30분). | 아니요.<br/>대량 삽입을 사용하는 경우 적용합니다.        |
| preCopyScript     | 각 실행에서 Azure Synapse Analytics에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예                                            |
| tableOption | 원본 스키마에 따라 존재하지 않는 경우 [싱크 테이블을 자동으로 만들지](copy-activity-overview.md#auto-create-sink-tables) 여부를 지정합니다. 허용되는 값은 `none`(기본값) 또는 `autoCreate`입니다. |예 |
| disableMetricsCollection | 이 서비스는 복사 성능 최적화 및 권장 사항을 위해 Azure Synapse Analytics DWU와 같은 메트릭을 수집하여 추가 마스터 DB 액세스를 도입합니다. 이 동작에 관심이 있는 경우 `true`를 지정하여 해제합니다. | 아니요(기본값: `false`) |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

#### <a name="azure-synapse-analytics-sink-example"></a>Azure Synapse Analytics 싱크 예

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="parallel-copy-from-azure-synapse-analytics"></a>Azure Synapse Analytics에서 병렬 복사

복사 작업의 Azure Synapse Analytics 커넥터는 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-sql-server/connector-sql-partition-options.png)

분할된 복사본을 사용하도록 설정하면 복사 작업이 Azure Synapse Analytics 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 `parallelCopies`를 4로 설정하는 경우, 이 서비스는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 Azure Synapse Analytics에서 데이터의 일부를 검색합니다.

특히 Azure Synapse Analytics에서 대량의 데이터를 로드하는 경우 특별히 데이터 분할로 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일로 쓰는 것이 좋습니다(폴더 이름만 지정). 이 경우 단일 파일에 쓰는 것보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. <br><br/>테이블에 물리적 파티션이 있는지 확인하려면 [이 쿼리](#sample-query-to-check-physical-partition)를 참조하세요. |
| 데이터 분할을 위해 실제 파티션을 사용하지 않지만 정수 또는 날짜/시간 열을 사용하여 대규모 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**(선택 사항): 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 인덱스 또는 기본 키 열이 사용됩니다.<br/>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블의 행을 필터링하려는 것이 아니라 테이블의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. |
| 실제 파티션이 없는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 또는 날짜/시간 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다.<br>**파티션 상한** 및 **파티션 하한**(선택 사항): 파티션 stride를 결정하려는지를 지정합니다. 테이블에서 행을 필터링하려는 것이 아니라 쿼리 결과의 모든 행을 분할 및 복사합니다. 지정하지 않으면 복사 작업에서 값을 자동으로 검색합니다.<br><br>실행하는 동안 이 서비스는 `?AdfRangePartitionColumnName`을 각 파티션의 실제 열 이름과 값 범위로 바꾸고 Azure Synapse Analytics로 보냅니다. <br>예를 들어 "ID" 파티션 열의 값 범위가 1~100이고 하한을 20으로 설정하고 상한을 80으로 설정하고 병렬 복사를 4로 설정하면 서비스에서 4개의 파티션별로(각각 ID 범위: 20 이하, [21, 50], [51, 80] 및 81 이상) 데이터를 검색합니다. <br><br>다양한 시나리오에 대한 추가적인 샘플 쿼리는 다음과 같습니다.<br> 1. 전체 테이블 쿼리: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. 열을 선택하고 where 절 필터를 추가하여 테이블 쿼리: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. 하위 쿼리를 사용하여 쿼리: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. 하위 쿼리에 파티션을 사용하여 쿼리: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

파티션 옵션을 사용하여 데이터를 로드하는 모범 사례:

1. 데이터 기울이기를 방지하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택합니다. 
2. 테이블에 내장 파티션이 있는 경우 파티션 옵션 "테이블의 물리적 파티션"을 사용하여 성능을 향상시킵니다.
3. Azure Integration Runtime을 사용하여 데이터를 복사하는 경우 대규모 "[DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)"(>4)를 설정하여 더 많은 컴퓨팅 리소스를 활용할 수 있습니다. 해당하는 시나리오를 확인합니다.
4. "[복사 병렬성 정도](copy-activity-performance-features.md#parallel-copy)"는 파티션 번호를 제어합니다. 이 번호를 너무 크게 설정하면 성능이 저하될 수 있습니다. 이 번호를(DIU 또는 자체 호스팅 IR 노드 수) * (2~4)로 설정하는 것이 좋습니다.
5. Azure Synapse Analytics는 한 번에 최대 32개의 쿼리를 실행할 수 있으므로 "복사 병렬성 정도"를 너무 크게 설정하면 Synapse 제한 문제가 발생할 수 있습니다.

**예: 물리적 파티션이 있는 대규모 테이블에서 전체 로드**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "SqlDWSource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

테이블에 물리적 파티션이 있는 경우 "HasPartition"이 "예"로 표시됩니다.

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> COPY 문을 사용하여 Azure Synapse Analytics에 데이터 로드

[COPY 문](/sql/t-sql/statements/copy-into-transact-sql)을 사용하는 것은 처리량이 높은 Azure Synapse Analytics로 데이터를 로드하는 간단하고 유연한 방법입니다. 자세한 내용을 보려면 [COPY 문을 사용하여 데이터 대량 로드](../synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql.md)를 확인합니다.


- 원본 데이터가 **Azure Blob 또는 Azure Data Lake Storage Gen2** 이고 **형식이 COPY 문과 호환** 인 경우 복사 작업을 사용하여 COPY 문을 직접 호출하여 Azure Synapse Analytics가 원본에서 데이터를 가져오도록 합니다. 자세한 내용은 **[COPY 문을 사용하여 직접 복사](#direct-copy-by-using-copy-statement)** 를 참조하세요.
- 원본 데이터 저장소와 형식이 COPY 문에서 원래 지원되지 않는 경우, 대신 **[COPY 문을 사용한 준비된 복사](#staged-copy-by-using-copy-statement)** 기능을 사용합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 자동으로 데이터를 COPY 문 호환 형식으로 변환하고 Azure Blob Storage에 데이터를 저장한 다음 COPY 문을 호출하여 데이터를 Azure Synapse Analytics에 로드합니다.

>[!TIP]
>Azure Integration Runtime과 함께 COPY 문을 사용할 때 효과적인 [DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)는 항상 2입니다. DIU 조정은 스토리지에서 데이터를 로드하는 것이 Azure Synapse 엔진에 의해 구동되므로 성능에 영향을 주지 않습니다.

### <a name="direct-copy-by-using-copy-statement"></a>COPY 문을 사용하여 직접 복사

Azure Synapse Analytics COPY 문은 Azure Blob, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우, COPY 문을 사용하여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사합니다. 그렇지 않은 경우, [COPY 문을 사용한 준비된 복사](#staged-copy-by-using-copy-statement)를 사용합니다. 이 서비스는 설정을 확인하고 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

1. **원본 연결된 서비스 및 형식** 은 다음과 같은 유형 및 인증 방법을 사용합니다.

    | 지원되는 원본 데이터 저장소 형식                             | 지원되는 형식           | 지원되는 원본 인증 유형                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [구분된 텍스트](format-delimited-text.md)             | 계정 키 인증, 공유 액세스 서명 인증, 서비스 주체 인증, 관리 ID 인증 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 계정 키 인증, 공유 액세스 서명 인증 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 계정 키 인증, 공유 액세스 서명 인증 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [구분된 텍스트](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | 계정 키 인증, 서비스 주체 인증, 관리 ID 인증 |

    >[!IMPORTANT]
    >- 스토리지에 연결된 서비스에 관리 ID 인증을 사용하는 경우 각각 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity)에 필요한 구성을 알아봅니다.
    >- Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 '신뢰할 수 있는 Microsoft 서비스 허용'이 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요.

2. 형식 설정에는 다음이 포함됩니다.

   1. **Parquet** 의 경우: `compression`은 **압축 없음**, **Snappy** 또는 **``GZip``** 일 수 있습니다.
   2. **ORC** 의 경우: `compression`은 **압축 안 함**, **```zlib```** 또는 **Snappy** 일 수 있습니다.
   3. **구분 된 텍스트** 의 경우:
      1. `rowDelimiter`는 **단일 문자** 또는 " **\r\n**"으로 명시적으로 설정됩니다. 기본값은 지원되지 않습니다.
      2. `nullValue`는 기본값으로 남아 있거나 **빈 문자열**("")로 설정됩니다.
      3. `encodingName`는 기본값으로 남아 있거나 **utf-8 또는 utf-16** 으로 설정됩니다.
      4. `escapeChar`는 `quoteChar`와 동일해야 하며 비어 있지 않습니다.
      5. `skipLineCount`는 기본값으로 남아 있거나 0으로 설정됩니다.
      6. `compression`는 **압축 안 함** 또는 **``GZip``** 일 수 있습니다.

3. 원본이 폴더인 경우 복사 작업의 `recursive`를 true로 설정해야 하며 `wildcardFilename`는 `*` 또는 `*.*`이어야 합니다. 

4. `wildcardFolderPath`, `wildcardFilename`(`*` 또는 `*.*` 이외), `modifiedDateTimeStart`, `modifiedDateTimeEnd`, `prefix`, `enablePartitionDiscovery` 및 `additionalColumns`는 지정되지 않습니다.

복사 작업의 `allowCopyCommand`에서 다음 COPY 문 설정이 지원됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Azure Synapse Analytics의 각 대상 열에 대한 기본값을 지정합니다.  속성의 기본값은 데이터 웨어하우스의 기본 제약 조건 집합을 덮어쓰고 ID 열에는 기본값을 사용할 수 없습니다. | 예 |
| additionalOptions | [COPY 문](/sql/t-sql/statements/copy-into-transact-sql)의 "With" 절에서 직접 Azure Synapse Analytics COPY 문에 전달되는 추가 옵션입니다. COPY 문 요구 사항에 맞게 조정하는 데 필요한 값을 따옴표로 묶습니다. | 예 |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

### <a name="staged-copy-by-using-copy-statement"></a>COPY 문을 사용한 준비된 복사

원본 데이터가 COPY 문과 기본적으로 호환되지 않는 경우 중간 준비 Azure Blob 또는 Azure Data Lake Storage Gen2(Azure Premium Storage일 수 없음)를 통해 데이터 복사를 사용하도록 설정합니다. 이 경우, 이 서비스는 COPY 문의 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 변환합니다. 그런 다음, COPY 문을 호출하여 Azure Synapse Analytics에 데이터를 로드합니다. 마지막으로 스토리지에서 임시 데이터를 정리합니다. 스테이징을 통한 데이터 복사에 대한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 Azure Storage 계정을 중간 스토리지로 참조하는 **계정 키 또는 시스템 관리 ID 인증** 으로 [Azure Blob Storage에 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2에 연결된 서비스](connector-azure-data-lake-storage.md#linked-service-properties)를 만듭니다.

>[!IMPORTANT]
>- 스테이징하는 연결된 서비스에 관리 ID 인증을 사용하는 경우 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 각각에 필요한 구성을 알아봅니다.
>- 스테이징 Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 "신뢰할 수 있는 Microsoft 서비스 허용"이 사용하도록 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요. 

>[!IMPORTANT]
>스테이징하는 Azure Storage가 관리형 프라이빗 엔드포인트로 구성되고 스토리지 방화벽이 사용하도록 설정된 경우 관리 ID 인증을 사용하고 Synapse SQL Server에 스토리지 Blob 데이터 판독기 권한을 부여하여 COPY 문 로드 중에 스테이징된 파일에 액세스할 수 있도록 해야 합니다.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaCOPYstatement",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true
            },
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>PolyBase를 사용하여 Azure Synapse Analytics에 데이터 로드

[PolyBase](/sql/relational-databases/polybase/polybase-guide)를 사용하면 높은 처리량으로 대량 데이터를 Azure Synapse Analytics에 효율적으로 로드할 수 있습니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하면 처리량이 훨씬 증가합니다. 사용 사례가 있는 연습은 [Azure Synapse Analytics에 1TB 로드](v1/data-factory-load-sql-data-warehouse.md)를 참조하세요.

- 원본 데이터가 **Azure Blob, Azure Data Lake Storage Gen1 또는 Azure Data Lake Storage Gen2** 이고 **형식이 PolyBase와 호환** 인 경우 복사 작업을 사용하여 PolyBase를 직접 호출하여 Azure Synapse Analytics가 원본에서 데이터를 가져오도록 합니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-by-using-polybase)** 를 참조하세요.
- 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우, 대신 **[PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)** 기능을 사용합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 자동으로 데이터를 PolyBase 호환 형식으로 변환하고 Azure Blob Storage에 데이터를 저장한 다음 PolyBase를 호출하여 데이터를 Azure Synapse Analytics에 로드합니다.

> [!TIP]
> [PolyBase 사용에 대한 모범 사례](#best-practices-for-using-polybase)에 대해 자세히 알아봅니다. Azure Integration Runtime과 함께 PolyBase를 사용하는 경우 직접 또는 스테이징된 스토리지에서 Synapse까지의 효과적인 [DIU(데이터 통합 ​​단위)](copy-activity-performance-features.md#data-integration-units)는 항상 2입니다. DIU 조정은 스토리지에서 데이터를 로드하는 것이 Synapse 엔진에 의해 구동되므로 성능에 영향을 주지 않습니다.

복사 작업의 `polyBaseSettings`에서 다음 PolyBase 설정이 지원됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다.<br/><br/>[CREATE EXTERNAL TABLE(Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql)의 인수 섹션에서 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. <br/><br/>허용되는 값은 0(기본값), 1, 2 등입니다. | 예                                            |
| rejectType        | **rejectValue** 옵션이 리터럴 값인지 또는 백분율인지를 지정합니다.<br/><br/>허용되는 값은 **Value**(기본값) 및 **Percentage** 입니다. | 예                                            |
| rejectSampleValue | PolyBase가 거부된 행의 백분율을 다시 계산하기 전에 검색할 행 수를 결정합니다.<br/><br/>허용되는 값은 1, 2 등입니다. | **rejectType** 이 **percentage** 인 경우 예 |
| useTypeDefault    | PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다.<br><br> | 예                                            |

### <a name="direct-copy-by-using-polybase"></a>PolyBase를 사용한 직접 복사

Azure Synapse Analytics PolyBase는 Azure Blob, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우, PolyBase를 사용하여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사합니다. 조건을 충족하지 않는 경우, [PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)를 사용합니다.

> [!TIP]
> Azure Synapse Analytics로 데이터를 효율적으로 복사하려면 [Azure Data Factory를 통해 Azure Synapse Analytics와 함께 Data Lake Store를 사용할 때 데이터에서 통찰력을 더욱 쉽고 편리하게 찾을 수 있음](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse)에서 자세히 알아보세요.

조건을 충족하지 않는 경우, 이 서비스는 설정을 확인한 후 데이터 이동을 위해 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **원본 연결된 서비스** 는 다음과 같은 유형 및 인증 방법을 사용합니다.

    | 지원되는 원본 데이터 저장소 형식                             | 지원되는 원본 인증 유형                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 계정 키 인증, 관리 ID 인증 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 서비스 주체 인증                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 계정 키 인증, 관리 ID 인증 |

    >[!IMPORTANT]
    >- 스토리지에 연결된 서비스에 관리 ID 인증을 사용하는 경우 각각 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity)에 필요한 구성을 알아봅니다.
    >- Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 '신뢰할 수 있는 Microsoft 서비스 허용'이 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요.

2. **원본 데이터 형식** 은 다음 구성과 함께 **Parquet**, **ORC** 또는 **구분된 텍스트** 입니다.

   1. 폴더 경로는 와일드 카드 필터를 포함하지 않습니다.
   2. 파일 이름이 비어 있거나 단일 파일을 가리킵니다. 복사 작업에서 와일드 카드 파일 이름을 지정하는 경우 `*` 또는 `*.*`만 가능합니다.
   3. `rowDelimiter`는 **기본값**, **\n**, **\r\n** 또는 **\r** 입니다.
   4. `nullValue`는 기본값으로 남아 있거나 **빈 문자열**("")로 설정되며, `treatEmptyAsNull`는 기본값으로 남아 있거나 true로 설정됩니다.
   5. `encodingName`는 기본값으로 남아 있거나 **utf-8** 로 설정됩니다.
   6. `quoteChar`, `escapeChar` 및 `skipLineCount`는 지정되지 않습니다. PolyBase 지원은 `firstRowAsHeader`로 구성될 수 있는 헤더 행을 건너뜁니다.
   7. `compression`는 **압축 안 함**, **``GZip``** 또는 **Deflate** 일 수 있습니다.

3. 원본이 폴더인 경우 복사 작업의 `recursive`을 true로 설정해야 합니다.

4. `wildcardFolderPath` , `wildcardFilename`, `modifiedDateTimeStart`, `modifiedDateTimeEnd`, `prefix`, `enablePartitionDiscovery` 및 `additionalColumns`는 지정되지 않습니다.

>[!NOTE]
>원본이 폴더인 경우 PolyBase는 폴더와 모든 하위 폴더에서 파일을 검색하며, [여기 - LOCATION 인수](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2)에 설명된 대로 파일 이름이 밑줄(_) 또는 마침표(.)로 시작하는 파일에서 데이터를 검색하지 않습니다.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>PolyBase를 사용한 준비된 복사

원본 데이터가 PolyBase와 기본적으로 호환되지 않는 경우 중간 준비 Azure Blob 또는 Azure Data Lake Storage Gen2(Azure Premium Storage일 수 없음)를 통해 데이터 복사를 사용하도록 설정합니다. 이 경우, Azure Data Factory는 COPY 문의 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 변환합니다. 그런 다음 PolyBase를 호출하여 Azure Synapse Analytics에 데이터를 로드합니다. 마지막으로 스토리지에서 임시 데이터를 정리합니다. 스테이징을 통한 데이터 복사에 대한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 Azure Storage 계정을 중간 스토리지로 참조하는 **계정 키 또는 관리 ID 인증** 으로 [Azure Blob Storage에 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2에 연결된 서비스](connector-azure-data-lake-storage.md#linked-service-properties)를 만듭니다.

>[!IMPORTANT]
>- 스테이징하는 연결된 서비스에 관리 ID 인증을 사용하는 경우 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 각각에 필요한 구성을 알아봅니다.
>- 스테이징 Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 "신뢰할 수 있는 Microsoft 서비스 허용"이 사용하도록 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요. 

>[!IMPORTANT]
>스테이징하는 Azure Storage가 관리형 프라이빗 엔드포인트로 구성되고 스토리지 방화벽이 사용하도록 설정된 경우 관리 ID 인증을 사용하고 Synapse SQL Server에 스토리지 Blob 데이터 판독기 권한을 부여하여 PolyBase 로드 중에 스테이징된 파일에 액세스할 수 있도록 해야 합니다.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>PolyBase를 사용하는 모범 사례

다음 섹션에서는 [Azure Synapse Analytics에 대한 모범 사례](../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)에 설명된 사례 이외의 모범 사례를 제공합니다.

#### <a name="required-database-permission"></a>필수 데이터베이스 권한

PolyBase를 사용하려면 Azure Synapse Analytics에 데이터를 로드하는 사용자에게 대상 데이터베이스에 대한 ["CONTROL" 권한](/sql/relational-databases/security/permissions-database-engine)이 있어야 합니다. 한 가지 방법은 해당 사용자를 **db_owner** 역할의 구성원으로 추가하는 것입니다. [Azure Synapse Analytics 개요](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)에서 수행 방법을 알아봅니다.

#### <a name="row-size-and-data-type-limits"></a>행 크기 및 데이터 형식 한도

PolyBase는 1MB보다 작은 행으로 제한됩니다. VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)에 로드하는 데 사용할 수 없습니다. 자세한 내용은 [Azure Synapse Analytics 서비스 용량 제한](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)을 참조하세요.

원본 데이터에 1MB보다 큰 행이 있는 경우, 원본 테이블을 여러 개의 작은 테이블로 수직 분할하는 것이 좋습니다. 각 행의 최대 크기가 한도를 초과하지 않는지 확인합니다. 더 작은 테이블은 PolyBase를 사용하여 로드하고 Azure Synapse Analytics에 병합할 수 있습니다.

또는 이러한 광범위한 열이 포함된 데이터의 경우 "PolyBase 허용" 설정을 해제하여 비 PolyBase를 사용하고 데이터를 로드할 수 있습니다.

#### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics 리소스 클래스

가능한 최상의 처리량을 얻으려면 PolyBase를 통해 Azure Synapse Analytics에 데이터를 로드하는 사용자에게 더 큰 리소스 클래스를 할당합니다.

#### <a name="polybase-troubleshooting"></a>PolyBase 문제 해결

#### <a name="loading-to-decimal-column"></a>Decimal 열에 로드

원본 데이터가 텍스트 형식이나 기타 PolyBase 호환 저장소(준비된 복사 및 PolyBase 사용)에 있고 Azure Synapse Analytics Decimal 열에 로드할 빈 값을 포함하는 경우 다음 오류가 발생할 수 있습니다.

```output
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

이 솔루션은 복사 작업 싱크 -> PolyBase 설정에서 "**형식 기본값 사용**" 옵션(false로 설정)을 선택 취소하는 것입니다. "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)"는 PolyBase 네이티브 구성으로, PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.

#### <a name="check-the-tablename-property-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 tableName 속성 확인

다음 표에서는 JSON 데이터 세트의 **tableName** 속성을 지정하는 방법에 대한 예를 제공합니다. 스키마 및 테이블 이름의 여러 조합을 보여 줍니다.

| DB 스키마 | 테이블 이름 | **tableName** JSON 속성               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 또는 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 또는 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

다음 오류가 표시되는 경우, **tableName** 속성에 대해 지정한 값이 문제일 수 있습니다. **tableName** JSON 속성의 값을 지정하는 올바른 방법은 앞의 표를 참조하세요.

```output
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

#### <a name="columns-with-default-values"></a>기본값이 있는 열

현재, PolyBase 기능은 대상 테이블과 동일한 열 수만 허용합니다. 예를 들어, 네 개의 열이 있고 그 중 한 열이 기본값으로 정의된 테이블이 있다고 가정합니다. 이 경우, 입력 데이터에 네 개의 열이 있어야 합니다. 3열 입력 데이터 세트를 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

```output
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 값은 특별한 형태의 기본값입니다. 열이 Null을 허용하는 경우, 해당 열에 대한 Blob의 입력 데이터가 비어 있을 수 있습니다. 그러나 입력 데이터 세트에서는 누락할 수 없습니다. PolyBase는 Azure Synapse Analytics에서 누락된 값에 대해 NULL을 삽입합니다.

#### <a name="external-file-access-failed"></a>외부 파일 액세스 실패

다음 오류가 발생하면 관리 ID 인증을 사용하고 있고 Azure Synapse 작업 영역의 관리 ID에 대한 Storage Blob 데이터 판독기 권한을 부여했는지 확인합니다.

```output
Job failed due to reason: at Sink '[SinkName]': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External file access failed due to internal error: 'Error occurred while accessing HDFS: Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:\r\nHdfsBridge::isDirExist 
```

자세한 내용은 [작업 영역 생성 후 관리 ID에 권한 부여](../synapse-analytics/security/how-to-grant-workspace-managed-identity-permissions.md#grant-permissions-to-managed-identity-after-workspace-creation)를 참조하세요.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 Azure Synapse Analytics에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요.

### <a name="source-transformation"></a>원본 변환

Azure Synapse Analytics에 특정한 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다.

**입력** 원본을 테이블에 표시할지 선택하거나(```Select * from <table-name>```와 동일) 사용자 지정 SQL 쿼리를 입력합니다.

**스테이징 사용** Azure Synapse Analytics 원본이 있는 프로덕션 워크로드에서 이 옵션을 사용하는 것이 좋습니다. 파이프라인에서 Azure Synapse Analytics 원본을 사용하여 [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)을 실행하면 스테이징 위치 스토리지 계정을 입력하라는 메시지가 표시되며 이를 스테이징된 데이터 로드에 사용할 수 있습니다. Azure Synapse Analytics에서 데이터를 로드하는 가장 빠른 메커니즘입니다.

- 스토리지에 연결된 서비스에 관리 ID 인증을 사용하는 경우 각각 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity)에 필요한 구성을 알아봅니다.
- Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 '신뢰할 수 있는 Microsoft 서비스 허용'이 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요.
- Azure Synapse **서버리스** SQL 풀을 원본으로 사용하는 경우 스테이징 사용이 지원되지 않습니다.

**쿼리**: 입력 필드에서 쿼리를 선택하는 경우에는 원본에 대한 SQL 쿼리를 입력합니다. 이렇게 설정하면 데이터 세트에서 선택한 모든 테이블이 재정의됩니다. **Order By** 절은 여기서 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 테이블을 반환하는 SQL의 UDF입니다. 이 쿼리는 데이터 흐름에서 사용할 수 있는 원본 테이블을 생성합니다. 쿼리를 사용하는 것은 테스트 또는 조회를 위해 행을 줄이는 좋은 방법이기도 합니다.

SQL 예제: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**일괄 처리 크기**: 일괄 처리 크기를 입력하여 대량 데이터를 읽기로 청크합니다. 데이터 흐름에서 이 설정을 사용하여 Spark 열 형식 캐싱을 설정합니다. 이 필드는 비어 있는 경우 Spark 기본값을 사용하는 옵션 필드입니다.

**격리 수준**: 매핑 데이터 흐름에서 SQL 원본의 기본값은 커밋되지 않은 읽기입니다. 여기에서 격리 수준을 다음 값 중 하나로 변경할 수 있습니다.

- 커밋된 읽기
- 커밋되지 않은 읽기
- 반복 가능한 읽기
- 직렬화 가능
- 없음(격리 수준 무시)

![격리 수준](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>싱크 변환

Azure Synapse Analytics에 특정한 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 메서드:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 행 변경 변환이 필요합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.

- 없음: 테이블에 대한 작업이 수행되지 않습니다.
- 다시 만들기: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.
- 자르기: 대상 테이블의 모든 행이 제거됩니다.

**스테이징 사용:** 이렇게 하면 복사 명령을 사용하여 Azure Synapse Analytics SQL 풀에 로드할 수 있으며 대부분의 Synpase 싱크에 권장됩니다. 스테이징 스토리지는 [데이터 흐름 실행 작업](control-flow-execute-data-flow-activity.md)에서 구성됩니다. 

- 스토리지에 연결된 서비스에 관리 ID 인증을 사용하는 경우 각각 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity)에 필요한 구성을 알아봅니다.
- Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 '신뢰할 수 있는 Microsoft 서비스 허용'이 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요.

**일괄 처리 크기**: 각 버킷에 작성되는 행 수를 제어합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다.

**사전 및 사후 SQL 스크립트**: 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 입력합니다.

![사전 및 사후 SQL 처리 스크립트](media/data-flow/prepost1.png "SQL 처리 스크립트")

### <a name="error-row-handling"></a>오류 행 처리

Azure Synapse Analytics에 쓸 때 대상에서 설정된 제약 조건으로 인해 특정 데이터 행이 실패할 수 있습니다. 일반적인 오류는 다음과 같습니다.

*    테이블에서 문자열이나 이진 데이터는 잘립니다.
*    열에 NULL 값을 삽입할 수 없습니다.
*    값을 데이터 형식으로 변환하지 못했습니다.

기본적으로 발생하는 첫 번째 오류에서 데이터 흐름 실행이 실패합니다. 개별 행에 오류가 있는 경우에도 데이터 흐름이 완료될 수 있도록 **오류 발생 시 계속** 을 선택할 수 있습니다. 서비스는 이러한 오류 행을 처리하는 다양한 옵션을 제공합니다.

**트랜잭션 커밋:** 데이터를 단일 트랜잭션으로 쓸지 또는 일괄로 쓸지를 선택합니다. 단일 트랜잭션은 더 나은 성능을 제공하며 트랜잭션이 완료될 때까지 기록된 데이터가 다른 사용자에게 표시되지 않습니다. 일괄 처리 트랜잭션은 성능이 저하되지만 큰 데이터 세트에 대해 작동할 수 있습니다.

**거부된 데이터 출력:** 사용하도록 설정된 경우 선택한 Azure Blob Storage 또는 선택한 Azure Data Lake Storage Gen2 계정의 csv 파일에 오류 행을 출력할 수 있습니다. 이렇게 하면 세 개의 추가 열, 즉 INSERT 또는 UPDATE와 같은 SQL 작업, 데이터 흐름 오류 코드 및 오류 메시지를 포함하는 오류 행이 기록됩니다.

**오류 발생 시 성공 보고:** 사용하도록 설정된 경우 오류 행이 있는 경우에도 데이터 흐름이 성공으로 표시됩니다. 

:::image type="content" source="media/data-flow/sql-error-row-handling.png" alt-text="오류 행 처리를 보여주는 스크린샷" border="false":::

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요.

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 데이터 형식 매핑

Azure Synapse Analytics에서/로 데이터를 복사하는 경우, Azure Synapse Analytics 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. Synapse 파이프라인은 Azure Synapse 내에서 Azure Data Factory도 구현하므로 이러한 매핑은 Synapse 파이프라인을 사용하여 Azure Synapse Analytics 간에 데이터를 복사하는 경우에도 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

>[!TIP]
>Azure Synapse Analytics 지원 데이터 형식 및 지원되지 않는 데이터 형식에 대한 해결 방법에 관한 자세한 내용은 [Azure Synapse Analytics의 테이블 데이터 형식](../synapse-analytics/sql/develop-tables-data-types.md) 문서를 참조하세요.

| Azure Synapse Analytics 데이터 형식    | Data Factory 중간 데이터 형식 |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | 부울                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| DateTime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM 특성(varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| 이미지                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
