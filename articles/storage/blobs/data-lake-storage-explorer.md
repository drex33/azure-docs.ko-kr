---
title: Azure Data Lake Storage Gen2에서 Azure Storage Explorer 사용
description: Azure Storage Explorer를 사용하여 HNS(계층 구조 네임스페이스)가 활성화된 스토리지 계정에서 디렉터리와 파일 및 디렉터리 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/28/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 240953bfe223f5f8382101bf73707df5c9978753
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133360621"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리

이 문서에서는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 HNS(계층 구조 네임스페이스)가 활성화된 스토리지 계정에서 디렉터리 및 파일을 만들고 관리하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](../common/storage-account-create.md)을 수행합니다.

- Azure Storage Explorer가 로컬 컴퓨터에 설치되었습니다. Windows, Macintosh 또는 Linux용 Azure Storage Explorer를 설치하려면 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 참조하세요.

> [!NOTE]
> Storage Explorer는 Azure Data Lake Storage Gen2와 작업할 때 Blob(blob)과 Data Lake Storage Gen2(dfs) 양쪽의 [엔드포인트](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage)를 모두 사용합니다. 프라이빗 엔드포인트를 사용하여 Azure Data Lake Storage Gen2에 대한 액세스를 구성하는 경우, 스토리지 계정에 두 개의 프라이빗 엔드포인트를 만듭니다. 여기서 하나는 대상 하위 리소스 `blob`이고 다른 하나는 대상 하위 리소스 `dfs`입니다.

## <a name="sign-in-to-storage-explorer"></a>Storage Explorer에 로그인

Storage Explorer 처음 시작하면 Azure Storage Microsoft Azure Storage Explorer **- 커넥트** 창이 나타납니다. Storage Explorer에서는 몇 가지 방법으로 스토리지 계정에 연결할 수 있지만, 현재는 ACL을 관리하는 한 가지 방법만 지원됩니다.

**리소스 선택** 패널에서 **구독** 을 선택합니다.

:::image type="content" source="./media/data-lake-storage-explorer/storage-explorer-connect-sml.png" alt-text="Microsoft Azure Storage Explorer 보여 주는 스크린샷 - 리소스 선택 창" lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-connect-lrg.png":::

**Azure 환경 선택** 패널에서 로그인할 Azure 환경을 선택합니다. 글로벌 Azure, 국가 클라우드 또는 Azure Stack 인스턴스에 로그인할 수 있습니다. 그런 후 **다음** 을 선택합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer 표시하고 Azure 환경 선택 옵션을 강조 표시하는 스크린샷." source="./media/data-lake-storage-explorer/storage-explorer-select-sml.png"  lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-select-sml.png":::

Storage Explorer는 로그인할 웹 페이지를 엽니다.

Azure 계정으로 성공적으로 로그인하면 계정과 계정에 연결된 Azure 구독이 **계정 관리** 아래에 나타납니다. 작업할 Azure 구독을 선택한 다음 **탐색기 열기를** 선택합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer 표시하고 계정 관리 창 및 탐색기 열기 단추를 강조 표시하는 스크린샷." source="./media/data-lake-storage-explorer/storage-explorer-account-panel-sml.png"  lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-account-panel-sml.png":::

작업이 완료되면 Azure Storage Explorer는 표시된 **탐색기** 탭을 로드합니다. 이 보기는 [Azure Storage 에뮬레이터](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 계정 또는 [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 환경을 통해 구성된 로컬 스토리지뿐만 아니라 모든 Azure Storage 계정에 대한 정보를 제공합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 연결 창" source="./media/data-lake-storage-explorer/storage-explorer-main-page-sml.png" lightbox="./media/data-lake-storage-explorer-acl/storage-explorer-main-page-lrg.png":::

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 디렉터리와 파일을 보관합니다. 계정을 만들려면 진행 단계에서 만든 스토리지 계정을 확장합니다. **Blob 컨테이너** 를 선택하고 마우스 오른쪽 단추로 클릭하여 **Blob 컨테이너 만들기** 를 선택합니다. 또는 **Blob 컨테이너를** 선택한 다음, **작업** 창에서 **Blob 컨테이너 만들기를** 선택할 수 있습니다. 

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 컨테이너 만들기" source="./media/data-lake-storage-explorer/creating-a-filesystem-sml.png" lightbox="./media/data-lake-storage-explorer/creating-a-filesystem-lrg.png" :::

컨테이너 이름을 입력합니다. 컨테이너 이름 명명 규칙 및 제한 사항 목록은 [컨테이너 만들기](storage-quickstart-blobs-dotnet.md#create-a-container) 섹션을 참조하세요. 완료되면 **Enter** 를 눌러 컨테이너를 만듭니다. 컨테이너가 성공적으로 만들어지고 나면 선택한 스토리지 계정의 **Blob 컨테이너** 폴더 아래에 표시됩니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 컨테이너 생성" source="./media/data-lake-storage-explorer/container-created-sml.png" lightbox="./media/data-lake-storage-explorer/container-created-lrg.png" :::

## <a name="create-a-directory"></a>디렉터리 만들기

디렉터리를 만들려면 진행 단계에서 만든 컨테이너를 선택합니다. 컨테이너 리본에서 **새 폴더** 단추를 선택합니다. 디렉터리의 이름을 입력합니다. 완료되면 **Enter** 를 눌러 디렉터리를 만듭니다. 디렉터리를 성공적으로 만든 후 편집기 창에 나타납니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 디렉터리 만들기" source="media/data-lake-storage-explorer/create-directory-sml.png" lightbox="media/data-lake-storage-explorer/create-directory-lrg.png" :::

## <a name="upload-blobs-to-the-directory"></a>디렉터리에 Blob 업로드

디렉터리 리본에서 **업로드** 단추를 선택합니다. 이 작업에서는 폴더나 파일을 업로드할 수 있는 옵션이 제공됩니다.

업로드할 파일 또는 폴더를 선택합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - Blob 업로드" source="media/data-lake-storage-explorer/storage-explorer-upload-file-sml.png" lightbox="media/data-lake-storage-explorer/storage-explorer-upload-file-lrg.png" :::

**업로드** 선택하면 선택한 파일이 큐에 대기되고 각 파일이 업로드됩니다. 업로드가 완료되면 결과가 **활동** 창에 표시됩니다.

## <a name="view-blobs-in-a-directory"></a>디렉터리에서 Blob 보기

**Azure Storage Explorer** 애플리케이션에서 스토리지 계정 아래에 있는 디렉터리를 선택합니다. 기본 창에는 선택한 디렉터리의 Blob 목록이 표시됩니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 디렉터리에 Blob 나열" source="media/data-lake-storage-explorer/storage-explorer-list-files-sml.png" lightbox="media/data-lake-storage-explorer/storage-explorer-list-files-sml.png" :::"

## <a name="download-blobs"></a>Blob 다운로드

**Azure Storage Explorer** 를 사용하여 파일을 다운로드하려면 파일을 선택한 리본에서 **다운로드** 를 선택합니다. 파일 대화 상자가 열리면 파일 이름을 입력할 수 있습니다. **폴더 선택을 선택하여** 로컬 위치에 파일 다운로드를 시작합니다.

:::image type="content" alt-text="Microsoft Azure Storage Explorer - 디렉터리에서 Blob 다운로드" source="media/data-lake-storage-explorer/storage-explorer-download-blob-sml.png" lightbox="media/data-lake-storage-explorer/storage-explorer-download-blob-sml.png" :::

## <a name="next-steps"></a>다음 단계

ACL(액세스 제어 목록)을 설정하여 파일 및 디렉터리 권한을 관리하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리](./data-lake-storage-explorer-acl.md)
