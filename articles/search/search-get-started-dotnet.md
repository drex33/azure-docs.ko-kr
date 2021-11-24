---
title: '빠른 시작: .NET에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 C# 빠른 시작에서는 Azure.Search.Documents 클라이언트 라이브러리를 사용하여 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 설명합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/11/2021
ms.custom: devx-track-csharp, mode-other
ms.openlocfilehash: e5a4bc856e3840f9cfba9c1ca9b232afa588735b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050352"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>빠른 시작: Azure.Search.Documents 클라이언트 라이브러리를 사용하여 검색 인덱스 만들기

새로운 [Azure.Search.Documents(버전 11) 클라이언트 라이브러리](/dotnet/api/overview/azure/search.documents-readme)를 사용하여 C#에서 검색 인덱스를 만들고 로드하고 쿼리하는 .NET Core 콘솔 애플리케이션을 만듭니다.

[소스 코드를 다운로드](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)하여 완성된 프로젝트로 시작하거나 이 문서의 단계에 따라 새 프로젝트를 직접 만들 수 있습니다.

> [!NOTE]
> 이전 버전을 찾고 있나요? [Microsoft.Azure.Search v10을 사용하여 검색 인덱스 만들기](search-get-started-dotnet-v10.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에, 다음과 같은 도구 및 서비스를 준비해야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스 [서비스를 만들거나](search-create-service-portal.md) [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) 모든 버전. 샘플 코드는 Visual Studio 2019의 Community 평가판 버전에서 테스트되었습니다.

프로젝트를 설정할 때 [Azure.Search.Documents NuGet 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)를 다운로드합니다.

.NET용 Azure SDK는 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)을 준수합니다. 이는 최소 요구 사항으로 .NET Framework 4.6.1 및 .NET Core 2.0을 의미합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

서비스 연결 정보를 어셈블한 다음, Visual Studio를 시작하여 .NET Core에서 실행할 수 있는 새 콘솔 앱 프로젝트를 만듭니다.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>키 및 엔드포인트 복사

서비스를 호출하려면 모든 요청에서 URL 엔드포인트 및 액세스 키가 필요합니다. 첫 번째 단계로 프로젝트에 추가할 API 키와 URL을 찾습니다. 이후 단계에서 클라이언트를 만들 때 두 값을 모두 지정합니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키** 에서 서비스에 대한 모든 권한이 있는 관리자 키를 가져옵니다. 개체를 만들거나 삭제할 때 필요합니다. 상호 교환 가능한 기본 키와 보조 키가 있습니다. 둘 중 아무 키나 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-rest/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

### <a name="install-the-nuget-package"></a>NuGet 패키지 설치

프로젝트를 만든 후 클라이언트 라이브러리를 추가합니다. [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)는 .NET에서 검색 서비스를 사용하는 데 필요한 모든 API를 제공하는 하나의 클라이언트 라이브러리로 구성됩니다.

1. Visual Studio를 시작하고 .NET Core 콘솔 애플리케이션을 만듭니다.

1. **도구** > **NuGet 패키지 관리자** 에서 **솔루션의 NuGet 패키지 관리...** 를 선택합니다. 

1. **찾아보기** 를 클릭합니다.

1. `Azure.Search.Documents`를 검색하여 11.0 이상 버전을 선택합니다.

1. 오른쪽에서 **설치** 를 클릭하여 프로젝트 및 솔루션에 어셈블리를 추가합니다.

### <a name="create-a-search-client"></a>검색 클라이언트 만들기

1. **Program.cs** 에서 네임스페이스를 `AzureSearch.SDK.Quickstart.v11`로 변경하고 다음 `using` 지시문을 추가합니다.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 다음 두 클라이언트를 만듭니다. [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient)는 인덱스를 만들고, [SearchClient](/dotnet/api/azure.search.documents.searchclient)는 기존 인덱스를 로드하고 쿼리합니다. 둘 다 만들기/삭제 권한 인증을 위한 서비스 엔드포인트와 관리 API 키가 필요합니다.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

이 빠른 시작에서는 호텔 데이터와 함께 로드하고 쿼리를 실행할 호텔 인덱스를 작성합니다. 이 단계에서는 인덱스의 필드를 정의합니다. 각 필드 정의에는 이름, 데이터 형식 및 필드가 사용되는 방식을 결정하는 특성이 포함됩니다.

이 예제에서는 간단한 설명과 가독성을 위해 Azure.Search.Documents 라이브러리의 동기 메서드를 사용합니다. 그러나 프로덕션 시나리오의 경우 비동기 메서드를 사용하여 앱의 확장성 및 응답성을 유지해야 합니다. 예를 들어 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 대신 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync)를 사용해야 합니다.

1. 다음과 같이 프로젝트에 빈 클래스 정의를 추가합니다. **Hotel.cs**

1. **Hotel.cs** 에 다음 코드를 복사하여 호텔 문서의 구조를 정의합니다. 필드의 특성은 필드가 애플리케이션에서 사용되는 방식을 결정합니다. 예를 들어 `IsFilterable` 특성은 필터 식을 지원하는 모든 필드에 할당해야 합니다.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   Azure.Search.Documents 클라이언트 라이브러리에서 [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) 및 [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)를 사용하여 필드 정의를 간소화할 수 있습니다. 둘 다 [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield)의 파생물이며 잠재적으로 코드를 단순화할 수 있습니다.

   + `SimpleField`는 모든 데이터 형식일 수 있으며, 항상 검색할 수 없지만(전체 텍스트 검색 쿼리에서 무시됨) 조회할 수는 있습니다(숨겨지지 않음). 다른 특성은 기본적으로 꺼져 있지만 사용하도록 설정할 수 있습니다. 필터, 패싯 또는 점수 매기기 프로필에만 사용되는 문서 ID 또는 필드에는 `SimpleField`를 사용할 수 있습니다. 그렇다면 문서 ID에 대한 `IsKey = true`와 같이 시나리오에 필요한 특성을 적용해야 합니다. 자세한 내용은 소스 코드의 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)를 참조하세요.

   + `SearchableField`는 문자열이어야 하며, 항상 검색할 수 있고 조회할 수 있습니다. 다른 특성은 기본적으로 꺼져 있지만 사용하도록 설정할 수 있습니다. 이 필드 형식은 검색할 수 있으므로 동의어와 분석기 속성의 전체 보충을 지원합니다. 자세한 내용은 소스 코드의 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)를 참조하세요.

   기본 `SearchField` API를 사용하든지 도우미 모델 중 하나를 사용하든지 간에 필터, 패싯 및 정렬 특성을 명시적으로 사용하도록 설정해야 합니다. 예를 들어 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 및 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable)은 위의 샘플처럼 명시적으로 특성을 지정해야 합니다. 

1. 다음과 같이 프로젝트에 두 번째 빈 클래스 정의를 추가합니다. **Address.cs**.  다음 코드를 클래스에 복사합니다.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. 두 개 이상의 클래스를 만듭니다. ToString() 재정의에 대한 **Hotel.Methods.cs** 및 **Address.Methods.cs**. 이러한 클래스는 콘솔 출력에서 검색 결과를 렌더링하는 데 사용됩니다.  이러한 클래스의 콘텐츠는 이 문서에서 제공되지 않지만 [GitHub의 파일](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11)에서 코드를 복사할 수 있습니다.

1. **Program.cs** 에서 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 개체를 만든 다음, [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 메서드를 호출하여 검색 서비스에서 인덱스를 표시합니다. 인덱스에는 지정된 필드에서 자동 완성을 사용하도록 설정하는 [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester)도 포함되어 있습니다.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

Azure Cognitive Search는 서비스에 저장된 콘텐츠를 검색합니다. 이 단계에서는 방금 만든 호텔 인덱스를 따르는 JSON 문서를 로드합니다.

Azure Cognitive Search에서 검색 문서는 인덱싱에 대한 입력과 쿼리의 출력 모두에 해당하는 데이터 구조입니다. 외부 데이터 소스에서 가져온, 문서 입력은 데이터베이스의 행, Blob Storage의 Blob 또는 디스크의 JSON 문서일 수 있습니다. 이 예에서는 손쉬운 방법을 사용하여 4개 호텔에 대한 JSON 문서를 코드 자체에 포함합니다. 

문서를 업로드할 때 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 개체를 사용해야 합니다. `IndexDocumentsBatch` 개체에는 [Actions](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)의 컬렉션이 포함되며, 각 컬렉션에는 수행할 작업([upload, merge, delete 및 mergeOrUpload](search-what-is-data-import.md#indexing-actions))을 Azure Cognitive Search에 알려주는 속성과 문서가 포함됩니다.

1. **Program.cs** 에서 문서 및 인덱스 작업의 배열을 만든 다음, 배열을 `IndexDocumentsBatch`에 전달합니다. 아래 문서는 호텔 클래스에서 정의한 대로 hotels-quickstart 인덱스를 준수합니다.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 개체를 초기화 한 후에는 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 개체에서 [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments)를 호출하여 이 개체를 인덱스에 전송할 수 있습니다.

1. Main()에 다음 줄을 추가합니다. 문서 로드는 SearchClient를 사용하여 수행되지만 작업에는 일반적으로 SearchIndexClient와 관련된 서비스에 대한 관리자 권한도 있어야 합니다. 이 작업을 설정하는 한 가지 방법은 SearchIndexClient(이 예제에서는 adminClient)를 통해 SearchClient를 가져오는 것입니다.

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. 이 앱은 모든 명령을 순차적으로 실행하는 콘솔 앱이므로 인덱싱과 쿼리 사이에 2초 대기 시간을 추가합니다.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2초 지연은 비동기식 인덱싱을 보정합니다. 따라서 쿼리가 실행되기 전에 모든 문서를 인덱싱할 수 있습니다. 지연 시 코딩은 일반적으로 데모, 테스트, 샘플 애플리케이션에서만 필요합니다.

## <a name="3---search-an-index"></a>3 - 인덱스 검색

첫 번째 문서의 인덱싱이 완료되는 즉시 쿼리 결과를 얻을 수 있지만 인덱스에 대한 실제 테스트는 모든 문서의 인덱싱이 완료될 때까지 기다려야 합니다.

이 섹션에서는 쿼리 논리 및 결과라는 두 가지 기능을 추가합니다. 쿼리에는 [Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드를 사용합니다. 이 메서드는 검색 텍스트(쿼리 문자열)뿐 아니라 다른 [옵션](/dotnet/api/azure.search.documents.searchoptions)을 사용합니다.

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) 클래스는 결과를 나타냅니다.

1. **Program.cs** 에서 검색 결과를 콘솔에 출력하는 **WriteDocuments** 메서드를 만듭니다.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. 쿼리를 실행하고 결과를 반환하는 **RunQueries** 메서드를 만듭니다. 결과는 Hotel 개체입니다. 이 샘플에서는 메서드 서명과 첫 번째 쿼리를 보여줍니다. 이 쿼리는 문서에서 선택한 필드를 사용하여 결과를 작성할 수 있도록 하는 Select 매개 변수를 보여줍니다.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. 두 번째 쿼리에서는 용어를 검색하고 등급이 4보다 큰 문서를 선택하는 필터를 추가한 다음, 등급별로 내림차순으로 정렬합니다. 필터는 인덱스의 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 필드를 통해 평가되는 부울 식입니다. 필터는 포함 또는 제외 값을 쿼리합니다. 따라서 필터 쿼리와 관련된 관련성 점수가 없습니다. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. 세 번째 쿼리는 전체 텍스트 검색 작업의 범위를 특정 필드로 지정하는 데 사용되는 searchFields를 보여줍니다.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. 네 번째 쿼리는 패싯 탐색 구조를 구조화하는 데 사용할 수 있는 패싯을 보여줍니다. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. 다섯 번째 쿼리에서 특정 문서를 반환합니다. 문서 조회는 결과 집합의 OnClick 이벤트에 대한 일반적인 응답입니다.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. 마지막 쿼리는 인덱스에서 정의한 제안기와 연결된 sourceFields에서 가능한 두 개의 일치 항목으로 확인되는 "sa"의 부분 사용자 입력을 시뮬레이션하는 자동 구문을 보여줍니다.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. **RunQueries** 를 Main()에 추가합니다.

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

이전 쿼리는 전체 텍스트 검색, 필터 및 자동 완성과 같은 [쿼리에서 용어를 일치시키는 여러 방법](search-query-overview.md#types-of-queries)을 보여줍니다.

전체 텍스트 검색 및 필터는 [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드를 사용하여 수행됩니다. 검색 쿼리는 `searchText` 문자열로 전달할 수 있는 반면, 필터 식은 [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) 클래스의 [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) 속성으로 전달할 수 있습니다. 검색하지 않고 필터링하려면 [Search](/dotnet/api/azure.search.documents.searchclient.search) 메서드의 `searchText` 매개 변수에 대한 `"*"`를 전달합니다. 필터링하지 않고 검색하려면 `Filter` 속성을 설정하지 않고 그대로 두거나 `SearchOptions` 인스턴스에 전달하지 않아야 합니다.

## <a name="run-the-program"></a>프로그램 실행

F5 키를 눌러서 애플리케이션을 다시 빌드하고 프로그램 전체를 실행합니다. 

출력에는 쿼리 정보 및 결과가 추가된 [Console.WriteLine](/dotnet/api/system.console.writeline)의 메시지가 포함됩니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 C# 빠른 시작에서는 인덱스를 만들고, 문서와 함께 로드하고, 쿼리를 실행하는 일단의 작업을 수행했습니다. 여러 단계에서, 가독성과 이해를 돕기 위해 손쉬운 방법을 사용하여 코드를 간소화했습니다. 이제 기본 개념에 익숙해졌으므로 다음 자습서를 통해 웹앱의 컨텍스트에서 Cognitive Search API를 호출해 보세요.

> [!div class="nextstepaction"]
> [자습서: 웹앱에 검색 추가](tutorial-csharp-overview.md)
