---
title: 증분 보강 개념(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Storage의 AI 보강 파이프라인에서 중간 콘텐츠 및 증분 변경 내용을 캐시하여 기존의 처리된 문서에 대한 투자를 보존합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/17/2021
ms.openlocfilehash: cb04bbea71588ea8d9fa1f1c4734b10e3d0b6792
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179206"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure Cognitive Search의 증분 보강 및 캐싱

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)는 이 기능을 지원합니다.

*증분 보강* 는 [기술 실행](cognitive-search-working-with-skillsets.md) 중에 캐시 된 강화을 사용 하 여 새롭고 변경 된 기술과 문서만 AI 처리를 수행 하도록 합니다. 캐시는 [문서 크랙](search-indexer-overview.md#document-cracking)의 출력과 모든 문서에 대 한 각 기술 출력을 포함 합니다. 이미지 추출 및 AI 처리가 청구 가능한 [이벤트](search-sku-manage-costs.md#billable-events)이기 때문에 캐시를 사용 하도록 설정 하면 비용을 줄이고 AI 보강 처리 시간을 향상 시킬 수 있습니다. 

캐싱을 사용 하도록 설정 하는 경우 인덱서는 업데이트를 평가 하 여 기존 강화를 캐시에서 끌어올 수 있는지 여부를 확인 합니다. 문서 크랙 단계의 이미지 및 텍스트 콘텐츠 및 편집에 대 한 업스트림 또는 직교 된 기술 출력은 재사용할 수 있습니다.

기술 업데이트에 표시 된 대로 증분 강화을 수행한 후에는 새로 고침 결과가 캐시에, 검색 인덱스나 기술 자료 저장소에도 다시 기록 됩니다.

## <a name="cache-configuration"></a>캐시 구성

실제로 캐시는 인덱서 당 하나씩 Azure Storage 계정의 blob 컨테이너에 저장 됩니다. 각 인덱서에는 사용 중인 컨테이너에 해당 하는 고유 하 고 변경할 수 없는 캐시 식별자가 할당 됩니다.

캐시는 "cache" 속성을 지정 하 고 인덱서를 실행할 때 생성 됩니다. 보강 콘텐츠만 캐시할 수 있습니다. 인덱서에 연결 된 기술 없으면 캐싱이 적용 되지 않습니다. 

다음 예제에서는 캐싱이 설정된 인덱서를 보여 줍니다. 전체 지침은 [보강 캐싱 사용](search-howto-incremental-index.md) 을 참조 하세요. Cache 속성을 추가 하는 경우 요청에서 preview API 버전, 2020-06-30-Preview 이상을 사용 합니다.

```json
POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    {
        "name": "myIndexerName",
        "targetIndexName": "myIndex",
        "dataSourceName": "myDatasource",
        "skillsetName": "mySkillset",
        "cache" : {
            "storageConnectionString" : "<Your storage account connection string>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

## <a name="cache-management"></a>캐시 관리

캐시의 수명 주기는 인덱서에서 관리합니다. 인덱서를 삭제 하면 해당 캐시도 삭제 됩니다. 인덱서의 "cache" 속성이 null로 설정 되거나 연결 문자열이 변경 된 경우 다음 인덱서 실행 시 기존 캐시가 삭제 됩니다. 

증분 보강는 사용자가 개입할 필요 없이 변경 내용을 감지 하 고 대응 하도록 설계 되었지만 특정 동작을 호출 하는 데 사용할 수 있는 매개 변수는 다음과 같습니다.

+ [새 문서 우선 순위 지정](#Prioritize-new-documents)
+ [기술 세트 검사 무시](#Bypass-skillset-checks)
+ [데이터 원본 검사 무시](#Bypass-data-source-check)
+ [기술 세트 평가 강제 적용](#Force-skillset-evaluation)

<a name="Prioritize-new-documents"></a>

### <a name="prioritize-new-documents"></a>새 문서 우선 순위 지정

"Cache" 속성은 "enableReprocessing" 매개 변수를 포함 합니다. 캐시에 이미 표시 된 들어오는 문서에 대 한 처리를 제어 하는 데 사용 됩니다. True (기본값) 이면 인덱서를 다시 실행할 때 캐시에 이미 있는 문서가 다시 처리 됩니다. 즉, 기술 업데이트가 해당 문서에 영향을 주는 것으로 가정 합니다. 

False 인 경우 기존 문서를 다시 처리 하지 않으므로 기존 콘텐츠를 통해 새로운 들어오는 콘텐츠의 우선 순위를 효과적으로 결정 합니다. "EnableReprocessing"은 일시적으로 false로 설정 해야 합니다. "EnableReprocessing"를 true로 설정 하면 대부분의 경우 새 문서와 기존 문서가 모두 현재 기술 정의에 따라 유효 하 게 유지 됩니다.

<a name="Bypass-skillset-checks"></a>

### <a name="bypass-skillset-evaluation"></a>기술 세트 평가 무시

해당 기술에 대 한 기술 및 다시 처리를 수정 하는 것은 일반적으로 직접 수행 됩니다. 그러나 기술에 대 한 일부 변경으로 인해 다시 처리 하지 않아야 합니다 (예: 새 위치에 사용자 지정 기술 배포 또는 새 액세스 키로). 대부분의 경우는 기술 출력 자체의 영향을 받지 않는 주변을 수정 하는 것입니다. 

기술에 대 한 변경 내용이 실제로 피상적인 "disableCacheReprocessingChangeDetection" 매개 변수를 true로 설정 하 여 기술 평가를 재정의 해야 합니다.

1. [Update 기술](/rest/api/searchservice/update-skillset) 를 호출 하 고 기술 정의를 수정 합니다.
1. 요청에 "disableCacheReprocessingChangeDetection = true" 매개 변수를 추가 합니다.
1. 변경 내용을 제출합니다.

이 매개 변수를 설정 하면 기술 정의에 대 한 업데이트만 커밋되고 기존 캐시의 효과에 대 한 변경 내용은 평가 되지 않습니다. Preview API 버전 2020-06-30-Preview 이상을 사용 합니다.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview
    {
        "disableCacheReprocessingChangeDetection" : true
    }
```

<a name="Bypass-data-source-check"></a>

### <a name="bypass-data-source-validation-checks"></a>데이터 원본 유효성 검사 무시

데이터 원본 정의를 변경하면 대부분의 경우 캐시가 무효화됩니다. 그러나 연결 문자열을 변경 하거나 저장소 계정에서 키를 회전 하는 등의 변경으로 캐시가 무효화 되지 않아야 하는 경우에는 데이터 원본 업데이트에 "ignoreResetRequirement 사항" 매개 변수를 추가 합니다. 이 매개 변수를 true로 설정 하면 모든 개체를 다시 작성 하 여 처음부터 채우기 위해 다시 설정 조건을 트리거하지 않고 커밋을 진행할 수 있습니다.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview
    {
        "ignoreResetRequirement" : true
    }
```

<a name="Force-skillset-evaluation"></a>

### <a name="force-skillset-evaluation"></a>기술 세트 평가 강제 적용

캐시의 목적은 불필요한 처리를 방지하는 것이지만 인덱서가 검색하지 않는 기술(예: 사용자 지정 기술 등의 외부 코드를 변경하는 경우)을 변경한다고 가정해보겠습니다.

이 경우 해당 기술의 출력에 대한 종속성이 있는 다운스트림 기술을 비롯하여 특정 기술에 대한 재처리를 적용하는 데 [기술 다시 설정](/rest/api/searchservice/preview-api/reset-skills)을 사용할 수 있습니다. 이 API는 무효화하고 다시 처리하도록 표시해야 하는 기술 세트의 목록과 함께 POST 요청을 수락합니다. 스킬을 다시 설정한 후에는 [Run 인덱서](/rest/api/searchservice/run-indexer) 요청을 따라 파이프라인 처리를 호출 합니다.

## <a name="re-cache-specific-documents"></a>특정 문서 다시 캐시

[인덱서를 다시 설정](/rest/api/searchservice/reset-indexer) 하면 검색 모음 모든 문서가 다시 처리 됩니다. 몇 개의 문서만 다시 처리 해야 하는 시나리오에서 [문서 다시 설정 (미리 보기)](/rest/api/searchservice/preview-api/reset-documents) 을 사용 하 여 특정 문서의 다시 처리를 강제로 수행 합니다. 문서를 다시 설정 하면 인덱서는 해당 문서에 대 한 캐시를 무효화 합니다. 그러면 데이터 원본에서 해당 문서를 읽어 다시 처리 합니다. 자세한 내용은 [인덱서, 기술 및 문서 실행 또는 다시 설정](search-howto-run-reset-indexers.md)을 참조하세요.

특정 문서를 다시 설정 하기 위해 요청은 검색 인덱스에서 읽은 문서 키 목록을 제공 합니다. 키가 외부 데이터 원본의 필드에 매핑된 경우 입력 한 값은 검색 인덱스에 사용 되는 값 이어야 합니다.

API를 호출하는 방법에 따라 요청은 키 목록을 추가하거나 덮어쓰거나 큐에 추가합니다.

+ 각각의 키로 API를 여러 번 호출하면 새로운 키가 문서 키 다시 설정 목록에 추가됩니다. 

+ "덮어쓰기" 쿼리 문자열 매개 변수를 true로 설정 하 여 API를 호출 하면 요청의 페이로드에서 다시 설정할 현재 문서 키 목록이 덮어쓰여집니다.

+ API를 호출하면 인덱서가 수행하는 큐에 문서 키만 추가됩니다. 인덱서는 다음에 호출될 때(예약된 대로 또는 요청 시) 데이터 원본에서 다른 변경 내용이 있기 전에 재설정 문서 키를 처리하는 데 우선 순위를 지정합니다.

다음 예제에서는 문서 재설정 요청을 보여 줍니다.

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
    {
        "documentKeys" : [
            "key1",
            "key2",
            "key3"
        ]
    }
```

## <a name="changes-that-invalidate-the-cache"></a>캐시를 무효화하는 변경 내용

캐시를 사용하도록 설정하면 인덱서가 파이프라인 구성 변경을 평가하여 다시 사용할 수 있는 콘텐츠와 다시 처리해야 하는 콘텐츠를 확인합니다. 이 섹션에서는 캐시를 완전히 무효화하는 변경 내용 및 증분 처리를 트리거하는 변경 내용을 열거합니다. 

변경 내용이 무효화되면 전체 캐시가 더 이상 유효하지 않습니다. 변경 내용 무효화의 예로 데이터 원본이 업데이트되는 경우가 있습니다. 다음은 캐시를 무효화 하는 인덱서 파이프라인의 모든 부분에 대 한 전체 변경 내용 목록입니다.

+ 데이터 원본 유형 변경
+ 데이터 원본 컨테이너 변경
+ 데이터 원본 자격 증명 변경
+ 데이터 원본 변경 검색 정책 변경
+ 데이터 원본 삭제 검색 정책 변경
+ 인덱서 필드 매핑 변경
+ 인덱서 매개 변수 변경:
  + 구문 분석 모드
  + 제외된 파일 이름 확장명
  + 인덱싱된 파일 이름 확장명
  + 대용량 문서에 대한 스토리지 메타데이터만 인덱싱
  + 구분 기호로 분리된 텍스트 헤더
  + 구분 기호로 분리된 텍스트 구분 기호
  + 문서 루트
  + 이미지 작업(이미지를 추출하는 방법에 대한 변경)

## <a name="changes-that-trigger-incremental-processing"></a>증분 처리를 트리거하는 변경 내용

증분 처리는 기술 세트 정의를 평가하고 문서 트리에서 영향을 받는 부분을 선택적으로 업데이트하여 다시 실행할 기술을 결정합니다. 다음은 증분 보강이 발생하는 변경 내용의 전체 목록입니다.

+ 기술 유형 변경 (기술의 OData 유형이 업데이트 됨)
+ 기술 관련 매개 변수가 업데이트 됩니다 (예: URL, 기본값 또는 기타 매개 변수).
+ 기술 출력 변경 내용, 기술에서 추가 또는 다른 출력 반환
+ 기술 입력 변경으로 인해 다른 상위 구조, 기술 체인이 변경 됨
+ 이 기술에 대한 입력을 제공하는 기술이 업데이트되면 업스트림 기술이 무효화됩니다.
+ 기술 자료 저장소 프로젝션 위치를 업데이트 하 여 문서를 다시 프로젝션 합니다.
+ 기술 자료 저장소에 대 한 변경 내용으로 인해 문서를 다시 프로젝션 합니다.
+ 인덱서에서 출력 필드 매핑이 변경 되어 문서를 인덱스로 다시 프로젝션 하는 결과가 발생 합니다.

## <a name="apis-used-for-caching"></a>캐싱에 사용 되는 Api

REST API 버전 `2020-06-30-Preview` 이상에서는 인덱서의 추가 속성을 통해 증분 보강 제공 합니다. 기술 세트 및 데이터 원본은 일반 공급 버전을 사용할 수 있습니다. 작업 순서에 대 한 자세한 내용은 참조 설명서 외에도  [증분 보강에 대 한 캐싱 구성](search-howto-incremental-index.md) 을 참조 하세요.

+ [인덱서 만들기 또는 업데이트 (api-version = 2020-06 -30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) 

+ [기술 세트 업데이트(api-version=2020-06-30)](/rest/api/searchservice/update-skillset)(요청에 대한 새 URI 매개 변수)

+ [기술 세트 다시 설정(api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Preview API 버전을 사용 하 여 호출 되는 경우 [, 업데이트 작업](/rest/api/searchservice/update-data-source)에서 캐시를 무효화 하지 않아야 하는 경우 true로 설정 해야 하는 "ignoreResetRequirement 사항" 이라는 새 매개 변수를 제공 합니다. "IgnoreResetRequirement 사항"을 사용 하면 쉽게 검색 되지 않는 데이터에서 의도 하지 않은 불일치가 발생할 수 있으므로 매우 유용 합니다.

## <a name="next-steps"></a>다음 단계

증분 보강은 변경 내용 추적을 기술 세트 및 AI 보강으로 확장하는 강력한 기능입니다. 증분 보강을 사용하면 기술 세트 디자인을 반복하면서 처리된 기존 콘텐츠를 다시 사용할 수 있습니다. 다음 단계로 인덱서에서 캐싱을 사용 하도록 설정 합니다.

> [!div class="nextstepaction"]
> [증분 보강 캐싱을 사용 하도록 설정](search-howto-incremental-index.md)