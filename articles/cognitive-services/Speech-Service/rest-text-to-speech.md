---
title: Text-to-Speech API 참조(REST) - Speech Service
titleSuffix: Azure Cognitive Services
description: Text-to-Speech REST API를 사용하는 방법에 대해 알아봅니다. 이 문서에서는 권한 부여 옵션, 쿼리 옵션, 요청을 구성하고 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: eur
ms.custom: references_regions
ms.openlocfilehash: bb21358418b30a45bc0d9578d49d025cc4d1a408
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503831"
---
# <a name="text-to-speech-rest-api"></a>Text-to-Speech REST API

Speech Service를 사용하면 [텍스트를 합성된 음성으로 변환](#convert-text-to-speech)하고 REST API 세트를 사용하여 지역에 대해 [지원되는 음성 목록을 가져올](#get-a-list-of-voices) 수 있습니다. 사용 가능한 각 엔드포인트는 지역과 연결됩니다. 사용하려는 엔드포인트/지역에 대한 구독 키가 필요합니다.

텍스트를 음성으로 변환 REST API는 인공신경망 및 표준 텍스트를 음성으로 보이스를 지원하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다.

* 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.
* 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#text-to-speech)을 참조하세요.

> [!IMPORTANT]
> 비용은 표준, 사용자 지정 및 인공신경망 음성별로 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

이 API를 사용하기 전에 다음을 이해해야 합니다.

* Text-to-Speech REST API에는 인증 헤더가 필요합니다. 즉, 서비스에 액세스하기 위해 토큰 교환을 완료해야 합니다. 자세한 내용은 [인증](#authentication)을 참조하세요.

> [!TIP]
> Azure Government 및 Azure 중국 엔드포인트는 [이 문서](sovereign-clouds.md)를 참조하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>음성 목록 가져오기

`voices/list` 엔드포인트를 사용하면 특정 지역/엔드포인트에 대한 전체 음성 목록을 가져올 수 있습니다.

### <a name="regions-and-endpoints"></a>지역 및 엔드포인트

| 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 브라질 남부 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 캐나다 중부 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 중부 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 동아시아 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 동부 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 동부 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 프랑스 중부 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 인도 중부 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 일본 동부 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 한국 중부 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 중북부 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 북유럽 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 남아프리카 북부 | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 중남부 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 동남아시아 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 영국 남부 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 중서부 | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 서유럽 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 서부 | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 미국 서부 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [미리 보기로 제공되는 음성](language-support.md#neural-voices-in-preview)은 미국 동부, 서유럽 및 동남 아시아의 3개 지역에서만 사용할 수 있습니다.

### <a name="request-headers"></a>요청 헤더

이 표에는 텍스트 음성 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나열되어 있습니다.

| 헤더 | Description | 필수/선택 |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Speech Service 구독 키입니다. | 이 헤더 또는 `Authorization`가 필요합니다. |
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 이 헤더 또는 `Ocp-Apim-Subscription-Key`가 필요합니다. |



### <a name="request-body"></a>요청 본문

이 엔드포인트에 대한 `GET` 요청에는 본문이 필요하지 않습니다.

### <a name="sample-request"></a>샘플 요청

이 요청에는 권한 부여 헤더가 필요합니다.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>샘플 응답

이 응답은 응답의 구조를 설명하기 위해 잘렸습니다.

> [!NOTE]
> 음성 가용성은 지역/엔드포인트에 따라 다릅니다.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)",
    "DisplayName": "Christopher",
    "LocalName": "Christopher",
    "ShortName": "en-US-ChristopherNeural",
    "Gender": "Male",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

    ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, JennyMultilingualNeural)",
    "ShortName": "en-US-JennyMultilingualNeural",
    "DisplayName": "Jenny Multilingual",
    "LocalName": "Jenny Multilingual",
    "Gender": "Female",
    "Locale": "en-US",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "SecondaryLocaleList": [
        "de-DE",
        "en-AU",
        "en-CA",
        "en-GB",
        "es-ES",
        "es-MX",
        "fr-CA",
        "fr-FR",
        "it-IT",
        "ja-JP",
        "ko-KR",
        "pt-BR",
        "zh-CN"
      ],
    "Status": "Preview"
    },
    
  ...
    
    {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | Description | 가능한 원인 |
|------------------|-------------|-----------------|
| 200 | 확인 | 요청이 성공했습니다. |
| 400 | 잘못된 요청 | 필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다. |
| 401 | 권한 없음 | 요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요. |
| 429 | 너무 많은 요청 | 구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다. |
| 502 | 잘못된 게이트웨이    | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다. |


## <a name="convert-text-to-speech"></a>텍스트 음성 변환

`v1` 엔드포인트를 사용하면 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)을 사용하여 텍스트를 음성으로 변환할 수 있습니다.

### <a name="regions-and-endpoints"></a>지역 및 엔드포인트

이러한 지역은 REST API를 사용한 텍스트 음성 변환에 대해 지원됩니다. 사용자 구독 지역과 일치하는 엔드포인트를 선택해야 합니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>요청 헤더

이 표에는 텍스트 음성 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나열되어 있습니다.

| 헤더 | Description | 필수/선택 |
|--------|-------------|---------------------|
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 필수 |
| `Content-Type` | 제공된 텍스트의 콘텐츠 형식을 지정합니다. 허용되는 값: `application/ssml+xml`. | 필수 |
| `X-Microsoft-OutputFormat` | 오디오 출력 형식을 지정합니다. 허용되는 값의 전체 목록은 [오디오 출력](#audio-outputs)을 참조하세요. | 필수 |
| `User-Agent` | 애플리케이션 이름입니다. 값은 255자 미만이어야 합니다. | 필수 |

### <a name="audio-outputs"></a>오디오 출력

각 요청에서 `X-Microsoft-OutputFormat` 헤더로 전송되는 지원되는 오디오 형식 목록입니다. 각 항목에 전송률 및 인코딩 형식이 포함됩니다. Speech Service는 24kHz, 16kHz 및 8kHz 오디오 출력을 지원합니다.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> 선택한 음성 및 출력 형식의 비트 전송률이 다른 경우 필요에 오디오가 다시 샘플링됩니다.
> ogg-24khz-16bit-mono-opus는 [opus codec](https://opus-codec.org/downloads/)으로 디코딩할 수 있습니다.

### <a name="request-body"></a>요청 본문

각 `POST` 요청의 본문은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 전송됩니다. SSML에서는 텍스트 음성 변환 서비스에서 반환한 합성된 음성의 목소리와 언어를 선택할 수 있습니다. 지원되는 목소리의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

> [!NOTE]
> 사용자 지정 목소리를 사용하는 경우에는 요청 본문을 일반 텍스트(ASCII 또는 UTF-8)로 전송할 수 있습니다.

### <a name="sample-request"></a>샘플 요청

이 HTTP 요청은 SSML을 사용하여 음성 및 언어를 지정합니다. 본문 길이가 길고 결과 오디오가 10분을 초과하면 10분으로 잘립니다. 즉, 오디오 길이는 10분을 초과할 수 없습니다.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Male'
    name='en-US-ChristopherNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | Description | 가능한 원인 |
|------------------|-------------|-----------------|
| 200 | 확인 | 요청이 성공했습니다. 응답 본문이 오디오 파일입니다. |
| 400 | 잘못된 요청 | 필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다. |
| 401 | 권한 없음 | 요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요. |
| 415 | 지원되지 않는 미디어 유형 | 잘못된 `Content-Type`이 제공되었을 수 있습니다. `Content-Type`는 `application/ssml+xml`로 설정되어야 합니다. |
| 429 | 너무 많은 요청 | 구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다. |
| 502 | 잘못된 게이트웨이    | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다. |

HTTP 상태가 `200 OK`인 경우 응답 본문은 요청된 형식으로 오디오 파일을 포함합니다. 이 파일은 전송 시 재생하거나, 버퍼에 저장하거나, 파일에 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
- [긴 형식의 오디오를 위한 비동기 합성](./long-audio-api.md)
- [사용자 지정 신경망 음성 시작](how-to-custom-voice.md)
