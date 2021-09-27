---
title: 모니터링 Azure Key Vault
description: 여기에서 시작하여 Azure Key Vault 모니터링하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 3696fc2a1231598835b483c9a411a746a56d1faf
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060690"
---
# <a name="monitoring-azure-key-vault"></a>모니터링 Azure Key Vault

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. Azure Key Vault 경우 키 자격 증명 모음으로 전송되는 요청 수가 증가하므로 크기 조정을 시작할 때 서비스를 모니터링하는 것이 중요합니다. 이로 인해 요청 대기 시간이 증가하고 극단적인 경우 요청이 제한되어 서비스 성능에 영향을 줄 수 있습니다.

이 문서에서는 Key Vault 생성된 모니터링 데이터에 대해 설명합니다. Key Vault [Azure Monitor](/azure/azure-monitor/overview)을 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](/azure/azure-monitor/essentials/monitor-azure-resource)을 읽어 보세요.

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure Portal의 모니터링 개요 페이지

각 키 자격 증명 모음에 대한 Azure Portal **개요** 페이지에는 "모니터링" 탭에 다음 메트릭이 포함되어 있습니다.

- 총 요청 수
- 평균 대기 시간
- 성공 비율

"추가 메트릭"(또는 "모니터링" 아래의 왼쪽 사이드바에 있는 "메트릭" 탭)을 선택하여 이러한 메트릭도 볼 수 있습니다.

- 전체 서비스 API 대기 시간
- 전체 자격 증명 모음 가용성
- 전체 자격 증명 모음 포화
- 총 서비스 API 적중 수
- 총 서비스 API 결과

## <a name="key-vault-insights"></a>Key Vault 인사이트

Azure의 일부 서비스는 Azure Portal에서 서비스 모니터링을 위한 시작점을 제공하는 미리 빌드된 모니터링 대시보드를 특별히 제공합니다. 이러한 특수 대시보드를 “인사이트”라고 합니다.

Key Vault 인사이트는 Key Vault 요청, 성능, 실패 및 대기 시간에 대한 통합 보기를 제공하여 키 자격 증명 모음의 종합적인 모니터링을 제공합니다. 자세한 내용은 Key Vault 인사이트를 통해 키 자격 증명 모음 [서비스 모니터링을 참조하세요.](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="monitoring-data"></a>데이터 모니터링

Key Vault Azure 리소스에서 데이터 모니터링에 설명된 다른 Azure 리소스와 동일한 종류의 [모니터링 데이터를 수집합니다.](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data-from-Azure-resources)

Key Vault 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 모니터링 Key Vault [데이터 참조를  ](monitor-key-vault-reference.md) 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. *Key Vault* 범주는 Key Vault 모니터링 데이터 [참조](monitor-key-vault-reference.md#resource-logs)에 나열됩니다.

키 자격 증명 모음에 대한 진단 설정을 만들려면 [Key Vault 로깅 사용을 참조하세요.](howto-logging.md)  수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

Azure Monitor 메뉴에서 메트릭을 열어 메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 **Key Vault** **대한 메트릭을** 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](/azure/azure-monitor/platform/metrics-getting-started)을 참조하세요.

Key Vault 대해 수집된 플랫폼 메트릭 목록은 Key Vault 데이터 [참조 메트릭 모니터링을 참조하세요.](monitor-key-vault-reference.md#metrics)  

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 Azure Monitor [리소스 로그 스키마에](../../azure-monitor/platform/diagnostic-logs-schema.md#top-level-resource-logs-schema) 설명되어 있습니다. 

[활동 로그](../../azure-monitor/platform/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그 유형입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Key Vault 대해 수집된 리소스 로그 유형 목록은 모니터링 [Key Vault 데이터 참조를 참조하세요.](monitor-key-vault-reference.md#resource-logs)  

Azure Monitor Logs에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [Key Vault 데이터 참조 모니터링을 참조하세요.](monitor-key-vault-reference.md#azure-monitor-logs-tables)  

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> Key Vault 메뉴에서 **로그를** 선택하면 쿼리 범위가 현재 키 자격 증명 모음으로 설정된 상태로 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 키 자격 증명 모음의 데이터 또는 다른 Azure 서비스의 데이터가 포함된 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그를** 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

다음은 **로그 검색 검색** 표시줄에 입력하여 Key Vault 리소스를 모니터링하는 데 도움이 되는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](/azure-monitor/logs/log-query-overview)에서 작동합니다.

* 느린 요청이 있나요?

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* 오류가 있나요?

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* 입력 역직렬화 오류

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* 이 KeyVault가 얼마나 활성화되었나요?

    ```Kusto
    //  
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* 이 KeyVault를 호출하는 Who? 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* 이 KeyVault가 요청을 얼마나 빠르게 제공하나요? 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* 지난 달에 발생한 변경 사항은 무엇인가요? 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../../azure-monitor/platform/alerts-metric-overview.md), [로그](../../azure-monitor/platform/alerts-unified-log.md) 및 [활동 로그](../../azure-monitor/platform/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다

Azure Key Vault 실행되는 애플리케이션을 만들거나 실행하는 경우 [Azure Monitor 애플리케이션 Insights](/azure-monitor/overview#application-insights.md) 추가 유형의 경고를 제공할 수 있습니다.

다음은 Azure Key Vault 대한 몇 가지 일반적이고 권장되는 경고 규칙입니다.

- Key Vault 가용성이 100% 아래로 떨어짐(고정 임계값)
- Key Vault 대기 시간이 500ms보다 큼(고정 임계값)
- 전체 자격 증명 모음 포화도가 75%보다 큼(고정 임계값)
- 전체 자격 증명 모음 포화도가 평균 초과(동적 임계값)
- 총 오류 코드가 평균보다 높음(동적 임계값)

자세한 내용은 Azure Key Vault 대한 [경고를](alert.md) 참조하세요.

## <a name="next-steps"></a>다음 단계

축하합니다. 이제 Azure Key Vault 모니터링하고 있습니다. 경고를 설정하려는 경우 [Azure Key Vault 대한 경고를](alert.md)참조하세요.

- Key Vault 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 모니터링 Azure Key Vault [데이터 참조를](monitor-key-vault-reference.md) 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resource)을 참조하세요.
