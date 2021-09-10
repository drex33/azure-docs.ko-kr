---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 95f69f04ec1aa0fb0c5588647709d8d938ad2a53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441373"
---
### <a name="neural-and-standard-voices"></a>인공신경망 및 표준 음성

이 표를 사용하여 지역/엔드포인트별로 **인공신경망 및 표준 음성의 가용성** 을 확인할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| 브라질 남부 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| 캐나다 중부 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 중부 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 동아시아 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 동부 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 동부 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| 프랑스 중부 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 인도 중부 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 일본 동부 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| 일본 서부 | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| 한국 중부 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 중북부 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 북유럽 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 중남부 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 동남아시아 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 영국 남부 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 중서부 | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 서유럽 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 서부 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 미국 서부 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [미리 보기로 제공되는 음성](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview)은 미국 동부, 서유럽 및 동남 아시아의 3개 지역에서만 사용할 수 있습니다.

### <a name="custom-voices"></a>사용자 지정 음성

사용자 지정 음성 글꼴을 만든 경우 사용자가 만든 엔드포인트를 사용합니다. 아래에 나열된 엔드포인트를 사용하여 `{deploymentId}`를 음성 모델의 배포 ID로 바꿀 수도 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 오스트레일리아 동부 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 브라질 남부 | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 캐나다 중부 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중부 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 동아시아 | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 동부 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 동부 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 프랑스 중부 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 인도 중부 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 일본 동부 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 일본 서부 | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 한국 중부 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중북부 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 북유럽 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 중남부 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 동남아시아 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 영국 남부 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 서유럽 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 서부 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 미국 서부 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>사용자 지정 신경망 음성

다음 표에서는 사용자 지정 신경망 음성 기능에 대한 지역별 지원을 자세히 설명합니다.

| 기능 | 지원되는 지역 |
|---|---|
| 음성 모델 호스팅 | 미국 동부, 미국 서부 2, 미국 중남부, 동남 아시아, 영국 남부, 서유럽, 오스트레일리아 동부 |
| 실시간 문자 | 미국 동부, 미국 서부 2, 미국 중남부, 동남 아시아, 영국 남부, 서유럽, 오스트레일리아 동부 |
| 긴 오디오 문자 | 미국 동부, 서유럽, 동남 아시아, 영국 남부, 인도 중부 |
| 사용자 지정 신경망 학습 | 미국 동부, 동남 아시아, 영국 남부 |

### <a name="long-audio-api"></a>긴 오디오 API

긴 오디오 API는 고유한 엔드포인트를 통해 여러 지역에서 사용할 수 있습니다.

| 지역 | 엔드포인트 |
|--------|----------|
| 미국 동부 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 인도 중부 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 동남아시아 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 영국 남부 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 서유럽 | `https://westeurope.customvoice.api.speech.microsoft.com` |
