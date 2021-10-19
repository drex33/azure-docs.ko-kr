---
title: Azure Lab Services에서 Linux용 그래픽 원격 데스크톱을 사용하도록 설정 | Microsoft Docs
description: Azure Lab Services의 랩에서 Linux 가상 머신에 대해 원격 데스크톱을 활성화하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 079f572062978fcb655e2ac7d2829b1d18ba7345
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163640"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Azure Lab Services에서 Linux 가상 머신에 대해 그래픽 원격 데스크톱을 사용하도록 설정
이 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- Linux VM에 대해 그래픽 원격 데스크톱 세션을 사용하도록 설정
- RDP(원격 데스크톱 프로토콜) 또는 X2Go 원격 데스크톱 클라이언트를 사용하여 Linux VM에 연결하는 방법

## <a name="set-up-graphical-remote-desktop-solution"></a>그래픽 원격 데스크톱 솔루션 설정
**Linux** 이미지에서 랩이 만들어지면 강사가 SSH를 사용하여 명령줄에서 템플릿 VM에 연결할 수 있도록 **SSH**(Secure Shell) 액세스가 자동으로 구성됩니다.  마찬가지로 템플릿 VM이 게시되면 학생이 SSH를 사용하여 VM에 연결할 수도 있습니다.

**GUI**(그래픽 사용자 인터페이스)를 사용하여 Linux VM에 연결하려면 **RDP** 또는 **X2Go** 를 사용하는 것이 좋습니다.  이 문서의 나머지 부분에는 랩의 템플릿 VM에서 RDP 또는 X2Go를 설정하는 단계가 표시됩니다.

> [!NOTE]
> Linux는 [Xrdp라는](https://en.wikipedia.org/wiki/Xrdp)RDP의 오픈 소스 버전을 사용합니다.  간단히 하기 위해 이 문서 전체에서 RDP라는 용어를 사용합니다.

### <a name="rdp-setup"></a>RDP 설정
강사는 RDP를 사용하기 위해 다음을 수행해야 합니다.
  - 원격 데스크톱 연결을 사용하도록 설정합니다. 이 설정은 특히 RDP의 VM 포트를 여는 데 필요합니다.
  - RDP 원격 데스크톱 서버를 설치합니다.
  - Linux 그래픽 데스크톱 환경을 설치합니다.

### <a name="x2go-setup"></a>X2Go 설정
강사는 X2Go를 사용하기 위해 다음을 수행해야 합니다.
- X2Go 원격 데스크톱 서버를 설치합니다.
- Linux 그래픽 데스크톱 환경을 설치합니다.

X2Go는 SSH에 대해 이미 사용하도록 설정된 것과 동일한 포트를 사용합니다.  따라서 X2Go용 VM에서 포트를 여는 데 필요한 추가 구성은 없습니다.

> [!NOTE]
> Ubuntu LTS 18.04를 사용할 때와 같은 몇몇 경우에는 X2Go가 더 나은 성능을 제공합니다.  RDP를 사용하여 그래픽 데스크톱 환경과 상호 작용할 때 대기 시간이 발생하는 경우 성능을 향상할 수 있는 X2Go를 사용해 보는 것이 좋습니다.

> [!IMPORTANT]
>  일부 마켓플레이스 이미지에는 이미 그래픽 데스크톱 환경 및 원격 데스크톱 서버가 설치되어 있습니다.  예를 들어 [Linux(Ubuntu)용 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)에는 이미 [XFCE 및 X2Go 서버가 설치되어 있고 클라이언트 연결을 허용하도록 구성되어 있습니다](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>RDP에 대해 원격 데스크톱 연결을 사용하도록 설정

이 단계는 RDP를 사용하여 연결하는 경우에만 필요합니다.  대신 X2Go를 사용하려는 경우 X2Go에서는 SSH 포트를 사용하므로 다음 섹션으로 건너뛸 수 있습니다.

1.  강사는 랩을 만드는 동안 **원격 데스크톱 연결 사용** 옵션을 선택할 수 있습니다.  강사는 이 옵션을 **사용하도록 설정** 하여 RDP 원격 데스크톱 세션에 필요한 Linux VM의 포트를 열어야 합니다.  이 옵션을 **사용하지 않도록 설정한** 상태로 놔두면 SSH의 포트만 열립니다.
  
    ![“원격 데스크톱 연결 사용” 옵션이 있는 “새 랩” 창을 보여 주는 스크린샷](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. **원격 데스크톱 연결 활성화** 메시지 상자에서 **원격 데스크톱을 사용하여 계속** 을 선택합니다. 

    ![Linux 이미지에 대해 원격 데스크톱 연결 활성화](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>RDP 또는 X2Go 설치

강사는 랩이 만들어진 후 템플릿 VM에 그래픽 데스크톱 환경과 원격 데스크톱 서버가 설치되어 있는지 확인해야 합니다.  강사는 먼저 SSH를 사용하여 템플릿 VM에 연결하여 다음을 위한 패키지를 설치해야 합니다.
- RDP 또는 X2Go 원격 데스크톱 서버 중 하나
- [GNOME,](https://www.gnome.org/) [MATE,](https://mate-desktop.org/) [XFCE,](https://www.xfce.org/) [Xubuntu](https://xubuntu.org/)등과 같은 그래픽 데스크톱 환경

강사는 이 설정을 마친 후 **RDP(Microsoft 원격 데스크톱)** 클라이언트 또는 **X2Go** 클라이언트를 사용하여 템플릿 VM에 연결할 수 있습니다.

템플릿 VM을 설정하려면 아래 단계를 따르세요.

1. 도구 모음에서 **템플릿 사용자 지정** 이 표시되는 경우 해당 항목을 선택합니다. 그런 다음, **템플릿 사용자 지정** 대화 상자에서 **계속** 을 선택합니다. 이 작업을 수행하면 템플릿 VM이 시작됩니다.  

    ![템플릿 사용자 지정](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. 템플릿 VM을 시작한 후 **템플릿 연결** 을 선택한 다음, 도구 모음에서 **SSH를 통해 연결** 을 선택할 수 있습니다. 

    ![랩을 만든 후 RDP를 통해 템플릿에 연결](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. 다음 **가상 머신에 연결** 대화 상자가 표시됩니다. 텍스트 상자 옆에 있는 **복사** 단추를 선택하여 클립보드에 복사합니다. SSH 연결 정보를 저장합니다. SSH 터미널(예: [Putty](https://www.putty.org/))에서 이 연결 정보를 사용하여 가상 머신에 연결합니다.
 
    ![SSH 연결 문자열](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 마지막으로 선택한 그래픽 데스크톱 환경과 함께 RDP 또는 X2Go를 설치합니다.

성능을 최적화하려면 일반적으로 XFCE 그래픽 데스크톱을 사용하고 사용자가 X2Go를 사용하여 데스크톱에 연결하는 것이 좋습니다.  Ubuntu에서 X2Go를 사용하여 XFCE를 설정하려면 다음 지침을 사용합니다.
  - [X2Go 설치 및 구성](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/XFCE_Xubuntu/ReadMe.md)

대신 사용자가 RDP를 사용하여 그래픽 데스크톱에 연결해야 하는 경우 Ubuntu에 대해 다음 지침을 사용합니다.
  - [RDP 설치 및 구성](../virtual-machines/linux/use-remote-desktop.md)

GNOME 또는BRIDGE 그래픽 데스크톱 환경의 경우 VM이 Azure Lab Services 제대로 작동하는 데 필요한 Azure Linux 에이전트와 네트워킹 충돌이 발생할 수 있습니다.  예를 들어 기본적으로 GNOME 또는 MATE가 설치된 Ubuntu 18.04 LTS 이미지에서 랩을 만드는 경우 다음 오류 메시지와 함께 랩 만들기가 **실패합니다. VM 에이전트를 사용하여 통신을 설정할 수 없습니다.  VM 에이전트가 사용하도록 설정되어 있고 작동하는지 확인하세요.**  마찬가지로 이 네트워킹 충돌로 인해 학생 VM을 프로비전하려고 할 때 게시가 중단됩니다.

Ubuntu에서 GNOME 또는 MATE 그래픽 데스크톱을 설정하려면 다음 지침을 사용하는 것이 좋습니다.  이러한 지침에는 Ubuntu 18.04 LTS와 함께 존재하는 네트워킹 충돌에 대한 수정이 포함됩니다.  또한 Ubuntu 20.04 LTS 및 21.04 LTS도 지원합니다.
 - [GNOME/RDP 및 MATE/X2go 설치 및 구성](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md)

## <a name="connect-to-the-template-vm-via-the-gui"></a>GUI를 통해 템플릿 VM에 연결

강사는 템플릿 VM이 설정된 후 **RDP(Microsoft 원격 데스크톱)** 클라이언트 또는 **X2Go** 클라이언트를 사용하여 GUI를 통해 연결할 수 있습니다.  사용하는 클라이언트는 RDP 또는 X2Go가 템플릿 VM에서 원격 데스크톱 서버로 구성되었는지 여부에 따라 달라집니다.  

### <a name="microsoft-remote-desktop-rdp-client"></a>RDP(Microsoft 원격 데스크톱) 클라이언트

RDP(Microsoft 원격 데스크톱) 클라이언트는 RDP가 구성된 템플릿 VM에 연결하는 데 사용됩니다.  원격 데스크톱 클라이언트는 Windows, Chromebook, Mac 등에서 사용할 수 있습니다.  자세한 내용은 [원격 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)의 문서를 참조하세요.

템플릿 VM에 연결하는 데 사용되는 컴퓨터의 유형에 따라 아래 단계를 따르세요.

- Windows
  1. 랩의 도구 모음에서 **템플릿에 연결** 을 클릭하고 **RDP를 통해 연결** 을 선택하여 템플릿 VM에 연결합니다. 
  1. RDP 파일을 저장한 다음에 원격 데스크톱 클라이언트를 사용하여 템플릿 VM에 연결하는 데 이 파일을 사용합니다. 
  1. 일반적으로 원격 데스크톱 클라이언트는 Windows에 이미 설치 및 구성되어 있습니다.  따라서 RDP 파일을 클릭하여 열고 원격 세션을 시작하기만 하면 됩니다.

- Mac
  1. 랩의 도구 모음에서 **템플릿에 연결** 을 클릭한 후 **RDP를 통해 연결** 을 선택하여 RDP 파일을 저장합니다.  
  1. 그런 다음, [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-remote-desktop.md) 방법 문서를 참조하세요.

- Chromebook
  1. 랩의 도구 모음에서 **템플릿에 연결** 을 클릭한 후 **RDP를 통해 연결** 을 선택하여 RDP 파일을 저장합니다.  
  1. 그런 다음, [Chromebook에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-chromebook-remote-desktop.md) 방법 문서를 참조하세요.

### <a name="x2go-client"></a>X2Go 클라이언트

X2Go 클라이언트는 X2Go가 구성된 템플릿 VM에 연결하는 데 사용됩니다.  템플릿 VM의 SSH 연결 정보를 사용하여 [X2Go를 사용하여 VM에 연결](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go) 방법 문서의 단계를 따르세요.

## <a name="next-steps"></a>다음 단계
강사가 템플릿 VM에서 RDP 또는 X2Go를 설정하고 게시한 후에 학생은 GUI 원격 데스크톱 또는 SSH를 통해 VM에 연결할 수 있습니다.

자세한 내용은 다음을 참조하세요.
 - [Linux VM에 연결](how-to-use-remote-desktop-linux-student.md)