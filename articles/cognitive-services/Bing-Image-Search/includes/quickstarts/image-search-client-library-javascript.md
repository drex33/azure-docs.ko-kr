---
title: Bing Image Search JavaScript 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f56346b9282be42d6022fd6f15f4ce206ae8dbd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253381"
---
이 빠른 시작을 통해 API에 대한 래퍼이며 동일한 기능을 포함하는 Bing Image Search 클라이언트 라이브러리를 사용하여 첫 번째 이미지 검색을 수행합니다. 이 간단한 JavaScript 애플리케이션은 이미지 검색 쿼리를 보내고 JSON 응답을 구문 분석하고 반환된 첫 번째 이미지의 URL을 표시합니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 최신 버전의 [Node.js](https://nodejs.org/en/download/).
* [JavaScript용 Bing Image Search SDK](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  설치하려면 `npm install @azure/cognitiveservices-imagesearch`를 실행합니다.
* 클라이언트를 인증하는 `@azure/ms-rest-azure-js` 패키지의 `CognitiveServicesCredentials` 클래스입니다.
     * 설치하려면 `npm install @azure/ms-rest-azure-js`를 실행합니다.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 JavaScript 파일을 만들고 엄격성, https 및 기타 요구 사항을 설정합니다.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. 프로젝트의 주요 메서드에서 유효한 구독 키에 대한 변수, Bing에서 반환할 이미지 및 검색어를 만듭니다. 그런 다음, 키를 사용하여 이미지 검색 클라이언트를 인스턴스화합니다.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>비동기 도우미 함수 만들기

1. 클라이언트를 비동기적으로 호출할 함수를 만들고 Bing Image Search 서비스에서 응답을 반환합니다.

    ```javascript
    // a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```

## <a name="send-a-query-and-handle-the-response"></a>쿼리 보내기 및 응답 처리

1. 도우미 함수를 호출하고 해당 `promise`를 처리하여 응답에서 반환되는 이미지 결과를 구문 분석합니다.

    응답이 검색 결과를 포함하는 경우 첫 번째 결과를 저장하고 썸네일 URL, 원본 URL 및 반환된 총 이미지 수와 같은 세부 내용을 출력합니다.
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](../../overview.md)
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Azure Cognitive Services SDK에 대한 Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Azure Cognitive Services 설명서](../../../index.yml)
* [Bing Image Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)