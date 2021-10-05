---
title: 용량 예약 그룹에서 가상 머신 확장 집합 연결 제거(미리 보기)
description: 용량 예약 그룹에서 가상 머신 확장 집합을 제거하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 03b89b1b8c0221795f58ff28addd4fdeaad5053e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532581"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>용량 예약 그룹에서 가상 머신 확장 집합 연결 제거 

이 문서에서는 용량 예약 그룹에서 가상 머신 확장 집합 연결을 제거하는 단계를 안내합니다. 용량 예약에 대한 자세한 내용은 [개요 문서](capacity-reservation-overview.md)를 참조하세요. 

VM과 기본 용량 예약은 모두 논리적으로 용량을 차지하므로 Azure에서는 이 프로세스에 몇 가지 제약 조건을 적용하여 모호한 할당 상태와 예기치 않은 오류를 방지합니다.  

연결을 변경하는 방법은 다음 두 가지가 있습니다. 
- 옵션 1: 가상 머신 확장 집합의 할당을 취소하고, 확장 집합 수준에서 용량 예약 그룹 속성을 변경한 다음, 기본 VM을 업데이트합니다.
- 옵션 2: 예약된 수량을 0으로 업데이트한 다음 용량 예약 그룹 속성을 변경합니다.

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deallocate-the-virtual-machine-scale-set"></a>가상 머신 확장 집합 할당 취소

첫 번째 옵션은 가상 머신 확장 집합의 할당을 취소하고, 확장 집합 수준에서 용량 예약 그룹 속성을 변경한 다음, 기본 VM을 업데이트하는 것입니다. 

자동, 롤링 및 수동 업그레이드에 대한 자세한 내용을 보려면 [업그레이드 정책](#upgrade-policies)으로 이동합니다. 

### <a name="api"></a>[API](#tab/api1)

1. 가상 머신 확장 집합 할당 해제

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. 용량 예약 그룹과의 연결을 제거하도록 가상 머신 확장 집합 업데이트
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    요청 본문에서 `capacityReservationGroup` 속성을 null로 설정하여 그룹에 대한 가상 머신 확장 집합 연결을 제거합니다.

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 가상 머신 확장 집합 할당을 취소합니다. 다음 작업은 확장 집합 내의 모든 가상 머신 할당을 취소합니다. 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. 용량 예약 그룹과의 연결을 제거하도록 확장 집합을 업데이트합니다. 속성을 `CapacityReservationGroupId` null로 설정해도 확장 집합이 용량 예약 그룹에 연결됩니다. 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

자세히 알아보려면 Azure PowerShell 명령 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 및 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>예약된 수량을 0으로 업데이트 

두 번째 옵션에는 예약된 수량을 0으로 업데이트한 다음 용량 예약 그룹 속성을 변경하는 작업이 포함됩니다.

이 옵션은 가상 머신 확장 집합의 할당을 취소할 수 없는 경우와 예약이 더 이상 필요하지 않은 경우에 효과적입니다. 예를 들어 대규모 배포 중에 용량을 일시적으로 보장하기 위해 용량 예약을 만들 수 있습니다. 완료되면 더 이상 예약이 필요하지 않습니다. 

자동, 롤링 및 수동 업그레이드에 대한 자세한 내용을 보려면 [업그레이드 정책](#upgrade-policies)으로 이동합니다. 

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

1. 용량 예약 그룹과의 연결을 제거하도록 가상 머신 확장 집합 업데이트

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    요청 본문에서 `capacityReservationGroup` 속성을 null로 설정하여 연결을 제거합니다.
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
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

2. 속성을 null로 설정하여 용량 예약 그룹과의 연결을 제거하도록 확장 집합을 업데이트합니다. `CapacityReservationGroupId` 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

자세히 알아보려면 Azure PowerShell 명령 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 및 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>업그레이드 정책

- **자동 업그레이드** – 이 모드에서 확장 집합 VM 인스턴스는 추가 작업 없이 용량 예약 그룹과 자동으로 분리됩니다.
- **롤링 업그레이드** – 이 모드에서 확장 집합 VM 인스턴스는 추가 작업 없이 용량 예약 그룹과 분리됩니다. 그러나 둘 사이의 일시 중지 시간(선택 사항)을 통해 일괄로 업데이트됩니다.
- **수동 업그레이드** – 이 모드에서는 가상 머신 확장 집합이 업데이트될 때 확장 집합 VM 인스턴스에는 아무 작업도 발생하지 않습니다. [최신 확장 집합 모델로 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)하여 각 확장 집합 VM을 개별적으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약 전체 할당에 대해 알아보기](capacity-reservation-overallocate.md)
