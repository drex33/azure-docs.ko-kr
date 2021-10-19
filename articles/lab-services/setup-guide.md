---
title: Azure Lab Services에 대한 가속 랩 설정 가이드
description: 랩 작성자는 이 가이드를 통해 학교에서 랩 계정을 신속하게 설정할 수 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8d370ee6ffef902633e7b0c4c2895c2ba475814c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177634"
---
# <a name="lab-setup-guide"></a>랩 설정 가이드

이 가이드에서는 학교의 학생을 위한 랩을 만드는 방법을 알아봅니다.

학생에게 랩을 게시하는 프로세스는 최대 몇 시간이 걸릴 수 있습니다. 설치 시간은 랩에서 만들려는 VM(가상 머신)의 수에 따라 달라집니다. 랩이 제대로 작동하는지 확인하고 학생의 VM을 게시할 수 있는 충분한 시간을 확보하기 위해 최소 하루 이상을 허용합니다.

## <a name="understand-the-lab-requirements-of-your-class"></a>클래스 랩 요구 사항 이해

새 랩을 설정하기 전에 다음 질문을 고려해야 합니다.

### <a name="what-software-requirements-does-the-class-have"></a>클래스에서 소프트웨어 요구 사항은 무엇인가요?

랩의 VM에 설치해야 하는 운영 체제, 애플리케이션 및 도구를 결정할 때는 클래스의 학습 목표를 참조하세요. 랩 VM을 설정하려면 다음 세 가지 옵션을 사용할 수 있습니다.

- **Azure Marketplace 이미지 사용**: Azure Marketplace는 랩을 만들 때 사용할 수 있는 수백 개의 이미지를 제공합니다. 일부 클래스의 경우 이러한 이미지 중 하나에 클래스에 필요한 모든 항목이 이미 포함되어 있을 수 있습니다.

- **새 사용자 지정 이미지 만들기**: Azure Marketplace 이미지를 시작점으로 사용하여 고유의 사용자 지정 이미지를 만들 수 있습니다. 그런 다음, 추가 소프트웨어를 설치하고 구성을 변경하여 이미지를 사용자 지정할 수 있습니다.

- **기존 사용자 지정 이미지 사용**: 이전에 만들었거나 학교의 다른 관리자나 교직원이 만든 사용자 지정 이미지를 재사용할 수 있습니다. 사용자 지정 이미지를 사용하려면 관리자가 Shared Image Gallery를 설정해야 합니다.  Shared Image Gallery는 사용자 지정 이미지를 저장하는 데 사용되는 리포지토리입니다.

> [!NOTE]
> 관리자는 Azure Marketplace 이미지와 사용자 지정 이미지를 사용하도록 설정해야 합니다. 필요한 이미지를 사용할 수 있도록 하기 위해 IT 부서와 조정합니다. 사용자가 만든 사용자 지정 이미지는 사용자의 랩 내에서 자동으로 사용하도록 설정됩니다.

### <a name="what-hardware-requirements-does-the-class-have"></a>클래스에서 하드웨어 요구 사항은 무엇인가요?

다음과 같이 다양한 컴퓨팅 크기 중에서 선택할 수 있습니다.

- **중첩된 가상화 크기**: 학생에게 중첩된 여러 VM을 호스트할 수 있는 VM에 대한 액세스 권한을 부여할 수 있습니다. 예를 들어, 네트워킹 또는 윤리적 해킹 수업을 위해 이 컴퓨팅 크기를 사용할 수 있습니다.

- **GPU 크기**: 학생이 컴퓨팅 집약적인 애플리케이션 형식을 사용할 수 있도록 합니다. 예를 들어, 이 옵션은 인공 지능 및 기계 학습과 함께 사용되는 경우가 많습니다.

적절한 VM 크기를 선택하기 위한 지침은 다음을 참조하세요.
- [VM 크기 조정](./administrator-guide.md#vm-sizing)
- [물리적 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> 컴퓨팅 크기 가용성은 지역에 따라 다르기 때문에 랩에서 사용할 수 있는 크기가 더 작을 수 있습니다. 일반적으로 사용자의 요구에 적합한 가장 작은 컴퓨팅 크기를 선택해야 합니다. Azure Lab Services를 사용하면 필요한 경우 나중에 더 큰 컴퓨팅 용량으로 새로운 랩을 설정할 수 있습니다.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>클래스에서 외부 Azure 또는 네트워크 리소스에 대해 어떤 종속성이 있나요?
랩 VM은 데이터베이스, 파일 공유 또는 라이선스 서버와 같은 외부 리소스에 액세스해야 할 수 있습니다.  랩 VM에서 외부 리소스를 사용할 수 있도록 하려면 IT 관리자와 조정합니다.

> [!NOTE]
> VM에서 직접 네트워크 리소스를 제공하여 랩의 외부 리소스에 대한 종속성을 줄일 수 있는지 고려해야 합니다. 예를 들어 외부 데이터베이스에서 데이터를 읽을 필요가 없도록 VM에 직접 데이터베이스를 설치하면 됩니다.  

### <a name="how-will-you-control-costs"></a>비용을 어떻게 제어하나요?
랩 서비스는 종량제 가격 책정 모델을 사용하므로 랩 VM이 실행되는 시간에 대해서만 비용을 지불합니다. 비용을 제어하려면 다음 옵션 중 일부 또는 전체를 사용합니다.

- **일정**: 일정을 사용하면 랩의 VM이 시작되고 종료되는 시간을 자동으로 제어할 수 있습니다.
- **할당량**: 할당량을 사용하여 학생이 예정된 시간 외에 VM에 액세스할 수 있는 시간을 제어합니다.  학생이 VM을 사용하고 할당량에 도달하면 VM이 자동으로 종료됩니다.  할당량을 늘리지 않으면 학생이 VM을 다시 시작할 수 없습니다.
- **자동 종료**: 자동 종료 설정을 사용하도록 설정하면 학생이 RDP(원격 데스크톱 프로토콜) 세션에서 연결을 끊은 후 Windows VM이 자동으로 종료됩니다. 기본적으로 이 설정은 사용하지 않도록 설정되어 있습니다.

비용 제어에 대한 자세한 내용은 다음을 참조하세요.
- [비용 예측](./cost-management-guide.md#estimate-the-lab-costs)
- [비용 관리](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>학생은 어떻게 작업을 저장하나요?
각 개별 학생은 랩의 수명 동안 VM에 할당됩니다. 학생들은 다음 위치에 자신의 작업을 저장할 수 있습니다.

- VM에
- OneDrive 또는 GitHub와 같은 외부 위치에 랩 VM에서 학생을 위해 OneDrive를 자동으로 구성할 수 있습니다.

> [!NOTE]
> 학생들이 랩 외부에 저장된 작업에 계속 액세스할 수 있도록 하고 클래스가 종료된 후에는 학생들은 외부 리포지토리에 작업을 저장하는 것이 좋습니다.

### <a name="how-will-students-connect-to-their-vms"></a>학생이 해당 VM에 어떻게 연결하나요?
Windows VM에 대한 RDP 연결의 경우 학생이 [Microsoft 원격 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)를 사용하는 것이 좋습니다. 원격 데스크톱 클라이언트는 Mac, Chromebook 및 Windows 디바이스를 지원합니다.

Linux VM의 경우 학생이 SSH(Secure Shell) 또는 RDP 프로토콜 중 하나를 사용할 수 있습니다. 학생이 RDP를 사용하여 연결하도록 하려면 필요한 RDP 및 GUI(그래픽 사용자 인터페이스) 패키지를 설치하고 구성해야 합니다.

### <a name="will-students-also-use-microsoft-teams"></a>학생들이 Microsoft Teams도 사용하나요?
Azure Lab Services는 교직원 멤버가 Teams에서 랩을 만들고 관리할 수 있도록 Microsoft Teams와 통합됩니다.  마찬가지로 학생들은 Teams에서 랩에 액세스할 수 있습니다.

자세한 내용은 [Microsoft Teams의 Azure Lab Services](./lab-services-within-teams-overview.md)를 참조하세요.

## <a name="set-up-your-lab"></a>랩 설정

클래스 랩에 대한 요구 사항을 파악하면 설정할 준비가 된 것입니다. 방법을 알아보려면 이 섹션의 링크를 따르세요. 또한 Teams에서 랩을 설정하기 위한 지침도 제공됩니다.

1. **랩을 만듭니다.** 다음 자습서를 참조하세요.
    - [클래스룸 랩 만들기](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Teams에서 랩 만들기](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > 클래스에 중첩된 가상화가 필요한 경우 [중첩된 가상화 사용](./how-to-enable-nested-virtualization-template-vm.md)을 참조하세요.

1. **이미지를 사용자 지정하고 랩 VM을 게시합니다**. 템플릿 VM이라는 특수한 VM에 연결하려면 다음을 참조하세요.
    - [템플릿 VM 만들기 및 관리](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [공유 이미지 갤러리 사용](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Windows를 사용하는 경우 [Windows 템플릿 VM 설정](./how-to-prepare-windows-template.md)도 참조하세요. 이 지침에는 학생들을 위해 OneDrive 및 Microsoft Office를 설정하는 단계가 포함되어 있습니다.

1. **VM 풀 및 용량을 관리합니다**. 클래스에 필요한 만큼 VM 용량을 쉽게 확장하거나 축소할 수 있습니다. VM 용량을 늘리려면 새 VM을 설정하게 되므로 몇 시간이 걸릴 수 있습니다. 다음 문서를 참조하세요.
    - [VM 풀 설정 및 관리](./how-to-set-virtual-machine-passwords.md)
    - [Teams에서 Lab Services의 VM 풀 관리](./how-to-manage-vm-pool-within-teams.md)

1. **랩 사용자를 추가 및 관리합니다**. 랩에 사용자를 추가하려면 다음을 참조하세요.
   - [랩에 사용자 추가](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [사용자에게 초대 보내기](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Teams의 Lab Services 사용자 목록 관리](./how-to-manage-user-lists-within-teams.md)

    학생이 사용할 수 있는 계정 유형에 대한 자세한 내용은 [학생 계정](./how-to-configure-student-usage.md#student-accounts)을 참조하세요.
  
1. **비용 제어를 설정합니다**. 일정을 설정 고 할당량을 설정하고 자동 종료를 사용하도록 설정하려면 다음 자습서를 참조하세요.

   - [일정 설정](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > 설치한 운영 체제의 유형에 따라 VM을 시작하는 데 몇 분 정도 걸릴 수 있습니다. 예약된 시간 동안 랩 VM을 사용할 준비가 되었는지 확인하려면 30분 전에 시작하는 것이 좋습니다.

   - [사용자에 대한 할당량 설정](./how-to-configure-student-usage.md#set-quotas-for-users) 및 [특정 사용자에 대한 추가 할당량 설정](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [연결 해제 시 자동 종료 사용](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > 일정 및 할당량은 템플릿 VM에 적용되지 않지만 자동 종료 설정은 적용됩니다. 
        > 
        > 랩을 만들면 템플릿 VM이 만들어지지만 시작되지는 않습니다. 템플릿 VM을 시작하고, 연결하고, 랩에 대한 필수 구성 요소 소프트웨어를 설치한 다음, 게시할 수 있습니다. 템플릿 VM을 게시하는 경우 자동으로 종료됩니다(수동으로 종료하지 않은 경우). 
        > 
        > 템플릿 VM은 실행될 때 *비용* 이 발생하므로 템플릿 VM을 실행할 필요가 없는 경우 종료해야 합니다.

    - [Teams에서 Lab Services 일정 만들기 및 관리](./how-to-create-schedules-within-teams.md) 

1. **대시보드를 사용합니다**. 지침은 [클래스룸 랩 대시보드 사용](./use-dashboard.md)을 참조하세요.

    > [!NOTE]
    > 대시보드에 표시되는 예상 비용은 학생의 랩 사용에 대해 부과될 것으로 예상할 수 있는 최대 비용입니다. 예를 들어 학생이 사용하지 않은 할당량 시간에 대한 요금이 청구되지 *않습니다*. 예상 비용에는 랩 작성자가 사용자 머신을 시작할 경우나 템플릿 VM 또는 공유 이미지 갤러리 사용에 대한 요금이 반영되지 않습니다.

## <a name="next-steps"></a>다음 단계

랩을 관리하는 과정에서 다음 문서를 참조하세요.
- [클래스룸 랩 사용량 추적](tutorial-track-usage.md).  
- [클래스룸 랩 액세스](tutorial-connect-virtual-machine-classroom-lab.md)
