---
title: 기존 데이터 디스크를 랩 VM에 연결
description: Azure DevTest Labs의 랩 가상 컴퓨터에 랩 데이터 디스크를 연결 하거나 분리 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/26/2021
ms.openlocfilehash: 8afd2fff50a893fb293d7b419c4839c34487c32d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453992"
---
# <a name="attach-or-detach-a-lab-data-disk-to-a-lab-virtual-machine-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩 가상 컴퓨터에 랩 데이터 디스크 연결 또는 분리

랩 Azure VM (가상 머신)에 대 한 새 랩 [데이터 디스크](../virtual-machines/managed-disks-overview.md) 를 만들고 연결할 수 있습니다. 그런 다음 데이터 디스크를 분리할 수 있습니다. 즉, 사용자가 소유 하는 다른 랩 VM에 데이터 디스크를 삭제 하거나 다시 연결 하거나 연결할 수 있습니다. 이 기능은 개별 가상 머신 외부에서 스토리지나 소프트웨어를 관리하는 데 편리합니다.

이 문서에서는 랩 가상 컴퓨터에 데이터 디스크를 연결 하 고 분리 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

랩 가상 머신이 실행 되 고 있어야 합니다. 가상 컴퓨터 크기는 연결할 수 있는 데이터 디스크 수를 제어 합니다. 자세한 내용은 [가상 머신의 크기](../virtual-machines/sizes.md)를 참조하세요.

## <a name="attach-a-new-data-disk"></a>새 데이터 디스크 연결

다음 단계에 따라 새 관리되는 데이터 디스크를 Azure DevTest Labs의 VM에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. 실행 중인 가상 컴퓨터를 선택 합니다.

1. **가상 컴퓨터** 페이지의 **설정** 에서 **디스크** 를 선택 합니다.
 
1. **새 연결** 을 선택 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png" alt-text="가상 컴퓨터에 새 데이터 디스크 연결의 스크린샷":::

1. **새 디스크 연결** 페이지에서 다음 정보를 제공 합니다. 

    |속성 | Description |
    |---|---|
    |Name|고유한 이름을 입력합니다.|
    |디스크 유형| 드롭다운 목록에서 [디스크 유형을](../virtual-machines/disks-types.md) 선택 합니다.|
    |크기 (GiB)|크기 (gb)를 입력 합니다.|

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png" alt-text="' 새 디스크 연결 ' 양식 완료의 스크린샷":::

1. **확인** 을 선택합니다.

1. **가상 컴퓨터** 페이지로 돌아갑니다. **데이터 디스크** 에서 연결 된 디스크를 확인 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attached-data-disk.png" alt-text="연결 된 디스크의 스크린샷 데이터 디스크 아래에 나타납니다.":::

## <a name="detach-a-data-disk"></a>데이터 디스크 분리

분리는 랩 VM에서 랩 디스크를 제거 하지만 나중에 사용할 수 있도록 저장소에 보관 합니다.

### <a name="detach-from-the-vms-management-page"></a>VM의 관리 페이지에서 분리

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. 연결 된 데이터 디스크를 사용 하 여 실행 중인 가상 컴퓨터를 선택 합니다.

1. **가상 컴퓨터** 페이지의 **설정** 에서 **디스크** 를 선택 합니다.

1. **데이터 디스크** 에서 분리 하려는 데이터 디스크를 선택 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png" alt-text="가상 컴퓨터에 대 한 데이터 디스크 선택의 스크린샷":::

1. **데이터 디스크** 페이지에서 **분리** 를 선택 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk-2.png" alt-text="스크린샷은 ' 분리 ' 작업이 강조 표시 된 디스크의 세부 정보 창을 보여 줍니다.":::

1. **확인** 을 선택 하 여 데이터 디스크 분리를 확인 합니다. 디스크가 분리되어 다른 VM에 연결할 수 있습니다. 

### <a name="detach-from-the-labs-management-page"></a>랩의 관리 페이지에서 분리

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. **내 랩에서** **내 데이터 디스크** 를 선택 합니다.

1. 분리할 디스크에 대해 줄임표 (**...**)를 선택 하 고 **분리** 를 선택 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png" alt-text="데이터 디스크 분리의 스크린샷":::

1. **예** 를 선택하여 데이터 디스크를 분리할 것인지 확인합니다.

   > [!NOTE]
   > 데이터 디스크가 이미 분리되어 있는 경우 **삭제** 를 선택하여 사용 가능한 데이터 디스크 목록에서 제거하도록 선택할 수 있습니다.

## <a name="attach-an-existing-disk"></a>기존 디스크 연결

사용 가능한 기존 데이터 디스크를 실행 중인 VM에 연결 하려면 다음 단계를 수행 합니다. 

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. 실행 중인 가상 컴퓨터를 선택 합니다.

1. **가상 컴퓨터** 페이지의 **설정** 에서 **디스크** 를 선택 합니다.

1. **기존 연결** 을 선택 합니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png" alt-text="' 디스크 ' 설정을 선택 하 고 ' 기존 연결 '을 선택한 보여 주는 스크린샷":::

1. **기존 디스크 연결** 페이지에서 디스크를 선택 하 고 **확인** 을 선택 합니다. 잠시 후에 데이터 디스크가 VM에 연결되고 해당 VM의 **데이터 디스크** 목록에 나타납니다.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png" alt-text="기존 데이터 디스크를 가상 컴퓨터에 연결 하는 스크린샷":::

## <a name="upgrade-an-unmanaged-data-disk"></a>관리되지 않는 데이터 디스크 업그레이드

관리 되지 않는 데이터 디스크가 있는 VM이 있는 경우 VM을 변환 하 여 managed disks를 사용할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

먼저 관리 되지 않는 VM에서 [데이터 디스크를 분리](#detach-a-data-disk) 합니다. 그런 다음 관리되는 VM에 [디스크를 다시 연결](#attach-an-existing-disk)하여 데이터 디스크를 관리되지 않는 데이터 디스크에서 관리되는 데이터 디스크로 자동으로 업그레이드합니다.

## <a name="next-steps"></a>다음 단계

[클레임할 수 있는 가상 머신](devtest-lab-add-claimable-vm.md#unclaim-a-vm)의 데이터 디스크를 관리하는 방법을 알아봅니다.
