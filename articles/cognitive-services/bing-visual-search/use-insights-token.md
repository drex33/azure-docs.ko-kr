---
title: 인사이트 토큰 사용 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API에서 이미지의 인사이트 토큰을 사용하여 이미지에 대한 중요 정보를 얻는 방법을 보여줍니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 04239a1fb2ce060ac2622c8607d66c5b5aacc790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663196"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>인사이트 토큰을 사용하여 이미지에 대한 사이트 가져오기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Visual Search API는 사용자가 제공하는 이미지에 대한 정보를 반환합니다. 이미지의 URL, 인사이트 토큰을 사용하거나 이미지를 업로드하여 이미지를 제공할 수 있습니다. 이러한 옵션에 대한 내용은 [Bing Visual Search API란?](overview.md)을 참조하세요. 이 문서에서는 인사이트 토큰 사용을 보여 줍니다. 이미지를 업로드하여 인사이트를 가져오는 방법을 보여주는 예제는 빠른 시작을 참조하세요.

* ([C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [Node.JS](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)).

Bing Visual Search에 이미지 토큰 또는 URL을 전송하는 경우 다음은 POST 본문에 포함해야 하는 양식 데이터를 보여 줍니다. 양식 데이터에는 `Content-Disposition` 헤더가 포함되어야 하고, 해당 `name` 매개 변수는 ‘knowledgeRequest’로 설정해야 합니다. `imageInfo` 개체에 대한 자세한 내용은 요청을 참조하세요.

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

이 문서의 예제에서는 인사이트 토큰을 사용하는 방법을 보여 줍니다. /Images/search API 응답의 `Image` 개체에서 인사이트 토큰을 가져옵니다. 인사이트 토큰을 가져오는 방법에 대한 내용은 [Bing Image Search API란?](../Bing-Image-Search/overview.md)을 참조하세요.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

인사이트 토큰을 사용하는 예제는 다음을 참조하세요.

* [C#](#use-with-c)

* [Java](#use-with-java)

* [Node.JS](#use-with-nodejs)

* [Python](#use-with-python)

## <a name="use-with-c"></a>C#에서 사용

### <a name="c-prerequisites"></a>C# 필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
* Windows에서 실행되는 이 코드를 가져오기 위한 모든 버전의 [Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="run-the-application"></a>애플리케이션 실행

이 애플리케이션을 실행하려면 다음 단계를 따릅니다.

1. Visual Studio에서 콘솔 솔루션을 만듭니다.
2. Program.cs의 내용을 이 빠른 시작에 표시되는 코드로 바꿉니다.
3. `accessKey` 값을 구독 키로 바꿉니다.
4. `insightsToken` 값을 /images/search 응답의 인사이트 토큰으로 바꿉니다.
5. 프로그램을 실행합니다.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Java에서 사용

### <a name="java-prerequisites"></a>Java 필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
* 이 코드를 컴파일하고 실행하기 위한 [JDK 7 또는 8](/azure/developer/java/fundamentals/java-support-on-azure). 즐겨찾기가 있는 경우 Java IDE를 사용할 수 있지만 텍스트 편집기로 충분합니다.


## <a name="run-the-java-application"></a>Java 애플리케이션 실행

이 애플리케이션을 실행하려면 다음 단계를 따릅니다.

1. [Gson Java 라이브러리](https://github.com/google/gson)를 다운로드하거나 설치합니다. Maven을 통해 Gson을 얻을 수도 있습니다.
2. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만듭니다.
3. `VisualSearch.java`라는 파일에 제공되는 코드를 추가합니다.
4. `subscriptionKey` 값을 구독 키로 바꿉니다.
5. 프로그램을 실행합니다.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Node.js에서 사용

### <a name="nodejs-prerequisites"></a>Node.js 필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
* 이 코드를 실행하려면 [Node.js 6](https://nodejs.org/en/download/)가 있어야 합니다.

## <a name="run-the-javascript-application"></a>JavaScript 애플리케이션 실행

이 애플리케이션을 실행하려면 다음 단계를 따릅니다.

1. 프로젝트에 대한 폴더를 만듭니다(또는 즐겨찾는 IDE 또는 편집기 사용).
2. 명령 프롬프트 또는 터미널에서, 방금 만든 폴더로 이동합니다.
3. 요청 모듈을 설치합니다.
  
   ```
   npm install request  
   ```
1. 다음과 같이 form-data 모듈을 설치합니다.  
   ```
   npm install form-data  
   ```
1. GetVisualInsights.js라는 파일을 만들고 다음 코드를 추가합니다.
1. `subscriptionKey` 값을 구독 키로 바꿉니다.
1. 프로그램을 실행합니다.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Python에서 사용

### <a name="python-prerequisites"></a>Python 필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
* 이 코드를 실행하려면 [Python 3](https://www.python.org/)가 있어야 합니다.

## <a name="run-the-python-application"></a>Python 애플리케이션 실행

이 애플리케이션을 실행하려면 다음 단계를 따릅니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Python 프로젝트를 만듭니다.
2. visualsearch.py라는 파일을 만들고, 이 빠른 시작에 표시되는 코드를 추가합니다.
3. `SUBSCRIPTION_KEY` 값을 구독 키로 바꿉니다.
4. 프로그램을 실행합니다.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# Run the following in a command console window

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# pip3 install requests

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
# the imageInsightsToken field in the Image object.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>다음 단계

[Visual Search 단일 페이지 웹앱 만들기](tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search API란?](overview.md)  
[Cognitive Services 체험하기](https://aka.ms/bingvisualsearchtryforfree)  
[이미지 - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)