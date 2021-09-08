---
title: Azure Data Factory를 사용하여 Hive에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Hive에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: db88b0250a52ea96a116097469e43e1f0c26ece2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306050"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Hive에서 데이터 복사 및 변환 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Hive에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Hive 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Hive에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-hive-using-ui"></a>UI를 사용하여 Hive에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Hive에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI로 연결된 새 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. Hive를 검색하고 Hive 커넥터를 선택합니다.

    :::image type="content" source="media/connector-hive/hive-connector.png" alt-text="Hive 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-hive/configure-hive-linked-service.png" alt-text="Hive에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보



다음 섹션에서는 Hive 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Hive 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Hive** 로 설정해야 합니다. | 예 |
| host | Hive 서버의 IP 주소 또는 호스트 이름으로, 호스트가 여러 개인 경우 ‘,’로 구분합니다(serviceDiscoveryMode가 사용되는 경우에만 해당).  | 예 |
| 포트 | Hive 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. Azure HDInsight에 연결하는 경우 포트를 443으로 지정합니다. | 예 |
| serverType | Hive 서버의 유형입니다. <br/>허용되는 값은 **HiveServer1**, **HiveServer2**, **HiveThriftServer** 입니다. | 예 |
| thriftTransportProtocol | Thrift 계층에서 사용할 전송 프로토콜입니다. <br/>허용되는 값은 **Binary**, **SASL**, **HTTP** 입니다. | 예 |
| authenticationType | Hive 서버에 액세스하는 데 사용되는 인증 방법입니다. <br/>허용되는 값은 **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** 입니다. Kerberos 인증은 현재 지원되지 않습니다. | 예 |
| serviceDiscoveryMode | true이면 ZooKeeper 서비스 사용을 나타내고, false이면 그렇지 않습니다.  | 예 |
| zooKeeperNameSpace | ZooKeeper에서 Hive 서버 2 노드가 추가되는 네임스페이스입니다.  | 예 |
| useNativeQuery | 드라이버가 네이티브 HiveQL 쿼리를 사용할지 또는 HiveQL에서 동일한 형식으로 변환할지를 지정합니다.  | 예 |
| 사용자 이름 | Hive 서버에 액세스하는 데 사용하는 사용자 이름입니다.  | 예 |
| password | 사용자에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| httpPath | Hive 서버에 해당하는 부분 URL입니다.  | 예 |
| enableSsl | TLS를 사용하여 서버 연결을 암호화할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| trustedCertPath | TLS를 통해 연결할 때 서버를 확인하는 데 사용되는 신뢰할 수 있는 CA 인증서를 포함하는 .pem 파일의 전체 경로입니다. 이 속성은 자체 호스팅 IR에서 TLS를 사용하는 경우에만 설정할 수 있습니다. 기본값은 IR과 함께 설치된 cacerts.pem 파일입니다.  | 예 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| allowHostNameCNMismatch | TLS를 통해 연결할 때 CA에서 발급된 TLS/SSL 인증서 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |
| storageReference | 매핑 데이터 흐름에서 데이터를 준비하는 데 사용되는 스토리지 계정의 연결된 서비스에 대한 참조입니다. 이는 매핑 데이터 흐름에서 Hive 연결 서비스를 사용하는 경우에만 필요합니다. | 예 |

**예:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Hive 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Hive에서 데이터를 복사하려면 데이터 세트의 type 속성을 **HiveObject** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **HiveObject** 로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마 파트를 포함하는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Hive 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="hivesource-as-source"></a>HiveSource를 원본으로 설정

Hive에서 데이터를 복사하려면 복사 작업의 원본 형식을 **HiveSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **HiveSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

Hive 커넥터는 매핑 데이터 흐름에서 [인라인 데이터 세트](data-flow-source.md#inline-datasets) 원본으로 지원됩니다. 쿼리를 사용하거나 HDInsight의 Hive 테이블에서 직접 읽습니다. Hive 데이터는 데이터 흐름의 일부로 변환되기 전에 스토리지 계정에 parquet 파일로 준비됩니다. 

### <a name="source-properties"></a>원본 속성

다음 표에는 하이브 원본에서 지원하는 속성이 나열되어 있습니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 저장소 | 저장소는 `hive`여야 합니다. | 예 |  `hive` | store | 
| 형식 | 테이블 또는 쿼리에서 읽는지 여부 | 예 | `table` 또는 `query` | format |
| 스키마 이름 | 테이블에서 읽는 경우 원본 테이블의 스키마 |  예, 형식이 `table`인 경우 | String | schemaName |
| 테이블 이름 | 테이블에서 읽는 경우 테이블 이름 |   예, 형식이 `table`인 경우 | String | tableName |
| 쿼리 | 형식이 `query`인 경우 Hive 연결 서비스에 대한 원본 쿼리 | 예, 형식이 `query`인 경우 | String | Query |
| 스테이징됨 | Hive 테이블은 항상 준비됩니다. | 예 | `true` | 스테이징됨 |
| 스토리지 컨테이너 | Hive에서 읽거나 Hive에 쓰기 전에 데이터를 준비하는 데 사용되는 스토리지 컨테이너입니다. 하이브 클러스터에는 이 컨테이너에 대한 액세스 권한이 있어야 합니다. | 예 | String | storageContainer |
| 스테이징 데이터베이스 | 연결된 서비스에 지정된 사용자 계정에서 액세스할 수 있는 스키마/데이터베이스입니다. 스테이징 중에 외부 테이블을 만드는 데 사용되며 나중에 삭제됩니다. | 아니요 | `true` 또는 `false` | stagingDatabaseName |
| 사전 SQL 스크립트 | 데이터를 읽기 전에 Hive 테이블에서 실행할 SQL 코드 | 아니요 | String | preSQLs |

#### <a name="source-example"></a>원본 예

다음은 Hive 원본 구성의 예입니다.

![Hive 원본 예제](media/data-flow/hive-source.png "[Hive 원본 예제")

이러한 설정은 다음 데이터 흐름 스크립트로 변환됩니다.

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>알려진 제한 사항

* 배열, 맵, 구조체 및 공용 구조체와 같은 복합 형식은 읽을 수 없습니다. 
* Hive 커넥터는 4.0 이상 버전(Apache Hive 3.1.0)의 Azure HDInsight에서 Hive 테이블만 지원합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
