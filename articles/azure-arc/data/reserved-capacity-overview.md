---
title: 예약 된 용량으로 비용 절감
description: 비용 절감을 위해 Azure Arc 사용 SQL Managed Instance 예약 된 용량을 구입 하는 방법을 알아봅니다.
services: sql-database
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 10/27/2021
ms.openlocfilehash: 70a6f67fb5a7443e84c937c9a5bf56db5980022e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053184"
---
# <a name="reserved-capacity---azure-arc-enabled-sql-managed-instance"></a>예약 된 용량-Azure Arc 사용 SQL Managed Instance

종 량 제 가격과 비교 하 여 azure arc 서비스에 대 한 예약을 커밋하여 azure arc 사용 SQL Managed Instance으로 비용을 절감 하세요. 예약 된 용량을 사용 하면 1 년 또는 3 년 동안 Azure Arc 사용이 가능한 Managed Instance SQL에 대 한 약정을 설정 하 여 서비스 요금에 대해 상당한 할인을 받을 수 있습니다. 예약된 용량을 구매하려면 Azure 지역, 배포 유형, 성능 계층, 용어를 지정해야 합니다.

특정 데이터베이스 또는 관리형 인스턴스에 예약을 할당할 필요가 없습니다. 기존에 이미 실행 중인 배포나 새로운 배포와 일치하면 자동으로 혜택을 얻을 수 있습니다. 예약을 구매 하 여 1 년 또는 3 년 동안 Azure Arc 서비스 비용을 사용 하도록 커밋 합니다. 예약을 구매 하는 즉시 예약 특성과 일치 하는 서비스 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 

예약은 Azure Arc 서비스 비용에만 적용 되며 SQL IP 비용이 나 기타 요금은 다루지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료 되며 관리 되는 인스턴스는 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신되지 않습니다. 가격 책정 정보는 [예약된 용량 제품](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/reservationsBrowse)에서 예약된 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 종량제 요금이 있는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대한 소유자 역할에 속해야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다. 예약된 용량

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대 한 자세한 내용은 [Enterprise 등록에 대 한 azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종 량 제 구독에 대 한 azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조 하세요.

## <a name="determine-correct-size-before-purchase"></a>구매하기 전에 올바른 크기 결정

예약 크기는 특정 지역 예약 범위 내에서 기존 또는 곧 배포 되는 관리 되는 인스턴스에 사용 되는 vCores에서 측정 된 계산 리소스의 총 크기를 기준으로 해야 합니다.

다음 목록에서는 리소스를 예약 하는 방법을 프로젝션 하는 시나리오를 보여 줍니다. 

* **현재**: 
  - 하나의 범용, 16 vCore 관리 되는 인스턴스
  - 중요 한 두 개의 중요 한 vCore 관리 되는 인스턴스

* **다음 연도에 다음을 추가 합니다**. 
  - 하나 이상의 범용, 16 개 vCore 관리 되는 인스턴스
  - 중요 한 하나 이상의 중요 한 32 vCore 관리 되는 인스턴스

* 다음 **에 대 한 예약 구매**:
  - 32 (2x16) 범용 관리 되는 인스턴스의 vCore 1 년 예약
  - 48 (2x8 + 32) 비즈니스에 중요 한 관리 되는 인스턴스의 vCore 1 년 예약 

## <a name="buy-reserved-capacity"></a>예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음 **구매 예약** 창에서 **SQL Managed Instance** 를 선택 하 여 Azure Arc 사용 SQL Managed Instance에 대 한 새 예약을 구매 합니다.
4. 필수 필드를 입력합니다. 기존 SQL 선택한 특성과 일치 하는 리소스를 Managed Instance 하 여 예약 된 용량 할인을 받습니다. 할인을 받는 실제 데이터베이스나 관리형 인스턴스의 수는 선택한 범위와 수량에 따라 달라집니다.

    다음 표에서는 필수 필드에 대해 설명합니다.
    
    | 필드      | 설명|
    |------------|--------------|
    |Subscription|용량 예약의 요금을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제품 번호 MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격 책정이 적용되는 개별계약(제품 번호 MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 가격이 적용되는 개별 구독의 경우 구독 요금은 신용 카드 또는 청구서 결제 방법으로 청구됩니다.|
    |범위       |vCore 예약 범위는 하나 또는 여러 개의 구독(공유 범위)을 포함할 수 있습니다. 선택하는 경우에는 다음과 같습니다. <br/><br/>**공유** - vCore 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 데이터베이스 또는 관리형 인스턴스에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.<br/><br/>**단일 구독** - 해당 구독 내의 데이터베이스 또는 관리형 인스턴스에 vCore 예약 할인이 적용됩니다. <br/><br/>**단일 리소스 그룹**, 예약 할인은 선택한 구독에서 선택한 구독의 관리 되는 인스턴스 및 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다.</br></br>관리 **그룹**, 예약 할인은 관리 그룹 및 청구 범위의 일부인 구독 목록에서 관리 되는 인스턴스에 적용 됩니다.|
    |지역      |용량 예약이 적용되는 Azure 지역입니다.|
    |배포 유형|예약을 구매할 SQL 리소스 종류입니다.|
    |성능 계층|데이터베이스 또는 관리형 인스턴스의 서비스 계층입니다. |
    |용어        |1년 또는 3년입니다.|
    |수량    |용량 예약 내에서 구매한 컴퓨팅 리소스의 양입니다. 수량은 선택한 Azure 지역 및 성능 계층에서 예약 된 vCores의 수 이며 청구 할인을 받게 됩니다. 예를 들어 미국 동부 지역에서 Gen5 16 vCores의 총 계산 용량을 사용 하 여 여러 개의 관리 되는 인스턴스를 실행 하거나 계획 하는 경우 모든 데이터베이스에 대 한 혜택을 최대화 하려면 수량을 16으로 지정 합니다. |

1. **비용** 섹션에서 용량 예약의 비용을 검토합니다.
1. **구매** 를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기** 를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 예약에서 적용되지 않은 버퍼를 유지하여 예산을 초과하지 않고 성능 급증을 효과적으로 관리할 수 있습니다.

## <a name="limitation"></a>제한 사항

예약 된 용량 가격은 일반 공급 상태에 있는 기능 및 제품에 대해서만 지원 됩니다. 

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

VCore 예약 할인은 용량 예약 범위 및 특성과 일치 하는 관리 되는 인스턴스 수에 자동으로 적용 됩니다. 용량 예약 범위는 [Azure Portal](https://portal.azure.com), PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

azure arc 사용 SQL Managed Instance의 서비스 계층에 대 한 자세한 내용은 [azure arc 사용 SQL Managed Instance 서비스 계층](service-tiers.md)을 참조 하세요.

- vcore 모델에 대 한 azure SQL Managed Instance 서비스 계층에 대 한 자세한 내용은 [azure SQL Managed Instance-vcore 서비스 계층의 하드웨어 계산](../../azure-sql/managed-instance/service-tiers-managed-instance-vcore.md) 을 참조 하세요.

용량 예약을 관리하는 방법을 알아보려면 [예약된 용량 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Reservations 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure 예약 할인 이해](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)
