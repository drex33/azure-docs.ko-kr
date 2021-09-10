---
title: PII 검색 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 보강 파이프라인에서 텍스트의 개인 정보를 추출하고 마스킹합니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 71bd45fae729efed6d76ab65fc4ea45944998f45
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537414"
---
# <a name="pii-detection-cognitive-skill"></a>PII 검색 인식 기술

**PII 검색** 기술은 입력 텍스트에서 개인 정보를 추출하고 마스킹 옵션을 제공합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되며, 하루에 인덱서당 20개의 문서를 초과하는 트랜잭션에 대해 [청구 가능한 리소스](cognitive-search-attach-cognitive-services.md)를 필요로 합니다. 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다.
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>데이터 제한

레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 기술로 보내기 전에 데이터를 청크해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분하며 선택 사항입니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `defaultLanguageCode` | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다.  기본 언어 코드가 지정되지 않은 경우 영어(en)가 기본 언어 코드로 사용됩니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md?tabs=pii)을 참조합니다. |
| `minimumPrecision` | 0\.0과 1.0 사이의 값입니다. `piiEntities` 출력의 신뢰도 점수가 설정 `minimumPrecision` 값보다 낮으면 엔터티가 반환 또는 마스크되지 않습니다. 기본값은 0.0입니다. |
| `maskingMode` | 입력 텍스트에서 검색된 개인 정보를 마스킹하는 다양한 방법을 제공하는 매개 변수입니다. 다음과 같은 옵션이 지원됩니다. <ul><li>`none` (기본값): 마스킹을 수행하지 않고 `maskedText` 출력이 반환되지 않습니다. </li><li> `replace`: 검색된 엔터티를 `maskingCharacter` 매개 변수에 지정된 문자로 바꿉니다. 이 문자는 검색된 엔터티의 길이로 반복되어 오프셋이 입력 텍스트와 출력 `maskedText` 모두에 올바르게 일치하게 됩니다.</li></ul> <br/> PIIDetectionSkill 미리 보기 중에는 `maskingMode` 옵션 `redact`도 지원되어 검색된 엔터티를 교체 없이 완전히 제거할 수 있습니다. 이후 `redact` 옵션은 더 이상 사용되지 않으며 향후 기술에서 더 이상 지원되지 않습니다. |
| `maskingCharacter` | `maskingMode` 매개 변수가 `replace`로 설정된 경우 텍스트를 마스킹하는데 사용되는 문자입니다. 지원되는 옵션은 다음과 같습니다. `*`(기본값). `maskingMode`가 `replace`으로 설정되지 않은 경우에 이 매개 변수는 `null`만 될 수 있습니다. <br/><br/> PIIDetectionSkill 미리 보기 동안 추가 `maskingCharacter` 옵션 `X` 및 `#`에 대한 지원이 있었습니다. 이후 `X` 및 `#` 옵션은 더 이상 사용되지 않으며 향후 기술에서 더 이상 지원되지 않습니다. |
| `domain`   | (선택 사항) 문자열 값(지정된 경우)은 엔터티 범주의 하위 집합만 포함하도록 PII 도메인을 설정합니다. 가능한 값에는 `phi`(기밀 상태 정보만 검색), `none`이 포함됩니다. |
| `piiCategories`   | (선택 사항) 검색하고 반환할 엔터티를 지정하려면 적절한 엔터티 범주가 포함된 선택적 `piiCategories` 매개 변수(문자열 목록으로 정의)를 사용합니다. 이 매개 변수를 사용하여 문서 언어에 대해 기본적으로 사용되지 않는 엔터티를 검색할 수도 있습니다. 사용할 수 있는 범주 목록은 [TextAnalytics 설명서](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal)를 참조하세요.  |
| `modelVersion`   | (선택 사항) Text Analytics 서비스를 호출할 때 사용할 모델의 버전입니다. 지정되지 않은 경우 기본적으로 최신 버전을 지정합니다. 필요한 경우가 아니면 이 값을 지정하지 않는 것이 좋습니다. 자세한 내용은 [Text Analytics API의 모델 버전 관리](../cognitive-services/text-analytics/concepts/model-versioning.md)를 참조하세요. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | 레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 레코드를 분석하는 데 기본 언어 코드가 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md?tabs=pii) 참조  |
| `text`          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| `piiEntities` | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>텍스트(추출된 실제 PII)</li> <li>type</li><li>Subtype</li><li>점수(값이 클수록 실제 엔터티일 가능성이 높음)</li><li>입력 텍스트에 대한 오프셋입니다</li><li>length</li></ul> </br> [가능한 type 및 subtype은 여기에서 찾을 수 있습니다.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | `maskingMode`이 `none` 이외의 값으로 설정된 경우 이 출력은 선택한 `maskingMode`에 설명된 대로 입력 텍스트에서 수행된 마스킹의 문자열 결과가 됩니다.  `maskingMode`이 `none`로 설정된 경우에는 이 출력이 표시되지 않습니다. |

## <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
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
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
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
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

이 스킬의 출력값에서 엔터티에 대해 반환되는 오프셋은 [Text Analytics API](../cognitive-services/text-analytics/overview.md)에서 직접 반환되며, 이는 원래 문자열에 인덱싱하는 데 사용할 경우 올바른 콘텐츠를 추출하기 위해 .NET에서 [StringInfo](/dotnet/api/system.globalization.stringinfo) 클래스를 사용해야 합니다.  [자세한 내용은 다음에서 찾을 수 있습니다.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>오류 및 경고

문서의 언어 코드가 지원되지 않는 경우 경고가 반환되고 엔터티가 추출되지 않습니다.
텍스트가 비어 있는 경우 경고가 반환됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 분석하고 경고를 발생합니다.

기술에서 경고를 반환하는 경우 출력`maskedText`이 비어 있을 수 있으며 , 이로써 출력을 필요로 하는 모든 다운스트림 기술에 영향을 줄 수 있습니다. 이러한 이유로 기술 세트 정의를 작성할 때 누락된 출력과 관련된 모든 경고를 조사해야 합니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)