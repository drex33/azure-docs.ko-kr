---
title: SharePoint Online 목록에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인의 복사 작업을 사용하여 SharePoint Online 목록에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jianleishen
ms.openlocfilehash: 56ccc468b4576a8911091622d4e9b6810e58cd36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642907"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 SharePoint Online 목록에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업을 사용하여 SharePoint Online 목록에서 데이터를 복사하는 방법을 간략히 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업](copy-activity-overview.md)을 기준으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 SharePoint Online 목록 커넥터는 다음과 같은 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SharePoint Online 목록에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 SharePoint Online 목록 커넥터는 서비스 주체 인증을 사용하고 OData 프로토콜을 통해 데이터를 검색합니다.

> [!TIP]
> 이 커넥터는 파일이 아닌 SharePoint Online **목록** 에서의 데이터 복사를 지원합니다. [SharePoint Online에서 파일을 복사](#copy-file-from-sharepoint-online)하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

SharePoint Online 목록 커넥터는 서비스 주체 인증을 사용하여 SharePoint에 연결합니다. 다음 단계에 따라 설정합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 등록된 애플리케이션에 SharePoint Online 사이트 권한 부여: 

    > [!NOTE]
    > 이 작업을 수행하려면 SharePoint Online 사이트 소유자 권한이 있어야 합니다. 사이트 홈 페이지로 이동 -> 오른쪽 모서리에서 “X 멤버” 클릭 -> “소유자” 역할이 있는 멤버를 확인하여 소유자를 찾을 수 있습니다.

    1. `https://[your_site_url]/_layouts/15/appinv.aspx` 같은 SharePoint Online 사이트 링크(사이트 URL 대체)를 엽니다.
    2. 등록한 애플리케이션 ID를 검색하고 빈 필드를 입력한 다음, “만들기”를 클릭합니다.

        - 앱 도메인: `localhost.com`
        - 리디렉션 URL: `https://www.localhost.com`
        - 권한 요청 XML:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![SharePoint 권한 부여](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. 이 앱에 대해 "신뢰"를 클릭합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SharePoint Online 목록 커넥터에 한정된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SharePoint Online 목록에 연결된 서비스에 다음 속성이 지원됩니다.

| **속성**        | **설명**                                              | **필수** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | type 속성은  **SharePointOnlineList** 로 설정해야 합니다.  | 예          |
| siteUrl             | SharePoint Online 사이트 URL(예: `https://contoso.sharepoint.com/sites/siteName`) | 예          |
| servicePrincipalId  | Azure Active Directory에 등록된 애플리케이션의 애플리케이션(클라이언트) ID입니다. | 예          |
| servicePrincipalKey | 애플리케이션의 키입니다. 이 필드를 **SecureString** 으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예          |
| tenantId            | 애플리케이션이 있는 테넌트 ID입니다.          | 예          |
| connectVia          | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 이 문서 앞부분의 [필수 구성 요소](#prerequisites)에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. | 예           |

**예:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 이 섹션에서는 SAP 테이블 데이터 세트에서 지원되는 속성의 목록을 제공합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **SharePointOnlineLResource** 로 설정해야 합니다. | 예 |
| listName | SharePoint Online 목록의 이름입니다. | 예 |

**예제**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.  다음 섹션에서는 SharePoint Online 목록 원본에서 지원되는 속성의 목록을 제공합니다.

### <a name="sharepoint-online-list-as-source"></a>SharePoint Online 목록을 원본으로

SharePoint Online 목록에서 데이터를 복사하기 위해 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **SharePointOnlineListSource** 로 설정해야 합니다. | 예 |
| Query | 데이터 필터링을 위한 사용자 지정 OData 쿼리 옵션입니다. 예: `"$top=10&$select=Title,Number"`. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(초)입니다. 기본값은 300(5분)입니다. | 예 |

**예제**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> SharePoint Online 목록 원본에 대해 두 개 이상의 *선택* 데이터 형식을 선택할 수 없습니다.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>SharePoint Online 목록에 대한 데이터 형식 매핑

SharePoint Online 목록에서 데이터를 복사하는 경우 SharePoint Online 목록 데이터 형식과 서비스 내부에서 사용되는 중간 데이터 형식 사이에서 다음 매핑이 사용됩니다.

| **SharePoint Online 데이터 형식**                 | **OData 데이터 형식**                                  | **중간 데이터 형식** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| 한 줄의 텍스트                             | Edm.String                                           | String                                   |
| 여러 줄 텍스트                          | Edm.String                                           | String                                   |
| 선택(선택할 메뉴)                    | Edm.String                                           | String                                   |
| 숫자(1, 1.0, 100)                            | Edm.Double                                           | Double                                   |
| 통화($, ¥, &euro;)                              | Edm.Double                                           | Double                                   |
| 날짜 및 시간                                   | Edm.DateTime                                         | DateTime                                 |
| 조회(이 사이트에 이미 있는 정보)       | Edm.Int32                                            | Int32                                    |
| 예/아니요(확인란)                              | Edm.Boolean                                          | 부울                                  |
| 개인 또는 그룹                                 | Edm.Int32                                            | Int32                                    |
| 하이퍼링크 또는 그림                            | Edm.String                                           | String                                   |
| 계산됨(다른 열을 기반으로 계산) | Edm.String/Edm.Double/Edm.DateTime/Edm.Boolean | String/Double/DateTime/Boolean     |
| 첨부 파일                                      | 지원되지 않음                                        |                                          |
| 작업 결과                                    | 지원되지 않음                                        |                                          |
| 외부 데이터                                   | 지원되지 않음                                        |                                          |
| 관리되는 메타데이터                                | 지원되지 않음                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>SharePoint Online에서 파일 복사

**웹 작업** 을 사용하여 인증하고 SPO에서 액세스 토큰을 구한 다음, 이후의 **복사 작업** 에 전달하여 **HTTP 커넥터를 원본** 으로 데이터를 복사함으로써 SharePoint Online에서 파일을 복사할 수 있습니다.

![Sharepoint 파일 복사 흐름](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. [필수 구성 요소](#prerequisites) 섹션에 따라 AAD 애플리케이션을 만들고 SharePoint Online에 권한을 부여합니다. 

2. SharePoint Online에서 액세스 토큰을 가져오는 **웹 작업** 을 만듭니다.

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`. 테넌트 ID를 바꿉니다.
    - **메서드**: POST
    - **헤더**:
        - Content-Type: application/x-www-form-urlencoded
    - **본문**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`. 클라이언트 ID(애플리케이션 ID), 클라이언트 암호(애플리케이션 키), 테넌트 ID 및 테넌트 이름(SharePoint 테넌트)을 대체합니다.

    > [!CAUTION]
    > 웹 작업에서 보안 출력 옵션을 true로 설정하여 토큰 값이 일반 텍스트로 기록되지 않게 합니다. 이 값을 사용하는 추가 작업의 경우 보안 입력 옵션을 true로 설정해야 합니다.

3. HTTP 커넥터를 원본으로 사용하여 SharePoint Online 파일 콘텐츠를 복사하는 **복사 작업** 과의 체인

    - HTTP 연결 서비스:
        - **기준 URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`. 사이트 URL 및 상대 경로를 파일로 바꿉니다. 파일의 샘플 상대 경로는 `/sites/site2/Shared Documents/TestBook.xlsx`입니다.
        - **인증 유형:** 익명 *(복사 작업 원본에 구성된 전달자 토큰을 나중에 사용)*
    - 데이터 세트: 원하는 형식을 선택합니다. 파일을 있는 그대로 복사하려면 "이진" 형식을 선택합니다.
    - 복사 작업 원본:
        - **요청 메서드**: GET
        - **추가 헤더**: 업스트림 웹 작업에서 생성한 전달자 토큰을 인증 헤더로 사용하는 다음 식 `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`을 사용합니다. 웹 작업 이름을 바꿉니다.
    - 보통처럼 복사 작업 싱크를 구성합니다.

> [!NOTE]
> Azure AD 애플리케이션에 SharePoint Online에 대한 `FullControl` 권한이 있더라도 IRM을 사용하도록 설정한 문서 라이브러리에서 파일을 복사할 수 없습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
