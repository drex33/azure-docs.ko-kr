---
title: Azure에서 용량 예약 초과 할당(미리 보기)
description: 용량 예약과 관련하여 초과 할당이 어떻게 작동하는지 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 0486263551246a794f90867621be0e87d42747c6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273406"
---
# <a name="overallocating-capacity-reservation-preview"></a>용량 예약 초과 할당(미리 보기)

Azure는 예약된 용량 한도를 관리하는 오버헤드 없이 버스트 및 기타 스케일 아웃 시나리오를 지원하기 위해 예약된 용량 예약 수를 초과하는 추가 VM의 연결을 허용합니다. 유일한 차이점은 예약된 수량을 초과하는 VM 수가 용량 가용성 SLA 혜택을 받지 않는다는 것입니다. Azure에 가상 머신 요구 사항을 충족하는 사용 가능한 용량이 있는 한 추가 할당은 성공합니다. 

용량 예약 그룹의 인스턴스 보기는 각 구성원 용량 예약에 대한 사용량의 스냅샷을 제공합니다. 인스턴스 보기를 사용하여 초과 할당 작동 방식을 확인할 수 있습니다. 

이 문서에서는 그룹에 연결된 용량 예약 그룹(`myCapacityReservationGroup`), 구성원 용량 예약(`myCapacityReservation`) 및 가상 머신(*myVM1*)을 만들었다고 가정합니다. 자세한 정보는 [용량 예약 만들기](capacity-reservation-create.md) 및 [용량 예약에 VM 연결](capacity-reservation-associate-vm.md)로 이동합니다.

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 

## <a name="register-for-capacity-reservation"></a>용량 예약 등록 

용량 예약 기능을 사용하려면 먼저 [미리 보기에 대한 구독을 등록](capacity-reservation-overview.md#register-for-capacity-reservation)해야 합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. Azure CLI 또는 PowerShell을 사용하여 기능 등록을 완료할 수 있습니다.

## <a name="instance-view-for-capacity-reservation-group"></a>용량 예약 그룹에 대한 인스턴스 보기 

용량 예약 그룹에 대한 인스턴스 보기는 다음과 같습니다. 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
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

*myVM2* 라는 이름의 다른 가상 머신을 만들고 위의 용량 예약 그룹과 연결한다고 가정합니다. 

용량 예약 그룹에 대한 인스턴스 보기는 이제 다음과 같습니다. 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
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

`virtualMachinesAllocated`(2)의 길이가 `capacity`(1)보다 큰 것을 알 수 있습니다. 이 유효한 상태를 *초과 할당* 이라고 합니다. 

> [!IMPORTANT]
> Azure는 용량 예약이 완전히 사용되었다고 해서 할당을 중지하지 않습니다. Azure에 사용 가능한 용량이 있는 한 자동 크기 조정 규칙, 임시 스케일 아웃 및 관련 요구 사항은 예약된 용량의 수량을 초과하여 작동합니다.  


## <a name="states-and-considerations"></a>상태 및 고려 사항  

지정된 용량 예약에는 세 가지 유효한 상태가 있습니다. 

| 시스템 상태  | 상태  | 고려 사항  |
|---|---|---|
| 사용 가능한 예약된 용량  | `virtualMachinesAllocated` < `capacity`의 길이  | 예약된 용량이 모두 필요하십니까? 필요에 따라 용량을 줄여 비용을 절감합니다.  |
| 사용된 예약  | `virtualMachinesAllocated` == `capacity`의 길이  | 일부 기존 VM의 할당이 취소되지 않으면 추가 VM은 용량 SLA를 받지 않습니다. 필요에 따라 용량을 늘려 계획된 추가 VM이 SLA를 받도록 합니다.  |
| 예약 초과 할당  | `virtualMachinesAllocated` > `capacity`의 길이  | 추가 VM은 용량 SLA를 받지 않습니다. 또한 할당이 취소된 경우 VM 수량(길이 `virtualMachinesAllocated` – `capacity`)은 용량 SLA를 받지 않습니다. 필요에 따라 용량을 늘려 더 많은 기존 VM에 용량 SLA를 추가합니다.  |


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약에서 VM을 제거하는 방법을 알아보기](capacity-reservation-remove-vm.md)