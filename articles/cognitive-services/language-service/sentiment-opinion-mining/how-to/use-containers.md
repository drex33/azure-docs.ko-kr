---
title: 감정 분석 용 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: 감정 분석 API에 대 한 Docker 컨테이너를 사용 하 여 온-프레미스 감정 분석과 같은 자연어 처리를 수행 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/29/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
keywords: 온-프레미스, Docker, 컨테이너, 감정 분석, 자연어 처리
ms.openlocfilehash: c29a1c6e609b7c4f32eecf87813a0fb1a268bab5
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133270855"
---
# <a name="install-and-run-sentiment-analysis-containers"></a>감정 분석 컨테이너 설치 및 실행

컨테이너를 사용 하면 사용자의 인프라에서 감정 분석 API를 호스트할 수 있습니다. 감정 분석를 원격으로 호출 하 여 수행할 수 없는 보안 또는 데이터 거 버 넌 스 요구 사항이 있는 경우 컨테이너가 좋은 옵션 일 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

감정 분석 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

* 호스트 컴퓨터에 [Docker](https://docs.docker.com/)가 설치되어 있습니다. Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. 
    * Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.
    * [Docker 개념](https://docs.docker.com/get-started/overview/)에 대한 기본 지식이 있어야 합니다. 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> 무료 (F0) 또는 표준 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 사용 하는 언어 리소스 언어 리소스를 만듭니다.

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>호스트 컴퓨터 요구 사항 및 권장 사항

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

다음 표에서는 사용 가능한 컨테이너의 최소 및 권장 사양을 설명 합니다. 각 CPU 코어는 속도가 2.6GHz 이상이어야 합니다. 허용되는 TPS(초당 트랜잭션 수)도 나열됩니다.

|  | 최소 호스트 사양 | 권장 호스트 사양 | 최소 TPS | 최대 TPS|
|---|---------|-------------|--|--|
| **감정 분석**   | 1개 코어, 2GB 메모리 | 4개 코어, 8GB 메모리 |15 | 30|

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 CPU 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

감정 분석 컨테이너 v3 컨테이너는 여러 언어로 제공됩니다. 영어 컨테이너용 컨테이너를 다운로드하려면 아래 명령을 사용하세요. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

다른 언어용 컨테이너를 다운로드 하려면를 `3.0-en` 아래의 이미지 태그 중 하나로 바꿉니다. 

| 감정 분석 컨테이너 | 이미지 태그 |
|--|--|
| 중국어 간체    |   `3.0-zh-hans`   |
| 중국어 번체   |   `3.0-zh-hant`   |
| 네덜란드어                 |     `3.0-nl`      |
| 영어               |     `3.0-en`      |
| 프랑스어                |     `3.0-fr`      |
| 독일어                |     `3.0-de`      |
| 힌디어                 |    `3.0-hi`       |
| 이탈리아어               |     `3.0-it`      |
| 일본어              |     `3.0-ja`      |
| 한국어                |     `3.0-ko`      |
| 노르웨이어(복말)   |     `3.0-no`      |
| 포르투갈어(브라질)   |    `3.0-pt-BR`    |
| 포르투갈어(포르투갈) |    `3.0-pt-PT`    |
| 스페인어               |     `3.0-es`      |
| 터키어               |     `3.0-tr`      |

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

컨테이너가 호스트 컴퓨터에 있으면 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. 컨테이너는 중지할 때까지 계속 실행됩니다.

> [!IMPORTANT]
> * 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
> * 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

감정 분석 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` . 다음 자리 표시자를 고유한 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | 언어 리소스의 키입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | API에 액세스 하기 위한 끝점입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_TAG}** | 실행 하려는 컨테이너의 언어를 나타내는 이미지 태그입니다. 이 `docker pull` 명령이 사용한 명령과 일치 하는지 확인 합니다. | `3.0-en` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:{IMAGE_TAG} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 감정 분석 컨테이너 실행
* 1개 CPU 코어 및 8GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](../../concepts/configure-containers.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

감정 분석 컨테이너는 Azure 계정의 _언어_ 리소스를 사용 하 여 azure로 청구 정보를 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](../../concepts/configure-containers.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 감정 분석 컨테이너를 다운로드, 설치 및 실행 하기 위한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* Docker 용 Linux 컨테이너를 제공 하 감정 분석
* 컨테이너 이미지는 Microsoft 컨테이너 레지스트리(MCR)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정은 [컨테이너 구성](../../concepts/configure-containers.md)을 참조하세요.
