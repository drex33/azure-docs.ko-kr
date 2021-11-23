---
title: 대상 리소스가 다른 Azure 지역으로 이동 될 때 경고 규칙 또는 경고 처리 규칙을 업데이트 하는 방법
description: 대상 리소스를 다른 Azure 지역으로 이동할 때 경고 규칙 또는 경고 처리 규칙을 업데이트 하는 방법에 대 한 배경 및 지침입니다.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: f255b7917f31199c999e219c7e583bb5b535c077
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132939713"
---
# <a name="how-to-update-alert-rules-or-alert-processing-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>대상 리소스가 다른 Azure 지역으로 이동 될 때 경고 규칙 또는 경고 처리 규칙을 업데이트 하는 방법

이 문서에서는 지역 간에 다른 Azure 리소스를 이동할 때 기존 [경고 규칙](./alerts-overview.md) 및 [경고 처리 규칙이](./alerts-action-rules.md) 영향을 받을 수 있는 이유와 이러한 문제를 식별 하 고 해결 하는 방법을 설명 합니다. 지역 간 리소스 이동이 유용한 경우에 대한 추가 정보와 이동 프로세스의 설계를 위한 체크리스트를 보려면 기본 [리소스 이동 설명서](../../azure-resource-manager/management/move-resources-overview.md)를 확인하세요.

## <a name="why-the-problem-exists"></a>문제가 존재하는 이유

경고 규칙 및 경고 처리 규칙은 다른 Azure 리소스를 참조 합니다. 예를 들어 [Azure VM](../../site-recovery/azure-to-azure-tutorial-migrate.md), [Azure SQL](../../azure-sql/database/move-resources-across-regions.md) 및 [Azure Storage](../../storage/common/storage-account-move.md)가 있습니다. 이러한 규칙이 참조하는 리소스를 이동하면 해당 규칙이 참조하는 리소스를 찾을 수 없기 때문에 올바르게 작동하지 않을 수 있습니다.

대상 리소스를 이동한 후 규칙의 작동이 중지될 수 있는 두 가지 주요 이유는 다음과 같습니다.

- 규칙의 범위가 기존 리소스를 명시적으로 참조합니다.
- 경고 규칙이 메트릭을 기반으로 합니다.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>규칙 범위가 기존 리소스를 명시적으로 참조함

리소스를 이동하면 대부분의 경우 리소스 ID가 변경됩니다. 시스템은 기존 지역에서 리소스를 삭제하기 전에 백그라운드에서 리소스를 새 지역에 복제합니다. 이 프로세스를 수행하려면 두 개의 리소스와 두 가지 리소스 ID가 짧은 시간 동안 동시에 존재해야 합니다. 리소스 ID는 고유해야 하므로 프로세스 중에 새 ID를 만들어야 합니다. 

**리소스 이동이 기존 규칙에 미치는 영향**

경고 규칙 및 경고 처리 규칙에는 적용 되는 리소스 범위가 있습니다. 범위는 전체 구독, 리소스 그룹 또는 하나 이상의 특정 리소스가 될 수 있습니다.
예를 들어 두 개의 리소스(두 개의 가상 머신)가 있는 범위에 대한 규칙은 다음과 같습니다.

![다중 리소스 경고 규칙](media/alerts-resource-move/multi-resource-alert-rule.png)

규칙 범위에서 리소스를 명시적으로 언급하고 해당 리소스가 이동하여 리소스 ID가 변경된 경우 해당 규칙은 잘못되었거나 존재하지 않는 리소스를 검색하므로 실패합니다.

**문제를 해결하는 방법**

영향을 받는 규칙을 업데이트하거나 다시 만들어 새 리소스를 가리키도록 합니다. 범위를 업데이트하는 프로세스는 이 문서의 뒷부분에 나와 있습니다.

이 문제는 다음 규칙 유형에 적용됩니다.

- 활동 로그 경고 규칙
- 경고 처리 규칙
- 메트릭 경고 – 자세한 내용은 다음 섹션 [메트릭 기반 경고 규칙](#alert-rules-based-on-metrics)을 참조하세요.

> [!NOTE]
> 로그 검색 경고 규칙 및 스마트 탐지기 경고 규칙의 범위는 작업 영역이거나 Application Insights이므로 영향을 받지 않습니다. 현재 이러한 범위는 지역 이동을 지원하지 않습니다.

## <a name="alert-rules-based-on-metrics"></a>메트릭 기반 경고 규칙

Azure 리소스에서 내보내는 메트릭은 지역별 메트릭입니다. 리소스가 새 지역으로 이동할 때마다 새 지역에서 해당 메트릭의 내보내기를 시작합니다. 따라서 메트릭을 기반으로 하는 모든 경고 규칙은 올바른 지역의 현재 메트릭 스트림을 가리키도록 업데이트하거나 다시 만들어야 합니다.

이 설명은 [메트릭 경고 규칙](alerts-metric-overview.md) 및 [가용성 테스트 경고 규칙](../app/monitor-web-app-availability.md) 모두에 적용됩니다.

범위에 있는 **모든** 리소스를 이동한 경우에는 규칙을 다시 만들 필요가 없습니다. 경고 규칙 설명과 같은 경고 규칙의 모든 필드를 업데이트하고 저장할 수 있습니다.
범위에 있는 리소스 중 **일부만** 이동한 경우에는 기존 규칙에서 이동한 리소스를 제거하고 이동한 리소스에만 적용되는 새 규칙을 만들어야 합니다.

## <a name="procedures-to-fix-problems"></a>문제 해결 절차

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Azure Portal에서 이동한 리소스와 연결된 규칙 식별

- **경고 규칙** - 경고 > 경고 규칙 관리로 이동하고 포함 구독 및 이동한 리소스로 필터링합니다.
> [!NOTE]
> 활동 로그 경고 규칙은 이 프로세스를 지원하지 않습니다. 활동 로그 경고 규칙의 범위를 업데이트하고 다른 구독의 리소스를 가리키도록 할 수는 없습니다. 대신, 기존 규칙을 대체할 새 규칙을 만들 수 있습니다.

- **경고 처리 규칙의 경우** -경고 처리 규칙 (미리 보기) > 경고로 이동 하 > 포함 하는 구독과 이동 된 리소스를 기준으로 필터링 합니다.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Azure Portal에서 규칙의 범위 변경

1. 이전 단계에서 확인한 규칙을 클릭하여 엽니다.
2. **리소스** 에서 **편집** 을 클릭하고 필요에 따라 범위를 조정합니다.
3. 필요에 따라 규칙의 다른 속성을 조정합니다.
4. **저장** 을 클릭합니다.

![경고 규칙 범위 변경](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 규칙의 범위 변경

1. 규칙의 Azure Resource Manager 템플릿을 가져옵니다.   Azure Portal에서 규칙의 템플릿을 내보내려면 다음을 수행합니다.
   1. 포털에서 리소스 그룹 섹션으로 이동하고 규칙을 포함하는 리소스 그룹을 엽니다.
   2. 개요 섹션에서 **숨김 유형 표시** 확인란을 선택하고 규칙의 관련 유형을 기준으로 필터링합니다.
   3. 관련 규칙을 선택하여 세부 정보를 봅니다.
   4. **설정** 아래에서 **템플릿 내보내기** 를 선택합니다.
2. 템플릿을 수정합니다. 필요한 경우 두 가지 규칙으로 분할합니다(위에 나와 있는 메트릭 경고의 일부 사례에 적합).
3. 템플릿을 다시 배포합니다.

### <a name="change-scope-of-a-rule-using-rest-api"></a>REST API를 사용하여 규칙의 범위 변경

1. 기존 규칙 가져오기([메트릭 경고](/rest/api/monitor/metricalerts/get), [활동 로그 경고](/rest/api/monitor/activitylogalerts/get))
2. 범위 수정([활동 로그 경고](/rest/api/monitor/activitylogalerts/update))
3. 규칙 다시 배포([메트릭 경고](/rest/api/monitor/metricalerts/createorupdate), [활동 로그 경고](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>PowerShell을 사용하여 규칙의 범위 변경

1. 기존 규칙 ([메트릭 경고](/powershell/module/az.monitor/get-azmetricalertrulev2), [활동 로그 경고](/powershell/module/az.monitor/get-azactivitylogalert), [경고 처리 규칙](/powershell/module/az.alertsmanagement/get-azactionrule))을 가져옵니다.
2. 범위를 수정합니다. 필요한 경우 두 가지 규칙으로 분할합니다(위에 나와 있는 메트릭 경고의 일부 사례에 적합).
3. 규칙을 다시 배포 합니다 ([메트릭 경고](/powershell/module/az.monitor/add-azmetricalertrulev2), [활동 로그 경고](/powershell/module/az.monitor/enable-azactivitylogalert), [경고 처리 규칙](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Azure CLI를 사용하여 규칙의 범위 변경

1.  기존 규칙 가져오기([메트릭 경고](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_show), [활동 로그 경고](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log-alert_list))
2.  규칙 범위를 직접 업데이트([메트릭 경고](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_update), [활동 로그 경고](/cli/azure/monitor/activity-log/alert/scope))
3.  필요한 경우 두 가지 규칙으로 분할합니다(위에 나와 있는 메트릭 경고의 일부 사례에 적합).

## <a name="next-steps"></a>다음 단계

[경고 알림](alerts-troubleshoot.md), [메트릭 경고](alerts-troubleshoot-metric.md) 및 [로그 경고](alerts-troubleshoot-log.md)와 관련된 다른 문제를 해결하는 방법에 대해 알아봅니다.
