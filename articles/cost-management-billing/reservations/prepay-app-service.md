---
title: 예약 용량으로 Azure App Service 비용 절감
description: Azure App Service Premium v3 및 Premium v2 예약 인스턴스 및 격리 인지세에 대한 비용을 절감할 수 있는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 09/20/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 305849e104c3d8fb48b5d661d3d21f589fd69516
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664431"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Azure App Service 예약 인스턴스를 사용하여 비용 절감

이 문서에서는 Azure App Service 예약 인스턴스를 사용하여 Premium v3 및 Premium v2 인스턴스 및 격리 인지세 비용을 절감할 수 있는 방법에 대해 설명합니다.

## <a name="save-with-premium-v3-reserved-instances"></a>Premium v3 예약 인스턴스를 사용하여 비용 절감

Azure App Service Premium v3 예약 인스턴스에 커밋하면 비용을 절감할 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 인스턴스의 수에 자동으로 적용됩니다. 할인을 받기 위해 예약을 인스턴스에 할당할 필요가 없습니다.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>구매하기 전에 적절한 예약 인스턴스 크기 결정

예약을 구매하기 전에 필요한 Premium v3 예약 인스턴스의 크기를 결정해야 합니다. 다음 섹션은 적절한 Premium v3 예약 인스턴스 크기를 결정하는 데 도움이 됩니다.

### <a name="use-reservation-recommendations"></a>예약 권장 사항 사용

예약 권장 사항을 사용하여 구매해야 하는 예약을 결정할 수 있습니다.

- 구매 권장 사항 및 권장 수량은 Azure Portal에서 Premium v3 예약 인스턴스를 구매할 때 표시됩니다.
- Azure Advisor는 개별 구독에 대한 구매 권장 사항을 제공합니다.
- API를 사용하여 공유 범위 및 단일 구독 범위 모두에 대한 구매 권장 사항을 모두 가져올 수 있습니다. 자세한 내용은 [기업 고객을 위한 예약 인스턴스 구매 권장 사항](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)을 참조하세요.
- EA(기업계약) 및 MCA(Microsoft 고객 계약) 고객의 경우 공유 및 단일 구독 범위에 대한 구매 권장 사항은 [Azure Consumption Insights Power BI 콘텐츠 팩](/power-bi/service-connect-to-azure-consumption-insights)에서 사용할 수 있습니다.

### <a name="analyze-your-usage-information"></a>사용량 정보 분석

사용량 정보를 분석하여 구매해야 하는 예약을 결정합니다. 사용량 데이터는 사용량 파일 및 API에서 사용할 수 있습니다. 모두 사용하여 구매할 예약을 결정합니다. 매일 사용량이 많은 Premium v3 인스턴스를 확인하여 구매할 예약 수량을 결정합니다.

사용량 파일에는 청구 기간 및 일별 사용량을 기준으로 요금이 표시됩니다. 사용량 파일을 다운로드하는 방법에 대한 자세한 내용은 [Azure 사용량 및 요금 보기 및 다운로드](../understand/download-azure-daily-usage.md)를 참조하세요. 그런 다음, 사용량 파일 정보를 사용하여 [구매할 예약을 결정](determine-reservation-purchase.md)할 수 있습니다.

## <a name="buy-a-premium-v3-reserved-instance"></a>Premium v3 예약 인스턴스 구매

예약된 Premium v3 예약 인스턴스는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](prepare-buy-reservation.md)로 처리할 수 있습니다. Premium v3 예약 인스턴스 구매에 적용되는 요구 사항은 다음과 같습니다.

- 하나 이상의 EA 구독 또는 종량제 요금이 적용되는 구독에 대한 소유자 역할에 속해야 합니다.
- EA 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구매하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택하여 새 예약을 구매한 다음, **인스턴스** 를 클릭합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 Premium v3 예약 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받는 Premium v3 예약 인스턴스의 실제 수는 선택한 범위와 수량에 따라 달라집니다.

EA 계약이 있는 경우 **추가 옵션** 을 사용하여 인스턴스를 빠르게 더 추가할 수 있습니다. 이 옵션은 다른 구독 유형에 사용할 수 없습니다.

| **필드** | **설명** |
|---|---|
| Subscription | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다. 종량제 요금이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다. |
| 범위 | 예약 범위에는 하나의 구독 또는 여러 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>**단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다. </li><li>**단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li><li>**관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.</li></ul> |
| 지역 | 예약이 적용되는 Azure 지역입니다. |
| Premium v3 예약 인스턴스 크기 | Premium v3 예약 인스턴스의 크기입니다. |
| 용어 | 1년 또는 3년입니다. HBv2 Premium v3 예약 인스턴스에만 사용할 수 있는 5년 기간도 있습니다. |
| 수량 | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Premium v3 예약 인스턴스의 수입니다. 예를 들어 미국 동부에서 10개의 Standard\_D2 Premium v3 예약 인스턴스를 실행하는 경우 실행 중인 모든 Premium v3 예약 인스턴스에 대한 혜택을 극대화하려면 수량을 10으로 지정합니다. |

## <a name="buy-an-isolated-v2-reserved-instance"></a>Isolated v2 예약 인스턴스 구매

예약된 Isolated v2 예약 인스턴스는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](prepare-buy-reservation.md)로 처리할 수 있습니다. Isolated v2 예약 인스턴스 구매에 적용되는 요구 사항은 다음과 같습니다.

- 하나 이상의 EA 구독 또는 종량제 요금이 적용되는 구독에 대한 소유자 역할에 속해야 합니다.
- EA 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구매하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약** 을 선택합니다.
3. **추가** 를 선택하여 새 예약을 구매한 다음, **인스턴스** 를 클릭합니다.
4. 필수 필드를 입력합니다. 선택한 특성과 일치하는 Isolated v2 예약 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받는 Isolated v2 예약 인스턴스의 실제 수는 선택한 범위와 수량에 따라 달라집니다.

EA 계약이 있는 경우 **추가 옵션** 을 사용하여 인스턴스를 빠르게 더 추가할 수 있습니다. 이 옵션은 다른 구독 유형에 사용할 수 없습니다.

| **필드** | **설명** |
| --- | --- |
| Subscription | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다. 종량제 요금이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다. |
| 범위 | 예약 범위에는 하나의 구독 또는 여러 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:<UL><LI>**단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li><li>**관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.</li></ul> |
| 지역 | 예약이 적용되는 Azure 지역입니다. |
| Isolated v2 예약 인스턴스 크기 | Isolated v2 예약 인스턴스의 크기입니다. |
| 용어 | 1년 또는 3년입니다. HBv2 Isolated v2 예약 인스턴스에만 사용할 수 있는 5년 기간도 있습니다. |
| 수량 | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Isolated v2 예약 인스턴스의 수입니다. 예를 들어 미국 동부에서 10개의 Standard\_D2 Isolated v2 예약 인스턴스를 실행하는 경우 실행 중인 모든 Isolated v2 예약 인스턴스에 대한 혜택을 극대화하려면 수량을 10으로 지정합니다. |

## <a name="save-with-isolated-stamp-fees"></a>격리 인지세를 사용하여 비용 절감

3년간의 스탬프 사용량에 대한 예약을 커밋하여 Azure App Service 격리 인지세 비용을 절감할 수 있습니다. 격리 인지세 예약 용량을 구매하려면 스탬프를 배포할 Azure 지역 및 구매할 스탬프 수를 선택해야 합니다.

예약을 구매하는 경우 예약 특성과 일치하는 격리 인지세 사용에 대해 종량제 요금이 더 이상 청구되지 않습니다. 예약은 예약 용량 범위 및 지역과 일치하는 격리 스탬프 수에 자동으로 적용됩니다. 격리 스탬프에는 예약을 할당할 필요가 없습니다. 예약은 작업자에게 적용되지 않으므로 스탬프와 연결된 다른 모든 리소스에는 별도로 요금이 청구됩니다.

예약 용량이 만료되면 격리 스탬프가 계속 실행되지만 종량제 요금이 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>구매에 적절한 격리 인지세 예약 결정

예약을 구매하면 향후 3년 동안 예약된 수량을 사용하도록 커밋하는 것입니다. 사용량 데이터를 확인하여 지속적으로 사용 중이고 나중에 사용할 수 있는 App Service 격리 스탬프 수를 확인하세요.

또한 격리 스탬프에서 Linux 또는 Windows 미터를 내보내는 방법을 이해해야 합니다.

- 기본적으로 비어 있는 격리 스탬프는 Windows 스탬프 미터를 내보냅니다. 예를 들어 배포된 작업자가 없는 경우입니다. Windows 작업자가 스탬프에 배포된 경우에는 계속해서 이 미터를 내보냅니다.
- Linux 작업자를 배포하는 경우 미터가 Linux 스탬프 미터로 변경됩니다.
- Linux 및 Windows 작업자가 모두 배포된 경우 스탬프는 Windows 미터를 내보냅니다.

따라서 스탬프 미터는 스탬프 수명 동안 Windows와 Linux 사이에서 변경될 수 있습니다.

스탬프에 Windows 작업자가 하나 이상 있는 경우 Windows 스탬프 예약을 구매하세요. Linux 스탬프 예약을 구매해야 하는 유일한 경우는 스탬프에 Linux 작업자 _만_ 있게 하려는 경우입니다.

## <a name="buy-isolated-stamp-reserved-capacity"></a>격리 스탬프 예약 용량 구매

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)에서 격리 스탬프 예약 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](./prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 용량을 구매하려면 종량제 요금이 적용되는 하나 이상의 기업 구독 또는 개별 구독에 대한 소유자 역할이 있어야 합니다.

- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 활성화해야 합니다. 이 설정을 비활성화하려면 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 판매 담당자가 Azure Synapse Analytics 예약 용량을 구매할 수 있습니다.

**구매하려면 다음을 수행합니다.**

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)로 이동합니다.
1. 구독을 선택합니다. 예약 용량의 요금을 지불하는 데 사용되는 구독을 선택하려면 **구독** 목록을 사용하세요. 구독의 결제 방법에는 예약 용량에 대한 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P) 또는 CSP 구독입니다.
    - 엔터프라이즈 구독의 경우 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다.
    - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
1. 구독 범위를 선택하려면 **범위** 를 선택합니다.
    - **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.
    - **관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.
1. **지역** 을 선택하여 예약 용량에 포함되는 Azure 지역을 선택하고 카트에 예약을 추가합니다.
1. 격리된 계획 유형을 선택한 후 **선택** 을 클릭합니다.  
    ![예 ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. 예약할 App Service 격리 스탬프 수량을 입력합니다. 예를 들어 수량 3은 지역에 예약된 3개의 스탬프를 제공합니다. **다음: 검토 + 구매** 를 클릭합니다.
1. 검토하고 **지금 구매** 를 클릭합니다.

구매 후에는 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동하여 구매 상태를 확인하고 언제든지 모니터링할 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="discount-application-shown-in-usage-data"></a>사용량 데이터에 표시되는 할인 애플리케이션

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 사용량 데이터는 각 예약에서 각 스탬프 인스턴스의 예약 할인을 보여줍니다.

사용량 데이터에 예약 할인이 표시되는 방식에 대한 자세한 내용은 [기업계약 예약 비용 및 사용량 확인](understand-reserved-instance-usage-ea.md)(EA(기업계약) 고객인 경우)을 참조하세요. 그렇지 않은 경우 [종량제 요금을 사용하는 개별 구독에 대한 Azure 예약 사용량 이해](understand-reserved-instance-usage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure App Service 격리 스탬프 예약 할인이 적용되는 방식 이해](reservation-discount-app-service.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
