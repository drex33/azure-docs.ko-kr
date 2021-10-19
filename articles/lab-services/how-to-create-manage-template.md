---
title: Azure Lab Services에서 클래스룸 랩 템플릿 관리 | Microsoft Docs
description: Azure Lab Services에서 클래스룸 랩 템플릿을 만들고 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ed5e4cbbea7a950cb74c072681d992007024881d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180593"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 템플릿 만들기 및 관리
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다.

이 문서에서는 Azure Lab Services의 클래스룸 랩에서 템플릿 가상 머신을 만들고 관리하는 방법을 설명합니다. 

> [!NOTE]
> 랩을 만들면 템플릿 VM이 만들어지지만 시작되지는 않습니다. 이를 시작하고, 연결하고, 랩에 대한 필수 구성 요소 소프트웨어를 설치한 다음, 게시할 수 있습니다. 템플릿 VM을 게시하는 경우 자동으로 종료됩니다(아직 수행하지 않은 경우). 
> 
> 템플릿 VM은 실행될 때 **비용** 이 발생하므로 템플릿 VM을 실행할 필요가 없는 경우 종료해야 합니다. 


## <a name="set-or-update-template-title-and-description"></a>템플릿 제목 및 설명 설정 또는 업데이트
다음 단계에 따라 제목과 설명을 처음 설정한 후 나중에 업데이트합니다. 

1. **템플릿** 페이지에서 랩의 새 **제목** 을 입력합니다.  
2. 템플릿에 대한 새 **설명** 을 입력합니다. 포커스를 텍스트 상자 밖으로 이동하면 자동으로 저장됩니다. 

    ![템플릿 이름 및 설명](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>템플릿 VM 업데이트
템플릿 VM을 업데이트하려면 다음 단계를 따릅니다.  

1. 템플릿 VM이 시작될 때까지 기다린 다음, 도구 모음에서 **템플릿에 연결** 을 선택하여 템플릿 VM에 연결하고 지침을 따릅니다. Windows 머신의 경우 RDP 파일을 다운로드할 수 있는 옵션이 표시됩니다. 
1. 템플릿에 연결하여 변경하면 사용자에게 마지막으로 게시한 가상 머신과 동일한 설정이 더 이상 적용되지 않습니다. 템플릿 변경 내용은 다시 게시할 때까지 사용자의 기존 가상 머신에 반영되지 않습니다.

    ![템플릿 VM에 연결](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. 학생들이 랩을 사용하는 데 필요한 소프트웨어를 설치합니다(예: Visual Studio, Azure Storage Explorer 등). 
1. 템플릿 VM의 연결을 종료합니다(원격 데스크톱 세션 종료). 
1. **템플릿 중지** 를 선택하여 템플릿 VM을 **중지** 합니다. 
1. 다음 섹션의 단계에 따라 업데이트된 템플릿 VM을 **게시** 합니다. 

## <a name="publish-the-template-vm"></a>템플릿 VM 게시  
이 단계에서는 템플릿 VM을 게시합니다. 템플릿 VM이 게시되면 Azure Lab Services에서 템플릿을 사용하여 VM을 랩에 만듭니다. 모든 가상 머신은 템플릿과 구성이 동일합니다.


1. **템플릿** 페이지의 도구 모음에서 **게시** 를 선택합니다. 

    ![템플릿 게시 단추](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 게시한 후에는 게시를 취소할 수 없습니다. 
2. **템플릿 게시** 페이지에서 랩에서 만들려는 가상 머신의 수를 입력한 다음, **게시** 를 선택합니다. 

    ![템플릿 게시 - VM 수](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 템플릿 **게시 상태** 가 페이지에 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 게시가 완료될 때까지 기다린 다음, 왼쪽 메뉴에서 **가상 머신** 을 선택하거나 **가상 머신** 타일을 선택하여 **가상 머신 풀** 페이지로 전환합니다. **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 랩에 액세스](how-to-use-classroom-lab.md)
