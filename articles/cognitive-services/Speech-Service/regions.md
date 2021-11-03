---
title: 지역 - Speech Service
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역을 포함하여 Speech Service에 사용할 수 있는 지역 및 엔드포인트 목록입니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2021
ms.author: panosper
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 5ae6618731a6b8d0561a58a1997dbee9584e3551
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031122"
---
# <a name="speech-service-supported-regions"></a>Speech Service 지원 지역

음성 서비스를 통해 애플리케이션에서 오디오를 텍스트로 변환하고, 음성 번역을 수행하고, 텍스트를 음성으로 변환할 수 있습니다. 음성 SDK 및 REST API에 대한 고유한 엔드포인트를 사용하여 여러 지역에서 서비스를 사용할 수 있습니다.

모든 지역에 대 한 음성 환경에 대 한 사용자 지정 구성을 수행할 수 있는 음성 포털은 [speech.microsoft.com](https://speech.microsoft.com)에서 사용할 수 있습니다.

지역을 고려할 때는 다음 사항에 유의하세요.

* 애플리케이션에서 [Speech SDK](speech-sdk.md)를 사용하는 경우 Speech 구성을 만들 때 `westus`와 같은 지역 식별자를 제공합니다. 지역이 구독의 지역과 일치하는지 확인합니다.
* 애플리케이션에서 Speech 서비스의 [REST API](./overview.md#reference-docs) 중 하나를 사용하는 경우 지역은 요청 시 사용하는 엔드포인트 URI의 일부가 됩니다.
* 지역에 대해 만든 키는 해당 지역에서만 유효합니다. 다른 지역에서 사용하려고 하면 인증 오류가 발생합니다.

> [!NOTE]
> Speech Services에서는 고객이 서비스 인스턴스를 배포하는 지역 외부에서 고객 데이터를 저장하거나 처리하지 않습니다.

## <a name="speech-sdk"></a>Speech SDK

[SPEECH sdk](speech-sdk.md)에서 지역은 매개 변수로 지정 됩니다. 예를 들어 `SpeechConfig.FromSubscription` c # 용 Speech SDK의에 대 한 매개 변수로 지정 됩니다.

### <a name="speech-to-text-text-to-speech-and-translation"></a>음성 텍스트 변환, 텍스트 음성 변환 및 번역

Speech Service는 다음 지역에서 **음성 인식**, **텍스트 음성 변환** 및 **번역** 을 위해 사용할 수 있습니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

오디오 데이터로 사용자 지정 모델을 학습하려는 경우 더 빠른 학습을 위해 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account) 중 하나를 사용합니다. [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)를 사용하여 나중에 완전히 학습된 모델을 다른 지역에 복사할 수 있습니다.

### <a name="intent-recognition"></a>의도 인식

Speech SDK를 통해 **의도 인식** 을 사용할 수 있는 지역은 다음과 같습니다.

| 글로벌 지역 | 지역           | 지역 식별자 |
| ------------- | ---------------- | -------------------- |
| 아시아          | 동아시아        | `eastasia`           |
| 아시아          | 동남아시아   | `southeastasia`      |
| 오스트레일리아     | 오스트레일리아 동부   | `australiaeast`      |
| 유럽        | 북유럽     | `northeurope`        |
| 유럽        | 서유럽      | `westeurope`         |
| 북아메리카 | 미국 동부          | `eastus`             |
| 북아메리카 | 미국 동부 2        | `eastus2`            |
| 북아메리카 | 미국 중남부 | `southcentralus`     |
| 북아메리카 | 미국 중서부  | `westcentralus`      |
| 북아메리카 | 미국 서부          | `westus`             |
| 북아메리카 | 미국 서부 2        | `westus2`            |
| 남아메리카 | 브라질 남부     | `brazilsouth`        |

[LUIS(Language Understanding Service)](../luis/luis-reference-regions.md)에서 지원하는 게시 지역의 하위 집합입니다.

### <a name="voice-assistants"></a>음성 도우미

[Speech SDK](speech-sdk.md)는 다음 지역에서 [Direct Line Speech](./direct-line-speech.md)를 통해 **음성 도우미** 기능을 지원합니다.

| 글로벌 지역 | 지역           | 지역 식별자    |
| ------------- | ---------------- | -------------------- |
| 북아메리카 | 미국 서부          | `westus`             |
| 북아메리카 | 미국 서부 2        | `westus2`            |
| 북아메리카 | 미국 동부          | `eastus`             |
| 북아메리카 | 미국 동부 2        | `eastus2`            |
| 북아메리카 | 미국 중서부  | `westcentralus`      |
| 북아메리카 | 미국 중남부 | `southcentralus`     |
| 유럽        | 서유럽      | `westeurope`         |
| 유럽        | 북유럽     | `northeurope`        |
| 아시아          | 동아시아        | `eastasia`           |
| 아시아          | 동남아시아   | `southeastasia`      |
| 인도         | 인도 중부    | `centralindia`       |

### <a name="speaker-recognition"></a>Speaker Recognition

**Speaker Recognition** 사용할 수 있는 영역은 다음과 같습니다.

| Geography | 지역           | 지역 식별자 |
| ------------- | ---------------- | -------------------- |
| 아메리카     | 미국 중부   | `centralus` |
| 아메리카     | 미국 동부   | `eastus`  |
| 아메리카     | 미국 동부 2  | `eastus2`  |
| 아메리카     | 미국 중서부  | `westcentralus`  |
| 아메리카     | 미국 서부  | `westus`  |
| 아메리카     | 미국 서부 2  | `westus2`  |
| 아시아 태평양  | 동아시아   | `eastasia` |
| 아시아 태평양  | 동남 아시아   | `southeastasia` |
| 아시아 태평양  | 인도 중부   | `centralindia` |
| 오스트레일리아     | 오스트레일리아 동부   | `australiaeast` |
| 유럽     | 북유럽   | `northeurope` |
| 유럽     | 서유럽   | `westeurope` |
| 유럽     | 영국 남부   | `uksouth` |

## <a name="rest-apis"></a>REST API

또한 Speech Service는 음성-텍스트, 텍스트 음성 및 화자 인식 요청에 대한 REST 엔드포인트를 노출합니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

음성 텍스트 변환 참조 문서는 [Speech-to-text REST API](rest-speech-to-text.md)를 참조하세요.

REST API의 엔드포인트는 다음 형식을 갖습니다.

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`를 다음 표의 구독 지역과 일치하는 식별자로 바꿉니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP 오류가 발생하지 않도록 언어 매개 변수를 URL에 추가해야 합니다. 예를 들어 미국 서부 엔드포인트를 사용하는 미국 영어로 설정된 언어는 `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`입니다.

### <a name="text-to-speech"></a>텍스트 음성 변환

텍스트 음성 변환 참조 문서는 [Text-to-speech REST API](rest-text-to-speech.md)를 참조하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="speaker-recognition"></a>Speaker Recognition

화자 인식 참조 설명서는 [Speaker Recognition REST API](/rest/api/speakerrecognition/)참조하세요. 사용 가능한 지역은 Speaker Recognition SDK와 동일합니다.
