---
title: Teams에서 Azure Lab Services의 VM 풀 관리
description: Teams에서 Azure Lab Services의 VM 풀을 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 035344fa947403b77c35d716636472730d65752b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180156"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Teams에서 Lab Services의 VM 풀 관리

VM(가상 머신)을 만드는 작업은 템플릿 VM을 처음 게시하는 즉시 시작됩니다. 랩 사용자 목록의 사용자 수와 같은 VM이 생성됩니다. 학생이 Azure Lab Services에 처음 로그인할 때 VM이 자동으로 할당됩니다. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>템플릿 게시 및 VM 풀 관리

템플릿을 게시하려면 Teams Lab Services 창으로 이동한 다음 **템플릿** 탭 -> **...**  -> **게시** 를 선택합니다.

템플릿 VM이 구성된 후 교육자가 템플릿을 게시하도록 선택하면 랩 사용자 목록의 사용자 수와 같은 개수의 VM이 생성됩니다. 랩을 게시하고 VM이 생성된 후에는 사용자가 자동으로 랩에 등록되며 Azure Lab Services에 처음 로그인할 때, 즉 **Azure Lab Services** 앱이 있는 탭에 처음 액세스할 때 VM이 할당됩니다. 

사용자 목록 동기화가 트리거되면 랩 용량(랩의 VM 수)이 팀 멤버 자격의 변경 내용에 따라 자동으로 업데이트됩니다. 새 사용자를 추가하면 새 VM이 생성되고 팀에서 제거한 사용자에게 할당된 VM도 삭제됩니다. 자세한 내용은 [Teams 내에서 사용자를 관리하는 방법](how-to-manage-user-lists-within-teams.md)을 참조하세요. 

교육자는 VM 풀 탭에서 학생 VM에 직접 액세스할 수 있습니다. 또한 교육자는 **가상 머신 풀** 탭에서 또는 화면 오른쪽 위에 있는 **내 가상 머신** 단추를 클릭하여 자신에게 할당된 VM에 액세스할 수 있습니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM 풀":::

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [Teams 내에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [Teams에서 Lab Services 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [Teams에서 Lab Services 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [Teams에서 Lab Services 일정 만들기](how-to-create-schedules-within-teams.md)
- [Teams에서 Lab Services의 VM(학생 뷰) 액세스](how-to-access-vm-for-students-within-teams.md)


