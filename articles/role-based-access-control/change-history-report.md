---
title: Azure RBAC 변경 사항에 대한 활동 로그 보기
description: 지난 90일 동안의 Azure RBAC(역할 기반 액세스 제어) 변경 사항에 대한 활동 로그를 봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 43ee281bc9696740c7909ef6b6758d4542808721
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755219"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Azure RBAC 변경 사항에 대한 활동 로그 보기

감사 또는 문제 해결 등의 목적으로 Azure RBAC(Azure 역할 기반 액세스 제어) 변경 사항에 대한 정보가 필요한 경우가 있습니다. 구독 내에서 역할 할당 또는 역할 정의가 변경될 때마다 변경 사항이 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에 기록됩니다. 활동 로그를 보면 지난 90일 동안의 Azure RBAC 변경 사항을 모두 확인할 수 있습니다.

## <a name="operations-that-are-logged"></a>기록되는 작업

다음은 활동 로그에 기록되는 Azure RBAC 관련 작업입니다.

- 역할 할당 만들기
- 역할 할당 삭제
- 사용자 지정 역할 정의 만들기 또는 업데이트
- 사용자 지정 역할 정의 삭제

## <a name="azure-portal"></a>Azure portal

가장 손쉽게 시작할 수 있는 방법은 Azure Portal을 사용하여 활동 로그를 보는 것입니다. 다음 스크린샷은 활동 로그의 역할 할당 작업 예를 보여 줍니다. 로그를 CSV 파일로 다운로드하는 옵션도 포함되어 있습니다.

![포털을 사용한 활동 로그 - 스크린샷](./media/change-history-report/activity-log-portal.png)

자세한 내용을 보려면 항목을 클릭하여 요약 창을 엽니다. 자세한 로그를 보려면 **JSON** 탭을 클릭합니다.

![요약 창이 열려 있는 포털을 사용하는 활동 로그 - 스크린샷](./media/change-history-report/activity-log-summary-portal.png)

포털에서 활동 로그에는 몇 가지 필터가 있습니다. Azure RBAC 관련 필터는 다음과 같습니다.

| 필터 | 값 |
| --------- | --------- |
| 이벤트 범주 | <ul><li>관리</li></ul> |
| 작업 | <ul><li>역할 할당 만들기</li><li>역할 할당 삭제</li><li>사용자 지정 역할 정의 만들기 또는 업데이트</li><li>사용자 지정 역할 정의 삭제</li></ul> |

활동 로그에 대한 자세한 내용은 [Azure 활동 로그 를 참조하세요.](../azure-monitor/essentials/activity-log.md)


## <a name="interpret-a-log-entry"></a>로그 항목 해석

JSON 탭, Azure PowerShell, Azure CLI의 로그 출력에는 많은 정보가 포함될 수 있습니다. 로그 항목을 해석하려고 할 때 찾아볼 수 있는 몇 가지 주요 속성은 다음과 같습니다. Azure PowerShell 또는 Azure CLI를 사용하여 로그 출력을 필터링하는 방법은 다음 섹션을 참조하세요.

> [!div class="mx-tableFixed"]
> | 속성 | 예제 값 | Description |
> | --- | --- | --- |
> | authorization:action | Microsoft.Authorization/roleAssignments/write | 역할 할당 만들기 |
> |  | Microsoft.Authorization/roleAssignments/delete | 역할 할당 삭제 |
> |  | Microsoft.Authorization/roleDefinitions/write | 역할 정의 만들기 또는 업데이트 |
> |  | Microsoft.Authorization/roleDefinitions/delete | 역할 정의 삭제 |
> | authorization:scope | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | 작업 범위 |
> | caller | admin@example.com<br/>{objectId} | 작업을 시작한 사람 |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | 작업이 발생한 시간 |
> | status:value | 시작됨<br/>성공<br/>실패 | 작업의 상태 |

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 활동 로그를 보려면 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 명령을 사용합니다.

이 명령은 지난 7일 동안 구독에서 발생한 모든 역할 할당 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

이 명령은 지난 7일 동안 리소스 그룹에서 발생한 모든 역할 정의 변경 내용을 나열합니다.

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>로그 출력 필터링

로그 출력에는 많은 정보가 포함될 수 있습니다. 다음 명령은 지난 7일 동안 구독에서 발생한 모든 역할 할당 및 역할 정의 변경 사항을 나열하고 출력을 필터링합니다.

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

다음은 역할 할당을 만들 때 필터링된 로그 출력의 예를 보여 줍니다.

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

서비스 주체를 사용하여 역할 할당을 만드는 경우 Caller 속성은 서비스 주체 개체 ID입니다. [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 사용하여 서비스 주체에 대한 정보를 가져올 수 있습니다.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 활동 로그를 보려면 [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) 명령을 사용합니다.

다음 명령은 3월 1일부터 7일 동안의 기간에 발생한 리소스 그룹의 활동 로그를 나열합니다.

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

다음 명령은 3월 1일부터 7일 동안의 기간에 발생한 권한 부여 리소스 공급자의 활동 로그를 나열합니다.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>로그 출력 필터링

로그 출력에는 많은 정보가 포함될 수 있습니다. 다음 명령은 7일 동안 구독에서 발생한 모든 역할 할당 및 역할 정의 변경 사항을 나열하고 출력을 필터링합니다.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

다음은 역할 할당을 만들 때 필터링된 로그 출력의 예를 보여 줍니다.

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)는 모든 Azure 리소스의 Azure RBAC 변경 사항을 수집하고 분석하는 데 사용할 수 있는 또 다른 도구입니다. Azure Monitor 로그에는 다음과 같은 이점이 있습니다.

- 복잡한 쿼리 및 로직 작성
- 경고, Power BI 및 기타 도구와 통합
- 장기 보존 기간 동안 데이터 저장
- 보안, 가상 머신, 사용자 지정과 같은 다른 로그와 상호 참조

기본적인 시작 단계는 다음과 같습니다.

1. [Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/quick-create-workspace.md).

1. 작업 영역에 대해 [활동 로그 분석 솔루션을 구성](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)합니다.

1. [활동 로그를 봅니다](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). 활동 로그 분석 솔루션 개요 페이지로 빠르게 이동하는 방법은 **로그** 옵션을 클릭하는 것입니다.

   ![포털의 Azure Monitor 로그 옵션](./media/change-history-report/azure-log-analytics-option.png)

1. 필요에 따라 [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)를 사용하여 로그를 쿼리하고 봅니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조하세요.

다음은 대상 리소스 공급자가 구성한 새 역할 할당을 반환하는 쿼리입니다.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

다음은 차트에 표시되는 역할 할당 변경 내용을 반환하는 쿼리입니다.

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![고급 분석 포털을 사용한 활동 로그 - 스크린샷](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>다음 단계
* [View activity logs to monitor actions on resources](../azure-monitor/essentials/activity-log.md)(활동 로그를 보고 리소스에 대한 작업을 모니터링)합니다.
* [Azure 활동 로그로 구독 활동을 모니터링](../azure-monitor/essentials/platform-logs-overview.md)합니다.
