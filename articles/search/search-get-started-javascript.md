---
title: '빠른 시작: JavaScript에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 JavaScript 빠른 시작에서는 JavaScript를 사용하여 Azure Cognitive Search에서 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 알아봅니다.
author: dereklegenzoff
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/08/2021
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: a05fcecdb79623f5c78ad6dfad9e6fb2706a16c8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050239"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>빠른 시작: JavaScript SDK를 사용하여 Azure Cognitive Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [포털](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST (영문)](search-get-started-rest.md)


[Azure Cognition Search용 JavaScript/Typescript SDK](/javascript/api/overview/azure/search-documents-readme)를 사용하여 JavaScript에서 검색 인덱스를 만들고, 로드하고, 쿼리하는 Node.js 애플리케이션을 만듭니다.

이 문서에서는 애플리케이션을 단계별로 만드는 방법을 보여 줍니다. 또는 [소스 코드와 데이터를 다운로드](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)하고 명령줄에서 애플리케이션을 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에, 다음과 같은 도구 및 서비스를 준비해야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스 [서비스를 만들거나](search-create-service-portal.md) [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ [Node.js](https://nodejs.org) 및 [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) 또는 다른 IDE


## <a name="set-up-your-project"></a>프로젝트 설정

먼저 검색 서비스에 대한 엔드포인트 및 키를 가져옵니다. 그런 다음, 아래에서 설명한 대로 NPM을 사용하여 새 프로젝트를 만듭니다.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>키 및 엔드포인트 복사

서비스를 호출하려면 모든 요청에서 URL 엔드포인트 및 액세스 키가 필요합니다. 첫 번째 단계로 프로젝트에 추가할 API 키와 URL을 찾습니다. 이후 단계에서 클라이언트를 만들 때 두 값을 모두 지정합니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키** 에서 서비스에 대한 모든 권한이 있는 관리자 키를 가져옵니다. 개체를 만들거나 삭제할 때 필요합니다. 상호 교환 가능한 기본 키와 보조 키가 있습니다. 둘 중 아무 키나 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-rest/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

### <a name="create-a-new-npm-project"></a>새 NPM 프로젝트 만들기

먼저 VS Code 및 해당 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal) 또는 다른 터미널(예: Node.js 명령 프롬프트)을 엽니다.

1. 개발 디렉터리를 만들고 `quickstart`라는 이름을 지정합니다.

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. NPM을 실행하여 빈 프로젝트를 초기화합니다. 

    ```cmd
    npm init
    ```
     기본값을 그대로 사용하되, 라이선스는 "MIT"로 설정해야 합니다. 

3. `@azure/search-documents`([Azure Cognition Search용 Javascript/Typscript SDK](/javascript/api/overview/azure/search-documents-readme))를 설치합니다.

    ```cmd
    npm install @azure/search-documents
    ```

4. 서비스 이름 및 API 키와 같은 환경 변수를 가져오는 데 사용되는 `dotenv`를 설치합니다.
    ```cmd
    npm install dotenv
    ```

5. **Package. json** 파일이 다음 JSON과 비슷한지 확인하여 프로젝트 및 해당 종속성을 구성했는지 확인합니다.

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.2.0",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. 검색 서비스 매개 변수를 보관할 **.env** 파일을 만듭니다.

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

`<search-service-name>` 값을 검색 서비스의 이름으로 바꿉니다. `<search-admin-key>`를 앞에서 기록한 키 값으로 바꿉니다. 

### <a name="create-indexjs-file"></a>index.js 파일 만들기

다음으로, 코드를 호스팅하는 주 파일인 **index.js** 파일을 만듭니다.

이 파일의 위쪽에서 `@azure/search-documents` 라이브러리를 가져옵니다.

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

다음으로, 다음과 같이 `dotenv` 패키지에서 **.env** 파일의 매개 변수를 읽도록 요구해야 합니다.

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

가져오기 및 환경 변수가 있으면 main 함수를 정의할 준비가 되었습니다.

대부분의 SDK 기능이 비동기이므로 `async` main 함수를 만듭니다. 또한 발생한 모든 오류를 catch하고 기록하기 위해 `main().catch()`를 main 함수 아래에 포함합니다.

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

이제 인덱스를 만들 준비가 되었습니다.

## <a name="1---create-index"></a>1 - 인덱스 만들기 

**hotels_quickstart_index.json** 파일을 만듭니다.  이 파일은 다음 단계에서 로드할 문서에서 Azure Cognitive Search가 작동하는 방식을 정의합니다. 각 필드는 `name`으로 식별되며 지정된 `type`이 있습니다. 또한 각 필드에는 Azure Cognitive Search가 필드 검색, 필터링, 정렬 및 패싯을 수행할 수 있는지 여부를 지정하는 일련의 인덱스 특성도 있습니다. 대부분의 필드는 단순 데이터 형식이지만 `AddressType`과 같은 일부 형식은 인덱스에서 다양한 데이터 구조를 만들 수 있게 해주는 복합 형식입니다.  [인덱스 만들기(REST)](/rest/api/searchservice/create-index)에 설명된 [지원되는 데이터 형식](/rest/api/searchservice/supported-data-types) 및 인덱스 특성에 대해 자세히 알아볼 수 있습니다. 

**hotels_quickstart_index.json** 에 다음을 추가하거나 [파일을 다운로드](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json)하세요. 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzerName": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzerName": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

인덱스 정의가 있으면 main 함수에서 인덱스 정의에 액세스할 수 있도록 **index.js** 의 위쪽에 있는 **hotels_quickstart_index.json** 을 가져와야 합니다.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

그런 다음, main 함수 내에서 Azure Cognition Search에 대한 인덱스를 만들고 관리하는 데 사용되는 `SearchIndexClient`를 만듭니다. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

다음으로, 인덱스가 이미 있으면 이를 삭제해야 합니다. 이는 테스트/데모 코드에 대한 일반적인 사례입니다.

이 작업은 인덱스 삭제를 시도하는 간단한 함수를 정의하여 수행합니다.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

이 함수를 실행하기 위해 인덱스 정의에서 인덱스 이름을 추출하고 `indexName`을 `indexClient`와 함께 `deleteIndexIfExists()` 함수에 전달합니다.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

그러면 `createIndex()` 메서드를 사용하여 인덱스를 만들 준비가 되었습니다.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>샘플 실행

이 시점에서 샘플을 실행할 준비가 되었습니다. 터미널 창을 사용하여 다음 명령을 실행합니다.

```cmd
node index.js
```

[소스 코드를 다운로드](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11)했지만 필요한 패키지를 아직 설치하지 않은 경우 먼저 `npm install`을 실행합니다.

프로그램에서 수행하는 작업을 설명하는 일련의 메시지가 표시됩니다. 

Azure Portal에서 검색 서비스의 **개요** 를 엽니다. **인덱스** 탭을 선택합니다. 다음과 유사한 출력이 표시됩니다.

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Azure Portal, Search Service 개요, 인덱스 탭의 스크린샷" border="false":::

다음 단계에서는 데이터를 인덱스에 추가합니다. 

## <a name="2---load-documents"></a>2 - 문서 로드 


Azure Cognitive Search에서 문서는 인덱싱에 대한 입력인 동시에 쿼리의 출력인 데이터 구조입니다. 이러한 데이터를 인덱스에 푸시하거나 [인덱서](search-indexer-overview.md)를 사용할 수 있습니다. 이 경우 문서를 인덱스에 프로그래밍 방식으로 푸시합니다.

문서 입력은 데이터베이스의 행, Blob Storage의 Blob 또는 이 샘플처럼 디스크의 JSON 문서일 수 있습니다. 다음 콘텐츠를 사용하여 [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json)을 다운로드하거나 자체 **hotels.json** 파일을 만들 수 있습니다.

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

indexDefinition을 사용하여 수행한 작업과 마찬가지로 main 함수에서 데이터에 액세스할 수 있도록 **index.js** 의 위쪽에 있는 `hotels.json`을 가져와야 합니다.

```javascript
const hotelData = require('./hotels.json');
```


데이터를 검색 인덱스로 인덱싱하려면 이제 `SearchClient`를 만들어야 합니다. `SearchIndexClient`는 인덱스를 만들고 관리하는 데 사용되지만, `SearchClient`는 문서를 업로드하고 인덱스를 쿼리하는 데 사용됩니다.

`SearchClient`를 만드는 방법은 두 가지입니다. 첫 번째 옵션은 `SearchClient`를 처음부터 만드는 것입니다.

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

또는 `SearchIndexClient`의 `getSearchClient()` 메서드를 사용하여 `SearchClient`를 만들 수 있습니다.

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

이제 클라이언트가 정의되었으므로 문서를 검색 인덱스에 업로드합니다. 이 경우 `mergeOrUploadDocuments()` 메서드를 사용합니다. 이는 문서를 업로드하거나 동일한 키가 있는 문서가 이미 있는 경우 기존 문서와 병합합니다.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

`node index.js`를 사용하여 프로그램을 다시 실행합니다. 1단계에서 본 것과 약간 다른 메시지 세트가 표시됩니다. 이번에는 인덱스가 *있으며*, 앱에서 새 인덱스를 만들어 데이터를 이 인덱스에 게시하기 전에 해당 인덱스를 삭제하라는 메시지가 표시됩니다. 

다음 단계에서 쿼리를 실행하기 전에 프로그램에서 1초 동안 대기하도록 함수를 정의합니다. 이 작업은 인덱싱이 완료되고 쿼리에 대한 인덱스에서 문서를 사용할 수 있도록 하기 위해 테스트/데모 용도로만 수행됩니다.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

프로그램에서 1초 동안 대기하도록 하려면 아래와 같이 `sleep` 함수를 호출합니다.

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - 인덱스 검색

인덱스가 만들어지고 문서가 업로드되면 쿼리를 인덱스에 보낼 준비가 되었습니다. 이 섹션에서는 사용할 수 있는 다양한 쿼리 기능을 보여 주기 위해 5개의 다른 쿼리를 검색 인덱스에 보냅니다.

쿼리는 다음과 같이 main 함수에서 호출하는 `sendQueries()` 함수로 작성됩니다.

```javascript
await sendQueries(searchClient);
```

쿼리는 `searchClient`의 `search()` 메서드를 사용하여 보냅니다. 첫 번째 매개 변수는 검색 텍스트이고, 두 번째 매개 변수는 추가 검색 옵션입니다.

첫 번째 쿼리는 모든 항목을 검색하는 것과 동일한 `*`를 검색하고 인덱스에서 세 개의 필드를 선택합니다. 불필요한 데이터를 다시 끌어오면 쿼리의 대기 시간이 늘어날 수 있으므로 필요한 필드만 선택(`select`)하는 것이 좋습니다.

이 쿼리에 대한 `searchOptions`에도 `true`로 설정된 `includeTotalCount`가 있으며, 이 경우 일치하는 결과의 수를 반환합니다.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

아래에 설명된 나머지 쿼리도 `sendQueries()` 함수에 추가해야 합니다. 이 경우 읽기 쉽도록 구분됩니다.

다음 쿼리에서는 `"wifi"`라는 검색 용어를 지정하고, 상태가 `'FL'`인 결과만 반환하는 필터도 포함합니다. 결과는 Hotel의 `Rating`을 기준으로 정렬됩니다.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

다음으로, `searchFields` 매개 변수를 사용하여 검색을 검색 가능한 단일 필드로 제한합니다. 이는 특정 필드의 일치에만 관심이 있는 경우 쿼리를 더 효율적으로 만들 수 있는 좋은 옵션입니다. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

쿼리에 포함하는 또 다른 일반적인 옵션은 `facets`입니다. 패싯을 사용하면 사용자가 필터링할 수 있는 값을 쉽게 알 수 있도록 필터를 UI에 빌드할 수 있습니다.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

최종 쿼리는 `searchClient`의 `getDocument()` 메서드를 사용합니다. 이렇게 하면 해당 키를 통해 문서를 효율적으로 검색할 수 있습니다. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>샘플 실행

`node index.js`를 사용하여 프로그램을 실행합니다. 이제 이전 단계 외에도 쿼리가 보내지고 결과가 콘솔에 기록됩니다.

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 JavaScript 빠른 시작에서는 인덱스를 만들고, 이를 문서와 함께 로드하고, 쿼리를 실행하는 일련의 작업을 수행했습니다. 학습을 계속하려면 제안기(자동 완성 또는 자동 완성 쿼리), 필터 및 패싯 탐색을 시도하기 위한 발판으로 다음 자습서 및 샘플을 사용해 보세요. 

+ [자습서: 웹앱에 검색 추가](tutorial-csharp-overview.md)

+ [샘플: Azure Cognitive Search에 대한 React 프런트 엔드 빌드](https://github.com/dereklegenzoff/azure-search-react-template)
