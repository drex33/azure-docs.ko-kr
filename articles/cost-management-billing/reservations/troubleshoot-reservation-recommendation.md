---
title: Azure 예약 추천 사항 문제 해결
description: 이 문서는 Azure Portal에 표시되는 Azure 예약 추천 사항을 이해하고 문제를 해결하는 데 도움이 됩니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 9ab8626a10e729e5bdf070f355fcc480d6dc4f16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646818"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Azure 예약 추천 사항 문제 해결

이 문서는 Azure Portal에 표시되는 Azure 예약 추천 사항을 이해하고 문제를 해결하는 데 도움이 됩니다. 예상과 일치하지 않는 추천 사항이 없거나 이러한 추천 사항이 표시되지 않을 수 있습니다. 예를 들어 특정 VM 구성에 대한 예약 인스턴스가 이미 있을 수 있습니다. 비슷한 VM 구성에 대한 추천 사항이 표시된다고 예상할 수 있습니다.

## <a name="symptoms"></a>증상

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, **예약** 으로 이동합니다.
2. **+ 추가**, 제품을 차례로 선택합니다.
3. **추천** 탭에서 예상과 일치하지 않는 추천 사항이 없거나 이러한 추천 사항이 표시되지 않을 수 있습니다.

## <a name="cause"></a>원인

추천 제품 목록은 제품 예약 비용과 비교하여 범위(공유 또는 단일)에 대한 사용량에 따라 생성됩니다. 추천 엔진에서 절감액을 감지하면 구매할 제품을 추천합니다. 하지만 엔진에서 절감액을 확인하지 못하면 제품을 추천하지 않습니다.

지정된 구성을 사용하여 리소스를 실행하는 경우 해당 구성에 대한 예약을 구매하여 비용을 절감할 수 있다고 보장하지는 않습니다. 예를 들어 간헐적으로 사용할 수도 있습니다. 그렇다면 총 예약 비용에서 예약 수명 기간 동안 비용을 절약하지 못할 수 있습니다.

또한 범위 선택이 추천 사항에 미치는 영향을 이해하는 것이 중요합니다. 범위가 **공유** 로 설정되면 목록의 추천 사항은 Azure에서 청구 구독과 연결된 전체 등록에 대한 절감액을 찾는 예약 인스턴스를 보여 줍니다. 범위가 **단일** 로 설정되면 목록의 추천 사항은 구독에서 실행되는 리소스에만 적용됩니다. 일부 VM 크기는 한 범위에만 추천되고 다른 범위에는 추천되지 않을 수 있습니다. 예를 들어 등록 범위에서 예약을 구매하는 데 드는 비용을 정당화할 수 있을 만큼 충분히 높은 **Standard_B1ls** 의 사용량을 등록 전체에서 집계했을 수 있습니다. 그러나 등록의 단일 구독에는 범위에서 예약 구매 비용을 정당화할 수 있을 만큼 충분한 사용량이 없을 수 있습니다. **공유** 및 **단일** 사이의 범위를 변경하면 다른 추천 사항이 생성될 수 있습니다.

Azure는 확인된 비용 절감액을 기준으로 다른 기간이 아닌 특정 기간에 대한 예약을 구매하도록 추천할 수 있습니다. 특히 3년 기간은 1년 기간보다 할인이 더 큽니다. Azure는 1년 기간보다 3년 기간에 대한 절감액을 찾을 가능성이 더 큽니다.

Azure에서 특정 리소스 크기 및 수량을 추천하는 이유를 이해하려면 **&lt;수량&gt; 세부 정보 참조** 를 선택하여 심층적인 시각화에서 시간 경과에 따른 잠재적 절감액을 표시합니다.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="예약 추천 사항에 대한 세부 정보 참조 링크를 보여 주는 예" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>해결 방법

원하는 기간에 대한 예약 수량을 구매할 수 있습니다. 추천 사항과 다른 수량 및 기간으로 예약을 구매하는 경우 최적의 절감액 및 사용량에 미치지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 예약 추천 사항에 대한 자세한 내용은 [예약 구매 추천 사항](determine-reservation-purchase.md)을 참조하세요.
