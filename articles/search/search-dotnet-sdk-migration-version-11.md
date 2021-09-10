---
title: .NET SDK 버전 11로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Cognitive Search .NET SDK 버전 11로 코드를 마이그레이션합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: bb596e993981275bbf61efd2542dbf1b566e1a3a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003893"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Azure Cognitive Search .NET SDK 버전 11로 업그레이드

검색 솔루션이 [ **.NET용 Azure SDK**](/dotnet/azure/)를 기반으로 구축된 경우 이 문서는 이전 버전의 [**Microsoft.Azure.Search에서 코드를 마이그레이션하는 데 도움이 됩니다.**](/dotnet/api/overview/azure/search/client10)에서 버전 11, 새로운 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) 클라이언트 라이브러리. 버전 11은 완전히 다시 디자인된 클라이언트 라이브러리로, Azure SDK 개발 팀에서 릴리스했습니다(이전 버전은 Azure Cognitive Search 개발 팀에서 생성함). 

[한 가지 예외](#WhatsNew)를 제외하고 버전 10의 모든 기능은 버전 11에서 구현됩니다. 두 표준 간의 차이점은 다음과 같습니다.

+ 4개가 아닌 1개의 패키지(**Azure.Search.Documents**)
+ 2개 대신 3개의 클라이언트: SearchClient, SearchIndexClient, SearchIndexerClient
+ API 범위 전반의 명명 차이 및 일부 작업을 간소화하는 작은 구조적 차이

클라이언트 라이브러리의 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)에는 항목별 업데이트 목록이 있습니다.

Cognitive Search 제품 문서의 모든 C# 코드 샘플 및 조각은 새로운 **Azure.Search.Documents** 클라이언트 라이브러리를 사용하도록 수정되었습니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유

업그레이드의 이점은 다음과 같이 요약되어 있습니다.

+ 새 기능은 **Azure.Search.Documents** 에만 추가됩니다. 이전 버전인 Microsoft.Azure.Search는 이제 레거시 클라이언트입니다. 레거시 라이브러리에 대한 업데이트는 우선 순위가 높은 버그 수정으로만 제한됩니다.

+ 다른 Azure 클라이언트 라이브러리와의 일관성. **Azure.Search.Documents** 는 [Azure.Core](/dotnet/api/azure.core) 및 [System.Text.Json](/dotnet/api/system.text.json)에 대한 종속성을 사용하며 클라이언트 연결 및 승인과 같은 일반적인 작업에 대한 기존 접근 방식을 따릅니다.

## <a name="package-comparison"></a>패키지 비교

버전 11은 패키지 관리를 통합하고 간소화하므로 관리할 수가 줄어듭니다.

| 버전 10 이전 | 버전 11 |
|------------------------|------------|
| [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) </br>[Microsoft.Azure.Search.Service](https://www.nuget.org/packages/Microsoft.Azure.Search.Service/) </br>[Microsoft.Azure.Search.Data](https://www.nuget.org/packages/Microsoft.Azure.Search.Data/) </br>[Microsoft.Azure.Search.Common](https://www.nuget.org/packages/Microsoft.Azure.Search.Common/)  | [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/) |

## <a name="client-comparison"></a>클라이언트 비교

해당하는 경우 다음 표는 두 버전 간에 클라이언트 라이브러리를 매핑합니다.

|  클라이언트 작업 | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| 인덱스의 문서 컬렉션을 대상으로 합니다(쿼리 및 데이터 가져오기). | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| 인덱스 관련 개체(인덱스, 분석기, 동의어 맵)를 대상으로 합니다. | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 인덱서 관련 개체(인덱서, 데이터 원본, 기술 집합)를 대상으로 합니다. | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient(**신규**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Caution]
> SearchIndexClient는 두 버전 모두에 존재하지만 다른 작업을 대상으로 합니다. 버전 10에서 SearchIndexClient는 인덱스 및 기타 개체를 생성합니다. 버전 11에서 SearchIndexClient는 쿼리 및 데이터 수집 API를 사용하여 문서 컬렉션을 대상으로 기존 인덱스와 함께 작동합니다. 코드를 업데이트할 때 혼동을 방지하려면 클라이언트 참조가 업데이트되는 순서에 주의해야 합니다. [업그레이드 단계](#UpgradeSteps)의 시퀀스를 따르면 모든 문자열 대체 문제를 완화할 수 있습니다.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>명명 및 기타 API 차이점

이전에 언급되었지만 여기에서 생략된 클라이언트 차이점 외에도 다양한 API의 이름이 변경되었으며 일부는 다시 설계되었습니다. 클래스 이름 차이는 아래에 요약되어 있습니다. 이 목록은 전체 목록은 아니지만 작업별로 API 변경 사항을 그룹화하기 때문에 특정 코드 블록의 수정 시에 유용하게 쓰일 수 있습니다. API 업데이트의 항목별 목록은 GitHub에서 `Azure.Search.Documents`의 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)를 참조하세요.

### <a name="authentication-and-encryption"></a>인증 및 암호화

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| EncryptionKey(API 참조에서 문서화되지 않았습니다. 이 API에 대한 지원이 v10에서 일반 공급으로 전환되었지만 [미리 보기 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview)에서만 사용할 수 있었습니다. | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>인덱스, 분석기, 동의어 맵

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [필드](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [분석기](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)(또한 `AnalyzerName`이 `LexicalAnalyzerName`으로 변경됨) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer)(또한 `StandardTokenizerV2`가 `LuceneStandardTokenizerV2`로 변경됨) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer)(또한 `TokenizerName`이 `LexicalTokenizerName`으로 변경됨) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 없음 `Format`에 대한 참조를 제거합니다. |

다음과 같이 필드 정의가 간소화되었습니다. [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield)가 필드 정의를 만드는 새 API입니다.

### <a name="indexers-datasources-skillsets"></a>인덱서, 데이터 원본, 기술 세트

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [인덱서](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [기술](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [기술 집합](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>데이터 가져오기

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-requests-and-responses"></a>쿼리 요청 및 응답

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [DocumentsOperationsExtensions.SearchAsync](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.searchasync) | [SearchClient.SearchAsync](/dotnet/api/azure.search.documents.searchclient.searchasync) |
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) 또는 [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), 결과가 단일 문서인지 문서 여러 개인지에 따라 이름이 달라집니다. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |
| [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters) |  [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) |
| [SearchParameters.Filter](/dotnet/api/microsoft.azure.search.models.searchparameters.filter) |  [SearchFilter](/dotnet/api/azure.search.documents.searchfilter)(OData 필터 표현식을 구성하기 위한 새 클래스) |

### <a name="json-serialization"></a>JSON serialization

기본적으로 Azure SDK는 JSON serialization에 [System.Text.Json](/dotnet/api/system.text.json)을 사용하며, 해당 API의 기능에 의존하여 네이티브 [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) 클래스(신규 라이브러리에 상응하는 항목이 없음)를 통해 이전에 구현된 텍스트 변환을 처리합니다.

속성 이름을 camelCase로 직렬화하려는 경우 [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute)([이 예제](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)와 유사함)를 사용할 수 있습니다.

또는 [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions)에 제공된 [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy)를 설정할 수 있습니다. [Microsoft.Azure.Core.Spatial 추가 정보](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents)에서 가져온 다음과 같은 System.Text.Json 코드 예제에서는 모든 속성에 특성을 부여하지 않고도 camelCase를 사용하는 방법을 보여 줍니다.

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

JSON serialization에 Newtonsoft.Json을 사용하고 있다면 유사한 특성을 사용하거나 [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm)의 속성을 사용하여 전체 명명 정책을 전달할 수 있습니다. 위의 상황에 해당하는 예제는 Newtonsoft.Json 추가 정보에 있는 [문서 역직렬화 예제](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)를 참조하세요.

<a name="WhatsNew"></a>

## <a name="inside-v11"></a>V11 내부

Azure Cognitive Search 클라이언트 라이브러리의 각 버전은 해당 버전의 REST API를 대상으로 합니다. REST API는 서비스의 기본으로 간주되며, 개별 SDK가 REST API 버전을 래핑합니다. .NET 개발자로서 특정 개체 또는 작업에 대해 더 자세히 알아보려면 더 자세한 [REST API 문서](/rest/api/searchservice/)를 검토하는 것이 좋습니다. 버전 11은 [2020-06-30 검색 서비스](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)를 대상으로 합니다. 

버전 11.0은 다음 개체 및 작업을 완벽하게 지원합니다.

+ 인덱스 만들기 및 관리
+ 동의어 맵 만들기 및 관리
+ 인덱서 만들기 및 관리
+ 인덱서 데이터 원본 만들기 및 관리
+ 기술세트 생성 및 관리
+ 모든 쿼리 유형 및 구문

버전 11.1 추가 사항([변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1110-2020-08-11) 세부정보):

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder)(11.1에 추가)
+ 사용자 지정 serialization을 지원하기 위한 [직렬 변환기 속성](/dotnet/api/azure.search.documents.searchclientoptions.serializer)(11.1에 추가)

버전 11.2 추가 사항([변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1120-2021-02-10) 세부정보):

+ [EncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchindexer.encryptionkey) 속성 추가 인덱서, 데이터 소스 및 기술 세트
+ [IndexingParameters.IndexingParametersConfiguration](/dotnet/api/azure.search.documents.indexes.models.indexingparametersconfiguration) 속성 지원
+ [지리정보 유형](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype.geographypoint)은 [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder.build)에서 기본적으로 지원됩니다. [SearchFilter](/dotnet/api/azure.search.documents.searchfilter)는 명시적 어셈블리 종속성 없이 Microsoft.Spatial의 기하학적 유형을 인코딩할 수 있습니다.

  [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/)에 대한 종속성을 계속해서 명시적으로 선언할 수도 있습니다. 이 기술의 예는 [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) 및 [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)에 사용할 수 있습니다.

현재 모든 버전의 Azure.Search.Documents에서 지원되지 않습니다.

+ [지식 저장소](knowledge-store-concept-intro.md)

## <a name="before-upgrading"></a>업그레이드 이전

+ Azure.Search.Documents 패키지를 사용하도록 [빠른 시작](search-get-started-dotnet.md), 자습서 및 [C# 샘플](samples-dotnet.md)이 업데이트되었습니다. 마이그레이션 연습을 시작하기 전에 기존 샘플과 연습을 검토하여 새 API에 대해 알아보는 것이 좋습니다.

+ [Azure.Search.Documents 사용 방법](search-howto-dotnet-sdk.md)에서는 가장 일반적으로 사용되는 API를 소개합니다. Cognitive Search에 대한 지식이 풍부한 사용자라도 마이그레이션의 소개로 새 라이브러리에 대한 이 소개를 검토할 수 있습니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계

다음 단계에서는 특히 클라이언트 참조와 관련하여 첫 번째 필수 작업 집합을 탐색함으로써 코드 마이그레이션을 시작합니다.

1. Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 “NuGet 패키지 관리...”를 선택하여 [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)를 설치합니다.

1. Microsoft.Azure.Search에 대한 using 지시문을 다음으로 바꿉니다.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. JSON serialization이 필요한 클래스의 경우 `using Newtonsoft.Json`을 `using System.Text.Json.Serialization`으로 바꿉니다.

1. 클라이언트 인증 코드를 수정합니다. 이전 버전에서는 클라이언트 개체의 속성을 사용하여 API 키(예: [SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 속성)를 설정합니다. 현재 버전에서는 [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) 클래스를 사용하여 키를 자격 증명으로 전달합니다. 따라서 필요한 경우 새 클라이언트 개체를 만들지 않고도 API 키를 업데이트할 수 있습니다.

   클라이언트 속성은 `Endpoint`, `ServiceName`, `IndexName`(해당하는 경우)으로 간소화되었습니다. 다음 예제에서는 시스템 [Uri](/dotnet/api/system.uri) 클래스를 사용하여 키 값에서 읽을 엔드포인트와 [환경](/dotnet/api/system.environment) 클래스를 제공합니다.

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. 인덱서 관련 개체에 대한 새 클라이언트 참조를 추가합니다. 인덱서, 데이터 원본 또는 기술 세트를 사용하는 경우 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)에 대한 클라이언트 참조를 변경합니다. 이 클라이언트는 버전 11에서 새로 추가되었으며 선행 작업이 필요 없습니다.

1. 컬렉션과 목록을 수정합니다. 새 SDK에서는 목록에 null 값이 포함된 경우의 다운스트림 문제를 방지하기 위해 모든 목록이 읽기 전용입니다. 코드를 변경하는 것은 목록에 항목을 추가하는 것입니다. 예를 들어 Select 속성에 문자열을 할당하는 대신 다음과 같이 추가합니다.

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select, Facets, SearchFields, SourceFields, ScoringParameters, OrderBy는 모두 이제 재구성해야 하는 목록입니다.

1. 쿼리 및 데이터 가져오기에 대한 클라이언트 참조를 업데이트합니다. [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient)의 인스턴스를 [SearchClient](/dotnet/api/azure.search.documents.searchclient)로 변경해야 합니다. 이름 혼동을 방지하려면 다음 단계로 진행하기 전에 모든 인스턴스를 확인해야 합니다.

1. 인덱스, 동의어 맵, 분석기 개체에 대한 클라이언트 참조를 업데이트합니다. [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient)의 인스턴스를 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient)로 변경해야 합니다. 

1. 코드의 나머지 부분에 대해 새 라이브러리의 API를 사용하도록 클래스, 메서드, 속성을 업데이트합니다. 시작 위치는 [명명 차이](#naming-differences) 섹션이지만 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)를 검토할 수도 있습니다.

   동등한 API를 찾는 데 문제가 있는 경우 문서를 개선하거나 문제를 조사할 수 있도록 [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues)에 이슈를 기재하는 것이 좋습니다.

1. 솔루션을 다시 빌드합니다. 빌드 오류 또는 경고를 모두 수정한 후에는 [새로운 기능](#WhatsNew)을 활용하도록 애플리케이션을 추가로 변경할 수 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>주요 변경 내용

라이브러리와 API가 대폭 변경된 경우 버전 11로의 업그레이드는 사소한 변화가 아니며 코드가 더 이상 버전 10 이하의 이전 버전과 호환되지 않는다는 점에서 호환성이 손상되는 변경이 발생합니다. 차이점에 대해 철저하게 검토하려면 `Azure.Search.Documents`의 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)를 참조하세요.

서비스 버전 업데이트의 측면에서 버전 11의 코드 변경 내용이 단순히 API의 리팩터링뿐 아니라 기존 기능에 연관되는 경우 다음과 같은 동작 변경을 확인할 수 있습니다.

+ [BM25 순위 지정 알고리즘](index-ranking-similarity.md)은 이전 순위 지정 알고리즘을 최신 기술로 대체합니다. 새 서비스에서 자동으로 이 알고리즘을 사용합니다. 기존 서비스의 경우 새 알고리즘을 사용하도록 매개 변수를 설정해야 합니다.

+ 이 버전에서는 null 값에 대해 [정렬된 결과](search-query-odata-orderby.md)가 변경되었고, 정렬이 `asc`이면 null 값이 처음에 표시되고 정렬이 `desc`이면 마지막에 표시됩니다. null 값을 정렬하는 방법을 처리하는 코드를 작성한 경우, 더 필요하지 않으면 해당 코드를 검토하고 잠재적으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [C# .NET 응용 프로그램에서 Azure.Search.Documents를 사용하는 방법](search-howto-dotnet-sdk.md)
+ [자습서: 웹앱에 검색 추가](tutorial-csharp-overview.md)
+ [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 샘플](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API 참조](/dotnet/api/overview/azure/search.documents-readme)
