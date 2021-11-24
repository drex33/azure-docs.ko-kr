---
title: '빠른 시작: Bing Autosuggest REST API 및 Node.js로 검색 쿼리 제안'
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API 및 Node.js를 사용하여 실시간으로 검색어를 빠르게 제안하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: aahi
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: d6a946f6dc43355a2464e842ebe9fd581a54a551
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070415"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-nodejs"></a>빠른 시작: Bing Autosuggest REST API 및 Node.js로 검색 쿼리 제안

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작에 따라 Bing Autosuggest API를 호출하고 JSON 응답을 읽는 방법을 알아봅니다. 이 간단한 Node.js 애플리케이션은 부분 검색 쿼리를 API로 보내고 검색에 대한 제안을 반환합니다. 이 애플리케이션은 JavaScript에서 작성되지만 API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플의 소스 코드는 [GitHub에](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingAutosuggestv7.js) 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.js 6](https://nodejs.org/en/download/) 이상

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 엄격성 및 HTTPS 요구 사항을 설정합니다.
    
    ```javascript
    'use strict';
    
    let https = require ('https');
    ```

2. API 엔드포인트 호스트와 경로, 구독 키, [시장 코드](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes) 및 검색어에 대한 변수를 만듭니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용합니다.

    ```javascript
    // Replace the subscriptionKey string value with your valid subscription key.
    let subscriptionKey = 'enter key here';
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/Suggestions';
    
    let mkt = 'en-US';
    let query = 'sail';
    ```

## <a name="construct-the-search-request-and-query"></a>검색 요청 및 쿼리를 생성합니다.

1. 시장 코드를 `mkt=` 매개 변수에 추가하고 쿼리를 `q=` 매개 변수에 추가하여 쿼리에 대한 매개 변수 문자열을 만듭니다.

    ```javascript 
    let params = '?mkt=' + mkt + '&q=' + query;
    ```

2. `get_suggestions()`라는 함수를 만듭니다. 마지막 단계에서 변수를 사용하여 API 요청에 대한 검색 URL의 형식을 지정합니다. 검색 용어는 API로 보내기 전에 URL로 인코딩해야 합니다.

    ```javascript
    let get_suggestions = function () {
      let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
          'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
      };
    //...
    }
    ```

 1. 같은 함수에서 요청 라이브러리를 사용하여 쿼리를 API에 보냅니다. `response_handler`는 다음 섹션에서 정의됩니다.
    
    ```javascript
        //...
        let req = https.request(request_params, response_handler);
        req.end();
    ```

## <a name="create-a-search-handler"></a>검색 처리기 만들기

1. HTTP 호출, `response`를 매개 변수로 사용하는 `response_handler` 함수를 정의합니다. 이 함수 내에서 다음 단계를 수행합니다.
    
    1. JSON 응답 본문을 포함할 변수를 정의합니다.  

        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. `data` 플래그가 호출될 때 응답 본문을 저장합니다.
        
        ```javascript
        response.on ('data', function (d) {
        body += d;
        });
        ```

    3. `end` 플래그가 신호로 전송되면 사용자는 `JSON.parse()` 및 `JSON.stringify()`를 수행하여 응답을 출력합니다.
    
        ```javascript
        response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
            console.log (body__);
        });
        response.on ('error', function (e) {
            console.log ('Error: ' + e.message);
        });
        ```

2. `get_suggestions()`를 호출하여 Bing Autosuggest API에 요청을 보냅니다.

## <a name="example-json-response"></a>예제 JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorials/autosuggest.md)

- [Bing Autosuggest란?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 참조](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
