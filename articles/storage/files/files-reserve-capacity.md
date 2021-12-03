---
title: 예약된 용량으로 Azure Files에 대한 비용 최적화
titleSuffix: Azure Files
description: Azure Files 예약 용량을 사용하여 Azure 파일 공유 배포에 대한 비용을 절감하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1b30346c33604174b922ec6be1ec594ae2ca0f76
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517999"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>예약된 용량으로 Azure Files에 대한 비용 최적화
용량 예약을 사용하여 Azure 파일 공유에 대한 스토리지 비용을 절감할 수 있습니다. Azure Files 예약 용량은 1년 또는 3년 동안 예약을 커밋하는 경우 스토리지 비용에 대한 용량 할인을 제공합니다. 예약에서 예약 기간 동안 고정된 양의 스토리지 용량을 제공합니다.

Azure Files 예약 용량은 Azure 파일 공유에 데이터를 저장하기 위한 용량 비용을 크게 줄일 수 있습니다. 저장하는 양은 예약 기간, 예약하도록 선택한 총 용량, Azure 파일 공유에 대해 선택한 계층 및 중복 설정에 따라 달라집니다. 예약 용량은 청구 할인을 제공하며 Azure 파일 공유 상태에는 영향을 주지 않습니다.

Azure Files의 예약 용량에 대한 가격 책정 정보는 [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="reservation-terms-for-azure-files"></a>Azure Files에 대한 예약 조건
다음 섹션에서는 Azure Files 용량 예약의 조건에 대해 설명합니다.

### <a name="reservation-capacity"></a>예약 용량
1년 또는 3년 기간 동안 매월 10TiB 및 100TiB 단위로 Azure Files 예약 용량을 구매할 수 있습니다.

### <a name="reservation-scope"></a>예약 범위
단일 구독, 여러 구독 (공유 범위) 및 관리 그룹에 대해 예약 된 용량을 사용할 수 Azure Files. 단일 구독으로 범위가 지정되면 예약 할인이 선택한 구독에만 적용됩니다. 여러 구독으로 범위가 지정되면 고객의 청구 컨텍스트 내에서 해당 구독 간에 예약 할인이 공유됩니다. 관리 그룹으로 범위가 지정 된 경우 예약 할인은 관리 그룹 및 청구 범위의 일부인 구독에 적용 됩니다. 예약은 구매한 범위 내 사용에 적용되며, 구독 내의 특정 스토리지 계정, 컨테이너 또는 개체로 제한될 수 없습니다.

Azure Files에 대 한 용량 예약은 구독 또는 공유 리소스 그룹에 저장 된 데이터의 양만 다룹니다. 예약에는 트랜잭션, 대역폭, 데이터 전송 및 메타 데이터 저장소 요금이 포함 되지 않습니다. 예약을 구입하는 즉시, 예약 특성과 일치하는 용량 요금은 종량제 요금이 아닌 할인 요금으로 부과됩니다. Azure 예약에 대한 자세한 내용은 [Azure Reservations란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)을 참조하세요.

### <a name="supported-tiers-and-redundancy-options"></a>지원되는 계층 및 중복 옵션
Azure Files 예약된 용량은 프리미엄, 핫 및 쿨 파일 공유에 사용할 수 있습니다. 트랜잭션 최적화 계층의 Azure 파일 공유에는 예약된 용량을 사용할 수 없습니다. 모든 스토리지 중복은 예약을 지원합니다. 중복 옵션에 대한 자세한 내용은 [Azure Files 중복](storage-files-planning.md#redundancy)을 참조하세요.

### <a name="security-requirements-for-purchase"></a>구매를 위한 보안 요구 사항
예약된 용량을 구매하려면:

- 종량제 요금이 있는 하나 이상의 Enterprise 구독 또는 개별 구독에 대한 **소유자** 역할에 속해야 합니다.
- Enterprise 구독의 경우 EA 포털에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 영업 담당자만 Azure Files 예약 용량을 구입할 수 있습니다.

## <a name="determine-required-capacity-before-purchase"></a>구매 전 필요한 용량 결정
Azure Files 예약을 구매할 때 예약에 대한 지역, 계층 및 중복 옵션을 선택해야 합니다. 예약은 해당 지역, 계층 및 중복 수준에서 저장된 데이터에 대해서만 유효합니다. 예를 들어 ZRS(영역 중복 스토리지)를 사용하는 핫 계층에 대해 미국 서부의 데이터에 대한 예약을 구매한다고 가정합니다. 이 예약은 미국 동부, 쿨 계층의 데이터 또는 GRS(지역 중복 스토리지)의 데이터에는 적용되지 않습니다. 그러나 추가 요구 사항에 대해 다른 예약을 구매할 수 있습니다.  

예약은 10TiB 또는 100TiB 블록에 사용 가능하며, 100TiB 블록이 더 많이 할인됩니다. Azure Portal에서 예약을 구매하는 경우 Microsoft는 이전 사용량을 기준으로 권장 사항을 제공하여 구매해야 하는 예약을 결정하는 데 도움을 줄 수 있습니다.

## <a name="purchase-azure-files-reserved-capacity"></a>Azure Files 예약 용량 구매
[Azure Portal](https://portal.azure.com)를 통해 Azure Files 예약 용량을 구매할 수 있습니다. 예약 요금은 사전 결제 또는 월별 결제로 처리할 수 있습니다. 월간 결제로 구매하는 방법에 대한 자세한 내용은 [선불 또는 월간 결제로 Azure 예약 구매](../../cost-management-billing/reservations/prepare-buy-reservation.md)를 참조하세요.

시나리오에 적합한 예약 조건을 식별하는 데 도움이 필요하면 [Azure Storage 예약 용량 할인 이해](../../cost-management-billing/reservations/understand-storage-charges.md)를 참조하세요.

예약된 용량을 구매하려면 다음 단계를 수행합니다.

1. Azure Portal에서 [예약 구매](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 블레이드로 이동합니다.  
1. 새 예약을 구입하려면 **Azure Files** 를 선택합니다.  
1. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

    ![예약된 용량을 구매하는 방법을 보여 주는 스크린샷](./media/files-reserve-capacity/select-reserved-capacity.png)

   |필드  |설명  |
   |---------|---------|
   |**범위**   |  얼마나 많은 구독이 예약과 연결된 청구 혜택을 사용할 수 있는지 나타냅니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/> **공유** 를 선택하면 예약 할인이 청구 컨텍스트 내 모든 구독의 Azure Files 용량에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위에는 계정 관리자가 만든 종량제 요금을 사용하는 모든 개별 구독이 포함됩니다.  <br/><br/>  **단일 구독** 을 선택하면 예약 할인이 선택한 구독의 Azure Files 용량에 적용됩니다. <br/><br/> **단일 리소스 그룹** 을 선택하면 예약 할인이 선택한 구독의 Azure Files 용량과 해당 구독 내에서 선택한 리소스 그룹에 적용됩니다. <br/><br/> 예약을 구매한 후에는 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure Files 예약 요금을 지불하는 데 사용되는 구독입니다. 선택한 구독에 대한 지불 방법은 비용을 청구하는 데 사용됩니다. 구독은 다음 유형 중 하나여야 합니다. <br/><br/>  기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P): Enterprise 구독에 대한 요금은 등록의 Azure Prepayment(이전에는 금액 약정이라고 함) 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다. <br/><br/> 종량제 요금을 사용하는 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P): 종량제 요금을 사용하는 개별 구독의 경우 요금은 구독에 대한 신용 카드 또는 청구서 지불 방법으로 청구됩니다.    |
   | **지역** | 예약이 적용되는 지역입니다. |
   | **계층** | 예약이 적용되는 계층입니다. 옵션에는 *프리미엄*, *핫* 및 *쿨* 이 있습니다. |
   | **중복** | 예약에 대한 중복 옵션입니다. 옵션에는 *LRS*, *ZRS*, *GRS* 및 *GZRS* 가 포함됩니다. 중복 옵션에 대한 자세한 내용은 [Azure Files 중복](storage-files-planning.md#redundancy)을 참조하세요. |
   | **청구 주기** | 예약에 대한 계정 청구 빈도를 나타냅니다. 옵션에는 *월별* 또는 *선불* 이 있습니다. |
   | **크기** | 예약할 용량의 양입니다. |
   |**기간**  | 1년 또는 3년입니다.   |

1. 예약에 대한 매개 변수를 선택하면 Azure Portal에 비용이 표시됩니다. 또한 포털은 종량제 요금 청구에 대한 할인율을 보여 줍니다.

1. **구매 예약** 블레이드에서 예약의 총 비용을 검토합니다. 예약의 이름을 입력할 수도 있습니다.

예약을 구매하면 예약의 조건과 일치하는 기존 Azure 파일 공유에 예약이 자동 적용됩니다. Azure 파일 공유를 아직 만들지 않은 경우 예약의 조건과 일치하는 리소스를 만들 때마다 예약이 적용됩니다. 예약 기간은 두 경우 모두 성공적으로 구매한 후 즉시 시작됩니다.

## <a name="exchange-or-refund-a-reservation"></a>예약 교환 또는 환불
예약을 교환 또는 환불할 수 있지만 몇 가지 제한 사항이 있습니다. 다음 섹션에서는 이러한 제한 사항에 대해 설명합니다.

예약을 교환하거나 환불하려면 Azure Portal의 예약 세부 정보로 이동합니다. **교환** 또는 **환불** 을 선택하고 지침에 따라 지원 요청을 제출합니다. 요청이 처리되면 Microsoft에서 요청 완료를 확인하는 이메일을 보냅니다.

Azure Reservations 정책에 대한 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

### <a name="exchange-a-reservation"></a>예약 교환
예약을 교환하면 예약의 미사용 부분을 기준으로 비례 배분된 환불을 받을 수 있습니다. 그런 다음 새 Azure Files 예약의 구매 가격에 환불을 적용할 수 있습니다.

교환 가능한 수에는 제한이 없습니다. 또한 교환과 관련된 요금은 없습니다. 구매하는 새 예약은 원래 예약에서 비례 배분된 크레딧보다 크거나 같은 값이어야 합니다. Azure Files 예약은 다른 Azure 서비스에 대한 예약이 아닌 다른 Azure Files 예약에 대해서만 교환할 수 있습니다.

### <a name="refund-a-reservation"></a>예약 환불
언제든지 Azure Files 예약을 취소할 수 있습니다. 취소하는 경우 12%의 조기 종료 수수료를 제하고 예약의 남은 기간을 기준으로 비례 배분된 환불을 받습니다. 연간 최대 환불액은 5만 달러입니다.

예약을 취소하면 즉시 예약이 종료되고 남은 월이 Microsoft에 반환됩니다. 남은 비례 배분 잔액은 수수료를 제하고 원래 구매 형태로 환불됩니다.

## <a name="expiration-of-a-reservation"></a>예약 만료
예약이 만료되면 해당 예약에서 사용하는 모든 Azure Files 용량은 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

예약 만료 30일 전과 만료 당일에 다시 이메일 알림을 받게 됩니다. 예약이 제공하는 비용 절감 효과를 계속 활용하려면 만료 날짜보다 늦지 않게 갱신하십시오.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처
질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [Azure 예약이란?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Storage 서비스에 예약 할인이 적용되는 방식 이해](../../cost-management-billing/reservations/understand-storage-charges.md)
