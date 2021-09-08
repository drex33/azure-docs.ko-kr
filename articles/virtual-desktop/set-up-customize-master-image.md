---
title: Azure Virtual Desktop의 VHD 이미지 준비 및 사용자 지정 - Azure
description: Azure Virtual Desktop 이미지를 준비, 사용자 지정하고 Azure에 업로드하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3153fed07575c0eeaf3e062e18e80f08e8269344
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835226"
---
# <a name="prepare-and-customize-a-vhd-image-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 VHD 이미지 준비 및 사용자 지정

이 문서에서는 VM(가상 머신)을 만들고 여기에 소프트웨어를 설치하는 방법을 포함하여 Azure에 업로드할 마스터 VHD(가상 하드 디스크) 이미지를 준비하는 방법을 설명합니다. 이러한 지침은 조직의 기존 프로세스에 사용할 수 있는 Azure Virtual Desktop 관련 구성에 대한 것입니다.

>[!IMPORTANT]
>Azure 이미지 갤러리의 이미지를 사용하는 것이 좋습니다. 하지만 사용자 지정된 이미지를 사용해야 할 경우 VM에 Azure Virtual Desktop 에이전트를 아직 설치하지 않았는지 확인합니다. Azure Virtual Desktop 에이전트에 사용자 지정 이미지를 사용하면 등록 차단 및 사용자 세션 연결 방해와 같은 문제가 이미지에 발생할 수 있습니다.  

## <a name="create-a-vm"></a>VM 만들기

Windows 10 Enterprise 다중 세션은 Azure 이미지 갤러리에서 사용할 수 있습니다. 이 이미지를 사용자 지정하는 옵션은 두 가지입니다.

첫 번째 옵션은 [관리되는 이미지에서 VM 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)의 안내에 따라 Azure에서 VM(가상 머신)을 프로비전한 후 [소프트웨어 준비 및 설치](set-up-customize-master-image.md#software-preparation-and-installation)로 건너뛰는 방법입니다.

두 번째 옵션은 이미지를 다운로드하고, Hyper-V VM을 프로비전하고, 요구에 맞게 사용자 지정하여 이미지를 로컬로 만드는 방법입니다. 이에 대해서는 다음 섹션에서 설명합니다.

### <a name="local-image-creation"></a>로컬 이미지 만들기

로컬 위치에 이미지를 다운로드한 후 **Hyper-V 관리자** 를 열어 복사된 VHD로 VM을 만듭니다. 다음 지침은 간단한 내용만 표시합니다. 자세한 지침은 [Hyper-V에서 가상 머신 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)를 참조하세요.

복사된 VHD로 VM을 만들려면 다음을 수행합니다.

1. **새 Virtual Machine 마법사** 를 엽니다.

2. 세대 지정 페이지에서 **1세대** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![세대 지정 페이지 스크린샷입니다. “1세대” 옵션이 선택되었습니다.](media/a41174fd41302a181e46385e1e701975.png)

3. 검사점 유형에서 확인란을 선택 취소하여 검사점을 사용하지 않도록 설정합니다.

    > [!div class="mx-imgBorder"]
    > ![검사점 페이지의 검사점 유형 섹션에 대한 스크린샷입니다.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

또한 PowerShell에서 다음 cmdlet을 실행하여 검사점을 사용하지 않도록 설정할 수 있습니다.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>고정 디스크

기존 VHD에서 VM을 만드는 경우 기본적으로 동적 디스크가 생성됩니다. 다음 이미지에 표시된 것처럼 **디스크 편집...** 을 선택하여 고정 디스크로 변경할 수 있습니다. 자세한 내용은 [Azure에 업로드하기 위한 Windows VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md)를 참조하세요.

> [!div class="mx-imgBorder"]
> ![디스크 편집 옵션의 스크린샷입니다.](media/35772414b5a0f81f06f54065561d1414.png)

또한 다음 PowerShell cmdlet을 실행하여 디스크를 고정 디스크로 변경할 수 있습니다.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>소프트웨어 준비 및 설치

이 섹션에서는 FSLogix 및 Windows Defender를 준비하고 설치하는 방법, 그리고 앱 및 이미지 레지스트리의 몇 가지 기본 구성 옵션을 설명합니다.

엔터프라이즈용 Microsoft 365 앱과 OneDrive를 VM에 설치하는 경우 [마스터 VHD 이미지에 Office 설치](install-office-on-wvd-master-image.md)로 이동하고 해당 지침에 따라 앱을 설치합니다. 완료되었으면 이 문서로 돌아옵니다.

사용자가 특정 LOB 애플리케이션에 액세스해야 하는 경우 이 섹션의 지침을 따른 후 설치하는 것이 좋습니다.

### <a name="set-up-user-profile-container-fslogix"></a>사용자 프로필 컨테이너 설정(FSLogix)

FSLogix 컨테이너를 이미지의 일부로 포함하려면 [파일 공유를 사용하여 호스트 풀에 대해 프로필 컨테이너 만들기](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)의 지침을 따르세요. FSLogix 컨테이너의 기능을 [이 빠른 시작](/fslogix/configure-cloud-cache-tutorial/)으로 테스트할 수 있습니다.

### <a name="configure-windows-defender"></a>Windows Defender 구성

Windows Defender가 VM에 구성된 경우, 연결 중 VHD 및 VHDX 파일의 전체 내용을 스캔하지 않도록 구성되었는지 확인합니다.

이 구성은 연결 중 VHD 및 VHDX 파일의 스캔만 제거하고 실시간 스캔에 영향을 주지 않습니다.

Windows Server에서 Windows Defender를 구성하는 방법에 대한 자세한 내용은 [Windows Server에서 Windows Defender 바이러스 백신 제외 구성](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)을 참조하세요.

특정 파일을 스캔에서 제외하도록 Windows Defender를 구성하는 방법에 대한 자세한 내용은 [파일 확장명 및 폴더 이치를 기준으로 제외 구성 및 유효성 검사](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)를 참조하세요.

### <a name="disable-automatic-updates"></a>자동 업데이트 사용 안 함

로컬 그룹 정책을 통해 자동 업데이트를 사용하지 않도록 설정하려면 다음을 수행합니다.

1. **로컬 그룹 정책 편집기\\관리 템플릿\\Windows 구성 요소\\Windows Update** 를 엽니다.
2. **자동 업데이트 구성** 을 마우스 오른쪽 단추로 클릭하고 이를 **사용 안 함** 으로 설정합니다.

또한 명령 프롬프트에서 다음 명령을 실행하여 자동 업데이트를 사용하지 않도록 설정할 수 있습니다.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Windows 10 PC의 시작 레이아웃 지정(선택 사항)

이 명령을 실행하여 Windows 10 PC의 시작 레이아웃을 지정합니다.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>표준 시간대 리디렉션 설정

표준 시간대 리디렉션은 호스트 풀의 모든 VM이 동일한 보안 그룹에 속하기 때문에 그룹 정책 수준에서 적용될 수 있습니다.

표준 시간대를 리디렉션하려면 다음을 수행합니다.

1. Active Directory 서버에서 **그룹 정책 관리 콘솔** 을 엽니다.
2. 도메인 및 그룹 정책 개체를 확장합니다.
3. 그룹 정책 설정을 위해 만든 **그룹 정책 개체** 를 마우스 오른쪽 단추로 클릭하고 **편집** 을 선택합니다.
4. **그룹 정책 관리 편집기** 에서 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **디바이스 및 리소스 리디렉션** 으로 이동합니다.
5. **표준 시간대 리디렉션 허용** 설정을 사용하도록 설정합니다.

또한 마스터 이미지에서 이 명령을 실행하여 표준 시간대를 리디렉션할 수 있습니다.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>스토리지 센스 사용 안 함

Windows 10 Enterprise 또는 Windows 10 Enterprise 다중 세션을 사용하는 Azure Virtual Desktop 세션 호스트의 경우 저장 공간 센스를 사용하지 않도록 설정하는 것이 좋습니다. 다음 스크린샷에 표시된 것처럼 설정 메뉴에서 **스토리지** 아래의 스토리지 센스를 사용하지 않도록 설정할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![설정 아래의 스토리지 메뉴의 스크린샷입니다. “스토리지 센스” 옵션이 해제되어 있습니다.](media/storagesense.png)

또한 다음 명령을 실행하여 레지스트리로 설정을 변경할 수 있습니다.

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>추가 언어 지원 포함

이 문서에서는 언어 및 관계형 지원 구성 방법을 다루지 않습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Windows 이미지에 언어 추가](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [주문형 기능](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [언어 및 주문형 지역 기능(FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>기타 애플리케이션 및 레지스트리 구성

이 섹션에서는 애플리케이션 및 운영 체제 구성에 대해 다룹니다. 이 섹션의 모든 구성은 명령줄 및 regedit 도구로 실행될 수 있는 레지스트리 항목을 통해 수행됩니다.

>[!NOTE]
>GPO(그룹 정책 개체) 또는 레지스트리 가져오기를 사용하여 구성에서 모범 사례를 구현할 수 있습니다. 관리자는 조직의 요구 사항에 따라 두 옵션 중 하나를 선택할 수 있습니다.

Windows 10 Enterprise 다중 세션에서 원격 분석 데이터의 피드백 허브 컬렉션에 대해서는 다음 명령어를 실행합니다.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

다음 명령을 실행하여 Watson 크래시를 해결합니다.

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

5k 해상도 지원을 해결하기 위해 레지스트리 편집기에 다음 명령을 입력합니다. 병렬 스택을 사용하도록 설정하려면 먼저 명령어를 실행해야 합니다.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Azure에 업로드할 이미지 준비

구성을 완료하고 모든 애플리케이션을 설치한 후 [Azure에 업로드하기 위해 Windows VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md)의 지침에 따라 이미지를 준비합니다.

업로드할 이미지를 준비한 후 VM이 해제 상태 또는 할당 해제된 상태인지 확인합니다.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Azure에서 스토리지 계정에 마스터 이미지 업로드

이 섹션은 마스터 이미지가 로컬로 생성된 경우에만 적용됩니다.

다음 지침은 마스터 이미지를 Azure 스토리지 계정에 업로드하는 방법을 설명합니다. Azure 스토리지 계정이 없으면 [이 문서](../storage/common/storage-account-create.md)의 지침에 따라 만듭니다.

1. 아직 수행하지 않았으면 VM 이미지(VHD)를 수정됨으로 변환합니다. 이미지를 수정됨으로 변환하지 않으면 이미지를 성공적으로 만들 수 없습니다.

2. 스토리지 계정에서 Blob 컨테이너에 VHD를 업로드합니다. [Storage Explorer 도구](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 빠르게 업로드할 수 있습니다. Storage Explorer 도구에 대해서는 [이 문서](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)를 참조하세요.

    > [!div class="mx-imgBorder"]
    > ![Microsoft Azure Storage Explorer 도구의 검색 창에 대한 스크린샷입니다. “.vhd 또는 vhdx 파일을 페이지 Blob으로 업로드(권장)” 확인란이 선택되어 있습니다.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 그런 후 브라우저에서 Azure Portal로 이동하고 “이미지”를 검색합니다. 다음 스크린샷에 표시된 것처럼 검색을 수행하면 **이미지 만들기** 페이지가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![이미지의 예제 값이 포함된 Azure Portal의 이미지 만들기 페이지를 보여주는 스크린샷입니다.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 이미지를 만든 후에는 다음 스크린샷과 같은 알림이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![“성공적으로 생성된 이미지” 알림 스크린샷입니다.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>다음 단계

이제 이미지가 준비되었으므로 호스트 풀을 만들거나 업데이트할 수 있습니다. 호스트 풀을 만들고 업데이트하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Resource Manager 템플릿으로 호스트 풀 만들기](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [자습서: Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)
- [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)
- [Azure Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)

VHD 이미지를 준비하거나 사용자 지정한 후 연결 문제가 발생하면 [문제 해결 가이드](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved)를 참조하세요.
