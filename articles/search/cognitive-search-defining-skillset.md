---
title: 기술 집합 만들기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 사용을 위해 데이터에서 구조화된 정보를 추출하고 보강하기 위한 데이터 추출, 자연어 처리 또는 이미지 분석 단계입니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 5f47eef6d3aeecd64cb3777b1d0858b4904d137d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567793"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search에서 기술 세트 만들기

![인덱서 단계](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "인덱서 단계")

기술 세트는 검색 가능하도록 데이터를 추출하고 보강하는 작업을 정의합니다. 기술 세트는 원본 문서에서 텍스트와 이미지 콘텐츠가 추출된 경우 문서 크래킹 후에 실행되고, 원본 문서의 모든 필드가 인덱스 또는 지식 저장소의 대상 필드에 매핑된 후(선택 사항)에 실행됩니다.

이 문서에서는 기술 세트를 만드는 단계에 대해 알아봅니다. 참조를 위해 이 문서에서는 [기술 세트 만들기(REST API)](/rest/api/searchservice/create-skillset)를 사용합니다. 

기술 세트에 대한 몇 가지 사용 규칙은 다음과 같습니다.

+ 기술 세트는 최상위 리소스입니다. 이는 한 번 생성되어 여러 인덱서에서 참조될 수 있음을 의미합니다.
+ 기술 세트에는 기술이 한 가지 이상 포함되어 있어야 합니다.
+ 기술 세트는 동일한 유형의 기술(예: 여러 Shaper 기술)을 반복할 수 있습니다.

인덱서는 기술 세트 실행을 주도합니다. 즉, 기술 세트를 테스트하려면 먼저 [인덱서](search-howto-create-indexers.md), [데이터 원본](search-data-sources-gallery.md) 및 [검색 인덱스](search-what-is-an-index.md)도 만들어야 합니다.

> [!TIP]
> [보강 캐싱](cognitive-search-incremental-indexing-conceptual.md)을 사용하여 이미 처리한 콘텐츠를 다시 사용하고 개발 비용을 절감할 수 있습니다.

## <a name="skillset-definition"></a>기술 집합 정의

기본 구조로 시작합니다. [REST API](/rest/api/searchservice/create-skillset)에서 기술 세트는 JSON으로 작성되고 다음 섹션을 포함합니다.

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

기술 세트는 이름과 설명 뒤에 네 가지 기본 속성이 있습니다.

+ `skills` 배열은 순서가 지정되지 않은 [기술 컬렉션](cognitive-search-predefined-skills.md)이며, 검색 서비스에서는 각 기술에 필요한 입력을 기준으로 실행 순서를 결정합니다. 기술이 독립적이면 동시에 실행됩니다. 기술은 실용적(예: 텍스트 분할), 변형(Cognitive Services의 AI 기반) 또는 사용자가 제공하는 사용자 지정 기술이 될 수 있습니다. 다음 섹션에는 기술 배열의 예가 나와 있습니다.

+ `cognitiveServices`는 Cognitive Services API를 호출하는 [청구 가능 기술](cognitive-search-predefined-skills.md)에서 사용됩니다. 청구 가능한 기술 또는 사용자 지정 엔터티 조회를 사용하지 않는 경우 이 섹션을 제거합니다. 사용하는 경우 [리소스를 연결](cognitive-search-attach-cognitive-services.md)합니다.

+ `knowledgeStore`(선택 사항)는 Azure Storage의 테이블, Blob, 파일에 기술 세트 출력을 프로젝션하기 위한 Azure Storage 계정과 설정을 지정합니다. 필요하지 않은 경우 이 섹션을 제거하고, 그렇지 않은 경우 [지식 저장소를 지정](knowledge-store-create-rest.md)합니다.

+ `encryptionKey`(선택 사항)는 기술 세트 정의에서 중요한 콘텐츠를 암호화하는 데 사용되는 Azure Key Vault 및 [고객 관리형 키](search-security-manage-encryption-keys.md)를 지정합니다. 고객 관리형 암호화를 사용하지 않는 경우 이 속성을 제거합니다.

## <a name="add-a-skills-array"></a>기술 배열 추가

기술 세트 정의 내에서 기술 배열은 실행할 기술을 지정합니다. 다음 예제에서는 관련 없는 두 가지 [기본 제공 기술](cognitive-search-predefined-skills.md)을 표시하여 컴퍼지션을 소개합니다. 각 기술에는 형식과 컨텍스트, 입력, 출력이 있습니다. 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> [조건부 기술](cognitive-search-skill-conditional.md)을 사용하여 식을 만들면 루프와 분기를 사용하여 복잡한 기술 세트를 빌드할 수 있습니다. 구문은 몇 가지 수정 사항이 포함된 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 경로 표기를 기반으로 보강 트리의 노드를 식별합니다. `"/"`는 트리에서 더 낮은 수준으로 트래버스하고, `"*"`는 컨텍스트에서 for-each 연산자 역할을 합니다. 이 문서의 다양한 예제에서 구문을 보여 줍니다. 

### <a name="how-built-in-skills-are-structured"></a>기본 제공 기술을 구성하는 방법

각 기술은 입력 값과 필요한 매개 변수 측면에서 고유합니다. [각 기술 설명서](cognitive-search-predefined-skills.md)에서는 지정된 기술에 대한 매개 변수와 속성을 모두 설명합니다. 차이점이 있지만 대부분의 기술은 공통된 세트를 공유하고 패턴이 비슷합니다. 몇 가지 요점을 설명하기 위해 [엔터티 인식 기술](cognitive-search-skill-entity-recognition-v3.md)에서는 다음과 같은 예를 제공합니다.

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

공통 매개 변수에는 ‘odata.type’과 ‘inputs’, ‘outputs’가 포함됩니다. 다른 매개 변수, 즉 ‘categories’와 ‘defaultLanguageCode’는 엔터티 인식과 관련된 매개 변수의 예입니다. 

+ **‘odata.type’** 은 각 기술을 고유하게 식별합니다. [기술 참조 설명서](cognitive-search-predefined-skills.md)에서 유형을 찾을 수 있습니다.

+ **‘context’** 는 보강 트리의 노드로, 연산이 수행되는 수준을 나타냅니다. 모든 기술에는 이 속성이 있습니다. ‘context’ 필드가 명시적으로 설정되지 않은 경우 기본 컨텍스트는 `"/document"`입니다. 예에서 컨텍스트는 전체 문서로, 엔터티 인식 기술이 문서별로 한 번씩 호출된다는 의미입니다.

  또한 컨텍스트는 보강 트리에서 출력이 생성되는 위치도 결정합니다. 이 예에서 기술은 `"/document"` 자식 노드로 추가되는 `orgs`로 캡처된, `"organizations"`라는 속성을 반환합니다. 다운스트림 기술에서 새로 만든 이 보강 노드의 경로는 `"/document/orgs"`입니다. 특정 문서의 경우 `"/document/orgs"` 값은 텍스트에서 추출된 조직의 배열입니다(예: `["Microsoft", "LinkedIn"]`). 경로 구문에 대한 자세한 내용은 [기술 세트의 주석 참조](cognitive-search-concept-annotations-syntax.md)를 참조하세요.

+ **‘inputs’** 는 들어오는 데이터의 출처와 해당 데이터의 사용 방법을 지정합니다. 엔터티 인식의 경우 입력 중 하나가 `"text"`로, 이는 엔터티에 대해 분석할 콘텐츠입니다. 콘텐츠는 보강 트리의 `"/document/content"` 노드에서 제공됩니다. 보강 트리에서 `"/document"`는 루트 노드입니다. Azure Blob 인덱서를 사용하여 검색된 문서의 경우 각 문서의 `content` 필드는 인덱서로 만든 표준 필드입니다. 

+ **‘outputs’** 는 기술 출력을 나타냅니다. 각 기술은 기술 세트에서 이름으로 참조되는 특정 종류의 출력을 내보내도록 설계되었습니다. 엔터티 인식의 경우 `"organizations"`는 지원되는 출력 중 하나입니다. 각 기술 설명서에서는 생성할 수 있는 출력을 설명합니다.

출력은 처리 동안만 존재합니다. 이 출력을 다운스트림 기술의 입력에 연결하려면 출력을 `"/document/orgs"`로 참조합니다. 검색 인덱스의 필드에 출력을 보내려면 인덱서에서 [출력 필드 매핑을 만듭니다](cognitive-search-output-field-mapping.md). 출력을 지식 저장소로 보내려면 [프로젝션을 만듭니다](knowledge-store-projection-overview.md).

한 기술의 출력이 다른 기술의 출력과 충돌할 수 있습니다. 동일한 출력을 반환하는 기술이 다수 있는 경우 `"targetName"`을 사용하여 보강 노드 경로에서 이름을 명확하게 밝힙니다.

일부 상황에서는 별도로 배열의 각 요소를 참조하기 위해 호출합니다. 예를 들어, `"/document/orgs"`의 *각 요소* 를 다른 기술에 개별적으로 전달하려고 한다고 가정해 보겠습니다. 이렇게 하려면 `"/document/orgs/*"` 경로에 별표를 추가합니다. 

두 번째 감정 분석 기술은 첫 번째 보강자와 동일한 패턴을 따릅니다. `"/document/content"`을 입력으로 사용하고 각 콘텐츠 인스턴스에 대해 감정 점수를 반환합니다. ‘context’ 필드를 명시적으로 설정하지 않았으므로 출력(mySentiment)은 `"/document"`의 자식입니다.

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
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

## <a name="adding-a-custom-skill"></a>사용자 지정 기술 추가

다음은 [사용자 지정 기술](cognitive-search-custom-skill-web-api.md)의 예입니다. URI는 Azure 함수를 가리키며, 사용자가 제공하는 모델 또는 변환을 호출합니다. 자세한 내용은 [사용자 지정 인터페이스 정의](cognitive-search-custom-skill-interface.md)를 참조하세요.

사용자 지정 기술은 파이프라인 외부에 있는 코드를 실행하지만 기술 배열에서는 또 다른 기술에 불과합니다. 기본 제공 기술처럼 형식과 컨텍스트, 입력, 출력을 포함합니다. 또한 기본 제공 기술과 마찬가지로 보강 트리를 읽고 씁니다. 별표를 사용하여 "컨텍스트" 필드가 `"/document/orgs/*"`로 설정되는 것은 보강 단계가 `"/document/orgs"`에서 *각 조직에 대해* 호출된다는 의미입니다.

회사 설명의 경우 출력은 식별된 각 조직에 대해 생성됩니다. 다운스트림 단계에서 설명을 언급할 때(예: 핵심 구문 추출) 그렇게 하려면 경로 `"/document/orgs/*/companyDescription"`을 사용합니다. 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>인덱스로 출력 보내기

각 기술이 실행될 때 해당 출력은 문서의 보강 트리에 노드로 추가됩니다. 보강 문서는 임시 데이터 구조로 파이프라인에 존재합니다. 영구 데이터 구조를 만들고 기술이 실제로 생성하는 항목을 완전히 파악하려면, 검색 인덱스 또는 [지식 저장소](knowledge-store-concept-intro.md)로 출력을 보내야 합니다.

기술 평가의 초기 단계에서 최소한의 노력을 들여 예비 결과를 확인하고자 할 것입니다. 설정하는 것이 더 간단하기 때문에 검색 인덱스를 사용하는 것이 좋습니다. 각 기술 출력의 인덱서에서 [출력 필드 매핑을 정의](cognitive-search-output-field-mapping.md)하고 인덱스에서 필드를 정의합니다.

:::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="사람 엔터티가 기술 출력, 인덱서 필드 매핑 및 인덱스 필드에 정의되는 방식을 보여 주는 개체 다이어그램.":::

인덱서를 실행한 후에는 [검색 탐색기](search-explorer.md)를 사용해 인덱스에서 문서를 반환하고 각 필드의 콘텐츠를 확인하여 기술 세트에서 검색하거나 만든 내용을 확인할 수 있습니다.

다음 예제에는 텍스트 청크에서 사람, 위치, 조직 및 다른 엔터티를 검색한 엔터티 인식 기술의 결과가 나와 있습니다. 검색 탐색기에서 결과를 확인하면 기술이 솔루션에 가치를 더하는지 여부를 파악하는 데 도움이 될 수 있습니다.

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="검색 탐색기의 문서 스크린샷.":::

## <a name="next-steps"></a>다음 단계

컨텍스트 및 입력 원본 필드는 보강 트리에 있는 노드의 경로입니다. 다음 단계로, 보강 트리에서 노드의 경로를 설정하는 구문에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [기술 세트의 주석 참조](cognitive-search-concept-annotations-syntax.md)
