---
title: Azure Data Factory를 사용하여 Xero에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Xero에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: c95efb768dc66dd35a88d0cd57d37e4d7e43ce3c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123311132"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Xero에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Xero에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Xero 커넥터는 다음과 같은 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Xero에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Xero 커넥터는 다음을 지원합니다.

- OAuth 2.0 및 OAuth 1.0 인증 OAuth 1.0의 경우, 커넥터가 Xero [프라이빗 애플리케이션](https://developer.xero.com/documentation/getting-started/getting-started-guide)을 지원하지만, 퍼블릭 애플리케이션은 지원하지 않습니다.
- "보고서"를 제외한 모든 Xero 테이블(API 엔드포인트)

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-xero-using-ui"></a>UI를 사용하여 Xero에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Xero에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI로 연결된 새 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. Xero를 검색하고 Xero 커넥터를 선택합니다.

   :::image type="content" source="media/connector-xero/xero-connector.png" alt-text="Xero 커넥터를 선택합니다.":::    


1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/connector-xero/configure-xero-linked-service.png" alt-text="Xero에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Xero 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Xero 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Xero** 로 설정해야 합니다. | 예 |
| connectionProperties | Xero에 연결하는 방법을 정의하는 속성 그룹입니다. | 예 |
| ***`connectionProperties`*** 의 하위 속성: | | |
| 호스트 | Xero 서버(`api.xero.com`)의 엔드포인트입니다.  | 예 |
| authenticationType | 허용되는 값은 `OAuth_2.0`와 `OAuth_1.0`입니다. | 예 |
| consumerKey | OAuth 2.0에 대해 Xero 애플리케이션의 **클라이언트 ID** 를 지정합니다.<br>OAuth 1.0에 대해 Xero 애플리케이션에 연결된 소비자 키를 지정합니다.<br>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| privateKey | OAuth 2.0에 대해 Xero 애플리케이션에 대한 **클라이언트 비밀** 을 지정합니다.<br>OAuth 1.0에 대해 Xero 프라이빗 애플리케이션용으로 생성된 .pem 파일의 프라이빗 키를 지정합니다. [퍼블릭/프라이빗 키 쌍 만들기](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key)를 참조하세요. **512의 numbits로 privatekey.pem을 생성** 하기 위해 `openssl genrsa -out privatekey.pem 512`을 사용합니다. 1024는 지원되지 않습니다. Unix 줄 끝(\n)을 포함하여 .pem 파일의 모든 텍스트를 포함합니다. 아래 샘플을 참조하세요.<br/><br>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenantId | Xero 애플리케이션과 연결된 테넌트 ID입니다. OAuth 2.0 인증에 적용됩니다.<br>[액세스 권한이 부여된 테넌트 확인 섹션](https://developer.xero.com/documentation/oauth2/auth-flow)에서 테넌트 ID를 가져오는 방법에 대해 알아보기. | 예(OAuth 2.0 인증의 경우) |
| refreshToken | OAuth 2.0 인증에 적용됩니다.<br/>OAuth 2.0 새로 고침 토큰은 Xero 애플리케이션과 연결되며, 액세스 토큰을 새로 고치는 데 사용됩니다. 액세스 토큰은 30분 후에 만료됩니다. [이 문서](https://developer.xero.com/documentation/oauth2/auth-flow)에서는 Xero 권한 부여 흐름이 작동하는 방법 및 새로 고침 토큰을 가져오는 방법에 대해 알아봅니다. 새로 고침 토큰을 가져오려면, [offline_access 범위](https://developer.xero.com/documentation/oauth2/scopes)를 요청해야 합니다. <br/>**인식 제한**: Xero는 액세스 토큰 새로 고침에 사용된 후 새로 고침 토큰을 다시 설정한다는 점을 참고합니다. 조작 가능한 워크로드의 경우, 각 복사 작업을 실행하기 전에 ADF에서 사용할 유효한 새로 고침 토큰을 설정해야 합니다.<br/>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예(OAuth 2.0 인증의 경우) |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| useHostVerification | TLS를 통한 연결 시, 서버 인증서의 호스트 이름을 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 true입니다.  | 예 |
| usePeerVerification | TLS를 통해 연결할 때 서버의 ID 확인 여부를 지정합니다. 기본값은 true입니다.  | 예 |

**예: OAuth 2.0 인증**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**예: OAuth 1.0 인증**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**샘플 프라이빗 키 값:**

Unix 줄 끝(\n)을 포함하여 .pem 파일의 모든 텍스트를 포함합니다.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Xero 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Xero에서 데이터를 복사하려면 데이터 세트의 type 속성을 **XeroObject** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **XeroObject** 로 설정해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Xero 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="xero-as-source"></a>Xero 원본

Xero에서 데이터를 복사하려면 복사 작업의 원본 형식을 **XeroSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **XeroSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Contacts"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Xero 쿼리를 지정할 때 다음 사항에 유의합니다.

- 복잡한 항목이 있는 테이블은 여러 테이블로 분할됩니다. 예를 들어 은행 거래에는 "LineItems"라는 복합 데이터 구조가 있기 때문에, 은행 거래의 데이터는 `Bank_Transaction` 및 `Bank_Transaction_Line_Items` 테이블로 매핑되고 `Bank_Transaction_ID`가 이들을 연결하는 외래 키로 사용됩니다.

- Xero 데이터는 `Minimal`(기본값) 및 `Complete`라는 두 가지 스키마를 통해 사용할 수 있습니다. Complete 스키마는 원하는 쿼리를 만들기 전에 추가 데이터(예: ID 열)가 필요한 필수 구성 요소 호출 테이블을 포함합니다.

다음 테이블에는 Minimal 및 Complete 스키마와 동일한 정보가 있습니다. API 호출 수를 줄이기 위해 Minimal 스키마(기본값)를 사용합니다.

- Bank_Transactions
- Contact_Groups 
- 연락처 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- 송장 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

다음 테이블은 complete 스키마로만 쿼리할 수 있습니다.

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
복사 작업에서 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
