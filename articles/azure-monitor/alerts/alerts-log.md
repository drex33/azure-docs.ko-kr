---
title: Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리 | Microsoft Docs
description: Azure Monitor를 사용하여 로그 경고 규칙 만들기, 보기 및 관리
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e8c89f4542b29a69346f43f95a7d2f5de78148f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012495"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리

## <a name="overview"></a>개요

사용자는 로그를 통해 설정된 빈도마다 [Log Analytics](../logs/log-analytics-tutorial.md) 쿼리를 사용하여 리소스 로그를 평가하고 결과에 따라 경고를 실행할 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용하여 하나 이상의 작업을 트리거할 수 있습니다. [로그 경고의 기능 및 용어에 대해 자세히 알아보세요](./alerts-unified-log.md).

이 문서는 Azure Monitor를 사용하여 로그 경고를 만들고 관리하는 방법을 보여 줍니다. 경고 규칙은 세 가지 구성 요소에 의해 정의됩니다.
- 대상: 모니터링할 특정 Azure 리소스.
- 조건: 평가할 논리. 충족되는 경우 경고가 발생합니다.  
- 작업: 알림 또는 자동화 - 이메일, SMS, 웹후크 등.

또한 Azure Resource Manager 템플릿을 사용하여 로그 경고 규칙을 만들 수 있으며, 이 방법은 [별도의 문서](../alerts/alerts-log-create-templates.md)에 설명되어 있습니다.

> [!NOTE]
> [Log Analytics 작업 영역](../logs/log-analytics-tutorial.md)의 로그 데이터를 Azure Monitor 메트릭 저장소로 보낼 수 있습니다. 메트릭 경고에는 사용 중인 데이터에 따라 적합할 수 있는 [다른 동작](./alerts-metric-overview.md)이 있습니다. 로그를 메트릭에 라우팅하는 대상 및 방법에 대한 자세한 내용은 [로그에 대한 메트릭 경고](./alerts-metric-logs.md)를 참조하세요.

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Azure Portal을 통해 로그 경고 규칙 만들기

경고에 대한 쿼리 작성을 시작하는 단계는 다음과 같습니다.

1. 경고하려는 리소스로 이동합니다. 가능하면 구독 또는 리소스 그룹 범위를 선택하여 여러 리소스에 대한 경고 규칙을 설정하는 것이 좋습니다. 여러 리소스에 대한 경고를 사용하면 여러 경고 규칙을 관리하는 데 따른 비용과 필요성이 감소합니다.
1. **모니터** 에서 **로그** 를 선택합니다.
1. 문제를 나타낼 수 있는 로그 데이터를 쿼리합니다. [경고 쿼리 예제 토픽](../logs/queries.md)을 사용하여 발견할 수 있는 항목 또는 [쿼리 작성 시작하기](../logs/log-analytics-tutorial.md)에 대해 이해할 수 있습니다. 또한 [최적화된 경고 쿼리를 만드는 방법에 대해 알아봅니다](alerts-log-query.md).
1. '+ 새 경고 규칙' 단추를 눌러 경고 생성 흐름을 시작합니다.

    ![Log Analytics - 경고 설정](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> 리소스 그룹 또는 구독 범위를 사용하여 여러 리소스에서 실행되는 로그에 대해 리소스 액세스 모드를 사용하는 경우에는 대규모 경고를 만드는 것이 좋습니다. 대규모 경고는 규칙 관리 오버헤드를 줄입니다. 리소스를 대상으로 지정하려면 결과에 리소스 ID 열을 포함합니다. [차원별 경고 분할에 대해 자세히 알아보세요](./alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Log Analytics 및 Application Insights에 대한 로그 경고

1. 쿼리 구문이 올바른 경우 쿼리의 기록 데이터가 그래프로 표시되고, 지난 6시간에서 지난 주까지 차트 기간을 조정할 수 있습니다.
 
    쿼리 결과에 요약된 데이터 또는 시계열 없는 [프로젝트](/azure/kusto/query/projectoperator)별 열이 포함된 경우 차트에 단일 값이 표시됩니다.

    ![경고 규칙 구성](media/alerts-log/AlertsPreviewAlertLog.png)

1. [**기간**](./alerts-unified-log.md#query-time-range) 옵션을 사용하여 지정된 조건을 평가할 시간 범위를 선택합니다.

1. 로그 경고는 다음 두 가지 유형의 [**측정값**](./alerts-unified-log.md#measure)을 기반으로 할 수 있습니다.
    1. **결과 수** - 쿼리에서 반환하는 전체 레코드의 수입니다.
    1. **메트릭 측정** - 선택한 식과 [bin()](/azure/kusto/query/binfunction) 선택에 의해 그룹화된 요약을 사용하여 계산된 집계 값입니다. 예를 들면 다음과 같습니다.

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. 메트릭 측정 경고 논리의 경우 **집계** 옵션을 사용하여 [차원별 경고를 분할](./alerts-unified-log.md#split-by-alert-dimensions)하는 방법을 선택적으로 지정할 수 있습니다. 행 그룹화 식은 고유하고 정렬되어야 합니다.

    > [!NOTE]
    > [bin()](/azure/kusto/query/binfunction)에서 동일하지 않은 시간 간격이 발생할 수 있으므로, 경고 서비스는 런타임 시 적절한 시간을 사용하여 [bin()](/azure/kusto/query/binfunction) 함수를 [bin_at()](/azure/kusto/query/binatfunction) 함수로 자동 변환함으로써 고정 시점이 포함된 결과를 보장합니다.

    > [!NOTE]
    > 차원별 경고 분할은 현재 scheduledQueryRules API에 대해서만 사용할 수 있습니다. 레거시 [Log Analytics Alert API](./api-alerts.md)를 사용하는 경우 전환해야 합니다. [샘플링에 대해 자세히 알아보세요](./alerts-log-api-switch.md). 리소스 중심의 대규모 경고는 API 버전 `2020-08-01` 이상에서만 지원됩니다.

    ![집계 옵션](media/alerts-log/aggregate-on.png)

1. 다음으로 미리 보기 데이터를 기반으로 [**연산자**, **임계값**](./alerts-unified-log.md#threshold-and-operator) 및 [**빈도**](./alerts-unified-log.md#frequency)를 설정합니다.

1. 선택적으로 **총 위반 또는 연속 위반** 을 사용하여 [경고를 트리거할 위반 횟수](./alerts-unified-log.md#number-of-violations-to-trigger-alert)를 설정할 수도 있습니다.

1. **완료** 를 선택합니다. 

1. **경고 규칙 이름**, **설명** 을 정의하고, 경고 **심각도** 를 선택합니다. 이러한 세부 정보는 모든 경고 작업에 사용됩니다. 추가로 **만들면 바로 사용** 을 선택하여 생성 시 경고 규칙을 즉시 사용하도록 선택할 수 있습니다.

1. 경고가 발생한 후 일정 시간 동안 규칙 작업을 표시하지 않을지 선택하고, [**경고 표시 안 함**](./alerts-unified-log.md#state-and-resolving-alerts) 옵션을 사용합니다. 규칙은 계속 실행되고 경고를 만들지만 노이즈를 방지하기 위해 작업이 트리거되지 않습니다. 음소거 작업 값이 적용되려면 경고 빈도보다 커야 합니다.

    ![로그 경고에 대한 경고 표시 안 함](media/alerts-log/AlertsPreviewSuppress.png)

1. 경고 조건이 충족되면 경고 규칙이 하나 이상의 [**작업 그룹**](./action-groups.md#webhook)을 트리거할지 여부를 지정합니다.

    > [!NOTE]
    > 수행할 수 있는 작업에 대한 제한은 [Azure 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.  

1. 선택적으로 로그 경고 규칙에서 작업을 사용자 지정할 수 있습니다.

    - **사용자 지정 이메일 제목**: 이메일 작업의 이메일 제목을 재정의합니다. 메일 본문은 수정할 수 없으며, 이 필드는 **이메일 주소에 대한 필드가 아닙니다**.
    - **사용자 지정 JSON 페이로드 포함**: 작업 그룹에 웹후크 작업이 있다고 가정하고 작업 그룹에서 사용하는 웹후크 JSON을 재정의합니다. [로그 경고에 대한 웹후크 작업](./alerts-log-webhook.md)을 참조하세요.

    ![경고 로그에 대한 작업 재정의](media/alerts-log/AlertsPreviewOverrideLog.png)

1. 모든 필드가 올바르게 설정된 경우 **경고 규칙 만들기** 단추를 클릭하고 경고를 만들 수 있습니다.

    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

    ![규칙 만들기](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>경고 관리에서 Log Analytics 및 Application Insights에 대한 로그 경고 만들기

> [!NOTE]
> 경고 관리에서 만들기는 현재 리소스 중심 로그에 대해 지원되지 않습니다

1. [포털](https://portal.azure.com/)에서 **모니터링** 을 선택한 다음 **경고** 를 선택합니다.

    ![모니터링](media/alerts-log/AlertsPreviewMenu.png)

1. **새 경고 규칙** 을 선택합니다. 

    ![Add alert](media/alerts-log/AlertsPreviewOption.png)

1. **경고 만들기** 창이 표시됩니다. 창에는 4개 부분이 있습니다. 
    - 경고가 적용되는 리소스.
    - 확인할 조건.
    - 조건이 true인 경우 수행할 동작.
    - 경고의 이름을 입력하고 설명하는 세부 정보. 

    ![규칙 만들기](media/alerts-log/AlertsPreviewAdd.png)

1. **리소스 선택** 단추를 누릅니다. 구독, 리소스 종류를 선택하여 필터링하고, 리소스를 선택합니다. 리소스에 로그가 제공되는지 확인합니다.

   ![리소스 선택](media/alerts-log/Alert-SelectResourceLog.png)

1. 다음으로 **조건** 추가 단추를 사용하여 리소스에 대해 사용 가능한 신호 옵션의 목록을 확인합니다. **사용자 지정 로그 검색** 옵션을 선택합니다.

   ![리소스 선택 - 사용자 지정 로그 검색](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 경고 포털은 Log Analytics 및 Application Insights에서 저장된 쿼리를 나열하며, 이는 템플릿 경고 쿼리로 사용할 수 있습니다.

1. 선택한 후에는 **검색 쿼리** 필드에서 경고 쿼리를 작성, 붙여넣기 또는 편집합니다.

1. [마지막 섹션](#log-alert-for-log-analytics-and-application-insights)에서 설명하는 다음 단계를 계속합니다.

### <a name="log-alert-for-all-other-resource-types"></a>다른 모든 리소스 종류에 대한 로그 경고

1. **조건** 탭에서 시작합니다.

    1. [**측정값**](./alerts-unified-log.md#measure), [**집계 유형**](./alerts-unified-log.md#aggregation-type) 및 [**집계 세분성**](./alerts-unified-log.md#aggregation-granularity)이 올바른지 확인합니다. 
        1. 기본적으로 규칙은 지난 5분 동안의 결과 수를 계산합니다.
        1. 요약된 쿼리 결과를 검색하면 몇 초 내에 규칙이 자동으로 업데이트되어 이를 캡처합니다.

    1. 필요한 경우 [차원별 경고 분할](./alerts-unified-log.md#split-by-alert-dimensions)을 선택합니다. 
       - **리소스 ID 열** 이 자동으로 선택되고, 검색된 경우 발생한 경고의 컨텍스트를 레코드의 리소스로 변경합니다. 
       - **리소스 ID 열** 을 선택 취소하여 구독 또는 리소스 그룹에 대한 경고를 발생시킬 수 있습니다. 선택 취소는 쿼리 결과가 교차 리소스를 기반으로 하는 경우에 유용합니다. 예를 들어 리소스 그룹의 가상 머신 중 80%에서 높은 CPU 사용량이 발생하는지 확인하는 쿼리가 있습니다.
       - 차원 테이블을 사용하여 숫자 또는 텍스트 열 형식에 대해 최대 6개의 분할을 추가로 선택할 수도 있습니다.
       - 경고는 고유한 조합을 기준으로 분할에 따라 별도로 발생하며, 경고 페이로드에는 이 정보가 포함됩니다.
    
        ![집계 매개 변수 및 분할 선택](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. **미리 보기** 차트에는 시간에 따른 쿼리 평가 결과가 표시됩니다. 차트 기간을 변경하거나 차원별로 고유한 경고 분할로 인해 발생한 다른 시계열을 선택할 수 있습니다.

        ![차트 미리 보기](media/alerts-log/preview-chart.png)

    1. 다음으로 미리 보기 데이터를 기반으로 **경고 논리** 의 [**연산자**, **임계값**](./alerts-unified-log.md#threshold-and-operator) 및 [**빈도**](./alerts-unified-log.md#frequency)를 설정합니다.

        ![임계값 및 경고 논리가 있는 차트 미리 보기](media/alerts-log/chart-and-alert-logic.png)

    1. **고급 옵션** 섹션에서 [**경고를 트리거할 위반 횟수**](./alerts-unified-log.md#number-of-violations-to-trigger-alert)를 설정할 수 있습니다.
    
        ![고급 옵션](media/alerts-log/advanced-options.png)

1. **작업** 탭에서 필요한 [작업 그룹](./action-groups.md)을 선택하거나 만듭니다.

    ![작업 탭](media/alerts-log/actions-tab.png)

1. **세부 정보** 탭에서 **경고 작업 세부 정보** 및 **프로젝트 세부 정보** 를 정의합니다. **지금 실행을 시작** 할지 여부를 설정하거나 경고 규칙 발생 이후 일정 시간 동안 [**작업 음소거**](./alerts-unified-log.md#state-and-resolving-alerts)를 설정할 수 있습니다.

    > [!NOTE]
    > 로그 경고 규칙은 현재 상태 비저장이며 음소거가 정의되지 않는 한 경고가 생성될 때마다 작업을 발생시킵니다.

    ![자세히 탭](media/alerts-log/details-tab.png)

1. **태그** 탭에서 경고 규칙 리소스에 필요한 태그를 설정합니다.

    ![태그 탭](media/alerts-log/tags-tab.png)

1. **검토 + 만들기** 탭에서 유효성 검사가 실행되고 문제를 알립니다. 규칙 정의를 검토하고 승인합니다.
1. 모든 필드가 올바른 경우 **만들기** 단추를 선택하고 경고 규칙 만들기를 완료합니다. 경고 관리에서 모든 경고를 볼 수 있습니다.
 
    ![검토 및 만들기 탭](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Azure Portal에서 로그 경고 보기 및 관리

1. [포털](https://portal.azure.com/)에서 관련 리소스 또는 **모니터링** 서비스를 선택합니다. 그런 다음 모니터링 섹션에서 **경고** 를 선택합니다.

1. 경고 관리에 발생한 모든 경고가 표시됩니다. [경고 관리에 대해 자세히 알아보세요](alerts-managing-alert-instances.md).

    > [!NOTE]
    > 로그 경고 규칙은 현재 [상태 비저장이며 확인되지 않습니다](./alerts-unified-log.md#state-and-resolving-alerts).

1. 상단 표시줄의 **경고 규칙 관리** 단추를 선택하여 규칙을 편집합니다.

    ![ 경고 규칙 관리](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>PowerShell을 사용하여 로그 경고 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell은 현재 API 버전 `2020-08-01`에서 지원되지 않습니다.

아래 나열된 PowerShell cmdlet으로 [Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/)를 사용하여 규칙을 관리할 수 있습니다.

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule): 새 로그 규칙을 만드는 PowerShell cmdlet입니다.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule): 기존 로그 경고 규칙을 업데이트하는 PowerShell cmdlet입니다.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource): 로그 경고에 대한 소스 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 및 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet에 의해 입력으로 사용됩니다.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): 로그 경고에 대한 일정 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 및 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet에 의해 입력으로 사용됩니다.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction): 로그 경고에 대한 작업 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 및 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet에 의해 입력으로 사용됩니다.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup): 로그 경고에 대한 작업 그룹 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet에 의해 입력으로 사용됩니다.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition): 로그 경고에 대한 트리거 조건 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet에 의해 입력으로 사용됩니다.
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger): [메트릭 측정 유형 로그 경고](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)에 대한 메트릭 트리거 조건 매개 변수를 지정하는 개체를 만들거나 업데이트하는 PowerShell cmdlet입니다. [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet에 의해 입력으로 사용됩니다.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule): 기존 로그 경고 규칙 또는 특정 로그 경고 규칙을 나열하는 PowerShell cmdlet입니다.
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule): 로그 경고 규칙을 사용 또는 사용하지 않도록 설정하는 PowerShell cmdlet입니다.
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): 기존 로그 경고 규칙을 삭제하는 PowerShell cmdlet입니다.

> [!NOTE]
> ScheduledQueryRules PowerShell cmdlet은 현재 [Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/)에서 만든 규칙만 관리할 수 있습니다. 레거시 [Log Analytics Alert API](./api-alerts.md)를 사용하여 만든 로그 경고 규칙은 [Scheduled Query Rules API로 전환](../alerts/alerts-log-api-switch.md)한 이후에만 PowerShell을 사용하여 관리할 수 있습니다.

PowerShell을 사용하여 로그 경고 규칙을 만드는 예제 단계는 다음과 같습니다.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

교차 리소스 쿼리가 포함된 PowerShell을 사용하여 로그 경고 규칙을 만드는 예제 단계는 다음과 같습니다.

```powershell
$authorized = @ ("/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicewsCrossExample", "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceAppInsights")

$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews" -AuthorizedResource $authorized

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition
New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name" 
```

PowerShell을 사용하여 [템플릿 및 매개 변수](./alerts-log-create-templates.md) 파일을 사용하여 로그 경고를 만들 수도 있습니다.

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>CLI를 사용하여 로그 경고 관리

> [!NOTE]
> Azure CLI 지원은 scheduledQueryRules API 버전 `2020-08-01` 이상에서만 사용할 수 있습니다. 이전 API 버전은 아래에서 설명하는 것과 같이 Azure Resource Manager CLI를 사용할 수 있습니다. 레거시 [Log Analytics Alert API](./api-alerts.md)를 사용하는 경우 CLI를 사용하도록 전환해야 합니다. [샘플링에 대해 자세히 알아보세요](./alerts-log-api-switch.md).

이전 섹션에서는 Azure Portal을 사용하여 로그 경고 규칙을 만들고, 보고, 관리하는 방법을 설명했습니다. 이번 섹션에서는 플랫폼 간 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 동일한 작업을 수행하는 방법을 설명합니다. Azure CLI를 가장 빠르게 사용하는 방법은 [Azure Cloud Shell](../../cloud-shell/overview.md)입니다. 이 문서에서는 Cloud Shell을 사용합니다.

1. Azure Portal로 이동하여 **Cloud Shell** 을 선택합니다.

1. 프롬프트에서 ``--help`` 옵션이 있는 명령을 사용하여 명령 및 사용 방법에 대해 자세히 알아볼 수 있습니다. 예를 들어 다음 명령은 로그 경고를 만들고, 살펴보고, 관리하는 데 사용할 수 있는 명령 목록을 보여 줍니다.

    ```azurecli
    az monitor scheduled-query --help
    ```

1. 시스템 이벤트 오류 개수를 모니터링하는 로그 경고 규칙을 만들 수 있습니다.

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. 다음 명령을 사용하여 리소스 그룹의 모든 로그 경고를 볼 수 있습니다.

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. 규칙의 이름 또는 리소스 ID를 사용하여 특정 로그 경고 규칙의 세부 정보를 볼 수 있습니다.

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. 다음 명령을 사용하여 로그 경고 규칙을 비활성화할 수 있습니다.

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --disabled false
    ```

1. 다음 명령을 사용하여 로그 경고 규칙을 삭제할 수 있습니다.

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

[템플릿](./alerts-log-create-templates.md) 파일에 Azure Resource Manager CLI를 사용할 수도 있습니다.

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

만들기에 성공하면 201이 반환됩니다. 업데이트에 성공하면 200이 반환됩니다.

## <a name="next-steps"></a>다음 단계

* [로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
* [Azure Resource Manager 템플릿](./alerts-log-create-templates.md)을 사용하여 로그 경고를 만듭니다.
* [로그 경고에 대한 웹후크 작업](./alerts-log-webhook.md)을 이해합니다.
* [로그 쿼리](../logs/log-query-overview.md)에 대해 자세히 알아봅니다.
