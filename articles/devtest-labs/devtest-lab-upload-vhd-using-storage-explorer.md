---
title: Storage Explorer를 사용 하 여 VHD 파일 업로드
description: Microsoft Azure Storage Explorer를 사용 하 여 DevTest Labs 랩 저장소 계정에 VHD 파일을 업로드 합니다.
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 2531964c056ddbed38da435e16bde3e0f5e1eff9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054115"
---
# <a name="upload-a-vhd-file-to-a-labs-storage-account-by-using-storage-explorer"></a>Storage Explorer를 사용 하 여 랩의 저장소 계정에 VHD 파일 업로드

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs에서 VHD 파일을 사용 하 여 가상 컴퓨터를 프로 비전 하기 위한 사용자 지정 이미지를 만들 수 있습니다. 이 문서에서는 [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) 를 사용 하 여 랩의 저장소 계정에 VHD 파일을 업로드 하는 방법을 설명 합니다. DevTest Labs에 VHD 파일을 업로드 하면 업로드 된 파일에서 [사용자 지정 이미지를 만들](devtest-lab-create-custom-image-from-vhd-using-powershell.md) 수 있습니다. Azure의 디스크 및 Vhd에 대 한 자세한 내용은 [managed Disks 소개](../virtual-machines/managed-disks-overview.md)를 참조 하세요.

Storage Explorer는 여러 가지 연결 옵션을 지원합니다. 이 문서에서는 Azure 구독과 연결 된 저장소 계정에 연결 하는 방법을 설명 합니다. 다른 Storage Explorer 연결 옵션에 대 한 자세한 내용은 [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)하기를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

- [최신 버전의 Microsoft Azure Storage Explorer를 다운로드 하 여 설치](https://www.storageexplorer.com)합니다.

- Azure Portal를 사용 하 여 랩의 저장소 계정 이름을 가져옵니다.

  1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 **DevTest Labs** 를 검색 하 고 선택 하 고 목록에서 랩을 선택 합니다.
  1. 랩 페이지의 왼쪽 탐색 영역에서 **구성 및 정책** 을 선택 합니다. 
  1. **구성 및 정책** 페이지의 **가상 컴퓨터 기반** 에서 **사용자 지정 이미지** 를 선택 합니다.
  1. **사용자 지정 이미지** 페이지에서 **추가** 를 선택 합니다. 
  1. **사용자 지정 이미지** 페이지의 **vhd** 에서 **PowerShell을 사용 하 여 vhd 업로드** 링크를 선택 합니다.
     ![PowerShell 링크를 사용 하 여 VHD 업로드를 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png)
  1. PowerShell을 **사용 하 여 이미지 업로드** **추가-azurevhd** cmdlet에 대 한 호출에서 `Destination` 매개 변수는 랩의 저장소 계정 이름을 다음과 같은 형식으로 표시 합니다. `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/` .
  1. 다음 단계에서 사용할 저장소 계정 이름을 복사 합니다.

## <a name="step-by-step-instructions"></a>단계별 지침

1. Storage Explorer 열면 왼쪽 **탐색기** 창에 로그인 한 모든 Azure 구독이 표시 됩니다.

   다른 계정을 추가 해야 하는 경우 **계정 관리** 아이콘을 선택 하 고 계정 **관리** 창에서 **계정 추가** 를 선택 합니다.

   ![계정 관리 창에 계정을 추가 하는 방법을 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png)

   프롬프트에 따라 Azure 구독과 연결 된 Microsoft 계정 로그인 합니다.

1. 로그인 하면 사용자 계정과 연결 된 Azure 구독이 **탐색기** 창에 표시 됩니다. 사용 하려는 Azure 구독 옆에 있는 드롭다운 화살표를 선택 합니다. 왼쪽 창에는 선택한 Azure 구독과 연결 된 저장소 계정이 표시 됩니다.

   ![선택한 Azure 구독에 대 한 저장소 계정을 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png)

1. 이전에 저장 한 랩 저장소 계정 이름 옆에 있는 드롭다운 화살표를 선택 합니다.

1. **Blob 컨테이너** 노드를 확장 한 다음 **업로드** 를 선택 합니다.

   ![업로드 디렉터리를 사용 하 여 확장 된 Blob 컨테이너 노드를 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png)

1. Storage Explorer 오른쪽 창의 blob 편집기 도구 모음에서 **업로드** 를 선택 하 고 **파일 업로드** 을 선택 합니다. 

   ![업로드 단추와 업로드 파일을 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png)

1. **업로드 파일** 대화 상자에서 **선택한 파일** 필드 **옆에** 있는 ...를 선택 하 고 컴퓨터의 VHD 파일을 찾아 선택한 다음 **열기** 를 선택 합니다.

1. **Blob 유형** 에서 **블록 Blob** 을 **페이지 Blob** 로 변경 합니다.

1. **업로드** 를 선택합니다.

   ![파일 업로드 대화 상자를 보여주는 스크린샷.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png)

아래쪽의 **작업** 창에는 업로드 상태가 표시 됩니다. Vhd 파일을 업로드 하는 데는 VHD 파일 크기 및 연결 속도에 따라 시간이 오래 걸릴 수 있습니다.

![업로드 상태의 작업 창을 보여 주는 스크린샷](media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-template.md)
- [PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

