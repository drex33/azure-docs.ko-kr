---
title: '빠른 시작: Bing Image Search REST API 및 Node.js를 사용하여 이미지 검색'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 JavaScript 및 JSON 응답을 통해 이미지 검색 요청을 Bing Image Search REST API로 보냅니다.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js, mode-other
ms.openlocfilehash: 998535e3cf25de9018e5fb04fc89e82e73ee5c92
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064452"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>빠른 시작: Bing Image Search REST API 및 Node.js를 사용하여 이미지 검색

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 검색 요청을 Bing Image Search API로 보내는 방법을 알아봅니다. 이 JavaScript 애플리케이션에서는 검색 쿼리를 API로 보내고 결과에 첫 번째 이미지의 URL을 표시합니다. 이 애플리케이션은 JavaScript로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 최신 버전의 [Node.js](https://nodejs.org/en/download/).

* [JavaScript 요청 라이브러리](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

자세한 내용은 [Cognitive Services 가격 책정 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)를 참조하세요.

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 엄격성 및 HTTPS 요구 사항을 설정합니다.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. API 엔드포인트, 이미지 API 검색 경로, 구독 키 및 검색 용어에 대한 변수를 만듭니다. `host`의 경우 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>검색 요청 및 쿼리를 생성합니다.

1. 마지막 단계에서 변수를 사용하여 API 요청에 대한 검색 URL의 형식을 지정합니다. 검색어를 API로 보내기 전에 URL로 인코딩합니다.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. 요청 라이브러리를 사용하여 쿼리를 API에 보냅니다. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>응답 처리 및 구문 분석

1. HTTP 호출, `response`를 매개 변수로 사용하는 `response_handler` 함수를 정의합니다. 

2. 이 함수 내에서 JSON 응답 본문을 포함할 변수를 정의합니다. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. `data` 플래그가 호출될 때 응답 본문을 저장합니다.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. `end` 플래그 신호가 처리되면 JSON 응답에서 첫 번째 결과를 가져옵니다. 반환된 총 이미지 수와 함께 첫 번째 이미지의 URL을 출력합니다.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>예제 JSON 응답

Bing Image Search API의 응답은 JSON으로 반환됩니다. 이 샘플 응답은 단일 결과를 표시하도록 잘렸습니다.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 앱 만들기](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search API란?](../overview.md)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Bing Search API에 대한 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/). 
* [Azure Cognitive Services 설명서](../../index.yml).
* [Bing Image Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
