---
title: '빠른 시작: C#을 사용하여 REST API에 검색 요청 보내기 - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 C#을 통해 Bing Entity Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp, mode-other
ms.openlocfilehash: f82eca2d5a66e7b1eaa22924a50846db6152b4db
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064637"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>빠른 시작: C#을 사용하여 Bing Entity Search REST API에 검색 요청 보내기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Entity Search API를 처음 호출하고 JSON 응답을 봅니다. 이 간단한 C# 애플리케이션은 뉴스 검색 쿼리를 API에 보내고, 응답을 표시합니다. 이 애플리케이션의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs)에 제공됩니다.

이 애플리케이션은 C#으로 작성되었지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.


## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)의 모든 버전.
- 또는 Linux나 MacOS를 사용하는 경우 [Visual Studio Code](https://code.visualstudio.com/) 및 [.NET Core](/dotnet/core/install/macos)를 사용하여 이 빠른 시작을 수행할 수 있습니다.
- [체험판 Azure 계정](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. Visual Studio에서 새 C# 콘솔 솔루션을 만듭니다. 
1. [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 패키지를 추가합니다.
    1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
    2. **NuGet 패키지 관리** 를 선택합니다.
    3. *Newtonsoft.Json* 을 검색하여 선택한 다음, 패키지를 설치합니다.
1. 그런 다음, 주 코드 파일에 다음 네임스페이스를 추가합니다.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. 새 클래스를 만들고 API 엔드포인트, 구독 키 및 검색하려는 쿼리에 대한 변수를 추가합니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.bing.microsoft.com";
            static string path = "/v7.0/search";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>요청 보내기 및 API 응답 받기

1. 클래스 내에서 `Search()`라는 함수를 만듭니다. 이 함수 내에서 새 `HttpClient` 개체를 만들고 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 추가합니다.

2. 호스트와 경로를 결합하여 요청의 URI를 구성합니다. 그런 다음, 시장을 추가하고 쿼리를 URL로 인코딩합니다.

3. `client.GetAsync()`를 기다려서 HTTP 응답을 가져온 다음, `ReadAsStringAsync()`를 기다려서 JSON 응답을 저장합니다.

4. `JsonConvert.DeserializeObject()`를 사용하여 JSON 문자열의 서식을 지정한 후 콘솔에 출력합니다.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. 애플리케이션의 `Main()` 메서드에서 `Search()` 함수를 호출합니다.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>예제 JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API란?](../overview.md )
* [Bing Entity Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
