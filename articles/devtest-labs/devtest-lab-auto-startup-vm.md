---
title: VM에 대 한 자동 시작 설정 구성
description: 랩에서 VM의 자동 시작 설정을 구성하는 방법을 알아봅니다. 이 설정을 사용하면 일정에 따라 랩의 VM을 자동으로 시작할 수 있습니다.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 8493a5037c01741c11f89ec5df84adac2d2d70c3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255027"
---
# <a name="auto-startup-lab-virtual-machines"></a>랩 가상 머신 자동 시작

자동 시작을 사용 하면 매일 예약 된 시간에 가상 컴퓨터 (Vm)를 자동으로 시작할 수 있습니다. 먼저 자동 시작 정책을 만들어야 합니다. 그런 다음 정책을 따를 Vm을 선택 해야 합니다. 자동 시작에 대 한 Vm을 선택 하는 찬성의 추가 단계는 의도 하지 않은 Vm이 비용을 증가 시키는 것을 방지 하기 위한 것입니다.

이 문서에서는 랩에 대 한 자동 시작 정책을 구성 하는 방법을 보여 줍니다. 자동 종료 설정을 구성 하는 방법에 대 한 자세한 내용은 [Azure DevTest Labs 랩에 대 한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)를 참조 하세요. 

## <a name="configure-auto-start-settings-for-a-lab"></a>랩에 대 한 자동 시작 설정 구성 

정책은 랩의 모든 Vm에 자동 시작을 자동으로 적용 하지 않습니다. 정책을 구성한 후 [랩에서 VM에 대 한 자동 시작 사용](#enable-auto-start-for-a-vm-in-the-lab)의 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. **설정** 아래에서 **구성 및 정책** 을 선택 합니다. 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="DevTest Labs의 ' 구성 및 정책 ' 메뉴를 보여 주는 스크린샷":::

1. **구성 및 정책** 페이지의 **일정** 에서 **자동 시작** 을 선택 합니다.

1. **자동 시작 허용** 에 대해 **예** 를 선택 합니다. 그러면 일정 정보가 표시 됩니다.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="일정에서 자동 시작 옵션의 스크린샷":::
 
1. 다음 일정 정보를 제공 합니다.

    |속성 | 설명 |
    |---|---|
    |예약된 시작| 시작 시간을 입력 합니다.|
    |표준 시간대| 드롭다운 목록에서 표준 시간대를 선택 합니다.|
    |요일| 일정을 적용 하려는 날짜 옆에 있는 각 상자를 선택 합니다.|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="자동 시작 일정 설정의 스크린샷":::

1. **저장** 을 선택합니다. 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>랩에서 VM에 대 한 자동 시작 사용

이러한 단계는 이전 섹션에서 계속 됩니다. 이제 자동 시작 정책을 만들었으므로 정책을 적용할 Vm을 선택 합니다.

1. **구성 및 정책** 페이지를 닫아 **DevTest Labs** 페이지로 돌아갑니다.

1. **내 가상 머신** 에서 VM을 선택 합니다.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="내 가상 머신 아래의 목록에서 VM 선택의 스크린샷":::

1. **가상 컴퓨터** 페이지의 **작업** 아래에서 **자동 시작** 을 선택 합니다. 

1. **자동 시작** 페이지에서 **예** 를 선택한 다음 **저장** 을 클릭 합니다.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="자동 시작 메뉴의 스크린샷":::

## <a name="next-steps"></a>다음 단계

랩에 대한 자동 종료 정책을 구성하는 방법을 알아보려면 [Azure DevTest Labs에서 랩에 대한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)를 참조하세요.
