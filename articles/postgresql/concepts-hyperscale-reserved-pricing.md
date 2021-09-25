---
title: 예약된 컴퓨팅 가격 - Azure Database for PostgreSQL - Hyperscale(Citus)
description: 예약된 용량의 Azure Database for PostgreSQL - Hyperscale(Citus) 컴퓨팅 리소스에 대한 선불.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 422f8533c78d2242e1ef520d42dd09982990871a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577976"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>예약된 용량의 Azure Database for PostgreSQL - Hyperscale(Citus) 컴퓨팅 리소스에 대한 선불

Azure Database for PostgreSQL – Hyperscale(Citus)를 사용하여 종량제 가격과 비교하여 컴퓨팅 리소스에 대한 총 비용을 절감할 수 있습니다. Hyperscale(Citus) 예약된 용량을 사용하면 Hyperscale(Citus) 서버 그룹에 대해 1년 또는 3년 동안 선불 약정을 하여 컴퓨팅 비용을 크게 할인 받을 수 있습니다. Hyperscale(Citus) 예약 용량을 구입하려면 Azure 지역, 예약 기간, 청구 빈도를 지정해야 합니다.

> [!IMPORTANT]
> 이 문서는 Azure Database for PostgreSQL – Hyperscale(Citus)에 대한 예약된 용량에 관한 것입니다. Azure Database for PostgreSQL – 단일 서버에 예약된 용량에 대한 자세한 내용은 [Azure Database for PostgreSQL – 예약된 용량이 포함된 단일 서버 컴퓨팅 리소스에 대한 선불](./concept-reserved-pricing.md)을 참조하세요.

특정 Hyperscale(Citus) 서버 그룹에 예약을 할당할 필요가 없습니다. 이미 실행 중인 Hyperscale(Citus) 서버 그룹이나 새로 배포된 서버 그룹이 자동으로 예약된 가격 혜택을 받습니다. 예약을 구매하면 1년 또는 3년 동안의 컴퓨팅 비용을 선불할 수 있습니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는Hyperscale(Citus) 컴퓨팅 요금은 더 이상 종량제 요금으로 부과되지 않습니다. 

예약에는 Hyperscale(Citus) 서버 그룹과 연결된 소프트웨어, 네트워킹, 스토리지 요금이 포함되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 Hyperscale(Citus) 서버 그룹은 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다. 가격 정보는 [Azure Database for PostgreSQL – Hyperscale(Citus) 예약 용량 제안](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)을 참조하세요.

[Azure Portal](https://portal.azure.com/)에서 Hyperscale(Citus) 예약 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약된 용량을 구매하는 방법.

* 종량제 요금이 적용되는 하나 이상의 기업계약(EA) 또는 개별 구독에 대한 소유자여야 합니다.
* 기업계약의 경우, [EA Portal](https://ea.azure.com/)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우에는 구독에 대한 기업계약 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우, 관리 담당자 또는 판매 담당자가 Hyperscale(Citus) 예약 용량을 구매할 수 있습니다.

예약 구매에 대해 기업계약 고객과 종량제 고객의 요금을 청구하는 방법에 대한 자세한 내용은 다음을 참조하세요.
- [기업계약 등록의 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [종량제 구독의 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>구매 전 적절한 서버 그룹 크기 결정

예약 크기는 특정 지역 내의 Hyperscale(Citus) 서버 그룹에서 기존 또는 곧 배포되는 코디네이터 및 작업자 노드에 사용되는 총 컴퓨팅 크기를 기준으로 합니다.

예를 들어, 16 vCore 코디네이터 및 3개의 8 vCore 작업자 노드를 사용하여 Hyperscale(Citus) 서버 그룹 하나를 실행하고 있다고 가정해 보겠습니다. 또한 32 vCore 코디네이터 및 2개의 4 vCore 작업자 노드를 사용하여 다음 달에 추가 Hyperscale(Citus) 서버 그룹을 배포할 계획인 경우를 가정해 보겠습니다. 또한 1년 이상에 대해 이러한 리소스가 필요하다고 가정해 보겠습니다.

이 경우 다음에 대한 1년 예약을 구매합니다.

* 총 16 vCore + 32 vCore = 코디네이터 노드에 대한 48 vCore
* 총 3 노드 x 8 vCore + 2 노드 x 4 vCores = 24 + 8 = 작업자 노드에 대한 32 vCores

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL 예약 용량 구매

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **모든 서비스** > **예약** 을 선택합니다.
1. **추가** 를 선택합니다. **예약 구매** 창에서 **Azure Database for PostgreSQL** 을 선택하여 PostgreSQL 데이터베이스에 대한 새 예약을 구매합니다.
1. 구입할 **Hyperscale(Citus) 컴퓨팅** 유형을 선택하고 **선택** 을 클릭합니다.
1. **제품** 탭에서 선택한 컴퓨팅 유형의 수량을 검토합니다.
1. **구매 + 검토** 탭으로 이동하여 구매를 완료합니다.

다음 표에서는 필수 필드에 대해 설명합니다.

| 필드        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription | Azure Database for PostgreSQL 예약 용량 예약에 대한 요금을 지불하는 데 사용되는 구독입니다. 구독의 결제 방식은 Azure Database for PostgreSQL 예약 용량 예약에 대해 선불로 비용이 청구되는 방식입니다. 구독 유형은 기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격이 적용되는 개별계약(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 기업계약 구독의 경우 요금은 등록계약의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 가격이 적용되는 개별 구독의 경우 구독 요금은 신용 카드 또는 청구서 결제 방법으로 청구됩니다.                                                                                  |
| 범위        | vCore 예약 범위는 하나 또는 여러 개의 구독(공유 범위)을 포함할 수 있습니다. **공유** 를 선택하면 청구 컨텍스트 내의 모든 구독에서 실행되는 Hyperscale(Citus) 서버 그룹에 vCore 예약 할인이 적용됩니다. 기업계약 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 생성한 모든 종량제 구독입니다. 관리 그룹을 선택하면 **관리 그룹** 및 청구 범위의 일부인 모든 구독에서 실행되는 하이퍼스케일(Citus) 서버 그룹에 예약 할인이 적용됩니다. **단일 구독** 을 선택하면 이 구독의 Hyperscale(Citus) 서버 그룹에 vCore 예약 할인이 적용됩니다. **단일 리소스 그룹** 을 선택하는 경우 예약 할인은 선택한 구독의 Hyperscale(Citus) 서버 그룹 및 해당 구독 내에서 선택한 리소스 그룹에 적용됩니다. |
| 지역       | Azure Database for PostgreSQL – Hyperscale(Citus) 예약 용량 예약이 적용되는 Azure 지역입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 용어         | 1년 또는 3년입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 수량     | Hyperscale(Citus) 예약 용량 예약 내에서 구매한 컴퓨팅 리소스의 양. 예약되고 청구 할인을 받을 선택한 Azure 지역의 코디네이터 또는 작업자 노드 vCore 수. 예를 들어 미국 동부 지역에서 총 컴퓨팅 용량이 64 코디네이터 노드 vCore 및 32개 작업자 노드 vCore인 Hyperscale(Citus) 서버 그룹을 실행(또는 실행 계획)하는 경우, 코디네이터 및 작업자 노드의 수량을 각각 64 및 32로 지정하여 모든 서버에 대한 혜택을 최대화합니다.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 지역 내에서 코디네이터 및 작업자 노드 크기를 확장 또는 축소할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

VCore 예약 할인은 Azure Database for PostgreSQL 예약 용량 예약 범위 및 특성과 일치하는 Hyperscale(Citus) 서버 그룹 수에 자동으로 적용됩니다. Azure Portal, PowerShell, Azure CLI, API를 통해 Azure Database for PostgreSQL – Hyperscale(Citus) 예약 용량 예약의 범위를 업데이트할 수 있습니다.

Azure 예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure 예약은 무엇인가요?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure 예약 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-reservation-charges.md)
* [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [기업계약 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [파트너 센터 클라우드 솔루션 공급자 프로그램의 Azure 예약](/partner-center/azure-reservations)