---
title: Windows VM에서 Azure 파일 공유 만들기 및 사용
description: Azure Portal에서 Azure Files 공유를 만들고 사용합니다. Windows VM에 연결하여 파일 공유에 연결하고, 파일 공유에 파일을 업로드합니다.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 07/27/2021
ms.author: rogarana
ms.subservice: files
ms.custom: mode-other
ms.openlocfilehash: 1bfdbb6d707af42e4080e8ddc6e4c973ea485a6b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063659"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-windows-virtual-machines"></a>빠른 시작: Windows 가상 머신에서 Azure Files 공유 만들기 및 관리

이 문서에서는 Azure Files 공유를 만들고 사용하기 위한 기본 단계를 설명합니다. 이 빠른 시작에서는 서비스의 작동 원리를 확인할 수 있도록 Azure Files 공유를 신속하게 설정하는 것에 중점을 둡니다. 고유한 환경에서 Azure 파일 공유를 만들고 사용하기 위한 자세한 지침이 필요한 경우 [Windows로 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="prepare-your-environment"></a>환경 준비

이 빠른 시작에서는 다음 항목을 설정합니다.

- Azure 스토리지 계정 및 파일 공유
- Windows Server 2016 Datacenter VM

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure 파일 공유를 사용하려면 먼저 Azure 스토리지 계정을 만들어야 합니다. 범용 v2 스토리지 계정은 모든 Azure Storage 서비스(BLOB, 파일, 큐, 테이블)에 대한 액세스를 제공합니다. 빠른 시작에서는 범용 v2 스토리지 계정을 만들지만 모든 유형의 스토리지 계정을 만드는 단계는 비슷합니다. 스토리지 계정에 포함될 수 있는 공유 수에는 제한이 없습니다. 공유에 저장할 수 있는 파일 수에는 제한이 없으며, 스토리지 계정의 최대 용량까지 저장할 수 있습니다.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

이제 파일 공유를 만듭니다.

1. Azure 스토리지 계정 배포가 완료되면 **리소스로 이동** 을 선택합니다.
1. 스토리지 계정 창에서 **파일 공유** 를 선택합니다.

    ![파일 공유를 선택합니다.](./media/storage-files-quick-create-use-windows/click-files.png)

1. **+ 파일 공유** 를 선택합니다.

    ![새 파일 공유를 만들려면 + 파일 공유를 선택합니다.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. 새 파일 공유에 *qsfileshare* 라는 이름을 지정하고, **할당량** 에 "1"을 입력하고, **트랜잭션 최적화** 를 선택한 상태로 두고, **만들기** 를 선택합니다. 할당량은 최대 5TiB(대용량 파일 공유가 설정된 경우 100TiB)까지 가능하지만 이 자습서에서는 1GiB만 필요합니다.
1. 로컬 머신에 *qsTestFile* 이라는 새 txt 파일을 만듭니다.
1. 새 파일 공유를 선택한 다음, 파일 공유 위치에서 **업로드** 를 선택합니다.

    ![파일을 업로드합니다.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. .txt 파일을 만든 위치로 이동하고 > *qsTestFile.txt* 를 선택하고 > **업로드** 를 선택합니다.

지금까지 Azure에서 Azure 스토리지 계정과, 파일이 하나 있는 파일 공유를 만들었습니다. 이제 이 빠른 시작에서 온-프레미스 서버를 나타내는 Windows Server 2016 Datacenter가 있는 Azure VM을 만들겠습니다.

### <a name="deploy-a-vm"></a>VM 배포

1. 이제 포털 왼쪽의 메뉴를 확장하고 Azure Portal 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택합니다.
1. **인기 있는 서비스** 에서 **가상 머신** 을 선택합니다.
1. **기본** 탭의 **프로젝트 세부 정보** 에서 이 빠른 시작용으로 만든 리소스 그룹을 선택합니다.

   ![포털 블레이드에서 VM에 대한 기본 정보를 입력합니다.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. **인스턴스 세부 정보** 에서 VM 이름을 *qsVM* 으로 지정합니다.
1. **이미지** 에 대해 **Windows Server 2016 Datacenter - Gen2** 를 선택합니다.
1. **지역**, **가용성 옵션** 및 **크기** 는 기본 설정을 그대로 둡니다.
1. **관리자 계정** 에서 **사용자 이름** 을 추가하고 VM에 사용할 **암호** 를 입력합니다.
1. **인바운드 포트 규칙** 에서 **선택한 포트 허용** 을 선택한 다음, 드롭다운에서 **RDP(3389)** 및 **HTTP** 를 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다. 새 VM을 만드는 데 몇 분 정도 걸릴 수 있습니다.

1. VM 배포가 완료되면 **리소스로 이동** 을 선택합니다.

지금까지 새 가상 머신을 만들었고 데이터 디스크를 연결했습니다. 이제 VM에 연결해야 합니다.

### <a name="connect-to-your-vm"></a>VM에 연결

1. 가상 머신 속성 페이지에서 **연결** 을 선택합니다.

   ![포털에서 Azure VM에 연결](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. **가상 머신에 연결** 페이지에서 **포트 번호** *3389* 를 통해 **IP 주소** 로 연결하는 기본 옵션을 유지하고 **RDP 파일 다운로드** 를 선택합니다.
1. 다운로드한 RDP 파일을 열고 메시지가 표시되면 **연결** 을 선택합니다.
1. **Windows 보안** 창에서 **추가 선택 사항** 및 **다른 계정 사용** 을 차례로 선택합니다. 사용자 이름으로 *localhost\username* 을 입력합니다. 여기서 &lt;username&gt;은 가상 머신에 대해 만든 VM 관리자 사용자 이름입니다. 가상 머신에 대해 만든 암호를 입력한 다음, **확인** 을 선택합니다.

   ![다른 옵션 선택](./media/storage-files-quick-create-use-windows/local-host2.png)

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속** 을 선택하여 연결을 만듭니다.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Azure 파일 공유를 Windows 드라이브로 매핑

1. Azure Portal에서 *qsfileshare* fileshare로 이동하고 **연결** 을 선택합니다.
1. 드라이브 문자를 선택한 다음, 두 번째 상자의 내용을 복사하여 **메모장** 에 붙여넣습니다.

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="복사하여 메모장에 붙여넣어야 하는 상자의 내용을 보여주는 스크린샷." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. VM에서 **PowerShell** 을 열고 **메모장** 의 내용을 붙여넣은 다음, Enter 키를 눌러 명령을 실행합니다. 드라이브를 매핑해야 합니다.

## <a name="create-a-share-snapshot"></a>공유 스냅샷 만들기

이제 드라이브를 매핑했으므로 스냅샷을 만들 수 있습니다.

1. 포털에서 파일 공유로 이동하고 **스냅샷** 을 선택한 후 **+ 스냅샷 추가** 를 선택합니다.

   ![작업 섹션에서 스냅샷을 선택한 후 스냅샷 추가를 선택합니다.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. VM에서 *qstestfile.txt* 를 열고 “이 파일이 수정되었습니다”를 입력하고 > 파일을 저장하고 닫습니다.
1. 다른 스냅샷을 만듭니다.

## <a name="browse-a-share-snapshot"></a>공유 스냅샷 찾아보기

1. 파일 공유에서 **스냅샷** 을 선택합니다.
1. **스냅샷** 블레이드에서 목록의 첫 번째 스냅샷을 선택합니다.

   ![타임스탬프의 목록에서 선택한 스냅샷](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. 해당 스냅샷을 열고 *qsTestFile.txt* 를 선택합니다.

## <a name="restore-from-a-snapshot"></a>스냅샷에서 복원

1. 파일 공유 스냅샷 블레이드에서 *qsTestFile* 을 마우스 오른쪽 단추로 클릭하고 **복원** 단추를 선택합니다.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="qstestfile이 선택되어 있고 복원이 강조 표시된 스냅샷 블레이드의 스크린샷.":::

1. **원본 파일 덮어쓰기** 를 선택합니다.

   ![원본 파일 덮어쓰기가 선택된 복원 팝업의 스크린샷.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. VM에서 파일을 엽니다. 수정되지 않은 버전이 복원되었습니다.

## <a name="delete-a-share-snapshot"></a>공유 스냅샷 삭제

1. 파일 공유에서 **스냅샷** 을 선택합니다.
1. **스냅샷** 블레이드에서 목록의 마지막 스냅샷을 선택하고 **삭제** 를 선택합니다.

   ![마지막 스냅샷이 선택되어 있고, 삭제 단추가 강조 표시된 스냅샷 블레이드의 스크린샷](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Windows에서 공유 스냅샷 사용

온-프레미스 VSS 스냅샷과 마찬가지로 이전 버전 탭을 사용하여 탑재된 Azure 파일 공유의 스냅샷을 볼 수 있습니다.

1. 파일 탐색기에서 탑재된 공유를 찾습니다.

   ![파일 탐색기의 탑재된 공유](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. *qsTestFile.txt* 를 선택하고 > 마우스 오른쪽 단추를 클릭하고 메뉴에서 **속성** 을 선택합니다.

   ![선택한 디렉터리를 마우스 오른쪽 단추로 클릭하여 표시된 메뉴](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. **이전 버전** 을 선택하여 이 디렉터리에 대한 공유 스냅샷의 목록을 봅니다.

1. **열기** 를 선택하여 스냅샷을 엽니다.

   ![이전 버전 탭](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>이전 버전에서 복원

1. **복원** 을 선택합니다. 그러면 공유 스냅샷을 만들 때 전체 디렉터리의 콘텐츠가 원래 위치에 재귀적으로 복사됩니다.

   ![경고 메시지의 복원 단추](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > 파일이 변경되지 않은 경우 해당 파일이 스냅샷과 동일한 버전이므로 해당 파일의 이전 버전이 표시되지 않습니다. 이는 Windows 파일 서버에서 작동하는 방식과 일치합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Windows에서 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)
