---
title: Azure Lab Services 및 GNS3을 사용하여 네트워킹 랩 설정 | Microsoft Docs
description: GNS3을 사용해 네트워킹을 가르칠 수 있도록 Azure Lab Services를 사용하여 랩을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 01/19/2021
ms.openlocfilehash: b4e4562ac472b728f5feff1828897d6bee162b75
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176817"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>네트워킹 클래스 지도를 위한 랩 설정 
이 문서에서는 학생들이 [GNS3](https://www.gns3.com/) 소프트웨어를 사용하여 가상 및 실제 네트워크를 에뮬레이트, 구성, 테스트하고 문제를 해결하는 방법을 주로 가르치는 수업을 설정하는 방법을 보여 줍니다. 

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 클래스룸 랩을 만드는 방법에 대해 설명합니다. 두 번째 섹션에서는 GNS3을 설치 및 구성하고 중첩된 가상화를 사용해 템플릿 머신을 만드는 방법에 대해 설명합니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수도 있고 기존 계정을 사용할 수도 있습니다. 새 랩 계정 만들기에 대한 자습서는 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요.

[이 자습서](tutorial-setup-classroom-lab.md)에 따라 새 랩을 만들고 다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- | 
| 대형(중첩된 가상화) | Windows 10 Pro, 버전 1909 |

## <a name="template-machine"></a>템플릿 머신 

템플릿 머신을 만든 후에는 머신을 시작하고 연결하여 다음 세 개의 주요 작업을 완료합니다. 
 
1. 중첩된 가상화를 위한 템플릿 머신을 준비합니다.
2. GNS3을 설치합니다.
3. Hyper-V에서 중첩된 GNS3 VM을 만듭니다.
4. Windows Hyper-V VM을 사용하도록 GNS3을 구성합니다.
5. 적절한 어플라이언스를 추가합니다.
6. 템플릿을 게시합니다.


### <a name="prepare-template-machine-for-nested-virtualization"></a>중첩된 가상화를 위한 템플릿 머신 준비
- 중첩된 가상화를 위한 템플릿 가상 머신을 준비하려면 [이 문서](how-to-enable-nested-virtualization-template-vm.md)의 지침을 따릅니다. 

### <a name="install-gns3"></a>GNS3 설치
- [Windows에서 GNS3을 설치](https://docs.gns3.com/docs/getting-started/installation/windows)하는 방법에 대한 지침을 따릅니다.  구성 요소 대화 상자에 **GNS3 VM** 설치를 포함해야 합니다. 아래를 참조하세요.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

마지막으로, GNS3 VM을 선택하게 됩니다. **Hyper-V** 옵션을 선택해야 합니다.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  이 옵션을 사용하면 Hyper-V 관리자에서 PowerShell 스크립트 및 VHD 파일을 다운로드하여 GNS3 VM을 만듭니다. 기본값을 사용하여 설치를 계속합니다. **설치가 완료되더라도 GNS3을 시작하지 마세요**.

### <a name="create-gns3-vm"></a>GNS3 VM 만들기
설치가 완료되면 드라이브, 그리고 Hyper-V VM을 만드는 PowerShell 스크립트를 포함한 **“GNS3.VM.Hyper-V.2.2.17.zip”** zip 파일이 설치 파일과 동일한 폴더에 다운로드됩니다.
- GNS3.VM.Hyper-V.2.2.17.zip의 내용을 **모두 추출** 합니다.  이 작업을 통해 드라이브, 그리고 VM을 만드는 PowerShell 스크립트를 추출합니다.
- 파일을 마우스 오른쪽 단추로 클릭하여 “create-vm.ps1” PowerShell 스크립트를 **PowerShell로 실행** 합니다.
- 실행 정책 변경 요청이 표시될 수 있습니다. “Y”를 입력하여 스크립트를 실행합니다.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- 스크립트가 완료되면 Hyper-V 관리자에서 VM “GNS3 VM”이 생성되었는지 확인할 수 있습니다.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Hyper-V VM을 사용하도록 GNS3 구성
GNS3이 설치되고 GNS3 VM이 추가되면 GNS3을 시작하여 두 VM을 함께 연결합니다.  [GNS3 설치 마법사가 자동으로 시작됩니다.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- **가상 머신에서 어플라이언스 실행** 옵션을 사용합니다.  마법사의 나머지 과정에서 **VMware vmrun 도구를 찾을 수 없습니다.** 라는 오류가 나타날 때까지 기본값을 사용합니다. error.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- **확인** 을 선택하고 마법사에서 **취소** 를 선택합니다.
- Hyper-V VM에 대한 연결을 완료하려면 **편집** -> **기본 설정** -> **GNS3 VM** 을 열고 **GNS3 VM 사용** 을 선택하고 **Hyper-V** 옵션을 선택합니다.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>적절한 어플라이언스 추가

이제 [클래스에 적절한 어플라이언스](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)를 추가하려고 합니다.

### <a name="publish-template"></a>템플릿 게시

템플릿 VM이 올바르게 설정되고 게시할 준비가 완료되면 몇 가지 주요 사항을 확인해야 합니다.
- GNS3 VM이 종료되었거나 꺼져 있는지 확인합니다.  VM이 계속 실행되는 중에 게시하면 VM이 손상됩니다.
- GNS3을 닫고 실행하는 중에 게시하면 의도하지 않은 부작용이 발생할 수 있습니다.
- 설치 파일 또는 기타 불필요한 파일을 정리합니다.

## <a name="cost"></a>비용  

이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다. 
 
학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은 

학생 25명 * (20 + 10)시간 * 84 랩 단위 * 0.01 USD/시간 = 630USD. 

**중요:** 비용 추정치는 예시용으로만 사용됩니다.  가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="conclusion"></a>결론
이 문서에서는 GNS3을 사용하여 네트워크 구성과 관련된 랩을 만드는 단계를 살펴보았습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)