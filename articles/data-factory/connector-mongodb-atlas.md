---
title: MongoDB Atlas에서 또는 MongoDB Atlas로 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 MongoDB Atlas에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 MongoDB Atlas로 데이터를 복사하는 방법을 알아봅니다.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: b2c174fce6e79b574276d1f95cfc4e1c93c321cd
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318381"
---
# <a name="copy-data-from-or-to-mongodb-atlas-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MongoDB Atlas에서 또는 MongoDB Atlas로 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MongoDB Atlas 데이터베이스에서 또는 MongoDB Atlas 데이터베이스로 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

MongoDB Atlas 데이터베이스에서 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 지원되는 원본 데이터 저장소에서 MongoDB Atlas 데이터베이스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 MongoDB Atlas 커넥터는 **버전 4.2까지** 지원합니다.

## <a name="prerequisites"></a>필수 구성 요소

복사에 Azure Integration Runtime을 사용하는 경우 유효한 지역의 [Azure Integration Runtime](azure-integration-runtime-ip-addresses.md) IP를 MongoDB Atlas IP 액세스 목록에 추가해야 합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-mongodb-atlas-using-ui"></a>UI를 사용하여 MongoDB Atlas에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 MongoDB Atlas에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. MongoDB를 검색하고 MongoDB Atlas 커넥터를 선택합니다.

    :::image type="content" source="media/connector-mongodb-atlas/mongodb-atlas-connector.png" alt-text="MongoDB Atlas 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-mongodb-atlas/configure-mongodb-atlas-linked-service.png" alt-text="MongoDB Atlas에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보



다음 섹션에서는 MongoDB Atlas 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

MongoDB Atlas 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **MongoDbAtlas** 로 설정해야 합니다. |예 |
| connectionString |MongoDB Atlas 연결 문자열을 지정합니다(예: `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>`). <br/><br /> Azure Key Vault에 연결 문자열을 넣을 수도 있습니다. 자세한 내용은 [Azure Key Vault의 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. |예 |
| 데이터베이스 | 액세스하려는 데이터베이스 이름입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. MongoDB Atlas 데이터 세트에 대해 다음 속성을 지원합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **MongoDbAtlasCollection** 으로 설정해야 합니다. | 예 |
| collectionName |MongoDB Atlas 데이터베이스에 있는 컬렉션의 이름입니다. |예 |

**예:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MongoDB Atlas 원본 및 싱크에서 지원하는 속성의 목록을 제공합니다.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas의 출처

복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **MongoDbAtlasSource** 로 설정해야 합니다. | 예 |
| filter | 쿼리 연산자를 사용하여 선택 영역 필터를 지정합니다. 컬렉션의 모든 문서를 반환하려면 이 매개 변수를 생략하거나 빈 문서({})를 전달합니다. | 예 |
| cursorMethods.project | 프로젝션에 대한 문서에서 반환할 필드를 지정합니다. 일치하는 문서에서 모든 필드를 반환하려면 이 매개 변수를 생략합니다. | 예 |
| cursorMethods.sort | 쿼리가 일치하는 문서를 반환하는 순서를 지정합니다. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)를 참조하세요. | 예 |
| cursorMethods.limit | 서버에서 반환하는 문서의 최대 수를 지정합니다. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)를 참조하세요.  | 예 |
| cursorMethods.skip | MongoDB Atlas가 결과를 반환하기 시작하는 위치에서 건너뛸 문서 수를 지정합니다. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)을 참조하세요. | 예 |
| batchSize | MongoDB Atlas 인스턴스의 응답을 각각 일괄 처리로 반환할 문서 수를 지정합니다. 대부분의 경우 일괄 처리 크기를 수정해도 사용자 또는 애플리케이션에 영향이 없습니다. Cosmos DB는 각 일괄 처리가 문서 크기의 batchSize 수의 합인 40MB를 초과할 수 없도록 제한하므로 문서 크기가 대규모인 경우 이 값을 줄입니다. | 예<br/>(기본값은 **100**) |

>[!TIP]
>ADF는 **Strict 모드** 로 BSON 문서를 사용하는 것을 지원합니다. 필터 쿼리가 Shell 모드가 아닌 Strict 모드에 있는지 확인합니다. 자세한 설명은 [MongoDB 설명서](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)를 참조하세요.

**예:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="mongodb-atlas-as-sink"></a>싱크의 경우 MongoDB Atlas

복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성을 **MongoDbAtlasSink** 로 설정해야 합니다. |예 |
| writeBehavior |MongoDB Atlas에 데이터를 쓰는 방법을 설명합니다. 허용되는 값은 **insert** 및 **upsert** 입니다.<br/><br/>**upsert** 동작은 동일한 `_id`의 문서가 이미 존재하는 경우 문서를 바꾸는 것으로, 존재하지 않는 경우 문서를 삽입하는 것입니다.<br /><br />**참고**: `_id`가 원래 문서 또는 열 매핑에 지정되지 않은 경우 Data Factory는 문서에 대한 `_id`를 자동으로 생성합니다. 즉, **upsert** 가 예상대로 작동하려면 문서에 ID가 있는지 확인해야 합니다. |예<br />(기본값: **insert**) |
| writeBatchSize | **writeBatchSize** 속성은 각 일괄 처리에서 작성할 문서의 크기를 제어합니다. 성능을 개선하기 위해 **writeBatchSize** 에 대한 값을 늘리고 문서 크기가 커지는 경우 값을 줄여 볼 수 있습니다. |예<br />(기본값: **10,000**) |
| writeBatchTimeout | 시간 초과 전 배치 삽입 작업을 완료하기 위한 대기 시간입니다. 허용된 값은 시간 범위입니다. | 예<br/>(기본값은 **00:30:00** - 30분) |

>[!TIP]
>JSON 문서를 그대로 가져오려면 [JSON 문서 가져오기 또는 내보내기](#import-and-export-json-documents) 섹션을 참조하세요. 테이블 형식 데이터를 복사하려면 [스키마 매핑](#schema-mapping)을 참조하세요.

**예제**

```json
"activities":[
    {
        "name": "CopyToMongoDBAtlas",
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
                "type": "MongoDbAtlasSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON 문서 가져오기 및 내보내기

이 MongoDB Atlas 커넥터를 사용하여 다음을 쉽게 수행할 수 있습니다.

* 두 MongoDB Atlas 컬렉션 간에 데이터를 있는 그대로 복사
* Azure Cosmos DB, Azure Blob storage, Azure Data Lake Store 및 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등의 다양한 원본에서 MongoDB Atlas로 JSON 문서 가져오기
* MongoDB Atlas 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기

이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "구조"(*스키마* 라고도 함) 섹션을 건너뛰세요.


## <a name="schema-mapping"></a>스키마 매핑

데이터를 MongoDB Atlas에서 테이블 형식 싱크로 또는 그 반대로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
