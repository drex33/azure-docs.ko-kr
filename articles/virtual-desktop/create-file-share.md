---
title: 도메인 컨트롤러를 사용하여 Azure Files 파일 공유 만들기 - Azure
description: Active Directory 도메인을 사용하여 기존 Azure Virtual Desktop 호스트 풀의 Azure 파일 공유에 FSLogix 프로필 컨테이너를 설정합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 013a2d377ef4e30848d29c43c7275c6644cf4549
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593062"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Azure Files 및 AD DS를 사용하여 프로필 컨테이너 만들기

이 문서에서는 기존 Azure Virtual Desktop 호스트 풀의 도메인 컨트롤러에서 인증한 Azure 파일 공유를 만드는 방법에 대해 알아봅니다. 이 파일 공유를 사용하여 스토리지 프로필을 저장할 수 있습니다.

이 프로세스는 온-프레미스 디렉터리 서비스인 AD DS(Active Directory Domain Services)를 사용합니다. Azure AD DS를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법에 대한 자세한 내용은 [Azure Files를 사용하여 FSLogix 프로필 컨테이너 만들기](create-profile-container-adds.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 도메인 컨트롤러가 Azure에 동기화되고 세션 호스트가 연결된 Azure VNET(가상 네트워크)에서 확인할 수 있는지 확인합니다.

## <a name="set-up-a-storage-account"></a>스토리지 계정 설정

먼저 Azure Files 스토리지 계정을 설정해야 합니다.

스토리지 계정을 설정하려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.

2. 검색 창에서 **스토리지 계정** 을 검색합니다.

3. **+추가** 를 선택합니다.

4. **스토리지 계정 만들기** 페이지에 다음 정보를 입력합니다.

    - 새 리소스 그룹 만들기
    - 스토리지 계정에 고유한 이름을 입력합니다.
    - **위치** 의 경우 Azure Virtual Desktop 호스트 풀과 동일한 위치를 선택하는 것이 좋습니다.
    - **성능** 은 **표준** 을 선택합니다. (IOPS 요구 사항에 따라 달라집니다. 자세한 내용은 [Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션](store-fslogix-profile.md)을 참조하세요.)
    - **계정 유형** 의 경우 **StorageV2** 또는 **FileStorage**(성능 계층이 프리미엄인 경우에만 사용 가능)를 선택합니다.
    - **복제** 의 경우 **LRS(로컬 중복 스토리지)** 를 선택합니다.

5. 완료되면 **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다.

보다 자세한 구성 지침이 필요한 경우 [국가별 가용성](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)을 참조하세요.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

다음으로 Azure 파일 공유를 만들어야 합니다.

파일 공유를 만들려면,

1. **리소스로 이동** 을 선택합니다.

2. 개요 페이지에서 **파일 공유** 를 선택합니다.

3. **+파일 공유** 를 선택하고, **프로필** 이라는 새 파일 공유를 만든 다음, 적절한 할당량을 입력하거나 할당량 없이 필드를 비워 둡니다.

4. **만들기** 를 선택합니다.

## <a name="enable-active-directory-authentication"></a>Active Directory 인증 사용

다음으로 AD(Active Directory) 인증을 사용하도록 설정해야 합니다. 이 정책을 사용하도록 설정하려면 이미 도메인에 가입된 머신에서 이 섹션의 지침을 따라야 합니다. 인증을 사용하도록 설정하려면 도메인 컨트롤러를 실행하는 VM에서 다음 지침을 따르세요.

1. 도메인에 가입된 VM에 대한 원격 데스크톱 프로토콜입니다.

2. AzFilesHybrid 모듈을 설치하고 인증을 사용하도록 설정하려면 [Azure 파일 공유에 대한 AD DS 인증 사용](../storage/files/storage-files-identity-ad-ds-enable.md)의 지침을 따르세요.

3.  Azure Portal을 열고, 스토리지 계정을 열고, **구성** 을 선택한 다음, **AD(Active Directory)** 가 **사용** 으로 설정되어 있는지 확인합니다.

     > [!div class="mx-imgBorder"]
     > ![Azure AD(Active Directory)를 사용할 수 있는 구성 페이지의 스크린샷](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-azure-virtual-desktop-users"></a>Azure Virtual Desktop 사용자에게 Azure RBAC 권한 할당

스토리지 계정에 저장된 FSLogix 프로필을 포함해야 하는 모든 사용자에게 스토리지 파일 데이터 SMB 공유 기여자 역할을 할당해야 합니다.

Azure Virtual Desktop 세션 호스트에 로그인하는 사용자는 파일 공유에 액세스하기 위한 액세스 권한이 필요합니다. Azure 파일 공유에 대한 액세스 권한을 부여하려면 기존 Windows 공유와 마찬가지로 공유 수준과 NTFS 수준 모두에서 사용 권한을 구성해야 합니다.

공유 수준 권한을 구성하려면 각 사용자에게 적절한 액세스 권한이 있는 역할을 할당합니다. 권한은 개별 사용자 또는 Azure AD 그룹에 할당할 수 있습니다. 자세히 알아보려면 [ID에 액세스 권한 할당](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)을 참조하세요.

>[!NOTE]
>권한을 할당하는 계정 또는 그룹은 도메인에서 만들어지고 Azure AD와 동기화되어 있어야 합니다. Azure AD에서 만든 계정이 작동하지 않습니다.

Azure RBAC(Azure 역할 기반 액세스 제어) 권한을 할당하려면 다음을 수행합니다.

1. Azure Portal을 엽니다.

2. [스토리지 계정 설정](#set-up-a-storage-account)에서 만든 스토리지 계정을 엽니다.

3. **파일 공유** 를 선택한 다음, 사용하려는 파일 공유의 이름을 선택합니다.

4. **Access Control(IAM)** 을 선택합니다.

5. **역할 할당 추가** 를 선택합니다.

6. **역할 할당 추가** 탭에서 관리자 계정에 대한 **스토리지 파일 데이터 SMB 공유 관리자 권한 기여자** 를 선택합니다.

     해당 FSLogix 프로필에 대한 사용자 권한을 할당하려면 이와 동일한 지침을 따릅니다. 그러나 5단계에서는 **스토리지 파일 데이터 SMB 공유 기여자** 를 대신 선택합니다.

7. **저장** 을 선택합니다.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Azure 파일 공유에 대한 사용자 할당 권한

사용자에게 Azure RBAC 권한을 할당한 후에는 NTFS 권한을 구성해야 합니다.

시작하려면 Azure Portal에서 다음 두 가지를 알고 있어야 합니다.

- UNC 경로
- 스토리지 계정 키입니다.

### <a name="get-the-unc-path"></a>UNC 경로 가져오기

UNC 경로를 가져오는 방법은 다음과 같습니다.

1. Azure Portal을 엽니다.

2. [스토리지 계정 설정](#set-up-a-storage-account)에서 만든 스토리지 계정을 엽니다.

3. **설정** 을 선택한 다음, **속성** 을 선택합니다.

4. 선택한 텍스트 편집기에 **기본 파일 서비스 엔드포인트** URI를 복사합니다.

5. URI를 복사한 후 다음 작업을 수행하여 UNC로 변경합니다.

    - `https://`를 제거하고 `\\`로 바꿉니다.
    - 슬래시(`/`)를 백슬래시(`\`)로 바꿉니다.
    - [Azure 파일 공유 만들기](#create-an-azure-file-share)에서 만든 파일 공유의 이름을 UNC의 끝에 추가합니다.

        예: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>스토리지 계정 키 가져오기

스토리지 계정 키를 가져오려면 다음을 수행합니다.

1. Azure Portal을 엽니다.

2. [스토리지 계정 설정](#set-up-a-storage-account)에서 만든 스토리지 계정을 엽니다.

3. **스토리지 계정** 탭에서 **액세스 키** 를 선택합니다.

4. **Key1** 또는 **key2** 를 로컬 머신의 파일에 복사합니다.

### <a name="configure-ntfs-permissions"></a>NTFS 권한 구성

NTFS 권한을 구성하려면 다음을 수행합니다.

1. 도메인에 가입된 VM에서 명령 프롬프트를 엽니다.

2. 다음 명령을 실행하여 Azure 파일 공유를 탑재하고 드라이브 문자를 할당합니다.

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. 다음 명령을 실행하여 Azure 파일 공유에 대한 액세스 권한을 검토합니다.

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    `<mounted-drive-letter>`를 매핑한 드라이브의 문자로 바꿉니다.

    *NT Authority\Authenticated Users* 및 *BUILTIN\Users* 에는 기본적으로 특정 권한이 있습니다. 이러한 기본 권한을 통해 이러한 사용자는 다른 사용자의 프로필 컨테이너를 읽을 수 있습니다. 그러나 [프로필 컨테이너 및 Office 컨테이너에서 사용할 수 있도록 스토리지 권한 구성](/fslogix/fslogix-storage-config-ht)에서 설명하는 권한은 사용자가 다른 사용자의 프로필 컨테이너를 읽을 수 없습니다.

4. 다음 명령을 실행하여 Azure Virtual Desktop 사용자가 다른 사용자의 프로필 컨테이너에 대한 액세스를 차단하면서 자신의 프로필 컨테이너를 만들도록 할 수 있습니다.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - \<mounted-drive-letter\>를 드라이브를 매핑하는 데 사용한 드라이브의 문자로 바꿉니다.
     - \<user-email\>을 사용자의 UPN 또는 공유에 대한 액세스 권한이 필요한 사용자를 포함하는 Active Directory 그룹으로 바꿉니다.

     예:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>세션 호스트 VM에서 FSLogix 구성

이 섹션에서는 FSLogix를 사용하여 VM을 구성하는 방법을 보여 줍니다. 세션 호스트를 구성할 때마다 이 지침을 따라야 합니다. 구성을 시작하기 전에 [FSLogix 다운로드 및 설치](/fslogix/install-ht)의 지침을 따르세요. 모든 세션 호스트에서 레지스트리 키를 설정하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 이러한 옵션을 이미지에서 설정하거나 그룹 정책을 구성할 수 있습니다.

세션 호스트 VM에서 FSLogix를 구성하려면 다음을 수행하세요.

1. Azure Virtual Desktop 호스트 풀의 세션 호스트 VM으로 RDP합니다.

2. [FSLogix를 다운로드하고 설치합니다](/fslogix/install-ht).

5. [프로필 컨테이너 레지스트리 설정 구성](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)의 지침을 따르세요.

    - **컴퓨터** > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix** 로 이동합니다.

    - **프로필** 키를 만듭니다.

    - 1의 값으로 **Enabled, DWORD** 를 만듭니다.

    - **VHDLocations, MULTI_SZ** 를 만듭니다.

    - **VHDLocations** 의 값을 [UNC 경로 가져오기](#get-the-unc-path)에서 생성한 UNC 경로로 설정합니다.

6. VM을 다시 시작합니다.

## <a name="testing"></a>테스트

FSLogix를 설치하고 구성한 후에는 호스트 풀에서 앱 그룹이나 데스크톱이 할당된 사용자 계정으로 로그인하여 배포를 테스트할 수 있습니다. 로그인하는 사용자 계정에 파일 공유에 대한 권한이 있는지 확인합니다.

이전에 사용자가 로그인한 경우 이 세션 중에 사용되는 기존 로컬 프로필을 갖게 됩니다. 로컬 프로필을 만들지 않으려면 테스트에 사용할 새 사용자 계정을 만들거나 [자습서: 사용자 프로필을 리디렉션하도록 프로필 컨테이너 구성](/fslogix/configure-profile-container-tutorial/)에 설명된 구성 방법을 사용합니다.

세션에 대한 권한을 확인하려면 다음을 수행합니다.

1. Azure Virtual Desktop에서 세션을 시작합니다.

2. Azure Portal을 엽니다.

3. [스토리지 계정 설정](#set-up-a-storage-account)에서 만든 스토리지 계정을 엽니다.

4. Azure 파일 공유 만들기 페이지에서 **공유 만들기** 를 선택합니다.

5. 이제 사용자 프로필을 포함하는 폴더가 파일에 있는지 확인합니다.

추가 테스트를 수행하려면 [프로필이 작동하는지 확인](create-profile-container-adds.md#make-sure-your-profile-works)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

FSLogix 문제를 해결하려면 [이 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht)를 참조하세요.
