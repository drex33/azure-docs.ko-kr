---
title: Bing Image Search Python 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: c9dd8bdfb9af1d20433a083b48ee1b7e14658438
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253715"
---
이 빠른 시작을 통해 API에 대한 래퍼이며 동일한 기능을 포함하는 Bing Image Search 클라이언트 라이브러리를 사용하여 첫 번째 이미지 검색을 수행합니다. 이 간단한 Python 애플리케이션은 이미지 검색 쿼리를 보내고 JSON 응답을 구문 분석하고 반환된 첫 번째 이미지의 URL을 표시합니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Python 2.7 또는 3.6 이상](https://www.python.org/)

* Python용 [Azure Image Search 클라이언트 라이브러리](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/)
    * `pip install azure-cognitiveservices-search-imagesearch`를 사용하여 설치

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 스크립트를 만들고 다음 가져오기를 만듭니다.

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 구독 키 및 검색 용어에 대한 변수를 만듭니다.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>이미지 검색 클라이언트 만들기

1. `CognitiveServicesCredentials` 인스턴스를 만들고 이 인스턴스를 사용하여 클라이언트를 인스턴스화합니다.

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

1. 검색 쿼리를 Bing Image Search API에 보냅니다.

    ```python
    image_results = client.images.search(query=search_term)
    ```

## <a name="process-and-view-the-results"></a>결과 처리 및 보기

응답에서 반환된 이미지 결과를 구문 분석합니다.

응답이 검색 결과를 포함하는 경우 첫 번째 결과를 저장하고 썸네일 URL, 원본 URL 및 반환된 총 이미지 수와 같은 세부 내용을 출력합니다.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](../../overview.md)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK에 대한 Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Azure Cognitive Services 설명서](../../../index.yml)
* [Bing Image Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)