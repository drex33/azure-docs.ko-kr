---
title: Azure Container Registry 모니터링
description: Azure Monitor의 기능을 사용하여 Azure Container Registry를 모니터링하는 방법을 알아보려면 여기에서 시작
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 08/13/2021
ms.openlocfilehash: ff82571b791f20cf46150be06e583af29f13dbd5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586971"
---
# <a name="monitor-azure-container-registry"></a>Azure Container Registry 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Container Registry에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터를 분석하고 경고하는 방법을 설명합니다.

## <a name="monitor-overview"></a>모니터링 개요

각 레지스트리에 대한 Azure Portal의 **개요** 페이지에는 밀어넣기 및 끌어오기 작업과 같은 최근 리소스 사용량 및 활동에 대한 간략한 보기가 포함되어 있습니다. 이러한 개략적인 정보는 유용 하지만 적은 양의 데이터만 표시 됩니다. 

:::image type="content" source="media/monitor-service/metrics-overview.png" alt-text="레지스트리 메트릭 개요"::: 

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Container Registry는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. 이를 통해 다른 클라우드 및 온-프레미스의 리소스 이외에도 Azure 리소스를 모니터링할 수 있는 완전한 기능 세트를 제공합니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링 문서](../azure-monitor/essentials/monitor-azure-resource.md)로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure Container Registry에서 수집한 특정 데이터를 설명하고 Azure 도구를 사용하여 데이터 컬렉션을 구성하고 해당 데이터를 분석하기 위한 예제를 제공합니다.

## <a name="monitoring-data"></a>데이터 모니터링 

Azure Container Registry는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대해서는 [Azure 리소스의 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. 

Azure Container Registry에서 만든 메트릭 및 로그에 대한 세부 정보는 [Azure Container Registry 모니터링 데이터 참조](monitor-service-reference.md)를 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Container Registry의 범주는 [Azure Container Registry 모니터링 데이터 참조](monitor-service-reference.md#resource-logs)에 나와 있습니다.

> [!TIP]
> Portal에서 레지스트리로 이동하여 레지스트리 진단 설정을 만들 수도 있습니다. 메뉴의 **모니터링** 아래에서 **진단 설정** 을 선택합니다.

다음 이미지는 레지스트리에 대 한 진단 설정을 사용 하도록 설정 하는 경우의 옵션을 보여 줍니다.

:::image type="content" source="media/monitor-service/diagnostic-settings.png" alt-text="컨테이너 레지스트리에 대한 진단 설정":::

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics-preview"></a>메트릭 분석(미리 보기)

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Azure Container Registry에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

> [!TIP]
> Portal에서 레지스트리로 이동한 후 메트릭 탐색기로 이동해도 됩니다. 메뉴의 **모니터링에서** 에서 **메트릭(미리 보기)** 을 선택합니다.

Azure Container Registry용으로 수집된 플랫폼 메트릭 목록은 [Azure Container Registry 모니터링 데이터 참조 메트릭](monitor-service-reference.md#metrics)을 참조하세요.  

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

### <a name="azure-cli"></a>Azure CLI

다음 Azure CLI 명령을 사용하여 Azure Container Registry 메트릭에 대한 정보를 얻을 수 있습니다.

* [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) - 메트릭 정의 및 차원 나열
* [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) - 메트릭 값 검색

### <a name="rest-api"></a>REST API 

Azure Monitor REST API를 사용하여 Azure Container Registry 메트릭에 대한 정보를 프로그래밍 방식으로 얻을 수 있습니다.

* [메트릭 정의 및 차원 나열](/rest/api/monitor/metricdefinitions/list)
* [메트릭 값 검색](/rest/api/monitor/metrics/list)

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다. Azure Container Registry 리소스 로그에 대한 스키마는 [Azure Container Registry 데이터 참조](monitor-service-reference.md#schemas)에 제공됩니다. 

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Azure Container Registry용으로 수집된 리소스 로그 유형 목록은 모니터링 [Azure Container Registry 모니터링 데이터 참조](monitor-service-reference.md#resource-logs)를 참조하세요.  

Azure Monitor 로그에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [Azure Container Reference 모니터링 데이터 참조](monitor-service-reference.md#azure-monitor-logs-tables)를 참조하세요.  

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> **Azure Container Registry** 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 레지스트리로 설정된 상태로 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 레지스트리의 데이터 또는 다른 Azure 서비스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

예를 들어 다음 쿼리는 **ContainerRegistryRepositoryEvents** 테이블에서 가장 최근 24시간 동안의 데이터를 검색합니다.

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

다음 이미지는 샘플 출력을 보여 줍니다.

:::image type="content" source="media/monitor-service/azure-monitor-query.png" alt-text="로그 데이터 쿼리":::

다음은 레지스트리 리소스를 모니터링하는 데 사용할 수 있는 쿼리입니다. 

### <a name="error-events-from-the-last-hour"></a>지난 1시간의 오류 이벤트

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>가장 최근 레지스트리 이벤트 100개

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>리포지토리를 삭제한 사용자 또는 개체의 ID

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>태그를 삭제한 사용자 또는 개체의 ID

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>리포지토리 수준 작업 실패

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>리포지토리 인증 실패

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.


<!-- only include next line if applications run on your service and work with App Insights. 

If you are creating or running an application which run on <*service*> [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) may offer additional types of alerts.
-->

다음 표에는 Azure Container Registry에 대한 일반적인 경고 규칙과 권장되는 경고 규칙이 나와 있습니다.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->
| 경고 유형 | 조건 | Description  |
|:---|:---|:---|
| 메트릭 | 신호: 사용된 스토리지<br/>연산자: 초과<br/>집계 형식: 평균<br/>임계값: 5GB|  사용된 레지스트리 스토리지가 지정된 값을 초과하면 경고합니다.|
| | | |

### <a name="example-send-email-alert-when-registry-storage-used-exceeds-a-value"></a>예: 사용된 레지스트리 스토리지가 값을 초과하는 경우 메일 경고 보내기

1. Azure Portal에서 레지스트리로 이동합니다.
1. **모니터링** 에서 **메트릭(미리 보기)** 을 선택합니다.
1. 메트릭 탐색기의 **메트릭** 에서 **사용된 스토리지** 를 선택합니다.
1. **새 경고 규칙** 을 선택합니다.
1. **범위** 에서 경고 규칙을 만들 레지스트리 리소스를 확인합니다.
1. **조건** 에서 **조건 추가** 를 선택합니다.
    1. **신호 이름** 에서 **사용된 스토리지** 를 선택합니다.
    1. **차트 기간** 에서 **지난 24시간 동안** 을 선택합니다.
    1. **경고 논리** 의 **임계값** 에서 *5* 와 같은 값을 선택합니다. **단위** 에서 *GB* 와 같은 값을 선택합니다.
    1. 나머지 설정에 대해 기본값을 그대로 적용하고 **완료** 를 선택합니다.
1. **작업** 에서 **작업 그룹 추가** >  **+ 작업 그룹 만들기** 를 선택합니다.
    1. 작업 그룹의 세부 정보를 입력합니다.
    1. **알림** 탭에서 **메일/SMS 메시지/푸시/음성** 을 선택하고 받는 사람(예: *admin@contoso.com* )을 입력합니다. **검토 + 만들기** 를 선택합니다.
1. 경고 규칙의 이름과 설명을 입력하고, 심각도를 선택합니다.
1. **경고 규칙 만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- Azure Container Registry에서 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [Azure Container Registry 모니터링 데이터 참조](monitor-service-reference.md)를 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
- 레지스트리의 저장소 사용 및 기타 리소스 소비에 대 한 스냅숏을 가져오는 방법에 대 한 자세한 내용은 [레지스트리 사용 표시](container-registry-skus.md#show-registry-usage) 를 참조 하세요.