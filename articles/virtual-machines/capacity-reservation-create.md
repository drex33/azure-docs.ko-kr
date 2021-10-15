---
title: Azure에서 용량 예약 만들기(미리 보기)
description: 용량 예약을 만들어 Azure 지역 또는 가용성 영역에서 컴퓨팅 용량을 예약하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 51139277b0c8da4fb3dfdd61bf826305657280b5
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065018"
---
# <a name="create-a-capacity-reservation-preview"></a>용량 예약 만들기(미리 보기)

용량 예약은 항상 용량 예약 그룹의 일부로 만들어집니다. 첫 번째 단계는 적합한 그룹이 아직 없는 경우 그룹을 만든 다음 예약을 만드는 것입니다. 성공적으로 만들어지면 가상 머신에서 즉시 예약을 사용할 수 있습니다. 예약이 삭제되지 않는 한 용량은 사용을 위해 예약됩니다.     

용량 예약 그룹에 대한 올바른 형식의 요청은 용량을 예약하지 않으므로 항상 성공해야 합니다. 이는 단지 예약을 위해 컨테이너 역할을 수행합니다. 그러나 VM 시리즈에 필요한 할당량이 없거나 Azure에 요청을 수행하기에 충분한 용량이 없는 경우 용량 예약 요청이 실패할 수 있습니다. 더 많은 할당량을 요청하거나 다른 VM 크기, 위치 또는 영역 조합을 사용해보세요. 

용량 예약 만들기가 완전히 성공하거나 실패합니다. 10개의 인스턴스 예약 요청의 경우 10개를 모두 할당할 수 있는 경우에만 성공이 반환됩니다. 그렇지 않으면 용량 예약 만들기가 실패합니다. 

> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="considerations"></a>고려 사항

용량 예약은 다음 규칙을 충족해야 합니다. 
- 위치 매개 변수는 상위 용량 예약 그룹의 위치 속성과 일치해야 합니다. 일치하지 않으면 오류가 발생합니다. 
- VM 크기는 대상 지역에서 사용할 수 있어야 합니다. 그렇지 않으면 예약 만들기가 실패합니다. 
- 구독에는 VM 시리즈 및 전체 지역에 대해 예약된 VM 수량 이상의 승인된 할당량이 충분히 있어야 합니다. 필요한 경우 [추가 할당량을 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다.
- 각 용량 예약 그룹은 지정된 VM 크기에 대해 정확히 하나의 예약을 가질 수 있습니다. 예를 들어 VM 크기 `Standard_D2s_v3`에 대해 하나의 용량 예약만 만들 수 있습니다. 동일한 용량 예약 그룹에서 `Standard_D2s_v3`에 대한 두 번째 예약을 만들려고 하면 오류가 발생합니다. 그러나 `Standard_D4s_v3`, `Standard_D8s_v3` 등과 같은 다른 VM 크기에 대해 동일한 그룹에 다른 예약을 만들 수 있습니다.  
- 영역을 지원하는 용량 예약 그룹의 경우 각 예약 유형은 **VM 크기** 와 **영역** 의 조합으로 정의됩니다. 예를 들어, `Zone 1`의 `Standard_D2s_v3`에 대한 하나의 용량 예약, `Zone 2`의 `Standard_D2s_v3`에 대한 또 다른 용량 예약, `Zone 3`의 `Standard_D2s_v3`에 대한 세 번째 용량 예약이 지원됩니다.


## <a name="create-a-capacity-reservation"></a>용량 예약 만들기 

### <a name="api"></a>[API](#tab/api1)

1. 용량 예약 그룹 만들기 

    용량 예약 그룹을 만들려면 *Microsoft.Compute* 공급자에서 다음 PUT 요청을 구성합니다. 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    요청 본문에 다음을 포함합니다. 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    이 그룹은 미국 동부 위치에 대한 예약을 포함하도록 만들었습니다. 
    
    이 예에서 그룹은 만들어질 때 영역이 지정되지 않았기 때문에 지역 예약만 지원합니다. 영역 그룹을 만들려면 아래와 같이 요청 본문에 추가 매개 변수 *zones* 를 전달합니다. 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. 용량 예약 만들기 

    예약을 만들려면 *Microsoft.Compute* 공급자에서 다음 PUT 요청을 구성합니다. 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    요청 본문에 다음을 포함합니다. 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    위의 요청은 D2s_v3 VM 크기의 5개 수량에 대해 미국 동부 위치에 예약을 만듭니다. 


### <a name="portal"></a>[포털](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. [Azure Portal](https://portal.azure.com) 열기
1. 검색 창에 **용량 예약 그룹** 을 입력합니다.
1. 옵션에서 **용량 예약 그룹** 을 선택합니다.
1. **만들기** 를 선택합니다.
1. *기본* 탭에서 용량 예약 그룹을 만듭니다.
    1. **구독** 선택
    1. **리소스 그룹** 선택 또는 만들기
    1. 그룹 **이름 지정** 
    1. **지역** 선택 
    1. 선택적으로 **가용성 영역** 을 선택하거나 영역을 지정하지 않도록 선택하고 Azure에서 자동으로 선택하도록 허용합니다.
1. **다음** 을 선택합니다.
1. *예약* 탭에서 용량 예약을 하나 이상 만듭니다.
    1. 각 예약에 **예약 이름**, VM **인스턴스** 수량을 지정하고 고유한 **VM 크기** 를 선택합니다.
    1. *비용/월* 열에는 선택 항목에 따른 결제 정보가 표시됩니다.
1. **다음** 을 선택합니다.
1. *태그* 탭에서 선택적으로 태그를 만듭니다.
1. **다음** 을 선택합니다. 
1. *검토 + 만들기* 탭에서 용량 예약 그룹 정보를 검토합니다.
1. **만들기** 를 선택합니다.


### <a name="cli"></a>[CLI](#tab/cli1)

1. 용량 예약을 만들기 전에 `az group create`을 사용하여 리소스 그룹을 만듭니다. 다음 예에서는 미국 동부 위치에 리소스 그룹 *myResourceGroup* 을 만듭니다.

    ```azurecli-interactive
    az group create 
    -l eastus 
    -g myResourceGroup
    ```

1. 이제 `az capacity reservation group create`을 사용하여 용량 예약 그룹을 만듭니다. 다음 예에서는 3개의 가용성 영역 모두에 대해 미국 동부 위치에 *myCapacityReservationGroup* 그룹을 만듭니다.

    ```azurecli-interactive
    az capacity reservation group create 
    -n myCapacityReservationGroup 
    -l eastus 
    -g myResourceGroup 
    --zones 1 2 3 
    ```

1. 용량 예약 그룹이 만들어지면 `az capacity reservation create`을 사용하여 새 용량 예약을 만듭니다. 다음 예에서는 미국 동부 위치의 영역 1에 Standard_D2s_v3 VM 크기의 5개 수량에 대한 *myCapacityReservation* 을 만듭니다.

    ```azurecli-interactive
    az capacity reservation create 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    -l eastus 
    -g myResourceGroup 
    --sku Standard_D2s_v3 
    --capacity 5 
    --zone 1
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. 용량 예약을 만들기 전에 `New-AzResourceGroup`을 사용하여 리소스 그룹을 만듭니다. 다음 예에서는 미국 동부 위치에 리소스 그룹 *myResourceGroup* 을 만듭니다.

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. 이제 `New-AzCapacityReservationGroup`을 사용하여 용량 예약 그룹을 만듭니다. 다음 예에서는 3개의 가용성 영역 모두에 대해 미국 동부 위치에 *myCapacityReservationGroup* 그룹을 만듭니다.

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. 용량 예약 그룹이 만들어지면 `New-AzCapacityReservation`을 사용하여 새 용량 예약을 만듭니다. 다음 예에서는 미국 동부 위치의 영역 1에 Standard_D2s_v3 VM 크기의 5개 수량에 대한 *myCapacityReservation* 을 만듭니다.

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

자세히 알아보려면 Azure PowerShell 명령 [New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup), [New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup) 및 [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation)으로 이동합니다.


### <a name="arm-template"></a>[ARM 템플릿](#tab/arm1)

 [ARM 템플릿](../azure-resource-manager/templates/overview.md)은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 선언적 구문을 사용합니다. 선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다. 

ARM 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 단일 템플릿에서 용량 예약 그룹 및 용량 예약을 만들 수 있습니다. Azure Portal, Azure CLI 또는 Azure PowerShell을 통하거나 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 배포할 수 있습니다. 

환경이 필수 조건을 충족하고 ARM 템플릿 사용에 익숙하다면 다음 템플릿 중 하나를 사용합니다. 

- [영역 용량 예약 만들기](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [용량 예약으로 VM 만들기](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [용량 예약으로 가상 머신 확장 집합 만들기](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>용량 예약 확인 

성공적으로 만들어지면 VM에서 즉시 용량 예약을 사용할 수 있습니다. 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="cli"></a>[CLI](#tab/cli2)

 ```azurecli-interactive
 az capacity reservation show 
 -c myCapacityReservationGroup 
 -n myCapacityReservation 
 -g myResourceGroup
 ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

용량 예약 확인:

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

VM 크기와 예약 수량을 찾으려면 다음을 사용합니다. 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

자세히 알아보려면 Azure PowerShell 명령 [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation)으로 이동합니다.

### <a name="portal"></a>[포털](#tab/portal3)

1. [Azure Portal](https://portal.azure.com) 열기
1. 검색 창에 **용량 예약 그룹** 을 입력합니다.
1. 옵션에서 **용량 예약 그룹** 을 선택합니다.
1. 목록에서 방금 만든 용량 예약 그룹 이름을 선택합니다.
1. 왼쪽에서 **개요** 를 선택합니다.
1. **예약** 을 선택합니다.
1. 이 보기에서 VM 크기 및 예약 수량과 함께 그룹의 모든 예약을 볼 수 있습니다.
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 예약 수정 방법 알아보기](capacity-reservation-modify.md)
