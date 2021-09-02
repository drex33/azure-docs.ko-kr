---
title: SSH를 통해 Azure Percept DK에 연결
description: PuTTY를 사용하여 SSH를 통해 Azure Percept DK에 연결하는 방법을 알아봅니다.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: b22a359e112d7d0d3caae76acd20a7e3fd3ada01
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226230"
---
# <a name="connect-to-azure-percept-dk-over-ssh"></a>SSH를 통해 Azure Percept DK에 연결

아래 단계를 따라 OpenSSH 또는 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 통해 Azure Percept DK에 SSH 연결을 설정합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Wi-Fi 기능을 지원하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- SSH 클라이언트(설치 참고 자료는 다음 섹션 참조)
- Azure Percept DK(개발 키트)
- [Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md) 중에 만든 SSH 계정

## <a name="install-your-preferred-ssh-client"></a>기본 SSH 클라이언트 설치

호스트 컴퓨터에서 Linux 또는 OS X를 실행하는 경우 SSH 서비스가 운영 체제에 포함되며 별도의 클라이언트 애플리케이션 없이 실행할 수 있습니다. SSH 서비스를 실행하는 방법에 대한 자세한 내용은 운영 체제 제품 설명서를 참조하세요.

호스트 컴퓨터가 Windows를 실행하는 경우 두 개의 SSH 클라이언트 옵션, 즉 OpenSSH 및 PuTTY 중에서 선택할 수 있습니다.

### <a name="openssh"></a>OpenSSH

Windows 10에는 명령 프롬프트에서 간단한 명령을 사용하여 실행할 수 있는 OpenSSH라는 기본 제공 SSH 클라이언트가 포함되어 있습니다. OpenSSH를 사용할 수 있는 경우 Azure Percept와 함께 이를 사용하는 것이 좋습니다. Windows 컴퓨터에 OpenSSH가 설치되어 있는지 확인하려면 다음 단계를 수행합니다.

1. **시작** -> **설정** 으로 이동합니다.

1. **앱** 을 선택합니다.

1. **앱 및 기능** 에서 **선택적 기능** 을 선택합니다.

1. **설치된 기능** 검색 표시줄에 **OpenSSH Client** 를 입력합니다. OpenSSH이 표시되는 경우 클라이언트가 이미 설치되어 있다는 뜻이며, 다음 섹션으로 이동할 수 있습니다. OpenSSH가 표시되지 않는 경우 **기능 추가** 를 선택합니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="OpenSSH 설치 상태를 보여 주는 설정의 스크린샷.":::

1. **OpenSSH 클라이언트** 를 선택하고 **설치** 를 선택합니다. 이제 다음 섹션으로 이동할 수 있습니다. 컴퓨터에 OpenSSH를 설치할 수 없는 경우 아래 단계를 따라 타사 SSH 클라이언트인 PuTTY를 설치합니다.

### <a name="putty"></a>PuTTY

Windows 컴퓨터에 OpenSSH가 포함되어 있지 않은 경우 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 사용하는 것이 좋습니다. PuTTY를 다운로드하고 설치하려면 다음 단계를 완료합니다.

1. [PuTTY 다운로드 페이지](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)로 이동합니다.

1. **패키지 파일** 에서 32비트 또는 64비트 .msi 파일을 선택하여 설치 프로그램을 다운로드합니다. 어떤 버전을 선택해야 하는지 모르는 경우 [FAQ](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit)를 확인하세요.

1. 설치 프로그램을 선택하여 설치 과정을 시작합니다. 필요에 따라 프롬프트를 따릅니다.

1. 축하합니다! PuTTY SSH 클라이언트를 성공적으로 설치했습니다.

## <a name="initiate-the-ssh-connection"></a>SSH 연결 시작

1. Azure Percept DK를 켜도록 하겠습니다.

1. 개발 키트가 이더넷 또는 Wi-Fi를 통해 네트워크에 이미 연결되어 있는 경우 다음 단계로 건너뜁니다. 연결되어 있지 않은 경우 호스트 컴퓨터를 개발 키트의 Wi-Fi 액세스 지점에 직접 연결합니다. 다른 Wi-Fi 네트워크에 연결할 때와 마찬가지로 컴퓨터에서 네트워크 및 인터넷 설정을 열고, 다음 네트워크를 선택하고, 네트워크 암호를 입력하라는 메시지가 표시되면 입력합니다.

    - **네트워크 이름**: 개발 키트의 운영 체제 버전에 따라 Wi-Fi 액세스 지점의 이름은 **scz-xxxx** 또는 **apd-xxxx**(여기서 "xxxx"는 개발 키트 MAC 주소의 마지막 네 자리)입니다.
    - **암호**: 개발 키트와 함께 제공된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept DK Wi-Fi 액세스 지점에 연결된 동안 호스트 컴퓨터의 인터넷 연결이 일시적으로 끊깁니다. 활성 영상 회의 통화, 웹 스트리밍 또는 기타 네트워크 기반 환경이 중단됩니다.

1. SSH 클라이언트에 따라 SSH 연결 프로세스를 완료합니다.

### <a name="using-openssh"></a>OpenSSH 사용

1. 명령 프롬프트를 엽니다(**시작** -> **명령 프롬프트**).

1. 명령 프롬프트에 다음을 입력합니다.

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    컴퓨터가 개발 키트의 Wi-Fi 액세스 지점에 연결된 경우 IP 주소는 10.1.1.1이 됩니다. 개발 키트가 이더넷을 통해 연결된 경우 디바이스의 로컬 IP 주소를 사용합니다. 로컬 IP 주소는 이더넷 라우터 또는 허브에서 가져올 수 있습니다. 개발 키트가 Wi-F를 통해 연결된 경우 [Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md)에서 개발 키트에 할당한 IP 주소를 사용해야 합니다.

    > [!TIP]
    > 개발 키트가 Wi-Fi 네트워크에 연결되어 있지만 네트워크의 IP 주소를 알 수 없는 경우 Azure Percept Studio로 이동하여 [디바이스의 비디오 스트림을 엽니다](./how-to-view-video-stream.md). 비디오 스트림 브라우저 탭의 주소 표시줄에 디바이스의 IP 주소가 표시됩니다.

1. 메시지가 표시되면 SSH 암호를 입력합니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="OpenSSH 명령 프롬프트 열기의 스크린샷.":::

1. OpenSSH를 통해 개발 키트에 처음 연결하는 경우 호스트의 키를 허용하라는 메시지가 표시될 수도 있습니다. **yes** 를 입력하여 키를 허용합니다.

1. 축하합니다! SSH를 통해 개발 키트에 성공적으로 연결했습니다.

### <a name="using-putty"></a>PuTTY 사용

1. PuTTY를 엽니다. **PuTTY 구성** 창에 다음을 입력하고 **열기** 를 선택하여 개발 키트에 SSH를 연결합니다.

    1. 호스트 이름: [IP 주소]
    1. 포트: 22
    1. 연결 형식: SSH

    **호스트 이름** 은 개발 키트의 IP 주소입니다. 컴퓨터가 개발 키트의 Wi-Fi 액세스 지점에 연결된 경우 IP 주소는 10.1.1.1이 됩니다. 개발 키트가 이더넷을 통해 연결된 경우 디바이스의 로컬 IP 주소를 사용합니다. 로컬 IP 주소는 이더넷 라우터 또는 허브에서 가져올 수 있습니다. 개발 키트가 Wi-F를 통해 연결된 경우 [Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md)에서 개발 키트에 할당한 IP 주소를 사용해야 합니다.

    > [!TIP]
    > 개발 키트가 Wi-Fi 네트워크에 연결되어 있지만 네트워크의 IP 주소를 알 수 없는 경우 Azure Percept Studio로 이동하여 [디바이스의 비디오 스트림을 엽니다](./how-to-view-video-stream.md). 비디오 스트림 브라우저 탭의 주소 표시줄에 디바이스의 IP 주소가 표시됩니다.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="PuTTY 구성 창의 스크린샷.":::

1. PuTTY 터미널이 열립니다. 메시지가 표시되면 터미널에 SSH 사용자 이름 및 암호를 입력합니다.

1. 축하합니다! SSH를 통해 개발 키트에 성공적으로 연결했습니다.

## <a name="next-steps"></a>다음 단계

SSH를 통해 Azure Percept DK에 연결한 후에는 [디바이스 문제 해결](./troubleshoot-dev-kit.md) 및 [USB 업데이트](./how-to-update-via-usb.md)를 포함하여 다양한 작업을 수행할 수 있습니다.