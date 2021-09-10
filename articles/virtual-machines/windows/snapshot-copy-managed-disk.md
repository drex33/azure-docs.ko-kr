---
title: 포털 또는 PowerShell을 사용하여 가상 하드 드라이브의 스냅샷 만들기
description: 포털 또는 PowerShell을 사용하여 백업 또는 문제 해결을 위해 사용할 Azure VM의 복사본을 만드는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
manager: twooley
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.openlocfilehash: 4a4f3ec63b3998dfb6b8030599af100397a49629
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693800"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>포털 또는 PowerShell을 사용하여 스냅샷 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

스냅샷은 VHD(가상 하드 드라이브)의 전체 읽기 전용 복사본입니다. 백업으로 사용 또는 VM(가상 머신) 문제 해결을 위해 OS 또는 데이터 디스크 VHD의 스냅샷을 만들 수 있습니다.

스냅샷을 사용하여 새 VM을 만들려는 경우 진행 중인 모든 프로세스를 제거하려면 스냅샷을 만들기 전에 완전히 VM을 종료하는 것이 좋습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용 

스냅샷을 만들려면 다음 단계를 완료합니다. 
1.  [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
2. **스냅샷** 을 검색하고 선택합니다.
3. **스냅샷** 창에서 만들기 **만들기** 를 선택합니다. **스냅샷 만들기** 창이 나타납니다.
4. 스냅샷의 **이름** 을 입력합니다.
5. 기존 [리소스 그룹](../../azure-resource-manager/management/overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
6. Azure 데이터 센터 **위치** 를 선택합니다.  
7. **원본 디스크** 에서 스냅샷을 만들 관리 디스크를 선택합니다.
8. 스냅샷 저장에 사용할 **계정 유형** 을 선택합니다. 스냅샷이 고성능 디스크에 저장되어야 하는 경우가 아니면 **Standard_HDD** 를 선택합니다.
9. **만들기** 를 선택합니다.

## <a name="use-powershell"></a>PowerShell 사용

다음 단계에서는 VHD 디스크를 복사하고 스냅샷 구성을 만드는 방법을 보여 줍니다. 그런 다음 [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) cmdlet을 사용하여 디스크의 스냅샷을 만들 수 있습니다. 

 

1. 일부 매개 변수를 설정합니다. 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. 스냅샷 구성을 만듭니다. 이 예제에서 스냅샷은 OS 디스크의 스냅샷입니다.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > 스냅샷을 영역 중복 스토리지에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 `-SkuName Standard_ZRS` 매개 변수를 포함하는 지역에 만듭니다.   
   
4. 스냅샷을 만듭니다.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>다음 단계

스냅샷에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅샷에서 가상 머신을 만듭니다. 자세한 내용은 [PowerShell을 사용하여 스냅샷에서 VM 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot)에 있는 샘플을 참조하세요.