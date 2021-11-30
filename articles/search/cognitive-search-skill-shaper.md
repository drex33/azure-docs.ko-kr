---
title: 쉐이퍼 인식 기술
titleSuffix: Azure Cognitive Search
description: 구조화되지 않은 데이터에서 메타데이터 및 구조화된 정보를 추출하여 Azure Cognitive Search의 AI 보강 파이프라인에서 복합 형식으로 구성합니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 653a4b89eab4d35c6642e8a6b17b34906fe59267
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267359"
---
# <a name="shaper-cognitive-skill"></a>쉐이퍼 인식 기술

**쉐이퍼** 기술은 여러 입력을 보강 파이프라인에서 나중에 참조할 수 있는 [복합 형식](search-howto-complex-data-types.md)으로 통합합니다. **쉐이퍼** 기술을 사용하면 기본적으로 구조를 만들고, 해당 구조의 멤버 이름을 정의하고, 각 멤버에게 값을 할당할 수 있습니다. 검색 시나리오에서 유용한 통합 필드의 예로는 이름과 성을 하나의 구조로 결합하거나, 구/군/시와 시/도를 하나의 구조로 결합하거나, 이름과 생년월일을 하나의 구조로 결합하여 고유한 ID를 설정하는 것을 들 수 있습니다.

또한 [시나리오 3](#nested-complex-types)에 설명된 **쉐이퍼** 기술은 입력에 선택적 *sourceContext* 속성을 추가합니다. *source* 및 *sourceContext* 속성은 함께 사용할 수 없습니다. 입력이 기술 컨텍스트에 있는 경우 간단히 *source* 를 사용하면 됩니다. 입력이 기술 컨텍스트와 ‘다른’ 컨텍스트에 있는 경우 *sourceContext* 를 사용합니다. *sourceContext* 를 사용하려면 원본으로 지정된 특정 요소를 사용하여 중첩된 입력을 정의해야 합니다. 

출력 이름은 항상 “output”입니다. 내부적으로, 파이프라인은 아래 예제에 표시된 것처럼 “analyzedText” 같은 다른 이름을 매핑할 수 있지만 **쉐이퍼** 기술 자체는 응답에 “output”을 반환합니다. 이는 보강된 문서를 디버그하고 이름 불일치를 확인하거나 사용자 지정 기술을 작성하고 응답을 직접 구조화하는 경우에 중요합니다.

> [!NOTE]
> 이 기술은 Cognitive Services에 바인딩되지 않습니다. 청구할 수 없으며 Cognitive Services 주요 요구 사항이 없습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>시나리오 1: 복합 형식

각각 두 개의 멤버(*text* 및 *sentiment*)가 있는 *analyzedText* 라는 구조를 만드는 시나리오를 가정해 보세요. 인덱스에서 다중 파트 검색 가능 필드를 ‘복합 형식’이라고 합니다. 매핑되는 해당 복합 구조가 원본 데이터에 있을 때 이 필드가 만들어지는 경우가 많습니다.

복합 형식을 만드는 또 다른 방법은 **쉐이퍼** 기술을 사용하는 것입니다. 이 기술을 기술 세트에 포함하면 기술 세트를 처리하는 동안 메모리 내 작업이 중첩된 구조의 데이터 셰이프를 출력할 수 있으며, 인덱스의 복합 형식에 매핑될 수 있습니다. 

다음 예제 스킬 정의에서는 구성원 이름을 입력으로 제공합니다. 

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>샘플 인덱스

기술은 인덱서에 의해 호출되며 인덱서에는 인덱스가 필요합니다. 인덱스의 복합 필드 표현은 다음 예제와 유사합니다. 

```json
"name":"my-index",
"fields":[
   { "name":"myId", "type":"Edm.String", "key":true, "filterable":true  },
   { "name":"analyzedText", "type":"Edm.ComplexType",
      "fields":[
         {
            "name":"text",
            "type":"Edm.String",
            "facetable":false,
            "filterable":false,
            "searchable":true,
            "sortable":false  },
         {
            "name":"sentiment",
            "type":"Edm.Double",
            "facetable":true,
            "filterable":true,
            "searchable":true,
            "sortable":true }
      }
```

### <a name="skill-input"></a>기술 입력

이 **쉐이퍼** 기술에 사용 가능한 입력을 제공하는 수신 JSON 문서는 다음과 같을 수 있습니다.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>기술 출력

**쉐이퍼** 기술은 텍스트 및 *text* 및 *sentiment* 의 결합된 요소를 사용하여 *analyzedText* 라는 새 요소를 생성합니다. 이 출력은 인덱스 스키마를 준수합니다. Azure Cognitive Search 인덱스에서 가져오고 인덱싱합니다.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>시나리오 2: 입력 통합

다른 예제에서는 파이프라인 처리의 각기 다른 단계에서 책 제목과 책의 여러 페이지에 있는 장 제목을 추출했다고 가정합니다. 이제 다양한 입력으로 구성된 단일 구조를 만들 수 있습니다.

이 시나리오에 대한 **쉐이퍼** 기술 정의는 다음 예제와 유사합니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>기술 출력
이 경우, **쉐이퍼** 는 모든 장 제목을 단순화하여 단일 배열을 만듭니다. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>시나리오 3: 중첩된 컨텍스트의 입력 통합

책의 제목, 장, 콘텐츠가 있고 콘텐츠에서 엔터티 인식 및 핵심 구를 실행했으며 이제 여러 기술의 결과를 장 이름, 엔터티, 핵심 구가 포함된 단일 셰이프로 집계해야 한다고 가정해 보겠습니다.

이 시나리오에 대한 **쉐이퍼** 기술 정의는 다음 예제와 유사합니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>기술 출력
이 경우 **쉐이퍼** 는 복합 형식을 만듭니다. 이 구조는 메모리 내에 있습니다. [지식 저장소](knowledge-store-concept-intro.md)에 저장하려면 스토리지 특성을 정의하는 기술 세트에 프로젝션을 만들어야 합니다.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [복합 형식을 사용하는 방법](search-howto-complex-data-types.md)
+ [지식 저장소](knowledge-store-concept-intro.md)
+ [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)