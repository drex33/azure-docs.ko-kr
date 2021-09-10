---
title: Windows VM에서 데이터 디스크 분리 - Azure
description: Resource Manager 배포 모델을 사용하여 Azure의 가상 머신에서 데이터 디스크를 분리합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49b58a46b4abc65089970982c26424a2f615bad4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693419"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

가상 머신에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 머신에서 디스크가 제거되지만, 스토리지에서는 제거되지 않습니다.

> [!WARNING]
> 디스크를 분리해도 자동으로 삭제되지 않습니다. Premium Storage를 구독하는 경우 디스크에 대한 스토리지 요금이 계속 부과됩니다. 자세한 내용은 [Premium Storage 사용 시 가격 책정 및 청구](../disks-types.md#billing)를 참조하세요.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 머신에 다시 연결할 수 있습니다.

 

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell을 사용하여 데이터 디스크 분리

PowerShell을 사용하여 데이터 디스크를 *작동 중* 제거(hot remove)할 수 있지만, VM에서 해당 디스크를 분리할 때 해당 디스크가 전혀 사용되고 있지 않아야 합니다.

이 예제에서는 **myResourceGroup** 리소스 그룹의 VM **myVM** 에서 **myDisk** 라는 디스크를 제거합니다. 먼저 [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) cmdlet을 사용하여 디스크를 제거합니다. 그런 다음, [Update-AzVM](/powershell/module/az.compute/update-azvm) cmdlet으로 가상 머신의 상태를 업데이트하여 데이터 디스크 제거 프로세스를 완료합니다.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.

## <a name="detach-a-data-disk-using-the-portal"></a>포털을 사용하여 데이터 디스크 분리

데이터 디스크를 *작동 중* 제거(hot remove)할 수 있지만, VM에서 해당 디스크를 분리할 때 해당 디스크가 전혀 사용되고 있지 않아야 합니다.

1. 왼쪽 메뉴에서 **Virtual Machines** 을 선택합니다.
1. 분리할 데이터 디스크가 있는 가상 머신을 선택합니다.
1. **설정** 아래에서 **디스크** 를 선택합니다.
1. 분리하려면 **디스크** 창에서 분리할 데이터 디스크의 맨 오른쪽에 있는 **X** 단추를 선택합니다.
1. 페이지 맨 위에 있는 **저장** 을 선택하여 변경 내용을 저장합니다.

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다. 디스크가 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](attach-managed-disk-portal.md)할 수 있습니다.

디스크를 삭제하여 더 이상 스토리지 비용이 발생하지 않도록 하려면 [연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제 - Azure Portal](../disks-find-unattached-portal.md)을 참조하세요.
