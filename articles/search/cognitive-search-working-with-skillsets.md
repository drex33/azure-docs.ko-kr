---
title: 기술 세트 개념
titleSuffix: Azure Cognitive Search
description: 기술 세트는 Azure Cognitive Search에서 AI 보강 파이프라인을 작성하는 위치입니다. 기술 세트 작성에 대한 중요한 개념과 세부 정보를 알아보세요.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 08bf1a57c4b7d4905693c3f11964feeb04c8beef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014864"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure Cognitive Search의 기술 세트 개념

이 문서는 기술 세트 개념 및 컴퍼지션을 심층적으로 이해해야 하는 개발자를 위해 작성되었으며, 이 글을 읽는 독자가 [AI 보강](cognitive-search-concept-intro.md)의 개념과 워크플로에 대해 잘 알고 있다고 전제합니다.

기술 세트는 Azure Cognitive Search의 재사용 가능한 리소스이며 [인덱서](search-indexer-overview.md)에 연결되어 있습니다. 기술 세트에는 외부 데이터 원본에서 검색된 문서에 대한 기본 제공 AI 또는 외부 사용자 지정 처리를 호출하는 원자성 작업인 기술이 하나 이상 포함되어 있습니다.

기술 세트 처리 시작부터 마무리까지, 기술은 보강된 문서를 읽고 씁니다. 보강된 문서는 처음에는 데이터 원본에서 추출한 원시 콘텐츠에 불과하지만, 기술을 실행할 때마다 구조와 내용이 추가됩니다. 궁극적으로 보강된 문서의 노드는 검색 인덱스의 [필드에 매핑](cognitive-search-output-field-mapping.md)되거나 지식 저장소의 [프로젝션에 매핑](knowledge-store-projection-overview.md)되므로 콘텐츠를 적절하게 라우팅할 수 있으며, 이러한 콘텐츠를 다른 앱에서 쿼리하거나 사용할 수 있습니다.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="기술 세트가 있는 파이프라인" border="false":::

## <a name="skillset-definition"></a>기술 집합 정의

기술 세트는 텍스트 번역, 핵심 구 추출 또는 이미지 파일에서 광학 문자 인식 수행과 같은 원자성 보강 작업을 나타내는 하나 이상의 *인식* 배열입니다. 기술은 Microsoft의 [기본 제공 기술](cognitive-search-predefined-skills.md)일 수도 있고, 고객이 제공하는 모델 또는 처리 논리를 포함하는 [사용자 지정](cognitive-search-create-custom-skill-example.md) 기술일 수도 있습니다. 이러한 기술은 인덱싱 중에 사용되거나 지식 저장소에 프로젝션되는 보강된 문서를 생성합니다.

종종 기술의 유형, 컨텍스트, 입력 및 출력이 서로 연결됩니다. 다음 예제에서는 함께 작동하는 두 가지 [기본 제공 기술](cognitive-search-predefined-skills.md)을 보여주고 기술 세트 정의의 몇 가지 용어를 소개합니다. 

+ 기술 #1은 "reviews_text" 원본 필드의 콘텐츠를 입력으로 수락하고 해당 콘텐츠를 5000자 "페이지" 출력으로 분할하는 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)입니다. 큰 텍스트를 여러 개의 작은 청크로 분할하면 자연어를 처리하는 동안 보다 나은 결과를 얻을 수 있습니다.

+ 기술 #2는 [감정 검색 기술](cognitive-search-skill-sentiment.md)로, “pages”를 입력으로 수락하고 감정 분석의 결과를 포함하는 출력으로 “Sentiment”라는 새 필드를 생성합니다.

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

위의 예제에서 유의해야 할 핵심 사항은 입력 및 출력이 이름-값 쌍이고, 한 기술의 출력을 다운스트림 기술의 입력과 매칭할 수 있으며, 모든 기술에는 보강 트리에서 처리가 발생하는 위치를 결정하는 컨텍스트가 있다는 것입니다.

입력 및 출력을 만드는 방법에 대한 자세한 내용은 [주석을 참조하는 방법](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

## <a name="enrichment-tree"></a>보강 트리

보강된 문서는 기술 세트 실행 중에 만들어진 임시 트리와 같은 데이터 구조이며, 기술을 통해 적용된 모든 변경 내용을 수집하여 주소 지정 가능 노드의 계층 구조로 나타냅니다. 노드에는 외부 데이터 원본에서 축자로 전달되는 보강되지 않은 필드도 포함됩니다. 보강된 문서는 기술 세트가 실행되는 기간 동안 존재하지만 지식 저장소에 캐시하거나 유지할 수 있습니다. 

보강된 문서는 처음에는 [*문서 크래킹*](search-indexer-overview.md#document-cracking) 중에 데이터 원본에서 추출된 콘텐츠에 불과하며, 원본에서 텍스트와 이미지가 추출되어 언어 또는 이미지 분석에 사용됩니다. 

초기 콘텐츠는 *루트* 노드()이며 `document\content` 일반적으로 문서 크래킹 중에 데이터 원본에서 추출된 전체 문서 또는 정규화된 이미지입니다. 보강 트리에서 초기 콘텐츠를 설명하는 방법은 각 데이터 원본 형식에 따라 달라집니다. 다음 표에서는 지원되는 여러 데이터 원본에 대한 보강 파이프라인으로 들어가는 문서의 상태를 보여줍니다.

|데이터 원본\구문 분석 모드|기본값|JSON, JSON 줄 및 CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|해당 없음 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|해당 없음|

기술이 실행되면 출력이 보강 트리에 새 노드로 추가됩니다. 이러한 노드는 다운스트림 기술의 입력으로 사용할 수 있으며, 결국에는 지식 저장소에 프로젝션되거나 인덱스 필드에 매핑됩니다. 번역된 문자열과 같은 콘텐츠를 만드는 기술은 해당 출력을 보강된 문서에 씁니다. 마찬가지로 업스트림 기술의 출력을 사용하는 기술은 보강된 문서에서 읽고 필요한 입력을 얻습니다.

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="기술은 보강 트리에서 읽고 씁니다." border="false":::

보강 트리는 원본에서 추출한 콘텐츠 및 메타데이터와 [텍스트 번역 기술](cognitive-search-skill-text-translation.md)의 `translated_text`, [엔터티 인식 기술](cognitive-search-skill-entity-recognition-v3.md)의 `locations` 또는 [핵심 구 추출 기술](cognitive-search-skill-keyphrases.md)의 `keyPhrases`와 같은 기술로 생성된 새 노드로 구성됩니다. 시각적 편집기를 통해 [보강 트리를 시각화하고 작업](cognitive-search-debug-session.md)할 수 있지만, 보강 트리는 대부분 내부 구조입니다. 

보강은 변경할 수 없습니다. 노드를 만든 후에는 편집할 수 없습니다. 기술 세트가 복잡해질수록 보강 트리 역시 복잡해지겠지만, 보강 트리의 모든 노드를 인덱스나 지식 저장소로 만들어야 하는 것은 아닙니다. 보강 출력의 일부만 선택적으로 유지하여 사용하려는 것만 유지할 수 있습니다.

기술의 입력 및 출력은 보강 트리에서 읽고 보강 트리에 쓰기 때문에, 기술 디자인의 일부로 완료할 작업 중 하나는 보강 트리에서 검색 인덱스의 필드로 콘텐츠를 이동하는 [출력 필드 매핑](cognitive-search-output-field-mapping.md)을 만드는 것입니다. 마찬가지로 지식 저장소를 만드는 경우 출력을 프로젝션에 할당되는 [셰이프](knowledge-store-projection-shape.md)로 매핑할 수 있습니다.

> [!NOTE]
> 보강 트리 형식을 사용하면 보강 파이프라인이 메타데이터를 기본 데이터 형식에도 연결할 수 있습니다. 메타데이터는 유효한 JSON 개체가 아니지만 지식 저장소의 프로젝션 정의에서 유효한 JSON 형식으로 프로젝션할 수 있습니다. 자세한 내용은 [쉐이퍼 기술](cognitive-search-skill-shaper.md)을 참조하세요.

## <a name="context"></a>컨텍스트

각 기술에는 컨텍스트가 있으며, 컨텍스트는 전체 문서(`/document`) 또는 트리의 하위 노드(`/document/countries/`)입니다. 컨텍스트는 다음을 결정합니다.

+ 단일 값(필드마다, 문서마다 한 번) 또는 형식 컬렉션의 컨텍스트 값에 대해 기술이 실행되는 횟수. `/*`를 추가하면 컬렉션의 인스턴스마다 한 번씩 기술이 호출됩니다. 

+ 출력 선언 또는 보강 트리에서 기술 출력이 추가되는 위치. 출력은 항상 트리에 컨텍스트 노드의 자식으로 추가됩니다.

+ 입력 셰이프. 다중 수준 컬렉션의 경우 컨텍스트를 부모 컬렉션으로 설정하면 기술의 입력 셰이프에 영향을 줍니다. 예를 들어 국가/지역 목록이 포함된 보강 트리가 있는 경우 각 항목은 우편 번호 목록을 포함하는 시/도 목록으로 보강되고, 컨텍스트를 설정하는 방법에 따라 입력 해석 방법이 결정됩니다.

|Context|입력|입력 셰이프|기술 호출|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |해당 국가/지역의 모든 우편 번호 목록 |국가/지역당 한 번 |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |시/도의 우편 번호 목록 | 국가/지역 및 시/도 조합당 한 번|

## <a name="enrichment-example"></a>보강 예제

[호텔 리뷰 기술 세트](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)를 참조 지점으로 사용하는 이 예제에서는 기술 실행을 통해 [보강 트리](cognitive-search-working-with-skillsets.md#enrichment-tree)가 어떻게 진화하는지 개념 다이어그램을 사용하여 설명합니다.

이 예제에서는 다음과 같은 내용도 보여줍니다.

+ 기술의 컨텍스트와 입력이 작동하여 기술 실행 횟수를 결정하는 원리
+ 컨텍스트에서 입력 셰이프의 기준이 되는 것

이 예제에서 CSV 파일의 원본 필드에는 호텔에 대한 리뷰("reviews_text") 및 등급("reviews_rating")이 포함되어 있습니다. 인덱서는 Blob 스토리지의 메타데이터 필드를 추가하고, 기술은 번역된 텍스트, 감정 점수 및 핵심 구 감지를 추가합니다.

호텔 리뷰 예제에서 보강 프로세스 내의 "문서"는 단일 호텔 리뷰를 나타냅니다.

> [!TIP]
> [Azure Portal](knowledge-store-create-portal.md) 또는 [Postman 및 REST API](knowledge-store-create-rest.md)를 통해 이 데이터에 대한 검색 인덱스 및 지식 저장소를 만들 수 있습니다. [디버그 세션](cognitive-search-debug-session.md)을 사용하여 기술 세트 컴퍼지션, 의존성 및 보강 트리에 미치는 영향에 대한 인사이트를 얻을 수도 있습니다. 이 문서의 이미지는 디버그 세션에서 가져옵니다.

개념적으로 초기 트리는 다음과 같습니다.

![문서 크래킹 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "문서 크래킹 후 기술 실행 전의 보강 트리")

모든 보강의 루트 노드는 `"/document"`입니다. BLOB 인덱서를 사용하면 `"/document"` 노드에는 `"/document/content"` 및 `"/document/normalized_images"`의 자식 노드가 있습니다. 이 예제처럼 CSV 데이터를 사용하면 열 이름이 `"/document"` 아래의 노드에 매핑됩니다.

### <a name="skill-1-split-skill"></a>기술 #1: 분할 기술

원본 콘텐츠가 많은 텍스트 청크로 구성된 경우 언어, 감정 및 핵심 구 검색의 정확도를 높이기 위해 텍스트를 더 작은 구성 요소로 분할하는 것이 유용합니다. 사용할 수 있는 두 가지는 페이지와 문장입니다. 페이지는 약 5000자의 문자로 구성됩니다.

일반적으로 기술 세트에서 텍스트 분할 기술이 가장 먼저입니다.

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

`"/document/reviews_text"` 기술 컨텍스트에서 분할 기술은 `reviews_text`마다 한 번씩 실행됩니다. 기술 출력은 `reviews_text`가 5000개 문자 세그먼트로 청크 분할된 목록입니다. 분할 기술의 출력은 `pages`로 명명되어 보강 트리에 추가됩니다. `targetName` 기능을 사용하면 기술 출력이 보강 트리에 추가되기 전에 이름을 바꿀 수 있습니다.

이제 보강 트리의 기술 컨텍스트 아래에 새 노드가 배치되었습니다. 이 노드는 모든 기술, 프로젝션 또는 출력 필드 매핑에 사용할 수 있습니다. 
 
![기술 #1 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "기술 #1 실행 후의 보강 트리")

기술에 의해 노드에 추가된 보강에 액세스하려면 보강의 전체 경로가 필요합니다. 예를 들어 ```pages``` 노드의 텍스트를 다른 기술의 입력으로 사용하려면 ```"/document/reviews_text/pages/*"```으로 지정해야 합니다. 경로에 대한 자세한 내용은 [참조 주석](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

### <a name="skill-2-language-detection"></a>기술 #2 언어 감지

호텔 검토 문서는 여러 언어로 표현된 피드백을 포함합니다. 언어 감지 기술은 사용되는 언어를 결정합니다. 그 결과가 핵심 구 추출 및 감정 탐지(보이지 않음)로 전달되고, 감정 및 구를 탐지할 때 언어를 고려합니다.

언어 감지 기술은 기술 세트에 정의된 세 번째(기술 #3) 기술이지만, 바로 다음으로 실행할 기술입니다. 입력을 요구하여 차단되지 않으므로 이전 기술과 병렬로 실행됩니다. 앞에 있는 분할 기술과 마찬가지로 언어 감지 기술도 문서마다 한 번씩 호출됩니다. 이제 보강 트리에는 언어에 대한 새 노드가 있습니다.

 ![기술 #2 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "기술 #2 실행 후의 보강 트리")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>기술 #3 및 #4(감정 분석 및 핵심 구 탐지)

고객의 피드백은 다양한 긍정적인 경험과 부정적인 경험을 반영합니다. 감정 분석 기술은 피드백을 분석하여 마이너스 점수와 플러스 점수 또는 감정이 확인되지 않은 경우에는 중립 점수를 할당합니다. 감정 분석과 함께 핵심 구 탐지는 감정의 결과로 나타나는 단어와 짧은 구를 식별하고 추출합니다.

`/document/reviews_text/pages/*` 컨텍스트가 제공되면 `pages` 컬렉션의 항목마다 감정 분석 기술과 핵심 구 기술이 한 번씩 호출됩니다. 기술의 출력은 연결된 페이지 요소 아래의 노드가 됩니다. 

이제 기술 세트의 나머지 기술을 살펴보고 각 기술을 실행할 때마다 보강 트리가 어떻게 발전하는지 시각화할 수 있습니다. 병합 기술 및 쉐이퍼 기술 같은 일부 기술은 마찬가지로 새 노드를 만들지만, 기존 노드의 데이터만 사용하고 완전히 새로운 보강을 만들지는 않습니다.

![모든 기술 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "모든 기술 후의 보강 트리")

위의 트리에서 커넥터 색은 여러 기술이 보강을 만들었으며 노드를 개별적으로 처리해야 하고 부모 노드를 선택할 때 반환되는 개체에 포함되지 않는다는 것을 나타냅니다.

### <a name="skill-5-shaper-skill"></a>기술 #5 쉐이퍼 기술

출력에 [지식 저장소](knowledge-store-concept-intro.md)가 포함되어 있는 경우 [쉐이퍼 기술](cognitive-search-skill-shaper.md)을 마지막 단계로 추가합니다. 쉐이퍼 기술은 보강 트리의 노드에서 데이터 셰이프를 만듭니다. 예를 들어 여러 노드를 단일 셰이프로 통합할 수 있습니다. 그런 다음, 이 셰이프를 테이블로 프로젝션하여(노드는 테이블의 열이 됨) 이름을 기준으로 셰이프를 테이블 프로젝션에 전달할 수 있습니다.

쉐이퍼 기술은 한 기술 아래에서 셰이핑에 집중하기 때문에 쉽게 작업할 수 있습니다. 또는 개별 프로젝션 내에서 인라인 셰이핑을 선택할 수도 있습니다. 쉐이퍼 기술은 보강 트리에 추가하거나 제거하지 않으므로 시각화되지 않습니다. 이미 갖고 있는 보강 트리를 다시 설명하는 수단이라고 생각해면 됩니다. 개념적으로는 데이터베이스의 테이블에서 보기를 만드는 것과 비슷합니다.

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
    },
    {
      "name": "reviews_text",
      "source": "/document/reviews_text"
    },
    {
      "name": "reviews_title",
      "source": "/document/reviews_title"
    },
    {
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

뒤에 있는 소개 및 예제를 사용하여 [기본 제공 기술](cognitive-search-predefined-skills.md)로 첫 번째 기술을 만들어 보세요.

> [!div class="nextstepaction"]
> [첫 번째 기술 세트 만들기](cognitive-search-defining-skillset.md)
