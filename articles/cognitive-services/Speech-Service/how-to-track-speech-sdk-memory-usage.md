---
title: Speech SDK 메모리 사용량을 추적하는 방법 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service SDK는 음성 번역과 함께 음성 텍스트 변환 및 텍스트 음성 변환을 위해 다양한 프로그래밍 언어를 지원합니다. 이 문서에서는 SDK에 기본 제공되는 메모리 관리 도구에 대해 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: programming-languages-set-two
ROBOTS: NOINDEX
ms.openlocfilehash: 21161b9121708c805818210561f8a3ba546b354c
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133097908"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Speech SDK 메모리 사용량을 추적하는 방법

Speech SDK는 일련의 상호 운용성 계층을 통해 여러 프로그래밍 언어로 프로젝션되는 네이티브 코드베이스를 기반으로 합니다. 각 언어별 프로젝션에는 개체 수명 주기를 관리하기 위해 관용적으로 올바른 기능이 있습니다. 또한 Speech SDK에는 개체 로깅 및 개체 제한을 사용하여 리소스 사용량을 추적하기 위한 메모리 관리 도구가 포함되어 있습니다. 

## <a name="how-to-read-object-logs"></a>개체 로그를 읽는 방법

[Speech SDK 로깅이 사용하도록 설정](how-to-use-logging.md)된 경우 추적 태그가 생성되어 개체 기록을 관찰할 수 있습니다. 이러한 태그는 다음과 같습니다. 

* `TrackHandle` 또는 `StopTracking` 
* 개체 유형
* 개체의 유형을 추적하는 현재 개체 수와 추적 중인 현재 개체 수입니다.

다음은 샘플 로그입니다. 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>경고 임계값 설정

경고 임계값을 생성하는 옵션이 있으며 이 임계값을 초과하면(로깅이 사용하도록 설정된 경우) 경고 메시지가 기록됩니다. 경고 메시지에는 해당 개체 수와 함께 존재하는 모든 개체의 덤프가 포함됩니다. 이 정보를 통해 문제를 더 잘 이해할 수 있습니다. 

경고 임계값을 사용하려면 `SpeechConfig` 개체에 경고 임계값을 지정해야 합니다. 이 개체는 새 인식기가 생성될 때 확인됩니다. 다음 예에서는 `config`라고 하는 `SpeechConfig` 인스턴스를 생성했다고 가정합니다.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 10,000입니다.

## <a name="set-an-error-threshold"></a>오류 임계값 설정 

Speech SDK를 사용하여 지정된 시간에 허용되는 최대 개체 수를 설정할 수 있습니다. 이 설정을 사용하는 경우 최대 수에 도달하면 새 인식기 개체를 만들려고 할 때 작업이 실패합니다. 기존 보고서는 계속 작동합니다.

다음은 샘플 경고입니다.

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
Handle table dump by object type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

경고 임계값을 사용하려면 `SpeechConfig` 개체에 경고 임계값을 지정해야 합니다. 이 개체는 새 인식기가 생성될 때 확인됩니다. 다음 예에서는 `config`라고 하는 `SpeechConfig` 인스턴스를 생성했다고 가정합니다.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 `size_t` 데이터 형식의 플랫폼별 최댓값입니다. 일반적인 인식은 7~10개의 내부 개체를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK에 대한 자세한 정보](speech-sdk.md)
