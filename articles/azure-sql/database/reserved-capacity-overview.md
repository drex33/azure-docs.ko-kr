---
title: 예약된 용량으로 컴퓨팅 비용 절감
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 컴퓨팅 비용을 절약하기 위해 Azure SQL Database와 SQL Managed Instance의 예약된 용량을 구입하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/13/2020
ms.openlocfilehash: ab1baffe32add88e855d800ffe93652d5356c704
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167738"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>예약된 용량으로 리소스 비용 절감 - Azure SQL Database 및 SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

컴퓨팅 리소스를 예약에 커밋하여 Azure SQL Database와 SQL Managed Instance 비용을 종량제 가격보다 절감합니다. 예약된 용량을 사용하면 Azure SQL Database 및/또는 SQL Managed Instance 사용에 대해 1년이나 3년 기간의 약정을 체결하여 컴퓨팅 비용에서 상당한 할인을 받을 수 있습니다. 예약된 용량을 구매하려면 Azure 지역, 배포 유형, 성능 계층, 용어를 지정해야 합니다.

특정 데이터베이스 또는 관리형 인스턴스에 예약을 할당할 필요가 없습니다. 기존에 이미 실행 중인 배포나 새로운 배포와 일치하면 자동으로 혜택을 얻을 수 있습니다. 예약을 구매하면 1년 또는 3년 동안의 컴퓨팅 비용을 사용량에 커밋하게 됩니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는 컴퓨팅 요금은 더 이상 종량제 요금으로 부과되지 않습니다. 

예약은 기본 및 청구 가능한 보조 컴퓨팅 복제본 모두에 적용되지만 서비스와 관련된 소프트웨어, 네트워킹 또는 스토리지 요금은 다루지 않습니다. 예약 기간이 끝나면 청구 혜택이 만료되고 데이터베이스 또는 관리형 인스턴스는 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다. 가격 책정 정보는 [예약된 용량 제품](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.

[Azure Portal](https://portal.azure.com)에서 예약된 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 종량제 요금이 있는 하나 이상의 엔터프라이즈 구독 또는 개별 구독에 대한 소유자 역할에 속해야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다. 예약된 용량

예약 구매에 대해 엔터프라이즈 고객과 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 및 [종량제 구독에서 Azure 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.

> [!NOTE]
> 예약된 용량을 구매해도 내가 사용할 수 있는 특정 인프라 리소스(가상 머신 또는 노드)가 미리 할당되거나 예약되는 것은 아닙니다.

## <a name="determine-correct-size-before-purchase"></a>구매하기 전에 올바른 크기 결정

예약의 크기는 특정 지역 내에서 기존 또는 곧 배포될 데이터베이스나 관리형 인스턴스에서 사용되는 컴퓨팅의 총 양을 기반으로 해야 하며, 동일한 성능 계층과 하드웨어 세대를 사용해야 합니다.

예를 들어 한 개의 범용 Gen5 - 16개 vCore 탄력적 풀과 두 개의 중요 비즈니스용 Gen5 - 4개 vCore 단일 데이터베이스를 실행 중이라고 가정합니다. 또한 범용 Gen5 – 16개 vCore 탄력적 풀 한 개와 중요 비즈니스용 Gen5 – 32개 vCore 탄력적 풀 한 개를 다음 달 안에 추가로 배포할 것이라고 가정합니다. 또한 최소 1년 동안 이러한 리소스가 필요할 것으로 가정해 보겠습니다. 이 경우 단일 데이터베이스/탄력적 풀 범용 - Gen5에 대해 32개(2x16) vCore 1년 예약과 단일 데이터베이스/탄력적 풀 중요 비즈니스용 - Gen5에 대해 40개(2x4 + 32) vCore 1년 예약을 구매해야 합니다.

## <a name="buy-reserved-capacity"></a>예약된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택한 다음, **예약 구매** 창에서 **SQL Database** 를 선택하여 SQL Database에 대한 새 예약을 구매합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 SQL Database 및 SQL Managed Instance의 기존 데이터베이스는 예약된 용량 할인을 받을 수 있습니다. 할인을 받는 실제 데이터베이스나 관리형 인스턴스의 수는 선택한 범위와 수량에 따라 달라집니다.

    ![예약된 용량 구매를 제출하기 전의 스크린샷](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    다음 표에서는 필수 필드에 대해 설명합니다.
    
    | 필드      | Description|
    |------------|--------------|
    |Subscription|용량 예약의 요금을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제품 번호 MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제 가격 책정이 적용되는 개별계약(제품 번호 MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 가격이 적용되는 개별 구독의 경우 구독 요금은 신용 카드 또는 청구서 결제 방법으로 청구됩니다.|
    |범위       |vCore 예약 범위는 하나 또는 여러 개의 구독(공유 범위)을 포함할 수 있습니다. 선택하는 경우에는 다음과 같습니다. <br/><br/>**공유** - vCore 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 데이터베이스 또는 관리형 인스턴스에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.<br/><br/>**단일 구독** - 해당 구독 내의 데이터베이스 또는 관리형 인스턴스에 vCore 예약 할인이 적용됩니다. <br/><br/>**단일 리소스 그룹** - 선택된 구독 내의 데이터베이스 인스턴스 또는 관리형 인스턴스, 해당 구독 내의 선택된 리소스 그룹에 예약 할인이 적용됩니다.</br></br>**관리 그룹**, 예약 할인은 관리 그룹과 청구 범위의 일부인 구독 목록의 일치하는 리소스에 적용됩니다.|
    |지역      |용량 예약이 적용되는 Azure 지역입니다.|
    |배포 유형|예약을 구매할 SQL 리소스 종류입니다.|
    |성능 계층|데이터베이스 또는 관리형 인스턴스의 서비스 계층입니다. |
    |용어        |1년 또는 3년입니다.|
    |수량    |용량 예약 내에서 구매한 컴퓨팅 리소스의 양입니다. 수량은 선택된 Azure 지역 및 예약 중인 성능 계층의 vCore 개수이며, 청구 할인 혜택을 받을 수 있습니다. 예를 들어 미국 동부 지역에서 Gen5 16개 vCore의 총 컴퓨팅 용량을 사용하여 여러 데이터베이스를 실행하거나 실행하려는 경우, 모든 데이터베이스에 대한 혜택을 최대화하기 위해 수량을 16으로 지정합니다. |

1. **비용** 섹션에서 용량 예약의 비용을 검토합니다.
1. **구매** 를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기** 를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="vcore-size-flexibility"></a>vCore 크기 유연성

vCore 크기 유연성을 통해 예약된 용량 이점을 잃지 않고 성능 계층 및 지역 내에서 크기를 확장 또는 축소할 수 있습니다. 예약된 용량은 예약된 용량 혜택을 잃지 않고 일반 작업의 일부로서 (동일한 지역 및 성능 계층 내에서) 탄력적 풀 안팎으로 핫 데이터베이스를 일시적으로 이동할 수 있는 유연성도 제공합니다. 예약에서 적용되지 않은 버퍼를 유지하여 예산을 초과하지 않고 성능 급증을 효과적으로 관리할 수 있습니다.

## <a name="limitation"></a>제한 사항

SQL Database에서 DTU 기반(기본, 표준, 프리미엄) 데이터베이스를 예약할 수 없습니다. 예약 용량 가격은 일반 공급 상태인 기능 및 제품에 대해서만 지원됩니다. 

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

vCore 예약 할인은 용량 예약의 범위 및 특성과 일치하는 데이터베이스 또는 관리형 인스턴스의 수에 자동으로 적용됩니다. 용량 예약 범위는 [Azure Portal](https://portal.azure.com), PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

- vCore 모델의 Azure SQL Database 서비스 계층에 대한 자세한 내용은 [vCore 모델 개요 - Azure SQL Database](service-tiers-sql-database-vcore.md)를 참조하세요.
- vCore 모델의 Azure SQL Managed Instance 서비스 계층에 대한 자세한 내용은 [vCore 모델 개요 - Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md)를 참조하세요.

용량 예약을 관리하는 방법을 알아보려면 [예약된 용량 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Reservations 관리](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure 예약 할인 이해](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)
