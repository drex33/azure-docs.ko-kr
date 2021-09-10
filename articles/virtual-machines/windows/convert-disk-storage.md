---
title: Azure PowerShell을 사용하여 관리 디스크 스토리지를 여러 디스크 유형으로 변환
description: Azure PowerShell을 사용하여 관리 디스크 스토리지를 여러 디스크 유형으로 변환하는 방법입니다.
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 994b24110399057741b9f62f7ecf30a8a505fd6d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537990"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>관리 디스크의 스토리지 형식 업데이트

Azure 관리 디스크에는 Azure 울트라 디스크, 프리미엄 SSD, 표준 SSD, 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구 사항에 따라 프리미엄 SSD, 표준 SSD, 표준 HDD 간에 전환할 수 있습니다. 아직 울트라 디스크와의 전환은 불가능하기 때문에 새 디스크를 배포해야 합니다.

이 기능은 현재 비관리 디스크에 지원되지 않습니다. 하지만 [비관리 디스크를 관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)하면 디스크 유형을 간편하게 전환할 수 있습니다.



## <a name="before-you-begin"></a>시작하기 전에

* 변환하려면 VM(가상 머신)을 재시작해야 하므로 기존 유지 관리 기간 동안 디스크 스토리지의 마이그레이션을 예약해야 합니다.
* 디스크가 비관리형인 경우 먼저 [Managed Disks로 변환](convert-unmanaged-to-managed-disks.md)하여 스토리지 옵션 간을 전환할 수 있습니다.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>VM의 모든 관리 디스크를 한 계정에서 다른 계정으로 전환

이 예제에서는 VM의 모든 디스크를 프리미엄 스토리지로 변환하는 방법을 보여줍니다. 그러나 이 예제에서는 $storageType 변수를 변경하여 VM의 디스크 유형을 표준 SSD 또는 표준 HDD로 변환할 수 있습니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](../sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>표준과 프리미엄 간에 개별 Managed Disks 전환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합할 수도 있습니다. 더 나은 성능이 필요한 디스크만 업그레이드하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준에서 프리미엄 스토리지로 변환하는 방법을 보여줍니다. 그러나 이 예제에서는 $storageType 변수를 변경하여 VM의 디스크 유형을 표준 SSD 또는 표준 HDD로 변환할 수 있습니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](../sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환하는 방법을 보여 줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Managed Disks를 다른 디스크 형식으로 전환

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **가상 머신** 목록에서 VM을 선택합니다.
3. VM이 중지되지 않는 경우 VM **개요** 창 상단에서 **중지** 를 선택하고 VM이 중지될 때까지 기다립니다.
4. VM 창 메뉴에서 **디스크** 를 선택합니다.
5. 변환할 디스크를 선택합니다.
6. 메뉴에서 **크기 + 성능** 을 선택합니다.
7. **계정 유형** 을 원래 디스크 유형에서 원하는 디스크 유형으로 변경합니다.
8. **저장** 을 선택하고 디스크 창을 닫습니다.

디스크 유형 변환이 즉각적입니다. 변환 후 VM을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[스냅샷](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.
