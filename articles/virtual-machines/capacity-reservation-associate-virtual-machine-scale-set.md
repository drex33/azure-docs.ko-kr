---
title: 가상 머신 확장 집합을 용량 예약 그룹에 연결(미리 보기)
description: 신규 또는 기존 가상 머신 확장 집합을 용량 예약 그룹에 연결하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: e2563f7addb773b256a56dc67b2ec892b7231372
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532638"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>가상 머신 확장 집합을 용량 예약 그룹에 연결(미리 보기)

가상 머신 확장 집합에는 두 가지 모드가 있습니다. 

- **균일한 오케스트레이션 모드:** 이 모드에서 가상 머신 확장 집합은 VM 프로필 또는 템플릿을 사용하여 원하는 용량으로 스케일 업합니다. 개별 VM 인스턴스를 관리하거나 사용자 지정하는 기능이 있지만 Uniform은 동일한 VM 인스턴스를 사용합니다. 이러한 인스턴스는 가상 머신 확장 집합 VM API를 통해 노출되며 표준 Azure IaaS VM API 명령과 호환되지 않습니다. 확장 집합은 모든 실제 VM 작업을 수행하므로 예약은 가상 머신 확장 집합과 직접 연결됩니다. 확장 집합이 예약과 연결되면 모든 후속 VM 할당이 예약에 대해 수행됩니다. 
- **유연한 오케스트레이션 모드:** 이 모드에서는 확장 집합 인터페이스를 사용하지 않고 표준 Azure IaaS VM API를 사용할 수 있으므로 개별 가상 머신 확장 집합 VM 인스턴스를 보다 유연하게 관리할 수 있습니다. 이 모드는 공개 미리 보기 중에 용량 예약을 사용하지 않습니다.

이러한 모드에 대해 자세히 알아보려면 [가상 머신 확장 집합 오케스트레이션 모드](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)로 이동합니다. 이 문서의 나머지 부분에서는 균일한 가상 머신 확장 집합을 용량 예약 그룹에 연결하는 방법에 대해 설명합니다. 

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>균일한 오케스트레이션에서 확장 집합의 제한 사항 

- 균일한 오케스트레이션의 가상 머신 확장 집합이 용량 예약과 호환되도록 하려면 `singlePlacementGroup` 속성을 *False* 로 설정해야 합니다. 
- 다중 영역의 균일한 확장 집합에 대한 **정적 고정 분산** 가용성 옵션은 용량 예약에서 지원되지 않습니다. 이 옵션은 5개의 장애 도메인을 사용해야 하지만 예약은 범용 크기에 대해 최대 3개의 장애 도메인만 지원합니다. 권장되는 방식은 각 영역 내에서 가능한 한 많은 FD에 VM을 분산시키는 **최대 분산** 옵션을 사용하는 것입니다. 필요한 경우 3개 이하의 사용자 지정 장애 도메인을 구성합니다. 

용량 예약을 사용하는 동안 몇 가지 다른 제한 사항이 있습니다. 전체 목록은 [용량 예약 개요](capacity-reservation-overview.md)를 참조하세요.  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>새 가상 머신 확장 집합을 용량 예약 그룹에 연결


### <a name="api"></a>[API](#tab/api1)  

새 균일한 가상 머신 확장 집합을 용량 예약 그룹에 연결하려면 *Microsoft.Compute* 공급자에 대한 다음 PUT 요청을 구성합니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

아래와 같이 `virtualMachineProfile`에 `capacityReservationGroup` 속성을 추가합니다. 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

`New-AzVmss`을 사용하여 새 가상 머신 확장 집합을 만들고 `CapacityReservationGroupId` 속성을 추가하여 확장 집합을 기존 용량 예약 그룹에 연결합니다. 아래 예에서는 미국 동부 위치에서 Standard_Ds1_v2 VM에 대한 균일한 확장 집합을 만들고 확장 집합을 용량 예약 그룹에 연결합니다.

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

자세히 알아보려면 Azure PowerShell 명령 [New-AzVmss](/powershell/module/az.compute/new-azvmss)로 이동합니다.

### <a name="arm-template"></a>[ARM 템플릿](#tab/arm1)

 [ARM 템플릿](../azure-resource-manager/templates/overview.md)은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 선언적 구문을 사용합니다. 선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다. 

ARM 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 단일 템플릿에서 용량 예약 그룹 및 용량 예약을 만들 수 있습니다. Azure Portal, Azure CLI 또는 Azure PowerShell을 통하거나 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 배포할 수 있습니다. 

환경이 필수 조건을 충족하고 ARM 템플릿 사용에 익숙하다면 이 [용량 예약으로 가상 머신 확장 집합 만들기](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json) 템플릿을 사용합니다. 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>기존 가상 머신 확장 집합을 용량 예약 그룹에 연결 

공개 미리 보기의 경우 기존 균일한 가상 머신 확장 집합을 용량 예약 그룹에 연결하려면 먼저 확장 집합의 할당을 취소한 다음 다시 할당할 때 연결해야 합니다. 이렇게 하면 모든 확장 집합 VM이 다시 할당할 때 용량 예약을 사용합니다.

### <a name="important-notes-on-upgrade-policies"></a>업그레이드 정책에 대한 중요 참고 사항 

- **자동 업그레이드** – 이 모드에서 확장 집합 VM 인스턴스는 추가 작업 없이 용량 예약 그룹과 자동으로 연결됩니다. 확장 집합 VM이 다시 할당되면 예약된 용량을 사용하기 시작합니다.
- **롤링 업그레이드** – 이 모드에서 확장 집합 VM 인스턴스는 추가 작업 없이 용량 예약 그룹과 연결됩니다. 그러나 일괄 처리 사이에 선택적 일시 중지 시간을 두어 일괄적으로 업데이트됩니다. 확장 집합 VM이 다시 할당되면 예약된 용량을 사용하기 시작합니다.
- **수동 업그레이드** – 이 모드에서는 가상 머신 확장 집합이 용량 예약 그룹에 연결될 때 확장 집합 VM 인스턴스에 아무 일도 일어나지 않습니다. [최신 확장 집합 모델로 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)하여 각 확장 집합 VM에 대해 개별 업데이트를 수행해야 합니다.

### <a name="api"></a>[API](#tab/api2)

1. 가상 머신 확장 집합 할당을 해제합니다. 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. 확장 집합 모델에 `capacityReservationGroup` 속성을 추가합니다. *Microsoft.Compute* 공급자에 다음 PUT 요청을 구성합니다.

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    요청 본문에 `capacityReservationGroup` 속성을 포함합니다.

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. 가상 머신 확장 집합 할당을 해제합니다. 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. 확장 집합을 용량 예약 그룹에 연결합니다. 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

자세히 알아보려면 Azure PowerShell 명령 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 및 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>인스턴스 보기와 가상 머신 확장 집합 연결 보기 

균일한 가상 머신 확장 집합이 용량 예약 그룹과 연결되면 모든 후속 VM 할당이 용량 예약에 대해 발생합니다. Azure는 그룹에서 일치하는 용량 예약을 자동으로 찾고 예약된 슬롯을 사용합니다. 

### <a name="api"></a>[API](#tab/api3) 

용량 예약 그룹 *인스턴스 보기* 는 아래와 같이 `virtualMachinesAssociated` & `virtualMachinesAllocated` 속성 아래의 새 확장 집합 VM을 반영합니다. 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

PowerShell을 사용하여 인스턴스 보기와 가상 머신 확장 집합 및 용량 예약 그룹 연결을 봅니다. 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

자세히 알아보려면 Azure PowerShell 명령 [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup)으로 이동합니다.

### <a name="portal"></a>[포털](#tab/portal3)

1. [Azure Portal](https://portal.azure.com) 열기
1. 용량 예약 그룹으로 이동
1. 왼쪽의 **설정** 에서 **리소스** 를 선택합니다.
1. 테이블에서 용량 예약 그룹과 연결된 모든 확장 집합 VM을 볼 수 있습니다.
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>지역 및 가용성 영역 고려 사항 

가상 머신 확장 집합은 지역적으로 만들거나 하나 이상의 가용성 영역에 만들어 데이터 센터 수준의 장애로부터 보호할 수 있습니다. 다중 영역 가상 머신 확장 집합에 대한 자세한 내용은 [가용성 영역을 사용하는 가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)을 참조하세요.  

 
>[!IMPORTANT]
> 연결이 성공하려면 가상 머신 확장 집합의 위치(지역 및 가용성 영역)와 용량 예약 그룹이 일치해야 합니다. 지역 확장 집합의 경우 지역은 확장 집합과 용량 예약 그룹 간에 일치해야 합니다. 영역 확장 집합의 경우 지역과 영역 모두 확장 집합과 용량 예약 그룹 간에 일치해야 합니다. 


확장 집합이 여러 영역에 분산되어 있으면 항상 포함된 가용성 영역에 균등하게 배포하려고 시도합니다. 이렇게 균등한 배포로 인해 용량 예약 그룹은 항상 각 영역에서 예약된 VM의 수량이 동일해야 합니다. 이 배포가 중요한 이유를 설명하는 아래의 예를 참조하세요.   

이 예에서 각 영역에는 예약된 다른 수량이 있습니다. 가상 머신 확장 집합이 75개의 인스턴스로 스케일 아웃된다고 가정해 보겠습니다. 확장 집합은 항상 영역 간에 균등하게 배포하려고 시도하므로 VM 배포는 다음과 같아야 합니다. 

| 영역  | 예약 수량  | 아니요. 각 영역의 확장 집합 VM 수  | 사용되지 않는 예약 수량  | 초과 할당   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

이 경우 확장 집합은 영역 1에서 15개의 사용되지 않는 인스턴스에 대해 추가 비용을 발생시킵니다. 또한 스케일 아웃은 용량 예약으로 보호되지 않는 영역 2의 VM 5개와 영역 3의 VM 10개에 따라 다릅니다. 각 영역에 25개의 용량 인스턴스가 예약되어 있는 경우 75개의 VM 모두 용량 예약으로 보호되며 배포 시 사용되지 않은 인스턴스에 대한 추가 비용이 발생하지 않습니다.  

예약이 초과 할당될 수 있으므로 확장 집합은 예약 한도를 초과하여 정상적으로 계속 확장할 수 있습니다. 유일한 차이점은 예약된 수량 이상으로 할당된 VM에는 용량 예약 SLA가 적용되지 않는다는 것입니다. 자세한 내용을 보려면 [용량 예약 초과 할당](capacity-reservation-overallocate.md)으로 이동합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약에서 확장 집합 연결을 제거하는 방법 알아보기](capacity-reservation-remove-virtual-machine-scale-set.md)
