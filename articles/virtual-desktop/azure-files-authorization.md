---
title: Azure Files를 위해 Azure Virtual Desktop 호스트 풀에 권한을 부여하는 방법 - Azure
description: Azure Files를 사용하기 위해 Azure Virtual Desktop 호스트 풀에 권한을 부여하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539395"
---
# <a name="authorize-an-account-for-azure-files"></a>Azure Files를 위해 계정에 권한 부여

이 문서에서는 Azure Files를 사용하기 위해 Azure Virtual Desktop 호스트 풀에 권한을 부여하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항

시작하기 전에 다음 항목이 필요합니다.

- Azure AD(Azure Active Directory)와 동기화되는 AD DS(Active Directory Domain Services) 계정
- AD DS에 그룹을 만들 권한
- 스토리지 계정, 필요한 경우 새 스토리지 계정을 만드는 데 필요한 권한
- 액세스 권한이 있는 AD DS에 조인된 VM(가상 머신) 또는 물리적 머신
- 모든 세션 호스트가 도메인에 조인된 Azure Virtual Desktop 호스트 풀

## <a name="create-a-security-group-in-active-directory-domain-services"></a>Active Directory Domain Services에서 보안 그룹 만들기

먼저 AD DS에서 네트워크 보안 그룹을 만들어야 합니다. 이 보안 그룹은 이후 단계에서 공유 수준 및 NTFS(New Technology File System) 파일 공유 권한을 부여하는 데 사용됩니다.

>[!NOTE]
>사용하려는 기존 보안 그룹이 있는 경우 새 그룹을 만드는 대신 해당 그룹의 이름을 선택합니다.

보안 그룹을 만들려면

1. 보안 그룹에 추가하려는 AD DS에 조인된 VM 또는 물리적 머신이 있는 원격 세션을 엽니다.

2. **Active Directory 사용자 및 컴퓨터** 를 엽니다.

3. 도메인 노드 아래에서 머신의 이름을 마우스 오른쪽 단추로 클릭합니다. 드롭다운 메뉴에서 **새로 만들기** > **그룹** 을 선택합니다.

4. **새 개체 – 그룹** 창에서 새 그룹의 이름을 입력하고 다음 값을 선택합니다.

    - **그룹 범위** 로 **전역** 을 선택합니다.
    - **그룹 유형** 으로 **보안** 을 선택합니다.

5. 새 그룹을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.

6. **속성** 창에서 **멤버** 탭을 선택합니다.

7. **추가...** 를 선택합니다.

8. **사용자, 연락처, 컴퓨터, 서비스 계정 또는 그룹 선택** 창에서 **개체 형식...** > **컴퓨터** 를 선택합니다. 작업을 마쳤으면 **확인** 을 선택합니다.

9. **선택할 개체 이름 입력** 창에서 보안 그룹에 포함할 모든 세션 호스트의 이름을 입력합니다.

10. **이름 확인** 을 선택한 다음, 표시되는 목록에서 사용할 세션 호스트의 이름을 선택합니다.

11. **확인** 을 선택한 다음, **적용** 을 선택합니다.

>[!NOTE]
>새 보안 그룹은 Azure AD와 동기화하는 데 최대 1시간이 걸릴 수 있습니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

아직 스토리지 계정을 만들지 않은 경우 먼저 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 따릅니다. 새 스토리지 계정을 만드는 경우 새 파일 공유도 만들어야 합니다.

>[!NOTE]
>**프리미엄** 스토리지 계정을 만드는 경우 **계정 종류** 를 **FileStorage** 로 설정해야 합니다.

## <a name="get-rbac-permissions"></a>RBAC 권한 가져오기

RBAC 권한을 가져오려면:

1. 사용하려는 스토리지 계정을 선택합니다.

2. **액세스 제어(IAM)** 를 선택한 다음, **추가** 를 선택합니다. 다음으로, 드롭다운 메뉴에서 **역할 할당 추가** 를 선택합니다.

3. **역할 할당 추가** 화면에서 다음 값을 선택합니다.

    - **역할** 에 대해 **스토리지 파일 데이터 SMB 공유 기여자** 를 선택합니다.
    - **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.
    - **구독** 에 대해 **환경에 따라** 를 선택합니다.
    - **선택** 의 경우 세션 호스트가 포함된 Active Directory 그룹의 이름을 선택합니다.

4. **저장** 을 선택합니다.

## <a name="join-your-storage-account-to-ad-ds"></a>AD DS에 스토리지 계정 조인

다음으로, 스토리지 계정을 AD DS에 조인해야 합니다. 계정을 AD DS에 조인하려면 다음을 수행합니다.

1. AD DS에 조인된 VM이나 물리적 머신에서 원격 세션을 엽니다.

      >[!NOTE]
      > Azure AD에 동기화된 온-프레미스 AD DS 자격 증명을 사용하여 스크립트를 실행합니다. 온-프레미스 AD DS 자격 증명에는 스토리지 계정 소유자 또는 기여자 Azure 역할 권한이 있어야 합니다.

2. [AzFilesHybrid에 최신 버전](https://github.com/Azure-Samples/azure-files-samples/releases)을 다운로드하고 압축을 풉니다.

3. 관리자 권한 모드에서 **PowerShell** 을 엽니다.

4. 다음 cmdlet을 실행하여 실행 정책을 설정합니다.
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. 다음으로, AzfileHybrid의 압축을 푼 폴더로 이동하고 다음 명령을 실행합니다.

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. 이후 다음 cmdlet을 실행하여 AzFilesHybrid 모듈을 가져옵니다.
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. 그러고 나서 다음 cmdlet을 실행하여 Azure AD에 연결합니다.
   
   ```powershell
   Connect-AzAccount
   ```

8. 자리 표시자를 시나리오와 관련된 값으로 바꿔서 다음 매개 변수를 설정합니다.

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  마지막으로 다음 명령을 실행합니다.

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>NTFS 수준 권한 가져오기

Azure Files 스토리지 계정에 대해 AD DS 컴퓨터 계정으로 인증하려면 앞에서 설정한 RBAC 권한 외에도 NTFS 수준 권한을 할당해야 합니다.

NTFS 권한을 할당하려면 다음을 수행합니다.

1. Azure Portal을 열고 AD DS에 추가한 스토리지 계정으로 이동합니다.

2. **액세스 키** 를 선택하고 **Key1** 필드에 값을 복사합니다.

3. AD DS에 조인된 VM 또는 물리적 머신에서 원격 세션을 시작합니다.

4. 관리자 모드에서 명령 프롬프트를 엽니다.

5. 자리 표시자를 배포와 관련된 값으로 바꿔서 다음 명령을 실행합니다.

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >이 명령을 실행하면 출력에 “명령이 성공적으로 완료되었습니다.”가 표시됩니다. 표시되지 않으면 입력을 확인하고 다시 시도합니다.

6. **파일 탐색기** 를 열고 5단계에서 명령에 사용한 드라이브 문자를 찾습니다.

7. 드라이브 문자를 마우스 오른쪽 단추로 클릭한 다음, 드롭다운 메뉴에서 **속성** > **보안** 을 선택합니다.

8. **편집** 을 선택한 다음, **추가...** 를 선택합니다.

    >[!NOTE]
    >도메인 이름이 AD DS 도메인 이름과 일치하는지 확인합니다. 일치하지 않으면 스토리지 계정이 도메인에 조인되지 않은 것입니다. 계속하려면 도메인 조인 계정을 사용해야 합니다.

9. 메시지가 표시되면 관리자 자격 증명을 입력합니다.

10. **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 창의 [Active Directory Domain Services에서 보안 그룹 만들기](#create-a-security-group-in-active-directory-domain-services)에서 그룹 이름을 입력합니다.

11. **확인** 을 선택합니다. 이후에 그룹에 **읽기 및 실행** 권한이 있는지 확인합니다. 그룹에 권한이 있는 경우 다음 이미지와 같이 “허용” 확인란이 선택되어 있습니다.

    > [!div class="mx-imgBorder"]
    > ![보안 창의 스크린샷. “권한”으로 표시된 목록에서 “허용” 열 아래에 “읽기 및 실행” 권한에 녹색 확인 표시가 있음](media/read-and-execute.png)

12. **읽기 및 실행** 권한이 있는 컴퓨터 계정으로 Active Directory 그룹을 보안 그룹에 추가합니다.

13. **적용** 을 선택합니다. Windows 보안 프롬프트가 표시되면 **예** 를 선택하여 변경 내용을 확인합니다.

## <a name="next-steps"></a>다음 단계

설정 후 문제가 발생하면 [Azure Files 문제 해결 문서](troubleshoot-authorization.md)를 확인하세요.