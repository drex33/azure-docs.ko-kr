---
title: '빠른 시작: Python에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: Python, Jupyter Notebook 및 Python용 Azure.Documents.Search 클라이언트 라이브러리를 사용하여 검색 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/11/2021
ms.custom: devx-track-python, mode-other
ms.openlocfilehash: 193309ea9064d46878af6aaf42752a4c5241987e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050193"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>빠른 시작: Jupyter Notebook을 사용하여 Python에서 Azure Cognitive Search 인덱스 만들기

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell(REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST (영문)](search-get-started-rest.md)
> * [포털](search-get-started-portal.md)
>

Python 및 Python용 Azure SDK의 [azure-search-documents 라이브러리](/python/api/overview/azure/search-documents-readme)를 사용하여 Azure Cognitive Search 인덱스를 만들고, 로드하고, 쿼리하는 Notebook을 빌드합니다. 이 문서에서는 Notebook을 단계별로 빌드하는 방법에 대해 설명합니다. 또는 [완성된 Jupyter Python Notebook을 다운로드하여 실행](https://github.com/Azure-Samples/azure-search-python-samples)할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에 필요한 서비스와 도구는 다음과 같습니다.

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) - Python 3.x 및 Jupyter Notebook 제공

* [azure-search-documents 패키지](https://pypi.org/project/azure-search-documents/)

* [검색 서비스를 생성](search-create-service-portal.md)하거나 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 계층을 사용할 수 있습니다. 

## <a name="copy-a-key-and-url"></a>키 및 URL 복사

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키** 에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-rest/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-cognitive-search"></a>Azure Cognitive Search에 연결

이 작업에서는 Jupyter Notebook을 시작하고, Azure Cognitive Search에 연결할 수 있는지 확인합니다. 이 작업은 서비스에서 인덱스 목록을 요청하여 수행합니다. Anaconda3이 있는 Windows에서 Anaconda Navigator를 사용하여 Notebook을 시작할 수 있습니다.

1. 새 Python3 Notebook을 만듭니다.

1. 첫 번째 셀에서 [azure-search-documents](/python/api/azure-search-documents)를 포함하여 Python용 Azure SDK에서 라이브러리를 로드합니다.

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. 두 번째 셀에서 모든 요청에 상수로 사용할 요청 요소를 입력합니다. 이전 단계에서 복사한 검색 서비스 이름, 관리 API 키 및 쿼리 API 키를 제공합니다. 또한 이 셀은 특정 작업에 사용할 클라이언트를 설정합니다. [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient)는 인덱스를 만들고, [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient)는 인덱스를 쿼리합니다.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. 세 번째 셀에서 delete_index 작업을 실행하여 기존 *hotels-quickstart* 인덱스의 서비스를 지웁니다. 인덱스를 삭제하면 동일한 이름의 다른 *hotels-quickstart* 인덱스를 만들 수 있습니다.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. 각 단계를 실행합니다.

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

인덱스의 필수 요소에는 name, fields 컬렉션 및 key가 포함됩니다. fields 컬렉션은 데이터를 로드하고 결과를 반환하는 데 사용되는 논리적 *문서 검색* 의 구조를 정의합니다. 

각 필드에는 필드가 사용되는 방법(예: 검색 결과에서 전체 텍스트 검색 가능, 필터링 가능 또는 조회 가능 여부)을 결정하는 이름, 형식 및 속성이 있습니다. 인덱스 내에서 `Edm.String` 형식의 필드 중 하나는 문서 ID에 대한 *key* 로 지정해야 합니다.

이 인덱스의 이름은 "hotels-quickstart"이며 아래에 표시된 필드 정의가 있습니다. 다른 연습에서 사용되는 더 큰 [Hotels 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)의 하위 세트입니다. 이 빠른 시작에서는 간단히 하기 위해 다듬었습니다.

1. 다음 셀에서 다음 예제를 셀에 붙여넣어 스키마를 제공합니다.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. 다른 셀에서 요청을 작성합니다. 이 create_index 요청은 검색 서비스의 indexes 컬렉션을 대상으로 하고, 이전 셀에서 제공한 인덱스 스키마에 기반한 [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex)를 만듭니다.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. 각 단계를 실행합니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 로드하려면 작업 유형(업로드, 병합 후 업로드 등)에 대한 [인덱스 작업](/python/api/azure-search-documents/azure.search.documents.models.indexaction)을 사용하여 documents 컬렉션을 만듭니다. 문서는 GitHub의 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON)에서 시작됩니다.

1. 새 셀에서 인덱스 스키마를 준수하는 4개의 문서를 제공합니다. 각 문서에 대한 업로드 작업을 지정합니다.

    ```python
    documents = [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    ```  

1. 다른 셀에서 요청을 작성합니다. 이 upload_documents 요청은 hotels-quickstart 인덱스의 docs 컬렉션을 대상으로 하며 이전 단계에서 제공한 문서를 Cognitive Search 인덱스에 푸시합니다.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. 각 단계를 실행하여 문서를 검색 서비스의 인덱스로 푸시합니다.

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계에서는 [search.client 클래스](/python/api/azure-search-documents/azure.search.documents.searchclient)의 **검색** 메서드를 사용하여 인덱스를 쿼리하는 방법을 보여줍니다.

1. 다음 단계에서는 빈 검색(`search=*`)을 실행하여 순위가 없는 임의 문서 목록(검색 점수 = 1.0)을 반환합니다. 조건이 없으므로 모든 문서가 결과에 포함됩니다. 이 쿼리는 각 문서의 필드 중 두 개만 출력합니다. 또한 모든 문서의 수(4)를 결과에 가져오는 `include_total_count=True`를 추가합니다.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. 다음 쿼리는 전체 용어를 검색 식("wifi")에 추가합니다. 이 쿼리는 `select` 문의 해당 필드만 결과에 포함되도록 지정합니다. 반환되는 필드를 제한하면 유선을 통해 다시 보내지는 데이터의 양이 최소화되고 검색 대기 시간이 줄어듭니다.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. 다음으로, 필터 식을 적용하여 4보다 큰 등급의 호텔만 내림차순으로 정렬하여 반환합니다.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. 단일 필드에 일치하는 쿼리 범위를 지정하는 `search_fields`를 추가합니다.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. 패싯은 패싯 탐색 구조를 구성하는 데 사용할 수 있는 레이블입니다. 이 쿼리는 Category(범주)에 대한 패싯 및 개수를 반환합니다.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. 다음 예제에서는 해당 키에 기반한 특정 문서를 조회합니다. 일반적으로 사용자가 검색 결과에서 문서를 클릭하면 문서를 반환하려고 합니다.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. 다음 예제에서는 autocomplete(자동 완성) 함수를 사용합니다. 일반적으로 사용자가 검색 상자에 입력할 때 잠재적으로 일치하는 항목을 자동으로 완성하는 데 도움이 되는 검색 상자에서 사용됩니다.

   인덱스를 만들 때 "sg"라는 suggester(제안기)도 요청의 일부로 만들어졌습니다. 제안기 정의는 잠재적으로 제안기 요청과 일치하는 항목을 찾는 데 사용할 수 있는 필드를 지정합니다. 다음 예제에서 이러한 필드는 'Tags', 'Address/City', 'Address/Country'입니다. 자동 완성을 시뮬레이션하려면 "sa" 문자를 부분 문자열로 전달합니다. [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient)의 자동 완성 메서드는 잠재적으로 일치하는 용어 항목을 다시 보냅니다.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기에서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 JavaScript 빠른 시작에서는 인덱스를 만들고, 이를 문서와 함께 로드하고, 쿼리를 실행하는 일련의 작업을 수행했습니다. 학습을 계속하려면 다음 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [자습서: 웹앱에 검색 추가](tutorial-python-overview.md)
