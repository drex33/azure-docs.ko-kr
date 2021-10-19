---
title: 학습에 랩 사용 - Azure Lab Services
description: 이 문서에서는 학습 시나리오를 위해 Azure에서 랩을 만들기 위해 Azure DevTest Labs를 사용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e99c3f9d8f27571a88fcdf6d62fa16a86d180a1c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178617"
---
# <a name="use-labs-for-trainings"></a>학습에 랩 사용
교육자(교사, 교수, 강사 또는 교육 도우미 등)는 Azure Labs Services를 통해 쉽고 빠르게 온라인 랩을 만들어 실습생용으로 미리 구성된 학습 환경을 프로비저닝할 수 있습니다. 각 실습생은 학습에 동일하고 격리된 환경을 사용할 수 있습니다. 각 실습생이 필요할 때에만 학습 환경을 이용할 수 있도록 하고, 학습에 필요한 리소스(예: 가상 머신)가 학습 환경에 충분히 포함되도록 하는 정책을 적용할 수 있습니다. 

![클래스룸 랩](./media/classroom-labs-scenarios/classroom.png)

랩은 모든 가상 환경에서 학습을 수행하는 데 필요한 다음과 같은 요구 사항을 충족합니다. 

- 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
- 모든 학습 컴퓨터는 동일해야 합니다.
- 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
- 실습생이 학습에 필요한 만큼의 VM만 사용하고 사용하지 않을 때는 VM을 종료하도록 하여 비용을 제어합니다.
- 각 실습생과 학습 랩을 쉽게 공유합니다.
- 학습 랩을 반복해서 다시 사용합니다.

이 문서에서는 앞에서 설명한 학습 요구 사항을 충족하는 데 사용할 수 있는 여러 Azure Lab Services 기능과 학습용 랩을 설정하는 데 수행할 수 있는 자세한 단계에 대해 학습합니다.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>랩 계정 관리자로 랩 계정 만들기
Azure Lab Services를 사용하는 첫 번째 단계는 Azure Portal에서 랩 계정을 만드는 것입니다. 랩 계정 관리자가 랩 계정을 만든 후에는 관리자가 **랩 작성자** 역할에 랩을 만들려는 사용자를 추가합니다. 교육자들은 가르치고 있는 과정에 대한 연습을 학생들이 수행하도록 가상 머신이 있는 랩을 만듭니다. 자세한 내용은 [랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)를 참조하세요.

## <a name="create-and-manage-labs"></a>랩 만들기 및 관리
랩 계정에서 랩 작성자 역할의 멤버인 교육자는 랩 계정에서 랩을 하나 이상 만들 수 있습니다. 과정에서 연습을 수행하는 데 필요한 모든 소프트웨어를 사용하여 템플릿 VM을 만들고 구성합니다. 클래스룸 랩을 만들기 위해 사용 가능한 이미지에서 미리 만든 이미지를 선택한 다음, 랩에 필요한 소프트웨어를 설치하여 사용자 지정합니다. 자세한 내용은 [랩 만들기 및 관리](how-to-manage-classroom-labs.md)를 참고하세요.

## <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
랩 작성자는 랩에 사용자를 추가 또는 제거하고, 랩 사용자에게 보낼 등록 링크를 가져오고, 사용자별 개별 할당량을 설정하는 등의 정책을 설정하고, 랩에서 사용할 수 있는 VM의 수를 업데이트하는 등의 작업을 수행할 수 있습니다. 자세한 내용은 [사용 설정 및 정책 구성](how-to-configure-student-usage.md)을 참조하세요.

## <a name="create-and-manage-schedules"></a>일정 만들기 및 관리
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 자세한 내용은 [랩의 일정 만들기 및 관리](how-to-create-schedules.md)를 참고하세요.

## <a name="set-up-and-publish-a-template-vm"></a>템플릿 VM 설정 및 게시
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 학습 참여자에게 제공하려는 구성과 정확히 일치하도록 템플릿 VM을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 자세한 내용은 [템플릿 가상 머신 설정 및 게시](how-to-create-manage-template.md)를 참조하세요. 

## <a name="use-vms-in-the-classroom-lab"></a>클래스룸 랩에서 VM 사용
학생 또는 학습 참석자는 랩에 등록하고 과정에 대한 연습을 위해 VM에 연결합니다. 자세한 내용은 [클래스룸 랩에 액세스하는 방법](how-to-use-classroom-lab.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[자습서: Azure Lab Services로 랩 계정 설정](tutorial-setup-lab-account.md) 문서의 지침에 따라 랩에서 랩 계정 만들기를 시작합니다.