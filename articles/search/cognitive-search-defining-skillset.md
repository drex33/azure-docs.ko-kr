---
title: 기술 집합 만들기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 사용을 위해 데이터에서 구조화된 정보를 추출하고 보강하기 위한 데이터 추출, 자연어 처리 또는 이미지 분석 단계입니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2ec7f9a874bff6eaa0e23f5fb926bf031f2b059d
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555974"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Azure Cognitive Search에서 AI 보강 파이프라인에 기술 세트를 만드는 방법 

![인덱서 단계](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "인덱서 단계")

기술 세트는 검색 가능하도록 데이터를 추출하고 보강하는 작업을 정의합니다. 기술 세트는 원본 문서에서 텍스트 및 이미지 콘텐츠가 추출된 후에 실행되고, 또한 원본 문서의 모든 필드가 인덱스 또는 지식 저장소의 대상 필드에 매핑된 후에 실행됩니다.

기술 세트에는 텍스트 번역, 핵심 구문 추출 또는 이미지 파일에서 광학 문자 인식 수행과 같은 특정 보강 작업을 나타내는 하나 이상의 *인식 기술* 이 포함됩니다. 기술 세트를 만들기 위해 Microsoft의 [기본 제공 기술](cognitive-search-predefined-skills.md)을 사용하거나 제공하는 모델 또는 처리 로직이 포함된 사용자 지정 기술을 사용할 수 있습니다(자세한 내용은 [예: AI 보강 파이프 라인에서 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md) 참조).

이 문서에서는 사용하려는 기술에 대한 보강 파이프라인을 만드는 방법을 알아 봅니다. 기술 세트는 Azure Cognitive Search [인덱서](search-indexer-overview.md)에 첨부되어 있습니다. 이 문서에서 다루는 파이프라인 디자인의 한 부분은 기술 집합 자체를 생성하는 것입니다. 

> [!NOTE]
> 파이프라인 디자인의 다른 부분은 인덱서를 지정하는 것으로 [다음 단계](#next-step)에서 다루게 됩니다. 인덱서 정의에는 대상 인덱스의 출력에 입력을 연결하는 데 사용되는 필드 매핑 외에 기술 집합에 대한 참조가 포함됩니다.

유념해야 할 주요 사항입니다.

+ 인덱서 당 하나의 기술 집합만 사용할 수 있습니다.
+ 기술 집합에는 최소 하나 이상의 기술이 있어야 합니다.
+ 동일한 형식의 여러 기술(예: 이미지 분석 기술의 변형)을 만들 수 있습니다.

## <a name="begin-with-the-end-in-mind"></a>종료로 시작

권장된 초기 단계는 검색 솔루션에서 해당 데이터를 사용하려는 방법 및 원시 데이터에서 추출할 데이터를 결정하는 것입니다. 전체 보강 파이프라인의 일러스트레이션을 만들면 필요한 단계를 확인할 수 있습니다.

재무 분석가 주석의 집합을 처리하는 데 관심이 있는 것으로 가정합니다. 각 파일의 경우 회사 이름 및 주석의 일반 감정을 추출하려 합니다. 회사가 참여하는 비즈니스 종류 등 회사에 대한 추가 정보를 찾는 데 Bing Entity Search 서비스를 사용하는 사용자 지정 보강자를 작성할 수도 있습니다. 특히 각 문서에 대해 인덱스된 다음과 같은 정보를 추출하려 합니다.

| 레코드 텍스트 | 기업 | 감정 | 회사 설명 |
|--------|-----|-----|-----|
|샘플 레코드| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation는 미국의 다국적 기술 회사...", "LinkedIn은 비즈니스 및 고용 지향 소셜 네트워킹..."]

다음 다이어그램에서는 가상 보강 파이프라인을 보여 줍니다.

![가상 보강 파이프라인](media/cognitive-search-defining-skillset/sample-skillset.png "가상 보강 파이프라인")


파이프라인에서 원하는 것에 대해 잘 알고 있으면 이러한 단계를 제공하는 기술 집합을 표현할 수 있습니다. 기능적으로 Azure Cognitive Search에 인덱서 정의를 업로드할 때 기술 세트를 표현합니다. 인덱서를 업로드하는 방법에 대해 자세히 알려면 [인덱서 설명서](/rest/api/searchservice/create-indexer)를 참조합니다.


다이어그램에서 *문서 해독* 단계는 자동으로 발생합니다. 기본적으로 Azure Cognitive Search는 잘 알려진 파일을 여는 방법을 알고 각 문서에서 추출된 텍스트를 포함하는 *콘텐츠* 필드를 만듭니다. 흰색 상자는 기본 제공된 보강자이며, 점으로 구분된 "Bing Entity Search" 상자는 만들고 있는 사용자 지정 보강자를 나타냅니다. 설명처럼 기술 집합은 세 가지 기술이 포함되어 있습니다.

## <a name="skillset-definition-in-rest"></a>REST의 기술 집합 정의

기술 집합은 기술의 배열로서 정의됩니다. 각 기술은 해당 입력의 원본 및 생성된 출력의 이름을 정의합니다. [기술 집합 REST API 만들기](/rest/api/searchservice/create-skillset)를 사용하여 이전 다이어그램에 해당하는 기술 집합을 정의할 수 있습니다. 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
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
  ]
}
```

## <a name="create-a-skillset"></a>기술 집합 만들기

기술 집합을 만드는 동안 기술 집합을 자체 문서화하는 설명을 제공할 수 있습니다. 설명은 선택 사항이지만 기술 집합의 용도를 추적하는 데 유용합니다. 기술 집합은 주석을 허용하지 않는 JSON 문서이므로 주석을 위한 `description` 요소를 사용해야 합니다.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

기술 집합의 다음 부분은 기술 배열입니다. 각 기술을 보강의 기본 형식으로 생각할 수 있습니다. 각 기술은 이 보강 파이프라인에서 작은 작업을 수행합니다. 각 기술은 입력(또는 입력의 집합)을 사용하고 일부 출력을 반환합니다. 다음 몇 개 섹션은 입력 및 출력 참조를 통해 기술을 함께 연결하여 기본 제공 기술 및 사용자 지정 기술을 지정하는 방법에 집중합니다. 입력은 원본 데이터 또는 다른 기술에서 올 수 있습니다. 출력은 검색 인덱스의 필드에 매핑되거나 다운스트림 기술에 대한 입력으로 사용될 수 있습니다.

## <a name="add-built-in-skills"></a>기본 제공 기술 추가

첫 번째 기술인 기본 제공 [엔터티 인식 기술](cognitive-search-skill-entity-recognition-v3.md)을 살펴보겠습니다.

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

* 모든 기본 제공 기술에는 `odata.type`, `input` 및 `output` 속성이 있습니다. 기술 관련 속성은 해당 기술에 적용 가능한 추가 정보를 제공합니다. 엔터티 인식의 경우 `categories`은 미리 학습된 모델이 인식할 수 있는 엔터티 형식의 고정 집합 중 한 엔터티입니다.

* 각 기술에는 ```"context"```이 있어야 합니다. 컨텍스트는 작업을 수행할 수준을 나타냅니다. 위의 기술에서 컨텍스트는 전체 문서로서 엔터티 인식 기술이 문서당 한 번씩 호출된다는 의미입니다. 출력은 또한 해당 수준에서 생성됩니다. 이 기술은 ```orgs```로 캡처되는 ```organizations```라는 속성을 반환합니다. 자세히 설명하자면 이제 ```"orgs"```가 ```"/document"```의 멤버로 추가됩니다. 다운스트림 기술에서 새로 만든 이 보강을 ```"/document/orgs"```로 참조할 수 있습니다.  ```"context"``` 필드가 명시적으로 설정되지 않은 경우 기본 컨텍스트는 문서입니다.

* 한 기술의 출력이 다른 기술의 출력과 충돌할 수 있습니다. ```result``` 속성을 반환하는 기술이 여러 개 있는 경우 기술 출력의 ```targetName``` 속성을 사용하여 기술에서 다른 속성으로 명명된 JSON 출력을 캡처할 수 있습니다.

* 기술에는 원본 입력이 ```"/document/content"```로 설정된 "텍스트"라는 하나의 입력이 있습니다. 기술(엔티티 인식)은 Azure Blob 인덱서에서 만든 표준 필드인 각 문서의 *콘텐츠* 필드에서 작동합니다. 

* 기술에는 ```orgs``` 속성에서 캡처된 ```"organizations"```라는 출력이 하나 있습니다. 출력은 처리 동안만 존재합니다. 이 출력을 다운스트림 기술의 입력에 연결하려면 출력을 ```"/document/orgs"```로 참조합니다.

* 특정 문서의 경우 ```"/document/orgs"```의 값은 텍스트에서 추출된 조직의 배열입니다. 예를 들면 다음과 같습니다.

  ```json
  ["Microsoft", "LinkedIn"]
  ```

일부 상황에서는 별도로 배열의 각 요소를 참조하기 위해 호출합니다. 예를 들어 ```"/document/orgs"```의 각 요소를 별도로 다른 기술(예: 사용자 지정 Bing Entity Search 보강자)에 전달하려 한다고 가정합니다. 경로에 별표를 추가하여 배열의 각 요소를 참조할 수 있습니다. ```"/document/orgs/*"``` 

감정 추출을 위한 두 번째 기술은 첫 번째 보강자와 동일한 패턴을 따릅니다. ```"/document/content"```을 입력으로 사용하고 각 콘텐츠 인스턴스에 대해 감정 점수를 반환합니다. ```"context"``` 필드를 명시적으로 설정하지 않았으므로 출력(mySentiment)은 ```"/document"```의 자식입니다.

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
    },
```

## <a name="add-a-custom-skill"></a>사용자 지정 기술 추가

사용자 지정 Bing Entity Search 보강자의 구조를 회수합니다.

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
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

이 정의는 보강 절차의 일부로 웹 API를 호출하는 [사용자 지정 기술](cognitive-search-custom-skill-web-api.md)입니다. 엔터티 인식에서 식별한 각 조직의 경우 이 기술은 웹 API가 해당 조직에 대한 설명을 찾도록 호출합니다. 웹 API를 호출할 때 및 받은 정보를 이동하는 방법의 오케스트레이션은 보강 엔진에서 내부적으로 처리합니다. 그러나 이 사용자 지정 API를 호출하는 데 필요한 초기화는 JSON(예: uri, httpHeaders 및 예상 입력)에서 제공되어야 합니다. 보강 파이프라인에 대한 사용자 지정 웹 API를 만드는 지침은 [사용자 지정 인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md)을 참조합니다.

별표를 사용하여 "컨텍스트" 필드가 ```"/document/orgs/*"```로 설정되는 것은 보강 단계가 ```"/document/orgs"```에서 *각 조직에 대해* 호출된다는 의미입니다. 

회사 설명의 경우 출력은 식별된 각 조직에 대해 생성됩니다. 다운스트림 단계에서 설명을 언급할 때(예: 핵심 구문 추출) 그렇게 하려면 경로 ```"/document/orgs/*/description"```을 사용합니다. 

## <a name="add-structure"></a>구조 추가

기술 집합은 구조화되지 않은 데이터에서 구조화된 정보를 생성합니다. 다음 예제를 참조하세요.

*"Microsoft는 작년에 인수한 소셜 네트워킹 회사인 LinkedIn에서 4분기 매출 11억 달러를 달성했습니다. 인수를 통해 Microsoft는 LinkedIn 기능과 해당 CRM 및 Office 기능을 결합할 수 있었습니다. 주주들은 지금까지의 진행 상황에 대해 기뻐하고 있습니다."*

가능성 있는 결과는 다음 그림과 유사하게 생성된 구조입니다.

![샘플 출력 구조](media/cognitive-search-defining-skillset/enriched-doc.png "샘플 출력 구조")

지금까지 이 구조는 내부 전용이자 메모리 전용이었으며 Azure Cognitive Search 인덱스에서만 사용되었습니다. 지식 저장소를 추가하면 검색 외부에서 사용하기 위해 보강된 문서를 저장할 수 있습니다.

## <a name="add-a-knowledge-store"></a>지식 저장소 추가

[지식 저장소](knowledge-store-concept-intro.md)는 보강 문서를 저장하기 위한 Azure Cognitive Search의 기능입니다. 사용자가 생성한 지식 저장소는 Azure 스토리지 계정으로 지원되며, 보강 데이터가 있는 리포지토리입니다. 

지식 저장소 정의가 기술 세트에 추가됩니다. 전체 프로세스에 대한 연습은 [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)를 참조하세요.

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Blob Storage에서 보강 문서를 계층 관계가 유지된 테이블로 저장하거나 JSON 문서로 저장하도록 선택할 수 있습니다. 기술 세트에 있는 모든 기술의 출력을 프로젝션에 대한 입력으로 사용할 수 있습니다. 데이터를 특정 쉐이프로 표시하려는 경우 이제 업데이트된 [쉐이퍼 기술](cognitive-search-skill-shaper.md)이 사용할 복합 형식을 모델링할 수 있습니다. 

<a name="next-step"></a>

## <a name="next-steps"></a>다음 단계

보강 파이프라인 및 기술 집합에 익숙하므로 [기술 집합에서 주석을 참조하는 방법](cognitive-search-concept-annotations-syntax.md) 또는 [ 인덱스에서 필드에 출력을 매핑하는 방법](cognitive-search-output-field-mapping.md)을 계속 사용합니다.