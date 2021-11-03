---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 68e088b088c461b29311a1289e1867c4df9e5b1a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069159"
---
이 빠른 시작에서는 다음을 포함하여 Speech SDK를 사용하는 Speaker Recognition에 대한 기본적인 디자인 패턴을 알아봅니다.

* 텍스트 종속 및 텍스트 독립 확인
* 음성 그룹 간의 음성 샘플을 식별하는 화자 식별
* 음성 프로필 삭제

Speaker Recognition 개념에 대한 간략한 설명은 [개요](../../../speaker-recognition-overview.md) 문서를 참조하세요. 지원되는 플랫폼 목록은 왼쪽 탐색의 참조 노드를 참조하세요.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [C++ 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Microsoft에서는 Speaker Recognition에 대한 액세스를 제한합니다. [Azure Cognitive Services Speaker Recognition 제한된 액세스 검토](https://aka.ms/azure-speaker-recognition)를 통해 사용을 신청합니다. 승인 후에는 Speaker Recognition API에 액세스할 수 있습니다. 

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 다음 명령문을 .cpp 파일의 위쪽에 추가합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>텍스트-종속 확인

Speaker Verification는 화자가 알려진 또는 **등록된** 음성으로 일치하는지 확인하는 행위입니다. 첫 번째 단계는 음성 프로필을 **등록** 하는 것입니다. 따라서 서비스에 향후 음성 샘플과 비교할 내용이 있습니다. 다음 예제에서는 **텍스트 종속** 전략을 사용하여 프로필을 등록합니다. 이 전략에서는 특정 암호를 등록 및 검증 모두에 사용해야 합니다. 지원되는 암호 목록은 [참조 문서](/rest/api/speakerrecognition/)를 확인하세요.

### <a name="textdependentverification-function"></a>TextDependentVerification 함수

먼저 `TextDependentVerification` 함수를 만듭니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

이 함수는 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 메서드를 사용하여 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 개체를 만듭니다. `VoiceProfile`에는 다음 세 가지 [형식](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype)이 있습니다.

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

이 경우 `VoiceProfileType::TextDependentVerification`을 `CreateProfileAsync`에 전달합니다.

그런 다음, 다음에 정의할 두 개의 도우미 함수(`AddEnrollmentsToTextDependentProfile` 및 `SpeakerVerify`)를 호출합니다. 마지막으로 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)를 호출하여 프로필을 정리합니다.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 함수

다음 함수를 정의하여 음성 프로필을 등록합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

이 함수에서는 등록에 필요한 나머지 샘플 수를 추적하는 `while` 반복에 오디오 샘플을 등록합니다. 각 반복마다 [EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync)에서 마이크에 암호를 말하라는 메시지를 표시하고, 샘플을 음성 프로필에 추가합니다.

### <a name="speakerverify-function"></a>SpeakerVerify 함수

`SpeakerVerify`를 다음과 같이 정의합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

이 함수에서는 이전에 만든 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 개체를 전달하는 [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speaker-speakerverificationmodel#fromprofile) 메서드를 사용하여 [SpeakerVerificationModel](/cpp/cognitive-services/speech/speaker-speakerverificationmodel) 개체를 만듭니다.

다음으로 [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)에서 암호를 다시 말하라는 메시지를 표시하지만, 이번에는 음성 프로필과 비교하여 유효성을 검사하고 0.0~1.0 범위의 유사성 점수를 반환합니다. 또한 [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speaker-speakerrecognitionresult) 개체에서 암호가 일치하는지 여부에 따라 `Accept` 또는 `Reject`를 반환합니다.

## <a name="text-independent-verification"></a>텍스트 독립 확인

**텍스트 종속** 확인과 달리 **텍스트 독립** 확인에는 3개의 오디오 샘플이 필요하지 않지만 총 20초의 오디오가 *필요합니다.*

### <a name="textindependentverification-function"></a>TextIndependentVerification 함수

먼저 `TextIndependentVerification` 함수를 만듭니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

이 함수는 `TextDependentVerification` 함수와 마찬가지로 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 메서드를 사용하여 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 개체를 만듭니다.

이 경우 `VoiceProfileType::TextIndependentVerification`을 `CreateProfileAsync`에 전달합니다.

그런 다음, 다음에 정의할 `AddEnrollmentsToTextIndependentProfile` 및 이미 정의한 `SpeakerVerify`의 두 가지 도우미 함수를 호출합니다. 마지막으로 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)를 호출하여 프로필을 정리합니다.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

다음 함수를 정의하여 음성 프로필을 등록합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

이 함수에서는 등록에 필요한 나머지 오디오 시간(초)을 추적하는 `while` 반복에 오디오 샘플을 등록합니다. 각 반복마다 [EnrollProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#enrollprofileasync)에서 마이크에 말하라는 메시지를 표시하고, 샘플을 음성 프로필에 추가합니다.

## <a name="speaker-identification"></a>화자 식별

화자 식별은 지정 등록된 음성 그룹에서 **누가 말하고 있는지** 확인하는 데 사용됩니다. 이 프로세스는 **텍스트 독립 확인** 하는 것과 매우 유사합니다. 주요 차이점은 단일 프로필을 확인하는 것이 아니라 한 번에 여러 음성 프로필에 대해 확인할 수 있다는 것입니다.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 함수

먼저 `TextIndependentIdentification` 함수를 만듭니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

이 함수는 `TextDependentVerification` 및 `TextIndependentVerification` 함수와 마찬가지로 [CreateProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#createprofileasync) 메서드를 사용하여 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 개체를 만듭니다.

이 경우 `VoiceProfileType::TextIndependentIdentification`을 `CreateProfileAsync`에 전달합니다.

그런 다음, 이미 정의한 `AddEnrollmentsToTextIndependentProfile` 및 다음에 정의할 `SpeakerIdentify`의 두 가지 도우미 함수를 호출합니다. 마지막으로 [DeleteProfileAsync](/cpp/cognitive-services/speech/speaker-voiceprofileclient#deleteprofileasync)를 호출하여 프로필을 정리합니다.

### <a name="speakeridentify-function"></a>SpeakerIdentify 함수

`SpeakerIdentify` 함수를 다음과 같이 정의합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

이 함수에서는 [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel#fromprofiles) 메서드를 사용하여 [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speaker-speakeridentificationmodel) 개체를 만듭니다. `SpeakerIdentificationModel::FromProfiles`는 [VoiceProfile](/cpp/cognitive-services/speech/speaker-voiceprofile) 개체 목록을 허용합니다. 이 경우 이전에 만든 `VoiceProfile` 개체만 전달하면 됩니다. 그러나 원하는 경우 각각 다른 음성의 오디오 샘플에 등록된 여러 `VoiceProfile` 개체를 전달할 수 있습니다.

다음으로 [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)에서 다시 말하라는 메시지를 표시합니다. 이번에는 사용자의 음성을 등록된 음성 프로필과 비교하여 가장 비슷한 음성 프로필을 반환합니다.

## <a name="main-function"></a>main 함수

마지막으로 `main` 함수를 다음과 같이 정의합니다.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

이 함수는 이전에 정의한 함수를 호출하기만 합니다. 그러나 먼저 [VoiceProfileClient](/cpp/cognitive-services/speech/speaker-voiceprofileclient) 개체 및 [SpeakerRecognizer](/cpp/cognitive-services/speech/speaker-speakerrecognizer) 개체를 만듭니다.

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient`는 음성 프로필을 만들고, 등록하고, 삭제하는 데 사용됩니다. `SpeakerRecognizer`는 하나 이상의 등록된 음성 프로필과 비교하여 음성 샘플의 유효성을 검사하는 데 사용됩니다.

## <a name="changing-audio-input-type"></a>오디오 입력 형식 변경

이 문서의 예제에서는 기본 장치 마이크를 오디오 샘플에 대한 입력으로 사용합니다. 그러나 마이크 입력 대신 오디오 파일을 사용해야 하는 시나리오에서는 다음 줄을 변경하면 됩니다.

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

다음과 같이 변경합니다.

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

또는 `audio_config`의 모든 사용을 [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput)으로 바꿉니다. 예를 들어 등록용 마이크와 황인용 파일을 사용하여 입력을 혼합할 수 있습니다.
