---
title: REST를 사용하여 지식 저장소 만들기
titleSuffix: Azure Cognitive Search
description: REST API 및 Postman을 사용하여, 기술 세트로부터의 AI 보강을 지속하는 Azure Cognitive Search 지식 저장소를 만듭니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/02/2021
ms.openlocfilehash: acad54727ecb441cce64021ecba395d41820659a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535271"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>REST 및 Postman을 사용하여 지식 저장소 만들기

지식 저장소는 후속 지식 마이닝, 데이터 분석 또는 다운스트림 처리를 위해 [AI 보강 파이프라인](cognitive-search-concept-intro.md)의 기술 세트 출력을 Azure Storage에 보내는 Azure Cognitive Search의 한 기능입니다. 지식 저장소가 채워지면 [Storage Explorer](knowledge-store-view-storage-explorer.md) 또는 [Power BI](knowledge-store-connect-power-bi.md)와 같은 도구를 사용하여 콘텐츠를 검색할 수 있습니다.

이 문서에서는 REST API를 사용하여 Azure Storage의 지식 저장소에 있는 호텔 숙박에 대한 일단의 고객 리뷰를 수집, 보강 및 검색합니다. 최종 결과는 원본에서 끌어온 원본 텍스트 콘텐츠와 영어가 아닌 고객 의견의 감정 점수, 핵심 구 추출, 언어 감지 및 텍스트 번역이 포함된 AI 생성 콘텐츠가 포함된 지식 저장소입니다.

초기 데이터 세트를 사용할 수 있도록 호텔 리뷰를 먼저 Azure Blob Storage에 가져옵니다. 처리 후에는 결과가 Azure Table Storage에 지식 저장소로 저장됩니다.

> [!NOTE]
> 이 문서에서는 이 문서를 위한 [Postman 데스크톱 앱](https://www.getpostman.com/)을 가정하고 있습니다. 이 문서의 [소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)에는 모든 요청이 포함된 Postman 컬렉션이 포함되어 있습니다. 

## <a name="create-services-and-load-data"></a>서비스 만들기 및 데이터 로드

이 연습에서는 Azure Cognitive Search, Azure Blob Storage 및 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 AI에 사용합니다. 

워크로드가 너무 작으므로 매일 최대 20개의 트랜잭션을 무료로 제공하기 위해 백그라운드에 탭으로 처리됩니다. 워크로드가 작기 때문에 Cognitive Services 리소스 만들기 또는 연결을 건너뛸 수 있습니다.

1. [HotelReviews_Free.csv를 다운로드합니다](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). 이 데이터는 CSV 파일로 저장된 호텔 리뷰 데이터이며(Kaggle.com에서 가져온 데이터) 단일 호텔에 대한 19개 고객 피드백을 포함하고 있습니다. 

1. [Azure Storage 계정을 만들](../storage/common/storage-account-create.md?tabs=azure-portal)거나 [기존 계정을 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Azure Storage는 가져올 원시 콘텐츠와 최종 결과인 지식 저장소 모두에 사용합니다.

   **StorageV2(범용 V2)** 계정 유형을 선택합니다.

1. Azure Storage 리소스에서 **Storage Explorer** 를 사용하여 **hotel-reviews** 라는 Blob 컨테이너를 만듭니다.

1. 페이지 위쪽에서 **업로드** 를 선택하여 이전 단계에서 다운로드한 **HotelReviews-Free.csv** 파일을 로드합니다.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="업로드된 파일 및 왼쪽 탐색 창이 있는 Storage Explorer의 스크린샷" border="true":::

1. 이 리소스를 거의 완료했지만 이러한 페이지를 나가기 전에 왼쪽 탐색 창에서 **액세스 키** 를 선택하여 인덱서를 통해 이 데이터를 검색할 수 있도록 연결 문자열을 가져옵니다.

1. **액세스 키** 에서 페이지 위쪽에 있는 **키 표시** 를 선택하여 연결 문자열의 숨김을 해제한 다음, key1 또는 key2에 대한 연결 문자열을 복사합니다.

   연결 문자열의 형식은 `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`입니다.

## <a name="configure-requests"></a>요청 구성

1. GitHub에서 [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples)를 다운로드하고, 파일의 압축을 풉니다. 리포지토리에는 여러 컬렉션이 있습니다. knowledge-store 폴더의 컬렉션을 사용합니다.

1. Postman에서 **파일** > **가져오기** 를 차례로 선택하여 KnowledgeStore.postman_collection.json 파일을 가져옵니다.

1. **컬렉션** 탭, **...** (줄임표) 단추를 차례로 선택합니다.

1. **편집** 을 선택합니다.

   ![탐색 결과를 표시하는 Postman 앱](media/knowledge-store-create-rest/postman-edit-menu.png "Postman의 [편집] 메뉴로 이동")

1. **편집** 대화 상자에서 **변수** 탭을 선택합니다. 

**변수** 탭에서 이중 중괄호 안에 특정 변수가 있을 때마다 Postman에서 교환하는 값을 추가할 수 있습니다. 예를 들어 Postman에서 `{{admin-key}}` 기호를 `admin-key`에 설정한 현재 값으로 바꿉니다. Postman은 URL, 헤더, 요청 본문 등에서 이 대체를 수행합니다. 

변수는 Azure 서비스, 서비스 연결 및 개체 이름에 대해 정의됩니다. 서비스 및 연결 자리 표시자 값을 검색 서비스 및 스토리지 계정의 실제 값으로 바꿉니다. 이러한 값은 Azure Portal에서 찾을 수 있습니다.

+ `search-service-name` 및 `search-service-admin-key`에 대한 값을 가져오려면 포털에서 Azure Cognitive Search Service로 이동하고 **개요** 및 **키** 페이지에서 값을 복사합니다.

+ `storage-account-name` 및 `storage-account-connection-string`에 대한 값을 가져오려면 **액세스 키** 페이지를 확인합니다.

![Postman 앱 변수 탭](media/knowledge-store-create-rest/postman-variables-window.png "Postman의 변수 창")

| 변수    | 가져올 수 있는 위치 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search 서비스의 **키** 페이지.  |
| `api-version` | **2020-06-30** 으로 둡니다. |
| `datasource-name` | **hotel-reviews-ds** 로 유지 | 
| `indexer-name` | **hotel-reviews-ixr** 로 유지 | 
| `index-name` | **hotel-reviews-ix** 로 유지 | 
| `search-service-name` | Azure Cognitive Search 서비스의 이름. URL이 `https://mySearchService.search.windows.net`인 경우 입력해야 하는 값은 `mySearchService`입니다. | 
| `skillset-name` | **hotel-reviews-ss** 로 유지 | 
| `storage-account-name` | Azure 스토리지 계정 이름. | 
| `storage-connection-string` | 스토리지 계정의 **액세스 키** 탭에서 페이지 위쪽에 있는 **키 표시** 를 선택한 다음, **key1** > **연결 문자열** 을 차례로 선택합니다. | 
| `storage-container-name` | **hotel-reviews** 로 유지 | 

### <a name="review-the-request-collection-in-postman"></a>Postman에서 요청 컬렉션 검토

지식 저장소는 기술 세트에 정의되며 결과적으로 인덱서에 연결됩니다. 지식 저장소를 만들려면 인덱스, 데이터 원본, 기술 세트 및 인덱서 등, 모든 업스트림 개체를 만들어야 합니다. 인덱스는 지식 저장소와 관련이 없지만 인덱서에서 실행해야 하므로, 인덱서 필수 구성 요소로 인덱스를 만듭니다.

지식 저장소를 만드는 경우 다음 네 가지 HTTP 요청을 발급합니다. 

+ **인덱스 만들기 PUT 요청**: 이 인덱스에는 검색 서비스에 발급된 쿼리 요청에서 반환된 검색 가능한 데이터가 포함됩니다.

+ **데이터 원본 만들기 POST 요청**: 이 데이터 원본은 인덱서에 대한 연결 정보를 제공합니다. 인덱서에서 Azure Storage 계정에 연결하여 샘플 데이터를 검색합니다.

+ **기술 세트 만들기 PUT 요청**: 기술 세트는 데이터에 적용되는 보강을 지정합니다. 또한 지식 저장소의 구조를 지정합니다.

+ **인덱서 만들기 PUT 요청**: 인덱서가 실행되면 데이터를 읽고, 기술 세트를 적용하며, 지식 저장소를 Azure Storage에 만들고, 결과를 저장합니다. 이 요청은 마지막으로 실행해야 합니다.

이전에 설정한 변수는 헤더와 URL에 사용됩니다. 인덱스 만들기 요청에 대한 다음 스크린샷에서는 요청에서 이러한 변수가 표시되는 위치를 보여 줍니다.

![헤더에 대한 Postman의 인터페이스를 보여 주는 스크린샷](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 컬렉션 집합 `api-key` 및 `Content-type` 헤더의 모든 요청. 필수입니다. Postman에서 변수를 인식하면 이전 스크린샷의 `{{admin-key}}`와 같이 변수가 주황색 텍스트로 표시됩니다. 변수의 철자가 틀리면 빨간색 텍스트로 표시됩니다.
>

## <a name="create-an-index"></a>인덱스 만들기

[인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 사용하여 검색 서비스에서 검색 인덱스를 만듭니다. 검색 인덱스는 지식 저장소와 관련이 없지만 사용자가 인덱서에서 만든 것이어야 합니다. 검색 인덱스에는 지식 저장소와 동일한 콘텐츠가 포함됩니다. 콘텐츠를 검색하는 다른 방법을 원하는 경우 쿼리 요청을 검색 서비스에 보내 이 인덱스를 쿼리할 수 있습니다. 

`https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`에 대한 PUT 요청을 발급하여 인덱스를 만듭니다. 인덱스 스키마는 요청 본문에 제공됩니다.

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

이 인덱스 정의는 사용자에게 제공하려는 데이터의 조합입니다. 여기에는 CSV에 직접 매핑된 필드(호텔 이름, 리뷰 날짜 등)와 기술 세트에서 만든 필드(감정, 핵심 구, 언어 및 번역된 텍스트)가 포함됩니다.

**보내기** 를 선택하여 PUT 요청을 발급합니다.

`201 - Created` 상태가 표시됩니다. 다른 상태가 표시되면 **본문** 창에서 오류 메시지가 포함된 JSON 응답을 찾습니다. 무료 검색 서비스를 사용하는 경우 새 개체를 위한 공간이 있는지 확인합니다(무료 서비스는 최대 3개임).

이 시점에서 인덱스가 만들어지지만 로드되지는 않습니다. 문서 가져오기는 나중에 인덱서를 실행할 때 발생합니다. 

## <a name="create-a-data-source"></a>데이터 소스 만들기

다음으로, Azure Cognitive Search를 Blob 스토리지에 저장된 호텔 데이터에 연결합니다. 데이터 원본을 만들려면 [데이터 원본 만들기](/rest/api/searchservice/create-data-source) POST 요청을 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`에 보냅니다. 

Postman에서 **데이터 원본 만들기** 요청, **본문** 창으로 차례로 이동합니다. 다음 코드가 표시됩니다.

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

**보내기** 를 선택하여 POST 요청을 발급합니다. 

## <a name="create-a-skillset"></a>기술 집합 만들기 

다음 단계에서는 적용되는 개선된 기능 및 결과가 저장되는 지식 저장소를 모두 지정하는 기술 세트를 지정합니다. 이 요청은 [기술 세트 만들기](/rest/api/searchservice/create-skillset) PUT 요청을 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`에 보냅니다.

`skills` 및 `knowledgeStore`라는 두 개의 큰 최상위 개체가 있습니다.

+ "skills"는 기술 세트입니다. `skills` 개체 내의 각 개체는 보강 서비스입니다. 각 보강 서비스에는 `inputs` 및 `outputs`가 있습니다. `LanguageDetectionSkill`에는 `Language`의 `targetName` 출력이 있습니다. 이 노드의 값은 대부분의 다른 기술에서 입력으로 사용합니다. 원본은 `document/Language`입니다. 한 노드의 출력을 다른 노드의 입력으로 사용하는 기능은 데이터가 지식 저장소의 테이블로 이동하는 방법을 지정하는 `ShaperSkill`에서 더 명확하게 드러납니다.

+ "knowledgeStore"에는 스토리지 계정에 대한 연결 문자열과 일련의 프로젝션이 포함됩니다. 프로젝션 배열의 각 프로젝션 항목은 테이블을 Azure Storage에 만듭니다. 테이블의 열에는 교차 연결 및 콘텐츠 필드에 사용되는 생성된 열이 포함됩니다. 여기에는 보강 중에 만든 콘텐츠 필드도 포함됩니다.

  프로젝션은 데이터 셰이프를 입력으로 허용합니다. 기술 출력을 프로젝션에 매핑하는 Shaper 기술을 통해 셰이프를 제공하거나 인라인 셰이프를 각 프로젝션 항목에 정의할 수 있습니다. 

기술 세트를 생성하려면 Postman에서 **보내기** 단추를 선택하여 요청에 대한 PUT을 수행합니다.

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>인덱서 만들기

마지막 단계는 [인덱서 만들기](/rest/api/searchservice/create-indexer) 요청입니다. 인덱서는 데이터를 읽고 기술 세트를 활성화합니다. 인덱서의 정의는 이미 만든 몇 가지 다른 리소스(데이터 원본, 인덱스 및 기술 세트)를 참조합니다. 

+ `parameters/configuration` 개체는 인덱서에서 데이터를 수집하는 방법을 제어합니다. 여기서 입력 데이터는 헤더 줄 및 쉼표로 구분된 값이 있는 단일 CSV 파일에 있습니다. 

+ 필드 매핑 만들기의 "AzureSearch_DocumentKey"는 Blob 인덱서에서 생성하는 각 문서에 대한 고유 식별자입니다(메타데이터 스토리지 경로 기반). 

+ 출력 필드 매핑은 보강된 필드가 검색 인덱스의 필드에 매핑되는 방법을 지정합니다. 출력 필드 매핑은 지식 저장소에서 사용되지 않습니다(지식 저장소는 셰이프와 프로젝션을 사용하여 물리적 데이터 구조를 표현함).

Postman에서 **보내기** 를 선택하여 인덱서를 만들고 실행합니다. 이 단계에서는 데이터 가져오기, 기술 세트 실행 및 지식 저장소 만들기가 수행됩니다.

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>상태 확인

각 요청을 보내면 검색 서비스에서 201 성공 메시지로 응답해야 합니다. 오류가 발생하면 변수를 다시 확인하고 검색 서비스에 새 인덱스, 인덱서, 데이터 원본 및 기술 세트를 위한 공간이 있는지 확인합니다(무료 계층은 각각 3개로 제한됨).

Azure Portal에서 Azure Cognitive Search 서비스의 **개요** 페이지로 이동합니다. **인덱서** 탭을 선택한 다음, **hotels-reviews-ixr** 을 선택합니다. 상태가 1~2분 내에 오류 및 경고 없이 "진행 중"에서 "성공"으로 진행되어야 합니다.

## <a name="check-tables-in-storage-explorer"></a>Storage Explorer에서 테이블 확인

Azure Portal에서 Azure Storage 계정으로 전환하고, **Storage Explorer** 를 사용하여 새 테이블을 봅니다. 기술 세트에 정의된 각 프로젝션에 대해 하나씩 6개의 테이블이 표시됩니다.

각 테이블은 쿼리에서 테이블을 교차 연결하는 데 필요한 ID를 사용하여 생성됩니다. 테이블을 열 때 이러한 필드를 지나면서 스크롤하여 파이프라인에서 추가한 콘텐츠 필드를 봅니다.

   :::image type="content" source="media/knowledge-store-create-rest/knowledge-store-tables.png" alt-text="Storage Explorer의 지식 저장소 테이블에 대한 스크린샷" border="true":::

이 연습에서 지식 저장소는 테이블을 셰이핑하고 구조화하는 다양한 방법을 보여주는 다양한 테이블로 구성됩니다. 첫 번째 세트의 세 테이블은 Shaper 기술의 출력을 사용하여 열과 행을 결정하는 테이블을 보여 줍니다. 두 번째 세트의 세 테이블은 프로젝션 자체에 포함된 인라인 셰이핑 명령을 보여 줍니다. 두 방법 중 하나를 사용하여 동일한 결과를 얻을 수 있습니다.

| 테이블 | Description |
|-------|-------------|
| hotelReviews1Document | CSV에서 전달되는 필드(예: review_date 및 review_text)를 포함합니다. |
| hotelReviews2Pages | 기술 세트에서 만든 보강 필드(예: 감정 점수 및 번역된 텍스트)를 포함합니다. |
| hotelReviews3KeyPhrases | 핵심 구의 긴 목록을 포함합니다. |
| hotelReviews4InlineProjectionDocument | 첫 번째 테이블의 대안이며, Shaper 기술 대신 인라인 셰이핑을 사용하여 프로젝션에 대한 데이터를 셰이핑합니다. |
| hotelReviews5InlineProjectionPages | 두 번째 테이블의 대안이며, 인라인 셰이핑을 사용합니다. |
| hotelreviews6InlineProjectionKeyPhrases | 세 번째 테이블의 대안이며, 인라인 셰이핑을 사용합니다. |

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기에서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다.

> [!TIP]
> 이 연습을 반복하거나 다른 AI 보강 연습을 시도하려면 **hotel-reviews-idxr** 인덱서 및 관련 개체를 삭제하여 다시 만드세요. 인덱서를 삭제하면 사용 가능한 일별 트랜잭션 카운터가 0으로 다시 설정됩니다.

## <a name="next-steps"></a>다음 단계

이제 Cognitive Services를 사용하여 데이터를 보강하고 결과를 지식 저장소에 프로젝션했으므로 Storage Explorer 또는 다른 앱을 사용하여 보강된 데이터 세트를 검색할 수 있습니다.

Storage Explorer를 사용하여 이 지식 저장소를 검색하는 방법을 알아보려면 다음 연습을 참조하세요.

> [!div class="nextstepaction"]
> [Storage Explorer를 사용하여 보기](knowledge-store-view-storage-explorer.md)