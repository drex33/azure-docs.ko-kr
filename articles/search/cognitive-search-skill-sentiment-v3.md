---
title: 감성 인식 기술(V3)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인에서 텍스트에 대한 감정 레이블을 제공합니다.
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 1c74b716c6a22748a330fb3d325be3202ed89abc
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542771"
---
# <a name="sentiment-cognitive-skill-v3"></a>감성 인식 기술(V3)

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK는 지원되지 않습니다.

V3 **감정** 기술은 구조화되지 않은 텍스트를 평가하고, 각 레코드에 대해 문장 및 문서 수준에서 서비스에서 찾은 가장 높은 신뢰도 점수를 기반으로 감정 레이블(예: "부정", "중립" 및 "긍정")을 제공합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md) 버전 3에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.SentimentSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 5,000자여야 합니다. 감정 기술로 데이터를 보내기 전에 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)을 사용합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름 | 설명 |
|----------------|----------------------|
| `defaultLanguageCode` | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md) 참조 |
| `modelVersion`   | (선택 사항) Text Analytics 서비스를 호출할 때 사용할 모델의 버전입니다. 지정되지 않은 경우 기본적으로 최신 버전을 지정합니다. 필요한 경우가 아니면 이 값을 지정하지 않는 것이 좋습니다. 자세한 내용은 [Text Analytics API의 모델 버전 관리](../cognitive-services/text-analytics/concepts/model-versioning.md)를 참조하세요. |
| `includeOpinionMining` | `true`로 설정하면 [TextAnalytics의 오피니언 마이닝 기능](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)을 사용하여 양상 기반 감정 분석을 출력 결과에 포함할 수 있습니다. 기본값은 `false`입니다. |

## <a name="skill-inputs"></a>기술 입력 

| 입력 이름 | Description |
|--------------------|-------------|
| `text` | 분석할 텍스트입니다.|
| `languageCode`    |  (선택 사항) 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않을 경우 기본값은 “en”입니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다.|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름 | Description |
|--------------------|-------------|
| `sentiment` | 분석된 전체 텍스트(긍정, 중립 또는 부정)의 감정 레이블을 나타내는 문자열 값입니다. |
| `confidenceScores` | 세 개의 이중 값이 있는 복합 형식으로, 하나는 긍정 등급, 다른 하나는 중립 등급, 나머지 하나는 부정 등급입니다. 값의 범위는 0에서 1.00까지이며, 여기서 1.00은 지정된 레이블 할당에서 가능한 가장 높은 신뢰도를 나타냅니다. |
| `sentences` | 텍스트 문장의 감정을 문장별로 구분하는 복합 형식의 컬렉션입니다. 여기에서 `includeOpinionMining`이 `true`로 설정된 경우 오피니언 마이닝 결과가 대상 및 평가 형식으로 반환됩니다.|


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
    "includeOpinionMining": true,
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languageCode"
        }
    ],
    "outputs": [
        {
            "name": "sentiment",
            "targetName": "sentiment"
        },
        {
            "name": "confidenceScores",
            "targetName": "confidenceScores"
        },
        {
            "name": "sentences",
            "targetName": "sentences"
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
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
                "sentiment": "negative",
                "confidenceScores": {
                    "positive": 0.0,
                    "neutral": 0.0,
                    "negative": 1.0
                },
                "sentences": [
                    {
                        "text": "I had a terrible time at the hotel.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset": 0,
                        "length": 35,
                        "targets": [],
                        "assessments": [],
                    },
                    {
                        "text": "The staff was rude and the food was awful.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset":36,
                        "length": 42,
                        "targets": [
                            {
                                "text": "staff",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 40,
                                "length": 5,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/0",
                                    }
                                ]
                            },
                            {
                                "text": "food",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 63,
                                "length": 4,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/1",
                                    }
                                ]
                            }
                        ],
                        "assessments": [
                            {
                                "text": "rude",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 50,
                                "length": 4,
                                "isNegated": false
                            },
                            {
                                "text": "awful",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 72,
                                "length": 5,
                                "isNegated": false
                            }
                        ],
                    }
                ]
            }
        }
    ]
}
```

## <a name="warning-cases"></a>경고 사례
텍스트가 비어 있으면 경고가 생성되고 감정 결과가 반환되지 않습니다.
언어가 지원되지 않으면 경고가 생성되고 감정 결과가 반환되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)