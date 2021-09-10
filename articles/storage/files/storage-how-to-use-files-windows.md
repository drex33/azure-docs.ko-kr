---
title: Windows에 SMB Azure 파일 공유 탑재 | Microsoft Docs
description: Windows 및 Windows Server에서 Azure 파일 공유를 사용하는 방법을 알아봅니다. 온-프레미스 또는 Azure VM에서 실행되는 Windows 설치에서 SMB 3.x와 함께 Azure 파일 공유를 사용합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8b469eb7eb94ad5454f79c4c4893597670867ac
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969511"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>Windows에 SMB Azure 파일 공유 탑재
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows 및 Windows Server에서 매끄럽게 사용할 수 있습니다. 이 문서에서는 Windows 및 Windows Server에서 Azure 파일 공유를 사용할 때의 고려 사항을 설명합니다.

온-프레미스 또는 다른 Azure 지역과 같이 호스팅되는 Azure 지역 외부의 퍼블릭 엔드포인트를 통해 Azure 파일 공유를 사용하려면 OS에서 SMB 3.x를 지원해야 합니다. SMB 2.1만 지원하는 이전 버전의 Windows는 퍼블릭 엔드포인트를 통해 Azure 파일 공유를 탑재할 수 없습니다.

| Windows 버전 | SMB 버전 | Azure Files SMB 다중 채널 | 최대 SMB 채널 암호화 |
|-|-|-|-|
| Windows Server 2022 | SMB 3.1.1 | 예 | AES-256-GCM |
| Windows 10, 버전 21H1 | SMB 3.1.1 | 예. KB5003690 이상 | AES-256-GCM |
| Windows Server 버전 20H2 | SMB 3.1.1 | 예. KB5003690 이상 | AES-128-GCM |
| Windows 10 버전 20H2 | SMB 3.1.1 | 예. KB5003690 이상 | AES-128-GCM |
| Windows Server, 버전 2004 | SMB 3.1.1 | 예. KB5003690 이상 | AES-128-GCM |
| Windows 10, 버전 2004 | SMB 3.1.1 | 예. KB5003690 이상 | AES-128-GCM |
| Windows Server 2019 | SMB 3.1.1 | 예. KB5003703 이상 | AES-128-GCM |
| Windows 10, 버전 1809 | SMB 3.1.1 | 예. KB5003703 이상 | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | 예. KB5004238 이상 | AES-128-GCM |
| Windows 10 버전 1607 | SMB 3.1.1 | 예. KB5004238 이상 | AES-128-GCM |
| Windows 10 버전 1507 | SMB 3.1.1 | 예, KB5004249 이상 | AES-128-GCM |
| Windows Server 2012 R2 | SMB 3.0 | 예 | AES-128-CCM |
| Windows 8.1 | SMB 3.0 | 예 | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | 예 | AES-128-CCM |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | No | 지원되지 않음 |
| Windows 7<sup>1</sup> | SMB 2.1 | No | 지원되지 않음 |

<sup>1</sup>Windows 7 및 Windows Server 2008 R2에 대한 정식 Microsoft 지원이 종료되었습니다. [ESU(연장 보안 업데이트) 프로그램](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)을 통해서만 보안 업데이트에 대한 추가 지원을 구매할 수 있습니다. 이와 같은 운영 체제에서 마이그레이션하는 것이 좋습니다.

> [!Note]  
> 사용자의 Windows 버전에 대해 가장 최근의 KB를 선택하는 것이 좋습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>사전 요구 사항 
445 포트가 열려 있는지 확인: SMB 프로토콜은 TCP 포트 445가 열려 있어야 하며, 445 포트가 닫혀 있으면 연결이 실패합니다. `Test-NetConnection` cmdlet을 사용하여 방화벽이 포트 445를 차단하는지 확인할 수 있습니다. 차단된 445 포트를 해결하는 방법에 대한 자세한 내용은 Windows 문제 해결 가이드의 [원인 1: 포트 445 차단](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) 섹션을 참조하세요.

## <a name="using-an-azure-file-share-with-windows"></a>Windows에서 Azure 파일 공유 사용
Windows에서 Azure 파일 공유를 사용하려면 Azure 파일 공유를 탑재하거나(드라이브 문자 또는 탑재 지점 경로에 할당한다는 의미) [UNC 경로](/windows/win32/fileio/naming-a-file)를 통해 액세스해야 합니다. 

이 문서에서는 스토리지 계정 키를 사용하여 파일 공유에 액세스합니다. 스토리지 계정 키는 액세스하는 파일 공유 내 모든 파일 및 폴더에 대한 관리자 권한이 포함된 스토리지 계정의 관리 키이며, 스토리지 계정에 포함된 모든 파일 공유 및 다른 스토리지 리소스(Blob, 큐, 테이블 등)의 관리 키입니다. 스토리지 계정 키가 워크로드에 충분하지 않은 경우 [Azure 파일 동기화](../file-sync/file-sync-planning.md)를 사용하거나 [SMB에 대한 ID 기반 인증](storage-files-active-directory-overview.md)을 사용할 수 있습니다.

SMB 파일 공유를 기대하는 LOB 애플리케이션을 Azure로 전환하는 일반적인 패턴은 Azure VM에서 전용 Windows 파일 서버를 실행하는 대신 Azure 파일 공유를 사용하는 것입니다. Azure 파일 공유를 사용하도록 기간 업무 앱을 마이그레이션할 때 고려해야 하는 중요한 사항 중 하나로, 많은 기간 업무 앱은 VM 관리 계정이 아니라 시스템 권한이 제한된 전용 서비스 계정 하에서 실행됩니다. 따라서 관리 계정이 아닌 서비스 계정의 Azure 파일 공유에 대한 자격 증명을 탑재/저장해야 합니다.

### <a name="mount-the-azure-file-share"></a>Azure 파일 공유 탑재

Azure Portal은 파일 공유를 호스트에 직접 탑재하는 데 사용할 수 있는 스크립트를 제공합니다. 이 제공된 스크립트를 사용하는 것이 좋습니다.

이 스크립트를 가져오려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 탑재할 파일 공유가 포함된 스토리지 계정으로 이동합니다.
1. **파일 공유** 를 선택합니다.
1. 탑재할 파일 공유를 선택합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="파일 공유가 강조 표시된 파일 공유 블레이드의 스크린샷":::

1. **연결** 을 선택합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="파일 공유의 연결 아이콘의 스크린샷":::

1. 공유를 탑재할 드라이브 문자를 선택합니다.
1. 제공된 스크립트를 복사합니다.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="스크립트의 복사 버튼이 강조 표시된 연결 블레이드의 스크린샷":::

1. 파일 공유를 탑재할 호스트의 셸에 스크립트를 붙여넣고 실행합니다.

이제 Azure 파일 공유를 탑재했습니다.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>파일 탐색기를 통해 Azure 파일 공유 탑재
> [!Note]  
> 다음 지침은 Windows 10에서 보여 주는 것이며, 이전 릴리스와 다를 수 있습니다. 

1. 파일 탐색기를 엽니다. [시작] 메뉴에서 열거나 Win+E 바로 가기 키를 눌러서 열 수 있습니다.

1. 창 왼쪽에 있는 **이 PC** 로 이동합니다. 이렇게 하면 리본에서 사용할 수 있는 메뉴가 변경됩니다. [컴퓨터] 메뉴 아래에서 **네트워크 드라이브 연결** 을 선택합니다.
    
    !["네트워크 드라이브 연결" 드롭다운 메뉴의 스크린샷](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 드라이브 문자를 선택하고 UNC 경로를 입력합니다. UNC 경로 형식은 `\\<storageAccountName>.file.core.windows.net\<fileShareName>`입니다. 예: `\\anexampleaccountname.file.core.windows.net\example-share-name`
    
    !["네트워크 드라이브 연결" 대화 상자의 스크린샷](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 사용자 이름으로 `AZURE\`이(가) 앞에 붙은 Storage 계정 이름을 사용하고, 암호로 Storage 계정 키를 사용합니다.
    
    ![네트워크 자격 증명 대화 상자의 스크린샷](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Azure 파일 공유를 원하는 대로 사용합니다.
    
    ![현재 탑재된 Azure 파일 공유](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Azure 파일 공유를 분리할 준비가 되면 파일 탐색기의 **네트워크 위치** 아래에서 공유 항목을 마우스 오른쪽 단추로 클릭하고 **연결 해제** 를 선택하여 Azure 파일 공유를 탑재 해제할 수 있습니다.

### <a name="accessing-share-snapshots-from-windows"></a>Windows에서 공유 스냅샷에 액세스
Azure Backup 같은 스크립트 또는 서비스를 통해 수동으로 또는 자동으로 공유 스냅샷을 만든 경우 Windows의 파일 공유에서 이전 버전의 공유, 디렉터리 또는 특정 파일을 볼 수 있습니다. [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md) 또는 [Azure Portal](storage-how-to-use-files-portal.md)을 사용하여 공유 스냅샷을 만들 수 있습니다.

#### <a name="list-previous-versions"></a>이전 버전 나열
복원해야 하는 항목 또는 부모 항목을 찾습니다. 해당 항목을 두 번 클릭하여 원하는 디렉터리로 이동합니다. 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성** 을 선택합니다.

![선택한 디렉터리를 마우스 오른쪽 단추로 클릭하여 표시된 메뉴](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

**이전 버전** 을 선택하여 이 디렉터리에 대한 공유 스냅샷의 목록을 봅니다. 네트워크 속도 및 디렉터리의 공유 스냅샷 수에 따라 목록이 표시되는 데 수 초 정도 걸릴 수 있습니다.

![이전 버전 탭](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

**열기** 를 선택하여 특정 스냅샷을 열 수 있습니다. 

![열린 스냅샷](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>이전 버전에서 복원
**복원** 을 선택하여 공유 스냅샷을 만들 때의 전체 디렉터리의 내용을 원래 위치에 재귀적으로 복사합니다.

 ![경고 메시지의 복원 단추](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [FAQ](./storage-files-faq.md)
- [Windows에서 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)