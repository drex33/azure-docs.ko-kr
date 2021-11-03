---
title: Azure에서 관리형 이미지 만들기
description: Azure에서 일반화된 VM 또는 VHD의 관리 이미지를 만듭니다. 이미지를 사용하여 관리 디스크를 사용하는 여러 VM을 만들 수 있습니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.collection: windows
ms.openlocfilehash: 95f57b01f2c9e6bffd0cfc1f1d563605e320d6ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437014"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure에서 일반화된 VM의 관리 이미지 만들기

**적용 대상:** :heavy_check_mark: Windows VM 


스토리지 계정에 관리 디스크 또는 비관리 디스크로 저장되는 일반화된 VM(가상 머신)에서 관리 이미지 리소스를 만들 수 있습니다. 여러 VM을 만드는 데 이미지를 사용할 수 있습니다. 관리형 이미지의 청구 방법에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 

관리형 이미지 하나는 최대 20개의 동시 배포를 지원합니다. 동일한 관리형 이미지에서 20개가 넘는 VM을 동시에 만들려고 하면 단일 VHD의 스토리지 성능 제한으로 인해 프로비저닝 시간이 초과될 수 있습니다. 20 개 이상의 Vm을 동시에 만들려면 20 개의 동시 VM 배포 마다 1 개의 복제본으로 구성 된 [Azure 계산 갤러리](../shared-image-galleries.md) (이전의 공유 이미지 갤러리) 이미지를 사용 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이미지를 생성하려면 [일반화](../generalize.md)된 VM이 필요합니다.

## <a name="create-a-managed-image-in-the-portal"></a>포털에서 관리 이미지 만들기 

1. VM 이미지를 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 목록에서 VM을 선택합니다.

3. VM의 **가상 머신** 페이지 위쪽 메뉴에서 **캡처** 를 선택합니다.

   **이미지 만들기** 페이지가 나타납니다.

4. **이름** 에서 미리 입력된 이름을 적용하거나 이미지에 사용할 이름을 입력합니다.

5. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 이름을 입력하거나, 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다.

6. 이미지가 만들어진 후 원본 VM을 삭제하려면 **이미지를 만든 후 이 가상 머신을 자동으로 삭제** 를 선택합니다.

7. 모든 [가용성 영역](../../availability-zones/az-overview.md)에서 이미지를 사용하려면 **영역 복원력** 에서 **설정** 을 선택합니다.

8. **만들기** 를 선택하여 이미지를 만듭니다.

생성된 이미지는 리소스 그룹의 리소스 목록에 **이미지** 리소스로 표시됩니다.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Powershell을 사용하여 VM 이미지 만들기

 

VM에서 직접 이미지를 만들면 OS 디스크와 데이터 디스크를 포함하여 VM에 연결된 모든 디스크가 이미지에 포함됩니다. 이 예제에서는 VM에서 관리되는 디스크를 사용하는 관리되는 이미지를 만드는 방법을 보여 줍니다.

시작하기 전에 Azure PowerShell 모듈이 최신 버전인지 확인합니다. 버전을 확인하려면 PowerShell에서 `Get-Module -ListAvailable Az`를 실행합니다. 버전을 업그레이드해야 하는 경우 [PowerShellGet을 사용하여 Windows에서 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결합니다.


> [!NOTE]
> 이미지를 영역 중복 스토리지에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 이미지 구성에 `-ZoneResilient` 매개 변수(`New-AzImageConfig` 명령)를 포함하는 지역에 이미지를 만들어야 합니다.

VM 이미지를 만들려면 다음 단계를 수행합니다.

1. 일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM의 할당이 취소되었는지 확인합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 가상 머신의 상태를 **일반화됨** 으로 설정합니다. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 가상 머신을 가져옵니다. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell을 사용하여 관리되는 디스크에서 이미지 만들기

OS 디스크의 이미지만 만들려면 관리 디스크 ID를 OS 디스크로 지정합니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 관리되는 디스크의 ID를 가져옵니다.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Powershell을 사용하여 스냅샷에서 이미지 만들기

다음 단계를 수행하면 일반화된 VM의 스냅샷에서 관리되는 이미지를 만들 수 있습니다.

    
1. 일부 변수를 만듭니다. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 스냅샷을 가져옵니다.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 이미지 구성을 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 이미지를 만듭니다.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>스토리지 계정을 사용하는 VM에서 이미지 만들기

관리 디스크를 사용하지 않는 VM에서 관리형 이미지를 만들려면 스토리지 계정에 있는 OS VHD의 URI가 필요합니다. 이 URI는 https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd* 형식입니다. 이 예제의 VHD는 *vhdcontainer* 컨테이너의 *mystorageaccount* 에 있으며 VHD 파일 이름은 *vhdfilename.vhd* 입니다.


1.  일부 변수를 만듭니다.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM을 중지하고 할당을 취소합니다.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM을 일반화됨으로 표시합니다.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  일반화된 OS VHD를 사용하여 이미지를 만듭니다.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>다음 단계
- [관리되는 이미지에서 VM 만들기](create-vm-generalized-managed.md)를 수행합니다. 
