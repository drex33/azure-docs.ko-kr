---
title: Azure Automation에서 모듈식 Runbook 만들기
description: 이 문서에서는 다른 runbook에서 호출 하는 runbook을 만드는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4eff80637886901644b36b19f74d79ae83e7cfe3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455495"
---
# <a name="create-modular-runbooks-in-automation"></a>Automation에서 모듈식 runbook 만들기

다른 runbook이 호출 하는 불연속 함수를 사용 하 여 재사용 가능한 모듈식 runbook을 작성 Azure Automation 하는 것이 좋습니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 

자식 runbook을 호출 하는 방법에는 두 가지가 있습니다. 인라인 또는 cmdlet을 통해 호출 합니다. 다음 표에는 시나리오에 적합 한 방법을 결정 하는 데 도움이 되는 차이점이 요약 되어 있습니다.

|  | 인라인 | Cmdlet |
|:--- |:--- |:--- |
| **작업** |자식 runbook은 부모와 동일한 작업을 실행합니다. |자식 runbook에 대한 별도 작업을 만듭니다. |
| **실행** |계속 하기 전에 부모 runbook은 자식 runbook이 완료 될 때까지 기다립니다. |부모 runbook은 자식 runbook이 시작 된 직후에 계속 *되거나* 부모 runbook이 자식 작업이 완료 될 때까지 대기 합니다. |
| **출력** |부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다. |부모 runbook은 자식 runbook 작업에서 출력을 검색 해야 합니다. *그렇지* 않으면 부모 runbook이 자식 runbook에서 출력을 직접 가져올 수 있습니다. |
| **매개 변수** |자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다. |자식 runbook 매개 변수 값은 단일 해시 테이블로 결합해야 합니다. 이 해시 테이블은 JSON serialization을 사용 하는 단순, 배열 및 개체 데이터 형식만 포함할 수 있습니다. |
| **Automation 계정** |부모 runbook은 동일한 Automation 계정에서 자식 runbook을 사용할 수 있습니다. |부모 Runbook은 연결된 경우 동일한 Azure 구독 및 심지어 다른 구독의 Automation 계정에서 자식 Runbook을 사용할 수 있습니다. |
| **게시** |부모 runbook을 게시 하기 전에 자식 runbook을 게시 해야 합니다. |자식 runbook은 부모 runbook이 시작 되기 전에 언제 든 지 게시 됩니다. |

## <a name="call-a-child-runbook-by-using-inline-execution"></a>인라인 실행을 사용 하 여 자식 runbook 호출

다른 runbook에서 인라인으로 runbook을 호출 하려면 활동이 나 cmdlet을 사용 하는 것 처럼 runbook의 이름을 사용 하 고 매개 변수에 대 한 값을 제공 합니다. 동일한 Automation 계정의 모든 runbook은 이러한 방식으로 사용할 수 있습니다. 부모 runbook은 자식 runbook이 완료 될 때까지 기다린 후 다음 줄로 이동 하 고 출력은 부모에 직접 반환 됩니다.

인라인 runbook을 호출 하면 부모 runbook과 같은 작업에서 실행 됩니다. 자식 runbook의 작업 기록에는 표시 되지 않습니다. 자식 Runbook에서 모든 예외 및 출력 스트림을 부모와 연결합니다. 이 동작으로 작업 수가 감소하며 더 쉽게 작업을 추적하고 문제 해결할 수 있습니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. 그 이유는 Azure Automation이 Runbook을 컴파일할 때 모든 자식 Runbook과의 연결을 구축하기 때문입니다. 자식 runbook이 아직 게시 되지 않은 경우 부모 runbook이 올바르게 게시 된 것으로 나타나지만 시작 될 때 예외를 생성 합니다. 

예외가 발생 하면 부모 runbook을 다시 게시 하 여 자식 runbook을 올바르게 참조할 수 있습니다. 연결이 이미 만들어졌으므로 자식 runbook이 변경 된 경우에는 부모 runbook을 다시 게시할 필요가 없습니다.

인라인으로 호출되는 자식 Runbook의 매개 변수는 복잡한 개체를 비롯한 어떤 데이터 형식도 될 수 있습니다. Azure Portal를 사용 하거나 [AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 사용 하 여 runbook을 시작할 때와 마찬가지로 [JSON serialization](start-runbooks.md#work-with-runbook-parameters)은 없습니다.

### <a name="runbook-types"></a>Runbook 형식

현재 PowerShell 5.1 및 7.1 (미리 보기)가 지원 되며 특정 runbook 유형만 서로 호출할 수 있습니다.

* [Powershell runbook](automation-runbook-types.md#powershell-runbooks) 과 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks) 은 모두 powershell을 기반으로 하기 때문에 인라인을 호출할 수 있습니다.
* Powershell 워크플로 [runbook](automation-runbook-types.md#powershell-workflow-runbooks) 및 그래픽 powershell 워크플로 runbook은 모두 powershell 워크플로를 기반으로 하기 때문에 인라인을 호출할 수 있습니다.
* PowerShell 유형 및 PowerShell 워크플로 유형은 서로 인라인으로 호출할 수 없습니다. 를 사용 해야 합니다 `Start-AzAutomationRunbook` .

Runbook의 게시 순서는 PowerShell 워크플로 및 그래픽 PowerShell 워크플로 runbook에만 중요 합니다.

Runbook이 인라인 실행을 사용 하 여 그래픽 또는 PowerShell 워크플로 자식 runbook을 호출 하는 경우 runbook의 이름을 사용 합니다. `.\\`스크립트를 로컬 디렉터리에 지정 하려면 이름을로 시작 해야 합니다.

### <a name="example"></a>예제

다음 예제에서는 복합 개체, 정수 값 및 부울 값을 허용 하는 테스트 자식 runbook을 시작 합니다. 자식 runbook의 출력을 변수에 할당합니다. 이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

다음은 동일한 예제 이지만 PowerShell runbook을 자식으로 사용 하는 것입니다.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-by-using-a-cmdlet"></a>Cmdlet을 사용 하 여 자식 runbook 시작

> [!IMPORTANT]
> Runbook이 매개 변수와 함께 cmdlet을 사용 하 여 자식 runbook을 호출 하 `Start-AzAutomationRunbook` `Wait` 고 자식 runbook에서 개체 결과를 생성 하는 경우 작업에 오류가 발생할 수 있습니다. 오류를 해결 하려면 [개체 출력을 사용 하는 자식 runbook](troubleshoot/runbooks.md#child-runbook-object)을 참조 하세요. 이 문서에서는 [AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet을 사용 하 여 결과를 폴링하는 논리를 구현 하는 방법을 보여 줍니다.

Windows PowerShell를 사용 하 `Start-AzAutomationRunbook` [여 runbook 시작](start-runbooks.md#start-a-runbook-with-powershell)에 설명 된 대로를 사용 하 여 runbook을 시작할 수 있습니다. 이 cmdlet에 사용할 수 있는 두 가지 모드는 다음과 같습니다.

- Cmdlet은 자식 runbook에 대해 작업을 만들 때 작업 ID를 반환 합니다. 
- 이 cmdlet은 자식 작업이 완료 될 때까지 대기 하 고 자식 runbook의 출력을 반환 합니다. 스크립트는 매개 변수를 지정 하 여이 모드를 사용 하도록 설정 `Wait` 합니다.

cmdlet으로 시작된 자식 Runbook에서 작업은 부모 Runbook 작업과 별도로 실행됩니다. 이 동작은 runbook을 인라인으로 시작 하는 것 보다 더 많은 작업을 수행 하 고 작업을 추적 하기가 더 어려워집니다. 부모는 각 runbook이 완료 될 때까지 기다리지 않고 비동기적으로 둘 이상의 자식 runbook을 시작할 수 있습니다. 자식 Runbook을 인라인으로 호출하는 이 병렬 실행의 경우 부모 Runbook이 [parallel 키워드](automation-powershell-workflow.md#use-parallel-processing)를 사용해야 합니다.

시간 때문에 자식 runbook 출력이 부모 runbook으로 안정적으로 반환 되지 않습니다. 또한, `$VerbosePreference` `$WarningPreference` 및 기타 변수는 자식 runbook으로 전파 되지 않을 수 있습니다. 이러한 문제를 방지 하기 위해를 `Start-AzAutomationRunbook` 매개 변수와 함께 사용 하 여 개별 Automation 작업으로 자식 runbook을 시작할 수 있습니다 `Wait` . 이 기술은 자식 runbook이 완료 될 때까지 부모 runbook을 차단 합니다.

대기 중인 부모 runbook을 차단 하지 않으려면 `Start-AzAutomationRunbook` 매개 변수 없이를 사용 하 여 자식 runbook을 시작할 수 있습니다 `Wait` . 이 경우 Runbook이 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob)을 사용하여 작업이 완료될 때까지 기다려야 합니다. 또한 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 및 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord)를 사용하여 결과를 검색해야 합니다.

[Runbook 매개 변수](start-runbooks.md#work-with-runbook-parameters)에서 설명한 대로 cmdlet을 사용하여 시작된 자식 Runbook에 대한 매개 변수는 해시 테이블로 제공됩니다. 단순 데이터 형식만 사용할 수 있습니다. Runbook에 복합 데이터 형식의 매개 변수가 있는 경우이를 인라인으로 호출 해야 합니다.

개별 작업으로 자식 runbook을 시작 하는 경우 구독 컨텍스트가 손실 될 수 있습니다. 자식 Runbook이 특정 Azure 구독에 대해 Az 모듈 cmdlet을 실행하려면 부모 Runbook과는 별도로 이 구독에서 인증을 받아야 합니다.

동일한 Automation 계정 내의 작업이 여러 구독을 사용하는 경우 하나의 작업에서 구독을 선택하면 다른 작업에 대해서도 현재 선택한 구독 컨텍스트가 변경될 수 있습니다. 이 상황을 방지하려면 각 Runbook 맨 처음에 `Disable-AzContextAutosave -Scope Process`를 사용합니다. 이 작업을 수행하면 컨텍스트가 해당 runbook 실행에만 저장됩니다.

### <a name="example"></a>예제

다음 예에서는 매개 변수가 있는 자식 runbook을 시작한 다음 `Start-AzAutomationRunbook` 매개 변수와 함께 cmdlet을 사용 하 여 완료 될 때까지 기다립니다 `Wait` . 자식 runbook이 완료 되 면이 예제에서는 자식 runbook의 cmdlet 출력을 수집 합니다. `Start-AzAutomationRunbook`을 사용하려면 스크립트가 Azure 구독에 인증해야 합니다.

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

시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
1. 5 번 줄에서,를 제거 합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
1. 클라이언트 ID를 입력 합니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 실행 하려면 [Azure Automation에서 Runbook 시작](start-runbooks.md)을 참조 하세요.
* Runbook 작업을 모니터링 하려면 [Azure Automation의 runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조 하세요.
