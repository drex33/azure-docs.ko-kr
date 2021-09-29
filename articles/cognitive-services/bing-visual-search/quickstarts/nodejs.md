---
title: '빠른 시작: REST API 및 Node.js를 사용하여 이미지 인사이트 가져오기 - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API 및 Node.js를 사용하여 이미지를 업로드한 다음, 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: eeef03820a0a96376f9e7cb45bc22cc3e2331b22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663254"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>빠른 시작: Bing Visual Search REST API 및 Node.js를 사용하여 이미지 인사이트 가져오기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Visual Search API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 JavaScript 애플리케이션은 API에 이미지를 업로드하고, 이미지에 대한 반환된 정보를 표시합니다. 이 애플리케이션은 JavaScript로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.JS](https://nodejs.org/en/download/)
* JavaScript에 대한 요청 모듈. `npm install request` 명령을 사용하여 모듈을 설치할 수 있습니다.
* form-data 모듈. `npm install form-data` 명령을 사용하여 모듈을 설치할 수 있습니다. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>애플리케이션 초기화

1. 선호하는 IDE 또는 편집기에서 JavaScript 파일을 만들고 다음 요구 사항을 설정합니다.

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. API 엔드포인트, 구독 키에 대한 변수 및 이미지에 대한 경로를 만듭니다. `baseUri` 값의 경우 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. `requestCallback()`이라는 함수를 만들어 API에서 응답을 인쇄합니다.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>검색 요청 생성 및 보내기

1. 로컬 이미지를 업로드할 때 양식 데이터에 `Content-Disposition` 헤더가 포함되어야 합니다. 해당 `name` 매개 변수를 "이미지"로 설정하고 `filename` 매개 변수를 이미지의 파일 이름으로 설정합니다. 양식의 콘텐츠는 이미지의 이진 데이터를 포함합니다. 업로드할 수 있는 최대 이미지 크기는 1MB입니다.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. `FormData()`를 사용하여 새 `FormData` 개체를 만들고 `fs.createReadStream()`을 사용하여 해당 개체에 이미지 경로를 추가합니다.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. 요청 라이브러리를 사용하여 이미지를 업로드하고 `requestCallback()`을 호출하여 응답을 인쇄합니다. 요청 헤더에 구독 키를 추가합니다.

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Visual Search 단일 페이지 웹앱 빌드](../tutorial-bing-visual-search-single-page-app.md)