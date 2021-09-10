---
title: Azure Dedicated Host를 선불 구입하여 비용 절감
description: 컴퓨팅 비용을 절약하기 위해 Azure Dedicated Host 예약 인스턴스를 구입하는 방법에 대해 알아봅니다.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 464ffc8b5d4d04aeb7e4013f5b25d240d5ee9b06
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688966"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Azure Dedicated Host 예약을 사용하여 비용 절감

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Azure Dedicated Host의 예약 인스턴스로 커밋하면 비용을 절감할 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 전용 호스트의 수에 자동으로 적용됩니다. 할인을 받기 위해 예약을 전용 호스트에 할당할 필요가 없습니다. 예약 인스턴스를 구입하면 사용량 중 컴퓨팅 부분만 보장되며 소프트웨어 라이선스 비용이 포함됩니다. [가상 머신용 Azure Dedicated Host 개요](./dedicated-hosts.md)를 참조하세요.

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>구입 전 적절한 전용 호스트 SKU 확인


예약 구입 전에 필요한 전용 호스트를 결정해야 합니다. SKU는 전용 호스트에 제공되는 VM 시리즈와 유형에 따라 정의됩니다. 

먼저 VM 시리즈를 확인하기 위해 [Windows 가상 머신](./sizes.md) 또는 [Linux](./sizes.md)에서 지원하는 크기를 검토합니다.

다음으로, 해당 VM 시리즈를 Azure Dedicated Host에서 지원하는지 확인합니다. [Azure Dedicated Host 가격 책정](https://aka.ms/ADHPricing) 페이지에는 전용 호스트 SKU의 전체 목록, CPU 정보, 다양한 가격 책정 옵션(예약 인스턴스 포함)이 있습니다.

선택한 VM 시리즈를 지원하는 여러 SKU(다른 유형 포함)를 찾을 수 있습니다. 호스트의 용량(vCPU 개수)을 비교하여 가장 적합한 SKU를 확인합니다. 동일한 VM 시리즈를 지원하는 여러 전용 호스트 SKU(예: DSv3_Type1 및 DSv3_Type2)에는 예약을 적용할 수 있지만 VM 시리즈가 서로 다른 경우(예: DSv3 및 ESv3)에는 적용할 수 없습니다.



## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 인스턴스는 대부분의 전용 호스트 크기에서 이용할 수 있지만 몇 가지 예외가 있습니다.

다음과 같은 경우에는 예약 할인이 적용되지 않습니다.

- **클라우드** - 예약은 독일 또는 중국 지역에서 구매하는 데 사용할 수 없습니다.

- **할당량 부족** - 단일 구독으로 범위가 한정되는 예약은 vCPU 할당량을 새 예약 인스턴스에 대한 구독에서 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 DSv3 시리즈에 대한 할당량 한도가 10개 vCPU인 경우 이 시리즈를 지원하는 전용 호스트에 대한 예약을 구입할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM 및 전용 호스트가 포함됩니다. [할당량 증가 요청을 만들어](../azure-portal/supportability/resource-manager-core-quotas-request.md) 이 문제를 해결할 수 있습니다.

- **용량 제한** - 드문 경우, Azure는 지역의 낮은 용량으로 인해 전용 호스트 SKU의 하위 집합에 대한 새로운 예약 구매를 제한합니다.

## <a name="buy-a-reservation"></a>예약 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 Azure Dedicated Host 인스턴스의 예약 인스턴스를 구입할 수 있습니다.

예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 Dedicated Host 인스턴스 구매에 적용되는 요구 사항은 다음과 같습니다.

- 하나 이상의 EA 구독 또는 종량제 요금이 적용되는 구독에 대한 소유자 역할에 속해야 합니다.

- EA 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구매하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **모든 서비스** \> **예약** 을 선택합니다.

3. **추가** 를 선택하여 새 예약을 구입한 다음 **Dedicated Host** 를 클릭합니다.

4. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 Dedicated Host 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받는 실제 Dedicated Host 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

EA 계약이 있는 경우 **추가 옵션** 을 사용하여 인스턴스를 빠르게 더 추가할 수 있습니다. 이 옵션은 다른 구독 유형에 사용할 수 없습니다.

| **필드**           | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscription        | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. 가능한 경우, 요금은 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 요금이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다. |
| 범위               | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 지역              | 예약 범위에 해당하는 Azure 지역입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedicated Host 크기 | Dedicated Host 인스턴스의 크기입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 용어                | 1년 또는 3년입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 수량            | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Dedicated Host 인스턴스의 수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.

- **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.

- **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

## <a name="usage-data-and-reservation-utilization"></a>사용량 현황 데이터 및 예약 사용률

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 예약 할인을 받은 VM 인스턴스를 예약별로 확인할 수 있습니다.

EA 고객인 경우 사용량 현황 데이터에 예약 할인이 표시되는 방식에 대한 자세한 내용은 [기업 등록에 대한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조하세요. 개별 구독이 있는 경우, [종량제 구독에 대한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조하세요.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트

- 인스턴스 크기 유연성(해당하는 경우)

- 소유권

예약을 분할하거나 분할된 예약을 병합할 수 있습니다. 분할 또는 병합으로 인해 새로 상업용 트랜잭션이 발생하거나 예약 종료일이 변경되지 않습니다.

구매한 후 다음과 같은 유형의 변경은 직접 수행할 수 없습니다.

- 기존 예약 영역

- SKU

- 수량

- Duration

그거나 변경을 원하는 경우 예약을 ‘교환’할 수는 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조하세요.

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Azure Dedicated Host 사용](./dedicated-hosts.md)

- [Dedicated Host 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure에서 Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [예약 할인이 적용되는 방식 이해](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [예약에 포함되지 않는 Windows 소프트웨어 비용](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)
