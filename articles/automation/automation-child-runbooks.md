---
title: Azure Automation에서 모듈식 Runbook 만들기
description: 이 문서에서는 다른 Runbook에서 호출하는 Runbook을 만드는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b6ced29de1ddc71d44d1b93f6b46982865f62014
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353420"
---
# <a name="create-modular-runbooks-in-automation"></a>Automation에서 모듈식 Runbook 만들기

Azure Automation 다른 Runbook에서 호출하는 불연속 함수를 사용하여 재사용 가능한 모듈식 Runbook을 작성하는 것이 좋습니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 

자식 Runbook을 호출하는 방법에는 두 가지가 있으며, 시나리오에 가장 적합한 것을 결정하기 위해 이해해야 하는 고유한 차이점이 있습니다. 다음 표에는 다른 Runbook에서 Runbook을 호출하는 두 가지 방법 간의 차이점이 요약되어 있습니다.

|  | 인라인 | Cmdlet |
|:--- |:--- |:--- |
| **작업** |자식 runbook은 부모와 동일한 작업을 실행합니다. |자식 runbook에 대한 별도 작업을 만듭니다. |
| **실행** |계속하기 전에 부모 runbook은 자식 runbook이 완료되기를 기다립니다. |자식 Runbook이 시작된 후에 즉시 부모 Runbook이 계속되거나 *또는* 자식 작업이 완료될 때까지 부모 Runbook이 대기합니다. |
| **출력** |부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다. |부모 Runbook은 자식 Runbook 작업에서 출력을 검색하거나 *또는* 자식 Runbook에서 출력을 직접 가져올 수 있습니다. |
| **매개 변수** |자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다. |자식 runbook 매개 변수 값은 단일 해시 테이블로 결합해야 합니다. 이 해시 테이블은 JSON 직렬화를 사용하는 단순, 배열 및 개체 데이터 형식만 포함할 수 있습니다. |
| **Automation 계정** |부모 Runbook은 같은 Automation 계정에서 자식 Runbook을 사용할 수 있습니다. |부모 Runbook은 연결된 경우 동일한 Azure 구독 및 심지어 다른 구독의 Automation 계정에서 자식 Runbook을 사용할 수 있습니다. |
| **게시** |부모 runbook을 게시하기 전에 자식 runbook을 게시해야 합니다. |자식 Runbook은 부모 Runbook이 시작되기 전에 언제든지 게시됩니다. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>인라인 실행을 사용하여 자식 Runbook 호출

다른 Runbook에서 Runbook 인라인을 호출하려면 Runbook의 이름을 사용하고 활동 또는 cmdlet을 사용하는 것처럼 해당 매개 변수에 대한 값을 제공합니다. 같은 Automation 계정에서 모든 runbook이 이런 방식으로 사용할 다른 모든 사용자에게 제공됩니다. 부모 Runbook은 다음 줄로 이동하기 전에 자식 Runbook이 완료하기를 기다리고 어떤 출력도 직접 부모에게 반환됩니다.

runbook 인라인을 호출하면 동일한 작업에서 부모 runbook으로 실행됩니다. 자식 Runbook의 작업 기록에는 아무런 표시가 없습니다. 자식 Runbook에서 모든 예외 및 출력 스트림을 부모와 연결합니다. 이 동작으로 작업 수가 감소하며 더 쉽게 작업을 추적하고 문제 해결할 수 있습니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. 그 이유는 Azure Automation이 Runbook을 컴파일할 때 모든 자식 Runbook과의 연결을 구축하기 때문입니다. 자식 Runbook이 아직 게시되지 않은 경우 부모 Runbook은 제대로 게시되는 것처럼 보이지만 시작 시 예외를 생성합니다. 이 경우 자식 runbook을 제대로 참조하기 위해 부모 runbook을 다시 게시할 수 있습니다. 연결이 이미 만들어졌기 때문에 자식 Runbook이 변경된 경우에는 부모 Runbook을 다시 게시할 필요가 없습니다.

인라인으로 호출되는 자식 Runbook의 매개 변수는 복잡한 개체를 비롯한 어떤 데이터 형식도 될 수 있습니다. Azure Portal 사용하거나 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 사용하여 Runbook을 시작할 때와 마찬가지로 [JSON serialization](start-runbooks.md#work-with-runbook-parameters)은 없습니다.

### <a name="runbook-types"></a>Runbook 형식

서로 호출할 수 있는 Runbook 형식은 무엇입니까?

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)은 인라인으로 서로를 호출할 수 있습니다(둘 다 PowerShell 기반임).
* [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 및 그래픽 PowerShell 워크플로 Runbook은 인라인으로 서로를 호출할 수 있습니다(둘 다 PowerShell 워크플로 기반임).
* PowerShell 형식과 PowerShell 워크플로 형식은 인라인으로 서로를 호출할 수 없으므로 `Start-AzAutomationRunbook`을 사용해야 합니다.

게시 순서가 중요한 경우는 언제인가요?

Runbook의 게시 순서는 PowerShell 워크플로 및 그래픽 PowerShell 워크플로 Runbook에서만 중요합니다.

Runbook이 인라인 실행을 사용하여 그래픽 또는 PowerShell 워크플로 자식 Runbook을 호출하는 경우 해당 Runbook의 이름을 사용합니다. 스크립트를 로컬 디렉터리에 배치하도록 지정하려면 이름이 `.\\`로 시작해야 합니다.

### <a name="example"></a>예제

다음 예제는 복합 개체, 정수 값 및 부울 값을 허용하는 테스트 자식 Runbook을 시작합니다. 자식 runbook의 출력을 변수에 할당합니다. 이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

다음은 PowerShell Runbook을 자식으로 사용하는 것과 동일한 예제입니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>cmdlet을 사용하여 자식 Runbook 시작

> [!IMPORTANT]
> Runbook이 `Start-AzAutomationRunbook` cmdlet과 `Wait` 매개 변수를 사용하여 자식 Runbook을 호출하고 자식 Runbook이 개체 결과를 생성하는 경우 작업에 오류가 발생할 수 있습니다. 이 오류를 해결하려면 [개체 출력을 포함하는 자식 Runbook](troubleshoot/runbooks.md#child-runbook-object)을 참조하여 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet을 사용해 결과를 폴링하는 논리를 구현하는 방법을 알아보세요.

[Windows PowerShell을 사용하여 Runbook을 시작하려면](start-runbooks.md#start-a-runbook-with-powershell)에 설명된 대로 `Start-AzAutomationRunbook`를 사용하여 Runbook을 시작할 수 있습니다. 이 cmdlet에 사용할 두 가지 모드가 있습니다. 한 모드에서는 자식 Runbook에 대한 작업이 만들어지면 cmdlet이 작업 ID를 반환합니다. 스크립트가 *Wait* 매개 변수를 지정하여 사용하도록 설정할 수 있는 다른 모드에서 cmdlet은 자식 작업이 완료될 때까지 대기하고 자식 Runbook의 출력을 반환합니다.

cmdlet으로 시작된 자식 Runbook에서 작업은 부모 Runbook 작업과 별도로 실행됩니다. 이 경우 Runbook 인라인을 호출하는 것보다 더 많은 작업이 진행되므로 작업을 추적하기가 더 어려울 수 있습니다. 부모는 각각이 완료되기를 기다리지 않고 비동기식으로 여러 자식 runbook을 시작할 수 있습니다. 자식 Runbook을 인라인으로 호출하는 이 병렬 실행의 경우 부모 Runbook이 [parallel 키워드](automation-powershell-workflow.md#use-parallel-processing)를 사용해야 합니다.

자식 Runbook 출력은 타이밍 때문에 부모 Runbook으로 안정적으로 반환되지 않습니다. 또한 , 및 `$VerbosePreference` 기타 변수와 같은 `$WarningPreference` 변수는 자식 Runbook으로 전파되지 않을 수 있습니다. 이러한 문제를 방지하기 위해 `Start-AzAutomationRunbook`을 `Wait` 매개 변수와 함께 사용하여 별도 Automation 작업으로 자식 Runbook을 시작할 수 있습니다. 그러면 자식 Runbook이 완료될 때까지 부모 Runbook을 차단합니다.

기다리는 동안 부모 Runbook을 차단하지 않으려는 경우 `Start-AzAutomationRunbook`을 `Wait` 매개 변수 없이 사용하여 자식 Runbook을 시작할 수 있습니다. 이 경우 Runbook이 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob)을 사용하여 작업이 완료될 때까지 기다려야 합니다. 또한 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 및 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord)를 사용하여 결과를 검색해야 합니다.

[Runbook 매개 변수](start-runbooks.md#work-with-runbook-parameters)에서 설명한 대로 cmdlet을 사용하여 시작된 자식 Runbook에 대한 매개 변수는 해시 테이블로 제공됩니다. 단순한 데이터 형식만 사용할 수 있습니다. runbook에 복잡한 데이터 형식을 가진 매개 변수가 있는 경우 인라인으로 호출해야 합니다.

자식 Runbook을 별도 작업으로 시작할 때 구독 컨텍스트가 손실될 수 있습니다. 자식 Runbook이 특정 Azure 구독에 대해 Az 모듈 cmdlet을 실행하려면 부모 Runbook과는 별도로 이 구독에서 인증을 받아야 합니다.

동일한 Automation 계정 내의 작업이 여러 구독을 사용하는 경우 하나의 작업에서 구독을 선택하면 다른 작업에 대해서도 현재 선택한 구독 컨텍스트가 변경될 수 있습니다. 이 상황을 방지하려면 각 Runbook 맨 처음에 `Disable-AzContextAutosave -Scope Process`를 사용합니다. 이 작업을 수행하면 컨텍스트가 해당 runbook 실행에만 저장됩니다.

### <a name="example"></a>예제

다음 예제에서는 매개 변수를 사용하여 자식 Runbook을 시작한 다음 `Start-AzAutomationRunbook` cmdlet을 `Wait` 매개 변수와 함께 사용하여 완료될 때까지 기다립니다. 완료되면 자식 Runbook의 cmdlet 출력을 수집합니다. `Start-AzAutomationRunbook`을 사용하려면 스크립트가 Azure 구독에 인증해야 합니다.

```powershell
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    -Parameters $params -Wait
```

Runbook을 시스템 할당 관리 ID로 실행하려면 코드를 그대로 둡니다. 사용자 할당 관리 ID를 사용하려면 다음을 수행합니다.
1. 줄 5에서 `$AzureContext = (Connect-AzAccount -Identity).context` 를 제거합니다.
1. , 및 으로 대체합니다. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`
1. 클라이언트 ID를 입력합니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 실행하려면 [Azure Automation Runbook 시작을](start-runbooks.md)참조하세요.
* Runbook 작업 모니터링에 대한 자세한 내용은 [Azure Automation에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.
