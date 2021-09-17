---
title: 실시간 대화 기록 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 통해 실시간 대화 기록을 사용하는 방법을 알아봅니다. 대화 기록을 통해 Speech Service에 오디오를 스트리밍하여 여러 참가자를 추가, 제거 및 식별할 수 있는 기능으로 모임 및 기타 대화를 기록할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: pafarley
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: b3057bc9ee058ece0fd4a005c26ef8b95ec6a4b0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539086"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>실시간 대화 기록 시작

Speech SDK의 **ConversationTranscriber** API를 사용하면 `PullStream` 또는 `PushStream`을 사용하여 Speech Service에 오디오를 스트리밍하여 여러 참가자를 추가, 제거 및 식별하는 기능을 통해 모임 및 기타 대화를 기록할 수 있습니다. 먼저 REST API를 사용하여 각 참가자에 대한 음성 서명을 만든 다음, 이 음성 서명을 SDK와 함께 사용하여 대화를 기록합니다. 자세한 내용은 대화 기록 [개요](conversation-transcription.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

* `centralus``eastasia``eastus``westeurope` 구독 지역에서만 사용할 수 있습니다.
* 7마이크 원형 다중 마이크 배열이 필요합니다. 마이크 배열은 [우리의 사양](./speech-devices-sdk-microphone.md)을 충족해야 합니다.
* [Speech Devices SDK](speech-devices-sdk.md)는 적절한 디바이스와 대화 기록을 보여주는 샘플 앱을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [비동기 대화 기록](how-to-async-conversation-transcription.md)
> [ROOBO 디바이스 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
> [Azure Kinect Dev Kit 샘플 코드](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)