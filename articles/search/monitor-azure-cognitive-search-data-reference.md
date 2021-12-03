---
title: Azure Cognitive Search 모니터링 데이터 참조
description: Azure Cognitive Search의 모니터링 데이터에 대 한 로그 및 메트릭 참조입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: reference
ms.date: 12/01/2021
ms.custom: subject-monitoring
ms.openlocfilehash: fa191ba0891530758b831cb4e452b30c2593d77a
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517784"
---
# <a name="azure-cognitive-search-monitoring-data-reference"></a>Azure Cognitive Search 모니터링 데이터 참조

이 문서에서는 Azure Cognitive Search의 성능 및 가용성을 분석 하기 위해 수집 된 로그 및 메트릭 데이터에 대 한 참조를 제공 합니다. 검색 서비스에 대 한 모니터링 데이터를 수집 하 고 분석 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search 모니터링](monitor-azure-cognitive-search.md) 을 참조 하세요.

## <a name="metrics"></a>메트릭

이 섹션에서는 Azure Cognitive Search ([Microsoft. 검색/searchServices](../azure-monitor/essentials/metrics-supported.md#microsoftsearchsearchservices))에 대해 수집 된 플랫폼 메트릭을 나열 합니다.  

| 메트릭 ID | 단위 | 설명 |
|:-------|:-----|:------------|
| DocumentsProcessedCount | 개수 | 인덱서 또는 문서를 직접 푸시하여 인덱싱 작업에서 성공적으로 처리 된 총 문서 수입니다. |
| SearchLatency | 초 | 검색 서비스에서 실행 되는 쿼리에 대 한 평균 검색 대기 시간입니다. |
| SearchQueriesPerSecond | 초당 개수 | 검색 서비스에 대 한 QPS (초당 검색 쿼리)의 평균입니다. 쿼리가 실행 되는 데 일반적으로 밀리초 단위로 계산 되므로 초 단위로 측정 되는 쿼리만 QPS와 같은 메트릭에 표시 됩니다. </br>최소값은 해당 분 동안 등록 된 초당 검색 쿼리의 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분 동안의 집계입니다. 예를 들어 1분 내에 다음과 같은 패턴을 발견할 수 있습니다. SearchQueriesPerSecond에 대한 최대 로드 1초, 이후 평균 로드 58초, 그리고 마지막으로 최솟값인 한 개의 쿼리만 있는 1초가 이어집니다.|
| SkillExecutionCount | 개수 | 인덱서 작업 중에 처리 된 총 기술 실행 수입니다. |
| ThrottledSearchQueriesPercentage | 백분율 | 1 분 간격 동안 실행 된 총 검색 쿼리 수에서 제한 된 검색 쿼리의 평균 백분율입니다.|

참조는 [Azure Monitor에서 지원 되는 모든 플랫폼 메트릭](/azure/azure-monitor/platform/metrics-supported)목록을 참조 하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭의 차원은 메트릭 값을 설명 하기 위해 추가 데이터를 전달 하는 이름/값 쌍입니다. Azure Cognitive Search에는 실행 된 문서 또는 기술 수 ("처리 된 문서 수" 및 "기술 실행 호출 수")를 캡처하는 메트릭과 관련 된 다음과 같은 차원이 있습니다.

| 차원 이름 | Description |
| -------------- | ----------- |
| **DataSourceName** | 인덱서 실행 중에 사용 되는 명명 된 데이터 원본 연결입니다. 유효한 값은 [지원 되는 데이터 원본 유형](search-indexer-overview.md#supported-data-sources)중 하나입니다. |
| **실패** | 인스턴스가 실패 했는지 여부를 나타냅니다. |
| **IndexerName** | 인덱서의 이름입니다.|
| **IndexName** | 인덱스의 이름입니다.|
| **SkillsetName** | 인덱서 실행 중에 사용 되는 기술의 이름입니다. |
| **SkillName** | 기술 내의 기술 이름입니다. |
| **SkillType** | @odata.type기술에 대 한입니다. |

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

## <a name="resource-logs"></a>리소스 로그

[리소스 로그](../azure-monitor/essentials/resource-logs.md) 는 Azure 리소스 내에서 수행 된 작업에 대 한 통찰력을 제공 하는 플랫폼 로그입니다. 리소스 로그는 검색 서비스에서 자동으로 생성 되지만 기본적으로 수집 되지 않습니다. Log Analytics 작업 영역으로 리소스 로그를 전송 하는 진단 설정을 만들어 Azure Monitor 로그, Event Hubs azure 외부에서 전달 하는 데 사용 하거나, 보관을 위해 Azure Storage 합니다.

이 섹션에서는 Azure Cognitive Search에 대해 수집할 수 있는 리소스 로그의 유형 또는 범주를 식별 합니다.

+ 리소스 로그는 유형 또는 범주별로 그룹화 됩니다. Azure Cognitive Search는 [**작업 범주**](../azure-monitor/essentials/resource-logs-categories.md#microsoftsearchsearchservices)아래에 리소스 로그를 생성 합니다.

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](/azure/azure-monitor/platform/resource-logs-schema) 목록을 참조하세요.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

[Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md) 는 모니터링 되는 리소스에서 로그 및 성능 데이터를 수집 하 고 구성 하는 Azure Monitor의 기능입니다. Log Analytics에 대 한 진단 설정을 구성한 경우 Azure Cognitive Search에서 생성 된 리소스 로그에 대 한 Azure Monitor Logs 테이블을 쿼리할 수 있습니다.

이 섹션에서는 Azure Cognitive Search와 관련 된 모든 Azure Monitor 로그를 참조 하 고 Azure Portal에서 Log Analytics 하 고 메트릭 탐색기 하 여 쿼리에 사용할 수 있습니다.

| 테이블 | 설명 |
|-------|-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) | 제어 평면 작업에 대 한 통찰력을 제공 하는 Azure 활동 로그의 항목입니다. 복제본 및 파티션 추가 또는 제거와 같이 제어 평면에서 호출 되는 작업은 "관리자 키 가져오기" 작업을 통해 표시 됩니다. |
| [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) | 기록 쿼리 및 인덱싱 작업.|
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | 상태 및 성능을 측정 하는 Azure Cognitive Search에서 내보내는 메트릭 데이터입니다. |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype#search-services)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블

Azure Cognitive Search는 [**Azure 진단**](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블을 사용 하 여 검색 서비스의 쿼리와 관련 된 리소스 로그를 수집 합니다.

Log Analytics의이 테이블에 대 한 쿼리에는 공용 속성, [검색 관련 속성](#resource-log-search-props)및 스키마 참조 섹션에 나열 된 [검색 관련 작업이](#resource-log-search-ops) 포함 될 수 있습니다.

Azure Cognitive Search에 유용한 Kusto 쿼리 예제는 azure [Cognitive Search 모니터링](monitor-azure-cognitive-search.md) 및 [azure Cognitive Search에서 성능 분석](search-performance-analysis.md)을 참조 하세요.

## <a name="activity-logs"></a>활동 로그

다음 표에서는 azure 활동 로그에서 만들 수 있는 Azure Cognitive Search와 관련 된 일반적인 작업을 보여 줍니다.

| 작업(Operation) | Description |
|:----------|:------------|
| 관리자 키 가져오기 | 관리 권한이 필요한 모든 작업은 "관리자 키 가져오기" 작업으로 기록 됩니다.  |
| 쿼리 키 가져오기 | 인덱스의 문서 컬렉션에 대 한 읽기 전용 작업입니다.  |
| 관리자 키 다시 생성 | 기본 또는 보조 관리 API 키를 다시 생성 하는 요청입니다. |

공통 항목에는 API 키에 대한 참조(*관리자 키 가져오기* 및 *쿼리 키 가져오기* 와 같은 일반 정보 알림)가 포함됩니다. 이러한 활동은 관리자 키(개체 만들기 또는 삭제) 또는 쿼리 키를 사용하여 수행된 요청을 나타내지만 요청 자체를 표시하지는 않습니다. 이러한 시간 조직에 대한 자세한 내용은 진단 로깅을 구성해야 합니다. 

또는 변경 기록을 통해 몇 가지 정보를 얻을 수 있습니다. Azure Portal에서 작업을 선택 하 여 세부 정보 페이지를 연 다음 "변경 기록"을 선택 하 여 기본 작업에 대 한 정보를 선택 합니다.

활동 로그 항목의 스키마에 대한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요.

## <a name="schemas"></a>스키마

Azure Cognitive Search에서 다음 스키마를 사용 하 고 있습니다. 쿼리나 사용자 지정 보고서를 작성 하는 경우 Azure Cognitive Search 리소스 로그가 포함 된 데이터 구조는 아래 스키마를 따릅니다.

Blob 저장소로 전송 되는 리소스 로그의 경우 각 blob에는 로그 개체의 배열을 포함 하는 **레코드** 라는 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

<a name="resource-log-schema"></a>

### <a name="resource-log-schema"></a>리소스 로그 스키마

Azure Monitor 통해 사용할 수 있는 모든 리소스 로그는 [공통 최상위 스키마 를](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)공유합니다. Azure Cognitive Search 검색 서비스에 고유한 [추가 속성](#resource-log-search-props) 및 [작업으로](#resource-log-search-ops) 보완합니다.

다음 예제에서는 공용 속성(TimeGenerated, Resource, Category 등) 및 검색 관련 속성(OperationName 및 OperationVersion)을 포함하는 리소스 로그를 보여 줍니다.

| Name | Type | 설명 및 예 |
| ---- | ---- | ----------------------- |
| TimeGenerated | DateTime | 작업의 타임스탬프입니다. 예를 들면 다음과 같습니다. `2021-12-07T00:00:43.6872559Z` |
| 리소스 | String | 리소스 ID입니다. 예를 들면 다음과 같습니다. `/subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group-name>/providers/Microsoft.Search/searchServices/<your-search-service-name>` |
| 범주 | String | "OperationLogs". 이 값은 상수입니다. OperationLogs는 리소스 로그에 사용되는 유일한 범주입니다. |
| OperationName | String |  작업의 이름입니다(아래 [작업의 전체 목록](#resource-log-search-ops) 참조). 예를 들면 다음과 같습니다. `Query.Search` |
| OperationVersion | String | 요청에 사용되는 api-version입니다. 예를 들면 다음과 같습니다. `2020-06-30` |
| ResultType | String |"Success". 기타 가능한 값: 성공 또는 실패 |
| ResultSignature | Int | HTTP 결과 코드입니다. 예를 들면 다음과 같습니다. `200` |
| DurationMS | Int | 작업 기간(밀리초)입니다. |
| 속성 |Object | 작업별 데이터를 포함하는 개체입니다. 다음 속성 스키마 테이블을 참조하세요.|

<a name="resource-log-search-props"></a>

#### <a name="properties-schema"></a>속성 스키마

아래 속성은 Azure Cognitive Search에만 적용됩니다.

| Name | Type | 설명 및 예 |
| ---- | ---- | ----------------------- |
| Description_s | String | 작업의 엔드포인트입니다. 예를 들면 다음과 같습니다. `GET /indexes('content')/docs` |
| Documents_d | Int | 처리된 문서 수입니다. |
| IndexName_s | String | 작업과 연결된 인덱스의 이름입니다. |
| Query_s | String | 요청에 사용되는 쿼리 매개 변수입니다. 예를 들면 다음과 같습니다. `?search=beach access&$count=true&api-version=2020-06-30` |

<a name="resource-log-search-ops"></a>

#### <a name="operationname-values-logged-operations"></a>OperationName 값(기록된 작업)

아래 작업은 리소스 로그에 표시할 수 있습니다.

| OperationName | 설명 |
|:------------- |:------------|
| DataSources.*  | 인덱서 데이터 원본에 적용됩니다. 만들기, 삭제, 가져오기, 목록일 수 있습니다.  |
| DebugSessions.* | 디버그 세션에 적용됩니다. 만들기, 삭제, 가져오기, 나열, 시작 및 상태일 수 있습니다. |
| DebugSessions.DocumentStructure | 보강된 문서는 디버그 세션에 로드됩니다. |
| DebugSessions.RetrieveIndexerExecutionHistoricalData | 인덱서 실행 세부 정보 요청입니다. |
| DebugSessions.RetrieveProjectedIndexerExecutionHistoricalData  | 지식 저장소에 프로젝션된 보강에 대한 실행 기록입니다. |
| Indexers.* | 인덱서에 적용됩니다. 만들기, 삭제, 가져오기, 나열 및 상태일 수 있습니다. |
| 인덱스* | 검색 인덱스에 적용됩니다. 만들기, 삭제, 가져오기, 목록일 수 있습니다.  |
| indexes.Prototype | 이는 데이터 가져오기 마법사에서 만든 인덱스입니다. |
| Indexing.Index  | 이 작업은 [문서를 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)하는 호출입니다. |
| Metadata.GetMetadata | 검색 서비스 시스템 데이터에 대한 요청입니다.  |
| Query.Autocomplete | 인덱스를 위한 자동 완성 쿼리입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Lookup |  인덱스를 위한 조회 쿼리입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Search |  인덱스를 위한 전체 텍스트 검색 요청입니다. [쿼리 형식 및 컴퍼지션 을](search-query-overview.md)참조하세요. |
| Query.Suggest |  인덱스를 미리 쿼리합니다. [쿼리 형식 및 컴퍼지션](search-query-overview.md)을 참조 하세요. |
| ServiceStats | 이 작업은 로드되거나 새로 고쳐질 때 포털 개요 페이지를 채우도록 직접 또는 암시적으로 호출되는 [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)에 대한 루틴 호출입니다. |
| 기술력과. * | 기술에 적용 됩니다. 만들기, 삭제, 가져오기, 나열할 수 있습니다. |

## <a name="see-also"></a>추가 정보

+ 개념 및 지침은 [Azure Cognitive Search 모니터링](monitor-azure-cognitive-search.md) 을 참조 하세요.

+ Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
