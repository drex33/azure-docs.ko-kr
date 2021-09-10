---
title: Azure Data Factory를 사용하여 REST 엔드포인트에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에서 클라우드 또는 온-프레미스 REST 원본으로부터 지원되는 싱크 데이터 저장소로 또는 지원되는 원본 데이터 저장소로부터 REST 싱크로 복사 작업을 사용하여 데이터를 복사하고 Data Flow를 사용하여 데이터를 변환하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: makromer
ms.openlocfilehash: 16bb4ac7062c39ad57becce4d5280ed227160690
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123311588"
---
# <a name="copy-and-transform-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 REST 엔드포인트에서 데이터 복사 및 변환
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 REST 엔드포인트에서 그리고 REST 엔드포인트로 데이터를 복사하는 방법을 간략히 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

이 REST 커넥터와 [HTTP 커넥터](connector-http.md)와 [웹 테이블 커넥터](connector-web-table.md)의 차이점은 다음과 같습니다.

- **REST 커넥터** 는 특히 RESTful API에서 데이터를 복사하는 것을 지원합니다. 
- **HTTP 커넥터** 는 파일을 다운로드하는 등 모든 HTTP 엔드포인트에서 데이터를 검색하는 데 일반적입니다. 이 REST 커넥터 전에는 HTTP 커넥터를 사용하여 지원은 되지만 REST 커넥터와 비교할 때 기능이 적은 RESTful API에서 데이터를 복사할 수도 있습니다.
- **웹 테이블 커넥터** 는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.

## <a name="supported-capabilities"></a>지원되는 기능

REST 원본에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 REST 싱크에 복사할 수도 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 일반 REST 커넥터는 다음을 지원합니다.

- **GET** 또는 **POST** 메서드를 사용하여 데이터를 REST 엔드포인트로 복사하고 **Post**, **PUT** 또는 **PATCH** 메서드를 사용하여 데이터를 복사합니다.
- 다음 인증 중 하나를 사용하여 데이터를 복사합니다. **익명**, **기본**, **AAD 서비스 주체**, **Azure 리소스에 대한 관리 ID**.
- REST API의 **[페이지 매김](#pagination-support)**
- 원본인 REST에 REST JSON 응답을 [있는 그대로](#export-json-response-as-is) 복사하거나 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 사용하여 구문 분석합니다. **JSON** 의 응답 페이로드만 지원됩니다.

> [!TIP]
> Data Factory에서 REST 커넥터를 구성하기 전에 데이터 검색을 위한 요청을 테스트하려면 헤더 및 본문 요구 사항의 API 사양을 알아봅니다. Postman 또는 웹 브라우저와 같은 도구를 사용하여 유효성을 검사할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-rest-linked-service-using-ui"></a>UI를 사용하여 REST 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 REST 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷":::

2. REST를 검색하여 REST 커넥터를 선택합니다.

    :::image type="content" source="media/connector-rest/rest-connector.png" alt-text="REST 커넥터 선택.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-rest/configure-rest-linked-service.png" alt-text="REST 연결된 서비스 구성.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 REST 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

REST 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **RestService** 로 설정되어야 합니다. | 예 |
| url | REST 서비스의 기본 URL입니다. | 예 |
| enableServerCertificateValidation | 엔드포인트에 연결할 때 서버 쪽 TLS/SSL 인증서의 유효성을 검사할지를 나타냅니다. | 예<br /> (기본값: **true**) |
| authenticationType | REST 서비스에 연결하는 데 사용되는 인증 형식입니다. 허용되는 값은 **Anonymous**, **Basic**, **AadServicePrincipal** 및 **ManagedServiceIdentity** 입니다. 사용자 기반 OAuth는 지원되지 않습니다. `authHeader` 속성에서 인증 헤더를 추가로 구성할 수 있습니다. 추가 속성 및 예제를 보려면 아래 해당 섹션을 참조하세요.| 예 |
| authHeaders | 인증을 위한 추가 HTTP 요청 헤더입니다.<br/> 예를 들어 API 키 인증을 사용하려면 인증 유형을 “Anonymous”로 선택하고 헤더에 API 키를 지정할 수 있습니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 요소](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 이 속성은 기본 Azure Integration Runtime을 사용합니다. |예 |

### <a name="use-basic-authentication"></a>기본 인증 사용

**authenticationType** 속성을 **Basic** 으로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| userName | REST 엔드포인트에 액세스하는 데 사용할 사용자 이름입니다. | 예 |
| password | 사용자(**userName** 값)의 암호입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>AAD 서비스 주체 인증 사용

**authenticationType** 속성을 **AadServicePrincipal** 로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | Azure Active Directory 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| 예 |
| azureCloudType | 서비스 주체 인증의 경우 AAD 애플리케이션이 등록된 Azure 클라우드 환경의 형식을 지정합니다. <br/> 허용되는 값은 **AzurePublic**, **AzureChina**, **AzureUsGovernment**, **AzureGermany** 입니다. 기본적으로 데이터 팩터리의 클라우드 환경이 사용됩니다. | 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> 시스템 할당 관리 ID 인증 사용

**authenticationType** 속성을 **ManagedServiceIdentity** 로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| 예 |

**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-user-assigned-managed-identity-authentication"></a>사용자 할당 관리 ID 인증 사용
**authenticationType** 속성을 **ManagedServiceIdentity** 로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스(예: `https://management.core.windows.net`)를 지정합니다.| 예 |
| 자격 증명 | 사용자 할당 관리 ID를 자격 증명 개체로 지정합니다. | 예 |


**예제**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }    
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-authentication-headers"></a>인증 헤더 사용

또한, 기본 제공 인증 형식과 함께 인증을 위한 요청 헤더를 구성할 수 있습니다.

**예제: API 키 인증 사용**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
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

이 섹션에서는 REST 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

REST의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **RestResource** 로 설정해야 합니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 이 속성을 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. HTTP 커넥터가 결합된 URL(`[URL specified in linked service]/[relative URL specified in dataset]`)에서 데이터를 복사합니다. | 예 |

데이터 세트에서 `requestMethod`, `additionalHeaders`, `requestBody` 및 `paginationRules`를 설정한 경우 여전히 있는 그대로 지원되지만, 앞으로는 작업에 새 모델을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 REST 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="rest-as-source"></a>REST를 원본으로

복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **RestSource** 로 설정해야 합니다. | 예 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **GET**(기본값) 또는 **POST** 입니다. | 예 |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| requestBody | HTTP 요청의 본문입니다. | 예 |
| paginationRules | 다음 페이지 요청을 작성하기 위한 페이지 매김 규칙입니다. 자세한 내용은 [페이지 매김 지원](#pagination-support)을 참조하세요. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40** 입니다.  | 예 |
| requestInterval | 다음 페이지에 대한 요청을 보내기 전에 대기할 시간입니다. 기본값은 **00:00:01** 입니다. |  예 |

>[!NOTE]
>REST 커넥터는 `additionalHeaders`에 지정된 "Accept" 헤더를 무시합니다. REST 커넥터는 JSON의 응답만 지원하므로 `Accept: application/json`의 헤더를 자동으로 생성합니다.

**예제 1: Get 메서드에서 페이지 매김 사용**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예제 2: POST 메서드 사용**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>싱크인 REST

복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **RestSink** 로 설정해야 합니다. | 예 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **POST**(기본값), **PUT**, **PATCH** 입니다. | 예 |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 쓰는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40** 입니다.  | 예 |
| requestInterval | 여러 요청 간의 간격 시간(밀리초)입니다. 요청 간격 값은 [10, 60000] 사이의 숫자여야 합니다. |  예 |
| httpCompressionType | 최적의 압축 수준으로 데이터를 전송하는 동안 사용할 HTTP 압축 형식. 허용되는 값은 **none** 및 **gzip** 입니다. | 예 |
| writeBatchSize | 일괄 처리당 REST 싱크에 쓸 레코드 수입니다. 기본값은 10000입니다. | 예 |

싱크인 REST 커넥터는 JSON을 허용하는 REST API와 함께 작동합니다. 데이터는 다음 패턴을 사용하여 JSON으로 전송됩니다. 필요에 따라 복사 작업인 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 사용하여 REST API에서 필요한 페이로드를 따르도록 원본 데이터의 모양을 변경할 수 있습니다.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**예:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

REST는 통합 데이터 세트와 인라인 데이터 세트 모두에 대한 데이터 흐름에서 지원됩니다.

### <a name="source-transformation"></a>원본 변환

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **GET** 및 **POST** 입니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 이 속성을 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. HTTP 커넥터가 결합된 URL(`[URL specified in linked service]/[relative URL specified in dataset]`)에서 데이터를 복사합니다. | 예 |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 쓰는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40** 입니다.  | 예 |
| requestInterval | 여러 요청 간의 간격 시간(밀리초)입니다. 요청 간격 값은 [10, 60000] 사이의 숫자여야 합니다. |  예 |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter"는 다음 HTTP 요청 URL에 있는 하나의 쿼리 매개 변수 이름을 참조하는 사용자 정의 항목입니다. | No |

### <a name="sink-transformation"></a>싱크 변환

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 쓰는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40** 입니다.  | 예 |
| requestInterval | 여러 요청 간의 간격 시간(밀리초)입니다. 요청 간격 값은 [10, 60000] 사이의 숫자여야 합니다. |  예 |
| httpCompressionType | 최적의 압축 수준으로 데이터를 전송하는 동안 사용할 HTTP 압축 형식. 허용되는 값은 **none** 및 **gzip** 입니다. | 예 |
| writeBatchSize | 일괄 처리당 REST 싱크에 쓸 레코드 수입니다. 기본값은 10000입니다. | 예 |

CRUD 작업을 위해 REST 싱크로 보낼 상대 행 데이터와 함께 delete, insert, update 및 upsert 메서드를 설정할 수 있습니다.

![데이터 흐름 REST 싱크](media/data-flow/data-flow-sink.png)

## <a name="sample-data-flow-script"></a>샘플 데이터 흐름 스크립트

싱크 이전에 행 변경 변환을 사용하여 ADF에 REST 싱크로 수행할 작업 유형을 알려 줍니다. 즉. insert, update, upsert, delete.

```
AlterRow1 sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:true,
    rowRelativeUrl: 'periods',
    insertHttpMethod: 'PUT',
    deleteHttpMethod: 'DELETE',
    upsertHttpMethod: 'PUT',
    updateHttpMethod: 'PATCH',
    timeout: 30,
    requestFormat: ['type' -> 'json'],
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```

## <a name="pagination-support"></a>페이지 매김 지원

REST API에서 데이터를 복사할 때 일반적으로 REST API는 단일 요청의 응답 페이로드 크기를 적절한 수치로 제한합니다. 대량의 데이터를 반환하려는 경우 결과를 여러 페이지로 분할하고 호출자에게 연속 요청을 전송하여 결과의 다음 페이지를 가져오도록 요구합니다. 보통 한 페이지에 대한 요청은 동적이고 이전 페이지의 응답에서 반환된 정보로 구성됩니다.

이 일반 REST 커넥터는 다음 페이지 매김 패턴을 지원합니다. 

* 다음 요청의 절대 URL 또는 상대 URL = 현재 응답 본문의 속성 값
* 다음 요청의 절대 URL 또는 상대 URL = 현재 응답 헤더의 헤더 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 본문의 속성 값
* 다음 요청의 쿼리 매개 변수 = 현재 응답 헤더의 헤더 값
* 다음 요청의 헤더 = 현재 응답 본문의 속성 값
* 다음 요청의 헤더 = 현재 응답 헤더의 헤더 값

**페이지 매김 규칙** 은 하나 이상의 대/소문자 구분 키-값 쌍을 포함하는 데이터 세트의 사전으로 정의됩니다. 이 구성은 두 번째 페이지에서 시작하는 요청을 생성하는 데 사용됩니다. 커넥터는 HTTP 상태 코드 204(콘텐츠 없음)를 가져오거나 "paginationRules"의 JSONPath 식 중 하나가 null을 반환하면 반복을 중지합니다.

페이지 매김 규칙의 **지원되는 키** 는 다음과 같습니다.

| 키 | 설명 |
|:--- |:--- |
| AbsoluteUrl | 다음 요청을 실행할 URL을 나타냅니다. **절대 URL 또는 상대 URL** 일 수 있습니다. |
| QueryParameters.*request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter"는 다음 HTTP 요청 URL에 있는 하나의 쿼리 매개 변수 이름을 참조하는 사용자 정의 항목입니다. |
| Headers.*request_header* OR Headers['request_header'] | "request_header"는 다음 HTTP 요청에 있는 하나의 헤더 이름을 참조하는 사용자 정의 항목입니다. |

페이지 매김 규칙의 **지원되는 값** 은 다음과 같습니다.

| 값 | 설명 |
|:--- |:--- |
| Headers.*response_header* OR Headers['response_header'] | "response_header"는 현재 HTTP 요청에 있는 하나의 헤더 이름을 참조하는 사용자 정의 항목으로, 다음에 요청할 때 해당 값이 사용됩니다. |
| "$"(응답 본문의 루트를 나타냄)로 시작하는 JSONPath 식 | 응답 본문은 JSON 개체를 하나만 포함해야 합니다. JSONPath 식은 다음 요청을 실행하는 데 사용되는 단일 기본 값을 반환해야 합니다. |

**예:**

Facebook Graph API는 다음 구조의 응답을 반환합니다. 이 경우 다음 페이지의 URL은 ***paging.next*** 에 표시됩니다.

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

해당 REST 복사 작업 원본 구성에서 `paginationRules`는 다음과 같습니다.

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>OAuth 사용
이 섹션에서는 솔루션 템플릿을 사용하여 REST 커넥터에서 OAuth를 사용하는 JSON 형식의 Azure Data Lake Storage로 데이터를 복사하는 방법을 설명합니다. 

### <a name="about-the-solution-template"></a>솔루션 템플릿 정보

이 템플릿은 다음 두 가지 작업을 포함합니다.
- **Web** 작업은 전달자 토큰을 검색한 다음 권한 부여로 후속 복사 작업에 전달합니다.
- **복사** 작업은 REST에서 Azure Data Lake Storage로 데이터를 복사합니다.

이 템플릿은 다음 두 개의 매개 변수를 정의합니다.
- **SinkContainer** 는 Azure Data Lake Storage에서 데이터가 복사되는 루트 폴더 경로입니다. 
- **SinkDirectory** 는 Azure Data Lake Storage에서 데이터가 복사되는 루트 아래의 디렉터리 경로입니다. 

### <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **OAuth를 사용하여 REST 또는 HTTP에서 복사** 템플릿으로 이동합니다. 원본 연결에 대한 새 연결을 만듭니다. 
    ![새 연결 만들기](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    다음은 새 연결된 서비스(REST) 설정에 대한 주요 단계입니다.
    
     1. **기준 URL** 에서 원본 REST 서비스에 대한 URL 매개 변수를 지정합니다. 
     2. **인증 유형** 을 *익명* 으로 선택합니다.
        ![새 REST 연결](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. 대상 연결에 대한 새 연결을 만듭니다.  
    ![새 2세대 연결](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. **이 템플릿 사용** 을 선택합니다.
    ![이 템플릿 사용](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. 다음 예제와 같이 생성된 파이프라인이 표시됩니다. ![ 스크린샷은 템플릿에서 만든 파이프라인을 보여 줍니다.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. **웹** 작업을 선택합니다. **설정** 에서 데이터를 복사하려는 서비스의 로그인 API에서 OAuth 전달자 토큰을 검색할 해당 **URL**, **메서드**, **헤더**, **본문** 을 지정합니다. 템플릿의 자리 표시자는 AAD(Azure Active Directory) OAuth의 샘플을 보여 줍니다. AAD 인증은 기본적으로 REST 커넥터에서 지원됩니다. 여기에는 OAuth 흐름에 대한 예제만 있습니다. 

    | 속성 | 설명 |
    |:--- |:--- |
    | URL |OAuth 전달자 토큰을 검색할 URL을 지정합니다. 예를 들어 여기 샘플에서는 https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token 입니다. |
    | 방법 | HTTP 메서드입니다. 허용되는 값은 **GET** 및 **POST** 입니다. | 
    | 헤더 | 헤더는 HTTP 요청에 있는 하나의 헤더 이름을 참조하는 사용자 정의 항목입니다. | 
    | 본문 | HTTP 요청의 본문입니다. | 

    ![파이프라인](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. **데이터 복사** 작업에서 *원본* 탭을 선택하면, 이전 단계에서 검색된 전달자 토큰(access_token)이 추가 헤더의 **권한 부여** 로 데이터 복사 작업에 전달되는 것을 볼 수 있습니다. 파이프라인 실행을 시작하기 전에 다음 속성의 설정을 확인합니다.

    | 속성 | 설명 |
    |:--- |:--- |
    | 요청 메서드 | HTTP 메서드입니다. 허용되는 값은 **Get**(기본값) 또는 **Post** 입니다. | 
    | 추가 헤더 | 추가 HTTP 요청 헤더입니다.| 

   ![원본 인증 복사](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.
   ![파이프라인 실행](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. 파이프라인 실행이 성공적으로 완료되면 다음 예제와 유사한 결과가 표시됩니다. ![파이프라인 실행 결과](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. **작업** 열에서 WebActivity의 "출력" 아이콘을 클릭하면 서비스에서 반환되는 access_token이 표시됩니다.

   ![토큰 출력](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. **작업** 열에서 CopyActivity의 "입력" 아이콘을 클릭하면 WebActivity로 검색된 access_token이 인증을 위해 CopyActivity에 전달됩니다. 

    ![토큰 입력](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >토큰을 일반 텍스트로 로그하지 않으려면 Web 작업에서 "보안 출력"을, 복사 작업에서 "보안 입력"을 사용합니다.


## <a name="export-json-response-as-is"></a>JSON 응답을 있는 그대로 내보내기

이 REST 커넥터를 사용하여 REST API JSON 응답을 다양한 파일 기반 저장소에 있는 그대로 내보낼 수 있습니다. 이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "구조"(*스키마* 라고도 함) 섹션을 건너뛰세요.

## <a name="schema-mapping"></a>스키마 매핑

데이터를 REST 엔드포인트에서 테이블 형식 싱크로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
