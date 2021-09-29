---
title: 소프트웨어 플랜 선결제 - Azure Reservations
description: 소프트웨어 플랜을 선결제로 지급하고 종량제 비용에 대한 비용을 절약하는 방법을 알아봅니다.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: c46215bde33f726aba61ad42bf27a14ac2edb4dc
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216498"
---
# <a name="prepay-for-azure-software-plans"></a>Azure 소프트웨어 플랜 선결제

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 SUSE 및 RedHat 소프트웨어 사용 대금을 선결제할 경우 종량제 요금에 대한 비용을 절감할 수 있습니다. 할인은 가상 머신 사용량이 아닌 SUSE 및 RedHat 미터에 대해서만 적용됩니다. 비용을 더 절약하기 위해 가상 머신에 대한 예약을 개별적으로 구매할 수 있습니다.

Azure Portal에서 SUSE 및 RedHat 소프트웨어 플랜을 구매할 수 있습니다. 플랜을 구매하려면:

- 종량제 가격이 있는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대한 소유자 역할이 있어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 활성화해야 합니다. 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 소프트웨어 플랜을 구매할 수 있습니다.

## <a name="buy-a-software-plan"></a>소프트웨어 플랜 구입

1. Azure Portal에 로그인하고 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동합니다.
2. **추가** 를 클릭하고 구매할 소프트웨어 플랜을 선택합니다.
필수 필드를 입력합니다. 구매하려는 특성과 일치하는 모든 SUSE Linux VM 또는 RedHat VM은 할인을 받습니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.
3. 구독을 선택합니다. 플랜에 대한 요금을 지불하는 데 사용됩니다.
구독 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P)이나 종량제 가격이 적용되는 개별 계약(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다.
    - 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다.
    - 종량제 가격이 적용되는 개별 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다.
4. 범위를 선택합니다. 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다.
    - 단일 구독 - 플랜 할인이 이 구독에서 일치하는 사용량에 적용됩니다.
    - 공유 - 플랜 할인이 청구 컨텍스트 내의 모든 구독에서 일치하는 사용량에 적용됩니다. 기업 고객의 경우 청구 컨텍스트는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 가격이 적용되는 개별 플랜 고객의 경우 청구 컨텍스트는 계정 관리자가 만든 종량제 가격 구독이 적용되는 모든 개별 플랜입니다.
    - 관리 그룹 - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.
    - 단일 리소스 그룹 - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
5. 제품을 선택하여 VM 크기와 이미지 형식을 선택합니다. 할인은 선택한 VM 크기에만 적용됩니다.
6. 1년 또는 3년의 기간을 선택합니다.
7. 청구 할인을 받을 수 있는 선불 VM 인스턴스 수인 수량을 선택합니다.
8. 장바구니에 제품을 추가하고 검토한 다음 구매합니다.

예약 할인은 선결제하는 소프트웨어 미터에 자동으로 적용됩니다. VM 컴퓨팅 요금에는 플랜이 적용되지 않습니다. VM 예약은 별도로 구입할 수 있습니다.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>다른 SUSE VM 크기에 할인이 적용됩니다.

예약 VM 인스턴스와 같이 SUSE Linux 플랜은 인스턴스 크기 유연성을 제공합니다. 구매한 SUSE 계획과 크기가 다른 VM을 배포할 때에도 할인이 적용됩니다. 자세한 내용은 [소프트웨어 플랜 할인의 적용 방식 이해](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)를 참조하세요.

## <a name="redhat-plan-discount"></a>RedHat 플랜 할인

플랜은 Red Hat Enterprise Linux 가상 머신에만 사용할 수 있습니다. 할인은 RedHat Enterprise Linux SAP HANA VM 또는 RedHat Enterprise Linux SAP Business Apps VM에는 적용되지 않습니다.

RedHat 플랜 할인은 구매할 때 선택한 VM 크기에만 적용됩니다. RHEL 플랜은 구매 후 환불하거나 교환할 수 없습니다.


## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

구매한 SUSE 또는 RedHat 플랜은 취소하거나 교환할 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구매 대상을 식별하는 데 도움이 필요하다면 [소프트웨어 플랜 할인이 적용되는 방식의 이해](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure에서 Reservations 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [SUSE 예약 할인이 적용되는 방식 이해](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
