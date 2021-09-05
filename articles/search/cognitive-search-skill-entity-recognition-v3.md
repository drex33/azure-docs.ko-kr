---
title: 엔터티 인식(V3) 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 보강 파이프라인에 있는 Text Analytics V3을 사용하여 다양한 형식의 엔터티를 추출합니다.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 14811591feea9df735bf41e23a81e3a96faa2662
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537410"
---
# <a name="entity-recognition-cognitive-skill-v3"></a>엔터티 인식 기술(V3)

**엔터티 인식** 기술은 텍스트에서 다양한 형식의 엔터티를 추출합니다. 이러한 엔터티는 사람 및 조직에서 URL 및 전화 번호에 이르기까지 14개의 고유한 범주에 속합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되며, 하루에 인덱서당 20개의 문서를 초과하는 트랜잭션에 대해 [청구 가능한 리소스](cognitive-search-attach-cognitive-services.md)를 필요로 합니다. 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityRecognitionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. EntityRecognition 기술로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려하세요.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분하며 모두 선택 사항입니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `categories`    | 추출되어야 하는 범주 배열입니다.  가능한 범주 형식: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"`, `"IP Address"` 범주가 제공되지 않으면 모든 형식이 반환됩니다.|
| `defaultLanguageCode` |    입력 텍스트의 언어 코드입니다. 기본 언어 코드가 지정되지 않은 경우 영어(en)가 기본 언어 코드로 사용됩니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다. 모든 엔터티 범주가 모든 언어로 지원되는 것은 아닙니다. 아래를 참고하세요.|
| `minimumPrecision` | 0과 1 사이의 값입니다. `namedEntities` 출력의 신뢰도 점수가 이 값보다 낮으면 엔터티가 반환되지 않습니다. 기본값은 0입니다. |
| `modelVersion` | (선택 사항) Text Analytics 서비스를 호출할 때 사용할 모델의 버전입니다. 지정되지 않은 경우 기본적으로 사용 가능한 최신 버전을 지정합니다. 필요한 경우가 아니면 이 값을 지정하지 않는 것이 좋습니다. 자세한 내용은 [Text Analytics API의 모델 버전 관리](../cognitive-services/text-analytics/concepts/model-versioning.md)를 참조하세요.|


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 레코드를 분석하는 데 기본 언어 코드가 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다. |
| `text`          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

> [!NOTE]
>모든 엔터티 범주가 모든 언어로 지원되는 것은 아닙니다. 사용할 언어에 대해 지원되는 엔터티 범주를 알아보려면 [Text Analytics API v3에서 지원되는 엔터티 범주](../cognitive-services/text-analytics/named-entity-types.md)를 참조하세요.

| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| `persons`       | 각 문자열이 사람 이름을 나타내는 경우 문자열 배열입니다. |
| `locations`  | 각 문자열이 위치를 나타내는 경우 문자열 배열입니다. |
| `organizations`  | 각 문자열이 조직을 나타내는 경우 문자열 배열입니다. |
| `quantities`  | 각 문자열이 수량을 나타내는 경우 문자열 배열입니다. |
| `dateTimes`  | 각 문자열이 DateTime(텍스트에 표시되는 대로) 값을 나타내는 경우 문자열 배열입니다. |
| `urls` | 각 문자열이 URL을 나타내는 경우 문자열 배열입니다. |
| `emails` | 각 문자열이 메일을 나타내는 경우 문자열 배열입니다. |
| `personTypes` | 각 문자열이 PersonType을 나타내는 경우 문자열 배열입니다. |
| `events` | 각 문자열이 이벤트를 나타내는 경우 문자열 배열입니다. |
| `products` | 각 문자열이 제품을 나타내는 경우 문자열 배열입니다. |
| `skills` | 각 문자열이 기술을 나타내는 경우 문자열 배열입니다. |
| `addresses` | 각 문자열이 주소를 나타내는 경우 문자열 배열입니다. |
| `phoneNumbers` | 각 문자열이 전화 번호를 나타내는 경우 문자열 배열입니다. |
| `ipAddresses` | 각 문자열이 IP 주소를 나타내는 경우 문자열 배열입니다. |
| `namedEntities` | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>category</li> <li>subcategory</li> <li>confidenceScore(값이 클수록 실제 엔터티에 가까움)</li> <li>length(이 엔터티의 길이(문자 수))</li> <li>오프셋(텍스트에 발견된 위치)</li> <li>text(텍스트에 표시되는 실제 엔터티 이름)</li></ul> |


##    <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
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
            "name": "persons", 
            "targetName": "people"
        },
        {
            "name": "emails", 
            "targetName": "emails"
        },
        {
            "name": "namedEntities", 
            "targetName": "namedEntities"
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
             "text": "Contoso Corporation was founded by Jean Martin. They can be reached at contact@contoso.com",
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
        "people": [ "Jean Martin"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category": "Person",
            "subcategory": null,
            "length": 11,
            "offset": 35,
            "confidenceScore": 0.98,
            "text": "Jean Martin"
          },
          {
            "category": "Email",
            "subcategory": null,
            "length": 19,
            "offset": 71,
            "confidenceScore": 0.8,
            "text": "contact@contoso.com"
          }
        ],
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