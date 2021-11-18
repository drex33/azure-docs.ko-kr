---
title: 스폿 vCPU 할당량 증가
description: Azure Portal 스폿 vCPU 할당량에 대한 증가를 요청하는 방법을 알아봅니다.
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: edbea31db418e3feae640e18be137b3ca09fac1f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730507"
---
# <a name="increase-spot-vcpu-quotas"></a>스폿 vCPU 할당량 증가

Azure Resource Manager 가상 머신에 대해 두 가지 유형의 vCPU 할당량을 적용합니다.

- 표준 vCPU 할당량
- 스폿 vCPU 할당량

표준 vCPU 할당량은 종량제 VM 및 예약된 VM 인스턴스에 적용됩니다. 각 지역의 각 구독에 대해 두 계층에 적용됩니다.

- 첫 번째 계층은 총 지역 vCPU 할당량입니다.
- 두 번째 계층은 D 시리즈 vCPU와 같은 VM 제품군 vCPU 할당량입니다.

스폿 vCPU 할당량은 모든 [VM 제품군(SPU)에서 VM(가상 머신)을 스폿하는](../../virtual-machines/spot-vms.md) 데 적용됩니다.

이 문서에서는 스폿 vC CPU에 대한 할당량 증가를 요청하는 방법을 보여줍니다. 지역별 [VM 제품군 vCPU 할당량 또는 vCPU 할당량에](per-vm-quota-requests.md) 대한 증가를 요청할 수도 [있습니다.](regional-quota-requests.md)

## <a name="special-considerations"></a>특별 고려 사항

스폿 vCPU 요구 사항을 고려할 때 다음 사항에 유의하세요.

- 새 스폿 VM을 배포하는 경우 모든 스폿 VM 인스턴스에 대한 총 신규 및 기존 vCPU 사용량이 승인된 스폿 vCPU 할당량 제한을 초과해서는 안 됩니다. 스폿 할당량을 초과하면 스폿 VM을 배포할 수 없습니다.

- Azure에 용량이 다시 필요할 때 언제든지 Azure 인프라는 스폿 VM을 제거합니다.

## <a name="increase-a-spot-vcpu-quota"></a>스폿 vCPU 할당량 늘리기

스폿 vCPU 할당량에 대한 할당량 증가를 요청하려면 다음을 수행합니다.

1. Azure Portal에서 **구독** 을 검색하고 선택합니다.
1. 할당량을 늘릴 구독 등록을 선택하세요.
1. 왼쪽 창에서 **사용량 + 할당량** 을 선택하세요.
1. 주 창에서 스폿을 검색하고 증가하려는 지역에 대한 **총 지역별 스폿 vC CPU를** 선택합니다.
1. **할당량 세부 정보에서** 새 할당량 한도를 입력합니다.

   아래 예제에서는 미국 서부 지역의 모든 VM 제품군 vC CPU에서 스폿 vC CPU에 대해 103의 새 할당량 한도를 요청합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/spot-quota.png" alt-text="Azure Portal 스폿 vCPU 할당량 증가 요청의 스크린샷." lightbox="media/resource-manager-core-quotas-request/spot-quota.png":::

1. **저장하고 계속** 을 선택합니다.

요청이 검토되고 요청이 승인 또는 거부되는지에 대한 알림이 표시됩니다. 일반적으로 몇 분 이내에 발생합니다. 요청이 거부되면 지원 엔지니어가 증가를 지원할 수 있도록 지원 요청을 열 수 있는 링크가 표시됩니다.

## <a name="increase-a-spot-quota-from-help--support"></a>도움말 + 지원에서 스폿 할당량 늘리기

**도움말 + 지원에서** 스폿 vCPU 할당량 증가를 요청하려면 Azure Portal 새 지원 요청을 만듭니다.

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.
1. **구독** 의 경우 할당량을 늘릴 구독을 선택합니다.
1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure Portal VM 제품군 vCPU 할당량을 늘리기 위한 지원 요청을 보여주는 스크린샷":::

해당 위치에서 위에서 설명한 단계에 따라 스폿 할당량 증가 요청을 완료합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 스폿 가상 머신에](../../virtual-machines/spot-vms.md)대해 자세히 알아보세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-resource-manager/management/azure-subscription-service-limits)에 대해 알아봅니다.
