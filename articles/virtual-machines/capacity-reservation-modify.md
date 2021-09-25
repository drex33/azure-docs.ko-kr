---
title: Azure에서 용량 예약 수정(미리 보기)
description: 용량 예약을 수정하는 방법에 대해 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 8b6e2ba3c65b5fd521bdb6326069ce5d8be05599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564888"
---
# <a name="modify-a-capacity-reservation-preview"></a>용량 예약 수정(미리 보기)

용량 예약 그룹 및 용량 예약을 만든 후 예약을 수정하는 것이 좋습니다. 이 문서에서는 API, Azure Portal, PowerShell을 사용하여 다음을 수행하는 방법을 설명합니다. 

> [!div class="checklist"]
> * 용량 예약에 예약된 인스턴스 수 업데이트 
> * 용량 예약 그룹과 연결된 VM 크기 조정
> * 용량 예약 그룹 및 용량 예약 삭제

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="update-the-number-of-instances-reserved"></a>예약된 인스턴스 수 업데이트 

용량 예약에 예약된 가상 머신 인스턴스 수를 업데이트합니다.  

> [!IMPORTANT]
> 드문 경우지만 Azure에서 기존 용량 예약을 위해 예약된 수량을 늘리기 위한 요청을 수행할 수 없는 경우, 예약이 *실패* 상태로 전환 될 수 있으며 [수량이 원래 양만큼 복원](#restore-instance-quantity)될 때까지 사용할 수 없게 됩니다.

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
요청 본문에서 `capacity` 속성을 예약 하려는 새 카운트로 업데이트합니다. 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

본 예시에서는 `capacity` 속성이 5로 설정되어 있습니다.


### <a name="portal"></a>[포털](#tab/portal1) 

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 용량 예약 그룹으로 이동합니다
1. **개요** 를 선택합니다 
1. **예약** 을 선택합니다 
1. 맨 위에서 **예약 관리** 를 선택합니다 
1. *예약 관리* 페이지에서 **인스턴스** 필드에 예약할 새 수량을 입력합니다 
1. **저장** 을 선택합니다. 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

예약된 수량을 업데이트하려면 업데이트된 `capacityToReserve`속성과 함께 `New-AzCapacityReservation`을 사용합니다.

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

자세한 내용은 Azure PowerShell 명령 [Update-AzCapacityReservation으로](/powershell/module/az.compute/update-azcapacityreservation)이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>용량 예약 그룹과 연결된 VM 크기 조정 

크기를 조정하는 가상 머신이 현재 용량 예약 그룹에 연결되어 있고, 해당 그룹에 대상 크기에 대한 예약이 없는 경우, 크기를 조정하기 전에 해당 크기에 대한 새 예약을 만들거나 예약 그룹에서 가상 머신을 제거합니다. 

대상 크기가 예약 그룹의 일부인지 확인합니다. 

### <a name="api"></a>[API](#tab/api2)

1. 그룹 내 모든 용량 예약의 이름을 가져옵니다.
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. 각 예약에 예약된 VM 크기를 확인합니다. 다음 예는 `capacityReservationName1`에 대한 것이지만, 다른 예약에 대해 이 단계를 반복할 수 있습니다.

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. 다음을 살펴보세요. 
    1. 대상 VM 크기가 그룹의 일부가 아니면, 대상 VM에 대한 [새 용량 예약을 생성](capacity-reservation-create.md)합니다 
    1. 대상 VM 크기가 이미 그룹에 있는 경우, [가상 머신의 크기를 조정](resize-vm.md)합니다 

### <a name="portal"></a>[포털](#tab/portal2)

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 용량 예약 그룹으로 이동합니다
1. **개요** 를 선택합니다 
1. **예약** 을 선택합니다 
1. 각 예약에 예약된 *VM 크기* 를 확인합니다 
    1. 대상 VM 크기가 그룹의 일부가 아니면, 대상 VM에 대한 [새 용량 예약을 생성](capacity-reservation-create.md)합니다 
    1. 대상 VM 크기가 이미 그룹에 있는 경우, [가상 머신의 크기를 조정](resize-vm.md)합니다 

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. `Get-AzCapacityReservationGroup`을 사용하여 그룹 내 모든 용량 예약의 이름을 가져옵니다

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. 응답에서 모든 용량 예약의 이름을 찾습니다

1. 다음 명령을 실행하여 각 예약에 예약된 VM 크기를 확인합니다

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. 다음을 살펴보세요. 
    1. 대상 VM 크기가 그룹의 일부가 아니면, 대상 VM에 대한 [새 용량 예약을 생성](capacity-reservation-create.md)합니다 
    1. 대상 VM 크기가 이미 그룹에 있는 경우, [가상 머신의 크기를 조정](resize-vm.md)합니다 


자세히 알아보려면, Azure PowerShell 명령 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup) 및 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)을 참조하세요.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>용량 예약 그룹 및 용량 예약 삭제 

모든 구성원 용량 예약이 삭제되고 그룹에 연결된 가상 머신이 없는 경우, Azure에서 용량 예약 그룹을 삭제할 수 있습니다.  

용량 예약을 삭제하려면, 먼저 연결된 모든 가상 머신을 찾습니다. 가상 머신의 목록은 `virtualMachinesAssociated` 속성에서 사용할 수 있습니다. 

### <a name="api"></a>[API](#tab/api3)

먼저 용량 예약 그룹과 연결된 모든 가상 머신을 찾아 분리합니다.
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
}  
```
위의 응답에서 `virtualMachinesAssociated` 속성 아래에 있는 모든 가상 머신의 이름을 찾고, [용량 예약에 대한 VM 연결 제거](capacity-reservation-remove-vm.md) 단계를 사용하여 용량 예약 그룹에서 제거합니다. 

모든 가상 머신이 용량 예약 그룹에서 제거되면 구성원 용량 예약을 삭제합니다.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

마지막으로, 부모 용량 예약 그룹을 삭제합니다.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[포털](#tab/portal3)

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 용량 예약 그룹으로 이동합니다
1. **리소스** 를 선택합니다 
1. 그룹과 연결된 모든 가상 머신을 찾습니다
1. [모든 가상 머신을 분리합니다](capacity-reservation-remove-vm.md)
1. 그룹의 모든 용량 예약을 삭제합니다
    1. **예약** 으로 이동합니다
    1. 각 예약을 선택합니다 
    1. **삭제** 선택
1. 용량 예약 그룹을 삭제합니다
    1. 용량 예약 그룹으로 이동합니다
    1. 페이지 위쪽에서 **삭제** 를 선택합니다


### <a name="powershell"></a>[PowerShell](#tab/powershell3)

용량 예약 그룹과 연결된 모든 가상 머신을 찾아 분리합니다.

1. 다음을 실행합니다. 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. 위의 응답에서 `VirtualMachinesAssociated` 속성 아래에 있는 모든 가상 머신의 이름을 확인하고, [용량 예약 그룹에서 가상 머신 연결 제거](capacity-reservation-remove-vm.md)에 설명된 단계를 사용하여 용량 예약 그룹에서 제거합니다.

1. 모든 가상 머신이 그룹에서 제거되면 다음 단계를 진행합니다. 

1. 용량 예약을 삭제합니다.

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. 용량 예약 그룹을 삭제합니다.

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

자세히 알아보려면, Azure PowerShell 명령 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup), [Remove-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation), [Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup)을 참조하세요.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>인스턴스 수량 복원 

예약된 수량을 줄이기 위한 올바른 형식의 요청은 예약에 연결된 가상 머신 수에 관계 없이 항상 성공해야 합니다. 그러나 예약된 수량을 늘리려면 Azure에서 추가 용량 요청을 처리하기 위해 추가 할당량을 요구할 수 있습니다. 드문 경우지만 Azure에서 기존 예약을 위해 예약된 수량을 늘리기 위한 요청을 수행할 수 없는 경우, 예약이 *실패* 상태로 전환 될 수 있으며 예약된 수량이 원래 양만큼 복원될 때까지 사용할 수 없게 됩니다.  

> [!NOTE]
> 예약이 *실패* 상태이면 예약과 연결된 모든 VM이 정상적으로 계속 작동합니다.  

예를 들어, `myCapacityReservation`에 예약된 수량 5개가 있다고 가정해 보겠습니다. 5개의 추가 인스턴스를 요청하여 총 수량을 10으로 예약합니다. 그러나 지역의 제한된 용량 상황 때문에 Azure는 요청된 추가 5개 수량을 충족할 수 없습니다. 이 경우, `myCapacityReservation`은 예약된 10개 수량의 의도된 상태를 충족하지 못하고 *실패* 상태로 전환됩니다. 

이 오류를 해결하려면, 다음 단계를 수행하여 예약된 이전 수량 값을 찾습니다.  

1. Azure 포털에서 [애플리케이션 변경 분석](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade)으로 이동합니다 
1. 필터에서 **구독**, **리소스 그룹**, **시간 범위** 를 선택합니다
    - **시간 범위** 필터에서 지난 14일까지만 백업할 수 있습니다 
1. 용량 예약의 이름을 검색합니다
1. 해당 예약에 대한 `sku.capacity` 속성의 변경을 찾습니다 
    - 예약된 이전 수량은 **이전 값** 열 아래의 값이 됩니다 

예약된 이전 수량으로 `myCapacityReservation`을 업데이트합니다. 업데이트된 후에는 가상 머신에 대한 예약을 즉시 사용할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약에서 VM을 제거하는 방법을 알아보기](capacity-reservation-remove-vm.md)
