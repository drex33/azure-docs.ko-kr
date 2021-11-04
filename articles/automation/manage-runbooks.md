---
title: Azure Automation에서 Runbook 관리
description: 이 문서에서는 Azure Automation에서 Runbook을 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99f86065b9a33125e3ca4cf72af5b8ec442e3cc0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455460"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation에서 Runbook 관리

새로 만들거나 파일 또는 [Runbook 갤러리](automation-runbook-gallery.md)에서 기존 Runbook을 가져와 Azure Automation에 Runbook을 추가할 수 있습니다. 이 문서에서는 runbook 디자인에서 runbook 및 권장 패턴 및 모범 사례를 관리 하는 방법에 대 한 정보를 제공 합니다. [Azure Automation에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 Runbook과 모듈 액세스에 대한 모든 상세 정보를 확인할 수 있습니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Azure Portal 또는 PowerShell을 사용 하 여 Azure Automation에서 새 runbook을 만듭니다. Runbook을 만든 후에는 다음의 정보를 사용하여 Runbook을 편집할 수 있습니다.

* [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)
* [Automation runbook에 대 한 주요 PowerShell 워크플로 개념 알아보기](automation-powershell-workflow.md)
* [Azure Automation에서 Python 2 패키지 관리](python-packages.md)
* [Azure Automation에서 Python 3 패키지(미리 보기) 관리](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 만들기

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정에서 **프로세스 자동화** 아래에 있는 **Runbook** 을 선택하여 Runbook 목록을 엽니다.
1. **Runbook 만들기** 를 클릭합니다.
    1. Runbook의 이름을로 합니다.
    1. **Runbook 유형** 드롭다운에서 해당 [유형을](automation-runbook-types.md)선택 합니다. Runbook 이름은 문자로 시작 해야 하 고 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
    1. **런타임 버전** 을 선택 합니다.
    1. 적용 가능한 **설명** 입력
1. **만들기** 를 클릭 하 여 runbook을 만듭니다.

### <a name="create-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 만들기

[New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) cmdlet을 사용하여 빈 Runbook을 만듭니다. `Type` 매개 변수를 사용하여 `New-AzAutomationRunbook`에 대해 정의된 Runbook 유형 중 하나를 지정합니다.

다음 예제에서는 빈 Runbook을 새로 만드는 방법을 보여줍니다.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Runbook 가져오기

PowerShell 또는 PowerShell 워크플로( **.ps1**) 스크립트, 그래픽 Runbook( **.graphRunbook**) 또는 Python 2 또는 Python 3 스크립트( **.py**)를 가져와 고유한 Runbook을 만들 수 있습니다. 다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정합니다.

* 워크플로가 포함되지 않은 **.ps1** 파일을 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. PowerShell 워크플로 Runbook으로 가져오는 경우 워크플로로 변환됩니다. 이 경우 변경 내용을 설명하는 주석이 Runbook에 포함됩니다.

* PowerShell 워크플로가 포함된 **.ps1** 파일만을 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. 파일에 여러 PowerShell 워크플로 있으면 가져오기가 실패합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.

* PowerShell 워크플로가 포함된 **.ps1** 파일을 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)으로 가져오지 마세요. PowerShell 스크립트 엔진에서 이를 식별할 수 있습니다.

* **.graphrunbook** 파일만을 새로운 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)으로 가져옵니다.

### <a name="import-a-runbook-from-the-azure-portal"></a>Azure Portal에서 Runbook 가져오기

Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.

> [!NOTE]
> 포털을 사용하는 PowerShell 워크플로 Runbook에만 **.ps1** 파일을 가져올 수 있습니다.

1. Azure Portal에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정에서 **프로세스 자동화** 아래에 있는 **Runbook** 을 선택하여 Runbook 목록을 엽니다.
1. **Runbook 가져오기** 를 클릭합니다. 다음 옵션 중 하나를 선택할 수 있습니다.
    1. **파일 찾아보기** -로컬 컴퓨터에서 파일을 선택 합니다.
    1. **갤러리에서 찾아보기** -갤러리에서 기존 runbook을 찾아보고 선택할 수 있습니다.
1. 파일을 선택합니다.
1. **이름** 필드가 사용 설정된 경우 Runbook 이름을 변경할 수 있습니다. 이름은 문자로 시작해야 하며 문자, 숫자, 밑줄, 대시 등이 포함될 수 있습니다.
1. [**Runbook 형식이**](automation-runbook-types.md) 자동으로 채워지므로 해당 제한 사항을 고려한 후에 형식을 변경할 수 있습니다.
1. **런타임 버전이** 자동으로 채워지거나 드롭다운 목록에서 버전을 선택 합니다.
1. **가져오기** 를 클릭합니다. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
1. 실행에 앞서 [Runbook을 게시](#publish-a-runbook)해야 합니다.

> [!NOTE]
> 그래픽 Runbook을 가져온 후에는 이를 다른 형식으로 변환할 수 있습니다. 하지만 그래픽 Runbook을 텍스트 Runbook으로 변환할 수는 없습니다.

### <a name="import-a-runbook-with-powershell"></a>PowerShell을 사용 하 여 runbook 가져오기

[Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) cmdlet을 사용하여 스크립트 파일을 초안 Runbook으로 가져옵니다. 해당 Runbook이 이미 있는 경우 cmdlet에 `Force` 매개 변수를 사용하지 않으면 가져오기에 실패합니다.

다음 예제는 Runbook에 스크립트 파일을 가져오는 방법을 보여줍니다.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>리소스 처리

Runbook에서 [리소스](automation-runbook-execution.md#resources)를 만드는 경우 만들기 전에 리소스가 이미 존재하는지 스크립트에서 확인해야 합니다. 기본 예제는 다음과 같습니다.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>활동 로그에서 세부 정보 검색하기

Runbook을 시작한 사용자 또는 계정과 같은 Runbook 세부 정보는 Automation 계정의 [활동 로그](automation-runbook-execution.md#activity-logging)에서 검색할 수 있습니다. 다음 PowerShell 예에서는 지정된 Runbook을 실행하는 마지막 사용자를 제공합니다.

```powershell-interactive
$rgName = 'MyResourceGroup'
$accountName = 'MyAutomationAccount'
$runbookName = 'MyRunbook'
$startTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $rgName
    StartTime         = $startTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $runbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $rgName
            AutomationAccountName = $accountName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $runbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName, $Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>진행률 추적

쉽게 재사용 및 재시작할 수 있는 논리를 포함하여 Runbook을 모듈식으로 작성하는 것이 좋습니다. Runbook에서 진행률을 추적하면 문제가 있는 경우 Runbook 논리가 올바르게 실행되도록 할 수 있습니다.

스토리지 계정, 데이터베이스 또는 공유 파일과 같은 외부 원본을 사용하여 Runbook의 진행률을 추적할 수 있습니다. Runbook에서 논리를 만들어서 마지막으로 수행한 작업의 상태를 먼저 확인합니다. 그런 다음 검사 결과에 따라 논리는 Runbook의 특정 작업을 건너뛰거나 계속할 수 있습니다.

## <a name="prevent-concurrent-jobs"></a>동시 작업 방지

여러 작업을 동시에 실행하는 경우 일부 Runbook에서는 이상하게 작동합니다. 이 경우 이미 실행 중인 작업이 Runbook에 있는지 확인하는 논리를 구현해야 합니다. 기본 예제는 다음과 같습니다.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

# Check for already running or new runbooks
$runbookName = "runbookName"
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName `
    -AutomationAccountName $automationAccountName `
    -RunbookName $runbookName `
    -DefaultProfile $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook $runbookName is already running"
    exit 1
} else {
    # Insert Your code here
    Write-Output "Runbook $runbookName is not running"
}
```

시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.

1. 5 번 줄에서,를 제거 합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
1. 클라이언트 ID를 입력 합니다.

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>시간 종속 스크립트의 일시적인 오류 처리

Runbook은 강력하고 재시작 또는 실패의 원인이 될 수 있는 일시적 오류를 포함한 [오류](automation-runbook-execution.md#errors)를 처리할 수 있어야 합니다. Runbook이 실패하면 Azure Automation에서 이를 재시도합니다.

Runbook이 시간 제약 조건 내에서 정상적으로 실행되는 경우 스크립트에서 실행 시간을 확인하는 논리를 구현하도록 합니다. 이 검사는 특정 시간 동안에만 시작, 종료 또는 스케일 아웃 등의 작업 실행을 보장합니다.

> [!NOTE]
> Azure 샌드박스 프로세스의 현지 시간은 UTC로 설정됩니다. Runbook에서의 날짜 및 시간 계산 시 이 사실을 고려해야 합니다.

## <a name="work-with-multiple-subscriptions"></a>여러 구독 작업

Runbook은 [구독](automation-runbook-execution.md#subscriptions)을 사용하여 작업할 수 있어야 합니다. 예를 들어 여러 구독을 처리하기 위해 Runbook은 [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) cmdlet을 사용합니다. 이 cmdlet을 사용하면 동일한 샌드박스에서 실행되는 다른 Runbook에서 인증 컨텍스트가 검색되지 않습니다. 

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

$childRunbookName = 'childRunbookDemo'
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$startParams = @{
    ResourceGroupName     = $resourceGroupName
    AutomationAccountName = $automationAccountName
    Name                  = $childRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.

1. 5 번 줄에서,를 제거 합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
1. 클라이언트 ID를 입력 합니다.

## <a name="work-with-a-custom-script"></a>사용자 지정 스크립트 작업

> [!NOTE]
> 일반적으로 Log Analytics 에이전트가 설치된 호스트에서 사용자 지정 스크립트 및 Runbook을 실행할 수 없습니다.

사용자 지정 스크립트 사용:

1. Automation 계정을 만듭니다.
2. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) 역할을 배포합니다.
3. Linux 컴퓨터의 경우 높은 권한이 필요합니다. 로그인하여 [서명 확인을 해제](automation-linux-hrw-install.md#turn-off-signature-validation)합니다.

## <a name="test-a-runbook"></a>Runbook 테스트

Runbook을 테스트할 때 [초안 버전](#publish-a-runbook) 이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 만들어지지 않지만 [출력](automation-runbook-output-and-messages.md#use-the-output-stream)과 [경고 및 오류](automation-runbook-output-and-messages.md#working-with-message-streams) 스트림은 **테스트 출력** 창에 표시됩니다. [자세한 정보 스트림](automation-runbook-output-and-messages.md#write-output-to-verbose-stream)에 대한 메시지는 [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) 변수가 `Continue`로 설정되는 경우에만 출력 창에 표시됩니다.

초안 버전을 실행하더라도 Runbook은 정상적으로 실행되며, 해당 환경의 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 비프로덕션 리소스에서만 Runbook을 테스트해야 합니다.

각 [유형의 Runbook](automation-runbook-types.md)을 테스트하는 절차는 동일합니다. Azure Portal에서 테스트할 때 텍스트 편집기와 그래픽 편집기 간에 차이가 없습니다.

1. [텍스트 편집기](automation-edit-textual-runbook.md) 또는 [그래픽 편집기](automation-graphical-authoring-intro.md)에서 Runbook의 초안 버전을 엽니다.
1. **테스트** 를 클릭하여 **테스트** 페이지를 엽니다.
1. Runbook에 매개 변수가 있는 경우 왼쪽 창에 나열되며, 여기서 테스트에 사용할 값을 제공할 수 있습니다.
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 테스트를 실행하려는 경우는 **실행 설정** 을 **Hybrid Worker** 로 변경하고 대상 그룹의 이름을 선택합니다. 그렇지 않은 경우 기본 **Azure** 를 유지하여 클라우드에서 테스트를 실행합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다.
1. **출력** 창의 단추를 사용하여 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks) Runbook을 테스트 중인 동안 중지 또는 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단하기 전에 현재 작업을 완료합니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
1. **출력** 창에서 Runbook의 출력을 검사합니다.

## <a name="publish-a-runbook"></a>Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다. Azure Automation의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있게 되면 이를 게시합니다. 그러면 초안 버전이 현재 게시된 버전을 덮어씁니다.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 게시하기

1. Azure Portal에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정에서 runbook을 엽니다.
1. **편집** 을 클릭합니다.
1. **게시** 를 클릭 한 다음 확인 메시지에 대 한 응답으로 **예** 를 선택 합니다.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell을 사용하여 Runbook 게시

[Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) cmdlet을 사용하여 Runbook을 게시합니다. 

```azurepowershell-interactive
$accountName = "MyAutomationAccount"
$runbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $accountName
    ResourceGroupName     = $rgName
    Name                  = $runbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 일정 지정

Runbook이 게시되면 작업에 대해 일정을 지정할 수 있습니다.

1. Azure Portal에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Runbook 목록에서 runbook을 선택 합니다.
1. **리소스** 에서 **일정** 을 선택합니다.
1. **일정 추가** 를 선택합니다.
1. **Runbook 예약** 창에서 **Runbook에 일정 연결** 을 선택 합니다.
1. **일정** 창에서 **새 일정 만들기** 를 선택 합니다.
1. **새 일정** 창에 이름, 설명 및 기타 매개 변수를 입력 합니다.
1. 일정이 생성되면 강조 표시하고 **확인** 을 클릭합니다. 이제 Runbook에 연결될 것입니다.
1. 사용자의 사서함에서 Runbook 상태를 알리는 전자 메일을 찾습니다.

## <a name="obtain-job-statuses"></a>작업 상태 가져오기

### <a name="view-statuses-in-the-azure-portal"></a>Azure Portal에서 상태 보기

Azure Automation에서의 작업 처리 관련 세부 정보는 [작업](automation-runbook-execution.md#jobs)에서 제공됩니다. Runbook 작업을 볼 준비가 되면 Azure Portal을 사용하여 Automation 계정에 액세스합니다. 오른쪽에 있는 **작업 통계** 에서 모든 Runbook 작업의 요약을 볼 수 있습니다.

![작업 통계 타일](./media/manage-runbooks/automation-account-job-status-summary.png)

요약에서는 실행된 각 작업의 개수 및 작업 상태를 그래픽으로 표시합니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 작업 페이지가 표시됩니다. 이 페이지에는 각 작업의 상태, Runbook 이름, 시작 시간 및 완료 시간이 표시됩니다.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="작업 페이지 스크린샷.":::

**작업 필터링** 을 선택하여 작업 목록을 필터링할 수 있습니다. 드롭다운 목록에서 특정 Runbook, 작업 상태 또는 선택 항목을 필터링하고 검색에 대한 시간 범위를 입력합니다.

![작업 상태 필터링](./media/manage-runbooks/automation-account-jobs-filter.png)

또는 Automation 계정의 Runbook 페이지에서 Runbook을 선택하여 특정 Runbook에 대한 작업 요약 세부 정보를 확인한 다음, **작업** 타일을 선택할 수 있습니다. 이 작업은 작업 페이지를 표시합니다. 여기에서 작업 레코드를 클릭하여 세부 정보 및 출력을 볼 수 있습니다.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="오류 단추가 강조 표시된 작업 페이지의 스크린샷.":::

### <a name="retrieve-job-statuses-using-powershell"></a>PowerShell을 사용하여 작업 상태 검색

[Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) cmdlet을 사용하여 Runbook에 대해 생성된 작업 및 특정 작업을 검색합니다. `Start-AzAutomationRunbook`을 사용하여 Runbook을 시작하는 경우 결과로 나온 작업이 반환됩니다. [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput)을 사용하여 작업 출력을 검색합니다.

다음 예제는 샘플 Runbook에 대한 마지막 작업을 가져오고 작업의 상태, Runbook 매개 변수에 제공된 값, 작업 출력을 표시합니다.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

다음 예제는 특정 작업에 대한 출력을 검색하고, 각 레코드를 반환합니다. 레코드 중 하나에 대한 [예외](automation-runbook-execution.md#exceptions)가 발생한 경우 스크립트에서 값 대신 예외를 씁니다. 이 동작은 출력하는 동안 예외가 일반적으로 기록되지 않을 수 있는 추가 정보를 제공하므로 유용합니다.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>다음 단계

* Runbook 관리에 대한 자세한 내용은 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조하세요.
* PowerShell Runbook을 준비하려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* Runbook 실행과 관련된 문제 해결은 [Runbook 문제 해결](troubleshoot/runbooks.md)을 참조하세요.
