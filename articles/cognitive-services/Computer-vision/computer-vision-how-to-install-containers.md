---
title: Computer Vision에서 Read OCR Docker 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: Computer Vision에서 Read OCR Docker 컨테이너를 사용하여 온-프레미스 이미지와 문서에서 텍스트를 추출합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: 온-프레미스, OCR, Docker, 컨테이너
ms.openlocfilehash: bfe2bbe061f92c4290e63d36e8dcd647b4f7f830
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131078012"
---
# <a name="install-read-ocr-docker-containers"></a>Read OCR Docker 컨테이너 설치

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

컨테이너를 사용하면 사용자 환경에서 Computer Vision API를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하는 방법에 대해 알아봅니다.

*Read* OCR 컨테이너를 사용하면 JPEG, PNG, BMP, PDF 및 TIFF 파일 형식에 대한 지원이 포함된 이미지와 문서로부터 인쇄된 텍스트 및 필기 텍스트를 추출할 수 있습니다. 자세한 내용은 [Read API 방법 가이드](Vision-API-How-to-Topics/call-read-api.md)를 참조하세요.

## <a name="read-32-container"></a>Read 3.2 컨테이너

Read 3.2 OCR 컨테이너는 다음을 제공합니다.
* 향상된 정확도를 위한 새 모델.
* 동일한 문서 내에서 여러 언어 지원.
* 총 73개의 언어 지원. [OCR 지원 언어](./language-support.md#optical-character-recognition-ocr)의 전체 목록을 참조하세요.
* 문서와 이미지 모두에 대한 단일 작업.
* 더 큰 문서와 이미지 지원.
* 신뢰도 점수.
* 인쇄 및 필기 텍스트를 모두 포함하는 문서 지원.
* 문서에서 선택한 페이지에서만 텍스트를 추출하는 기능.
* 기본값으로 라틴어 전용으로 텍스트 줄 출력 순서를 보다 자연스러운 읽기 순서로 선택 가능.
* 텍스트 줄 분류를 필기 스타일 또는 라틴어 전용 아님으로 사용 가능.

지금 Read 2.0 컨테이너를 사용하는 경우 새 버전의 변경 내용에 대해 알아보려면 [마이그레이션 가이드](read-container-migration-guide.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

컨테이너를 사용하려면 먼저 다음 사전 요구 사항을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/install/#server)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows** 에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Computer Vision 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Computer Vision** 리소스 및 관련 API 키는 엔드포인트 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 제공되며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : **키** 페이지에 제공된 두 개의 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공된 엔드포인트|

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="request-approval-to-run-the-container"></a>컨테이너 실행에 대한 승인 요청

컨테이너 실행에 대한 승인을 요청하려면 [요청 양식](https://aka.ms/csgate)을 작성하여 제출하세요. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트** 컴퓨터는 Docker 컨테이너를 실행하는 컴퓨터입니다. 호스트는 [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)(AVX2)를 *지원해야 합니다*. 다음 명령을 사용하여 Linux 호스트에서의 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> 호스트 컴퓨터는 AVX2를 지원하는 데 *필요합니다.* AVX2가 제대로 지원되지 않으면 컨테이너가 제대로 *작동하지 않습니다.*

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

읽기에 대한 컨테이너 이미지를 사용할 수 있습니다.

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|-----------|------------|
| Read 2.0 미리 보기 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Read 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 이미지를 다운로드합니다.

### <a name="docker-pull-for-the-read-ocr-container"></a>Read OCR 컨테이너에 대한 Docker pull

# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[버전 2.0 미리 보기](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](computer-vision-resource-container-config.md)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.

# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Read OCR 컨테이너를 실행합니다.
* 8코어 CPU 및 18GB(기가바이트) 메모리를 할당합니다.
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

또는 환경 변수를 사용하여 컨테이너를 실행할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
--env Eula=accept \
--env Billing={ENDPOINT_URI} \
--env ApiKey={API_KEY} \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[버전 2.0 미리 보기](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Read OCR 컨테이너를 실행합니다.
* 8코어 CPU 및 16GB(기가바이트) 메모리를 할당합니다.
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

또는 환경 변수를 사용하여 컨테이너를 실행할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
--env Eula=accept \
--env Billing={ENDPOINT_URI} \
--env ApiKey={API_KEY} \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```
---


`docker run` 명령의 자세한 [예제](./computer-vision-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

더 높은 처리량(예: 다중 페이지 파일을 처리할 때)이 필요한 경우 [Azure Storage](../../storage/common/storage-account-create.md) 및 [Azure Queue](../../storage/queues/storage-queues-introduction.md)를 사용하여 [Kubernetes 클러스터에](deploy-computer-vision-on-premises.md) 여러 컨테이너를 배포하는 것이 좋습니다.

Azure Storage를 사용하여 처리할 이미지를 저장하는 경우 컨테이너를 호출할 때 사용할 [연결 문자열](../../storage/common/storage-configure-connection-string.md)을 만들 수 있습니다.

연결 문자열 찾는 방법:

1. Azure Portal의 **Storage 계정** 으로 이동하여 계정을 찾습니다.
2. 왼쪽 탐색 목록에서 **액세스 키** 를 클릭합니다.
3. 연결 문자열은 **연결 문자열** 아래에 있습니다.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다. `http://localhost:5000/swagger/vision-v3.2-read/swagger.json`에서 Swagger 경로를 볼 수 있습니다.

# <a name="version-20-preview"></a>[버전 2.0 미리 보기](#tab/version-2)

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다. `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json`에서 Swagger 경로를 볼 수 있습니다.

---

### <a name="asynchronous-read"></a>비동기 읽기


# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

Computer Vision 서비스에서 해당하는 REST 작업을 사용하는 방법과 비슷하게, `POST /vision/v3.2/read/analyze` 및 `GET /vision/v3.2/read/operations/{operationId}` 작업을 함께 사용하여 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 HTTP GET 요청에 대한 식별자로 사용되는 `operationId`을 반환합니다.


Swagger UI에서 `Analyze`을 선택하여 브라우저에서 확장합니다. **그런 다음** > **파일 선택** 을 선택합니다. 이 예제에서는 다음 이미지를 사용합니다.

![탭 대 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행되면 **HTTP 202** 상태 코드를 반환합니다. 응답의 일부로 요청에 대한 결과 엔드포인트를 포함하는 `operation-location` 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`은 정규화된 URL이며 HTTP GET을 통해 액세스됩니다. 이전 이미지에서 `operation-location` URL을 실행하여 얻은 JSON 응답은 다음과 같습니다.

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[버전 2.0 미리 보기](#tab/version-2)

Computer Vision 서비스에서 해당하는 REST 작업을 사용하는 방법과 비슷하게 `POST /vision/v2.0/read/core/asyncBatchAnalyze` 및 `GET /vision/v2.0/read/operations/{operationId}` 작업을 함께 사용하여 이미지를 비동기적으로 읽을 수 있습니다. 비동기 POST 메서드는 HTTP GET 요청에 대한 식별자로 사용되는 `operationId`을 반환합니다.

Swagger UI에서 `asyncBatchAnalyze`을 선택하여 브라우저에서 확장합니다. **그런 다음** > **파일 선택** 을 선택합니다. 이 예제에서는 다음 이미지를 사용합니다.

![탭 대 공백](media/tabs-vs-spaces.png)

비동기 게시물이 성공적으로 실행되면 **HTTP 202** 상태 코드를 반환합니다. 응답의 일부로 요청에 대한 결과 엔드포인트를 포함하는 `operation-location` 헤더가 있습니다.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`은 정규화된 URL이며 HTTP GET을 통해 액세스됩니다. 이전 이미지에서 `operation-location` URL을 실행하여 얻은 JSON 응답은 다음과 같습니다.

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> 예를 들어 Docker Compose 또는 Kubernetes에서 부하 분산 장치 뒤에 여러 Read OCR 컨테이너를 배포하는 경우 외부 캐시가 있어야 합니다. 처리 컨테이너와 GET 요청 컨테이너는 다를 수 있기 때문에 외부 캐시는 결과를 저장하고 컨테이너 간에 결과를 공유합니다. 캐시 설정에 대한 자세한 내용은 [Computer Vision Docker 컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요.

### <a name="synchronous-read"></a>동기 읽기

다음 작업을 사용하여 이미지를 동기적으로 읽을 수 있습니다. 

# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[버전 2.0 미리 보기](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

이미지를 완전히 다 읽어야만 API가 JSON 응답을 반환합니다. 유일한 예외는 오류가 발생하는 경우입니다. 오류가 발생하면 다음 JSON이 반환됩니다.

```json
{
    "status": "Failed"
}
```

JSON 응답 개체에는 비동기 버전과 동일한 개체 그래프가 있습니다. JavaScript 사용자이며 형식 안전성이 필요한 경우 TypeScript를 사용하여 JSON 응답을 캐스팅하는 것이 좋습니다.

예제 사용 사례는 <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">여기서 TypeScript 샌드박스</a>를 참조하고 **실행** 을 선택하여 사용 편의성을 시각화합니다.

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./computer-vision-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

[!INCLUDE [Diagnostic container](../containers/includes/diagnostics-container.md)]

## <a name="billing"></a>결제

Cognitive Services 컨테이너는 Azure 계정의 해당 리소스를 사용하여 청구 정보를 Azure로 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./computer-vision-resource-container-config.md)을 참조하세요. 

## <a name="summary"></a>요약

이 문서에서는 Computer Vision 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Computer Vision은 읽기를 캡슐화하는 Docker용 Linux 컨테이너를 제공합니다.
* 읽기 컨테이너 이미지를 실행하려면 애플리케이션이 필요합니다. 
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Read OCR 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [OCR 개요](overview-ocr.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.
* [Read API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.yml)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
