---
title: ServiceNow에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인의 복사 활동을 사용하여 ServiceNow에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 505874b03e5a5c187f0bb5ea638d09808ffb418c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045620"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics 사용하여 ServiceNow에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인의 복사 작업을 사용하여 ServiceNow에서 데이터를 복사하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 ServiceNow 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

ServiceNow에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

이 서비스는 연결을 활성화하는 기본 제공 드라이버를 제공합니다.  따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-servicenow-using-ui"></a>UI를 사용하여 ServiceNow에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 ServiceNow에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. ServiceNow를 검색하고 ServiceNow 커넥터를 선택합니다.

    :::image type="content" source="media/connector-servicenow/servicenow-connector.png" alt-text="ServiceNow 커넥터를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-servicenow/configure-servicenow-linked-service.png" alt-text="ServiceNow에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 ServiceNow 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 ServiceNow 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **ServiceNow** 로 설정해야 합니다. | 예 |
| 엔드포인트(endpoint) | ServiceNow 서버(`http://<instance>.service-now.com`)의 엔드포인트입니다.  | 예 |
| authenticationType | 사용할 인증 유형입니다. <br/>허용되는 값은 **Basic**, **OAuth2** 입니다. | 예 |
| 사용자 이름 | 기본 및 OAuth2 인증을 위해 ServiceNow 서버에 연결하는 데 사용되는 사용자 이름입니다.  | 예 |
| password | 기본 및 OAuth2 인증의 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| clientId | OAuth2 인증의 클라이언트 ID입니다.  | 예 |
| clientSecret | OAuth2 인증의 클라이언트 암호입니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| useHostVerification | TLS를 통해 연결할 때 서버 인증서의 호스트 이름을 서버의 호스트 이름과 일치시킬지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| usePeerVerification | TLS를 통해 연결할 때 서버의 ID 확인 여부를 지정합니다. 기본값은 true입니다.  | 예 |

**예:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 ServiceNow 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

ServiceNow에서 데이터를 복사하려면 데이터 세트의 type 속성을 **ServiceNowObject** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **ServiceNowObject** 로 설정해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ServiceNow 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="servicenow-as-source"></a>ServiceNow 원본

ServiceNow에서 데이터를 복사하려면 복사 작업의 원본 형식을 **ServiceNowSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **ServiceNowSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Actual.alm_asset"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

쿼리에서 ServiceNow에 대해 스키마 및 열을 지정하는 경우 다음에 유의하고, **복사 성능에 미치는 영향에 대한 [성능 팁](#performance-tips)을 참조하세요**.

- **스키마:** ServiceNow 쿼리에서 또는 로 스키마를 `Actual` `Display` 지정합니다. 이 스키마는 `sysparm_display_value` [ServiceNow REST API를](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)호출할 때 의 매개 변수를 true 또는 false로 볼 수 있습니다. 
- **열:**`Actual` 스키마의 실제 값에 대한 열 이름은 `[column name]_value`이며 `Display` 스키마의 표시 값에 대한 열 이름은 `[column name]_display_value`입니다. 열 이름은 쿼리에 사용되는 스키마에 매핑되어야 합니다.

**샘플 쿼리:** 
`SELECT col_value FROM Actual.alm_asset` 또는 `SELECT col_display_value FROM Display.alm_asset`

**예:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>성능 팁

### <a name="schema-to-use"></a>사용할 스키마

ServiceNow에는 2개의 다른 스키마가 있습니다. 하나는 실제 데이터를 반환하는 **"Actual"** 이고, 다른 하나는 데이터의 표시 값을 반환하는 **"Display"** 입니다. 

쿼리에 필터가 있는 경우 복사 성능이 더 좋은 "Actual" 스키마를 사용합니다. "Actual" 스키마에 대해 쿼리하는 경우 ServiceNow는 데이터를 인출하여 필터링된 결과 세트만 반환할 경우 기본적으로 필터를 지원하지만, "Display" 스키마에 대해 쿼리할 때는 ADF가 모든 데이터를 검색하고 내부적으로 필터를 적용합니다.

### <a name="index"></a>인덱스

ServiceNow 테이블 인덱스는 쿼리 성능 향상에 도움이 될 수 있습니다. [테이블 인덱스 만들기](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/table-administration/task/t_CreateCustomIndex.html)를 참조하세요.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
