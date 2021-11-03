---
title: SharePoint Online의 데이터 인덱싱(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 문서 라이브러리 콘텐츠의 인덱싱을 자동화하도록 SharePoint Online 인덱서를 설정합니다.
author: nitinme
ms.author: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 1494d0e5b6a3879d926b7d7a96979ca148806ec1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076019"
---
# <a name="index-data-from-sharepoint-online"></a>SharePoint Online의 데이터 인덱싱

> [!IMPORTANT] 
> SharePoint Online 지원은 현재 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. 이 기능에 대한 [액세스를 요청](https://aka.ms/azure-cognitive-search/indexer-preview)하고 액세스가 활성화된 후 [미리 보기 REST API(2020-06-30-preview 이상)](search-api-preview.md)를 사용하여 콘텐츠를 인덱싱합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

이 문서에서는 Azure Cognitive Search를 사용하여 SharePoint Online 문서 라이브러리에 저장된 문서(예: PDF, Microsoft Office 문서 및 기타 일반적인 형식)를 Azure Cognitive Search 인덱스로 인덱싱하는 방법을 설명합니다. 먼저, 인덱서 설정 및 구성의 기본 사항을 설명합니다. 그런 다음, 동작 및 발생할 수 있는 시나리오의 심층적 탐색을 제공합니다.

> [!NOTE]
> SharePoint Online은 문서 수준에서 사용자별 액세스를 결정하는 세분화된 권한 부여 모델을 지원합니다. SharePoint Online 인덱서는 이러한 권한을 검색 인덱스에 가져오지 않으며 Cognitive Search는 문서 수준 권한 부여를 지원하지 않습니다. 문서가 SharePoint Online에서 검색 서비스로 인덱싱되는 경우 인덱스에 대한 읽기 액세스 권한이 있는 사용자는 누구든지 해당 콘텐츠를 사용할 수 있습니다. 문서 수준 사용 권한이 필요한 경우에는 보안 필터를 조사하여 권한 없는 콘텐츠의 결과를 트리밍합니다. 자세한 내용은 [Active Directory ID를 사용한 보안 트리밍](search-security-trimming-for-azure-search-with-aad.md)을 참조하세요.

## <a name="functionality"></a>기능

Azure Cognitive Search의 인덱서는 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하는 크롤러입니다. SharePoint Online 인덱서는 SharePoint Online 사이트에 연결한 후 하나 이상의 문서 라이브러리에 있는 문서를 인덱싱합니다. 인덱서는 다음과 같은 기능을 제공합니다.
+ 하나 이상의 SharePoint Online 문서 라이브러리에 있는 콘텐츠를 인덱싱합니다.
+ 인덱서는 증분 인덱싱을 지원합니다. 증분 인덱싱에서는 문서 라이브러리에서 변경된 콘텐츠를 식별하고 후속 인덱싱 실행 시 업데이트 된 내용만 인덱싱됩니다. 예를 들어, 처음에 5개의 PDF가 인덱서에서 인덱싱되는 경우 1개가 업데이트되면 인덱서가 다시 실행된 후 업데이트된 1개의 PDF만 인덱싱합니다.
+ 텍스트 및 정규화된 이미지는 기본적으로 인덱싱되는 문서에서 추출됩니다. 필요에 따라 추가 콘텐츠 보강을 위해 파이프라인에 기술 세트를 추가할 수 있습니다. 기술 세트에 대한 자세한 내용은 [Azure Cognitive Search의 기술 세트 개념](cognitive-search-working-with-skillsets.md) 문서를 참조하세요.

## <a name="supported-document-formats"></a>지원되는 문서 형식

Azure Cognitive Search SharePoint Online 인덱서는 다음 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지
기본적으로 SharePoint Online 인덱서는 증분 인덱싱을 지원합니다. 증분 인덱싱에서는 문서 라이브러리에서 변경된 콘텐츠를 식별하고 후속 인덱싱 실행 시 업데이트 된 내용만 인덱싱됩니다. 예를 들어, 처음에 5개의 Word 문서가 인덱서에서 인덱싱되는 경우 1개가 업데이트되면 인덱서가 다시 실행된 후 업데이트된 1개의 Word 문서만 다시 인덱싱합니다.

삭제 검색도 기본적으로 지원됩니다. 즉, SharePoint Online 문서 라이브러리에서 문서를 삭제하는 경우 인덱서는 후속 인덱서 실행 동안 삭제를 검색하고 인덱스에서 해당 문서를 제거합니다.

## <a name="setting-up-sharepoint-online-indexing"></a>SharePoint Online 인덱싱 설정
SharePoint Online 인덱서를 설정하려면 Azure Portal 및 미리 보기 REST API에서 각각 수행해야 할 작업이 있습니다. 이 미리 보기는 SDK에서 지원되지 않습니다.

 다음 비디오에서는 SharePoint Online 인덱서를 설정하는 방법을 보여 줍니다.
 
> [!VIDEO https://www.youtube.com/embed/QmG65Vgl0JI]

### <a name="step-1-optional-enable-system-assigned-managed-identity"></a>1단계(선택 사항): 시스템 할당 관리 ID 사용

시스템 할당 관리 ID를 사용하도록 설정하면 Azure는 인덱서에서 사용할 수 있는 검색 서비스의 ID를 만듭니다. 이 ID는 검색 서비스가 프로비전되는 테넌트 자동 검색에 사용됩니다.

SharePoint Online 사이트가 검색 서비스와 동일한 테넌트에 있는 경우 검색 서비스에 대해 시스템 할당 관리 ID를 사용하도록 설정해야 합니다. SharePoint Online 사이트가 검색 서비스와 다른 테넌트인 경우 시스템 할당 관리 ID를 사용하도록 설정할 필요가 없습니다.

![시스템이 할당한 관리 ID 사용](media/search-howto-index-sharepoint-online/enable-managed-identity.png "시스템이 할당한 관리 ID 사용")

**저장** 을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![시스템 할당 관리 ID](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "시스템 할당 관리 ID")

### <a name="step-2-create-an-aad-application"></a>2단계: AAD 애플리케이션 만들기
SharePoint Online 인덱서는 인증을 위해 이 AAD 애플리케이션을 사용합니다. 

1.  [Azure Portal](https://portal.azure.com/)로 이동합니다.

1.  기본 페이지의 왼쪽에 있는 메뉴를 열고 **Azure Active Directory** 를 선택한 다음, **앱 등록** 을 선택합니다. **+ 새 등록** 을 선택합니다.
    1.  앱의 이름을 입력합니다.
    2.  **단일 테넌트** 를 선택합니다.
    3.  리디렉션 URI는 필요하지 않습니다.
    4.  **등록** 을 선택합니다.

1.  왼쪽의 메뉴에서 **API 사용 권한** 을 선택하고 **사용 권한 추가**, **Microsoft Graph** 및 **위임된 권한** 을 차례로 선택합니다. 다음 API 사용 권한을 추가합니다. 
    1.  **위임됨 - Files.Read.All** 
    2.  **위임됨 - Sites.Read.All** 
    3.  **위임됨 - User.Read**

    ![위임된 API 사용 권한](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "위임된 API 사용 권한")

    위임된 권한을 사용하면 인덱서가 사용자 컨텍스트에서 SharePoint 사이트에 액세스하게 됩니다. 따라서 실행되는 인덱서는 로그인한 사용자가 액세스할 수 있는 콘텐츠에만 액세스할 수 있습니다. 사용자 로그인은 인덱서를 만들거나 날짜 원본을 업데이트할 때 발생합니다. 로그인 단계는 이 문서의 뒷부분에 설명되어 있습니다.

1.  **인증** 탭을 선택합니다. **퍼블릭 클라이언트 흐름 허용** 을 **예** 로 설정하고 **저장** 을 선택합니다.

1.  **+ 플랫폼 추가**, **모바일 및 데스크톱 애플리케이션** 을 차례로 선택하고 `https://login.microsoftonline.com/common/oauth2/nativeclient` 및 **구성** 을 차례로 선택합니다.

    ![AAD 앱 인증 구성](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD 앱 인증 구성")

1.  관리자 동의를 제공합니다(특정 테넌트에만 필요).

    일부 테넌트는 이러한 위임된 API 권한에 대해 관리자 동의가 필요하도록 잠깁니다. 이 경우 인덱서를 만들기 전에 관리자에게 이 AAD 애플리케이션에 대한 동의를 허용해야 합니다.

    모든 테넌트가 이러한 사항을 요구하는 것은 아니므로 먼저 이 단계를 건너뛰고 지침을 계속 진행하는 것이 좋습니다. 인덱서를 만들 때 관리자 동의가 필요한지 여부를 알 수 있습니다. 인증이 실패하면서 관리자가 인증을 승인해야 한다는 메시지가 표시됩니다. 이 경우 테넌트 관리자는 아래 단추를 사용하여 동의를 허용합니다.

    ![AAD 앱 관리자 동의 허용](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD 앱 관리자 동의 허용")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>3단계: 데이터 원본 만들기
> [!IMPORTANT] 
> 이 섹션부터는 나머지 단계 동안 미리 보기 REST API를 사용해야 합니다. Azure Cognitive Search REST API에 익숙하지 않은 경우 이 [빠른 시작](search-get-started-rest.md)을 살펴보는 것이 좋습니다.

데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 데이터 원본을 동일한 검색 서비스의 여러 인덱서에서 사용할 수 있습니다.

데이터 원본에는 SharePoint 인덱싱을 위한 다음과 같은 필수 속성이 있어야 합니다.
+ **name** 은 검색 서비스 내 데이터 원본의 고유 이름입니다.
+ **type** 은 "sharepoint"여야 합니다. 대/소문자를 구분합니다.
+ **credentials** 는 SharePoint ONLINE 엔드포인트와 AAD 애플리케이션(클라이언트) ID를 제공합니다. SharePoint Online 엔드포인트의 예는 `https://microsoft.sharepoint.com/teams/MySharePointSite`입니다. SharePoint 사이트의 홈페이지로 이동하고 브라우저에서 URL을 복사하여 SharePoint Online 엔드포인트를 가져올 수 있습니다.
+ **container** 는 인덱싱할 문서 라이브러리를 지정합니다. 컨테이너를 만드는 방법에 대한 자세한 내용은 이 문서의 [인덱싱되는 문서 제어](#controlling-which-documents-are-indexed) 섹션에서 찾을 수 있습니다.

데이터 원본을 만드는 방법:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID];TenantId=[SharePoint Online site tenant id]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

> [!NOTE]
> SharePoint Online 사이트가 검색 서비스와 동일한 테넌트에 있고 시스템 할당 관리 ID가 사용하도록 설정된 경우 `TenantId` 연결 문자열에 를 포함할 필요가 없습니다. SharePoint Online 사이트가 검색 서비스와 다른 테넌트인 경우 를 `TenantId` 포함해야 합니다.

### <a name="step-4-create-an-index"></a>4단계: 인덱스 만들기
인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

문서 라이브러리의 문서에서 추출된 텍스트를 저장하기 위해 검색 가능한 콘텐츠 필드를 사용하여 인덱스를 만드는 방법은 다음과 같습니다.

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="step-5-create-an-indexer"></a>5단계: 인덱서 만들기
인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다. 인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

이 섹션을 진행하는 동안 SharePoint 사이트에 대한 액세스 권한이 있는 조직 자격 증명을 사용하여 로그인해야 합니다. 가능하면 새 조직 사용자 계정을 만들고 해당 새 사용자에게 인덱서에 필요한 정확한 사용 권한을 부여하는 것이 좋습니다.

인덱스를 만들려면 다음과 같은 몇 가지 단계를 수행해야 합니다.

1.  인덱서 만들기를 시도하기 위한 요청을 보냅니다.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  처음으로 인덱서를 만들 때 실패하며 다음과 같은 오류가 표시됩니다. 오류 메시지의 링크로 이동합니다. 10분 내에 링크로 이동하지 않으면 코드가 만료되므로 [데이터 원본](#create-data-source)을 다시 만들어야 합니다.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  오류 메시지에 포함된 코드를 제공합니다.

    ![디바이스 코드 입력](media/search-howto-index-sharepoint-online/enter-device-code.png "디바이스 코드 입력")

1.  SharePoint 인덱서는 로그인한 사용자 권한으로 SharePoint 콘텐츠에 액세스합니다. 이 단계 중에 로그인하는 사용자는 로그인한 사용자가 됩니다. 따라서 인덱싱할 문서 라이브러리의 문서에 대한 액세스 권한이 없는 사용자 계정으로 로그인하는 경우 인덱서에도 해당 문서에 대한 액세스 권한이 없습니다.

    가능하면 새 사용자 계정을 만들고 해당 사용자에게 인덱서에 필요한 정확한 사용 권한을 부여하는 것이 좋습니다.

1.  요청된 사용 권한을 승인합니다.

    ![API 사용 권한 승인](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API 사용 권한 승인")

1.  인덱서 만들기 요청을 다시 보냅니다. 이번에는 요청이 성공합니다. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>6단계: 인덱서 상태 확인
인덱서를 만든 후에는 다음 요청을 수행하여 인덱서 상태를 확인할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

인덱서 상태에 대한 자세한 내용은 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)를 참조하세요.

## <a name="updating-the-data-source"></a>데이터 원본 업데이트
데이터 원본 개체에 대한 업데이트가 없는 경우 인덱서는 사용자 상호 작용 없이 일정대로 실행될 수 있습니다. 그러나 Azure Cognitive Search 데이터 원본 개체가 업데이트될 때마다 인덱서를 실행하기 위해 다시 로그인해야 합니다. 예를 들어, 데이터 원본 쿼리를 변경하는 경우 `https://microsoft.com/devicelogin` 및 새 코드를 사용하여 다시 로그인해야 합니다.

데이터 원본을 업데이트한 후에는 다음 단계를 수행합니다.

1.  인덱서 실행을 수동으로 시작합니다.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    인덱서 실행 요청에 대한 자세한 내용은 [인덱서 실행](/rest/api/searchservice/run-indexer)을 참조하세요.

1.  인덱서 상태를 확인합니다. 마지막 인덱서 실행에서 오류가 발생하고 `https://microsoft.com/devicelogin`으로 이동하라는 메시지가 표시되면 해당 페이지로 이동하여 새 코드를 제공합니다. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    인덱서 상태에 대한 자세한 내용은 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)를 참조하세요.

1.  로그인

1.  인덱서 실행을 수동으로 다시 시작하고 인덱서 상태를 확인합니다. 이번에는 인덱서 실행이 성공적으로 시작되어야 합니다.

## <a name="indexing-document-metadata"></a>문서 메타데이터 인덱싱
문서 메타데이터를 인덱싱하도록 인덱서를 설정한 경우 다음 메타데이터를 인덱싱할 수 있습니다.

| ID | 유형 | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | 사이트의 문서 라이브러리에서 항목을 고유하게 식별하는 사이트 ID, 라이브러리 ID 및 항목 ID의 조합 키입니다. |
| metadata_spo_site_id | Edm.String | SharePoint Online 사이트의 ID입니다. |
| metadata_spo_library_id | Edm.String | 문서 라이브러리의 ID입니다. |
| metadata_spo_item_id | Edm.String | 문서에 있는 (문서) 항목의 ID입니다. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | 항목의 마지막으로 수정한 날짜/시간(UTC)입니다. |
| metadata_spo_item_name | Edm.String | 항목의 이름입니다. |
| metadata_spo_item_size | Edm.Int64 | 항목의 크기(바이트)입니다. | 
| metadata_spo_item_content_type | Edm.String | 항목의 콘텐츠 유형입니다. | 
| metadata_spo_item_extension | Edm.String | 항목의 확장입니다. |
| metadata_spo_item_weburi | Edm.String | 항목의 URI입니다. |
| metadata_spo_item_path | Edm.String | 부모 경로와 항목 이름의 조합입니다. | 

SharePoint Online 인덱서는 각 문서 유형과 관련된 메타데이터도 지원합니다. 자세한 내용은 [Azure Cognitive Search에서 사용되는 콘텐츠 메타데이터 속성](search-blob-metadata-properties.md)을 참조하세요.

> [!NOTE]
> 사용자 지정 메타데이터를 인덱싱하려면 [ `additionalColumns` 쿼리 정의에 를 지정해야 합니다.](#query)

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>인덱싱되는 문서 제어
단일 SharePoint Online 인덱서는 하나 이상의 문서 라이브러리에 있는 콘텐츠를 인덱싱할 수 있습니다. 인덱싱할 문서 라이브러리를 나타내기 위해 데이터 원본을 만들 때 *container* 매개 변수를 사용합니다. 데이터 원본 *컨테이너* 에는 *name* 및 *query* 라는 두 개의 속성이 있습니다. 

### <a name="name"></a>이름
*name* 속성은 필수이며 다음 세 값 중 하나여야 합니다.
+ *defaultSiteLibrary*
    + 사이트 기본 문서 라이브러리의 모든 콘텐츠를 인덱싱합니다.
+   *allSiteLibraries*
    + 사이트의 모든 문서 라이브러리에서 모든 콘텐츠를 인덱싱합니다. 하위 사이트의 문서 라이브러리는 인덱싱되지 않습니다. 그렇지만 이러한 사항은 *query* 에서 지정할 수 있습니다.
+   *useQuery*
    + *query* 에는 인덱스 콘텐츠만 정의됩니다.

<a name="query"></a>

### <a name="query"></a>쿼리
*query* 속성은 키워드/값 쌍으로 구성됩니다. 다음은 사용할 수 있는 키워드입니다. 값은 사이트 URL 또는 문서 라이브러리 URL 중 하나입니다.

> [!NOTE]
> 특정 키워드에 대한 값을 가져오려면 브라우저에서 SharePoint Online을 열고, 포함/제외하려는 문서 라이브러리로 이동한 후 브라우저에서 해당 URI를 복사하는 것이 좋습니다. 이 방법이 쿼리에서 키워드에 사용할 값을 가져오는 가장 쉬운 방법입니다.

| 키워드 | 쿼리 설명 | 예제 |
| ------------- | -------------- | ----------- |
| null | null이거나 비어 있는 경우 컨테이너 이름에 따라 기본 문서 라이브러리 또는 모든 문서 라이브러리를 인덱싱합니다. | 기본 사이트 라이브러리의 모든 콘텐츠를 인덱싱합니다. <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | 연결 문자열에서 정의된 사이트에 있는 모든 라이브러리의 콘텐츠를 인덱싱합니다. 사이트의 하위 사이트로 제한됩니다. <br><br> 이 키워드의 *query* 값은 사이트 또는 하위 사이트의 URI여야 합니다. | mysite의 모든 문서 라이브러리에 있는 모든 콘텐츠를 인덱싱합니다. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | 이 라이브러리의 콘텐츠를 인덱싱합니다. <br><br> 이 키워드의 *query* 값은 다음 형식 중 하나여야 합니다. <br><br> 예제 1: <br><br> *includeLibrary=[사이트 또는 하위 사이트]/[문서 라이브러리]* <br><br> 예제 2: <br><br> 브라우저에서 복사되는 URI입니다. | MyDocumentLibrary의 모든 콘텐츠를 인덱싱합니다. <br><br> 예제 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 예제 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  이 라이브러리의 콘텐츠를 인덱싱하지 않습니다. <br><br> 이 키워드의 *query* 값은 다음 형식 중 하나여야 합니다. <br><br> 예제 1: <br><br> *excludeLibrary=[사이트 또는 하위 사이트 URI]/[문서 라이브러리]* <br><br> 예제 2: <br><br> 브라우저에서 복사되는 URI입니다. | MyDocumentLibrary를 제외한 모든 라이브러리의 모든 콘텐츠를 인덱싱합니다. <br><br> 예제 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 예제 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| additionalColumns | 이 라이브러리의 열을 인덱싱합니다. <br><br> 이 키워드의 쿼리 값에는 인덱스를 만들려는 열 이름의 쉼표로 구분 된 목록이 포함 되어야 합니다. 이중 백슬래시를 사용 하 여 열 이름에 세미콜론과 쉼표를 이스케이프 합니다. <br><br> 예제 1: <br><br> additionalColumns = MyCustomColumn, MyCustomColumn2 <br><br> 예제 2: <br><br> additionalColumns = MyCustomColumnWith \\ ,, MyCustomColumn2With \\ ; | MyDocumentLibrary의 모든 콘텐츠를 인덱싱합니다. <br><br> 예제 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary;additionalColumns=MyCustomColumn,MyCustomColumn2" } ``` <br><br> 참고 문자를 이스케이프 하는 경우 이중 백슬래시 (JSON은 백슬래시를 사용 해야 함)는 다른 백슬래시로 이스케이프 됩니다. <br><br> 예제 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx;additionalColumns=MyCustomColumnWith\\,,MyCustomColumnWith\\;" } ``` |

## <a name="index-by-file-type"></a>파일 형식별 인덱싱
인덱싱할 문서와 건너뛸 문서를 제어할 수 있습니다.

### <a name="include-documents-having-specific-file-extensions"></a>특정 파일 확장명의 문서 포함
`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용하여 지정한 파일 이름 확장명을 가진 문서만 인덱싱할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PDF 및 .DOCX 문서만 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>특정 파일 확장명의 문서 제외
`excludedFileNameExtensions` 구성 매개 변수를 사용하여 인덱싱에서 특정 파일 이름 확장명으로 문서를 제외할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 문서를 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Cognitive Search는 먼저 `indexedFileNameExtensions`를 확인한 후 `excludedFileNameExtensions`를 찾습니다. 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

## <a name="handling-errors"></a>오류 처리
기본적으로 SharePoint Online 인덱서는 지원되지 않는 콘텐츠 형식(예: 이미지)을 포함하는 문서를 발견하는 즉시 중지됩니다. 물론 `excludedFileNameExtensions` 매개 변수를 사용하여 특정 콘텐츠 형식을 건너뛸 수 있습니다. 하지만, 있을 수 있는 모든 콘텐츠 형식을 미리 알지 못하는 상태에서 문서를 인덱싱해야 하는 경우도 있습니다. 지원되지 않는 콘텐츠 형식이 발견될 때 인덱싱을 계속하려면 `failOnUnsupportedContentType` 구성 매개 변수를 false로 설정합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

일부 문서의 경우 Azure Cognitive Search는 콘텐츠 형식을 확인할 수 없거나 지원되는 다른 콘텐트 형식의 문서를 처리할 수 없습니다. 이 오류 모드를 무시하려면 `failOnUnprocessableDocument` 구성 매개 변수를 False로 설정합니다.

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search는 인덱싱되는 문서의 크기를 제한합니다. 이러한 제한 사항은 [의 서비스 제한 사항](./search-limits-quotas-capacity.md)에서 설명됩니다. 너무 큰 문서는 기본적으로 오류로 처리됩니다. 그러나 `indexStorageMetadataOnlyForOversizedDocuments` 구성 매개 변수를 true로 설정한 경우 너무 큰문서의 스토리지 메타데이터를 여전히 인덱싱할 수 있습니다.

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

또한 문서를 구문 분석하거나 문서를 인덱스를 추가할 때 임의 처리 지점에서 오류가 발생하는 경우에도 인덱싱을 계속할 수 있습니다. 설정 개수의 오류를 무시하려면 `maxFailedItems` 및 `maxFailedItemsPerBatch` 구성 매개 변수를 원하는 값으로 설정합니다. 예를 들면 다음과 같습니다.

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>참고 항목
+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure Cognitive Search에서 사용하는 콘텐츠 메타데이터 속성](search-blob-metadata-properties.md)
