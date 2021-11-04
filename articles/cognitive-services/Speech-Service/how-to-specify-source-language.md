---
title: 음성 텍스트 변환에 대한 소스 언어를 지정하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하면 음성을 텍스트로 변환할 때 소스 언어를 지정할 수 있습니다. 이 문서에서는 FromConfig 및 SourceLanguageConfig 메서드를 사용하여 Speech Service에 소스 언어를 알리고 사용자 지정 모델 대상을 제공하는 방법을 설명합니다.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8ef79c841fcbaecb2b70ad0a95ce04c9628e4709
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508291"
---
# <a name="specify-source-language-for-speech-to-text"></a>음성 텍스트 변환에 대한 소스 언어 지정

이 문서에서는 음성 인식을 위해 Speech SDK에 전달된 오디오 입력의 소스 언어를 지정하는 방법을 알아봅니다. 또한 인식 개선을 위해 Custom Speech 모델을 지정하는 코드 예가 제공됩니다.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>C#에서 소스 언어를 지정하는 방법

다음 예에서는 소스 언어가 `SpeechRecognizer` 구문을 사용하여 매개 변수로 명시적으로 제공됩니다.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

다음 예에서는 소스 언어가 `SourceLanguageConfig`을 사용하여 제공됩니다. 그런 다음 `sourceLanguageConfig`이 `SpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

다음 예에서 소스 언어 및 사용자 지정 엔드포인트는 `SourceLanguageConfig`을 사용하여 제공됩니다. 그런 다음 `sourceLanguageConfig`가 `SpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` 및 `EndpointId` set 메서드는 C#의 `SpeechConfig` 클래스에서 더 이상 사용되지 않습니다. 이러한 메서드의 사용은 권장되지 않으므로 `SpeechRecognizer`를 구성할 때 사용하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>C++에서 소스 언어를 지정하는 방법

다음 예에서 소스 언어는 `FromConfig` 메서드를 사용하여 매개 변수로 명시적으로 제공됩니다.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

다음 예에서는 소스 언어가 `SourceLanguageConfig`를 사용하여 제공됩니다. 그런 다음 `recognizer`를 작성할 때 `sourceLanguageConfig`가 `FromConfig`에 매개 변수로 전달됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

다음 예에서 소스 언어 및 사용자 지정 엔드포인트는 `SourceLanguageConfig`를 사용하여 제공됩니다. 그런 다음 `recognizer`를 작성할 때 `sourceLanguageConfig`가 `FromConfig`에 매개 변수로 전달됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 및 `SetEndpointId`는 C++ 및 Java의 `SpeechConfig` 클래스에서 더 이상 사용되지 않는 메서드입니다. 이러한 메서드의 사용은 권장되지 않으므로 `SpeechRecognizer`를 구성할 때 사용하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java에서 소스 언어를 지정하는 방법

다음 예에서는 새 `SpeechRecognizer`를 작성할 때 소스 언어가 명시적으로 제공됩니다.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

다음 예에서는 소스 언어가 `SourceLanguageConfig`를 사용하여 제공됩니다. 그런 다음 새 `SpeechRecognizer`를 작성할 때 `sourceLanguageConfig`가 매개 변수로 전달됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

다음 예에서 소스 언어 및 사용자 지정 엔드포인트는 `SourceLanguageConfig`를 사용하여 제공됩니다. 그런 다음 새 `SpeechRecognizer`를 작성할 때 `sourceLanguageConfig`가 매개 변수로 전달됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 및 `setEndpointId`는 C++ 및 Java의 `SpeechConfig` 클래스에서 더 이상 사용되지 않는 메서드입니다. 이러한 메서드의 사용은 권장되지 않으므로 `SpeechRecognizer`를 구성할 때 사용하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python에서 소스 언어를 지정하는 방법

다음 예에서는 소스 언어가 `SpeechRecognizer` 구문을 사용하여 매개 변수로 명시적으로 제공됩니다.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

다음 예에서는 소스 언어가 `SourceLanguageConfig`을 사용하여 제공됩니다. 그런 다음 `SourceLanguageConfig`이 `SpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

다음 예에서 소스 언어 및 사용자 지정 엔드포인트는 `SourceLanguageConfig`을 사용하여 제공됩니다. 그런 다음 `SourceLanguageConfig`가 `SpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` 및 `endpoint_id` 속성은 Python의 `SpeechConfig` 클래스에서 더 이상 사용되지 않습니다. 이러한 속성은 사용하지 않는 것이 좋으며 `SpeechRecognizer`를 생성할 때 사용해서는 안 됩니다.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Javascript에서 소스 언어를 지정하는 방법

첫 번째 단계는 `SpeechConfig`를 만드는 것입니다.

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

다음으로 `speechRecognitionLanguage`를 사용하여 오디오의 소스 언어를 지정합니다.

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

사용자 지정 모델을 인식에 사용하는 경우 `endpointId`로 엔드포인트를 지정할 수 있습니다.

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Objective-C에서 소스 언어를 지정하는 방법

다음 예에서는 소스 언어가 `SPXSpeechRecognizer` 구문을 사용하여 매개 변수로 명시적으로 제공됩니다.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

다음 예에서는 소스 언어가 `SPXSourceLanguageConfiguration`을 사용하여 제공됩니다. 그런 다음 `SPXSourceLanguageConfiguration`이 `SPXSpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

다음 예에서 소스 언어 및 사용자 지정 엔드포인트는 `SPXSourceLanguageConfiguration`을 사용하여 제공됩니다. 그런 다음 `SPXSourceLanguageConfiguration`이 `SPXSpeechRecognizer` 구성에 매개 변수로 전달됩니다.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` 및 `endpointId` 속성은 Objective-C의 `SPXSpeechConfiguration` 클래스에서 더 이상 사용되지 않습니다. 이러한 속성은 사용하지 않는 것이 좋으며 `SPXSpeechRecognizer`를 생성할 때 사용해서는 안 됩니다.

::: zone-end

## <a name="see-also"></a>참고 항목

* 음성 텍스트 변환에 지원되는 언어 및 로캘 목록은 [언어 지원](language-support.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
