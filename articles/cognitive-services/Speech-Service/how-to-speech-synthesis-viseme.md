---
title: 립싱크를 위한 얼굴 포즈 이벤트를 가져오는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치와 같은 관찰된 음성의 주요 포즈를 나타내는 음성 합성 중 viseme 이벤트를 지원합니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 3601cd6f7580a4d87dda7488826e25ca85b233c9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654201"
---
# <a name="get-facial-pose-events"></a>얼굴 포즈 이벤트 가져오기

> [!NOTE]
> Viseme 이벤트는 현재 `en-US` 영어(미국) [신경망 음성](language-support.md#text-to-speech)에만 사용할 수 있습니다.

_viseme_ 은 음성 언어로 된 음소의 시각적 설명입니다.
단어를 말할 때 얼굴 및 입의 위치를 정의합니다.
각 viseme은 특정 음소 세트에 대한 주요 얼굴 포즈를 묘사합니다.
Viseme를 사용하여 2D 및 3D 아바타 모델의 움직임을 제어하면 입 모양을 합성 음성과 완벽하게 일치시킬 수 있습니다.

Viseme를 사용하면 아바타를 더 쉽게 사용하고 제어할 수 있습니다. Viseme를 사용하면 다음을 수행할 수 있습니다.

 * 고객을 위한 다중 모드 통합 서비스를 구축하여 지능형 키오스크를 위한 **애니메이션 가상 음성 도우미** 를 만듭니다.
 * **몰입형 뉴스 방송** 을 만들고 자연스러운 얼굴과 입의 움직임으로 대상 그룹의 경험을 개선합니다.
 * 동적 콘텐츠로 대화할 수 있는 더 많은 **대화형 게임 아바타 및 만화 캐릭터** 를 생성합니다.
 * 언어 학습자가 각 단어와 음소의 입 동작을 이해하는 데 도움이 되는 **효과적인 언어 교육 비디오** 를 만듭니다.
 * 청각 장애가 있는 사용자는 시각적으로 소리를 포착할 수 있고 애니메이션 얼굴의 viseme을 보여주는 **"입술 읽기"** 음성 콘텐츠를 선택할 수도 있습니다.

viseme의 [소개 비디오](https://youtu.be/ui9XT47uwxs)를 참조하세요.
> [!VIDEO https://www.youtube.com/embed/ui9XT47uwxs]

## <a name="azure-neural-tts-can-produce-visemes-with-speech"></a>Azure 신경 TTS를 사용해 음성으로 viseme 생성

신경 음성은 입력 텍스트 또는 SSML(Speech Synthesis Markup Language)을 합성된 음성으로 변환합니다. 음성 오디오 출력에는 viseme ID 및 해당 오프셋 타임스탬프가 포함될 수 있습니다. 각 viseme ID는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치와 같이 관찰된 음성에서 특정 포즈를 지정합니다. 2D 또는 3D 렌더링 엔진을 사용하면 이러한 viseme 이벤트로 아바타에 애니메이션을 적용할 수 있습니다.

viseme의 전체 워크플로는 아래 순서도에 설명되어 있습니다.

![Viseme의 전체 워크플로](media/text-to-speech/viseme-structure.png)

| 매개 변수 | Description |
|-----------|-------------|
| Viseme ID | viseme을 지정하는 정수입니다. 영어(미국)에서는 특정 음소 세트의 입 모양을 묘사하기 위해 22개의 다른 viseme을 제공합니다. viseme과 음소 간에는 일대일 대응이 없습니다. `s` 및 `z`와 같이 여러 가지 음소가 생성될 때 얼굴에서 동일하게 보이기 때문에 종종 몇 가지 음소는 단일 viseme에 해당합니다. [Viseme ID와 음소 간의 매핑 테이블](#map-phonemes-to-visemes)을 참조하세요.  |
| 오디오 오프셋 | 각 viseme의 시작 시간(틱 단위(100나노초)). |

## <a name="get-viseme-events-with-the-speech-sdk"></a>Speech SDK를 사용하여 viseme 이벤트 가져오기

합성 음성으로 viseme를 가져오려면 Speech SDK에서 `VisemeReceived` 이벤트를 구독합니다.
다음 코드 조각은 viseme 이벤트를 구독하는 방법을 보여줍니다.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

다음은 viseme 출력의 예제입니다.

```text
(Viseme), Viseme ID: 1, Audio offset: 200ms.

(Viseme), Viseme ID: 5, Audio offset: 850ms.

……

(Viseme), Viseme ID: 13, Audio offset: 2350ms.
```

viseme 출력을 가져온 후 이러한 이벤트를 사용하여 문자 애니메이션을 수행할 수 있습니다. 나만의 캐릭터를 만들고 자동으로 캐릭터에 애니메이션을 적용할 수 있습니다.

2D 캐릭터의 경우 시나리오에 맞는 캐릭터를 디자인하고 각 viseme ID에 대해 SVG(Scalable Vector Graphics)를 사용하여 시간 기반 얼굴 위치를 얻을 수 있습니다. viseme 이벤트에서 제공되는 임시 태그를 사용하면 잘 디자인된 이러한 SVG가 다듬어져 사용자에게 강력한 애니메이션을 제공합니다. 예를 들어 아래 그림은 언어 학습을 위해 디자인된 빨간색 입술 캐릭터를 보여줍니다.

![2D 렌더링 예제](media/text-to-speech/viseme-demo-2D.png)

3D 캐릭터의 경우 캐릭터를 문자열 꼭두각시 인형으로 생각하세요. 꼭두각시 인형의 마스터는 한 상태에서 다른 상태로 줄을 당기고 나머지는 물리 법칙을 통해 수행되어 꼭두각시 인형이 유동적으로 움직이도록 합니다. viseme 출력은 작업 시간 표시를 제공하는 꼭두각시 인형의 마스터 역할을 합니다. 애니메이션 엔진은 작업의 물리 법칙을 정의합니다. 프레임을 여유 알고리즘으로 보간함으로써 엔진은 고품질 애니메이션을 추가로 생성할 수 있습니다.

## <a name="map-phonemes-to-visemes"></a>viseme에 음소 매핑

Viseme은 언어에 따라 달라집니다. 각 언어에는 특정 음소에 해당하는 viseme 세트가 있습니다. 다음 표에서는 IPA(International Phonetic Alphabet) 음소와 영어(미국)의 viseme ID 간의 상관 관계를 보여줍니다.

| IPA | 예제 | Viseme ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|일  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|분  |   **m** at, s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|초  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
