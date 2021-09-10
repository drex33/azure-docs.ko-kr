---
title: Azure PowerShell을 사용하여 근접 배치 그룹 만들기
description: Azure PowerShell을 사용하여 근접 배치 그룹을 만들고 사용하는 방법에 대해 알아봅니다.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: zivr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cad5029c88c3444ab42a53af950d78a4167ea878
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697171"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-powershell"></a>Azure PowerShell을 사용하여 근접 배치 그룹에 VM 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

VM을 최대한 가깝게 유지하고, 대기 시간을 최대한 줄이려면, VM을 [근접 배치 그룹](../co-location.md#proximity-placement-groups) 내에 배포해야 합니다.

근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가까운 위치에 있도록 하는 데 사용되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구하는 작업에 유용합니다.


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용하여 근접 배치 그룹을 만듭니다. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>VM 만들기

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 경우 `-ProximityPlacementGroup $ppg.Id`를 사용하여 근접 배치 그룹 ID를 참조하여 근접 배치 그룹에 VM을 만듭니다.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)를 사용하여 배치 그룹에서 VM을 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>기존 VM을 근접 배치 그룹으로 이동

근접 배치 그룹에 기존 VM을 추가할 수도 있습니다. 먼저 VM의 할당을 취소한 다음에 VM을 업데이트하고 다시 시작해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>기존 VM을 근접 배치 그룹에서 제거

근접 배치 그룹에서 VM을 제거하려면 먼저 VM의 할당을 취소한 다음 VM을 업데이트하고 다시 시작해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>가용성 집합
또한 근접 배치 그룹에 가용성 집합을 만들 수 있습니다. [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) cmdlet과 동일한 `-ProximityPlacementGroup` 매개 변수를 사용하여 가용성 집합을 만들면 가용성 집합에서 만든 모든 VM이 동일한 근접 배치 그룹에도 만들어집니다.

근접 배치 그룹에 기존 가용성 집합을 추가하거나 제거하려면 먼저 가용성 집합의 모든 VM을 중지해야 합니다. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>기존 가용성 집합을 근접 배치 그룹으로 이동

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>기존 가용성 집합을 근접 배치 그룹에서 제거

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>확장 집합

또한 근접 배치 그룹에 확장 집합을 만들 수 있습니다. [New-AzVmss](/powershell/module/az.compute/new-azvmss)와 동일한 `-ProximityPlacementGroup` 매개 변수를 사용하여 확장 집합을 만들면 모든 인스턴스가 동일한 근접 배치 그룹에 만들어집니다.


근접 배치 그룹에 기존 확장 집합을 추가하거나 제거하려면 먼저 확장 집합을 중지해야 합니다. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>기존 확장 집합을 근접 배치 그룹으로 이동

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>기존 확장 집합을 근접 배치 그룹에서 제거

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

또한 [Azure CLI](../linux/proximity-placement-groups.md)를 사용하여 근접 배치 그룹을 만들 수도 있습니다.
