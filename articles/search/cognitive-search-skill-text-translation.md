---
title: 텍스트 번역 인식 기술
titleSuffix: Azure Cognitive Search
description: 텍스트를 평가하고 각 레코드에 대해 Azure Cognitive Search의 AI 보강 파이프라인에서 지정된 대상 언어로 번역된 텍스트를 반환합니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: c55d0e9c7897fdf2e34016bd0f2657db123cee69
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567456"
---
#   <a name="text-translation-cognitive-skill"></a>텍스트 번역 인식 기술

**텍스트 번역** 기술은 텍스트를 평가하고 각 레코드에 대해 지정된 대상 언어로 번역된 텍스트를 반환합니다. 이 기술은 Cognitive Services에서 제공하는 [Translator Text API v3.0](../cognitive-services/translator/reference/v3-0-translate.md)을 사용합니다.

이 기능은 문서가 하나의 언어로 되어 있지 않을 것으로 간주되는 경우에 유용합니다. 이 경우 검색을 인덱싱하기 전에 번역을 통해 텍스트를 단일 언어로 정규화할 수 있습니다.  동일한 텍스트의 복사본을 여러 언어로 제공하는 지역화 사용 사례에도 유용합니다.

[Translator Text API v3.0](../cognitive-services/translator/reference/v3-0-reference.md)은 지역과 무관한 인식 서비스입니다. 즉, 데이터가 Azure Cognitive Search 또는 연결된 Cognitive Services 리소스와 동일한 지역에 유지됨이 보장되지 않습니다.

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되며, 하루에 인덱서당 20개의 문서를 초과하는 트랜잭션에 대한 [청구 가능한 리소스](cognitive-search-attach-cognitive-services.md)를 필요로 합니다. 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 텍스트 번역 기술로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 나누기 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력 | Description |
|---------------------|-------------|
| defaultToLanguageCode | (필수) to 언어를 명시적으로 지정하지 않는 문서에 대해 문서를 번역할 언어 코드입니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/translator/language-support.md)을 참조합니다. |
| defaultFromLanguageCode | (선택 사항) from 언어를 명시적으로 지정하지 않는 문서에 대해 문서를 번역할 언어 코드입니다.  defaultFromLanguageCode가 지정되지 않은 경우 Translator Text API에서 제공하는 자동 언어 감지를 사용하여 from 언어를 결정합니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/translator/language-support.md)을 참조합니다. |
| suggestedFrom | (선택 사항) fromLanguageCode 입력이나 defaultFromLanguageCode 매개 변수가 제공되지 않고 자동 언어 감지에 실패한 경우 문서를 번역할 언어 코드입니다.  suggestedFrom 언어가 지정되지 않은 경우 영어(en)가 suggestedFrom 언어로 사용됩니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/translator/language-support.md)을 참조합니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름     | Description |
|--------------------|-------------|
| text | 번역할 텍스트입니다.|
| toLanguageCode    | 텍스트를 번역할 언어를 나타내는 문자열입니다. 이 입력을 지정하지 않으면 defaultToLanguageCode가 텍스트 번역에 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/translator/language-support.md) 참조|
| fromLanguageCode  | 텍스트의 현재 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 defaultFromLanguageCode(또는 defaultFromLanguageCode가 제공되지 않는 경우 자동 언어 감지)가 텍스트 번역에 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/translator/language-support.md) 참조|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | Description |
|--------------------|-------------|
| translatedText | translatedFromLanguageCode에서 translatedToLanguageCode로의 텍스트 번역 결과에 대한 문자열입니다.|
| translatedToLanguageCode  | 번역한 텍스트의 to 언어 코드를 나타내는 문자열입니다. 여러 언어로 번역하고 어떤 텍스트가 어떤 언어에 대응하는지 지속적으로 확인하려는 경우에 유용합니다.|
| translatedFromLanguageCode    | 번역한 텍스트의 from 언어 코드를 나타내는 문자열입니다. 자동 언어 감지 옵션을 선택한 경우 이 출력이 해당 검색 결과를 제공하므로 유용합니다.|

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>오류 및 경고
from 또는 to 언어에 대해 지원되지 않는 언어 코드를 제공하면 오류가 발생하고 텍스트가 번역되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 번역되고 경고가 발생합니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)