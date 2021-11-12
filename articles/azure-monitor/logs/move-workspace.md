---
title: Azure Monitor에서 Log Analytics 작업 영역 이동 | Microsoft Docs
description: Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동하는 방법을 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4962849bb08983bb821d5ffed90a8312a52e9172
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335787"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동

이 문서에서는 Log Analytics 작업 영역을 동일한 지역의 다른 리소스 그룹 또는 구독으로 이동하는 단계를 알아봅니다. Azure Portal, PowerShell, Azure CLI 또는 REST API를 통해 Azure 리소스를 이동하는 방법에 대해 자세히 알아볼 수 있는 사이트는 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)입니다. 

> [!IMPORTANT]
> 다른 지역으로는 작업 영역을 이동할 수 없습니다.

## <a name="verify-active-directory-tenant"></a>Active Directory 테넌트 확인
작업 영역 원본 및 대상 구독은 동일한 Azure Active Directory 테넌트 내에 있어야 합니다. Azure PowerShell을 사용하여 두 구독에 동일한 테넌트 ID가 있는지 확인할 수 있습니다.

```powershell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>작업 영역 이동 고려 사항
- 작업 영역에 설치된 관리형 솔루션은 Log Analytics 작업 영역 이동 작업을 통해 이동됩니다. 
- 작업 영역 키(기본 및 보조 모두)는 작업 영역 이동 작업을 통해 다시 생성됩니다. 작업 영역 키의 복사본을 키 자격 증명 모음에 보관하는 경우에는 작업 영역을 이동한 후 생성된 새 키로 업데이트합니다. 
- 연결된 에이전트가 연결된 상태를 유지하고 이동 후에도 작업 영역으로 데이터를 계속 전송합니다. 
- 이동 작업을 수행하려면 작업 영역에서 연결된 서비스가 없어야 하므로 작업 영역 이동이 가능하려면 해당 링크를 사용하는 솔루션을 제거해야 합니다. Automation 계정 연결을 해제하기 전에 제거해야 하는 솔루션은 다음과 같습니다.
  - 업데이트 관리
  - 변경 내용 추적
  - 작업이 없는 동안 VM 시작/중지
  - Microsoft Defender for Cloud

>[!IMPORTANT]
> **Microsoft Sentinel 고객**
> - 현재 Microsoft Sentinel이 작업 영역에 배포된 후에는 작업 영역을 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. 
> - 작업 영역을 이미 이동한 경우 **Analytics** 에서 모든 활성 규칙을 사용하지 않도록 설정하고 5분 후에 다시 사용하도록 설정합니다. 이 방법이 대부분의 경우에 효과적인 솔루션이어야 하지만, 이는 지원되지 않으며 사용자의 책임하에 수행됩니다.
> - 완료하는 데 몇 시간 Azure Resource Manager 걸릴 수 있으며 작업 중에 솔루션이 응답하지 않을 수 있습니다.
> 
> **경고 다시 만들기**
> - 권한은 작업 영역을 이동하거나 리소스 이름을 변경하는 중에 변경되는 작업 영역 리소스 ID를 기준으로 하기 때문에 모든 경고를 다시 만들어야 합니다. 2019년 6월 1일 이후에 만들어진 작업 영역 또는 [레거시 Log Analytics 경고 API에서 scheduledQueryRules API로 업그레이드](../alerts/alerts-log-api-switch.md)된 작업 영역의 경고는 템플릿에서 내보내고 이동 후 배포할 수 있습니다. [작업 영역에서 경고 시 scheduledQueryRules API가 사용되는지 확인](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)할 수 있습니다. 또는 대상 작업 영역에서 경고를 수동으로 구성할 수 있습니다.
>
> **리소스 경로 업데이트**
> - 작업 영역을 이동한 후에는 작업 영역을 가리키는 모든 Azure 또는 외부 리소스를 검토하고 새 리소스 대상 경로를 가리키도록 업데이트해야 합니다.
> 
>   *예:*
>   - [Azure Monitor 경고 규칙](../alerts/alerts-resource-move.md)
>   - 타사 애플리케이션
>   - 사용자 지정 스크립팅
>

### <a name="delete-solutions-in-azure-portal"></a>Azure Portal에서 솔루션 삭제
다음 절차에 따라 Azure Portal을 사용하여 솔루션을 제거할 수 있습니다.

1. 솔루션이 설치된 리소스 그룹의 메뉴를 엽니다.
2. 제거할 솔루션을 선택합니다.
3. **리소스 삭제** 를 클릭한 다음, **삭제** 를 클릭하여 제거할 리소스를 확인합니다.

![솔루션 삭제](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>PowerShell을 사용하여 삭제

PowerShell을 사용하여 솔루션을 제거하려면 다음 예제와 같이 [Remove-AzResource](/powershell/module/az.resources/remove-azresource) cmdlet을 사용합니다.

```powershell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>VM 시작/중지 솔루션에 대한 경고 규칙 제거
**VM 시작/중지** 솔루션을 제거하려면 이 솔루션에서 만든 경고 규칙도 제거해야 합니다. Azure Portal에서 다음 절차를 사용하여 이러한 규칙을 제거합니다.

1. **모니터** 메뉴를 열고 **경고** 를 선택합니다.
2. **경고 규칙 관리** 를 클릭합니다.
3. 다음 세 가지 경고 규칙을 선택한 다음, **삭제** 를 클릭합니다.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![규칙 삭제](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation 계정 연결 해제
다음 절차에 따라 Azure Portal을 사용하여 작업 영역에서 Automation 계정의 연결을 해제할 수 있습니다.

1. **Automation 계정** 메뉴를 열고 제거할 계정을 선택합니다.
2. 메뉴의 **관련 리소스** 섹션에서 **연결된 작업 영역** 을 선택합니다. 
3. **작업 영역 연결 해제** 를 클릭하여 Automation 계정에서 작업 영역의 연결을 해제합니다.

    ![작업 영역 연결 해제](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>작업 영역 이동

### <a name="azure-portal"></a>Azure portal
다음 절차에 따라 Azure Portal을 사용하여 작업 영역을 이동할 수 있습니다.

1. **Log Analytics 작업 영역** 메뉴를 열고 작업 영역을 선택합니다.
2. **개요** 페이지에서 **리소스 그룹** 또는 **구독** 옆의 **변경** 을 클릭합니다.
3. 작업 영역과 관련된 리소스 목록이 있는 새 페이지가 열립니다. 작업 영역과 동일한 대상 구독 및 리소스 그룹으로 이동할 리소스를 선택합니다. 
4. 대상 **구독** 및 **리소스 그룹** 을 선택합니다. 작업 영역을 동일한 구독의 다른 리소스 그룹으로 이동하는 경우에는 **구독** 옵션이 표시되지 않습니다.
5. **확인** 을 클릭하여 작업 영역 및 선택한 리소스를 이동합니다.

    ![리소스 그룹 및 구독 이름을 변경하는 옵션이 있는 Log Analytics 작업 영역의 개요 창을 보여주는 스크린샷](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
PowerShell을 사용하여 작업 영역을 이동하려면 [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource)를 다음 예제와 같이 사용합니다.

```powershell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 이동 작업 후 작업 영역을 이전 상태로 되돌리려면 제거된 솔루션 및 Automation 계정 링크를 다시 구성해야 합니다.


## <a name="next-steps"></a>다음 단계
- 이동을 지원하는 리소스 목록은 [리소스에 대한 이동 작업 지원](../../azure-resource-manager/management/move-support-resources.md)을 참조하세요.
