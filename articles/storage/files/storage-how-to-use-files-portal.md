---
title: Azure Portal을 사용하여 Azure 파일 공유를 관리하기 위한 빠른 시작
description: Azure Portal에서 Azure 파일 공유 만들고 관리하는 방법을 참조하세요. 스토리지 계정을 만들고, Azure 파일 공유를 만들고, Azure 파일 공유를 사용합니다.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3f470bea0a5a0e53ba10910ac2dd116a9ffea13e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117816"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure 파일 공유 만들기 및 관리 
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows, Linux 및 macOS에 탑재할 수 있습니다. 이 가이드에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure 파일 공유 작업의 기본 사항을 안내합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure 파일 공유 만들려면:

1. 대시보드에서 스토리지 계정을 선택합니다.
1. 스토리지 계정 페이지의 **서비스** 섹션에서 **파일** 을 선택합니다.
    
    ![스토리지 계정의 데이터 스토리지 섹션에 대한 스크린샷: 파일 공유를 선택합니다.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. **파일 서비스** 페이지 위쪽의 메뉴에서 **파일 공유** 를 클릭합니다. **새 파일 공유** 페이지가 드롭다운 방식으로 펼쳐집니다.
1. **이름** 에 *myshare* 를 입력하고, 할당량을 입력하고, **계층** 에 대해 **최적화된 트랜잭션** 을 선택한 상태로 둡니다.
1. **만들기** 를 선택하여 Azure 파일 공유를 만듭니다.

공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

## <a name="use-your-azure-file-share"></a>Azure 파일 공유 사용
Azure Files는 Azure 파일 공유 내에서 파일 및 폴더를 사용하는 세 가지 방법 즉, [SMB(서버 메시지 블록) 프로토콜](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview), NFS(네트워크 파일 시스템) 프로토콜(미리 보기) 및 [File REST 프로토콜](/rest/api/storageservices/file-service-rest-api)을 제공합니다. 

SMB를 사용하여 파일 공유를 탑재하려면 OS에 따라 다음 문서를 참조하세요.
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure Portal에서 Azure 파일 공유 사용
Azure Portal을 통한 모든 요청은 파일 REST API를 통해 수행되므로 SMB에 액세스하지 않고도 클라이언트에서 파일과 디렉터리를 만들고, 수정하고, 삭제할 수 있습니다. File REST 프로토콜을 작업하는 것이 가능하지만(즉, REST HTTP 호출 직접 생성), 가장 일반적인 방법(Azure Portal 사용 이외)은 [Azure PowerShell 모듈](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md) 또는 Azure Storage SDK를 사용하는 것입니다. 이들 모두 선택한 스크립팅/프로그래밍 언어에서 File REST 프로토콜에 좋은 래퍼를 제공합니다. 

대부분의 Azure Files 사용자는 사용할 수 있을 것으로 예상하는 기존 애플리케이션 및 도구를 사용할 수 있기 때문에 SMB 프로토콜을 통해 Azure 파일 공유를 작업하길 원할 것으로 예상하지만, SMB보다는 File REST API를 사용하는 것이 이점인 이유가 다음과 같이 몇 가지가 있습니다.

- 이동 중, 즉 SMB 액세스가 없는 랩톱, 태블릿 또는 모바일 디바이스 등에서 Azure 파일 공유에 빠르게 변경 사항을 적용해야 해야 합니다.
- 포트 445가 잠금 해제되어 있지 않은 온-프레미스 클라이언트 등, SMB 공유를 탑재할 수 없는 클라이언트에서 스크립트 또는 애플리케이션을 실행해야 하는 경우
- 서버리스 리소스([Azure Functions](../../azure-functions/functions-overview.md) 등)를 활용하고 있는 경우 

다음 예제에서는 Azure Portal을 사용하여 File REST 프로토콜로 Azure 파일 공유를 조작하는 방법을 보여 줍니다. 

이제 Azure 파일 공유를 만들었으므로 SMB를 사용하여 [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) 또는 [macOS](storage-how-to-use-files-mac.md)에 파일 공유를 탑재할 수 있습니다. 또는 Azure Portal을 사용하여 Azure 파일 공유를 작업할 수 있습니다. 

#### <a name="create-a-directory"></a>디렉터리 만들기
Azure 파일 공유의 루트에 *myDirectory* 라는 새 디렉터리를 만들려면,

1. **파일 서비스** 페이지에서 **myshare** 파일 공유를 선택합니다. 파일 공유 페이지가 열립니다.
1. 페이지 위쪽의 메뉴에서 **+ 디렉터리 추가** 를 선택합니다. **새 디렉터리** 페이지가 드롭다운 방식으로 펼쳐집니다.
1. *myDirectory* 를 입력한 다음, **확인** 을 클릭합니다.

#### <a name="upload-a-file"></a>파일 업로드 
파일 업로드를 실제로 보여 주려면 먼저 업로드할 파일을 만들거나 선택해야 합니다. 적절하다고 판단되는 어떤 방법으로든 이 작업을 수행할 수 있습니다. 업로드하려는 파일이 선택되면 다음을 수행합니다.

1. **myDirectory** 디렉터리를 클릭합니다. **myDirectory** 패널이 열립니다.
1. 위쪽 메뉴에서 **업로드** 를 선택합니다. **파일 업로드** 패널이 열립니다.  
    
    ![파일 업로드 패널의 스크린샷](media/storage-how-to-use-files-portal/upload-file-1.png)

1. 폴더 아이콘을 클릭하여 로컬 파일을 탐색할 수 있는 창을 엽니다. 
1. 파일을 선택한 다음, **열기** 를 클릭합니다. 
1. **파일 업로드** 페이지에서 파일 이름을 확인한 다음, **업로드** 를 클릭합니다.
1. 완료되면 파일이 **myDirectory** 페이지의 목록에 표시됩니다.

#### <a name="download-a-file"></a>파일 다운로드
파일을 마우스 오른쪽 단추로 클릭하여 업로드한 파일의 복사본을 다운로드할 수 있습니다. 다운로드 단추를 클릭하면 표시되는 정확한 환경은 사용 중인 운영 체제와 브라우저에 따라 다릅니다.

## <a name="clean-up-resources"></a>리소스 정리
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Files란?](storage-files-introduction.md)