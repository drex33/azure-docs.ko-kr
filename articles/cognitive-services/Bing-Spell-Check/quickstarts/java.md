---
title: '빠른 시작: REST API 및 Java로 맞춤법 검사 - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API및 Java를 사용하여 맞춤법 및 문법 검사를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java, mode-other
ms.author: aahi
ms.openlocfilehash: af737fec3c89dc8006343e558a6a196f151c0e33
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039910"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>빠른 시작: Bing Spell Check REST API 및 Java로 맞춤법 검사

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Spell Check REST API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 Java 애플리케이션은 API에 요청을 보내고 제안된 수정 사항을 반환합니다. 

이 애플리케이션은 Java로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 애플리케이션의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java)에 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* JDK(Java Development Kit) 7 이상

* [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) 또는 최신 [Gson](https://github.com/google/gson) 버전을 가져옵니다. 명령줄 실행의 경우 주 클래스를 사용하여 Java 폴더에 `.jar`을 추가합니다.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>애플리케이션 만들기 및 초기화

1. 선택한 클래스 이름을 사용하여 원하는 IDE 또는 편집기에서 새 Java 프로젝트를 만든 후, 다음 패키지를 가져옵니다.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. API 엔드포인트의 호스트, 경로 및 구독 키에 대한 변수를 만듭니다. 그런 다음, 지역/국가, 맞춤법을 검사할 텍스트 및 맞춤법 검사 모드의 문자열에 대한 변수를 만듭니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>API 요청 만들기 및 보내기

1. `check()`라는 함수를 만들고 API 요청을 만들어서 보냅니다. 이 함수 내에서 다음 단계에서 지정한 코드를 추가합니다. 요청 매개 변수에 대한 문자열을 만듭니다.

   1. `=` 연산자를 사용하여 `mkt` 매개 변수에 시장 코드를 할당합니다. 

   1. `&` 연산자를 사용하여 `mode` 매개 변수를 추가한 다음, 맞춤법 검사 모드를 할당합니다. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. 엔드포인트 호스트, 경로 및 매개 변수 문자열을 결합하여 URL을 만듭니다. 새 `HttpsURLConnection` 개체를 만듭니다.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. URL에 대한 연결을 엽니다. 요청 메서드를 `POST`로 설정하고 요청 매개 변수를 추가합니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가해야 합니다.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. 새 `DataOutputStream` 개체를 만들고 API에 요청을 보냅니다.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>API 응답 형식 지정 및 읽기

1. 더 읽기 쉬운 출력을 위해 JSON 형식을 지정하는 클래스에 `prettify()` 메서드를 추가합니다.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. `BufferedReader`를 만들고 API에서 응답을 읽어옵니다. 이것을 콘솔에 출력합니다.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>API 호출

애플리케이션의 main 함수에서 이전에 만든 `check()` 메서드를 호출합니다.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>애플리케이션 실행

프로젝트를 빌드한 후 실행합니다. 명령줄을 사용하는 경우 다음 명령을 사용하여 애플리케이션을 빌드하고 실행합니다.

1. 애플리케이션을 빌드합니다.

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. 애플리케이션을 실행합니다.

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
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
