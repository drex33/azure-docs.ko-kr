---
title: Azure Cognitive Search 모니터링
description: 리소스 로깅을 사용하도록 설정하고, 쿼리 메트릭, 리소스 사용량 및 Azure Cognitive Search 서비스에 대한 기타 시스템 데이터를 얻습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2021
ms.custom: subject-monitoring
ms.openlocfilehash: bc8af84bf571a02f478ebe81ee3ca216c89532f4
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133129725"
---
# <a name="monitoring-azure-cognitive-search"></a>모니터링 Azure Cognitive Search

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Cognitive Search 생성된 모니터링 데이터와 Azure Monitor 사용하여 이 데이터를 분석하고 경고하는 방법을 설명합니다.

## <a name="monitor-overview"></a>모니터링 개요

Azure Portal 각 Azure Cognitive Search 서비스에 대한 **개요** 페이지의 **모니터링** 탭에는 쿼리 볼륨, 대기 시간 및 제한된 쿼리를 비롯한 주요 메트릭에 대한 간략한 요약이 포함되어 있습니다. 이 데이터는 자동으로 수집되며 리소스를 만드는 즉시 분석에 사용할 수 있습니다.

이러한 메트릭은 유용하지만 Azure Cognitive Search 사용할 수 있는 모니터링 및 진단 데이터의 하위 집합입니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다. 추가 데이터 수집은 Azure Monitor 통해 지원됩니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Cognitive Search Azure 리소스를 모니터링하는 전체 기능 집합을 제공하는 Azure의 전체 스택 모니터링 서비스인 Azure Monitor [사용하여](../azure-monitor/overview.md) 모니터링 데이터를 만듭니다.

Azure 서비스를 모니터링하는 방법을 잘 모르는 경우 다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md) 문서를 먼저 참조하세요.

* Azure Monitor 무엇이며 다른 Azure 서비스에 대한 포털에 통합되는 방법
* Azure 리소스에 대해 Azure Monitor 수집한 데이터 형식
* 데이터를 수집하고 분석하는 데 사용되는 Azure Monitor 도구

다음 섹션은 Azure Cognitive Search 수집된 특정 데이터를 설명하고 Azure 도구를 사용하여 데이터 수집을 구성하고 이 데이터를 분석하는 예제를 제공하여 이 문서를 기반으로 합니다.

## <a name="monitoring-data"></a>데이터 모니터링

Azure Cognitive Search Azure 리소스에서 데이터 모니터링에 설명된 다른 Azure 리소스와 동일한 종류의 [모니터링 데이터를 수집합니다.](../azure-monitor/essentials/monitor-azure-resource.md) Azure Cognitive Search 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 [데이터 참조를](monitor-azure-cognitive-search-data-reference.md) 참조하세요.

통계, 개수 및 상태를 포함하여 검색 서비스 자체에서 시스템 데이터를 가져올 수도 있습니다.

* [서비스 통계(REST)](/rest/api/searchservice/get-service-statistics)
* [인덱스 통계(REST)](/rest/api/searchservice/get-index-statistics)
* [문서 수(REST)](/rest/api/searchservice/count-documents)
* [인덱서 상태(REST)](/rest/api/searchservice/get-indexer-status)

위의 정보는 Azure Portal 읽을 수도 있습니다. REST 호출의 경우 [관리자 API 키와](search-security-api-keys.md) [Postman](search-get-started-rest.md) 또는 다른 REST 클라이언트를 사용합니다.

Azure Portal 페이지에서 사용량 및 모니터링 탭에서 개수 및 메트릭을 확인합니다. 왼쪽 탐색의 명령은 구성 및 데이터 탐색 페이지에 대한 액세스를 제공합니다.

  ![검색 서비스에 Azure Monitor 통합](./media/search-monitor-usage/azure-monitor-search.png
 "검색 서비스에 Azure Monitor 통합")

> [!NOTE]
> Cognitive Search 검색 데이터에 대한 개별 사용자 액세스를 모니터링하지 않습니다(문서 수준 또는 행 수준 액세스라고도 함). 인덱싱 및 쿼리 요청은 요청에 대한 관리자 또는 쿼리 API 키를 제공하는 클라이언트 애플리케이션에서 시작됩니다.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다. 로그 데이터 수집 및 보존은 사용량 기반 가격 책정에 따라 청구됩니다. 진단 설정을 만들 때까지 청구가 시작되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](/azure/azure-monitor/platform/diagnostic-settings)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. *Azure Cognitive Search* 범주는 [Azure Cognitive Search 모니터링 데이터 참조](monitor-azure-cognitive-search-data-reference.md#resource-logs)에 나열됩니다.

> [!Important]
> 이러한 설정을 사용하려면 추가 Azure 서비스(스토리지 계정, 이벤트 허브 또는 Log Analytics)가 필요하므로 비용이 늘어날 수 있습니다. 자세한 내용은 [Azure Monitor 가격 책정을](https://azure.microsoft.com/pricing/details/monitor/#pricing) 참조하세요.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

Azure Monitor 메뉴에서 메트릭을 열어 메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 **Azure Cognitive Search** **메트릭을** 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](/azure/azure-monitor/essentials/metrics-getting-started)을 참조하세요.

Azure Cognitive Search 대해 수집된 플랫폼 메트릭 목록은 Azure Cognitive Search 모니터링 [데이터 참조(메트릭)를 참조하세요.](monitor-azure-cognitive-search-data-reference.md#metrics)  

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](/azure/azure-monitor/essentials/resource-logs-schema)에서 설명합니다. Azure Cognitive Search 리소스 로그에 대한 확장 스키마는 [Azure Cognitive Search 모니터링 데이터 참조(스키마)에 있습니다.](monitor-azure-cognitive-search-data-reference.md#schemas)

[활동 로그는](/azure/azure-monitor/essentials/activity-log) 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure 내의 플랫폼 로그 유형입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Azure Cognitive Search 대해 수집된 리소스 로그 유형 목록은 [Azure Cognitive Search 모니터링 데이터 참조(리소스 로그)를 참조하세요.](monitor-azure-cognitive-search-data-reference.md#resource-logs)

Azure Monitor Logs에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [Azure Cognitive Search 모니터링 데이터 참조(Azure Monitor 로그 테이블)를 참조하세요.](monitor-azure-cognitive-search-data-reference.md#azure-monitor-logs-tables)

> [!NOTE]
> 포털 페이지는 몇 분 마다 새로고침되고, 보고된 숫자는 근사값이며, 시스템에서 요청을 처리하는 정도에 대한 일반적 개념을 제공하기 위한 것입니다. 초당 쿼리 수(QPS)와 같은 실제 메트릭은 페이지에 표시되는 숫자보다 크거나 작을 수 있습니다. 정밀도가 요구 사항인 경우 API를 사용하는 것이 좋습니다.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> Azure Cognitive Search 메뉴에서 **로그를** 선택하면 쿼리 범위가 현재 검색 서비스로 설정된 상태로 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 검색 서비스의 데이터 또는 다른 Azure 서비스의 데이터가 포함된 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그를** 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/logs/scope)를 참조하세요.

다음은 검색 서비스를 모니터링하는 데 사용할 수 있는 쿼리입니다.

#### <a name="list-metrics-by-name"></a>이름별 메트릭 나열

메트릭 목록 및 연결된 집계를 반환합니다. 쿼리의 범위는 지정한 시간 범위에 따라 현재 검색 서비스로 지정됩니다.

```kusto
AzureMetrics
| project MetricName, Total, Count, Maximum, Minimum, Average
```

#### <a name="list-operations-by-name"></a>이름별 작업 나열

작업 목록 및 각 작업의 수를 반환합니다.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="long-running-queries"></a>장기 실행 쿼리

AzureDiagnostics에 대한 이 Kusto 쿼리는 `Query.Search` 지속 시간(밀리초)을 따라 정렬된 작업을 반환합니다. 

```Kusto
AzureDiagnostics
| project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
| where OperationName == "Query.Search"
| sort by DurationMs   
```

#### <a name="indexer-status"></a>인덱서 상태

이 Kusto 쿼리는 인덱서 작업의 상태를 반환합니다. 결과에는 작업 이름, 요청에 대한 설명(인덱서 이름 포함), 결과 상태(성공 또는 실패) 및 [HTTP 상태 코드](/rest/api/searchservice/http-status-codes)가 포함됩니다.

```Kusto
AzureDiagnostics
| project OperationName, Description_s, Documents_d, ResultType, resultSignature_d
| where OperationName == "Indexers.Status"
```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](/azure/azure-monitor/alerts/alerts-metric-overview), [로그](/azure/azure-monitor/alerts/alerts-unified-log) 및 [활동 로그](/azure/azure-monitor/alerts/activity-log-alerts)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.

다음 표에서는 Azure Cognitive Search 대한 일반 및 권장 경고 규칙을 나열합니다. 검색 서비스에서 지정된 임계값을 초과하는 제한 또는 쿼리 대기 시간이 가장 일반적으로 사용되는 경고이지만 검색 서비스가 삭제되면 알림을 받을 수도 있습니다.

| 경고 유형 | 조건 | Description  |
|:---|:---|:---|
| 검색 대기 시간(메트릭 경고) | 평균 검색 대기 시간이 15초보다 클 때마다 | 평균 쿼리 응답 시간이 15초를 초과하면 SMS 경고를 보냅니다. |
| 제한 검색 쿼리 백분율(메트릭 경고) | 총 제한 검색 쿼리 비율이 20%보다 크거나 같은 경우 | 삭제된 쿼리가 10%를 초과하기 시작하면 SMS 경고를 보냅니다.|
| Search Service 삭제(활동 로그 경고) | 활동 로그에 Category='Administrative', Signal name='Delete Search Service (searchServices)', Level='critical'인 이벤트가 있을 때마다 | 구독에서 검색 서비스가 삭제된 경우 전자 메일을 보냅니다. |

> [!NOTE]
> 사용 중인 스토리지와 관련된 경고는 현재 사용할 수 없습니다(스토리지 사용량은 **AzureMetrics** 테이블에 집계되거나 로그인되지 않음). 스토리지 경고를 가져오려면, 코드에서 스토리지 크기를 확인하고 응답을 처리하는 리소스 관련 알림을 내보내는 [사용자 지정 솔루션을 빌드](../azure-monitor/insights/solutions.md)해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Cognitive Search와 같은 리소스를 포함하여 Azure 서비스를 감독하는 데 Azure Monitor를 능숙하게 다루는 것이 필요합니다. Azure Monitor 익숙하지 않은 경우 시간을 내어 리소스 로깅과 관련된 문서를 검토하세요. 다음 문서를 시작 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)