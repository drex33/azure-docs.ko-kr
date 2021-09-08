---
title: Azure Data Factory를 사용하여 Concur에서 데이터 복사(미리 보기)
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Concur에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: 81c27f6e137c36c5856f823a6f490f5ba0d16a2f
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123313836"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Azure Data Factory를 사용하여 Concur에서 데이터 복사(미리 보기)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Concur에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!IMPORTANT]
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 사용해 보고 피드백을 제공할 수 있습니다. 솔루션의 미리 보기 커넥터에 종속성을 적용하려면 [Azure 지원](https://azure.microsoft.com/support/)에 문의하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Concur 커넥터는 다음과 같은 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Concur에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

> [!NOTE]
> 파트너 계정은 현재 지원되지 않습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-concur-using-ui"></a>UI를 사용하여 Concur에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Concur에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만드는 스크린샷.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. Concur를 검색하고 Concur 커넥터를 선택합니다.

   :::image type="content" source="media/connector-concur/concur-connector.png" alt-text="Concur 커넥터의 스크린샷.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-concur/configure-concur-linked-service.png" alt-text="Concur의 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Concur 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Concur 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Concur** 로 설정해야 합니다. | 예 |
| connectionProperties | Concur에 연결하는 방법을 정의하는 속성 그룹입니다. | 예 |
| ***`connectionProperties`*** 의 하위 속성: | | |
| authenticationType | 사용 가능한 값은 `OAuth_2.0_Bearer` 및 `OAuth_2.0`입니다(레거시). OAuth 2.0 인증 옵션은 2017년 2월부터 사용되지 않는 이전 Concur API와 호환됩니다. | 예 |
| host | Concur 서버의 엔드포인트(예: `implementation.concursolutions.com`)입니다.  | 예 |
| baseUrl | Concur의 권한 부여 URL의 기본 URL입니다. | `OAuth_2.0_Bearer` 인증의 경우 예 |
| clientId | Concur App Management에서 제공하는 애플리케이션 클라이언트 ID입니다.  | 예 |
| clientSecret | 클라이언트 ID에 해당하는 클라이언트 비밀입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | `OAuth_2.0_Bearer` 인증의 경우 예 |
| 사용자 이름 | Concur 서비스에 액세스하는 데 사용되는 사용자 이름입니다. | 예 |
| password | username 필드에서 제공한 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| useHostVerification | TLS를 통해 연결할 때 서버 인증서의 호스트 이름을 서버의 호스트 이름과 일치시킬지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| usePeerVerification | TLS를 통해 연결할 때 서버의 ID 확인 여부를 지정합니다. 기본값은 true입니다.  | 예 |

**예:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**예(레거시):**

다음은 `connectionProperties` 및 `OAuth_2.0` 인증을 사용하지 않고 레거시 연결된 서비스 모델입니다.

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Concur 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Concur에서 데이터를 복사하려면 데이터 세트의 type 속성을 **ConcurObject** 로 설정합니다. 이 형식의 데이터 세트에는 추가적인 형식별 속성이 없습니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형태 속성을 **ConcurObject** 로 설정해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |


**예제**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Concur 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="concursource-as-source"></a>ConcurSource를 원본으로 설정

Concur에서 데이터를 복사하려면 복사 작업의 원본 형식을 **ConcurSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **ConcurSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Opportunities where Id = xxx "` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
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
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
