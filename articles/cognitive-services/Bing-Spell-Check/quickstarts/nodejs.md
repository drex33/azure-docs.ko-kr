---
title: '빠른 시작: REST API 및 Node.js로 맞춤법 검사 - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API 및 Node.js를 사용하여 맞춤법 및 문법 검사를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 2e71f6e7cd539dba31493e31bd0040eb25495714
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075754"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>빠른 시작: Bing Spell Check REST API 및 Node.js로 맞춤법 검사

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Spell Check REST API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 JavaScript 애플리케이션은 API에 요청을 보내고 제안된 수정 사항을 반환합니다. 

이 애플리케이션은 JavaScript로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 애플리케이션의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js)에 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.js 6](https://nodejs.org/en/download/) 이상

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만듭니다. 엄격성을 설정하고 `https`를 요구하도록 설정합니다. 그런 다음, API 엔드포인트의 호스트, 경로 및 구독 키에 대한 변수를 만듭니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. 검색 매개 변수 및 확인할 텍스트에 대한 변수를 만듭니다. 

   1. `=` 연산자를 사용하여 `mkt` 매개 변수에 시장 코드를 할당합니다. 시장 코드는 요청을 수행한 국가/지역의 코드입니다. 

   1. `&` 연산자를 사용하여 `mode` 매개 변수를 추가한 다음, 맞춤법 검사 모드를 할당합니다. 모드는 `proof`(대부분의 맞춤법/문법 오류 catch) 또는 `spell`(대부분의 맞춤법은 catch하지만 문법 오류는 많지 않음) 중 하나일 수 있습니다.

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>요청 매개 변수 만들기

`POST` 메서드로 새 개체를 만들어서 요청 매개 변수를 만듭니다. 엔드포인트 경로와 쿼리 문자열을 추가하여 경로를 추가합니다. 그런 다음, `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가합니다.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>응답 처리기 만들기

`response_handler`라는 함수를 만들어 API에서 JSON 응답을 받아서 출력합니다. 응답 본문에 대한 변수를 만듭니다. `data` 플래그가 수신되면 `response.on()`을 사용하여 응답을 추가합니다. `end` 플래그가 수신된 후 JSON 본문을 콘솔에 출력합니다.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>요청 보내기

요청 매개 변수 및 응답 핸들러와 함께 `https.request()`를 사용하여 API를 호출합니다. API에 텍스트를 작성한 다음, 요청을 종료합니다.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>애플리케이션 실행

1. 프로젝트를 빌드한 후 실행합니다.

1. 명령줄을 사용하는 경우 다음 명령을 사용하여 애플리케이션을 빌드하고 실행합니다.

   ```bash
   node <FILE_NAME>.js
   ```


## <a name="example-json-response"></a>예제 JSON 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../tutorials/spellcheck.md)

- [Bing Spell Check API란?](../overview.md)
- [Bing Spell Check API v7 참조](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
