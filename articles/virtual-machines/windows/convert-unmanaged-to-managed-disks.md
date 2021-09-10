---
title: 비관리 디스크에서 관리 디스크로 Windows 가상 컴퓨터 변환
description: Resource Manager 배포 모델에서 PowerShell을 사용하여 비관리 디스크에서 관리 디스크로 Windows VM을 변환하는 방법
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 57f0d5ed33bf662867ce7a4323f5f69d889e6653
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688912"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>비관리 디스크에서 관리 디스크로 Windows 가상 컴퓨터 변환

**적용 대상:** :heavy_check_mark: Windows VM 

비관리 디스크를 사용하는 기존 Windows VM(가상 머신)이 있는 경우 [Azure Managed Disks](../managed-disks-overview.md) 서비스를 통해 관리 디스크를 사용하도록 VM을 변환할 수 있습니다. 이 프로세스는 OS(운영 체제) 디스크와 연결된 데이터 디스크를 변환합니다.


## <a name="before-you-begin"></a>시작하기 전에


* [Managed Disks로 마이그레이션 계획 수립](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토합니다.

* [Managed Disks로의 마이그레이션에 대한 FAQ](/azure/virtual-machines/faq-for-disks#migrate-to-managed-disks)를 검토합니다.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* 원래 VHD와 변환 전 VM에서 사용된 스토리지 계정은 삭제되지 않습니다. 이들 작업은 요금이 계속 청구됩니다. 이러한 아티팩트에 대한 요금이 청구되지 않도록 하려면 변환이 완료되었는지 확인한 후 원래 VHD Blob을 삭제합니다. 연결되지 않은 디스크를 삭제하기 위해 찾아야 하는 경우 [연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제](find-unattached-disks.md) 문서를 참조하세요.


## <a name="convert-single-instance-vms"></a>단일 인스턴스 VM 변환
이 섹션에서는 단일 인스턴스 Azure VM을 비관리 디스크에서 Managed Disks로 변환하는 방법을 설명합니다. VM이 가용성 집합에 있는 경우 다음 섹션을 참조하세요. 

1. [Stop-AzVM](/powershell/module/az.compute/stop-azvm) cmdlet을 사용하여 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다. 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) cmdlet을 사용하여 VM을 관리 디스크로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하여 이전 VM을 변환하고 Virtual Machine을 시작합니다.

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>가용성 집합의 VM 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

1. [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) cmdlet을 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 업데이트합니다.

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   가용성 집합이 있는 지역에 관리되는 장애 도메인은 2개뿐이고 관리되지 않는 장애 도메인은 3개인 경우 이 명령은 "지정된 장애 도메인 수 3은 1~2 범위에 있어야 합니다."와 유사한 오류를 표시합니다. 오류를 해결하려면 다음과 같이 장애 도메인을 2로 업데이트하고 `Sku`를 `Aligned`로 업데이트합니다.

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. 가용성 집합의 VM을 할당 취소하고 변환합니다. 다음 스크립트는 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) cmdlet을 사용하여 각 VM의 할당을 취소하고, [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk)를 사용하여 변환하고, 변환 프로세스의 일부로 각 VM을 자동으로 다시 시작합니다.

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>문제 해결

- 변환하기 전에 모든 VM 확장이 '프로비전 성공' 상태에 있는지 확인합니다. 그렇지 않으면 변환이 오류 코드 409로 실패합니다.
- 변환하는 동안 오류가 발생한 경우 또는 이전 변환에서의 문제로 인해 VM 상태가 실패인 경우 `ConvertTo-AzVMManagedDisk` cmdlet을 다시 실행합니다. 다시 시도만으로 상황이 해결되는 경우가 많습니다.
- Linux VM을 관리 디스크로 변환하는 경우 최신 버전의 Azure Linux 에이전트를 사용하세요. Azure Linux 에이전트 버전 '2.2.0' 이하를 사용하는 작업은 실패할 수 있습니다. 일반화된 VM 또는 클래식 가용성 집합에 속하는 VM에서 변환을 실행하는 것도 지원되지 않습니다.
- "SnapshotCountExceeded" 오류와 함께 변환이 실패하면 일부 스냅샷을 삭제하고 작업을 다시 시도합니다.


## <a name="convert-using-the-azure-portal"></a>Azure Portal을 사용하여 변환

Azure Portal을 사용하여 관리되지 않는 디스크에서 관리 디스크로 변환할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 VM 목록에서 VM을 선택합니다.
3. VM 블레이드의 메뉴에서 **디스크** 를 선택합니다.
4. **디스크** 블레이드 상단에서 **관리 디스크로 마이그레이션** 을 선택합니다.
5. VM이 가용성 집합에 있으면 **관리 디스크로 마이그레이션** 블레이드에 가용성 집합을 먼저 변환해야 한다는 경고가 표시됩니다. 경고에는 클릭하여 가용성 집합을 변환할 수 있는 링크가 있습니다. 가용성 집합이 변환되거나 VM이 가용성 집합에 없는 경우에는 **마이그레이션** 을 클릭하여 디스크를 관리 디스크로 마이그레이션하는 프로세스를 시작합니다.

VM이 중지되고 마이그레이션이 완료된 후 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계

[표준 관리 디스크를 프리미엄으로 변환](convert-disk-storage.md)

[스냅샷](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.
