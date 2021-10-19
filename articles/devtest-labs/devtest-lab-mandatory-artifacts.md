---
title: Azure DevTest Labs에서 필수 아티팩트 지정
description: 랩에서 Vm (가상 컴퓨터)에 사용자가 선택한 아티팩트를 설치 하기 전에 설치 해야 하는 필수 아티팩트를 지정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 1f755266a3176ed0cdc5a7426850c09d20b2af86
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179244"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에 대한 필수 아티팩트 지정

랩의 소유자는 랩에서 만들어진 모든 머신에 적용되는 필수 아티팩트를 지정할 수 있습니다. 랩의 각 컴퓨터에 Visual Studio Code 설치 해야 할 시나리오를 Imagine 합니다. 이 경우 각 랩 사용자는 컴퓨터가 Visual Studio Code를 수신 하는지 확인 하기 위해 가상 컴퓨터를 만드는 동안 Visual Studio Code 아티팩트를 추가 해야 합니다. 즉, 랩 사용자가 해당 머신에 필수 아티팩트를 적용하는 것을 잊어버린 경우 기본적으로 머신을 다시 만들어야 합니다. 랩 소유자는 랩의 필수 아티팩트로 Visual Studio Code 아티팩트를 만듭니다. 이 단계를 수행 하면 각 컴퓨터가 Visual Studio Code 하 고 랩 사용자의 시간과 노력을 절약할 수 있습니다.
 
다른 필수 아티팩트에는 팀에서 사용 하는 일반적인 도구 또는 각 컴퓨터에 기본적으로 필요한 플랫폼 관련 보안 팩이 포함 될 수 있습니다. 즉, 랩의 모든 머신이 갖춰야 하는 일반 소프트웨어가 필수 아티팩트가 됩니다. 필수 아티팩트가 적용된 머신에서 사용자 지정 이미지를 만든 다음, 해당 이미지에서 새 머신을 만들 경우 생성 동안 필수 아티팩트가 머신에 다시 적용됩니다. 이 동작은 사용자 지정 이미지가 오래됐다고 해도 해당 이미지에서 머신을 만들 때마다 생성 흐름 동안 필수 아티팩트의 최신 업데이트 버전이 해당 이미지에 적용된다는 의미이기도 합니다. 
 
매개 변수가 없는 아티팩트만 필수 아티팩트로 지원됩니다. 랩 사용자는 랩 생성 중에 다른 매개 변수를 입력 하지 않아도 VM 만들기 프로세스를 간단 하 게 만들 수 있습니다. 

## <a name="specify-mandatory-artifacts"></a>필수 아티팩트 지정
Windows 및 Linux 머신에 대한 필수 아티팩트를 각각 선택할 수 있습니다. 적용하려는 순서에 따라 이러한 아티팩트를 다시 정렬할 수도 있습니다. 

1. 랩의 홈 페이지의 **설정** 아래에서 **구성 및 정책** 을 선택합니다. 
3. **외부 리소스** 아래에서 **필수 아티팩트** 를 선택합니다. 
4. **Windows** 또는 **Linux** 섹션에서 **편집** 을 선택합니다. 이 예제에서는 **Windows** 옵션을 사용합니다. 

    ![필수 아티팩트 페이지 - 편집 단추](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. 아티팩트를 선택합니다. 이 예제에서는 **7-Zip** 옵션을 사용합니다. 
5. **아티팩트 추가** 페이지에서 **추가** 를 선택합니다. 

    ![필수 아티팩트 페이지 - 7-zip 추가](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. 다른 아티팩트를 추가하려면 문서를 선택하고 **추가** 를 선택합니다. 이 예제에서는 **Chrome** 을 두 번째 필수 아티팩트로 추가합니다.

    ![필수 아티팩트 페이지 - Chrome 추가](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. **필수 아티팩트** 페이지에 선택한 아티팩트 수를 지정하는 메시지가 표시됩니다. 메시지를 선택 하면 선택한 아티팩트가 표시 됩니다. **저장** 을 선택하여 저장합니다. 

    ![필수 아티팩트 페이지 - 아티팩트 저장](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Linux VM에 대한 필수 아티팩트를 지정하는 단계를 반복합니다. 
    
    ![필수 아티팩트 페이지 - Windows 및 Linux 아티팩트](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. 목록에서 아티팩트를 **삭제** 하려면 행의 끝에서 **...(줄임표)** 를 선택하고 **삭제** 를 선택합니다. 
10. 목록에서 아티팩트를 **다시 정렬** 하려면 행의 시작 부분에 표시되는 **...(줄임표)** 를 선택하고 항목을 끌어 새 위치로 이동합니다. 
11. 랩에 필수 아티팩트를 저장하려면 **저장** 을 선택합니다. 

    ![필수 아티팩트 페이지 - 랩에 아티팩트 저장](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. 랩에 대 한 홈 페이지로 돌아가려면 **구성 및 정책** 페이지를 닫습니다(오른쪽 위 모서리에서 **X** 선택).  

## <a name="delete-a-mandatory-artifact"></a>필수 아티팩트 삭제
랩에서 필수 아티팩트를 삭제하려면 다음 작업을 수행합니다. 

1. **설정** 아래에서 **구성 및 정책** 을 선택합니다. 
2. **외부 리소스** 아래에서 **필수 아티팩트** 를 선택합니다. 
3. **Windows** 또는 **Linux** 섹션에서 **편집** 을 선택합니다. 이 예제에서는 **Windows** 옵션을 사용합니다. 
4. 맨 위에 있는 필수 아티팩트 수를 사용하여 메시지를 선택합니다. 

    ![필수 아티팩트 페이지 - 메시지 선택](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. **선택한 아티팩트** 페이지에서 삭제될 아티팩트에 대해 **...(줄임표)** 를 선택하고 **제거** 를 선택합니다. 
    
    ![필수 아티팩트 페이지 - 아티팩트 제거](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. **확인** 을 선택하여 **선택한 아티팩트** 페이지를 닫습니다. 
7. **필수 아티팩트** 페이지에서 **저장** 을 선택합니다.
8. 필요한 경우 **Linux** 이미지에 대한 단계를 반복합니다. 
9. **저장** 를 선택하여 모든 변경 내용을 랩에 저장합니다. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>VM을 만들 때 필수 아티팩트 보기
이제 랩 사용자는 랩에서 VM을 만드는 동안 필수 아티팩트 목록을 볼 수 있습니다. 랩 소유자가 랩에서 설정한 필수 아티팩트를 편집하거나 삭제할 수 없습니다.

1. 랩에 대한 홈 페이지의 메뉴에서 **개요** 를 선택합니다.
2. 랩에 VM을 추가하려면 **+ 추가** 를 선택합니다. 
3. **기본 이미지** 를 선택합니다. 이 예제에서는 **Windows Server 버전 1709** 를 사용합니다.
4. 선택한 필수 아티팩트 수를 사용하여 **아티팩트** 에 대한 메시지를 확인합니다. 
5. **아티팩트** 를 선택합니다. 
6. 랩의 구성 및 정책에서 지정한 **필수 아티팩트** 가 표시되는지 확인합니다. 

    ![VM 만들기 - 필수 아티팩트](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>다음 단계
* [랩에 Git 아티팩트 리포지토리를 추가](devtest-lab-add-artifact-repo.md)하는 방법에 대해 알아봅니다.
