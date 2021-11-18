---
title: VM 제품군 vCPU 할당량 늘리기
description: Azure Portal에서 VM 제품군에 대 한 vCPU 할당량 한도 증가를 요청 하는 방법에 대해 알아봅니다. 그러면 총 지역 vCPU 한도가 같은 양만큼 증가 합니다.
ms.date: 11/15/2021
ms.topic: how-to
ms.openlocfilehash: 7941c19de7143bc794ffd6d197d31c6db6f0a0d5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714375"
---
# <a name="increase-vm-family-vcpu-quotas"></a>VM 제품군 vCPU 할당량 늘리기

Azure Resource Manager는 가상 컴퓨터에 대해 두 가지 유형의 vCPU 할당량을 적용 합니다.

- 표준 vCPU 할당량
- 지점 vCPU 할당량

표준 vCPU 할당량은 종 량 제 Vm 및 예약 된 VM 인스턴스에 적용 됩니다. 각 지역에서 각 구독에 대해 두 계층에 적용 됩니다.

- 첫 번째 계층은 총 지역 vCPU 할당량입니다.
- 두 번째 계층은 D 시리즈 Vcpu와 같은 VM 패밀리 vCPU 할당량입니다.

이 문서에서는 VM 패밀리 vCPU 할당량에 대 한 향상을 요청 하는 방법을 보여 줍니다. 지역 또는 지역 [vCPU 할당량](spot-quota.md)을 [기준으로 vcpu 할당량](regional-quota-requests.md) 에 대 한 향상을 요청할 수도 있습니다.

## <a name="increase-a-vm-family-vcpu-quota"></a>VM 패밀리 vCPU 할당량 증가

**사용량 + 할당량** 에서 VM 제품군 당 표준 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

1. Azure Portal에서 **구독** 을 검색하고 선택합니다.
1. 할당량을 늘릴 구독 등록을 선택하세요.
1. 왼쪽 창에서 **사용량 + 할당량** 을 선택하세요.
1. 기본 창에서 증가 시킬 VM 패밀리 vCPU 할당량을 찾은 다음 연필 아이콘을 선택 합니다. 아래 예제에서는 미국 동부 지역에 배포 된 표준 DSv3 제품군 vCPUs를 보여 줍니다. **사용** 열에는 현재 할당량 사용량과 현재 할당량 한도가 표시 됩니다.
1. **할당량 정보** 에서 새 할당량 한도를 입력 하 고 **저장 및 계속** 을 선택 합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-increase-example.png" alt-text="사용량 + 할당량 창의 스크린샷" lightbox="media/resource-manager-core-quotas-request/quota-increase-example.png":::

요청을 검토 하 고 요청이 승인 또는 거부 되었는지 여부에 대 한 알림이 표시 됩니다. 이는 일반적으로 몇 분 내에 발생 합니다. 요청이 거부 되 면 지원 엔지니어가 증가를 지원할 수 있도록 지원 요청을 열 수 있는 링크가 표시 됩니다.

> [!NOTE]
> VM 제품군 할당량을 늘리는 요청이 승인 되 면 Azure는 VM이 배포 되는 지역에 대 한 지역 vCPU 할당량을 자동으로 증가 시킵니다.

> [!TIP]
> 가상 컴퓨터를 만들거나 크기를 조정 하 고 VM 크기를 선택 하는 경우 **할당량 없음-제품군 제한 부족** 아래 나열 된 몇 가지 옵션이 표시 될 수 있습니다. 그렇다면 할당량 **요청** 링크를 선택 하 여 VM 만들기 페이지에서 할당량 증가를 직접 요청할 수 있습니다.

## <a name="increase-a-vm-family-vcpu-quota-from-help--support"></a>도움말 + 지원에서 VM 패밀리 vCPU 할당량 늘리기

**도움말 + 지원** 에서 VM 제품군 당 표준 vcpu 할당량 증가를 요청 하려면 Azure Portal에서 새 지원 요청을 만듭니다.

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.
1. **구독** 의 경우 할당량을 늘릴 구독을 선택합니다.
1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure Portal에서 VM 패밀리 vCPU 할당량을 늘리는 지원 요청을 보여 주는 스크린샷":::

여기에서 위에 설명 된 단계에 따라 할당량 증가 요청을 완료 합니다.

## <a name="increase-multiple-vm-family-cpu-quotas-in-one-request"></a>한 요청에서 여러 VM 제품군 CPU 할당량 늘리기

동시에 여러 번의 증가가 요청 (대량 요청) 할 수도 있습니다. 대량 요청 할당량 증가는 단일 할당량 증가를 요청 하는 것 보다 시간이 더 오래 걸릴 수 있습니다.

여러 증가량을 함께 요청 하려면 먼저 위에 설명 된 대로 **사용량 + 할당량** 페이지로 이동 합니다. 그런 다음 아래 작업을 수행합니다.

1. 화면 위쪽에서 **요청 증가** 를 선택 합니다.
1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.
1. **다음** 을 선택 하 여 **추가 세부 정보** 화면으로 이동한 다음, **세부 정보 입력** 을 선택 합니다.
1. **할당량 정보** 화면에서 다음을 수행 합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png" alt-text="할당량 정보 화면 및 선택 항목을 보여 주는 스크린샷":::

   1. **배포 모델** 의 경우 **리소스 관리자** 가 선택 되어 있는지 확인 합니다.
   1. **위치** 에서 할당량을 증가 시키려는 모든 지역을 선택 합니다.
   1. 선택한 각 지역에 대해 **할당량** 드롭다운 목록에서 하나 이상의 VM 시리즈를 선택 합니다.
   1. 선택한 각 **VM 시리즈** 에 대해이 구독에 대해 원하는 새 vcpu 제한을 입력 합니다.
   1. 완료 되 면 **저장 후 계속** 을 선택 합니다.
1. 연락처 세부 정보를 입력 하거나 확인 한 후 **다음** 을 선택 합니다.
1. 마지막으로 **검토 + 만들기** 페이지에서 모든 항목이 올바른지 확인 한 다음 **만들기** 를 선택 하 여 요청을 제출 합니다.

## <a name="next-steps"></a>다음 단계

- [Vcpu 할당량](/azure/virtual-machines/windows/quotas)에 대해 자세히 알아보세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-resource-manager/management/azure-subscription-service-limits)에 대해 알아봅니다.
