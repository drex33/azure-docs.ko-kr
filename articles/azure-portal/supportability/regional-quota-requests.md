---
title: 지역별 vCPU 할당량 증가
description: Azure Portal 지역에 대한 vCPU 할당량 한도 증가를 요청하는 방법을 알아봅니다.
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: references-regions
ms.openlocfilehash: 45337a6ce029fe3bf8442ac5343ce9145faf904f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717091"
---
# <a name="increase-regional-vcpu-quotas"></a>지역별 vCPU 할당량 증가

Azure Resource Manager 가상 머신에 대해 두 가지 유형의 vCPU 할당량을 적용합니다.

- 표준 vCPU 할당량
- 스폿 vCPU 할당량

표준 vCPU 할당량은 종량제 VM 및 예약된 VM 인스턴스에 적용됩니다. 각 지역의 각 구독에 대해 두 계층에 적용됩니다.

- 첫 번째 계층은 총 지역 vCPU 할당량입니다.
- 두 번째 계층은 D 시리즈 vCPU와 같은 VM 제품군 vCPU 할당량입니다.

이 문서에서는 지정된 지역의 모든 VM에 대해 지역별 vCPU 할당량 증가를 요청하는 방법을 보여줍니다. [VM 제품군 vCPU 할당량](per-vm-quota-requests.md) 또는 스폿 [vCPU 할당량에](spot-quota.md)대한 증가를 요청할 수도 있습니다.

## <a name="special-considerations"></a>특별 고려 사항

지역 간 vCPU 요구 사항을 고려할 때는 다음 사항에 유의하세요.

- 지역 vCPU 할당량은 지정된 지역의 모든 VM 시리즈에 적용됩니다. 따라서 구독의 각 지역에 필요한 vC CPU의 개수를 결정합니다. 각 지역에 충분한 vCPU 할당량이 없는 경우 해당 지역에서 vCPU 할당량을 늘리는 요청을 제출합니다. 예를 들어 서유럽에 30개 vC CPU가 필요하고 할당량이 충분하지 않은 경우 특히 서유럽에서 30개 vC CPU에 대한 할당량을 요청합니다. 이렇게 하면 다른 지역의 구독에서 vCPU 할당량이 증가하지 않습니다. 서유럽의 vCPU 할당량 한도만 30개 vCPU로 증가합니다.

- VM 시리즈에 대한 vCPU 할당량 증가를 요청하면 Azure는 지역 vCPU 할당량 한도를 동일한 양만큼 늘림합니다.

- 새 구독을 만들 때 지역의 총 vCPU 수에 대한 기본값이 모든 개별 VM 시리즈에 대한 총 기본 vCPU 할당량과 같지 않을 수 있습니다. 이러한 차이로 인해 배포하려는 개별 VM 시리즈에 대한 할당량이 충분한 구독이 생성될 수 있습니다. 그러나 모든 배포에 대한 총 지역 vC CPU를 수용하기에 충분한 할당량이 없을 수 있습니다. 이 경우 요청을 제출하여 지역 vCPU 할당량의 할당량 한도를 명시적으로 늘려야 합니다.

## <a name="increase-a-regional-vcpu-quota"></a>지역별 vCPU 할당량 늘리기

**사용량 +** 할당량에서 지역 vCPU 할당량을 요청하려면:

1. Azure Portal에서 **구독** 을 검색하고 선택합니다.

1. 할당량을 늘릴 구독 등록을 선택하세요.

1. 왼쪽 창에서 **사용량 + 할당량** 을 선택하세요. 필터를 사용하여 사용량별로 할당량을 확인합니다.

1. 주 창에서 Total **Regional vC CPU를** 선택한 다음, 연필 아이콘을 선택합니다. 아래 예제에서는 미국 북부 지역의 지역 vCPU 할당량을 보여줍니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/regional-quota-total.png" alt-text="Azure Portal 총 지역 vC CPU를 보여주는 사용량 + 할당량 화면의 스크린샷." lightbox="media/resource-manager-core-quotas-request/regional-quota-total.png":::

1. **할당량 세부 정보에서** 새 할당량 한도를 입력한 다음, **저장 후 계속을** 선택합니다.

   요청이 검토되고 요청이 승인 또는 거부되는지에 대한 알림이 표시됩니다. 일반적으로 몇 분 이내에 발생합니다. 요청이 거부되면 지원 엔지니어가 증가를 지원할 수 있도록 지원 요청을 열 수 있는 링크가 표시됩니다.

> [!TIP]
> 동시에 여러 증가를 요청할 수도 있습니다. 자세한 내용은 [한 요청에서 여러 VM 제품군 CPU 할당량 증가를 참조하세요.](per-vm-quota-requests.md#increase-multiple-vm-family-cpu-quotas-in-one-request)

## <a name="increase-a-regional-quota-from-help--support"></a>도움말 + 지원에서 지역 할당량 늘리기

**도움말 + 지원에서** VM 제품군당 표준 vCPU 할당량 증가를 요청하려면 Azure Portal 새 지원 요청을 만듭니다.

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.
1. **구독** 의 경우 할당량을 늘릴 구독을 선택합니다.
1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure Portal VM 제품군 vCPU 할당량을 늘리기 위한 지원 요청을 보여주는 스크린샷":::

이 위치에서 위에 설명된 단계에 따라 지역 할당량 증가 요청을 완료합니다.

## <a name="next-steps"></a>다음 단계

- Azure [지역 및 해당 위치 목록을 검토합니다.](https://azure.microsoft.com/regions/)
- [가상 머신에 대한 Azure 지역](../../virtual-machines/regions.md) 개요 및 지정된 지역에서 VM 성능, 가용성 및 중복성을 최대화하는 방법을 확인합니다.
