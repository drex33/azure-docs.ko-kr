---
title: Search 인덱스에 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Azure Search 인덱스에 데이터를 푸시하거나 복사하는 방법에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: fbeeb112712afa5a47401d10b3f4b2d9f01d1713
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313750"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Cognitive Search 인덱스에 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-search-connector.md)
> * [현재 버전](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cognitive Search 인덱스로 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

지원되는 모든 원본 데이터 저장소에서 Search 인덱스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-search-using-ui"></a>UI를 사용하여 Azure Search에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Azure Search에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI로 연결된 새 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. Search를 검색하고 Azure Search 커넥터를 선택합니다.

   :::image type="content" source="media/connector-azure-search/azure-search-connector.png" alt-text="Azure Search 커넥터를 선택합니다.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-azure-search/configure-azure-search-linked-service.png" alt-text="Azure Search에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Azure Cognitive Search 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cognitive Search 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureSearch** 로 설정되어야 합니다. | 예 |
| url | 검색 서비스에 대한 URL입니다. | 예 |
| key | 검색 서비스에 대한 관리 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

> [!IMPORTANT]
> 클라우드 데이터 저장소에서 검색 인덱스로 데이터를 복사하는 경우 Azure Cognitive Search 연결된 서비스에서 connactVia의 명시적 영역을 통해 Azure 통합 런타임을 참조해야 합니다. 영역을 검색 서비스가 있는 위치로 설정합니다. [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)에서 자세히 알아봅니다.

**예:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Cognitive Search 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Azure Cognitive Search에 데이터를 복사할 수 있도록 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **AzureSearchIndex** 로 설정해야 합니다. | 예 |
| indexName | 검색 인덱스의 이름입니다. Data Factory는 인덱스를 만들지 않습니다. 인덱스는 Azure Cognitive Search에 있어야 합니다. | 예 |

**예:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Cognitive Search 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="azure-cognitive-search-as-sink"></a>싱크로서의 Azure Cognitive Search

Azure Cognitive Search에 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureSearchIndexSink** 로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **AzureSearchIndexSink** 로 설정해야 합니다. | 예 |
| writeBehavior | 문서가 인덱스에 이미 있는 경우 병합할지 또는 바꿀지를 지정합니다. [WriteBehavior 속성](#writebehavior-property)을 참조하세요.<br/><br/>허용되는 값은 **Merge**(기본값) 및 **Upload** 입니다. | 예 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달한 경우 검색 인덱스에 데이터를 업로드합니다. 자세한 내용은 [WriteBatchSize 속성](#writebatchsize-property)을 참조하세요.<br/><br/>허용되는 값은 정수 1~1,000이고 기본값은 1,000입니다. | 예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

### <a name="writebehavior-property"></a>WriteBehavior 속성

데이터를 쓸 때 AzureSearchSink가 삽입됩니다. 즉, 문서를 작성할 때 문서 키가 검색 인덱스에 이미 있는 경우 Azure Cognitive Search는 충돌 예외를 throw하지 않는 대신 기존 문서를 업데이트합니다.

AzureSearchSink는 AzureSearch SDK를 사용하여 다음 두 가지 삽입 동작을 제공합니다.

- **병합**: 새 문서의 모든 열을 기존 문서와 결합합니다. 새 문서에서 null 값을 가진 열의 경우 기존 문서의 값이 유지됩니다.
- **업로드**: 새 문서가 기존 문서를 대체합니다. 새 문서에 지정되지 않은 열의 경우 기본 문서에 null이 아닌 값이 있는지 여부에 상관없이 값이 null로 설정됩니다.

기본 동작은 **병합** 입니다.

### <a name="writebatchsize-property"></a>writeBatchSize 속성

Azure Cognitive Search 서비스는 일괄 처리로 문서 작성을 지원합니다. 일괄 처리는 1~1,000개의 동작을 포함할 수 있습니다. 하나의 동작에서 하나의 문서를 처리하여 업로드/병합 작업을 수행합니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>데이터 형식 지원

다음 표에서는 Azure Cognitive Search 데이터 형식이 지원되는지 여부를 지정합니다.

| Azure Cognitive Search 데이터 형식 | Azure Cognitive Search 싱크에서 지원됨 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| 부울 | Y |
| DataTimeOffset | Y |
| 문자열 배열 | N |
| GeographyPoint | N |

현재 다른 데이터 형식(예: ComplexType)은 지원되지 않습니다. Azure Cognitive Search 지원 데이터 형식의 전체 목록은 [지원되는 데이터 형식(Azure Cognitive Search)](/rest/api/searchservice/supported-data-types)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.