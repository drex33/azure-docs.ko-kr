---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: a651fe0ddb25fb44de518c794264133f16879982
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378094"
---
이 빠른 시작에서는 다음을 포함하여 Speech SDK를 사용하는 Speaker Recognition에 대한 기본적인 디자인 패턴을 알아봅니다.

* 텍스트 종속 및 텍스트 독립 확인
* 음성 그룹 간의 음성 샘플을 식별하는 화자 식별
* 음성 프로필 삭제

음성 인식 개념에 대한 간략한 설명은 [개요](../../../speaker-recognition-overview.md) 문서를 참조하세요.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [JavaScript 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition는 현재 `westus` 지역에서 만든 Azure Speech 리소스에서만 지원되는 *유일한 것* 입니다.

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

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 다음 명령문을 .js 파일의 위쪽에 추가합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

이러한 문은 필수 라이브러리를 가져오고, 환경 변수에서 Speech Service 구독 키 및 지역을 가져옵니다. 또한 다음 작업에서 사용할 오디오 파일의 경로를 지정합니다.

## <a name="create-helper-function"></a>도우미 함수 만들기

Speech Service에서 사용할 오디오 파일을 스트림으로 읽어들이기 위해 다음 도우미 함수를 추가합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

이 함수에서는 [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) 및 [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) 메서드를 사용하여 [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) 개체를 만듭니다. 이 `AudioConfig` 개체는 오디오 스트림을 나타냅니다. 다음 작업 중에 이러한 `AudioConfig` 개체 중 몇 가지를 사용합니다.

## <a name="text-dependent-verification"></a>텍스트-종속 확인

Speaker Verification는 화자가 알려진 또는 **등록된** 음성으로 일치하는지 확인하는 행위입니다. 첫 번째 단계는 음성 프로필을 **등록** 하는 것입니다. 따라서 서비스에 향후 음성 샘플과 비교할 내용이 있습니다. 다음 예제에서는 **텍스트 종속** 전략을 사용하여 프로필을 등록합니다. 이 전략에서는 특정 암호를 등록 및 검증 모두에 사용해야 합니다. 지원되는 암호 목록은 [참조 문서](/rest/api/speakerrecognition/)를 확인하세요.

### <a name="textdependentverification-function"></a>TextDependentVerification 함수

먼저 `TextDependentVerification` 함수를 만듭니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

이 함수는 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 메서드를 사용하여 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 개체를 만듭니다. `VoiceProfile`에는 다음 세 가지 [형식](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype)이 있습니다.

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

이 경우 `VoiceProfileType.TextDependentVerification`을 `VoiceProfileClient.createProfileAsync`에 전달합니다.

그런 다음, 다음에 정의할 두 개의 도우미 함수(`AddEnrollmentsToTextDependentProfile` 및 `SpeakerVerify`)를 호출합니다. 마지막으로 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-)를 호출하여 프로필을 제거합니다.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 함수

다음 함수를 정의하여 음성 프로필을 등록합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

이 함수에서는 이전에 정의한 `GetAudioConfigFromFile` 함수를 호출하여 오디오 샘플에서 `AudioConfig` 개체를 만듭니다. 이러한 오디오 샘플에는 "내 목소리가 내 여권입니다. 나를 확인해 보세요."와 같은 암호가 포함되어 있습니다. 그런 다음, [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 메서드를 사용하여 이러한 오디오 샘플을 등록합니다.

### <a name="speakerverify-function"></a>SpeakerVerify 함수

`SpeakerVerify`를 다음과 같이 정의합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

이 함수에서는 이전에 만든 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 개체를 전달하는 [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-) 메서드를 사용하여 [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) 개체를 만듭니다.

다음으로 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 메서드를 호출하여 이전에 등록한 오디오 샘플과 동일한 암호가 포함된 오디오 샘플의 유효성을 검사합니다. `SpeechRecognizer.recognizeOnceAsync`는 `score` 속성에 0.0~1.0 범위의 유사성 점수가 포함된 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 개체를 반환합니다. 또한 `SpeakerRecognitionResult` 개체에는 [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason) 형식의 `reason` 속성이 포함되어 있습니다. 검증에 성공하면 `reason` 속성의 값이 `RecognizedSpeaker`입니다.

## <a name="text-independent-verification"></a>텍스트 독립 확인

**텍스트-종속** 확인을 하는 것과 달리 **텍스트-독립** 을 확인합니다:

* 특정 암호를 말할 필요가 없고, 모든 항목을 말할 수 있습니다.
* 세 개의 오디오 샘플이 필요하지 않지만, 총 20초의 오디오가 *필요합니다*.

### <a name="textindependentverification-function"></a>TextIndependentVerification 함수

먼저 `TextIndependentVerification` 함수를 만듭니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

이 함수는 `TextDependentVerification` 함수와 마찬가지로 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 메서드를 사용하여 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 개체를 만듭니다.

이 경우 `VoiceProfileType.TextIndependentVerification`을 `createProfileAsync`에 전달합니다.

그런 다음, 다음에 정의할 `AddEnrollmentsToTextIndependentProfile` 및 이미 정의한 `SpeakerVerify`의 두 가지 도우미 함수를 호출합니다. 마지막으로 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-)를 호출하여 프로필을 제거합니다.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

다음 함수를 정의하여 음성 프로필을 등록합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

이 함수에서는 이전에 정의한 `GetAudioConfigFromFile` 함수를 호출하여 오디오 샘플에서 `AudioConfig` 개체를 만듭니다. 그런 다음, [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) 메서드를 사용하여 이러한 오디오 샘플을 등록합니다.

## <a name="speaker-identification"></a>화자 식별

화자 식별은 지정 등록된 음성 그룹에서 **누가 말하고 있는지** 확인하는 데 사용됩니다. 이 프로세스는 **텍스트 독립 검증** 과 비슷합니다. 주요 차이점은 단일 프로필과 비교하여 검증하는 것이 아니라 한 번에 여러 음성 프로필과 비교하여 검증할 수 있다는 것입니다.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 함수

먼저 `TextIndependentIdentification` 함수를 만듭니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

이 함수는 `TextDependentVerification` 및 `TextIndependentVerification` 함수와 마찬가지로 [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) 메서드를 사용하여 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 개체를 만듭니다.

이 경우 `VoiceProfileType.TextIndependentIdentification`을 `VoiceProfileClient.createProfileAsync`에 전달합니다.

그런 다음, 이미 정의한 `AddEnrollmentsToTextIndependentProfile` 및 다음에 정의할 `SpeakerIdentify`의 두 가지 도우미 함수를 호출합니다. 마지막으로 [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-)를 호출하여 프로필을 제거합니다.

### <a name="speakeridentify-function"></a>SpeakerIdentify 함수

`SpeakerIdentify` 함수를 다음과 같이 정의합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

이 함수에서는 이전에 만든 [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) 개체를 전달하는 [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---) 메서드를 사용하여 [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) 개체를 만듭니다.

다음으로 [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) 메서드를 호출하여 오디오 샘플을 전달합니다.
`SpeechRecognizer.recognizeOnceAsync`는 `SpeakerIdentificationModel`을 만드는 데 사용한 `VoiceProfile` 개체에 따라 이 오디오 샘플의 음성을 식별하려고 합니다. 이는 `profileId` 속성에서 일치하는 `VoiceProfile`을 식별하지만(있는 경우) `score` 속성에 0.0~1.0 범위의 유사성 점수가 포함된 [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) 개체를 반환합니다.

## <a name="main-function"></a>main 함수

마지막으로 `main` 함수를 다음과 같이 정의합니다.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

이 함수는 음성 프로필을 만들고, 등록하고, 삭제하는 데 사용되는 [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient) 개체를 만듭니다. 그런 다음, 이전에 정의한 함수를 호출합니다.