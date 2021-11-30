---
title: 엔터티 연결 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 보강 파이프라인에 있는 텍스트에서 연결된 다양한 엔터티를 추출합니다.
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 6c5cdf6442d97172d9a5f66488177598bd6d56cb
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271102"
---
# <a name="entity-linking-cognitive-skill"></a>엔터티 연결 인식 기술

**엔터티 링크 설정** 기술은 잘 알려진 기술 자료인 Wikipedia의 문서에 대한 링크가 있는 인식된 엔터티의 목록을 반환합니다.

> [!NOTE]
> 이 기술은 Cognitive Services [Text Analytics](../cognitive-services/text-analytics/overview.md)에 바인딩되며, 하루에 인덱서당 20개의 문서를 초과하는 트랜잭션에 대한 [청구 가능한 리소스](cognitive-search-attach-cognitive-services.md)를 필요로 합니다. 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>데이터 제한

레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. EntityLinking 기술로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 나누기 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수 이름은 대/소문자를 구분하며 모두 선택 사항입니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |    입력 텍스트의 언어 코드입니다. 기본 언어 코드가 지정되지 않은 경우 영어(en)가 기본 언어 코드로 사용됩니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다. |
| `minimumPrecision` | 0과 1 사이의 값입니다. `entities` 출력의 신뢰도 점수가 이 값보다 낮으면 엔터티가 반환되지 않습니다. 기본값은 0입니다. |
| `modelVersion` | (선택 사항) Text Analytics 서비스를 호출할 때 사용할 모델의 버전입니다. 지정되지 않은 경우 기본적으로 사용 가능한 최신 버전을 지정합니다. 필요한 경우가 아니면 이 값을 지정하지 않는 것이 좋습니다. 자세한 내용은 [Text Analytics API의 모델 버전 관리](../cognitive-services/text-analytics/concepts/model-versioning.md)를 참조하세요.|

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 레코드를 분석하는 데 기본 언어 코드가 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다. |
| `text`          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| `entities` | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>name(텍스트에 표시되는 실제 엔터티 이름)</li> <li>id </li> <li>language(기술에서 결정된 텍스트의 언어)</li> <li>url(이 엔터티에 대한 연결된 URL)</li> <li>bingId(이 연결된 엔터티의 bingId)</li> <li>dataSource(url과 연결된 데이터 원본) </li> <li>matches(`text`, `offset`, `length` 및 `confidenceScore`를 포함하는 복합 형식의 배열)</li></ul>|

## <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
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
            "name": "entities", 
            "targetName": "entities" 
        }
    ]
}
```

## <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
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