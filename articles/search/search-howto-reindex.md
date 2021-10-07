---
title: 검색 인덱스 다시 빌드
titleSuffix: Azure Cognitive Search
description: 새 요소를 추가하거나, 기존 요소 또는 문서를 업데이트하거나, 전체 다시 빌드 또는 부분 인덱싱에서 사용되지 않는 문서를 삭제하여 Azure Cognitive Search 인덱스를 새로 고칩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 1f457f4b86a4f0d6b535b00c45042d5e70a49d2d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614292"
---
# <a name="rebuild-an-index-in-azure-cognitive-search"></a>Azure Cognitive Search 인덱스 다시 작성

이 문서에서는 Azure Cognitive Search 인덱스를 다시 빌드하는 방법, 다시 빌드가 필요한 상황 및 지속적인 쿼리 요청에 다시 빌드가 미치는 영향을 완화하기 위한 권장 사항을 설명합니다.

*다시 작성* 은 모든 필드 기반의 반전된 인덱스를 포함하여 인덱스와 연결된 실제 데이터 구조를 삭제했다가 다시 만드는 것을 의미합니다. Azure Cognitive Search에서는 개별 필드를 삭제했다가 다시 만들 수 없습니다. 인덱스를 다시 작성하려면 모든 필드 스토리지를 삭제하고, 기존 또는 수정된 인덱스 스키마를 기준으로 다시 만든 후 인덱스에 푸시되었거나 외부 원본에서 가져온 데이터로 다시 채워야 합니다. 

인덱스 디자인을 반복 처리할 때 개발 중에 인덱스를 다시 빌드하는 것이 일반적이지만, 복합 형식을 추가하거나 제안기에 필드를 추가하는 경우처럼 구조적 변경을 수용하기 위해 프로덕션 수준의 인덱스를 다시 빌드해야 할 수도 있습니다.

## <a name="rebuild-versus-refresh"></a>"다시 빌드" 및 "새로 고침"

새 문서, 수정된 문서 또는 삭제된 문서를 사용하여 인덱스 내용을 새로 고치는 것과 다시 빌드를 혼동해서는 안 됩니다. 검색 모음을 새로 고치는 것은 거의 모든 검색 앱에서 제공되며 일부 시나리오에서는 최신 업데이트가 필요합니다(예: 검색 모음이 온라인 판매 앱의 인벤토리 변경을 반영해야 하는 경우).

인덱스의 구조를 변경하지 않는 한 인덱스를 처음 로드하는 데 사용한 것과 동일한 기술을 사용하여 인덱스를 새로 고칠 수 있습니다.

* 푸시 모드 인덱싱의 경우 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)를 호출하여 변경 내용을 인덱스에 푸시합니다.

* 인덱서의 경우 [인덱서 실행을 예약](search-howto-schedule-indexers.md)하고 변경 내용 추적 또는 타임스탬프를 사용하여 델타를 식별할 수 있습니다. 스케줄러에서 관리할 수 있는 것보다 빠르게 업데이트를 반영해야 하는 경우에는 푸시 모드 인덱싱을 대신 사용할 수 있습니다.

## <a name="rebuild-conditions"></a>다시 작성 조건

다음 조건 중 하나라도 충족되면 인덱스를 삭제하고 다시 만듭니다. 

| 조건 | 설명 |
|-----------|-------------|
| 필드 정의 변경 | 필드 이름, 데이터 형식 또는 특정 [인덱싱 특성](/rest/api/searchservice/create-index)(검색 가능, 필터링 가능, 정렬 가능, 패싯 가능)을 수정하려면 전체적으로 다시 작성해야 합니다. |
| 필드에 분석기 할당 | [분석기](search-analyzers.md)는 인덱스에 정의된 후 필드에 할당됩니다. 언제든지 새 분석기 정의를 인덱스에 추가할 수 있지만, 분석기 *할당* 은 필드를 만들 때만 가능합니다. **분석기** 와 **indexAnalyzer** 둘 다 그렇습니다. **searchAnalyzer** 속성은 예외입니다(이 속성을 기존 필드에 할당 가능). |
| 인덱스의 분석기 정의 업데이트 또는 삭제 | 전체 인덱스를 다시 작성하지 않는 한 인덱스의 기존 분석기 구성(분석기, 토크나이저, 토큰 필터 또는 char 필터)을 삭제 또는 변경할 수 없습니다. |
| 제안기에 필드 추가 | 필드가 이미 있고 필드에 [Suggesters](index-add-suggesters.md) 구문을 추가하려면 인덱스를 다시 작성해야 합니다. |
| 필드 삭제 | 필드의 모든 추적을 실제로 제거하려면 인덱스를 다시 작성해야 합니다. 즉시 다시 빌드가 실용적이지 않은 경우 "삭제됨" 필드에 대한 액세스를 사용하지 않도록 애플리케이션 코드를 수정하거나 [$select 쿼리 매개 변수](search-query-odata-select.md)를 사용하여 결과 집합에 표시되는 필드를 선택할 수 있습니다. 실제로 필드 정의와 콘텐츠는 다음 번에 의심스러운 필드를 생략하는 스키마를 적용할 때 다시 작성이 수행될 때까지 인덱스에 남아 있습니다. |
| 계층 전환 | 용량이 더 필요한 경우 Azure Portal에 현재 위치 업그레이드가 수행되지 않습니다. 새 서비스를 만들어야 하며 새 서비스에서 처음부터 인덱스를 작성해야 합니다. 이 프로세스를 자동화하는 데 도움이 되도록 이 [Azure Cognitive Search .NET 샘플 리포지토리의](https://github.com/Azure-Samples/azure-search-dotnet-samples) **index-backup-restore** 샘플 코드를 사용할 수 있습니다. 이 앱은 일련의 JSON 파일에 인덱스를 백업한 다음 지정하는 검색 서비스에서 인덱스를 다시 만듭니다.|

## <a name="update-conditions"></a>업데이트 조건

기존 실제 구조에 영향을 주지 않으면서 많은 다른 부분을 수정할 수 있습니다. 특히, 다음 변경은 인덱스를 다시 빌드할 필요가 *없습니다*. 이러한 변경의 경우 변경 사항으로 [인덱스 정의를 업데이트](/rest/api/searchservice/update-index)할 수 있습니다.

+ 새 필드 추가
+ 기존 필드의 **retrievable** 특성 설정
+ 기존 필드의 **searchAnalyzer** 설정
+ 인덱스에 새 분석기 정의 추가
+ 점수 매기기 프로필 추가, 업데이트 또는 삭제
+ CORS 설정 추가, 업데이트 또는 삭제
+ synonymMaps 추가, 업데이트 또는 삭제

새 필드를 추가할 때 기존의 인덱싱된 문서에는 새 필드에 null 값이 제공됩니다. 나중에 데이터를 새로 고치면 외부 원본 데이터의 값이 Azure Cognitive Search에 의해 추가된 null을 대체합니다. 인덱스 콘텐츠 업데이트에 대한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

## <a name="how-to-rebuild-an-index"></a>인덱스를 다시 작성하는 방법

개발하는 동안 인덱스 스키마는 자주 변경됩니다. 작은 대표 데이터 세트로 신속하게 삭제, 다시 만들기 및 다시 로드될 수 있는 인덱스를 만들어 이를 계획할 수 있습니다.

이미 프로덕션 환경에서 사용되는 애플리케이션의 경우 기존 인덱스와 나란히 실행되는 새 인덱스를 만들어 쿼리 가동 중지 시간을 피하는 것이 좋습니다. 애플리케이션 코드는 새 인덱스에 대한 리디렉션을 제공합니다.

인덱싱은 백그라운드에서 실행되지 않으며 서비스는 추가 인덱싱과 진행 중인 쿼리의 균형을 맞춥니다. 인덱싱 중에 포털에서 [쿼리 요청을 모니터링](search-monitor-queries.md)하여 쿼리가 적시에 완료되도록 할 수 있습니다.

1. 다시 빌드가 필요한지 여부를 확인합니다. 필드를 추가하거나 필드와 관련이 없는 인덱스의 일부를 변경하는 경우 삭제, 다시 만들기 및 완전히 다시 로드하지 않고도 [정의를 업데이트](/rest/api/searchservice/update-index)할 수 있습니다.

1. 나중에 참조하기 위해 필요한 경우 [인덱스 정의를 가져옵니다](/rest/api/searchservice/get-index) .

1. 새 인덱스와 이전 인덱스를 나란히 실행하지 않는다고 가정하고 [기존 인덱스를 삭제](/rest/api/searchservice/delete-index)합니다. 

   해당 인덱스를 대상으로 하는 모든 쿼리는 즉시 삭제됩니다. 인덱스 삭제는 되돌릴 수 없으며, 필드 컬렉션 및 기타 구성에 대한 실제 스토리지가 제거됩니다. 삭제하기 전에 어떤 영향이 미칠지 생각합니다. 

1. 요청 본문에 변경되거나 수정된 필드 정의가 포함된 [수정된 인덱스를 만듭니다](/rest/api/searchservice/create-index).

1. 외부 원본의 [문서를 사용하는 인덱스를 로드](/rest/api/searchservice/addupdate-or-delete-documents)합니다.

인덱스를 만들면 실제 스토리지가 인덱스 스키마의 각 필드에 할당되고 검색 가능한 각 필드에 대해 반전된 인덱스가 생성됩니다. 검색할 수 없는 필드는 필터 또는 식에 사용할 수 있지만, 반전된 인덱스를 갖고 있지 않으며 전체 텍스트 또는 유사 항목 검색이 불가능합니다. 인덱스 다시 작성 시, 사용자가 제공하는 인덱스 스키마에 따라 이러한 반전된 인덱스가 삭제된 후 다시 만들어집니다.

인덱스를 로드할 때 각 필드의 반전된 인덱스가 각 문서의 고유한 토큰화된 모든 단어와 해당 문서 ID에 대한 맵으로 채워집니다. 예를 들어, 호텔 데이터 세트를 인덱싱할 때 City 필드용으로 만든 반전된 인덱스는 Seattle, Portland 등의 용어를 포함할 수 있습니다. City 필드에 Seattle 또는 Portland가 포함된 문서는 해당 용어와 함께 문서 ID가 표시됩니다. [추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents) 작업에서 용어 및 문서 ID 목록이 이에 따라 업데이트됩니다.

> [!NOTE]
> SLA 요구 사항이 엄격한 경우, 특별히 이 작업용으로 새 서비스를 프로비전하고 프로덕션 인덱스와는 완전히 고립된 상태로 개발 및 인덱싱을 진행하는 것이 바람직할 수 있습니다. 별도 서비스를 자체 하드웨어에서 실행하여 리소스 경합 가능성을 모두 제거합니다. 개발이 완료되면 새 인덱스를 원래 위치에 두고, 쿼리를 새 엔드포인트 및 인덱스로 리디렉션하거나, 완료된 코드를 실행하여 원래 Azure Cognitive Search 서비스에서 수정된 인덱스를 게시할 수 있습니다. 현재는 사용 준비가 완료된 인덱스를 다른 서비스로 이동하기 위한 메커니즘이 없습니다.

## <a name="check-for-updates"></a>업데이트 확인

첫 번째 문서를 로드하는 즉시 인덱스 쿼리를 시작할 수 있습니다. 문서 ID를 알고 있는 경우 [문서 조회 REST API](/rest/api/searchservice/lookup-document)가 특정 문서를 반환합니다. 보다 광범위한 테스트를 위해서는 인덱스가 완전히 로드될 때까지 기다린 다음, 쿼리를 사용하여 보려는 컨텍스트를 확인합니다.

[검색 탐색기](search-explorer.md)나 [Postman](search-get-started-rest.md) 또는 [Visual Studio Code](search-get-started-vs-code.md)와 같은 웹 테스트 도구를 사용하여 업데이트된 콘텐츠를 확인할 수 있습니다.

필드를 추가하거나 이름을 변경한 경우 [$select](search-query-odata-select.md)를 사용하여 해당 필드를 반환합니다. `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [대규모 데이터 집합 인덱싱](search-howto-large-index.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search의 보안](search-security-overview.md)