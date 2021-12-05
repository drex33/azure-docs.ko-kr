---
title: Azure Virtual Desktop FSLogix 프로필 컨테이너 공유 - Azure
description: 가상 머신 기반 파일 공유를 사용하여 Azure Virtual Desktop 호스트 풀에 대해 FSLogix 프로필 컨테이너를 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 4701cb9396bc38d1b94a624213a579d4bc559241
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133569132"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기

Windows Virtual Desktop 서비스는 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공합니다. UPD(사용자 프로필 디스크) 솔루션을 사용하지 않는 것이 좋습니다. 이 솔루션은 이후 버전의 Azure Virtual Desktop에서 더 이상 사용되지 않습니다.

이 문서에서는 가상 머신 기반 파일 공유를 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 공유를 설정하는 방법을 설명합니다. 파일 공유 대신 Azure Files를 사용하는 것이 좋습니다. 추가 FSLogix 설명서는 [FSLogix 사이트](https://docs.fslogix.com/)를 참조하세요.

>[!NOTE]
>Azure의 다양한 FSLogix 프로필 컨테이너 스토리지 옵션에 대한 비교 자료를 찾고 있다면 [FSLogix 프로필 컨테이너의 스토리지 옵션](store-fslogix-profile.md)을 참조하세요.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>파일 공유 역할을 하는 새 가상 머신 만들기

가상 머신을 만들 때 호스트 풀 가상 머신과 동일한 가상 네트워크 또는 호스트 풀 가상 머신에 연결된 가상 네트워크에 가상 머신을 배치해야 합니다. 다음과 같은 여러 방법으로 가상 머신을 만들 수 있습니다.

- [Azure Gallery 이미지에서 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [관리 이미지에서 가상 머신 만들기](../virtual-machines/windows/create-vm-generalized-managed.md)
- [관리되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image)

가상 머신을 만든 후 다음 작업을 수행하여 도메인에 조인합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 가상 머신에서 **제어판** 을 시작하고 **시스템** 을 선택합니다.
3. **컴퓨터 이름**, **설정 변경**, **변경...** 을 차례로 선택합니다.
4. **도메인** 을 선택한 다음, 가상 네트워크에 Active Directory 도메인을 입력합니다.
5. 도메인 조인 머신에 대한 권한이 있는 도메인 계정으로 인증합니다.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>사용자 프로필에 대한 파일 공유 역할을 하는 가상 머신 준비

다음은 사용자 프로필의 파일 공유 역할을 하도록 가상 머신을 준비하는 방법에 대한 일반적인 지침입니다.

1. Azure Virtual Desktop Active Directory 사용자를 [Active Directory 보안 그룹](/windows/security/identity-protection/access-control/active-directory-security-groups/)에 추가합니다. 이 보안 그룹은 방금 만든 파일 공유 가상 머신에 대해 Azure Virtual Desktop 사용자를 인증하는 데 사용됩니다.
2. [파일 공유 가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
3. 파일 공유 가상 머신에서 프로필 공유로 사용할 **C 드라이브** 에 폴더를 만듭니다.
4. 새 폴더를 마우스 오른쪽 단추로 클릭하고 **속성**, **공유**, **고급 공유...** 를 차례로 선택합니다.
5. **이 폴더 공유**, **권한...** , **추가...** 를 차례로 선택합니다.
6. Azure Virtual Desktop 사용자를 추가한 보안 그룹을 검색한 다음, 그룹에 **모든 권한** 이 있는지 확인합니다.
7. 보안 그룹을 추가한 후 폴더를 마우스 오른쪽 단추로 클릭하고 **속성**, **공유** 를 차례로 선택한 다음, 나중에 사용할 **네트워크 경로** 를 복사합니다.

권한에 대한 자세한 내용은 [FSLogix 설명서](/fslogix/fslogix-storage-config-ht/)를 참조하세요.

## <a name="configure-the-fslogix-profile-container"></a>FSLogix 프로필 컨테이너 구성

FSLogix 소프트웨어를 사용하여 가상 머신을 구성하려면 호스트 풀에 등록된 각 머신에서 다음을 수행합니다.

1. 가상 머신을 만들 때 입력한 자격 증명으로 [가상 머신에 연결](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)합니다.
2. 인터넷 브라우저를 시작하고 [이 링크](https://aka.ms/fslogix_download)로 이동하여 FSLogix 에이전트를 다운로드합니다.
3. .zip 파일에서 \\\\Win32\\ 릴리스 또는 \\\\X64\\ 릴리스로 이동하고 **FSLogixAppsSetup** 를 실행하여 FSLogix 에이전트를 설치합니다.  FSLogix를 설치하는 방법에 대해 자세히 알아보려면 [FSLogix 다운로드 및 설치](/fslogix/install-ht/)를 참조하세요.
4. **Program Files** > **FSLogix** > **Apps** 로 이동하여 에이전트가 설치되었는지 확인합니다.
5. 시작 메뉴에서 관리자 권한으로 **RegEdit** 를 실행합니다. **컴퓨터\\HKEY_LOCAL_MACHINE\\software\\FSLogix** 로 이동합니다.
6. **Profiles** 이라는 키를 만듭니다.
7. 프로필 키에 대해 다음 값을 만듭니다.

| 속성                | 유형               | Data/Value                        |
|---------------------|--------------------|-----------------------------------|
| 사용             | DWORD              | 1                                 |
| VHDLocations        | 다중 문자열 값 | "파일 공유의 네트워크 경로"     |

>[!IMPORTANT]
>Azure에서 Azure Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Azure Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.
