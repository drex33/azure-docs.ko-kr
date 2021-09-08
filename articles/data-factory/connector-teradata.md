---
title: Azure Data Factory를 사용하여 Teradata Vantage에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: 데이터 팩터리의 Teradata 커넥터 서비스를 통해 Teradata Vantage의 데이터를 데이터 팩터리에서 싱크로 지원하는 데이터 저장소로 복사할 수 있습니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 26ac697767be4023b166b8d751bdbac331dde6a6
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304234"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Teradata Vantage에서 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-onprem-teradata-connector.md)
> * [현재 버전](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Teradata Vantage에서 데이터를 복사하는 방법을 설명합니다. [복사 작업 개요](copy-activity-overview.md)를 기반으로 빌드됩니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Teradata 커넥터는 다음과 같은 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Teradata Vantage에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Teradata 커넥터는 다음을 지원합니다.

- Teradata **버전 14.10, 15.0, 15.10, 16.0, 16.10 및 16.20**.
- **기본**, **Windows** 또는 **LDAP** 인증을 사용한 데이터 복사.
- Teradata 원본에서의 병렬 복사. 자세한 내용은 [Teradata에서 병렬 복사](#parallel-copy-from-teradata) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

자체 호스팅 통합 런타임을 사용하는 경우 버전 3.18부터 Teradata 드라이버가 기본 제공됩니다. 드라이버를 수동으로 설치할 필요가 없습니다. 드라이버를 사용하려면 자체 호스팅 통합 런타임 컴퓨터에 "Visual C++ 재배포 가능 패키지 2012 업데이트 4"가 필요합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)서 다운로드하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-teradata-using-ui"></a>UI를 사용하여 Teradata에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Teradata에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI로 연결된 새 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. Teradata를 검색하고 Teradata 커넥터를 선택합니다.

    :::image type="content" source="media/connector-teradata/teradata-connector.png" alt-text="Teradata 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-teradata/configure-teradata-linked-service.png" alt-text="Teradata에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Teradata 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Teradata 연결된 서비스는 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Teradata** 로 설정되어야 합니다. | 예 |
| connectionString | Teradata 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>Azure Key Vault에 암호를 입력하고 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault의 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. | 예 |
| 사용자 이름 | Teradata에 연결할 사용자 이름을 지정합니다. Windows 인증을 사용하는 경우 적용됩니다. | 예 |
| password | username에 지정한 사용자 계정의 암호를 지정합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. <br>Windows 인증을 사용하거나 기본 인증에 Key Vault의 암호를 참조하는 경우에 적용됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

경우에 따라 연결 문자열에서 설정할 수 있는 추가 연결 속성이 있습니다.

| 속성 | 설명 | 기본값 |
|:--- |:--- |:--- |
| TdmstPortNumber | Teradata 데이터베이스에 액세스하는 데 사용되는 포트 번호입니다.<br>지원팀에서 지시하지 않는 한 이 값을 변경하지 마세요. | 1025 |
| UseDataEncryption | Teradata 데이터베이스와의 모든 통신을 암호화할지 여부를 지정합니다. 허용되는 값은 0 또는 1입니다.<br><br/>- **0(사용하지 않음, 기본값)** : 인증 정보만을 암호화합니다.<br/>- **1(사용)** : 드라이버와 데이터베이스 간에 전달되는 모든 데이터를 암호화합니다. | `0` |
| CharacterSet | 세션에 사용되는 문자 집합입니다. 예: `CharacterSet=UTF16`.<br><br/>이 값은 사용자 정의 문자 집합 또는 미리 정의된 다음 문자 집합 중 하나일 수 있습니다. <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS(Windows, DOS 호환, KANJISJIS_0S)<br/>- EUC(Unix 호환, KANJIEC_0U)<br/>- IBM Mainframe(KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5(TCHBIG5_1R0)<br/>- GB(SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean(HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | `ASCII` |
| MaxRespSize |SQL 요청에 대한 응답 버퍼의 최대 크기(KB)입니다. 예: `MaxRespSize=‭10485760‬`.<br/><br/>Teradata 데이터베이스 버전 16.00 이상에서는 최댓값이 7361536입니다. 이전 버전을 사용하는 연결의 경우 최댓값은 1048576입니다. | `65536` |
| MechanismName | LDAP 프로토콜을 사용하여 연결을 인증하려면 `MechanismName=LDAP`를 지정합니다. | 해당 없음 |

**예제: 기본 인증 사용**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Windows 인증 사용**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: LDAP 인증 사용**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 다음 페이로드는 계속 지원됩니다. 그러나 앞으로 새 페이로드를 사용해야 합니다.

**이전 페이로드:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Teradata 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요.

Teradata의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 `TeradataTable`로 설정해야 합니다. | 예 |
| 데이터베이스 | Teradata 인스턴스의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | Teradata 인스턴스에 있는 테이블의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` 형식 데이터 세트는 계속 지원됩니다. 그러나 새 데이터 세트를 사용하는 것이 좋습니다.

**이전 페이로드:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Teradata 원본에서 지원하는 속성의 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="teradata-as-source"></a>Teradata를 원본으로

>[!TIP]
>데이터 분할을 사용하여 Teradata에서 데이터를 효율적으로 로드하려면 [Teradata에서 병렬 복사](#parallel-copy-from-teradata) 섹션에서 자세히 알아보세요.

Teradata에서 데이터를 복사하기 위해 복사 작업의 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 `TeradataSource`로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할된 로드를 사용하도록 설정하는 경우 쿼리에 해당하는 기본 제공 파티션 매개 변수를 후크해야 합니다. 예제는 [Teradata에서 병렬 복사](#parallel-copy-from-teradata) 섹션을 참조하세요. | No(데이터 세트의 테이블이 지정된 경우) |
| partitionOptions | Teradata에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **Hash**, **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우(즉, `None`은 안 됨) Teradata에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용합니다. | 예 |
| partitionColumnName | 병렬 복사를 위해 범위 분할 또는 해시 분할에서 사용되는 원본 열의 이름을 지정합니다. 지정하지 않으면 테이블의 기본 인덱스가 자동으로 검색되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `Hash` 또는 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfHashPartitionCondition` 또는 `?AdfRangePartitionColumnName`을 후크합니다. [Teradata에서 병렬 복사](#parallel-copy-from-teradata)에서 예제를 참조하세요. | 예 |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최댓값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용합니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionUpbound`을 후크합니다. 예제는 [Teradata에서 병렬 복사](#parallel-copy-from-teradata) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최솟값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionLowbound`를 후크합니다. 예제는 [Teradata에서 병렬 복사](#parallel-copy-from-teradata) 섹션을 참조하세요. | 예 |

> [!NOTE]
>
> `RelationalSource` 형식 복사 원본은 계속 지원되지만 Teradata(분할 옵션)에서의 새로운 기본 제공 병렬 로드는 지원되지 않습니다. 그러나 새 데이터 세트를 사용하는 것이 좋습니다.

**예: 파티션이 없는 기본 쿼리를 사용하여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Teradata에서 병렬 복사

Data Factory Teradata 커넥터는 Teradata에서 병렬로 데이터를 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-teradata/connector-teradata-partition-options.png)

분할된 복사를 사용하도록 설정하면 Data Factory가 Teradata 원본에 대한 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 `parallelCopies`의 값을 4로 설정하면 Data Factory는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 Teradata에서 데이터의 일부를 검색합니다.

특히 Teradata 데이터베이스에서 대량의 데이터를 로드하는 경우 특별히 데이터 분할로 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사하는 경우 폴더에 여러 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드입니다.                                   | **파티션 옵션**: 해시. <br><br/>실행 중에 Data Factory는 기본 인덱스 열을 자동으로 검색하고, 이에 대한 해시를 적용하고, 파티션별로 데이터를 복사합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다.                 | **파티션 옵션**: 해시.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 해시 파티션 적용에 사용되는 열을 지정합니다. 지정하지 않으면 Data Factory는 Teradata 데이터 세트에서 지정한 테이블의 PK 열을 자동으로 검색합니다.<br><br>실행 중 Data Factory는 `?AdfHashPartitionCondition`을 해시 파티션 논리로 바꾸고, Teradata로 보냅니다. |
| 범위 분할에 대해 균등하게 분산된 값을 가진 정수 열이 있는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한값**: 파티션 열에 대해 필터링하여 하한과 상한 사이에서만 데이터를 검색하도록 지정합니다.<br><br>실행하는 동안 Data Factory는 `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` 및 `?AdfRangePartitionLowbound`를 각 파티션의 실제 열 이름과 값 범위로 바꾸고 Teradata로 보냅니다. <br>예를 들어 하한으로 설정된 파티션 열 "ID"가 1로 설정되고 상한이 80로 설정된 경우 병렬 복사를 4로 설정하면 Data Factory가 4개의 파티션으로 데이터를 검색합니다. 해당 ID는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

**예: 해시 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata에 대한 데이터 형식 매핑

Teradata에서 데이터를 복사하는 경우 다음 매핑이 적용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Teradata 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| 정수 |Int32 |
| Interval Day |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Day To Hour |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Day To Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Day To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Hour |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Hour To Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Hour To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Minute To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Month |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Year |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Interval Year To Month |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| 숫자 |Double |
| Period(Date) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Period(Time) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Period(Time With Time Zone) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Period (Timestamp) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Period(Timestamp With Time Zone) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| 타임스탬프 |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |
| Xml |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용합니다. |


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
