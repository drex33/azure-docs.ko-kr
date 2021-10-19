---
title: Teams 내에서 Azure Lab Services 랩 시작 및 만들기
description: Teams 내에서 Azure Lab Services 랩을 시작하고 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e406a73a086a742bcb30d0c45c1ebda629b6bdf2
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180232"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Teams 내에서 Lab Services 랩 시작 및 만들기

이 문서에서는 팀에 **Azure Lab Services** 앱을 추가하고 MS Teams 환경 내에서 랩을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 가상 머신을 사용하여 팀에 대한 랩을 설정합니다. 랩 계정에서 랩을 설정하려면 랩 계정에서 소유자, 랩 작성자 또는 기여자 역할 중 하나를 수행하는 멤버여야 합니다. 랩 계정을 만드는 데 사용된 계정은 소유자 역할에 자동으로 추가됩니다. 따라서 랩을 만드는 데 사용한 랩 계정을 만드는 데 사용한 사용자 계정을 사용할 수 있습니다.

Teams 내에서 Azure Lab Services를 사용하는 일반적인 워크플로는 다음과 같습니다.

1. 사용자가 Azure Portal에서 [랩 계정을 생성](tutorial-setup-lab-account.md#create-a-lab-account)합니다.
1. [랩 계정 작성자는 다른 사용자를 **랩 작성자** 역할에 추가](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)합니다. 예를 들어 랩 계정 작성자/관리자는 **랩 작성자** 역할에 교육자를 추가하여 해당 클래스에 대한 랩을 만들 수 있습니다.
1. 그런 다음, 교육자는 랩을 만들고, 템플릿 VM을 미리 구성하고, 팀의 모든 사람에게 VM을 프로비저닝하는 랩을 게시합니다.
1. 랩을 게시한 후에는 Teams(SSO) 내에서 **Azure Lab Services** 앱을 포함하는 탭을 클릭하거나 [랩 웹 사이트](https://labs.azure.com)에 액세스하여 Azure Lab Services에 처음 로그인하는 팀 멤버 자격 목록에 있는 모든 사용자에게 VM이 할당됩니다. 그러면 사용자가 VM을 사용하여 클래스 작업 및 과제를 수행할 수 있습니다.

> [!IMPORTANT]
> 랩 계정이 Teams와 같은 테넌트에 생성된 경우에만 Teams 내에서 Azure Lab Services를 사용할 수 있습니다.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>팀에 탭으로 Azure Lab Services 앱 추가

Teams 소유자는 Teams 채널에서 직접 **Azure Lab Services** 앱을 추가할 수 있으며, 팀의 모든 사용자가 앱을 사용할 수 있습니다. 다음 세 단계를 수행합니다.

1. 앱을 추가하려는 Teams 채널로 이동하고 **+** 를 선택하여 탭을 추가합니다. 
1. 탭 옵션에서 **Azure Lab Services** 를 검색하고 이 앱을 추가합니다. 

    > [!NOTE]
    > 팀 **소유자** 만이 팀에 대한 랩을 만들 수 있습니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="탭 추가":::
1. 이 팀에서 랩을 만드는 데 사용할 Lab Services 계정을 선택합니다. 

    Azure Lab Services에서 [Azure Lab Services 웹 사이트](https://labs.azure.com)에 Single Sign-On을 사용하고 액세스 권한이 있는 모든 랩 계정을 가져옵니다. 

    Teams와 같은 테넌트에 있으면서 **소유자**, **기여자** 또는 **작성자** 액세스 권한이 있는 계정이 표시됩니다. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="ALS 시작":::
1. **저장** 을 누르면 탭이 채널에 추가됩니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="ALS 탭 생성":::

    이제 채널에서 **Azure Lab Services** 탭을 선택하고 다음 문서에 설명된 대로 랩 관리를 시작할 수 있습니다.

랩 계정을 선택하면 팀 소유자는 팀에 대한 랩을 만들 수 있습니다. 전체 랩 만들기 프로세스와 랩 수준의 모든 작업은 Teams 내에서 수행할 수 있습니다. 사용자는 동일한 팀 내에서 여러 랩을 만들 수 있으며, 랩 계정 수준에서 적절한 액세스 권한을 가진 Teams 소유자는 특정 팀과 연결된 랩만 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Teams 내에서 랩을 만들면 랩 사용자 목록이 자동으로 채워지고 팀 멤버 자격과 동기화됩니다. 소유자, 멤버, 게스트를 비롯한 팀의 모든 사용자가 랩 사용자 목록에 자동으로 추가됩니다. Azure Lab Services는 팀 멤버 자격과 동기화를 유지하며 자동 동기화는 24시간마다 트리거됩니다. 자세한 내용은 다음을 참조하세요.

[Teams 내의 Lab Services 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>참고 항목

다음 문서도 참조합니다.

- [Teams 내에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [Teams 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [Teams 내에서 랩 일정 만들기 및 관리](how-to-create-schedules-within-teams.md)
- [Teams 내에서 VM 액세스 – 학생 보기](how-to-access-vm-for-students-within-teams.md)
- [Teams 내에서 랩 삭제](how-to-delete-lab-within-teams.md)
