---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 40bd77f11ed7d8949672bf57b95494f24d6793f6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646702"
---
압축된 오디오 입력을 허용하도록 Speech SDK를 구성하려면 `PullAudioInputStream` 또는 `PushAudioInputStream`을 만듭니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 만들어 스트림의 압축 형식을 지정합니다. [Speech SDK 오디오 입력 스트림 API 정보](../../../../how-to-use-audio-input-streams.md)에서 관련 샘플 코드 조각을 찾습니다.

`pushStream`이라는 입력 스트림 클래스가 있고 OPUS/OGG를 사용하고 있다고 가정해 보겠습니다. 코드는 다음과 같이 표시될 수 있습니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```