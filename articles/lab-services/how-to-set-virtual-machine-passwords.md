---
title: Azure Lab Services의 VM 암호 설정 | Microsoft Docs
description: Azure Lab Services의 랩에서 VM(가상 머신)의 암호를 설정 및 재설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: b39dcae2f9568785313a77c9a3b2a9afd4f0a581
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180099"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>가상 머신 풀 설정 및 관리 
이 문서에서는 다음 작업을 수행하는 방법을 보여줍니다.

- 랩의 VM(가상 머신) 수 늘리기
- 모든 VM 또는 선택한 VM 시작 
- VM 초기화

## <a name="update-the-lab-capacity"></a>랩 용량 업데이트
랩 용량(랩의 가상 머신 수)을 늘리거나 줄이려면 다음 단계를 수행합니다.

1. **가상 머신 풀** 페이지에서 **랩 용량: &lt;number&gt;개 머신** 을 선택합니다.
2. 랩에서 원하는 새 **VM 수** 를 입력합니다. 이 숫자는 랩에 등록된 사용자 수보다 크거나 같아야 합니다. 
3. 그런 다음 **저장** 을 선택합니다. 

    !["랩의 최대 컴퓨터 수" 및 "저장" 단추가 선택된 "랩 용량" 창을 보여주는 스크린샷](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 용량을 늘린 경우 VM이 생성되는 것을 확인할 수 있습니다. 새 VM이 목록에 표시되지 않으면 페이지를 새로 고칩니다. 

    ![생성되는 VM](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM 시작

### <a name="start-ot-stop-all-vms"></a>모든 VM 시작 또는 중지
1. **가상 머신 풀** 페이지로 전환합니다. 
2. 도구 모음에서 **모든** 시작을 선택합니다. 

    ![모두 시작 단추](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 모든 VM이 시작되면 도구 모음에서 **모두 중지** 단추를 선택하여 모든 VM을 중지할 수 있습니다. 

    ![모두 시작 단추](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>선택한 VM 시작
선택한 VM(하나 이상)을 시작하는 방법은 두 가지입니다. 첫 번째 방법은 목록에서 VM을 선택한 다음, 도구 모음에서 **시작** 을 선택하는 것입니다. 

두 번째 방법은 목록에서 하나 이상의 VM을 선택하고 **상태** 열의 단추를 설정/해제하는 것입니다. 

![선택한 VM 시작](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

마찬가지로 **상태** 열의 단추를 전환하거나 도구 모음의 **중지** 를 선택하여 하나 이상의 VM을 중지할 수 있습니다. 

> [!NOTE]
> 교육자가 학생 VM을 켜는 경우 학생에 대한 할당량에 영향을 주지 않습니다. 사용자에 대한 할당량은 사용자가 예약된 수업 시간 이외의 시간에 사용할 수 있는 랩 시간 수를 지정합니다. 할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정](how-to-configure-student-usage.md?#set-quotas-for-users)을 참조하세요.

## <a name="reset-vms"></a>VM 초기화

하나 이상의 VM을 초기화하려면 목록에서 해당 VM을 선택한 다음, 도구 모음에서 **초기화** 를 선택합니다. 

![선택한 VM 초기화](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

**가상 머신 초기화** 대화 상자에서 **초기화** 를 선택합니다. 

![VM 초기화 대화 상자](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>VM의 암호 설정
랩 소유자(교육자)는 랩을 만들 때(랩 만들기 마법사) 또는 **템플릿** 페이지에서 랩을 만든 후 VM의 암호를 설정/재설정할 수 있습니다. 

### <a name="set-password-at-the-time-of-lab-creation"></a>랩 생성 시 암호 설정
랩 소유자(교육자)는 랩 만들기 마법사의 **가상 머신 자격 증명** 페이지에서 랩의 VM 암호를 설정할 수 있습니다.

![새 랩 창](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

교육자는 이 페이지에서 **모든 가상 머신에 동일한 암호 사용** 옵션을 사용하거나 사용하지 않도록 설정하여 랩의 모든 VM에 동일한 암호를 사용하도록 선택하거나 학생이 VM 암호를 설정하도록 허용할 수 있습니다. 기본적으로 이 설정은 Ubuntu를 제외한 모든 Windows 및 Linux 운영 체제 이미지에 사용하도록 설정됩니다. 이 설정을 사용하지 않도록 설정하면 학생들이 처음으로 VM에 연결을 시도할 때 암호를 설정하라는 메시지가 표시됩니다. 

### <a name="reset-password-later"></a>나중에 암호 재설정

1. 랩의 **템플릿** 페이지에서 도구 모음의 **암호 다시 설정** 을 선택합니다. 
1. **암호 다시 설정** 대화 상자에서 암호를 입력하고 **암호 다시 설정** 을 선택합니다.
    
    ![암호 설정 대화 상자](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>학생 VM에 연결
다음 조건이 충족되면 랩 작성자(교육자)가 학생 VM에 연결할 수 있습니다. 

- 랩을 만들 때 **모든 가상 머신에 동일한 암호 사용** 옵션을 선택한 경우
- VM이 실행 중인 경우 

 학생 VM에 연결하려면 목록에서 해당 VM을 마우스로 가리키고 컴퓨터 단추를 선택합니다.  

![학생 VM에 연결 단추](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 교육자가 VM을 시작하고 연결할 때 학생 할당량은 영향을 받지 않습니다. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>가상 머신 목록을 CSV 파일로 내보내기

1. **가상 머신 풀** 탭으로 전환합니다.
2. 도구 모음에서 **...** (줄임표)를 선택한 후 **CSV 내보내기** 를 선택합니다. 

    ![가상 머신 목록 내보내기](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>다음 단계
랩 소유자가 구성할 수 있는 다른 학생 사용량 옵션에 대해 알아보려면 다음 문서를 참조하세요. [학생 사용량 구성](how-to-configure-student-usage.md)

학생이 자신의 VM에 대한 암호를 다시 설정하는 방법에 대한 자세한 내용은 [랩에서 가상 머신의 암호 설정 또는 다시 설정(학생)](how-to-set-virtual-machine-passwords-student.md)을 참조하세요.