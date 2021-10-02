---
title: Azure 메트릭 경고 문제 해결
description: Azure Monitor 메트릭 경고 및 가능한 솔루션에서 발생하는 일반적인 문제입니다.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 09/30/2021
ms.openlocfilehash: df925bd149c8516f4c6af8b49a65969737aaffa2
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352377"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Azure Monitor 메트릭 경고 문제 해결 

이 문서에서는 Azure Monitor [메트릭 경고](alerts-metric-overview.md)에서 발생하는 일반적인 문제 및 해결 방법에 대해 설명합니다.

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대한 자세한 내용은 [Microsoft Azure의 경고 개요](./alerts-overview.md)를 참조하세요.

## <a name="metric-alert-should-have-fired-but-didnt"></a>메트릭 경고가 발생해야 했지만 발생하지 않음 

메트릭 경고가 발생해야 했지만 발생하지 않았고 Azure Portal에서 찾을 수 없는 경우 다음 단계를 수행합니다.

1. **구성** - 메트릭 경고 규칙 구성을 검토하여 제대로 구성되었는지 확인합니다.
    - **집계 유형** 및 **집계 세분성(기간)** 이 예상대로 구성되었는지 확인합니다. **집계 유형** 은 메트릭 값을 집계하는 방법을 결정하고([여기](../essentials/metrics-aggregation-explained.md#aggregation-types)에서 자세히 알아보기), **집계 세분성(기간)** 은 경고 규칙이 실행될 때마다 평가에서 메트릭 값을 집계하는 정도를 제어합니다.
    -  **임계값** 또는 **민감도** 가 예상대로 구성되었는지 확인합니다.
    - 동적 임계값을 사용하는 경고 규칙의 경우 고급 설정이 구성되었는지 확인하세요. **위반 횟수** 가 경고를 필터링할 수 있고 **이전 데이터 무시** 가 임계값 계산 방법에 영향을 미칠 수 있기 때문입니다.

       > [!NOTE] 
       > 동적 임계값은 활성 상태가 되려면 3일 이상 및 메트릭 샘플 30개 이상이 필요합니다.

2. **경고가 발생했지만 알림이 오지 않음** - [발생한 경고 목록](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)을 검토하여 발생한 검토를 찾을 수 있는지 확인합니다. 경고가 목록에 표시되지만 일부 경고 관련 작업 또는 알림에 문제가 있는 경우 [여기](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)에서 자세한 정보를 참조하세요.

3. **이미 활성 상태** - 경고가 발생할 것으로 예상했던 메트릭 시계열에 이미 경고가 발생했는지 확인합니다. 메트릭 경고는 상태 저장 경고입니다. 즉, 특정 메트릭 시계열에서 경고가 발생하면 문제가 더 이상 관찰되지 않을 때까지 해당 시계열에 대한 추가 경고가 발생하지 않습니다. 이 디자인을 선택하면 노이즈가 줄어듭니다. 3번 연속된 평가에서 경고 조건이 부합하지 않으면 경고가 자동으로 해결됩니다.

4. **차원 사용됨** - [메트릭의 차원 값](./alerts-metric-overview.md#using-dimensions)을 선택한 경우 경고 규칙에서는 개별 메트릭 시계열을 모니터링하여(차원 값의 조합으로 정의된 대로) 임계값 위반을 확인합니다. 선택한 차원이 없는 집계 메트릭 시계열도 모니터링하려면 차원을 선택하지 않고 메트릭에 대한 경고 규칙을 추가로 구성합니다.

5. **집계 및 시간 세분성** - [메트릭 차트](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)를 사용하여 메트릭을 시각화하는 경우 다음을 확인합니다.
    * 선택한 메트릭 차트의 **집계** 가 경고 규칙의 **집계 유형** 과 동일합니다.
    * 선택한 **시간 세분성** 이 자동이 아니라 경고 규칙의 **집계 세분성(기간)** 과 동일합니다.

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>메트릭 검색 경고가 발생하면 안 되는 상황에서 발생

메트릭 경고가 발생하면 안 되는 상황에서 발생했다고 생각되는 경우 다음 단계를 수행하면 문제 해결에 도움이 될 수 있습니다.

1. [발생한 경고 목록](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)을 검토하여 발생한 경고를 찾은 후 클릭하여 세부 정보를 확인합니다. **이 경고가 발생한 이유는 무엇입니까?** 아래에서 제공된 정보를 검토하여 경고가 트리거되었던 시간의 메트릭 차트, **메트릭 값** 및 **임계값** 을 확인합니다.

    > [!NOTE] 
    > 동적 임계값 조건 유형을 사용 중이며 사용된 임계값이 올바르지 않다고 생각될 경우, 찡그린 아이콘을 사용하여 피드백을 보내주세요. 이 피드백은 기계 학습 알고리즘 연구에 영향을 주어 향후 감지 결과 개선에 도움을 줄 것입니다.

2. 메트릭의 차원 값을 여러 개 선택한 경우 메트릭 시계열 중 **하나라도** 임계값을 위반하면 경고가 트리거됩니다(차원 값 조합의 정의에 따름). 메트릭 경고의 차원 사용에 대한 자세한 내용은 [여기](./alerts-metric-overview.md#using-dimensions)를 참조하세요.

3. 경고 규칙 구성을 검토하여 제대로 구성되었는지 확인합니다.
    - **집계 유형**, **집계 세분성(기간)** 및 **임계값** 또는 **민감도** 가 예상대로 구성되었는지 확인합니다.
    - 동적 임계값을 사용하는 경고 규칙의 경우 고급 설정이 구성되었는지 확인하세요. **위반 횟수** 가 경고를 필터링할 수 있고 **이전 데이터 무시** 가 임계값 계산 방법에 영향을 미칠 수 있기 때문입니다.

   > [!NOTE]
   > 동적 임계값은 활성 상태가 되려면 3일 이상 및 메트릭 샘플 30개 이상이 필요합니다.

4. [메트릭 차트](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)를 사용하여 메트릭을 시각화하는 경우 다음 사항을 확인합니다.
    - 선택한 메트릭 차트의 **집계** 가 경고 규칙의 **집계 유형** 과 동일합니다.
    - 선택한 **시간 세분성** 이 자동이 아니라 경고 규칙의 **집계 세분성(기간)** 과 동일합니다.

5. 이미 발생한 경고가 동일한 기준(해결되지 않은)을 모니터링하는 동안 다시 발생한 경우 경고 규칙이 경고를 자동으로 해결하지 않도록 구성되었는지 확인합니다. 이러한 구성으로 인해 경고 규칙이 상태 비저장이 됩니다. 즉, 경고 규칙은 발생된 경고를 자동으로 해결하지 않으며 동일한 시계열에서 다시 발생하기 전에 발생한 경고를 해결할 필요가 없습니다.
    다음 방법 중 하나를 선택하여 경고 규칙을 자동으로 해결하지 않도록 구성되었는지 확인할 수 있습니다.
    - Azure Portal에서 경고 규칙을 편집하고 '경고 자동 해결' 확인란이 선택 취소되어 있는지 확인합니다('경고 규칙 세부 정보' 섹션에서 사용 가능).
    - 경고 규칙을 배포하는 데 사용된 스크립트를 검토하거나 경고 규칙 정의를 검색하고 *autoMitigate* 속성이 **false** 로 설정되어 있는지 확인합니다.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>가상 머신 게스트 메트릭에 대해 경고하는 메트릭을 찾을 수 없음

가상 머신의 게스트 운영 체제 메트릭에 대해 경고하려면(예: 메모리, 디스크 공간) 해당 데이터를 Azure Monitor 메트릭에 수집하는 데 필요한 에이전트를 설치했는지 확인합니다.
- [Windows VM의 경우](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM의 경우](../essentials/collect-custom-metrics-linux-telegraf.md)

가상 머신의 게스트 운영 체제에서 데이터를 수집하는 방법에 대한 자세한 내용은 [여기](../vm/monitor-vm-azure.md#guest-operating-system)를 참조하세요.

> [!NOTE] 
> Log Analytics 작업 영역으로 보낼 게스트 메트릭을 구성한 경우 이러한 메트릭은 Log Analytics 작업 영역 리소스에 표시되며, 이를 모니터링하는 경고 규칙을 **만든 후에만** 데이터가 표시됩니다. 이렇게 하려면 [로그에 대한 메트릭 경고를 구성](./alerts-metric-logs.md#configuring-metric-alert-for-logs)하는 단계를 수행합니다.

> [!NOTE] 
> 단일 경고 규칙을 사용하여 여러 가상 머신에 대한 게스트 메트릭을 모니터링하는 기능은 현재 메트릭 경고에서 지원되지 않습니다. [로그 경고 규칙](./alerts-unified-log.md)을 사용하여 이를 수행할 수 있습니다. 이렇게 하려면 게스트 메트릭이 Log Analytics 작업 영역으로 수집되는지 확인하고 작업 영역에서 로그 경고 규칙을 만듭니다.

## <a name="cant-find-the-metric-to-alert-on"></a>경고할 메트릭을 찾을 수 없음

특정 메트릭에 대한 경고를 찾고 있지만 경고 규칙을 만들 때 볼 수 없는 경우 다음을 확인합니다.
- 리소스에 대한 메트릭이 표시되지 않는 경우 [메트릭 경고에 대해 리소스 유형이 지원되는지 확인](./alerts-metric-near-real-time.md)합니다.
- 리소스에 대한 일부 메트릭을 볼 수 있지만 특정 메트릭을 찾을 수 없는 경우, [메트릭을 사용할 수 있는지 확인](../essentials/metrics-supported.md)하고 필요한 경우 메트릭 설명을 참조하여 리소스의 특정 버전에서만 사용할 수 있는지 확인합니다.
- 메트릭을 리소스에 사용할 수 없는 경우 리소스 로그에서 사용할 수 있으며 로그 경고를 사용하여 모니터링할 수 있습니다. [Azure 리소스에서 리소스 로그를 수집하고 분석](../essentials/tutorial-resource-logs.md)하는 방법에 대한 자세한 내용은 여기를 참조하세요.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>경고한 메트릭 차원을 찾을 수 없음

[메트릭의 특정 차원 값](./alerts-metric-overview.md#using-dimensions)에 대한 경고가 표시되지만 이러한 값을 찾을 수 없는 경우 다음을 참조하세요.

1. **차원 값** 목록에 차원 값이 표시되는 데 몇 분 정도 걸릴 수 있습니다.
2. 표시되는 차원 값은 과거에 수집된 메트릭 데이터를 기반으로 합니다.
3. 차원 값이 아직 내보내지 않았거나 표시되지 않는 경우 '사용자 지정 값 추가' 옵션을 사용하여 사용자 지정 차원 값을 추가할 수 있습니다.
4. 미래 값을 포함하여 한 차원의 가능한 모든 값에 대해 경고하려면 ‘모든 현재 및 미래 값 선택’ 옵션을 선택합니다.
5. Application Insights 리소스의 사용자 지정 메트릭 차원은 기본적으로 꺼져 있습니다. 이러한 사용자 지정 메트릭에 대한 차원 모음을 사용하려면 [여기](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)를 참조하세요.

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>삭제된 리소스에 대한 메트릭 경고 규칙이 여전히 정의되어 있음 

Azure 리소스를 삭제하면 연결된 메트릭 경고 규칙이 자동으로 삭제되지 않습니다. 삭제된 리소스에 연결된 경고 규칙을 삭제하려면 다음을 수행합니다.

1. 삭제된 리소스가 정의된 리소스 그룹을 엽니다.
1. 리소스를 표시하는 목록에서 **숨겨진 유형 표시** 확인란을 선택합니다.
1. 유형 == **microsoft metricalerts/** 별로 목록을 필터링합니다.
1. 관련 경고 규칙을 선택하고 **삭제** 를 선택합니다.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>조건이 충족될 때마다 메트릭 경고가 발생하도록 설정

메트릭 경고는 기본적으로 상태 저장이므로 지정된 시계열에 이미 경고가 발생했다면 추가 경고가 발생하지 않습니다. 특정 메트릭 경고 규칙 상태 비저장을 만들고 경고 조건이 충족되는 모든 평가에 대한 경고를 받으려면 다음 옵션 중 하나를 수행합니다.
- 프로그래밍 방식으로 경고 규칙을 만드는 경우(예: [Resource Manager](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert) 사용) *autoMitigate* 속성을 'False'로 변경합니다.
- Azure Portal을 통해 경고 규칙을 만드는 경우 '경고 자동 해결' 옵션을 선택 취소합니다('경고 규칙 세부 정보' 섹션에서 사용 가능).

> [!NOTE] 
> 메트릭 경고 규칙을 상태 비저장으로 설정하면 발생한 경고가 해결되지 않습니다. 따라서 조건이 더 이상 충족되지 않는 경우에도 발생한 경고는 30일의 보존 기간이 끝날 때까지 발생함 상태로 유지됩니다.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>아직 내보내지 않은 사용자 지정 메트릭의 경고 규칙 지정

메트릭 경고 규칙을 만드는 경우 메트릭 이름은 [메트릭 정의 API](/rest/api/monitor/metricdefinitions/list)에 대해 유효성을 검사를 거쳐 해당 이름이 존재하는지 확인합니다. 경우에 따라 아직 내보내기 전부터 사용자 지정 메트릭에 대한 경고 규칙을 만들 수도 있습니다. 예를 들어 Resource Manager 템플릿을 사용하여 사용자 지정 메트릭을 내보내는 Application Insights 리소스를 만들 때 해당 메트릭을 모니터링하는 경고 규칙을 만들 수 있습니다.

사용자 지정 메트릭의 정의 유효성을 검사하는 동안 배포가 실패하지 않도록 하려면 경고 규칙의 조건 섹션에서 *skipMetricValidation* 매개 변수를 사용할 수 있습니다. 이렇게 하면 메트릭 유효성 검사를 건너뜁니다. Resource Manager 템플릿에서 이 매개 변수를 사용하는 방법은 아래 예제를 참조하세요. 자세한 내용은 [메트릭 경고 규칙에 대한 Resource Manager 템플릿 샘플 완료](./alerts-metric-create-templates.md)를 참조하세요.

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```
> [!NOTE] 
> 3 일 넘게 내보내지 않은 기존 사용자 지정 메트릭에 대 한 경고 규칙을 정의 하는 경우에도 *skipMetricValidation* 매개 변수를 사용 해야 합니다.

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Azure Portal을 통해 메트릭 경고 규칙의 Azure Resource Manager 템플릿 내보내기

메트릭 경고 규칙의 Resource Manager 템플릿을 내보내면 해당 JSON 구문과 속성을 이해하는 데 도움이 되며 향후 배포를 자동화할 때도 사용할 수 있습니다.
1. Azure Portal에서 경고 규칙을 열어 세부 정보를 확인합니다.
2. **속성** 을 클릭합니다.
3. **자동화** 에서 **템플릿 내보내기** 를 선택합니다.

## <a name="metric-alert-rules-quota-too-small"></a>메트릭 경고 규칙 할당량이 너무 적음

구독당 메트릭 경고 규칙에 허용되는 수는 [할당량 한도](../service-limits.md)에 따라 결정됩니다.

할당량 한도에 도달했다면 다음 단계를 통해 문제를 해결할 수 있습니다.
1. 더 이상 사용되지 않는 메트릭 경고 규칙을 삭제하거나 비활성화합니다.

2. 여러 리소스를 모니터링하는 메트릭 경고 규칙을 사용하도록 전환합니다. 이 기능을 사용하면 단일 경고 규칙이 할당량에 대해 하나의 경고 규칙만 계산하여 여러 리소스를 모니터링할 수 있습니다. 이 기능과 지원되는 리소스 유형에 대한 자세한 내용은 [여기](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 참조하세요.

3. 할당량 한도를 늘려야 하는 경우 지원 요청을 열고 다음 정보를 제공하세요.

    - 할당량 한도를 늘려야 하는 구독 ID
    - 할당량을 늘릴 리소스 종류: **메트릭 경고** 또는 **메트릭 경고(클래식)**
    - 요청된 할당량 한도

## <a name="check-total-number-of-metric-alert-rules"></a>메트릭 경고 규칙의 총 수 확인

메트릭 경고 규칙의 현재 사용량을 확인하려면 아래 단계를 수행합니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

1. **경고** 화면을 열고 **경고 규칙 관리** 를 클릭합니다.
2. **구독** 드롭다운 컨트롤을 사용하여 관련 구독을 필터링합니다.
3. 특정 리소스 그룹, 리소스 유형 또는 리소스로 필터링하지 않도록 합니다.
4. **신호 형식** 드롭다운 컨트롤에서 **메트릭** 을 선택합니다.
5. **상태** 드롭다운 컨트롤이 **사용** 으로 설정되어 있는지 확인합니다.
6. 메트릭 경고 규칙의 총 수가 경고 규칙 목록 위에 표시됩니다.

### <a name="from-api"></a>API에서

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [구독별 목록](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Resource Manager 템플릿, REST API, PowerShell 또는 Azure CLI를 사용하여 경고 규칙 관리

Resource Manager 템플릿, REST API, PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 메트릭 경고를 만들거나, 업데이트하거나, 검색하거나, 삭제하는 데 문제가 있는 경우 다음 단계에 따라 문제를 해결할 수 있습니다.

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿

- [일반적인 Azure 배포 오류](../../azure-resource-manager/templates/common-deployment-errors.md) 목록을 검토하고 그에 따라 문제를 해결합니다.
- 모든 매개 변수를 올바르게 전달하는지 확인하려면 [메트릭 경고 Azure Resource Manager 템플릿 예제](./alerts-metric-create-templates.md)를 참조하세요.

### <a name="rest-api"></a>REST API

[REST API 가이드](/rest/api/monitor/metricalerts/)를 검토하여 모든 매개 변수를 올바르게 전달하고 있는지 확인합니다.

### <a name="powershell"></a>PowerShell

메트릭 경고에 적합한 PowerShell cmdlet을 사용하고 있는지 확인합니다.

- 메트릭 경고에 대한 PowerShell cmdlet은 [Az.Monitor 모듈](/powershell/module/az.monitor/)에서 사용할 수 있습니다.
- 클래식이 아닌 새 메트릭 경고의 경우 ‘V2’로 끝나는 cmdlet을 사용해야 합니다(예: [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2)).

### <a name="azure-cli"></a>Azure CLI

메트릭 경고에 적합한 CLI 명령을 사용하고 있는지 확인합니다.

- 메트릭 경고에 대한 CLI 명령은 `az monitor metrics alert`로 시작합니다. [Azure CLI 참조](/cli/azure/monitor/metrics/alert)를 검토하여 구문에 대해 알아봅니다.
- [메트릭 경고 CLI를 사용하는 방법을 보여주는 샘플](./alerts-metric.md#with-azure-cli)을 볼 수 있습니다.
- 사용자 지정 메트릭에 대해 경고하려면 다음과 같이 메트릭 이름 앞에 관련 메트릭 네임스페이스를 접두사로 지정해야 합니다. NAMESPACE.METRIC

### <a name="general"></a>일반

- `Metric not found` 오류를 수신하는 경우:

   - 플랫폼 메트릭의 경우 **메트릭 표시 이름** 이 아닌 [Azure Monitor 지원되는 메트릭 페이지](../essentials/metrics-supported.md)의 **메트릭** 이름을 사용 중인지 확인합니다.

   - 사용자 지정 메트릭의 경우 메트릭이 이미 내보내지고 있는지 확인하고(아직 존재하지 않는 사용자 지정 메트릭에는 경고 규칙을 만들 수 없음) 사용자 지정 메트릭의 네임스페이스를 제공하고 있는지 확인합니다(Resource Manager 템플릿 예제 [여기](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric) 참조).

- [로그에 대한 메트릭 경고](./alerts-metric-logs.md)를 만드는 경우 적절한 종속성이 포함되었는지 확인합니다. [샘플 템플릿](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)을 참조하세요.

- 여러 조건을 포함하는 경고 규칙을 만드는 경우 다음 제약 조건을 확인하세요.

   - 각 조건 내에서 차원당 하나의 값만 선택할 수 있습니다.
   - "\*"를 차원 값으로 사용할 수 없습니다.
   - 다른 조건에서 구성된 메트릭이 동일한 차원을 지원하는 경우 구성된 차원 값은 이러한 모든 메트릭에 대해 동일한 방식으로 명시적으로 설정되어야 합니다(Resource Manager 템플릿 예제는 [여기](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)에서 참조).


## <a name="no-permissions-to-create-metric-alert-rules"></a>메트릭 경고 규칙을 만들 수 있는 권한 없음

메트릭 경고 규칙을 만들려면 다음과 같은 권한이 있어야 합니다.

- 경고 규칙의 대상 리소스에 대한 읽기 권한
- 경고 규칙이 만들어지는 리소스 그룹에 대한 쓰기 권한(Azure Portal에서 경고 규칙을 만드는 경우 경고 규칙은 기본적으로 대상 리소스가 있는 리소스 그룹에 만들어짐)
- 경고 규칙에 연결된 모든 작업 그룹에 대한 읽기 권한(해당하는 경우)


## <a name="naming-restrictions-for-metric-alert-rules"></a>메트릭 경고 규칙에 대한 명명 제한

메트릭 경고 규칙 이름에 대한 다음과 같은 제한 사항을 고려하세요.

- 메트릭 경고 규칙 이름을 만든 후에는 변경(이름 다시 지정)할 수 없습니다.
- 메트릭 경고 규칙 이름은 각 리소스 그룹 내에서 고유해야 합니다.
- 메트릭 경고 규칙 이름에는 * # & + : < > ? 문자를 포함할 수 없습니다. @ % { } \ / 
- 메트릭 경고 규칙 이름은 공백 또는 마침표로 끝날 수 없습니다.
- 결합된 리소스 그룹 이름과 경고 규칙 이름은 252자를 초과할 수 없습니다.

> [!NOTE] 
> 경고 규칙 이름에 영문자 또는 숫자가 아닌 문자가 포함된 경우(예: 공백, 문장 부호 또는 기호) 해당 문자는 특정 클라이언트에서 검색할 때 URL로 인코딩될 가능성이 있습니다.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>여러 조건이 있는 메트릭 경고 규칙에서 차원을 사용할 때의 제한 사항

메트릭 경고는 다차원 메트릭에 대한 경고 및 여러 조건의 정의를 지원합니다(경고 규칙당 최대 5개 조건까지 정의할 수 있음).

여러 조건이 포함된 경고 규칙에서 차원을 사용하는 경우 다음 제약 조건을 고려하세요.
- 각 조건 내에서 차원당 하나의 값만 선택할 수 있습니다.
- “모든 현재 및 미래 값 선택” 옵션을 사용할 수 없습니다(\* 선택).
- 다른 조건으로 구성된 메트릭에서 동일한 차원을 지원하는 경우 구성된 차원 값은 관련 조건의 모든 메트릭에 대해 동일한 방식으로 명시적으로 설정해야 합니다.
예를 들면 다음과 같습니다.
    - 스토리지 계정에 정의되고 두 개의 조건을 모니터링하는 메트릭 경고 규칙을 고려해 보세요.
        * 총 **Transactions** > 5
        * 평균 **SuccessE2ELatency** > 250밀리초
    - 첫 번째 조건을 업데이트하고 **ApiName** 차원이 *“GetBlob”* 과 일치하는 트랜잭션만 모니터링하고 싶습니다.
    - **Transactions** 와 **SuccessE2ELatency** 메트릭은 모두 **ApiName** 차원을 지원하므로 두 조건을 모두 업데이트해야 하며 두 조건 모두에 대해 *“GetBlob”* 값을 가진 **ApiName** 차원을 지정해야 합니다.

## <a name="setting-the-alert-rules-period-and-frequency"></a>경고 규칙의 기간 및 빈도 설정

다음 경우에는 추가된 시계열에 대한 첫 번째 평가가 누락될 가능성을 줄이기 위해 *평가 빈도* 보다 큰 *집계 세분성(기간)* 을 선택하는 것이 좋습니다.
-   여러 차원을 모니터링하는 메트릭 경고 규칙 - 새로운 차원 값 조합이 추가된 경우
-   여러 리소스를 모니터링하는 메트릭 경고 규칙 - 범위에 새 리소스가 추가된 경우
-   연속으로 내보내지 않는 메트릭을 모니터링하는 메트릭 경고 규칙(스파스 메트릭) – 메트릭을 내보내지 않은 기간이 24시간을 경과한 후에 메트릭을 내보내는 경우

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>동적 임계값 경계가 데이터와 맞지 않음

메트릭 동작이 최근에 변경된 경우, 변경 내용이 동적 임계값 경계(상한 및 하한 범위)에 즉시 반영되지 않을 수 있습니다. 이는 지난 10일 동안의 메트릭 데이터를 기반으로 계산되기 때문입니다. 지정된 메트릭에 대한 동적 임계값 경계를 볼 때는 최근 시간이나 일뿐만 아니라 지난주의 메트릭 추세 또한 확인해야 합니다.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>동적 임계값이 주간 계절성을 검색하지 못하는 이유

주간 계절성을 식별하려면 동적 임계값 모델에 3주 이상의 기록 데이터가 필요합니다. 기록 데이터를 충분히 사용할 수 있게 되면 메트릭 데이터에 존재하는 주간 계절성이 식별되며 모델은 그에 따라 조정됩니다. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>메트릭에 항상 양수 값이 있는 경우에도 동적 임계값이 메트릭에 대한 음수 하한값을 표시함

메트릭이 큰 변동을 나타내는 경우 동적 임계값은 메트릭 값을 중심으로 보다 범위가 넓은 모델을 생성하며, 이로 인해 하한 경계가 0 미만이 될 수 있습니다. 이는 특히 다음과 같은 사례에서 발생할 수 있습니다.
1. 민감도를 낮음으로 설정함 
2. 중앙값이 0에 가까움
3. 메트릭이 가변성이 큰 불규칙한 동작을 보임(데이터가 급증하거나 급감함)

하한 경계가 음수 값을 갖는 경우 이는 메트릭의 불규칙한 동작을 감안할 때 메트릭이 0의 값에 도달할 수 있다는 것을 의미합니다. 민감도를 더 높게 선택하거나 더 큰 *집계 세분성(기간)* 을 선택하여 모델의 민감성을 낮추세요. *다음 이전의 데이터 무시* 옵션을 사용하여 모델 빌드에 사용된 이전 데이터에서 발생한 최근의 불규칙성을 제외할 수도 있습니다.

## <a name="the-dynamic-thresholds-alert-rule-is-too-noisy-fires-too-much"></a>동적 임계값 경고 규칙에 노이즈가 많음(너무 많이 발생함)
동적 임계값 경고 규칙의 민감도를 줄이려면 다음 옵션 중 하나를 사용합니다.
1. 임계값 민감도 - 편차에 대한 내성을 높이기 위해 민감도를 *낮음* 으로 설정합니다.
2. 위반 횟수(*고급 설정* 아래) - 특정 기간 내에 여러 편차가 발생하는 경우에만 트리거하도록 경고 규칙을 구성합니다. 이렇게 하면 규칙이 일시적인 편차에 덜 취약해질 수 있습니다.


## <a name="the-dynamic-thresholds-alert-rule-is-too-insensitive-doesnt-fire"></a>동적 임계값 경고 규칙은 너무 민감하지 않음(발생하지 않음)
경우에 따라 높은 민감도가 구성된 경우에도 경고 규칙이 트리거되지 않습니다. 이 문제는 일반적으로 메트릭의 분포가 매우 불규칙한 경우에 발생합니다.
다음 옵션 중 하나를 고려합니다.
* 시나리오에 적합한 보완 메트릭 모니터링으로 이동합니다(해당하는 경우). 예를 들어 실패율이 아닌 성공률의 변경 내용을 확인합니다.
* 다른 집계 세분성(기간)을 선택해 봅니다. 
* 지난 10일 동안 메트릭 동작이 크게 변경되었는지 확인합니다(중단). 급격한 변경은 메트릭에 대해 계산된 상한 및 하한 임계값에 영향을 주고 더 광범위하게 만들 수 있습니다. 중단이 더 이상 임계값 계산에 반영되지 않을 때까지 며칠 동안 기다리거나 *다음 이전의 데이터 무시* 옵션을 사용합니다(*고급 설정* 아래).
* 데이터에 주간 계절성이 있지만 메트릭에 사용할 수 있는 기록이 충분하지 않은 경우 계산된 임계값으로 인해 광범위한 상한과 하한이 발생할 수 있습니다. 예를 들어 계산은 평일과 주말을 동일한 방식으로 처리하고 항상 데이터에 맞지 않는 넓은 테두리를 빌드할 수 있습니다. 이 문제는 충분한 메트릭 기록을 사용할 수 있게 되면 자체적으로 해결되어야 하며, 이 시점에서 올바른 계절성이 검색되고 계산된 임계값이 그에 따라 업데이트됩니다.


## <a name="next-steps"></a>다음 단계

- 경고 및 알림에 대한 일반적인 문제 해결 정보는 [Azure Monitor 경고 문제 해결](alerts-troubleshoot.md)을 참조하세요.
