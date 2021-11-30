---
title: 엔터티 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 보강 파이프라인에 있는 텍스트에서 다양한 형식의 엔터티를 추출합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: reference
ms.date: 09/24/2021
ms.openlocfilehash: 5d09a7601d509d1283b2dc7588d26a942dab2b11
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271083"
---
# <a name="entity-recognition-cognitive-skill"></a>엔터티 인식 기술

**엔터티 인식** 기술은 텍스트에서 다양한 형식의 엔터티를 추출합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

> [!IMPORTANT]
> 엔터티 인식 기술은 이제 [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md)로 대체되어 사용되지 않습니다. [사용되지 않는 인지 검색 기술](cognitive-search-skill-deprecated.md)의 권장 사항에 따라 지원되는 기술로 마이그레이션하세요.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분하며 모두 선택 사항입니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `categories`    | 추출되어야 하는 범주 배열입니다.  가능한 범주 형식: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. 범주가 제공되지 않으면 모든 형식이 반환됩니다.|
| `defaultLanguageCode` |    입력 텍스트의 언어 코드입니다. 다음 언어가 지원됩니다. `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans` 모든 엔터티 범주가 모든 언어로 지원되는 것은 아닙니다. 아래를 참고하세요.|
| `minimumPrecision` | 0과 1 사이의 값입니다. `namedEntities` 출력의 신뢰도 점수가 이 값보다 낮으면 엔터티가 반환되지 않습니다. 기본값은 0입니다. |
| `includeTypelessEntities` | 현재 범주에 맞지 않는 잘 알려진 엔터티를 인식하려면 `true`로 설정합니다. 인식된 엔터티는 `entities` 복합 출력 필드에서 반환됩니다. 예를 들어 "Windows 10"은 잘 알려진 엔터티(제품)이지만 "제품"은 지원되는 범주가 아니므로 이 엔터티는 엔터티 출력 필드에 포함됩니다. 기본값은 `false` |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | 선택 사항입니다. 기본값은 `"en"`입니다.  |
| `text`          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

> [!NOTE]
> 모든 엔터티 범주가 모든 언어로 지원되는 것은 아닙니다. `"Person"`, `"Location"`, `"Organization"` 엔터티 범주 형식은 위 언어 전체 목록에 대해 지원됩니다. _de_, _en_, _es_, _fr_, _zh-hans_ 만 `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` 형식의 추출을 지원합니다. 자세한 내용은 [Text Analytics API에 대한 언어 및 지역 지원](../cognitive-services/text-analytics/language-support.md)을 참조하세요.  

| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| `persons`       | 각 문자열이 사람 이름을 나타내는 경우 문자열 배열입니다. |
| `locations`  | 각 문자열이 위치를 나타내는 경우 문자열 배열입니다. |
| `organizations`  | 각 문자열이 조직을 나타내는 경우 문자열 배열입니다. |
| `quantities`  | 각 문자열이 수량을 나타내는 경우 문자열 배열입니다. |
| `dateTimes`  | 각 문자열이 DateTime(텍스트에 표시되는 대로) 값을 나타내는 경우 문자열 배열입니다. |
| `urls` | 각 문자열이 URL을 나타내는 경우 문자열 배열입니다. |
| `emails` | 각 문자열이 메일을 나타내는 경우 문자열 배열입니다. |
| `namedEntities` | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>category</li> <li>값(실제 엔터티 이름)</li><li>오프셋(텍스트에 발견된 위치)</li><li>신뢰도(값이 클수록 실제 엔터티에 가까움)</li></ul> |
| `entities` | 다음 필드와 함께 텍스트에서 추출된 엔터티에 대한 풍부한 정보가 포함된 복합 형식의 배열입니다. <ul><li> name(실제 엔터티 이름. “정규화된” 형식을 나타냄)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl(엔터티에 대한 Wikipedia 페이지 링크)</li><li>bingId</li><li>type(인식된 엔터티의 범주)</li><li>subType(특정 범주에서만 사용 가능, 더 세부적인 엔터티 형식 보기를 제공함)</li><li> matches(포함된 복합 컬렉션)<ul><li>text(엔터티의 원시 텍스트)</li><li>offset(발견된 위치)</li><li>length(원시 엔터티 텍스트의 길이)</li></ul></li></ul> |

##    <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

이 출력값에서 엔터티에 대해 반환되는 오프셋은 [Text Analytics API](../cognitive-services/text-analytics/overview.md)에서 직접 반환되며, 이는 원래 문자열에 인덱싱하는 데 사용할 경우 올바른 콘텐츠를 추출하기 위해 .NET에서 [StringInfo](/dotnet/api/system.globalization.stringinfo) 클래스를 사용해야 한다는 의미입니다.  [자세한 내용은 여기에서 찾을 수 있습니다.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>경고 사례
문서의 언어 코드가 지원되지 않는 경우 경고가 반환되고 엔터티가 추출되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술(V3)](cognitive-search-skill-entity-recognition-v3.md)