---
title: Azure Reservations의 셀프 서비스 교환 및 환불
description: Azure Reservations을 교환하거나 환불하는 방법을 알아봅니다. 예약을 교환하거나 환불하려면 예약 주문에 대한 소유자 액세스 권한이 있어야 합니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/14/2021
ms.author: banders
ms.openlocfilehash: 56ebba760defcb47d336874294255e2e97e5a151
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546231"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure Reservations의 셀프 서비스 교환 및 환불

Azure Reservations는 진화하는 요구 사항을 충족하는 데 유용한 유연성을 제공합니다. 예약을 동일한 유형의 다른 예약으로 교환할 수 있습니다. 예를 들어 Azure Dedicated Host, Azure VMware Solution 및 Azure Virtual Machines를 포함한 여러 컴퓨팅 예약을 모두 한 번에 반환할 수 있습니다. 즉, 예약 제품은 동일한 유형의 예약인 경우 서로 바꿔 사용할 수 있습니다. 다른 예에서는 Managed Instance를 비롯한 여러 SQL 데이터베이스 예약 유형과 Elastic Pool을 서로 교환할 수 있습니다.

그러나 다른 예약은 교환할 수 없습니다. 예를 들어 Cosmos DB 예약을 SQL Database로 교환할 수 없습니다.

다른 지역에서 비슷한 유형의 다른 예약을 구매하기 위해 예약을 교환할 수도 있습니다. 예를 들어 미국 서부 2에 있는 예약은 유럽 서부에 있는 예약을 교환할 수 있습니다.

예약을 교환할 때 1년에서 3년까지 기간을 변경할 수 있습니다.

예약을 환불할 수도 있지만 청구 범위(예: EA, Microsoft 고객 계약 및 Microsoft 파트너 계약)에서 취소된 모든 예약 약정의 합계는 12개월 연속 기간 내에 50,000USD를 초과할 수 없습니다.

Azure Databricks 예약된 용량, Azure VMware solution by CloudSimple 예약, Azure Red Hat Open Shift 예약, Red Hat 플랜 및 SUSE Linux 플랜은 환불에 적합하지 않습니다.

> [!NOTE]
> - **기존 예약을 교환하거나 환불하려면 예약 주문에 대한 소유자 액세스 권한이 있어야 합니다**. [예약을 관리할 수 있는 사용자를 추가 또는 변경](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)할 수 있습니다.
> - Microsoft는 현재 예약 환불에 대해 조기 종료 수수료를 청구하지 않습니다. 향후에 환불에 대한 수수료가 부과될 수 있습니다. 현재 이 요금을 사용하도록 설정된 날짜가 없습니다.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>기존 예약을 교환하거나 환불하는 방법

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 예약을 교환할 수 있습니다.

1. 환불하려는 예약을 선택하고 **교환** 을 선택합니다.  
    [![반환할 예약을 보여 주는 예제 이미지](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. 구매하려는 VM 제품을 선택하고 수량을 입력합니다. 새 구매 합계가 반품 합계보다 많은지 확인합니다. [구매하기 전에 적절한 크기를 결정합니다](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![교환으로 구매할 VM 제품을 보여 주는 예제 이미지](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. 검토 후 거래를 완료합니다.  
    [![교환으로 구매하여 반환이 완료되는 VM 제품을 보여 주는 예제 이미지](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

예약을 환불하려면 **예약 정보** 로 이동하여 **환불** 을 선택합니다.

## <a name="exchange-multiple-reservations"></a>여러 예약 교환

한 번의 작업으로 비슷한 유형의 예약을 반환할 수 있습니다.

예약을 교환할 때 새로운 구매 통화 금액은 환불 금액보다 커야 합니다. 새 구매 금액이 환불 금액보다 적으면 오류가 발생합니다. 오류가 표시되면 반품할 수량을 줄이거나 구매할 금액을 늘립니다.

1. Azure Portal에 로그인하고, **예약** 으로 이동합니다.
1. 예약 목록에서 교환하려는 각 예약의 확인란을 선택합니다.
1. 페이지 맨 위에서 **교환** 을 선택합니다.
1. 필요한 경우 각 예약에 대해 반환할 수량을 수정합니다.
1. 자동 채우기 반환 수량을 선택한 경우 **모두 환불** 을 선택하여 각 예약에 대해 소유한 전체 수량으로 목록을 채우거나 **사용률 최적화(7일)** 를 선택하여 마지막 7일 사용량을 기준으로 사용률을 최적화하는 수량으로 목록을 채웁니다. **적용을 선택합니다**.
1. 페이지 맨 아래에서 **다음: 구매** 를 선택합니다.
1. 구매 탭에서 교환하려는 사용 가능한 제품을 선택합니다. 종류가 다른 여러 제품을 선택할 수 있습니다.
1. 구매할 제품 선택 창에서 원하는 제품을 선택한 다음, **장바구니에 추가** 를 선택하고 **닫기** 를 선택합니다.
1. 완료되면 **다음: 검토** 를 선택합니다.
1. 예약을 검토하여 구매할 새 예약을 반환하고 **교환 확인** 을 선택합니다.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>프리미엄이 아닌 스토리지를 프리미엄 스토리지로 교환

프리미엄 스토리지를 지원하지 않는 VM 크기에 대해 구매한 예약을 프리미엄 스토리지를 지원하는 해당 VM 크기로 교환할 수 있습니다. 예를 들어, _F1_ 을 _F1s_ 로 교환할 수 있습니다. 교환을 수행하려면 예약 정보로 이동하여 **교환** 을 선택합니다. 교환은 예약 인스턴스의 기간을 재설정하거나 새 트랜잭션을 만들지 않습니다.
다른 크기, 계열, 지역 또는 결제 빈도로 교환하는 경우 새 예약의 기간이 다시 설정됩니다. 

## <a name="how-transactions-are-processed"></a>거래가 처리되는 방식

먼저 Microsoft가 기존 예약을 취소하고 예약에 해당하는 비례 배분 금액을 환불합니다. 교환이 있으면 새로운 구매가 처리됩니다. Microsoft는 계정 유형과 지불 방법에 따라 다음 방법 중 하나를 사용하여 환불을 처리합니다.

### <a name="enterprise-agreement-customers"></a>기업계약 고객

원래 구매 시 Azure 선불(이전에는 현금 약정 금액이라고 함)을 사용한 경우에는 교환 및 환불을 위해 금액이 추가됩니다. 예약을 사용하여 구매한 Azure 선불 기간이 더 이상 활성 상태가 아니면, 현재 기업계약 Azure 선불 기간에 크레딧이 추가됩니다. 크레딧은 환불 날짜로부터 90일 동안 유효합니다. 사용하지 않은 크레딧은 90일 종료 시 만료됩니다.

원래 구매가 초과분으로 설정된 경우 예약이 구매된 원래 청구서와 모든 이후 청구서가 다시 열리고 다시 조정됩니다. Microsoft에서 환불에 대한 크레딧 메모를 발급합니다.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>종량제 청구서 지불 및 CSP 프로그램

원래 예약 구매 청구서가 취소된 다음, 환불을 위한 새 청구서가 생성됩니다. 교환의 경우, 새 청구서에 환불 및 새로운 구매가 표시됩니다. 환불 금액은 구매에 맞춰 조정됩니다. 예약만 환불한 경우에는 비례 배분된 금액이 Microsoft에 유지되며 향후 예약 구매에 맞게 조정됩니다. 종량제 요금으로 예약을 구입하고 나중에 CSP로 이동하는 경우에는 영향을 주지 않고 예약을 반환하고 다시 구매할 수 있습니다.
CSP를 통한 환불 요청 연락처 지원에 대해서는 CSP에서 셀프 서비스 환불을 수행할 수 없습니다.

### <a name="pay-as-you-go-credit-card-customers"></a>종량제 신용 카드 고객

원래 청구서가 취소되고 새 청구서가 생성됩니다. 원래 구매에 사용한 신용 카드로 금액이 환불됩니다. 카드를 변경한 경우에는, [지원 부서에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)하세요.

## <a name="cancel-exchange-and-refund-policies"></a>취소, 교환 및 환불 정책

Azure에는 취소, 교환 및 환불에 대해 다음과 같은 정책이 있습니다.

**교환 정책**

- 기존 예약 여러 개를 반환하여 동일한 유형의 새 예약 하나를 구매할 수 있습니다. 한 가지 유형의 예약을 다른 유형으로 교환할 수 없습니다. 예를 들어, VM 예약을 반환하고 SQL 예약을 구매할 수 없습니다. 교환을 통해 제품군, 시리즈, 버전, SKU, 지역, 수량 및 기간과 같은 예약 속성을 변경할 수 있습니다.
- 예약 소유자만 교환을 처리할 수 있습니다. [예약을 관리할 수 있는 사용자를 추가 또는 변경하는 방법을 알아보세요](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- 교환은 환불 및 재구매로 처리됩니다. 취소 및 새 예약 구매에 대해서는 다른 거래가 생성됩니다. 거래한 예약에 대해서는 비례 배분된 예약 금액이 환불됩니다. 새로운 구매에 대해서는 전체 금액이 청구됩니다. 비례 배분된 예약 금액은 반환하는 예약에 대한 일일 비례 배분 잔액입니다.
- 예약을 구매하는 데 사용된 기업 계약이 만료되고 새 계약으로 갱신된 경우에도 예약을 교환하거나 환불할 수 있습니다.
- 새 예약의 수명 약정은 반환된 예약의 남은 약정보다 크거나 같아야 합니다. 예제: 매월 $100이고 18번째 지급 후에 교환되는 3년 예약의 경우 새 예약의 수명 약정은 $1800 이상(월별 지불 또는 선불)이어야 합니다.
- 교환의 일부로 구매한 신규 예약에는 교환 시부터 시작하는 새 기간이 포함됩니다.
- 교환에 대한 위약금이나 연간 한도는 없습니다.

**환불 정책**

- 현재는 조기 종료 수수료를 청구하지 않지만 향후에 취소 시 12%의 조기 종료 수수료가 있을 수 있습니다.
- 취소된 총 약정은 청구 프로필 또는 단일 등록에 대한 12개월 연속 기간 동안 50,000USD를 초과할 수 없습니다. 예를 들어 매월 100USD이고 18개월 내 환불된 3년 예약의 경우 취소된 약정은 1,800USD입니다. 환불 후 환불에 대한 사용 가능한 새 한도는 48,200USD가 됩니다. 환불로부터 365일 내에 48,200USD 한도가 1,800USD로 증가하고 새 풀이 50,000USD가 됩니다. 청구 프로필 또는 EA 등록에 대한 다른 모든 예약 취소는 동일한 풀을 소진하고, 동일한 보급 논리가 적용됩니다.
- Azure는 청구 프로필 또는 EA 등록에 대해 12개월 기간 동안 50,000USD 한도를 초과하는 환불을 처리하지 않습니다.
    - Exchange에서 발생하는 환불은 환불 한도에 계산되지 않습니다.
- 환불은 예약의 구매 가격 또는 현재 가격 중 가장 낮은 가격을 기준으로 계산됩니다.
- 예약 주문 소유자만 환불을 처리할 수 있습니다. [예약을 관리할 수 있는 사용자를 추가 또는 변경하는 방법을 알아보세요](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure 예약이란?](save-compute-costs-reservations.md)
    - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](../manage/understand-vm-reservation-charges.md)
    - [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)
    - [CSP 프로그램에서 Azure 예약](/partner-center/azure-reservations)
