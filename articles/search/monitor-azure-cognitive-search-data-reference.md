---
title: 모니터링 데이터 참조 Azure Cognitive Search
description: Azure Cognitive Search 데이터를 모니터링하기 위한 로그 및 메트릭 참조입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: reference
ms.date: 11/24/2021
ms.custom: subject-monitoring
ms.openlocfilehash: 99199166f0bea8124f95c81e396664af09bdfeff
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133217745"
---
# <a name="azure-cognitive-search-monitoring-data-reference"></a>모니터링 데이터 참조 Azure Cognitive Search

이 문서에서는 Azure Cognitive Search 성능 및 가용성을 분석하기 위해 수집된 로그 및 메트릭 데이터에 대한 참조를 제공합니다. 검색 서비스에 대한 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 모니터링 [Azure Cognitive Search](monitor-azure-cognitive-search.md) 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션에서는[Azure Cognitive Search(Microsoft.Search/searchServices)에](../azure-monitor/essentials/metrics-supported.md#microsoftsearchsearchservices)대해 수집된 플랫폼 메트릭을 나열합니다.  

| 메트릭 ID | 단위 | 설명 |
|:-------|:-----|:------------|
| DocumentsProcessedCount | 개수 | 인덱싱 작업(인덱서에서 또는 문서를 직접 푸시하여)에서 성공적으로 처리된 문서 수의 합계입니다. |
| SearchLatency | 초 | 검색 서비스에서 실행되는 쿼리의 평균 검색 대기 시간 |
| SearchQueriesPerSecond | 초당 개수 | 검색 서비스에 대한 QPS(초당 검색 쿼리 수)의 평균입니다. 일반적으로 쿼리는 밀리초 단위로 실행되므로 초로 측정되는 쿼리만 QPS와 같은 메트릭에 표시됩니다. </br>최소값은 해당 분 동안 등록된 초당 검색 쿼리의 최하위 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분의 집계입니다. 예를 들어 1분 내에 다음과 같은 패턴을 발견할 수 있습니다. SearchQueriesPerSecond에 대한 최대 로드 1초, 이후 평균 로드 58초, 그리고 마지막으로 최솟값인 한 개의 쿼리만 있는 1초가 이어집니다.|
| SkillExecutionCount | 개수 | 인덱서 작업 중에 처리된 총 기술 실행 수입니다. |
| ThrottledSearchQueriesPercentage | 백분율 | 1분 간격 동안 실행된 총 검색 쿼리 수에서 제한된 검색 쿼리의 평균 백분율입니다.|

참조는 [Azure Monitor 지원되는 모든 플랫폼 메트릭](/azure/azure-monitor/platform/metrics-supported)목록을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭의 차원은 메트릭 값을 설명하는 추가 데이터를 전달하는 이름/값 쌍입니다. Azure Cognitive Search 실행된 문서 또는 기술 수("문서 처리 횟수" 및 "기술 실행 호출 수")를 캡처하는 메트릭과 연결된 차원은 다음과 입니다.

| 차원 이름 | Description |
| -------------- | ----------- |
| **DataSourceName** | 인덱서 실행 중에 사용되는 명명된 데이터 원본 연결입니다. 유효한 값은 [지원되는 데이터 원본 형식](search-indexer-overview.md#supported-data-sources)중 하나입니다. |
| **실패** | 인스턴스가 실패했는지 여부를 나타냅니다. |
| **IndexerName** | 인덱서의 이름입니다.|
| **IndexName** | 인덱스의 이름입니다.|
| **SkillsetName** | 인덱서 실행 중에 사용되는 기술 기술의 이름입니다. |
| **SkillName** | 기술 기술 내의 기술 이름입니다. |
| **SkillType** | @odata.type기술의 입니다. |

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

## <a name="resource-logs"></a>리소스 로그

[리소스 로그는](../azure-monitor/essentials/resource-logs.md) Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공하는 플랫폼 로그입니다. 리소스 로그는 검색 서비스에서 자동으로 생성되지만 기본적으로 수집되지는 않습니다. Azure 외부에서 전달하거나 보관을 위해 Azure Storage Azure Event Hubs Azure Monitor Log Analytics 작업 영역에 사용할 리소스 로그를 보내는 진단 설정을 만들어야 합니다.

이 섹션에서는 Azure Cognitive Search 위해 수집할 수 있는 리소스 로그의 유형(또는 범주)을 식별합니다.

+ 리소스 로그는 유형(또는 범주)으로 그룹화됩니다. Azure Cognitive Search 작업 범주 아래에 리소스 [**로그를 생성합니다.**](../azure-monitor/essentials/resource-logs-categories.md#microsoftsearchsearchservices)

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](/azure/azure-monitor/platform/resource-logs-schema) 목록을 참조하세요.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

[Azure Monitor 로그는](../azure-monitor/logs/data-platform-logs.md) 모니터링되는 리소스에서 로그 및 성능 데이터를 수집하고 구성하는 Azure Monitor 기능입니다. Log Analytics에 대한 진단 설정을 구성한 경우 Azure Monitor Logs 테이블에서 Azure Cognitive Search 생성된 리소스 로그를 쿼리할 수 있습니다.

이 섹션에서는 Azure Cognitive Search 관련되고 Log Analytics 및 Azure Portal 메트릭 탐색기 쿼리에 사용할 수 있는 모든 Azure Monitor Logs Kusto 테이블을 참조합니다.

| 테이블 | 설명 |
|-------|-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | 제어 평면 작업에 대한 인사이트를 제공하는 Azure 활동 로그의 항목입니다. 복제본 및 파티션 추가 또는 제거와 같이 컨트롤 플레인에서 호출되는 작업은 "관리자 키 얻기" 작업을 통해 표시됩니다. |
| [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) | 기록된 쿼리 및 인덱싱 작업입니다.|
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | 상태 및 성능을 측정하는 Azure Cognitive Search 내보낸 메트릭 데이터입니다. |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype#search-services)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블

Azure Cognitive Search [**Azure Diagnostics**](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블을 사용하여 검색 서비스의 쿼리 및 인덱싱과 관련된 리소스 로그를 수집합니다.

Log Analytics의 이 테이블에 대한 쿼리에는 공통 속성, [검색 관련 속성](#resource-log-search-props)및 스키마 참조 섹션에 나열된 검색 관련 [작업이](#resource-log-search-ops) 포함될 수 있습니다.

Azure Cognitive Search 유용한 Kusto 쿼리의 예는 Azure Cognitive Search [모니터링](monitor-azure-cognitive-search.md) 및 Azure Cognitive Search [성능 분석을](search-performance-analysis.md)참조하세요.

## <a name="activity-logs"></a>활동 로그

다음 표에서는 Azure 활동 로그에서 만들 수 있는 Azure Cognitive Search 관련된 일반적인 작업을 나열합니다.

| 작업(Operation) | Description |
|:----------|:------------|
| 관리자 키 받기 | 관리 권한이 필요한 모든 작업은 "관리자 키 얻기" 작업으로 기록됩니다.  |
| 쿼리 키 가져오기 | 인덱스의 문서 컬렉션에 대한 읽기 전용 작업입니다.  |
| 관리자 키 다시 생성 | 기본 또는 보조 관리자 API 키를 다시 생성하기 위한 요청입니다. |

공통 항목에는 API 키에 대한 참조(*관리자 키 가져오기* 및 *쿼리 키 가져오기* 와 같은 일반 정보 알림)가 포함됩니다. 이러한 활동은 관리자 키(개체 만들기 또는 삭제) 또는 쿼리 키를 사용하여 수행된 요청을 나타내지만 요청 자체를 표시하지는 않습니다. 이러한 시간 조직에 대한 자세한 내용은 진단 로깅을 구성해야 합니다. 

또는 변경 기록을 통해 몇 가지 인사이트를 얻을 수 있습니다. Azure Portal 활동을 선택하여 세부 정보 페이지를 연 다음, 기본 작업에 대한 정보를 위해 "기록 변경"을 선택합니다.

활동 로그 항목의 스키마에 대한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요.

## <a name="schemas"></a>스키마

Azure Cognitive Search 사용 중인 스키마는 다음과 같습니다. 쿼리 또는 사용자 지정 보고서를 작성하는 경우 Azure Cognitive Search 리소스 로그를 포함하는 데이터 구조는 아래 스키마를 준수합니다.

Blob Storage로 전송된 리소스 로그의 경우 각 Blob에는 로그 개체 배열을 포함하는 **레코드라는** 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

<a name="resource-log-schema"></a>

### <a name="resource-log-schema"></a>리소스 로그 스키마

Azure Monitor 통해 사용할 수 있는 모든 리소스 로그는 [공통 최상위 스키마 를](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)공유합니다. Azure Cognitive Search 검색 서비스와 관련한 [추가 속성](#resource-log-search-props) 및 [작업으로](#resource-log-search-ops) 보완합니다.

리소스 로그에는 다음 예제와 같이 일반적인 속성과 검색 관련 속성의 조합이 포함됩니다.

| 이름 | Type | 설명 및 예 |
| ---- | ---- | ----------------------- |
| TimeGenerated | DateTime | 작업의 타임스탬프입니다. 예: `2021-12-07T00:00:43.6872559Z` |
| 리소스 | String | 리소스 ID입니다. 예: `/subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group-name>/providers/Microsoft.Search/searchServices/<your-search-service-name>` |
| 범주 | String | "OperationLogs". 이 값은 상수입니다. OperationLogs는 리소스 로그에 사용되는 유일한 범주입니다. |
| OperationName | String |  작업의 이름입니다(아래 [작업의 전체 목록](#resource-log-search-ops) 참조). 예를 들면 다음과 같습니다. `Query.Search` |
| OperationVersion | 문자열 | 요청에 사용되는 api-version입니다. 예: `2020-06-30` |
| ResultType | String |"Success". 가능한 값: Success 또는 Failure |
| ResultSignature | Int | HTTP 결과 코드입니다. 예: `200` |
| DurationMS | Int | 작업 기간(밀리초)입니다. |
| 속성 |Object | 작업별 데이터를 포함하는 개체입니다. 다음 속성 스키마 테이블을 참조하세요.|

<a name="resource-log-search-props"></a>

#### <a name="properties-schema"></a>속성 스키마

아래 속성은 Azure Cognitive Search에만 적용됩니다.

| 이름 | Type | 설명 및 예 |
| ---- | ---- | ----------------------- |
| Description_s | 문자열 | 작업의 엔드포인트입니다. 예: `GET /indexes('content')/docs` |
| Documents_d | Int | 처리된 문서 수입니다. |
| IndexName_s | String | 작업과 연결된 인덱스의 이름입니다. |
| Query_s | 문자열 | 요청에 사용되는 쿼리 매개 변수입니다. 예: `?search=beach access&$count=true&api-version=2020-06-30` |

<a name="resource-log-search-ops"></a>

#### <a name="operationname-values-logged-operations"></a>OperationName 값(기록된 작업)

OperationName에 대해 다음 작업을 지정합니다.

| OperationName | Description |
|:------------- |:------------|
| Indexing.Index  | 이 작업은 [문서를 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)하는 호출입니다. |
| Indexers.Create | 데이터 가져오기 마법사를 통해 명시적으로 또는 암시적으로 인덱서를 만듭니다. |
| Indexers.Get | 인덱서가 실행될 때마다 인덱서의 이름을 반환합니다. |
| Indexers.Status | 인덱서가 실행될 때마다 인덱서의 상태를 반환합니다. |
| DataSources.Get | 인덱서가 실행될 때마다 데이터 원본의 이름을 반환합니다.|
| Indexes.Get | 인덱서가 실행될 때마다 인덱스의 이름을 반환합니다. |
| indexes.Prototype | 이는 데이터 가져오기 마법사에서 만든 인덱스입니다. |
| Query.Autocomplete |  인덱스를 위한 자동 완성 쿼리입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Lookup |  인덱스를 위한 조회 쿼리입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Search |  인덱스를 위한 전체 텍스트 검색 요청입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Suggest |  인덱스를 미리 쿼리합니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| ServiceStats | 이 작업은 로드되거나 새로 고쳐질 때 포털 개요 페이지를 채우도록 직접 또는 암시적으로 호출되는 [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)에 대한 루틴 호출입니다. |

## <a name="see-also"></a>참고 항목

+ 개념 및 [지침은 모니터링 Azure Cognitive Search](monitor-azure-cognitive-search.md) 참조하세요.

+ Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
