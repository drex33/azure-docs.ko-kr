---
title: 랩에서 VM 크기 조정
description: CPU, 네트워크 또는 디스크 성능에 대한 요구 사항에 따라 Azure DevTest Labs에서 VM(가상 머신)의 크기를 변경하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 03339a1d500611bde31ebfa14101a0dbd7d1da08
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649495"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에서 VM 크기 조정
Azure Virtual Machines의 주요 기능 중 하나는 CPU, 네트워크 또는 디스크 성능 관련 요구에 따라 VM(가상 머신) 크기를 변경할 수 있다는 것입니다. Azure DevTest Labs의 랩에서는 현재 VM에 대해 이 기능이 지원됩니다. 크기 조정 기능은 랩에서 허용되는 VM 크기와 관련한 랩 정책을 따릅니다. 즉, VM의 크기는 랩에서 허용되는 크기로만 변경할 수 있습니다. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>랩에서 VM의 크기를 조정하는 단계 
Azure DevTest Labs의 랩에서 VM의 크기를 조정하려면 다음 단계를 수행합니다. 

> [!NOTE]
> RDP(원격 데스크톱 세션)를 통해 VM에 연결한 경우 VM 크기를 조정하기 전에 작업 내용을 저장하고 VM에서 연결을 끊습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 크기를 조정할 VM이 포함된 랩을 선택합니다.  
4. 왼쪽 패널에서 **내 Virtual Machine** 을 선택합니다. 
5. VM 목록에서 원하는 VM을 선택합니다.
6. VM이 실행되고 있으면 도구 모음에서 **중지** 를 선택합니다. **알림** 창에서 작업의 상태를 확인합니다. VM이 중지될 때까지 기다렸다가 **알림** 창을 닫습니다. 

    ![VM을 중지합니다.](media/devtest-lab-resize-vm/stop-vm.png)
1. VM의 가상 머신 페이지에서 왼쪽 메뉴의 **설정** 아래에 있는 **크기** 를 선택합니다.

    ![크기 메뉴](media/devtest-lab-resize-vm/size-menu.png)
1. **크기 선택** 창에서 VM에 원하는 크기를 찾아서 선택한 다음 **선택** 을 클릭합니다.     
1. **알림** 창에서 크기 조정 작업의 상태를 확인합니다.

    ![크기 조정 상태](media/devtest-lab-resize-vm/resize-status.png)
10. 크기 조정 작업이 정상적으로 완료되면 **알림** 창을 닫습니다. 
11. 왼쪽 메뉴에서 **개요** 를 선택하고 도구 모음에서 **다시 시작** 을 선택하여 VM을 다시 시작합니다. 

## <a name="next-steps"></a>다음 단계
Azure Virtual Machines에서 지원되는 크기 조정 기능에 대한 자세한 내용은 [가상 머신 크기 조정](https://azure.microsoft.com/blog/resize-virtual-machines/)을 참조하세요.
