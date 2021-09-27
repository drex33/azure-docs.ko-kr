---
title: 명령줄 도구를 사용 하 여 Vm 시작 및 중지
description: Azure DevTest Labs에서 명령줄 도구를 사용하여 가상 머신을 시작하고 중지하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e0c75cda526f5a365badf3f97aeb92a94b93588f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655565"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>명령줄 도구를 사용하여 Azure DevTest Labs의 가상 머신 시작 및 중지
이 문서에서는 Azure PowerShell 또는 Azure CLI를 사용하여 Azure DevTest Labs의 랩에서 가상 머신을 시작하거나 중지하는 방법을 보여 줍니다. PowerShell/CLI 스크립트를 만들면 작업을 자동화할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
Azure DevTest Labs는 빠르고 쉽게 간결한 개발/테스트 환경을 만드는 수단입니다. 이를 통해 비용을 관리하고, 신속하게 VM을 프로비저닝하고, 낭비를 최소화할 수 있습니다.  Azure Portal에는 특정 시간에 랩의 VM을 자동으로 시작 및 중지하도록 구성할 수 있는 기본 제공 기능이 있습니다. 

그러나 일부 시나리오의 경우 PowerShell/CLI 스크립트에서 VM의 시작 및 중지를 자동화하는 것이 좋습니다. 이는 특정 시간이 아니라 언제든지 개별 머신을 시작하고 중지할 수 있는 유연성을 제공합니다. 스크립트를 사용하여 작업을 실행하는 것이 도움이 되는 몇 가지 상황은 다음과 같습니다.

- 3계층 애플리케이션을 테스트 환경의 일부로 사용하는 경우 계층을 순서대로 시작해야 합니다. 
- 사용자 지정 조건을 충족할 때는 VM을 꺼서 비용을 절약합니다. 
- CI/CD 워크플로 내에서 이를 작업으로 사용하여 흐름의 시작 부분에서 시작하고, VM을 빌드 머신, 테스트 머신 또는 인프라로 사용한 다음, 프로세스가 완료되면 VM을 중지합니다. 이에 대한 예는 Azure DevTest Labs를 사용한 사용자 지정 이미지 팩터리입니다.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 다음 스크립트는 Azure PowerShell Az 모듈을 사용합니다. 

다음 PowerShell 스크립트는 랩에서 VM을 시작합니다. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)이 이 스크립트의 핵심입니다. **ResourceId** 매개 변수는 랩의 VM에 맞게 정규화된 리소스 ID입니다. **Action** 매개 변수는 필요에 따라 **시작** 또는 **중지** 옵션이 설정되는 위치입니다.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
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
[Azure CLI](/cli/azure/get-started-with-azure-cli)는 DevTest Labs VM의 시작 및 중지를 자동화하는 또 다른 수단입니다. Azure CLI를 다른 운영 체제에 [설치](/cli/azure/install-azure-cli)할 수 있습니다. 다음 스크립트는 랩에서 VM을 시작 및 중지하기 위한 명령을 제공합니다. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>다음 단계
Azure Portal을 사용하여 이러한 작업을 수행하는 방법에 대해서는 [VM 다시 시작](devtest-lab-restart-vm.md) 문서를 참조하세요.
