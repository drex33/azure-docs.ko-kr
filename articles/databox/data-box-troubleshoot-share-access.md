---
title: Azure Data Box에 데이터를 복사하는 동안 공유 연결 오류 문제 해결 | Microsoft Docs
description: Azure Data Box에 데이터를 복사하는 동안 SMB 공유 연결을 막는 네트워크 문제를 확인하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773174"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>Azure Data Box로 데이터를 복사하는 동안 공유 연결 오류 문제 해결

이 문서에서는 네트워크 문제로 인해 Azure Data Box 디바이스의 SMB 공유에 연결할 수 없는 경우 수행할 작업을 설명합니다.

디바이스에서 공유에 연결할 수 없는 가장 일반적인 이유는 다음과 같습니다.

- [도메인 문제](#check-for-a-domain-issue)
- [계정이 공유 외부에서 잠겨 있습니다](#account-locked-out-of-share)
- [그룹 정책이 연결을 막고 있습니다](#check-for-a-blocking-group-policy)
- [사용 권한 문제](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>도메인 문제 확인

도메인 문제로 공유가 연결되지 않는지 확인하려면 다음을 수행합니다.

- 디바이스에 연결을 시도하고 사용자 이름을 다음 형식 중 하나로 입력합니다.

    - `<device IP address>\<user name>`
    - `\<user name>`

디바이스에 연결할 수 있다면 도메인 문제로 공유에 연결되지 않는 것은 아닙니다.

## <a name="account-locked-out-of-share"></a>계정이 공유 외부에서 잠김

잘못된 암호로 공유에 연결하는 데 5번 실패한 후에는 공유가 잠기므로 15분 동안 연결할 수 없습니다.
 
실패한 연결 시도에는 재시도와 같은 백그라운드 프로세스가 포함될 수 있습니다(사용자는 인식하지 못할 수도 있음).

> [!NOTE]
> 이전 디바이스에서 Data Box 4.0 이전 버전을 사용한다면 3번 로그인 시도에 실패한 후 30분 동안 계정이 잠깁니다.

**오류 설명.** 공유에 액세스하는 방법에 따라 다음 오류 중 하나가 표시됩니다.

- SMB를 통해 호스트 컴퓨터에서 연결을 시도하는 경우 “참조된 계정이 현재 잠겨 있으므로 로그온할 수 없습니다.”라는 오류가 표시됩니다.

  다음 예에서는 이러한 연결 시도의 출력이 나타나 있습니다.

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- 데이터 복사 서비스를 사용하는 경우 디바이스의 로컬 웹 UI에서 다음과 같은 알림을 받게 됩니다.

  ![Data Box에 대한 로컬 웹 UI 내 알림 창의 스크린샷 잠긴 공유 계정에 대한 알림이 강조 표시됩니다.](media/data-box-troubleshoot-share-access/share-lock-01.png)


**권장 해결 방법.** 공유 계정 잠금 후에 SMB 공유에 연결하려면 다음 단계를 수행합니다.

1. 공유에 대한 SMB 자격 증명을 확인합니다. 디바이스의 로컬 웹 UI에서 **연결 및 복사** 로 이동하고 해당 공유에 대해 **SMB** 를 선택합니다. 다음과 같은 대화 상자가 표시됩니다.

    ![Data Box의 SMB 공유에 대한 액세스 공유 및 데이터 복사 화면의 스크린샷 계정, 사용자 이름, 암호에 대한 복사 아이콘이 강조 표시됩니다.](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. 잠금 기간이 종료된 후(15분 또는 30분) 잠금이 해제됩니다. 이제 공유에 연결할 수 있습니다.

   - SMB를 통해 호스트 컴퓨터에서 공유에 연결하려면 다음 명령을 실행합니다. 관련 절차는 [SMB를 통해 Data Box에 데이터 복사](data-box-deploy-copy-data.md#connect-to-data-box)를 참조하세요.
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - 데이터 복사 서비스를 사용하여 공유에 연결하려면 아래와 같이 사용자 계정이 잠금 해제되었음을 나타내는 알림을 확인합니다. **데이터 복사** 창에서 [Data Box에 데이터를 복사](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box)할 수 있습니다.

     ![Data Box 로컬 웹 UI에서 데이터 복사 창의 스크린샷. 공유 사용자 계정이 잠금 해제되었다는 알림 및 데이터 복사 옵션이 강조 표시됩니다.](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>차단 그룹 정책 확인

클라이언트/호스트 컴퓨터의 그룹 정책이 공유에 대한 연결을 막고 있는지 확인합니다. 가능하면 클라이언트/호스트 컴퓨터를 GPO(그룹 정책 개체)가 적용되지 않은 OU(조직 구성 단위)로 이동합니다.

Data Box에서 공유에 대한 액세스를 막는 그룹 정책이 없는지 확인하려면 다음을 수행합니다.

* 클라이언트/호스트 컴퓨터가 Active Directory에 대한 자체 OU 내에 있는지 확인합니다.

* 클라이언트/호스트 컴퓨터에 적용되는 GPO가 없는지 확인합니다. 클라이언트/호스트 컴퓨터(자식 노드)가 부모로부터 GPO를 자동으로 상속하지 않도록 상속을 차단할 수 있습니다. 자세한 내용은 [블록 상속](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11))을 참조하세요.

## <a name="check-for-permissions-issues"></a>사용 권한 이슈 확인

도메인 문제가 없고 공유에 대한 액세스를 차단하는 그룹 정책이 없다면 감사 로그 및 보안 이벤트 로그를 검토하여 디바이스에서 사용 권한 문제를 확인합니다.

### <a name="review-security-event-logs"></a>보안 이벤트 로그 검토

디바이스에서 인증 실패를 나타내는 오류에 대해 Windows 보안 이벤트 로그를 검토합니다.

`etw` 폴더에서 `Smbserver.Security` 이벤트 로그를 검토하거나 이벤트 뷰어에서 보안 오류를 볼 수 있습니다.

이벤트 뷰어에서 Windows 보안 이벤트 로그를 검토하려면 다음 단계를 수행합니다.

1. Windows 이벤트 뷰어를 열려면 **시작 화면** 에서 **이벤트 뷰어** 를 입력하고 Enter 키를 누릅니다.

1. 이벤트 뷰어 탐색 창에서 **Windows 로그** 를 확장하고 **보안** 폴더를 선택합니다.

    ![보안 이벤트가 표시된 Windows 이벤트 뷰어의 스크린샷 Windows 폴더와 보안 하위 폴더가 강조 표시됩니다.](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. 다음 오류 중 하나를 찾습니다.

    오류 1:

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    오류 2:
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    두 오류는 디바이스에서 LAN 관리자 인증 수준을 변경해야 함을 나타냅니다.
 
### <a name="change-lan-manager-authentication-level"></a>LAN 관리자 인증 수준 변경
 
디바이스의 LAN 관리자 인증 수준을 변경하려면 [로컬 보안 정책을 사용](#use-local-security-policy)하거나 [직접 레지스트리를 업데이트](#update-the-registry)합니다.

#### <a name="use-local-security-policy"></a>로컬 보안 정책 사용

로컬 보안 정책을 사용하여 LAN 관리자 인증 수준을 변경하려면 다음 단계를 수행합니다.
 
1. 로컬 보안 정책을 열려면 **시작** 화면에서 `secpol.msc`를 입력한 뒤 Enter 키를 누릅니다.

1. **로컬 정책** > **보안 옵션** 으로 이동하여 **네트워크 보안: LAN 매니저 인증 수준** 을 엽니다.

    ![로컬 보안 정책 편집기의 보안 옵션을 보여 주는 스크린샷 “네트워크 보안: LAN 관리자 인증 수준” 정책이 강조 표시됩니다.](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. 설정을 **NTLMv2 응답만 보내기. LM 및 NTLM 거부** 로 변경합니다.

    ![로컬 보안 정책 편집기의 “네트워크 보안: LAN 관리자 인증 수준” 정책을 보여 주는 스크린샷 “NTLMv2 응답만 보내기. LM 및 NTLM 거부” 옵션이 강조 표시되어 있습니다.](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>레지스트리 업데이트

로컬 보안 정책에서 LAN 관리자 인증 수준을 변경할 수 없는 경우 레지스트리를 직접 업데이트합니다.

레지스트리를 직접 업데이트하려면 다음 단계를 수행합니다.

1. 레지스트리 편집기(regedit32.exe)를 열려면 **시작** 화면에서 `regedt32`를 입력한 다음, Enter 키를 누릅니다.

1. HKEY_LOCAL_MACHINE \ SYSTEM \ CurrentControlSet \ Control \ LSA로 이동합니다.

    ![LSA 폴더가 강조 표시된 레지스트리 편집기를 보여 주는 스크린샷](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. LSA 폴더에서 LMCompatibilityLevel 레지스트리 키를 열고 값을 5로 변경합니다.

    ![레지스트리의 LmcompatibilityLevel 키를 변경하는 데 사용되는 대화 상자의 스크린샷 값 데이터 필드가 강조 표시됩니다.](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. 컴퓨터를 다시 시작하여 레지스트리 변경 내용을 적용합니다.

## <a name="next-steps"></a>다음 단계

- [SMB를 통한 데이터 복사](data-box-deploy-copy-data.md).
- [Data Box의 데이터 복사 문제 해결](data-box-troubleshoot.md).
- [Microsoft 지원에 문의하세요](data-box-disk-contact-microsoft-support.md).