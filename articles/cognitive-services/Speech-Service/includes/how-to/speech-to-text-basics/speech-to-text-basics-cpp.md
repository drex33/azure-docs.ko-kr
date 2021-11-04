---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: eur
ms.openlocfilehash: 7906a2e317f209550e91016c1749d1325b090cac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501921"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [C++ 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 만들어야 합니다. 이 클래스에는 키 및 관련 위치/지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독 정보가 포함됩니다. 키와 지역을 사용하여 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 만듭니다. 키-위치/지역 쌍을 찾으려면 [키 및 위치/지역 찾기](../../../overview.md#find-keys-and-locationregion) 페이지를 참조하세요.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

[`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone"></a>마이크에서 인식

디바이스 마이크를 사용하여 음성을 인식하려면 `FromDefaultMicrophoneInput()`을 사용하여 `AudioConfig`를 만듭니다. 그런 다음, [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer)를 초기화하고 `audioConfig` 및 `config`를 전달합니다.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

*특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식

마이크를 사용하는 대신 오디오 파일에서 음성을 인식하려는 경우에도 `AudioConfig`를 만들어야 합니다. 그러나 [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig)를 만들 때 `FromDefaultMicrophoneInput()`을 호출하는 대신 `FromWavFileInput()`을 호출하고 파일 경로를 전달합니다.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>음성 인식

C++용 음성 SDK의 [인식기 클래스](/cpp/cognitive-services/speech/speechrecognizer)는 음성 인식에 사용할 수 있는 몇 가지 메서드를 공개합니다.

### <a name="single-shot-recognition"></a>단일 샷 인식

단일 샷 인식은 단일 발화를 비동기적으로 인식합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다. 다음은 [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)를 사용하는 비동기 단일 샷 인식의 예입니다.

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

결과를 처리하는 코드를 작성해야 합니다. 이 샘플은 [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason)을 평가합니다.

* 인식 결과 `ResultReason::RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `ResultReason::NoMatch`를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `ResultReason::Canceled`를 출력합니다.

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the speech key and location/region info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>연속 인식

연속 인식은 단일 샷 인식보다 약간 더 복잡합니다. `Recognizing`, `Recognized` 및 `Canceled` 이벤트를 구독하여 인식 결과를 얻어야 합니다. 인식을 중지하려면 [StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)를 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

먼저 입력을 정의하고 [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer)를 초기화하겠습니다.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

다음으로, 음성 인식 상태를 관리하는 변수를 만들어 보겠습니다. 시작하기 위해, `promise<void>`를 선언합니다. 이렇게 하면 인식을 시작할 때 인식이 완료되지 않았다고 안전하게 가정할 수 있습니다.

```cpp
promise<void> recognitionEnd;
```

[`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer)에서 전송된 이벤트를 구독합니다.

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the speech key and location/region info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

모든 설정이 끝났으면 [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)를 호출할 수 있습니다.

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)에서 [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) 메서드를 사용합니다.

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 독일어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 찾아서 바로 아래에 다음 줄을 추가합니다.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)는 문자열을 인수로 사용하는 매개 변수입니다. 지원되는 [로캘/언어](../../../language-support.md) 목록에 원하는 값을 입력할 수 있습니다.

## <a name="improve-recognition-accuracy"></a>인식 정확도 향상

구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다. 구 목록을 제공함으로써 음성 인식의 정확성을 향상시킬 수 있습니다.

예를 들어, "Move to"라는 명령과 "Ward"라는 대상을 말하면 "Move to Ward" 항목을 추가할 수 있습니다. 구를 추가하면 오디오를 인식할 때 "Move toward" 대신 "Move to Ward"가 인식될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 구 목록의 항목은 항목이 발화 중간에 나타나더라도 목록의 단어와 구문의 인식률을 높이는 데 사용됩니다. 

> [!IMPORTANT]
> 구문 목록 기능은 en-US, de-DE, en-AU, en-CA, en-GB, en-IN, es-ES, fr-FR, it-IT, ja-JP, pt-BR, zh-CN의 언어로 제공됩니다.
>
> 구 목록 기능에서 사용하는 구를 수백 개 이하로 제한해야 합니다. 현재 지원되지 않는 더 큰 목록이나 언어의 경우 [사용자 지정 모델을 학습](../../../custom-speech-overview.md)하는 것이 정확도를 향상시키는 더 나은 선택일 수 있습니다.
>
> 구 목록 기능은 사용자 지정 엔드포인트에서 지원되지 않습니다. 사용자 지정 엔드포인트에서 사용하지 마세요. 대신 구를 포함하는 사용자 지정 모델을 학습하세요.

구 목록을 사용하려면 [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) 개체를 만든 다음, [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

구 목록을 지워야 하는 경우: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../custom-speech-overview.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
