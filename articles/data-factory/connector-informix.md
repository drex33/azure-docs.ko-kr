---
title: Azure Data Factory를 사용하여 IBM Informix에서의 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 IBM Informix에서의 데이터를 복사하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 7fe423c487bfba377290cd65e4a46125768ab094
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123309020"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Azure Data Factory를 사용하여 IBM Informix에서의 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 IBM Informix 데이터 저장소에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Informix 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Informix 원본에서 지원되는 싱크 데이터 저장소로 또는 지원되는 원본 데이터 저장소에서 Informix 싱크로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

이 Informix 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- 통합 런타임 컴퓨터에 데이터 저장소에 대한 Informix ODBC 드라이버를 설치합니다. 드라이버 설치 및 설정에 대한 자세한 내용은 IBM 지식 센터의 [Informix ODBC 드라이버 가이드](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) 문서를 참조하거나, 드라이버 설치 지침은 IBM 지원 팀에 문의하십시오.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-informix-using-ui"></a>UI를 사용하여 Informix에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Informix에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만드는 스크린샷.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Informix를 검색하고 Informix 커넥터를 선택합니다.

   :::image type="content" source="media/connector-informix/informix-connector.png" alt-text="Informix 커넥터의 스크린샷.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-informix/configure-informix-linked-service.png" alt-text="Informix의 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Informix 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Informix 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Informix** 로 설정해야 합니다. | 예 |
| connectionString | 자격 증명 부분을 제외한 ODBC 연결 문자열입니다. 연결 문자열을 지정하거나 Integration Runtime 컴퓨터에 설정한 시스템 DSN(데이터 원본 이름)을 사용할 수 있습니다(이에 따라 연결된 서비스에서 자격 증명 부분도 계속 지정해야 함). <br> Azure Key Vault에 암호를 넣고 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault의 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요.| 예 |
| authenticationType | Informix 데이터 저장소에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 **Basic** 및 **Anonymous** 입니다. | 예 |
| userName | 기본 인증을 사용하는 경우 사용자 이름을 지정합니다. | 예 |
| password | userName에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| 자격 증명(credential) | 드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 이 필드를 SecureString으로 표시합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Informix 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Informix의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 **InformixTable** 로 설정해야 합니다. | 예 |
| tableName | Informix에 있는 테이블의 이름입니다. | 원본: 아니요(작업 원본에서 "query"가 지정된 경우)<br/>싱크: 예 |

**예제**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Informix 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="informix-as-source"></a>원본으로 Informix

Informix에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **InformixSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>싱크로 Informix

Informix로 데이터를 복사하기 위해 복사 작업 **싱크** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **InformixSink** 로 설정해야 합니다. | 예 |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예제: "00:30:00"(30분). |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 0 - 자동 검색됨) |
| preCopyScript |각 실행 시 데이터 저장소에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
