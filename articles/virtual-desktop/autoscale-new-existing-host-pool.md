---
title: 호스트 풀에 대한 Azure Virtual Desktop 크기 조정 계획(미리 보기)
description: 배포의 신규 또는 기존 호스트 풀에 크기 조정 계획을 할당하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b61869e09003456f55a6d69cae01356a06bc633d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182374"
---
# <a name="enable-scaling-plans-for-existing-and-new-host-pools-preview"></a>기존 및 새 호스트 풀에 대한 크기 조정 계획 사용(미리 보기)

> [!IMPORTANT]
> 자동 크기 조정 기능은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

배포의 기존 호스트 풀에 대해 크기 조정 계획을 사용하도록 설정할 수 있습니다. 호스트 풀에 크기 조정 계획을 적용하면 해당 호스트 풀 내의 모든 세션 호스트에도 계획이 적용됩니다. 크기 조정은 할당된 호스트 풀에서 만드는 모든 새 세션 호스트에도 자동으로 적용됩니다.

크기 조정 계획을 사용하지 않도록 설정하면 할당된 모든 리소스는 사용하지 않도록 설정할 당시의 크기 조정 상태로 유지됩니다.

## <a name="assign-a-scaling-plan-to-an-existing-host-pool"></a>기존 호스트 풀에 크기 조정 계획 할당

기존 호스트 풀에 크기 조정 계획을 할당하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. Windows **Virtual Desktop으로** 이동합니다.

3. **호스트 풀을** 선택한 **다음, 크기 조정 계획으로** 이동하여 **새** 를 선택합니다.

4. **크기 조정 계획을** 선택한 **다음, + 할당을** 선택하여 다음 스크린샷과 같이 할당되지 않은 호스트 풀에 크기 조정 계획을 할당합니다.

    > [!div class="mx-imgBorder"]
    > ![크기 조정 계획 창의 스크린샷. 왼쪽 위 모서리에 있는 + 추가 단추가 빨간색 테두리로 강조 표시됩니다.](media/assign-scaling-plan.png)

    배포 중에 크기 조정 계획을 사용하도록 설정한 경우 다음 스크린샷과 같이 크기 조정 계획 사용 확인란을 선택 취소하여 **크기 조정 계획** 메뉴에서 선택한 호스트 풀에 대한 **계획을** 사용하지 않도록 설정하는 옵션도 있습니다.

    > [!div class="mx-imgBorder"]
    > ![크기 조정 계획 창의 스크린샷. "자동 크기 조정 사용" 확인란이 선택되고 빨간색 테두리로 강조 표시됩니다.](media/enable-autoscale.png)

## <a name="edit-an-existing-scaling-plan"></a>기존 크기 조정 계획 편집

기존 크기 조정 계획을 편집하려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

2. Azure **Virtual Desktop으로** 이동합니다.

3. **크기 조정 계획 을** 선택한 다음 편집할 크기 조정 계획의 이름을 선택합니다. 설정 창이 열립니다.

4. 계획의 표시 이름, 설명, 표준 시간대 또는 제외 태그를 편집하려면 **속성** 탭으로 이동합니다.

5. 호스트 풀을 할당하거나 일정을 편집하려면 **관리** 탭으로 이동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Virtual Desktop 세션 호스트에 대한 자동 크기 조정(미리 보기)에서](autoscale-new-existing-host-pool.md)크기 조정 계획을 만드는 방법을 검토합니다.
- 크기 조정 계획에 [대한 진단 사용에서 크기 조정 계획의](autoscale-diagnostics.md)문제를 해결하는 방법을 알아봅니다.