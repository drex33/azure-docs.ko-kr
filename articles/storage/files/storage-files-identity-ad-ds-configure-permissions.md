---
title: 사용자가 파일 수준에서 수행할 수 있는 작업 제어 - Azure 파일 공유
description: Azure 파일 공유에 대한 온-프레미스 AD DS 인증에 맞게 Windows ACL 권한을 구성하는 방법을 알아봅니다. 이를 통해 세부적인 액세스 제어를 활용할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: efa2ec8873374604e252677e436e6883867f982a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255497"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>3부: SMB를 통한 디렉터리 및 파일 수준 권한 구성 

이 문서를 시작하기 전에 이전 문서의 [ID에 공유 수준 권한 할당](storage-files-identity-ad-ds-assign-permissions.md)을 완료하여 공유 수준 권한을 제대로 제공해야 합니다.

Azure RBAC를 사용하여 공유 수준 권한을 할당한 후에는 세분화된 액세스 제어를 활용하기 위해 루트, 디렉터리 또는 파일 수준에서 적절한 Windows ACL을 구성해야 합니다. Azure RBAC 공유 수준 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정하는 상위 수준의 게이트키퍼로 생각하면 됩니다. Windows ACL은 보다 세부적인 수준에서 작동하여 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정합니다. 사용자가 파일/디렉터리에 액세스하려고 할 때 공유 수준과 파일/디렉터리 수준 권한이 모두 적용되므로 둘 중 하나에 차이가 있는 경우 가장 제한이 많은 권한만 적용됩니다. 예를 들어 사용자가 파일 수준에서는 읽기/쓰기 액세스 권한을 가지고 있고 공유 수준에서는 읽기 액세스 권한만 가지고 있으면 해당 파일을 읽을 수만 있습니다. 반대의 경우에도 마찬가지로, 사용자가 공유 수준에서는 읽기/쓰기 액세스 권한을 가지고 있고 파일 수준에서는 읽기 액세스 권한만 가지고 있으면 파일을 읽을 수만 있습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="azure-rbac-permissions"></a>Azure RBAC 권한

다음 표에는 이 구성과 관련된 Azure RBAC 권한이 포함되어 있습니다.


| 기본 제공 역할  | NTFS 권한  | 결과 액세스  |
|---------|---------|---------|
|스토리지 파일 데이터 SMB 공유 Reader | 모든 권한, 수정, 읽기, 쓰기, 실행 | 읽기 & 실행  |
|     |   읽기 |     읽기  |
|스토리지 파일 데이터 SMB 공유 Contributor  |  모든 권한    |  수정, 읽기, 쓰기, 실행 |
|     |  수정         |  수정    |
|     |  읽기 & 실행 |  읽기 & 실행 |
|     |  읽기           |  읽기    |
|     |  쓰기          |  쓰기   |
|스토리지 파일 데이터 SMB 공유 관리자 권한 Contributor | 모든 권한  |  수정, 읽기, 쓰기, 편집(사용 권한 변경), 실행 |
|     |  수정          |  수정 |
|     |  읽기 & 실행  |  읽기 & 실행 |
|     |  읽기            |  읽기   |
|     |  쓰기           |  쓰기  |



## <a name="supported-permissions"></a>지원되는 권한

Azure Files는 기본 및 고급 Windows ACL 전체를 지원합니다. 공유를 탑재한 다음 Windows 파일 탐색기를 사용하거나 Windows [icacls](/windows-server/administration/windows-commands/icacls) 명령 또는 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 명령을 실행하여 Azure 파일 공유의 디렉터리와 파일에 대한 Windows ACL 권한을 확인하고 구성할 수 있습니다. 

슈퍼 사용자 권한으로 ACL을 구성하려면 도메인 조인 VM에서 스토리지 계정 키를 사용하여 공유를 탑재해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재하고 Windows ACL을 적절하게 구성하세요.

파일 공유의 루트 디렉터리에 포함되는 권한은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|사용자|정의|
|---|---|
|BUILTIN\Administrators|온-프레미스 AD DS 환경의 도메인 관리자인 모든 사용자입니다.
|BUILTIN\Users|AD의 기본 제공 보안 그룹입니다. 여기에는 기본적으로 NT AUTHORITY\Authenticated Users가 포함됩니다. 기존 파일 서버의 경우 서버당 멤버 자격 정의를 구성할 수 있습니다. Azure Files에는 호스팅 서버가 없으므로 BUILTIN\Users에는 NT AUTHORITY\Authenticated Users와 동일한 사용자 집합이 포함됩니다.|
|NT AUTHORITY\SYSTEM|파일 서버의 운영 체제에 대한 서비스 계정입니다. 이러한 서비스 계정은 Azure Files 컨텍스트에서 적용되지 않습니다. 이는 하이브리드 시나리오에 대한 Windows 파일 서버 환경과 일치하도록 루트 디렉터리에 포함되어 있습니다.|
|NT AUTHORITY\Authenticated Users|유효한 Kerberos 토큰을 가져올 수 있는 모든 AD 사용자입니다.|
|CREATOR OWNER|디렉터리 또는 파일 개체마다 해당 개체의 소유자가 있습니다. 해당 개체의 "작성자 소유자"에 할당된 ACL이 있는 경우 이 개체의 소유자인 사용자에게 ACL에서 정의한 개체에 대한 권한이 있습니다.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows `net use` 명령을 사용하여 Azure 파일 공유를 탑재합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Windows에서 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)을 참조하세요. 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Azure Files에 연결하는 데 문제가 발생하는 경우 [Windows에서 발생하는 Azure Files 탑재 오류를 위해 게시된 문제 해결 도구](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)를 참조하세요. 또한 포트 445가 차단되는 시나리오를 해결하기 위한 [지침](./storage-files-faq.md#on-premises-access)을 제공합니다. 

## <a name="configure-windows-acls"></a>Windows ACL 구성

스토리지 계정 키를 사용하여 파일 공유를 탑재한 후에는 Windows ACL(NTFS 권한이라고도 함)을 구성해야 합니다. Windows 파일 탐색기 또는 icacls 중 하나를 사용하여 Windows ACL을 구성할 수 있습니다.

AD DS ID에 따라 Windows DACL이 구성된 온-프레미스 파일 서버에 디렉터리 또는 파일이 있는 경우 Robocopy 또는 [Azure AzCopy v 10.4 이상](https://github.com/Azure/azure-storage-azcopy/releases)과 같은 기존의 파일 복사 도구를 사용하여 ACL을 유지하는 Azure Files로 복사할 수 있습니다. 디렉터리와 파일이 Azure 파일 동기화를 통해 Azure Files로 계층화되는 경우 ACL은 해당 원시 형식으로 전달되고 유지됩니다.

### <a name="configure-windows-acls-with-icacls"></a>icacls로 Windows ACL 구성

루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값은 실제 값으로 바꾸세요.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls를 사용하여 Windows ACL을 설정하는 방법과 지원되는 여러 권한 유형에 대한 자세한 내용은 [icacls용 명령줄 참조](/windows-server/administration/windows-commands/icacls)를 참조하세요.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용하여 Windows ACL 구성

루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 Windows 파일 탐색기를 사용합니다. Windows 파일 탐색기에서 AD 도메인 정보를 올바르게 로드할 수 없는 경우 온-프레미스 AD 환경의 신뢰 구성 때문일 수 있습니다. 클라이언트 머신이 Azure Files 인증을 위해 등록된 AD 도메인 컨트롤러에 연결할 수 없었습니다. 이 경우에는 Windows ACL을 구성할 때 icacls를 사용합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리를 마우스 오른쪽 단추로 클릭한 다음 **속성** 을 선택합니다.
1. **보안** 탭을 선택합니다.
1. **편집** 을 선택하여 권한을 변경합니다.
1. 기존 사용자의 권한을 변경하거나 **추가...** 를 선택하여 새 사용자에게 권한을 부여할 수 있습니다.
1. 새 사용자 추가 프롬프트 창에서 **선택할 개체 이름 입력** 상자에 권한을 부여하려는 대상 사용자 이름을 입력하고 **이름 확인** 을 선택하여 대상 사용자의 전체 UPN 이름을 찾습니다.
1.    **확인** 을 선택합니다.
1.    **보안** 탭에서 새 사용자에게 부여할 권한을 모두 선택합니다.
1.    **적용** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

기능을 사용하도록 설정하고 구성했으므로 이제 도메인 조인 VM에서 Azure 파일 공유를 탑재하는 다음 문서로 계속 진행합니다.

[4부: 도메인 조인 VM에서 파일 공유 탑재](storage-files-identity-ad-ds-mount-file-share.md)
