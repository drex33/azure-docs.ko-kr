---
title: 예약을 사용하여 Azure Disk Storage에 대한 비용 최적화
description: 프리미엄 SSD 관리 디스크에 대한 비용을 절감하기 위해 Azure Disk Storage 예약 구매에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 6e58548365be63ed13a8ac20c42cca81b66bc407
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593043"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Azure 디스크 예약을 사용하여 비용 절감

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

예약된 용량으로 Azure Disk Storage 사용량을 절약하세요. Azure Reserved Virtual Machine Instances와 결합된 Azure Disk Storage 예약을 사용하면 총 VM(가상 머신) 비용을 낮출 수 있습니다. 예약 할인은 선택한 예약 범위에서 일치하는 디스크에 자동으로 적용됩니다. 이 자동 애플리케이션 덕분에, 할인을 받기 위해 예약을 관리 디스크에 할당할 필요가 없습니다.

할인은 디스크 사용량에 따라 매시간 적용됩니다. 사용하지 않은 예약된 용량은 이월되지 않습니다. Azure Disk Storage 예약 할인은 관리되지 않는 디스크, Ultra Disks 또는 페이지 Blob 사용량에는 적용되지 않습니다.

## <a name="determine-your-storage-needs"></a>스토리지 요구 사항 확인

예약을 구입하기 전에 스토리지 요구 사항을 확인하세요. 현재 Azure Disk Storage 예약은 Azure 프리미엄 SSD SKU를 선택했을 때만 사용할 수 있습니다. 프리미엄 SSD의 SKU는 디스크의 크기와 성능을 결정합니다.

스토리지 요구 사항을 결정할 때 단지 용량을 기반으로 디스크를 고려하지 마세요. 예를 들어 P40 디스크의 예약을 사용하고 이를 사용하여 두 개의 더 작은 P30 디스크에 대한 요금을 지불할 수는 없습니다. 예약을 구매하는 경우 SKU당 총 디스크 수에 대한 예약만 구매합니다.

디스크 예약은 디스크 SKU마다 생성됩니다. 따라서 예약 사용량은 제공된 크기가 아닌 디스크 SKU의 단위를 기준으로 합니다.

예를 들어 2TiB의 프로비저닝된 스토리지 용량을 가진 P40 디스크 하나를 예약한다고 가정합니다. 그리고 두 개의 P30 디스크만 할당한다고 가정합니다. 이 경우 P40 예약은 P30 사용량을 고려하지 않으며 P30 디스크에서 종량제 요금을 지불합니다.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>구매 시 고려 사항

디스크 예약 구매를 고려할 때 다음 방법을 권장합니다.

- 사용량 정보를 분석하여 구매해야 하는 예약을 결정합니다. 프로비저닝되거나 사용된 디스크 용량이 아닌 디스크 SKU의 사용량을 추적해야 합니다.
- VM 예약과 함께 디스크 예약을 검사합니다. 최대 절약을 위해 VM 사용량과 디스크 사용량에 대한 예약을 수행하는 것이 좋습니다. 먼저 올바른 VM 예약을 확인한 다음 디스크 예약을 평가할 수 있습니다. 일반적으로 각 워크로드에 대한 표준 구성이 있습니다. 예를 들어 SQL Server 서버에는 두 개의 P40 데이터 디스크와 P30 운영 체제 디스크가 하나씩 있을 수 있습니다.
  
  이러한 종류의 패턴을 통해 구매할 수 있는 예약된 용량을 확인할 수 있습니다. 이 방법은 평가 프로세스를 간소화시키며, VM과 디스크 모두에 대해 정렬된 플랜을 가지고 있는지 확인할 수 있습니다. 플랜에는 구독 또는 지역과 같은 고려 사항이 포함됩니다.

## <a name="purchase-restrictions"></a>구매 제한

현재 다음에 대해서는 예약 할인을 사용할 수 없습니다.

- 관리되지 않는 디스크 또는 페이지 Blob
- 표준 SSD 또는 표준 HDD(하드 디스크 드라이브)
- P30보다 작은 프리미엄 SSD: P1, P2, P3, P4, P6, P10, P15 및 P20 SSD SKU
- Azure Government, Azure 독일 또는 Azure 중국 지역의 디스크

드물긴 하지만, 해당 지역의 낮은 용량으로 인해 Azure가 디스크 SKU의 하위 집합에 대한 새로운 예약 구매를 제한하는 경우도 있습니다.

## <a name="buy-a-disk-reservation"></a>디스크 예약 구매

[Azure Portal](https://portal.azure.com/)을 통해 Azure Disk Storage 예약을 구매할 수 있습니다. 선결제 또는 월 단위로 예약 요금을 지불할 수 있습니다. 월별 지불로 구매하는 방법에 대한 자세한 내용은 [월별 지불로 예약 구매](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)를 참조하세요.

예약된 용량을 구매하려면 다음 단계를 수행합니다.

1. Azure Portal에서 [예약 구매](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 창으로 이동합니다.

1. **Azure Managed Disks** 를 선택하여 예약을 구매합니다.

    ![예약 구매 창](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 다음 표에서 설명하는 필수 값을 지정합니다.

   |요소  |Description  |
   |---------|---------|
   |**범위**   |  예약과 관련된 청구 혜택을 사용할 수 있는 구독 수입니다. 이 값은 특정 구독에 예약이 적용되는 방법을 지정하기도 합니다. <br/><br/> **공유** 를 선택하면 청구 컨텍스트 내의 모든 구독에서 Azure Storage 용량에 예약 할인이 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위에는 계정 관리자가 만든 종량제 요금을 사용하는 모든 개별 구독이 포함됩니다. </br></br>**관리 그룹** 을 선택 하는 경우 관리 그룹 및 청구 범위에 속하는 모든 구독의 Azure Storage 용량에 예약 할인이 적용 됩니다. <br/><br/>  **단일 구독** 을 선택하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용됩니다. <br/><br/> **단일 리소스 그룹** 을 선택하는 경우 선택한 구독의 Azure Storage 용량과 해당 구독의 선택된 리소스 그룹에 예약 할인이 적용됩니다. <br/><br/> 예약 용량을 구입한 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure Storage 예약 요금을 지불하는 데 사용하는 구독입니다. 선택한 구독의 결제 방법을 사용하여 비용을 청구합니다. 구독은 다음 유형 중 하나여야 합니다.<br/><ul><li> 기업계약(제품 번호 MS-AZR-0017P 및 MS-AZR-0148P) 기업 구독의 경우 요금은 등록계약의 Azure 선불(이전에는 현금 약정 금액이라고 함) 잔액에서 차감되거나 초과분에 대해 청구됩니다.</li><br/><li>종량제 요금을 사용한 개별 구독(제품 번호: MS-AZR-0003P 및 MS-AZR-0023P) 종량제 요율이 적용되는 구독의 경우 요금은 구독의 신용 카드 또는 청구서 결제 방법으로 청구됩니다.</li></ul>    |
   | **디스크** | 만들려는 SKU입니다. |
   | **지역** | 예약이 적용되는 지역입니다. |
   | **대금 청구 주기**: | 예약에 대한 계정에 대금을 청구하는 빈도입니다. 옵션 중에는 **월별** 및 **선불** 이 있습니다. |

    ![구매하려는 제품을 선택하는 창.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 예약에 대한 값을 지정하면 Azure Portal에 비용이 표시됩니다. 또한 포털에서 종량제 요금 청구에 대한 할인율을 볼 수 있습니다. **다음** 을 선택하여 **예약 구매** 창으로 이동합니다.

1. **예약 구매** 창에서 예약의 이름을 지정하고 원하는 예약의 총 수량을 선택할 수 있습니다. 예약 수가 디스크 수에 매핑됩니다. 예를 들어, 디스크를 100개 예약하려면 **수량** 값에 **100** 을 입력합니다.

1. 총 예약 비용을 검토합니다.

    ![예약 구매 창](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

예약을 구매한 후에는 예약 사용 약관과 일치하는 기존 Disk Storage 리소스에 자동으로 적용됩니다. Disk Storage 리소스를 아직 만들지 않은 경우 예약 사용 약관과 일치하는 리소스를 만들 때마다 예약이 적용됩니다. 두 경우 모두 성공적으로 구매한 후 예약 기간이 즉시 시작됩니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

특정 제한 내에서 예약을 취소, 교환 또는 환불할 수 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료되면 해당 예약에서 사용하는 모든 Azure Disk Storage 용량은 종량제 요금으로 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

예약이 만료되기 30일 전에 메일 알림을 받게 되고, 만료 날짜가 다시 표시됩니다. 예약이 제공하는 비용 절감 효과를 계속 활용하려면 늦어도 만료 날짜에는 갱신해야 합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [예약 할인이 Azure Disk Storage에 적용되는 방법 이해](../cost-management-billing/reservations/understand-disk-reservations.md)