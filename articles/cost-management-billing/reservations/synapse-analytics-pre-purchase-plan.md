---
title: 사전 구매 계획을 Azure Synapse Analytics 비용 최적화
description: SCU(Azure Synapse 커밋 단위)를 1년 동안 미리 예약할 때 Azure Synapse Analytics 비용을 절감하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/08/2021
ms.author: banders
ms.openlocfilehash: 031dc79bfabf03bfadf0c7e01171cbb72a15008e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008711"
---
# <a name="optimize-azure-synapse-analytics-costs-with-a-pre-purchase-plan"></a>사전 구매 계획을 Azure Synapse Analytics 비용 최적화

1년 동안 Azure Synapse 커밋 단위(SCU)를 미리 예약할 때 Azure Synapse Analytics 비용을 절감할 수 있습니다. 구매 기간 동안 언제든지 미리 구매한 SCU를 사용할 수 있습니다. VM과 달리, 사전 구매 단위는 시간 단위로 만료되지 않으며 구매 기간 동안 언제든지 사용합니다.

모든 Azure Synapse Analytics 사전 구매된 SCU에서 자동으로 차감됩니다. 사전 구매 할인을 받기 위해 Azure Synapse Analytics 작업 영역에 사전 구매 계획을 다시 배포하거나 할당할 필요가 없습니다.

## <a name="determine-the-right-size-to-buy"></a>구매할 올바른 크기 결정

synapse 사전 결제는 모든 Synapse 워크로드 및 계층에 적용됩니다. 사전 구매 계획을 선불 Synapse 커밋 단위 풀로 생각할 수 있습니다. 워크로드 또는 계층에 관계없이 사용량은 풀에서 차감됩니다. 컴퓨팅, 스토리지 및 네트워킹 등의 기타 요금은 별도로 청구됩니다.

Synapse 사전 결제 할인은 다음 제품의 사용량에 적용됩니다.

- Azure Synapse Analytics 전용 SQL 풀
- 관리되는 VNET Azure Synapse Analytics
- Azure Synapse Analytics Pipelines
- 서버리스 SQL 풀 Azure Synapse Analytics
- Azure Synapse Analytics 서버리스 Apache Spark 풀 - 메모리 최적화
- Azure Synapse Analytics Data Flow - 기본
- Azure Synapse Analytics Data Flow - Standard

사용 가능한 SCU 계층 및 가격 할인에 대한 자세한 내용은 다음 섹션에서 예약 구매 환경을 사용합니다.

## <a name="purchase-synapse-commit-units"></a>Synapse 커밋 단위 구매

[Azure Portal](https://portal.azure.com)Synapse 플랜을 구입합니다. 사전 구매 계획을 구입하려면 하나 이상의 엔터프라이즈 구독에 대한 소유자 역할이 있어야 합니다.

- 하나 이상의 기업계약(제품 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
- EA(기업계약) 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 사용하도록 설정해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

### <a name="to-purchase"></a>구매하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/?synapse=true#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand/autoOpenSpecPicker//productType/Reservation)로 이동합니다.
1. 필요한 경우 **예약으로** 이동한 다음, 페이지 맨 위에서 **+ 추가를** 선택합니다.
1. 예약 구매 페이지에서 Azure Synapse Analytics **사전 구매 계획을** 선택합니다.
1. 구매할 제품 선택 페이지에서 구독을 선택합니다. 예약 용량의 요금을 지불하는 데 사용되는 구독을 선택하려면 **구독** 목록을 사용합니다. 구독의 결제 방법에는 예약 용량에 대한 선불 비용이 청구됩니다. 요금은 등록의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다.
1. 범위를 선택합니다. 구독 범위를 선택하려면 **범위** 목록을 사용합니다.
    - **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다.
    - **관리 그룹** - 관리 그룹 및 청구 범위의 일부인 구독 목록에서 일치하는 리소스에 예약 할인을 적용합니다.
1. 구매할 Azure Synapse 커밋 단위의 개수를 선택한 다음 구매를 완료합니다.  
    :::image type="content" source="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" alt-text="Azure Synapse Analytics 사전 구매 계획에 대한 제품 환경 선택을 보여주는 스크린샷." lightbox="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" :::

## <a name="change-scope-and-ownership"></a>범위 및 소유권 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트
- Azure RBAC(Azure 역할 기반 액세스 제어)

Synapse 커밋 단위 사전 구매 계획을 분할하거나 병합할 수 없습니다. 예약을 관리하는 방법에 대한 자세한 내용은 [구매 후 예약 관리](manage-reserved-vm-instance.md)를 참조하세요.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

Synapse 사전 구매 계획에는 취소 및 교환이 지원되지 않습니다. 모든 구매는 최종입니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [Azure 예약 할인 이해](understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)