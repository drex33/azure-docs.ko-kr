---
title: 로그 데이터 수집
titleSuffix: Azure Cognitive Search
description: 진단 설정을 사용하도록 설정하여 로그 데이터를 수집하고 분석한 다음, Kusto 쿼리 언어를 사용하여 결과를 탐색합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5643f9a8bb1caec8d264ba5dfba9913d5d13b2ec
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414848"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 로그 데이터를 수집 및 분석

진단 또는 운영 로그는 Azure Cognitive Search의 자세한 작업에 대한 인사이트를 제공하고 서비스 상태 및 프로세스를 모니터링하는 데 유용합니다. 내부적으로 Microsoft는 지원 티켓을 제출하는 경우 조사 및 분석에 충분한 정도의 짧은 시간 동안(약 30일) 백 엔드에 대한 시스템 정보를 유지합니다. 그러나, 작동 데이터에 대한 소유권을 원할 경우 로깅 정보가 수집되는 위치를 지정하는 진단 설정을 구성해야 합니다.

진단 로깅은 [Azure Monitor](../azure-monitor/index.yml)에 대한 백 엔드 통합을 통해 사용할 수 있습니다. 진단 로깅을 설정하면 로그를 유지하기 위해 스토리지 옵션을 지정하라는 메시지가 표시됩니다. 다음 표에는 옵션이 열거되어 있습니다.

| 리소스 | 사용 대상 |
|----------|----------|
| [Log Analytics 작업 영역으로 보내기](../azure-monitor/essentials/tutorial-resource-logs.md) | 이벤트 및 메트릭은 Log Analytics 작업 영역으로 전송되며, 자세한 정보를 반환하기 위해 포털에서 쿼리할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요. |
| [Blob Storage 보관](../storage/blobs/storage-blobs-overview.md) | 이벤트 및 메트릭은 Blob 컨테이너에 보관되고 JSON 파일로 저장됩니다. 로그는 상당히 세분화되어(시간/분 단위로) 특정 인시던트를 조사하는 데 유용하지만 개방형 조사에는 유용하지 않습니다. JSON 편집기를 사용하여 원시 로그 파일을 보거나 Power BI를 사용하여 로그 데이터를 집계 및 시각화할 수 있습니다.|
| [이벤트 허브로 스트리밍](../event-hubs/index.yml) | 이벤트 및 메트릭은 Azure Event Hubs 서비스로 스트리밍됩니다. 이 서비스는 매우 큰 로그에 대한 대체 데이터 수집 서비스로 선택합니다. |

## <a name="prerequisites"></a>필수 구성 요소

진단 로깅을 구성할 때 하나 이상의 항목을 선택할 수 있도록 리소스를 미리 만듭니다.

+ [Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)

+ [스토리지 계정을 만드는](../storage/common/storage-account-create.md)

+ [이벤트 허브 만들기](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>데이터 수집 사용

진단 설정은 기록된 이벤트 및 메트릭을 수집하는 방법을 지정합니다.

1. **모니터링** 아래에서 **진단 설정** 을 선택합니다.

   ![진단 설정](./media/search-monitor-usage/diagnostic-settings.png "진단 설정")

1. **+ 진단 설정 추가** 를 선택합니다

1. **Log Analytics** 를 확인하고 작업 영역을 선택한 후 **OperationLogs** 및 **AllMetrics** 을 선택합니다.

   ![데이터 수집 구성](./media/search-monitor-usage/configure-storage.png "데이터 수집 구성")

1. 설정을 저장합니다.

1. 로깅이 사용하도록 설정된 후 검색 서비스는 로그와 메트릭 생성을 시작합니다. 로그된 이벤트와 메트릭을 사용할 수 있게 되기까지는 시간이 걸릴 수 있습니다.

Log Analytics의 경우 데이터를 사용할 수 있을 때까지 몇 분 정도 기다려야 합니다. 이후 Kusto 쿼리를 실행하여 데이터를 반환할 수 있습니다. 자세한 내용은 [쿼리 요청 모니터링](search-monitor-logs.md)을 참조하세요.

Blob Storage의 경우, 컨테이너가 Blob Storage에 표시될 때까지 1시간이 걸립니다. 시간 및 컨테이너당 하나의 Blob가 있습니다. 기록하거나 측정하는 활동이 있는 경우에만 컨테이너가 만들어집니다. 데이터가 스토리지 계정에 복사되면 해당 데이터가 JSON 형식으로 다음 두 컨테이너에 배치됩니다.

+ insights-logs-operationlogs: 검색 트래픽 로그인 경우
+ insights-metrics-pt1m: 메트릭인 경우

## <a name="query-log-information"></a>로그 정보 쿼리

두 테이블에는 Azure Cognitive Search인 **AzureDiagnostics** 및 **AzureMetrics** 에 대한 로그 및 메트릭이 포함되어 있습니다.

1. **모니터링** 에서 **로그** 를 선택합니다.

1. 쿼리 창에서 **AzureMetrics** 를 입력하고 범위(검색 서비스)와 시간 범위를 확인한 다음, **실행** 을 클릭하여 이 테이블에 수집된 데이터를 파악합니다.

   메트릭 및 값을 보려면 테이블을 스크롤합니다. 위쪽에 있는 레코드 수에 유의하세요. 서비스가 잠시 메트릭을 수집했다면 시간 간격을 조정하여 관리 가능한 데이터 세트를 가져올 수 있습니다.

   ![AzureMetrics 테이블](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 테이블")

1. 다음 쿼리를 입력하여 테이블 형식 결과 집합을 반환합니다.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. **AzureDiagnostics** 부터 이전 단계를 반복하여 모든 열을 정보용으로 반환한 다음, 더 흥미로운 정보를 추출하는 보다 선택적 쿼리를 수행합니다.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics 테이블](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics 테이블")

## <a name="kusto-query-examples"></a>Kusto 쿼리 예제

진단 로깅을 사용하도록 설정한 경우 서비스에서 실행된 작업 목록 및 시점에 대해 **AzureDiagnostics** 를 쿼리할 수 있습니다. 작업을 상호 연결하여 성능 변화를 조사할 수도 있습니다.

#### <a name="example-list-operations"></a>예제: 작업 나열 

작업 목록 및 각 작업의 수를 반환합니다.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>예제: 작업 상호 연결

쿼리 요청을 인덱싱 작업과 상호 연결하고 시간 차트에 걸쳐 데이터 요소를 렌더링하여 작업이 일치하는지 확인합니다.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(durationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>기록된 작업

Azure Monitor에서 캡처한 기록된 이벤트에는 인덱싱 및 쿼리와 관련된 이벤트가 포함됩니다. Log Analytics의 **AzureDiagnostics** 테이블은 쿼리 및 인덱싱과 관련된 작동 데이터를 수집합니다.

| OperationName | 설명 |
|---------------|-------------|
| ServiceStats | 이 작업은 로드되거나 새로 고쳐질 때 포털 개요 페이지를 채우도록 직접 또는 암시적으로 호출되는 [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)에 대한 루틴 호출입니다. |
| Query.Search |  인덱스에 대한 요청을 쿼리합니다. 기록된 쿼리에 대한 자세한 내용은 [쿼리 모니터링](search-monitor-queries.md)을 참조하세요.|
| Indexing.Index  | 이 작업은 [문서를 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)하는 호출입니다. |
| indexes.Prototype | 이는 데이터 가져오기 마법사에서 만든 인덱스입니다. |
| Indexers.Create | 데이터 가져오기 마법사를 통해 명시적으로 또는 암시적으로 인덱서를 만듭니다. |
| Indexers.Get | 인덱서가 실행될 때마다 인덱서의 이름을 반환합니다. |
| Indexers.Status | 인덱서가 실행될 때마다 인덱서의 상태를 반환합니다. |
| DataSources.Get | 인덱서가 실행될 때마다 데이터 원본의 이름을 반환합니다.|
| Indexes.Get | 인덱서가 실행될 때마다 인덱스의 이름을 반환합니다. |

## <a name="log-schema"></a>로그 스키마

사용자 지정 보고서를 작성하는 경우 Azure Cognitive Search 로그 데이터를 포함하는 데이터 구조는 아래 스키마를 준수합니다. Blob Storage의 경우 각 Blob에 로그 개체의 배열이 포함된 **records** 라는 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

다음 표는 리소스 로깅에 공통적인 필드의 일부 목록입니다.

| Name | Type | 예제 | 참고 |
| --- | --- | --- | --- |
| timeGenerated |Datetime |"2018-12-07T00:00:43.6872559Z" |작업 타임스탬프 |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId |
| operationName |문자열 |"Query.Search" |작업 이름 |
| operationVersion |문자열 |"2020-06-30" |사용된 api-version |
| category |문자열 |"OperationLogs" |constant |
| resultType |문자열 |"Success" |가능한 값: Success 또는 Failure |
| resultSignature |int |200 |HTTP 결과 코드 |
| durationMS |int |50 |밀리초 단위의 작업 기간 |
| properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체 |

### <a name="properties-schema"></a>속성 스키마

아래 속성은 Azure Cognitive Search에만 적용됩니다.

| 이름 | Type | 예제 | 참고 |
| --- | --- | --- | --- |
| Description_s |문자열 |"GET /indexes('content')/docs" |작업의 엔드포인트 |
| Documents_d |int |42 |처리된 문서 수 |
| IndexName_s |문자열 |"test-index" |작업과 연결된 인덱스의 이름 |
| Query_s |문자열 |"?search=AzureSearch&$count=true&api-version=2020-06-30" |쿼리 매개 변수 |

## <a name="metrics-schema"></a>메트릭 스키마

메트릭은 쿼리 요청에 대해 캡처되고 1분 간격으로 측정됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다. 자세한 내용은 [쿼리 요청 모니터링](search-monitor-queries.md)을 참조하세요.

| Name | Type | 예제 | 참고 |
| --- | --- | --- | --- |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |리소스 ID |
| metricName |문자열 |"Latency" |메트릭 이름 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
| average |int |64 |메트릭에 따라 메트릭 시간 간격에 원시 샘플의 평균 값(초 단위 또는 백분율 단위) |
| minimum |int |37 |메트릭 시간 간격에 원시 샘플의 최소 값(초 단위) |
| maximum |int |78 |메트릭 시간 간격에 원시 샘플의 최대 값(초 단위)  |
| total |int |258 |메트릭 시간 간격에 원시 샘플의 전체 값(초 단위)  |
| count |int |4 |1분간 노드에서 로그로 내보내진 메트릭 수  |
| timegrain |문자열 |"PT1M" |ISO 8601에서 메트릭의 시간 조직 |

일반적으로 쿼리는 밀리초 단위로 실행되므로 초 단위 시간으로 측정되는 쿼리만 QPS와 같은 메트릭에 표시됩니다.

**초당 검색 쿼리** 메트릭의 경우, 최소값은 해당 분 동안 등록된 초당 검색 쿼리 수에 대한 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다. 예를 들어, 1분 내에 다음과 같은 패턴을 발견할 수 있습니다. SearchQueriesPerSecond에 대한 최대 로드 1초, 이후 평균 로드 58초, 그리고 마지막으로 최소값인 한 개의 쿼리만 있는 1초가 이어집니다.

**제한된 검색 쿼리 백분율** 의 경우 최소, 최대, 평균 및 합계가 모두 다음과 같은 값을 가집니다. 1분 동안 검색 쿼리의 총 수에서 제한된 검색 쿼리가 차지하는 비율입니다.

## <a name="view-raw-log-files"></a>원시 로그 파일 보기

Blob Storage는 로그 파일을 보관하는 데 사용됩니다. JSON 편집기를 사용하여 로그 파일을 볼 수 있습니다. 없는 경우 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하는 것이 좋습니다.

1. Azure Portal에서 Storage 계정을 엽니다. 

2. 왼쪽 탐색 창에서 **Blob** 을 클릭합니다. **sights-logs-operationlogs** 및 **inights-metrics-pt1m** 이 표시됩니다. 이러한 컨테이너는 로그 데이터를 Blob Storage로 내보낼 때 Azure Cognitive Search에서 만들어집니다.

3. .json 파일에 도달할 때까지 폴더 계층 구조를 따라 아래로 클릭합니다.  상황에 맞는 메뉴를 사용하여 파일을 다운로드합니다.

파일이 다운로드되면 JSON 편집기에서 파일을 열어 내용을 봅니다.

## <a name="next-steps"></a>다음 단계

검색 서비스 모니터링의 기본 사항을 아직 검토하지 않았다면 관련 내용을 검토하여 모든 감독 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에서 작업 및 활동 모니터링하기](search-monitor-usage.md)