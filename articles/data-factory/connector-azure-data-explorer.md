---
title: Azure Data Explorer로/에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 활동을 사용하여 Azure Data Explorer 데이터를 복사하는 방법을 알아봅니다.
ms.author: orspodek
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 511e1d58e3abf3c44025a02059c5d6aa947809c0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771900"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics 사용하여 Azure Data Explorer 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인에서 복사 작업을 사용하여 Azure Data Explorer 데이터를 복사하는 방법을 [설명합니다.](/azure/data-explorer/data-explorer-overview) 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>서비스와의 Azure Data Explorer 통합에 대한 자세한 내용은 Azure Data Explorer [통합을](/azure/data-explorer/data-factory-integration)읽어보세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Data Explorer 커넥터는 다음 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

지원되는 모든 원본 데이터 저장소에서 Azure Data Explorer로 데이터를 복사할 수 있습니다. 또한 Azure Data Explorer에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

>[!NOTE]
>자체 호스트된 통합 런타임을 사용하여 온-프레미스 데이터 저장소를 통해 Azure Data Explorer로/에서 데이터를 복사하는 기능은 3.14 이상 버전에서 지원됩니다.

Azure Data Explorer 커넥터를 사용하여 다음을 수행할 수 있습니다.

* **서비스 주체** 와 함께 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
* 원본의 경우 KQL(Kusto) 쿼리를 사용하여 데이터를 검색합니다.
* 싱크의 경우 대상 테이블에 데이터를 추가합니다.

## <a name="getting-started"></a>시작

>[!TIP]
>Azure Data Explorer 커넥터에 대한 연습은 Azure Data Explorer [데이터 복사](/azure/data-explorer/data-factory-load-data) 및 [데이터베이스에서 Azure Data Explorer 대량 복사를](/azure/data-explorer/data-factory-template)참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-data-explorer-using-ui"></a>UI를 사용하여 Azure Data Explorer에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Data Explorer에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. Explorer를 검색하고 Azure Data Explorer(Kusto) 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-data-explorer/azure-data-explorer-connector.png" alt-text="Azure Data Explorer(Kusto) 커넥터 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-data-explorer/configure-azure-data-explorer-linked-service.png" alt-text="Azure Data Explorer의 연결된 서비스 구성 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Data Explorer 커넥터와 관련된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Explorer 커넥터는 다음과 같은 인증 형식을 지원합니다. 자세한 내용은 해당 섹션을 참조하세요.

- [서비스 주체 인증](#service-principal-authentication)
- [시스템이 할당한 관리 ID 인증](#managed-identity)
- [사용자가 할당한 관리 ID 인증](#user-assigned-managed-identity-authentication)

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 인증을 사용하려면 다음 단계에 따라 서비스 주체를 가져오고 사용 권한을 부여합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) 단계에 따라 Azure Active Directory에 애플리케이션 엔터티를 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Data Explorer에서 서비스 주체에게 올바른 권한을 부여합니다. 역할 및 사용 권한과 사용 권한 관리에 대한 자세한 내용은 [Azure Data Explorer 데이터베이스 권한 관리](/azure/data-explorer/manage-database-permissions)를 참조하세요. 일반적으로 다음을 수행해야 합니다.

    - **원본으로** 데이터베이스에 적어도 **데이터베이스 뷰어** 역할을 부여합니다.
    - **싱크로** 데이터베이스에 적어도 **데이터베이스 수집기** 역할을 부여합니다.

>[!NOTE]
>UI를 사용하여 작성하면 기본적으로 로그인 사용자 계정이 Azure Data Explorer 클러스터, 데이터베이스 및 테이블을 나열하는 데 사용됩니다. 새로 고침 단추 옆의 드롭다운을 클릭하여 서비스 주체를 사용하여 개체를 나열하도록 선택하거나 해당 작업에 대한 권한이 없는 경우 수동으로 이름을 입력할 수 있습니다.

Azure Data Explorer 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorer** 로 설정해야 합니다. | 예 |
| 엔드포인트(endpoint) | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름입니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. 이를 [Kusto 연결 문자열](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)의 “Authority ID”라고 합니다. Azure Portal의 오른쪽 위 모서리를 마우스 포인터로 가리켜 검색합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. 이를 [Kusto 연결 문자열](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)의 “AAD 애플리케이션 클라이언트 ID”라고 합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이를 [Kusto 연결 문자열](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)의 “AAD 애플리케이션 키”라고 합니다. 이 필드를 **SecureString으로** 표시하여 안전하게 저장하거나 [Azure Key Vault 저장된 보안 데이터를 참조합니다.](store-credentials-in-key-vault.md) | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

**예: 서비스 주체 키 인증 사용**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> 시스템이 할당한 관리 ID 인증

Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

시스템이 할당한 관리 ID 인증을 사용하려면 다음 단계를 수행하여 권한을 부여합니다.

1. 팩터리 또는 Synapse 작업 영역과 함께 생성된 **관리 ID 개체 ID** 값을 복사하여 관리 ID [정보를](data-factory-service-identity.md#retrieve-managed-identity) 검색합니다.

2. Azure Data Explorer에서 관리 ID에 올바른 권한을 부여합니다. 역할 및 사용 권한과 사용 권한 관리에 대한 자세한 내용은 [Azure Data Explorer 데이터베이스 권한 관리](/azure/data-explorer/manage-database-permissions)를 참조하세요. 일반적으로 다음을 수행해야 합니다.

    - **원본으로** 데이터베이스에 적어도 **데이터베이스 뷰어** 역할을 부여합니다.
    - **싱크로** 데이터베이스에 적어도 **데이터베이스 수집기** 역할을 부여합니다.

>[!NOTE]
>UI를 사용하여 작성하면 로그인 사용자 계정이 Azure Data Explorer 클러스터, 데이터베이스 및 테이블을 나열하는 데 사용됩니다. 해당 작업에 대한 권한이 없는 경우 수동으로 이름을 입력합니다.

Azure Data Explorer 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorer** 로 설정해야 합니다. | 예 |
| 엔드포인트(endpoint) | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

**예제: 시스템이 할당한 관리 ID 인증 사용**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>사용자가 할당한 관리 ID 인증
Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

사용자가 할당한 관리 ID 인증을 사용하려면 다음 단계를 수행합니다.

1. [사용자가 할당한 관리 ID를 하나 이상 만들고](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) Azure Data Explorer에 액세스 권한을 부여합니다. 역할 및 사용 권한과 사용 권한 관리에 대한 자세한 내용은 [Azure Data Explorer 데이터베이스 권한 관리](/azure/data-explorer/manage-database-permissions)를 참조하세요. 일반적으로 다음을 수행해야 합니다.

    - **원본으로** 데이터베이스에 적어도 **데이터베이스 뷰어** 역할을 부여합니다.
    - **싱크로** 데이터베이스에 적어도 **데이터베이스 수집기** 역할을 부여합니다.
     
2. 하나 또는 여러 사용자 할당 관리 id를 data factory 또는 Synapse 작업 영역에 할당 하 고 사용자가 할당 한 각 관리 id에 대 한 [자격 증명을 만듭니다](data-factory-service-identity.md#credentials) .

Azure Data Explorer 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorer** 로 설정해야 합니다. | 예 |
| 엔드포인트(endpoint) | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름입니다. | 예 |
| 자격 증명 | 사용자가 할당한 관리 ID를 자격 증명 개체로 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

**예제: 사용자가 할당한 관리 ID 인증 사용**
```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 이 섹션에서는 Azure Data Explorer 데이터 세트에서 지원하는 속성을 나열합니다.

Azure Data Explorer에 데이터를 복사하려면 데이터 세트의 형식 속성을 **AzureDataExplorerTable** 로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorerTable** 로 설정해야 합니다. | 예 |
| 테이블 | 연결된 서비스가 참조하는 테이블의 이름입니다. | 싱크의 경우 예이며, 원본의 경우 아니오입니다. |

**데이터 세트 속성 예제:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Pipelines 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 Azure Data Explorer 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer가 원본인 경우

Azure Data Explorer에서 데이터를 복사하려면 복사 작업 원본의 **형식** 속성을 **AzureDataExplorerSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **형식** 속성을 **AzureDataExplorerSource** 로 설정해야 합니다. | 예 |
| Query | [KQL 형식](/azure/kusto/query/)으로 제공되는 읽기 전용 요청입니다. 사용자 지정 KQL 쿼리를 참조로 사용합니다. | 예 |
| queryTimeout | 쿼리 요청 시간이 초과되기 전의 대기 시간입니다. 기본값은 10분(00:10:00)이며 허용되는 최댓값은 1시간(01:00:00)입니다. | 예 |
| noTruncation | 반환된 결과 집합을 잘라낼지를 나타냅니다. 기본적으로 50만 레코드 또는 64MB 후에 결과가 잘립니다. 자르기는 작업의 올바른 동작을 보장하는 데 강력히 권장됩니다. |예 |

>[!NOTE]
>기본적으로 Azure Data Explorer 원본의 크기 제한은 50만 레코드 또는 64MB입니다. 잘림 없이 모든 레코드를 검색하려면 쿼리의 시작 부분에 `set notruncation;`을 지정하면 됩니다. 자세한 내용은 [쿼리 제한](/azure/kusto/concepts/querylimits)을 참조하세요.

**예:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer가 싱크인 경우

Azure Data Explorer로 데이터를 복사하려면 복사 작업 원본의 형식 속성을 **AzureDataExplorerSink** 로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **형식** 속성을 **AzureDataExplorerSink** 로 설정해야 합니다. | 예 |
| ingestionMappingName | Kusto 테이블에 미리 만들어진 [매핑](/azure/kusto/management/mappings#csv-mapping)의 이름입니다. 원본에서 Azure Data Explorer로(CSV/JSON/Avro 형식을 포함하여 [지원되는 모든 원본 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)에 적용됨) 열을 매핑하려면 복사 작업 [열 매핑](copy-activity-schema-and-type-mapping.md)(이름에 의해 암시적으로 또는 명시적으로 구성된 대로) 및/또는 Azure Data Explorer 매핑을 사용할 수 있습니다. | 예 |
| additionalProperties | Azure Data Explorer 싱크에 의해 이미 설정되지 않은 수집 속성을 지정하는 데 사용할 수 있는 속성 모음입니다. 특히 수집 태그를 지정하는 데 유용할 수 있습니다. [Azure Data Explore 데이터 수집 문서](/azure/data-explorer/ingestion-properties)에서 자세히 알아보세요. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 관한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 복사 작업에서 원본 및 싱크로 지원하는 데이터 저장소 목록은 지원되는 데이터 저장소 를 [참조하세요.](copy-activity-overview.md#supported-data-stores-and-formats)

* [Azure Data Factory 및 Synapse Analytics 데이터를 Azure Data Explorer 복사하는](/azure/data-explorer/data-factory-load-data)방법에 대해 자세히 알아봅니다.