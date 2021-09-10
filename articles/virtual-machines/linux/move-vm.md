---
title: Azure CLI를 사용하여 VM 이동
description: Azure CLI를 사용하여 다른 Azure 구독 또는 리소스 그룹으로 VM을 이동합니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: c2ceae3fbf1a59f5f148a488900e9bb88e6be4e4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688282"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>VM을 다른 구독 또는 리소스 그룹으로 이동

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 리소스 그룹 또는 구독 간에 VM(가상 머신)을 이동하는 방법을 안내합니다. 개인 구독에서 VM을 만들고 회사 구독으로 이동하려면 구독 간의 VM 이동이 편리할 수 있습니다.

> [!IMPORTANT]
>새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동한 후에는 새 리소스 ID를 사용하도록 도구와 스크립트를 업데이트해야 합니다.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Azure CLI를 사용하여 VM 이동


Azure CLI를 사용하여 VM을 이동하기 전에 동일한 테넌트 내에서 원본 및 대상 구독이 존재하는지 확인해야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 [az account show](/cli/azure/account)를 사용합니다.

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
원본 및 대상 구독에 대한 테넌트 ID가 동일하지 않으면 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하여 리소스를 새 테넌트로 이동해야 합니다.

VM을 성공적으로 이동하려면 VM 및 모든 지원 리소스를 이동해야 합니다. [az resource list](/cli/azure/resource) 명령을 사용하여 리소스 그룹 및 해당 ID에서 모든 리소스를 나열합니다. 이후 명령에 ID를 복사하고 붙여넣을 수 있도록 파일에이 명령의 출력을 파이프할 수 있습니다.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```
`--interactive`를 사용하는 경우 `table` 출력은 사용할 수 없습니다. `json`과 같은 다른 옵션으로 출력을 변경합니다.

다른 리소스 그룹에 VM 및 해당 리소스를 이동하려면 [az resource move](/cli/azure/resource)를 사용합니다. 다음 예제에서는 필요한 VM 및 가장 일반적인 리소스를 이동하는 방법을 보여 줍니다. **-ids** 매개 변수를 사용하고 이동할 리소스에 대한 ID의 쉼표로 구분된 목록을 공백 없이 제출합니다.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

VM 및 해당 리소스를 다른 구독으로 이동하려는 경우 **--destination-subscriptionId** 매개 변수를 추가하여 대상 구독을 지정합니다.

지정된 리소스를 이동할지 확인하는 메시지가 표시되면 **Y** 를 입력하여 확인합니다.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>다음 단계
리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.    
