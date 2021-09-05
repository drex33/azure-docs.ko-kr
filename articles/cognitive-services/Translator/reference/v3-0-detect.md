---
title: Translator Detect 메서드
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Detect 메서드를 사용하여 텍스트 조각의 언어를 식별합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 36691229ebe59a6b3b5dd59776b6af41e326a3c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529343"
---
# <a name="translator-30-detect"></a>Translator 3.0: Detect

텍스트의 언어를 식별합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

| 쿼리 매개 변수 | Description |
| --- | --- |
| api-version | *필수 매개 변수* 입니다.<br/>클라이언트에서 요청한 API 버전입니다. 값은 `3.0`이어야 합니다. |

요청 헤더에는 다음이 포함됩니다.

| headers | Description |
| --- | --- |
| 인증 헤더 | <em>필수 요청 헤더</em><br/>[인증에 사용할 수 있는 옵션](./v3-0-reference.md#authentication)</a>을 참조하세요. |
| 콘텐츠 형식 | *필수 요청 헤더*<br/>페이로드의 콘텐츠 형식을 지정합니다. 가능한 값은 `application/json`입니다. |
| Content-Length | *필수 요청 헤더*<br/>요청 본문의 길이입니다. |
| X-ClientTraceId | *선택 사항입니다*.<br/>요청을 고유하게 식별하는 클라이언트 생성 ID입니다. `ClientTraceId`라는 쿼리 매개 변수를 사용하는 쿼리 문자열에서 추적 ID를 포함하는 경우 이 헤더를 생략할 수 있습니다. |

## <a name="request-body"></a>요청 본문

요청 본문은 JSON 배열입니다. 각 배열 요소는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다. 언어 검색은 `Text` 속성 값에 적용됩니다. 언어 자동 검색은 입력 텍스트가 길수록 더 효과적입니다. 샘플 응답 본문은 다음과 같습니다.

```json
[
    { "Text": "Ich würde wirklich gerne Ihr Auto ein paar Mal um den Block fahren." }
]
```

다음과 같은 제한 사항이 적용됩니다.

* 배열에는 최대 100개 요소가 있을 수 있습니다.
* 요청에 포함된 전체 텍스트는 공백을 포함하여 50,000자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `language`: 검색된 언어의 코드입니다.

  * `score`: 결과의 신뢰도를 나타내는 부동 값입니다. 점수는 0~1 사이이고 낮은 점수는 낮은 신뢰도를 나타냅니다.

  * `isTranslationSupported`: 검색된 언어가 텍스트 번역에 지원되는 언어 중 하나인 경우 true인 부울 값입니다.

  * `isTransliterationSupported`: 검색된 언어가 음역에 지원되는 언어 중 하나인 경우 true인 부울 값입니다.
  
  * `alternatives`: 가능한 다른 언어의 배열입니다. 배열의 각 요소는 위에 나열된 동일한 속성의 다른 개체입니다. `language`, `score`, `isTranslationSupported` 및 `isTransliterationSupported`

예제 JSON 응답은 다음과 같습니다.

```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

]
```

## <a name="response-headers"></a>응답 헤더

| headers | Description |
| --- | --- |
| X-RequestId | 요청을 식별하기 위해 서비스에서 생성한 값입니다. 문제 해결을 위해 사용됩니다. |

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다. 

| 상태 코드 | 설명 |
| --- | --- |
| 200 | 성공. |
| 400 | 쿼리 매개 변수 중 하나가 없거나 잘못되었습니다. 다시 시도하기 전에 요청 매개 변수를 수정합니다. |
| 401 | 요청을 인증할 수 없습니다. 지정된 자격 증명이 있고 유효한지 확인합니다. |
| 403 | 요청에 권한이 없습니다. 세부 정보 오류 메시지를 확인합니다. 이것은 흔히 평가판 구독으로 제공된 사용 가능한 모든 번역이 모두 사용되었음을 나타냅니다. |
| 429 | 클라이언트가 요청 한도를 초과했기 때문에 서버가 요청을 거부했습니다. |
| 500 | 예기치 않은 오류가 발생했습니다. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 `X-RequestId`의 요청 식별자 및 요청 헤더 `X-ClientTraceId`의 클라이언트 식별자를 사용해서 보고합니다. |
| 503 | 서버를 일시적으로 사용할 수 없습니다. 요청을 다시 시도하십시오. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 `X-RequestId`의 요청 식별자 및 요청 헤더 `X-ClientTraceId`의 클라이언트 식별자를 사용해서 보고합니다. |

오류가 발생하는 경우 요청은 JSON 오류 응답도 반환합니다. 오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 [v3 Translator 참조 페이지](./v3-0-reference.md#errors)에서 확인할 수 있습니다. 

## <a name="examples"></a>예

다음 예제에서는 텍스트 번역에 지원되는 언어를 검색하는 방법을 보여줍니다.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```
