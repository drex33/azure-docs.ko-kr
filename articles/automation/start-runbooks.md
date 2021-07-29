---
title: Azure Automation에서 Runbook 시작
description: 이 문서에서는 Azure Automation에서 Runbook을 시작하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b178ff8b667c5c1db508d8062b4cd5b53955047
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108277231"
---
# <a name="start-a-runbook-in-azure-automation"></a>Azure Automation에서 Runbook 시작

다음 표를 통해 특정 시나리오에 가장 적합하게 Azure Automation에서 Runbook을 시작하는 방법을 결정할 수 있습니다. 이 문서에서는 Azure Portal 및 Windows PowerShell을 사용하여 Runbook을 시작하는 방법에 대해 자세히 설명합니다. 다른 방법에 대한 자세한 내용은 아래 링크에서 액세스할 수 있는 다른 설명서에 제공됩니다.

| **메서드** | **특성** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>대화형 사용자 인터페이스를 사용하는 간단한 방법<br> <li>단순한 매개 변수 값을 제공하는 양식<br> <li>작업 상태를 쉽게 추적<br> <li>Azure 로그인을 사용하여 액세스 인증 |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Windows PowerShell cmdlet을 사용하여 명령줄에서 호출<br> <li>여러 단계로 구성된 자동화된 기능에 포함할 수 있음<br> <li>인증서 또는 OAuth 사용자 계정/서비스 보안 주체를 사용하여 요청 인증<br> <li>단순한 매개 변수 값 및 복잡한 매개 변수 값 제공<br> <li>작업 상태 추적<br> <li>클라이언트에서 PowerShell cmdlet을 지원해야 함 |
| [Azure Automation API](/rest/api/automation/) |<li>가장 유연하지만 가장 복잡한 방법<br> <li>HTTP 요청을 수행할 수 있는 모든 사용자 지정 코드에서 호출<br> <li>인증서 또는 OAuth 사용자 계정/서비스 보안 주체를 사용하여 요청 인증<br> <li>단순한 매개 변수 값 및 복잡한 매개 변수 값 제공 ‘API를 사용하여 Python Runbook을 호출하는 경우 JSON 페이로드를 직렬화해야 합니다.’<br> <li>작업 상태 추적 |
| [Webhook](automation-webhooks.md) |<li>단일 HTTP 요청에서 Runbook 시작<br> <li>URL의 보안 토큰으로 인증<br> <li>웹후크를 만들 때 지정된 매개 변수 값을 클라이언트에서 재정의할 수 없음 Runbook에서 HTTP 요청 세부 정보로 채워진 단일 매개 변수를 정의할 수 있음<br> <li>Webhook URL을 통해 작업 상태를 추적할 수 없음 |
| [Azure 경고에 응답](../azure-monitor/alerts/alerts-overview.md) |<li>Azure 경고에 답하여 Runbook을 시작합니다.<br> <li>Runbook에 대한 Webhook과 경고 링크를 구성합니다.<br> <li>URL의 보안 토큰으로 인증 |
| [일정](./shared-resources/schedules.md) |<li>매시간, 매일, 매주 또는 매월 일정에 따라 Runbook을 자동으로 시작<br> <li>Azure Portal, PowerShell cmdlet 또는 Azure API를 통해 일정 조작<br> <li>일정에서 사용할 매개 변수 값 제공 |
| [다른 Runbook에서](automation-child-runbooks.md) |<li>Runbook을 다른 Runbook의 활동으로 사용<br> <li>여러 Runbook에서 사용하는 기능에 유용<br> <li>자식 Runbook에 매개 변수 값을 제공하고 부모 Runbook에서 출력 사용 |

다음 이미지는 Runbook의 수명 주기에서 자세한 단계별 프로세스를 보여 줍니다. Runbook이 Azure Automation에서 시작하는 다양한 방법, Hybrid Runbook Worker에서 Azure Automation Runbook을 실행하는 데 필요한 구성 요소 및 여러 구성 요소 간의 상호 작용을 포함합니다. 데이터 센터에서 Automation Runbook의 실행에 대해 알아보려면 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook 아키텍처](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Runbook 매개 변수 사용

Azure Portal 또는 Windows PowerShell을 사용하여 Runbook을 시작한 경우 Azure Automation 웹 서비스를 통해 지침이 전송됩니다. 이 서비스는 복잡한 데이터 형식을 가진 매개 변수를 지원하지 않습니다. 복잡한 매개 변수의 값을 제공해야 하는 경우 [Azure Automation에서 자식 Runbook](automation-child-runbooks.md)에 설명된 대로 다른 Runbook에서 인라인으로 호출해야 합니다.

Azure Automation 웹 서비스는 다음 섹션에 설명된 대로 특정 데이터 형식을 사용하는 매개 변수에 대해 특별한 기능을 제공합니다.

### <a name="named-values"></a>명명된 값

매개 변수의 데이터 형식이 [object]인 경우 *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}* JSON 형식을 사용하여 명명된 값 목록으로 전송할 수 있습니다. 이러한 값은 단순한 형식이어야 합니다. Runbook에 각 명명된 값에 해당하는 속성이 있는 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)로 매개 변수가 수신됩니다.

예를 들어 다음 테스트 Runbook에서는 user라는 매개 변수를 허용합니다.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

user 매개 변수에 다음 텍스트를 사용할 수 있습니다.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

그러면 다음과 같이 출력됩니다.

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>배열

매개 변수가 [array] 또는 [string[]]과 같은 배열인 경우 *[Value1, Value2, Value3]* JSON 형식을 사용하여 값 목록으로 전송해야 합니다. 이러한 값은 단순한 형식이어야 합니다.

예를 들어 다음 테스트 Runbook에서는 *user* 라는 매개 변수를 허용합니다.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

user 매개 변수에 다음 텍스트를 사용할 수 있습니다.

```input
["Joe","Smith",2,true]
```

그러면 다음과 같이 출력됩니다.

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>자격 증명

매개 변수의 데이터 형식이 `PSCredential`인 경우 Azure Automation [자격 증명 자산](./shared-resources/credentials.md)의 이름을 입력하면 됩니다. Runbook에서 지정한 이름의 자격 증명을 검색합니다. 다음 테스트 Runbook은 `credential`이라는 매개 변수를 수락합니다.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

`My Credential`이라는 자격 증명 자산이 있다고 가정할 경우 user 매개 변수에 다음 텍스트를 사용할 수 있습니다.

```input
My Credential
```

자격 증명의 사용자 이름이 `jsmith`라고 가정할 경우 다음 출력이 표시됩니다.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Azure Portal을 사용하여 Runbook 시작

1. Azure Portal에서 **Automation** 을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 왼쪽 창에서 **Runbook** 을 선택합니다.
3. **Runbook** 창에서 Runbook을 선택하고 **시작** 을 클릭합니다.
4. Runbook에 매개 변수가 있는 경우 각 매개 변수에 대한 텍스트 상자와 함께 값을 제공하라는 메시지가 표시됩니다. 매개 변수에 대한 자세한 내용은 [Runbook 매개 변수](#work-with-runbook-parameters)를 참조하세요.
5. **작업** 창에서 Runbook 작업의 상태를 볼 수 있습니다.

## <a name="start-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 시작

Windows PowerShell에서 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)을 사용하여 Runbook을 시작할 수 있습니다. 다음 샘플 코드는 **Test-Runbook** 이라는 Runbook을 시작합니다.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`은 Runbook이 시작된 후 해당 상태를 추적하는 데 사용할 수 있는 작업 개체를 반환합니다. 그런 다음, 이 작업 개체를 [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob)에서 사용하여 작업 상태를 확인하고, [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput)에서 사용하여 해당 출력을 검색할 수 있습니다. 다음 예제는 **Test-Runbook** 이라는 Runbook을 시작하고, 완료될 때까지 기다렸다가 해당 출력을 표시합니다.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook -AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup -Stream Output
```

Runbook에 매개 변수가 필요한 경우 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)로 매개 변수를 제공해야 합니다. 해시 테이블의 키는 매개 변수 이름과 일치해야 하고, 값은 매개 변수 값입니다. 다음 예제에서는 FirstName 및 LastName이라는 두 개의 문자열 매개 변수와 RepeatCount라는 정수 및 Show라는 부울 매개 변수를 사용하여 Runbook을 시작하는 방법을 보여 줍니다. 매개 변수에 대한 자세한 내용은 [Runbook 매개 변수](#work-with-runbook-parameters)를 참조하세요.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" -Parameters $params
```

## <a name="next-steps"></a>다음 단계

* Runbook 관리에 대한 자세한 내용은 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [PowerShell 설명서](/powershell/scripting/overview)를 참조하세요.
* Runbook 실행과 관련된 문제 해결은 [Runbook 문제 해결](troubleshoot/runbooks.md)을 참조하세요.
