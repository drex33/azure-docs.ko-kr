---
title: '빠른 시작: Azure Blob Storage 라이브러리 v12 - C++'
description: 이 빠른 시작에서는 C++용 Azure Blob Storage 클라이언트 라이브러리 버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, Blob을 로컬 컴퓨터로 다운로드하는 방법과 컨테이너의 모든 Blob을 나열하는 방법을 알아봅니다.
author: kyle-patterson
ms.author: kylepa
ms.date: 06/21/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: mode-other
ms.openlocfilehash: 217d9a48a1e14a8f172d82622eb3c13a5dee04f0
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271064"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>빠른 시작: C++용 Azure Blob Storage 클라이언트 라이브러리 v12

C++용 Azure Blob Storage 클라이언트 라이브러리 v12를 시작합니다. Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Blob Storage는 대량의 비정형 데이터를 저장하는 데 최적화되어 있습니다.

C++용 Azure Blob Storage 클라이언트 라이브러리 v12를 사용하여 다음을 수행합니다.

- 컨테이너 만들기
- Azure Storage에 Blob 업로드
- 컨테이너의 모든 blob 나열
- 로컬 컴퓨터에 blob 다운로드
- 컨테이너 삭제

리소스:

- [API 참조 설명서](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [샘플](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Storage 계정](../common/storage-account-create.md)
- [C++ 컴파일러](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C 및 C++ 패키지 관리자](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>설치

이 섹션에서는 C++용 Azure Blob Storage 클라이언트 라이브러리 v12를 사용하는 프로젝트를 준비하는 과정을 안내합니다.

### <a name="install-the-packages"></a>패키지 설치

`vcpkg install` 명령은 C++용 Azure Storage Blob SDK 및 필요한 종속성을 설치합니다.

```console
vcpkg.exe install azure-storage-blobs-cpp:x64-windows
```

자세한 내용을 보려면 GitHub를 방문하여 [C++용 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)를 다운로드하고 빌드합니다.

### <a name="create-the-project"></a>프로젝트 만들기

Visual Studio에서 *BlobQuickstartV12* 라는 Windows용 새 C++ 콘솔 애플리케이션을 만듭니다.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="새 C++ Windows 콘솔 앱을 구성하기 위한 Visual Studio 대화 상자":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하는 데 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델이나 정의를 따르지 않는 데이터입니다. Blob Storage에서 제공하는 세 가지 종류의 리소스는 다음과 같습니다.

- 스토리지 계정
- 스토리지 계정의 컨테이너
- 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blobs-introduction/blob1.png)

다음 C++ 클래스를 사용하여 다음 리소스와 상호 작용합니다.

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): `BlobContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다. 모든 특수 Blob 클래스의 기본 클래스입니다.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): `BlockBlobClient` 클래스를 통해 Azure Storage 블록 Blob을 조작할 수 있습니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각 예제에서는 C++용 Azure Blob Storage 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

- [포함 파일 추가](#add-include-files)
- [연결 문자열 가져오기](#get-the-connection-string)
- [컨테이너 만들기](#create-a-container)
- [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
- [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
- [Blob 다운로드](#download-blobs)
- [컨테이너 삭제](#delete-a-container)

### <a name="add-include-files"></a>포함 파일 추가

프로젝트 디렉터리에서 다음을 수행합니다.

1. Visual Studio에서 *BlobQuickstartV12.sln* 솔루션 파일을 엽니다.
1. Visual Studio 내에서 *BlobQuickstartV12.cpp* 원본 파일을 엽니다.
1. `main` 내에서 자동 생성된 코드를 모두 제거합니다.
1. `#include` 문 추가

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string)에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

이 코드를 `main()` 내에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-container"></a>컨테이너 만들기

[CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a5d253aacb6e20578b7f5f233547be3e2) 함수를 호출하여 [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) 클래스의 인스턴스를 만듭니다. 그런 다음, [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ab3ef187d2e30e1a19ebadf45d0fdf9c4)를 호출하여 실제 컨테이너를 스토리지 계정에 만듭니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. "Hello Azure!"가 포함된 문자열을 선언합니다.
1. [컨테이너 만들기](#create-a-container) 섹션에서 컨테이너에 대한 [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f)를 호출하여 [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) 개체에 대한 참조를 가져옵니다.
1. [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) 함수를 호출하여 문자열을 Blob에 업로드합니다. Blob이 아직 없는 경우 이 함수에서 Blob을 만들고, 있는 경우 이를 업데이트합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[ListBlobs](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a8f7e6653ef69788cb4fb23ec85d4fd96) 함수를 호출하여 컨테이너의 blob을 나열합니다. 하나의 Blob만 컨테이너에 추가되었으므로 작업에서 해당 Blob만 반환합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blob 다운로드

업로드된 Blob의 속성을 가져옵니다. 그런 다음, 업로드된 Blob의 속성을 사용하여 새 `std::vector<uint8_t>` 개체를 선언하고 크기를 조정합니다. [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) 기본 클래스에서 [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa0bcbdbf6ebd17e4888cced4736420f7) 함수를 호출하여 이전에 만든 Blob을 새 `std::vector<uint8_t>` 개체로 다운로드합니다. 마지막으로 다운로드한 Blob 데이터를 표시합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Blob 삭제

다음 코드에서는 [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#ac121df9f5dfcdeb80f47b50543df0984) 함수를 호출하여 Azure Blob Storage 컨테이너에서 Blob을 삭제합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드에서는 [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ae7661b6d8f37213b409504c0e9a68d69)를 통해 전체 컨테이너를 삭제하여 앱에서 만든 리소스를 정리합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>코드 실행

이 앱은 컨테이너를 만들고, 텍스트 파일을 Azure Blob Storage에 업로드합니다. 그런 다음, 예제에서 컨테이너의 Blob을 나열하고, 파일을 다운로드하고, 파일 내용을 표시합니다. 마지막으로 앱에서 Blob 및 컨테이너를 삭제합니다.

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 C++를 사용하여 Blob을 업로드하고, 다운로드하고, 나열하는 방법을 알아보았습니다. 또한 Azure Blob Storage 컨테이너를 만들고 삭제하는 방법도 알아보았습니다.

Blob Storage 샘플을 보려면 다음으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [C++용 Azure Blob Storage SDK v12 샘플](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-blobs/samples)
