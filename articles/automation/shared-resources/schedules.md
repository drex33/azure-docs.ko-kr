---
title: Azure Automation의 일정 관리
description: 이 문서에서는 Azure Automation에서 일정을 만들고 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 012380b98987bc09440d025b6533b62cc1a4c043
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271653"
---
# <a name="manage-schedules-in-azure-automation"></a>Azure Automation의 일정 관리

Azure Automation에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. Azure Portal에서 일정이 Runbook에 대해 한 번 실행되거나 매시간 또는 매일 반복되도록 구성할 수 있습니다. 매주, 매월, 주의 특정 요일이나 매월 특정 요일에 또는 특정 날짜에 실행되도록 예약할 수도 있습니다. Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

> [!NOTE]
> Azure Automation은 일광 절약 시간을 지원하고 자동화 작업에 맞게 적절히 예약합니다.

> [!NOTE]
> 현재 Azure Automation DSC 구성에는 일정을 사용할 수 없습니다.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>일정에 액세스하는 데 사용되는 PowerShell cmdlet

다음 표에 나와 있는 cmdlet은 PowerShell을 사용하여 Automation 일정을 만들고 관리합니다. Az 모듈의 일부로 제공됩니다.

| Cmdlet | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |일정을 검색합니다. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |예약된 Runbook을 검색합니다. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |새 일정을 만듭니다. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Runbook을 일정에 연결합니다. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |일정을 제거합니다. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |기존 일정에 대한 속성을 설정합니다. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |일정에서 Runbook을 분리합니다. |

## <a name="create-a-schedule"></a>일정 만들기

Azure Portal에서 PowerShell 또는 ARM(Azure Resource Manager) 템플릿을 사용하여 Runbook의 새 일정을 만들 수 있습니다. 자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 먼저 테스트 전용 Automation 계정으로 일정을 연결한 모든 Runbook을 테스트해야 합니다. 테스트는 예약된 Runbook이 계속 올바르게 작동하는지 검증합니다. 문제가 표시되면 업데이트된 Runbook 버전을 프로덕션으로 마이그레이션하기 전에 문제를 해결하고 필요한 변경 내용을 적용할 수 있습니다.

> [!NOTE]
> **모듈** 에서 [Azure 모듈 업데이트](../automation-update-azure-modules.md) 옵션을 선택하여 수동으로 업데이트하지 않는 한 Automation 계정에서 새 버전의 모듈을 자동으로 가져오지 않습니다. Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Azure Portal에서 새 일정 만들기

1. Automation 계정의 왼쪽 창에서 **공유 리소스** 아래에 있는 **일정** 을 선택합니다.
2. **일정** 페이지에서 **일정 추가** 를 선택합니다.
3. **새 일정** 창에서 이름을 입력하고, 선택적으로 새 일정에 대한 설명을 입력합니다.

    >[!NOTE]
    >Automation 일정은 현재 일정 이름에 특수 문자 사용을 지원하지 않습니다.
    >

4. **한 번** 또는 **되풀이** 를 선택하여 일정을 한 번 실행할지 또는 되풀이 일정에 따라 실행할지 선택합니다. **한 번** 을 선택하는 경우 시작 시간을 지정한 다음, **만들기** 를 선택합니다. **되풀이** 를 선택할 경우에는 시작 시간을 지정합니다. **되풀이 간격** 에서 Runbook을 반복할 빈도를 선택합니다. 시간, 일, 주 또는 월별로 선택합니다.

    * **주** 를 선택하는 경우 선택 가능한 요일이 제공됩니다. 요일을 원하는 수만큼 선택합니다. 첫 번째 일정은 시작 시간 이후에 선택한 첫 번째 요일에 실행됩니다. 예를 들어 주말 일정을 선택하려면 토요일과 일요일을 선택합니다.

    ![주말 되풀이 일정 설정](../media/schedules/week-end-weekly-recurrence.png)

    * **월** 을 선택하는 경우에는 다른 옵션이 제공됩니다. **월별 발생 빈도** 옵션에서는 **일(월 중)** 또는 **평일** 중 하나를 선택합니다. **일(월 중)** 을 선택하는 경우 원하는 일수를 선택할 수 있도록 달력이 표시됩니다. 현재 달은 30일까지인데 31일을 선택하는 등의 경우에는 일정이 실행되지 않습니다. 일정이 해당 월의 말일에 실행되도록 하려면 **매월 말일 실행** 아래에서 **예** 를 선택합니다. **평일** 을 선택하는 경우에는 **되풀이 간격** 옵션이 표시됩니다. **첫 번째**, **두 번째**, **세 번째**, **네 번째** 또는 **마지막** 중 하나를 선택하고, 마지막으로 반복할 요일을 선택합니다.

    ![매월 첫날, 15일 및 말일에 대한 월별 일정](../media/schedules/monthly-first-fifteenth-last.png)

5. 작업이 완료되면 **만들기** 를 선택합니다.

### <a name="create-a-new-schedule-with-powershell"></a>PowerShell을 사용하여 새 일정 만들기

[New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet을 사용하여 일정을 만듭니다. 일정의 시작 시간 및 실행 빈도를 지정합니다. 다음 예에서는 다양한 일정 시나리오를 만드는 방법을 보여 줍니다.

>[!NOTE]
>Automation 일정은 현재 일정 이름에 특수 문자 사용을 지원하지 않습니다.
>

#### <a name="create-a-one-time-schedule"></a>일회성 일정 만들기

다음 예에서는 일회성 일정을 만듭니다.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>되풀이 일정 만들기

다음 예에서는 1년 동안 매일 오후 1:00에 실행되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>주별 되풀이 일정 만들기

다음 예에서는 평일에만 실행되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>주말에 대한 주별 되풀이 일정 만들기

다음 예에서는 주말에만 실행되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>매월 첫날, 15일 및 말일에 대한 되풀이 일정 만들기

다음 예에서는 매월 첫날, 15일 및 말일에 실행되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Resource Manager 템플릿으로 일정 만들기

이 예에서는 새 작업 일정을 만드는 ARM(Automation Resource Manager) 템플릿을 사용합니다. Automation 작업 일정을 관리하는 이 템플릿에 관한 일반 정보는 [Microsoft.Automation automationAccounts/jobSchedules 템플릿 참조](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)를 확인하세요.

이 템플릿 파일을 텍스트 편집기에 복사합니다.

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

다음 매개 변수 값을 편집하고 템플릿을 JSON 파일로 저장합니다.

* 작업 일정 개체 이름: GUID(Globally Unique Identifier)가 작업 일정 개체의 이름으로 사용됩니다.

   >[!IMPORTANT]
   > ARM 템플릿을 사용하여 배포된 작업 일정마다 GUID는 고유해야 합니다. 기존 일정을 다시 예약하는 경우에도 GUID를 변경해야 합니다. 같은 템플릿으로 만든 기존 작업 일정을 이전에 삭제한 경우에도 적용됩니다. 같은 GUID를 재사용하면 배포에 실패합니다.</br></br>
   > 이 [무료 온라인 GUID 생성기](https://guidgenerator.com/)와 같이 새 GUID를 생성할 수 있는 온라인 서비스가 있습니다.

* 일정 이름: 지정된 runbook에 연결될 Automation 작업 일정의 이름을 나타냅니다.
* Runbook 이름: 작업 일정을 연결할 Automation runbook의 이름을 나타냅니다.

파일이 저장되면 다음 PowerShell 명령을 사용하여 runbook 작업 일정을 만들 수 있습니다. 이 명령은 `TemplateFile` 매개 변수를 사용하여 템플릿의 경로와 파일 이름을 지정합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Runbook에 일정 연결

Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 필요에 따라 선택적 매개 변수의 값을 제공할 수도 있습니다. 이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다. 동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure Portal을 사용하여 Runbook에 일정 연결

1. Azure Portal의 Automation 계정에서 **프로세스 자동화** 아래에 있는 **Runbook** 을 선택합니다.
1. 예약할 Runbook의 이름을 선택합니다.
1. 현재 Runbook이 일정에 연결되지 않은 경우 새 일정을 만들거나 기존 일정에 연결하는 옵션이 제공됩니다.
1. Runbook에 매개 변수가 있는 경우 **실행 설정 수정(기본값: Azure)** 옵션을 선택할 수 있고, **매개 변수** 창이 표시됩니다. 여기에 매개 변수 정보를 입력할 수 있습니다.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Windows PowerShell을 사용하여 Runbook에 일정 연결

일정을 연결하려면 [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) cmdlet을 사용합니다. Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값을 지정하는 방법에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](../start-runbooks.md)을 참조하세요.
다음 예제에서는 매개 변수를 포함하는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 연결하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Runbook을 더 자주 실행하도록 예약

Azure Automation에서 일정에 구성할 수 있는 가장 빈번한 간격은 1시간입니다. 일정을 이보다 더 자주 실행해야 하는 경우 두 가지 옵션이 있습니다.

* Runbook에 대한 [웹후크](../automation-webhooks.md)를 만들고 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md)를 사용하여 웹후크를 호출합니다. Azure Logic Apps는 일정을 더 세부적으로 정의할 수 있게 지원합니다.

* 한 시간에 한 번씩 15분 간격으로 돌아가며 실행되는 4개 일정을 만듭니다. 이 시나리오를 적용하면 Runbook을 다른 일정으로 15분마다 실행할 수 있습니다.

## <a name="disable-a-schedule"></a>일정 해제

일정을 사용하지 않도록 설정하면 연결된 모든 Runbook이 해당 일정에서 더 이상 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 빈도를 사용하여 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달하면 일정이 사용되지 않습니다.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Azure Portal에서 일정 해제

1. Automation 계정의 왼쪽 창에서 **공유 리소스** 아래에 있는 **일정** 을 선택합니다.
1. 일정 이름을 선택하여 해당 세부 정보 창을 엽니다.
1. **사용** 을 **아니오** 로 변경합니다.

> [!NOTE]
> 시작 시간이 과거인 일정을 사용하지 않도록 설정하려면 시작 날짜를 미래로 변경한 후 저장해야 합니다.

### <a name="disable-a-schedule-with-powershell"></a>PowerShell을 사용하여 일정 해제

[Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) cmdlet을 사용하여 기존 일정의 속성을 변경할 수 있습니다. 일정을 사용하지 않도록 설정하려면 `IsEnabled` 매개 변수에 False를 지정합니다.

다음 예제에서는 Azure Resource Manager cmdlet을 사용하여 Runbook 일정을 사용하지 않도록 설정하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>일정 제거

일정을 제거할 준비가 되면 Azure Portal 또는 PowerShell을 사용할 수 있습니다. 이전 섹션에서 설명한 대로, 사용하지 않도록 설정된 일정만 제거할 수 있습니다.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Azure Portal을 사용하여 일정 제거

1. Automation 계정의 왼쪽 창에서 **공유 리소스** 아래에 있는 **일정** 을 선택합니다.
2. 일정 이름을 선택하여 해당 세부 정보 창을 엽니다.
3. **삭제** 를 클릭합니다.

### <a name="remove-a-schedule-with-powershell"></a>PowerShell을 사용하여 일정 제거

아래와 같이 `Remove-AzAutomationSchedule` cmdlet을 사용하여 기존 일정을 삭제할 수 있습니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>다음 단계

* 일정에 액세스하는 데 사용되는 cmdlet에 대해 자세히 알아보려면 [Azure Automation에서 모듈 관리](modules.md)를 참조하세요.
* Runbook에 대한 일반적인 정보는 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.
