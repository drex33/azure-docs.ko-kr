---
title: Azure Reservations 관리
description: Azure Reservations를 관리하는 방법을 알아봅니다. 예약 범위를 변경하고, 예약을 분할하고, 예약 사용을 최적화하는 단계를 알아봅니다.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: cee0acf851d82ba09867b8d66c09a17b21e7af45
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989060"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure 리소스에 대한 예약 관리

Azure 예약을 구입하면 다른 구독에 예약을 적용하거나, 예약을 관리할 수 있는 사용자를 변경하거나, 예약의 범위를 변경해야 할 수 있습니다. 또한 예약을 두 개로 분할하여 구입한 일부 인스턴스를 다른 구독에 적용할 수도 있습니다.

Azure Reserved Virtual Machine Instances를 구입한 경우 예약에 대한 최적화 설정을 변경할 수 있습니다. 예약 할인을 동일한 시리즈의 VM에 적용하거나, 특정 VM 크기에 대한 데이터 센터 용량을 예약할 수 있습니다. 예약이 완전히 사용되도록 예약을 최적화해야 합니다.

*예약을 관리하는 데 필요한 권한은 구독 권한과는 다릅니다.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>예약 주문 및 예약

예약을 구매하면, **예약 주문** 및 **예약** 이라는 두 가지 개체가 생성됩니다.

구매 시 예약 주문에 하나의 예약이 포함됩니다. 분할, 병합, 부분 환불 또는 교환 등의 작업은 **예약 주문** 에 새 예약을 생성합니다.

예약 주문을 보려면 **Reservations** 로 이동하여 예약을 선택한 다음, **예약 주문 ID** 를 선택합니다.

![예약 주문 ID를 보여주는 예약 주문 세부 정보의 예 ](./media/manage-reserved-vm-instance/reservation-order-details.png)

예약은 예약 주문에서 권한을 상속합니다. 예약을 교환하거나 환불하려면 예약 주문에 사용자를 추가해야 합니다.

## <a name="change-the-reservation-scope"></a>예약 범위 변경

 예약 할인은 예약과 일치하고 예약 범위에서 실행되는 가상 머신, SQL 데이터베이스, Azure Cosmos DB 또는 기타 리소스에 적용됩니다. 청구 컨텍스트는 예약을 구입하는 데 사용되는 구독에 따라 달라집니다.

예약의 범위를 업데이트하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성** 을 선택합니다.
5. 범위를 변경합니다.

공유 범위에서 단일 범위로 변경하는 경우, 자신이 소유자인 구독만 선택할 수 있습니다. 동일한 청구 컨텍스트 내에서 예약된 구독만을 선택할 수 있습니다.

범위는 종량제 요금을 사용하는 개별 구독(MS-AZR-0003P 또는 MS-AZR-0023P 제안), 엔터프라이즈 제안 MS-AZR-0017P 또는 MS-AZR-0148P 또는 CSP 구독 유형에만 적용됩니다.

## <a name="who-can-manage-a-reservation-by-default"></a>기본적으로 예약을 관리할 수 있는 사용자

기본적으로 다음 사용자는 예약을 보고 관리할 수 있습니다.

- 예약한 사람과 청구 구독 계정 소유자는 예약 구매에 대한 Azure RBAC 액세스 권한을 얻습니다.
-  기업계약 및 Microsoft 고객 계약 청구 기여자는 Cost Management + Billing > 예약 트랜잭션 > 파란색 배너를 선택하여 모든 예약을 관리할 수 있습니다.

다른 사용자가 예약을 관리할 수 있게 하려면 다음 두 가지 옵션을 사용하면 됩니다.

- 예약 주문의 리소스 범위에 있는 사용자에게 소유자 역할을 할당하여 개별 예약 주문에 대한 액세스 관리를 위임합니다. 제한된 액세스 권한을 부여하려면 다른 역할을 선택합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

- 기업계약 또는 Microsoft 고객 계약에 사용자를 청구 관리자로 추가합니다.
    - 기업계약의 경우 _엔터프라이즈 관리자_ 역할이 있는 사용자를 추가하여 기업계약에 적용되는 모든 예약 주문을 보고 관리합니다. _엔터프라이즈 관리자(읽기 전용)_ 역할이 있는 사용자는 예약만 볼 수 있습니다. 부서 관리자 및 계정 소유자는 액세스 제어(IAM)를 사용하여 명시적으로 추가하지 _않는 한_ 예약을 볼 수 없습니다. 자세한 내용은 [Azure Enterprise 역할 관리](../manage/understand-ea-roles.md)를 참조하세요.

        _엔터프라이즈 관리자는 예약 주문의 소유권을 가질 수 있으며, 액세스 제어(IAM)를 사용하여 예약에 다른 사용자를 추가할 수 있습니다._
    - Microsoft 고객 계약의 경우 청구 프로필 소유자 역할 또는 청구 프로필 기여자 역할을 가진 사용자는 청구 프로필을 사용하여 이루어진 모든 예약 구매를 관리할 수 있습니다. 청구 프로필 리더와 청구서 관리자는 청구 프로필로 지불되는 모든 예약을 볼 수 있습니다. 그러나 예약을 변경할 수는 없습니다.
    자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

### <a name="how-billing-administrators-view-or-manage-reservations"></a>청구 관리자가 예약을 보거나 관리하는 방법

청구 관리자는 다음 단계를 수행하여 모든 예약과 예약 트랜잭션을 보고 관리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Cost Management + Billing** 으로 이동합니다.
    - EA 관리자인 경우 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음, 청구 범위 목록에서 하나를 선택합니다.
    - Microsoft 고객 계약 청구 프로필 소유자인 경우 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 하나를 선택합니다.
2. 왼쪽 메뉴에서 **제품 + 서비스** > **예약** 을 선택합니다.
3. EA 등록 또는 청구 프로필에 대한 전체 예약 목록이 표시됩니다.
4. 청구 관리자는 예약을 선택한 다음, 표시되는 창에서 **액세스 권한 부여** 를 선택하여 예약 소유권을 가져올 수 있습니다.

## <a name="change-billing-subscription-for-an-azure-reservation"></a>Azure Reservation에 대한 청구 구독 변경

예약을 구매한 후에는 청구 구독을 변경할 수 없습니다. 구독을 변경하려면 교환 프로세스를 사용하여 예약에 적합한 청구 구독을 설정합니다.

## <a name="split-a-single-reservation-into-two-reservations"></a>단일 예약을 두 개의 예약으로 분할

 한 예약 내에서 둘 이상의 리소스 인스턴스를 구입한 후에는 예약 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 기본적으로 모든 인스턴스에는 단일 구독, 리소스 그룹 또는 공유 중 하나의 범위가 지정됩니다. 10개의 VM 인스턴스에 대한 예약을 구입하고, 범위를 구독 A로 지정했다고 가정해 보겠습니다. 이제 7개의 VM 인스턴스에 대한 범위를 구독 A로 변경하고 남은 3개를 구독 B로 변경하려고 합니다. 예약을 분할하면 이 작업을 수행할 수 있습니다. 예약을 분할하면 원래 ReservationID가 취소되고 두 개의 새로운 예약이 생성됩니다. 분할은 예약 순서에 영향을 주지 않습니다. 분할된 새 상업적 트랜잭션이 없으며 새 예약의 종료 날짜는 분할된 예약과 동일합니다.

 PowerShell, CLI 또는 API를 통해 예약을 두 개로 분할할 수 있습니다.

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell을 사용하여 예약 분할

1. 다음 명령을 실행하여 예약 주문 ID를 가져옵니다.

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 예약의 세부 정보를 가져옵니다.

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 예약을 두 개로 분할하고 인스턴스를 배포합니다.

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 다음 명령을 실행하여 범위를 업데이트할 수 있습니다.

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Reserved VM Instances에 대한 최적화 설정 변경

 Reserved VM Instance를 구매하는 경우 인스턴스 크기 유연성 또는 용량 우선 순위를 선택합니다. 인스턴스 크기 유연성은 동일한 [VM 크기 그룹](../../virtual-machines/reserved-vm-instance-size-flexibility.md)의 다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포에 가장 중요한 데이터 센터 용량을 지정합니다. 이 옵션을 사용하면 필요할 때 VM 인스턴스를 보다 확실히 시작할 수 있게 됩니다.

기본적으로 예약의 범위를 공유할 경우 인스턴스 크기 유연성이 켜집니다. 데이터 센터 용량은 VM 배포에서 우선적으로 고려되지 않습니다.

범위가 단일인 예약의 경우 VM 인스턴스 크기 유연성 대신 용량 우선 순위에 대한 예약을 최적화할 수 있습니다.

예약에 대한 최적화 설정을 업데이트하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성** 을 선택합니다.
  ![구성 항목을 보여주는 예제](./media/manage-reserved-vm-instance/add-product03.png)
5. **다음에 맞게 최적화** 설정을 변경합니다.
  ![최적화 설정을 보여주는 예제](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>예약 사용 최적화

Azure 예약 절감은 리소스를 지속적으로 사용하는 경우에만 발생합니다. 예약 구매 시, 1년 또는 3년 동안 기본적으로 100% 가능한 리소스 사용에 대한 비용을 지불합니다. 최대로 사용하고 절감할 수 있도록 예약을 최대화해보세오. 다음 섹션에서는 예약을 모니터링하고 사용을 최적화하는 방법을 설명합니다.

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure Portal에서 예약 사용 보기

예약 사용량을 보는 한 가지 방법은 Azure Portal입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > [**Reservations**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)를 선택하고 예약에 대한 **사용률(%)** 을 적어둡니다.  
  ![예약 목록을 보여주는 이미지](./media/manage-reserved-vm-instance/reservation-list.png)
3. 예약을 선택합니다.
4. 시간별 예약 사용 추세를 검토합니다.
  ![예약 사용을 보여주는 이미지](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>API를 사용하여 예약 사용 보기

EA(기업계약) 고객인 경우 조직에서 예약을 사용하는 방법을 프로그래밍 방식으로 볼 수 있습니다. 사용 현황 데이터를 통해 사용하지 않는 예약을 알 수 있습니다. 예약 요금을 검토할 때는 데이터가 실제 비용과 분할 상환 비용으로 구분된다는 점에 유의하세요. 실제 비용은 월별 요금 청구를 조정하는 데이터를 제공합니다. 여기에는 예약 구매 비용 및 예약 적용 세부 정보도 포함됩니다. 분할 상환 비용은 예약 사용에 대한 실효 가격이 비례 배분된 것을 제외한 실제 비용과 같습니다. 사용하지 않은 예약 시간은 분할상환 비용 데이터에 표시됩니다. EA 고객의 사용 현황 데이터에 대한 자세한 내용은 [기업 계약 예약 비용 및 사용량 가져오기](understand-reserved-instance-usage-ea.md)를 참조하세요.

다른 구독 유형의 경우 API [예약 요약 - 예약 주문 및 예약별 목록](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)을 사용하세요.

### <a name="optimize-your-reservation"></a>예약 최적화

조직의 예약이 충분히 사용되지 않는 경우:

- 조직에서 생성한 가상 머신이 예약에 해당하는 VM 크기와 일치하는지 확인합니다.
- 인스턴스 크기 유연성이 켜져 있는지 확인합니다. 자세한 내용은 [예약 관리 - 예약 VM 인스턴스에 대한 최적화 설정 변경](#change-optimize-setting-for-reserved-vm-instances)을 참조하세요.
- 예약이 보다 광범위하게 적용되도록 범위를 _공유_ 로 변경합니다. 자세한 내용은 [예약 범위 변경](#change-the-reservation-scope)을 참조하세요.
- 사용하지 않는 수량을 교환합니다. 자세한 내용은 [취소 및 교환](#cancel-exchange-or-refund-reservations)을 참조하세요.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
 - [예약 사용률 보기](reservation-utilization.md)
 - [교환 및 환불](exchange-and-refund-azure-reservations.md)
 - [예약 갱신](reservation-renew.md)
 - [테넌트 간 전송](troubleshoot-reservation-transfers-between-tenants.md)
 - [Azure 로그에서 예약 구매자 찾기](find-reservation-purchaser-from-logs.md)
