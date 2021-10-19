---
title: Azure Lab Services에서 VM에 액세스하는 교육자
description: 이 문서에서는 교육자가 교육자 뷰에서 학생 VM에 액세스하는 방법을 보여줍니다. 예를 들어, 조교는 한 클래스의 교육자이지만 다른 클래스의 학생일 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d20d95f7f86e3116d9daf42417a3a0aae5855bbb
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179643"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>교육자 뷰에서 학생으로 가상 머신에 액세스
이 문서에서는 교육자가 학생으로 참석하는 클래스를 위해 VM에 액세스하는 방법을 보여줍니다. 

이 기능이 도움이 되는 시나리오는 다음과 같습니다. 조교는 한 클래스의 교육자이지만 다른 클래스의 학생입니다. 또한 조교는 자신이 소유하고 있는 랩을 보여주는 교육자 뷰에서 학생 VM을 보고 액세스하려고 합니다. 

## <a name="access-vms-from-educator-view"></a>교육자 뷰에서 VM에 액세스

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)에 로그인합니다. 소유하고 있는 랩을 확인합니다. 이러한 랩은 사용자가 직접 만든 랩 또는 관리자가 소유자로 할당된 랩일 수 있습니다. 자세한 내용은 [기존 랩에 추가 소유자를 추가하는 방법](how-to-add-user-lab-owner.md)을 참조하세요.
2. 학생으로 참석하는 클래스의 VM에 액세스하려면 오른쪽 위 모서리에 있는 컴퓨터 아이콘을 선택합니다. 학생으로 액세스할 수 있는 VM이 표시되는지 확인합니다. 다음 예제에서 사용자는 Python 랩의 조교이지만 Java 랩의 학생입니다. 따라서 사용자는 드롭다운 목록에서 Java 랩의 VM을 볼 수 있습니다. 사용자가 VM을 시작하고 연결할 수 있습니다. 
    
    ![학생 VM에 액세스](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [VM에 연결](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-remote-desktop.md)
- [에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-chromebook-remote-desktop.md)
- [Linux 가상 머신에 원격 데스크톱 사용](how-to-use-remote-desktop-linux-student.md)
