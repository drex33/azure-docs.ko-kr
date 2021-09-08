---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: fdbb9f163d0e34ba6f71d6066531a861d6cdd53a
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542919"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [JavaScript 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node)을 참조하세요.

또는 브라우저 기반 환경에서 Speech SDK를 사용하는 방법을 알아보려면 [React 샘플](https://github.com/Azure-Samples/AzureSpeechReactSample)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 Node.js용 Speech SDK를 설치해야 합니다. 설치할 패키지 이름만 알고 싶으면 `npm install microsoft-cognitiveservices-speech-sdk`를 실행합니다. 단계별 설치 지침은 [시작](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript&tabs=dotnet%2clinux%2cjre%2cnodejs) 문서를 참조하세요.

다음 `require` 문을 사용하여 SDK를 가져옵니다.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require` 문에 대한 자세한 내용은 [require 설명서](https://nodejs.org/en/knowledge/getting-started/what-is-require/)를 참조하세요.

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)를 만들어야 합니다. 이 클래스에는 키 및 관련 위치/지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독 정보가 포함됩니다. 키와 위치/지역을 사용하여 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)를 만듭니다. 키-위치/지역 쌍을 찾으려면 [키 및 위치/지역 찾기](../../../overview.md#find-keys-and-locationregion) 페이지를 참조하세요.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
```

[`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 관련 위치/지역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone-browser-only"></a>마이크에서 인식(브라우저만 해당)

마이크의 음성 인식은 **Node.js에서 지원되지 않으며**, 브라우저 기반 JavaScript 환경에서만 지원됩니다. [마이크 구현에서 음성 텍스트 변환](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29)을 보려면 GitHub의 [React 샘플](https://github.com/Azure-Samples/AzureSpeechReactSample)을 참조하세요.

> [!NOTE]
> *특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식 

오디오 파일에서 음성을 인식하려면 `Buffer` 개체를 수락하는 `fromWavFileInput()`을 사용하여 `AudioConfig`를 만듭니다. 그런 다음, [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)를 초기화하고 `audioConfig` 및 `speechConfig`를 전달합니다.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>메모리 내 스트림에서 인식

많은 사용 사례에서 오디오 데이터가 Blob 스토리지에서 제공되거나, 그렇지 않은 경우 이미 메모리 내에 [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 또는 이와 비슷한 원시 데이터 구조로 있을 수 있습니다. 코드는 다음과 같습니다.

* `createPushStream()`을 사용하여 푸시 스트림을 만듭니다.
* `fs.createReadStream`을 데모 용도로 사용하여 `.wav` 파일을 읽지만 이미 오디오 데이터가 `ArrayBuffer`에 있는 경우 콘텐츠를 입력 스트림에 쓰도록 바로 건너뛸 수 있습니다.
* 푸시 스트림을 사용하여 오디오 구성을 만듭니다.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

푸시 스트림을 입력으로 사용하면 오디오 데이터가 원시 PCM이라고 가정합니다(예: 헤더 건너뛰기).
헤더를 건너뛰지 않는 경우에도 API는 특정 사례에서 계속 작동하지만, 최상의 결과를 위해 `fs`가 *오디오 데이터의 시작* 에서 시작되도록 헤더를 읽는 논리를 구현하는 것이 좋습니다.

## <a name="error-handling"></a>오류 처리

이전 예제에서는 단순히 `result.text`에서 인식된 텍스트를 가져오지만 오류 및 기타 응답을 처리하려면 결과를 처리하는 코드를 작성해야 합니다. 다음 코드에서는 [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) 속성을 평가하여 다음을 수행합니다.

* 인식 결과 `ResultReason.RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `ResultReason.NoMatch`를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `ResultReason.Canceled`를 출력합니다.

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the key and location/region info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>연속 인식

이전 예제에서는 단일 발화를 인식하는 단일 샷 인식을 사용합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다.

이와 대조적으로, 인식을 중지할 때 **제어** 하려는 경우에는 연속 인식이 사용됩니다. `Recognizing`, `Recognized` 및 `Canceled` 이벤트를 구독하여 인식 결과를 얻어야 합니다. 인식을 중지하려면 [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync)를 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

입력을 정의하고 [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)를 초기화하여 시작합니다.

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

그런 다음, [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer)에서 보낸 이벤트를 구독합니다.

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the key and location/region info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

모든 설정이 완료되면 [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync)를 호출하여 인식을 시작합니다.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)에서 [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) 메서드를 사용합니다.

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 이탈리아어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig)를 찾아서 바로 아래에 다음 줄을 추가합니다.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) 속성에는 언어 로캘 형식 문자열이 필요합니다. 지원되는 [로캘/언어](../../../language-support.md) 목록의 **로캘** 열에 원하는 값을 입력할 수 있습니다.

## <a name="improve-recognition-accuracy"></a>인식 정확도 향상

구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다. 구 목록을 제공함으로써 음성 인식의 정확성을 향상시킬 수 있습니다.

예를 들어, "Move to"라는 명령과 "Ward"라는 대상을 말하면 "Move to Ward" 항목을 추가할 수 있습니다. 구를 추가하면 오디오를 인식할 때 "Move toward" 대신 "Move to Ward"가 인식될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 구 목록의 항목은 항목이 발화 중간에 나타나더라도 목록의 단어와 구문의 인식률을 높이는 데 사용됩니다. 

> [!IMPORTANT]
> 구문 목록 기능은 en-US, de-DE, en-AU, en-CA, en-GB, en-IN, es-ES, fr-FR, it-IT, ja-JP, pt-BR, zh-CN의 언어로 제공됩니다.
>
> 구 목록 기능에서 사용하는 구를 수백 개 이하로 제한해야 합니다. 현재 지원되지 않는 더 큰 목록이나 언어의 경우 [사용자 지정 모델을 학습](../../../custom-speech-overview.md)하는 것이 정확도를 향상시키는 더 나은 선택일 수 있습니다.
>
> 사용자 지정 엔드포인트에는 구문 목록 기능을 사용하지 마세요. 대신 구를 포함하는 사용자 지정 모델을 학습하세요.

구 목록을 사용하려면 [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) 개체를 만든 다음, [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

구 목록을 지워야 하는 경우:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../custom-speech-overview.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
