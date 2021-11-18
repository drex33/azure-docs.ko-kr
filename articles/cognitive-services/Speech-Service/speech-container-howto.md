---
title: Speech Service API용 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: Speech Service용 Docker 컨테이너를 사용하여 온-프레미스에서 음성 인식, 대화 내용 기록, 생성 등을 수행할 수 있습니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: eur
ms.custom: cog-serv-seo-aug-2020
keywords: 온-프레미스, Docker, 컨테이너
ms.openlocfilehash: 25063bd9a23f0d156719404e183c44fdc7b45051
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757554"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Speech Service API용 Docker 컨테이너 설치 및 실행 

컨테이너를 사용하면 사용자 환경에서 _일부_ Speech Service API를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 음성 컨테이너를 다운로드, 설치 및 실행하는 방법에 대해 알아봅니다.

음성 컨테이너는 고객이 강력한 클라우드 기능 및 에지 지역성 모두에 최적화된 음성 애플리케이션 아키텍처를 구축할 수 있도록 합니다. 클라우드 기반 Azure Speech Service와 동일한 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 사용하는 여러 컨테이너를 사용할 수 있습니다.

> [!IMPORTANT]
> 2021년 8월 31일에 표준 음성 합성 음성 및 텍스트 음성 대 음성 컨테이너를 사용 중지했습니다. 대신 신경망 텍스트 음성 대 음성 컨테이너를 사용하도록 애플리케이션을 마이그레이션하는 것이 좋습니다. 애플리케이션 업데이트에 대한 자세한 내용은 [다음 단계를 따르세요.](./text-to-speech.md#migrate-to-neural-voice)

| 컨테이너 | 기능 | 최신 | 릴리스 상태 |
|--|--|--|--|
| 음성 텍스트 변환 | 감정을 분석하고 연속적인 실시간 음성 또는 일괄 오디오 녹음을 기록하며 중간 결과를 제공합니다.  | 2.16.0 | 일반 공급 |
| 사용자 지정 음성 텍스트 변환 | [Custom Speech 포털](https://speech.microsoft.com/customspeech)의 사용자 지정 모델을 사용하여 연속적인 실시간 음성 또는 일괄 오디오 녹음을 텍스트로 기록하고 중간 결과를 제공합니다. | 2.16.0 | 일반 공급 |
| 텍스트 음성 변환 | 일반 텍스트 입력 또는 SSML(Speech Synthesis Markup Language)을 사용하여 텍스트를 자연스러운 음성으로 변환합니다. | 1.15.0 | 일반 공급 |
| 음성 언어 식별 | 오디오 파일에서 음성 언어를 감지합니다. | 1.5.0 | 미리 보기 |
| 인공신경망 텍스트 음성 변환 | 심층 신경망 기술을 사용하여 텍스트를 자연스러운 음성으로 변환함으로써 보다 자연스러운 합성 음성을 사용할 수 있도록 합니다. | 1.10.0 | 일반 공급 |

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> * 음성 컨테이너를 사용하려면 온라인 요청을 제출하고 승인을 받아야 합니다. 자세한 내용은 아래의 **컨테이너 실행에 대한 승인 요청** 섹션을 참조하세요. 
> * ‘일반 공급’ 컨테이너는 Microsoft의 안정성 및 지원 요구 사항을 충족합니다. ‘미리 보기’의 컨테이너는 개발 중입니다.

Speech Service 컨테이너를 사용하기 전에 다음 필수 조건을 충족해야 합니다. Azure 구독이 없는 경우 시작하기 전에 먼저 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

* 호스트 컴퓨터에 [Docker](https://docs.docker.com/)가 설치되어 있습니다. Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. 
    * Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.
    * [Docker 개념](https://docs.docker.com/get-started/overview/)에 대한 기본 지식이 있어야 합니다. 
* 무료(F0) 또는 표준(S) [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 사용하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices"  title="Speech Service 리소스 "  target="_blank">Speech Service 리소스</a>를 만듭니다.

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]


## <a name="host-computer-requirements-and-recommendations"></a>호스트 컴퓨터 요구 사항 및 권장 사항

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Speech 컨테이너의 최소 및 권장 리소스 할당에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 음성 텍스트 변환 | 2개 코어, 2GB 메모리 | 4개 코어, 4GB 메모리 |
| 사용자 지정 음성 텍스트 변환 | 2개 코어, 2GB 메모리 | 4개 코어, 4GB 메모리 |
| 텍스트 음성 변환 | 1개 코어, 2GB 메모리 | 2코어, 3GB 메모리 |
| 음성 언어 식별 | 1개 코어, 1GB 메모리 | 1개 코어, 1GB 메모리 |
| 인공신경망 텍스트 음성 변환 | 6개 코어, 12GB 메모리 | 8개 코어, 16GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!NOTE]
> 최소 및 권장 사항은 Docker 제한 기반을 두고 있으며 호스트 컴퓨터 리소스가 *아닙니다*. 예를 들어, 음성-텍스트 컨테이너는 큰 언어 모델의 맵 부분을 기억하며 추가로 4 ~ 6GB의 메모리에 전체 파일을 두는 것을 *권장* 합니다. 또한 모델은 메모리로 페이징되므로 두 컨테이너의 첫 번째 실행은 더 오래 걸릴 수 있습니다.

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트** 는 Docker 컨테이너를 실행하는 컴퓨터입니다. 호스트는 [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)(AVX2)를 *지원해야 합니다*. 다음 명령을 사용하여 Linux 호스트에서의 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 호스트 컴퓨터는 AVX2를 지원하는 데 *필요합니다.* AVX2가 제대로 지원되지 않으면 컨테이너가 제대로 *작동하지 않습니다.*

## <a name="request-approval-to-the-run-the-container"></a>컨테이너 실행에 대한 승인 요청

컨테이너에 대한 액세스를 요청하려면 [요청 양식](https://aka.ms/csgate)을 작성하여 제출하세요. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

음성용 컨테이너 이미지는 다음 Container Registry에서 사용할 수 있습니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 음성 텍스트 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[사용자 지정 음성 텍스트 변환](#tab/cstt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 사용자 지정 음성 텍스트 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 텍스트 음성 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[인공신경망 텍스트 음성 변환](#tab/ntts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 인공신경망 텍스트 음성 변환 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="speech-language-identification"></a>[음성 언어 식별](#tab/lid)

> [!TIP]
> 가장 유용한 결과를 얻으려면 음성 언어 식별 컨테이너를 음성 텍스트 변환 또는 사용자 지정 음성 텍스트 변환 컨테이너와 함께 사용하는 것이 좋습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 음성 언어 식별 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>음성 컨테이너용 docker pull

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>음성 텍스트 변환 컨테이너용 docker pull

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest` 태그는 `en-US` 로캘을 끌어옵니다. 추가 로캘은 [음성 텍스트 변환 로캘을 참조하세요.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>음성 텍스트 변환 로캘

`latest`를 제외한 모든 태그는 다음 형식이며 대/소문자를 구분합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
2.6.0-amd64-en-us
```

**음성 텍스트 변환** 컨테이너의 지원되는 모든 로캘은 [음성 텍스트 변환 이미지 태그](../containers/container-image-tags.md#speech-to-text)를 참조하세요.

# <a name="custom-speech-to-text"></a>[사용자 지정 음성 텍스트 변환](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>사용자 지정 음성 텍스트 변환 컨테이너용 docker pull

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> 사용자 지정 음성 컨테이너용 `locale` 및 `voice`는 컨테이너에서 수집한 사용자 지정 모델에 의해 결정됩니다.

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>텍스트 음성 변환 컨테이너용 docker pull

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 태그는 `en-US` 로캘 및 `ariarus` 음성을 끌어오기 합니다. 추가 로캘은 [텍스트 음성 변환 로캘](#text-to-speech-locales)을 참조하세요.

#### <a name="text-to-speech-locales"></a>텍스트 음성 변환 로캘

`latest`를 제외한 모든 태그는 다음 형식이며 대/소문자를 구분합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
1.8.0-amd64-en-us-ariarus
```

지원되는 모든 로캘 및 **텍스트 음성 변환** 컨테이너의 해당 음성은 [텍스트 음성 변환 이미지 태그](../containers/container-image-tags.md#text-to-speech)를 참조하세요.

> [!IMPORTANT]
> *텍스트 음성 변환* HTTP POST를 생성할 때 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md) 메시지에는 `name` 특성이 있는 `voice` 요소가 필요합니다. 이 값은 해당 컨테이너 로캘 및 음성이며 ["짧은 이름"](language-support.md#standard-voices)이라고도 합니다. 예를 들어 `latest` 태그에는 `en-US-AriaRUS`라는 음성 이름이 있습니다.

# <a name="neural-text-to-speech"></a>[인공신경망 텍스트 음성 변환](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>인공신경망 텍스트 음성 변환 컨테이너용 docker pull

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 태그는 `en-US` 로캘 및 `arianeural` 음성을 끌어오기 합니다. 추가 로캘은 [인공신경망 텍스트 음성 변환 로캘](#neural-text-to-speech-locales)을 참조하세요.

#### <a name="neural-text-to-speech-locales"></a>인공신경망 텍스트 음성 변환 로캘

`latest`를 제외한 모든 태그는 다음 형식이며 대/소문자를 구분합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

다음 태그는 해당 형식의 예입니다.

```
1.3.0-amd64-en-us-arianeural
```

지원되는 모든 로캘 및 **인공신경망 텍스트 음성 변환** 컨테이너의 해당 음성은 [인공신경망 텍스트 음성 변환 이미지 태그](../containers/container-image-tags.md#neural-text-to-speech)를 참조하세요.

> [!IMPORTANT]
> *인공신경망 텍스트 음성 변환* HTTP POST를 생성할 때 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md) 메시지에는 `name` 특성이 있는 `voice` 요소가 필요합니다. 이 값은 해당 컨테이너 로캘 및 음성이며 ["짧은 이름"](language-support.md#neural-voices)이라고도 합니다. 예를 들어 `latest` 태그에는 `en-US-AriaNeural`라는 음성 이름이 있습니다.

# <a name="speech-language-identification"></a>[음성 언어 식별](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-identification-container"></a>음성 언어 식별 컨테이너용 Docker pull

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#host-computer-requirements-and-recommendations)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](speech-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{Endpoint_URI}` 및 `{API_Key}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. `docker run` 명령의 추가 [예제](speech-container-configuration.md#example-docker-run-commands)도 볼 수 있습니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

표준 *음성 텍스트 변환* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *음성 텍스트 변환* 컨테이너를 실행합니다.
* 4개 CPU 코어 및 4GB(기가바이트) 메모리를 할당합니다.
* TCP 포트 5000을 노출하고 컨테이너의 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

> [!NOTE]
> 컨테이너는 GStreamer를 사용하여 음성 SDK에 압축 된 오디오 입력을 지원합니다.
> 컨테이너에 GStreamer를 설치하려면 [음성 SDK를 통해 코덱 압축 오디오 입력 사용](how-to-use-codec-compressed-audio-input-streams.md)에서 GStreamer에 대한 Linux 지침을 따릅니다.

#### <a name="diarization-on-the-speech-to-text-output"></a>음성 텍스트 변환 출력의 분리
분리는 기본적으로 사용됩니다. 응답에 분리를 구현하려면 `diarize_speech_config.set_service_property`를 사용합니다.

1. 구문 출력 형식을 `Detailed`로 설정합니다.
2. 분리 모드를 설정합니다. 지원되는 모드는 `Identity` 및 `Anonymous`입니다.
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> "ID" 모드는 `"SpeakerId": "Customer"` 또는 `"SpeakerId": "Agent"`를 반환합니다.
> "익명" 모드는 `"SpeakerId": "Speaker 1"` 또는 `"SpeakerId": "Speaker 2"`를 반환합니다.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>음성 텍스트 변환 출력의 감정 분석 
음성 텍스트 번역 컨테이너의 v2.6.0부터 미리 보기 엔드포인트 대신 언어 서비스 3.0 API 엔드포인트를 사용해야 합니다. 예를 들면 다음과 같습니다.
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> 언어 서비스 `v3.0` API는 이전 버전과 호환되지  `v3.0-preview.1` 않습니다. 최신 감정 기능 지원을 받으려면 음성 텍스트 컨테이너 이미지 및 언어 서비스 를 `v2.6.0` `v3.0` 사용합니다.

음성 텍스트 변환 컨테이너 v2.2.0부터 출력에서 [감정 분석 v3 API를](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 호출할 수 있습니다. 감정 분석을 호출하려면 언어 서비스 API 리소스 엔드포인트가 필요합니다. 다음은 그 예입니다. 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

클라우드에서 언어 서비스 엔드포인트에 액세스하는 경우 키가 필요합니다. 언어 서비스 기능을 로컬로 실행하는 경우 이를 제공하지 않아도 될 수 있습니다.

키와 엔드포인트는 다음 예제와 같이 음성 컨테이너에 인수로 전달됩니다.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

이 명령은 다음을 수행합니다.

* 위의 명령과 동일한 단계를 수행합니다.
* 감정 분석 요청을 보내기 위한 언어 서비스 API 엔드포인트 및 키를 저장합니다. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>음성 텍스트 변환 출력의 Phraselist v2 

음성 텍스트 변환 컨테이너 v2.6.0부터 전체 문장 또는 중간에 있는 구문과 같은 고유한 구문이 포함된 출력을 얻을 수 있습니다. 예를 들면 다음 문장의 *the tall man* 입니다.

* "This is a sentence **the tall man** this is another sentence."

구문 목록을 구성하려면 호출할 때 고유한 구문을 추가해야 합니다. 예를 들면 다음과 같습니다.

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

추가할 구문이 여러 개 있는 경우 각 구문에 대해 `.addPhrase()`를 호출하여 구문 목록에 추가합니다. 


# <a name="custom-speech-to-text"></a>[사용자 지정 음성 텍스트 변환](#tab/cstt)

*사용자 지정 음성 텍스트 변환* 컨테이너는 사용자 지정 음성 모델을 사용합니다. 사용자 지정 모델은 [사용자 지정 음성 포털](how-to-custom-speech-train-model.md)을 사용하여 [학습](https://speech.microsoft.com/customspeech)시켜야 합니다.

컨테이너를 실행하려면 사용자 지정 음성 **모델 ID** 가 필요합니다. 이는 사용자 지정 음성 포털의 **학습** 페이지에서 찾을 수 있습니다. 사용자 지정 음성 포털에서 **학습** 페이지로 이동하고 모델을 선택합니다.
<br>

![사용자 지정 음성 학습 페이지](media/custom-speech/custom-speech-model-training.png)

`docker run` 명령의 `ModelId` 매개 변수에 대한 인수로 사용할 **모델 ID를** 가져옵니다.
<br>

![사용자 지정 음성 모델 세부 정보](media/custom-speech/custom-speech-model-details.png)

다음 표에는 다양한 `docker run` 매개 변수와 해당하는 설명이 나와 있습니다.

| 매개 변수 | 설명 |
|---------|---------|
| `{VOLUME_MOUNT}` | docker에서 사용자 지정 모델을 유지하는 데 사용하는 호스트 컴퓨터 [볼륨 탑재](https://docs.docker.com/storage/volumes/)입니다. 예를 들면 *C:\CustomSpeech* 입니다. 여기서 *C 드라이브* 는 호스트 컴퓨터에 있습니다. |
| `{MODEL_ID}` | 사용자 지정 음성 포털의 **학습** 페이지에서 Custom Speech **모델 ID** 입니다. |
| `{ENDPOINT_URI}` | 이 엔드포인트는 측정 및 청구에 필요합니다. 자세한 내용은 [필수 매개 변수 모음](#gathering-required-parameters)을 참조하세요. |
| `{API_KEY}` | API 키가 필요합니다. 자세한 내용은 [필수 매개 변수 모음](#gathering-required-parameters)을 참조하세요. |

*사용자 지정 음성 텍스트 변환* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *사용자 지정 음성 텍스트 변환* 컨테이너를 실행합니다.
* 4개 CPU 코어 및 4GB(기가바이트) 메모리를 할당합니다.
* 볼륨 입력 탑재에서 *사용자 지정 음성 텍스트 변환* 모델을 로드합니다(예: *C:\CustomSpeech*).
* TCP 포트 5000을 노출하고 컨테이너의 의사-TTY를 할당합니다.
* `ModelId`가 지정된 모델을 다운로드합니다(볼륨 탑재에서 찾을 수 없는 경우).
* 사용자 지정 모델이 이전에 다운로드된 경우에는 `ModelId`가 무시됩니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>사용자 지정 음성 텍스트 변환 컨테이너에서 기본 모델 다운로드  
사용자 지정 음성 텍스트 변환 컨테이너의 v2.6.0부터 `BaseModelLocale=<locale>` 옵션을 사용하여 사용 가능한 기본 모델 정보를 가져올 수 있습니다. 이 옵션은 해당 로캘에서 청구 계정에서 사용 가능한 기본 모델 목록을 제공합니다. 예를 들면 다음과 같습니다.

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *사용자 지정 음성 텍스트 변환* 컨테이너를 실행합니다.
* 대상 로캘의 사용 가능한 기본 모델을 확인하고 반환합니다.

정보 로캘, 모델 ID 및 생성 날짜 시간이 있는 기본 모델 목록이 출력에 제공됩니다. 모델 ID를 사용하여 원하는 특정 기본 모델을 다운로드하고 사용할 수 있습니다. 예를 들면 다음과 같습니다.
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>사용자 지정 음성 텍스트 변환 컨테이너의 사용자 지정 발음 
사용자 지정 음성 텍스트 변환 컨테이너의 v2.5.0부터 출력에서 사용자 지정 발음 결과를 얻을 수 있습니다. 사용자 지정 모델에 고유한 사용자 지정 발음 규칙을 설정하고 모델을 사용자 지정 음성 텍스트 변환 컨테이너에 탑재하기만 하면 됩니다.


# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

표준 *텍스트 음성 변환* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 표준 *텍스트 음성 변환* 컨테이너를 실행합니다.
* 1개 CPU 코어 및 2GB(기가바이트) 메모리를 할당합니다.
* TCP 포트 5000을 노출하고 컨테이너의 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="neural-text-to-speech"></a>[인공신경망 텍스트 음성 변환](#tab/ntts)

*인공신경망 텍스트 음성 변환* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *인공신경망 텍스트 음성 변환* 컨테이너를 실행합니다.
* 6개 CPU 코어 및 12GB(기가바이트) 메모리를 할당합니다.
* TCP 포트 5000을 노출하고 컨테이너의 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="speech-language-identification"></a>[음성 언어 식별](#tab/lid)

*음성 언어 식별* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다. 

* 컨테이너 이미지에서 음성 언어 감지 컨테이너를 실행합니다. 현재 이 이미지 실행에 대한 요금은 청구되지 않습니다.
* 1개 CPU 코어 및 1GB(기가바이트) 메모리를 할당합니다.
* 5003 TCP 포트 표시 및 컨테이너에 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

음성 텍스트 변환 컨테이너를 사용하여 이 컨테이너를 실행하려면 이 [Docker 이미지](https://hub.docker.com/r/antsu/on-prem-client)를 사용하면 됩니다. 두 컨테이너가 모두 시작된 후 이 Docker Run 명령을 사용하여 `speech-to-text-with-languagedetection-client`를 실행합니다.

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> 동시 호출 수를 늘리면 안정성 및 대기 시간에 영향을 줄 수 있습니다. 언어 식별의 경우 CPU 1개와 1GB 메모리를 사용한 최대 4개의 동시 호출을 권장합니다. CPU 2개와 2GB 메모리를 사용하는 호스트의 경우 최대 6개의 동시 호출을 권장합니다.

***

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

> [!NOTE]
> 여러 컨테이너를 실행하는 경우 고유한 포트 번호를 사용합니다.

| 컨테이너 | SDK 호스트 URL | 프로토콜 |
|--|--|--|
| 표준 음성 텍스트 변환 및 사용자 지정 음성 텍스트 변환 | `ws://localhost:5000` | WS |
| 텍스트 음성 변환(표준, 인공신경망 포함), 음성 언어 식별 | `http://localhost:5000` | HTTP |

WSS 및 HTTPS 프로토콜을 사용하는 방법에 대한 자세한 내용은 [컨테이너 보안](../cognitive-services-container-support.md#azure-cognitive-services-container-security)을 참조하세요.

### <a name="speech-to-text-standard-and-custom"></a>음성 텍스트 변환(표준 및 사용자 지정)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>감정 분석

[컨테이너에](#analyze-sentiment-on-the-speech-to-text-output)언어 서비스 API 자격 증명을 제공한 경우 speech SDK를 사용 하 여 감정 분석을 통해 음성 인식 요청을 보낼 수 있습니다. *단순* 또는 *상세* 형식을 사용하도록 API 응답을 구성할 수 있습니다.
> [!NOTE]
> Speech Service Python SDK의 v1.13에는 감정 분석과 관련된 문제가 있습니다. Speech Service Python SDK에서 감정 분석을 사용하는 경우에는 v1.12.x 또는 이전 버전을 사용하세요.

# <a name="simple-format"></a>[단순 형식](#tab/simple-format)

단순 형식을 사용하도록 음성 클라이언트를 구성하려면 `"Sentiment"`에 `Simple.Extensions`를 값으로 추가합니다. 특정 언어 서비스 모델 버전을 선택 하려면 `'latest'` 속성 구성에서를 대체 합니다 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`는 응답의 루트 계층에 감정 결과를 반환합니다.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[상세 형식](#tab/detailed-format)

상세 형식을 사용하도록 음성 클라이언트를 구성하려면 `Detailed.Extensions`, `Detailed.Options` 또는 둘 다에 `"Sentiment"`를 값으로 추가합니다. 특정 감정 분석 모델 버전을 선택 하려면 `'latest'` 속성 구성에서를 대체 합니다 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` .

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`는 응답의 루트 계층에 감정 결과를 제공합니다. `Detailed.Options`는 응답의 `NBest` 계층에 결과를 제공합니다. 개별적으로 또는 함께 사용할 수 있습니다.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

감정 분석을 완전히 사용하지 않도록 설정하려면 `false`에 `sentimentanalysis.enabled` 값을 추가합니다.

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-and-neural"></a>텍스트 음성 변환(표준 및 인공신경망)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.

[!INCLUDE [Validate container is running - Container API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

컨테이너를 시작하거나 실행할 때 문제가 발생할 수 있습니다. 출력 [탑재](speech-container-configuration.md#mount-settings)를 사용하고 로깅을 사용하도록 설정합니다. 이렇게 하면 컨테이너에서 문제를 해결할 때 도움이 되는 로그 파일을 생성할 수 있습니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

[!INCLUDE [Diagnostic container](../containers/includes/diagnostics-container.md)]


## <a name="billing"></a>결제

음성 컨테이너는 Azure 계정의 *음성* 리소스를 사용하여 청구 정보를 Azure로 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](speech-container-configuration.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 음성 컨테이너 다운로드, 설치 및 실행에 대한 개념 및 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* 음성은 다양한 기능을 캡슐화하는 네 가지 Linux 컨테이너를 Docker에 제공합니다.
  * *음성 텍스트 변환*
  * *사용자 지정 음성 텍스트 변환*
  * *텍스트 음성 변환*
  * *사용자 지정 텍스트 음성 변환*
  * *인공신경망 텍스트 음성 변환*
  * *음성 언어 식별*
* 컨테이너 이미지는 Azure의 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API(텍스트 음성 변환 전용) 또는 SDK(음성 텍스트 변환 또는 텍스트 음성 변환)를 사용하는지 여부에 관계없이 컨테이너의 호스트 URI를 지정합니다. 
* 컨테이너를 인스턴스화할 때 청구 정보를 제공해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](speech-container-configuration.md)에서 구성 설정 검토
* [Kubernetes 및 Helm을 지원하는 Speech Service 컨테이너를 사용](speech-container-howto-on-premises.md)하는 방법 알아보기
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용
