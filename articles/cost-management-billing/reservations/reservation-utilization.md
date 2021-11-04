---
title: Azure 예약 사용률 보기
description: 예약 사용률 및 세부 정보를 가져오는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: f69c39205ddc465ee4634ec621905fed2d701682
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446242"
---
# <a name="view-reservation-utilization-after-purchase"></a>구매 후 예약 사용률 보기

Azure Portal 및 Cost Management Power BI 앱에서 예약 사용률과 예약에 사용된 리소스를 볼 수 있습니다.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Azure RBAC 액세스 권한으로 Azure Portal에서 사용률 보기

예약 사용률을 보려면 예약에 대한 Azure RBAC 액세스 권한이나 모든 Azure 구독과 관리 그룹을 관리할 수 있는 높은 액세스 권한이 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동합니다.
1. 목록에는 소유자 또는 Reader 역할을 보유한 예약 목록이 표시됩니다. 각 예약에는 마지막으로 알려진 사용률이 표시됩니다.
1. 사용률을 선택하여 사용률 기록 및 세부 정보를 봅니다. 다음 동영상에서는 예제를 보여줍니다.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>청구 관리자로 사용률 보기

EA(기업계약) 관리자 또는 MCA(Microsoft 고객 계약) 청구 관리자는 **Cost Management + Billing** 에서 사용률을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + Billing** > **예약** 으로 이동합니다.
1. 사용률을 선택하여 사용률 기록 및 세부 정보를 봅니다.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>API, PowerShell 및 CLI를 사용하여 예약 및 사용률 가져오기

예약 인스턴스 사용량 API를 사용하여 [예약 사용률](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)을 가져올 수 있습니다.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI에서 예약 및 사용률 보기

Power BI 사용자에게는 두 가지 옵션이 있습니다.

- [Power BI Desktop에 대 한 Cost Management 커넥터](/power-bi/desktop-connect-azure-cost-management)에서 Power BI Desktop 예약 구매 날짜 및 사용률 데이터에 대 한 Cost Management 커넥터를 사용할 수 있습니다. 커넥터를 사용하여 원하는 보고서를 만듭니다.
- Cost Management Power BI 앱-추가로 사용자 지정할 수 있는 미리 생성 된 보고서에 [Cost Management Power BI 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) 을 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 예약 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량을 이해](understand-reserved-instance-usage.md)합니다.
- [엔터프라이즈 등록에서 예약 사용량을 이해](understand-reserved-instance-usage-ea.md)합니다.
- [CSP 구독의 예약 사용량을 이해](/partner-center/azure-reservations)합니다.
