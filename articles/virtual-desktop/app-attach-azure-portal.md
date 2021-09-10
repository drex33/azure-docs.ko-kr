---
title: Azure Virtual Desktop MSIX 앱 연결 포털 - Azure
description: Azure Portal을 사용하여 Azure Virtual Desktop에 대한 MSIX 앱 연결을 설정하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ec462d04402f20d801c43cebd7f4561eb6177f1c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707115"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Azure Portal을 사용하여 MSIX 앱 연결 설정

이 문서에서는 Azure Virtual Desktop 환경에서 MSIX 앱 연결을 설정하는 방법을 안내합니다.

## <a name="requirements"></a>요구 사항

MSIX 앱 연결을 구성하는 데 필요한 사항은 다음과 같습니다.

- 작동하는 Azure Virtual Desktop 배포 Azure Virtual Desktop(클래식)을 배포하는 방법을 알아보려면 [Azure Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조하세요. Azure Resource Manager 통합을 사용하여 Azure Virtual Desktop을 배포하는 방법을 알아보려면 [Azure Portal을 사용하여 호스트 풀 만들기](./create-host-pools-azure-marketplace.md)를 참조하세요.
- 하나 이상의 활성 세션 호스트를 포함하는 Azure Virtual Desktop 호스트 풀.
- MSIX 패키징 도구
- MSIX 패키지 애플리케이션은 파일 공유에 업로드된 MSIX 이미지로 확장됩니다.
- MSIX 패키지를 저장할 Azure Virtual Desktop 배포의 파일 공유.
- MSIX 이미지를 업로드한 파일 공유도 호스트 풀의 모든 VM(가상 머신)에 액세스할 수 있어야 합니다. 사용자는 이미지에 액세스하기 위한 읽기 전용 권한이 필요합니다.
- 인증서를 공개적으로 신뢰할 수 없는 경우 [인증서 설치](app-attach.md#install-certificates)의 지침을 따릅니다.

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>MSIX 앱 연결 애플리케이션의 자동 업데이트 끄기

시작하기 전에 MSIX 앱 연결 애플리케이션의 자동 업데이트를 사용하지 않도록 설정해야 합니다. 자동 업데이트를 사용하지 않도록 설정하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행해야 합니다.

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

## <a name="configure-the-msix-app-attach-management-interface"></a>MSIX 앱 연결 관리 인터페이스 구성

이제 Azure Portal에 대해 MSIX 앱 연결 관리 인터페이스를 다운로드하고 구성해야 합니다.

관리 인터페이스를 설정하려면

1. [Azure Portal을 엽니다](https://portal.azure.com).
2. 확장을 신뢰할 수 있는지 여부를 묻는 메시지가 표시되면 **허용** 을 선택합니다.

      > [!div class="mx-imgBorder"]
      > ![신뢰할 수 없는 확장 창의 스크린샷. "허용"이 빨강으로 강조 표시됩니다.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>호스트 풀에 MSIX 이미지 추가

다음으로 MSIX 이미지를 호스트 풀에 추가해야 합니다.

MSIX를 추가하려면

1. Azure Portal을 엽니다.

2. 검색 창에 **Azure Virtual Desktop** 을 입력한 다음, 서비스 이름을 선택합니다.

3. MSIX 앱을 배치할 호스트 풀을 선택합니다.

4. 현재 호스트 풀에 추가된 모든 **MSIX 패키지** 가 있는 데이터 그리드를 열도록 **MSIX 패키지** 를 선택합니다.

5. **+ 추가** 를 선택하여 **MSIX 패키지 추가** 탭을 엽니다.

6. **MSIX 패키지 추가** 탭에서 다음 값을 입력합니다.

      - **MSIX 이미지 경로** 에 파일 공유의 MSIX 이미지를 가리키는 유효한 UNC 경로를 입력합니다. (예: `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd`) 마치면 **추가** 를 선택하여 MSIX 컨테이너 질의를 통해 경로가 유효한지 확인합니다.

      - **MSIX 패키지** 의 경우 드롭다운 메뉴에서 관련 MSIX 패키지 이름을 선택합니다. 이 메뉴는 **MSIX 이미지 경로** 에 유효한 이미지 경로를 입력한 경우에만 채워집니다.

      - **패키지 애플리케이션** 의 경우 MSIX 패키지의 사용자에게 제공하려는 모든 MSIX 애플리케이션이 목록에 포함되어야 합니다.

      - 필요에 따라 사용자 배포에서 패키지가 사용자에게 친숙해 보이도록 하려면 **표시 이름** 을 입력합니다.

      - **버전** 의 버전 번호가 올바른지 확인합니다.

      - 사용하려는 **등록 형식** 을 선택합니다. 어떤 것을 사용할지는 필요에 따라 다릅니다.

    - **주문형 등록** 은 사용자가 애플리케이션을 시작할 때까지 MSIX 애플리케이션의 전체 등록을 연기합니다. 이 등록 형식을 사용하는 것이 좋습니다.

    - **로그온 차단** 은 사용자가 로그인하는 동안만 등록합니다. 사용자에 대한 로그인 시간이 길어질 수 있으므로 이 형식은 권장하지 않습니다.

7.  **상태** 에서 기본 설정 상태를 선택합니다.
    -  **활성** 상태는 사용자가 패키지와 상호 작용할 수 있도록 합니다.
    -  **비활성** 상태를 설정하면 Azure Virtual Desktop이 패키지를 무시하고 사용자에게 전달하지 않습니다.

8. 완료되면 **추가** 를 선택합니다.

## <a name="publish-msix-apps-to-an-app-group"></a>앱 그룹에 MSIX 앱 게시

이제 패키지에 앱을 게시해야 합니다. 데스크톱 및 원격 앱 애플리케이션 그룹 모두에 대해 이 작업을 수행해야 합니다.

MSIX 이미지가 이미 있는 경우 [앱 그룹에 MSIX 앱 게시](#publish-msix-apps-to-an-app-group)로 건너뜁니다. 레거시 애플리케이션을 테스트하려면 [VM의 데스크톱 설치 관리자에서 MSIX 패키지 만들기](/windows/msix/packaging-tool/create-app-package-msi-vm/)의 지침에 따라 레거시 애플리케이션을 MSIX 패키지로 변환합니다.

앱을 게시하려면

1. Azure Virtual Desktop 리소스 공급자에서 **애플리케이션 그룹** 탭을 선택합니다.

2. 앱을 게시할 애플리케이션 그룹을 선택합니다.

   >[!NOTE]
   >MSIX 애플리케이션은 원격 앱과 데스크톱 앱 그룹 모두에 MSIX 앱 연결을 통해 배달할 수 있습니다.

3. 앱 그룹에 있는 상태에서 **애플리케이션** 탭을 선택합니다. **애플리케이션** 그리드에 앱 그룹 안에 있는 모든 기존 앱이 표시됩니다.

4. **+ 추가** 를 선택하여 **애플리케이션 추가** 탭을 엽니다.

      > [!div class="mx-imgBorder"]
      > ![+ 추가를 선택하여 애플리케이션 추가 탭을 여는 사용자의 스크린샷](media/select-add.png)

5. **애플리케이션 원본** 에서 애플리케이션의 원본을 선택합니다.
    - 데스크톱 앱 그룹을 사용하는 경우 **MSIX 패키지** 를 선택합니다.
      
      > [!div class="mx-imgBorder"]
      > ![애플리케이션 원본 드롭다운 메뉴에서 MSIX 패키지를 선택하는 고객의 스크린샷. MSIX 패키지가 빨강으로 강조 표시됩니다.](media/select-source.png)
    
    - 원격 앱 그룹을 사용하는 경우 다음 옵션 중 하나를 선택합니다.
        
        - 시작 메뉴
        - 앱 경로
        - MSIX 패키지

    - **애플리케이션 이름** 에 애플리케이션에 대한 설명이 포함된 이름을 입력합니다.

    다음의 선택적 기능도 구성할 수 있습니다.
   
    - **표시 이름** 에 사용자에게 표시되는 패키지의 새 이름을 입력합니다.

    - **설명** 에 앱 패키지의 간단한 설명을 입력합니다.

    - 원격 앱 그룹을 사용하는 경우 다음 옵션을 구성할 수도 있습니다.

        - **아이콘 경로**
        - **아이콘 인덱스**
        - **웹 피드에 표시**

6. 완료되면 **저장** 을 선택합니다.

>[!NOTE]
>사용자가 원격 앱과, 동일한 호스트 풀의 데스크톱 앱 그룹에 할당되면 데스크톱 앱 그룹이 피드에 표시됩니다.

## <a name="assign-a-user-to-an-app-group"></a>앱 그룹 사용자 할당

앱 그룹에 MSIX 앱을 할당한 후에는 사용자에게 액세스 권한을 부여해야 합니다. 게시된 MSIX 애플리케이션이 있는 앱 그룹에 사용자 또는 사용자 그룹을 추가하여 액세스 권한을 할당할 수 있습니다. [Azure Portal로 앱 그룹 관리](manage-app-groups.md)의 지침에 따라 사용자를 앱 그룹에 할당합니다.

>[!NOTE]
>공개 미리 보기 중에는 원격 앱을 테스트할 때 MSIX 앱 연결 원격 앱이 피드에서 사라질 수 있습니다. 평가 환경에서 사용하는 호스트 풀을 프로덕션 환경의 RD Broker에서 서비스하기 때문에 앱이 표시되지 않습니다. 프로덕션 환경의 RD Broker는 MSIX 앱 연결 원격 앱의 현재 상태를 등록하지 않으므로 피드에 앱이 표시되지 않습니다.

## <a name="change-msix-package-state"></a>MSIX 패키지 상태 변경

이제 패키지로 수행하려는 작업에 따라 MSIX 패키지 상태를 **활성** 또는 **비활성** 으로 변경해야 합니다. 활성 패키지는 게시되면 사용자가 상호 작용할 수 있는 패키지입니다. 비활성 패키지는 Azure Virtual Desktop에서 무시되므로 사용자가 그 안의 앱과 상호 작용할 수 없습니다.

### <a name="change-state-with-the-applications-list"></a>애플리케이션 목록으로 상태 변경

애플리케이션 목록으로 패키지 상태를 변경하려면

1. 호스트 풀로 이동하여 **MSIX 패키지** 를 선택합니다. 호스트 풀 안에 있는 모든 기존 MSIX 패키지 목록이 표시될 것입니다.

2. 상태 변경이 필요한 MSIX 패키지를 선택하고 **상태 변경** 을 선택합니다.

### <a name="change-state-with-update-package"></a>업데이트 패키지로 상태 변경

업데이트 패키지로 패키지 상태를 변경하려면

1. 호스트 풀로 이동하여 **MSIX 패키지** 를 선택합니다. 호스트 풀 안에 있는 모든 기존 MSIX 패키지 목록이 표시될 것입니다.

2. MSIX 패키지 목록에서 상태를 변경하려는 패키지의 이름을 선택합니다. 그러면 **패키지 업데이트** 탭이 열립니다.

3. **상태** 스위치를 **비활성** 또는 **활성** 으로 전환하고 **저장** 을 선택합니다.

## <a name="change-msix-package-registration-type"></a>MSIX 패키지 등록 형식 변경

패키지의 등록 형식을 변경하려면

1. **MSIX 패키지** 를 선택합니다. 호스트 풀 안에 있는 모든 기존 MSIX 패키지 목록이 표시될 것입니다.

2. **MSIX 패키지 그리드** 에서 **패키지 이름** 을 선택하여 패키지 업데이트를 위한 블레이드를 엽니다.

3. 필요에 따라 **요청 시/로그온 차단** 단추를 통해 **등록 형식** 을 전환하고 **저장** 을 선택합니다.

## <a name="remove-an-msix-package"></a>MSIX 패키지 제거

호스트 풀에서 MSIX 패키지를 제거하려면

1. **MSIX 패키지** 를 선택합니다.  호스트 풀 안에 있는 모든 기존 MSIX 패키지 목록이 표시될 것입니다.

2. 삭제할 패키지의 이름 오른쪽에 있는 줄임표(...)를 선택하고 **제거** 를 선택합니다.

## <a name="remove-msix-apps"></a>MSIX 앱 제거

패키지에서 개별 MSIX 앱을 제거하려면

1. 호스트 풀로 이동하고 **애플리케이션 그룹** 을 선택합니다.

2. MSIX 앱을 제거할 애플리케이션 그룹을 선택합니다.

3. **애플리케이션** 탭을 엽니다.

4. 제거할 앱을 선택하고 **제거** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 이 기능에 대해 커뮤니티에 질문합니다.

[Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.

다음은 도움이 될 수 있는 다른 문서입니다.

- [MSIX 앱 연결 용어](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.yml)
