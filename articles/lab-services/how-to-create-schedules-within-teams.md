---
title: Teams 내에서 Azure Lab Services 일정 만들기
description: Teams 내에서 Lab Services 일정을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 5427105d0bac3fd652764345c1d31fd0ef8bb2db
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180574"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Teams 내에서 Lab Services 일정 만들기 및 관리

일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 다음 절차에서는 Classroom(강의실) 랩의 일정을 만들고 관리하는 단계를 제공합니다. 

아래에서는 일정이 랩 가상 머신에 어떻게 영향을 주는지를 확인할 수 있습니다. 

- 템플릿 가상 머신은 일정에 포함되지 않습니다. 
- 할당된 가상 머신만 시작됩니다. 즉, 최종 사용자(학생)가 클레임하지 않은 머신은 예약된 시간에 시작되지 않습니다. 
- 사용자의 클레임 여부에 관계없이 모든 가상 머신이 랩 일정에 따라 중지됩니다. 

> [!IMPORTANT]
> VM의 예약된 실행 시간은 사용자에게 할당된 할당량에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다. 

사용자는 [랩 웹 사이트](https://labs.azure.com)에서와 마찬가지로 Teams 내에서 랩 일정을 만들고, 편집하고, 삭제할 수 있습니다. [일정 만들기 및 관리](how-to-create-schedules-within-teams.md) 문서를 참조하세요.

## <a name="automatic-shutdown-and-disconnect-settings"></a>자동 종료 및 연결 끊기 설정

여러 가지 자동 종료 비용 제어 기능을 사용하도록 설정하여 가상 머신이 활발하게 사용되지 않을 때 추가 비용을 사전에 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능을 결합하면 사용자가 실수로 가상 머신 실행을 방치하는 경우를 대부분 포착할 수 있습니다.
 
- OS가 유휴 상태로 간주하는 가상 머신에서 사용자 연결을 자동으로 끊습니다.
- 사용자가 연결을 끊을 때 가상 머신을 자동으로 종료합니다.
- 시작되었지만 사용자가 연결하지 않는 가상 머신을 자동으로 종료합니다.

자세한 내용은 [랩에 대한 자동 종료 설정 구성](how-to-enable-shutdown-disconnect.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [Teams 내에서 Azure Lab Services 사용 개요](lab-services-within-teams-overview.md)
- [Teams 내에서 시작 및 랩 만들기](how-to-get-started-create-lab-within-teams.md)
- [Teams 내에서 랩 사용자 목록 관리](how-to-manage-user-lists-within-teams.md)
- [Teams 내에서 랩의 VM 풀 관리](how-to-manage-vm-pool-within-teams.md)
- [Teams 내에서 VM 액세스 – 학생 뷰](how-to-access-vm-for-students-within-teams.md)
