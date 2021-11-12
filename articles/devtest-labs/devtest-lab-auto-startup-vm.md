---
title: VM에 대한 자동 시작 설정 구성
description: 랩에서 VM에 대한 자동 시작 설정을 구성하는 방법을 알아봅니다. 이 설정을 사용하면 일정에 따라 랩의 VM을 자동으로 시작할 수 있습니다.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: ea4220af8a3d7ade997b9e64b7164757edfc165a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286719"
---
# <a name="start-up-lab-virtual-machines-automatically"></a>랩 가상 머신 자동 시작

자동 시작을 사용하면 매일 예약된 시간에 랩에서 VM(가상 머신)을 자동으로 시작할 수 있습니다. 먼저 자동 시작 정책을 만들어야 합니다. 그런 다음 정책을 따를 VM을 선택해야 합니다. 자동 시작할 VM을 적극적으로 선택하는 추가 단계는 비용이 증가하는 VM의 의도하지 않은 시작을 방지하기 위한 것입니다.

이 문서에서는 랩에 대한 자동 시작 정책을 구성하는 방법을 보여줍니다. 자동 종료 설정을 구성하는 방법에 대한 자세한 내용은 [Azure DevTest Labs 랩에 대한 자동 종료 정책 관리를](devtest-lab-auto-shutdown.md)참조하세요. 

## <a name="configure-auto-start-settings-for-a-lab"></a>랩에 대한 자동 시작 설정 구성 

정책은 랩의 모든 VM에 자동 시작을 자동으로 적용하지 않습니다. 정책을 구성한 후 [랩에서 VM에 대해 자동 시작 사용의](#enable-auto start-for-a-vm-in-the-lab)단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs에서 랩으로** 이동합니다.

1. **설정** 아래에서 **구성 및 정책을** 선택합니다. 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="DevTest Labs의 '구성 및 정책' 메뉴를 보여주는 스크린샷.":::

1. 구성 **및 정책** 페이지의 **일정** 아래에서 **자동 시작을** 선택합니다.

1. **자동 시작 허용에서** **예를** 선택합니다. 그러면 일정 정보가 표시됩니다.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="일정 아래의 자동 시작 옵션 스크린샷.":::
 
1. 다음 일정 정보를 제공합니다.

    |속성 | 설명 |
    |---|---|
    |예약된 시작| 시작 시간을 입력합니다.|
    |표준 시간대| 드롭다운 목록에서 표준 시간대를 선택합니다.|
    |요일| 일정을 적용하려는 날짜 옆에 있는 각 상자를 선택합니다.|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="자동 시작 일정 설정의 스크린샷.":::

1. **저장** 을 선택합니다. 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>랩에서 VM에 대해 자동 시작 사용

이러한 단계는 이전 섹션에서 계속 진행합니다. 이제 자동 시작 정책을 만들었으므로 정책을 적용할 VM을 선택합니다.

1. 구성 **및 정책** 페이지를 닫고 **DevTest Labs** 페이지로 돌아갑니다.

1. **내 가상 머신** 아래에서 VM을 선택합니다.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="내 가상 머신 아래의 목록에서 VM 선택 스크린샷.":::

1. 가상 **머신** 페이지의 **작업** 아래에서 **자동 시작을** 선택합니다. 

1. 자동 **시작** 페이지에서 **예** 를 선택한 다음, **를 저장합니다.**

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="자동 시작 선택 메뉴의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [Azure DevTest Labs 랩에 대한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)
