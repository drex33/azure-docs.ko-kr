---
title: VM에 대 한 자동 시작 설정 구성
description: 랩에서 VM의 자동 시작 설정을 구성하는 방법을 알아봅니다. 이 설정을 사용하면 일정에 따라 랩의 VM을 자동으로 시작할 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 828350cb130e990d6a6ce3f16f084d5629518293
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642950"
---
# <a name="auto-startup-lab-virtual-machines"></a>랩 가상 머신 자동 시작  
Azure DevTest Labs를 사용하면 랩의 가상 머신이 일정에 따라 자동으로 시작 및 종료되도록 구성할 수 있습니다. 자동 종료 설정을 구성하는 방법에 대한 자세한 내용은 [Azure DevTest Labs에서 랩에 대한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)를 참조하세요. 

정책이 설정된 경우 모든 VM이 포함되는 자동 종료와 달리 자동 시작 정책은 랩 사용자가 명시적으로 VM을 선택하고 해당 일정을 옵트인해야 합니다. 이렇게 하면 원치 않는 VM이 실수로 자동 시작되어 예기치 않은 지출이 발생하는 상황을 방지할 수 있습니다.

이 문서에서는 랩에 대한 자동 시작 정책을 구성하는 방법을 보여 줍니다.

## <a name="configure-autostart-settings-for-a-lab"></a>랩의 자동 시작 설정 구성 
1. 랩의 홈페이지로 이동합니다. 
2. 왼쪽 메뉴에서 **구성 및 정책** 을 선택합니다. 

    ![DevTest Lab의 “구성 및 정책” 메뉴 스크린샷](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. **구성 및 정책** 페이지에서 다음 단계를 수행합니다.
    
    1. 이 랩에서 자동 시작 기능을 사용하도록 설정하려면 **가상 머신의 자동 시작 예약을 허용** 에서 **켜기** 를 선택합니다. 
    2. **일정 시작** 필드에서 시작 시간(예: 오전 8:00:00)을 선택합니다. 
    3. 사용할 **표준 시간대** 를 선택합니다. 
    4. VM을 자동으로 시작해야 하는 **요일** 을 선택합니다. 
    5. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

        ![자동 시작 설정](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 이 정책은 랩의 가상 머신에 자동 시작을 자동으로 적용하지 않습니다. 개별 가상 머신을 **옵트인** 하려면 가상 머신 페이지로 이동하여 해당 VM에 대해 **자동 시작** 을 사용하도록 설정합니다.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>랩의 VM에 대해 자동 시작 사용
다음 절차는 랩의 자동 시작 정책에 VM을 옵트인하는 단계를 제공합니다. 

1. 랩의 홈페이지에 있는 **내 가상 머신** 목록에서 **VM** 을 선택합니다. 

    ![구성 및 정책 메뉴](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. **가상 머신** 페이지의 왼쪽 메뉴 또는 **일정** 목록에서 **자동 시작** 을 선택합니다. 

    ![자동 시작 선택 메뉴](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. **자동 시작** 페이지에 있는 **이 가상 머신의 자동 시작 예약을 허용** 옵션에서 **켜기** 를 선택합니다.

    ![VM에 대해 자동 시작 사용](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 


## <a name="next-steps"></a>다음 단계
랩에 대한 자동 종료 정책을 구성하는 방법을 알아보려면 [Azure DevTest Labs에서 랩에 대한 자동 종료 정책 관리](devtest-lab-auto-shutdown.md)를 참조하세요.
