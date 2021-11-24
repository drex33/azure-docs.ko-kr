---
title: '빠른 시작: Node.js를 사용하여 Bing Custom Search 엔드포인트 호출 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Node.js로 Bing Custom Search 인스턴스의 검색 결과를 요청할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 99b7fa29265b9c4fa2559f01a8888d0905d7aac0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051259"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Bing Custom Search 엔드포인트 호출

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Custom Search 인스턴스의 검색 결과를 요청하는 방법에 대해 알아봅니다. 이 애플리케이션은 JavaScript로 작성되지만 Bing Custom Search API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js)에 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Bing Custom Search 인스턴스 자세한 내용은 [빠른 시작: 첫 번째 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.

- [Node.js JavaScript 런타임](https://www.nodejs.org/).

- [JavaScript 요청 라이브러리](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

- 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 요청 라이브러리에 대해 `require()` 문을 추가합니다. 구독 키, 사용자 지정 구성 ID 및 검색어에 대한 변수를 만듭니다.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>검색 요청 보내고 받기 

1. 요청에서 전송되는 정보를 저장할 변수를 만듭니다. 검색어를 `q=` 쿼리 매개 변수에 추가하고 검색 인스턴스의 사용자 지정 구성 ID를 `customconfig=` 매개 변수에 추가하여 요청 URL을 구성합니다. 매개 변수를 앰퍼샌드(`&`)로 분리합니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. JavaScript 요청 라이브러리를 사용하여 Bing Custom Search 인스턴스에 검색 요청을 보내고 이름, URL 및 웹 페이지가 마지막으로 크롤링된 날짜를 비롯한 결과에 대한 정보를 출력합니다.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Search 웹앱 빌드](./tutorials/custom-search-web-page.md)
