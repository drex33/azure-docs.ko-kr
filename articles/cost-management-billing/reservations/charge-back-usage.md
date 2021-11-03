---
title: Azure Reservation 비용 차지백
description: 차지백에 대한 Azure Reservation 비용을 확인하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: dfe5a6e1dd95e3f5aa93499eee9c8b6b609fd502
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425826"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure Reservation 비용 차지백

기업계약 및 Microsoft 고객 계약 청구 독자는 예약에 대한 분할 상환 비용 데이터를 볼 수 있습니다. 비용 데이터를 사용하여 구독, 리소스 그룹, 리소스 또는 태그에 대한 통화 값을 파트너에게 차지백할 수 있습니다. 분할 상환 데이터에서 유효 가격은 비례하는 시간당 예약 비용입니다. 비용은 해당 날짜에 대한 리소스의 총 예약 사용량 비용입니다.

개별 구독이 있는 사용자는 해당 사용량 파일에서 분할 상환 비용 데이터를 가져올 수 있습니다. 리소스가 예약 할인을 가져오는 경우 사용량 파일의 *AdditionalInfo* 섹션에는 예약 정보가 포함됩니다. 자세한 내용은 [Azure Portal에서 사용량 다운로드](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv)를 참조하세요.

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>뒤로 표시 및 다시 충전에 대한 예약 사용량 데이터 참조

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + Billing** 으로 이동합니다. 
3. 왼쪽 탐색에서 **비용 분석** 을 선택합니다. 
4. **실제 비용** 에서 **분할 상환 비용** 메트릭을 선택합니다.
5. 예약에서 사용된 리소스를 보려면 **예약** 필터를 적용한 다음, 예약을 선택합니다.
6. **세분성** 을 **월별** 또는 **일별** 로 설정합니다.
7. 차트 유형을 **테이블** 로 설정합니다.
8. **그룹화 기준** 옵션을 **리소스** 로 설정합니다.

[![차니백에 사용할 수 있는 예약 리소스 비용을 보여 주는 예제](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

다음은 Azure Portal의 구독, 리소스 그룹 및 리소스 수준에서 예약 사용 비용을 보는 방법을 보여주는 비디오입니다.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>뒤로 표시 및 다시 충전에 대한 데이터 가져오기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + Billing** 으로 이동합니다. 
3. 왼쪽 탐색에서 **내보내기** 를 선택합니다. 
4. **추가** 단추를 클릭합니다.
5. 메트릭 단추로 분할 상환 비용을 선택하고 내보내기를 설정합니다.

예약 할인을 받는 사용량의 EffectivePrice는 0이 아니라 예약 비용에 비례합니다. 이 데이터를 통해 예약 사용의 금전적 가치를 구독, 리소스 그룹 또는 리소스별로 파악할 수 있으며, 내부적으로 예약 사용에 대해 차지백할 수 있습니다. 데이터 세트에도 사용되지 않은 예약 시간이 있습니다. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API를 사용하여 Azure 소비 및 예약 사용량 데이터 가져오기

API를 사용하여 데이터를 가져오거나 Azure Portal에서 데이터를 다운로드할 수 있습니다.

새 데이터를 가져오려면 [사용량 세부 정보 API](/rest/api/consumption/usagedetails/list)를 호출합니다. 용어에 대한 자세한 내용은 [사용 약관](../understand/understand-usage.md)을 참조하세요.

다음은 사용량 세부 정보 API에 대한 호출 예제입니다.

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{enrollmentId} 및 {billingPeriodId}에 대한 자세한 내용은 [사용량 세부 정보 – 나열](/rest/api/consumption/usagedetails/list) API 문서를 참조하세요.

메트릭 및 필터에 대한 다음 표의 정보는 일반적인 예약 문제를 해결하는 데 도움이 될 수 있습니다.

| **API 데이터 유형** | API 호출 작업 |
| --- | --- |
| **모든 요금(사용량 및 구매)** | {metric}을 ActualCost로 바꾸기 |
| **예약 할인이 적용된 사용량** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/reservationId%20ne%20으로 바꾸기 |
| **예약 할인이 적용되지 않은 사용량** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/reservationId%20eq%20으로 바꾸기 |
| **분할 상환 요금(사용량 및 구매)** | {metric}을 AmortizedCost로 바꾸기 |
| **사용하지 않는 예약 보고서** | {metric}을 AmortizedCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'UnusedReservation'로 바꾸기 |
| **예약 구매** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'Purchase'로 바꾸기  |
| **환불** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'Refund'로 바꾸기 |

## <a name="download-the-usage-csv-file-with-new-data"></a>새 데이터가 포함된 사용량 CSV 파일 다운로드

EA 관리자는 Azure Portal에서 새로운 사용량 데이터가 포함된 CSV 파일을 다운로드할 수 있습니다. 이 데이터는 EA 포털(ea.azure.com)에서 사용할 수 없습니다. 새 데이터를 보려면 Azure Portal(portal.azure.com)에서 사용 파일을 다운로드해야 합니다.

Azure Portal에서 [Cost Management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)로 이동합니다.

1. 청구 계정을 선택합니다.
2. **사용량 + 요금** 을 클릭합니다.
3. **다운로드** 를 클릭합니다.  
![Azure Portal에서 CSV 사용량 데이터 파일을 다운로드하는 위치를 보여주는 예제](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **사용량 세부 정보** 에서 **분할 상환 사용량 데이터** 를 선택합니다.

다운로드한 CSV 파일에는 실제 비용과 분할 상환 비용이 포함되어 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계
- Azure 예약 사용량 데이터에 대해 자세히 알아보려면 다음 문서를 참조하세요.
  - [기업계약 및 Microsoft 고객 계약 예약 비용 및 사용량](understand-reserved-instance-usage-ea.md)
 
