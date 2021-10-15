---
title: OData 원본에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인의 복사 작업을 사용 하 여 OData 원본에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 10/13/2021
ms.author: jianleishen
ms.openlocfilehash: c1ac63dde4dd489449db5db913aeb55f5d3de3ad
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064972"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 OData 원본에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-odata-connector.md)
> * [현재 버전](connector-odata.md)

이 문서에서는 Azure Data Factory 또는 Synapse Analytics 파이프라인에서 복사 작업을 사용 하 여 OData 원본에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업](copy-activity-overview.md)을 기준으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 OData 커넥터는 다음과 같은 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

OData 소스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 OData 커넥터는 다음을 지원합니다.

- OData 버전 3.0 및 4.0
- **익명**, **기본**, **Windows**, **AAD 서비스 주체** 등의 인증 중 하나를 사용한 데이터 복사

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-odata-store-using-ui"></a>UI를 사용하여 OData 저장소에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 OData 저장소에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. OData를 검색하고 OData 커넥터를 선택합니다.

    :::image type="content" source="media/connector-odata/odata-connector.png" alt-text="OData 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-odata/configure-odata-linked-service.png" alt-text="OData 저장소의 연결된 서비스 구성의 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 OData 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

OData 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **OData** 로 설정해야 합니다. |예 |
| url | OData 서비스의 루트 URL입니다. |예 |
| authenticationType | OData 원본에 연결하는 데 사용되는 인증 형식입니다. 허용되는 값은 **익명**, **기본**, **Windows**, **AadServicePrincipal** 입니다. 사용자 기반 OAuth는 지원되지 않습니다. `authHeader` 속성에서 인증 헤더를 추가로 구성할 수 있습니다.| 예 |
| authHeaders | 인증을 위한 추가 HTTP 요청 헤더입니다.<br/> 예를 들어 API 키 인증을 사용하려면 인증 유형을 “Anonymous”로 선택하고 헤더에 API 키를 지정할 수 있습니다. | 예 |
| userName | Basic 또는 Windows 인증을 사용할 경우 **userName** 을 지정합니다. | 예 |
| password | **userName** 에 지정한 사용자 계정의 **password** 를 지정합니다. 이 필드를 **SecureString** 형식으로 표시하여 안전하게 저장합니다. 또한 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 예 |
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| aadServicePrincipalCredentialType | 서비스 주체 인증에 사용할 자격 증명 유형을 지정합니다. 허용되는 값은 `ServicePrincipalKey` 또는 `ServicePrincipalCert`입니다. | 예 |
| servicePrincipalKey | Azure Active Directory 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| servicePrincipalEmbeddedCert | Azure Active Directory에 등록된 애플리케이션의 base64로 인코딩된 인증서를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| servicePrincipalEmbeddedCertPassword | 인증서가 암호로 보호되는 경우 인증서의 암호를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다.  | 예|
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스를 지정합니다.| 예 |
| azureCloudType | 서비스 주체 인증의 경우 AAD 애플리케이션이 등록된 Azure 클라우드 환경의 형식을 지정합니다. <br/> 허용되는 값은 **AzurePublic**, **AzureChina**, **AzureUsGovernment**, **AzureGermany** 입니다. 기본적으로 이 서비스의 클라우드 환경이 사용됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 요소](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

**예제 1: 익명 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: 기본 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**예제 3: Windows 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**예제 4: 서비스 주체 키 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**예제 5: 서비스 주체 인증서 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**예제 6: API 키 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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

이 섹션에서는 OData 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

OData에서 데이터를 복사하려면 데이터 세트의 **type** 속성을 **ODataResource** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **ODataResource** 로 설정해야 합니다. | 예 |
| 경로 | OData 리소스에 대한 경로입니다. | 예 |

**예제**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 OData 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="odata-as-source"></a>OData를 원본으로

OData에서 데이터를 복사하기 위해 복사 작업의 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 source 섹션의 **type** 속성은 **ODataSource** 로 설정해야 합니다. | 예 |
| Query | 데이터 필터링에 대한 OData 쿼리 옵션입니다. 예: `"$select=Name,Description&$top=5"`.<br/><br/>**참고**: OData 커넥터가 결합된 URL(`[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`)에서 데이터를 복사합니다. 자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 지정하지 않으면 기본값은 **00:30:00**(30분)입니다. | 예 |

**예제**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 형식의 원본을 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="data-type-mapping-for-odata"></a>OData에 대한 데이터 형식 매핑

OData에서 데이터를 복사 하는 경우 서비스 내에서 내부적으로 사용 되는 데이터 형식 및 OData 데이터 형식 간에 다음과 같은 매핑이 사용 됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법을 알아보려면 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| OData 데이터 형식 | 중간 서비스 데이터 형식 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData 복합 데이터 형식(예: **Object**)은 지원되지 않습니다.

## <a name="copy-data-from-project-online"></a>Project Online에서 데이터 복사

Project Online에는 Azure Data Factory에서 지원 하지 않는 사용자 기반 OAuth가 필요 합니다. Project Online에서 데이터를 복사하려면 OData 커넥터와 Postman과 같은 도구에서 얻은 액세스 토큰을 사용하면 됩니다.

> [!CAUTION]
> 액세스 토큰은 기본적으로 1시간 후에 만료되며, 만료되면 새 액세스 토큰을 받아야 합니다.

1. **Postman** 을 사용하여 액세스 토큰을 가져옵니다.

   1. Postman 웹 사이트의 **권한 부여** 탭으로 이동합니다.
   1. **형식** 상자에서 **OAuth 2.0** 을 선택하고 **Add authorization data to(권한 부여 데이터 추가 대상)** 상자에서 **요청 헤더** 를 선택합니다.
   1. 새 액세스 토큰을 가져오려면 **Configure New Token(새 토큰 구성)** 페이지에 다음 정보를 입력합니다. 
      - **권한 부여 유형**: **인증 코드** 를 선택합니다.
      - **콜백 URL**: `https://www.localhost.com/`을 입력합니다. 
      - **Auth URL(인증 URL)** : `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com`을 입력합니다. `<your tenant name>`을 사용자 고유의 테넌트 이름으로 바꿉니다. 
      - **Access Token URL(액세스 토큰 URL)** : `https://login.microsoftonline.com/common/oauth2/token`을 입력합니다.
      - **클라이언트 ID**: AAD 서비스 주체 ID를 입력합니다.
      - **클라이언트 암호**: 서비스 주체 비밀을 입력합니다.
      - **클라이언트 인증**: **Send as Basic Auth header(기본 인증 헤더로 보내기)** 를 선택합니다.
     
   1. 사용자 이름 및 암호를 사용하여 로그인하라는 메시지가 표시됩니다.
   1. 액세스 토큰을 가져오면 다음 단계를 위해 토큰을 복사하여 저장하세요.
   
    :::image type="content" source="./media/connector-odata/odata-project-online-postman-access-token-inline.png" alt-text="Postman을 사용 하 여 액세스 토큰을 가져오는 스크린샷" lightbox="./media/connector-odata/odata-project-online-postman-access-token-expanded.png":::        

1. OData 연결된 서비스 만들기:
    - **서비스 URL**: `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata`를 입력합니다. `<your tenant name>`을 사용자 고유의 테넌트 이름으로 바꿉니다. 
    - **인증 형식**: **Anonymous** 를 선택합니다.
    - **인증 헤더**:
        - **속성 이름**: **Authorization** 을 선택합니다.
        - **값**: `Bearer <access token from step 1>`를 입력합니다.
    - 연결된 서비스 테스트

    :::image type="content" source="./media/connector-odata/odata-project-online-linked-service.png" alt-text="OData 연결된 서비스 만들기":::

1. OData 데이터 세트 만들기:
    1. 2 단계에서 만든 OData 연결된 서비스를 사용하여 데이터 세트를 만듭니다.
    1. 데이터 미리 보기
 
    :::image type="content" source="./media/connector-odata/odata-project-online-preview-data.png" alt-text="데이터 미리 보기":::
 


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.