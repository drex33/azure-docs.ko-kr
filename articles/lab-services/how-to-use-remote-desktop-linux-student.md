---
title: Azure Lab Services에서 Linux VM에 연결 | Microsoft Docs
description: Azure Lab Services의 랩에서 Linux 가상 머신에 대해 원격 데스크톱을 사용하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8cbb4b9366aa8de5827f010f0759fe4235cf1343
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263385"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Azure Lab Services의 교실 랩에서 Linux 가상 머신에 연결
이 문서에서는 학생이 다음을 사용하여 랩에서 Linux VM(가상 머신)에 연결할 수 있는 방법을 보여 줍니다.
- SSH(보안 셸 프로토콜) 터미널
- GUI(그래픽 사용자 인터페이스) 원격 데스크톱

> [!IMPORTANT] 
> SSH는 학생 및 강사가 추가 설치 없이 Linux VM에 대해 SSH를 수행할 수 있도록 자동으로 구성됩니다. 그러나 학생들이 GUI 원격 데스크톱을 사용하여 연결해야 하는 경우 강사는 추가 설치를 수행해야 할 수 있습니다.  자세한 내용은 [Linux 가상 머신에 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조하세요.

## <a name="connect-to-the-student-vm-using-ssh"></a>SSH를 사용하여 학생 VM에 연결

1. 학생이 랩 포털에 직접(`https://labs.azure.com`) 또는 등록 링크(`https://labs.azure.com/register/<registrationCode>`)를 사용하여 로그인하면 학생이 액세스할 수 있는 각 랩에 대한 타일이 표시됩니다. 
   
1. 타일에서 VM이 중지된 상태인 경우 단추를 전환하여 VM을 시작합니다. 

2. **연결** 을 선택합니다. VM에 연결하는 두 가지 옵션이 표시됩니다. **SSH** 및 **RDP**

    ![학생 VM - 연결 옵션](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. **SSH** 옵션을 선택하면 **가상 머신에 연결** 대화 상자가 표시됩니다.  

    ![SSH 연결 문자열](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 텍스트 상자 옆에 있는 **복사** 단추를 클릭하여 SSH 연결 정보를 클립보드에 복사합니다. 

5. 다음 단계에서 이 연결 정보를 사용할 수 있도록 텍스트 패드 등에 SSH 연결 정보를 저장합니다.

6. SSH 터미널(예: [Putty](https://www.putty.org/))에서 VM에 연결합니다.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>GUI 원격 데스크톱을 사용하여 학생 VM에 연결
강사는 학생들이 GUI 원격 데스크톱을 사용하여 연결할 수 있도록 VM을 구성하도록 선택할 수 있습니다.  이 경우 학생은 **Microsoft RDP(원격 데스크톱)** 또는 **X2Go** 클라이언트 애플리케이션 중 어떤 방식으로 VM에 연결할지 강사에게 확인해야 합니다.  학생들은 이러한 애플리케이션을 사용하여 VM에 원격으로 연결하고 해당 로컬 컴퓨터에 Linux 그래픽 데스크톱을 표시할 수 있습니다.

> [!WARNING]
>  [GNOME](https://www.gnome.org/) 또는 [면맞춤](https://mate-desktop.org/) 을 사용 해야 하는 경우 강사와 협력 하 여 랩 VM을 제대로 구성할 수 있는지 확인 해야 합니다.  Azure Lab Services에서 Vm이 제대로 작동 하는 데 필요한 Azure Linux 에이전트에서 발생할 수 있는 알려진 네트워킹 충돌이 있습니다.  대신 [Xfce](https://www.xfce.org/)와 같은 다른 그래픽 데스크톱 환경을 사용 하는 것이 좋습니다.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Microsoft RDP(원격 데스크톱)를 사용하여 학생 VM에 연결
학습자는 xfce와 같은 linux 그래픽 데스크톱 환경에 대 한 rdp 및 GUI 패키지를 사용 하 여 랩을 설정한 후에 rdp (Microsoft 원격 데스크톱)를 사용 하 여 linux vm에 연결할 수 있습니다. 연결 단계는 다음과 같습니다. 

1. VM에 대한 타일에서 VM이 실행 중인지 확인하고 **연결** 을 클릭합니다. VM에 연결하는 두 가지 옵션이 표시됩니다. **SSH** 및 **RDP**

    ![학생 VM - 연결 옵션](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. **RDP** 옵션을 선택합니다.  RDP 파일이 머신에 다운로드되면 VM에 저장합니다.

3. Windows 컴퓨터에서 연결 하는 경우 일반적으로 RDP 클라이언트를 이미 설치 하 고 구성 합니다.  따라서 RDP 파일을 클릭하여 열고 원격 세션을 시작하기만 하면 됩니다.

    대신 Mac 또는 Chromebook에서 연결하는 경우 다음 단계를 참조하세요.
   - [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-remote-desktop.md)
   - [Chromebook에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-chromebook-remote-desktop.md)

RDP 클라이언트에는 사용자의 연결 환경을 최적화 하기 위해 조정할 수 있는 다양 한 설정이 포함 되어 있습니다.  일반적으로 이러한 설정은 변경할 필요가 없습니다.  기본적으로 설정은 네트워크 연결에 따라 올바른 환경을 선택 하도록 이미 구성 되어 있습니다.  이러한 설정에 대 한 자세한 내용은 [RDP 클라이언트의 **환경** 설정에 대 한 문서를 참조](/windows-server/administration/performance-tuning/role/remote-desktop/session-hosts#client-experience-settings)하세요.

강사가 RDP 클라이언트를 사용 하 여 GNOME 그래픽 데스크톱을 구성한 경우 성능을 최적화 하려면 다음 설정을 사용 하는 것이 좋습니다.
- **표시** 탭에서 색 농도를 **높은 색 (15 비트)** 으로 설정 합니다.
    ![RDP 표시 설정 변경](./media/how-to-enable-remote-desktop-linux/rdp-display-settings.png)

- **환경** 탭에서 연결 속도를 **모뎀 (56 kbps)** 으로 설정 합니다.
    ![RDP 환경 변경 설정](./media/how-to-enable-remote-desktop-linux/rdp-experience-settings.png)

### <a name="connect-to-the-student-vm-using-x2go"></a>X2Go를 사용하여 학생 VM에 연결
X2Go를 사용 하 여 강사가 X2Go를 사용 하 여 랩을 설정 하 고 XFCE와 같은 Linux 그래픽 데스크톱 환경을 위한 GUI 패키지를 설치한 후에 해당 Linux Vm에 연결할 수 있습니다.

학생들은 강사가 설치한 Linux 그래픽 데스크톱 환경이 어떤 것인지 확인해야 합니다.  이 정보는 다음 단계에서 X2Go 클라이언트를 사용하여 연결하는 데 필요합니다.

1. 로컬 컴퓨터에 [X2Go 클라이언트](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)를 설치합니다.

1. [첫 번째 섹션](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)의 지침에 따라 VM에 대한 SSH 연결 정보를 복사합니다.  X2Go 클라이언트를 사용하여 연결하려면 이 정보가 필요합니다.

1. SSH 연결 정보를 찾았으면 X2Go 클라이언트를 열고 **세션** > **새 세션** 을 선택합니다.
   ![X2Go 새 세션 만들기](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. SSH 연결 정보에 따라 **세션 기본 설정** 창에 값을 입력합니다.  예를 들어 연결 정보는 다음과 유사하게 표시됩니다.

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    이 예제를 사용하면 다음 값이 입력됩니다.

   - **세션 이름** - 이름(예: VM의 이름)을 지정합니다.
   - **호스트** - VM의 ID입니다(예: **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** ).
   - **로그인** - VM의 사용자 이름입니다(예: **student**).
   - **SSH 포트** - VM에 할당된 고유 포트입니다(예: **12345**).
   - **세션 유형** - 강사가 VM을 구성한 Linux 그래픽 데스크톱 환경을 선택합니다.  강사 로부터이 정보를 받아야 합니다.  예를 들어 `XFCE` XFCE 또는 Xubuntu 그래픽 데스크톱 환경을 사용 하는 경우를 선택 합니다.
        

    마지막으로 **확인** 을 클릭하여 세션을 만듭니다.

    ![X2Go 세션 기본 설정](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  오른쪽 창에서 세션을 클릭합니다.

    ![X2Go 새 세션 시작](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > 이와 유사한 메시지가 표시되면 **예** 를 선택하여 암호를 계속 입력합니다. **호스트 '[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345'의 신뢰성을 설정할 수 없습니다.  ECDSA 키 지문이 SHA256:00000000000000000000000000000000000000000000입니다. 연결을 계속하시겠습니까(예/아니요)?**

2. 메시지가 표시되면 암호를 입력하고 **확인** 을 클릭합니다.  이제 VM의 GUI 데스크톱 환경에 원격으로 연결됩니다.

## <a name="next-steps"></a>다음 단계
교실 랩에서 Linux VM에 대해 원격 데스크톱 연결 기능을 사용하도록 설정하는 방법에 대한 내용은 [Linux 가상 머신에 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조하세요. 

