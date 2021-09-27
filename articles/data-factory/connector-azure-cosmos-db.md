---
title: Azure Cosmos DB(SQL API)에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics를 사용하여 Azure Cosmos DB(SQL API) 간에 데이터를 복사하고 Azure Cosmos DB(SQL API)에서 데이터를 변환하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 836ad2692dffbb6c4977f5757c6f305c6fd66d40
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761881"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Cosmos DB(SQL API)에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-documentdb-connector.md)
> * [현재 버전](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cosmos DB(SQL API) 간에 데이터를 복사하고 Data Flow를 사용하여 Azure Cosmos DB(SQL API)에서 데이터를 변환하는 방법을 설명합니다. 자세한 내용은 [Azure Data Factory](introduction.md) 및 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)의 소개 문서를 참조하세요.

>[!NOTE]
>이 커넥터는 Cosmos DB SQL API만 지원합니다. MongoDB API의 경우 [Azure Cosmos DB의 API for MongoDB용 커넥터](connector-azure-cosmos-db-mongodb-api.md)를 참조하세요. 다른 API 형식은 이제 지원되지 않습니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos DB(SQL API) 커넥터는 다음과 같은 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

복사 작업의 경우 Azure Cosmos DB(SQL API) 커넥터는 다음을 지원합니다.

- Azure 리소스 인증에 대한 키, 서비스 주체 또는 관리 ID를 사용하여 Azure Cosmos DB [SQL API](../cosmos-db/introduction.md)에서 데이터를 복사합니다.
- **insert** 또는 **upsert** 로 Azure Cosmos DB에 씁니다.
- JSON 문서를 있는 그대로 가져오고 내보내거나 데이터를 테이블 형식 데이터 세트 간에 복사합니다. 예로는 SQL 데이터베이스 및 CSV 파일이 있습니다. JSON 파일 간 또는 다른 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사하려면 [JSON 문서 가져오기 또는 내보내기](#import-and-export-json-documents)를 참조하세요.

Data Factory 및 Synapse 파이프라인은 Azure Cosmos DB에 쓸 때 최상의 성능을 제공하기 위해 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)와 통합됩니다.

> [!TIP]
> [데이터 마이그레이션 동영상](https://youtu.be/5-SRNiC_qOU)에서는 Azure Blob Storage에서 Azure Cosmos DB로 데이터를 복사하는 단계를 안내합니다. 이 동영상에서는 또한 일반적으로 Azure Cosmos DB에 데이터를 수집하기 위한 성능 조정 고려 사항도 설명합니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]
## <a name="create-a-linked-service-to-azure-cosmos-db-using-ui"></a>UI를 사용하여 Azure Cosmos DB에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Cosmos DB에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택하고 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스 만들기 스크린샷":::

2. Cosmos를 검색하고 Azure Cosmos DB(SQL API) 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-cosmos-db/azure-cosmos-db-connector.png" alt-text="Azure Cosmos DB(SQL API) 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-cosmos-db/configure-azure-cosmos-db-linked-service.png" alt-text="Azure Cosmos DB의 연결된 서비스 구성의 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보


다음 섹션에서는 Azure Cosmos DB(SQL API)에 한정된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cosmos DB(SQL API) 커넥터는 다음과 같은 인증 유형을 지원합니다. 자세한 내용은 해당 섹션을 참조하세요.

- [키 인증](#key-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증용 관리 ID](#managed-identity)

### <a name="key-authentication"></a>키 인증

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **CosmosDb** 로 설정해야 합니다. | 예 |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다.<br />**참고**: 다음 예제에 표시된 대로 연결 문자열에 데이터베이스 정보를 지정해야 합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 이 속성을 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

**예제**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
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

### <a name="service-principal-authentication"></a><a name="service-principal-authentication"></a>서비스 주체 인증

>[!NOTE]
>현재 서비스 주체 인증은 데이터 흐름에서 지원되지 않습니다.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD(Azure Active Directory) 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) 단계에 따라 Azure AD에 애플리케이션 엔터티를 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체에게 적절한 사용 권한을 부여합니다. [파일 및 디렉터리에 대한 액세스 제어 목록](../cosmos-db/how-to-setup-rbac.md)에서 Cosmos DB 권한이 작동하는 방식에 대한 예시를 참조하세요. 보다 구체적으로, 역할 정의를 만들고 서비스 주체 개체 ID를 통해 서비스 주체에 역할을 할당합니다. 

연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **CosmosDb** 로 설정해야 합니다. |예 |
| accountEndpoint | Azure Cosmos DB에 계정 엔드포인트 URL을 지정합니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름을 지정합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalCredentialType | 서비스 주체 인증에 사용할 자격 증명 유형입니다. 허용되는 값은 **ServicePrincipalKey** 및 **ServicePrincipalCert** 입니다. | 예 |
| servicePrincipalCredential | 서비스 주체 자격 증명입니다. <br/> **ServicePrincipalKey** 를 자격 증명 유형으로 사용하는 경우 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장하거나 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. <br/> **ServicePrincipalCert** 를 자격 증명으로 사용하는 경우 Azure Key Vault에서 인증서를 참조합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure 포털의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure Active Directory 애플리케이션이 등록된 Azure 클라우드 환경의 유형을 지정합니다. <br/> 허용되는 값은 **AzurePublic**, **AzureChina**, **AzureUsGovernment**, **AzureGermany** 입니다. 기본적으로 이 서비스의 클라우드 환경이 사용됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

**예: 서비스 주체 키 인증 사용**

Azure Key Vault에 서비스 주체 키를 저장할 수도 있습니다.

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**예: 서비스 주체 인증서 인증 사용**
```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

>[!NOTE]
>현재 관리 ID 인증은 데이터 흐름에서 지원되지 않습니다.

이 특정 서비스 인스턴스를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리 또는 Synapse 파이프라인을 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용하는 것과 비슷하게 Cosmos DB 인증에 이 관리 ID를 직접 사용할 수 있습니다. 이 지정된 리소스는 Cosmos DB 간에서 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증에 관리 ID를 사용하려면 다음 단계를 수행합니다.

1. 서비스와 함께 생성된 **관리 ID개체 ID** 의 값을 복사하여 [관리 ID 정보를 검색](data-factory-service-identity.md#retrieve-managed-identity)합니다.

2. 적절한 사용 권한을 관리 ID에 부여합니다. [파일 및 디렉터리에 대한 액세스 제어 목록](../cosmos-db/how-to-setup-rbac.md)에서 Cosmos DB 권한이 작동하는 방식에 대한 예시를 참조하세요. 특히 역할 정의를 만들고 관리 ID에 역할을 할당합니다.

연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **CosmosDb** 로 설정해야 합니다. |예 |
| accountEndpoint | Azure Cosmos DB에 계정 엔드포인트 URL을 지정합니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름을 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

**예:**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "accountEndpoint": "<account endpoint>",
            "database": "<database name>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요.

Azure Cosmos DB(SQL API) 데이터 세트에 대해 다음과 같은 속성이 지원됩니다. 

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **CosmosDbSqlApiCollection** 으로 설정해야 합니다. |예 |
| collectionName |Azure Cosmos DB 문서 컬렉션의 이름입니다. |예 |

“DocumentDbCollection” 형식 데이터 세트를 사용하는 경우 버전과의 호환성을 위해 복사 및 조회 작업에 그대로 계속 지원되지만 Data Flow에는 지원되지 않습니다. 앞으로는 새 모델을 사용하는 것이 좋습니다.

**예제**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Azure Cosmos DB(SQL API) 원본 및 싱크에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB(SQL API) 원본

Azure Cosmos DB(SQL API)에서 데이터를 복사하려면 복사 작업의 **source** 형식을 **DocumentDbCollectionSource** 로 설정합니다. 

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성을 **CosmosDbSqlApiSource** 로 설정해야 합니다. |예 |
| Query |데이터를 읽는 Azure Cosmos DB 쿼리를 지정합니다.<br/><br/>예제:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |예 <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| preferredRegions | Cosmos DB에서 데이터를 검색할 때 연결할 지역의 기본 목록입니다. | 예 |
| pageSize | 쿼리 결과의 페이지당 문서 수입니다. 기본값은 서비스 쪽 동적 페이지 크기를 1000까지 사용함을 의미하는 “-1”입니다. | 예 |
| detectDatetime | 문서의 문자열 값에서 날짜/시간을 검색할지를 지정합니다. 허용되는 값은 **true**(기본값), **false** 입니다. | 예 |

“DocumentDbCollectionSource” 형식 원본을 사용하는 경우 이전 버전과의 호환성을 위해 그대로 계속 지원됩니다. 앞으로 Cosmos DB에서 데이터를 복사하는 다양한 기능을 제공하는 새 모델을 사용하는 것이 좋습니다.

**예제**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Cosmos DB에서 데이터를 복사할 때에는 [JSON 문서를 있는 그대로 내보내려는](#import-and-export-json-documents) 경우가 아닌 한, 복사 작업에서 매핑을 지정하는 것이 가장 좋습니다. 이 서비스는 작업에 대해 지정된 매핑을 사용합니다. 행에 열에 대한 값이 포함되어 있지 않으면 열 값에 대해 null 값이 제공됩니다. 매핑을 지정하지 않으면 서비스는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB(SQL API) 싱크

Azure Cosmos DB(SQL API)로 데이터를 복사하려면 복사 작업의 **sink** 형식을 **DocumentDbCollectionSink** 로 설정합니다. 

복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **CosmosDbSqlApiSink** 로 설정해야 합니다. |예 |
| writeBehavior |Azure Cosmos DB에 데이터를 쓰는 방법을 설명합니다. 허용되는 값은 **insert** 및 **upsert** 입니다.<br/><br/>**upsert** 의 동작은 동일한 ID의 문서가 이미 존재하는 경우 문서를 바꾸는 것이며, 존재하지 않는 경우 문서를 삽입하는 것입니다.<br /><br />**참고**: ID가 원래 문서 또는 열 매핑에 지정되지 않은 경우 이 서비스는 문서에 대한 ID를 자동으로 생성합니다. 즉, **upsert** 가 예상대로 작동하려면 문서에 ID가 있는지 확인해야 합니다. |예<br />(기본값: **insert**) |
| writeBatchSize | 이 서비스는 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)를 사용하여 Azure Cosmos DB에 데이터를 씁니다. **writeBatchSize** 속성은 서비스가 라이브러리에 제공하는 문서의 크기를 제어합니다. 성능을 개선하기 위해 **writeBatchSize** 에 대한 값을 늘리고 문서 크기가 커지는 경우 값을 줄이도록 시도할 수 있습니다. 아래 팁을 참조하세요. |예<br />(기본값: **10,000**) |
| disableMetricsCollection | 이 서비스는 복사 성능 최적화 및 권장 사항에 대한 Cosmos DB RU와 같은 메트릭을 수집합니다. 이 동작에 관심이 있는 경우 `true`를 지정하여 해제합니다. | 아니요(기본값: `false`) |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |


>[!TIP]
>JSON 문서를 그대로 가져오려면 [JSON 문서 가져오기 또는 내보내기](#import-and-export-json-documents) 섹션을 참조하세요. 표 형식 데이터를 복사하려면 [관계성 데이터베이스에서 Cosmos DB로 마이그레이션](#migrate-from-relational-database-to-cosmos-db)을 참조하세요.

>[!TIP]
>Cosmos DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. **"요청 크기가 너무 큽니다."** 라는 오류가 발생하는 경우 **복사 싱크 구성에서 `writeBatchSize` 값** 을 줄입니다.

“DocumentDbCollectionSink” 형식 원본을 사용하는 경우 이전 버전과의 호환성을 위해 그대로 계속 지원됩니다. 앞으로 Cosmos DB에서 데이터를 복사하는 다양한 기능을 제공하는 새 모델을 사용하는 것이 좋습니다.

**예제**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>스키마 매핑

Azure Cosmos DB에서 표 형식 싱크로 또는 싱크에서 API로 데이터를 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 Cosmos DB에서 컬렉션에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요.

### <a name="source-transformation"></a>원본 변환

Azure Cosmos DB에만 해당하는 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다. 

**시스템 열 포함:** true인 경우 ```id```, ```_ts``` 및 기타 시스템 열이 CosmosDB에서 들어오는 데이터 흐름 메타데이터에 포함됩니다. 컬렉션을 업데이트할 때에는 기존 행 ID를 가져올 수 있도록 이 옵션을 포함하는 것이 중요합니다.

**페이지 크기:** 쿼리 결과의 페이지당 문서 수입니다. 기본값은 서비스 동적 페이지를 1000까지 사용하는 “-1”입니다.

**처리량:** 읽기 작업을 수행하는 동안 이 데이터 흐름이 실행될 때마다 CosmosDB 컬렉션에 적용할 RU 수에 대해 선택적 값을 설정합니다. 최솟값은 400입니다.

**기본 설정 지역:** 이 프로세스에 대한 기본 읽기 지역을 선택합니다.

#### <a name="json-settings"></a>JSON 설정

**단일 문서:** 서비스가 전체 파일을 단일 JSON 문서로 처리하도록 하려면 이 옵션을 선택합니다.

**따옴표로 묶여 있지 않은 열 이름:** JSON에서 열 이름이 따옴표로 묶여 있지 않은 경우 이 옵션을 선택합니다.

**주석 있음:** JSON 문서의 데이터에 주석이 있는 경우 이 선택을 사용합니다.

**작은따옴표:** 문서의 열과 값이 작은따옴표로 묶여있는 경우 이 옵션을 선택해야 합니다.

**백슬래시가 이스케이프됨:** JSON에서 문자를 이스케이프하는 데 백슬래시를 사용하는 경우 이 옵션을 선택합니다.

### <a name="sink-transformation"></a>싱크 변환

Azure Cosmos DB에 특정한 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 메서드:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 행 변경 변환이 필요합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.

**컬렉션 작업:** 쓰기 전에 대상 컬렉션을 다시 만들지를 결정합니다.
* 없음: 테이블에 대해 아무 작업도 수행되지 않습니다.
* 다시 만들기: 컬렉션이 삭제되고 다시 만들어집니다.

**일괄 처리 크기**: 각 일괄 처리에서 Cosmos DB 컬렉션에 얼마나 많은 개체가 기록되는지 나타내는 정수입니다. 일반적으로 기본 일괄 처리 크기로 시작하는 것으로 충분합니다. 이 값을 추가로 조정하려면 다음을 참고하세요.

- Cosmos DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 “요청 크기 = 단일 문서 크기 * 일괄 처리 크기”입니다. “요청 크기가 너무 큼”이라는 오류가 발생하는 경우 일괄 처리 크기 값을 줄입니다.
- 일괄 처리 크기가 클수록 서비스의 처리량은 높아지지만 워크로드를 지원하기에 충분한 RU를 할당해야 합니다.

**파티션 키:** 컬렉션에 대한 파티션 키를 나타내는 문자열을 입력합니다. 예: ```/movies/title```

**처리량:** 이 데이터 흐름이 실행될 때마다 CosmosDB 컬렉션에 적용할 RU 수에 대해 선택적 값을 설정합니다. 최솟값은 400입니다.

**쓰기 처리량 예산:** 컬렉션에 할당된 총 처리량 중 이 데이터 흐름 쓰기 작업에 할당하려는 RU를 나타내는 정수입니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="import-and-export-json-documents"></a>JSON 문서 가져오기 및 내보내기

이 Azure Cosmos DB(SQL API) 커넥터를 사용하여 손쉽게 다음을 수행할 수 있습니다.

* 두 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사
* Azure Blob Storage, Azure Data Lake Store 및 서비스에서 지원하는 기타 파일 기반 저장소 등 다양한 원본에서 Azure Cosmos DB로 JSON 문서 가져오기
* Azure Cosmos DB 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기

스키마 중립적 복사를 수행하려면 다음을 수행합니다.

* 데이터 복사 도구를 사용하는 경우, **JSON 파일 또는 Cosmos DB 컬렉션으로 있는 그대로 내보내기** 옵션을 선택합니다.
* 활동 제작을 사용하는 경우 원본 또는 싱크에 해당하는 파일 저장소를 사용하여 JSON 형식을 선택합니다.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>관계형 데이터베이스에서 Cosmos DB로 마이그레이션

관계형 데이터베이스(예: SQL Server)에서 Azure Cosmos DB로 마이그레이션하는 경우 복사 작업은 Cosmos DB에서 JSON 문서를 평면화하기 위해 원본에서 표 형식 데이터를 쉽게 매핑할 수 있습니다. 경우에 따라 데이터 모델을 다시 디자인하여 [Azure Cosmos DB의 데이터 모델링](../cosmos-db/modeling-data.md)에 따른 NoSQL 사용 사례에 맞게 최적화하는 것이 좋습니다. 예를 들어 하나의 JSON 문서 내에 모든 관련 하위 항목을 포함함으로써 데이터를 비정규화할 수 있습니다. 이 경우 복사 작업을 사용하여 이를 달성하는 방법을 안내하는 [이 문서](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.