---
title: 빠른 시작 - Node.js를 사용하여 API에 쿼리 보내기 - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청 전송을 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 5556b894e05bd727512c1321ece52fbbe99cb145
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053270"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Bing Local Business Search API에 쿼리 보내기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청을 보내는 방법을 알아봅니다. 이 간단한 애플리케이션은 Node.js에서 작성되었지만 API는 RESTful 웹 서비스로, HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다.

이 예제 애플리케이션은 검색 쿼리에 대한 API에서 로컬 응답 데이터를 가져옵니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 최신 버전의 [Node.js](https://nodejs.org/en/download/).
* [JavaScript 요청 라이브러리](https://github.com/request/request).
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.


## <a name="code-scenario"></a>코드 시나리오

다음 코드는 다음 단계에서 구현된 요청을 정의하고 보냅니다.

1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 쿼리를 지정하고 쿼리 매개 변수를 추가합니다.
3. 응답에 대한 처리기 함수를 만듭니다.
4. 요청을 만들고 `Ocp-Apim-Subscription-Key` 헤더를 추가하는 검색 기능을 정의합니다.
5. 검색 함수를 실행합니다.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>다음 단계

* [Local Business Search C# 빠른 시작](local-quickstart.md)
* [Local Business Search Java 빠른 시작](local-search-java-quickstart.md)
* [Local Business Search Python 빠른 시작](local-search-python-quickstart.md)
