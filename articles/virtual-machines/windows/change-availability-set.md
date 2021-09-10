---
title: Azure PowerShell을 사용하여 VM 가용성 집합 변경
description: Azure PowerShell을 사용하여 가상 머신에 대한 가용성 집합을 변경하는 방법에 대해 알아봅니다.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 3/8/2021
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aaf9502699bbd7108af1d037fa05d82115fb0deb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697579"
---
# <a name="change-the-availability-set-for-a-vm-using-azure-powershell"></a>Azure PowerShell을 사용하여 VM의 가용성 집합 변경    

**적용 대상:** :heavy_check_mark: Windows VM 


다음 단계에서는 Azure PowerShell을 사용하여 VM의 가용성 집합을 변경하는 방법을 설명합니다. VM은 생성될 때만 가용성 집합에 추가될 수 있습니다. 가용성 집합을 변경하려면 가상 머신을 삭제한 다음, 다시 만들어야 합니다. 

이 문서는 Linux 및 Windows VM 모두에 적용됩니다.

이 문서는 2019/2/12에 [Azure Cloud Shell](https://shell.azure.com/powershell) 및 [Az PowerShell 모듈](/powershell/azure/install-az-ps) 버전 1.2.0을 사용하여 마지막으로 테스트되었습니다.

이 예제에서는 VM이 부하 분산 장치에 연결되어 있는지 확인하지 않습니다. VM이 부하 분산 장치에 연결되어 있는 경우 스크립트를 업데이트하여 해당 사례를 처리해야 합니다. 이 프로세스를 완료한 후 일부 확장을 다시 설치해야 할 수도 있습니다.


## <a name="change-the-availability-set"></a>가용성 집합 변경 

다음 스크립트는 필요한 정보를 수집하고, 원본 VM을 삭제한 다음 새 가용성 집합에서 다시 만드는 예제를 제공합니다.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>다음 단계

[데이터 디스크](attach-managed-disk-portal.md)를 더 추가하여 VM에 스토리지를 좀 더 추가합니다.
