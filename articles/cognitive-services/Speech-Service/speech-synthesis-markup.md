---
title: SSML(Speech Synthesis Markup Language) - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Synthesis Markup Language를 사용하여 텍스트 음성 변환의 발음 및 운율을 제어합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: eur
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: dbb9f42f7f0b58b14ae9a5c8824475c0fc15fd05
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505084"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>SSML(Speech Synthesis Markup Language)을 사용하여 합성 향상

SSML(Speech Synthesis Markup Language)은 텍스트 음성 변환 서비스를 사용하여 입력 텍스트를 합성 음성으로 변환하는 방법을 개발자가 지정할 수 있는 XML 기반 태그 언어입니다. 일반 텍스트와 비교할 때, SSML은 개발자가 텍스트 음성 변환 출력의 피치, 발음, 말하기 속도, 볼륨 등을 세밀하게 조정할 수 있습니다. 마침표 뒤에서 잠시 쉬거나 문장이 물음표로 끝날 때 올바른 억양을 사용하는 등의 일반 문장 부호는 자동으로 처리됩니다.

SSML의 Speech Service 구현은 World Wide Web 컨소시엄의 [Speech Synthesis Markup Language 버전 1.0](https://www.w3.org/TR/2004/REC-speech-synthesis-20040907/)을 기반으로 합니다.

> [!IMPORTANT]
> 중국어, 일본어 및 한국어 문자는 요금 청구 시 두 글자로 계산됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="neural-and-custom-voices"></a>인공신경망 및 사용자 지정 음성

인간과 유사한 신경망 음성을 사용하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만듭니다. 지원되는 언어, 로캘 및 음성의 전체 목록은 [언어 지원](language-support.md)을 참조하세요. 인공신경망 및 사용자 지정 음성에 대한 자세한 내용은 [텍스트 음성 변환 개요](text-to-speech.md)를 참조하세요.


> [!NOTE]
> [텍스트 음성 변환 페이지](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)를 사용하여 다양한 스타일과 피치의 음성으로 예제 텍스트를 들을 수 있습니다.


## <a name="special-characters"></a>특수 문자

SSML를 사용할 때 따옴표, 아포스트로피, 대괄호 등의 특수 문자를 이스케이프 처리해야 합니다. 자세한 내용은 [XML(Extensible Markup Language) 1.0: 부록 D](https://www.w3.org/TR/xml/#sec-entexpand)를 참조하세요.

## <a name="supported-ssml-elements"></a>지원되는 SSML 요소

각 SSML 문서는 SSML 요소(또는 태그)를 사용하여 생성됩니다. 이러한 요소는 피치, 운율, 볼륨 등을 조정하는 데 사용됩니다. 다음 섹션에서는 각 요소를 사용하는 방법 및 요소가 필수인 경우와 선택 사항인 경우에 대해 자세히 설명합니다.

> [!IMPORTANT]
> 잊지 말고 특성 값을 큰따옴표로 묶어야 합니다. 올바른 형식의 유효한 XML 표준에서는 특성 값을 큰따옴표로 묶을 것을 요구합니다. 예를 들어 `<prosody volume="90">`은 올바르고 유효한 형식이지만 `<prosody volume=90>`은 그렇지 않습니다. SSML에서는 따옴표로 묶지 않은 특성 값이 인식되지 않을 수 있습니다.

## <a name="create-an-ssml-document"></a>SSML 문서 만들기

`speak`는 루트 요소이며 모든 SSML 문서에 **필수** 입니다. `speak` 요소에는 버전, 언어, 태그 어휘 정의 등의 중요한 정보가 포함되어 있습니다.

**구문**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `version` | 문서 태그를 해석하는 데 사용되는 SSML 사양의 버전을 나타냅니다. 현재 버전은 1.0입니다. | 필수 |
| `xml:lang` | 루트 문서의 언어를 지정합니다. 이 값에는 소문자 2자 언어 코드(예: `en`) 또는 언어 코드와 대문자 국가/지역(예: `en-US`)이 포함될 수 있습니다. | 필수 |
| `xmlns` | SSML 문서의 태그 어휘(요소 형식 및 특성 이름)를 정의하는 문서의 URI를 지정합니다. 현재 URI는 http://www.w3.org/2001/10/synthesis 입니다. | 필수 |

## <a name="choose-a-voice-for-text-to-speech"></a>텍스트 음성 변환의 음성 선택

`voice` 요소는 필수입니다. 텍스트 음성 변환에 사용되는 음성을 지정하는 데 사용됩니다.

**구문**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환 출력에 사용되는 음성을 식별합니다. 지원되는 전체 음성 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요. | 필수 |

**예제**

> [!NOTE]
> 이 예제에서는 `en-US-ChristopherNeural` 음성을 사용합니다. 지원되는 전체 음성 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용

`speak` 요소 내에서 텍스트 음성 변환 출력의 여러 음성을 지정할 수 있습니다. 이러한 음성은 서로 언어를 사용할 수 있습니다. 각 음성의 텍스트를 `voice` 요소에 래핑해야 합니다.

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환 출력에 사용되는 음성을 식별합니다. 지원되는 전체 음성 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요. | 필수 |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-ChristopherNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>말하기 스타일 조정

기본적으로 텍스트 음성 변환 서비스는 인공신경망 음성에 중립 음성 스타일을 사용하여 텍스트를 합성합니다. `mstts:express-as` 요소를 통해 말하기 스타일을 조정하여 명랑함, 공감, 차분함 등의 다양한 감정을 표현하거나 고객 서비스, 뉴스캐스트, 음성 도우미 등의 다양한 시나리오에 맞게 음성을 최적화할 수 있습니다. 이 요소는 Speech Service의 고유한 선택적 요소입니다.

현재 말하기 스타일 조정을 지원하는 인공신경망 음성은 다음과 같습니다.
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `en-US-SaraNeural`
* `ja-JP-NanamiNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural`
* `zh-CN-XiaohanNeural`
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`
* `zh-CN-XiaoruiNeural`
* `zh-CN-XiaoshuangNeural`

사용 사례에 맞게 말하기 스타일의 강도를 추가로 변경할 수 있습니다. `styledegree`에서 더 강한 스타일 또는 더 부드러운 스타일을 지정하여 음성에 감정을 더 싣거나 더 차분하게 말할 수 있습니다. 현재 중국어(북경어, 간체) 인공신경망 음성에 대한 말하기 스타일 조정을 지원합니다.

말하기 스타일과 스타일 정도를 조정하는 기능 외에도, `role` 음성을 조정하여 다른 연령 및 성별을 모방할 수 있습니다. 예를 들어 남자 음성의 피치를 높이고 억양을 변경하여 여자 음성을 모방할 수 있지만 음성 이름은 변경되지 않습니다. 현재 중국어(북경어, 간체) 인공신경망 음성에 대한 역할 조정을 지원합니다.
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

위의 변경 내용은 문장 수준에서 적용되며 스타일 및 롤플레이는 음성에 따라 다릅니다. 스타일 또는 롤플레이가 지원되지 않는 경우 서비스에서는 기본적인 중립적 말하기 스타일로 음성을 반환합니다. [음성 목록 API](rest-text-to-speech.md#get-a-list-of-voices) 또는 코드 없는 [오디오 콘텐츠 만들기](https://aka.ms/audiocontentcreation) 플랫폼을 통해 각 음성에 지원되는 스타일 및 역할을 확인할 수 있습니다.

**구문**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> 현재 `styledegree`는 중국어(북경어, 간체) 인공신경망 음성만 지원합니다. `role`은 zh-CN-XiaomoNeural 및 zh-CN-XiaoxuanNeural만 지원합니다.

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `style` | 말하기 스타일을 지정합니다. 현재 말하기 스타일은 음성에만 적용됩니다. | 인공신경망 음성의 말하기 스타일을 조정하는 경우에 필요합니다. `mstts:express-as`를 사용하는 경우 스타일을 제공해야 합니다. 잘못된 값을 제공하면 이 요소가 무시됩니다. |
| `styledegree` | 말하기 스타일의 강도를 지정합니다. **허용되는 값**: 0.01~2(포함). 기본값은 미리 정의된 스타일 강도를 의미하는 1입니다. 최소 단위인 0.01은 대상 스타일의 성향을 약간 보입니다. 값이 2이면 기본 스타일 강도는 두 배가 됩니다.  | 선택 사항(현재 `styledegree`는 중국어(북경어, 간체) 인공신경망 음성만 지원)|
| `role` | 말하기 롤플레이를 지정합니다. 음성이 다른 연령과 성별을 모방하지만 음성 이름은 변경되지 않습니다.  | 선택 사항(현재 `role`은 zh-CN-XiaomoNeural 및 zh-CN-XiaoxuanNeural만 지원)|

다음 표를 사용하여 각 인공신경망 음성에서 지원할 말하기 스타일을 결정하세요.

| 음성                   | 스타일                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | 뉴스 보도에 적합한 정중하고 신뢰할 수 있고 권위 있는 톤을 표현합니다. |
|                         | `style="newscast-casual"` | 일반 뉴스 보도에 적합한 가볍고 자유로운 톤을 표현합니다.        |
|                         | `style="narration-professional"` | 콘텐츠 읽기에 적합한 전문적이고 객관적인 톤을 표현합니다.        |
|                         | `style="customerservice"` | 고객 지원에 적합한 친근하고 친절한 톤을 표현합니다.  |
|                         | `style="chat"`            | 가볍고 편안한 톤을 표현합니다.                         |
|                         | `style="cheerful"`        | 긍정적이고 즐거운 톤을 표현합니다.                         |
|                         | `style="empathetic"`      | 사려 깊고 이해심 많은 톤을 표현합니다.               |
| `en-US-JennyNeural`     | `style="customerservice"` | 고객 지원에 적합한 친근하고 친절한 톤을 표현합니다.  |
|                         | `style="chat"`            | 가볍고 편안한 톤을 표현합니다.                         |
|                         | `style="assistant"`       | 디지털 비서에 적합한 따뜻하고 편안한 톤을 표현합니다.    |
|                         | `style="newscast"`        | 일반 뉴스 보도에 적합한 가볍고 자유로운 톤을 표현합니다.   |
| `en-US-GuyNeural`       | `style="newscast"`        | 뉴스 내레이션에 적합한 정중하고 전문적인 톤을 표현합니다. |
| `en-US-SaraNeural`      | `style="cheerful"`        | 긍정적이고 즐거운 톤을 표현합니다.    |
|                         | `style="sad"`             | Sorrowful을 나타냅니다.   |
|                         | `style="angry"`           | 화난 및 표정이 톤을 나타냅니다.   |
| `ja-JP-NanamiNeural`    | `style="cheerful"`        | 긍정적이고 즐거운 톤을 표현합니다.   |
|                         | `style="chat"`            | 가볍고 편안한 톤을 표현합니다.   |
|                         | `style="customerservice"` | 고객 지원에 적합한 친근하고 친절한 톤을 표현합니다.    |
| `pt-BR-FranciscaNeural` | `style="calm"`            | 말할 때 냉정하고 침착하고 차분한 태도를 표현합니다. 톤, 피치 및 운율은 다른 종류의 음성에 비해 훨씬 균일합니다.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | 뉴스 내레이션에 적합한 정중하고 전문적인 톤을 표현합니다. |
|                         | `style="customerservice"` | 고객 지원에 적합한 친근하고 친절한 톤을 표현합니다.  |
|                         | `style="assistant"`       | 디지털 비서에 적합한 따뜻하고 편안한 톤을 표현합니다.    |
|                         | `style="chat"`            | 잡담에 적합한 가볍고 편안한 톤을 표현합니다.           |
|                         | `style="calm"`            | 말할 때 냉정하고 침착하고 차분한 태도를 표현합니다. 톤, 피치 및 운율은 다른 종류의 음성에 비해 훨씬 균일합니다.                                |
|                         | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                         |
|                         | `style="sad"`             | 높은 음, 낮은 강도, 낮은 에너지의 목소리로 슬픈 톤을 표현합니다. 이 감정을 표현하는 일반적인 방법은 말하는 중에 훌쩍이거나 우는 것입니다.            |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                          |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.              |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.          |
|                         | `style="affectionate"`    | 높은 음과 에너지 넘치는 목소리로 따뜻하고 다정한 톤을 표현합니다. 말하는 사람이 듣는 사람의 주의를 집중시키는 상태입니다. 이와 같은 "개성"을 보이는 사람은 매력을 타고난 경우가 많습니다.          |
|                         | `style="gentle"`          | 낮은 음과 에너지 넘치는 목소리로 부드럽고 공손하고 상냥한 톤을 표현합니다.         |
|                         | `style="lyrical"`         | 노래를 부르는 듯한 감성적인 방식으로 감정을 표현합니다.         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | 고객 지원에 적합한 친근하고 친절한 톤을 표현합니다.  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | 말할 때 냉정하고 침착하고 차분한 태도를 표현합니다. 톤, 피치 및 운율은 다른 종류의 음성에 비해 훨씬 균일합니다.    |
|                         | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                         |
|                         | `style="sad"`             | 높은 음, 낮은 강도, 낮은 에너지의 목소리로 슬픈 톤을 표현합니다. 이 감정을 표현하는 일반적인 방법은 말하는 중에 훌쩍이거나 우는 것입니다.            |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                          |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.              |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.          |
|   `zh-CN-YunxiNeural`   | `style="assistant"`       | 디지털 비서에 적합한 따뜻하고 편안한 톤을 표현합니다.    |
|                         | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                         |
|                         | `style="sad"`             | 높은 음, 낮은 강도, 낮은 에너지의 목소리로 슬픈 톤을 표현합니다. 이 감정을 표현하는 일반적인 방법은 말하는 중에 훌쩍이거나 우는 것입니다.            |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                          |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.              |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.    |
|                         | `style="depressed"`       | 낮은 음과 힘 없는 목소리로 우울하고 낙담한 톤을 표현합니다.    |
|                         | `style="embarrassed"`     | 말하는 사람이 불편함을 느낄 때 불확실하고 망설이는 톤을 표현합니다.   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                         |
|                         | `style="sad"`             | 높은 음, 낮은 강도, 낮은 에너지의 목소리로 슬픈 톤을 표현합니다. 이 감정을 표현하는 일반적인 방법은 말하는 중에 훌쩍이거나 우는 것입니다.            |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                          |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.              |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.    |
|                         | `style="embarrassed"`     | 말하는 사람이 불편함을 느낄 때 불확실하고 망설이는 톤을 표현합니다.   |
|                         | `style="affectionate"`    | 높은 음과 에너지 넘치는 목소리로 따뜻하고 다정한 톤을 표현합니다. 말하는 사람이 듣는 사람의 주의를 집중시키는 상태입니다. 이와 같은 "개성"을 보이는 사람은 매력을 타고난 경우가 많습니다.          |
|                         | `style="gentle"`          | 낮은 음과 에너지 넘치는 목소리로 부드럽고 공손하고 상냥한 톤을 표현합니다.         |
| `zh-CN-XiaomoNeural`    | `style="calm"`            | 말할 때 냉정하고 침착하고 차분한 태도를 표현합니다. 톤, 피치 및 운율은 다른 종류의 음성에 비해 훨씬 균일합니다.                         |
|                         | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                 |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                       |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.         |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.  |
|                         | `style="depressed"`       | 낮은 음과 힘 없는 목소리로 우울하고 낙담한 톤을 표현합니다.    |
|                         | `style="gentle"`          | 낮은 음과 에너지 넘치는 목소리로 부드럽고 공손하고 상냥한 톤을 표현합니다.         |
| `zh-CN-XiaoxuanNeural`  | `style="calm"`            | 말할 때 냉정하고 침착하고 차분한 태도를 표현합니다. 톤, 피치 및 운율은 다른 종류의 음성에 비해 훨씬 균일합니다.                         |
|                         | `style="cheerful"`        | 더 높은 음과 에너지 넘치는 목소리로 긍정적이고 열정적인 톤을 표현합니다.                              |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                       |
|                         | `style="disgruntled"`     | 언짢고 불쾌한 톤을 표현합니다. 이 감정의 음성은 불쾌감과 무시를 나타냅니다.         |
|                         | `style="serious"`         | 엄격한 명령조 톤을 표현합니다. 딱딱한 억양으로 말하는 사람은 더 강경하고 경직된 것처럼 들립니다.  |
|                         | `style="depressed"`       | 낮은 음과 힘 없는 목소리로 우울하고 낙담한 톤을 표현합니다.    |
|                         | `style="gentle"`          | 낮은 음과 에너지 넘치는 목소리로 부드럽고 공손하고 상냥한 톤을 표현합니다.         |
| `zh-CN-XiaoruiNeural`   | `style="sad"`             | 높은 음, 낮은 강도, 낮은 에너지의 목소리로 슬픈 톤을 표현합니다. 이 감정을 표현하는 일반적인 방법은 말하는 중에 훌쩍이거나 우는 것입니다.         |
|                         | `style="angry"`           | 낮은 음, 높은 강도, 높은 에너지의 목소리로 화가 나고 짜증이 난 톤을 표현합니다. 말하는 사람이 화가 나고 기분이 상한 상태입니다.       |
|                         | `style="fearful"`         | 높은 음, 높은 에너지의 목소리로 빠르게 말하여 무섭고 초초한 톤을 표현합니다. 말하는 사람이 긴장하고 불안한 상태입니다.                       |
| `zh-CN-XiaoshuangNeural`   | `style="chat"` | 평범한 톤 및 낮은 톤을 나타냅니다. |

다음 표를 사용하여 지원되는 역할 및 해당 정의를 확인하세요.

|역할                     | Description                |
|-------------------------|----------------------------|
|`role="Girl"`            | 음성이 여자 아이를 모방합니다. |
|`role="Boy"`             | 음성이 남자 아이를 모방합니다. |
|`role="YoungAdultFemale"`| 음성이 젊은 여성을 모방합니다.|
|`role="YoungAdultMale"`  | 음성이 젊은 남성을 모방합니다.|
|`role="OlderAdultFemale"`| 음성이 중년 여성을 모방합니다.|
|`role="OlderAdultMale"`  | 음성이 중년 남성을 모방합니다.|
|`role="SeniorFemale"`    | 음성이 노년 여성을 모방합니다.|
|`role="SeniorMale"`      | 음성이 노년 남성을 모방합니다.|


**예제**

이 SSML 코드 조각은 `<mstts:express-as>` 요소를 사용하여 말하기 스타일을`cheerful`로 변경하는 방법을 보여줍니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

이 SSML 코드 조각은 `styledegree` 특성을 사용하여 XiaoxiaoNeural의 말하기 스타일 강도를 변경하는 방법을 보여줍니다.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

이 SSML 코드 조각은 `role` 특성을 사용하여 XiaomoNeural의 롤플레이를 변경하는 방법을 보여줍니다.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="adjust-speaking-languages"></a>말하기 언어 조정

인공신경망 음성에 대해 말하기 언어를 조정할 수 있습니다.
`<lang xml:lang>` 요소를 사용하여 한 가지 음성으로 다른 언어(예: 영어, 스페인어 및 중국어)를 유창하게 말할 수 있습니다. 이 요소는 Speech Service의 고유한 선택적 요소입니다. 이 요소가 없으면 음성이 주 언어를 사용합니다.
현재 말하기 언어 조정을 지원하는 인공신경망 음성은 `en-US-JennyMultilingualNeural`입니다. 위의 변경 사항은 문장 수준 및 단어 수준에서 적용됩니다. 언어가 지원되지 않는 경우 서비스는 오디오 스트림을 반환하지 않습니다.

> [!NOTE]
> 현재 `<lang xml:lang>` 요소는 `prosody` 및 `break` 요소와 호환되지 않으므로 이 요소의 피치, 윤곽선, 속도, 기간, 볼륨과 같은 일시 중지 및 프로소디를 조정할 수 없습니다.

**구문**

```xml
<lang xml:lang="string"></lang>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `lang` | 말하기 언어를 지정합니다. 현재 다른 언어로 말하는 것은 음성에 따라 다릅니다. | 인공신경망 음성의 말하기 언어를 조정하는 경우에 필요합니다. `lang xml:lang`을 사용하는 경우 로캘을 제공해야 합니다. |

다음 표를 사용하여 각 인공신경망 음성에서 지원할 말하기 언어를 결정하세요.

| 음성                            | 로캘 언어           | 설명                                                 |
|----------------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-JennyMultilingualNeural`  | `lang="en-US"`            | en-us 로캘 말하기(이 음성의 주 로캘) |
|                                  | `lang="en-CA"`            | en-CA 로캘 언어 말하기                                  |
|                                  | `lang="en-AU"`            | en-AU 로캘 언어 말하기                                  |
|                                  | `lang="en-GB"`            | en-GB 로캘 언어 말하기                                  |
|                                  | `lang="de-DE"`            | de-DE 로캘 언어 말하기                                  |
|                                  | `lang="fr-FR"`            | fr-FR 로캘 언어 말하기                                  |
|                                  | `lang="fr-CA"`            | fr-CA 로캘 언어 말하기                                  |
|                                  | `lang="es-ES"`            | es-ES 로캘 언어 말하기                                  |
|                                  | `lang="es-MX"`            | es-MX 로캘 언어 말하기                                  |
|                                  | `lang="zh-CN"`            | zh-CN 로캘 언어 말하기                                  |
|                                  | `lang="ko-KR"`            | ko-KR 로캘 언어 말하기                                  |
|                                  | `lang="ja-JP"`            | ja-JP 로캘 언어 말하기                                  |
|                                  | `lang="it-IT"`            | it-IT 로캘 언어 말하기                                  |
|                                  | `lang="pt-BR"`            | pt-BR 로캘 언어 말하기                                  |

**예제**

이 SSML 조각은 `<lang xml:lang>`을 사용하여 말하기 언어를 `en-US`, `es-MX` 및 `de-DE`로 변경하는 방법을 보여 줍니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JennyMultilingualNeural">
        I am looking forward to the exciting things.
        <lang xml:lang="es-MX">
            Estoy deseando que lleguen las cosas emocionantes.
        </lang>
        <lang xml:lang="de-DE">
            Ich freue mich auf die spannenden Dinge.
        </lang>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>break/pause 추가 또는 제거

`break` 요소를 사용하여 단어 사이에 일시 중지(또는 중단)를 삽입하거나, 텍스트 음성 변환 서비스가 자동으로 일시 중지를 추가하지 못하게 방지할 있습니다.

> [!NOTE]
> 단어 또는 구가 합성된 음성이 부자연스럽게 들리는 경우 이 요소를 사용하여 해당 단어 또는 구의 TTS(텍스트 음성 변환) 기본 동작을 재정의할 수 있습니다. `strength`를 `none`으로 설정하면 텍스트 음성 변환 서비스가 자동으로 삽입하는 운율적 중단을 방지할 수 있습니다.

**구문**

```xml
<break strength="string" />
<break time="string" />
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `strength` | 다음 값 중 하나를 사용하여 일시 중지의 상대 기간을 지정합니다.<ul><li>없음</li><li>x-weak</li><li>weak</li><li>medium(기본값)</li><li>강력</li><li>x-strong</li></ul> | Optional |
| `time` | 일시 중지의 절대 기간을 초 또는 밀리초 단위로 지정합니다. 이 값은 5000ms 미만으로 설정해야 합니다. 예를 들어 `2s` 및 `500ms`는 유효한 값입니다. | Optional |

| Strength                      | Description |
|-------------------------------|-------------|
| None 또는 제공된 값이 없는 경우 | 0ms        |
| x-weak                        | 250ms      |
| weak                          | 500ms      |
| 중간                        | 750ms      |
| 강력                        | 1000ms     |
| x-strong                      | 1250ms     |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>무언 추가

`mstts:silence` 요소를 사용하여 텍스트 앞이나 뒤 또는 인접한 2개 문장 사이에 일시 중지를 삽입할 수 있습니다.

> [!NOTE]
>`mstts:silence`와 `break`의 다른 점으로, `break`는 텍스트의 어느 위치에나 추가할 수 있지만 무언은 입력 텍스트의 시작이나 끝 또는 인접한 2개 문장의 경계에서만 작동합니다.


**구문**

```xml
<mstts:silence  type="string"  value="string"/>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `type` | 무언이 추가되는 위치를 지정합니다. <ul><li>`Leading` – 텍스트의 시작 부분 </li><li>`Tailing` – 텍스트 끝 </li><li>`Sentenceboundary` – 인접한 문장 사이 </li></ul> | 필수 |
| `Value` | 일시 중지의 절대 기간을 초 또는 밀리초 단위로 지정합니다. 이 값은 5000ms 미만으로 설정해야 합니다. 예를 들어 `2s` 및 `500ms`는 유효한 값입니다. | 필수 |

**예** 이 예제에서는 `mtts:silence`를 사용하여 두 문장 사이에 200밀리초의 무언을 추가합니다.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-ChristopherNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>단락 및 문장 지정

`p` 및 `s` 요소는 각각 단락과 문장을 나타내는 데 사용됩니다. 이러한 요소가 없으면 텍스트 음성 변환 서비스가 자동으로 SSML 문서의 구조를 결정합니다.

`p` 요소에는 텍스트와 `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as` 및 `s` 요소가 포함될 수 있습니다.

`s` 요소에는 텍스트와 `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, `sub` 요소가 포함될 수 있습니다.

**구문**

```xml
<p></p>
<s></s>
```

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>음소를 사용하여 발음 향상

`ph` 요소는 SSML 문서의 음소 발음에 사용됩니다. `ph` 요소는 텍스트만 포함할 수 있고 다른 요소는 포함할 수 없습니다. 항상 사람이 읽을 수 있는 음성을 대안으로 제공해야 합니다.

발음 기호는 음으로 구성되고, 음은 글자, 숫자, 문자 또는 이러한 것들의 조합으로 구성됩니다. 각 전화 음성의 고유한 소리를 설명합니다. 이것은 글자가 여러 발성을 나타낼 수 있는 라틴어 알파벳과 대조적입니다. 단어 "사탕" 및 "중단", "c" 문자의 다양 한 발음 또는 고려 문자 조합과 다른 발음 "th" 단어 "thing" 및 "는" 합니다.

> [!NOTE]
> 현재 et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural 및 mt-MT-GarceNeural의 5개 음성은 음소 태그를 지원하지 않습니다.

**구문**

```xml
<phoneme alphabet="string" ph="string"></phoneme>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `alphabet` | `ph` 특성의 문자열 발음을 합성할 때 사용되는 발음 기호를 지정합니다. 알파벳을 지정하는 문자열은 소문자로 지정해야 합니다. 다음은 지정할 수 있는 알파벳입니다.<ul><li>`ipa` &ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">국제 발음 기호</a></li><li>`sapi` &ndash; [Speech Service 발음 기호](speech-ssml-phonetic-sets.md)</li><li>`ups` &ndash;<a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank"> 범용 음성 세트</a></li></ul><br>알파벳은 요소의 `phoneme`에만 적용됩니다. | Optional |
| `ph` | `phoneme` 요소에 있는 단어의 발음을 지정하는 음을 포함하는 문자열입니다. 지정된 문자열에 인식할 수 없는 음이 들어 있는 경우 TTS(텍스트 음성 변환) 서비스는 전체 SSML 문서를 거부하고 문서에 지정된 음성 출력을 하나도 생성하지 않습니다. | 음소를 사용하는 경우 필수입니다. |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <phoneme alphabet="ipa" ph="təˈmeɪtoʊ"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>사용자 지정 어휘집을 사용하여 발음 개선

텍스트 음성 변환 서비스가 단어를 정확하게 발음하지 못하는 경우가 있습니다. 회사 이름, 의료 용어 또는 이모지를 예로 들 수 있습니다. 개발자는 `phoneme` 및 `sub` 태그를 사용하여 SSML에서 단일 엔터티를 읽는 방법을 정의할 수 있습니다. 그러나 여러 엔터티를 읽는 방법을 정의해야 하는 경우 `lexicon` 태그를 사용하여 사용자 지정 어휘집을 만들면 됩니다.

> [!NOTE]
> 현재 사용자 지정 어휘는 UTF-8 인코딩을 지원합니다.

> [!NOTE]
> 현재 et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural 및 mt-MT-GarceNeural의 5개 음성은 사용자 지정 어휘집을 지원하지 않습니다.


**구문**

```xml
<lexicon uri="string"/>
```

**특성**

| attribute | Description                               | 필수/선택 |
|-----------|-------------------------------------------|---------------------|
| `uri`     | 외부 PLS 문서의 주소입니다. | 필수 요소.           |

**사용 현황**

여러 엔터티를 읽는 방법을 정의하려면 .xml 또는 .pls 파일로 저장되는 사용자 지정 어휘집을 만들면 됩니다. 다음은 .xml 파일 샘플입니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
  <lexeme>
    <grapheme>😀</grapheme>
    <alias>test emoji</alias>
  </lexeme>
</lexicon>
```

`lexicon` 요소에는 하나 이상의 `lexeme` 요소가 포함됩니다. 각 `lexeme` 요소에는 하나 이상의 `grapheme` 요소와 하나 이상의 `grapheme`, `alias` 및 `phoneme` 요소가 포함됩니다. `grapheme` 요소는 <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">표기법</a>을 설명하는 텍스트를 포함합니다. `alias` 요소는 머리글자어 또는 축약된 용어의 발음을 나타내는 데 사용됩니다. `phoneme` 요소는 `lexeme`를 어떻게 발음하는지 설명하는 텍스트를 제공합니다. `alias` 및 `phoneme` 요소가 `grapheme`에 제공되는 경우 `alias`의 우선 순위가 더 높습니다.

> [!IMPORTANT]
> `lexeme`요소는 사용자 지정 어휘에서 대/소문자 구분입니다. 예를 들어 'Hello'에 대한 음소만 제공하는 경우 `lexeme` `lexeme` 'hello'에서는 작동하지 않습니다.

어휘집에는 필요한 `xml:lang` 특성이 포함되어 어떤 로캘이 적용되어야 하는지 나타냅니다. 하나의 사용자 지정 어휘집은 설계상 하나의 로캘로 제한되므로 다른 로캘에 적용하면 작동하지 않습니다.

사용자 지정 어휘집을 사용하여 구의 발음을 직접 설정할 수 없다는 점에 유의해야 합니다. 머리글자어 또는 축약된 용어의 발음을 설정해야 하는 경우 `alias`를 제공한 다음, `phoneme`을 해당 `alias`와 연결합니다. 예를 들면 다음과 같습니다.

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

> [!NOTE]
> 음절 경계는 국제 음성 알파벳에서 '.'입니다.

머리글자어 축약된 용어의 예상 `alias`를 직접 제공할 수도 있습니다. 예를 들면 다음과 같습니다.
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> IPA를 사용하는 경우 `phoneme` 요소는 공백을 포함할 수 없습니다.

사용자 지정 어휘집 파일에 대한 자세한 내용은 [PLS(발음 어휘집 사양) 버전 1.0](https://www.w3.org/TR/pronunciation-lexicon/)을 참조하세요.

다음으로, 사용자 지정 어휘집 파일을 게시합니다. 이 파일을 저장할 수 있는 위치에 대한 제한은 없지만 [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md)를 사용하는 것이 좋습니다.

사용자 지정 어휘집을 게시한 후 SSML에서 참조할 수 있습니다.

> [!NOTE]
> `lexicon` 요소는 `voice` 요소 내에 있어야 합니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

이 사용자 지정 어휘집을 사용하는 경우 "BTW"를 "By the way"로 읽습니다. "Benigni"는 제공된 IPA "bɛˈniːnji"로 읽습니다.

사용자 지정 어휘에서 실수를 쉽게 할 수 있기 때문에 Microsoft는 [사용자 지정 어휘에 대 한 유효성 검사 도구](https://github.com/jiajzhan/Custom-Lexicon-Validation)를 제공 했습니다. 오류를 찾는 데 도움이 되는 자세한 오류 메시지를 제공 합니다. 사용자 지정 어휘를 사용 하 여 사용자 지정 어휘를 음성 서비스에 보내기 전에이 도구를 사용 하 여 사용자 지정 어휘를 확인 해야 합니다. 

**제한 사항**
- 파일 크기: 사용자 지정 어휘집 파일 크기 최대 제한은 100KB이며, 이 크기를 초과하면 합성 요청이 실패합니다.
- 어휘집 캐시 새로 고침: 사용자 지정 어휘집은 처음 로드될 때 URI를 사용하여 TTS 서비스에서 키로 캐시됩니다. 동일한 URI를 사용하는 어휘집은 15분 이내에 다시 로드되지 않으므로 사용자 지정 어휘집의 변경 내용은 최대 15분이 지나야 적용됩니다.

**Speech Service 음성 세트**

위의 샘플에서는 IPA 음성 세트라고도 하는 국제 발음 기호를 사용합니다. 개발자는 국제 표준인 IPA를 사용하는 것이 좋습니다. 일부 IPA 문자의 경우 유니코드로 표시될 때 '미리 구성된' 버전과 '분해된' 버전이 있습니다. 사용자 지정 어휘집은 분해된 유니코드만 지원합니다.

IPA를 기억하는 것이 쉽지 않다는 점을 고려하여 Speech Service는 7개 언어(`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN` 및 `zh-TW`)의 음성 세트를 정의합니다.

아래와 같이 사용자 지정 어휘집을 통해 `x-microsoft-sapi`를 `alphabet` 특성의 값으로 사용할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="x-microsoft-sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Speech Service 발음 기호에 대한 자세한 내용은 [Speech Service 음성 세트](speech-ssml-phonetic-sets.md)를 참조하세요.

## <a name="adjust-prosody"></a>운율 조정

`prosody` 요소는 텍스트 음성 변환 출력의 피치, 피치 곡선, 범위, 속도, 기간 및 볼륨에 대한 변경 내용을 지정하는 데 사용됩니다. `prosody` 요소에는 텍스트와 `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub` 및 `s` 요소가 포함될 수 있습니다.

운율 특성 값이 광범위하게 달라질 수 있으므로 음성 인식기는 할당된 값을 선택한 음성에 대해 권장하는 실제 운율 값으로 해석합니다. 텍스트 음성 변환 서비스는 지원되지 않는 값을 제한하거나 대체합니다. 지원되지 않는 값의 예로 피치 1MHz 또는 볼륨 120을 들 수 있습니다.

**구문**

```xml
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `pitch` | 텍스트의 기준 피치를 나타냅니다. 다음과 같이 피치를 표현할 수 있습니다.<ul><li>숫자에 "Hz"(헤르츠)를 붙여서 표현하는 절대값. 예들 들어 `<prosody pitch="600Hz">some text</prosody>`입니다.</li><li>"+" 또는 "-" 기호 뒤에 숫자를 붙이고 그 뒤에 "Hz" 또는 "st"를 붙여서 피치를 변경하는 양을 지정하는 상대값. 예를 들어 `<prosody pitch="+80Hz">some text</prosody>` 또는 `<prosody pitch="-2st">some text</prosody>`입니다. "st"는 변경 단위가 반음, 즉, 표준 온음계 톤의 절반(반음)임을 나타냅니다.</li><li>상수 값:<ul><li>x-low</li><li>low</li><li>중간</li><li>high</li><li>x-high</li><li>default</li></ul></li></ul> | Optional |
| `contour` |이제 피치 곡선은 인공신경망 음성과 표준 음성을 모두 지원합니다. 피치 곡선은 피치의 변화를 나타냅니다. 이러한 변화는 음성 출력에서 특정 시간 위치에 있는 대상의 배열로 정의됩니다. 각 대상은 매개 변수 쌍 세트로 정의됩니다. 예를 들면 다음과 같습니다. <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>각 매개 변수 세트의 첫 번째 값은 피치 변경의 위치를 텍스트 기간의 백분율로 지정합니다. 두 번째 값은 피치에 대한 상대값 또는 열거형 값을 사용하여 피치를 높이거나 낮출 크기를 지정합니다(`pitch` 참조). | Optional |
| `range` | 텍스트의 피치 범위를 나타내는 값입니다. `range`는 `pitch`를 설명하는 데 사용한 것과 동일한 절대값, 상대값 또는 열거형 값을 사용하여 나타낼 수 있습니다. | Optional |
| `rate` | 텍스트를 읽는 속도를 나타냅니다. `rate`를 다음과 같이 나타낼 수 있습니다.<ul><li>기본값의 승수 역할을 하는 숫자로 표시되는 상대값. 예를 들어 값이 *1* 이면 속도가 변경되지 않습니다. 값이 *0.5* 이면 속도는 절반이 됩니다. 값이 *3* 이면 속도는 3배가 됩니다.</li><li>상수 값:<ul><li>x-slow</li><li>slow</li><li>중간</li><li>fast</li><li>x-fast</li><li>default</li></ul></li></ul> | Optional |
| `duration` | 음성 합성(TTS) 서비스가 텍스트를 읽는 동안 경과해야 하는 기간(초 또는 밀리초)입니다. 예를 들어 *2s* 또는 *1800ms* 입니다. 기간은 표준 음성만 지원합니다.| Optional |
| `volume` | 말하기 음성의 볼륨 수준을 나타냅니다. 볼륨을 다음과 같이 나타낼 수 있습니다.<ul><li>*가장 작은* 0.0부터 *가장 큰* 100.0 사이의 숫자로 표현하는 절대값. 예: 75. 기본값은 100.0입니다.</li><li>"+" 또는 "-" 기호 뒤에 숫자를 붙여서 볼륨을 변경하는 양을 지정하는 상대값. 예: +10 또는 -5.5.</li><li>상수 값:<ul><li>무음</li><li>x-soft</li><li>soft</li><li>중간</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>말하기 속도 변경

말하기 속도는 단어 또는 문장 수준에서 인공신경망 및 표준 음성에 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>볼륨 변경

단어 또는 문장 수준에서는 볼륨 변경을 표준 음성에 적용할 수 있습니다. 반면 문장 수준에서는 볼륨 변경을 인공신경망 음성에만 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>피치 변경

단어 또는 문장 수준에서는 피치 변경을 표준 음성에 적용할 수 있습니다. 반면 문장 수준에서는 피치 변경을 인공신경망 음성에만 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>피치 곡선 변경

> [!IMPORTANT]
> 이제 인공신경망 음성에서 피치 곡선 변경이 지원됩니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as 요소

`say-as`는 요소 텍스트의 콘텐츠 형식(예: 숫자 또는 날짜)을 나타내는 선택적 요소입니다. 이 요소는 텍스트를 발음하는 방법에 대한 지침을 음성 합성 엔진에 제공합니다.

**구문**

```xml
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `interpret-as` | 요소 텍스트의 콘텐츠 형식을 나타냅니다. 형식 목록은 아래 표를 참조하세요. | 필수 |
| `format` | 형식이 모호할 수 있는 콘텐츠 형식에 대한 요소 텍스트의 정확한 서식 지정 정보를 제공합니다. SSML은 이러한 형식을 사용하는 콘텐츠 형식의 형식을 정의합니다(아래 표 참조). | Optional |
| `detail` | 읽어야 하는 세부 수준을 나타냅니다. 예를 들어 이 특성은 음성 합성 엔진이 문장 부호를 발음하도록 요청할 수 있습니다. `detail`에 대해 정의된 표준 값은 없습니다. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

다음은 `interpret-as` 및 `format` 특성에 대해 지원되는 콘텐츠 형식입니다. `interpret-as`가 날짜 및 시간으로 설정된 경우에만 `format` 특성을 포함해야 합니다.

| interpret-as | format | 해석 |
|--------------|--------|----------------|
| `address` | | 텍스트를 주소로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"I'm at 150th court north east redmond washington"으로 읽습니다. |
| `cardinal`, `number` | | 텍스트를 기수로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"There are three alternatives"로 읽습니다. |
| `characters`, `spell-out` | | 텍스트를 개별 문자(철자)로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"로 읽습니다. |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | 텍스트를 날짜로 읽습니다. `format` 특성은 날짜의 형식(*d=일, m=월, y=연*)을 지정합니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Today is October nineteenth two thousand sixteen"으로 읽습니다. |
| `digits`, `number_digit` | | 텍스트를 개별 숫자 순서대로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9"로 읽습니다. |
| `fraction` | | 텍스트를 분수로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"three eighths of an inch"로 읽습니다. |
| `ordinal` | | 텍스트를 서수로 읽습니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"Select the third option"으로 읽습니다. |
| `telephone` | | 텍스트를 전화 번호로 읽습니다. `format` 특성에 국가 번호를 나타내는 숫자가 포함될 수 있습니다. 예를 들어 미국을 나타내는 "1" 또는 이탈리아를 나타내는 "39"가 포함될 수 있습니다. 음성 합성 엔진은 이 정보를 사용하여 전화 번호 발음을 안내할 수 있습니다. 전화 번호에 국가 번호도 포함될 수 있으며, 이 경우 `format`에서 국가 코드보다 우선적으로 적용됩니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"My number is area code eight eight eight five five five one two one two"로 읽습니다. |
| `time` | hms12, hms24 | 텍스트를 시간으로 읽습니다. `format` 특성은 시간을 지정할 때 12시간제(hms12)를 사용할 것인지 아니면 24시간제(hms24)를 사용할 것인지 지정합니다. 콜론을 사용하여 시간, 분, 초를 나타내는 숫자를 구분합니다. 예를 들어 12:35, 1:14:32, 08:15 및 02:50:45는 유효한 시간입니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"The train departs at four A M"으로 읽습니다. |
| `name` | | 텍스트는 사람 이름으로 읽힙니다. 음성 합성 엔진이 다음과 같이 읽습니다.<br /><br />`<say-as interpret-as="name">ED</say-as>`<br /><br />[æd]와 같이. <br />중국어 이름에서는 일부 문자가 성에 나타날 때 다르게 발음됩니다. 예를 들어 음성 합성 엔진은 仇를 다음과 같이 읽습니다. <br /><br />`<say-as interpret-as="address">仇先生</say-as>`<br /><br /> [chóu] 대신 [qiú]와 같이. |

**사용 현황**

`say-as` 요소에는 텍스트만 포함될 수 있습니다.

**예제**

음성 합성 엔진은 다음 예제를 "Your first request was for one room on October nineteenth twenty ten with early arrival at twelve thirty five PM"으로 읽습니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>기록된 오디오 추가

`audio`는 SSML 문서에 MP3 오디오를 삽입할 수 있는 선택적 요소입니다. audio 요소의 본문에는 오디오 파일을 사용할 수 없거나 재생할 수 없을 때 읽을 일반 텍스트 또는 SSML 태그를 포함할 수 있습니다. 또한 `audio` 요소에는 텍스트와 `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as` 및 `sub` 요소가 포함될 수 있습니다.

SSML 문서에 포함된 오디오는 다음 요구 사항을 충족해야 합니다.

* MP3는 인터넷에 액세스할 수 있는 HTTPS 엔드포인트에 호스트해야 합니다. HTTPS가 필요하며, MP3 파일을 호스트하는 도메인은 신뢰할 수 있는 유효한 TLS/SSL 인증서를 제공해야 합니다.
* MP3는 유효한 MP3 파일(MPEG v2)이어야 합니다.
* 비트 전송률은 48kbps여야 합니다.
* 샘플 속도는 16,000Hz여야 합니다.
* 단일 응답의 모든 텍스트와 오디오 파일을 더한 총 시간이 90초를 초과하면 안 됩니다.
* MP3에 고객 관련 정보나 기타 중요한 정보가 포함되면 안 됩니다.

**구문**

```xml
<audio src="string"/></audio>
```

**특성**

| attribute | Description                                   | 필수/선택                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | 오디오 파일의 위치/URL을 지정합니다. | SSML 문서에서 audio 요소를 사용하는 경우 필수입니다. |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>백그라운드 오디오 추가

`mstts:backgroundaudio` 요소를 사용하여 SSML 문서에 백그라운드 오디오를 추가하거나 오디오 파일을 텍스트 음성 변환과 혼합할 수 있습니다. `mstts:backgroundaudio`를 사용하면 백그라운드에서 오디오 파일을 반복하고, 텍스트 음성 변환을 시작할 때 페이드 인하고, 텍스트 음성 변환이 끝날 때 페이드 아웃할 수 있습니다.

제공된 백그라운드 오디오가 텍스트 음성 변환 또는 페이드 아웃보다 짧으면 백그라운드 오디오가 반복됩니다. 텍스트 음성 변환보다 길면 페이드 아웃이 완료될 때 중지됩니다.

백그라운드 오디오 파일은 SSML 문서당 하나만 허용됩니다. 하지만 `audio` 요소 내에 `voice` 태그를 배치하여 SSML 문서에 오디오를 더 추가할 수 있습니다.

**구문**

```xml
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `src` | 백그라운드 오디오 파일의 위치/URL을 지정합니다. | SSML 문서에서 백그라운드 오디오를 사용하는 경우 필수입니다. |
| `volume` | 백그라운드 오디오 파일의 볼륨을 지정합니다. **허용되는 값** 은 `0`~`100`(포함)입니다. 기본값은 `1`입니다. | Optional |
| `fadein` | 백그라운드 오디오 "페이드 인" 기간을 밀리초 단위로 지정합니다. 기본값은 페이드 인이 없는 `0`입니다. **허용되는 값** 은 `0`~`10000`(포함)입니다.  | Optional |
| `fadeout` | 백그라운드 오디오 페이드 아웃 기간을 밀리초 단위로 지정합니다. 기본값은 페이드 아웃이 없는 `0`입니다. **허용되는 값** 은 `0`~`10000`(포함)입니다.  | Optional |

**예제**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>bookmark 요소

bookmark 요소를 사용하면 SSML로 사용자 지정 표식을 삽입하여 오디오 스트림에 있는 각 표식의 오프셋을 얻을 수 있습니다.
bookmark 요소는 읽지 않습니다.
bookmark 요소는 텍스트 또는 태그 시퀀스의 특정 위치를 참조하는 데 사용할 수 있습니다.

> [!NOTE]
> 현재 `bookmark` 요소는 `en-US-AriaNeural` 음성에서만 작동합니다.

**구문**

```xml
<bookmark mark="string"/>
```

**특성**

| attribute | Description                                   | 필수/선택                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | `bookmark` 요소의 참조 텍스트를 지정합니다. | 필수 요소. |

**예제**

예를 들어 다음과 같이 꽃을 의미하는 각 단어의 시간 오프셋을 알아야 하는 경우가 있습니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Speech SDK를 사용하여 책갈피 가져오기

Speech SDK에서 `BookmarkReached` 이벤트를 구독하여 책갈피 오프셋을 가져올 수 있습니다.

> [!NOTE]
> `BookmarkReached` 이벤트는 Speech SDK 1.16.0 버전부터 사용할 수 있습니다.

`BookmarkReached` 이벤트는 출력 오디오 데이터를 사용할 수 있게 될 때 발생하며, 출력 디바이스로 재생하는 것보다 빠릅니다.

* `AudioOffset`은 합성의 시작과 bookmark 요소 사이에 출력 오디오의 경과 시간을 보고합니다. 이 값은 100HNS(나노초 단위)를 사용하며 10,000HNS는 1밀리초입니다.
* `Text`는 bookmark 요소의 참조 텍스트이며, `mark` 특성에서 설정하는 문자열입니다.

# <a name="c"></a>[C#](#tab/csharp)

자세한 내용은 <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>를 참조하세요.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

위의 SSML 예제에서는 `BookmarkReached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

자세한 내용은 <a href="/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>를 참조하세요.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

위의 SSML 예제에서는 `BookmarkReached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 <a href="/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>를 참조하세요.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

위의 SSML 예제에서는 `BookmarkReached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>를 참조하세요.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

위의 SSML 예제에서는 `bookmark_reached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

자세한 내용은 <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached`</a>를 참조하세요.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

위의 SSML 예제에서는 `bookmarkReached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

자세한 내용은 <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>를 참조하세요.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

위의 SSML 예제에서는 `BookmarkReached` 이벤트가 두 번 트리거되고 콘솔 출력은 다음과 같습니다.
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

자세한 내용은 <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
