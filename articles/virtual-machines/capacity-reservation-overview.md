---
title: Azure의 주문형 용량 예약(미리 보기)
description: 용량 예약을 사용하여 Azure 지역 또는 가용성 영역에서 컴퓨팅 용량을 예약하는 방법을 알아봅니다.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: ee14ea525575a49abd4e4026201c3fa39ffa84b9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273411"
---
# <a name="on-demand-capacity-reservation-preview"></a>주문형 용량 예약(미리 보기)

주문형 용량 예약을 사용하면 일정 기간 Azure 지역 또는 가용성 영역에서 컴퓨팅 용량을 예약할 수 있습니다. [예약 인스턴스](https://azure.microsoft.com/pricing/reserved-vm-instances/)와 달리 1년 또는 3년 기간 약정에 등록할 필요가 없습니다. 언제든지 예약을 만들고 삭제하며 예약을 관리할 방법을 완전히 제어할 수 있습니다.  

용량 예약이 만들어지면 즉시 용량을 사용할 수 있으며 예약이 삭제될 때까지 사용자가 단독으로 사용하도록 예약됩니다.  


> [!IMPORTANT]
> 용량 예약은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


용량 예약에는 생성 시 항상 정의되는 다음 몇 가지 기본 속성이 있습니다. 
- **VM 크기** - 각 예약은 하나의 VM 크기에 대한 것입니다. 예: `Standard_D2s_v3`. 
- **위치** - 각 예약은 한 위치(지역)에 대한 것입니다. 해당 위치에 가용성 영역이 있는 경우 예약에서 해당 영역 중 하나를 지정할 수 있습니다. 
- **수량** - 각 예약에는 예약할 인스턴스의 수량이 있습니다. 

이러한 매개 변수는 용량 예약을 만들기 위해 Azure에 용량 요청으로 전달됩니다. 구독에 필요한 할당량이 부족하거나 Azure에 사양에 맞는 사용 가능한 용량이 없는 경우 예약이 배포되지 않습니다. 배포 실패를 방지하려면 더 큰 할당량을 요청하거나 다른 VM 크기, 위치 또는 영역 조합을 지정해 보세요. 

Azure에서 예약 요청을 수락하면 일치하는 구성의 VM에서 사용할 수 있습니다. 용량 예약을 사용하려면 VM에서 예약을 속성 중 하나로 지정해야 합니다. 그렇지 않으면 용량 예약이 미사용 상태로 유지됩니다. 이 디자인의 한 가지 이점은 중요한 워크로드만 예약 대상으로 지정할 수 있고 기타 중요하지 않은 워크로드는 예약된 용량 없이 실행할 수 있다는 것입니다.   

> [!NOTE]
> 용량 예약은 가상 머신에서 사용할 수 있도록 Azure 가용성 SLA와 함께 제공됩니다. 공개 미리 보기 중에는 SLA가 적용되지 않으며 용량 예약을 일반적으로 사용할 수 있을 때 정의됩니다.


## <a name="register-for-capacity-reservation"></a>용량 예약 등록 

용량 예약 기능을 사용하려면 먼저 미리 보기에 대한 구독을 등록해야 합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. Azure CLI 또는 PowerShell을 사용하여 기능 등록을 완료할 수 있습니다.

### <a name="cli"></a>[CLI](#tab/cli1)

1. [az feature register](/cli/azure/feature#az_feature_register)를 사용하여 구독에서 미리 보기를 사용하도록 설정합니다.

    ```azurecli-interactive
    az feature register --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. 기능 등록에는 최대 15분이 걸립니다. 다음 방법으로 등록 상태를 확인합니다.

    ```azurecli-interactive
    az feature show --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. 기능이 구독에 등록되면 변경 내용을 컴퓨팅 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

    ```azurecli-interactive
    az provider register --namespace Microsoft.Compute
    ``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. [Register-AzProviderFeature cmdlet](/powershell/module/az.resources/register-azproviderfeature)을 사용하여 구독에 대한 미리 보기를 사용하도록 설정합니다.

    ```powershell-interactive
    Register-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. 기능 등록에는 최대 15분이 걸립니다. 다음 방법으로 등록 상태를 확인합니다.

    ```powershell-interactive
    Get-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. 기능이 구독에 등록되면 변경 내용을 컴퓨팅 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ``` 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="benefits-of-capacity-reservation"></a>용량 예약의 이점 

- 배포된 용량은 사용할 수 있도록 예약되며 해당 SLA 범위 내에서 항상 사용할 수 있습니다.  
- 언제든지 기간 약정 없이 배포하고 삭제할 수 있습니다. 
- 예약 인스턴스와 자동으로 결합하여 기간 약정 할인을 받을 수 있습니다.  

## <a name="sla-for-capacity-reservation"></a>용량 예약에 대한 SLA 

용량 예약에 대한 SLA는 나중에 이 기능을 일반적으로 사용할 수 있을 때 정의됩니다.  


## <a name="limitations-and-restrictions"></a>제한 사항 

- 용량 예약을 만들려면 가상 머신을 만드는 것과 동일한 방식으로 할당량이 필요합니다. 
- 스폿 VM 및 Azure Dedicated Host 노드는 용량 예약에서 지원되지 않습니다. 
- 다음과 같은 일부 배포 제약 조건은 지원되지 않습니다. 
    - 근접 배치 그룹 
    - 업데이트 도메인 
    - UltraSSD 스토리지  
- 공개 미리 보기 중에서 Av2, B, D, E, F VM 시리즈만 지원됩니다. 
- 공개 미리 보기 중에 지원되는 VM 시리즈의 경우 최대 3개의 FD(장애 도메인)가 지원됩니다. 4개 이상의 FD가 있는 배포는 용량 예약에 대해 배포하지 못합니다. 
- 가용성 집합은 용량 예약에서 지원되지 않습니다. 
- 이 미리 보기 중에서 예약을 만든 구독만 예약을 사용할 수 있습니다. 
- 예약은 유료 Azure 고객만 사용할 수 있습니다. 무료 평가판 및 Azure for Students와 같은 스폰서 계정은 이 기능을 사용할 수 없습니다. 


## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구 

용량 예약은 기본 VM 크기와 동일한 요금으로 가격이 책정됩니다. 예를 들어 D2s_v3 VM 10개에 대한 예약을 만드는 경우 예약이 만들어지는 즉시 예약을 사용하지 않더라도 D2s_v3 VM 10개에 대한 요금이 청구되기 시작합니다.  

그런 다음 D2s_v3 VM을 배포하고 예약을 해당 속성으로 지정하면 용량 예약이 사용됩니다. 사용되면 VM에 대해서만 요금을 지불하며 용량 예약에 대한 추가 비용은 없습니다. 이전에 언급한 용량 예약에 대해 D2s_v3 VM 5개를 배포한다고 가정해 보겠습니다. D2s_v3 VM과 동일한 요금으로 청구되는 D2s_v3 VM 5개 및 사용되지 않은 용량 예약 5개에 대한 청구서가 표시됩니다.    

사용된 용량 예약과 사용되지 않는 용량 예약은 모두 예약 인스턴스 기간 약정 할인을 받을 수 있습니다. 위의 예제에서, 동일한 Azure 지역의 D2s_v3 VM 2개에 대한 예약 인스턴스가 있는 경우 2개의 리소스(VM 또는 사용되지 않은 용량 예약)에 대한 청구는 0이 되고 나머지 8개 리소스(즉, 사용되지 않은 용량 예약 5개와 D2s_v3 VM 3개)에 대해서만 요금을 지불하게 됩니다. 이 경우 기간 약정 할인이라는 용어는 VM 또는 사용되지 않은 용량 예약에 적용될 수 있으며, 둘 다 동일한 PAYG 요금으로 청구됩니다. 


## <a name="work-with-capacity-reservation"></a>용량 예약 작업 

용량 예약은 Azure 지역 또는 가용성 영역의 특정 VM 크기에 대해 만들 수 있습니다. 모든 예약은 용량 예약 그룹의 일부로 생성되고 관리되며, 이를 통해 그룹을 만들어 단일 다층 계층 애플리케이션에서 여러 VM 크기를 관리할 수 있습니다. 각 예약은 하나의 VM 크기에 대한 것이고 그룹에는 VM 크기당 하나의 예약만 포함할 수 있습니다.  

용량 예약을 사용하려면 용량 예약 그룹을 VM 속성 중 하나로 지정합니다. 그룹에 일치하는 예약이 없으면 Azure에서 오류 메시지를 반환합니다. 

예약에 대해 예약된 수량은 용량 속성을 변경하여 초기 배포 후에 조정할 수 있습니다. VM 크기 또는 위치와 같은 용량 예약의 다른 변경은 허용되지 않습니다. 권장되는 접근 방식은 기존 예약을 삭제하고 새 요구 사항을 사용하여 새 예약을 만드는 것입니다. 

용량 예약은 VM 배포 수에 대한 제한을 만들지 않습니다. Azure는 예약에 대해 원하는 만큼의 VM 할당을 지원합니다. 예약 자체에 할당량이 필요하므로 예약된 수량까지 VM 배포에 대한 할당량 확인이 생략됩니다. 예약에 대해 더 많은 VM을 할당하는 경우 할당량 확인이 적용되고 Azure가 추가 용량을 충족합니다. 배포된 후에는 이러한 추가 VM 인스턴스로 인해 예약에 대해 할당된 VM의 수량이 예약된 수량을 초과할 수 있습니다. 이 상태를 초과 할당이라고 합니다. 자세한 내용을 보려면 [용량 예약 초과 할당](capacity-reservation-overallocate.md)으로 이동합니다. 


## <a name="capacity-reservation-lifecycle"></a>용량 예약 수명 주기

예약이 만들어지면 Azure는 지정된 위치에 요청된 용량 인스턴스 수를 따로 설정합니다. 

![용량 예약 이미지 1.](\media\capacity-reservation-overview\capacity-reservation-1.jpg) 

다음 속성을 통해 전체 예약의 상태를 추적합니다.  
- `capacity` = 고객이 예약한 인스턴스의 총 수량입니다. 
- `virtualMachinesAllocated` = 용량 예약에 대해 할당된 VM의 목록이며 용량 사용량을 계산합니다. 이러한 VM은 *실행 중* 또는 *중지됨*(*할당됨*)이거나 *시작* 또는 *중지* 와 같은 전환 상태에 있을 수 있습니다. 이 목록에는 *중지됨*(*할당 취소됨*)이라고 하는 할당 취소됨 상태인 VM이 포함되지 않습니다. 
- `virtualMachinesAssociated` = 용량 예약과 연결된 VM 목록입니다. 이 목록에는 할당 취소됨 상태인 VM을 포함하여 예약을 사용하도록 구성된 모든 VM이 있습니다.  

위의 예제는 `capacity`가 2로, `virutalMachinesAllocated` 및 `virtualMachinesAssociated` 길이가 0으로 시작됩니다.  

그런 다음 용량 예약에 대해 VM이 할당되면 예약된 용량 인스턴스 중 하나를 논리적으로 사용합니다. 

![용량 예약 이미지 2.](\media\capacity-reservation-overview\capacity-reservation-2.jpg) 

이제 용량 예약 상태에서 `capacity`이(가) 2로 표시되고 `virutalMachinesAllocated` 및 `virtualMachinesAssociated` 길이가 1로 표시됩니다.  

VM에 일치하는 속성이 있고 빈 용량 인스턴스가 하나 이상 있으면 용량 예약에 대한 할당이 성공합니다.  

용량 예약에 대해 세 번째 VM이 할당되는 우리 예제를 사용하면 예약이 [초과 할당됨](capacity-reservation-overallocate.md) 상태로 전환됩니다. 이 세 번째 VM에는 사용되지 않은 할당량과 Azure에서 충족한 추가 용량이 필요합니다. 세 번째 VM이 할당되면 이제 용량 예약이 다음과 같이 표시됩니다. 

![용량 예약 이미지 3.](\media\capacity-reservation-overview\capacity-reservation-3.jpg) 

`capacity`는 2이고, `virutalMachinesAllocated` 및 `virtualMachinesAssociated`의 길이는 3입니다. 

이제 애플리케이션이 최소 두 개의 VM으로 규모를 축소한다고 가정합니다. VM 0은 업데이트가 필요하므로 할당 취소에 대해 선택됩니다. 예약이 자동으로 다음 상태로 전환됩니다. 

![용량 예약 이미지 4.](\media\capacity-reservation-overview\capacity-reservation-4.jpg) 

`capacity` 및 `virtualMachinesAllocated`의 길이는 모두 2입니다. 그러나 `virtualMachinesAssociated`의 길이는 여전히 VM 0으로 3이지만 할당 취소된 경우에도 계속 용량 예약과 연결됩니다.  

용량 예약은 명시적으로 삭제될 때까지 존재합니다. 용량 예약을 삭제하려면 첫 번째 단계로 `virtualMachinesAssociated` 속성의 모든 VM을 분리합니다. 연결 취소가 완료되면 용량 예약이 다음과 같이 표시됩니다. 

![용량 예약 이미지 5.](\media\capacity-reservation-overview\capacity-reservation-5.jpg) 

이제 용량 예약 상태에서 `capacity`이(가) 2로 표시되고 `virtualMachinesAssociated` 및 `virtualMachinesAllocated` 길이가 0으로 표시됩니다. 이 상태에서 용량 예약을 삭제할 수 있습니다. 삭제한 후에는 더 이상 예약에 대한 비용을 지불하지 않습니다.  

![용량 예약 이미지 6.](\media\capacity-reservation-overview\capacity-reservation-6.jpg)


## <a name="usage-and-billing"></a>사용량 및 결제 

용량 예약이 비어 있으면 해당 VM 크기 및 위치에 대해 VM 사용량이 보고됩니다. [VM 예약 인스턴스](https://azure.microsoft.com/pricing/reserved-vm-instances/)는 VM이 배포되지 않은 경우에도 용량 예약 사용의 일부 또는 전부를 커버할 수 있습니다. 

### <a name="example"></a>예제

예를 들어, 예약된 수량 2로 용량 예약이 생성되었다고 가정해 보겠습니다. 구독으로는 동일한 크기의 일치하는 예약 VM 인스턴스 하나에 액세스할 수 있습니다. 그 결과 용량 예약에 대한 두 개의 사용 스트림이 만들어지며, 그중 하나에는 예약 인스턴스가 적용됩니다. 

![용량 예약 이미지 7.](\media\capacity-reservation-overview\capacity-reservation-7.jpg)

위의 이미지에서는 사용되지 않은 인스턴스 중 하나에 예약 VM 인스턴스 할인이 적용되고 해당 인스턴스에 대한 비용이 0이 됩니다. 다른 인스턴스의 경우 예약된 VM 크기에 대한 PAYG 요금이 부과됩니다.  

용량 예약에 대해 VM이 할당되면 디스크, 네트워크, 확장 및 기타 요청된 구성 요소와 같은 다른 VM 구성 요소도 할당되어야 합니다. 이 상태에서 VM 사용량에는 할당된 VM 하나와 사용하지 않은 용량 인스턴스 하나가 반영됩니다. 예약 VM 인스턴스는 VM 또는 사용되지 않은 용량 인스턴스의 비용을 0으로 설정합니다. 할당 VM과 연결된 디스크, 네트워킹 및 기타 구성 요소에 대한 기타 요금은 청구서에도 표시됩니다. 

![용량 예약 이미지 8.](\media\capacity-reservation-overview\capacity-reservation-8.jpg)

위의 이미지에서 VM 예약 인스턴스 할인은 VM 0에 적용되어, 디스크 및 네트워킹 등 다른 구성 요소에 대해서만 요금이 부과됩니다. 사용되지 않은 다른 인스턴스는 예약된 VM 크기에 대해 PAYG 요금으로 청구됩니다.


## <a name="frequently-asked-questions"></a>질문과 대답 

- **주문형 용량 예약의 가격은 얼마인가요?**

    주문형 용량 예약의 가격은 예약과 연결된 기본 VM 크기의 가격과 동일합니다. 용량 예약을 사용하는 경우 VM이 프로비전되었는지 여부에 관계없이 종량제 요금으로 선택한 VM 크기에 대한 요금이 청구됩니다.  자세한 내용은 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 및 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) VM 가격 책정 페이지를 참조하세요.

- **주문형 용량 예약의 비용과 최종적으로 VM을 프로비저닝할 때의 실제 VM에 대한 요금으로 두 번 부과되나요?**

    아니요, 주문형 용량 예약에 대해 한 번만 청구됩니다.   

- **RI(예약 가상 머신 인스턴스)를 주문형 용량 예약에 적용하여 비용을 낮출 수 있나요?**

    예, 기존 또는 미래의 RI를 주문형 용량 예약에 적용하여 RI 할인을 받을 수 있습니다. 사용 가능한 RI는 VM에 적용되는 것과 동일한 방식으로 용량 예약에 자동으로 적용됩니다.

- **RI(예약 가상 머신 인스턴스)와 주문형 용량 예약의 차이점은 무엇인가요?**

    RI와 주문형 용량 예약은 모두 Azure VM에 적용됩니다. 그러나 RI는 1년 또는 3년 기간 약정으로 인해 종량제 요금에 비해 VM에 대해 할인된 예약 요금을 제공합니다. 반대로, 주문형 용량 예약은 약정을 요구하지 않습니다. 언제든지 용량 예약을 만들거나 취소할 수 있습니다. 그러나 할인은 적용되지 않으며 용량 예약이 성공적으로 프로비저닝된 후 종량제 요금이 부과됩니다. 용량 우선 순위를 지정하지만 보장하지 않는 RI와 달리, 주문형 용량 예약을 구매하면 Azure에서 VM에 대해 컴퓨팅 용량을 따로 설정하고 SLA 보증을 제공합니다.  

- **주문형 용량 예약의 가장 큰 혜택은 무엇인가요?**

    일반적인 시나리오에는 비즈니스 연속성, 재해 복구 및 중요 업무용 애플리케이션의 스케일 아웃이 포함됩니다.  


## <a name="next-steps"></a>다음 단계

용량 예약을 만들고 Azure 지역 또는 가용성 영역에서 컴퓨팅 용량 예약을 시작합니다. 

> [!div class="nextstepaction"]
> [용량 예약 만들기](capacity-reservation-create.md)