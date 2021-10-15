---
title: 가상 머신을 용량 예약 그룹에 연결(미리 보기)
description: 신규 또는 기존 가상 머신을 용량 예약 그룹에 연결하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 8bfb5811cd8c4ffe2efa10a0bb8fcac8b449092e
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065047"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>VM을 용량 예약 그룹에 연결(미리 보기) 

이 문서에서는 신규 또는 기존 가상 머신을 용량 예약 그룹에 연결하는 단계를 안내합니다. 용량 예약에 대한 자세한 내용은 [개요 문서](capacity-reservation-overview.md)를 참조하세요. 

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="associate-a-new-vm"></a>새 VM 연결

새 VM을 용량 예약 그룹에 연결하려면 명시적으로 그룹을 가상 머신의 속성으로 참조해야 합니다. 이 참조는 그룹에서 일치하는 예약을 사용하지 않으려는 덜 중요한 애플리케이션 및 워크로드가 우발적으로 사용하지 않도록 보호합니다.  

### <a name="api"></a>[API](#tab/api1)

VM에 `capacityReservationGroup` 속성을 추가하려면 *Microsoft.Compute* 공급자에 대한 다음 PUT 요청을 구성합니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

요청 본문에 아래와 같이 `capacityReservationGroup` 속성을 포함합니다.

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[포털](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure Portal](https://portal.azure.com) 열기
1. 검색 창에 **가상 머신** 을 입력합니다.
1. *서비스* 에서 **가상 머신** 을 선택합니다.
1. *가상 머신* 페이지에서 **만들기** 를 선택한 다음 **가상 머신** 을 선택합니다.
1. *기본* 탭의 *프로젝트 세부 정보* 에서 올바른 **구독** 을 선택한 다음 새 **리소스 그룹** 을 만들거나 기존 리소스 그룹을 사용하도록 선택합니다.
1. *인스턴스 세부 정보* 에서 가상 머신 **이름** 을 입력하고 **지역** 을 선택합니다.
1. **이미지** 및 **VM 크기** 를 선택합니다.
1. *관리 계정* 에서 **사용자 이름** 과 **암호** 를 입력합니다.
    1. 암호는 12자 이상이어야 하며 정의된 복잡성 요구 사항을 충족해야 합니다.
1. *인바운드 포트 규칙* 에서 **선택한 포트 허용** 을 선택한 다음, 드롭다운에서 **RDP**(3389) 및 **HTTP**(80)를 선택합니다.
1. *고급 섹션* 으로 이동합니다.
1. **용량 예약** 드롭다운에서 VM을 연결할 용량 예약 그룹을 선택합니다.
1. **검토 + 만들기** 단추를 선택합니다. 
1. 유효성 검사가 실행된 후 **만들기** 단추를 선택합니다. 
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="cli"></a>[CLI](#tab/cli1)

`az vm create`을 사용하여 새 VM을 만들고 `capacity-reservation-group` 속성을 추가하여 기존 용량 예약 그룹에 연결합니다. 아래 예에서는 미국 동부 위치에 Standard_D2s_v3 VM을 만들고 VM을 용량 예약 그룹에 연결합니다.

```azurecli-interactive
az vm create 
--resource-group myResourceGroup 
--name myVM 
--location eastus 
--size Standard_D2s_v3 
--image UbuntuLTS 
--capacity-reservation-group /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

`New-AzVM`을 사용하여 새 VM을 만들고 `CapacityReservationGroupId` 속성을 추가하여 기존 용량 예약 그룹에 연결합니다. 아래 예에서는 미국 동부 위치에 Standard_D2s_v3 VM을 만들고 VM을 용량 예약 그룹에 연결합니다.

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

자세히 알아보려면 Azure PowerShell 명령 [New-AzVM](/powershell/module/az.compute/new-azvm)으로 이동합니다.

### <a name="arm-template"></a>[ARM 템플릿](#tab/arm1)

 [ARM 템플릿](../azure-resource-manager/templates/overview.md)은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 선언적 구문을 사용합니다. 선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다. 

ARM 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 단일 템플릿에서 용량 예약 그룹 및 용량 예약을 만들 수 있습니다. Azure Portal, Azure CLI 또는 Azure PowerShell을 통하거나 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 배포할 수 있습니다. 

환경이 필수 조건을 충족하고 ARM 템플릿 사용에 익숙하다면 이 [용량 예약이 있는 VM 만들기](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json) 템플릿을 사용합니다. 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>기존 VM 연결 

용량 예약이 미리 보기 상태인 동안 기존 VM을 용량 예약 그룹에 연결하려면 먼저 VM 할당을 해제한 다음 다시 할당할 때 연결해야 합니다. 이 프로세스는 VM이 예약의 빈 스폿 중 하나를 사용하도록 합니다. 

### <a name="api"></a>[API](#tab/api2)

1. VM 할당을 취소합니다. 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. VM에 `capacityReservationGroup` 속성을 추가합니다. *Microsoft.Compute* 공급자에 다음 PUT 요청을 구성합니다.

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    요청 본문에 `capacityReservationGroup` 속성을 포함합니다. 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[포털](#tab/portal2)

1. [Azure Portal](https://portal.azure.com) 열기
1. 가상 머신으로 이동합니다.
1. 왼쪽에서 **개요** 를 선택합니다.
1. 페이지 상단에서 **중지** 를 선택하여 VM 할당을 취소합니다. 
1. 왼쪽의 **구성** 으로 이동합니다.
1. **용량 예약 그룹** 드롭다운에서 VM을 연결할 그룹을 선택합니다. 

### <a name="cli"></a>[CLI](#tab/cli2)

1. VM 할당을 취소합니다.

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

1. VM을 용량 예약 그룹에 연결합니다.

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. VM 할당을 취소합니다.

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. VM을 용량 예약 그룹에 연결합니다.

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

자세히 알아보려면 Azure PowerShell 명령 [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) 및 [Update-AzVM](/powershell/module/az.compute/update-azvm)으로 이동합니다.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>인스턴스 보기와 VM 연결 보기 

`capacityReservationGroup` 속성이 설정되면 이제 VM과 그룹 간에 연결이 존재합니다. Azure는 그룹에서 일치하는 용량 예약을 자동으로 찾고 예약된 슬롯을 사용합니다. 용량 예약 *인스턴스 보기* 는 아래와 같이 `virtualMachinesAllocated` 속성의 새 VM을 반영합니다. 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 

### <a name="cli"></a>[CLI](#tab/cli3)

```azurecli-interactive
az capacity reservation show 
-g myResourceGroup
-c myCapacityReservationGroup 
-n myCapacityReservation 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

자세히 알아보려면 Azure PowerShell 명령 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)으로 이동합니다.


### <a name="portal"></a>[포털](#tab/portal3)

1. [Azure Portal](https://portal.azure.com) 열기
1. 용량 예약 그룹으로 이동
1. 왼쪽의 **설정** 에서 **리소스** 를 선택합니다.
1. 용량 예약 그룹과 연결된 모든 VM을 보려면 표를 확인합니다.  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약 그룹에 대한 VM 연결 제거](capacity-reservation-remove-vm.md)
