---
title: Azure Virtual Desktop의 Microsoft Teams - Azure
description: Azure Virtual Desktop에서 Microsoft Teams를 사용하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/02/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 63c75636fcab47a31735b772f85a8dccbb9c65f5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528366"
---
# <a name="use-microsoft-teams-on-azure-virtual-desktop"></a>Azure Virtual Desktop에서 Microsoft Teams 사용

>[!IMPORTANT]
>Teams에 대한 미디어 최적화는 GCC(Microsoft 365 Government) 및 GCC-High 환경에서 지원됩니다. Teams에 대한 미디어 최적화는 Microsoft 365 DoD에 지원되지 않습니다.

>[!NOTE]
>Microsoft Teams에 대한 미디어 최적화는 Windows 10 컴퓨터의 Windows 데스크톱 클라이언트에서만 사용할 수 있습니다. 미디어 최적화에는 Windows 데스크톱 클라이언트 버전 1.2.1026.0 이상이 필요합니다.

Azure Virtual Desktop의 Microsoft Teams는 채팅 및 공동 작업을 지원합니다. 미디어 최적화를 사용하면 통화 및 모임 기능도 지원합니다. VDI(가상 데스크톱 인프라)에서의 Microsoft Teams 사용 방법에 대한 자세한 정보는 [가상화된 데스크톱 인프라용 Teams](/microsoftteams/teams-for-vdi/)를 참조하세요.

Microsoft Teams에 대한 미디어 최적화를 통해 Windows 데스크톱 클라이언트가 Teams 통화 및 모임에 대한 오디오 및 비디오를 로컬로 처리합니다. Azure Virtual Desktop에서 통화 및 모임 최적화 없는 다른 클라이언트에도 Microsoft Teams를 사용할 수 있습니다. Teams 채팅 및 협업 기능은 모든 플랫폼에서 지원됩니다. 원격 세션에서 로컬 디바이스를 리디렉션하려면 [호스트 풀의 사용자 지정 원격 데스크톱 프로토콜 속성](#customize-remote-desktop-protocol-properties-for-a-host-pool)을 체크 아웃합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Virtual Desktop에서 Microsoft Teams를 사용하려면 먼저 다음을 수행해야 합니다.

- Microsoft Teams용 [네트워크를 준비합니다](/microsoftteams/prepare-network/).
- Windows PC의 Teams에 대한 Microsoft Teams [하드웨어 요구 사항](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)에 부합하는 Windows 10 또는 Windows 10 IoT Enterprise 디바이스에 [Windows 데스크톱 클라이언트](./user-documentation/connect-windows-7-10.md)를 설치합니다.
- Windows 10 다중 세션 또는 Windows 10 Enterprise VM(가상 머신)에 연결합니다.

## <a name="install-the-teams-desktop-app"></a>Teams 데스크톱 앱 설치

이 섹션에서는 Windows 10 다중 세션 또는 Windows 10 Enterprise VM 이미지에 Teams 데스크톱 앱을 설치하는 방법을 보여줍니다. 자세한 정보는 [VDI에서 Teams 데스크톱 앱 설치 또는 업데이트](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi)를 참조하세요.

### <a name="prepare-your-image-for-teams"></a>Teams용 이미지 준비

Teams에서 미디어 최적화를 사용하려면 호스트에서 다음 레지스트리 키를 설정합니다.

1. 시작 메뉴에서 관리자 권한으로 **RegEdit** 를 실행합니다. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams** 로 이동합니다. 아직 없으면 Teams 키를 만듭니다.

2. Teams 키에 대해 다음 값을 만듭니다.

| 속성             | 유형   | Data/Value  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Teams WebSocket 서비스 설치

VM 이미지에 최신 [원격 데스크톱 WebRTC 리디렉터 서비스](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWFYsj)를 설치합니다. 설치 오류가 발생할 경우 [최신 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)를 설치하고 다시 시도하세요.

#### <a name="latest-websocket-service-versions"></a>최신 WebSocket 서비스 버전

다음 표는 WebSocket 서비스의 최신 버전 목록입니다.

|버전        |릴리스 날짜  |
|---------------|--------------|
|1.0.2106.14001 |07/29/2021    |
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10210614001"></a>1\.0.2106.14001 버전에 대한 업데이트

WebRTC 리디렉터 서비스와 WebRTC 클라이언트 플러그 인 간의 연결 안정성이 개선되었습니다.

#### <a name="updates-for-version-10200611001"></a>1\.0.2006.11001 버전에 대한 업데이트

- 전화 또는 모임 중에 Teams 앱을 최소화하면 들어오는 비디오가 끊기는 문제가 해결되었습니다.
- 다중 모니터 데스크톱 세션에서 하나의 모니터를 공유하도록 선택할 수 있는 지원이 추가되었습니다.

### <a name="install-microsoft-teams"></a>Microsoft Teams 설치

컴퓨터별 또는 사용자별 설치를 통해 Teams 데스크톱 앱을 배포할 수 있습니다. Azure Virtual Desktop 환경에 Microsoft Teams를 설치하려면 다음을 수행합니다.

1. 환경에 맞는 [Teams MSI 패키지](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)를 다운로드합니다. 64비트 운영 체제의 64비트 설치 프로그램을 사용하는 것이 좋습니다.

      > [!IMPORTANT]
      > Teams Desktop 클라이언트 버전 1.3.00.21759에서는 채팅, 채널, 캘린더에서 UTC 표준 시간대를 표시하는 문제가 해결되었습니다. 새 버전의 클라이언트는 원격 세션 표준 시간대를 표시합니다.

2. 다음 명령 중 하나를 실행하여 MSI를 호스트 VM에 설치합니다.

    - 사용자 단위 설치

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        이 프로세스는 기본 설치로, Teams를 **%AppData%** 사용자 폴더에 설치합니다. Teams는 비영구적 설정의 사용자별 설치에서 올바르게 작동하지 않습니다.

    - 컴퓨터별 설치

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        32비트 운영 체제의 Program Files(x86) 폴더, 64비트 운영 체제의 Program Files 폴더에 Teams를 설치합니다. 이제 골든 이미지 설정이 완료되었습니다. 비영구적 설정에서는 Teams를 컴퓨터별로 설치해야 합니다.

        Teams를 설치할 때는 **ALLUSER=1** 및 **ALLUSERS=1** 등의 두 플래그가 설정될 수 있습니다. 이 매개 변수의 차이를 이해하는 것이 중요합니다. **ALLUSER=1** 매개 변수는 VDI 환경에서만 컴퓨터별 설치를 지정하기 위해 사용됩니다. **ALLUSERS=1** 매개 변수는 비 VDI와 VDI 환경에서 사용할 수 있습니다. 이 매개 변수를 설정할 경우 제어판의 프로그램 및 기능과, Windows 설정의 앱 및 기능 모두에 **Teams 컴퓨터 수준 설치 프로그램** 이 표시됩니다. 컴퓨터에서 관리자 자격 증명이 있는 모든 사용자는 Teams를 제거할 수 있습니다.

        > [!NOTE]
        > 현재는 사용자 및 관리자가 로그인하는 동안 Teams 자동 시작을 사용하지 않게 설정할 수 없습니다.

3. 호스트 VM에서 MSI를 제거하려면 다음 명령을 실행합니다.

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      그러면 운영 체제 환경에 따라 Program Files(x86) 폴더 또는 Program Files 폴더에서 Teams가 제거됩니다.

      > [!NOTE]
      > MSI 설정 ALLUSER=1을 사용하여 Teams를 설치하는 경우 자동 업데이트를 사용하지 않습니다. 한 달에 한 번 이상 Teams를 업데이트하는 것이 좋습니다. Teams 데스크톱 앱을 배포하는 방법에 대한 자세한 정보는 [Teams 데스크톱 앱을 VM에 배포](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)를 확인하세요.

### <a name="verify-media-optimizations-loaded"></a>미디어 최적화가 로드되었는지 확인

WebSocket 서비스와 Teams 데스크톱 앱을 설치한 후 다음 단계에 따라 Teams 미디어 최적화가 로드되었는지 확인합니다.

1. Teams 애플리케이션을 종료하고 다시 시작합니다.

2. 사용자 프로필 이미지를 선택한 다음, **정보** 를 선택합니다.

3. **버전** 을 선택합니다.

      미디어 최적화가 로드되면 배너에  **Virtual Desktop 최적화됨** 이 표시됩니다. 배너에 **Azure Virtual Desktop 미디어가 연결되지 않음** 이라고 표시되면 Teams 앱을 종료하고 다시 시도하세요.

4. 사용자 프로필 이미지를 선택한 다음, **설정** 을 선택합니다.

      미디어 최적화가 로드되면 로컬에서 사용할 수 있는 오디오 디바이스와 카메라가 디바이스 메뉴에 열거됩니다. 메뉴에 **원격 오디오** 가 표시되면 Teams를 종료하고 다시 시도하세요. 그래도 메뉴에 디바이스가 표시되지 않으면 로컬 PC에서 개인 정보 설정을 확인합니다. **설정** > **개인 정보** > **앱 사용 권한-마이크** 에서 **앱에서 마이크에 액세스하도록 허용** 이 **켬** 으로 전환되었는지 확인합니다. 원격 세션에서 연결을 끊은 다음, 다시 연결하고 오디오 및 비디오 디바이스를 다시 확인합니다. 통화와 모임을 비디오에 조인하려면 앱에 카메라 액세스 권한도 부여해야 합니다.

      최적화가 로드되지 않는 경우 Teams를 제거하고 다시 설치한 다음, 다시 시도합니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

가상화된 환경에서의 Teams 사용은 가상화되지 않은 환경에서의 Teams 사용과 다릅니다. 가상화된 환경에서의 Teams 제한 사항에 대한 자세한 내용은 [가상화된 데스크톱 인프라에서의 Teams](/microsoftteams/teams-for-vdi#known-issues-and-limitations)를 확인하세요.

### <a name="client-deployment-installation-and-setup"></a>클라이언트 배포, 설치 및 설정

- 컴퓨터별 설치에서는 VDI의 Teams가 비 VDI Teams 클라이언트와 같은 방식으로 자동 업데이트되지 않습니다. 클라이언트를 업데이트하려면 새 MSI를 설치하여 VM 이미지를 업데이트해야 합니다.
- Teams에 대한 미디어 최적화는 Windows 10을 실행하는 컴퓨터의 Windows 데스크톱 클라이언트에 대해서만 지원됩니다.
- 클라이언트 엔드포인트 디바이스에 정의된 명시적 HTTP 프록시의 사용은 지원되지 않습니다.

### <a name="calls-and-meetings"></a>통화 및 모임

- Azure Virtual Desktop 환경의 Teams 데스크톱 클라이언트는 라이브 이벤트 만들기를 지원하지 않으나, 라이브 이벤트를 조인할 수는 있습니다. 현재는 [Teams 웹 클라이언트](https://teams.microsoft.com)에서 라이브 이벤트를 만드는 것이 좋습니다.
- 통화 또는 모임에서는 현재 애플리케이션 공유를 지원하지 않습니다. 데스크톱 세션은 데스크톱 공유를 지원합니다.
- 컨트롤 제공 및 제어는 현재 지원되지 않습니다.
- Azure Virtual Desktop에서의 Teams는 한 번에 하나의 수신 비디오 입력만 지원합니다. 즉, 누군가 자신의 화면 공유를 시도할 때마다 모임 리더의 화면 대신 해당 화면이 표시됩니다.
- WebRTC 제한으로 인해 들어오고 나가는 비디오 스트림 해상도는 720p로 제한됩니다.
- Teams 앱은 HID 단추나, 다른 디바이스의 LED 컨트롤을 지원하지 않습니다.
- NME(새 모임 환경)은 현재 VDI 환경에서 지원되지 않습니다.

가상화된 환경과 무관한 알려진 Teams 문제에 대해서는 [해당 조직의 Teams 지원 팀](/microsoftteams/known-issues)에 문의하세요.

## <a name="collect-teams-logs"></a>Teams 로그 수집

Azure Virtual Desktop 환경에서 Teams 데스크톱에 문제가 발생한 경우 호스트 VM의 **%appdata%\Microsoft\Teams\logs.txt** 에서 클라이언트 로그를 수집합니다.

통화 및 모임에서 문제가 발생한 경우 **Ctrl** + **Alt** + **Shift** + **1** 키 조합으로 Teams 웹 클라이언트 로그를 수집합니다. 로그는 호스트 VM의 **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt** 에 기록됩니다.

## <a name="contact-microsoft-teams-support"></a>Microsoft Teams 지원 문의

Microsoft Teams 고객 지원팀에 문의하려면 [Microsoft 365 관리 센터](/microsoft-365/admin/contact-support-for-business-products)로 이동합니다.

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정

다중 모니터 환경, 마이크 및 오디오 리디렉션 사용 등 호스트 풀의 RDP(원격 데스크톱 프로토콜)을 사용자 지정하면 사용자의 요구에 따라 최적의 환경을 제공할 수 있습니다.

미디어 최적화를 적용하여 Teams를 사용할 때는 디바이스 리디렉션을 사용하지 않아도 됩니다. 미디어 최적화 없이 Teams를 사용하는 경우 다음 RDP 속성을 설정하여 마이크와 카메라 리디렉션을 사용하도록 설정합니다.

- `audiocapturemode:i:1`은 로컬 디바이스에서의 오디오 캡처를 사용하고 원격 세션에서 오디오 애플리케이션을 리디렉션합니다.
- `audiomode:i:0`은 로컬 컴퓨터에서 오디오를 재생합니다.
- `camerastoredirect:s:*`는 모든 카메라를 리디렉션합니다.

자세한 정보는 [호스트 풀에 대한 원격 데스크톱 프로토콜 사용자 지정](customize-rdp-properties.md)을 확인하세요.
