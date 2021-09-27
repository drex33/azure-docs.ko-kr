---
title: 사용할 수 없는 Azure 예약 유형 문제 해결
description: 이 문서는 Azure Portal에서 사용할 수 없는 것으로 표시되는 예약 인스턴스를 이해하고 문제를 해결하는 데 도움이 됩니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: primittal
ms.topic: troubleshooting
ms.date: 09/20/2021
ms.openlocfilehash: 51d30767c3e084ade041d22bac8a246aa5d1f502
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631137"
---
# <a name="troubleshoot-reservation-type-not-available"></a>사용할 수 없는 예약 유형 문제 해결

이 문서는 Azure Portal에서 사용할 수 없는 것으로 표시되는 예약 인스턴스를 이해하고 문제를 해결하는 데 도움이 됩니다.

## <a name="symptoms"></a>증상

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, **예약** 으로 이동합니다.
2. **+ 추가**, 제품을 차례로 선택합니다.
3. **모든 제품** 탭을 선택합니다.
4. 제품 목록에서 하나를 선택합니다. 다음 메시지 중 하나가 표시될 수 있습니다.
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="선택한 구독 또는 지역 메시지에 사용할 수 없는 제품을 보여 주는 예" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="코어 할당량 부족 메시지를 보여 주는 예" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>원인

일부 예약을 구매할 수 없는 이유는 다음과 같습니다.

- 일부 예약 인스턴스 제품은 모든 지역에서 구매할 수 없습니다.
- 구독에 할당량 제한이 있습니다.

### <a name="cause-1"></a>원인 1

일부 예약 인스턴스 제품은 구매할 수 없습니다. Azure는 고객에게 금액 할인을 제공하는 것과 관련된 비용을 기준으로 일부 제품을 허용할지 여부를 결정합니다. 다른 경우에는 Azure에서 특정 데이터 센터의 용량 조건에 따라 일부 제품을 제공하지 않습니다. 또한 일부 Azure 제품 개발 그룹은 이전 제품을 사용 중지하려고 하므로 이제는 특정 제품을 허용할 수도 있습니다.

다른 경우 Azure는 일부 지역에서 이러한 리소스에 대한 수요에 따라 다양한 제품에 용량 제한을 적용합니다. 예를 들어 데이터 센터에서 특정 VM 크기에 대한 수요가 부족할 때 이러한 제한이 적용될 수 있습니다. 이 예의 경우 Azure는 해당 지역에서 해당 크기에 대한 예약을 구매한 고객에게 용량을 보장할 수 없습니다. 마지막으로 다양한 이유로 고유한 몇 가지 제품이 있습니다. 이러한 제품은 선택된 소수의 고객 집합에서만 사용할 수 있습니다.

일부 예약은 구매할 수 없지만 Azure Portal에 표시되는 이유는 무엇인가요? 이는 사용자가 원하는 제품을 찾을 수 없다는 지원 요청을 만들기 때문입니다. Azure 예약 개발 팀은 `Product unavailable` 메시지와 함께 모든 제품을 표시하는 경우 표시하지 않는 것보다 지원 요청이 더 적다는 것을 확인했습니다.

### <a name="cause-2"></a>원인 2

구독에 할당량 제한이 있습니다. 구독에는 사용할 수 있는 CPU 코어 수에 대한 제한이 있습니다. 이 제한은 일부 예약 인스턴스 제품, 특히 가상 머신에 적용됩니다. Azure는 예약 인스턴스를 구매하는 사용자가 사용할 수 있도록 보장하려고 합니다. Azure는 Azure Portal에서 간단한 형식적인 검사를 통해 VM을 구독에 배포하고 예약 구매 혜택을 받을 수 있을 만큼 사용 가능한 코어가 충분히 있는지 확인합니다.

특정 제품을 카트에 추가하고 예약을 구매할 수 있도록 허용하는 검사는 간단합니다. Azure는 구독에 사용할 수 있는 총 CPU 코어 수를 평가하고, 해당 수가 선택한 항목의 코어 수보다 큰지 여부를 확인합니다.

Azure는 **공유** 범위 또는 **관리 그룹** 범위 예약 된 인스턴스에 대 한 할당량을 확인 하지 않습니다. 공유 범위에 대한 예약 인스턴스 혜택은 등록의 모든 구독에 적용됩니다. 관리 그룹 범위에 대 한 예약 인스턴스 혜택은 관리 그룹 및 청구 범위의 일부인 모든 구독에 적용 됩니다. Azure는 모든 구독에서 리소스를 배포할 수 있을 만큼 사용 가능한 코어가 충분히 있는지 확인할 수 없습니다. 할당량에 관계 없이, Azure는 선택한 범위가 공유 또는 관리 그룹으로 설정 된 경우 항상 VM 크기를 선택할 수 있습니다.

또한 Azure는 **추천** 구매에 대한 할당량 검사를 수행하지 않습니다. 추천 사항은 활성 사용량을 기준으로 합니다. 추천 사항을 만드는 데 필요한 사용량을 이미 생성했으므로 Azure는 특정 VM 크기를 실행할 수 있을 만큼 코어가 충분히 있다고 가정합니다.

## <a name="solution"></a>해결 방법

받은 오류 메시지에 따라 다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

### <a name="solution-1"></a>해결 방법 1

_사용할 수 없는 제품_ 메시지가 표시되면 오류 메시지에서 **고객 지원 문의** 링크를 선택하여 구독에 대한 예외를 추가하도록 요청합니다. 일부 예외는 허용되지 않습니다.

### <a name="solution-2"></a>해결 방법 2

_코어 할당량 부족_ 메시지가 표시되면 범위를 **공유** 로 변경할 수 있습니다. 예약을 구매한 후에는 예약 범위를 **공유** 에서 **단일** 로 변경할 수 있습니다.

또는 오류 메시지에서 **할당량 증가 요청** 링크를 선택하여 구독에 대한 추가 CPU 코어 할당량을 요청합니다.

## <a name="next-steps"></a>다음 단계

- 예약 범위 옵션에 대한 자세한 내용은 [예약 범위 지정](prepare-buy-reservation.md#scope-reservations)을 참조하세요.