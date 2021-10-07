---
title: 예약 컴퓨팅 가격 책정 - Azure Database for PostgreSQL
description: 예약된 용량의 Azure Database for PostgreSQL 컴퓨팅 리소스에 대한 선불
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 02c8e93603672f18c456911a99503f05c78bd323
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658117"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>예약된 용량의 Azure Database for PostgreSQL 컴퓨팅 리소스에 대한 선불

[!INCLUDE[applies-to-postgres-single-flexible-server-hyperscale](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

종량제 가격에 비해 컴퓨팅 리소스 비용을 선결제하면 Azure Database for PostgreSQL에서의 비용을 절감할 수 있습니다. Azure Database for PostgreSQL 예약된 용량을 사용하면 PostgreSQL 서버에 대해 1년 또는 3년 동안 선불 약정을 하여 컴퓨팅 비용을 크게 할인 받을 수 있습니다. Azure Database for PostgreSQL 예약된 용량을 구매하려면 Azure 지역, 배포 유형, 성능 계층 및 용어를 지정해야 합니다. </br>

## <a name="how-does-the-instance-reservation-work"></a>인스턴스 예약은 어떻게 작동하나요?
특정 Azure Database for PostgreSQL 서버에 예약을 할당할 필요가 없습니다. 이미 실행 중이거나 새로 배포된 Azure Database for PostgreSQL이 예약된 가격 책정의 혜택을 자동으로 받게 됩니다. 예약을 구매하면 1년 또는 3년 동안의 컴퓨팅 비용을 선결제할 수 있습니다. 예약을 구매하는 즉시, 예약 특성과 일치하는 Azure Database for PostgreSQL 컴퓨팅은 더 이상 종량제 요금으로 부과되지 않습니다. 예약에는 PostgreSQL Database 서버와 연결된 소프트웨어, 네트워킹 또는 스토리지 요금이 포함되지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 Azure Databases for PostgreSQL은 종량제 요금으로 청구됩니다. 예약은 자동 갱신되지 않습니다. 가격 책정 정보는 [Azure Databases for PostgreSQL 예약된 용량 제품](https://azure.microsoft.com/pricing/details/postgresql/)을 참조하세요. </br>

> [!IMPORTANT]
> 예약된 용량 가격은 [단일 서버,](./overview.md#azure-database-for-postgresql---single-server) [유연한](flexible-server/overview.md)서버 및 [하이퍼스케일 Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) 배포 옵션의 Azure Database for PostgreSQL 사용할 수 있습니다. 하이퍼스케일(Citus)의 RI 가격 책정에 대한 자세한 내용은 [이 페이지](concepts-hyperscale-reserved-pricing.md)를 참조하세요.

[Azure Portal](https://portal.azure.com/)에서 Azure Database for PostgreSQL 예약된 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약된 용량을 구매하려면 다음과 같이 수행합니다.

* 종량제 요금이 적용되는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대해 소유자 역할을 해야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트가 Azure Database for PostgreSQL 예약된 용량을 구매할 수 있습니다. </br>

예약 구매에 대해 엔터프라이즈 고객 및 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종량제 구독의 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.

## <a name="reservation-exchanges-and-refunds"></a>예약 변경 및 환불

동일한 유형의 다른 예약에 대한 예약을 교환할 수 있으며 Azure Database for PostgreSQL - 유연한 서버가 있는 단일 서버에서 예약을 교환할 수도 있습니다. 더 이상 필요하지 않은 경우 예약을 환불할 수도 있습니다. Azure Portal을 통해 예약을 교환하거나 환불합니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="reservation-discount"></a>예약 할인

예약 인스턴스를 사용하여 컴퓨팅 비용을 최대 65% 절감할 수 있습니다. 사례에 대한 할인을 찾으려면 Azure Portal [예약 블레이드를](https://aka.ms/reservations) 방문하여 가격 책정 계층 및 지역별 절감액을 확인하세요. 예약 인스턴스를 사용하면 1년 또는 3년 사용 약관에 대한 선불 결제를 통해 워크로드, 예산 및 예측을 보다 효율적으로 관리할 수 있습니다. 비즈니스 요구 사항이 변경되면 예약을 변경 또는 취소할 수 있습니다.

## <a name="determine-the-right-server-size-before-purchase"></a>구매 전 적절한 서버 크기 결정

예약의 크기는 특정 지역 내에서 기존 또는 곧 배포될 단일 서버에서 사용되는 컴퓨팅의 총 양을 기반으로 해야 하며, 동일한 성능 계층 및 하드웨어 세대를 사용해야 합니다.</br>

예를 들어 하나의 범용 Gen5 - 32개 vCore PostgreSQL 데이터베이스 및 두 개의 메모리 최적화 Gen5 - 16개 vCore PostgreSQL 데이터베이스를 실행 중이라고 가정해 보겠습니다. 또한 범용 Gen5 – 8개 vCore 데이터베이스 서버 및 하나의 메모리 최적화 Gen5 – 32개 vCore 데이터베이스 서버를 추가로 다음 달 안에 배포하려고 가정합니다. 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 단일 데이터베이스 범용 - Gen5용으로 40개(32 + 8) vCore 1년 예약을 구매하고, 단일 데이터베이스 메모리 최적화 - Gen5용으로는 64개(2x16 + 32) vCore 1년 예약을 구매해야 합니다.


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL 예약된 용량 구매

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 구매 예약 창에서 **Azure Database for PostgreSQL** 을 선택하여 PostgreSQL 데이터베이스에 대한 새 예약을 구매합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 기존 또는 새 데이터베이스는 예약된 용량 할인을 받을 수 있습니다. 할인을 받는 실제 Azure Database for PostgreSQL 서버 수는 선택한 범위 및 수량에 따라 달라집니다.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="예약된 가격 책정 개요":::


다음 표에서는 필수 필드에 대해 설명합니다.

| 필드 | 설명 |
| :------------ | :------- |
| Subscription   | Azure Database for PostgreSQL 예약 용량 예약에 대한 요금을 지불하는 데 사용되는 구독입니다. 구독의 결제 방식은 Azure Database for PostgreSQL 예약된 용량 예약에 대해 선불로 비용이 청구되는 방식입니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격이 적용되는 개별계약(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 가격이 적용되는 개별 구독의 경우 구독 요금은 신용 카드 또는 청구서 결제 방법으로 청구됩니다.
| 범위 | vCore 예약 범위는 하나 또는 여러 개의 구독(공유 범위)을 포함할 수 있습니다. 다음을 선택하는 경우: </br></br> **공유** - vCore 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 Azure Database for PostgreSQL 서버에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</br></br>**관리 그룹**, 예약 할인은 관리 그룹 및 청구 범위의 일부인 모든 구독에서 실행되는 Azure Database for PostgreSQL 적용됩니다.</br></br> **단일 구독** - vCore 예약 할인이 이 구독의 Azure Database for PostgreSQL에 적용됩니다. </br></br> **단일 리소스 그룹**-예약 할인이 선택한 구독의 Azure Database for PostgreSQL 서버와 해당 구독 내에서 선택한 리소스 그룹에 적용됩니다.
| 지역 | Azure Database for PostgreSQL 예약된 용량 예약에 포함되는 Azure 지역입니다.
| 배포 유형 | 예약을 구매할 Azure Database for PostgreSQL 리소스 형식입니다.
| 성능 계층 | Azure Database for PostgreSQL 서버에 대한 서비스 계층입니다.
| 용어 | 1년
| 수량 | Azure Database for PostgreSQL 예약된 용량 예약 내에서 구매한 계산 리소스의 양입니다. 수량은 선택된 Azure 영역 및 예약 중인 성능 계층의 vCore 개수이며, 청구 할인 혜택을 받을 수 있습니다. 예를 들어 미국 동부 지역에서 Gen5 16개 vCore의 총 컴퓨팅 용량을 사용하여 Azure Database for PostgreSQL 서버를 실행 중이거나 실행할 계획인 경우 모든 서버에 대한 혜택을 최대화하려면 수량을 16으로 지정합니다.

## <a name="reserved-instances-api-support"></a>예약 인스턴스 API 지원

Azure API를 사용하여 프로그래밍 방식으로 Azure 서비스 또는 소프트웨어 예약에 대한 사용자 조직의 정보를 가져옵니다. 예를 들어 API를 사용하여 다음을 수행합니다.

- 구입할 예약 찾기
- 예약 구입
- 구매한 예약 보기
- 예약 액세스 보기 및 관리
- 예약 분할 또는 병합
- 예약 범위 변경
 
자세한 내용은 [Azure 예약 자동화를 위한 API](../cost-management-billing/reservations/reservation-apis.md)를 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 예약된 용량보다 더 많은 vCore로 규모를 조정하는 경우 종량제 가격 책정을 사용하여 과도한 vCore에 대해 요금이 청구됩니다.

## <a name="how-to-view-reserved-instance-purchase-details"></a>예약 인스턴스 구매 세부 정보를 보는 방법

[Azure Portal 왼쪽의 예약 메뉴를](https://aka.ms/reservations)통해 예약 인스턴스 구매 세부 정보를 볼 수 있습니다. 자세한 내용은 [Azure Database for PostgreSQL 예약 할인이 적용되는 방식을 참조하세요.](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)

## <a name="reserved-instance-expiration"></a>예약된 인스턴스 만료

예약 만료 전에 첫 30일 전에와 만료 날짜에 이메일 알림을 받게 됩니다. 예약이 만료되면 배포된 VM은 계속 실행되며 종량제 요금으로 청구됩니다. 자세한 내용은 [Azure Database for PostgreSQL 예약 인스턴스를](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

vCore 예약 할인은 Azure Database for PostgreSQL 예약된 용량 예약 범위 및 특성과 일치하는 Azure Database for PostgreSQL 서버 수에 자동으로 적용됩니다. Azure Portal, PowerShell, CLI 또는 API를 통해 Azure database for PostgreSQL 예약된 용량 예약의 범위를 업데이트할 수 있습니다.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 예약이란](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-reservation-charges.md)
* [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)