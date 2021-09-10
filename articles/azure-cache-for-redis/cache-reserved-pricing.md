---
title: 예약된 용량의 컴퓨팅 선불 - Azure Cache for Redis
description: 예약된 용량의 Azure Cache for Redis 컴퓨팅 리소스 선불
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: d9fcb8567d9f7593a059567df6edc279b6fa701c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110782948"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>예약된 용량의 Azure Cache for Redis 컴퓨팅 리소스 선불

종량제 가격에 비해 컴퓨팅 리소스 비용을 선 결제하면 Azure Cache for Redis에서의 비용을 절감할 수 있습니다. Azure Cache for Redis 예약된 용량을 사용하면 캐시에 대해 1년 또는 3년 동안 선불 약정을 하여 컴퓨팅 비용을 크게 할인받을 수 있습니다. Azure Cache for Redis 예약된 용량을 구매하려면 Azure 지역, 서비스 계층, 용어를 지정해야 합니다.

특정 Azure Cache for Redis 인스턴스에 예약을 할당할 필요가 없습니다. 이미 실행 중이거나 새로 배포된 Azure Cache for Redis가 예약된 캐시 크기 한도에서 예약된 가격 책정의 혜택을 자동으로 받게 됩니다. 예약을 구매하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제하게 됩니다. 예약을 구매하는 즉시, 예약 특성과 일치하는 Azure Cache for Redis 컴퓨팅은 더 이상 종량제 요금으로 부과되지 않습니다. 예약에는 캐시와 연결된 네트워킹 또는 스토리지 요금이 포함되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 Azure Cache for Redis은 종량제 요금으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 가격 책정 정보는 [ 예약 용량 제품](https://azure.microsoft.com/pricing/details/cache)을 참조하세요.

[Azure Portal](https://portal.azure.com/)에서 Azure Cache for Redis 예약된 용량을 구매할 수 있습니다. 예약된 용량을 구매하려면 다음과 같이 수행합니다.

* 종량제 요금이 적용되는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대해 소유자 역할을 해야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트가 Azure Cache for Redis 예약된 용량을 구매할 수 있습니다.

예약 구매에 대해 엔터프라이즈 고객 및 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종량제 구독의 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.


## <a name="determine-the-right-cache-size-before-purchase"></a>구매 전 적절한 캐시 크기 결정

예약의 크기는 특정 지역 내에서 기존 또는 곧 배포될 캐시에서 사용되는 총 메모리 크기를 기준으로 해야 하며, 동일한 서비스 계층을 사용해야 합니다.

예를 들어 13GB와 26GB의 캐시를 하나씩 실행한다고 가정해 보겠습니다. 두 캐시 모두 1년 넘게 필요합니다. 또한 계절적 수요를 충족하기 위해 한 달 동안 기존 13GB 캐시를 26GB로 확장했다가 다시 축소할 계획입니다. 이 경우 1년 예약에서 1개의 P2 캐시와 1개의 P3 캐시 또는 3개의 P2 캐시를 구매하여 비용 절감을 최대화할 수 있습니다. 캐시에 할당된 양에 상관없이 예약한 총 캐시 메모리 양에 대해 할인을 받게 됩니다.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure Cache for Redis 예약된 용량 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/)에서 예약 VM 인스턴스를 구입할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 구매 예약 창에서 **Azure Cache for Redis** 을 선택하여 캐시에 대한 새 예약을 구매합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 기존 또는 새 데이터베이스는 예약된 용량 할인을 받을 수 있습니다. 할인을 받는 Azure Cache for Redis 인스턴스의 실제 수는 선택한 범위와 수량에 따라 달라집니다.


![예약된 가격 책정 개요](media/cache-reserved-pricing/cache-reserved-price.png)


다음 표에서는 필수 필드에 대해 설명합니다.

| 필드 | 설명 |
| :------------ | :------- |
| Subscription   | Azure Cache for Redis 예약된 용량 예약에 대한 요금을 지불하는 데 사용되는 구독입니다. 구독의 결제 방식은 Azure Cache for Redis 예약된 용량 예약에 대해 선불로 비용이 청구되는 방식입니다. 구독 유형은 기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격이 적용되는 개별계약(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 가격이 적용되는 개별 구독의 경우 구독 요금은 신용 카드 또는 청구서 결제 방법으로 청구됩니다.
| 범위 | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: </br></br> **공유** - 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행되는 Azure Cache for Redis 인스턴스에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br> **단일 구독** - 예약 할인이 구독의 Azure Cache for Redis 인스턴스에 적용됩니다. </br></br> **단일 리소스 그룹** 을 선택하면 선택한 구독의 Azure Cache for Redis 인스턴스와 해당 구독 내에서 선택한 리소스 그룹에 예약 할인이 적용됩니다.
| 지역 | Azure Cache for Redis 예약된 용량 예약에 포함되는 Azure 지역입니다.
| 가격 책정 계층 | Azure Cache for Redis 서버를 위한 서비스 계층입니다.
| 용어 | 1년 또는 3년
| 수량 | Azure Cache for Redis 예약된 용량 예약 내에서 구매한 계산 리소스의 양입니다. 수량은 선택된 Azure 지역 및 예약 중인 서비스 계층의 캐시 개수이며, 청구 할인 혜택을 받을 수 있습니다. 예를 들어 미국 동부 지역에서 26GB의 총 캐시 용량을 사용하여 Azure Cache for Redis 서버를 실행 중이거나 실행할 계획인 경우 모든 캐시에 대한 혜택을 최대화하려면 26GB에 해당하는 수량을 지정합니다. 수량은 1개의 P3 캐시 또는 2개의 P2 캐시일 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="cache-size-flexibility"></a>캐시 크기 유연성

캐시 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 서비스 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 Azure Cache for Redis 인스턴스의 수에 자동으로 적용됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  예약 용량 할인이 Azure Cache for Redis에 적용되는 방법을 알아보려면 [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)를 참조하세요.

* Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

    * [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)