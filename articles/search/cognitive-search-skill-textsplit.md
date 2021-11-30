---
title: 텍스트 분할 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 파이프라인의 길이에 따라 텍스트를 텍스트의 페이지나 청크로 나눕니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 4e0b5fd69e31eb035c9155986dd9f00f97986814
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271349"
---
# <a name="text-split-cognitive-skill"></a>텍스트 분할 인식 기술

**분할 텍스트** 기술은 텍스트를 텍스트의 청크로 나눕니다. 텍스트를 문장으로 또는 특정 길이의 페이지로 나눌 것인지 여부를 지정할 수 있습니다. 이 기술은 다른 기술 다운스트림에서 최대 텍스트 길이 요구 사항이 있는 경우 특히 유용합니다. 

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되지 않습니다. 청구할 수 없으며 Cognitive Services 주요 요구 사항이 없습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| `textSplitMode`    | `pages` 또는 `sentences` | 
| `maximumPageLength` | `textSplitMode`가 `pages`로 설정된 경우에만 적용됩니다. 이 값은 `String.Length`로 측정된 최대 페이지 길이(문자 수)를 나타냅니다. 최솟값은 300이고 최댓값은 100,000이며, 기본값은 10,000입니다.  알고리즘은 문장 경계에서 텍스트를 분할하려고 하므로 각 청크 크기가 `maximumPageLength`보다 약간 작을 수 있습니다. | 
| `defaultLanguageCode` | (선택 사항) 다음 언어 코드 `am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans` 중 하나입니다. 기본값은 영어(en)입니다. 고려할 사항은 다음과 같습니다.<ul><li>언어 코드를 제공하면 중국어, 일본어, 한국어 등 비공백 언어에 대해 단어를 반으로 줄이지 못하게 하는 데 유용합니다.</li><li>언어를 모르는 경우(예: 입력 텍스트를 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)로 분할해야 하는 경우) 영어(en)의 기본값은 충분해야 합니다. </li></ul>  |


## <a name="skill-inputs"></a>기술 입력

| 매개 변수 이름       | Description      |
|----------------------|------------------|
| `text`    | 하위 문자열로 분할될 텍스트입니다. |
| `languageCode`    | (선택 사항) 문서에 대한 언어 코드입니다. 언어를 모르는 경우(예: 입력 텍스트를 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)로 분할해야 하는 경우) 이 입력을 제거하는 것이 안전합니다. 언어가 위의 `defaultLanguageCode` 매개 변수에 대해 지원되는 목록에 없는 경우 경고가 표시되고 텍스트가 분할되지 않습니다.  |

## <a name="skill-outputs"></a>기술 출력 

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `textItems`   | 추출된 하위 문자열의 배열입니다. |


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia...",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>샘플 출력

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>오류 사례
언어가 지원되지 않으면 경고가 생성됩니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
