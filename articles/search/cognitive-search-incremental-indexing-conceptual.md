---
title: 증분 보강 개념(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Storage의 AI 보강 파이프라인에서 중간 콘텐츠 및 증분 변경 내용을 캐시하여 기존의 처리된 문서에 대한 투자를 보존합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 7b3d0fc85afbff58641edea332576f921c96b672
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727789"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure Cognitive Search의 증분 보강 및 캐싱

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)는 이 기능을 지원합니다.

증분 보강은 [기술 세트](cognitive-search-working-with-skillsets.md)를 대상으로 하는 기능입니다. Azure Storage 활용하여 보강 파이프라인에서 내보낸 처리 출력을 이후 인덱서 실행에서 재사용할 수 있도록 저장합니다. 인덱서는 가능할 경우 여전히 유효한 캐시된 출력을 모두 재사용합니다. 

증분 보강은 처리(특히 OCR 및 이미지 처리)에 대한 금전적 투자를 보존할 뿐만 아니라 시스템 효율을 개선하기도 합니다. 

증분 캐싱을 사용하는 워크플로에는 다음 단계가 포함됩니다.

1. 로그를 저장할 [Azure Storage 계정을 만들거나 식별](../storage/common/storage-account-create.md)합니다.
1. 인덱서에서 [증분 보강을 사용하도록 설정](search-howto-incremental-index.md)합니다.
1. 파이프라인을 호출할 [인덱서](/rest/api/searchservice/create-indexer) 및 [기술 세트](/rest/api/searchservice/create-skillset)를 만듭니다. 처리하는 동안 보강 단계가 나중에 사용하기 위해 Blob Storage의 각 문서에 대해 저장됩니다.
1. 코드를 테스트하고 변경한 후에 [기술 세트 업데이트](/rest/api/searchservice/update-skillset)를 사용하여 정의를 수정합니다.
1. [인덱서를 실행](/rest/api/searchservice/run-indexer)하여 보다 빠르고 비용 효율적인 처리를 위해 캐시된 출력을 검색하는 파이프라인을 호출합니다.

기존 인덱서를 사용할 때의 단계 및 고려 사항에 대한 자세한 내용은 [증분 보강 설정](search-howto-incremental-index.md)을 참조하세요.

## <a name="indexer-cache"></a>인덱서 캐시

증분 보강은 보강 파이프라인에 캐시를 추가합니다. 인덱서는 [문서 크래킹](search-indexer-overview.md#document-cracking) 결과와 모든 문서에 대한 각 기술의 출력을 캐시합니다. 기술 세트가 업데이트되면 변경된 기술 또는 다운스트림 기술만 다시 실행됩니다. 업데이트된 결과는 캐시에 기록되고, 문서는 검색 인덱스 또는 지식 저장소에서 업데이트됩니다.

실제로 캐시는 Azure Storage 계정의 Blob 컨테이너에 저장됩니다. 또한 캐시는 업데이트 처리에 대한 내부 기록에도 테이블 스토리지를 사용합니다. 검색 서비스 내의 모든 인덱스는 인덱서 캐시에 대해 동일한 스토리지 계정을 공유할 수 있습니다. 각 인덱서에는 사용 중인 컨테이너에 대해 고유하고 변경할 수 없는 캐시 식별자가 할당됩니다.

> [!NOTE]
> 인덱서 캐시에는 범용 저장소 계정이 필요합니다. 자세한 내용은 [다양한 유형의 저장소 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

## <a name="cache-configuration"></a>캐시 구성

증분 보강의 이점을 활용하려면 인덱서에서 `cache` 속성을 설정해야 합니다. 다음 예제에서는 캐싱이 설정된 인덱서를 보여 줍니다. 이 구성의 특정 부분에 대해서는 다음 섹션에서 설명합니다. 자세한 내용은 [증분 보강 설정](search-howto-incremental-index.md)을 참조하세요.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

기존 인덱서에 이 속성을 설정하려면 인덱서를 다시 설정하고 다시 실행해야 합니다. 그러면 데이터 원본의 모든 문서가 다시 처리됩니다. 이 단계는 이전 버전의 기술 세트에 의해 보강된 문서를 제거하기 위해 필요합니다. 

## <a name="cache-management"></a>캐시 관리

캐시의 수명 주기는 인덱서에서 관리합니다. 인덱서의 `cache` 속성이 null로 설정되거나 연결 문자열이 변경된 경우 다음 인덱서 실행 시 기존 캐시가 삭제됩니다. 캐시 수명 주기는 인덱서 수명 주기와도 연결됩니다. 인덱서가 삭제되면 연결된 캐시도 삭제됩니다.

증분 보강은 사용자가 개입할 필요 없이 변경 내용을 감지하고 대응하도록 설계되었지만 기본 동작을 재정의하는 데 사용할 수 있는 매개 변수가 있습니다.

+ 새 문서 우선 순위 지정
+ 기술 세트 검사 무시
+ 데이터 원본 검사 무시
+ 기술 세트 평가 강제 적용

### <a name="prioritize-new-documents"></a>새 문서 우선 순위 지정

캐시에 이미 표시된 들어오는 문서에 대한 처리를 제어하도록 `enableReprocessing` 속성을 설정합니다. `true`(기본값)을 설정하는 경우 인덱서를 다시 실행할 때 캐시에 이미 있는 문서가 다시 처리됩니다. 즉, 기술 업데이트가 해당 문서에 영향을 주는 것으로 가정됩니다. 

`false`를 설정하는 경우 기존 문서가 다시 처리되지 않고 실질적으로 새로운 들어오는 콘텐츠가 기존 콘텐츠보다 우선적으로 처리됩니다. `enableReprocessing`은 일시적으로만 `false`로 설정해야 합니다. 모음에서 일관성을 유지하려면 대부분의 경우에 `enableReprocessing`을 `true`로 설정하여 신규 문서와 기존 문서 모두 현재 기술 세트 정의에 따라 유효하도록 해야 합니다.

### <a name="bypass-skillset-evaluation"></a>기술 세트 평가 무시

기술 세트의 수정과 재처리는 일반적으로 나란히 수행됩니다. 그러나 기술 세트에 대한 일부 변경 내용은 다시 처리되지 않습니다(예: 사용자 지정 기술을 새 위치에 배포하거나 새 액세스 키로 배포하는 경우). 대부분의 경우 기술 세트 자체의 수준에 영향을 주지 않는 부수적 수정이 이에 해당합니다. 

기술 세트에 대한 변경 내용이 실제로 피상적인 경우 `disableCacheReprocessingChangeDetection` 매개 변수를 `true`로 설정하여 기술 세트 평가를 재정의해야 합니다.

1. 기술 세트 업데이트를 호출하고 기술 세트 정의를 수정합니다.
1. 요청에 `disableCacheReprocessingChangeDetection=true` 매개 변수를 추가합니다.
1. 변경 내용을 제출합니다.

이 매개 변수를 설정하면 기술 세트 정의에 대한 업데이트만 커밋되고, 변경으로 인해 기존 모음에 미치는 영향은 평가되지 않습니다.

다음 예제에서는 매개 변수를 사용하는 기술 세트 업데이트 요청을 보여 줍니다.

```http
PUT https://[search service].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>데이터 원본 유효성 검사 무시

데이터 원본 정의를 변경하면 대부분의 경우 캐시가 무효화됩니다. 그러나 연결 문자열 변경 또는 스토리지 계정 키 회전과 같이 변경으로 인해 캐시가 무효화되지 않아야 하는 경우에는 데이터 원본 업데이트에 `ignoreResetRequirement` 매개 변수를 추가합니다. 이 매개 변수를 `true`로 설정하면 모든 개체를 다시 작성하고 처음부터 채워야 하는 다시 설정 조건을 트리거하지 않고 커밋을 진행할 수 있습니다.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>기술 세트 평가 강제 적용

캐시의 목적은 불필요한 처리를 방지하는 것이지만 인덱서가 검색하지 않는 기술(예: 사용자 지정 기술 등의 외부 코드를 변경하는 경우)을 변경한다고 가정해보겠습니다.

이 경우 해당 기술의 출력에 대한 종속성이 있는 다운스트림 기술을 비롯하여 특정 기술에 대한 재처리를 적용하는 데 [기술 다시 설정](/rest/api/searchservice/preview-api/reset-skills)을 사용할 수 있습니다. 이 API는 무효화하고 다시 처리하도록 표시해야 하는 기술 세트의 목록과 함께 POST 요청을 수락합니다. 기술을 다시 설정한 후 인덱서를 실행하여 파이프라인을 호출합니다.

### <a name="reset-documents"></a>문서 다시 설정

[인덱서를 다시 설정](/rest/api/searchservice/reset-indexer)하면 검색 모음의 모든 문서가 다시 처리됩니다. 몇 개의 문서만 다시 처리해야 하고 데이터 원본은 업데이트할 수 없는 시나리오에서는 [문서 다시 설정(미리 보기)](/rest/api/searchservice/preview-api/reset-documents)을 사용하여 특정 문서를 강제로 다시 처리합니다. 문서를 다시 설정하면 인덱서는 해당 문서에 대한 캐시를 무효화하고 문서는 데이터 원본에서 읽어 다시 처리됩니다. 자세한 내용은 [인덱서, 기술 및 문서 실행 또는 다시 설정](search-howto-run-reset-indexers.md)을 참조하세요.

특정 문서를 다시 설정하기 위해 요청 페이로드에는 인덱스에서 읽은 문서 키 목록이 포함됩니다. API를 호출하는 방법에 따라 요청은 키 목록을 추가하거나 덮어쓰거나 큐에 추가합니다.

+ 각각의 키로 API를 여러 번 호출하면 새로운 키가 문서 키 다시 설정 목록에 추가됩니다. 

+ true로 설정된 `overwrite` 쿼리 문자열 매개 변수로 API를 호출하면 해당 요청의 페이로드로 다시 설정하도록 현재 문서 키 목록을 덮어씁니다.

+ API를 호출하면 인덱서가 수행하는 큐에 문서 키만 추가됩니다. 인덱서는 다음에 호출될 때(예약된 대로 또는 요청 시) 데이터 원본에서 다른 변경 내용이 있기 전에 재설정 문서 키를 처리하는 데 우선 순위를 지정합니다.

다음 예제에서는 문서 재설정 요청을 보여 줍니다.

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "documentKeys" : [
        "key1",
        "key2",
        "key3"
    ]
}
```

## <a name="change-detection"></a>변경 내용 검색

캐시를 사용하도록 설정하면 인덱서가 파이프라인 구성 변경을 평가하여 다시 사용할 수 있는 콘텐츠와 다시 처리해야 하는 콘텐츠를 확인합니다. 이 섹션에서는 캐시를 완전히 무효화하는 변경 내용 및 증분 처리를 트리거하는 변경 내용을 열거합니다. 

### <a name="changes-that-invalidate-the-cache"></a>캐시를 무효화하는 변경 내용

변경 내용이 무효화되면 전체 캐시가 더 이상 유효하지 않습니다. 변경 내용 무효화의 예로 데이터 원본이 업데이트되는 경우가 있습니다. 캐시를 무효화할 수 있는 변경 내용의 전체 목록은 다음과 같습니다.

* 데이터 원본 유형에 대한 변경
* 데이터 원본 컨테이너에 대한 변경
* 데이터 원본 자격 증명
* 데이터 원본 변경 내용 검색 정책
* 데이터 원본 삭제 검색 정책
* 인덱서 필드 매핑
* 인덱서 매개 변수
    * 구문 분석 모드
    * 제외된 파일 이름 확장명
    * 인덱싱된 파일 이름 확장명
    * 대용량 문서에 대한 스토리지 메타데이터만 인덱싱
    * 구분 기호로 분리된 텍스트 헤더
    * 구분 기호로 분리된 텍스트 구분 기호
    * 문서 루트
    * 이미지 작업(이미지를 추출하는 방법에 대한 변경)

### <a name="changes-that-trigger-incremental-processing"></a>증분 처리를 트리거하는 변경 내용

증분 처리는 기술 세트 정의를 평가하고 문서 트리에서 영향을 받는 부분을 선택적으로 업데이트하여 다시 실행할 기술을 결정합니다. 다음은 증분 보강이 발생하는 변경 내용의 전체 목록입니다.

* 기술 세트의 기술 유형이 다릅니다. 기술의 Odata 형식이 업데이트되었습니다.
* 기술 관련 매개 변수(예: URL, 기본값 또는 기타 매개 변수)가 업데이트되었습니다.
* 기술 출력이 변경되고, 기술에서 추가 또는 다른 출력을 반환합니다.
* 기술 업데이트 결과가 다른 상위 구조이며, 기술 연결이 변경되었습니다(예: 기술 입력).
* 이 기술에 대한 입력을 제공하는 기술이 업데이트되면 업스트림 기술이 무효화됩니다.
* 지식 저장소 프로젝션 위치가 업데이트되어 문서를 다시 프로젝션합니다.
* 지식 저장소 프로젝션이 변경되어 문서를 다시 프로젝션합니다.
* 인덱서에서 출력 필드 매핑이 변경되어 문서를 인덱스로 다시 프로젝션합니다.

## <a name="api-reference"></a>API 참조

REST API 버전 `2020-06-30-Preview`에서는 인덱서의 추가 속성을 통해 증분 보강을 제공합니다. 기술 세트 및 데이터 원본은 일반 공급 버전을 사용할 수 있습니다. API를 호출하는 방법에 대한 자세한 내용은 참조 설명서 외에 [증분 보강을 위한 캐싱 구성](search-howto-incremental-index.md)을 참조하세요.

+ [인덱서 만들기(api-version=2020-06-30-Preview)](/rest/api/searchservice/create-indexer) 

+ [인덱서 업데이트(api-version=2020-06-30-Preview)](/rest/api/searchservice/update-indexer) 

+ [기술 세트 업데이트(api-version=2020-06-30)](/rest/api/searchservice/update-skillset)(요청에 대한 새 URI 매개 변수)

+ [기술 세트 다시 설정(api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ 데이터베이스 인덱서(Azure SQL, Cosmos DB). 일부 인덱서는 쿼리를 통해 데이터를 검색합니다. 데이터를 검색하는 쿼리의 경우 [데이터 원본 업데이트](/rest/api/searchservice/update-data-source)는 요청 **ignoreResetRequirement** 에 대한 새 매개 변수를 지원합니다. 업데이트 작업에서 캐시를 무효화하지 않아야 하는 경우 이 매개 변수가 `true`로 설정되어야 합니다. 

  데이터에서 쉽게 검색되지 않는 의도치 않은 불일치가 발생할 수 있으므로 **ignoreResetRequirement** 는 신중하게 사용하세요.

## <a name="next-steps"></a>다음 단계

증분 보강은 변경 내용 추적을 기술 세트 및 AI 보강으로 확장하는 강력한 기능입니다. 증분 보강을 사용하면 기술 세트 디자인을 반복하면서 처리된 기존 콘텐츠를 다시 사용할 수 있습니다.

다음 단계로, 기존 인덱서에 캐싱을 사용하도록 설정하거나 새 인덱서를 정의할 때 캐시를 추가합니다.

> [!div class="nextstepaction"]
> [증분 보강을 위한 캐싱 구성](search-howto-incremental-index.md)