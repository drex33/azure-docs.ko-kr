---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: eur
ms.openlocfilehash: bb01d62fdccb93fd890bde1a2695db44fc53eecf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506878"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 다른 언어로 번역하는 기능입니다. 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성 번역을 수행하는 방법을 알아봅니다. 이 빠른 시작에서는 다음 토픽을 다룹니다.

* 음성을 텍스트로 번역
* 음성을 여러 대상 언어로 번역
* 음성을 음성으로 직접 번역 수행

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Python 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 플랫폼에 따라 _Speech SDK 정보_ 문서의 <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK 가져오기</a> 섹션에 나오는 지침을 따릅니다.

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 Python 코드 파일 맨 위에 다음 `import` 문을 포함합니다.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>중요한 데이터 및 환경 변수

이 문서의 예제 소스 코드는 음성 리소스 구독 키 및 지역과 같은 중요한 데이터 저장을 위한 환경 변수에 따라 달라집니다. Python 코드 파일에는 호스트 컴퓨터 환경 변수에서 할당된 두 개의 값, 즉 `SPEECH__SUBSCRIPTION__KEY` 및 `SPEECH__SERVICE__REGION`이 포함되어 있습니다. 두 변수 모두 전역 범위에 있으므로 코드 파일의 함수 정의 내에서 액세스할 수 있습니다. 환경 변수에 대한 자세한 내용은 [환경 변수 및 애플리케이션 구성](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)을 참조하세요.

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>음성 번역 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechTranslationConfig`][config]를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

> [!TIP]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

[`SpeechTranslationConfig`][config]를 초기화하는 다음과 같은 방법이 있습니다.

* 구독 사용: 키 및 연결된 영역을 전달합니다.
* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

키와 영역을 사용하여 [`SpeechTranslationConfig`][config]를 만드는 방법을 살펴보겠습니다. [음성 서비스 무료로 사용해 보기](../../../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>소스 언어 변경

음성 번역에 대한 공통 작업 중 하나는 입력(또는 원본) 언어를 지정하는 것입니다. 입력 언어를 이탈리아어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechTranslationConfig`][config] 인스턴스와 상호 작용하여 `speech_recognition_language` 속성에 할당합니다.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang] 속성에는 언어 로캘 형식 문자열이 필요합니다. 지원되는 [로캘/언어](../../../language-support.md) 목록의 **로캘** 열에 원하는 값을 입력할 수 있습니다.

## <a name="add-translation-language"></a>번역 언어 추가

음성 번역의 또 다른 공통 작업은 대상 번역 언어를 지정하는 것입니다. 대상 언어는 최소 하나 이상을 지정해야 하며, 여러 언어가 지원됩니다. 다음 코드 조각은 프랑스어와 독일어를 모두 번역 언어 대상으로 설정합니다.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

[`add_target_language`][addlang]에 대한 모든 호출에서 새 대상 번역 언어가 지정됩니다. 즉, 소스 언어에서 음성이 인식될 때 각 대상 번역은 번역 작업의 일부로 사용할 수 있습니다.

## <a name="initialize-a-translation-recognizer"></a>번역 인식기 초기화

[`SpeechTranslationConfig`][config]를 만든 후에 수행할 단계는 [`TranslationRecognizer`][recognizer] 초기화입니다. [`TranslationRecognizer`][recognizer]를 초기화할 때 `translation_config`를 전달해야 합니다. 구성 개체는 Speech Service가 요청의 유효성을 검사하는 데 필요한 자격 증명을 제공합니다.

디바이스의 기본 마이크를 사용하여 음성을 인식하는 경우 [`TranslationRecognizer`][recognizer]가 다음과 같이 생겼습니다.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

오디오 입력 디바이스를 지정하려면 [`AudioConfig`][audioconfig]를 만들고 [`TranslationRecognizer`][recognizer]를 초기화할 때 `audio_config` 매개 변수를 제공해야 합니다.

> [!TIP]
> [오디오 입력 디바이스의 디바이스 ID를 가져오는 방법을 알아봅니다](../../../how-to-select-audio-input-devices.md).

먼저, 다음과 같이 `AudioConfig` 개체를 참조합니다.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

마이크를 사용하는 대신 오디오 파일을 제공하려는 경우에도 `audioConfig`를 제공해야 합니다. 하지만 `use_default_microphone=True`를 호출하는 대신 [`AudioConfig`][audioconfig]를 만드는 경우 `filename="path-to-file.wav"`를 호출하고 `filename` 매개 변수를 전달합니다.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>음성 변환

음성 번역을 위해 Speech SDK는 마이크 또는 오디오 파일 입력을 사용합니다. 음성 인식은 음성 번역 전에 이루어집니다. 모든 개체가 초기화된 후에는 recognize-once 함수를 호출하고 결과를 가져옵니다.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

음성 텍스트 변환에 대한 자세한 내용은 [음성 인식의 기본 사항](../../../get-started-speech-to-text.md)을 참조하세요.

## <a name="synthesize-translations"></a>번역 합성

음성 인식 및 번역이 성공적으로 완료되면 해당 결과에 사전에 있는 모든 번역이 포함됩니다. [`translations`][translations] 사전 키는 대상 번역 언어이고 값은 번역된 텍스트입니다. 인식된 음성은 번역한 다음, 다른 언어로 합성(음성-음성 번역)할 수 있습니다.

### <a name="event-based-synthesis"></a>이벤트 기반 합성

`TranslationRecognizer` 개체는 `Synthesizing` 이벤트를 노출합니다. 이벤트는 여러 번 발생하며, 번역 인식 결과에서 합성된 오디오를 검색할 수 있는 메커니즘을 제공합니다. 여러 언어로 번역하는 경우 [수동 합성](#manual-synthesis)을 참조하세요. [`voice_name`][voicename]을 할당하여 합성 음성을 지정하고 `Synthesizing` 이벤트에 대한 이벤트 처리기를 제공하여 오디오를 가져옵니다. 다음 예제에서는 번역된 오디오를 *.wav* 파일로 저장합니다.

> [!IMPORTANT]
> 이벤트 기반 합성은 단일 번역에서만 작동하며 여러 대상 번역 언어를 추가하지 **않습니다**. 또한 [`voice_name`][voicename]은 대상 번역 언어와 동일한 언어여야 합니다. 예를 들어 `"de"`는 `"de-DE-Hedda"`에 매핑할 수 있습니다.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>수동 합성

[`translations`][translations] 사전을 사용하여 번역 텍스트에서 오디오를 합성할 수 있습니다. 각 번역을 반복하고 번역을 합성합니다. `SpeechSynthesizer` 인스턴스를 만들 때 `SpeechConfig` 개체의 [`speech_synthesis_voice_name`][speechsynthesisvoicename] 속성을 원하는 음성으로 설정해야 합니다. 다음 예제에서는 5개의 언어로 번역되고 각 번역은 해당 인공신경망 언어에서 오디오 파일로 합성됩니다.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

음성 합성에 대한 자세한 내용은 [음성 합성의 기본 사항](../../../get-started-text-to-speech.md)을 참조하세요.

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name
