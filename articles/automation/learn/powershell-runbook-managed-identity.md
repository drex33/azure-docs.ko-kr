---
title: Azure Automation에서 관리 ID를 사용하여 PowerShell Runbook 만들기
description: 이 자습서에서는 Azure Automation에서 PowerShell Runbook의 관리 ID를 사용하는 방법을 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2021
ms.topic: tutorial
ms.openlocfilehash: e1b3b59ecf5df22a5727b0b6924b6d8a39cf9b8a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054630"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>자습서: 관리 ID를 사용하여 Automation PowerShell Runbook 만들기

이 자습서에서는 Azure Automation에서 실행 계정이 아닌 [관리 ID](../automation-security-overview.md#managed-identities)를 사용하여 리소스와 상호 작용하는 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks)을 만드는 방법을 안내합니다. PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure AD(Azure Active Directory)의 관리 ID를 사용하면 Runbook에서 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 관리 ID에 권한 할당
> * PowerShell Runbook 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 사용자 할당 관리 ID가 하나 이상 있는 Azure Automation 계정. 자세한 내용은 [Azure Automation 계정에 대해 사용자 할당 관리 ID 사용](../add-user-assigned-identity.md)을 참조하세요.
* Az 모듈 `Az.Accounts`, `Az.Automation`, `Az.ManagedServiceIdentity` 및 `Az.Compute`를 Automation 계정으로 가져옵니다. 자세한 내용은 [Az 모듈 가져오기](../shared-resources/modules.md#import-az-modules)를 참조하세요.
* 로컬 컴퓨터에 설치된 [Azure Az PowerShell 모듈](/powershell/azure/new-azureps-module-az)입니다. 설치 또는 업그레이드하려면 [Azure Az PowerShell 모듈을 설치하는 방법](/powershell/azure/install-az-ps)을 참조하세요. `Az.ManagedServiceIdentity`는 미리 보기 모듈이며 Az 모듈의 일부로 설치되지 않습니다. 설치하려면 `Install-Module -Name Az.ManagedServiceIdentity`를 실행합니다.
* [Azure 가상 머신](../../virtual-machines/windows/quick-create-powershell.md). 이 머신을 중지하고 시작하므로 프로덕션 VM이 아니어야 합니다.
* [Automation Runbook](../manage-runbooks.md)에 대해 일반적으로 잘 알고 있습니다.

## <a name="assign-permissions-to-managed-identities"></a>관리 ID에 권한 할당

관리 ID에 권한을 할당하여 가상 머신을 중지 및 시작할 수 있도록 합니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 아래 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedManagedIdentity = "xUAMI"
    ```

1. PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 시스템 할당 관리 ID에 역할을 할당합니다.

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. 사용자 할당 관리 ID에 동일한 역할 할당이 필요합니다.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. 이 자습서에 사용된 대로 cmdlet `Get-AzUserAssignedIdentity` 및 `Get-AzAutomationAccount`를 실행하려면 시스템 할당 관리 ID에 대한 추가 권한이 필요합니다.

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>PowerShell Runbook 만들기

관리 ID에서의 실행을 허용하는 Runbook을 만듭니다. Runbook이 중지된 VM을 시작하거나 실행 중인 VM을 중지합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, Azure Automation 계정으로 이동합니다.

1. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.

1. **Runbook 만들기** 를 선택합니다.

    1. Runbook 이름을 `miTesting`로 지정합니다.
    1. **Runbook 형식** 드롭다운에서 **PowerShell** 을 선택합니다. 
    1. **런타임 버전** 드롭다운에서 **7.1(미리 보기)** 또는 **5.1** 을 선택합니다.
    1. 해당하는 **설명** 을 입력합니다.
    
1. **만들기** 를 클릭하여 Runbook을 만듭니다.

1. Runbook 편집기에서 다음 코드를 붙여넣습니다.

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            $AzureContext = (Connect-AzAccount -Identity).context
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
        -DefaultProfile $AzureContext
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
                -Name $UAMI -DefaultProfile $AzureContext
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup `
                    -Name $automationAccount `
                    -DefaultProfile $AzureContext).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    $AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context
    
                    # set and store context
                    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName `
        -Status -DefaultProfile $AzureContext).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status `
        -DefaultProfile $AzureContext).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " $AzureContext.Account.Id
    ```

1. 편집기에서 줄 8에 필요에 따라 `$automationAccount` 변수의 값을 수정합니다.

1. **저장** 을 선택한 후 **테스트 창** 을 선택합니다.

1. `RESOURCEGROUP` 및 `VMNAME` 매개 변수를 적절한 값으로 채웁니다. `METHOD` 매개 변수에 `SA`를 입력하고 `UAMI` 매개 변수에 `xUAMI`를 입력합니다. Runbook은 시스템 할당 관리 ID를 사용하여 VM의 전원 상태를 변경하려고 시도합니다.

1. **시작** 을 선택합니다. Runbook이 완료되면 출력은 다음과 유사하게 표시되어야 합니다.

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. `METHOD` 매개 변수의 값을 `UA`로 변경합니다.

1. **시작** 을 선택합니다. Runbook은 명명된 사용자 할당 관리 ID를 사용하여 VM의 전원 상태를 변경하려고 시도합니다. Runbook이 완료되면 출력은 다음과 유사하게 표시되어야 합니다.

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 리소스를 제거하려면 다음 Runbook을 실행합니다.

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 실행 계정이 아닌 [관리 ID](../automation-security-overview.md#managed-identities)를 사용하여 Azure Automation에서 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks)을 만들었습니다. PowerShell 워크플로 Runbook에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: PowerShell 워크플로 Runbook 만들기](automation-tutorial-runbook-textual.md)