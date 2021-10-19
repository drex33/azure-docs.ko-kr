---
title: Teams에서 Azure Lab Services 사용자 목록 관리
description: Teams에서 Azure Lab Services 사용자 목록을 관리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 598edd21943364d25f59848236f838cb10fa3ce5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180194"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Teams에서 Lab Services 사용자 목록 관리

Teams 내에서 랩을 만들 때([Teams에서 Lab Services 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md) 참조) 랩 사용자 목록은 자동으로 채워지고 팀 멤버 자격과 동기화됩니다. 소유자, 멤버, 게스트를 비롯한 팀의 모든 사용자가 랩 사용자 목록에 자동으로 추가됩니다. Azure lab Services에서 팀 멤버 자격과 동기화를 유지 관리하며 24시간마다 자동 동기화가 트리거됩니다. 

## <a name="sync-users"></a>사용자 동기화

교육자는 팀 멤버 자격이 업데이트된 후 **동기화** 단추를 사용하여 수동 동기화를 트리거할 수 있습니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="사용자 동기화":::

자동 또는 수동 동기화가 완료되면 랩이 게시되었는지 여부에 따라 다음이 적용됩니다.

* 랩이 한 번도 게시되지 않은 경우:
    * 팀 멤버 자격의 변경 내용에 따라 랩 사용자 목록에 사용자가 추가되거나 삭제됩니다. 
* 랩이 한 번 이상 게시된 경우 사용자를 추가하거나 삭제하는 것 외에도 랩 용량이 자동으로 업데이트됩니다.
    * 팀에 새 사용자를 추가하면 새 VM이 생성됩니다.
    * 팀에서 사용자를 삭제한 경우 연결된 VM도 삭제됩니다.

## <a name="next-steps"></a>다음 단계

템플릿 VM이 구성된 후 교육자가 템플릿을 게시하도록 선택하면 랩 사용자 목록의 사용자 수와 같은 개수의 VM이 생성됩니다. 랩을 게시하고 VM이 생성된 후에는 사용자가 자동으로 랩에 등록되며 Azure Lab Services에 처음 로그인할 때, 즉 **Azure Lab Services** 앱이 있는 탭에 처음 액세스할 때 VM이 할당됩니다. 

템플릿 VM을 게시하려면 Teams Lab Services 창으로 이동한 다음 **템플릿** 탭 -> **...**  -> **게시** 를 선택합니다.

VM 풀을 관리하려면 [Teams에서 Lab Services의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)를 참조하세요.

### <a name="also-review"></a>관련 항목

다음 문서를 참조하세요.

- [Teams 내에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [Teams에서 Lab Services 랩 시작 및 만들기](how-to-get-started-create-lab-within-teams.md)
- [Teams에서 Lab Services 일정 만들기](how-to-create-schedules-within-teams.md)
- [Teams에서 Lab Services의 VM(학생 뷰) 액세스](how-to-access-vm-for-students-within-teams.md)

