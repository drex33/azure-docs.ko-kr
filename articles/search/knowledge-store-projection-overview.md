---
title: 프로젝션 개념
titleSuffix: Azure Cognitive Search
description: 프로젝션 개념과 모범 사례를 소개 합니다. Cognitive Search에서 기술 자료 저장소를 만드는 경우 프로젝션은 Azure Storage 개체의 유형, 수량 및 컴퍼지션을 결정 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: e6df23e1f63f358d0b9083f93b46abf1f0cf82b6
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066180"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소 "프로젝션"

프로젝션은 Cognitive Search AI 보강 파이프라인의 콘텐츠를 허용 하는 [**기술 자료 저장소**](knowledge-store-concept-intro.md) 의 물리적 테이블, 개체 및 파일입니다. 기술 자료 저장소를 만드는 경우 예측을 정의 하 고 셰이핑 하는 작업은 대부분입니다.

이 문서에서는 코딩을 시작 하기 전에 약간의 배경 지식이 있도록 프로젝션 개념과 워크플로를 소개 합니다.

투영은 Cognitive Search 기술력과에서 정의 되지만 최종 결과는 Azure Storage의 테이블, 개체 및 이미지 파일 프로젝션입니다.

:::image type="content" source="media/knowledge-store-concept-intro/projections-azure-storage.png" alt-text="Azure Storage로 표현 된 프로젝션" border="true":::

## <a name="types-of-projections-and-usage"></a>프로젝션 및 사용의 유형

기술 자료 저장소는 Azure Storage에서 테이블, JSON 개체 또는 이진 이미지 파일의 느슨한 컬렉션으로 물리적으로 표현 되는 논리적 생성입니다.

| 프로젝션 | 스토리지 | 사용량 |
|------------|---------|-------|
| [테이블](knowledge-store-projections-examples.md#define-a-table-projection) | Azure Table Storage | 행과 열로 가장 잘 표현 되는 데이터 또는 데이터의 세부적인 표현 (예: 데이터 프레임)이 필요할 때마다 사용 됩니다. 테이블 프로젝션을 사용 하면 Shaper 기술을 사용 하 여 스키마 화 된 셰이프를 정의 [하거나 인라인 셰이핑을 사용](knowledge-store-projection-shape.md) 하 여 열과 행을 지정할 수 있습니다. 친숙 한 정규화 원칙에 따라 여러 테이블로 콘텐츠를 구성할 수 있습니다. 동일한 그룹에 있는 테이블은 자동으로 관련 됩니다. |
| [개체](knowledge-store-projections-examples.md#define-an-object-projection) | Azure Blob Storage | 단일 JSON 문서에서 데이터 및 강화의 전체 JSON 표현을 필요로 할 때 사용 됩니다. 테이블 프로젝션과 마찬가지로 유효한 JSON 개체만 개체로 프로젝션될 수 있으며 셰이핑이 이를 수행하는 데 도움이 될 수 있습니다. |
| [파일](knowledge-store-projections-examples.md#define-a-file-projection) | Azure Blob Storage | 정규화 된 이진 이미지 파일을 저장 해야 하는 경우에 사용 됩니다. |

## <a name="projection-definition"></a>프로젝션 정의

투영은 [기술](/rest/api/searchservice/create-skillset)의 "knowledgeStore" 속성 아래에 지정 됩니다. 보강 콘텐츠를 사용 하 여 Azure Storage에서 개체를 만들고 로드 하기 위해 인덱서를 호출 하는 동안 프로젝션 정의가 사용 됩니다. 이러한 개념을 잘 모르는 경우에는 [AI 보강](cognitive-search-concept-intro.md) 부터 소개 하세요.

다음 예제에서는 knowledgeStore 아래의 프로젝션 배치 및 기본 생성을 보여 줍니다. 이름, 유형 및 콘텐츠 원본은 프로젝션 정의를 구성 합니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
          { "tableName": "ks-museumsEntities", "generatedKeyName": "Entitiesid","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "museumsKey", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>프로젝션 그룹

프로젝션은 복합 컬렉션의 배열입니다. 즉, 각 형식의 집합을 여러 개 지정할 수 있습니다. 하나의 프로젝션 그룹만 사용 하는 것이 일반적 이지만 저장소 요구 사항에 다른 도구 및 시나리오를 지 원하는 경우 여러 가지를 사용할 수 있습니다. 예를 들어 기술의 디자인 및 디버그에 그룹 하나를 사용할 수 있으며, 두 번째 집합은 온라인 앱에 사용 되는 출력을 수집 하 고 데이터 과학 워크 로드의 경우 세 번째 집합을 수집 합니다.

투영 된 모든 그룹을 채우는 데 동일한 기술 출력이 사용 됩니다. 다음 예제에서는 두 개의 프로젝션 그룹을 보여 줍니다.

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

테이블, 개체 및 파일의 각 집합은 다른 그룹의 도형과 격리 되는 *프로젝트 그룹* 입니다. 프로젝션 그룹에는 상호 배타성 및 관련성의 다음과 같은 주요 특징이 있습니다. 

| 원칙 | Description |
|-----------|-------------|
| 상호 배제 | 각 그룹은 다른 데이터 셰이핑 시나리오를 지원 하기 위해 다른 그룹에서 완전히 격리 됩니다. 예를 들어 서로 다른 테이블 구조와 조합을 테스트 하는 경우 AB 테스트를 위해 각 집합을 다른 프로젝션 그룹에 배치 합니다. 각 그룹은 동일한 원본(보강 트리)에서 데이터를 가져오지만 피어 프로젝션 그룹의 table-object-file 조합에서 완전히 격리됩니다.|
| 관련성 | 프로젝션 그룹 내에서 테이블, 개체 및 파일의 내용이 관련 됩니다. 기술 자료 저장소는 생성 된 키를 공통 부모 노드에 대 한 참조 점으로 사용 합니다. 예를 들어 이미지와 텍스트가 포함된 문서가 있는 시나리오를 생각해 보겠습니다. 텍스트를 테이블에 프로젝션 하 고 이미지를 이진 파일로 프로젝션 할 수 있으며, 테이블과 개체 모두에는 파일 URL이 포함 된 열/속성이 있습니다.|

## <a name="shaping-projections"></a>프로젝션 셰이핑

데이터 셰이프는 프로젝션에 "source" 라는 콘텐츠로 전달 되는 구조체입니다. 셰이핑은 테이블 프로젝션 및 개체 프로젝션에 적용 되며 각 항목에 포함 된 항목의 컴퍼지션을 결정 합니다.

프로젝션에 대 한 스키마를 제공 하는 보강 트리에 개체가 있는 경우이를 정의 하는 것이 더 쉽습니다. 계획 된 사용량을 기준으로 데이터를 구조화 하는 기능은 일반적으로 기술에 [Shaper 기술을](cognitive-search-skill-shaper.md) 추가 하 여 얻을 수 있습니다. 쉐이퍼 기술에서 생성된 도형은 프로젝션의 `source`로 사용되지만 다른 기술에 대한 입력으로 사용될 수도 있습니다. 다른 대안은의 기술에 인라인 셰이핑을 추가 하는 것입니다.

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

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

프로젝션에는 데이터 원본의 원본 데이터에 연결된 수명 주기가 있습니다. 원본 데이터가 업데이트되고 다시 인덱싱되면 프로젝션이 보강 결과로 업데이트되므로 프로젝션이 결국 데이터 원본의 데이터와 일치합니다. 그러나 프로젝션도 Azure Storage에 독립적으로 저장됩니다. 인덱서나 검색 서비스 자체가 삭제되면 프로젝션은 삭제되지 않습니다. 

## <a name="consume-in-apps"></a>앱에서 사용

인덱서를 실행 한 후 프로젝션에 연결 하 고 다른 앱 및 워크 로드의 데이터를 사용 합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) 를 사용 하 여 개체 생성 및 콘텐츠를 확인 합니다.

+ [데이터 탐색을 위해 Power BI를](knowledge-store-connect-power-bi.md)사용 합니다. 이 도구는 데이터가 Azure Table Storage에 있는 경우에 가장 잘 작동 합니다. Power BI 내에서 쿼리 및 분석 하기가 더 쉬운 새 테이블로 데이터를 조작할 수 있습니다.

+ 데이터 과학 파이프라인의 blob 컨테이너에서 보강 데이터를 사용 합니다. 예를 들어 [blob의 데이터를 Pandas 데이터 프레임에 로드할](/azure/architecture/data-science-process/explore-data-blob)수 있습니다.

+ 마지막으로 지식 저장소에서 데이터를 내보내야 하는 경우, Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치하는 커넥터가 있습니다.

## <a name="checklist-for-getting-started"></a>시작 하기 위한 검사 목록

예측은 기술 자료 저장소에만 국한 되며 검색 인덱스를 구조화 하는 데 사용 되지 않습니다.

1. Azure Storage에서 **액세스 키** 에서 연결 문자열을 가져오고 계정이 StorageV2 (범용 V2) 인지 확인 합니다.

1. Azure Storage 하는 동안에는 예측에 대해 충돌 하지 않는 이름을 선택할 수 있도록 컨테이너 및 테이블의 기존 콘텐츠를 숙지 합니다. 기술 자료 저장소는 테이블 및 컨테이너의 느슨한 컬렉션입니다. 명명 규칙을 채택 하 여 관련 개체를 추적 하는 것이 좋습니다.

1. Cognitive Search에서 인덱서에 [보강 캐싱을 사용 하도록 설정](search-howto-incremental-index.md) 하 고 [인덱서를 실행](search-howto-run-reset-indexers.md) 하 여 기술를 실행 하 고 캐시를 채웁니다. 캐시가 채워지면 기술이 수정 되지 않는 한 기술 자료 저장소에서 프로젝션 정의를 무료로 수정할 수 있습니다.

1. 코드에서 모든 프로젝션이 기술에만 정의 됩니다. 프로젝션에 적용 되는 인덱서 속성 (예: 필드 매핑 또는 출력 필드 매핑)이 없습니다. 기술 정의 내에서 knowledgeStore 속성 및 기술 배열 이라는 두 가지 영역에 초점을 맞출 수 있습니다.

   1. KnowledgeStore 아래에서 섹션에 테이블, 개체, 파일 프로젝션을 지정 `projections` 합니다. 개체 유형, 개체 이름 및 수량 (사용자가 정의한 프로젝션 수 별)은이 섹션에서 결정 됩니다.

   1. Skills 배열에서 각 프로젝션의에서 참조 되는 기술 출력을 결정 `source` 합니다. 모든 프로젝션에는 원본이 있습니다. 원본은 업스트림 기술에 대 한 출력이 될 수 있지만 종종 Shaper 기술에 대 한 출력입니다. 투영의 컴포지션은 셰이프를 통해 결정 됩니다. 

1. 기존 기술에 프로젝션을 추가 하는 경우 기술를 [업데이트](/rest/api/searchservice/update-skillset) 하 고 [인덱서를 실행](/rest/api/searchservice/run-indexer)합니다.

1. Azure Storage의 결과를 확인 합니다. 후속 실행 시 Azure Storage에서 개체를 삭제 하거나 기술에서 프로젝트 이름을 변경 하 여 이름 충돌을 방지 합니다.

## <a name="next-steps"></a>다음 단계

각 프로젝션 형식에 대한 구문 및 예제를 검토합니다.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)