---
title: 인덱서 만들기
titleSuffix: Azure Cognitive Search
description: 인덱서의 속성을 설정하여 데이터 원본과 대상을 결정합니다. 매개 변수를 설정하여 런타임 동작을 수정할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: d0d36c9936fdf61ff46688caaec215684ae0f9af
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132809915"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서 만들기

검색 인덱서는 외부 데이터 원본에서 콘텐츠를 읽고 해당 콘텐츠를 검색 서비스의 검색 인덱스에 수집 하는 자동화 된 워크플로를 제공 합니다. 인덱서는 다음 두 가지 워크플로를 지원 합니다. 

+ 전체 텍스트 검색에 대 한 텍스트 및 메타 데이터 추출
+ 텍스트 및 구조에 대 한 이미지 및 매우 차별화 되지 않은 텍스트를 분석 하 여 더 심층적인 콘텐츠 처리를 위해 [AI 보강](cognitive-search-concept-intro.md) 을 파이프라인에 추가 합니다. 

인덱서를 사용하면 작성해야 하는 코드의 양과 복잡성이 크게 감소합니다. 이 문서에서는 원본별 인덱서와 [기술 세트](cognitive-search-working-with-skillsets.md)를 사용한 고급 작업을 위한 준비로 인덱서를 만드는 방법을 중점적으로 다룹니다.

## <a name="indexer-structure"></a>인덱서 구조

다음 인덱스 정의는 텍스트 기반 및 AI 보강 시나리오에 대해 만들 수 있는 일반적인 항목입니다.

### <a name="indexing-for-full-text-search"></a>전체 텍스트 검색에 대 한 인덱싱

인덱서의 원래 목적은 데이터 원본에 있는 필드의 텍스트와 숫자 콘텐츠에 연결하고 이들을 읽는 메커니즘을 제공하여 복잡한 인덱스 로드 프로세스를 단순화하고, 해당 콘텐츠를 JSON 문서로 직렬화하고, 인덱싱을 위해 해당 문서를 검색 엔진에 전달하는 것이었습니다. 이는 여전히 기본 사용 사례이며 이 작업의 경우 다음 예제에 정의된 속성으로 인덱서를 만들어야 합니다.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`** , **`dataSourceName`** 및 **`targetIndexName`** 속성은 필수 항목이며 인덱서를 실행하려면 먼저 데이터 원본과 인덱스가 모두 서비스에 있어야 합니다. 

**`parameters`** 속성은 전체 작업이 실패하기 전에 허용할 오류 수와 같은 런타임 동작을 수정합니다. 매개 변수는 원본 관련 동작을 지정하는 방법이기도 합니다. 예를 들어 원본이 Blob Storage인 경우 파일 확장명을 기준으로 필터링하는 매개 변수를 설정할 수 있습니다. `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }`

**`field mappings`** 속성은 필드의 이름이나 형식이 다른 경우 원본 필드와 대상 필드를 명시적으로 매핑하는 데 사용됩니다. 다른 속성(표시되지 않음)은 [일정을 지정](search-howto-schedule-indexers.md)하거나 사용되지 않는 상태에서 인덱서를 생성하거나 미사용 데이터의 보충 암호화를 위한 [암호화 키](search-security-manage-encryption-keys.md)를 지정하는 데 사용됩니다.

### <a name="indexing-for-ai-enrichment"></a>AI 보강 인덱싱

인덱서는 검색 서비스가 아웃바운드 요청을 수행하는 메커니즘이기 때문에 이 사용 사례 구현을 위해 인프라와 개체를 추가하여 AI 보강을 지원하도록 인덱서가 확장되었습니다.

위의 모든 속성과 매개 변수는 AI 보강을 수행하는 인덱서에 적용됩니다. **`skillSets`** , **`outputFieldMappings`** , **`cache`** 속성은 AI 보강에만 해당됩니다(미리 보기 및 REST만). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI 보강은 이 문서에서 다루지 않습니다. 자세히 알아보려면 [AI 보강](cognitive-search-concept-intro.md), [Azure Cognitive Search의 기술 세트](cognitive-search-working-with-skillsets.md) 및 [기술 세트 만들기(REST)](/rest/api/searchservice/create-skillset) 문서로 시작하세요.

## <a name="prerequisites"></a>사전 요구 사항

+ [지원 되는 데이터 원본을](search-indexer-overview.md#supported-data-sources)사용 합니다.

+ 관리 권한이 있어야 합니다. 상태 또는 정의에 대한 GET 요청을 포함하여 인덱서와 관련된 모든 작업에는 요청에 대한 [admin api-key](search-security-api-keys.md)가 필요합니다.

[서비스 계층](search-limits-quotas-capacity.md#indexer-limits)마다 만들 수 있는 개체 수에 제한이 있습니다. 무료 계층을 사용하는 경우 형식별 개체 3개와 인덱서 처리 시간 2분(기술 세트 제외)으로 제한됩니다.

## <a name="how-to-create-indexers"></a>인덱서를 만드는 방법

원격 검색 서비스에서 인덱서를 만들 준비가 되면 Azure Portal 또는 Postman과 같은 도구 형태의 검색 클라이언트나 인덱서 클라이언트를 인스턴스화하는 코드가 필요합니다. 초기 개발 및 개념 증명 테스트에는 Azure Portal 또는 REST API를 사용하는 것이 좋습니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/indexer-portal)

포털은 인덱서 생성을 위한 두 가지 옵션, 즉 [**데이터 가져오기 마법사**](search-import-data-portal.md)와 인덱서 정의 지정을 위한 필드를 제공하는 **새 인덱서** 를 제공합니다. 마법사는 필요한 모든 요소를 만든다는 점에서 고유합니다. 다른 방법을 사용하려면 데이터 원본과 인덱스를 미리 정의해야 합니다.

다음 스크린샷은 포털에서 이러한 기능을 찾을 수 있는 위치를 보여줍니다. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="호텔 인덱서" border="true":::

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

Postman과 Visual Studio Code(Azure Cognitive Search용 확장 사용) 모두 인덱서 클라이언트로 작동할 수 있습니다. 둘 중 어떤 도구를 사용하든 검색 서비스에 연결하고 [인덱서 만들기(REST)](/rest/api/searchservice/create-indexer) 요청을 보낼 수 있습니다. 개체를 만들기 위한 REST 클라이언트를 보여주는 다양한 자습서와 예제가 있습니다. 

각 클라이언트에 대해 알아보려면 다음 문서 중 하나로 시작하세요.

+ [REST 및 Postman을 사용하여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

인덱서 요청을 작성하는 데 도움이 필요한 경우 [인덱서 작업(REST)](/rest/api/searchservice/Indexer-operations)을 참조하세요.

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-csharp)

Cognitive Search의 경우 Azure SDK는 일반적으로 사용 가능한 기능을 구현합니다. 따라서 모든 SDK를 사용하여 인덱서 관련 개체를 만들 수 있습니다. 이들 모두 스킬 세트를 포함하여 인덱서와 관련 개체를 만드는 메서드가 있는 **SearchIndexerClient** 를 제공합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [인덱서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

---

## <a name="run-the-indexer"></a>인덱서 실행

인덱서 정의에서을 설정 하지 않으면 **`disabled=true`** 서비스에서 인덱서를 만들 때 인덱서가 즉시 실행 됩니다. 이때 데이터 원본 연결 오류, 필드 매핑 문제 또는 기술 문제가 있는지 확인할 수 있습니다. 

여러 가지 방법으로 인덱서를 실행할 수 있습니다.

+ [인덱서 만들기](/rest/api/searchservice/create-indexer) 또는 [인덱서 업데이트](/rest/api/searchservice/update-indexer)에 대한 HTTP 요청을 보내 정의를 추가하거나 변경하고 인덱서를 실행합니다.

+ [인덱서 실행](/rest/api/searchservice/run-indexer)에 대한 HTTP 요청을 보내 정의를 변경하지 않고 인덱서를 실행합니다.

+ 만들기, 업데이트 또는 실행을 위해 SearchIndexerClient 메서드를 호출하는 프로그램을 실행합니다.

또는 인덱서를 [예약](search-howto-schedule-indexers.md)하여 정기적으로 처리를 호출합니다. 

예약된 처리는 일반적으로 변경된 콘텐츠의 증분 인덱싱에 대한 필요와 일치합니다. 변경 검색 논리는 원본 플랫폼에 기본 제공되는 기능입니다. Blob 컨테이너의 변경 내용은 인덱서에 의해 자동으로 검색됩니다. 다른 데이터 원본에서 변경 검색을 활용하는 방법에 대한 지침은 특정 데이터 원본에 대한 인덱서 문서를 참조하세요.

+ [Azure SQL 데이터베이스](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>변경 검색 및 인덱서 상태

인덱서는 기본 데이터의 변경 내용을 검색하고 각 인덱서 실행 시 새 문서나 업데이트된 문서만 처리할 수 있습니다. 예를 들어, `0/0`개의 문서가 처리된 상태에서 실행이 성공했다고 표시되면 인덱서가 기본 데이터 원본에서 새롭거나 변경된 행 또는 Blob을 찾지 못한 것입니다.

인덱서가 변경 감지를 지원하는 방법은 데이터 원본에 따라 다릅니다.

+ Azure Blob Storage, Azure Table Storage 및 Azure Data Lake Storage Gen2는 각 Blob 또는 행 업데이트에 날짜와 시간을 찍습니다. 다양한 인덱서가 이 정보를 사용하여 인덱스에서 업데이트할 문서를 결정합니다. 기본 제공 변경 검색은 사용자가 추가로 구성할 필요 없이 인덱서가 새 문서와 업데이트된 문서를 인식할 수 있음을 의미합니다.

+ Azure SQL과 Cosmos DB는 해당 플랫폼에서 변경 검색 기능을 제공합니다. 데이터 원본 정의에서 변경 검색 정책을 지정할 수 있습니다.

인덱스를 대량으로 로드하는 경우 인덱서는 내부 "상위 워터마크"를 통해 처리한 마지막 문서를 추적하기도 합니다. 마커는 API에서 노출되지 않지만 내부적으로 인덱서는 중지된 위치를 추적합니다. 예약된 실행이나 주문형 호출을 통해 인덱싱이 다시 시작되면 인덱서는 중지된 부분을 선택할 수 있도록 상위 워터마크를 참조합니다.

전체를 다시 인덱싱하기 위해 상위 워터마크를 지워야 하는 경우 [인덱서 다시 설정](/rest/api/searchservice/reset-indexer)을 사용할 수 있습니다. 선택적 다시 인덱싱을 위해 [기술 다시 설정](/rest/api/searchservice/preview-api/reset-skills) 또는 [문서 다시 설정](/rest/api/searchservice/preview-api/reset-documents)을 사용합니다. 다시 설정 API를 통해 내부 상태를 지울 수 있으며 [증분 보강](search-howto-incremental-index.md)를 사용하도록 설정한 경우 캐시를 플러시할 수도 있습니다. 각 다시 설정 옵션의 배경 및 비교에 대한 자세한 내용은 [인덱서, 기술 및 문서 실행 또는 다시 설정](search-howto-run-reset-indexers.md)을 참조하세요.

## <a name="data-preparation"></a>데이터 준비

인덱서는 각 행이 인덱스에서 전체 또는 부분 검색 문서가 되는 테이블 형식의 행 집합을 필요로 합니다. 데이터베이스의 행과 결과 검색 문서 간에 일 대 일 대응이 발생하는 경우가 많습니다. 이 경우 행 집합의 모든 필드가 각 문서를 완전히 채웁다. 그러나 인덱서를 사용하여 문서 필드의 하위 집합을 생성하고 다른 인덱서 또는 방법론을 사용하여 나머지 필드를 채울 수 있습니다. 

관계형 데이터를 행 집합으로 평면화하려면 SQL 보기를 만들거나 동일한 행에 부모 및 자식 레코드를 반환하는 쿼리를 작성해야 합니다. 예를 들어 기본 제공 호텔 샘플 데이터 세트는 관련 테이블의 객실 레코드에 연결된 50개의 레코드(호텔당 하나씩)가 있는 SQL 데이터베이스입니다. 축적된 데이터를 행 집합으로 평면화하는 쿼리는 각 호텔 레코드의 JSON 문서에 모든 객실 정보를 포함합니다. 포함된 객실 정보는 **FOR JSON AUTO** 절을 사용하는 쿼리에 의해 생성됩니다. 이 기술에 대한 자세한 내용은 [포함된 JSON을 반환하는 쿼리 정의](index-sql-relational-data.md#define-a-query-that-returns-embedded-json)를 참조하세요. 이는 하나의 예일 뿐입니다. 동일한 효과를 얻을 수 있는 다른 방법을 찾을 수 있습니다.

평면화된 데이터 외에도 검색 가능한 데이터만 끌어오는 것이 중요합니다. 검색 가능한 데이터는 영숫자입니다. Cognitive Search는 이미지 파일의 텍스트 설명을 추출하고 추론([AI 보강](cognitive-search-concept-intro.md) 참조)하여 검색 가능한 콘텐츠를 만들 수 있지만 어떤 형식의 이진 데이터도 검색할 수 없습니다. 마찬가지로 AI 보강을 사용하여 자연어 모델에서 큰 텍스트를 분석하여 구조 또는 관련 정보를 찾아 검색 문서에 추가할 수 있는 새 콘텐츠를 생성할 수 있습니다.

인덱서가 데이터 문제를 수정하지 않는 경우 다른 형태의 데이터 정리 또는 조작이 필요할 수 있습니다. 자세한 내용은 [Azure 데이터베이스 제품](../index.yml?product=databases)의 설명서를 참조해야 합니다.

## <a name="index-preparation"></a>인덱스 준비

인덱서는 인덱싱을 위해 검색 문서를 검색 엔진에 전달합니다. 인덱서에 실행 동작을 결정하는 속성이 있는 것처럼 인덱스 스키마에는 문자열이 인덱싱되는 방식에 큰 영향을 주는 속성이 있습니다(문자열만 분석되고 토큰화됨). 분석기 할당에 따라 인덱싱된 문자열이 전달된 문자열과 다를 수 있습니다. [텍스트 분석(REST)](/rest/api/searchservice/test-analyzer)을 사용하여 분석기의 효과를 평가할 수 있습니다. 분석기에 대한 자세한 내용은 [텍스트 처리용 분석기](search-analyzers.md)를 참조하세요.

인덱서는 인덱스와 상호 작용하는 방식에 따라 필드 이름과 형식만 검사합니다. 인덱스의 해당 검색 필드에 대해 들어오는 내용이 올바른지 확인하는 유효성 검사 단계가 없습니다. 확인 단계로 전체 문서 또는 선택한 필드를 반환하는 채워진 인덱스에 대해 쿼리를 실행할 수 있습니다. 인덱스의 내용을 쿼리하는 방법에 대한 자세한 내용은 [기본 쿼리 만들기](search-query-create.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

+ [인덱서 일정](search-howto-schedule-indexers.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 상태 모니터링](search-howto-monitor-indexers.md)
+ [관리 ID를 사용하여 연결](search-howto-managed-identities-data-sources.md)