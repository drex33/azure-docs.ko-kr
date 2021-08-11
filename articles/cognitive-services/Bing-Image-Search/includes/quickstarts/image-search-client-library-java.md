---
title: Bing Image Search Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: a07d6c6b397f8461202ffebe081b2fde91b53547
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113289650"
---
이 빠른 시작을 통해 API에 대한 래퍼이며 동일한 기능을 포함하는 Bing Image Search 클라이언트 라이브러리를 사용하여 첫 번째 이미지 검색을 수행합니다. 이 간단한 Java 애플리케이션은 이미지 검색 쿼리를 보내고 JSON 응답을 구문 분석하고 반환된 첫 번째 이미지의 URL을 표시합니다.

이 샘플에 대한 소스 코드는 추가 오류 처리 및 주석과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-support-on-azure)의 최신 버전

Maven, Gradle 또는 기타 종속성 관리 시스템을 사용하여 Bing Image Search 클라이언트 라이브러리 종속성을 설치합니다. Maven POM 파일에는 다음 선언이 필요합니다.

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. 즐겨찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만들고 다음 가져오기를 클래스 구현에 추가합니다.

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. 주 메서드에서 구독 키 및 검색 용어에 대한 변수를 만듭니다. 그런 다음, Bing Image Search 클라이언트를 인스턴스화합니다.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>API에 검색 요청 보내기

1. `bingImages().search()`를 사용하여 검색 쿼리를 포함하는 HTTP 요청을 보냅니다. 응답을 `ImagesModel`로 저장합니다.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>결과 구문 분석 및 보기

응답에서 반환된 이미지 결과를 구문 분석합니다.
응답에 검색 결과가 포함된 경우 첫 번째 결과를 저장하고 반환된 총 이미지 수와 함께 썸네일 URL, 원래 URL과 같은 세부 정보를 출력합니다.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Image Search 단일 페이지 앱 자습서](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>참고 항목

* [Bing Image Search란?](../../overview.md)  
* [온라인 대화형 데모 사용해보기](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK에 대한 Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Azure Cognitive Services 설명서](../../../index.yml)
* [Bing Image Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)