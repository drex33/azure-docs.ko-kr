---
title: 랩 액세스
description: 이 자습서에서는 Azure DevTest Labs에서 랩에 액세스합니다. 가상 머신을 사용하여 클레임을 취소한 후 다시 클레임합니다.
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: spelluru
ms.openlocfilehash: d5ace346c8f4b853e862b2e12b8b2adee55bab8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578543"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>자습서: Azure DevTest Labs에서 랩에 액세스

이 자습서에서는 [ 자습서: Azure DevTest Labs에서 랩 만들기](tutorial-create-custom-lab.md)에서 만든 랩을 사용합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 VM에 연결
> * 랩 VM 클레임 취소
> * 랩 VM(가상 머신) 클레임

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure 가상 머신을 사용하는 DevTest Labs의 랩](tutorial-create-custom-lab.md)

## <a name="connect-to-the-lab-vm"></a>랩 VM에 연결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **DevTest Lab** 에서 랩으로 이동합니다.

1. **내 가상 머신** 아래에서 VM을 선택합니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines.png" alt-text="내 가상 머신의 VM 스크린샷":::

1. 위쪽 메뉴에서 **연결** 을 선택합니다. 그런 다음, 머신에 다운로드하는 `.rdp` 파일을 선택합니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/vm-connect.png" alt-text="VM 연결 단추의 스크린샷":::

1. **원격 데스크톱 연결** 대화 상자에서 **연결** 을 선택합니다.

1. **자격 증명 입력** 대화 상자에서 암호를 입력한 다음, **확인** 을 선택합니다.

1. 표시되는 대화 상자에서 **원격 컴퓨터의 ID를 확인할 수 없습니다.** 가 표시되는 경우 **이 컴퓨터로의 연결을 다시 묻지 않음** 확인란을 선택합니다. 그런 다음, **예** 를 선택합니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/remote-computer-verification.png" alt-text="원격 컴퓨터 확인 스크린샷":::

Linux VM에 연결하는 단계는 [Azure에서 Linux VM에 연결](../virtual-machines/linux/use-remote-desktop.md)을 참조하세요. 

## <a name="unclaim-the-lab-vm"></a>랩 VM 클레임 취소

VM 작업을 마친 후에는 다음 단계에 따라 VM을 클레임 취소합니다. 

1. 동일한 이전 단계를 사용하여 DevTest Labs에서 VM을 선택합니다.

1. **가상 머신** 페이지의 위쪽 메뉴에서 **클레임 취소** 를 선택합니다. 

    :::image type="content" source="./media/tutorial-use-custom-lab/virtual-machine-unclaim.png" alt-text="클레임 취소 옵션 스크린샷":::

1. VM은 클레임 취소되기 전에 종료됩니다. **알림** 에서 이 작업의 상태를 모니터링할 수 있습니다.

1. **DevTest Lab 개요** 페이지로 반환될 **가상 머신** 페이지를 닫습니다.

1. **내 랩** 에서 **클레임 가능한 가상 머신** 을 선택합니다. 이제 VM을 클레임할 수 있습니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines.png" alt-text="클레임 가능한 가상 머신 아래의 옵션 스크린샷":::

## <a name="claim-a-lab-vm"></a>랩 VM 클레임

VM을 사용해야 하는 경우 다시 클레임할 수 있습니다.

1. **클레임할 수 있는 가상 머신** 목록에서 **...**(줄임표)를 선택하고 **머신 클레임** 을 선택합니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines-claimed.png" alt-text="클레임 옵션 스크린샷":::

1. **내 가상 머신** 목록에 VM이 있는지 확인합니다.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines-2.png" alt-text="내 가상 머신에 반환된 VM을 보여 주는 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 랩 및 VM을 실행하는 데 드는 요금이 발생하지 않도록 리소스를 삭제합니다. 랩에서 VM에 액세스하기 위한 다음 자습서를 진행하려는 경우 해당 자습서를 완료한 후 리소스를 정리할 수 있습니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요. 

1. 만든 랩의 홈 페이지로 돌아갑니다.

1. 위쪽 메뉴에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/tutorial-use-custom-lab/portal-lab-delete.png" alt-text="랩 삭제 단추의 스크린샷":::

1. **삭제하시겠습니까?** 페이지의 텍스트 상자에서 랩 이름을 입력한 다음, **삭제** 를 선택합니다.

1. 삭제하는 동안 화면 위쪽에서 **알림** 을 선택하여 진행 상황을 볼 수 있습니다. 랩을 삭제하는 데 시간이 걸립니다. 랩이 삭제되면 다음 단계를 계속합니다.

1. 기존 리소스 그룹에서 랩을 만든 경우 모든 랩 리소스가 제거됩니다. 이 자습서에 대한 새 리소스 그룹을 만든 경우 이제 비어 있으며 삭제할 수 있습니다. 랩이 아직 남아 있는 동안에는 리소스 그룹을 먼저 삭제할 수 없습니다.
    
## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure DevTest Labs에서 랩을 액세스하고 사용하는 방법을 알아보았습니다. 랩에서 VM 액세스 및 사용에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [방법: 랩에서 VM 사용](devtest-lab-add-vm.md)
