---
title: 비동기 대화 전사 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service를 사용하여 비동기 대화 전사를 사용하는 방법을 알아봅니다. Java 및 C#에서만 사용할 수 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 9dbee96d6b277ac76b07e3fd8b2b72b1c84b32f9
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546408"
---
# <a name="asynchronous-conversation-transcription"></a>비동기 대화 전사

이 문서에서는 **RemoteConversationTranscriptionClient** API를 사용하여 비동기 대화 전사를 보여 줍니다. 비동기 전사를 수행하도록 대화 전사를 구성하고 `conversationId`가 있는 경우 **RemoteConversationTranscriptionClient** API를 사용하여 해당 `conversationId`와 관련된 전사를 가져올 수 있습니다.

## <a name="asynchronous-vs-real-time--asynchronous"></a>비동기 및 실시간+비동기

비동기 전사를 사용하면 대화 오디오를 스트리밍할 수 있지만 실시간으로 전사를 반환할 필요는 없습니다. 대신 오디오를 전송한 후 `Conversation`의 `conversationId`를 사용하여 비동기 전사 상태를 쿼리합니다. 비동기 전사가 준비되면 `RemoteConversationTranscriptionResult`가 표시됩니다.

실시간+비동기 전사를 사용하면 실시간으로 전사를 얻을 수 있지만 `conversationId`로 쿼리하여 전사를 얻을 수도 있습니다(비동기 시나리오와 유사).

비동기 전사를 수행하려면 두 단계가 필요합니다. 첫 번째 단계는 비동기식만으로 또는 실시간+비동기식으로 오디오를 업로드하는 것입니다. 두 번째 단계는 전사 결과를 가져오는 것입니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
