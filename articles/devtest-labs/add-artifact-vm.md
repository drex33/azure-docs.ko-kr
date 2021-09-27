---
title: VM에 아티팩트 추가
description: Azure DevTest Labs의 랩에서 가상 머신에 아티팩트를 추가하는 방법 알아보기
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 16228abd5be6aed3fced36332b02d735b04996f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644356"
---
# <a name="add-an-artifact-to-a-vm"></a>VM에 아티팩트 추가
VM을 만드는 동안 기존 아티팩트를 추가할 수 있습니다. 이러한 아티팩트는 [공용 DevTest Labs Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) 또는 자체 Git 리포지토리에서 가져올 수 있습니다. 이 문서에서는 Azure Portal에서, 그리고 Azure PowerShell을 사용하여 아티팩트를 추가하는 방법을 보여줍니다. 

Windows PowerShell 스크립트 실행, Bash 명령 실행 및 소프트웨어 설치 등 Azure DevTest Labs *아티팩트* 를 통해 VM을 프로비전할 때 수행하는 *작업* 을 지정할 수 있습니다. 아티팩트 *매개 변수* 를 통해 특정 시나리오에 대한 아티팩트를 사용자 지정할 수 있습니다.

사용자 지정 아티팩트를 만드는 방법에 대한 자세한 내용은 [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md) 문서를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Azure Portal 사용 
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
1. 랩 목록에서 사용하려는 VM을 포함하는 랩을 선택합니다.  
1. **내 가상 머신** 를 선택합니다.
1. 원하는 VM을 선택합니다.
1. **아티팩트 관리** 를 선택합니다. 
1. **아티팩트 적용** 을 선택합니다.
1. **아티팩트 적용** 창에서 VM에 추가하려는 아티팩트를 선택합니다.
1. **아티팩트 추가** 창에서 필수 매개 변수 값 및 필요한 선택적 매개 변수를 입력합니다.  
1. **추가** 를 선택하여 아티팩트를 추가하고 **아티팩트 적용** 창으로 돌아갑니다.
1. VM에 필요한 만큼 계속해서 아티팩트를 추가합니다.
1. 아티팩트를 추가한 후에는 [아티팩트가 실행되는 순서를 변경](#change-the-order-in-which-artifacts-are-run)할 수 있습니다. 뒤로 돌아가서 [아티팩트를 확인 또는 수정](#view-or-modify-an-artifact)할 수도 있습니다.
1. 아티팩트 추가를 마친 경우 **적용** 을 선택합니다.

### <a name="change-the-order-in-which-artifacts-are-run"></a>아티팩트가 실행되는 순서 변경
기본적으로 아티팩트 작업은 VM에 추가된 순서로 실행됩니다. 다음 단계에서는 아티팩트가 실행되는 순서를 변경하는 방법을 보여 줍니다.

1. **아티팩트 적용** 창의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.
   
    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 창에서 아티팩트를 원하는 순서로 끌어다 놓습니다. 아티팩트를 끌어오는 데 문제가 있으면 아티팩트의 왼쪽에서 끌어오는지 확인하세요. 
1. 완료되면 **확인** 을 선택합니다.  

### <a name="view-or-modify-an-artifact"></a>아티팩트를 확인 또는 수정
다음 단계에서는 아티팩트의 매개 변수를 확인 또는 수정하는 방법을 보여 줍니다.

1. **아티팩트 적용** 창의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.
   
    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 창에서 확인 또는 편집하려는 아티팩트를 선택합니다.  
1. **아티팩트 추가** 창에서 필요한 부분을 변경하고 **확인** 을 선택하여 **아티팩트 추가** 창을 닫습니다.
1. **확인** 을 선택하여 **선택한 아티팩트** 창을 닫습니다.

## <a name="use-powershell"></a>PowerShell 사용
다음 스크립트는 지정된 아티팩트를 지정된 VM에 적용합니다. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 명령은 작업을 수행하는 명령입니다.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>다음 단계
아티팩트에 대한 다음 문서를 참조하세요:

- [랩에 대한 필수 아티팩트 지정](devtest-lab-mandatory-artifacts.md)
- [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
- [랩에 아티팩트 리포지토리 추가](devtest-lab-artifact-author.md)
- [아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)
