---
title: 가상 머신 크기 유연성 - Azure Reserved VM Instances
description: 예약 할인은 예약 VM 인스턴스를 구입할 때에 어떤 크기 계열을 적용하는지 알아봅니다.
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.openlocfilehash: 8d76bdbf17fbc03b9c99a2c08c56de1faf2ffea9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215437"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Reserved VM Instances에서 가상 머신 크기 유연성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

예약 VM 인스턴스를 구매하는 경우 인스턴스 크기 유연성 또는 용량 우선 순위를 최적화하도록 선택할 수 있습니다. 예약 VM 인스턴스의 최적화 설정을 지정하거나 변경하는 방법에 대한 자세한 내용은 [예약 VM 인스턴스의 최적화 설정 변경](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)을 참조하세요.

인스턴스 크기 유연성에 최적화된 예약 가상 머신 인스턴스에서 사용자가 구입한 예약은 동일한 인스턴스 크기 유연성 그룹에서 VM(가상 머신) 크기에 적용할 수 있습니다. 예를 들어 Standard_DS3_v2와 같은 DSv2 시리즈 표에 나열된 VM 크기에 대한 예약을 구입하는 경우 예약 할인은 동일한 인스턴스 크기 유연성 그룹에 나와 있는 다른 크기에 적용할 수 있습니다.

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

하지만 해당 예약 할인이 다른 인스턴스 크기 유연성 그룹에 나열된 VM 크기에 적용되지 않습니다(예: DSv2 시리즈 대용량 메모리의 SKU: Standard_DS11_v2, Standard_DS12_v2 등).

인스턴스 크기 유연성 그룹 내에서 예약 할인을 적용하는 VM 수는 예약을 구입하는 경우 선택한 VM 크기에 따라 달라집니다. 또한 실행하는 VM의 크기에 따라서도 달라집니다. 이 비율 열은 해당 인스턴스 크기 유연성 그룹의 각 VM 크기에 대한 상대적 공간을 비교합니다. 예약 할인을 실행하고 있는 VM에 적용하는 방법을 계산하기 위해 비율 값을 사용합니다.

## <a name="examples"></a>예

다음 예제에서는 DSv2 시리즈 테이블의 크기 및 비율을 사용합니다.

크기의 비율 또는 해당 계열에서 다른 크기에 비해 상대적 공간은 8 Standard_DS4_v2 예약 VM 인스턴스를 구입 합니다.

- 시나리오 1: 1의 비율로 8대의 Standard_DS1_v2 크기 VM을 실행합니다. 예약 할인은 8대의 모든 해당 VM에 적용됩니다.
- 시나리오 2: 각각 2의 비율로 2대의 Standard_DS2_v2 크기 VM을 실행합니다. 또한 4의 비율로 Standard_DS3_v2 크기 VM을 실행합니다. 총 공간은 2+2+4=8입니다. 따라서 예약 할인은 3대의 모든 해당 VM에 적용됩니다.
- 시나리오 3: 16의 비율로 한 대의 Standard_DS5_v2를 실행합니다. 예약 할인은 해당 VM의 컴퓨팅 비용 절반에 적용됩니다.
- 시나리오 4: 비율이 16인 Standard_DS5_v2를 하나 실행하고 비율이 8인 Standard_DS4_v2 예약을 추가로 구매합니다. 두 예약이 결합되어 전체 VM에 할인을 적용합니다.

다음 섹션에서는 인스턴스 크기 유연성에 최적화된 예약 VM 인스턴스를 구매하는 경우 동일한 크기 시리즈 그룹에 있는 크기를 보여줍니다.

## <a name="instance-size-flexibility-ratio-for-vms"></a>VM의 인스턴스 크기 유연성 비율 

아래 CSV에는 인스턴스 크기 유연성 그룹, ArmSkuName 및 비율이 있습니다.  

[인스턴스 크기 유연성 비율](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Azure는 파일을 프로그래밍 방식으로 사용할 수 있도록 링크와 스키마를 업데이트된 상태로 유지합니다.

## <a name="view-vm-size-recommendations"></a>VM 크기 권장 사항 보기

Azure는 구매 환경에서 권장되는 VM 크기를 보여줍니다. 가장 작은 크기 권장 사항을 보려면 **Group by smallest size(가장 작은 크기로 그룹화)** 를 선택합니다.

:::image type="content" source="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" alt-text="권장 수량을 보여 주는 스크린샷" lightbox="./media/reserved-vm-instance-size-flexibility/select-product-recommended-quantity.png" :::

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Reservations란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)을 참조하세요.
