---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: 1d7fc134ab6e3c27e38b00f61a2463bc9d3d0167
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378184"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 다른 언어로 번역하는 기능입니다. 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성 번역을 수행하는 방법을 알아봅니다. 이 빠른 시작에서는 다음 토픽을 다룹니다.

* 음성을 텍스트로 번역
* 음성을 여러 대상 언어로 번역
* 음성을 음성으로 직접 번역 수행

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [JavaScript 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript용 Speech SDK</a>를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.
- [Node.JS](../../../speech-sdk.md?tabs=nodejs#get-the-speech-sdk)
- [웹 브라우저](../../../speech-sdk.md?tabs=browser#get-the-speech-sdk)

또한 대상 환경에 따라 다음 중 하나를 사용합니다.

# <a name="script"></a>[스크립트](#tab/script)

<a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript용 Speech SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 파일을 다운로드하여 압축을 풀고 HTML 파일에 액세스할 수 있는 폴더에 저장합니다.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 웹 브라우저를 대상으로 지정하고 `<script>` 태그를 사용하는 경우 `sdk` 접두사는 필요하지 않습니다. `sdk` 접두사는 `require` 모듈의 이름을 지정하는 데 사용되는 별칭입니다.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

`import`에 대한 자세한 내용은 <a href="https://javascript.info/import-export" target="_blank">내보내기 및 가져오기</a>를 참조하세요.

# <a name="require"></a>[필수](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require`에 대한 자세한 내용은 <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">필요한 항목</a>을 참조하세요.

---

## <a name="create-a-translation-configuration"></a>번역 구성 만들기

Speech SDK를 사용하여 번역 서비스를 호출하려면 [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.
[`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig)를 초기화하는 다음과 같은 방법이 있습니다.

* 구독 사용: 키 및 연결된 영역을 전달합니다.
* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

키와 영역을 사용하여 [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig)를 만드는 방법을 살펴보겠습니다. [음성 서비스 무료로 사용해 보기](../../../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요.

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>번역기 초기화

[`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig)를 만든 후에 수행할 단계는 [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) 초기화입니다. [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)를 초기화할 때 `speechTranslationConfig`를 전달해야 합니다. 그러면 번역 서비스에서 요청 유효성을 검사하는 데 필요한 자격 증명이 제공됩니다.

디바이스의 기본 마이크를 통해 제공되는 음성을 번역하는 경우 [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)가 다음과 같이 생겼습니다.

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

오디오 입력 디바이스를 지정하려면 [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig)를 만들고 [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)를 초기화할 때 `audioConfig` 매개 변수를 제공해야 합니다.

> [!TIP]
> [오디오 입력 디바이스의 디바이스 ID를 가져오는 방법을 알아봅니다](../../../how-to-select-audio-input-devices.md).
다음과 같이 `AudioConfig` 개체를 참조하세요.

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

마이크를 사용하는 대신 오디오 파일을 제공하려는 경우에도 `audioConfig`를 제공해야 합니다. 그러나 이 작업은 **Node.js** 를 대상으로 지정하는 경우에만 수행할 수 있으며 `fromDefaultMicrophoneInput`을 호출하는 대신 [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig)를 만들면 `fromWavFileOutput`을 호출하여 `filename` 매개 변수를 전달합니다.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>음성 변환

JavaScript용 Speech SDK의 [TranslationRecognizer 클래스](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)는 음성 번역에 사용할 수 있는 몇 가지 메서드를 공개합니다.

* 단일 샷 번역(비동기) - 비차단(비동기) 모드에서 번역을 수행합니다. 단일 발화를 번역합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다.
* 연속 번역(비동기) - 연속 번역 작업을 비동기적으로 시작합니다. 사용자는 이벤트에 등록하고 다양한 애플리케이션 상태를 처리합니다. 비동기 연속 번역을 중지하려면 [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync)를 호출합니다.

> [!NOTE]
> [음성 인식 모드를 선택하는 방법](../../../get-started-speech-to-text.md)에 대해 자세히 알아보세요.
### <a name="specify-a-target-language"></a>대상 언어 지정

번역하려면 소스 언어와 하나 이상의 대상 언어를 지정해야 합니다.
[음성 번역 테이블](../../../language-support.md#speech-translation)에 나열된 로캘을 사용하여 소스 언어를 선택할 수 있습니다. 동일한 링크에서 번역된 언어에 대한 옵션을 찾습니다. 텍스트를 보거나 합성 번역된 음성을 들으려는 경우 대상 언어에 대한 옵션이 다릅니다. 영어에서 독일어로 번역하려면 번역 구성 개체를 수정합니다.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>단일 샷 인식

다음은 [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync)를 사용하는 비동기 단일 샷 번역의 예입니다.

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

결과를 처리하는 코드를 작성해야 합니다. 이 샘플은 독일어로 번역을 위해 [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult)을 평가합니다.

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

코드는 번역이 처리되는 동안 제공되는 업데이트도 처리할 수 있습니다.
이러한 업데이트를 사용하여 번역 진행률에 대한 시각적 피드백을 제공할 수 있습니다.
번역 프로세스 중 제공되는 업데이트를 보여주는 샘플 코드는 [이 JavaScript Node.js 예제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js)를 참조하세요. 다음 코드는 번역 프로세스 중 생성되는 세부 정보도 표시합니다.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>연속 번역

연속 번역은 단일 샷 인식보다 약간 더 복잡합니다. `recognizing`, `recognized` 및 `canceled` 이벤트를 구독하여 인식 결과를 얻어야 합니다. 번역을 중지하려면 [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync)를 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 번역이 수행되는 방식을 보여주는 예입니다.

먼저 입력을 정의하고 [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)를 초기화하겠습니다.

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

[`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer)에서 전송된 이벤트를 구독합니다.

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): 중간 번역 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): 최종 번역 결과가 포함된 이벤트에 대한 신호입니다(성공적인 번역 시도를 나타냄).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): 번역 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): 취소된 번역 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 번역 시도를 나타냄).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

모든 설정이 끝났으면 [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync)를 호출할 수 있습니다.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>소스 언어 선택

음성 번역에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 이탈리아어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig)를 찾아서 바로 아래에 다음 줄을 추가합니다.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) 속성에는 언어 로캘 형식 문자열이 필요합니다. 지원되는 [로캘/언어](../../../language-support.md) 목록의 **로캘** 열에 원하는 값을 입력할 수 있습니다.

## <a name="choose-one-or-more-target-languages"></a>하나 이상의 대상 언어 선택

Speech SDK는 여러 대상 언어로 동시에 번역할 수 있습니다. 사용 가능한 대상 언어는 소스 언어 목록과 약간 다르며 로캘이 아닌 언어 코드를 사용하여 대상 언어를 지정합니다.
[언어 지원 페이지의 음성 번역 표](../../../language-support.md#speech-translation)에서 텍스트 대상에 대한 언어 코드 목록을 참조하세요. 합성 언어로 번역에 대한 세부 정보도 찾을 수 있습니다.

다음 코드는 독일어를 대상 언어로 추가합니다.

```javascript
translationConfig.addTargetLanguage("de");
```

여러 대상 언어 번역이 가능하므로 결과를 검사할 때 코드에서 대상 언어를 지정해야 합니다. 다음 코드는 독일어 번역 결과를 가져옵니다.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```