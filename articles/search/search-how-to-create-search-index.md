---
title: 검색 인덱스 만들기
titleSuffix: Azure Cognitive Search
description: Azure Portal, REST API 또는 Azure SDK를 사용하여 검색 인덱스를 만듭니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 2bbc9acad41525389fe5bc7a8f1164f4e727aed4
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759291"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Azure Cognitive Search 검색 인덱스 만들기

Azure Cognitive Search 쿼리는 검색 인덱스의 검색 가능한 텍스트를 대상으로 합니다. 이 문서에서는 Azure Cognitive Search 지원하는 형식을 사용하여 검색 인덱스 정의 및 게시 단계를 알아봅니다. 

인덱서 를 사용하지 않는 한 [인덱스](search-howto-create-indexers.md)만들기 및 인덱스 채우기는 별도의 두 작업입니다. 인덱서가 아닌 시나리오의 경우 인덱스를 만든 후 다음 단계는 [데이터 가져오기입니다.](search-what-is-data-import.md) 

인덱스 관련 개념에 대한 자세한 내용은 [Azure Cognitive Search 인덱스 검색을 참조하세요.](search-what-is-an-index.md)

## <a name="prerequisites"></a>사전 요구 사항

인덱스를 만들고 로드하려면 쓰기 권한이 필요하며, 요청에 대한 [관리자 API 키를](search-security-api-keys.md) 통해 부여됩니다. 또는 Azure Active Directory [역할 기반 액세스 제어 공개 미리 보기](search-security-rbac.md)에 참여하는 경우 검색 참가자 역할의 멤버로 요청을 발급할 수 있습니다.

인덱스 만들기는 주로 스키마 정의 연습입니다. 만들기 전에 다음이 있어야 합니다.

+ 인덱스(스키마 [검사 목록에서](#schema-checklist)자세히 설명)에서 검색 가능하고, 검색 가능하고, 필터링 가능하고, facetable이고, 정렬 가능하도록 만들려는 필드를 명확하게 파악합니다.

+ 인덱스 문서 키(또는 ID)로 사용할 수 있는 원본 데이터의 고유 식별자입니다.

+ 안정적인 인덱스 위치입니다. 기존 인덱스 를 다른 검색 서비스로 이동하는 것은 지원되지 않습니다. 애플리케이션 요구 사항을 다시 확인하고 기존 검색 서비스, 해당 용량 및 위치가 요구 사항에 충분한지 확인합니다.

마지막으로, 모든 서비스 계층에는 만들 수 있는 개체 수에 대한 [인덱스 제한이](search-limits-quotas-capacity.md#index-limits) 있습니다. 예를 들어 무료 계층에서 실험하는 경우 지정된 시간에 3개의 인덱스만 가질 수 있습니다. 인덱스 자체 내에는 복합 필드 및 컬렉션의 수에 제한이 있습니다.

## <a name="allowed-updates"></a>허용되는 업데이트

[**인덱스 만들기는**](/rest/api/searchservice/create-index) 검색 서비스에서 물리적 데이터 구조(파일 및 반전된 인덱스)를 만드는 작업입니다. 인덱스가 만들어지면 [**인덱스 업데이트를**](/rest/api/searchservice/update-index) 사용하여 변경 내용을 적용하는 기능은 수정으로 인해 물리적 구조가 무효화되는지 여부에 따라 달라집니다. 인덱스로 필드를 만든 후에는 대부분의 필드 특성을 변경할 수 없습니다.

디자인 프로세스의 변동을 최소화하기 위해 다음 표에서는 스키마에서 고정되고 유연한 요소를 설명합니다. 고정 요소를 변경하려면 인덱스를 다시 작성해야 하는 반면 유연한 요소는 물리적 구현에 영향을 주지 않고 언제든지 변경할 수 있습니다. 

| 요소 | 업데이트할 수 있나요? |
|---------|-----------------|
| Name | 예 |
| 키 | 아니요 |
| 필드 이름 및 형식 | 아니요 |
| 필드 특성(검색 가능, 필터링 가능, facetable, 정렬 가능) | No |
| 필드 특성(검색 가능) | 예 |
| [분석기](search-analyzers.md) | 인덱스 내의 사용자 지정 분석기를 추가하고 수정할 수 있습니다. 문자열 필드에 대한 분석기 할당과 관련하여 "searchAnalyzer"만 수정할 수 있습니다. 다른 모든 할당 및 수정에는 다시 빌드가 필요합니다. |
| [점수 매기기 프로필](index-add-scoring-profiles.md) | 예 |
| [확인기](index-add-suggesters.md) | No |
| [CORS(원본 간 원격 스크립팅)](#corsoptions) | 예 |
| [암호화](search-security-manage-encryption-keys.md) | 예 |

> [!NOTE]
> [동의어 맵은](search-synonyms.md) 인덱스 정의의 일부가 아닙니다. 동의어 맵을 수정해도 물리적 검색 인덱스에는 영향을 미치지 않습니다.

## <a name="schema-checklist"></a>스키마 검사 목록

이 검사 목록을 사용하여 검색 인덱스에 대한 디자인 결정을 내릴 수 있습니다.

1. 인덱스 및 필드 이름이 [명명 규칙을](/rest/api/searchservice/naming-rules) 준수하도록 명명 규칙을 검토합니다.

1. [지원되는 데이터 형식을 검토합니다.](/rest/api/searchservice/supported-data-types) 데이터 형식은 필드 사용 방법에 영향을 미칩니다. 예를 들어 숫자 콘텐츠는 필터링 가능하지만 전체 텍스트를 검색할 수는 없습니다. 가장 일반적인 데이터 형식은 `Edm.String` 전체 텍스트 검색 엔진을 사용하여 토큰화되고 쿼리되는 검색 가능한 텍스트에 대한 것입니다.

1. 고유 값이 포함된 원본 데이터에서 하나의 필드를 식별하여 인덱스의 키 필드로 작동할 수 있도록 합니다. 예를 들어 Blob Storage 인덱싱하는 경우 스토리지 경로가 문서 키로 사용되는 경우가 많습니다. 

   모든 인덱스에는 *문서 키로* 사용되는 하나의 필드("문서 ID"라고도 함)가 필요합니다. 키는 검색 인덱스 문자열이지만 원본 데이터의 고유 식별자에도 매핑할 수 있습니다. 검색 결과에서 레코드 또는 엔터티를 다시 구성하고, 검색 인덱스에서 특정 문서를 검색하고, 문서 단위 수준에서 선택적 데이터를 처리하려면 특정 검색 문서를 고유하게 식별하는 기능이 필요합니다.

1. 인덱스 내 검색 가능한 콘텐츠에 기여할 데이터 원본의 필드를 식별합니다. 검색 가능한 콘텐츠에는 전체 텍스트 검색 엔진을 사용하여 쿼리되는 짧거나 긴 문자열이 포함됩니다. 콘텐츠가 자세한 내용(작은 구 또는 더 큰 청크)인 경우 다양한 분석기로 실험하여 텍스트가 토큰화되는 방법을 확인합니다.

   [필드 특성 할당은](search-what-is-an-index.md#index-attributes) 검색 동작과 검색 서비스에서 인덱스의 물리적 표현을 모두 결정합니다. 필드를 지정하는 방법은 많은 고객에게 반복적인 프로세스입니다. 반복 속도를 높이기 위해 쉽게 삭제하고 다시 빌드할 수 있도록 샘플 데이터로 시작합니다.

1. 필터로 사용할 수 있는 원본 필드를 식별합니다. 숫자 콘텐츠 및 짧은 텍스트 필드, 특히 반복 값이 있는 필드가 좋은 선택입니다. 필터를 작업할 때 다음을 기억하세요.

   + 필터링 가능한 필드는 필요에 따라 측면 탐색에 사용할 수 있습니다.

   + 필터링 가능한 필드는 임의의 순서로 반환되므로 정렬 가능하도록 만드는 것이 좋습니다.

## <a name="formulate-a-request"></a>요청 작성

인덱스 만들기 준비가 되면 여러 가지 방법으로 진행할 수 있습니다. 초기 개발 및 개념 증명 테스트에는 Azure Portal 또는 REST API를 사용하는 것이 좋습니다.

개발하는 동안 다시 빌드하는 계획을 자주 세워야 합니다. 서비스에서 물리적 구조가 생성되기 때문에 많은 수정을 위해 [인덱스를 삭제하고 다시 만들어야](search-howto-reindex.md) 합니다. 보다 빠르게 다시 작성할 수 있도록 데이터 하위 집합을 사용하는 방안을 고려해 볼 수 있습니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/index-portal)

포털을 통한 인덱스 디자인은 숫자 필드에서 전체 텍스트 검색 기능을 허용하지 않는 등의 특정 데이터 형식에 대한 요구 사항 및 스키마 규칙을 적용합니다. 포털에는 검색 인덱스 만들기에 대한 두 가지 옵션이 있습니다. 

+ **인덱스 추가는 인덱스** 스키마를 지정하기 위한 포함된 편집기입니다.
+ [**데이터 가져오기는**](search-import-data-portal.md) 마법사입니다.

마법사는 인덱서, 데이터 원본, 로딩 데이터를 만들어 추가 작업을 압축합니다. 원하는 것보다 많은 경우 **인덱스 추가** 또는 다른 접근 방식만 사용해야 합니다.

다음 스크린샷은 명령 모음에서 **인덱스 추가** 및 **데이터 가져오기를** 찾을 수 있는 위치를 보여줍니다. 인덱스를 만든 후 인덱스 탭에서 **인덱스를** 다시 찾을 수 있습니다.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="인덱스 추가 명령" border="true":::

> [!Tip]
> 포털에서 인덱스 만들기 후 JSON 표현을 복사하여 애플리케이션 코드에 추가할 수 있습니다.

### <a name="rest"></a>[**REST**](#tab/index-rest)

[**인덱스 만들기(REST)는**](/rest/api/searchservice/create-index) 인덱스 만들기에 사용됩니다. Postman과 Visual Studio Code(Azure Cognitive Search용 확장 사용)는 모두 검색 인덱스 클라이언트로 작동할 수 있습니다. 두 도구 중 하나를 사용하여 검색 서비스에 연결하고 요청을 보낼 수 있습니다.

+ [REST 및 Postman을 사용하여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

REST API 필드 특성에 대한 기본값을 제공합니다. 예를 들어 모든 Edm.String 필드는 기본적으로 검색할 수 있습니다. 특성은 설명을 위해 아래에 전체로 표시되지만 기본값이 적용되는 경우 특성을 생략할 수 있습니다.

인덱스 요청을 작성하는 데 도움이 필요한 경우 [인덱스 작업(REST)](/rest/api/searchservice/index-operations)을 참조하세요.

```json
POST https://[servicename].search.windows.net/indexes?api-version=[api-version] 
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "retrievable": true, "searchable": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Address", "type": "Edm.ComplexType", 
      "fields": [
          { "name": "StreetAddress", "type": "Edm.String", "retrievable": true, "filterable": false, "sortable": false, "facetable": false, "searchable": true },
          { "name": "City", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true },
          { "name": "StateProvince", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true }
        ]
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ]
  }
}
```

### <a name="net-sdk"></a>[**.NET SDK**](#tab/index-dotnet)

.NET용 Azure SDK에는 인덱스를 만들고 업데이트하는 메서드가 있는 [**SearchIndexClient가**](/dotnet/api/azure.search.documents.indexes.searchindexclient) 있습니다.

```csharp
// Create the index
string indexName = "hotels";
SearchIndex index = new SearchIndex(indexName)
{
    Fields =
    {
        new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
        new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
        new SearchableField("description") { AnalyzerName = LexicalAnalyzerName.EnLucene },
        new SearchableField("descriptionFr") { AnalyzerName = LexicalAnalyzerName.FrLucene }
        new ComplexField("address")
        {
            Fields =
            {
                new SearchableField("streetAddress"),
                new SearchableField("city") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("stateProvince") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("country") { SynonymMapNames = new[] { synonymMapName }, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("postalCode") { IsFilterable = true, IsSortable = true, IsFacetable = true }
            }
        }
    }
};

await indexClient.CreateIndexAsync(index);
```

자세한 예제는[azure-search-dotnet-samples/quickstart/v11/ 를 참조하세요.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)

### <a name="other-sdks"></a>[**기타 SDK**](#tab/index-other-sdks)

Cognitive Search의 경우 Azure SDK는 일반적으로 사용 가능한 기능을 구현합니다. 따라서 SDK를 사용하여 검색 인덱스를 만들 수 있습니다. 모두 인덱스를 만들고 업데이트하는 메서드가 있는 **SearchIndexClient** 를 제공합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [인덱스](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

<a name="corsoptions"></a>

## <a name="set-corsoptions-for-cross-origin-queries"></a>`corsOptions`원본 간 쿼리에 대해 설정

인덱스 스키마에는 `corsOptions` 설정에 대한 섹션이 포함됩니다. 브라우저에서는 모든 원본 간 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 원본 간 쿼리를 허용하려면 **corsOptions** 특성을 설정하여 CORS(원본 간 리소스 공유)를 사용하도록 설정합니다. 보안상의 이유로 [쿼리 API만](search-query-create.md#choose-query-methods) CORS를 지원합니다.

```json
"corsOptions": {
  "allowedOrigins": [
    "*"
  ],
  "maxAgeInSeconds": 300
```

CORS에 대해 다음 속성을 설정할 수 있습니다.

+ **allowedOrigins**(필수): 인덱스에 대한 액세스 권한을 부여할 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://<fully-qualified-domain-name>:<port>` 형식이지만 `<port>`는 대개 생략됩니다. 자세한 내용은 [원본 간 리소스 공유(위키백과)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 참조하세요.

  모든 원본에 대한 액세스를 허용하려면 **allowedOrigins** 배열에서 `*`를 단일 항목으로 포함합니다. *프로덕션 검색 서비스에 권장되는 방법은* 아니지만 개발 및 디버깅에 유용한 경우가 많습니다.

+ **maxAgeInSeconds**(선택): 브라우저는 이 값을 사용하여 CORS 실행 전 응답을 캐시할 기간(초)을 결정합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

## <a name="next-steps"></a>다음 단계

데이터를 사용하여 인덱스 로드에 익숙해지려면 다음 링크를 사용합니다.

+ [데이터 가져오기 개요](search-what-is-data-import.md)

+ [문서 추가, 업데이트 또는 삭제(REST)](/rest/api/searchservice/addupdate-or-delete-documents) 