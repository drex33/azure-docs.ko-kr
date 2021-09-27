---
title: 문서 번역 시작
description: C#, Go, Java, Node.js 또는 Python 프로그래밍 언어 및 플랫폼을 사용하여 문서 번역 서비스를 만드는 방법입니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 0e1192e310c658e93c4cfe5738e9ad4d36984113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580875"
---
# <a name="get-started-with-document-translation"></a>문서 번역 시작

 이 문서에서는 HTTP REST API 메서드와 함께 문서 번역을 사용하는 방법을 알아봅니다. 문서 번역은 [Azure Translator](../translator-overview.md) 서비스의 클라우드 기반 기능입니다.  Document Translation API를 사용하면 원본 문서 구조와 텍스트 형식을 유지하면서 전체 문서를 번역할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!NOTE]
>
> 1. 일반적으로 Azure Portal에서 Cognitive Service 리소스를 만들 때 다중 서비스 구독 키 또는 단일 서비스 구독 키를 만들 수 있습니다. 그러나 문서 번역은 현재 Translator(단일 서비스) 리소스에서만 지원되며 Cognitive Services(다중 서비스) 리소스에는 포함되지 **않습니다**.
> 2. 문서 번역은 S1 표준 서비스 계획(종량제) 또는 D3 볼륨 할인 플랜에서 **만** 사용할 수 있습니다. [Cognitive Services 가격 책정 - Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator/)_를 참조하세요_.
>

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들 수**](https://azure.microsoft.com/free/) 있습니다.

* [**단일 서비스 Translator 리소스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)(다중 서비스 Cognitive Services 리소스 **아님**).

* [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다.

## <a name="custom-domain-name-and-subscription-key"></a>사용자 지정 도메인 이름 및 구독 키

> [!IMPORTANT]
>
> * **문서 번역 서비스에 대한 모든 API 요청에는 사용자 지정 도메인 엔드포인트가 필요합니다.**
> * Azure Portal 리소스 _키 및 엔드포인트_ 페이지에 있는 엔드포인트나 전역 번역기 엔드포인트(`api.cognitive.microsofttranslator.com`)를 사용하여 문서 번역에 대한 HTTP 요청을 수행하지 않습니다.

### <a name="what-is-the-custom-domain-endpoint"></a>사용자 지정 도메인 엔드포인트란?

사용자 지정 도메인 엔드포인트는 리소스 이름, 호스트 이름 및 Translator 하위 디렉터리로 형식이 지정된 URL입니다.

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0
```

### <a name="find-your-custom-domain-name"></a>사용자 지정 도메인 이름 찾기

**NAME-OF-YOUR-RESOURCE**(*사용자 지정 도메인 이름* 이라고도 함) 매개 변수는 Translator 리소스를 만들 때 **이름** 필드에 입력한 값입니다.

:::image type="content" source="../media/instance-details.png" alt-text="Azure Portal의 이미지, 리소스 만들기, 인스턴트 세부 정보, 이름 필드":::

### <a name="get-your-subscription-key"></a>구독 키 가져오기

Translator 서비스에 대한 요청에는 액세스 인증을 위한 읽기 전용 키가 필요합니다.

1. 새 리소스를 만든 경우 배포한 후 **리소스로 이동** 을 선택합니다. 기존 문서 번역 리소스가 있는 경우 리소스 페이지로 직접 이동합니다.
1. 왼쪽 레일의 *리소스 관리* 에서 **키 및 엔드포인트** 를 선택합니다.
1. *Microsoft 메모장* 과 같은 편리한 위치에 구독 키를 복사하여 붙여넣습니다.
1. 문서 번역 서비스에 대한 요청을 인증하기 위해 아래 코드에 붙여넣습니다.

:::image type="content" source="../media/translator-keys.png" alt-text="Azure Portal의 구독 키 가져오기 필드 이미지":::

## <a name="create-azure-blob-storage-containers"></a>Azure Blob Storage 컨테이너 만들기

원본 및 대상 파일에 대한 [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)에서 [**컨테이너를 만들어야**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 합니다.

* **원본 컨테이너**. 이 컨테이너는 번역을 위해 파일을 업로드하는 위치입니다(필수).
* **대상 컨테이너**. 이 컨테이너는 번역된 파일이 저장되는 위치입니다(필수).

> [!NOTE]
> 문서 번역은 대상 컨테이너에서 용어집을 Blob으로서 지원합니다(별도 용어집 컨테이너 아님). 사용자 지정 용어집을 포함하려면 대상 컨테이너에 추가하고 요청에 ` glossaryUrl`을 포함합니다.  용어집에 번역 언어 쌍이 없으면 적용되지 않습니다. [사용자 지정 용어집을 사용하여 문서 번역](#translate-documents-using-a-custom-glossary) *참조*

### <a name="create-sas-access-tokens-for-document-translation"></a>**문서 번역을 위한 SAS 토큰 생성**

`sourceUrl`, `targetUrl` 및 선택적 `glossaryUrl`에는 쿼리 문자열로 추가된 SAS(공유 액세스 서명) 토큰이 포함되어야 합니다. 토큰은 컨테이너 또는 특정 Blob에 할당될 수 있습니다. [**문서 번역을 위한 SAS 토큰 생성 프로세스**](create-sas-tokens.md)를 *참조하세요*.

* **원본** 컨테이너 또는 Blob에는 **읽기** 및 **나열** 액세스가 지정되어 있어야 합니다.
* **대상** 컨테이너 또는 Blob에는 **쓰기** 및 **나열** 액세스가 지정되어 있어야 합니다.
* **용어집** Blob에는 **읽기** 및 **나열** 액세스가 지정되어 있어야 합니다.

> [!TIP]
>
> * 작업에서 **여러** 파일(Blob)을 번역하는 경우 **컨테이너 수준에서 SAS 액세스 권한을 위임** 합니다.
> * 작업에서 **단일** 파일(Blob)을 번역하는 경우 **Blob 수준에서 SAS 액세스 권한을 위임** 합니다.
>

## <a name="document-translation-http-requests"></a>문서 번역: HTTP 요청

일괄 처리 문서 번역 요청은 POST 요청을 통해 Translator 서비스 엔드포인트로 전송됩니다. 성공하면 POST 메서드가 `202 Accepted` 응답 코드를 반환하고 서비스에서 일괄 요청을 만듭니다.

### <a name="http-headers"></a>HTTP 헤더

각 Document Translator API 요청에는 다음 헤더가 포함됩니다.

|HTTP 헤더|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**필수**: 이 값은 Translator 또는 Cognitive Services 리소스에 대한 Azure 구독 키입니다.|
|콘텐츠 형식|**필수**: 페이로드의 콘텐츠 형식을 지정합니다. 허용되는 값은 application/json 또는 charset=UTF-8입니다.|
|Content-Length|**필수**: 요청 본문의 길이입니다.|

### <a name="post-request-body-properties"></a>POST 요청 본문 속성

* POST 요청 URL이 POST `https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches`입니다.
* POST 요청 본문은 `inputs`라는 JSON 객체입니다.
* `inputs` 개체는 원본 및 대상 언어 쌍에 대한 `sourceURL` 및 `targetURL` 컨테이너 주소를 모두 포함합니다.
* `prefix` 및 `suffix` 필드(선택 사항)는 폴더를 포함하여 컨테이너의 문서를 필터링하는 데 사용됩니다.
* `glossaries` 필드(선택 사항)의 값은 문서가 번역될 때 적용됩니다.
* 각 대상 언어의 `targetUrl`은 고유해야 합니다.

>[!NOTE]
> 이름이 같은 파일이 대상에 이미 있으면 해당 파일을 덮어씁니다.

<!-- markdownlint-disable MD024 -->
### <a name="translate-all-documents-in-a-container"></a>컨테이너의 모든 문서 번역

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

### <a name="translate-a-specific-document-in-a-container"></a>컨테이너의 특정 문서 번역

* “storageType”: “File”을 지정했는지 확인합니다.
* 특정 Blob/문서(컨테이너 아님)에 대한 원본 URL 및 SAS 토큰을 생성했는지 확인합니다.
* SAS 토큰은 컨테이너용이지만 대상 URL의 일부로 대상 파일 이름을 지정했는지 확인합니다.
* 아래 샘플 요청은 두 개의 대상 언어로 번역되는 단일 문서를 보여줍니다.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="translate-documents-using-a-custom-glossary"></a>사용자 지정 용어집을 사용하여 문서 번역

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://myblob.blob.core.windows.net/source",
                "filter": {
                    "prefix": "myfolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://myblob.blob.core.windows.net/target",
                    "language": "es",
                    "glossaries": [
                        {
                            "glossaryUrl": "https:// myblob.blob.core.windows.net/glossary/en-es.xlf",
                            "format": "xliff"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="use-code-to-submit-document-translation-requests"></a>코드를 사용하여 문서 번역 요청 제출

### <a name="set-up-your-coding-platform"></a>코딩 플랫폼 설정

### <a name="c"></a>[C#](#tab/csharp)

* 새 프로젝트를 만듭니다.
* Program.cs를 아래에 표시된 C# 코드로 바꿉니다.
* Program.cs에서 엔드포인트, 구독 키 및 컨테이너 URL 값을 설정합니다.
* JSON 데이터를 처리하려면 [.NET CLI를 사용하여 Newtonsoft.Json 패키지](https://www.nuget.org/packages/Newtonsoft.Json/)를 추가합니다.
* 프로젝트 디렉터리에서 프로그램을 실행합니다.

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

* 새 Node.js 프로젝트를 만듭니다.
* `npm i axios`를 사용하여 Axios 라이브러리를 설치합니다.
* 아래 코드를 복사하여 프로젝트에 붙여넣습니다.
* 엔드포인트, 구독 키 및 컨테이너 URL 값을 설정합니다.
* 프로그램을 실행합니다.

### <a name="python"></a>[Python](#tab/python)

* 새 프로젝트를 만듭니다.
* 샘플 중 하나의 코드를 프로젝트에 복사하여 붙여넣습니다.
* 엔드포인트, 구독 키 및 컨테이너 URL 값을 설정합니다.
* 프로그램을 실행합니다. 예: `python translate.py`

### <a name="java"></a>[Java](#tab/java)

* 프로젝트에 대한 작업 디렉터리를 만듭니다. 예를 들면 다음과 같습니다.

```powershell
mkdir sample-project
```

* 프로젝트 디렉터리에서 다음 하위 디렉터리 구조를 만듭니다.

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**참고**: Java 원본 파일(예: _sample.java_)은 src/main/**java** 에 있습니다.

* 루트 디렉터리(예: *sample-project*)에서 Gradle로 프로젝트를 초기화합니다.

```powershell
gradle init --type basic
```

* **DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

* `build.gradle.kts` 파일을 업데이트합니다. 샘플에 따라 `mainClassName`을 업데이트해야 합니다.

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* **java** 디렉터리에서 Java 파일을 만들고 제공된 샘플에서 코드를 복사하여 붙여넣습니다. 구독 키 및 엔드포인트를 추가하는 것을 잊지 마세요.

* **루트 디렉터리에서 샘플 빌드 및 실행**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)

* 새 Go 프로젝트를 만듭니다.
* 아래 제공된 코드를 추가합니다.
* 엔드포인트, 구독 키 및 컨테이너 URL 값을 설정합니다.
* '.go' 확장명으로 파일을 저장합니다.
* Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
* 파일을 빌드합니다. 예: 'go build example-code.go'.
* 파일을 실행합니다(예: 'example-code').

 ---

> [!IMPORTANT]
>
> 아래 코드 샘플의 경우 표시된 곳에 키와 엔드포인트를 하드 코딩합니다. 작업이 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요.  자격 증명을 안전하게 저장하고 액세스하는 방법은 [Azure Cognitive Services 보안](../../cognitive-services-security.md?tabs=command-line%2ccsharp)을 참조하세요.
>
> 작업에 따라 다음 필드를 업데이트해야 할 수 있습니다.
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`(작업 ID)
>>

#### <a name="locating--the-id-value"></a>`id` 값 찾기

* POST 메서드 응답 헤더 `Operation-Location` URL 값에서 작업 `id`를 찾습니다. URL의 마지막 매개 변수는 작업의 작업 **`id`** 입니다.

|**응답 헤더**|**결과 URL**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* **작업 가져오기(GET)** 요청을 사용하여 문서 번역 작업 `id`를 검색할 수도 있습니다.

>

 ## <a name="translate-documents"></a>문서 번역

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;


    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");

        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {

                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;

                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:");
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="get-file-formats"></a>파일 형식 가져오기

지원되는 파일 형식 목록을 검색합니다. 성공한 경우 이 메서드는 `200 OK` 응답 코드를 반환합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-job-status"></a>작업 상태 가져오기

단일 작업의 현재 상태와 문서 번역 요청의 모든 작업 요약을 가져옵니다. 성공한 경우 이 메서드는 `200 OK` 응답 코드를 반환합니다.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-document-status"></a>문서 상태 가져오기

### <a name="brief-overview"></a>간략한 개요

문서 번역 요청에서 특정 문서의 상태를 검색합니다. 성공한 경우 이 메서드는 `200 OK` 응답 코드를 반환합니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="delete-job"></a>작업 삭제

### <a name="brief-overview"></a>간략한 개요

현재 처리 중이거나 대기 중인 작업을 취소합니다. 번역이 시작되지 않은 문서만 취소됩니다.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>콘텐츠 제한

다음 표에는 문서 번역으로 보내는 데이터에 대한 제한이 나열되어 있습니다.

|attribute | 제한|
|---|---|
|문서 크기| ≤ 40MB |
|총 파일 수|≤ 1000 |
|일괄 처리의 총 콘텐츠 크기 | ≤ 250MB|
|일괄 처리의 대상 언어 수| ≤ 10 |
|번역 메모리 파일 크기| ≤ 10MB|

문서 번역을 사용하여 암호화된 암호를 가진 문서 또는 콘텐츠 복사를 위한 액세스가 제한된 문서와 같은 보안 문서를 번역할 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="common-http-status-codes"></a>일반 HTTP 상태 코드

| HTTP 상태 코드 | Description | 가능한 원인 |
|------------------|-------------|-----------------|
| 200 | 확인 | 요청이 성공했습니다. |
| 400 | 잘못된 요청 | 필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다. |
| 401 | 권한 없음 | 요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요. Azure Portal에서 구독을 관리할 때 **Cognitive Services** 다중 서비스 리소스가 _아닌_ **Translator** 단일 서비스 리소스를 사용하고 있는지 확인하세요.
| 429 | 너무 많은 요청 | 구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다. |
| 502 | 잘못된 게이트웨이    | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다. |

## <a name="learn-more"></a>자세한 정보

* [Translator v3 API 참조](../reference/v3-0-reference.md)
* [언어 지원](../language-support.md)
* [Azure API Management의 구독](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Custom Translator를 사용하여 사용자 지정된 언어 시스템 만들기](../custom-translator/overview.md)
>
>
