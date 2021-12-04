---
title: 기존 Azure NetApp Files SMB 볼륨에서 지속적인 가용성 사용 | Microsoft Docs
description: 기존 Azure NetApp Files SMB 볼륨에서 SMB 지속적인 가용성을 사용하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/02/2021
ms.author: b-hchen
ms.openlocfilehash: 5bdf60a10a2775febd6e6ef4930be14bf9ba36c7
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545467"
---
# <a name="enable-continuous-availability-on-existing-smb-volumes"></a>기존 SMB 볼륨에서 지속적인 가용성 사용

[새 SMB 볼륨을 만들](azure-netapp-files-create-volumes-smb.md#continuous-availability) 때 SMB CA(지속적인 가용성) 기능을 사용하도록 설정할 수 있습니다. 기존 SMB 볼륨에서 SMB CA를 사용할 수도 있습니다. 이 문서에서는 이 작업을 수행하는 방법을 보여 줍니다.

> [!IMPORTANT]   
> SMB 지속적인 가용성 기능은 현재 공개 미리 보기로 제공됩니다. **[Azure NetApp Files SMB 지속적인 가용성 공유 공개 미리 보기 대기 목록 제출 페이지](https://aka.ms/anfsmbcasharespreviewsignup)** 를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. 지속적인 가용성 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 메일을 기다리세요.   
> 
> 추가 세부 정보 및 고려 사항은 [**연속 가용성 사용**](azure-netapp-files-create-volumes-smb.md#continuous-availability) 옵션을 참조하세요. 

## <a name="considerations"></a>고려 사항

* [**스냅샷 경로 숨기기**](snapshots-edit-hide-path.md) 옵션은 현재 CA 사용 SMB 볼륨에는 적용되지 않습니다.  

* 다른 SMB 볼륨에서 트래버스하는 데 사용할 수 있는 `~snapshot` 디렉터리는 CA 사용 SMB 볼륨에 대해 표시되지 않습니다. 여전히 수동으로 `~snapshot\<snapshotName>`을 입력하여 스냅샷에 액세스할 수 있습니다.

## <a name="steps"></a>단계

1. [SMB 지속적인 가용성 공유](https://aka.ms/anfsmbcasharespreviewsignup) 기능을 등록했는지 확인합니다.  

    SQL Server 및 [FSLogix 사용자 프로필 컨테이너](../virtual-desktop/create-fslogix-profile-container.md)에 대해서만 지속적인 가용성을 사용하도록 설정해야 합니다. SQL Server 및 FSLogix 사용자 프로필 컨테이너 이외의 워크로드에는 SMB 지속적인 가용성 공유를 사용할 수 *없습니다*. 이 기능은 현재 Windows SQL Server에서 지원됩니다. Linux SQL Server는 현재 지원되지 않습니다. 비관리자(도메인) 계정을 사용하여 SQL Server를 설치하는 경우 계정에 필요한 보안 권한이 할당되어 있어야 합니다. 도메인 계정에 필요한 보안 권한(`SeSecurityPrivilege`)이 없고 도메인 수준에서 권한을 설정할 수 없는 경우에는 Active Directory 연결의 **보안 권한 사용자** 필드를 사용하여 계정에 권한을 부여하면 됩니다. [Active Directory 연결 만들기](create-active-directory-connections.md#create-an-active-directory-connection)를 참조하세요.
            
3. SMB CA를 사용할 SMB 볼륨을 클릭합니다. **편집** 을 클릭합니다.  
4. 나타나는 편집 창에서 **지속적인 가용성 사용** 확인란을 선택합니다.   
    ![지속적인 가용성 사용 옵션을 보여주는 스냅샷.](../media/azure-netapp-files/enable-continuous-availability.png)

4. 기존 SMB 공유에 연결하는 Windows 시스템을 다시 부팅합니다.   

    > [!NOTE]
    > **지속적인 가용성 사용** 옵션만 선택해도 기존 SMB 세션을 계속 사용할 수 있는 것은 아닙니다. 옵션을 선택한 후에는 서버를 다시 부팅하여 변경 내용을 적용해야 합니다.  

5. 다음 명령을 사용하여 CA가 활성화되어 있고 볼륨을 탑재하는 시스템에서 사용되는지 확인합니다.

    ```powershell-interactive
    get-smbconnection | select -Property servername,ContinuouslyAvailable
    ```
 
    최신 PowerShell 버전을 설치해야 할 수 있습니다. 

    서버 이름을 알고 있는 경우 `-ServerName` 매개 변수를 명령과 함께 사용할 수 있습니다. [Get-SmbConnection](/powershell/module/smbshare/get-smbconnection?view=windowsserver2019-ps&preserve-view=true) PowerShell 명령 세부 정보를 참조하세요.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
