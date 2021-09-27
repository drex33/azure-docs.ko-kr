---
title: 가상 머신 크기 조정
description: Azure 가상 머신에 사용되는 VM 크기를 변경합니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 80387608b8c8ab7069ed989078472102e3d9afe8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705846"
---
# <a name="change-the-size-of-a-virtual-machine"></a>가상 컴퓨터의 크기 변경 

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 VM을 다른 [VM 크기](sizes.md)로 전환하는 방법을 보여 줍니다.

VM(가상 머신)을 만든 후 VM 크기를 변경하여 VM의 크기를 확장 또는 축소할 수 있습니다. 경우에 따라 먼저 VM의 할당을 취소해야 합니다. 이는 현재 VM을 호스트하는 하드웨어 클러스터에서 새 크기를 사용할 수 없는 경우에 발생할 수 있습니다.

VM에서 Premium Storage를 사용하는 경우 크기의 **s** 버전을 선택하여 Premium Storage 지원을 받는지 확인합니다. 예를 들어 Standard_E4_v3 대신 Standard_E4 **s** _v3을 선택합니다.

## <a name="change-the-vm-size"></a>VM 크기 변경

### <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 가상 머신에 대한 페이지를 엽니다.
1. 왼쪽 메뉴에서 **크기** 를 선택합니다.
1. 사용 가능한 크기 목록에서 새 크기를 선택하고 **크기 조정** 을 선택합니다.


가상 머신이 현재 실행 중인 경우, 크기를 변경하면 가상 머신이 다시 시작됩니다. 

VM이 아직 실행 중이 고 목록에 원하는 크기가 표시 되지 않는 경우 가상 컴퓨터를 중지 하면 더 많은 크기를 표시할 수 있습니다.

### <a name="cli"></a>[CLI](#tab/cli)

VM의 크기를 조정하려면 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다.

1. VM이 [az vm list-vm-resize-options](/cli/azure/vm)로 호스트된 하드웨어 클러스터에서 사용 가능한 VM 크기의 목록을 봅니다. 다음 예제에서는 리소스 그룹 `myResourceGroup` 지역의 VM `myVM`에 대한 VM 크기를 나열합니다.
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. 원하는 VM 크기가 나열되는 경우 [az vm resize](/cli/azure/vm)로 VM의 크기를 조정합니다. 다음 예제에서는 VM `myVM`을 `Standard_DS3_v2` 크기로 조정합니다.
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    이 과정에서 VM이 다시 시작됩니다. 다시 시작한 후 기존의 OS 및 데이터 디스크는 다시 매핑됩니다. 임시 디스크에 있는 모든 내용이 손실됩니다.

3. 원하는 VM 크기가 목록에 없는 경우 먼저 [az vm deallocate](/cli/azure/vm)로 VM 할당을 취소해야 합니다. 이 프로세스를 통해 VM은 해당 지역에서 지원하는 사용 가능한 크기로 조정된 다음 시작될 수 있습니다. 다음 단계에서는 할당을 취소하고 크기를 조정한 다음 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 시작합니다.
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소도 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

**PowerShell을 사용 하 여 가용성 집합에 없는 VM의 크기를 조정 합니다.**

일부 변수를 설정합니다. 사용자 고유의 정보로 값을 대체합니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM이 호스트 되는 지역에서 사용할 수 있는 VM 크기를 나열 합니다. 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다. 원하는 크기가 목록에 나열되지 않으면 3단계로 이동합니다.
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

원하는 크기가 나열되지 않은 경우에는 다음 명령을 실행하여 VM의 할당을 취소하고 크기를 조정한 다음, VM을 다시 시작합니다. **\<newVMsize>** 를 원하는 크기로 바꿉니다.
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소가 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다. 
> 
> 

**PowerShell을 사용하여 가용성 집합에서 VM의 크기 조정**

가용성 집합에서 VM에 대한 새 크기를 현재 VM을 호스트하는 하드웨어 클러스터에서 사용할 수 없는 경우 가용성 집합의 모든 VM을 할당 취소하여 VM 크기를 조정해야 합니다. 또한 VM 크기를 조정한 후 가용성 집합에서 다른 VM의 크기를 업데이트해야 할 수 있습니다. 가용성 집합에서 VM의 크기를 조정하려면 다음 단계를 수행합니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다. 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다. 나열되지 않으면 다음 섹션으로 이동합니다.
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
원하는 크기가 목록에 나열되지 않는 경우 다음 단계를 진행하여 가용성 집합의 모든 VM을 할당 취소하고 VM 크기를 조정한 후 다시 시작합니다.

가용성 집합의 VM을 모두 중지합니다.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

가용성 집합의 VM을 크기 조정하고 다시 시작합니다.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>다음 단계

스케일링 성능을 높이려면 여러 VM 인스턴스를 실행하고 스케일 아웃하세요. 자세한 내용은 [Virtual Machine Scale Set에서 자동으로 머신 스케일링](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)을 참조하세요.
