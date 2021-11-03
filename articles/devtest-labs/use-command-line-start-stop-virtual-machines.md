---
title: 명령줄 도구를 사용하여 VM 시작 및 중지
description: Azure DevTest Labs에서 명령줄 도구를 사용하여 가상 머신을 시작하고 중지하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 58440d00f888816f95aac0159063a7b6d6fc5289
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430194"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>명령줄 도구를 사용하여 Azure DevTest Labs의 가상 머신 시작 및 중지

이 문서에서는 Azure DevTest Labs 랩 가상 머신을 시작하거나 중지하는 방법을 보여줍니다. Azure PowerShell 또는 Azure CLI 스크립트를 만들어 이러한 작업을 자동화할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
- PowerShell을 사용하는 경우 [워크스테이션에 Az 모듈이](/powershell/azure/new-azureps-module-az) 설치되어 있어야 합니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.

- Azure CLI를 사용하려 하나 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

- DevTest Labs 랩의 가상 머신.

## <a name="overview"></a>개요

Azure DevTest Labs 빠르고 쉽고 간결한 개발/테스트 환경을 만드는 방법을 제공합니다. 랩을 사용하면 비용을 관리하고, 신속하게 VM을 만들고, 낭비를 최소화할 수 있습니다. Azure Portal 기능을 사용하여 특정 시간에 VM을 자동으로 시작하고 중지할 수 있습니다. 그러나 스크립트에서 VM의 시작 및 중지를 자동화할 수 있습니다. 다음은 스크립트를 사용하여 이러한 작업을 실행하는 것이 유용한 몇 가지 상황입니다.

- 테스트 환경의 일부로 3계층 애플리케이션을 사용하는 경우 계층을 시퀀스로 시작해야 합니다. 
- 비용을 절약하기 위해 사용자 지정 조건이 충족되는 경우 VM을 해제합니다. 
- 연속 통합 및 지속적인 업데이트 워크플로 내의 작업으로서 처음부터 시작한 다음 프로세스가 완료되면 VM을 중지합니다. 이 워크플로의 예로는 Azure DevTest Labs 있는 사용자 지정 이미지 팩터리입니다.  

## <a name="azure-powershell"></a>Azure PowerShell

다음 PowerShell 스크립트는 랩에서 VM을 시작하거나 중지할 수 있습니다. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)이 이 스크립트의 핵심입니다. **ResourceId** 매개 변수는 랩의 VM에 맞게 정규화된 리소스 ID입니다. **Action** 매개 변수는 필요에 따라 **시작** 또는 **중지** 옵션이 설정되는 위치입니다.

1. 워크스테이션에서 PowerShell [커넥트-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $devTestLabName = "yourlabname"
    $vMToStart = "vmname"
    
    # The action on the virtual machine (Start or Stop)
    $vmAction = "Start"
    ```

1. 에 전달한 값에 따라 VM을 시작하거나 `$vmAction` 중지합니다.

    ```powershell
    # Get the lab information
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName
    
    # Start or stop the VM and return a succeeded or failed status
    $returnStatus = Invoke-AzResourceAction `
                        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                        -Action $vmAction `
                        -Force
    
    if ($returnStatus.Status -eq 'Succeeded') {
        Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
    }
    else {
        Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
    }
    ```

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/get-started-with-azure-cli)는 DevTest Labs VM의 시작 및 중지를 자동화하는 또 다른 수단입니다. 다음 스크립트는 랩에서 VM을 시작 및 중지하기 위한 명령을 제공합니다. 이 섹션에서 변수를 사용하는 것은 Windows 환경을 기반으로 합니다. Bash 또는 다른 환경에서는 약간의 변형이 필요합니다.

1. `SubscriptionID`, `yourlabname`, `yourVM` 및 `action`을 적절한 값으로 바꿉니다. 그런 다음 스크립트를 실행합니다.

    ```azurecli
    set SUBSCIPTIONID=SubscriptionID
    set DEVTESTLABNAME=yourlabname
    set VMNAME=yourVM
    
    REM The action on the virtual machine (Start or Stop)
    set ACTION=action
    ```

1. Azure 구독에 로그인하고 랩이 포함된 리소스 그룹의 이름을 얻습니다.

    ```azurecli
    az login
    
    REM If you have multiple subscriptions, set the one to use
    REM az account set --subscription %SUBSCIPTIONID%

    az resource list --resource-type "Microsoft.DevTestLab/labs" --name %DEVTESTLABNAME% --query "[0].resourceGroup"
    ```

1. `resourceGroup`를 이전 단계에서 가져온 값으로 대체합니다. 그런 다음 스크립트를 실행합니다.

    ```azurecli
    set RESOURCEGROUP=resourceGroup
    ```

1. 에 전달한 값에 따라 VM을 시작하거나 `ACTION` 중지합니다.

    ```azurecli
    az lab vm %ACTION% --lab-name %DEVTESTLABNAME% --name %VMNAME% --resource-group %RESOURCEGROUP%
    ```

## <a name="next-steps"></a>다음 단계

Azure Portal을 사용하여 이러한 작업을 수행하는 방법에 대해서는 [VM 다시 시작](devtest-lab-restart-vm.md) 문서를 참조하세요.
