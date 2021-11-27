---
title: 음성 SDK를 발음 평가에 사용하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 정확도, 능숙도, 완전성 등의 지표를 사용하여 음성 입력의 발음 품질을 평가하는 발음 평가를 지원합니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 426ca92dc97fcc5ab7c545292753ad5f984df0ab
ms.sourcegitcommit: 625ffc4a23b7b71680aaf572fede507053d4ef03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2021
ms.locfileid: "133177255"
---
# <a name="pronunciation-assessment"></a>발음 평가

발음 평가는 음성 발음을 평가하고 음성 오디오의 정확도와 능숙도에 대한 피드백을 발표자에게 제공합니다. 발음 평가를 통해 언어 학습자는 자신 있게 말하고 발표할 수 있도록 연습하고, 즉각적인 피드백을 받고, 발음을 개선할 수 있습니다. 교육자는 이 기능을 사용하여 여러 발표자의 발음을 실시간으로 평가할 수 있습니다.

이 문서에서는 Speech SDK를 사용하여 `PronunciationAssessmentConfig`를 설정하고 `PronunciationAssessmentResult`를 검색하는 방법을 알아봅니다.

> [!NOTE]
> 로캘에 대 한 발음 평가 `en-US` 는 모든 음성- [텍스트 지역](regions.md#speech-to-text-text-to-speech-and-translation)에서 사용할 수 있습니다. 및 로캘에 대 한 지원은 `en-GB` `zh-CN` 미리 보기 상태입니다.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Speech SDK를 사용한 발음 평가

다음 코드 조각에서는를 만든 다음에 적용 하는 방법을 보여 줍니다 `PronunciationAssessmentConfig` `SpeechRecognizer` .

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=speechsdk.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=speechsdk.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

## <a name="configuration-parameters"></a>구성 매개 변수

이 표에서는 발음 평가에 대한 구성 매개 변수를 나열합니다.

| 매개 변수 | 설명 | 필수 여부 |
|-----------|-------------|---------------------|
| `ReferenceText` | 발음이 평가되는 기준 텍스트입니다. | 필수 |
| `GradingSystem` | 점수 보정을 위한 지점 시스템입니다. `FivePoint` 시스템은 0~5 부동 소수점 점수를 제공하고 `HundredMark`는 0~100 부동 소수점 점수를 제공합니다. 기본값: `FivePoint`. | 선택 사항 |
| `Granularity` | 평가 세분성입니다. 허용되는 값은 전체 텍스트, 단어, 음소 수준에 대한 점수를 표시하는 `Phoneme`, 전체 텍스트 및 단어 수준에 대한 점수를 표시하는 `Word`, 전체 텍스트 수준에 대한 점수만 표시하는 `FullText`입니다. 기본값: `Phoneme`. | 선택 사항 |
| `EnableMiscue` | 발음 단어가 참조 텍스트와 비교 될 때 miscue 계산을 사용 하도록 설정 합니다. 이 값이 이면 `True` `ErrorType` 결과 값이 `Omission` 비교에 따라 또는로 설정 될 수 있습니다 `Insertion` . 허용되는 값은 `False` 및 `True`입니다. 기본값: `False`. | 선택 사항 |
| `ScenarioId` | 사용자 지정된 지점 시스템을 나타내는 GUID입니다. | 선택 사항 |

## <a name="result-parameters"></a>결과 매개 변수

이 표에는 발음 평가의 결과 매개 변수가 나열되어 있습니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| `AccuracyScore` | 음성의 발음 정확도입니다. 정확도는 음소가 원어민의 발음에 얼마나 근접하게 일치하는지를 나타냅니다. 단어 및 전체 텍스트 수준 정확도 점수는 음소 수준 정확도 점수에서 집계 됩니다. |
| `FluencyScore` | 지정된 음성의 능숙도입니다. 능숙도는 음성이 원어민이 사용하는 단어 사이의 무음 분리에 얼마나 근접하게 일치하는지를 나타냅니다. |
| `CompletenessScore` | 음성의 완전성으로, 입력 참조 텍스트에 대 한 단어를 발음 하는 비율로 계산 됩니다. |
| `PronScore` | 지정된 음성의 발음 품질을 나타내는 전체 점수입니다. 이는 `AccuracyScore` , 및에서 `FluencyScore` `CompletenessScore` 가중치를 사용 하 여 집계 됩니다. |
| `ErrorType` | 이 값은과 비교 하 여 단어가 생략, 삽입 또는 mispronounced 여부를 나타냅니다 `ReferenceText` . 가능한 값은 `None`, `Omission`, `Insertion` 및 `Mispronunciation`입니다. |

### <a name="sample-responses"></a>샘플 응답

JSON의 일반적인 발음 평가 결과:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계

* 발음 평가에 대한 [비디오 소개](https://www.youtube.com/watch?v=cBE8CUHOFHQ) 및 [비디오 자습서](https://www.youtube.com/watch?v=zFlwm7N4Awc) 보기

* [발음 평가 데모](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)를 사용해 보세요.

::: zone pivot="programming-language-csharp"
* 발음 평가는 GitHub에서 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-cpp"
* 발음 평가는 GitHub에서 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-java"
* 발음 평가는 GitHub에서 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-python"
* 발음 평가는 GitHub에서 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576)를 참조하세요.
::: zone-end

::: zone pivot="programming-language-objectivec"
* 발음 평가는 GitHub에서 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642)를 참조하세요.
::: zone-end

* [Speech SDK 참조 설명서](speech-sdk.md)

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
