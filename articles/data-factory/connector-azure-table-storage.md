---
title: Azure Table Storage 간 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인을 사용 하 여 지원 되는 원본 저장소에서 Azure Table storage로 또는 테이블 저장소에서 지원 되는 싱크 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: devx-track-azurepowershell, synapse
ms.date: 09/09/2021
ms.openlocfilehash: e88b4cfc2d49aa072021ed81dc4c1eb7105c6112
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811945"
---
# <a name="copy-data-to-and-from-azure-table-storage-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 Azure Table storage 간에 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-table-connector.md)
> * [현재 버전](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인에서 복사 작업을 사용 하 여 Azure Table storage 간에 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Table Storage 커넥터는 다음 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

지원되는 모든 원본 데이터 스토리지의 데이터를 Table Storage에 복사할 수 있습니다. 또한 Table Storage의 데이터를 지원되는 모든 싱크 데이터 스토리지에 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure Table 커넥터는 계정 키 및 서비스 공유 액세스 서명 인증을 모두 사용하여 데이터를 복사할 수 있습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-table-storage-linked-service-using-ui"></a>UI를 사용하여 Azure Table Storage 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Table Storage 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Azure Table을 검색하고 Azure Table Storage 커넥터를 선택합니다.

    :::image type="content" source="media/connector-azure-table-storage/azure-table-storage-connector.png" alt-text="Azure Table Storage 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-azure-table-storage/configure-azure-table-storage-linked-service.png" alt-text="Azure Table Storage 연결된 서비스의 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Table Storage에 한정된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

### <a name="use-an-account-key"></a>계정 키 사용

계정 키를 사용하여 Azure Storage 연결된 서비스를 만들 수 있습니다. Storage에 대 한 전역 액세스를 서비스에 제공 합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureTableStorage** 로 설정되어야 합니다. |예 |
| connectionString | connectionString 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. <br/>Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureTableStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="use-shared-access-signature-authentication"></a>공유 액세스 서명 인증 사용

공유 액세스 서명을 사용하여 Storage 연결된 서비스를 만들 수도 있습니다. 저장소의 모든/특정 리소스에 대해 제한 된/시간 제한 액세스를 서비스에 제공 합니다.

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 이를 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 지정된 권한 집합이 있는 제한된 권한을 클라이언트에 부여할 수 있습니다. 계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 스토리지 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 스토리지 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-sas-overview.md)를 참조하세요.

> [!NOTE]
> **서비스 공유 액세스 서명** 및 **계정 공유 액세스 서명은** 이제 지원 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../storage/common/storage-sas-overview.md)를 참조하세요. 

> [!TIP]
> 스토리지 계정에 대한 서비스 공유 액세스 서명을 생성하려면 다음 PowerShell 명령을 실행합니다. 자리 표시자를 바꾸고 필요한 권한을 부여합니다.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureTableStorage** 로 설정되어야 합니다. |예 |
| sasUri | 테이블에 대한 공유 액세스 서명 URI의 SAS URI를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 안전하게 저장합니다. SAS 토큰을 Azure Key Vault에 넣어 자동 회전을 활용하고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureTableStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

공유 액세스 서명 URI를 만들 때 고려해야 할 사항은 다음과 같습니다.

- 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방법에 따라 개체에 대한 적절한 읽기/쓰기 권한을 설정합니다.
- **만료 시간** 을 적절하게 설정합니다. Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않도록 합니다.
- URI는 필요에 따라 적절한 테이블 수준에서 만들어야 합니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Table 데이터 세트에서 지원되는 속성의 목록을 제공합니다.

Azure Table 간에 데이터를 복사하려면 데이터 세트의 type 속성을 **AzureTable** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 **AzureTable** 로 설정해야 합니다. |예 |
| tableName |연결된 서비스에서 참조하는 Table Storage 데이터베이스 인스턴스의 테이블 이름입니다. |예 |

**예:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-inference-by-the-service"></a>서비스에 의한 스키마 유추

Azure Table과 같은 스키마 없는 데이터 저장소의 경우 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

* 복사 작업에서 열 매핑을 지정하면 서비스에서 원본 쪽 열 목록을 사용하여 데이터를 검색합니다. 이 경우 열 값이 행에 포함되어 있지 않으면 null 값이 제공됩니다.
* 복사 작업에서 열 매핑을 지정하지 않으면 서비스에서 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 전체 스키마가 첫 번째 행에 포함되어 있지 않으면(예: 일부 열에 null 값이 있음) 복사 작업의 결과에서 일부 열이 누락됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Table 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="azure-table-as-a-source-type"></a>Azure Table을 원본 형식으로

Azure Table에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureTableSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 source의 type 속성은 **AzureTableSource** 로 설정해야 합니다. |예 |
| AzureTableSourceQuery |사용자 지정 Table Storage 쿼리를 사용하여 데이터를 읽습니다.<br/>원본 쿼리는 Azure Table Storage에서 지원하는 `$filter` 쿼리 옵션의 직접 맵입니다. [이 문서](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)에서 구문에 대해 자세히 알아보고 다음 [azureTableSourceQuery 예 섹션](#azuretablesourcequery-examples)에 있는 예를 참조하세요. |예 |
| azureTableSourceIgnoreTableNotFound |테이블의 예외가 존재하지 않도록 허용할지 여부를 나타냅니다.<br/>허용되는 값은 **True** 및 **False**(기본값)입니다. |예 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 예제

>[!NOTE]
>Azure Table 쿼리 작업은 [Azure Table service에서 적용된](/rest/api/storageservices/setting-timeouts-for-table-service-operations) 대로 30초 후에 시간 초과됩니다. [쿼리 디자인](../storage/tables/table-storage-design-for-query.md) 문서에서 쿼리를 최적화하는 방법을 알아보세요.

datetime 형식 열에 대해 데이터를 필터링하려면 다음 예제를 참조하세요.

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

문자열 형식 열에 대해 데이터를 필터링하려는 경우 다음 예제를 참조하세요.

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

파이프라인 매개 변수를 사용하는 경우 이전 샘플에 따라 날짜/시간 값을 적절한 형식으로 캐스팅합니다.

### <a name="azure-table-as-a-sink-type"></a>Azure Table을 싱크 형식으로

Azure 테이블로 데이터를 복사하려면 복사 작업의 싱크 형식을 **AzureTableSink** 로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 sink의 type 속성은 **AzureTableSink** 로 설정해야 합니다. |예 |
| azureTableDefaultPartitionKeyValue |싱크에서 사용할 수 있는 기본 파티션 키 값입니다. |예 |
| azureTablePartitionKeyName |값이 파티션 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 "AzureTableDefaultPartitionKeyValue"가 파티션 키로 사용됩니다. |예 |
| azureTableRowKeyName |열 값이 행 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 각 행에 GUID를 사용합니다. |예 |
| azureTableInsertType |Azure Table에 데이터를 삽입하는 모드입니다. 이 속성은 출력 테이블에서 파티션 및 행 키가 일치하는 기존 행의 값을 바꿀지 또는 병합할지 제어합니다. <br/><br/>허용되는 값은 **merge**(기본값) 및 **replace** 입니다. <br/><br> 이 설정은 테이블 수준이 아닌 행 수준에 적용됩니다. 두 옵션 모두 출력 테이블에서 입력에 존재하지 않는 행을 삭제하지 않습니다. merge 및 replace 설정이 작동하는 방법을 알아보려면 [Insert Or Merge Entity](/rest/api/storageservices/Insert-Or-Merge-Entity)(엔터티 삽입 또는 병합) 및 [Insert Or Replace Entity](/rest/api/storageservices/Insert-Or-Replace-Entity)(엔터티 삽입 또는 바꾸기)를 참조하세요. |예 |
| writeBatchSize |writeBatchSize 또는 writeBatchTimeout에 도달하면 Azure Table에 데이터를 삽입합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout |writeBatchSize 또는 writeBatchTimeout에 도달하면 Azure Table에 데이터를 삽입합니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:20:00"(20 분)입니다. |아니요(기본값: 90초 - 스토리지 클라이언트의 기본 시간 제한) |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

대상 열을 azureTablePartitionKeyName으로 사용할 수 있기 전에 **"translator"** 속성을 사용하여 원본 열을 대상 열에 매핑합니다.

다음 예제에서는 DivisionID 원본 열이 DivisionID 대상 열에 매핑됩니다.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID"가 파티션 키로 지정됩니다.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure 테이블에 대한 데이터 형식 매핑

Azure Table 간 데이터를 복사하는 경우 Azure Table 데이터 형식에서 서비스 내에서 내부적으로 사용되는 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

Azure Table에서 데이터를 이동하는 경우 Azure Table OData 형식에서 .NET 형식으로 또는 그 반대로 다음과 같은 [Azure Table에서 정의된 매핑](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)이 사용됩니다.

| Azure Table 데이터 형식 | 중간 서비스 데이터 형식 | 세부 정보 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |바이트 배열은 최대 64KB입니다. |
| Edm.Boolean |bool |부울 값입니다. |
| Edm.DateTime |DateTime |UTC(협정 세계시)로 표현되는 64비트 값입니다. 지원되는 DateTime 범위는 서기 1601년 1월 1일 자정에 시작합니다. (서기), UTC입니다. 범위는 9999년 12월 31일 끝납니다. |
| Edm.Double |double |64비트 부동 소수점 값입니다. |
| Edm.Guid |Guid |전역적으로 고유한 128 비트 식별자입니다. |
| Edm.Int32 |Int32 |32비트 정수입니다. |
| Edm.Int64 |Int64 |64비트 정수입니다. |
| Edm.String |String |UTF-16으로 인코딩된 값입니다. 문자열 값은 최대 64KB입니다. |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
