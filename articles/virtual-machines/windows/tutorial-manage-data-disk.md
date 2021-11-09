---
title: 자습서- Azure PowerShell을 사용하여 Azure 디스크 관리
description: 이 자습서에서는 Azure PowerShell은 사용하여 가상 머신용 Azure 디스크를 만들고 관리하는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: a1ea898dd246977bbb7284a18349a265efd7655d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063121"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 디스크 관리

Azure VM(가상 머신)은 디스크를 사용하여 OS(운영 체제), 애플리케이션 및 데이터를 저장합니다. VM을 만드는 경우 예상되는 워크로드에 적합한 디스크 크기와 구성을 선택해야 합니다.

이 자습서에서는 VM 디스크의 배포 및 관리에 대해 설명 합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 데이터 디스크 만들기, 연결 및 초기화
> * 디스크의 상태 확인
> * 디스크 초기화
> * 디스크 확장 및 업그레이드
> * 디스크 분리 및 삭제

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 포함된 Azure 계정이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>VM 만들기

이 자습서의 연습에는 VM이 ​​필요합니다. 이 섹션의 단계에 따라 하나를 만듭니다.

시작하기 전에 샘플 코드의 첫 번째 줄에 있는 `$azRegion` 변수를 찾아서 원하는 지역을 반영하도록 값을 업데이트합니다. 예를 들어 **미국 중부** 지역을 지정하려면 `$azRegion = "Central US"`를 사용합니다. 다음으로, 코드를 사용하여 VM을 새 리소스 그룹 내에 배포합니다. VM의 로컬 관리자 계정에 대한 사용자 이름 및 암호 값을 입력하라는 메시지가 표시됩니다.

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

출력에서 VM이 성공적으로 만들어졌는지 확인합니다.

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

VM이 프로비전되고 두 개의 디스크가 자동으로 만들어져 연결됩니다.

- 가상 머신의 운영체제를 호스트하는 **운영 체제 디스크**
- 주로 임시 데이터 처리와 같은 작업에 사용되는 **임시 디스크**

## <a name="add-a-data-disk"></a>데이터 디스크 추가

가능하면 애플리케이션 및 사용자 데이터와 OS 관련 데이터를 분리하는 것이 좋습니다. 사용자 또는 애플리케이션 데이터를 VM에 저장해야 하는 경우 일반적으로 추가 데이터 디스크를 만들어 연결합니다.

이 섹션의 단계에 따라 VM에서 데이터 디스크를 만들고, 연결하고, 초기화합니다.

### <a name="create-the-data-disk"></a>데이터 디스크 만들기

이 섹션에서는 데이터 디스크를 만드는 과정을 안내합니다.

1. 데이터 디스크를 만들려면 먼저 디스크 개체를 만들어야 합니다. 다음 코드 샘플에서는 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) cmdlet을 사용하여 디스크 개체를 구성합니다.

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. 디스크 개체가 만들어지면 [New-AzDisk](/powershell/module/az.compute/new-azdisk) ​​cmdlet을 사용하여 데이터 디스크를 프로비전합니다.

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    [Get-AzDisk](/powershell/module/az.compute/get-azdisk) ​​cmdlet을 사용하여 디스크가 만들어졌는지 확인할 수 있습니다.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    이 예제의 출력에서 디스크가 만들어졌는지 확인합니다. `DiskState` 및 `ManagedBy` 속성 값에서 디스크가 아직 연결되지 않았는지 확인합니다.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>데이터 디스크 연결

VM에서 액세스할 수 있으려면 먼저 데이터 디스크를 VM에 연결해야 합니다. 이 섹션의 단계를 완료하여 VM에 대한 참조를 만들고, 디스크를 연결하고, VM 구성을 업데이트합니다.

1. 데이터 디스크를 연결할 VM을 가져옵니다. 다음 샘플 코드에서는 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet을 사용하여 VM에 대한 참조를 만듭니다.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. 그런 다음, [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) ​​cmdlet을 사용하여 데이터 디스크를 VM 구성에 연결합니다.

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. 마지막으로 [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) cmdlet을 사용하여 VM 구성을 업데이트합니다.

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    잠시 후 출력에서 ​​성공적으로 연결되었는지 확인합니다.

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>데이터 디스크 초기화

데이터 디스크가 VM에 연결되면 디스크를 사용하도록 OS를 구성해야 합니다. 다음 섹션에서는 원격 VM에 연결하고 추가된 첫 번째 디스크를 구성하는 방법에 대한 지침을 제공합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 데이터 디스크를 연결한 VM을 찾습니다. RDP(원격 데스크톱 프로토콜) 연결을 만들고, 로컬 관리자 권한으로 로그인합니다.

1. 원격 VM에 대한 RDP 연결이 설정되면 Windows **시작** 메뉴를 선택합니다. 검색 상자에서 **PowerShell** 을 입력하고, **Windows PowerShell** 을 선택하여 PowerShell 창을 엽니다.

    [![원격 데스크톱 연결 창의 이미지](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. 열려 있는 PowerShell 창에서 다음 스크립트를 실행합니다.

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    출력에서 성공적으로 초기화되었는지 확인합니다.

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>디스크 확장

VM에 사용 가능한 디스크 공간이 부족한 경우 Azure 디스크를 확장하여 추가 스토리지 용량을 제공할 수 있습니다.

일부 시나리오에서는 데이터를 OS 디스크에 저장해야 합니다. 예를 들어 구성 요소를 OS 드라이브에 설치하는 레거시 애플리케이션을 지원해야 할 수 있습니다. 또한 더 큰 OS 드라이브가 있는 온-프레미스 물리적 PC 또는 VM을 마이그레이션해야 할 수도 있습니다. 이러한 경우 VM의 OS 디스크를 확장해야 할 수 있습니다.

기존 디스크를 축소하는 것은 지원되지 않으며 이는 잠재적으로 데이터가 손실될 수 있습니다.

### <a name="update-the-disks-size"></a>디스크 크기 업데이트

아래 단계에 따라 OS 디스크 또는 데이터 디스크의 크기를 조정합니다.

1. `Get-AzVM` cmdlet을 사용하여 크기를 조정할 디스크가 포함된 VM을 선택합니다.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. VM의 디스크 크기를 조정하려면 먼저 VM을 중지해야 합니다. `Stop-AzVM` cmdlet을 사용하여 VM을 중지합니다. 확인하라는 메시지가 표시됩니다.

    > [!IMPORTANT]
    > VM 종료를 시작하기 전에 항상 손실될 수 있는 중요한 리소스 또는 데이터가 없는지 확인하세요.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    잠시 후 출력에서 ​​컴퓨터가 성공적으로 중지되었는지 확인합니다.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. VM이 중지되면 `Get-AzDisk` cmdlet을 사용하여 VM에 연결된 OS 또는 데이터 디스크에 대한 참조를 가져옵니다.

    다음 예제에서는 VM의 OS 디스크를 선택합니다.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    다음 예제에서는 VM의 첫 번째 데이터 디스크를 선택합니다.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. 이제 디스크에 대한 참조가 있으므로 디스크 크기를 250GiB로 설정합니다.

    > [!IMPORTANT]
    > 새 크기가 기존 디스크 크기보다 커야 합니다. OS 디스크에 허용되는 최대 크기는 4,095GiB입니다.

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. 다음으로 `Update-AzDisk` cmdlet을 사용하여 디스크 이미지를 업데이트합니다.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    디스크 이미지가 업데이트되고, 출력에서 ​​디스크의 새 크기를 확인합니다.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    잠시 후 출력에서 ​​컴퓨터가 성공적으로 시작되었는지 확인합니다.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>OS에서 디스크 볼륨 확장

새 디스크 크기를 활용하려면 먼저 OS 내에서 볼륨을 확장해야 합니다. 아래 단계에 따라 디스크 볼륨을 확장하고 새 디스크 크기를 활용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 데이터 디스크를 연결한 VM을 찾습니다. RDP(원격 데스크톱 프로토콜) 연결을 만들고, 로그인합니다. 관리 계정에 더 이상 액세스할 수 없는 경우 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet을 사용하여 지정된 사용자 이름과 암호에 대한 자격 증명 개체를 만듭니다.

1. 원격 VM에 대한 RDP 연결이 설정되면 Windows **시작** 메뉴를 선택합니다. 검색 상자에서 **PowerShell** 을 입력하고, **Windows PowerShell** 을 선택하여 PowerShell 창을 엽니다.

    [![원격 데스크톱 연결 창의 이미지](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. PowerShell을 열고, 다음 스크립트를 실행합니다. `-DriveLetter` 변수의 값을 적절하게 변경합니다. 예를 들어 **F:** 드라이브의 파티션 크기를 조정하려면 `$driveLetter = "F"`를 사용합니다.

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. RDP 창을 최소화하고, Azure Cloud Shell로 다시 전환합니다. `Get-AzDisk` cmdlet을 사용하여 디스크 크기가 성공적으로 조정되었는지 확인합니다.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>디스크 업그레이드

조직의 워크로드 변경에 대응하는 방법에는 여러 가지가 있습니다. 예를 들어 증가하는 수요를 처리하기 위해 표준 HDD를 프리미엄 SSD로 업그레이드하도록 선택할 수 있습니다.

이 섹션의 단계에 따라 관리 디스크를 표준에서 프리미엄으로 업그레이드합니다.

1. `Get-AzVM` cmdlet을 사용하여 업그레이드할 디스크가 포함된 VM을 선택합니다.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. VM의 디스크를 업그레이드하려면 먼저 VM을 중지해야 합니다. `Stop-AzVM` cmdlet을 사용하여 VM을 중지합니다. 확인하라는 메시지가 표시됩니다.

    > [!IMPORTANT]
    > VM 종료를 시작하기 전에 항상 손실될 수 있는 중요한 리소스 또는 데이터가 없는지 확인하세요.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    잠시 후 출력에서 ​​컴퓨터가 성공적으로 중지되었는지 확인합니다.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. VM이 중지되면 `Get-AzDisk` cmdlet을 사용하여 VM에 연결된 OS 또는 데이터 디스크에 대한 참조를 가져옵니다.

    다음 예제에서는 VM의 OS 디스크를 선택합니다.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    다음 예제에서는 VM의 첫 번째 데이터 디스크를 선택합니다.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. 이제 디스크에 대한 참조가 있으므로 디스크의 SKU를 **Premium_LRS** 로 설정합니다.

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. 다음으로 `Update-AzDisk` cmdlet을 사용하여 디스크 이미지를 업데이트합니다.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    디스크 이미지가 업데이트됩니다. 다음 예제 코드를 사용하여 디스크의 SKU가 업그레이드되었는지 확인합니다.

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    출력에서 디스크의 새 SKU를 확인합니다.

    ```Output
    Premium_LRS
    ```

1. 마지막으로 `Start-AzVM` cmdlet을 사용하여 VM을 다시 시작합니다.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    잠시 후 출력에서 ​​컴퓨터가 성공적으로 시작되었는지 확인합니다.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>데이터 디스크 분리

데이터 디스크를 다른 VM에 연결하려는 경우 또는 더 이상 필요하지 않은 경우 VM에서 데이터 디스크를 분리할 수 있습니다. 기본적으로 분리된 디스크는 의도하지 않은 데이터 손실을 방지하기 위해 삭제되지 않습니다. 분리된 디스크는 삭제될 때까지 스토리지 요금이 계속 발생합니다.

1. 먼저 `Get-AzVM` cmdlet을 사용하여 디스크가 연결된 VM을 선택합니다.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 다음으로 `Remove-AzVMDataDisk` cmdlet을 사용하여 VM에서 디스크를 분리합니다.

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. `Update-AzVM` cmdlet을 사용하여 VM 상태를 업데이트한 다음, 데이터 디스크를 제거합니다.

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    잠시 후 출력에서 ​​VM이 성공적으로 업데이트되었는지 ​​확인합니다.

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>데이터 디스크 삭제

VM이 삭제되면 VM에 연결된 데이터 디스크는 프로비전된 상태로 유지되고 삭제될 때까지 요금이 계속 발생합니다. 이 기본 동작은 의도하지 않은 삭제로 인한 데이터 손실을 방지하는 데 도움이 됩니다.

다음 샘플 PowerShell 스크립트를 사용하여 연결되지 않은 디스크를 삭제할 수 있습니다. `-ResourceGroupName` 스위치가 `Get-AzDisk` cmdlet에서 사용되므로 디스크 검색은 **myDemoResourceGroup** 으로 제한됩니다.

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

출력과 같이 연결되지 않은 데이터 디스크가 삭제됩니다.

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, VM 및 모든 관련 리소스를 삭제합니다. 다음 샘플 PowerShell 스크립트를 사용하여 이 자습서의 앞부분에서 만든 리소스 그룹을 삭제할 수 있습니다.  

> [!CAUTION]
> 리소스 그룹을 삭제할 때에 주의합니다. 중요한 데이터가 손실되지 않도록 방지하려면 항상 삭제하기 전에 중요한 리소스 또는 데이터가 리소스 그룹 내에 포함되어 있지 않은지 확인하세요.

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

확인하라는 메시지가 표시됩니다. 잠시 후 `True` 응답에서 **myDemoResourceGroup** 이 성공적으로 삭제되었는지 확인합니다.
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 데이터 디스크 만들기, 연결 및 초기화
> * 디스크의 상태 확인
> * 디스크 초기화
> * 디스크 확장 및 업그레이드
> * 디스크 분리 및 삭제

VM 구성을 자동화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요

> [!div class="nextstepaction"]
> [VM 구성 자동화](./tutorial-automate-vm-deployment.md)
