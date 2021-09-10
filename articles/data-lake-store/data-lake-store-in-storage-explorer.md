---
title: Azure Storage Explorer에서 Data Lake Storage Gen1 리소스 관리
description: Azure Storage Explorer에서 Azure Data Lake Storage Gen1 데이터 및 리소스에 액세스하고 관리하는 방법 알아보기
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/04/2021
ms.author: jejiang
ms.openlocfilehash: 9ef06e1b13141e3b5c342842a63e8e520e3e03e9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111589905"
---
# <a name="manage-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>저장소 탐색기를 사용하여 Data Lake Storage Gen1 리소스 관리

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md)은 텍스트 또는 이진 데이터와 같이 많은 양의 구조화되지 않은 데이터를 저장하는 서비스입니다. HTTP 또는 HTTPS를 통해 어디서나 데이터에 액세스할 수 있습니다. Azure Storage Explorer의 Data Lake Storage Gen1을 사용하면 사용자가 Blob 및 큐와 같은 다른 Azure 엔터티와 함께 Data Lake Storage Gen1 데이터 및 리소스에 액세스하고 관리할 수 있습니다. 이제 동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다.

또 다른 이점은 사용자에게 Data Lake Storage Gen1 데이터를 관리하기 위한 구독 권한이 필요하지 않다는 것입니다. 저장소 탐색기에서 누군가 사용 권한을 부여하는 한 Data Lake Storage Gen1 경로를 **로컬 및 연결된** 노드에 연결할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial)을 참조하세요.
* Data Lake Storage Gen1 계정. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](./data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="install-storage-explorer"></a>Storage Explorer 설치

[제품 웹 페이지](https://azure.microsoft.com/features/storage-explorer/)에서 최신 Azure Storage Explorer 비트를 설치합니다. 설치는 Windows, Linux 및 Mac 버전을 지원합니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. 저장소 탐색기에서 플러그 인 아이콘을 선택합니다.

   ![사용자 인터페이스에서 플러그 인 아이콘이 있는 위치를 보여주는 스크린샷](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

   **Azure Storage에 연결** 대화 상자가 열립니다.
1. **리소스 선택** 페이지에서 **구독** 을 선택합니다.
1. **Azure 환경 선택** 페이지에서 로그인할 Azure 환경을 선택한 후 **다음** 을 선택합니다.
1. **로그인** 대화 상자에서 Azure 자격 증명을 입력한 후 **다음** 을 선택합니다.

1. 저장소 탐색기의 **계정 관리** 창에서 관리할 Data Lake Storage Gen1 계정이 포함된 구독을 선택한 다음, **탐색기 열기** 를 선택합니다.
1. **탐색기** 창에서 구독을 펼칩니다. 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다. 여기에는 다음과 같은 Data Lake Storage Gen1 계정이 포함됩니다.

     ![Data Lake Storage Gen1 노드의 예제 계정을 보여주는 스크린샷](./media/data-lake-store-in-storage-explorer/account-list.png)

## <a name="connect-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 연결

누군가 리소스에 대한 URI를 제공하는 경우 구독에 존재하지 않는 리소스에 액세스할 수 있습니다. 그런 다음, 로그인한 후 URI를 사용하여 Data Lake Storage Gen1에 연결할 수 있습니다.

1. Storage Explorer를 엽니다.
1. **로컬 및 첨부** 를 펼칩니다.
1. **Data Lake Storage Gen1(미리 보기)** 을 마우스 오른쪽 단추로 클릭한 다음, **Data Lake Storage Gen1에 연결** 을 선택합니다.
1. URI를 입력합니다. 예를 들면 다음과 같습니다.

      ![URI를 입력하기 위한 텍스트 상자가 있는 "Data Lake Store에 연결" 대화 상자를 보여주는 스크린샷](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

   이 도구는 방금 입력한 URL의 위치를 찾습니다.

      ![UI의 Data Lake Storage Gen1(미리 보기) 노드 아래에 나열된 Data Lake Storage Gen1 계정이 표시됨](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-the-contents-of-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정의 콘텐츠 보기

Data Lake Storage Gen1 계정의 리소스에는 폴더와 파일이 포함되어 있습니다. 다음 단계에서는 저장소 탐색기 내에서 Data Lake Storage Gen1 계정의 내용을 보는 방법을 보여줍니다.

1. Storage Explorer를 엽니다.
1. 보려는 Data Lake Storage Gen1 계정이 포함된 구독을 펼칩니다.
1. **Data Lake Storage Gen1(미리 보기)** 을 펼칩니다.
1. 보려는 Data Lake Storage Gen1 계정을 선택합니다.

   기본 창에 Data Lake Storage Gen1 계정의 콘텐츠가 표시됩니다.

   ![선택된 Data Lake Storage Gen1 계정과 계정의 폴더 목록이 있는 기본 창이 표시됩니다.](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 리소스 관리

Data Lake Storage Gen1 리소스는 다음 작업을 수행하여 관리할 수 있습니다.

* 여러 Data Lake Storage Gen1 계정을 통해 Data Lake Storage Gen1 리소스를 찾아봅니다.  
* 연결 문자열을 사용하여 Data Lake Storage Gen1을 직접 연결하고 관리합니다.
* **로컬 및 연결됨** 아래에 있는 ACL을 통해 다른 사용자가 공유한 Data Lake Storage Gen1 리소스를 봅니다.
* 파일 및 폴더 CRUD 작업 수행: 재귀 폴더 및 선택한 여러 파일을 지원합니다.
* 빠르게 최근 위치에 액세스하려면 폴더를 끌고 드롭하고 추가합니다. 이 작업에는 데스크톱 파일 탐색기 경험이 반영됩니다.
* Storage Explorer에서 한 번의 클릭으로 Data Lake Storage Gen1 하이퍼링크를 복사하고 엽니다.
* 하단 창에 **활동** 로그를 표시하여 활동 상태를 표시합니다.
* 폴더 통계 및 파일 속성을 표시합니다.

## <a name="manage-resources-in-azure-storage-explorer"></a>Azure Storage Explorer에서 리소스 관리

Data Lake Storage Gen1 계정을 만든 후 다음 작업을 수행할 수 있습니다.

* 폴더와 파일을 업로드하고 폴더와 파일을 다운로드하고 로컬 컴퓨터에서 리소스를 엽니다.
* **빠른 액세스** 에 고정하고 새 폴더를 만들고 URL을 복사한 다음, 모두를 선택합니다.
* 복사 및 붙여넣기, 이름 바꾸기, 삭제, 폴더 통계 가져오기 및 새로 고침합니다.

다음 항목에서는 Data Lake Storage Gen1 계정에서 리소스를 관리하는 방법을 보여줍니다. 수행할 작업에 대한 단계를 따릅니다.

### <a name="upload-files"></a>파일 업로드

1. 기본 창의 도구 모음에서 **업로드** 를 선택한 후 **파일 업로드** 를 선택합니다.
1. **업로드할 파일 선택** 대화 상자에서 업로드하려는 파일을 선택합니다.
1. **열기** 를 선택하여 업로드를 시작합니다.

> [!NOTE]
> 로컬 컴퓨터에 있는 파일을 직접 끌어서 업로드를 시작할 수도 있습니다.

### <a name="upload-a-folder"></a>폴더 업로드

1. 기본 창의 도구 모음에서 **업로드** 를 선택한 후 **폴더 업로드** 를 선택합니다.
1. **업로드할 폴더 선택** 대화 상자에서 업로드할 폴더를 선택합니다.
1. **폴더 선택** 을 선택하여 업로드를 시작합니다.

> [!NOTE]
> 로컬 컴퓨터에 있는 폴더를 직접 끌어서 업로드를 시작할 수도 있습니다.

### <a name="download-folders-or-files-to-your-local-computer"></a>로컬 컴퓨터에 폴더 또는 파일 다운로드

1. 다운로드하려는 폴더 또는 파일을 선택합니다.
1. 기본 창 도구 모음에서 **다운로드** 를 선택합니다.
1. **다운로드한 파일을 저장할 폴더 선택** 대화 상자에서 위치와 이름을 지정합니다.
1. **저장** 을 선택합니다.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>로컬 컴퓨터에서 폴더 또는 파일 열기

1. 열려는 폴더 또는 파일을 선택합니다.
1. 기본 창 도구 모음에서 **열기** 를 선택합니다. 또는 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭한 다음, 바로 가기 메뉴에서 **열기** 를 선택합니다.

파일이 파일의 기본 파일 형식과 연결된 애플리케이션을 통해 다운로드되고 열립니다. 또는 기본 창에서 폴더가 열립니다.

### <a name="copy-folders-or-files-to-the-clipboard"></a>클립보드에 폴더 또는 파일 복사

Data Lake Storage Gen1 폴더 또는 파일을 복사해 다른 Data Lake Storage Gen1 계정에 붙여넣을 수 있습니다. 스토리지 형식에서의 복사 및 붙여넣기 작업은 지원되지 않습니다. 예를 들어 Azure Blob Storage에 또는 다른 방법으로는 Data Lake Storage Gen1 폴더나 파일을 복사해 붙여넣을 수 없습니다.

1. 복사하려는 폴더 또는 파일을 선택합니다.
1. 기본 창 도구 모음에서 **복사** 를 선택합니다. 또는 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭한 다음, 바로 가기 메뉴에서 **복사** 를 선택합니다.
1. 탐색 창에서 다른 Data Lake Storage Gen1 계정으로 이동해 기본 창에서 보도록 선택합니다.
1. 기본 창의 도구 모음에서 **붙여넣기** 를 선택하여 복사본을 만듭니다. 또는 대상의 바로 가기 메뉴에서 **붙여넣기** 를 선택합니다.

> [!NOTE]
> 복사-붙여넣기 작업은 폴더 또는 파일을 로컬 컴퓨터로 다운로드한 다음, 대상에 업로드하여 작동합니다. 도구는 백 엔드에서 작업을 수행하지 않습니다. 큰 파일의 복사-붙여넣기 작업은 느립니다.

### <a name="delete-folders-or-files"></a>폴더 또는 파일 삭제

1. 삭제하려는 폴더 또는 파일을 선택합니다.
1. 기본 창 도구 모음에서 **삭제** 를 선택합니다. 또는 선택한 폴더 또는 파일을 마우스 오른쪽 단추로 클릭한 다음, 바로 가기 메뉴에서 **삭제** 를 선택합니다.
1. 확인 대화 상자에서 **예** 를 선택합니다.

### <a name="pin-to-quick-access"></a>빠른 액세스에 고정

1. 리소스에 쉽게 액세스할 수 있도록 고정할 폴더를 선택합니다.
1. 기본 창의 도구 모음에서 **빠른 액세스에 고정** 을 선택합니다.

   탐색 창에서 선택한 폴더가 **빠른 액세스** 노드에 추가됩니다.

   ![UI의 빠른 액세스 노드 아래에 나열된 폴더가 표시됨](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

### <a name="use-deep-links"></a>딥 링크 사용

URL이 있는 경우 파일 탐색기 또는 브라우저의 주소 경로에 URL을 입력할 수 있습니다. 그러면 저장소 탐색기가 자동으로 열리고 URL의 위치로 이동됩니다.

![파일 탐색기 창에 복사된 Data Lake Storage Gen1 계정의 폴더 URL이 표시됨](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>다음 단계

* [최신 Storage Explorer 릴리스 정보 및 비디오](https://www.storageexplorer.com)를 보세요.
* [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Data Lake Storage Gen1 시작](./data-lake-store-overview.md)
