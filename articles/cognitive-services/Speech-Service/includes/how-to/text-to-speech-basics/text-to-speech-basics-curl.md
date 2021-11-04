---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 2272fbe18ac4d2241618f650fd3dab223ef242ee
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510832"
---
이 빠른 시작에서는 Speech Service 및 cURL을 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다.

텍스트 음성 변환 개념에 대한 간략한 설명은 [개요](../../../text-to-speech.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>텍스트 음성 변환

명령 프롬프트에서 다음 명령을 실행합니다. 명령에 다음 값을 삽입해야 합니다.
- Speech Service 구독 키입니다.
- Speech Service 지역입니다.

다음 값을 변경할 수도 있습니다.
- 오디오 출력 형식을 제어하는 `X-Microsoft-OutputFormat` 헤더 값입니다. 지원되는 오디오 출력 형식 목록은 [텍스트 음성 변환 REST API 참조](../../../rest-text-to-speech.md#audio-outputs)에서 찾을 수 있습니다.
- 출력 음성입니다. 음성 엔드포인트에 사용할 수 있는 음성 목록을 가져오려면 다음 섹션을 참조하세요.
- 출력 파일입니다. 이 예제에서는 서버의 응답을 `output.wav`라는 파일로 보냅니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>음성 엔드포인트에 사용 가능한 음성 나열

음성 엔드포인트에 사용할 수 있는 음성을 나열하려면 다음 명령을 실행합니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

다음과 같은 응답을 받게 됩니다.

```http
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "DisplayName": "Hoda",
        "LocalName": "هدى",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "DisplayName": "Naayf",
        "LocalName": "نايف",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "DisplayName": "Ivan",
        "LocalName": "Иван",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        // This response is truncated. The response will include 
        // a complete list of supported languages and specific 
        // details like short name, gender, etc. 
    }
]
```
