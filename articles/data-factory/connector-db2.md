---
title: DB2에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 작업을 사용 하 여 DB2에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: def376920d111f915edfa7f367fcbedd4bc370f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778061"
---
# <a name="copy-data-from-db2-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 DB2에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-db2-connector.md)
> * [현재 버전](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인의 복사 작업을 사용 하 여 DB2 데이터베이스에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 DB2 데이터베이스 커넥터는 다음과 같은 작업에서 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

DB2 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 DB2 커넥터는 DRDA(분산 관계형 데이터베이스 아키텍처) SQLAM(SQL Access Manager) 버전 9, 10 및 11과 함께 다음 IBM DB2 플랫폼 버전을 지원합니다.  DDM/DRDA 프로토콜을 활용합니다.

* z/OS용 IBM DB2 12.1
* z/OS용 IBM DB2 11.1
* z/OS용 IBM DB2 10.1
* i용 IBM DB2 7.3
* i용 IBM DB2 7.2
* i용 IBM DB2 7.1
* LUW용 IBM DB2 11
* LUW용 IBM DB2 10.5
* LUW용 IBM DB2 10.1

>[!TIP]
>DB2 커넥터는 Microsoft OLE DB Provider for DB2를 토대로 빌드됩니다. DB2 커넥터 오류 문제를 해결하려면 [데이터 공급자 오류 코드](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

통합 런타임은 기본 제공 DB2 드라이버를 제공하므로 DB2에서 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-db2-using-ui"></a>UI를 사용하여 DB2에 연결된 서비스 만들기

다음의 단계를 사용하여 Azure Portal UI에서 DB2에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. DB2를 검색하고 DB2 커넥터를 선택합니다.

    :::image type="content" source="media/connector-db2/db2-connector.png" alt-text="DB2 커넥터의 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-db2/configure-db2-linked-service.png" alt-text="DB2에 연결된 서비스 구성의 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 DB2 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

DB2 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **Db2** | 예 |
| connectionString | DB2 인스턴스에 연결하는 데 필요한 정보를 지정합니다.<br/> Azure Key Vault에 암호를 넣고, 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

연결 문자열 내부의 일반적인 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 서버 |DB2 서버의 이름입니다. 콜론으로 구분된 서버 이름 뒤에 포트 번호를 지정할 수 있습니다(예: `server:port`).<br>DB2 커넥터는 DDM/DRDA 프로토콜을 사용하며, 포트를 지정하지 않으면 기본적으로 포트 50000을 사용합니다. 특정 DB2 데이터베이스에서 사용하는 포트는 버전 및 설정에 따라 다를 수 있습니다. 예를 들어, DB2 LUW의 경우 기본 포트는 50000이고, AS400의 경우 기본 포트는 446 또는 448(TLS를 사용하도록 설정한 경우)입니다. 일반적으로 포트를 구성하는 방법에 대해서는 DB2 문서 [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm) 및 [DB2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html)를 참조하세요. |예 |
| 데이터베이스 |DB2 데이터베이스의 이름입니다. |예 |
| authenticationType |DB2 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 다음과 같습니다. **Basic**. |예 |
| 사용자 이름 |DB2 데이터베이스에 연결할 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| packageCollection    | 데이터베이스를 쿼리할 때 서비스에서 필요한 패키지를 자동으로 생성 하는 위치를 지정 합니다. 이 값을 설정 하지 않으면 서비스에서 {username}을 기본값으로 사용 합니다. | 예 |
| certificateCommonName | SSL(Secure Sockets Layer) 또는 TLS(전송 계층 보안) 암호화를 사용하는 경우 인증서 일반 이름에 값을 입력해야 합니다. | 예 |

> [!TIP]
> `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`라는 오류 메시지가 표시되는 경우 필요한 패키지가 만들어지지 않은 것입니다. 기본적으로 서비스는 DB2에 연결 하는 데 사용한 사용자로 이름이 지정 된 패키지를 만들려고 시도 합니다. 패키지 컬렉션 속성을 지정 하 여 데이터베이스를 쿼리할 때 서비스에서 필요한 패키지를 만들지 여부를 지정 합니다.

**예:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
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
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
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

다음 페이로드에서 DB2 연결된 서비스를 사용하는 경우, 있는 그대로도 지원되지만 앞으로 새 옵션을 사용하는 것이 좋습니다.

**이전 페이로드:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 DB2 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

DB2의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **Db2Table** | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마가 있는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`를 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 형식의 데이터 세트를 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 DB2 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="db2-as-source"></a>DB2를 원본으로

DB2에서 데이터를 복사하기 위해 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **Db2Source** | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 형식의 원본을 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="data-type-mapping-for-db2"></a>DB2에 대한 데이터 형식 매핑

DB2에서 데이터를 복사할 때 DB2 데이터 형식에서 서비스 내에서 내부적으로 사용되는 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| DB2 데이터베이스 형식 | 중간 서비스 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| 이진 |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Date |DateTime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| 정수 |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| 숫자 |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| 타임스탬프 |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.