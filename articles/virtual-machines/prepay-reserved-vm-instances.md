---
title: 비용 절감을 위한 Azure 가상 머신 선불
description: 컴퓨팅 비용을 절약하기 위해 Azure Reserved Virtual Machine Instances를 구입하는 방법에 대해 알아봅니다.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 51608ccc0a05d1c1aac739b48865815cbafc373e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216063"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances에 대한 비용 절감

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure reserved VM instance에 커밋하면 비용을 아낄 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 가상 머신 수에 자동으로 적용됩니다. 할인을 받기 위해 가상 머신에 예약을 할 필요가 없습니다. 예약 인스턴스 구매는 VM 사용량의 계산 부분에 대해서만 적용됩니다. Windows VM의 경우, 사용량 미터는 두 개의 개별 미터로 나뉩니다. Linux 미터와 같은 계산 미터와 Windows IP 미터입니다. 구매 시 확인할 수 있는 요금은 계산 비용에만 해당하는 것입니다. 요금에는 Windows 소프트웨어 비용이 들어가지 않습니다. 소프트웨어 비용에 대한 자세한 내용은 [Azure Reserved VM Instances에 포함되지 않는 소프트웨어 비용](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)을 참조하세요.

## <a name="determine-the-right-vm-size-before-you-buy"></a>구매하기 전에 적절한 VM 크기를 결정합니다.

예약 구입 전에 반드시 필요한 VM 크기를 결정해야 합니다. 다음 섹션은 적절한 VM 결정에 도움이 됩니다.

### <a name="use-reservation-recommendations"></a>예약 권장 사항 사용

예약 권장 사항을 사용하여 구매해야 하는 예약을 결정할 수 있습니다.

- 구매 권장 사항 및 권장 수량은 Azure Portal에서 VM 예약 인스턴스를 구매할 때 표시됩니다.
- Azure Advisor는 개별 구독에 대한 구매 권장 사항을 제공합니다.  
- API를 사용하여 공유 범위 및 단일 구독 범위 모두에 대한 구매 권장 사항을 모두 가져올 수 있습니다. 자세한 내용은 [기업 고객을 위한 예약 인스턴스 구매 권장 사항](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)을 참조하세요.
- EA(기업계약) 및 MCA(Microsoft 고객 계약) 고객의 경우 공유 및 단일 구독 범위에 대한 구매 권장 사항은 [Azure Consumption Insights Power BI 콘텐츠 팩](/power-bi/service-connect-to-azure-consumption-insights)에서 사용할 수 있습니다.

### <a name="services-that-get-vm-reservation-discounts"></a>VM 예약 할인을 가져오는 서비스

VM 예약은 VM 배포뿐만 아니라 여러 서비스에서 내보낸 VM 사용량에 적용될 수 있습니다. 예약 할인을 받는 리소스는 인스턴스 크기 유연성 설정에 따라 변경됩니다.

#### <a name="instance-size-flexibility-setting"></a>인스턴스 크기 유연성 설정

인스턴스 크기 유연성 설정은 예약 인스턴스 할인을 받는 서비스를 결정합니다.

설정이 on 또는 off인지 여부에 따라 예약 할인은 *ConsumedService* 가 `Microsoft.Compute`일 때 일치하는 VM 사용량에 자동으로 적용됩니다. 따라서 *ConsumedService* 값에 대한 사용량 데이터를 확인합니다. 일부 사례:

- 가상 머신
- 가상 머신 크기 집합
- 컨테이너 서비스
- Azure Batch 배포(사용자 구독 모드에서)
- AKS(Azure Kubernetes Service)
- Service Fabric

설정이 on일 때 예약 할인은 *ConsumedService* 가 다음 항목 중 하나에 해당하는 경우 일치하는 VM 사용량에 자동으로 적용됩니다.

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

사용량이 예약 할인에 적합한지 확인하려면 사용량 데이터의 *ConsumedService* 값을 확인합니다.

인스턴스 크기 유연성에 대한 자세한 내용은 [예약 VM 인스턴스에서 가상 머신 크기 유연성](reserved-vm-instance-size-flexibility.md)을 참조하세요.

### <a name="analyze-your-usage-information"></a>사용량 정보 분석

사용량 정보를 분석하여 구매해야 하는 예약을 결정합니다. 사용량 데이터는 사용량 파일 및 API에서 사용할 수 있습니다. 모두 사용하여 구매할 예약을 결정합니다. 매일 사용량이 많은 VM 인스턴스를 확인하여 구매할 예약 수량을 결정합니다. 사용량 데이터의 `Meter` 하위 범주와 `Product` 필드는 사용하지 않습니다. 해당 필드에서는 Premium Storage를 사용하는 VM 간의 크기를 구분하지 않습니다. 해당 필드를 사용해 예약 구매할 VM 크기를 결정하는 경우 잘못된 크기를 구매할 수 있습니다. 이런 경우에는 기대하는 예약 할인을 받지 못합니다. 적절한 VM 크기를 결정하려면 사용량 파일 또는 사용량 API의 `AdditionalInfo` 필드를 참조하세요.

사용량 파일에는 청구 기간 및 일별 사용량을 기준으로 요금이 표시됩니다. 사용량 파일을 다운로드하는 방법에 대한 자세한 내용은 [Azure 사용량 및 요금 보기 및 다운로드](../cost-management-billing/understand/download-azure-daily-usage.md)를 참조하세요. 그런 다음, 사용량 파일 정보를 사용하여 [구매할 예약을 결정](../cost-management-billing/reservations/determine-reservation-purchase.md)할 수 있습니다.

### <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 VM 인스턴스는 대부분의 VM 크기에서 이용할 수 있지만 몇 가지 예외가 있습니다. 예약 할인은 다음과 같은 VM에 적용되지 않습니다.

- **VM 시리즈** - A-시리즈, Av2-시리즈, or G-시리즈.

- **미리 보기 또는 프로모션 VM** - 미리 보기에 있거나 프로모션 미터를 사용하는 모든 VM 시리즈 또는 크기입니다.

- **클라우드** - 예약은 독일 또는 중국 지역에서 구매하는 데 사용할 수 없습니다.

- **할당량 부족** - 단일 구독으로 범위가 한정되는 예약은 vCPU 할당량을 새 RI에 대한 구독에서 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU인 경우 11개의 Standard_D1 인스턴스에 대해 예약을 구입할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM이 포함됩니다. 예를 들어, 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU이고 두 개의 standard_D1 인스턴스가 배포된 경우 이 구독에서 10개의 standard_D1 인스턴스에 대해 예약을 구입할 수 있습니다. [견적 증가 요청을 만들어](../azure-portal/supportability/regional-quota-requests.md) 문제를 해결할 수 있습니다.

- **용량 제한** - 드문 경우, Azure는 지역의 낮은 용량으로 인해 VM 크기의 하위 집합에 대한 새로운 예약 구매를 제한합니다.

## <a name="buy-a-reserved-vm-instance"></a>예약 VM 인스턴스 구입

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 예약 VM 인스턴스를 구입할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다.
예약 VM 인스턴스 구매에 적용되는 요구 사항은 다음과 같습니다.

- 하나 이상의 EA 구독 또는 종량제 요금이 적용되는 구독에 대한 소유자 역할에 속해야 합니다.
- EA 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

인스턴스를 구매하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** > **예약** 을 선택합니다.
1. **추가** 를 선택하여 새 예약을 구매한 다음 **가상 머신** 을 클릭합니다.
1. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 VM 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받을 실제 VM 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

EA 계약이 있는 경우 **추가 옵션** 을 사용하여 인스턴스를 빠르게 더 추가할 수 있습니다. 이 옵션은 다른 구독 유형에 사용할 수 없습니다.


| 필드      | Description|
|------------|--------------|
|Subscription|예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. 가능한 경우, 요금은 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다. 종량제 요금이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다.|    
|범위       |예약 범위에는 하나의 구독 또는 여러 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>**단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.</li><li>**공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. EA 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li><li>**관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.</li></ul>|
|지역    |예약이 적용되는 Azure 지역입니다.|    
|VM 크기     |VM 인스턴스의 크기입니다.|
|다음에 맞게 최적화     |VM 인스턴스 크기 유연성이 기본값으로 선택됩니다. 동일한 [VM 크기 그룹](reserved-vm-instance-size-flexibility.md)의 다른 VM에 예약 할인을 적용하려면 **고급 설정** 을 클릭하여 인스턴스 크기 유연성 값을 변경합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량에서 우선됩니다. 이를 통해 필요할 때 VM 인스턴스를 시작하는 기능을 더욱 신뢰할 수 있습니다. 용량 우선 순위는 예약 범위가 단일 구독일 때에 사용할 수 있습니다. |
|용어        |1년 또는 3년입니다. HBv2 VM에만 적용되는 5년 기한도 있습니다.|
|수량    |예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 VM 인스턴스의 수입니다. 예를 들어 미국 동부 지역에서 10개의 Standard_D2 VM을 실행 중인 경우 실행 중인 모든 VM에 대한 혜택을 극대화하려면 수량을 10으로 지정할 수 있습니다. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>사용량 데이터 및 예약 사용률

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

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Azure에서 Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)