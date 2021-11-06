---
title: Gremlin API에서 데이터 인덱싱(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Gremlin API 콘텐츠 인덱싱을 자동화하도록 Azure Cosmos DB 인덱서를 설정합니다.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/11/2021
ms.openlocfilehash: a0876ef39d74403ad6ae5d88fbac12aeed543e27
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893807"
---
# <a name="index-data-using-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API를 사용하여 데이터 인덱싱

> [!IMPORTANT]
> Cosmos DB Gremlin API 인덱서는 현재 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. 이 기능에 대한 [액세스를 요청](https://aka.ms/azure-cognitive-search/indexer-preview)하고 액세스가 활성화된 후 [미리 보기 REST API(2020-06-30-preview 이상)](search-api-preview.md)를 사용하여 콘텐츠를 인덱싱합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure Cosmos DB 인덱서를 구성하는 방법을 보여 줍니다. 이 워크플로에서는 Azure Cognitive Search에서 검색 인덱스를 만들고 Gremlin API를 사용하여 Azure Cosmos DB에서 추출한 기존 텍스트로 로드합니다.

용어가 혼동될 수 있기 때문에 [Azure Cosmos DB 인덱싱](../cosmos-db/index-overview.md) 및 [Azure Cognitive Search 인덱싱](search-what-is-an-index.md)은 각 서비스에 고유한 별개의 작업이라는 점에 유의해야 합니다. Azure Cognitive Search 인덱싱을 시작하기 전에 Azure Cosmos DB 데이터베이스가 이미 존재하고 데이터를 포함해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Cognitive Search에서 Gremlin API를 통해 Cosmos DB의 데이터를 인덱싱하도록 하려면 [Cosmos DB의 자체 인덱싱](../cosmos-db/index-overview.md)도 사용하도록 설정하고 [일관됨](../cosmos-db/index-policy.md#indexing-mode)으로 설정해야 합니다. 이것이 Cosmos DB의 기본 구성입니다. Cosmos DB 인덱싱이 이미 사용하도록 설정되어 있지 않으면 Azure Cognitive Search 인덱싱이 작동하지 않습니다.

## <a name="get-started"></a>시작하기

[미리 보기 REST API](/rest/api/searchservice/index-preview)를 사용하여 Gremlin API를 통해 제공되는 Azure Cosmos DB 데이터를 인덱싱할 수 있습니다. Azure Cognitive Search의 모든 인덱서에 공통적이며 데이터 원본 만들기, 인덱스 만들기, 인덱서 만들기라는 세 부분으로 구성된 워크플로를 수행하면 됩니다. 아래 프로세스에서는 인덱서 만들기 요청을 제출할 때 Cosmos DB에서 데이터 추출을 시작합니다.

기본적으로 Azure Cognitive Search Cosmos DB Gremlin API 인덱서는 그래프의 모든 꼭짓점을 인덱스의 문서로 만듭니다. 가장자리는 무시됩니다. 가장자리만 인덱싱하도록 쿼리를 설정할 수도 있습니다.

### <a name="step-1---assemble-inputs-for-the-request"></a>1단계 - 요청에 대한 입력 어셈블

요청마다 Azure Cognitive Search의 서비스 이름 및 관리자 키를(POST 헤더에) 제공해야 합니다. [Postman](./search-get-started-rest.md) 또는 REST API 클라이언트를 사용하여 Azure Cognitive Search에 HTTPS 요청을 보낼 수 있습니다.

요청에 사용할 수 있도록 다음 값을 복사하여 저장합니다.

+ Azure Cognitive Search 서비스 이름
+ Azure Cognitive Search 관리자 키
+ Cosmos DB Gremlin API 연결 문자열

Azure Portal에서 해당 값을 찾을 수 있습니다.

1. Azure Cognitive Search에 대한 포털 페이지에서 개요 페이지의 검색 서비스 URL을 복사합니다.

2. 왼쪽 탐색 창에서 **키** 를 클릭한 다음 기본 키 또는 보조 키를 복사합니다.

3. Cosmos DB 계정에 대한 포털 페이지로 전환합니다. 왼쪽 탐색 창의 **설정** 에서 **키** 를 클릭합니다. 이 페이지에서는 URI, 두 개의 연결 문자열 집합 및 두 개의 키 집합을 제공합니다. 연결 문자열 중 하나를 메모장에 복사합니다.

### <a name="step-2---create-a-data-source"></a>2단계 - 데이터 원본 만들기

**데이터 원본** 은 인덱싱할 데이터, 자격 증명 및 데이터의 변경 사항 식별 정책(예: 컬렉션 내 수정 또는 삭제된 문서)을 지정합니다. 데이터 소스는 독립 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

데이터 원본을 만들려면 POST 요청을 작성합니다.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myGraphId", "query": null }
    }
```

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | Description |
|---------|-------------|
| **name** | 필수 사항입니다. 데이터 원본 개체를 나타낼 이름을 선택합니다. |
|**type**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**credentials** | 필수 사항입니다. **connectionString** 에는 AccountEndpoint, AccountKey, ApiKind, Database가 포함되어야 합니다. ApiKind는 **Gremlin** 입니다.</br></br>예를 들면 다음과 같습니다.<br/>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=Gremlin`<br/><br/>AccountEndpoint는 `*.documents.azure.com` 엔드포인트를 사용해야 합니다.
| **container** | 다음과 같은 요소가 있습니다. <br/>**name**: 필수 사항입니다. 그래프의 ID를 지정합니다.<br/>**query**: (선택 사항) 기본값은 `g.V()`입니다. 가장자리를 인덱싱하려면 쿼리를 `g.E()`로 설정합니다. |
| **dataChangeDetectionPolicy** | 증분 진행은 기본적으로 사용하도록 설정되며 `_ts`를 상위 워터마크 열로 사용합니다. |
|**dataDeletionDetectionPolicy** | 선택 사항입니다. [삭제된 문서 인덱싱](#DataDeletionDetectionPolicy) 섹션을 참조하세요.|

### <a name="step-3---create-a-target-search-index"></a>3단계 - 대상 검색 인덱스 만들기

아직 없는 경우 [대상 Azure Cognitive Search 인덱스를 만듭니다](/rest/api/searchservice/create-index). 다음 예제에서는 ID, 레이블, 설명 필드를 사용하여 인덱스를 만듭니다.

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [
        {
            "name": "rid",
            "type": "Edm.String",
            "facetable": false,
            "filterable": false,
            "key": true,
            "retrievable": true,
            "searchable": true,
            "sortable": false,
            "analyzer": "standard.lucene",
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "synonymMaps": [],
            "fields": []
        },{
            "name": "id",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
        }, {
            "name": "label",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": true,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": "standard.lucene",
            "synonymMaps": []
       }]
     }
```

대상 인덱스 스키마가 그래프와 호환되는지 확인합니다.

분할된 컬렉션의 경우, 기본 문서 키는 Azure Cosmos DB의 `_rid` 속성입니다. 필드 이름은 밑줄 문자로 시작할 수 없으므로 Azure Cognitive Search에서 이름이 `rid`로 자동으로 바뀝니다. 또한 Azure Cosmos DB의 `_rid` 값은 Azure Cognitive Search 키에 잘못된 문자를 포함합니다. 그러므로 문서 키로 만들려면 `_rid` 값이 Base64로 인코딩되어야 합니다.

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 데이터 형식과 Azure Cognitive Search 데이터 형식 사이의 매핑

| JSON 데이터 형식 | 호환되는 대상 인덱스 필드 형식 |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| 정수와 같이 보이는 숫자 |Edm.Int32, Edm.Int64, Edm.String |
| 부동소수점처럼 보이는 숫자 |Edm.Double, Edm.String |
| String |Edm.String |
| 기본 형식의 배열, 예: ["a", "b", "c"] |Collection(Edm.String) |
| 날짜처럼 보이는 문자열 |Edm.DateTimeOffset, Edm.String |
| GeoJSON 개체, 예: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 기타 JSON 개체 |해당 없음 |

### <a name="step-4---configure-and-run-the-indexer"></a>4단계 - 인덱서 구성 및 실행

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name": "mycosmosdbgremlinindexer",
      "description": "My Cosmos DB Gremlin API indexer",
      "dataSourceName": "mycosmosdbgremlindatasource",
      "targetIndexName": "mysearchindex"
    }
```

이 인덱서는 만들어진 후 실행을 시작하고 한 번만 실행됩니다. 요청에 선택적 일정 매개 변수를 추가하여 인덱서가 일정에 따라 실행되도록 설정할 수 있습니다. 인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법](search-howto-schedule-indexers.md)을 참조하세요.

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>삭제된 문서 인덱싱

그래프 데이터가 삭제되면 검색 인덱스에서 상응하는 문서도 삭제하는 것이 좋습니다. 데이터 삭제 검색 정책은 삭제된 데이터 항목을 효율적으로 식별하고 인덱스에서 전체 문서를 삭제하기 위한 것입니다. 데이터 삭제 검색 정책은 부분적인 문서 정보를 삭제하기 위한 것이 아닙니다. 현재까지 지원되는 유일한 정책은 다음과 같이 지정되는 `Soft Delete` 정책입니다(삭제 시 일부 유형의 플래그로 표시됨).

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

다음 예제에서는 일시 삭제 정책을 사용하여 데이터 원본을 만듭니다.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [Search service admin key]
    
    {
        "name": "mycosmosdbgremlindatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;ApiKind=Gremlin;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "`_ts`"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

<a name="MappingGraphData"></a>

## <a name="mapping-graph-data-to-a-search-index"></a>검색 인덱스에 그래프 데이터 매핑

Cosmos DB Gremlin API 인덱서는 다음과 같은 몇 가지 그래프 데이터를 자동으로 매핑합니다.

1. 인덱서는 `_rid`를 인덱스의 `rid` 필드에 매핑합니다(있는 경우). 인덱스에서 `rid` 값을 키로 사용하려는 경우 해당 키를 Base64로 인코딩해야 합니다. Azure Cognitive Search 문서 키에서 잘못된 문자가 `_rid`에 포함되어 있을 수 있기 때문입니다.

1. 인덱서는 `_id`를 인덱스의 `id` 필드에 매핑합니다(있는 경우).

1. Gremlin API를 사용하여 Cosmos DB 데이터베이스를 쿼리하는 경우 각 속성의 JSON 출력에 `id` 및 `value`가 있는 것을 알 수 있습니다. Azure Cognitive Search Cosmos DB 인덱서는 `value` 속성과 이름이 동일한 검색 인덱스의 필드에 이 속성을 자동으로 매핑합니다(있는 경우). 다음 예제에서는 450이 검색 인덱스의 `pages` 필드에 매핑됩니다.

```http
    {
        "id": "Cookbook",
        "label": "book",
        "type": "vertex",
        "properties": {
          "pages": [
            {
              "id": "48cf6285-a145-42c8-a0aa-d39079277b71",
              "value": "450"
            }
          ]
        }
    }
```

쿼리 출력을 인덱스의 필드에 매핑하려면 [출력 필드 매핑](cognitive-search-output-field-mapping.md)을 사용해야 할 수 있습니다. 사용자 지정 쿼리에 복잡한 데이터가 있을 가능성이 높으므로 [필드 매핑](search-indexer-field-mappings.md) 대신 출력 필드 매핑을 사용하려 할 것입니다.

예를 들어 쿼리에서 다음 출력을 생성한다고 가정해 보겠습니다.

```json
    [
      {
        "vertex": {
          "id": "Cookbook",
          "label": "book",
          "type": "vertex",
          "properties": {
            "pages": [
              {
                "id": "48cf6085-a211-42d8-a8ea-d38642987a71",
                "value": "450"
              }
            ],
          }
        },
        "written_by": [
          {
            "yearStarted": "2017"
          }
        ]
      }
    ]
```

위의 JSON에 있는 `pages` 값을 인덱스의 `totalpages` 필드에 매핑하려면 다음 [출력 필드 매핑](cognitive-search-output-field-mapping.md)을 인덱서 정의에 추가하면 됩니다.

```json
    ... // rest of indexer definition 
    "outputFieldMappings": [
        {
          "sourceFieldName": "/document/vertex/pages",
          "targetFieldName": "totalpages"
        }
    ]
```

어떻게 출력 필드 매핑이 `/document`로 시작하며 JSON의 속성 키에 대한 참조를 포함하지 않는지 확인하세요. 인덱서가 그래프 데이터를 수집할 때 `/document` 노드에 각 문서를 배치하기 때문입니다. 또한 인덱서를 사용하면 `pages` 배열의 첫 번째 개체를 참조해야 하는 것이 아니라 간단히 `pages`를 참조하여 `pages` 값을 참조할 수 있기 때문입니다.

## <a name="next-steps"></a>다음 단계

+ Azure Cosmos DB Gremlin API에 대한 자세한 내용은 [Azure Cosmos DB: Gremlin API 소개]()../cosmos-db/graph-introduction.md)를 참조하세요.

+ Azure Cognitive Search 시나리오 및 가격 책정에 대한 자세한 내용은 [azure.microsoft.com의 Search 서비스 페이지](https://azure.microsoft.com/services/search/)를 참조하세요.