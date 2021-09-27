---
title: Azure에서 업데이트 관리 배포의 사전 스크립트 및 사후 스크립트 관리
description: 이 문서에서는 업데이트 배포를 위한 사전 및 사후 스크립트를 구성하고 관리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 09/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f94a21268625adf3df4dda2f022868f7cc40f72f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060323"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>사전 스크립트 및 사후 스크립트 관리

사전 스크립트 및 사후 스크립트는 Azure Automation 계정에서 업데이트 배포 이전(사전 작업) 및 이후(사후 작업)에 실행되는 Runbook입니다. 사전 스크립트 및 사후 스크립트는 로컬이 아닌 Azure 컨텍스트에서 실행됩니다. 사전 스크립트는 업데이트 배포가 시작할 때 실행됩니다. Windows에서 사후 스크립트는 배포 종료 시와 구성 된 다시 부팅 후에 실행 됩니다. Linux의 경우 사후 스크립트는 컴퓨터가 다시 부팅 된 후가 아니라 배포가 끝난 후에 실행 됩니다. 

## <a name="pre-script-and-post-script-requirements"></a>사전 스크립트 및 사후 스크립트 요구 사항

Runbook을 사전 스크립트 또는 사후 스크립트로 사용하려면 해당 Runbook을 Automation 계정으로 가져온 다음 [Runbook을 게시](../manage-runbooks.md#publish-a-runbook)해야 합니다.

현재 PowerShell 및 Python 2 Runbook만 사전/사후 스크립트로 지원됩니다. Python 3, 그래픽, PowerShell 워크플로, 그래픽 PowerShell 워크플로 등의 다른 Runbook 유형은 현재 사전/사후 스크립트로 지원되지 않습니다.

## <a name="pre-script-and-post-script-parameters"></a>사전 스크립트 및 사후 스크립트 매개 변수

사전 스크립트 및 사후 스크립트가 구성되면 Runbook 예약과 같은 매개 변수를 전달할 수 있습니다. 매개 변수는 업데이트 배포를 만들 때 정의됩니다. 사전 스크립트 및 사후 스크립트는 다음 형식을 지원합니다.

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

사전 스크립트 및 사후 스크립트 Runbook 매개 변수는 부울, 개체 또는 배열 형식을 지원하지 않습니다. 이러한 값을 사용하면 Runbook이 실패합니다. 

다른 개체 형식이 필요한 경우 Runbook에서 사용자 고유의 논리를 사용하여 다른 형식으로 캐스트할 수 있습니다.

표준 Runbook 매개 변수 외에도 `SoftwareUpdateConfigurationRunContext` 매개 변수(JSON 문자열 형식)가 제공됩니다. 사전 스크립트 또는 사후 스크립트 Runbook에서 이 매개 변수를 정의하는 경우 업데이트 배포가 자동으로 전달합니다. 이 매개 변수는 [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)에서 반환하는 정보의 하위 집합인 업데이트 배포에 대한 정보를 포함합니다. 다음 섹션에서는 연결된 속성을 정의합니다.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext 속성

|속성  |유형 |Description  |
|---------|---------|---------|
|SoftwareUpdateConfigurationName     |String | 소프트웨어 업데이트 구성의 이름입니다.        |
|SoftwareUpdateConfigurationRunId     |GUID | 실행의 고유 ID입니다.        |
|SoftwareUpdateConfigurationSettings     || 소프트웨어 업데이트 구성과 관련된 속성의 모음입니다.         |
|SoftwareUpdateConfigurationSettings.OperatingSystem     |Int | 업데이트 배포를 대상으로 하는 운영 체제입니다. `1` = Windows 및 `2` = Linux        |
|SoftwareUpdateConfigurationSettings.Duration     |Timespan(HH:MM:SS) | 업데이트 배포의 최대 기간은 ISO8601에 따라 `PT[n]H[n]M[n]S`로 실행됩니다(유지 관리 기간이라고도 함).<br> 예: 02:00:00         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration     || Windows 컴퓨터와 관련된 속성의 모음입니다.         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.excludedKbNumbers     |String | 업데이트 배포에서 제외되는 공백으로 구분된 KB 목록입니다.        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.includedKbNumbers     |String | 업데이트 배포에 포함된 공백으로 구분된 KB 목록입니다.        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.UpdateCategories     |정수 | 1 = "위험";<br> 2 = "보안"<br> 4 = "UpdateRollUp"<br> 8 = "FeaturePack"<br> 16 = "ServicePack"<br> 32 = "정의"<br> 64 = "도구"<br> 128 = "업데이트"        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.rebootSetting     |String | 업데이트 배포에 대한 다시 부팅 설정입니다. 값은 `IfRequired`, `Never`, `Always`입니다.      |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration     || Linux 컴퓨터와 관련된 속성의 모음입니다.         |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageClassifications |정수 |0 = "분류되지 않음"<br> 1 = "위험"<br> 2 = "보안"<br> 4 = "기타"|
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageNameMasks |String | 업데이트 배포에 포함된 공백으로 구분된 패키지 이름 목록입니다. |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.ExcludedPackageNameMasks |String |업데이트 배포에서 제외되는 공백으로 구분된 패키지 이름 목록입니다. |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.RebootSetting |String |업데이트 배포에 대한 다시 부팅 설정입니다. 값은 `IfRequired`, `Never`, `Always`입니다.      |
|SoftwareUpdateConfiguationSettings.AzureVirtualMachines     |문자열 배열 | 업데이트 배포의 Azure VM에 대한 resourceId 목록입니다.        |
|SoftwareUpdateConfigurationSettings.NonAzureComputerNames|문자열 배열 |업데이트 배포의 비 Azure 컴퓨터 FQDN 목록입니다.|

다음 예제는 Linux 컴퓨터의 **SoftwareUpdateConfigurationSettings** 속성에 전달되는 JSON 문자열입니다.

```json
"SoftwareUpdateConfigurationSettings": {
     "OperatingSystem": 2,
     "WindowsConfiguration": null,
     "LinuxConfiguration": {
         "IncludedPackageClassifications": 7,
         "ExcludedPackageNameMasks": "fgh xyz",
         "IncludedPackageNameMasks": "abc bin*",
         "RebootSetting": "IfRequired"
     },
     "Targets": {
         "azureQueries": null,
         "nonAzureQueries": ""
     },
     "NonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
     ],
     "AzureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/vm-01"
     ],
     "Duration": "02:00:00",
     "PSComputerName": "localhost",
     "PSShowComputerName": true,
     "PSSourceJobInstanceId": "2477a37b-5262-4f4f-b636-3a70152901e9"
 }
```

다음 예제는 Windows 컴퓨터의 **SoftwareUpdateConfigurationSettings** 속성에 전달되는 JSON 문자열입니다.

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "02:00:00",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

모든 속성을 포함하는 전체 예제는 다음에서 확인할 수 있습니다. [이름을 기준으로 소프트웨어 업데이트 구성을 가져옵니다](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` 개체에는 머신에 대한 중복 항목이 포함될 수 있습니다. 이로 인해 사전 스크립트와 사후 스크립트가 동일한 머신에서 여러 번 실행될 수 있습니다. 이 동작을 해결하려면 `Sort-Object -Unique`를 사용하여 고유한 VM 이름만 선택합니다.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>배포에 사전 스크립트 또는 사후 스크립트 사용

업데이트 배포에 사전 스크립트 또는 사후 스크립트를 사용하려면 업데이트 배포부터 만들어야 합니다. **사전 스크립트 + 사후 스크립트** 를 선택합니다. 그러면 **사전 스크립트 + 사후 스크립트 선택** 페이지가 열립니다.

![스크립트 선택](./media/pre-post-scripts/select-scripts.png)

사용할 스크립트를 선택합니다. 이 예제에서는 **UpdateManagement-TurnOnVms** Runbook을 사용합니다. Runbook을 선택하면 **스크립트 구성** 페이지가 열립니다. **사전 스크립트** 를 선택한 다음 **확인** 을 선택합니다.

**UpdateManagement-TurnOffVms** 스크립트에 대해 이 프로세스를 반복합니다. 하지만 **스크립트 유형** 을 선택할 때 **사후 스크립트** 를 선택합니다.

이제 **선택한 항목** 섹션에 선택한 스크립트가 모두 표시됩니다. 하나는 사전 스크립트이고 다른 하나는 사후 스크립트입니다.

![선택한 항목](./media/pre-post-scripts/selected-items.png)

업데이트 배포 구성을 마칩니다.

업데이트 배포가 완료되면 **업데이트 배포** 로 이동하여 결과를 볼 수 있습니다. 여기서 알 수 있듯이 사전 스크립트 및 사후 스크립트의 상태가 제공됩니다.

![업데이트 결과](./media/pre-post-scripts/update-results.png)

업데이트 배포 실행을 선택하여 사전 스크립트 및 사후 스크립트에 대한 추가 세부 정보를 볼 수 있습니다. 실행 시 스크립트 원본에 대한 링크가 제공됩니다.

![배포 실행 결과](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>배포 중지

사전 스크립트를 기반으로 배포를 중지하려면 예외를 [throw](../automation-runbook-execution.md#throw)해야 합니다. 그렇지 않으면 배포 및 사후 스크립트가 계속 실행됩니다. 다음 코드 조각은 PowerShell을 사용하여 예외를 throw하는 방법을 보여 줍니다.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

Python 2에서 예외 처리는 [try](https://www.python-course.eu/exception_handling.php) 블록에서 관리됩니다.

## <a name="interact-with-machines"></a>머신과 상호 작용

사전 스크립트 및 사후 스크립트는 Automation 계정에서 Runbook으로 실행되며 배포의 머신에서 직접 실행되지 않습니다. 사전 작업 및 사후 작업도 Azure 컨텍스트에서 실행되며 비 Azure 머신에는 액세스할 수 없습니다. 다음 섹션에서는 Azure VM 또는 비 Azure 머신 상관없이 머신과 직접 상호 작용할 수 있는 방법을 보여 줍니다.

### <a name="interact-with-azure-machines"></a>Azure 머신과 상호 작용

사전 작업 및 사후 작업은 Runbook으로 실행되며 배포의 Azure VM에서 기본적으로 실행되지 않습니다. Azure VM과 상호 작용하려면 다음 항목이 있어야 합니다.

* [관리 ID](../automation-security-overview.md#managed-identities-preview) 또는 실행 계정
* 실행하려는 Runbook

Azure 머신과 상호 작용하려면 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) cmdlet을 사용하여 Azure VM과 상호 작용해야 합니다. 이 작업을 수행하는 방법의 예는 Runbook 예제 [업데이트 관리 – 실행 명령을 사용하여 스크립트 실행](https://github.com/azureautomation/update-management-run-script-with-run-command)을 참조하세요.

### <a name="interact-with-non-azure-machines"></a>비 Azure 머신과의 상호 작용

사전 작업 및 사후 작업은 Azure 컨텍스트에서 실행되며 비 Azure 머신에는 액세스할 수 없습니다. 비 Azure 머신과 상호 작용하려면 다음 항목이 있어야 합니다.

* [관리 ID](../automation-security-overview.md#managed-identities-preview) 또는 실행 계정
* 머신에 설치된 Hybrid Runbook Worker
* 로컬에서 실행하려는 Runbook
* 부모 Runbook

비 Azure 머신과 상호 작용하기 위해 부모 Runbook이 Azure 컨텍스트에서 실행됩니다. 이 Runbook은 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 사용하여 자식 Runbook을 호출합니다. `RunOn` 매개 변수를 지정하고, 스크립트가 실행될 Hybrid Runbook Worker의 이름을 제공해야 합니다. Runbook 예제 [업데이트 관리 – 로컬에서 스크립트 실행](https://github.com/azureautomation/update-management-run-script-locally)을 참조하세요.

## <a name="abort-patch-deployment"></a>패치 배포 중단

사전 스크립트가 오류를 반환하는 경우 배포를 중단해야 할 수 있습니다. 이렇게 하려면 오류를 구성하는 모든 논리에 대한 스크립트에서 오류를 [throw](/powershell/module/microsoft.powershell.core/about/about_throw)해야 합니다.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Python 2에서 특정 조건이 발생하는 경우 오류를 throw하려면 [raise](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement) 문을 사용합니다.

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>샘플

사전 스크립트 및 사후 스크립트 샘플은 [Azure Automation GitHub 조직](https://github.com/azureautomation) 및 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)에서 찾거나 Azure Portal을 통해 가져올 수 있습니다. 이렇게 하려면 Automation 계정의 **프로세스 자동화** 아래에서 **Runbook 갤러리** 를 선택합니다. 필터에 대해 **업데이트 관리** 를 사용합니다.

![갤러리 목록](./media/pre-post-scripts/runbook-gallery.png)

또는 다음 목록에서 표시된 대로 스크립트 이름으로 검색할 수 있습니다.

* 업데이트 관리 - VM 켜기
* 업데이트 관리 - VM 끄기
* 업데이트 관리 - 로컬에서 스크립트 실행
* 업데이트 관리 - 사전/사후 스크립트에 대한 템플릿
* 업데이트 관리 - 실행 명령을 사용하여 스크립트 실행

> [!IMPORTANT]
> 가져온 Runbook은 먼저 게시한 후에 사용해야 합니다. 이렇게 하려면 Automation 계정에서 Runbook을 찾고 **편집** 을 선택한 다음 **게시** 를 클릭합니다.

샘플은 모두 다음 예제에 정의된 기본 템플릿을 기반으로 합니다. 이 템플릿은 사전 스크립트 및 사후 스크립트에 사용할 사용자 고유의 Runbook을 만드는 데 사용할 수 있습니다. Azure로 인증하고 `SoftwareUpdateConfigurationRunContext` 매개 변수를 처리하는 데 필요한 논리가 포함되어 있습니다.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires the Automation account's system-assigned managed identity.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)

#region BoilerplateAuthentication
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

Runbook을 시스템 할당 관리 ID로 실행하려면 코드를 그대로 둡니다. 사용자 할당 관리 ID를 사용하려면 다음을 수행합니다.
1. 줄 22에서 를 제거합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
1. , 및 으로 대체합니다. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`
1. 클라이언트 ID를 입력합니다.

> [!NOTE]
> 비그래픽 PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)에 대한 별칭입니다. 이러한 cmdlet을 사용하거나 Automation 계정의 [모듈을 최신 버전으로 업데이트](../automation-update-azure-modules.md)할 수 있습니다. 새 Automation 계정을 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

업데이트 관리에 대한 자세한 내용은 [VM의 업데이트 및 패치 관리](manage-updates-for-vm.md)를 참조하세요.
