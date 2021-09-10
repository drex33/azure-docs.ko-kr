---
title: Azure 스폿 가상 머신 사용
description: Azure 스폿 가상 머신을 사용하여 비용을 절감하는 방법을 알아봅니다.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: ce20b7815c89a45a2677029a28fd205499751e30
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695402"
---
# <a name="use-azure-spot-virtual-machines"></a>Azure 스폿 가상 머신 사용 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Azure 스폿 가상 머신을 사용하면 대폭 절감된 비용으로 사용되지 않은 용량을 활용할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 따라서 Azure 스폿 가상 머신은 일괄 처리 작업, 개발/테스트 환경, 대용량 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

사용 가능한 용량의 크기는 크기, 지역, 하루 중 시간 등에 따라 달라질 수 있습니다. Azure 스폿 가상 머신을 배포할 때 사용 가능한 용량이 있는 경우 Azure에서 VM을 할당하지만, 이러한 VM에 대한 SLA는 없습니다. Azure 스폿 가상 머신이 고가용성을 보장하지 않습니다. Azure에 용량이 다시 필요한 경우에는 언제든지 Azure 인프라에서 30초 통지를 사용하여 Azure 스폿 가상 머신을 제거합니다. 


## <a name="eviction-policy"></a>제거 정책

VM은 사용자가 설정한 최대 가격 또는 용량에 따라 제거할 수 있습니다. Azure 스폿 가상 머신을 만들 때 제거 정책을 *할당 취소*(기본값) 또는 *삭제* 하도록 설정할 수 있습니다. 

*할당 취소* 정책은 VM을 중지됨-할당 취소됨 상태로 전환하여 나중에 다시 배포할 수 있도록 합니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 할당량에 따라 계산되며 기본 디스크에 대한 스토리지 요금이 청구됩니다. 

VM을 제거할 때 삭제하려면 제거 정책을 *삭제* 하도록 설정할 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 스토리지 요금이 계속 청구되지 않습니다. 

[Azure Scheduled Events](./linux/scheduled-events.md)를 통해 VM 내 알림을 받도록 옵트인(opt-in)할 수 있습니다. 이렇게 하면 VM을 제거하는 경우에 알림이 표시되며, 제거 전에 작업을 완료하고 종료 작업을 수행할 수 있도록 30초 정도의 시간 여유가 제공됩니다. 


| 옵션 | 결과 |
|--------|---------|
| 최대 가격이 현재 가격 이상으로 설정됩니다. | 용량 및 할당량을 사용할 수 있는 경우 VM이 배포됩니다. |
| 최대 가격이 현재 가격 미만으로 설정됩니다. | VM이 배포되지 않습니다. 최대 가격이 현재 가격 이상이어야 한다는 오류 메시지가 표시됩니다. |
| 최대 가격이 현재 가격 이상이면 중지/할당 취소된 VM 다시 시작 | 용량과 할당량이 있으면 VM이 배포됩니다. |
| 최대 가격이 현재 가격 미만이면 중지/할당 취소된 VM 다시 시작 | 최대 가격이 현재 가격 이상이어야 한다는 오류 메시지가 표시됩니다. | 
| VM 가격이 증가하여 이제 최대 가격을 초과합니다. | VM이 제거됩니다. 실제로 제거되기 30초 전에 알림을 받습니다. | 
| 제거 후 VM 가격이 다시 최대 가격 미만으로 떨어집니다. | VM이 자동으로 다시 시작되지 않습니다. VM을 직접 다시 시작하고 현재 가격으로 요금이 청구됩니다. |
| 최대 가격이 `-1`로 설정된 경우 | VM은 가격 책정 이유로 제거되지 않습니다. 최대 가격은 표준 VM의 가격이 될 때까지 현재 가격이 됩니다. 표준 가격 이상으로는 요금이 부과되지 않습니다.| 
| 최대 가격 변경 | 최대 가격을 변경하려면 VM의 할당을 취소해야 합니다. VM 할당을 취소하고, 새 최대 가격을 설정한 다음, VM을 업데이트합니다. |


## <a name="limitations"></a>제한 사항

Azure 스폿 가상 머신에 지원되지 않는 VM 크기는 다음과 같습니다.
 - B 시리즈
 - 다양한 크기의 프로모션 버전(예: Dv2, NV, NC, H 프로모션 크기)

Azure 스폿 가상 머신은 Microsoft Azure 중국 21Vianet을 제외하고 모든 지역에 배포할 수 있습니다.

<a name="channel"></a>

현재 지원되는 [제품 유형](https://azure.microsoft.com/support/legal/offer-details/)은 다음과 같습니다.

-   기업 계약 
-   종량제 제품 코드(003P)
-   스폰서(0036P 및 0136P)
- CSP(클라우드 서비스 공급자)의 경우 파트너에게 문의


## <a name="pricing"></a>가격 책정

Azure 스폿 가상 머신의 가격 책정은 지역과 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대한 VM 가격 책정을 참조하세요. 

또한 [Azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices)를 사용하여 가격 책정 정보를 쿼리하여 스폿 가격 책정에 대한 정보를 쿼리할 수 있습니다. `meterName` 및 `skuName`에는 `Spot`이(가) 모두 포함됩니다.

가변 가격 책정을 사용하는 경우 최대 5개의 소수 자릿수를 사용하여 미국 달러(USD)로 최대 가격을 설정하는 옵션을 사용할 수 있습니다. 예를 들어 `0.98765` 값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하는 경우 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과하는 경우 스폿의 현재 가격과 표준 VM의 가격 중에서 더 작은 가격이 됩니다.

## <a name="pricing-and-eviction-history"></a>가격 책정 및 제거 기록

포털의 지역에서 크기별 기록 가격 책정 및 제거 요금을 확인할 수 있습니다. **가격 책정 기록 보기 및 주변 지역의 가격 비교** 를 선택하여 특정 크기에 대한 가격 책정 그래프 또는 테이블을 확인합니다.  다음 이미지에 나와 있는 가격 책정 및 제거 요금은 예제에 불과합니다. 

**차트**:

:::image type="content" source="./media/spot-chart.png" alt-text="가격 책정 및 제거 요금의 차이가 차트로 포함된 지역 옵션의 스크린샷입니다.":::

**테이블**:

:::image type="content" source="./media/spot-table.png" alt-text="가격 책정 및 제거 요금의 차이가 테이블로 포함된 지역 옵션의 스크린샷입니다.":::



##  <a name="frequently-asked-questions"></a>질문과 대답

**Q:** 일단 만들어지면 Azure 스폿 가상 머신은 일반 표준 VM과 동일한가요?

**A:** 예. 단, Azure 스폿 가상 머신용 SLA는 없으며 언제든지 제거할 수 있습니다.


**Q:** 제거되었지만 여전히 용량이 필요한 경우에는 어떻게 해야 하나요?

**A:** 용량을 당장 사용해야 한다면 Azure 스폿 VM 대신 표준 VM을 사용하는 것이 좋습니다.


**Q:** Azure 스폿 가상 머신에 대한 할당량은 어떻게 관리하나요?

**A:** Azure 스폿 가상 머신에는 별도의 할당량 풀이 있습니다. 스폿 할당량은 VM과 확장 집합 인스턴스 간에 공유됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.


**Q:** Azure 스폿 가상 머신에 대한 추가 할당량을 요청할 수 있나요?

**A:** 예, [표준 할당량 요청 프로세스](../azure-portal/supportability/per-vm-quota-requests.md)를 통해 Azure 스폿 가상 머신의 할당량을 늘리겠다는 요청을 제출할 수 있습니다.


**Q:** 질문은 어디에 게시할 수 있나요?

**A:** [Q&A](/answers/topics/azure-spot.html)에서 `azure-spot`을(를) 사용하여 질문을 게시하고 태그를 지정할 수 있습니다. 


**Q:** 스폿 VM의 최대 가격을 변경하려면 어떻게 해야 하나요?

**A:** 최대 가격을 변경하려면 먼저 VM의 할당을 취소해야 합니다. 그런 다음 VM **구성** 섹션에서 포털의 최대 가격을 변경할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[CLI](./linux/spot-cli.md), [포털](spot-portal.md), [ARM 템플릿](./linux/spot-template.md) 또는 [PowerShell](./windows/spot-powershell.md)을 사용하여 Azure 스폿 가상 머신을 배포합니다.

[Azure 스폿 가상 머신 인스턴스를 사용하여 확장 집합](../virtual-machine-scale-sets/use-spot.md)도 배포할 수 있습니다.

오류가 발생하는 경우 [오류 코드](./error-codes-spot.md)를 참조하세요.
