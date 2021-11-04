---
title: 현재 Azure Monitor 로그 경고 API로 업그레이드
description: 로그 경고 ScheduledQueryRules API로 전환하는 방법에 대해 알아보기
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f3d55bfe93ec3bcaa713e77db6326488851994d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026225"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>레거시 Log Analytics 경고 API에서 현재 로그 경고 API로 업그레이드

> [!NOTE]
> 이 문서는 Azure 퍼블릭(Azure Government 또는 Azure 중국 클라우드 **아님**)에만 해당됩니다.

> [!NOTE]
> 사용자가 기본 설정을 현재 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)로 전환하도록 선택하면 이전 [레거시 Log Analytics 경고 API](./api-alerts.md)로 되돌릴 수 없습니다.

이전에는 사용자가 [레거시 Log Analytics 경고 API](./api-alerts.md)를 사용하여 로그 경고 규칙을 관리했습니다. 현재 작업 영역에서는 [ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)를 사용합니다. 이 문서에서는 레거시 API에서 현재 API로 전환하는 프로세스와 이점에 대해 설명합니다.

## <a name="benefits"></a>이점

- 경고 규칙 생성을 위한 단일 템플릿(이전에는 별도의 템플릿이 3개 필요했음)
- 모든 Azure 리소스에 대 한 단일 API 로그 경고
- 상태 저장 및 1 분 로그 경고 미리 보기를 지원 합니다.
- [PowerShell cmdlet 지원](./alerts-log.md#managing-log-alerts-using-powershell)
- 다른 모든 경고 유형과 심각도 맞춤
- Log Analytics 작업 영역 또는 Application Insights 리소스와 같은 여러 외부 리소스를 포함하는 [교차 작업 영역 로그 경고](../logs/cross-workspace-query.md)를 만드는 기능
- 사용자는 경고를 나눌 차원을 지정할 수 있습니다.
- 로그 경고의 데이터 연장 기간은 최대 2일입니다(이전에는 1일로 제한됨).

## <a name="impact"></a>영향

- 모든 새 규칙은 현재 API로 만들고 편집해야 합니다. [Azure Resource Template을 통한 샘플 사용](alerts-log-create-templates.md)과 [PowerShell을 통한 샘플 사용](./alerts-log.md#managing-log-alerts-using-powershell)을 참조하세요.
- 규칙이 현재 API에서 Azure Resource Manager 추적 리소스가 되고 고유해야 하므로 규칙 리소스 ID가 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 구조로 변경됩니다. 경고 규칙의 표시 이름은 변경되지 않고 그대로 유지됩니다.

## <a name="process"></a>Process

전환 프로세스는 대화형이 아니며 대부분의 경우 수동 단계가 필요 없습니다. 경고 규칙은 전환 중 또는 전환 후 중지되거나 중단되지 않습니다.
특정 Log Analytics 작업 영역과 연결된 모든 경고 규칙을 전환하려면 이 호출을 수행합니다.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

아래 JSON을 포함하는 요청 본문을 사용합니다.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

다음은 위의 API 호출 호출을 간소화하는 오픈 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)의 사용 예입니다.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": true}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

전환이 성공하는 경우 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>작업 영역의 전환 상태 확인

이 API 호출을 사용하여 전환 상태를 확인할 수도 있습니다.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient) 도구를 사용할 수도 있습니다.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Log Analytics 작업 영역이 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)로 전환된 경우 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Log Analytics 작업 영역이 전환되지 않은 경우 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor - 로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
- [API를 사용하여 로그 경고를 관리](alerts-log-create-templates.md)하는 방법을 알아봅니다.
- [PowerShell을 사용하여 로그 경고를 관리](./alerts-log.md#managing-log-alerts-using-powershell)하는 방법을 알아봅니다.
- [Azure Alerts 환경](./alerts-overview.md)에 대해 자세히 알아봅니다.
