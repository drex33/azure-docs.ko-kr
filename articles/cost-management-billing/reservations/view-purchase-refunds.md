---
title: Azure Reservation 구매 및 환불 트랜잭션 보기
description: Azure Reservation 구매 및 환불 트랜잭션을 보는 방법에 대해 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: 0c9e247b4006fd22c7e820dd2f6d68362a12eea8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705894"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>예약 구매 및 환불 트랜잭션 보기

몇 가지 방법으로 예약 구매 및 환불 트랜잭션을 볼 수 있습니다. Azure Portal, Power BI 및 REST API를 사용할 수 있습니다. 거래에서 교환 된 예약을 환불 하 고 구매할 수 있습니다.

## <a name="view-reservation-purchases-in-the-azure-portal"></a>Azure Portal에서 예약 구매 보기

기업계약 및 Microsoft 고객 계약 청구 읽기 권한자는 비용 분석에서 누적 예약 구매를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 비용 분석을 선택합니다.
1.  **가격 책정 모델** 에 필터를 적용한 다음, **예약** 을 선택합니다.
1. 예약 구매를 보려면  **요금 유형** 에 필터를 적용한 다음, **구매** 를 선택합니다.
1.  **세분성** 을 **월별** 로 설정합니다.
1. 차트 유형을 **열(누적)** 로 설정합니다.

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="비용 분석에서 예약 구매를 보여 주는 스크린샷" lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Azure Portal에서 예약 트랜잭션 보기

기업 등록 또는 Microsoft 고객 계약 청구 관리자는 Cost Management 및 청구에서 예약 트랜잭션을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구** 를 검색합니다.
1. **예약 트랜잭션** 을 선택합니다.
1. 결과를 필터링하려면 **시간 범위**, **유형** 또는 **설명** 을 선택합니다.
1. **적용** 을 선택합니다.

[![Azure Portal에서 예약 트랜잭션을 보여 주는 스크린샷](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Power BI에서 예약 트랜잭션 보기

기업 등록 관리자는 Cost Management Power BI 앱을 사용하여 예약 트랜잭션을 볼 수 있습니다.

1. [Cost Management Power BI 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)을 가져옵니다.
1. RI 구매 보고서로 이동합니다.

[![예약 트랜잭션을 보여 주는 예제.](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

자세한 내용은 [Cost Management Power BI 앱 Enterprise 계약](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md)을 참조 하세요.

## <a name="use-apis-to-get-reservation-transactions"></a>API를 사용하여 예약 트랜잭션 가져오기

EA(기업계약) 및 Microsoft 고객 계약 사용자는 [예약 트랜잭션 - 목록 API](/rest/api/consumption/reservationtransactions/list)를 사용하여 예약 트랜잭션 데이터를 가져올 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
