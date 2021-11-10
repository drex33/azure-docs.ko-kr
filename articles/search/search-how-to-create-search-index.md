---
title: 검색 인덱스 만들기
titleSuffix: Azure Cognitive Search
description: Azure Portal, REST Api 또는 Azure SDK를 사용 하 여 검색 인덱스를 만듭니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: d472d5c7e9ccec3ffb59f040a380fc0656c1ef5c
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137732"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 인덱스 만들기

Azure Cognitive Search 쿼리 요청은 검색 인덱스에서 검색 가능한 텍스트를 대상으로 합니다. 이 문서에서는 Azure Cognitive Search에서 지 원하는 소프트웨어나 중 하나를 사용 하 여 검색 인덱스를 정의 하 고 게시 하는 단계를 알아봅니다. 

[인덱서](search-howto-create-indexers.md)를 사용 하지 않는 경우 인덱스를 만들고 인덱스를 채우는 작업은 별개의 작업입니다. 인덱서가 아닌 시나리오의 경우 인덱스를 만든 후 다음 단계는 [데이터 가져오기](search-what-is-data-import.md)입니다. 자세한 배경 정보는 [Azure Cognitive Search에서 인덱스 검색](search-what-is-an-index.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

요청에 대 한 [관리 API 키](search-security-api-keys.md) 를 통해 부여 되는 인덱스를 만들고 로드 하려면 쓰기 권한이 필요 합니다. 또는 Azure Active Directory [역할 기반 액세스 제어 공개 미리 보기](search-security-rbac.md)에 참여 하는 경우 검색 참가자 역할의 구성원으로 요청을 실행할 수 있습니다.

인덱스 생성은 주로 스키마 정의 연습입니다. 하나를 만들기 전에 다음을 수행 해야 합니다.

+ 인덱스에서 검색 가능 하 고, 필터링 가능 하 고, 필터링 가능 하 고, 패싯 가능 정렬할 필드를 명확 하 게 파악 합니다 (이에 대 한 자세한 내용은 [스키마 검사 목록](#schema-checklist)에서 설명).

+ 인덱스의 문서 키 (또는 ID)로 사용할 수 있는 원본 데이터의 고유 식별자입니다.

+ 안정적인 인덱스 위치입니다. 기존 인덱스를 다른 검색 서비스로 이동 하는 것은 기본으로 지원 되지 않습니다. 응용 프로그램 요구 사항을 다시 확인 하 고 기존 검색 서비스의 용량 및 위치를 요구에 맞게 충분 한지 확인 합니다.

마지막으로 모든 서비스 계층에는 만들 수 있는 개체 수에 대 한 [인덱스 제한이](search-limits-quotas-capacity.md#index-limits) 있습니다. 예를 들어 무료 계층을 실험 하는 경우 지정 된 시간에 3 개의 인덱스만 사용할 수 있습니다. 인덱스 자체 내에서는 복잡 한 필드와 컬렉션의 수에 제한이 있습니다.

## <a name="allowed-updates"></a>허용 된 업데이트

[Create Index](/rest/api/searchservice/create-index) 는 검색 서비스에 물리적 데이터 구조 (파일 및 반전 된 인덱스)를 만드는 작업입니다. [업데이트 인덱스](/rest/api/searchservice/update-index) 를 사용 하 여 변경 내용을 적용 하는 기능은 수정 시 해당 물리적 구조를 무효화 하는지 여부에 따라 결정 됩니다. 인덱스에서 필드를 만든 후에는 대부분의 필드 특성을 변경할 수 없습니다.

디자인 프로세스의 변동를 최소화 하기 위해 다음 표에서는 스키마에서 수정 되 고 유연성이 있는 요소에 대해 설명 합니다. 고정 된 요소를 변경 하려면 인덱스를 다시 작성 해야 하지만 유연 요소는 물리적 구현에 영향을 주지 않고 언제 든 지 변경할 수 있습니다. 

| 요소 | 업데이트 가능 여부 |
|---------|-----------------|
| Name | No |
| 키 | No |
| 필드 이름 및 형식 | No |
| 필드 특성 (검색, 필터링, 패싯 가능, 정렬 가능) | No |
| Field attribute (검색할 때) | Yes |
| [분석기](search-analyzers.md) | 인덱스에서 사용자 지정 분석기를 추가 하 고 수정할 수 있습니다. 문자열 필드에 대 한 분석기 할당과 관련 하 여 "searchAnalyzer"만 수정할 수 있습니다. 다른 모든 할당과 수정 작업을 수행 하려면 다시 빌드해야 합니다. |
| [점수 매기기 프로필](index-add-scoring-profiles.md) | Yes |
| [확인기](index-add-suggesters.md) | No |
| [CORS (원본 간 원격 스크립팅)](search-what-is-an-index.md#corsoptions) | 예 |
| [암호화](search-security-manage-encryption-keys.md) | 예 |

> [!NOTE]
> [동의어 맵은](search-synonyms.md) 인덱스 정의의 일부가 아닙니다. 동의어 맵을 수정 해도 실제 검색 인덱스에는 영향을 주지 않습니다.

## <a name="schema-checklist"></a>스키마 검사 목록

이 검사 목록을 사용 하 여 검색 인덱스에 대 한 디자인 결정을 내리는 데 도움을 줍니다.

1. 인덱스 및 필드 이름이 명명 규칙을 준수 하도록 [명명 규칙](/rest/api/searchservice/naming-rules) 을 검토 합니다.

1. [지원 되는 데이터 형식](/rest/api/searchservice/supported-data-types)을 검토 합니다. 데이터 형식은 필드가 사용 되는 방식에 영향을 줍니다. 예를 들어 숫자 콘텐츠는 필터링 할 수 있지만 전체 텍스트는 검색할 수 없습니다. 가장 일반적인 데이터 형식은 `Edm.String` 전체 텍스트 검색 엔진을 사용 하 여 토큰화 되 고 쿼리 되는 검색 가능한 텍스트에 대 한 것입니다.

1. 고유 값을 포함 하는 원본 데이터에서 한 필드를 식별 하 여 인덱스의 키 필드 역할을 할 수 있도록 합니다. 예를 들어 Blob Storage에서 인덱싱하는 경우 저장소 경로가 주로 문서 키로 사용 됩니다. 

   모든 인덱스에는 *문서 키* ("문서 ID" 라고도 함) 역할을 하는 하나의 필드가 필요 합니다. 키는 원본 데이터의 고유 식별자에 매핑되어야 합니다. 특정 검색 문서를 고유 하 게 식별 하는 기능은 검색 인덱스에서 특정 문서를 검색 하 고, 문서 단위 수준에서 선택적 데이터를 처리 하는 데 필요 합니다.

1. 인덱스에서 검색 가능한 콘텐츠를 제공할 데이터 원본의 필드를 식별 합니다. 검색 가능한 콘텐츠에는 전체 텍스트 검색 엔진을 사용 하 여 쿼리 되는 짧고 긴 문자열이 포함 됩니다. 콘텐츠가 자세한 (작은 구 이상 청크) 인 경우 다른 분석기를 시험해 보고 텍스트가 토큰화 되는 방법을 확인 합니다.

   [필드 특성 할당](search-what-is-an-index.md#index-attributes) 은 검색 동작과 검색 서비스의 인덱스에 대 한 물리적 표현을 모두 결정 합니다. 필드를 지정 하는 방법을 결정 하는 것은 많은 고객을 위한 반복적인 프로세스입니다. 반복 속도를 높이려면 쉽게 삭제 하 고 다시 작성할 수 있도록 샘플 데이터부터 시작 합니다.

1. 필터로 사용할 수 있는 원본 필드를 식별 합니다. 숫자 내용과 짧은 텍스트 필드, 특히 반복 되는 값이 있는 경우 적절 한 선택입니다. 필터를 사용할 때는 다음 사항에 주의 하십시오.

   + 필요에 따라 패싯 탐색에서 필터링 가능한 필드를 사용할 수 있습니다.

   + 필터링 가능한 필드는 임의의 순서로 반환 되므로 정렬할 수 있도록 하는 것이 좋습니다.

## <a name="formulate-a-request"></a>요청 공식화

인덱스를 만들 준비가 되 면 여러 가지 방법으로 이동할 수 있습니다. 초기 개발 및 개념 증명 테스트에는 Azure Portal 또는 REST API를 사용하는 것이 좋습니다.

개발하는 동안 다시 빌드하는 계획을 자주 세워야 합니다. 물리적 구조는 서비스에서 만들어지므로 [인덱스를 삭제 하 고 다시 만드는](search-howto-reindex.md) 작업은 많은 수정 작업에 필요 합니다. 보다 빠르게 다시 작성할 수 있도록 데이터 하위 집합을 사용하는 방안을 고려해 볼 수 있습니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/indexer-portal)

포털을 통한 인덱스 디자인은 숫자 필드에서 전체 텍스트 검색 기능을 허용하지 않는 등의 특정 데이터 형식에 대한 요구 사항 및 스키마 규칙을 적용합니다. 포털에는 검색 인덱스를 만드는 두 가지 옵션이 있습니다. 

+ 인덱스 **추가** 는 인덱스 스키마를 지정 하기 위한 포함 편집기입니다.
+ [**데이터 가져오기**](search-import-data-portal.md) 는 마법사입니다.

마법사는 인덱서, 데이터 원본, 로딩 데이터를 만들어 추가 작업을 압축합니다. 원하는 것 보다 많은 경우에는 **인덱스 추가** 또는 다른 방법만 사용 하면 됩니다.

다음 스크린샷은 명령 모음에서 **인덱스 추가** 및 **데이터 가져오기를** 찾을 수 있는 위치를 보여 줍니다. 인덱스를 만든 후 **에는 인덱스 탭에서** 인덱스를 다시 찾을 수 있습니다.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="인덱스 추가 명령" border="true":::

> [!Tip]
> 포털에서 인덱스를 만든 후 JSON 표시를 복사 하 여 응용 프로그램 코드에 추가할 수 있습니다.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

인덱스 만들기 [**(REST)**](/rest/api/searchservice/create-index) 를 사용 하 여 인덱스를 만듭니다. Postman과 Visual Studio Code(Azure Cognitive Search용 확장 사용)는 모두 검색 인덱스 클라이언트로 작동할 수 있습니다. 두 가지 도구를 사용 하 여 검색 서비스에 연결 하 고 요청을 보낼 수 있습니다.

+ [REST 및 Postman을 사용하여 검색 인덱스 만들기](search-get-started-rest.md)
+ [Visual Studio Code 및 Azure Cognitive Search 시작](search-get-started-vs-code.md)

REST API는 필드 특성에 대 한 기본값을 제공 합니다. 예를 들어 모든 Edm. 문자열 필드는 기본적으로 검색할 수 있습니다. 특성은 설명을 위해 아래 전체에 표시 되지만 기본값이 적용 되는 경우에는 특성을 생략할 수 있습니다.

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

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

.NET 용 Azure SDK에는 인덱스를 만들고 업데이트 하는 메서드가 포함 된 [**Searchindexclient**](/dotnet/api/azure.search.documents.indexes.searchindexclient) 가 있습니다.

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

더 많은 예제를 보려면[azure-검색-dotnet-samples/v11//](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)를 참조 하세요.

### <a name="other-sdks"></a>[**기타 Sdk**](#tab/other-sdks)

Cognitive Search의 경우 Azure SDK는 일반적으로 사용 가능한 기능을 구현합니다. 따라서 SDK를 사용하여 검색 인덱스를 만들 수 있습니다. 모두 인덱스를 만들고 업데이트하는 메서드가 있는 **SearchIndexClient** 를 제공합니다.

| Azure SDK | 클라이언트 | 예제 |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [인덱스](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

## <a name="next-steps"></a>다음 단계

다음 링크를 사용 하 여 데이터를 사용 하 여 인덱스를 로드 하는 방법을 익힐 수 있습니다.

+ [데이터 가져오기 개요](search-what-is-data-import.md)

+ [문서 추가, 업데이트 또는 삭제(REST)](/rest/api/searchservice/addupdate-or-delete-documents) 