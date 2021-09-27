---
title: Phoenix에서 데이터 복사
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 활동을 사용하여 Phoenix에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 49ff77848b2a467e13dba6fdb85f7bac1560ff45
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793097"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics 사용하여 Phoenix에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 작업을 사용하여 Phoenix에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Phoenix 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Phoenix에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

이 서비스는 연결을 사용하도록 설정하는 기본 제공 드라이버를 제공하므로 이 커넥터를 사용하여 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-phoenix-using-ui"></a>UI를 사용하여 Phoenix에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Phoenix에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Phoenix를 검색하고 Phoenix 커넥터를 선택합니다.

   :::image type="content" source="media/connector-phoenix/phoenix-connector.png" alt-text="Phoenix 커넥터의 스크린샷.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-phoenix/configure-phoenix-linked-service.png" alt-text="Phoenix의 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Phoenix 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Phoenix 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Phoenix** 로 설정해야 합니다. | 예 |
| host | Phoenix 서버의 IP 주소 또는 호스트 이름입니다. 즉, 192.168.222.160입니다.  | 예 |
| 포트 | Phoenix 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. 기본값은 8765입니다. Azure HDInsights에 연결하는 경우 포트를 443으로 지정합니다. | 예 |
| httpPath | Phoenix 서버에 해당하는 부분 URL입니다. 즉, /gateway/sandbox/phoenix/version입니다. HDInsights를 사용 중인 경우 `/hbasephoenix0`을 지정합니다.  | 예 |
| authenticationType | Phoenix 서버에 연결하는 데 사용되는 인증 메커니즘입니다. <br/>허용되는 값은 **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** 입니다. | 예 |
| 사용자 이름 | Phoenix 서버에 연결하는 데 사용되는 사용자 이름입니다.  | 예 |
| password | 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| enableSsl | TLS를 사용하여 서버 연결을 암호화할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| trustedCertPath | TLS를 통해 연결할 때 서버를 확인하는 데 사용되는 신뢰할 수 있는 CA 인증서를 포함하는 .pem 파일의 전체 경로입니다. 이 속성은 자체 호스팅 IR에서 TLS를 사용하는 경우에만 설정할 수 있습니다. 기본값은 IR과 함께 설치된 cacerts.pem 파일입니다.  | 예 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| allowHostNameCNMismatch | TLS를 통해 연결할 때 CA에서 발급된 TLS/SSL 인증서 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>클러스터가 HDInsight와 같은 고정 세션을 지원하지 않으면 Http 경로 설정의 끝에 노드 인덱스를 명시적으로 추가합니다. 예를 들어 `/hbasephoenix` 대신 `/hbasephoenix0`을 지정합니다.

**예:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Phoenix 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Phoenix에서 데이터를 복사하려면 데이터 세트의 type 속성을 **PhoenixObject** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **PhoenixObject** 로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마가 있는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`를 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Phoenix 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="phoenix-as-source"></a>Phoenix를 원본으로

Phoenix에서 데이터를 복사하려면 복사 작업의 원본 형식을 **PhoenixSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **PhoenixSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
