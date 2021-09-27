---
title: Netezza에서 데이터 복사
description: Azure Data Factory 또는 Synapse Analytics 파이프라인의 복사 작업을 사용 하 여 Netezza에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 588700126d87361e4530c073cdcaf1a5a9339511
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828175"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 Netezza에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 작업을 사용 하 여 Netezza에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업](copy-activity-overview.md)을 기준으로 합니다.

>[!TIP]
>Netezza에서 Azure로의 데이터 마이그레이션 시나리오의 경우 [온-프레미스 Netezza 서버에서 azure로 데이터](data-migration-guidance-netezza-azure-sqldw.md)마이그레이션에서 자세히 알아보세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Netezza 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용하여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)


Netezza에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

Netezza 커넥터는 원본에서의 병렬 복사를 지원합니다. 자세한 내용은 [Netezza에서 병렬 복사](#parallel-copy-from-netezza) 섹션을 참조하세요.

서비스는 연결을 사용할 수 있는 기본 제공 드라이버를 제공 합니다. 이 커넥터를 사용하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 통해 복사 작업을 사용하는 파이프라인을 만들 수 있습니다. 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

## <a name="create-a-linked-service-to-netezza-using-ui"></a>UI를 사용하여 Netezza에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Netezza에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Netezza를 검색하고 Netezza 커넥터를 선택합니다.

   :::image type="content" source="media/connector-netezza/netezza-connector.png" alt-text="Netezza 커넥터의 스크린샷.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-netezza/configure-netezza-linked-service.png" alt-text="Netezza의 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Netezza 커넥터와 관련 된 엔터티를 정의 하는 데 사용할 수 있는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 속성은 Netezza 연결 서비스에 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **Netezza** 로 설정되어야 합니다. | 예 |
| connectionString | Netezza에 연결할 ODBC 연결 문자열입니다. <br/>Azure Key Vault에 암호를 넣고, 연결 문자열에서 `pwd` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 요소](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

일반적인 연결 문자열은 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`입니다. 다음 표에서는 설정 가능한 여러 속성을 설명합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| SecurityLevel | 드라이버가 데이터 저장소에 연결하는 데 사용하는 보안 수준입니다. 이 드라이버는 SSL 버전 3을 사용하는 단방향 인증과의 SSL 연결을 지원합니다. <br>예: `SecurityLevel=preferredSecured`. 지원되는 값은 다음과 같습니다.<br/>- **Only unsecured**(**onlyUnSecured**): 드라이버가 SSL을 사용하지 않습니다.<br/>- **Preferred unsecured(preferredUnSecured)(기본값)**: 서버가 선택 사항을 제공하면 드라이버는 SSL을 사용하지 않습니다. <br/>- **Preferred secured(preferredSecured)** : 서버가 선택 사항을 제공하면 드라이버는 SSL을 사용합니다. <br/>- **Only secured(onlySecured)**: SSL 연결을 사용할 수 있는 경우가 아니면 드라이버가 연결되지 않습니다. | 예 |
| CaCertFile | 서버에 사용되는 SSL 인증서의 전체 경로입니다. 예: `CaCertFile=<cert path>;`| 예(SSL을 사용하는 경우) |

**예제**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

이 섹션에서는 Netezza 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요.

Netezza에서 데이터를 복사하려면 데이터 세트의 **형식** 속성을 **NetezzaTable** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **NetezzaTable** | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마가 있는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`를 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Netezza 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="netezza-as-source"></a>Netezza를 원본으로

>[!TIP]
>데이터 분할을 사용하여 Netezza에서 데이터를 효율적으로 로드하려면 [Netezza에서 병렬 복사](#parallel-copy-from-netezza) 섹션에서 자세히 알아보세요.

Netezza에서 데이터를 복사하려면 복사 작업의 **원본** 형식을 **NetezzaSource** 로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **형식** 속성을 **NetezzaSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |
| partitionOptions | Netezza에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **DataSlice**, **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우 (즉, `None`은 안 됨), Netezza 데이터베이스에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용합니다. | 예 |
| partitionColumnName | 병렬 복사를 위해 범위 분할에서 사용되는 **정수 형식으로** 원본 열의 이름을 지정합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionColumnName`을 후크합니다. [Netezza에서 병렬 복사](#parallel-copy-from-netezza)에서 예제를 참조하세요. | 예 |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최댓값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용합니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionUpbound`을 후크합니다. 예제는 [Netezza에서 병렬 복사](#parallel-copy-from-netezza) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최솟값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionLowbound`를 후크합니다. 예제는 [Netezza에서 병렬 복사](#parallel-copy-from-netezza) 섹션을 참조하세요. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Netezza에서 병렬 복사

Data Factory Netezza 커넥터는 Netezza에서 병렬로 데이터를 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/connector-netezza/connector-netezza-partition-options.png" alt-text="파티션 옵션의 스크린샷":::

분할 된 복사를 사용 하도록 설정 하는 경우 서비스는 Netezza 원본에 대 한 병렬 쿼리를 실행 하 여 파티션으로 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어를 `parallelCopies` 4로 설정 하는 경우 서비스는 지정 된 파티션 옵션 및 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며 각 쿼리는 Netezza 데이터베이스에서 데이터의 일부를 검색 합니다.

특히 Netezza 데이터베이스에서 대량의 데이터를 로드하는 경우 특별히 데이터 분할로 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사하는 경우 폴더에 여러 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드입니다.                                   | **파티션 옵션**: 데이터 조각입니다. <br><br/>실행 중에 서비스는 [Netezza의 기본 제공 데이터 조각](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)에 따라 데이터를 자동으로 분할 하 고 파티션으로 데이터를 복사 합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다.                 | **파티션 옵션**: 데이터 조각입니다.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>실행 중에 서비스는 `?AdfPartitionCount` (복사 작업에 설정 된 병렬 복사 번호)와 `?AdfDataSliceCondition` 데이터 조각 파티션 논리를 대체 하 고 Netezza로 보냅니다. |
| 범위 분할에 대해 균등하게 분산된 값을 가진 정수 열이 있는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한값**: 파티션 열에 대해 필터링하여 하한과 상한 사이에서만 데이터를 검색하도록 지정합니다.<br><br>실행 중에 서비스는 `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` 및를 `?AdfRangePartitionLowbound` 각 파티션에 대 한 실제 열 이름 및 값 범위로 바꾸고 Netezza로 보냅니다. <br>예를 들어 하 한을 1로 설정 하 고 상한을 1로 설정 하 고 상한을 80로 설정 하 여 병렬 복사를 4로 설정 하면 서비스는 4 개의 파티션으로 데이터를 검색 합니다. 해당 ID는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

**예: 데이터 조각 파티션이 있는 쿼리**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
