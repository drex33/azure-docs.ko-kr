---
title: 언어 식별을 사용하는 방법
titleSuffix: Azure Cognitive Services
description: 언어 식별은 제공된 언어 목록과 비교했을 때 Speech SDK에 전달되는 오디오에서 사용되는 언어를 결정하는 데 사용됩니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/21/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e24a24b54d31a98497bfba453b6677ebf0f560a9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530615"
---
# <a name="how-to-use-language-identification"></a>언어 식별을 사용하는 방법

언어 식별은 제공된 언어 목록과 비교했을 때 Speech SDK에 전달되는 오디오에서 사용되는 언어를 결정하는 데 사용됩니다. 그런 다음, 언어 식별에서 반환되는 값을 사용하여 음성 텍스트 변환의 언어 모델을 선택하여 더 정확한 대화 내용 기록을 제공합니다. 

[음성 번역](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall#multi-lingual-translation-with-language-identification)을 수행하거나 [독립 실행형 식별](#standalone-language-identification)을 수행하는 동안 언어 식별을 사용할 수도 있습니다. 사용할 수 있는 언어를 확인하려면 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure 구독 및 음성 리소스가 있고 음성 인식 기본 사항을 알고 있다고 가정합니다. 아직 시작하지 않은 경우 [빠른 시작을 완료](get-started-speech-to-text.md)합니다.

## <a name="language-identification-with-speech-to-text"></a>음성 텍스트 변환을 사용하는 언어 식별

현재 언어 식별에는 1단계 인식을 위한 **4개의 언어** 와 연속 인식을 위한 **10개의 언어** 로 제한되어 있습니다. `AutoDetectSourceLanguageConfig` 개체를 구성할 때 이러한 제한을 염두에 두세요. 아래 샘플에서는 `AutoDetectSourceLanguageConfig`를 사용하여 식별할 수 있는 언어 목록을 정의한 다음, 음성 인식을 실행할 때 이러한 언어를 참조합니다.

> [!IMPORTANT]
> 연속 언어 식별은 C#, C++ 및 Python에서만 지원됩니다.

::: zone pivot="programming-language-csharp"

다음 예제에서는 1단계 인식을 실행하고 `Latency`의 우선 순위를 지정합니다. 사용 사례에 대한 우선 순위에 따라 이 속성을 `Accuracy`로 설정할 수도 있습니다. `Latency`는 대기 시간이 짧은 결과가 필요하지만(예: 라이브 스트리밍 시나리오의 경우) 오디오 샘플에서 언어를 모를 경우 사용하기에 가장 좋은 옵션입니다. 

`Accuracy`는 오디오 품질이 저하될 수 있으며 더 많은 대기 시간이 허용되는 시나리오에서 사용해야 합니다. 예를 들어 음성 메일에는 배경 노이즈가 있을 수 있으며, 처음에는 약간의 소음을 발생시킬 수 있고, 엔진에 더 많은 시간 허용하면 인식 결과가 향상됩니다.

두 경우 모두 동일한 오디오 샘플 내에서 언어가 변경될 수 있는 시나리오에는 아래와 같이 1단계 인식을 **사용하면 안 됩니다**. 이러한 유형의 시나리오에 대한 연속 인식을 보려면 아래를 참조하세요.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE", "ja-JP", "de-DE" });

using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

다음 예제에서는 다국어 시나리오에 대한 연속 음성 인식을 설정하는 방법을 보여 줍니다. 이 예제에서는 언어 구성에서만 `en-US` 및 `ja-JP`를 사용하지만 이 디자인 패턴에는 최대 **10개의 언어** 를 사용할 수 있습니다. 음성이 감지될 때마다 소스 언어가 식별되고 오디오도 텍스트 출력으로 변환됩니다. 이 예제에서는 응답 시간의 우선 순위를 지정하는 `Latency` 모드를 사용합니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
var endpointUrl = new Uri(endpointString);

var config = SpeechConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");
// can switch "Latency" to "Accuracy" depending on priority
config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP" });

var stopRecognition = new TaskCompletionSource<int>();
using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
{
    using (var recognizer = new SpeechRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
    {
        // Subscribes to events.
        recognizer.Recognizing += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizingSpeech)
            {
                Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
        };

        recognizer.Recognized += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizedSpeech)
            {
                Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
            else if (e.Result.Reason == ResultReason.NoMatch)
            {
                Console.WriteLine($"NOMATCH: Speech could not be recognized.");
            }
        };

        recognizer.Canceled += (s, e) =>
        {
            Console.WriteLine($"CANCELED: Reason={e.Reason}");

            if (e.Reason == CancellationReason.Error)
            {
                Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                Console.WriteLine($"CANCELED: Did you update the subscription info?");
            }

            stopRecognition.TrySetResult(0);
        };

        recognizer.SessionStarted += (s, e) =>
        {
            Console.WriteLine("\n    Session started event.");
        };

        recognizer.SessionStopped += (s, e) =>
        {
            Console.WriteLine("\n    Session stopped event.");
            Console.WriteLine("\nStop recognition.");
            stopRecognition.TrySetResult(0);
        };

        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

        // Waits for completion.
        // Use Task.WaitAny to keep the task rooted.
        Task.WaitAny(new[] { stopRecognition.Task });

        // Stops recognition.
        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
    }
}
```

> [!NOTE]
> `Latency` 및 `Accuracy` 모드 및 다국어 연속 인식은 현재 C#, C++ 및 Python에서만 지원됩니다.
 
::: zone-end

::: zone pivot="programming-language-cpp"

다음 예제에서는 1단계 인식을 실행하고 `Latency`의 우선 순위를 지정합니다. 사용 사례에 대한 우선 순위에 따라 이 속성을 `Accuracy`로 설정할 수도 있습니다. `Latency`는 대기 시간이 짧은 결과가 필요하지만(예: 라이브 스트리밍 사례) 오디오 샘플에서 언어를 모를 경우 사용하기에 가장 좋은 옵션입니다. 

`Accuracy`는 오디오 품질이 저하될 수 있으며 더 많은 대기 시간이 허용되는 시나리오에서 사용해야 합니다. 예를 들어 음성 메일에는 배경 노이즈가 있을 수 있으며, 처음에는 약간의 소음을 발생시킬 수 있고, 엔진에 더 많은 시간 허용하면 인식 결과가 향상됩니다.

두 경우 모두 동일한 오디오 샘플 내에서 언어가 변경될 수 있는 시나리오에는 아래와 같이 1단계 인식을 **사용하면 안 됩니다**. 이러한 유형의 시나리오에 대한 연속 인식을 보려면 아래를 참조하세요.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto speechConfig = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE", "ja-JP", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

다음 예제에서는 다국어 시나리오에 대한 연속 음성 인식을 설정하는 방법을 보여 줍니다. 이 예제에서는 언어 구성에서만 `en-US` 및 `ja-JP`를 사용하지만 이 디자인 패턴에는 최대 **10개의 언어** 를 사용할 수 있습니다. 음성이 감지될 때마다 소스 언어가 식별되고 오디오도 텍스트 출력으로 변환됩니다. 이 예제에서는 응답 시간의 우선 순위를 지정하는 `Latency` 모드를 사용합니다.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
auto endpointString = std::format("wss://{}.stt.speech.microsoft.com/speech/universal/v2", region);
auto config = SpeechConfig::FromEndpoint(endpointString, "<paste-your-subscription-key>");

config->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP" });

auto audioInput = AudioConfig::FromWavFileInput("path-to-your-audio-file.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, autoDetectSourceLanguageConfig, audioInput);

// promise for synchronization of recognition end.
promise<void> recognitionEnd;

// Subscribes to events.
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
        cout << "Recognizing in " << lidResult->Language << ": Text =" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
            cout << "RECOGNIZED in " << lidResult->Language << ": Text=" << e.Result->Text << "\n"
                << "  Offset=" << e.Result->Offset() << "\n"
                << "  Duration=" << e.Result->Duration() << std::endl;
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
                << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });

recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

> [!NOTE]
> `Latency` 및 `Accuracy` 모드 및 다국어 연속 인식은 현재 C#, C++ 및 Python에서만 지원됩니다.

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end

## <a name="standalone-language-identification"></a>독립 실행형 언어 식별

::: zone pivot="programming-language-csharp"

사용되는 소스 언어만 검색하려는 사용 사례에서 다음 코드 샘플에 표시된 것과 같이 독립 실행형 언어 식별을 사용할 수 있습니다. `SourceLanguageRecognizer`는 연속 인식 시나리오에서도 사용할 수 있습니다.

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

사용되는 소스 언어만 검색하려는 사용 사례에서 다음 코드 샘플에 표시된 것과 같이 독립 실행형 언어 식별을 사용할 수 있습니다. `SourceLanguageRecognizer`는 연속 인식 시나리오에서도 사용할 수 있습니다.

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

::: zone pivot="programming-language-java"
> [!IMPORTANT]
> 이 기능은 현재 C#, C++ 및 Python에서만 지원됩니다.
::: zone-end

::: zone pivot="programming-language-python"
> [!IMPORTANT]
> 이 기능은 현재 C#, C++ 및 Python에서만 지원됩니다.
::: zone-end

::: zone pivot="programming-language-objectivec"
> [!IMPORTANT]
> 이 기능은 현재 C#, C++ 및 Python에서만 지원됩니다.
::: zone-end

::: zone pivot="programming-language-javascript"
> [!IMPORTANT]
> 이 기능은 현재 C#, C++ 및 Python에서만 지원됩니다.
::: zone-end

## <a name="use-a-custom-model-for-language-identification"></a>언어 식별에 사용자 지정 모델 사용

Speech Service 기본 모델을 사용하는 언어 식별 외에도 향상된 인식을 위해 사용자 지정 모델을 지정할 수도 있습니다. 사용자 지정 모델이 제공되지 않으면 서비스에서 기본 언어 모델을 사용합니다.

아래 코드 조각은 Speech Service 호출에서 사용자 지정 모델을 지정하는 방법을 보여줍니다. 검색된 언어가 `en-US`이면 기본 모델이 사용됩니다. 검색된 언어가 `fr-FR`이면 사용자 지정 모델의 엔드포인트가 사용됩니다.

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end

## <a name="next-steps"></a>다음 단계

::: zone pivot="programming-language-csharp"
* 언어 식별에 대한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-cpp"
* 언어 식별에 대한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-java"
* 언어 식별에 대한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-python"
* 언어 식별에 대한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-objectivec"
* 언어 식별에 대한 GitHub의 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525)를 참조하세요.
::: zone-end

* [Speech SDK 참조 설명서](speech-sdk.md)