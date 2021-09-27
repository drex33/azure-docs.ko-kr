---
title: 언어 식별-음성 서비스
titleSuffix: Azure Cognitive Services
description: 언어 식별은 제공된 언어 목록과 비교했을 때 Speech SDK에 전달되는 오디오에서 사용되는 언어를 결정하는 데 사용됩니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-cs-cpp-py
ms.openlocfilehash: 6830ded4b6a074a29de9741eee957b0a4a1ff4b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588127"
---
# <a name="language-identification-preview"></a>언어 식별 (미리 보기)

언어 식별은 제공된 언어 목록과 비교했을 때 Speech SDK에 전달되는 오디오에서 사용되는 언어를 결정하는 데 사용됩니다. 

[음성 번역](./get-started-speech-translation.md#multi-lingual-translation-with-language-identification)을 수행 하는 동안 또는 [음성 인식 중 언어 식별](./how-to-automatic-language-detection.md)을 수행 하는 동안 언어 id를 사용할 수도 있습니다. 

사용할 수 있는 언어를 확인하려면 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure 구독 및 음성 리소스가 있고 음성 인식 기본 사항을 알고 있다고 가정합니다.

## <a name="standalone-language-identification"></a>독립 실행형 언어 식별

사용되는 소스 언어만 검색하려는 사용 사례에서 다음 코드 샘플에 표시된 것과 같이 독립 실행형 언어 식별을 사용할 수 있습니다. `SourceLanguageRecognizer`는 연속 인식 시나리오에서도 사용할 수 있습니다.

::: zone pivot="programming-language-csharp"

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SourceLanguageRecognizer(speechConfig, autoDetectSourceLanguageConfig))
{
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.RecognizedSpeech)
    {
        var lang = AutoDetectSourceLanguageResult.FromResult(result).Language;
        Console.WriteLine($"DETECTED: Language={lang}");
    }
}
```

연속 식별 예제를 포함하여 독립 실행형 언어 식별의 추가 예제는 [GitHub의 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs)을 참조하세요.

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
config->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SourceLanguageRecognizer::FromConfig(config, autoDetectSourceLanguageConfig);
cout << "Say something...\n";

auto result = recognizer->RecognizeOnceAsync().get();
if (result->Reason == ResultReason::RecognizedSpeech)
{
    auto lidResult = AutoDetectSourceLanguageResult::FromResult(result);
    cout << "DETECTED: Language="<< lidResult->Language << std::endl;
}
```

연속 식별 예제를 포함하여 독립 실행형 언어 식별의 추가 예제는 [GitHub의 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp)을 참조하세요.

::: zone-end

::: zone pivot="programming-language-python"

```python
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, value='Accuracy')

speech_language_detection = speechsdk.SourceLanguageRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

result = speech_language_detection.recognize_once()

# Check the result
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("RECOGNIZED: {}".format(result))
    detectedSrcLang = result.properties[speechsdk.PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult]
    print("Detected Language: {}".format(detectedSrcLang))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized")
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

연속 식별 예제를 포함하여 독립 실행형 언어 식별의 추가 예제는 [GitHub의 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_language_detection_sample.py)을 참조하세요.

::: zone-end