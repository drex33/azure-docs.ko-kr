---
title: 용량 예약 그룹에서 가상 머신 연결 제거(미리 보기)
description: 용량 예약 그룹에서 가상 머신을 제거하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: f0c84e4b44218aa4f7659376251d1931ffc9516b
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063624"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>용량 예약 그룹에서 VM 연결 제거(미리 보기)

이 문서에서는 용량 예약 그룹에 대한 VM 연결을 제거하는 단계를 안내합니다. 용량 예약에 대한 자세한 내용은 [개요 문서](capacity-reservation-overview.md)를 참조하세요. 

VM과 기본 용량 예약은 모두 논리적으로 용량을 차지하므로 Azure에서는 이 프로세스에 몇 가지 제약 조건을 적용하여 모호한 할당 상태와 예기치 않은 오류를 방지합니다.  

연결을 변경하는 방법은 다음 두 가지가 있습니다. 
- 옵션 1: 가상 머신의 할당을 취소하고, 용량 예약 그룹 속성을 변경하고, 필요에 따라 가상 머신을 다시 시작합니다.
- 옵션 2: 예약된 수량을 0으로 업데이트한 다음 용량 예약 그룹 속성을 변경합니다.

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deallocate-the-vm"></a>VM 할당을 취소합니다.

첫 번째 옵션은 가상 머신의 할당을 취소하고, 용량 예약 그룹 속성을 변경하고, 필요에 따라 VM을 다시 시작하는 것입니다. 

### <a name="api"></a>[API](#tab/api1)

1. VM 할당을 취소합니다.

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. 용량 예약 그룹과의 연결을 제거하도록 VM을 업데이트합니다.
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    요청 본문에서 `capacityReservationGroup` 속성을 null로 설정하여 그룹에 대한 VM 연결을 제거합니다.

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    }
    ```

### <a name="portal"></a>[포털](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure Portal](https://portal.azure.com) 열기
1. 가상 머신으로 이동하여 **개요** 를 선택합니다.
1. **중지** 를 선택합니다. 
    1. 상태가 *중지됨(할당 취소됨)* 으로 변경되면 VM이 할당 취소됨을 알 수 있습니다.
    1. 프로세스의 이 시점에서는 아직 VM이 용량 예약 그룹에 연결되어 있으며, 이는 용량 예약의 `virtualMachinesAssociated` 속성에 반영됩니다. 
1. **구성** 을 선택합니다.
1. **용량 예약 그룹** 값을 *없음* 으로 설정합니다.
    - VM이 더 이상 용량 예약 그룹에 연결되지 않습니다. 

### <a name="cli"></a>[CLI](#tab/cli1)

1. 가상 머신 할당 취소

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

    상태가 **중지됨(할당 취소됨)** 으로 변경되면 가상 머신이 할당 취소됩니다.

1. 속성을 None으로 설정하여 용량 예약 그룹과의 연결을 `capacity-reservation-group` 제거하도록 VM을 업데이트합니다.

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 가상 머신 할당 취소

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    상태가 **중지됨(할당 취소됨)** 으로 변경되면 가상 머신이 할당 취소됩니다.

1. 속성을 null로 설정하여 용량 예약 그룹과의 연결을 제거하도록 VM을 업데이트합니다. `CapacityReservationGroupId`

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

자세히 알아보려면 Azure PowerShell 명령 [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) 및 [Update-AzVM](/powershell/module/az.compute/update-azvm)으로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>예약된 수량을 0으로 업데이트 

두 번째 옵션에는 예약된 수량을 0으로 업데이트한 다음 용량 예약 그룹 속성을 변경하는 작업이 포함됩니다.

이 옵션은 가상 머신의 할당을 취소할 수 없는 경우와 예약이 더 이상 필요하지 않은 경우에 효과적입니다. 예를 들어 대규모 배포 중에 용량을 일시적으로 보장하기 위해 용량 예약을 만들 수 있습니다. 완료되면 더 이상 예약이 필요하지 않습니다. 

### <a name="api"></a>[API](#tab/api2)

1. 예약된 수량을 0으로 업데이트 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    요청 본문에 다음을 포함합니다.
    
    ```json
    {
    "sku":
        {
        "capacity": 0
        }
    }
    ```
    
    `capacity` 속성이 0 이상으로 설정되어 있습니다.

1. 용량 예약 그룹과의 연결을 제거하도록 VM을 업데이트합니다.

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    요청 본문에서 `capacityReservationGroup` 속성을 null로 설정하여 연결을 제거합니다.
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":null
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[포털](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. [Azure Portal](https://portal.azure.com) 열기
1. 용량 예약 그룹으로 이동하고 **개요** 를 선택합니다.
1. **예약** 을 선택합니다. 
1. 페이지 맨 위에서 **예약 관리** 를 선택합니다. 
1. *예약 관리* 블레이드에서 다음을 수행합니다.
    1. **인스턴스** 필드에 `0`을 입력합니다.
    1. **저장** 을 선택합니다. 
1. 가상 머신으로 이동하여 **구성** 을 선택합니다.
1. **용량 예약 그룹** 값을 *없음* 으로 설정합니다.
    - VM이 더 이상 용량 예약 그룹에 연결되지 않습니다.

### <a name="cli"></a>[CLI](#tab/cli2)

1. 예약된 수량을 0으로 업데이트

   ```azurecli-interactive
   az capacity reservation update 
   -g myResourceGroup
   -c myCapacityReservationGroup 
   -n myCapacityReservation 
   --capacity 0
   ```

1. 속성을 None으로 설정하여 용량 예약 그룹과의 연결을 `capacity-reservation-group` 제거하도록 VM을 업데이트합니다.

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group None
    ```


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. 예약된 수량을 0으로 업데이트

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

1. 속성을 null로 설정하여 용량 예약 그룹과의 연결을 제거하도록 VM을 업데이트합니다. `CapacityReservationGroupId`

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId $null
    ```

자세히 알아보려면 Azure PowerShell 명령 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVM](/powershell/module/az.compute/get-azvm) 및 [Update-AzVM](/powershell/module/az.compute/update-azvm)으로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [확장 집합을 용량 예약 그룹에 연결하는 방법 알아보기](capacity-reservation-associate-virtual-machine-scale-set.md)
