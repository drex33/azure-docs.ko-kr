---
title: '컨테이너: Translator 번역 메서드'
titleSuffix: Azure Cognitive Services
description: 텍스트를 번역하기 위해 Azure Cognitive Services Translator의 컨테이너 Translate 메서드의 매개 변수, 헤더 및 본문 메시지를 파악합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: b2c13a98c6b689594628a258faf18f8f9c0d8c69
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539559"
---
# <a name="container-translator-translate-method-preview"></a>컨테이너: Translator 번역 메서드(미리 보기)

텍스트를 번역합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
http://localhost:{port}/translate?api-version=3.0
```

예: http://<span></span>localhost:5000/translate?api-version=3.0

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

### <a name="required-parameters"></a>필수 매개 변수

| 쿼리 매개 변수 | Description |
| --- | --- |
| api-version | _필수 매개 변수_ 입니다.  <br>클라이언트에서 요청한 API 버전입니다. 값은 `3.0`이어야 합니다. |
| 원본 | _필수 매개 변수_ 입니다.  <br>입력 텍스트의 언어를 지정합니다. `translation` 범위를 통해 [지원되는 언어](../reference/v3-0-languages.md)를 조회하여 번역할 수 있는 원본 언어를 찾습니다.|
| 을  | _필수 매개 변수_ 입니다.  <br>출력 텍스트의 언어를 지정합니다. 대상 언어는 `translation` 범위에 포함된 [지원되는 언어](../reference/v3-0-languages.md) 중 하나여야 합니다. 예를 들어, 독일어로 번역하려면 `to=de`를 사용합니다.  <br>쿼리 문자열의 매개 변수를 반복하여 동시에 여러 언어로 번역할 수도 있습니다. 예를 들어, 독일어 및 이탈리아어로 번역하려면 `to=de&to=it`를 사용합니다. |

### <a name="optional-parameters"></a>선택적 매개 변수

| 쿼리 매개 변수 | Description |
| --- | --- |
| textType | _선택적 매개 변수_ 입니다.  <br>번역되는 텍스트가 일반 텍스트인지 또는 HTML 인지를 정의합니다. 모든 HTML은 올바른 형식이 완전한 요소여야 합니다. 가능한 값은 `plain`(기본값) 또는 `html`입니다. |
| includeSentenceLength | _선택적 매개 변수_ 입니다.  <br>입력 텍스트 및 번역된 텍스트에 대한 문장 경계를 포함할지 여부를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. |

요청 헤더에는 다음이 포함됩니다.

| headers | Description |
| --- | --- |
| 인증 헤더 | _필수 요청 헤더_  <br>[인증에 사용할 수 있는 옵션](../reference/v3-0-reference.md#authentication)을 참조하세요. |
| 콘텐츠 형식 | _필수 요청 헤더_  <br>페이로드의 콘텐츠 형식을 지정합니다.  <br>허용되는 값은 `application/json; charset=UTF-8`입니다. |
| Content-Length | _필수 요청 헤더_  <br>요청 본문의 길이입니다. |
| X-ClientTraceId | _선택 사항입니다_.  <br>요청을 고유하게 식별하는 클라이언트 생성 ID입니다. `ClientTraceId`라는 쿼리 매개 변수를 사용하는 쿼리 문자열에서 추적 ID를 포함하는 경우 이 헤더를 생략할 수 있습니다. |

## <a name="request-body"></a>요청 본문

요청 본문은 JSON 배열입니다. 각 배열 요소는 번역할 문자열을 나타내는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

다음과 같은 제한 사항이 적용됩니다.

* 배열에는 최대 100개 요소가 있을 수 있습니다.
* 요청에 포함된 전체 텍스트는 공백을 포함하여 10,000자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

* `translations`: 번역 결과의 배열입니다. 배열 크기는 `to` 쿼리 매개 변수를 통해 지정된 대상 언어의 수와 일치합니다. 배열의 각 요소에는 다음이 포함됩니다.

* `to`: 대상 언어의 언어 코드를 나타내는 문자열입니다.

* `text`: 번역된 텍스트를 제공하는 문자열입니다.

* `sentLen`: 입력 및 출력 텍스트의 문장 경계를 반환하는 개체입니다.

* `srcSentLen`: 입력 텍스트의 문장 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

* `transSentLen`: 번역된 텍스트의 문장 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

    문장 경계는 요청 매개 변수 `includeSentenceLength`가 `true`일 때만 포함됩니다.

  * `sourceText`: 이름이 `text`인 단일 문자열 속성을 갖는 개체로, 소스 언어의 기본 스크립트로 입력 텍스트를 제공합니다. `sourceText` 속성은 입력이 언어에 대한 일반적이지 않은 스크립트로 표현될 때만 제공됩니다. 예를 들어, 입력이 라틴 스크립트로 작성된 아랍어인 경우 `sourceText.text`는 아랍 스크립트로 변환된 동일한 아랍어 텍스트가 됩니다.

JSON 응답 예제는 [예제](#examples) 섹션에 제공됩니다.

## <a name="response-headers"></a>응답 헤더

| headers | Description |
| --- | --- |
| X-RequestId | 요청을 식별하기 위해 서비스에서 생성한 값입니다. 문제 해결을 위해 사용됩니다. |
| X-MT-System | 번역에 대해 요청된 각 'to' 언어에 대해 번역에 사용된 시스템 형식을 지정합니다. 값은 쉼표로 구분된 문자열 목록입니다. 각 문자열은 다음 형식을 나타냅니다.  </br></br>&FilledVerySmallSquare; Custom - 요청에는 사용자 지정 시스템이 포함되고, 번역 중에 하나 이상의 사용자 지정 시스템이 사용되었습니다.</br>&FilledVerySmallSquare; Team - 다른 모든 요청 |

## <a name="response-status-codes"></a>응답 상태 코드

오류가 발생하는 경우 요청은 JSON 오류 응답도 반환합니다. 오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 [v3 Translator 참조 페이지](../reference/v3-0-reference.md#errors)에서 확인할 수 있습니다.

## <a name="examples"></a>예

### <a name="translate-a-single-input"></a>단일 입력 번역

이 예제에서는 단일 문장을 영어에서 중국어 간체로 번역하는 방법을 보여 줍니다.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 배열에는 입력의 단일 텍스트 번역을 제공하는 하나의 요소가 포함됩니다.

### <a name="translate-multiple-pieces-of-text"></a>여러 텍스트 번역

한 번에 여러 문자열을 번역하려면 요청 본문에서 문자열 배열을 지정하기만 하면 됩니다.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

응답에는 모든 텍스트 조각의 번역이 요청과 동일한 순서로 포함됩니다.
응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>여러 언어로 번역

이 예제에서는 하나의 요청에서 동일한 입력을 여러 다른 언어로 번역하는 방법을 보여 줍니다.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>태그가 있는 콘텐츠 번역 및 번역 내용 결정

HTML 페이지의 콘텐츠나 XML 문서의 콘텐츠처럼 태그를 포함하는 콘텐츠를 번역하는 것은 일반적입니다. 태그가 있는 콘텐츠를 번역할 때는 쿼리 매개 변수 `textType=html`을 포함합니다. 또한 경우에 따라 번역에서 특정 콘텐츠를 제외하는 것이 유용합니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예제에서 첫 번째 `div` 요소 내에 포함된 콘텐츠는 번역되지 않지만 두 번째 `div` 요소에 있는 콘텐츠는 번역됩니다.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

샘플 요청은 다음과 같습니다.

```curl
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

응답은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>동적 사전을 사용하여 번역

단어나 구에 적용할 번역을 이미 알고 있는 경우 요청 내에 태그로 제공할 수 있습니다. 동적 사전은 개인 이름 및 제품 이름과 같은 고유 명사에만 안전합니다.

제공할 태그는 다음 구문을 사용합니다.

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

예를 들어, 영어 문장 "The word wordomatic is a dictionary entry"를 고려해보세요. 단어 _wordomatic_ 을 번역에 유지하려면 다음 요청을 보냅니다.

```
curl -X POST "http://localhost:{port}/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

결과는 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

이 기능은 `textType=text` 또는 `textType=html`을 사용할 때와 같은 결과를 가져옵니다. 이 기능은 자주 사용하지 않는 것이 좋습니다. 번역을 사용자 지정하는 보다 적절하고 훨씬 더 나은 방법은 Custom Translator를 사용하는 것입니다. Custom Translator는 컨텍스트 및 통계적 확률을 최대한 활용합니다. 컨텍스트에 따라 사용자 작업 또는 구를 보여 주는 학습 데이터를 보유하고 있거나 이러한 데이터를 만들 수 있는 경제적 여유가 있으면 훨씬 더 나은 결과를 얻을 수 있습니다. [Custom Translator에 대해 자세히 알아보세요](../customization.md).

## <a name="request-limits"></a>요청 제한

각 번역 요청은 번역하는 모든 대상 언어 전체에서 10,000자로 제한됩니다. 예를 들어 3,000자를 3개 언어로 번역하는 번역 요청을 보내면 요청 크기가 3,000x3 = 9,000자가 되어 요청 제한을 충족합니다. 요청 수가 아닌 문자당 요금이 청구됩니다. 짧은 요청을 보내는 것이 좋습니다.

다음 표에는 Translator **번역** 작업에 대한 배열 요소 및 문자 제한이 나열되어 있습니다.

| 작업(Operation) | 배열 요소의 최대 크기 | 배열 요소 최대 개수 | 최대 요청 크기(문자) |
|:----|:----|:----|:----|
| translate | 10000 | 100 | 10000 |