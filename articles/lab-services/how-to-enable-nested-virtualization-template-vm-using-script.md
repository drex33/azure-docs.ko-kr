---
title: Azure Lab Services의 템플릿 VM에서 중첩된 가상화 사용 (스크립트) | Microsoft Docs
description: 스크립트를 사용하여 여러 VM 내에서 템플릿 VM을 만드는 방법을 알아봅니다.  즉, Azure Lab Services의 템플릿 VM에서 중첩된 가상화를 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: cd2d206ceff358421f7c36f31554a0cb5684a482
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180422"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>스크립트를 사용하여 Azure Lab Services의 템플릿 가상 머신에서 중첩된 가상화 사용

중첩된 가상화를 사용하면 랩의 템플릿 가상 머신 내에 다중 VM 환경을 만들 수 있습니다. 템플릿을 게시하면 랩의 각 사용자에게 여러 개의 VM이 설정된 가상 머신이 제공됩니다.  중첩된 가상화 및 Azure Lab Services에 대한 자세한 내용은 [Azure Lab Services의 템플릿 가상 머신에서 중첩된 가상화 사용](how-to-enable-nested-virtualization-template-vm.md)을 참조하세요.

이 문서의 단계는 Windows Server 2016, Windows Server 2019 또는 Windows 10에 대해 중첩된 가상화를 설정하는 방법에 중점을 둡니다. 스크립트를 사용하여 Hyper-V로 템플릿 머신을 설정합니다.  다음 단계에서는 [Lab Services Hyper-V 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)를 사용하는 방법을 안내합니다.

>[!IMPORTANT]
>새 랩을 만들 때 가상 머신 크기로 **대형(중첩된 가상화)** 또는 **중간(중첩된 가상화)** 을 선택합니다.  그렇지 않으면 중첩된 가상화가 작동하지 않습니다.  

## <a name="run-script"></a>스크립트 실행

1. Internet Explorer를 사용하는 경우 신뢰할 수 있는 사이트 목록에 `https://github.com`을 추가해야 할 수도 있습니다.
    1. Internet Explorer를 엽니다.
    1. 기어 아이콘을 선택한 다음 **인터넷 옵션** 을 선택합니다.  
    1. **인터넷 옵션** 대화 상자가 표시되면 **보안** 을 선택하고 **신뢰할 수 있는 사이트** 를 선택한 다음, **사이트** 단추를 클릭합니다.
    1. **신뢰할 수 있는 사이트** 대화 상자가 표시되면 신뢰할 수 있는 웹 사이트 목록에 `https://github.com`을 추가하고 **닫기** 를 선택합니다.

        ![신뢰할 수 있는 사이트](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 다음 단계에 설명된 대로 Git 리포지토리 파일을 다운로드합니다.
    1. [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)으로 이동합니다.
    1. **복제 또는 다운로드** 단추를 클릭합니다.
    1. **ZIP 다운로드** 를 클릭합니다.
    1. ZIP 파일 추출

    >[!TIP]
    >[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)에서 Git 리포지토리를 복제할 수도 있습니다.

1. **관리자** 모드에서 **PowerShell** 을 시작합니다.
1. PowerShell 창에서 다운로드한 스크립트가 있는 폴더로 이동합니다. 리포지토리 파일의 최상위 폴더에서 탐색하는 경우 스크립트는 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`에 있습니다.
1. 스크립트를 성공적으로 실행하기 위해 실행 정책을 변경해야 할 수도 있습니다. 다음 명령 실행:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 스크립트를 실행합니다.

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 스크립트를 위해 머신을 다시 시작해야 할 수도 있습니다. 스크립트의 지침을 따르고 **스크립트가 완료되었습니다.** 가 출력에 표시될 때까지 스크립트를 다시 실행합니다.
1. 실행 정책을 다시 설정해야 합니다. 다음 명령 실행:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>결론

이제 템플릿 머신에서 Hyper-V 가상 머신을 만들 준비가 되었습니다. Hyper-V 가상 머신을 만드는 방법에 대한 지침은 [Hyper-V에서 가상 머신 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)를 참조하세요. 또한 사용 가능한 운영 체제 및 소프트웨어를 확인하려면 [Microsoft 평가 센터](https://www.microsoft.com/evalcenter/)를 참조하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)