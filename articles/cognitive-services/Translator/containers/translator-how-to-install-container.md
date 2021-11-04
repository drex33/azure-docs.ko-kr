---
title: Translator API용 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: Translator API용 Docker 컨테이너를 사용하여 문자를 번역합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 10/14/2021
ms.author: lajanuar
recommendations: false
keywords: 온-프레미스, Docker, 컨테이너, 식별
ms.openlocfilehash: dbee487212f56eb5c99d3cc785cf8a2205280fcf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426148"
---
# <a name="install-and-run-translator-containers-preview"></a>Transaltor 컨테이너 설치 및 실행(미리 보기)

컨테이너를 사용하면 고유한 환경에서 Translator 서비스의 몇몇 기능을 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 Translator 컨테이너를 다운로드, 설치 및 실행하는 방법을 알아봅니다.

Translator 컨테이너를 사용하면 강력한 클라우드 기능과 에지 위치 모두에 최적화된 번역기 애플리케이션 아키텍처를 빌드할 수 있습니다.

번역기 컨테이너를 사용할 때 [지원 되는 언어](../language-support.md) 목록을 참조 하세요. 

> [!IMPORTANT]
>
> * Translator 컨테이너는 제어된 미리 보기로 제공되며 사용하려면 온라인 요청을 제출하고 승인을 받아야 합니다. 자세한 내용은 다음 [컨테이너를 실행하도록 승인 요청](#request-approval-to-run-container)을 참조하세요.
> * Translator 컨테이너는 클라우드 제품과 비교 시 제한된 기능을 지원합니다.  자세한 내용은 **컨테이너: 번역** 을 참조하세요.

<!-- markdownlint-disable MD033 -->

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/)이 필요합니다.  계정이 없는 경우 [**체험 계정을 만들 수 있습니다**](https://azure.microsoft.com/free/).

다음 구성 요소도 필요합니다.

| 필수 | 목적 |
|--|--|
| Docker 사용 경험 | <ul><li>기본 `docker` [용어와 명령](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념을 기본적으로 이해하고 있어야 합니다.</li></ul> |
| Docker 엔진 | <ul><li>[호스트 컴퓨터](#host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.</li><li> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. </li><li> **Windows** 에서 Docker가 **Linux** 컨테이너를 지원하도록 구성해야 합니다.</li></ul> |
| Translator 리소스 | <ul><li>연결된 API 키 및 엔드포인트 URI 등 ‘전역’ 이외의 지역에 있는 Azure [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 리소스. 컨테이너를 시작하는 데 두 값이 모두 필요하며 리소스 개요 페이지에서 찾을 수 있습니다.</li></ul>|
|||

|선택 사항|목적|
|---------|----------|
|Azure CLI(명령줄 인터페이스) |<ul><li> [Azure CLI](/cli/azure/install-azure-cli)를 사용하면 온라인 명령 세트를 사용하여 Azure 리소스를 만들고 관리할 수 있습니다. Windows, macOS 및 Linux 환경에 설치할 수 있으며 Docker 컨테이너와 Azure Cloud Shell에서 실행할 수 있습니다.</li></ul> |
|||

## <a name="required-elements"></a>필수 요소

모든 Cognitive Services 컨테이너에 3가지 기본 요소가 필요합니다.

* **EULA 승인 설정**. EULA(최종 사용자 사용권 계약)는 `Eula=accept` 값으로 설정됩니다.

* **API 키** 및 **엔드포인트 URL**.  API 키는 컨테이너를 시작하는 데 사용됩니다. Translator 리소스 _키 및 엔드포인트_ 페이지로 이동하고 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘을 선택하면 API 키 값과 엔드포인트 URL 값을 검색할 수 있습니다.

> [!IMPORTANT]
>
> * 구독 키는 Cognitive Service API에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용하여 안전하게 저장합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.

## <a name="host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

## <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 Translator 컨테이너의 최소 사양과 권장 사양을 설명합니다. 메모리는 최소 2GB(기가바이트) 이상이며 각 CPU는 최소 2.6GHz(기가헤르츠) 이상이어야 합니다. 각 Translator에 할당할 메모리(GB(기가바이트) 단위). 다음 표에서는 각 Translator 컨테이너의 리소스 최소 할당과 권장 할당을 설명합니다.

| 컨테이너 | 최소 |권장 | 언어 쌍 |
|-----------|---------|---------------|----------------------|
| Translator가 연결됨 |2개 코어, 2GB 메모리 |4개 코어, 8GB 메모리 | 4 |
|||

모든 언어 쌍에 2GB 메모리를 사용하는 것이 좋습니다. 기본적으로 Translator 오프라인 컨테이너에는 언어 쌍 4개가 있습니다. `docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!NOTE]
>
> * docker run 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 CPU 코어 및 메모리.
>
> * 최소 사양과 권장 사향은 호스트 컴퓨터 리소스가 아닌 Docker 한도에 따릅니다.

## <a name="request-approval-to-run-container"></a>컨테이너를 실행하도록 승인 요청

컨테이너에 대한 액세스를 요청하려면 [**제어된 서비스에 사용되는 Azure Cognitive Services 애플리케이션**](https://aka.ms/csgate-translator)을 완료하고 제출합니다.

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

## <a name="get-container-images-with-docker-commands"></a>**Docker 명령** 을 사용하여 컨테이너 이미지를 가져옵니다.

> [!IMPORTANT]
>
> * 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다.
> * 컨테이너를 인스턴스화하려면 `EULA`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.

[docker pull](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드하여 실행합니다.

```Docker
docker run --rm -it -p 5000:80 --memory 12g --cpus 4 \
-v /mnt/d/TranslatorContainer:/usr/local/models \
-e apikey={API_KEY} \
-e eula=accept \
-e billing={ENDPOINT_URI} \
-e Languages=en,fr,es,ar,ru  \
mcr.microsoft.com/azure-cognitive-services/translator/text-translation
```

위 명령은 다음과 같습니다.

* 컨테이너 이미지에서 Translator 컨테이너를 다운로드하여 실행합니다.
* 메모리 12GB(기가바이트)와 CPU 코어 4개를 할당합니다.
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* EULA(최종 사용자 계약)에 동의
* 청구 엔드포인트 구성
* 영어, 프랑스어, 스페인어, 아랍어 및 러시아어의 번역 모델 다운로드
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.

## <a name="query-the-containers-translator-endpoint"></a>컨테이너의 Translator 엔드포인트 쿼리

 컨테이너는 REST 기반 Translator 엔드포인트 API를 제공합니다. 다음은 요청 예제입니다.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en&to=zh-HANS"
    -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

> [!NOTE]
> 컨테이너가 준비되기 전에 cURL POST 요청을 시도하면 *서비스를 일시적으로 사용할 수 없음* 응답이 표시됩니다. 컨테이너가 준비될 때까지 기다린 다음, 다시 시도하세요.

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshoot"></a>문제 해결

### <a name="validate-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인

컨테이너가 실행되고 있는지 확인하는 방법에는 여러 가지가 있습니다.

* 컨테이너는 `\`에서 컨테이너가 실행 중인지 시각적으로 확인할 수 있는 홈페이지를 제공합니다.

* 즐겨 찾는 웹 브라우저를 열고 확인하려는 컨테이너의 외부 IP 주소와 노출된 포트로 이동하면 됩니다. 아래의 다양한 요청 URL을 사용하여 컨테이너가 실행 중인지 확인합니다. 아래에 나열된 요청 URL 예는 `http://localhost:5000`이지만 특정 컨테이너는 다를 수 있습니다. 컨테이너의 **외부 IP 주소** 와 노출된 포트로 이동해야 합니다.

| 요청 URL | 용도 |
|--|--|
| `http://localhost:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://localhost:5000/ready` | GET으로 요청했습니다. 컨테이너에서 모델에 대한 쿼리를 수락할 준비가 되었는지 확인할 수 있습니다.  이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/status` | GET으로 요청했습니다.  컨테이너를 시작하는 데 사용된 api-key가 엔드포인트 쿼리를 발생시키지 않으면서 유효한지 확인합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 세트와 **사용해 보기** 기능을 제공합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

:::image type="content" source="../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png" alt-text="컨테이너 홈페이지":::

[!INCLUDE [Diagnostic container](../../containers/includes/diagnostics-container.md)]

## <a name="text-translation-code-samples"></a>문자 번역 코드 샘플

### <a name="translate-text-with-swagger"></a>swagger를 사용하여 문자 번역

#### <a name="english-leftrightarrow-german"></a>영어 &leftrightarrow; 독일어

swagger 페이지(`<http://localhost:5000/swagger/index.html>`)로 이동합니다.

1. **POST /translate** 를 선택합니다.
1. **사용해 보기** 를 선택합니다.
1. **From** 매개 변수를 `en`으로 입력합니다.
1. **To** 매개 변수를 `de`로 입력합니다.
1. **api-version** 매개 변수를 `3.0`으로 입력합니다.
1. **texts** 에서 `string`을 다음 JSON으로 바꿉니다.

```json
  [
        {
            "text": "hello, how are you"
        }
  ]
```

**실행** 을 선택합니다. 그러면 **응답 본문** 에 번역이 출력됩니다. 다음 응답과 비슷한 내용이 표시되어야 합니다.

```json
"translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de"
      }
    ]
```

### <a name="translate-text-with-python"></a>Python을 사용하여 문자 번역

```python
import requests, json

url = 'http://localhost:5000/translate?api-version=3.0&from=en&to=fr'
headers = { 'Content-Type': 'application/json' }
body = [{ 'text': 'Hello, how are you' }]

request = requests.post(url, headers=headers, json=body)
response = request.json()

print(json.dumps(
    response,
    sort_keys=True,
     indent=4,
     ensure_ascii=False,
     separators=(',', ': ')))
```

### <a name="translate-text-with-cnet-console-app"></a>C#/.NET 콘솔 앱을 사용하여 문자 번역

Visual Studio를 시작하고 새 콘솔 애플리케이션을 만듭니다. `*.csproj` 파일을 편집하여 `<LangVersion>7.1</LangVersion>` 노드를 추가한 다음, C# 7.1을 지정합니다. [Newtoonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 패키지 버전 11.0.2를 추가합니다.

`Program.cs`에서 기존 모드 코드를 다음으로 바꿉니다.

```csharp
using Newtonsoft.Json;
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace TranslateContainer
{
    class Program
    {
        const string ApiHostEndpoint = "http://localhost:5000";
        const string TranslateApi = "/translate?api-version=3.0&from=en&to=de";

        static async Task Main(string[] args)
        {
            var textToTranslate = "Sunny day in Seattle";
            var result = await TranslateTextAsync(textToTranslate);

            Console.WriteLine(result);
            Console.ReadLine();
        }

        static async Task<string> TranslateTextAsync(string textToTranslate)
        {
            var body = new object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            var client = new HttpClient();
            using (var request =
                new HttpRequestMessage
                {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                    Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                })
            {
                // Send the request and await a response.
                var response = await client.SendAsync(request);

                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="summary"></a>요약

이 문서에서 Translator 컨테이너를 다운로드, 설치 및 실행에 대한 개념과 워크플로를 알아보았습니다. 이제는 다음을 알고 있습니다.

* Translator는 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 컨테이너 레지스트리에서 다운로드되어 Docker에서 실행됩니다.
* 컨테이너의 호스트 URI를 지정하면 REST API를 사용하여 Translator 컨테이너에서 ‘번역’ 작업을 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Cognitive Services 컨테이너에 대해 자세히 알아보기](../../containers/index.yml?context=%2fazure%2fcognitive-services%2ftranslator%2fcontext%2fcontext)
