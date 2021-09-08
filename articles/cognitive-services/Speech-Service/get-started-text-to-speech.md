---
title: 텍스트 음성 변환 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 빠른 시작에서는 개체 구성 및 디자인 패턴, 지원되는 오디오 출력 형식, Speech CLI, 음성 합성을 위한 사용자 지정 구성 옵션에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/17/2021
ms.author: pafarley
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: 텍스트 음성 변환
ms.openlocfilehash: 20b0bc23fb68eec97b4470004a91b903d449b8c1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539379"
---
# <a name="get-started-with-text-to-speech"></a>텍스트 음성 변환 시작

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>위치 정보 가져오기

프로젝트가 음성 타이밍에 따라 특정 작업을 수행할 수 있도록 텍스트 음성 변환에서 단어를 말하는 시간을 알아야 하는 경우가 있습니다.
예를 들어 읽는 단어를 강조 표시하려면 어떤 단어를, 언제, 얼마나 오랫동안 강조 표시해야 하는지 알아야 합니다.

`SpeechSynthesizer` 내에서 제공하는 `WordBoundary` 이벤트를 사용하여 이를 수행할 수 있습니다.
이 이벤트는 각각의 새 단어를 읽기 시작할 때 발생하며 음성 스트림 내의 시간 오프셋 및 입력 프롬프트 내의 텍스트 오프셋을 제공합니다.

* `AudioOffset`은 합성의 시작과 다음 단어의 시작 사이에 출력 오디오의 경과 시간을 보고합니다. 이 값은 100HNS(나노초 단위)를 사용하며 10,000HNS는 1밀리초입니다.
* `WordOffset`은 읽기 직전의 단어 바로 앞에 있는 입력 문자열(원래 텍스트 또는 [SSML](speech-synthesis-markup.md))의 문자 위치를 보고합니다.

> [!NOTE]
> `WordBoundary` 이벤트는 출력 오디오 데이터를 사용할 수 있게 될 때 발생하며, 출력 디바이스로 재생하는 것보다 빠릅니다. 호출자는 스트림 타이밍을 "실제 시간"과 적절하게 동기화해야 합니다.

`WordBoundary` 사용 예제는 GitHub의 [텍스트 음성 변환 샘플](https://aka.ms/csspeech/samples)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Custom Voice 시작](how-to-custom-voice.md)
* [SSML을 사용하여 합성 향상](speech-synthesis-markup.md)
* 책 또는 뉴스 문서와 같은 대형 텍스트 샘플에 [긴 오디오 API](long-audio-api.md)를 사용하는 방법에 대해 알아봅니다.
* GitHub의 [빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)을 참조하세요.
