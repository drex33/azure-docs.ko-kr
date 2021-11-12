---
title: Automation runbook을 사용 하 여 컴퓨터 시작
description: Azure Automation Runbook을 사용하여 Azure DevTest Labs의 랩에서 가상 머신을 시작하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24a9b21e9a081a875653a6b368f1ce501b1cb0fb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401899"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Azure Automation Runbook을 사용하여 Azure DevTest Labs의 랩에서 순서대로 가상 머신 시작
DevTest Labs의 [자동 시작](devtest-lab-set-lab-policy.md#set-autostart) 기능을 사용하면 지정된 시간에 VM이 자동으로 시작되도록 구성할 수 있습니다. 그러나이 기능은 특정 순서로 컴퓨터를 시작 하는 것을 지원 하지 않습니다. 이러한 종류의 자동화가 유용한 몇 가지 시나리오가 있습니다.  한 가지 시나리오는 랩에서 Jumpbox VM이 다른 Vm에 대 한 액세스 지점입니다. Jumpbox VM은 다른 Vm 보다 먼저 시작 해야 합니다. 이 문서에서는 스크립트를 실행하는 PowerShell Runbook을 사용하여 Azure Automation 계정을 설정하는 방법을 보여 줍니다. 스크립트는 랩에서 VM의 태그를 사용하여 스크립트를 변경할 필요 없이 시작 순서를 제어할 수 있도록 합니다.

## <a name="setup"></a>설치 프로그램
이 예제에서 랩의 Vm은 적절 한 값 (예: 0, 1, 2)을 사용 하 **여 추가 된** 태그를 포함 해야 합니다. -1로 시작 하지 않아도 되는 컴퓨터를 지정 합니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
[이 문서](../automation/automation-create-standalone-account.md)의 지침에 따라 Azure Automation 계정을 만듭니다. 계정을 만들 때 **실행 계정** 옵션을 선택합니다. Automation 계정이 만들어지면 **모듈** 페이지를 열고 메뉴 모음에서 **Azure 모듈 업데이트** 를 선택합니다. 기본 모듈은 몇 가지 버전이 오래되었으며 업데이트하지 않으면 스크립트가 작동하지 않을 수 있습니다.

## <a name="add-a-runbook"></a>Runbook 추가
이제 왼쪽 메뉴에서 **Runbook** 을 선택하여 Automation 계정에 Runbook을 추가합니다. 메뉴에서 **Runbook 추가** 를 선택하고 지침에 따라 [PowerShell Runbook을 만듭니다](../automation/learn/powershell-runbook-managed-identity.md).

## <a name="powershell-script"></a>PowerShell 스크립트
다음 스크립트는 구독 이름 및 랩 이름을 매개 변수로 사용합니다. 이 스크립트 흐름은 랩에서 모든 VM을 가져온 다음 태그 정보를 구문 분석하여 VM 이름 및 해당 시작 순서 목록을 만드는 것입니다. 이 스크립트는 VM을 순서대로 실행하고 VM을 시작합니다. 특정 주문 번호에 여러 Vm이 있는 경우 PowerShell 작업을 사용 하 여 비동기적으로 시작 합니다. 태그를 포함 하지 않는 Vm의 경우 시작 값을 마지막 (10)으로 설정 합니다. 이러한 컴퓨터는 기본적으로 마지막으로 시작 됩니다.  VM을 자동으로 시작 하지 않으려면 태그 값을 11로 설정 합니다. 그러면 스크립트에서 VM을 무시 합니다.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>일정 만들기
이 스크립트를 매일 실행하려면 Automation 계정에 [일정을 만듭니다](../automation/shared-resources/schedules.md#create-a-schedule). 일정이 만들어지면 [Runbook에 연결](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook)합니다. 

여러 랩을 포함 하는 대규모 상황에서는 여러 랩에 대 한 파일에 매개 변수 정보를 저장 합니다. 개별 매개 변수를 전달 하는 대신 스크립트에 파일을 전달 합니다. 스크립트를 수정 해야 하지만 코어 실행은 동일 합니다. 이 샘플에서는 Azure Automation을 사용하여 PowerShell 스크립트를 실행하지만 빌드/릴리스 파이프라인에서 작업을 사용하는 것과 같은 다른 옵션도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Automation: [Azure Automation 소개](../automation/automation-intro.md)에 대한 자세한 내용은 다음 문서를 참조하세요.