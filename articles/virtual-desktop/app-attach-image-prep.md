---
title: Azure Virtual Desktop에서 MSIX 앱 연결 이미지 준비 - Azure
description: Azure Virtual Desktop 호스트 풀에 대한 MSIX 앱 연결 이미지를 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b3e33fecc1df930cf4fb01808b2a8400a210fce8
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707152"
---
# <a name="prepare-an-msix-image-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 MSIX 이미지 준비

MSIX 앱 연결은 MSIX 패키지의 앱을 사용자 세션에 동적으로 연결하는 데 사용할 수 있는 애플리케이션 계층화 솔루션입니다. MSIX 패키지 시스템은 운영 체제에서 앱을 분리하여 가상 머신에 대한 이미지를 더 쉽게 빌드할 수 있습니다. 또한 MSIX 패키지를 사용하면 사용자가 가상 머신에서 액세스할 수 있는 앱을 보다 세부적으로 제어할 수 있습니다. 마스터 이미지에서 앱을 분리하여 나중에 사용자에게 제공할 수도 있습니다.

데스크톱 설치 프로그램(예: MSI, EXE, ClickOnce, App-V 또는 Script)을 MSIX로 변환하는 방법에 대한 지침은 [모든 데스크톱 설치 프로그램(MSI, EXE, ClickOnce 또는 App-V)에서 MSIX 패키지 만들기](/windows/msix/packaging-tool/create-app-package)에서 확인할 수 있습니다.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>MSIX용 VHD 또는 VHDX 패키지 만들기

MSIX 패키지가 제대로 작동하려면 VHD 또는 VHDX 형식이어야 합니다. 즉, 시작하려면 VHD 또는 VHDX 패키지를 만들어야 합니다.

>[!NOTE]
>아직 만들지 않은 경우 [Windows 10에 Hyper-V 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)의 지침에 따라 Hyper-V를 사용하도록 설정해야 합니다.

MSIX용 VHD 또는 VHDX 패키지를 만들려면 다음을 수행합니다.

1. 먼저 PowerShell을 엽니다.
2. 다음으로, 다음 cmdlet을 실행하여 VHD를 만듭니다.

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > VHD가 확장된 MSIX 패키지를 포함할 수 있을 만큼 큰지 확인해야 합니다.

3. 다음 cmdlet을 실행하여 방금 만든 VHD를 탑재합니다.

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 그런 다음, 이 cmdlet을 실행하여 탑재된 VHD를 초기화합니다.

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. 그다음, 이 cmdlet을 실행하여 초기화된 VHD에 대한 새 파티션을 만듭니다.

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 이 cmdlet을 실행하여 파티션을 포맷합니다.

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 마지막으로, 탑재된 VHD에 부모 폴더를 만듭니다. MSIX 패키지가 제대로 작동하려면 부모 폴더가 있어야 하기 때문에 이 단계가 필요합니다. 부모 폴더가 있는 한 부모 폴더의 이름은 중요하지 않습니다.

## <a name="expand-msix"></a>MSIX 확장

그런 다음, VHD에 해당 파일의 “압축을 풀어” MSIX 이미지를 확장해야 합니다.

MSIX 이미지를 확장하려면 다음을 수행합니다.

1. [msixmgr 도구를 다운로드](https://aka.ms/msixmgr)하고 .zip 폴더를 세션 호스트 VM 내 폴더에 저장합니다.

2. msixmgr 도구 .zip 폴더의 압축을 풉니다.

3. msixmgr 도구의 압축을 푼 폴더에 소스 MSIX 패키지를 넣습니다.

4. 관리자 권한으로 명령 프롬프트를 열고 msixmgr 도구를 다운로드하고 압축을 푼 폴더로 이동합니다.

5. 다음 cmdlet을 실행하여 이전 섹션에서 만든 VHD에 MSIX의 압축을 풉니다.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    압축 풀기가 완료되면 다음 메시지가 표시됩니다.

    > Successfully unpacked and applied ACLs for package:<package name>.msix(&lt;package name&gt;.msix 패키지에 대해 압축을 풀고 ACL을 적용했습니다.)

    >[!NOTE]
    > 네트워크 또는 인터넷에 연결되지 않은 디바이스에서 비즈니스 또는 교육용 Microsoft Store의 패키지를 사용하는 경우 앱을 실행하려면 Microsoft Store에서 패키지 라이선스를 다운로드하고 설치해야 합니다. 라이선스를 다운로드하려면 [오프라인으로 패키지 사용](app-attach.md#use-packages-offline)을 참조하세요.

6. 탑재된 VHD로 이동하고 앱 폴더를 열어 패키지 내용이 있는지 확인합니다.

7. VHD를 분리합니다.

## <a name="upload-msix-image-to-share"></a>공유할 MSIX 이미지 업로드

MSIX 패키지를 만든 후에는 결과 VHD, VHDX 또는 CIM 파일을 업로드하여 사용자의 가상 머신에서 액세스할 수 있는 위치에서 공유해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 이 기능에 대해 커뮤니티에 질문합니다.

[Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 도움이 될 수 있는 다른 문서입니다.

- [MSIX 앱 연결 용어](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.yml)
