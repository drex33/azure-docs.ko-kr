---
title: Azure에서 Windows VM 리소스 이동
description: Resource Manager 배포 모델에서 다른 Azure 구독 또는 리소스 그룹으로 Windows VM을 이동합니다.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 855045625467963d03e2aca57317b51ff20cc1aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697915"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>다른 Azure 구독 또는 리소스 그룹으로 Windows VM 이동

**적용 대상:** :heavy_check_mark: Windows VM

이 문서에서는 리소스 그룹 또는 구독 간에 Windows VM(가상 머신)을 이동하는 방법을 안내합니다. 개인 구독에서 원래 VM을 만들고 회사 구독으로 이동한 후 작업을 계속하려는 경우에 구독 간의 이동이 편리할 수 있습니다. VM을 이동하기 위해 VM을 중지할 필요가 없으며 이동 중에도 VM이 계속 실행되어야 합니다.

> [!IMPORTANT]
>새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동한 후에는 새 리소스 ID를 사용하도록 도구와 스크립트를 업데이트해야 합니다.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell을 사용하여 VM 이동

가상 머신을 다른 리소스 그룹에 이동하려면 모든 종속 리소스를 이동하는지 확인해야 합니다. 이러한 리소스 각각에 대한 리소스 ID의 목록을 가져오려면 [Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet을 사용합니다.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

이전 명령의 출력을 사용하여 [Move-AzResource](/powershell/module/az.resources/move-azresource)에 대해 쉼표로 구분된 리소스 ID 목록을 만들어 각 리소스를 대상으로 이동할 수 있습니다.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

리소스를 다른 구독으로 이동하기 위해 **-DestinationSubscriptionId** 매개 변수를 포함합니다.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


지정된 리소스를 이동할지 확인하는 메시지가 표시되면 **Y** 를 입력하여 확인합니다.

## <a name="next-steps"></a>다음 단계
리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.    
