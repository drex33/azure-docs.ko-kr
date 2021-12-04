---
title: 언어 감지를 수행하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 언어 감지를 사용하여 작성된 텍스트의 언어를 검색하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 12/03/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 88c6f88fa614fb92d98e49d4b34f8c011b63b134
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133541568"
---
# <a name="how-to-use-language-detection"></a>언어 감지를 사용하는 방법

언어 감지 기능은 텍스트를 평가하고 문서가 작성된 언어를 나타내는 언어 식별자를 반환할 수 있습니다.

언어 감지는 언어를 알 수 없는 임의의 텍스트를 수집하는 콘텐츠 저장소에 유용합니다. 이 분석의 결과를 구문 분석하여 입력 문서에서 사용된 언어를 판별할 수 있습니다. 또한 응답에서는 모델의 신뢰도를 반영하는 0과 1사이의 점수를 반환합니다.

언어 감지 기능은 다양한 언어, 변형, 방언 및 일부 지역 또는 문화권 언어를 감지할 수 있습니다.

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 문서](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예제 요청을 만들 수도 있습니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-language-detection-model"></a>언어 감지 모델 지정

기본적으로 언어 감지는 텍스트에서 사용 가능한 최신 AI 모델을 사용합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정하는 모델은 언어 감지 작업을 수행하는 데 사용됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
|  `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |

### <a name="input-languages"></a>입력 언어

평가할 문서를 제출할 때 언어 감지는 텍스트가 [지원되는 언어](../language-support.md)로 작성되었는지 여부를 확인하려고 합니다.  

사용 빈도가 낮은 언어로 표현된 콘텐츠가 있는 경우, 언어 감지 기능을 사용하여 코드가 반환되는지 확인할 수 있습니다. 감지할 수 없는 언어에 대한 응답은 `unknown`입니다.

## <a name="submitting-data"></a>데이터 제출

> [!TIP]
> 언어 감지에 [Docker 컨테이너](use-containers.md)를 사용할 수 있으므로 온-프레미스에서 API를 사용할 수 있습니다.

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래 데이터 제한을 참조하세요.

언어 감지 기능을 동기적으로 사용하는 것은 상태 비저장입니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

[!INCLUDE [asynchronous-result-availability](../../includes/async-result-availability.md)]


## <a name="getting-language-detection-results"></a>언어 감지 결과 가져오기

언어 감지에서 결과를 가져올 때 애플리케이션에 결과를 스트리밍하거나 로컬 시스템의 파일에 출력을 저장할 수 있습니다.

언어 감지는 제출하는 각 문서에 대해 [ISO 639-1](https://www.iso.org/standard/22109.html) 이름, 사람이 읽을 수 있는 이름 및 신뢰 점수와 함께 반환합니다. 긍정 점수 1은 분석의 가능한 가장 높은 신뢰도 수준을 나타냅니다.

### <a name="ambiguous-content"></a>모호한 콘텐츠

일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. `countryHint` 매개 변수를 사용하여 [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) 국가/지역 코드를 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 국가 힌트로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정할 수 있습니다.

예를 들어 "Impossible"은 영어와 프랑스어 모두에 공통적이며, 제한된 컨텍스트로 제공되는 경우 응답은 "US" 국가/지역 힌트를 기반으로 합니다. 텍스트의 원본이 힌트로 제공될 수 있는 프랑스에서 온 것으로 알려진 경우입니다.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

이제 언어 감지 모델에 더 나은 결정을 내릴 수 있는 추가 컨텍스트가 있습니다. 

**출력**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

분석기가 입력을 구문 분석할 수 없으면 `(Unknown)`을 반환합니다. 숫자로만 구성된 텍스트 문자열을 제출하는 경우를 예로 들 수 있습니다.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": 0.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

### <a name="mixed-language-content"></a>혼합 언어 콘텐츠

동일한 문서 내에 혼합 언어 콘텐츠가 있으면 콘텐츠에서 가장 많은 표현이 있지만 더 낮은 긍정적 등급을 갖는 언어를 반환합니다. 등급은 해당 평가의 한계 강도를 반영합니다. 다음 예제의 입력에는 영어, 스페인어 및 프랑스어가 혼합되어 있습니다. 분석기에서 각 세그먼트의 문자를 계산하여 주요 언어를 결정합니다.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**출력**

결과 출력은 1.0 미만의 점수를 갖는 주요 언어로 구성되어 낮은 신뢰도 수준을 나타냅니다.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 0.88
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-01-05"
}
```

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기(동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자. |
| 요청당 최대 문자 수(비동기)  | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다. |
| 전체 요청의 최대 크기 | 1MB  |
| 요청당 최대 문서 수 | 1000 |

문서가 문자 제한을 초과하면 API는 사용 중인 엔드포인트에 따라 다르게 작동합니다.

* 비동기: API는 전체 요청을 거부하고 그 안의 문서가 최대 크기를 초과하는 경우 `400 bad request` 오류를 반환합니다.
* 동기: API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>참고 항목

* [언어 감지 개요](../overview.md)
