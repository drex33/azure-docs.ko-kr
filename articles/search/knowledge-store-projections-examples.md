---
title: 프로젝션 정의
titleSuffix: Azure Cognitive Search
description: 구문 및 예제를 검토 하 여 기술 자료 저장소에서 테이블, 개체 및 파일 프로젝션을 정의 하는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 2b4e5232622b2e79d8277f0e7388da0e81b5ee82
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070796"
---
# <a name="define-projections-in-a-knowledge-store"></a>지식 저장소에서 프로젝션 정의

[프로젝션](knowledge-store-projection-overview.md) 은 AI 보강 콘텐츠가 Azure Storage에 저장 되는 방법을 결정 하는 [기술 자료 저장소 정의](knowledge-store-concept-intro.md) 의 구성 요소입니다. 프로젝션은 콘텐츠를 포함 하는 데이터 구조의 유형, 수량 및 컴퍼지션을 결정 합니다.

이 문서에서는 각 프로젝션 형식에 대 한 구문에 대해 알아봅니다.

+ [테이블 프로젝션](#define-a-table-projection)
+ [개체 프로젝션](#define-an-object-projection)
+ [파일 프로젝션](#define-a-file-projection)

투영은 기술의 "knowledgeStore" 속성 아래에 정의 되어 있습니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [ ],
        "objects": [ ],
        "files": [ ]
      }
    ]
```

시작 하기 전에 더 많은 배경이 필요한 경우 [이 검사 목록](knowledge-store-projection-overview.md#checklist-for-getting-started) 에서 팁과 워크플로를 검토 합니다.

> [!TIP]
> 프로젝션을 개발할 때 프로젝션 정의를 편집 하는 동안 기존 강화를 다시 사용할 수 있도록 [보강 캐싱을 사용 하도록 설정](search-howto-incremental-index.md) 합니다. 캐싱을 사용 하지 않을 경우 프로젝션을 간단 하 게 편집 하면 보강 콘텐츠가 완전히 다시 처리 됩니다. 강화을 캐싱하여 기술 처리 요금을 발생 시 키 지 않고 프로젝션을 반복할 수 있습니다.

## <a name="requirements"></a>요구 사항

모든 프로젝션에는 원본 및 대상 속성이 있습니다. 원본은 항상 기술 실행 중에 생성 된 보강 트리의 콘텐츠입니다. 대상은 Azure Storage에서 만들어지고 로드 되는 개체의 이름과 형식입니다.

이진 이미지만 허용 하는 파일 프로젝션을 제외 하 고 원본은 다음과 같아야 합니다.

+ 유효한 JSON
+ 보강 트리의 노드 경로 (예: `"source": "/document/objectprojection"` )

노드가 단일 필드로 확인 될 수 있지만 보다 일반적인 표현은 복합 도형에 대 한 참조입니다. 복합 도형은 [Shaper 기술](cognitive-search-skill-shaper.md) 또는 [인라인 셰이핑 정의](knowledge-store-projection-shape.md#inline-shape)를 통해 모양 지정 방법을 통해 생성 되지만 일반적으로 Shaper 기술입니다.

Shaper 기술은 대부분의 기술이 자체적으로 유효한 JSON을 출력 하지 않기 때문에 선호 됩니다. 대부분의 경우 Shaper 기술로 만든 동일한 데이터 셰이프를 테이블 및 개체 프로젝션 모두에서 동일 하 게 사용할 수 있습니다.

원본 입력 요구 사항이 지정 된 경우 특히 테이블 작업을 수행 하는 경우 [데이터 모양을](knowledge-store-projection-shape.md) 지정 하는 것은 프로젝션 정의에 대 한 실용적인 요구 사항이 됩니다.

## <a name="define-a-table-projection"></a>테이블 프로젝션 정의

데이터 프레임을 사용 하는 Power BI 또는 워크 로드를 사용한 분석과 같이 데이터 탐색을 위해를 호출 하는 시나리오의 경우 테이블 프로젝션을 사용 하는 것이 좋습니다. 프로젝션 배열의 테이블 섹션은 프로젝션 하려는 테이블의 목록입니다.

테이블 프로젝션을 정의 하려면 `tables` 프로젝션 속성에서 배열을 사용 합니다. 테이블 프로젝션에는 다음과 같은 세 가지 필수 속성이 있습니다.

| 속성 | 설명 |
|----------|-------------|
| tableName | Azure Table Storage에서 생성 되는 새 테이블의 이름을 결정 합니다.  |
| generatedKeyName | 각 행을 고유 하 게 식별 하는 키의 열 이름입니다. 값은 시스템에서 생성 됩니다. 이 속성을 생략하면 테이블 이름과 ‘키’를 명명 규칙으로 사용하는 열이 자동으로 만들어집니다. |
| source | 보강 트리의 노드에 대한 경로입니다. 노드는 테이블에 생성 되는 열을 결정 하는 복합 도형에 대 한 참조 여야 합니다.|

테이블 프로젝션에서 "source"는 일반적으로 테이블의 모양을 정의 하는 [Shaper 기술](cognitive-search-skill-shaper.md) 에 대 한 출력입니다. 테이블에는 행과 열이 있으며 셰이핑은 행과 열을 지정하는 메커니즘입니다. [쉐이퍼 기술이나 인라인 도형](knowledge-store-projection-shape.md)을 사용할 수 있습니다. Shaper 기술은 유효한 JSON을 생성 하지만 유효한 JSON 인 경우 소스는 모든 기술에서 출력 될 수 있습니다.

> [!NOTE]
> 테이블 프로젝션에는 Azure Storage에 의해 적용 되는 [저장소 제한이](/rest/api/storageservices/understanding-the-table-service-data-model) 적용 됩니다. 엔터티 크기는 1mb를 초과할 수 없으며 단일 속성은 64 KB 보다 클 수 없습니다. 이러한 제약 조건으로 인해 테이블은 많은 수의 작은 엔터티를 저장하는 데 좋은 솔루션이 됩니다.

### <a name="single-table-example"></a>단일 테이블 예제

테이블의 스키마는 부분적으로 투영 (테이블 이름 및 키) 및 테이블 (열) 셰이프를 제공 하는 원본에 의해 지정 됩니다. 이 예에서는 정의 세부 정보에 집중할 수 있도록 하나의 테이블을 보여 줍니다.

```json
"projections" : [
  {
    "tables": [
      { "tableName": "Hotels", "generatedKeyName": "HotelId", "source": "/document/tableprojection" }
    ]
  }
]
```

열은 "source"에서 파생 됩니다. HotelId, Ho Name, Category 및 Description을 포함 하는 다음 데이터 셰이프는 테이블에 이러한 열을 만듭니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#3",
    "description": null,
    "context": "/document",
    "inputs": [
    {
        "name": "HotelId",
        "source": "/document/HotelId"
    },
    {
        "name": "HotelName",
        "source": "/document/HotelName"
    },
    {
        "name": "Category",
        "source": "/document/Category"
    },
    {
        "name": "Description",
        "source": "/document/Description"
    },
    ],
    "outputs": [
    {
        "name": "output",
        "targetName": "tableprojection"
    }
    ]
}
```

### <a name="multiple-table-slicing-example"></a>다중 테이블 (조각화) 예제

테이블 프로젝션의 일반적인 패턴은 여러 관련 테이블을 포함 하는 것입니다. 여기서 시스템 생성 partitionKey 및 rowKey 열은 동일한 프로젝션 그룹의 모든 테이블에 대 한 교차 테이블 관계를 지원 하기 위해 생성 됩니다. 

관련 데이터가 집계 되는 방식을 제어 하려는 경우 여러 테이블을 만드는 것이 유용할 수 있습니다. 보강 콘텐츠에 관련 되지 않은 또는 독립적인 구성 요소가 있는 경우, 예를 들어 문서에서 추출 된 키워드는 동일한 문서에서 인식 되는 엔터티와 관련이 없을 수 있습니다. 이러한 필드는 인접 한 테이블로 분할할 수 있습니다.

여러 테이블로 프로젝션 하는 경우 자식 노드가 동일한 그룹 내에 있는 다른 테이블의 원본이 아니면 전체 셰이프를 각 테이블에 프로젝션 합니다. 기존 프로젝션의 자식인 원본 경로를 사용 하 여 프로젝션을 추가 하면 자식 노드가 부모 노드에서 분리 되 고 새 관련 테이블에 프로젝션 됩니다. 이 기법을 사용하면 모든 프로젝션의 원본이 될 수 있는 단일 노드를 쉐이퍼 기술에 정의할 수 있습니다.

여러 테이블의 패턴은 다음과 같이 구성 됩니다.

+ 부모 또는 주 테이블로 한 테이블
+ 보강 콘텐츠의 조각을 포함 하는 추가 테이블

예를 들어 Shaper 기술이 호텔 정보를 포함 하는 "EnrichedShape"을 출력 하 고 핵심 구, 위치 및 조직과 같은 보강 콘텐츠를 출력 한다고 가정 합니다. 주 테이블에는 호텔 (ID, 이름, 설명, 주소, 범주)을 설명 하는 필드가 포함 됩니다. 키 구는 키 구 열을 가져옵니다. 엔터티는 엔터티 열을 가져옵니다.

```json
"projections" : [
  {
    "tables": [
    { "tableName": "MainTable", "generatedKeyName": "HotelId", "source": "/document/EnrichedShape" },
    { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
    { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
    ]
  }
]
```

### <a name="naming-relationships"></a>관계 이름 지정

`generatedKeyName`및 `referenceKeyName` 속성은 여러 테이블 또는 프로젝션 형식 간에 데이터를 연관하는 데 사용됩니다. 자식 테이블/프로젝션의 각 행에는 부모를 가리키는 속성이 있습니다. 자식의 열 또는 속성 이름은 부모에서 `referenceKeyName`입니다. `referenceKeyName`이 제공되지 않은 경우 서비스에서 기본값을 부모의 `generatedKeyName`으로 지정합니다. 

Power BI는 이러한 생성된 키를 사용하여 테이블 내의 관계를 검색합니다. 자식 테이블의 열이 다른 이름으로 지정 되어야 하는 경우 부모 테이블에서 `referenceKeyName` 속성을 설정합니다. 한 가지 예를 들면 `generatedKeyName`을 pbiDocument 테이블의 ID로 설정하고 `referenceKeyName`을 DocumentID로 설정하는 것입니다. 그러면 문서 ID가 포함된 pbiEntities 및 pbiKeyPhrases 테이블의 열이 DocumentID로 명명됩니다.

## <a name="define-an-object-projection"></a>개체 프로젝션 정의

개체 프로젝션은 모든 노드에서 원본으로 사용할 수 있는 보강 트리의 JSON 표현입니다. 테이블 프로젝션과 비교 하 여 개체 프로젝션을 정의 하는 것이 더 간단 하며 전체 문서를 프로젝션 할 때 사용 됩니다. 개체 프로젝션은 컨테이너의 단일 프로젝션으로 제한되며 조각화할 수 없습니다.

개체 프로젝션을 정의 하려면 `objects` 프로젝션 속성에서 배열을 사용 합니다. 개체 프로젝션에는 다음과 같은 세 가지 필수 속성이 있습니다.

| 속성 | Description |
|----------|-------------|
| storageContainer | Azure Storage에서 만든 새 컨테이너의 이름을 결정 합니다.  |
| generatedKeyName | 각 행을 고유 하 게 식별 하는 키의 열 이름입니다. 값은 시스템에서 생성 됩니다. 이 속성을 생략하면 테이블 이름과 ‘키’를 명명 규칙으로 사용하는 열이 자동으로 만들어집니다. |
| 원본 | 보강 트리에서 프로젝션의 루트인 노드에 대 한 경로입니다. 노드는 일반적으로 blob 구조를 결정 하는 복잡 한 데이터 셰이프에 대 한 참조입니다.|

다음 예제에서는 개별 호텔 문서, blob 당 호텔 문서 하나를 이라는 컨테이너에 투영 합니다 `hotels` .

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
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

원본은 "objectprojection" 이라는 Shaper 기술 출력입니다. 각 blob에는 각 필드 입력의 JSON 표현이 있습니다.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="define-a-file-projection"></a>파일 프로젝션 정의

파일 프로젝션은 항상 이진, 정규화 된 이미지 이며, 정규화를 사용 하면 기술 실행에서 사용 하기 위한 잠재적 크기 조정 및 회전이 가능 합니다. 개체 프로젝션과 비슷한 파일 프로젝션은 Azure Storage blob으로 생성 되며 JSON이 아닌 이진 데이터를 포함 합니다.

파일 프로젝션을 정의하려면 `files` 프로젝션 속성에 배열을 사용합니다. 파일 프로젝션에는 세 가지 필수 속성이 있습니다.

| 속성 | Description |
|----------|-------------|
| storageContainer | Azure Storage 만든 새 컨테이너의 이름을 결정합니다.  |
| generatedKeyName | 각 행을 고유하게 식별하는 키의 열 이름입니다. 값은 시스템에서 생성됩니다. 이 속성을 생략하면 테이블 이름과 ‘키’를 명명 규칙으로 사용하는 열이 자동으로 만들어집니다. |
| 원본 | 프로젝션의 루트인 보강 트리의 노드에 대한 경로입니다. 이미지 파일의 경우 원본은 항상 `/document/normalized_images/*` 입니다.  파일 프로젝션은 `normalized_images` 컬렉션에서만 작동합니다. 인덱서와 기술셋은 모두 정규화되지 않은 원래 이미지를 통과하지 않습니다.|

대상은 항상 문서 ID의 base64로 인코딩된 값의 폴더 접두사로 Blob 컨테이너입니다. 여러 이미지가 있는 경우 동일한 폴더에 함께 배치됩니다. 파일 프로젝션은 개체 프로젝션과 동일한 컨테이너를 공유할 수 없으며, 다른 컨테이너에 프로젝션되어야 합니다. 

다음 예제에서는 보강된 문서의 문서 노드에서 추출된 모든 정규화된 이미지를 라는 컨테이너에 `myImages` 투영합니다.

```json
"projections": [
    {
        "tables": [ ],
        "objects": [ ],
        "files": [
            {
                "storageContainer": "myImages",
                "source": "/document/normalized_images/*"
            }
        ]
    }
]
```

## <a name="test-projections"></a>프로젝션 테스트

다음 단계에 따라 프로젝션을 처리할 수 있습니다.

1. 지식 저장소의 `storageConnectionString` 속성을 유효한 V2 범용 저장소 계정 연결 문자열로 설정합니다.  

1. [기술체 본문에](/rest/api/searchservice/update-skillset) 프로젝션 정의가 있는 PUT 요청을 발급하여 기술체를 업데이트합니다.

1. [인덱서 를 실행하여](/rest/api/searchservice/run-indexer) 기술 셋을 실행합니다. 

1. [인덱서 실행을 모니터링하여](search-howto-monitor-indexers.md) 진행 상황을 확인하고 오류를 catch합니다.

1. [Storage Explorer 사용하여 Azure Storage](knowledge-store-view-storage-explorer.md) 개체 생성을 확인합니다.

1. 테이블을 프로젝션하는 경우 테이블 조작 및 시각화를 위해 [Power BI](knowledge-store-connect-power-bi.md) 가져옵니다. 대부분의 경우 Power BI 테이블 간의 관계를 자동으로 검색합니다.

## <a name="common-issues"></a>일반적인 문제

다음 단계를 생략하면 예기치 않은 결과가 발생할 수 있습니다. 출력이 제대로 보이지 않으면 다음 조건을 확인합니다.

+ 문자열 보강은 유효한 JSON으로 셰이이프되지 않습니다. 예를 들어 키 구로 보강된 `merged_content` 문자열이 보강되면 보강된 속성은 보강 트리 내에서 `merged_content`의 자식으로 표시됩니다. 기본 표현은 잘 구성된 JSON이 아닙니다. 프로젝션 시 이름 및 값을 사용하여 보강을 유효한 JSON 개체로 변환해야 합니다. 쉐이퍼 기술을 사용하거나 인라인 셰이프를 정의하면 이 문제를 해결하는 데 도움이 됩니다.

+ `/*`원본 경로의 끝에서 의 생략입니다. 프로젝션의 원본이 `/document/projectionShape/keyPhrases`인 경우 키 구 배열은 단일 개체/행으로 프로젝션됩니다. 대신의 원본 경로를 `/document/projectionShape/keyPhrases/*`로 설정하여 각 키 구에 대해 단일 행 또는 개체를 생성합니다.

+ 경로 구문 오류입니다. [경로 선택기는](cognitive-search-concept-annotations-syntax.md) 대/소문자를 구분하며 선택기에 정확한 대/소문자를 사용하지 않으면 입력 경고가 누락됩니다. 

## <a name="next-steps"></a>다음 단계

다음 단계에서는 풍부한 기술에서 출력의 셰이핑 및 프로젝션을 안내합니다. 기술 기술이 복잡한 경우 다음 문서에서는 도형과 프로젝션의 예를 모두 제공합니다.

> [!div class="nextstepaction"]
> [도형 및 프로젝션의 자세한 예제](knowledge-store-projection-example-long.md)
