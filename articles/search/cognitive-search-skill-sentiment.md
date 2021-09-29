---
title: 감성 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인 텍스트에서 긍정-네거티브 감정 점수를 추출합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: b750e99d30c0b540a381ce9e9b32b228b4dceaf8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210951"
---
# <a name="sentiment-cognitive-skill"></a>감성 인식 기술

**감성** 기술은 긍정과 부정 지속성에 따라 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 0과 1 사이의 점수를 반환합니다. 점수가 1에 가까울수록 긍정적 감정을 나타내고 점수가 0에 가까울수록 부정적 감정을 나타냅니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

> [!IMPORTANT]
> 이제 감정 기술이 중단되고 [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)로 대체되었습니다. [사용되지 않는 인지 검색 기술](cognitive-search-skill-deprecated.md)의 권장 사항에 따라 지원되는 기술로 마이그레이션하세요.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 5,000자여야 합니다. 감성 분석기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)을 사용합니다.


## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름 | 설명 |
|----------------|----------------------|
| `defaultLanguageCode` | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md) 참조 |

## <a name="skill-inputs"></a>기술 입력 

| 입력 이름 | Description |
|--------------------|-------------|
| `text` | 분석할 텍스트입니다.|
| `languageCode`    |  (선택 사항) 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않을 경우 기본값은 “en”입니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다.|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름 | Description |
|--------------------|-------------|
| `score` | 분석된 텍스트의 감성를 나타내는 0과 1 사이의 값입니다. 값 0에 가까우면 부정적 감정이고, 0.5에 가까우면 중립적 감성이고, 1에 가까우면 긍정적인 감성입니다.|


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="warning-cases"></a>경고 사례
텍스트가 비어 있으면 경고가 생성되고 감정 점수가 반환되지 않습니다.
언어가 지원되지 않으면 경고가 생성되고 감정 점수가 반환되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [감정 기술(V3)](cognitive-search-skill-sentiment-v3.md)