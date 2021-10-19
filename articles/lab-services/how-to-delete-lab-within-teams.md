---
title: Teams에서 Azure Lab Services 랩 삭제
description: Teams에서 Azure Lab Services 랩을 삭제하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f93151e970fc38ca55b43c5322bf27862ad32b7a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180517"
---
# <a name="delete-labs-within-teams"></a>Teams에서 랩 삭제

이 문서는 **Azure Lab Services** 앱에서 랩을 삭제하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Portal에서 [Lab Services 계정을 만듭니다](tutorial-setup-lab-account.md#create-a-lab-account).
* [Teams 내에서 Lab Services 랩을 시작하고 만듭니다](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>랩 삭제

Teams 내에서 만든 랩은 [Azure Lab Services에서 랩 관리](how-to-manage-classroom-labs.md)에 설명된 대로 직접 랩을 삭제함으로써 [Lab Services 웹 사이트](https://labs.azure.com)에서 삭제할 수 있습니다. 

랩 삭제는 팀이 삭제될 때도 트리거됩니다. 랩이 만들어진 팀이 삭제되면 자동 사용자 목록 동기화가 트리거된 후 24시간 후에 랩이 자동으로 삭제됩니다. 

> [!IMPORTANT]
> 탭을 삭제하거나 앱을 제거해도 랩은 삭제되지 않습니다. 

탭이 삭제되면 웹 사이트에서 랩을 삭제하거나 팀을 삭제하여 랩 삭제를 명시적으로 트리거하지 않는 한, 팀 멤버 자격 목록의 사용자는 여전히 [Lab Services 웹 사이트](https://labs.azure.com)의 VM에 액세스할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [Teams 내에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [Teams 내에서 랩 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [Teams 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [Teams 내에서 랩 일정 만들기 및 관리](how-to-create-schedules-within-teams.md)
- [Teams 내에서 VM에 액세스 – 학생 뷰](how-to-access-vm-for-students-within-teams.md)

