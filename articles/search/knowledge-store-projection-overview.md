---
title: 프로젝션 개념
titleSuffix: Azure Cognitive Search
description: AI 보강 인덱싱 파이프라인의 보강 데이터를 전체 텍스트 검색 이외의 시나리오에서 사용할 지식 저장소로 저장하고 도형을 저장합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 841cd106f1c54e1c35d3b2785eb942842d77f825
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706762"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소 "프로젝션"

프로젝션은 Azure Storage에서 데이터의 물리 식을 지정 하는 [기술 자료 저장소](knowledge-store-concept-intro.md) 정의의 요소입니다. 프로젝션 정의는 Azure Storage의 데이터 구조 수와 유형을 결정 합니다.

## <a name="types-of-data-structures"></a>데이터 구조 형식

기술 자료 저장소는 테이블, JSON 개체 또는 이진 이미지 파일 처럼 Azure Storage로 물리적으로 표현 되는 논리적 생성입니다.

| 프로젝션 | 스토리지 | 사용량 |
|------------|---------|-------|
| 테이블 | Azure Table Storage | 행과 열로 가장 잘 표현 되는 데이터에 사용 됩니다. 테이블 프로젝션을 통해 스키마 화 된 셰이프 또는 프로젝션을 정의할 수 있습니다. 유효한 JSON 개체만 테이블로 프로젝션할 수 있습니다. 보강된 문서에는 이름이 JSON 개체가 아닌 노드가 포함될 수 있으므로 [쉐이퍼 기술을 추가하거나 기술 내에서 인라인 셰이핑을 사용](knowledge-store-projection-shape.md)하여 유효한 JSON을 만듭니다. |
| 개체 | Azure Blob Storage | 데이터 및 강화의 JSON 표현이 필요할 때 사용 됩니다. 테이블 프로젝션과 마찬가지로 유효한 JSON 개체만 개체로 프로젝션될 수 있으며 셰이핑이 이를 수행하는 데 도움이 될 수 있습니다. |
| 파일 | Azure Blob Storage | 정규화 된 이진 이미지 파일을 저장 해야 하는 경우에 사용 됩니다. |

보강되는 데이터의 여러 프로젝션을 정의할 수 있습니다. 여러 프로젝션은 개별 사용 사례에 대해 동일한 데이터의 모양을 다르게 하려는 경우에 유용합니다.

## <a name="basic-definition"></a>기본 정의

프로젝션은 `knowledgeStore` [기술 개체](/rest/api/searchservice/create-skillset)의 정의 아래에 있는 복잡 한 컬렉션의 배열입니다. 

각 테이블, 개체 및 파일 집합은 *프로젝트 그룹* 이며 스토리지 요구 사항에 다양한 도구와 시나리오 지원이 포함된 경우 그룹이 여러 개 있을 수 있습니다. 단일 그룹 내에 테이블, 개체 및 파일이 여러 개 있을 수 있습니다. 

일반적으로 하나의 그룹만 사용 되지만 다음 예제에서는 여러 그룹의 아이디어를 보강 하는 두 가지 방법을 보여 줍니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

## <a name="data-isolation-and-relatedness"></a>데이터 격리 및 관련성

table-object-file 조합 집합이 여러 개 있으면 다양한 시나리오를 지원하는 데 유용합니다. 기술 세트의 디자인과 디버그에 집합 하나를 사용하여 추가 검사에 사용되는 출력을 캡처할 수 있습니다. 두 번째 집합은 온라인 앱에 사용되는 출력을 수집하고 세 번째 집합은 데이터 과학 워크로드용입니다.

프로젝션 그룹에는 상호 배타성 및 관련성의 다음과 같은 주요 특징이 있습니다. 

| 원칙 | Description |
|-----------|-------------|
| 상호 배제 | 단일 그룹으로 프로젝션된 모든 콘텐츠는 다른 프로젝션 그룹에 프로젝션된 데이터와 독립적입니다. 이러한 독립성 때문에 동일한 데이터의 모양이 달라지지만, 각 프로젝션 그룹에서 반복될 수 있습니다. 각 그룹은 동일한 원본(보강 트리)에서 데이터를 가져오지만 피어 프로젝션 그룹의 table-object-file 조합에서 완전히 격리됩니다.|
| 관련성 | 프로젝션은 그룹 내에서 관련성을 지원합니다. 그룹 내에서 테이블의 콘텐츠는 개체나 파일의 콘텐츠와 관련이 있습니다. 동일한 그룹의 형식(테이블, 개체 및 파일)에서 보강 트리의 단일 노드(예: `/document/translated_text`)가 서로 다른 테이블과 개체에 프로젝션되면 관계가 유지됩니다. 테이블 내에서 관계는 생성된 키를 기반으로 하며, 각 자식 노드는 부모 노드에 대한 참조를 유지합니다. 예를 들어 이미지와 텍스트가 포함된 문서가 있는 시나리오를 생각해 보겠습니다. 테이블 또는 개체에 텍스트를 프로젝팅하고, 이미지를 테이블 또는 개체에 파일 URL을 포함하는 열/속성이 있는 파일에 프로젝팅할 수 있습니다.|

추가 프로젝션 그룹을 만들기 위한 결정은 데이터 표현 요구 사항에 따라 결정되는 경우가 많습니다. 다른 데이터 관계를 원하는 경우 이 작업을 수행하면 됩니다. 세트 내에서 데이터는 관련되어 있으며, 이러한 관계가 존재하며 검색될 수 있다고 가정합니다. 추가 세트를 만드는 경우 각 그룹의 문서는 서로 관련이 없습니다. 여러 프로젝션 그룹을 사용하는 예는 동일한 데이터를 온라인 시스템에서 사용하기 위해 프로젝션하고 특정 방식으로 표시해야 하는 경우 다르게 표시되는 데이터 과학 파이프라인에서 사용하기 위해 동일한 데이터를 프로젝션할 때입니다.

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>테이블 프로젝션

테이블 프로젝션은 Power BI 분석과 같이 데이터 탐색을 호출하는 시나리오에 사용하는 것이 좋습니다. 테이블 정의는 프로젝트하려는 테이블의 목록입니다. 각 테이블에는 세 가지 속성이 필요합니다.

+ tableName: Azure Table Storage에 있는 테이블의 이름입니다.

+ generatedKeyName: 이 행을 고유하게 식별하는 키의 열 이름.

+ source: 보강을 소싱할 보강 트리의 노드입니다. 이 노드는 일반적으로 테이블 모양을 정의하는 쉐이퍼 기술의 출력입니다. 테이블에는 행과 열이 있으며 셰이핑은 행과 열을 지정하는 메커니즘입니다. [쉐이퍼 기술이나 인라인 도형](knowledge-store-projection-shape.md)을 사용할 수 있습니다. 쉐이퍼 기술은 유효한 JSON을 생성하지만 유효한 JSON인 경우 모든 기술의 출력일 수 있습니다. 

이 예제에서 설명한 것처럼 핵심 구와 엔터티는 서로 다른 테이블로 모델링되며 각 행에 대한 부모(MainTable)에 대한 참조를 포함합니다. 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

"원본"에 지정된 보강 노드를 조각화하여 여러 테이블로 프로젝션할 수 있습니다. "EnrichedShape"에 대한 참조는 쉐이퍼 기술의 출력입니다(표시되지 않음). 기술 입력은 테이블 구성과 이를 채우는 행을 결정합니다. 생성된 키와 각 테이블 내 공통 필드는 테이블 관계의 기초를 제공합니다.

## <a name="object-projections"></a>개체 프로젝션

개체 프로젝션은 모든 노드에서 원본으로 사용할 수 있는 보강 트리의 JSON 표현입니다. 대부분의 경우 테이블 프로젝션을 만드는 동일한 쉐이퍼 기술을 사용하여 개체 프로젝션을 생성할 수 있습니다. 

개체 프로젝션을 생성하려면 몇 가지 개체별 특성이 필요합니다.

+ storageContainer: 개체가 저장될 blob 컨테이너

+ source: 프로젝션의 루트인 보강 트리의 노드 경로.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>파일 프로젝션

파일 프로젝션은 `normalized_images` 컬렉션에서만 작동합니다. 하지만 base64로 인코딩된 문서 ID 값의 폴더 접두사를 통해 Blob 컨테이너에 저장된다는 점에서 개체 프로젝션과 유사합니다. 

파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으며, 다른 컨테이너에 프로젝션되어야 합니다. 파일 프로젝션 속성은 개체 프로젝션 속성과 동일합니다.

+ storageContainer: 개체가 저장될 blob 컨테이너

+ source: 프로젝션의 루트인 보강 트리의 노드 경로.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>프로젝션 셰이핑

프로젝션의 스키마와 일치하는 보강 트리에 개체가 있는 경우 프로젝션을 테이블이나 개체로 정의하는 것이 더 쉽습니다. 계획된 사용량에 따라 데이터를 셰이핑하거나 구조화할 수 있는 기능은 일반적으로 [쉐이퍼 기술](cognitive-search-skill-shaper.md)을 기술 세트에 추가하면 얻어집니다. 쉐이퍼 기술에서 생성된 도형은 프로젝션의 `source`로 사용되지만 다른 기술에 대한 입력으로 사용될 수도 있습니다.

간단히 말해 테이블 프로젝션에서 쉐이퍼 기술은 테이블의 열이나 필드를 결정합니다. 쉐이퍼 기술에 대한 입력은 보강 트리의 노드로 구성됩니다. 출력은 테이블 프로젝션에서 지정하는 구조체입니다. 다음 예에서 "mytableprojection"이라는 테이블 프로젝션은 쉐이퍼 기술에서 지정한 바와 같이 입력으로 구성됩니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
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
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

쉐이퍼 기술을 사용하면 보강 트리의 여러 노드에서 개체를 작성하고 새 노드 아래에 부모를 지정할 수 있습니다. 쉐이퍼 기술을 사용하면 중첩된 개체를 사용하여 복합 형식을 정의할 수 있습니다. 예는 [쉐이퍼 기술](cognitive-search-skill-shaper.md) 문서를 참조하세요.

## <a name="projection-slicing"></a>프로젝션 조각화

테이블 프로젝션 그룹 내에서 보강 트리의 단일 노드를 여러 관련 테이블로 조각화하여 각 테이블에 데이터 범주를 포함할 수 있습니다. 이는 관련 데이터가 집계되는지 여부와 집계 방법을 제어할 수 있는 분석에 유용할 수 있습니다.

자식 테이블을 여러 개 만들려면 부모 테이블로 시작한 다음, 부모의 원본을 빌드하는 추가 테이블을 만듭니다. 이 예에서 "KeyPhrases" 및 "Entities"는 "/document/EnrichedShape"의 조각을 사용합니다.

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

여러 테이블로 프로젝션하는 경우, 자식 노드가 동일한 그룹 내에 있는 다른 테이블의 원본이 아니면 전체 셰이프가 각 테이블에 프로젝션됩니다. 기존 프로젝션의 자식인 원본 경로를 사용하여 프로젝션을 추가하면 자식 노드가 부모 노드에서 분리되고, 새로운 관련 테이블이나 개체로 프로젝션됩니다. 이 기법을 사용하면 모든 프로젝션의 원본이 될 수 있는 단일 노드를 쉐이퍼 기술에 정의할 수 있습니다.

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

프로젝션에는 데이터 원본의 원본 데이터에 연결된 수명 주기가 있습니다. 원본 데이터가 업데이트되고 다시 인덱싱되면 프로젝션이 보강 결과로 업데이트되므로 프로젝션이 결국 데이터 원본의 데이터와 일치합니다. 그러나 프로젝션도 Azure Storage에 독립적으로 저장됩니다. 인덱서나 검색 서비스 자체가 삭제되면 프로젝션은 삭제되지 않습니다. 

## <a name="using-projections"></a>프로젝션 사용

인덱서를 실행한 후에는 프로젝션을 통해 지정한 컨테이너 또는 테이블에서 프로젝션된 데이터를 읽을 수 있습니다.

분석의 경우 Power BI의 탐색은 Azure Table Storage를 데이터 원본으로 설정하는 것처럼 간단합니다. 내에서 관계를 사용하여 데이터에 대한 시각화 집합을 쉽게 만들 수 있습니다.

또는 데이터 과학 파이프라인에서 보강 데이터를 사용해야 하는 경우 [blob의 데이터를 Pandas DataFrame에 로드할](/azure/architecture/data-science-process/explore-data-blob) 수 있습니다.

마지막으로 지식 저장소에서 데이터를 내보내야 하는 경우, Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치하는 커넥터가 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 단계로 샘플 데이터 및 지침을 사용하여 첫 번째 지식 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)

조각화, 인라인 셰이핑 및 관계와 같은 고급 개념에 대한 자세한 내용은 [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)를 참조하세요.
