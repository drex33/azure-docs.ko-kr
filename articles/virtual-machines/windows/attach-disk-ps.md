---
title: Azure에서 PowerShell을 사용하여 Windows VM에 데이터 디스크 연결
description: Resource Manager 배포 모델에서 PowerShell을 사용하여 새롭거나 기존의 데이터 디스크를 Windows VM에 연결하는 방법입니다.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f8fbcbb70ae73c9fdfe0aa388c07d23d6c4f8e20
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692622"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>PowerShell을 사용하여 Windows VM에 데이터 디스크 연결

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 PowerShell을 사용하여 새 디스크와 기존 디스크를 Windows 가상 머신에 연결하는 방법을 보여 줍니다. 

먼저 다음 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](../sizes.md)를 참조하세요.
* 프리미엄 SSD를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 머신과 같은 [Premium Storage 지원 VM 유형](../sizes-memory.md)이 필요합니다.

이 문서는 최신 버전으로 꾸준히 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 PowerShell을 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>가상 머신에 빈 데이터 디스크 추가

이 예에서는 기존 가상 머신에 빈 데이터 디스크를 추가하는 방법을 보여줍니다.

### <a name="using-managed-disks"></a>관리 디스크 사용

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>가용성 영역에서 Managed Disks 사용

가용성 영역에서 디스크를 만들려면 `-Zone` 매개 변수와 함께 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig)를 사용합니다. 다음 예제에서는 영역 *1* 에서 디스크를 만듭니다.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>디스크 초기화

빈 디스크가 추가되면 해당 디스크를 초기화해야 합니다. 디스크를 초기화하려면 VM에 로그인하여 디스크 관리를 사용합니다. VM을 만들 때 [WinRM](/windows/desktop/winrm/portal) 및 인증서를 사용하도록 설정한 경우 원격 PowerShell을 사용하여 디스크를 초기화할 수 있습니다. 또한 사용자 지정 스크립트 확장을 사용할 수 있습니다.

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

스크립트 파일에는 다음과 같이 디스크를 초기화하는 코드가 포함될 수 있습니다.

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>VM에 기존 데이터 디스크 추가

기존의 관리되는 디스크를 데이터 디스크로 VM에 연결할 수 있습니다.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>다음 단계

템플릿을 사용하여 관리 디스크를 배포할 수도 있습니다. 자세한 내용은 여러 데이터 디스크 배포에 관한 [Azure Resource Manager 템플릿의 관리 디스크 사용](../using-managed-disks-template-deployments.md) 또는 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-multiple-data-disk)을 참조하세요.
