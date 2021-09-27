---
title: Azure VMware Solution의 예약 인스턴스
description: Azure VMware Solution에 대한 예약 인스턴스를 구매하는 방법을 알아봅니다. 예약 인스턴스에는 사용자 사용량의 컴퓨팅 부분만 해당되며 소프트웨어 라이선스 비용이 포함됩니다.
ms.topic: how-to
ms.date: 05/13/2021
ms.openlocfilehash: edb4136039624ceef8bd6a419a8b617798b611ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643171"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware Solution을 사용하여 비용 절감

[Azure VMware Solution](introduction.md)의 예약 인스턴스를 커밋하면 비용이 절약됩니다. 예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 Azure VMware Solution 호스트에 자동으로 적용됩니다. 또한 예약 인스턴스 구매에는 사용자 사용량의 컴퓨팅 부분만 해당되며 소프트웨어 라이선스 비용이 포함됩니다. 

## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

일부 예외를 제외하고 예약 인스턴스를 사용할 수 있습니다.

-   **클라우드** - 예약은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) 페이지에 나열된 지역에서만 가능합니다.

-   **불충분한 할당량** - 단일/공유 구독으로 범위가 지정된 예약에는 새 예약 인스턴스에 대한 구독에서 사용 가능한 호스트 할당량이 있어야 합니다. [할당량 증가 요청을 만들어](request-host-quota-azure-vmware-solution.md) 이 문제를 해결할 수 있습니다.

-   **제품 적격성** - Microsoft와의 [Azure EA(기업 계약)](../cost-management-billing/manage/ea-portal-agreements.md)가 필요합니다.

-   **용량 제한** - 드문 경우, Azure는 지역의 낮은 용량으로 인해 Azure VMware Solution 호스트 SKU의 하위 집합에 대한 새로운 예약 구매를 제한합니다.

## <a name="buy-a-reservation"></a>예약 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 Azure VMware Solution 호스트 인스턴스의 예약 인스턴스를 구입할 수 있습니다.

예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다.

예약 전용 호스트 인스턴스 구매에 적용되는 요구 사항은 다음과 같습니다.

-   하나 이상의 EA 구독 또는 종량제 요금이 적용되는 구독에 대한 *소유자* 역할에 속해야 합니다.

-   EA 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 옵션을 사용하도록 설정하려면 이 구독에 대한 EA 관리자여야 합니다.

-   CSP(클라우드 솔루션 공급자) Azure 플랜에 따른 구독의 경우 파트너는 Azure Portal에서 고객의 예약 인스턴스를 구매해야 합니다. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>EA 구독을 위한 예약 인스턴스 구매

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** > **예약** 을 선택합니다.

3. **지금 구매** 를 선택한 다음 **Azure VMware Solution** 을 선택합니다.

4. 필수 필드를 입력합니다. 실행 중인 Azure VMware Solution 호스트와 일치하는 선택된 특성은 예약 할인에 적합합니다.  특성에는 SKU, 지역(해당 하는 경우) 및 범위가 포함됩니다. 예약 범위는 예약 절감액이 적용될 위치를 선택합니다.

   EA 계약이 있는 경우 **추가 옵션** 을 사용하여 인스턴스를 빠르게 추가할 수 있습니다. 이 옵션은 다른 구독 유형에 사용할 수 없습니다.

   | 필드        |  Description |
   | ------------ | ------------ |
   | Subscription | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P), Microsoft 고객 계약, 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)이어야 합니다. 가능한 경우, 요금은 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 요금이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다. |
   | 범위        | 예약 범위에는 하나의 구독 또는 여러 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:<br><ul><li><b>단일 리소스 그룹 범위</b> - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li><b>단일 구독 범위</b> - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li><b>공유 범위</b> - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 따라서 청구 범위는 종량제 요금이 적용되는 개별 구독의 경우 계정 관리자가 만든 모든 적격 구독입니다.<li>**관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.</li></li></ul>       |
   | 지역       | 예약이 적용되는 Azure 지역입니다.   |
   | 호스트 크기    | AV36    |
   | 용어         | 1년 또는 3년입니다.  |
   | 수량     | 예약 내에서 구매할 인스턴스 수입니다. 이 수량은 청구 할인을 받을 수 있는 실행 중인 Azure VMware Solution 호스트의 수입니다.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>CSP 구독에 대해 예약 인스턴스 구입

고객을 위해 예약 인스턴스를 구매하려는 CSP는 [파트너 센터 설명서](/partner-center/azure-plan-manage)의 **AOBO(대신 관리)** 절차를 사용해야 합니다. 자세한 내용은 [AOBO(대신 관리)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 비디오를 시청하세요.

1. [파트너 센터](https://partner.microsoft.com)에 로그인합니다.

2. **CSP** 를 선택하여 **고객** 영역에 액세스합니다.

3. 고객 세부 정보를 확장하고 **Microsoft Azure 관리 포털** 을 선택합니다. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft Azure 관리 포털이 선택된 Microsoft 파트너 센터 고객 영역을 보여주는 스크린샷." lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Azure Portal에서 **모든 서비스** > **예약** 을 선택합니다.

5. **지금 구매** 를 선택한 다음 **Azure VMware Solution** 을 선택합니다.

   :::image type="content" source="media/reserved-instances/csp-buy-reserved-instance-azure-portal.png" alt-text="Microsoft Azure Portal에서 Azure VMware Solution 예약을 구매할 수 있는 위치를 보여주는 스크린샷." lightbox="media/reserved-instances/csp-buy-reserved-instance-azure-portal.png":::

6. 필수 필드를 입력합니다. 실행 중인 Azure VMware Solution 호스트와 일치하는 선택된 특성은 예약 할인에 적합합니다.  특성에는 SKU, 지역(해당 하는 경우) 및 범위가 포함됩니다. 예약 범위는 예약 절감액이 적용될 위치를 선택합니다.

   | 필드        |  Description |
   | ------------ | ------------ |
   | 구독 | 예약 자금을 지원하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형이 적합한 유형이어야 합니다. 이 경우에는 CSP 구독입니다.|
   | 범위        | 예약 범위에는 하나의 구독 또는 여러 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:<br><ul><li><b>단일 리소스 그룹 범위</b> - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li><b>단일 구독 범위</b> - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li><b>공유 범위</b> - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 따라서 청구 범위는 종량제 요금이 적용되는 개별 구독의 경우 계정 관리자가 만든 모든 적격 구독입니다.<li>**관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.</li></li></ul>       |
   | 지역       | 예약이 적용되는 Azure 지역입니다.   |
   | 호스트 크기    | AV36    |
   | 용어         | 1년 또는 3년입니다.  |
   | 수량     | 예약 내에서 구매할 인스턴스 수입니다. 이 수량은 청구 할인을 받을 수 있는 실행 중인 Azure VMware Solution 호스트의 수입니다.     |

고객을 위해 구매한 예약을 보는 방법에 대한 자세한 내용은 [CSP(클라우드 솔루션 공급자)로 Azure 예약 보기](../cost-management-billing/reservations/how-to-view-csp-reservations.md) 문서를 참조하세요.

## <a name="usage-data-and-reservation-usage"></a>사용량 데이터 및 예약 사용량

예약 할인을 받을 수 있는 사용량의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 Azure VMware Solution 인스턴스를 확인할 수 있습니다.

사용량 데이터에 예약 할인이 표시되는 방법에 대한 자세한 내용은 다음과 같습니다.

- EA 고객의 경우 [기업 등록을 위한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.
- 개별 구독의 경우 [종량제 구독에 대한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매 후 예약을 다음과 같이 변경할 수 있습니다.

-   예약 범위 업데이트

-   인스턴스 크기 유연성(해당하는 경우)

-   소유권

또한 예약을 더 작은 청크로 분할하거나 예약을 병합할 수도 있습니다. 분할 또는 병합으로 인해 새로 상업용 트랜잭션이 발생하거나 예약 종료일이 변경되지 않습니다.

CSP 관리 예약에 대한 자세한 내용은 [파트너 센터, Azure Portal 또는 API를 사용하여 고객에게 Microsoft Azure 예약 판매](/partner-center/azure-reservations)를 참조하세요.



>[!NOTE]
>예약을 구매한 후에는 다음과 같은 유형의 변경을 직접 수행할 수 없습니다.
>
> - 기존 예약 영역
> - SKU
> - 수량
> - Duration
>
>그거나 변경을 원하는 경우 예약을 ‘교환’할 수는 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

CSP는 고객을 위해 구매한 특정 제한 사항과 함께 취소, 교환 또는 환불 예약을 수행할 수 있습니다. 자세한 내용은 [고객을 위한 Microsoft Azure 예약 관리, 취소, 교환 또는 환불](/partner-center/azure-reservations-manage)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution의 예약 인스턴스를 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- [Azure VMware Solution 평가 작성](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [Azure VMware Solution용 DHCP 구성](configure-dhcp-azure-vmware-solution.md).
- [Azure VMware Solution에서 Azure 네이티브 서비스 통합](integrate-azure-native-services.md)
 
