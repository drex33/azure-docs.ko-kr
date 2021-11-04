---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: b0b351e12633c01e5036386895e61b671ce5d2ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506915"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 다른 언어로 번역하는 기능입니다. 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성 번역을 수행하는 방법을 알아봅니다. 이 빠른 시작에서는 다음 토픽을 다룹니다.

* 음성을 텍스트로 번역
* 음성을 여러 대상 언어로 번역
* 음성을 음성으로 직접 번역 수행

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Java 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 플랫폼에 따라 _Speech SDK 정보_ 문서의 <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK 가져오기</a> 섹션에 나오는 지침을 따릅니다.

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 * *.Java* 코드 파일 맨 위에 다음 `import` 문을 포함합니다.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>중요한 데이터 및 환경 변수

이 문서의 예제 소스 코드는 음성 리소스 구독 키 및 지역과 같은 중요한 데이터 저장을 위한 환경 변수에 따라 달라집니다. Java 코드 파일에는 호스트 컴퓨터 환경 변수에서 할당된 두 개의 `static final String` 문자열 값, 즉 `SPEECH__SUBSCRIPTION__KEY` 및 `SPEECH__SERVICE__REGION`이 포함되어 있습니다. 이러한 필드는 모두 클래스 범위에 있으므로 클래스의 메서드 본문 내에서 액세스할 수 있습니다. 환경 변수에 대한 자세한 내용은 [환경 변수 및 애플리케이션 구성](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)을 참조하세요.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>소스 언어 변경

음성 번역에 대한 공통 작업 중 하나는 입력(또는 원본) 언어를 지정하는 것입니다. 입력 언어를 이탈리아어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechTranslationConfig`][config] 인스턴스와 상호 작용하며 `setSpeechRecognitionLanguage` 메서드를 호출합니다.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang] 함수에는 언어 로캘 형식 문자열이 필요합니다. 지원되는 [로캘/언어](../../../language-support.md) 목록의 **로캘** 열에 원하는 값을 입력할 수 있습니다.

## <a name="add-translation-language"></a>번역 언어 추가

음성 번역의 또 다른 공통 작업은 대상 번역 언어를 지정하는 것입니다. 대상 언어는 최소 하나 이상을 지정해야 하며, 여러 언어가 지원됩니다. 다음 코드 조각은 프랑스어와 독일어를 모두 번역 언어 대상으로 설정합니다.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

[`addTargetLanguage`][addlang]에 대한 모든 호출에서 새 대상 번역 언어가 지정됩니다. 즉, 소스 언어에서 음성이 인식될 때 각 대상 번역은 번역 작업의 일부로 사용할 수 있습니다.

## <a name="initialize-a-translation-recognizer"></a>번역 인식기 초기화

[`SpeechTranslationConfig`][config]를 만든 후에 수행할 단계는 [`TranslationRecognizer`][recognizer] 초기화입니다. [`TranslationRecognizer`][recognizer]를 초기화할 때 `translationConfig`를 전달해야 합니다. 구성 개체는 Speech Service가 요청의 유효성을 검사하는 데 필요한 자격 증명을 제공합니다.

디바이스의 기본 마이크를 사용하여 음성을 인식하는 경우 [`TranslationRecognizer`][recognizer]가 다음과 같이 생겼습니다.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

오디오 입력 디바이스를 지정하려면 [`AudioConfig`][audioconfig]를 만들고 [`TranslationRecognizer`][recognizer]를 초기화할 때 `audioConfig` 매개 변수를 제공해야 합니다.

> [!TIP]
> [오디오 입력 디바이스의 디바이스 ID를 가져오는 방법을 알아봅니다](../../../how-to-select-audio-input-devices.md).

먼저, 다음과 같이 `AudioConfig` 개체를 참조합니다.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

마이크를 사용하는 대신 오디오 파일을 제공하려는 경우에도 `audioConfig`를 제공해야 합니다. 그러나 `fromDefaultMicrophoneInput`을 호출하는 대신 [`AudioConfig`][audioconfig]를 만드는 경우 `fromWavFileInput`을 호출하고 `filename` 매개 변수를 전달합니다.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>음성 변환

음성 번역을 위해 Speech SDK는 마이크 또는 오디오 파일 입력을 사용합니다. 음성 인식은 음성 번역 전에 이루어집니다. 모든 개체가 초기화된 후에는 recognize-once 함수를 호출하고 결과를 가져옵니다.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

음성 텍스트 변환에 대한 자세한 내용은 [음성 인식의 기본 사항](../../../get-started-speech-to-text.md)을 참조하세요.

## <a name="synthesize-translations"></a>번역 합성

음성 인식 및 번역이 성공적으로 완료되면 해당 결과에 사전에 있는 모든 번역이 포함됩니다. [`getTranslations`][translations] 함수는 키를 대상 번역 언어로 사용하여 사전을 반환하고 값은 번역된 텍스트입니다. 인식된 음성은 번역한 다음, 다른 언어로 합성(음성-음성 번역)할 수 있습니다.

### <a name="event-based-synthesis"></a>이벤트 기반 합성

`TranslationRecognizer` 개체는 `synthesizing` 이벤트를 노출합니다. 이벤트는 여러 번 발생하며, 번역 인식 결과에서 합성된 오디오를 검색할 수 있는 메커니즘을 제공합니다. 여러 언어로 번역하는 경우 [수동 합성](#manual-synthesis)을 참조하세요. [`setVoiceName`][voicename]을 할당하여 합성 음성을 지정하고 `synthesizing` 이벤트에 대한 이벤트 처리기를 제공하여 오디오를 가져옵니다. 다음 예제에서는 번역된 오디오를 *.wav* 파일로 저장합니다.

> [!IMPORTANT]
> 이벤트 기반 합성은 단일 번역에서만 작동하며 여러 대상 번역 언어를 추가하지 **않습니다**. 또한 [`setVoiceName`][voicename]은 대상 번역 언어와 동일한 언어여야 합니다. 예를 들어 `"de"`는 `"de-DE-Hedda"`에 매핑할 수 있습니다.

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>수동 합성

[`getTranslations`][translations] 함수는 번역 텍스트에서 오디오를 합성하는 데 사용할 수 있는 사전을 반환합니다. 각 번역을 반복하고 번역을 합성합니다. `SpeechSynthesizer` 인스턴스를 만들 때 `SpeechConfig` 개체의 [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] 속성을 원하는 음성으로 설정해야 합니다. 다음 예제에서는 5개의 언어로 번역되고 각 번역은 해당 인공신경망 언어에서 오디오 파일로 합성됩니다.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

음성 합성에 대한 자세한 내용은 [음성 합성의 기본 사항](../../../get-started-text-to-speech.md)을 참조하세요.

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename
