---
title: Azure Cosmos DB 모니터링 | Microsoft Docs
description: Azure Cosmos DB의 성능 및 가용성을 모니터링하는 방법에 대해 알아봅니다.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 27dd64e510ffe00ce1c3c9f24f60c8c018401ab1
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471042"
---
# <a name="monitor-azure-cosmos-db"></a>Azure Cosmos DB 모니터링
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있으며 해당 리소스의 가용성, 성능 및 작업을 모니터링하려고 합니다. 이 문서에서는 Azure Cosmos 데이터베이스에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터를 분석하고 경고하는 방법을 설명합니다.

클라이언트 쪽 및 서버 쪽 메트릭을 사용하여 데이터를 모니터링할 수 있습니다. 서버 쪽 메트릭을 사용하는 경우 다음 옵션을 사용하여 Azure Cosmos DB에 저장된 데이터를 모니터링할 수 있습니다.

* **Azure Cosmos DB 포털에서 모니터링:** Azure Cosmos 계정의 메트릭 탭 내에서 사용할 수 있는 **메트릭** 을 사용하여 모니터링할 수 있습니다. 이 탭의 메트릭에는 처리량, 스토리지, 가용성, 대기 시간, 일관성 및 시스템 수준 메트릭이 포함됩니다. 기본값으로 이러한 메트릭의 보존 기간은 7일입니다. 자세한 내용은 이 문서의 [Azure Cosmos DB에서 수집된 데이터 모니터링](#monitoring-from-azure-cosmos-db) 섹션을 참조하세요.

* **Azure Monitor에서 메트릭을 사용하여 모니터링:** Azure Cosmos 계정의 메트릭을 모니터링하고 Azure Monitor 대시보드를 만들 수 있습니다. Azure Monitor는 기본값으로 Azure Cosmos DB 메트릭을 수집하기 때문에 명시적으로 아무것도 구성하지 않습니다. 이러한 메트릭은 1분 단위로 수집되며, 세분성은 선택한 메트릭에 따라 달라질 수 있습니다. 기본값으로 이러한 메트릭의 보존 기간은 30일입니다. 이전 옵션에서 사용할 수 있는 대부분의 메트릭은 이러한 메트릭에서도 사용할 수 있습니다. 컨테이너 이름과 같은 메트릭의 차원 값은 대/소문자를 구분하지 않습니다. 따라서 해당 차원 값의 문자열 비교를 수행할 때는 대/소문자를 구분하지 않는 비교를 사용해야 합니다. 자세한 내용은 이 문서의 [메트릭 데이터 분석](#analyze-metric-data) 섹션을 참조하세요.

* **Azure Monitor 진단 로그를 통해 모니터링:** Azure Cosmos 계정의 로그를 모니터링하고 Azure Monitor 대시보드를 만들 수 있습니다. 두 번째 세분성에서 발생하는 이벤트 및 추적과 같은 원격 분석 데이터는 로그로 저장됩니다. 예를 들어 컨테이너의 처리량이 변경되면 Cosmos 계정의 속성이 변경됩니다. 이러한 이벤트는 로그 내에서 캡처됩니다. 수집된 데이터에 대해 쿼리를 실행하여 이러한 로그를 분석할 수 있습니다. 자세한 내용은 이 문서의 [로그 데이터 분석](#analyze-log-data) 섹션을 참조하세요.

* **SDK를 통해 프로그래밍 방식으로 모니터링:** .NET, Java, Python, Node.js SDK 및 REST API 헤더를 사용하여 프로그래밍 방식으로 Azure Cosmos 계정을 모니터링할 수 있습니다. 자세히 알아보려면 이 문서에서 [프로그래밍 방식으로 Azure Cosmos DB 모니터링](#monitor-cosmosdb-programmatically) 섹션을 참조하세요.

다음 이미지는 Azure Portal를 통해 Azure Cosmos DB 계정을 모니터링할 수 있는 다양한 옵션을 보여줍니다.

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Azure Portal에서 사용할 수 있는 모니터링 옵션" border="false":::

Azure Cosmos DB를 사용하는 경우, 클라이언트 쪽에서 요금 요청, 작업 ID, 예외/스택 추적 정보, HTTP 상태/하위 상태 코드, 진단 문자열에 대한 세부 정보를 수집하여 발생할 수 있는 문제를 디버그할 수 있습니다. 이 정보는 Azure Cosmos DB 지원 팀에 문의해야 하는 경우에도 필요합니다. 

## <a name="monitor-overview"></a>모니터링 개요

각 Azure Cosmos DB 계정에 대한 Azure Portal의 **개요** 페이지에는 총 요청, 특정 HTTP 상태 코드를 발생시킨 요청 및 시간당 청구와 같은 리소스 사용량에 대한 간략한 보기가 포함되어 있습니다. 이 정보는 유용하지만, 이 창에서 사용할 수 있는 모니터링 데이터는 그 양이 적습니다. 이 데이터 중 일부는 자동으로 수집되며 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Cosmos DB는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. 이를 통해 다른 클라우드 및 온-프레미스의 리소스 이외에도 Azure 리소스를 모니터링할 수 있는 완전한 기능 세트를 제공합니다.

Azure 서비스를 모니터링하는 방법을 잘 모르는 경우 다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md) 문서를 먼저 참조하세요.

* Azure Monitor란?
* 모니터링과 관련된 비용
* Azure에서 수집된 데이터 모니터링
* 데이터 수집 구성
* 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure Cosmos DB에서 수집한 특정 데이터를 설명하고 Azure 도구를 사용하여 데이터 컬렉션을 구성하고 이 데이터를 분석하기 위한 예제를 제공합니다.

## <a name="cosmos-db-insights"></a>Cosmos DB 인사이트

Cosmos DB 인사이트는 [Azure Monitor의 통합 문서 기능](../azure-monitor/visualize/workbooks-overview.md)을 기반으로 하며, 아래 섹션에 설명된 Azure Cosmos DB에 대해 수집된 것과 동일한 모니터링 데이터를 사용합니다. Azure Cosmos DB용 Azure Monitor를 사용하여 통합 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 실패, 용량 및 운영 상태를 볼 수 있고, Azure Monitor의 다른 기능을 활용하여 상세한 분석 및 경고를 받을 수 있습니다. 자세한 내용은 [Cosmos DB 인사이트 살펴보기](../azure-monitor/insights/cosmosdb-insights-overview.md) 문서를 참조하세요.

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> 데이터 모니터링 

Azure Cosmos DB는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대해서는 [Azure 리소스의 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. Azure Cosmos DB에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Cosmos DB 모니터링 데이터 참조](monitor-cosmos-db-reference.md)를 참조하세요.

각 Azure Cosmos 데이터베이스에 대한 Azure Portal의 **개요** 페이지에는 요청 및 시간별 청구 사용량을 비롯하여 데이터베이스 사용량에 대한 간략한 보기가 포함되어 있습니다. 이 정보는 유용하지만 적은 양의 모니터링 데이터만 사용할 수 있습니다. 이러한 데이터 중 일부는 일부 구성에서 추가 데이터 수집을 사용하도록 설정하는 동안 데이터베이스를 만드는 즉시 자동으로 수집되어 분석에 사용할 수 있습니다.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="개요 페이지":::

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal 및 몇 가지 진단 쿼리 샘플을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](cosmosdb-monitor-resource-logs.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> 메트릭 분석

Azure Cosmos DB는 메트릭을 사용하기 위한 사용자 지정 환경을 제공합니다. 메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Azure Cosmos DB에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 또한 Azure Cosmos DB 리소스에 대한 [서버 쪽 대기 시간](monitor-server-side-latency.md), [요청 단위 사용량](monitor-request-unit-usage.md), [정규화된 요청 단위 사용량](monitor-normalized-request-units.md)을 모니터링하는 방법을 확인할 수 있습니다.

Azure Cosmos DB에 대해 수집되는 플랫폼 메트릭의 목록은 [Azure Cosmos DB 데이터 참조 메트릭 모니터링](monitor-cosmos-db-reference.md#metrics) 문서를 참조하세요.

Azure Cosmos DB에 대한 모든 메트릭은 **Cosmos DB 표준 메트릭** 네임스페이스에 있습니다. 차트에 필터를 추가할 때 이러한 메트릭에 다음 차원을 사용할 수 있습니다.

* CollectionName
* DatabaseName
* OperationType
* 지역
* StatusCode

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 작업 수준 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터** 를 선택하고 **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창":::

1. **메트릭** 창 > **리소스 선택** 에서 필요한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 **Azure Cosmos DB 계정** 을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용** 을 선택합니다.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="메트릭을 볼 Cosmos DB 계정 선택":::

1. 다음에는 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 스토리지, 대기 시간, 가용성, Cassandra 등과 관련된 메트릭을 선택할 수 있습니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예에서는 **요청 단위** 를 선택하고 **Avg** 를 집계 값으로 하겠습니다.

   이러한 세부 정보 외에 메트릭의 **시간 범위** 와 **시간 단위** 를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다. 선택한 기간에 분당 사용한 평균 요청 단위 수를 볼 수 있습니다.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Azure Portal에서 메트릭 선택":::

### <a name="add-filters-to-metrics"></a>메트릭에 필터 추가

특정 **CollectionName**, **DatabaseName**, **OperationType**, **Region** 및 **StatusCode** 별로 표시되는 메트릭 및 차트를 필터링할 수도 있습니다. 메트릭을 필터링하려면 **필터 추가** 를 선택하고 필요한 속성(예: **OperationType**)을 선택하며 **쿼리** 와 같은 값을 선택합니다. 그러면 선택한 기간에 쿼리 작업에 소비된 요청 단위가 그래프에 표시됩니다. 저장 프로시저를 통해 실행되는 작업은 기록되지 않으므로 OperationType 메트릭 아래에서 사용할 수 없습니다.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="메트릭 세분성을 선택하는 필터 추가":::

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수 있습니다. 예를 들어 다음 이미지에 표시된 것처럼 작업 유형별 요청 단위를 그룹화하고 모든 작업의 그래프를 한 번에 볼 수 있습니다.

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="분할 적용 필터 추가":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> 로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다. Azure Cosmos DB에 대해 수집되는 리소스 로그의 유형 목록은 [Azure Cosmos DB 데이터 참조 모니터링](monitor-cosmos-db-reference.md#resource-logs)을 참조하세요.

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Azure Cosmos DB는 다음 테이블에 데이터를 저장합니다.

| 테이블 | Description |
|:---|:---|
| AzureDiagnostics | 여러 서비스에서 리소스 로그를 저장하는 데 사용하는 공통 테이블입니다. Azure Cosmos DB의 리소스 로그는 `MICROSOFT.DOCUMENTDB`를 사용해 파악할 수 있습니다.   |
| AzureActivity    | 활동 로그의 모든 레코드를 저장하는 공통 테이블입니다.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> Azure Cosmos DB 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 Azure Cosmos DB 계정으로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 계정의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

**로그 검색** 검색 창에 입력하여 Azure Cosmos 리소스를 모니터링하는 데 도움을 받을 수 있는 몇몇 쿼리가 아래에 있습니다. 이러한 쿼리는 [새 언어](../azure-monitor/logs/log-query-overview.md)에서 작동합니다.

* 지정된 기간 동안 Azure Cosmos DB의 모든 진단 로그를 쿼리하려면 다음을 사용합니다.

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* 모든 작업을 쿼리하고 리소스별로 그룹화하려면 다음을 사용합니다.

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 모든 사용자 작업을 쿼리하고 리소스별로 그룹화하려면 다음을 사용합니다.

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다

예를 들어 다음 표에서는 리소스에 대한 몇 가지 경고 규칙을 보여 줍니다. Azure Portal에서 경고 규칙의 자세한 목록을 찾을 수 있습니다. 자세히 알아보려면 [경고 구성 방법](create-alerts.md) 문서를 참조하세요.  

| 경고 유형 | 조건 | Description  |
|:---|:---|:---|
|요청 단위에 대한 트래픽률 제한(메트릭 경고) |차원 이름: StatusCode, 연산자: 같음, 차원 값: 429  | 컨테이너 또는 데이터베이스가 프로비전된 처리량 제한을 초과하는 경우 경고합니다. |
|지역 장애 조치(failover) |연산자: 보다 큼, 집계 유형: 개수, 임계값: 1 | 단일 지역이 장애 조치(failover)되는 경우. 이 경고는 자동 장애 조치(failover)를 사용하도록 설정하지 않은 경우에 유용합니다. |
| 키 회전(활동 로그 경고)| 이벤트 수준: 정보 제공, 상태: 시작| 계정 키가 회전될 때 경고합니다. 새 키로 애플리케이션을 업데이트할 수 있습니다. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> 프로그래밍 방식으로 Azure Cosmos DB 모니터링

포털에서 제공되는 계정 수준 메트릭(예: 계정 스토리지 사용 및 총 요청)은 SQL API를 통해 사용할 수 없습니다. 그러나 SQL API를 사용하여 컬렉션 수준에서 사용량 현황 데이터를 검색할 수 있습니다. 컬렉션 수준 데이터를 검색하려면 다음을 수행합니다.

* REST API를 사용하려면 [컬렉션에 대해 GET을 수행](/rest/api/cosmos-db/get-a-collection)합니다. 컬렉션에 대한 할당량 및 사용량 정보는 응답의 x-ms-resource-quota 및 x-ms-resource-usage 헤더에서 반환됩니다.

* .NET SDK를 사용하려면 [DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) 메서드를 사용합니다. 이 메서드는 **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** 등의 여러 사용량 속성이 포함된 [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1)를 반환합니다.

추가 메트릭에 액세스하려면 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)를 사용하세요. 가용 메트릭 정의는 다음을 호출하면 검색할 수 있습니다.

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

개별 메트릭을 검색하기 위해 다음 형식을 사용합니다.

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

자세한 내용은 [Azure 모니터링 REST API](../azure-monitor/essentials/rest-api-walkthrough.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에서 만든 로그 및 메트릭에 대한 내용은 [Azure Cosmos DB 모니터링 데이터 참조](monitor-cosmos-db-reference.md)를 참조하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용한 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
