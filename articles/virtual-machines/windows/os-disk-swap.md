---
title: PowerShell을 통해 Azure VM에 대한 OS 디스크 교체
description: PowerShell을 사용하여 Azure Virtual Machine에서 사용하는 운영 체제 디스크를 변경합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2892ae3ff10110c2e45d29edd009a9116da0b166
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687509"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>PowerShell을 사용하여 Azure VM에서 사용하는 OS 디스크를 변경합니다.

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

기존의 VM이 있지만 백업 디스크 또는 다른 OS 디스크에 대한 디스크를 교체하려는 경우 Azure PowerShell을 사용하여 OS 디스크를 교체할 수 있습니다. VM을 삭제하고 다시 만들 필요가 없습니다. 이미 사용 중이 아니면 관리 디스크를 다른 리소스에서 사용할 수도 있습니다.

 

VM은 반드시 중지/할당 취소되어야 합니다. 그래야만 관리 디스크의 리소스 ID를 다른 관리 디스크의 리소스 ID로 바꿀 수 있습니다.

VM 크기 및 스토리지 유형이 연결할 디스크와 호환되어야 합니다. 예를 들어, 사용하려는 디스크가 Premium Storage에 있으면, VM이 Premium Storage를 지원할 수 있어야 합니다(예: DS 시리즈 크기). 두 디스크의 크기도 같아야 합니다.
또한 암호화되지 않은 VM을 암호화된 OS 디스크와 혼합하는 것은 지원되지 않습니다. VM에서 Azure Disk Encryption을 사용하지 않는 경우 스왑되는 OS 디스크는 Azure Disk Encryption을 사용하지 않아야 합니다. 디스크에서 디스크 암호화 집합을 사용하는 경우 두 디스크는 모두 동일한 디스크 암호화 집합에 속해야 합니다.

[Get-AzDisk](/powershell/module/az.compute/get-azdisk)를 사용하여 리소스 그룹에서 디스크 목록 가져오기

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
사용할 디스크의 이름이 있는 경우 해당 이름을 VM에 대한 OS 디스크로 설정합니다. 이 예제에서는 *myVM* 이라는 VM을 중지/할당 취소하고 *newDisk* 라는 이름의 디스크를 새 OS제 디스크로 할당합니다. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**다음 단계**

디스크의 복사본을 만들려면 [디스크 스냅샷](snapshot-copy-managed-disk.md)을 참조하세요.
