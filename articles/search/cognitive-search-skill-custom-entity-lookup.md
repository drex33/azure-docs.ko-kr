---
title: 사용자 지정 엔터티 조회 인식 검색 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인지 검색 파이프라인의 텍스트에서 다른 사용자 지정 엔터티를 추출합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 977ac567f195e0ab8053d7b8bd98543801a3b6a4
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537274"
---
# <a name="custom-entity-lookup-cognitive-skill"></a>사용자 지정 엔터티 조회 인식 기술

**사용자 지정 엔터티 조회** 기술은 사용자 지정된 단어 및 구의 사용자 정의 목록에서 텍스트를 찾습니다. 이 목록을 사용하면 일치하는 엔터티가 있는 모든 문서에 레이블이 지정됩니다. 또한 이 기술은 비슷하지만 정확하지 않는 일치 항목을 찾는 데 적용할 수 있는 유사 항목 일치 수준을 지원합니다.  

> [!NOTE]
> 이 기술은 Cognitive Services API에 바인딩되지 않지만 20개를 초과하는 트랜잭션을 허용하려면 Cognitive Services 키가 필요합니다. 이 기술은 [Cognitive Search를 통해 측정](https://azure.microsoft.com/pricing/details/search/#pricing)됩니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>데이터 제한
+ 지원되는 최대 입력 레코드 크기는 256MB입니다. 사용자 지정 엔터티 조회 기술로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 나누기 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.
+ *entitiesDefinitionUri* 매개 변수를 사용하여 제공되는 경우 지원되는 최대 엔터티 정의 테이블은 10MB입니다. 
+ 엔터티가 인라인으로 정의되고 *inlineEntitiesDefinition* 매개 변수를 사용하여 제공되는 경우 지원되는 최대 크기는 10KB입니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `entitiesDefinitionUri`    | 일치시킬 대상 텍스트를 모두 포함하는 JSON 또는 CSV 파일의 경로입니다. 이 엔터티 정의는 인덱서 실행이 시작될 때 읽힙니다. 파일 실행 중 발생하는 업데이트는 이후에 실행될 때까지 인식되지 않습니다. 이 구성은 HTTPS로 액세스할 수 있어야 합니다. 필요한 CSV 또는 JSON 스키마는 아래 [사용자 지정 엔터티 정의](#custom-entity-definition-format) 형식을 참조하세요.|
|`inlineEntitiesDefinition` | 인라인 JSON 엔터티 정의입니다. 이 매개 변수가 있는 경우 entitiesDefinitionUri 매개 변수를 대체합니다. 10KB 이하의 구성은 인라인으로 제공될 수 있습니다. 필요한 JSON 스키마는 아래 [사용자 지정 엔터티 정의](#custom-entity-definition-format)를 참조하세요. |
|`defaultLanguageCode` |    (선택 사항) 입력 텍스트를 토큰화하고 나타내는 데 사용되는 입력 텍스트의 언어 코드입니다. `da, de, en, es, fi, fr, it, ko, pt`가 지원됩니다. 기본값은 영어(`en`)입니다. languagecode-countrycode 형식을 전달하는 경우 형식의 languagecode 부분만 사용됩니다.  |
|`globalDefaultCaseSensitive` | (선택 사항) 기술의 기본 대/소문자를 구분 값입니다. 엔터티의 `defaultCaseSensitive` 값이 지정되지 않은 경우 이 값은 엔터티의 `defaultCaseSensitive` 값이 됩니다. |
|`globalDefaultAccentSensitive` | (선택 사항) 기술의 기본 악센트 구분 값입니다. 엔터티의 `defaultAccentSensitive` 값이 지정되지 않은 경우 이 값은 엔터티의 `defaultAccentSensitive` 값이 됩니다. |
|`globalDefaultFuzzyEditDistance` | (선택 사항) 기술의 기본 유사 항목 편집 거릿값입니다. 엔터티의 `defaultFuzzyEditDistance` 값이 지정되지 않은 경우 이 값은 엔터티의 `defaultFuzzyEditDistance` 값이 됩니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                   |
|---------------|-------------------------------|
| `text`          | 분석할 텍스트입니다.          |
| `languageCode`    | 선택 사항입니다. 기본값은 `"en"`입니다.  |


## <a name="skill-outputs"></a>기술 출력


| 출력 이름      | 설명                   |
|---------------|-------------------------------|
| `entities` | 찾은 일치 항목 및 관련 메타데이터에 대한 정보를 포함하는 개체의 배열입니다. 식별된 각 엔터티에는 다음과 같은 필드가 포함될 수 있습니다.  <ul> <li> ‘이름’: 최상위 엔터티를 식별합니다. 엔터티는 “정규화된” 형식을 나타냅니다. </li> <li> *ID*: 사용자가 “사용자 지정 엔터티 정의 형식”에서 정의한 엔터티의 고유 식별자입니다.</li> <li> ‘설명’: 사용자가 “사용자 지정 엔터티 정의 형식”에서 정의한 엔터티 설명입니다. </li> <li> ‘유형’: 사용자가 “사용자 지정 엔터티 정의 형식”에서 정의한 엔터티 형식입니다.</li> <li> ‘하위 유형’: 사용자가 “사용자 지정 엔터티 정의 형식”에서 정의한 엔터티 하위 유형입니다.</li>  <li> ‘일치’: 원본 텍스트의 해당 엔터티와 일치하는 각 항목을 설명하는 컬렉션입니다. 각 일치 항목에는 다음 멤버가 포함됩니다. </li> <ul> <li> ‘텍스트’: 원본 문서와 일치하는 원시 텍스트입니다. </li> <li> ‘오프셋’: 텍스트에서 일치 항목을 찾은 위치입니다. </li> <li> ‘길이’: 일치하는 텍스트의 길이입니다. </li> <li> *matchDistance*: 원본 엔터티의 이름 또는 별칭과 다른 일치 문자 수  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>사용자 지정 엔터티 정의 형식

사용자 지정 엔터티 목록을 사용자 지정 엔터티 조회 기술에 제공하는 세 가지 방법이 있습니다. .CSV 파일, .JSON 파일, 또는 기술 정의의 일부인 인라인 정의로 목록을 제공할 수 있습니다.  

정의 파일이 .CSV 또는 .JSON 파일이면 파일 경로를 *entitiesDefinitionUri* 매개 변수의 일부로 제공해야 합니다. 이 경우 파일은 각 인덱서 실행이 시작될 때 한 번 다운로드됩니다. 인덱서를 실행하려면 파일에 액세스할 수 있어야 합니다. 또한 파일은 UTF-8로 인코딩해야 합니다.

정의가 인라인으로 제공되는 경우 *inlineEntitiesDefinition* 기술 매개 변수의 콘텐츠로 인라인 제공되어야 합니다. 

### <a name="csv-format"></a>CSV 형식

파일의 경로를 제공하고 *entitiesDefinitionUri* 기술 매개 변수에서 설정하여 CSV(쉼표로 구분된 값) 파일에서 찾을 사용자 지정 엔터티의 정의를 제공할 수 있습니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10MB입니다.

CSV 형식은 간단합니다. 각 줄은 아래와 같이 고유한 엔터티를 나타냅니다.

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

이 사례에서 세 가지 엔터티(Bill Gates, Satya Nadella, Microsoft)가 찾은 엔터티로 반환될 수 있지만, 줄의 용어(별칭)가 하나라도 텍스트와 일치하는 경우에 식별됩니다. 예를 들어, 문서에서 “William H. Gates” 문자열을 찾은 경우 “Bill Gates” 엔터티와 일치하는 항목이 반환됩니다.

### <a name="json-format"></a>JSON 형식

JSON 파일로도 검색할 사용자 지정 엔터티의 정의를 제공할 수 있습니다. JSON 형식에서는 용어마다 일치 규칙을 정의할 수 있으므로 좀 더 유연하게 사용할 수 있습니다. 예를 들어 각 용어의 유사 일치 거리(Damerau-Levenshtein 거리)를 지정하거나 일치가 대/소문자를 구분하는지 여부를 지정할 수 있습니다. 

 CSV 파일의 경우와 마찬가지로 JSON 파일의 경로를 제공하고 *entitiesDefinitionUri* 기술 매개 변수에서 설정해야 합니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10MB입니다.

가장 기본적인 JSON 사용자 지정 엔터티 목록 정의는 일치시킬 엔터티의 목록이 될 수 있습니다.

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON 정의의 좀 더 복잡한 예는 필요에 따라 각 엔터티의 ID, 설명, 유형 및 하위 유형뿐만 아니라 다른 ‘별칭’을 제공할 수 있습니다. 별칭 용어가 일치하면 엔터티도 반환됩니다.

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

아래 표는 일치시킬 엔터티를 정의할 때 설정할 수 있는 다양한 구성 매개 변수에 대한 자세한 정보를 보여줍니다.

|  필드 이름  |        Description  |
|--------------|----------------------|
| `name` | 최상위 엔터티 설명자입니다. 기술 출력의 일치 항목은 이 이름으로 그룹화되고 검색되는 텍스트의 “정규화된” 형식을 나타냅니다.  |
| `description`  | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 일치하는 모든 엔터티에서 이 필드의 값이 기술 출력에 표시됩니다. |
| `type` | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 일치하는 모든 엔터티에서 이 필드의 값이 기술 출력에 표시됩니다. |
| `subtype` | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 일치하는 모든 엔터티에서 이 필드의 값이 기술 출력에 표시됩니다. |
| `id` | (선택 사항) 이 필드는 일치하는 텍스트에 대한 사용자 지정 메타데이터에 대한 통과로 사용할 수 있습니다. 일치하는 모든 엔터티에서 이 필드의 값이 기술 출력에 표시됩니다. |
| `caseSensitive` | (선택 사항) 기본값은 false입니다. 엔터티 이름과 비교할 때 대/소문자를 구분해야 하는지를 나타내는 부울 값입니다. 대/소문자를 구분하지 않는 “Microsoft” 일치 샘플은 microsoft, microSoft, MICROSOFT입니다. |
| `accentSensitive` | (선택 사항) 기본값은 false입니다. ‘é’ 및 ‘e’와 같이 악센트가 있는 문자와 없는 문자가 동일한지를 나타내는 부울 값입니다. |
| `fuzzyEditDistance` | (선택 사항) 기본값은 0입니다. 최댓값은 5입니다. 엔터티 이름과 일치하는 것으로 여겨질 수 있는 허용 가능한 다른 문자 수를 나타냅니다. 각 일치에 대한 최소 허용량이 반환됩니다.  예를 들어 편집 거리가 3으로 설정된 경우 “Windows 10”은 “Windows”, “Windows10” 및 “windows 7”과 일치하는 것으로 여겨집니다. <br/> 대/소문자 구분이 false로 설정된 경우 대/소문자 차이는 허용 오차에 포함되지 않지만 그렇지 않은 경우에는 포함됩니다. |
| `defaultCaseSensitive` | (선택 사항) 이 엔터티의 기본 대/소문자 구분 값을 변경합니다. 모든 별칭 caseSensitive 값의 기본값을 변경하는 데 사용합니다. |
| `defaultAccentSensitive` | (선택 사항) 이 엔터티에 대한 기본 악센트 구분 값을 변경합니다. 모든 별칭 accentSensitive 값의 기본값을 변경하는 데 사용합니다.|
| `defaultFuzzyEditDistance` | (선택 사항) 이 엔터티의 기본 유사 항목 편집 거릿값을 변경합니다. 모든 별칭 fuzzyEditDistance 값의 기본값을 변경하는 데 사용합니다. |
| `aliases` | (선택 사항) 루트 엔터티 이름의 대체 철자 또는 동의어를 지정하는 데 사용할 수 있는 복합 개체의 배열입니다. |

| 별칭 속성 | 설명 |
|------------------|-------------|
| `text`  | 일부 대상 엔터티 이름의 대체 철자 또는 표현입니다.  |
| `caseSensitive` | (선택 사항) 위의 루트 엔터티 “caseSensitive” 매개 변수와 동일하게 작동하지만 이 별칭 하나에만 적용됩니다. |
| `accentSensitive` | (선택 사항) 위의 루트 엔터티 “accentSensitive” 매개 변수와 동일하게 작동하지만 이 별칭 하나에만 적용됩니다. |
| `fuzzyEditDistance` | (선택 사항) 위의 루트 엔터티 “fuzzyEditDistance” 매개 변수와 동일하게 작동하지만 이 별칭 하나에만 적용됩니다. |

### <a name="inline-format"></a>인라인 형식

기술 정의에 직접 인라인으로 일치시킬 사용자 지정 엔터티 목록을 제공하는 것이 더 편리한 경우도 있습니다. 이 경우 위에서 설명한 것과 비슷한 JSON 형식을 사용할 수 있지만 기술 정의에서 인라인됩니다.
크기가 10KB 미만(직렬화된 크기)인 구성만 인라인으로 정의될 수 있습니다. 

## <a name="sample-definition"></a>샘플 정의

인라인 형식을 사용하는 샘플 기술 정의는 다음과 같습니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```

또는 엔터티 정의 파일에 대한 포인터를 제공하려는 경우 `entitiesDefinitionUri` 형식을 사용한 샘플 기술 정의가 아래에 나와 있습니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
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
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>샘플 출력

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>오류 및 경고

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>경고: 일치 항목이 최대 용량에 도달하여 모든 중복 일치 항목을 건너뜁니다.

이 경고는 검색된 일치 항목의 수가 허용되는 최댓값보다 큰 경우에 나타납니다. 이 경우 중복된 일치 항목의 포함이 중지됩니다. 이를 원치 않으시는 경우 개별 사용 사례를 지원할 수 있도록 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하세요.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술(잘 알려진 엔터티를 검색)](cognitive-search-skill-entity-recognition-v3.md)
