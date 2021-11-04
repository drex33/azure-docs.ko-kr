---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: eur
ms.openlocfilehash: 7214efa174e16ea27fd24beded8fcbeb77ecc57e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508630"
---
먼저 `KeywordRecognitionModel`을 반환하는 `FromFile()` 정적 함수를 사용하여 키워드 모델 파일을 로드합니다. Speech Studio에서 다운로드한 `.table` 파일의 경로를 사용합니다. 또한 기본 마이크를 사용하여 `AudioConfig`를 만든 다음, 오디오 구성을 사용하여 새 `KeywordRecognizer`를 인스턴스화합니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

다음으로, 모델 개체를 전달하여 `RecognizeOnceAsync()`를 한 번 호출하면 키워드 인식이 실행됩니다. 이렇게 하면 키워드가 인식될 때까지 지속되는 키워드 인식 세션이 시작됩니다. 따라서 일반적으로 이 디자인 패턴은 다중 스레드 애플리케이션에서 사용하거나 절전 모드 해제 단어를 무기한 기다릴 수 있는 사용 사례에서 사용합니다.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 여기에 표시된 예제에서는 인증 컨텍스트에 `SpeechConfig` 개체가 필요하지 않으며 백 엔드에 연결하지 않기 때문에 로컬 키워드 인식을 사용합니다. 그러나 [직접 백 엔드 연결을 이용](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)하여 키워드 인식 및 확인을 둘 다 실행할 수 있습니다.

### <a name="continuous-recognition"></a>연속 인식

Speech SDK의 다른 클래스는 키워드 인식을 통해 음성 및 의도 인식을 위한 연속 인식을 지원합니다. 따라서 키워드 모델의 `.table` 파일을 참조하는 기능을 통해 일반적으로 연속 인식에 사용하는 것과 동일한 코드를 사용할 수 있습니다.

음성 텍스트 변환의 경우 [빠른 시작](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition)에 표시된 것과 동일한 디자인 패턴을 따라 연속 인식을 설정합니다. 그런 다음, `recognizer.StartContinuousRecognitionAsync()` 호출을 `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)`로 바꾸고 `KeywordRecognitionModel` 개체를 전달합니다. 키워드 인식으로 연속 인식을 중지하려면 `recognizer.StopContinuousRecognitionAsync()` 대신 `recognizer.StopKeywordRecognitionAsync()`를 사용합니다.

의도 인식은 [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) 및 [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 함수와 함께 동일한 패턴을 사용합니다.
