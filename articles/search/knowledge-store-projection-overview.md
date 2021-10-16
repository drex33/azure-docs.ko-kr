---
title: 프로젝션 개념
titleSuffix: Azure Cognitive Search
description: 프로젝션 개념 및 모범 사례를 소개합니다. Cognitive Search 지식 저장소를 만드는 경우 프로젝션에 따라 Azure Storage 개체의 형식, 수량 및 컴퍼지션이 결정됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: e61538009316c1eb0f3363734d6196f4aa9720a8
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074383"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소 "프로젝션"

프로젝션은 Cognitive Search AI 보강 파이프라인의 콘텐츠를 수락하는 [**지식 저장소의**](knowledge-store-concept-intro.md) 물리적 테이블, 개체 및 파일입니다. 지식 저장소를 만드는 경우 프로젝션을 정의하고 셰이핑하는 것이 대부분의 작업입니다.

이 문서에서는 코딩을 시작하기 전에 배경 지식이 있도록 프로젝션 개념 및 워크플로를 소개합니다.

프로젝션은 Cognitive Search 기술 세트에 정의되지만 최종 결과는 Azure Storage 테이블, 개체 및 이미지 파일 프로젝션입니다.

:::image type="content" source="media/knowledge-store-concept-intro/projections-azure-storage.png" alt-text="Azure Storage 표현된 프로젝션" border="true":::

## <a name="types-of-projections-and-usage"></a>프로젝션 및 사용 유형

지식 저장소는 Azure Storage 테이블, JSON 개체 또는 이진 이미지 파일의 느슨한 컬렉션으로 물리적으로 표현되는 논리적 구조입니다.

| 프로젝션 | 스토리지 | 사용량 |
|------------|---------|-------|
| [테이블](knowledge-store-projections-examples.md#define-a-table-projection) | Azure Table Storage | 행 및 열로 가장 잘 표현되는 데이터에 사용되거나 데이터의 세부적인 표현(예: 데이터 프레임)이 필요할 때마다 사용됩니다. 테이블 프로젝션을 사용하면 셰이퍼 기술을 사용하여 스키마화된 도형을 [정의하거나 인라인 셰이핑을 사용하여](knowledge-store-projection-shape.md) 열과 행을 지정할 수 있습니다. 친숙한 정규화 원칙에 따라 콘텐츠를 여러 테이블로 구성할 수 있습니다. 동일한 그룹에 있는 테이블은 자동으로 관련됩니다. |
| [개체](knowledge-store-projections-examples.md#define-an-object-projection) | Azure Blob Storage | 하나의 JSON 문서에서 데이터 및 보강의 전체 JSON 표현이 필요한 경우에 사용됩니다. 테이블 프로젝션과 마찬가지로 유효한 JSON 개체만 개체로 프로젝션될 수 있으며 셰이핑이 이를 수행하는 데 도움이 될 수 있습니다. |
| [파일](knowledge-store-projections-examples.md#define-a-file-projection) | Azure Blob Storage | 정규화된 이진 이미지 파일을 저장해야 할 때 사용됩니다. |

## <a name="projection-definition"></a>프로젝션 정의

프로젝션은 [기술 자료](/rest/api/searchservice/create-skillset)의 "knowledgeStore" 속성 아래에 지정됩니다. 프로젝션 정의는 인덱서 호출 중에 보강된 콘텐츠가 있는 Azure Storage 개체를 만들고 로드하는 데 사용됩니다. 이러한 개념에 익숙하지 않은 경우 [AI 보강부터](cognitive-search-concept-intro.md) 시작하여 소개하세요.

다음 예제에서는 knowledgeStore에서 프로젝션의 배치 및 기본 구성을 보여 줍니다. 이름, 형식 및 콘텐츠 소스는 프로젝션 정의를 구성합니다.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums-main", "generatedKeyName": "ID", "source": "/document/tableprojection" },
          { "tableName": "ks-museumEntities", "generatedKeyName": "ID","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "ID", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>프로젝션 그룹

프로젝션은 복잡한 컬렉션의 배열입니다. 즉, 각 형식의 여러 집합을 지정할 수 있습니다. 하나의 프로젝션 그룹만 사용하는 것이 일반적이지만 스토리지 요구 사항에 다양한 도구 및 시나리오 지원이 포함된 경우 여러 프로젝션 그룹을 사용할 수 있습니다. 예를 들어 기술 세트의 디자인 및 디버그에 하나의 그룹을 사용할 수 있고, 두 번째 집합은 온라인 앱에 사용되는 출력을 수집하고, 세 번째 그룹은 데이터 과학 워크로드에 사용합니다.

프로젝션 아래의 모든 그룹을 채우는 데 동일한 기술체 출력이 사용됩니다. 다음 예제에서는 두 가지입니다.

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

프로젝션 그룹에는 상호 배타성 및 관련성의 다음과 같은 주요 특징이 있습니다. 

| 원칙 | Description |
|-----------|-------------|
| 상호 배제 | 각 그룹은 다른 데이터 셰이핑 시나리오를 지원하기 위해 다른 그룹에서 완전히 격리됩니다. 예를 들어 서로 다른 테이블 구조와 조합을 테스트하는 경우 AB 테스트를 위해 각 집합을 다른 프로젝션 그룹에 배치합니다. 각 그룹은 동일한 원본(보강 트리)에서 데이터를 가져오지만 피어 프로젝션 그룹의 table-object-file 조합에서 완전히 격리됩니다.|
| 관련성 | 프로젝션 그룹 내에서 테이블, 개체 및 파일의 콘텐츠는 관련됩니다. 지식 저장소는 생성된 키를 공통 부모 노드에 대한 참조 지점으로 사용합니다. 예를 들어 이미지와 텍스트가 포함된 문서가 있는 시나리오를 생각해 보겠습니다. 텍스트를 테이블과 이미지에 이진 파일로 프로젝팅할 수 있으며 테이블과 개체 모두 파일 URL을 포함하는 열/속성을 갖게 됩니다.|

## <a name="projection-source"></a>프로젝션 "원본"

source 매개 변수는 프로젝션 정의의 세 번째 구성 요소입니다. 프로젝션은 AI 보강 파이프라인의 데이터를 저장하기 때문에 프로젝션의 원본은 항상 기술의 출력입니다. 따라서 출력은 단일 필드(예: 번역된 텍스트의 필드)일 수 있지만 종종 데이터 셰이프에 대한 참조입니다.

데이터 셰이프는 기술 집합에서 제공됩니다. Cognitive Search 제공되는 모든 기본 제공 기술 중에는 데이터 셰이프를 만드는 데 사용되는 [**쉐이퍼 기술이라는**](cognitive-search-skill-shaper.md) 유틸리티 기술이 있습니다. 지식 저장소에서 프로젝션을 지원하기 위해 쉐이퍼 기술(필요한 만큼)을 포함할 수 있습니다.

셰이프는 테이블 프로젝션과 함께 자주 사용됩니다. 여기서 셰이프는 테이블로 이동하는 행뿐만 아니라 생성되는 열도 지정합니다(도형을 개체 프로젝션에 전달할 수도 있습니다).

셰이프는 복잡할 수 있으며 여기에서 심층적으로 설명하는 것은 범위를 벗어나지만 다음 예제에서는 기본 도형을 간략하게 보여 줍니다. 쉐이퍼 기술의 출력은 테이블 프로젝션의 원본으로 지정됩니다. 테이블 프로젝션 자체에는 셰이프에 지정된 대로 "metadata-storage_path", "reviews_text", "reviews_title" 등에 대한 열이 있습니다.

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

## <a name="projection-lifecycle"></a>프로젝션 수명 주기

프로젝션에는 데이터 원본의 원본 데이터에 연결된 수명 주기가 있습니다. 원본 데이터가 업데이트되고 다시 인덱싱되면 프로젝션이 보강 결과로 업데이트되므로 프로젝션이 결국 데이터 원본의 데이터와 일치합니다. 그러나 프로젝션도 Azure Storage에 독립적으로 저장됩니다. 인덱서나 검색 서비스 자체가 삭제되면 프로젝션은 삭제되지 않습니다. 

## <a name="consume-in-apps"></a>앱에서 사용

인덱서가 실행된 후 프로젝션에 연결하고 다른 앱 및 워크로드에서 데이터를 소비합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) 사용하여 개체 만들기 및 콘텐츠를 확인합니다.

+ [데이터 탐색에 Power BI](knowledge-store-connect-power-bi.md)사용합니다. 이 도구는 데이터가 Azure Table Storage 있을 때 가장 잘 작동합니다. Power BI 내에서 쿼리 및 분석하기 쉬운 새 테이블로 데이터를 조작할 수 있습니다.

+ 데이터 과학 파이프라인의 Blob 컨테이너에서 보강된 데이터를 사용합니다. 예를 들어 [Blob에서 Pandas DataFrame으로 데이터를 로드할](/azure/architecture/data-science-process/explore-data-blob)수 있습니다.

+ 마지막으로 지식 저장소에서 데이터를 내보내야 하는 경우, Azure Data Factory에는 데이터를 내보내고 선택한 데이터베이스에 배치하는 커넥터가 있습니다.

## <a name="checklist-for-getting-started"></a>시작하기 위한 검사 목록

프로젝션은 지식 저장소에만 사용되며 검색 인덱스를 구성하는 데 사용되지 않습니다.

1. Azure Storage **액세스 키에서** 연결 문자열을 얻고 계정이 StorageV2(범용 V2)인지 확인합니다.

1. Azure Storage 동안 프로젝션에 대해 충돌하지 않는 이름을 선택할 수 있도록 컨테이너 및 테이블의 기존 콘텐츠를 숙지합니다. 지식 저장소는 테이블과 컨테이너의 느슨한 컬렉션입니다. 관련 개체를 추적하기 위해 명명 규칙을 채택하는 것이 좋습니다.

1. Cognitive Search 인덱서에서 [보강 캐싱을 사용하도록 설정한](search-howto-incremental-index.md) 다음 [인덱서 를 실행하여](search-howto-run-reset-indexers.md) 기술 도구를 실행하고 캐시를 채웁다. 캐시가 채워지면 기술 자체가 수정되지 않는 한 지식 저장소의 프로젝션 정의를 무료로 수정할 수 있습니다.

1. 코드에서 모든 프로젝션은 기술 모음에서만 정의됩니다. 프로젝션에 적용되는 인덱서 속성(예: 필드 매핑 또는 출력 필드 매핑)이 없습니다. 기술 자료 정의 내에서는 knowledgeStore 속성 및 기술 배열의 두 가지 영역에 집중합니다.

   1. knowledgeStore에서 섹션의 테이블, 개체, 파일 프로젝션을 `projections` 지정합니다. 개체 형식, 개체 이름 및 수량(정의한 프로젝션 수에 따라)은 이 섹션에서 결정됩니다.

   1. 기술 배열에서 각 프로젝션의 에서 참조할 기술 출력을 `source` 결정합니다. 모든 프로젝션에는 원본이 있습니다. 원본은 업스트림 기술의 출력일 수 있지만 쉐이퍼 기술의 출력인 경우가 많습니다. 프로젝션의 컴퍼지션은 도형을 통해 결정됩니다. 

1. 기존 기술 항목에 프로젝션을 추가하는 경우 [기술 을 업데이트하고](/rest/api/searchservice/update-skillset) [인덱서 를 실행합니다.](/rest/api/searchservice/run-indexer)

1. Azure Storage 결과를 확인합니다. 후속 실행에서 Azure Storage 개체를 삭제하거나 기술 Azure Storage 프로젝트 이름을 변경하여 이름 충돌을 방지합니다.

## <a name="next-steps"></a>다음 단계

각 프로젝션 형식에 대한 구문 및 예제를 검토합니다.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)