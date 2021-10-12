---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: pafarley
ms.openlocfilehash: 797d2cc40dfc10de1ce8c8d6bdb623eff56a375f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378580"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Python 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 다음과 같이 가정합니다.

* 여러분이 Azure 계정 및 음성 서비스 구독을 갖고 있습니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>음성 SDK 설치 및 가져오기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다.

```Python
pip install azure-cognitiveservices-speech
```

macOS를 사용 중인데 설치 문제가 발생하는 경우 다음 명령을 먼저 실행해야 할 수도 있습니다.

```Python
python3 -m pip install --upgrade pip
```

Speech SDK를 설치한 후 Python 프로젝트로 가져옵니다.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)를 만들어야 합니다. 이 클래스에는 키 및 관련 위치/지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독 정보가 포함됩니다. 키와 위치/지역을 사용하여 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)를 만듭니다. 키-위치/지역 쌍을 찾으려면 [키 및 위치/지역 찾기](../../../overview.md#find-keys-and-locationregion) 페이지를 참조하세요.

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
```

[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone"></a>마이크에서 인식

디바이스 마이크를 사용하여 음성을 인식하려면 `AudioConfig`를 전달하지 않고 `SpeechRecognizer`를 만들고 `speech_config`를 전달하면 됩니다.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

*특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정하고 이를 `SpeechRecognizer` 생성자의 `audio_config` 매개 변수에 전달해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식

마이크 대신 오디오 파일에서 음성을 인식하려면 [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig)을 만들고 `filename` 매개 변수를 사용합니다.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-speech-key-here>", region="<paste-your-speech-location/region-here>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>오류 처리

이전 예제에서는 단순히 `result.text`에서 인식된 텍스트를 가져오지만 오류 및 기타 응답을 처리하려면 결과를 처리하는 코드를 작성해야 합니다. 다음 코드에서는 [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason) 속성을 평가하여 다음을 수행합니다.

* 인식 결과 `speechsdk.ResultReason.RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `speechsdk.ResultReason.NoMatch`를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `speechsdk.ResultReason.Canceled`를 출력합니다.

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>연속 인식

이전 예제에서는 단일 발화를 인식하는 단일 샷 인식을 사용합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다.

이와 대조적으로, 인식을 중지할 때 **제어** 하려는 경우에는 연속 인식이 사용됩니다. 인식 결과를 얻으려면 `EventSignal`에 연결해야 하며 인식을 중지하려면 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) 또는 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition-async--)을 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

먼저 입력을 정의하고 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer)를 초기화하겠습니다.

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

다음으로, 음성 인식 상태를 관리하는 변수를 만들어 보겠습니다. 시작하기 위해, 이 변수를 `False`로 설정합니다. 이렇게 하면 인식을 시작할 때 인식이 완료되지 않았다고 안전하게 가정할 수 있습니다.

```Python
done = False
```

이제 `evt`가 수신되면 연속 인식을 중지하는 콜백을 만들어 보겠습니다. 몇 가지 주의할 사항이 있습니다.

* `evt`가 수신되면 `evt` 메시지가 출력됩니다.
* `evt`가 수신된 후에는 인식을 중지하기 위해 [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--)가 호출됩니다.
* 인식 상태가 `True`로 변경됩니다.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

다음 코드 샘플은 [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#start-continuous-recognition--)에서 보낸 이벤트에 콜백을 연결하는 방법을 보여줍니다.

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognizing): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognized): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-started): 인식 세션(작업)의 시작을 나타내는 이벤트에 대한 신호입니다.
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#canceled): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

모든 설정이 끝났으면 [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped)를 호출할 수 있습니다.

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)에서 [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#enable-dictation--) 메서드를 사용합니다.

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 독일어로 변경하는 방법을 살펴보겠습니다. 코드에서 SpeechConfig를 찾아서 바로 아래에 다음 줄을 추가합니다.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-recognition-language)는 문자열을 인수로 사용하는 매개 변수입니다. 지원되는 [로캘/언어](../../../language-support.md) 목록에 원하는 값을 입력할 수 있습니다.

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

구 목록을 사용하려면 [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) 개체를 만든 다음, [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar#addphrase-phrase--str-)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

구 목록을 지워야 하는 경우: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../custom-speech-overview.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
