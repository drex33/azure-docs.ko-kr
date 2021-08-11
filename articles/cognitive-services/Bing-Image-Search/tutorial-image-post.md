---
title: '자습서: REST API 및 C#을 사용하여 이미지 세부 정보 추출 - Bing Image Search'
titleSuffix: Azure Cognitive Services
description: 이 자습서를 사용하여 Bing Image Search API를 통해 이미지 세부 정보를 추출하는 C# 애플리케이션을 만듭니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 12/06/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 7e65e9c34ed114f846021d1c5c417825d4859ac3
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913440"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>자습서: Bing Image Search API 및 C#을 사용하여 이미지 세부 정보 추출

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Image Search API를 통해 사용할 수 있는 다양한 [엔드포인트](./image-search-endpoint.md)가 있습니다. `/details` 엔드포인트는 이미지가 포함된 POST 요청을 허용하며 이미지에 대한 다양한 세부 정보를 반환할 수 있습니다. 이 C# 애플리케이션은 API를 사용하여 이미지를 보내고 Bing에서 반환된 다음과 같은 JSON 개체인 세부 정보를 표시합니다.

![[JSON 결과]](media/cognitive-services-bing-images-api/jsonResult.jpg)

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * `POST` 요청에서 Image Search `/details` 엔드포인트 사용
> * 요청에 대한 헤더 지정
> * URL 매개 변수를 사용하여 결과 지정
> * 이미지 데이터를 업로드하고 `POST` 요청 보내기
> * JSON 결과를 콘솔에 인쇄

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs)에 제공됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Visual Studio 2017 이상](https://visualstudio.microsoft.com/downloads/)의 모든 버전.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>이미지 세부 정보 검색 요청 생성

다음은 요청 본문에 이미지 데이터와 함께 POST 요청을 허용하는 `/details` 엔드포인트입니다. 아래의 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

검색 요청 URL을 생성할 때 `modules` 매개 변수는 위의 엔드포인트에 따라 결과에 포함될 세부 정보 형식을 지정합니다.

* `modules=All`
* `modules=RecognizedEntities`(이미지에 표시되는 사람 또는 위치)

POST 요청에서 다음을 포함하는 JSON 텍스트를 가져오는 `modules=All`을 지정합니다.

* `bestRepresentativeQuery` - 업로드된 이미지와 비슷한 이미지를 반환하는 Bing 쿼리
* `detectedObjects` -이미지에 있는 개체
* `image` - 이미지의 메타데이터
* `imageInsightsToken` - 이미지에서 `RecognizedEntities`(이미지에 표시되는 사람 또는 위치)를 가져오는 후속 GET 요청의 토큰
* `imageTags` - 이미지의 태그
* `pagesIncluding` - 이미지를 포함하는 웹 페이지
* `relatedSearches` - 이미지에서 세부 정보를 기준으로 검색
* `visuallySimilarImages` - 웹에 있는 유사한 이미지.

POST 요청에서 `modules=RecognizedEntities`를 지정하여 후속 GET 요청에서 이미지의 사람이나 장소를 식별하는 데 사용될 수 있는 `imageInsightsToken`만 가져옵니다.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>WebClient 개체 만들기 및 API 요청의 헤더 설정

`WebClient` 개체를 만들고, 헤더를 설정합니다. Bing Search API에 대한 모든 요청에는 `Ocp-Apim-Subscription-Key`가 필요합니다. 이미지를 업로드하려는 `POST` 요청 역시 `ContentType: multipart/form-data`를 지정해야 합니다.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>이미지 업로드 및 결과 표시

`WebClient` 클래스의 `UpLoadFile()` 메서드는 `RequestStream`의 형식 지정 및 `HttpWebRequest` 호출을 포함하여 `POST` 요청에 대한 데이터의 형식을 지정합니다.

`/details` 엔드포인트 및 업로드할 이미지 파일을 사용하여 `WebClient.UpLoadFile()`을 호출합니다. JSON 응답을 사용하여 `SearchResult` 구조체의 인스턴스를 초기화하고 응답을 저장합니다.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
그러면 이 JSON 응답을 콘솔에 인쇄할 수 있습니다.

## <a name="use-an-image-insights-token-in-a-request"></a>요청에 이미지 인사이트 토큰 사용

`POST`의 결과와 함께 반환된 `ImageInsightsToken`을 사용하려면 `GET` 요청에 추가하면 됩니다. 다음은 그 예입니다.

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

이미지에 식별 가능한 사람 또는 위치가 있는 경우 이 요청은 그 사람 또는 위치에 대한 정보를 반환합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱에 이미지 및 검색 옵션 표시](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search API 참조](/rest/api/cognitiveservices/bing-images-api-v7-reference)