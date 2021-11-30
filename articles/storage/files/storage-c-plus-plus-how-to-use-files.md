---
title: '빠른 시작: Azure Storage 파일 공유 라이브러리 v12 - C++'
description: 이 빠른 시작에서는 C++용 Azure Storage 파일 공유 클라이언트 라이브러리 버전 12를 사용하여 파일 공유와 파일을 만드는 방법을 알아봅니다. 다음으로, 메타데이터를 설정하고 검색한 다음, 로컬 컴퓨터에 파일을 다운로드하는 방법을 알아봅니다.
author: kyle-patterson
ms.author: kylepa
ms.date: 06/22/2021
ms.service: storage
ms.subservice: files
ms.topic: quickstart
ms.custom: mode-other
ms.openlocfilehash: 704a3228da454243176d029702f8f84833c9e59d
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269525"
---
# <a name="develop-for-azure-files-with-c"></a>C++를 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="about-this-tutorial"></a>이 자습서 정보

이 자습서에서는 C++를 사용하여 Azure Files에서 기본 작업을 수행하는 방법을 알아봅니다. Azure Files를 처음 사용하는 경우 다음 섹션에 있는 개념들을 살펴보면 샘플에 대한 이해를 높이는 데 유용할 것입니다. 설명된 샘플 중 일부는 다음과 같습니다.

* Azure 파일 공유 만들기 및 삭제
* 디렉터리 만들기 및 삭제
* 파일 업로드, 다운로드 및 삭제
* 파일의 메타데이터 설정 및 나열

> [!Note]  
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 C++ I/O 클래스 및 함수를 사용하여 Azure 파일 공유에 액세스하는 간단한 애플리케이션을 작성할 수 있습니다. 이 문서에서는 [File REST API](/rest/api/storageservices/file-service-rest-api)를 사용하여 Azure Files와 통신하는 Azure Storage C++ SDK를 사용하는 애플리케이션을 작성하는 방법에 대해 설명합니다.


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
vcpkg.exe install azure-storage-files-shares-cpp:x64-windows
```

자세한 내용을 보려면 GitHub를 방문하여 [C++용 Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)를 다운로드하고 빌드합니다.

### <a name="create-the-project"></a>프로젝트를 만듭니다.

Visual Studio에서 *FilesShareQuickstartV12* 라는 Windows용 새 C++ 콘솔 애플리케이션을 만듭니다.

:::image type="content" source="./media/quickstart-files-c-plus-plus/visual-studio-create-project.png" alt-text="새 C++ Windows 콘솔 앱을 구성하기 위한 Visual Studio 대화 상자":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="code-examples"></a>코드 예제

이 예제 코드 조각에서는 C++용 Azure Files 공유 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

- [포함 파일 추가](#add-include-files)
- [연결 문자열 가져오기](#get-the-connection-string)
- [파일 공유 만들기](#create-a-files-share)
- [파일 공유에 파일 업로드](#upload-files-to-a-files-share)
- [파일의 메타데이터 설정](#set-the-metadata-of-a-file)
- [파일의 메타데이터 나열](#list-the-metadata-of-a-file)
- [파일 다운로드](#download-files)
- [파일 삭제](#delete-a-file)
- [파일 공유 삭제](#delete-a-files-share)

### <a name="add-include-files"></a>포함 파일 추가

프로젝트 디렉터리에서 다음을 수행합니다.

1. Visual Studio에서 *FilesShareQuickstartV12.sln* 솔루션 파일을 엽니다.
1. Visual Studio 내에서 *FilesShareQuickstartV12.cpp* 원본 파일을 엽니다.
1. `main` 내에서 자동 생성된 코드를 모두 제거합니다.
1. `#include` 문을 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string)에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

이 코드를 `main()` 내에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-files-share"></a>파일 공유 만들기

[CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a7462bcad8a9144f84b0acc70735240e2) 함수를 호출하여 [ShareClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html) 클래스의 인스턴스를 만듭니다. 그런 다음, [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a6432b4cc677ac03257c7bf234129ec5b)를 호출하여 스토리지 계정에 실제 파일 공유를 만듭니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_CreateFilesShare":::

### <a name="upload-files-to-a-files-share"></a>파일 공유에 파일 업로드

다음 코드 조각을 실행합니다.

1. "Hello Azure!"가 포함된 문자열을 선언합니다.
1. 루트 [ShareDirectoryClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html)를 가져온 후 [파일 공유 만들기](#create-a-files-share) 섹션의 파일 공유에서 [GetFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_directory_client.html#a93545b8d82ee94f9de183c4d277059d8)를 호출하여 [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) 개체에 대한 참조를 가져옵니다.
1. [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#aa80c1f0da6e6784aa0a67cff03d128ba) 함수를 호출하여 문자열을 파일에 업로드합니다. 이 함수는 파일이 없는 경우 파일을 만들고, 있는 경우 파일을 업데이트합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_UploadFile":::

### <a name="set-the-metadata-of-a-file"></a>파일의 메타데이터 설정

[ShareFileClient.SetMetadata](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a2f5a40b71040003e41ba8495101f67bb) 함수를 호출하여 파일의 메타데이터 속성을 설정합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_SetFileMetadata":::

### <a name="list-the-metadata-of-a-file"></a>파일의 메타데이터 나열

[ShareFileClient.GetProperties](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a220017815847877cfe8f244b482fb45c) 함수를 호출하여 파일의 메타데이터 속성을 가져옵니다. 메타데이터는 반환된 `Value`의 `Metadata` 필드 아래에 있습니다. 메타데이터는 [파일의 메타데이터 설정](#set-the-metadata-of-a-file)에 있는 예제와 비슷한 키-값 쌍입니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_GetFileMetadata":::

### <a name="download-files"></a>파일 다운로드

[파일의 메타데이터 나열](#list-the-metadata-of-a-file)에서 파일 속성을 검색한 경우 업로드된 파일의 속성을 사용하여 새 `std::vector<uint8_t>` 개체가 생성됩니다. [ShareFileClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html) 기본 클래스에서 [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a256e7b317fbf762c4f8f5023a2cd8cb9) 함수를 호출하여 이전에 만든 파일을 새 `std::vector<uint8_t>` 개체로 다운로드합니다. 마지막으로, 다운로드한 파일 데이터를 표시합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DownloadFile":::

### <a name="delete-a-file"></a>파일 삭제

다음 코드에서는 [ShareFileClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_file_client.html#a86036b445dce67a96f7bf6c45f163f59) 함수를 호출하여 Azure Storage 파일 공유에서 Blob을 삭제합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFile":::

### <a name="delete-a-files-share"></a>파일 공유 삭제

다음 코드에서는 [ShareClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-files-shares/12.0.0/class_azure_1_1_storage_1_1_files_1_1_shares_1_1_share_client.html#a9915630503f1675b5f49eb9c01ca2601)를 통해 전체 파일 공유를 삭제하여 앱에서 만든 리소스를 정리합니다.

다음 코드를 `main()`의 끝에 추가합니다.

:::code language="cpp" source="~/azure-storage-snippets/files/quickstarts/C++/V12/FilesShareQuickStartV12/FilesShareQuickStartV12/FilesShareQuickStartV12.cpp" ID="Snippet_DeleteFilesShare":::

## <a name="run-the-code"></a>코드 실행

이 앱은 컨테이너를 만들고, 텍스트 파일을 Azure Blob Storage에 업로드합니다. 그런 다음, 예제에서 컨테이너의 Blob을 나열하고, 파일을 다운로드하고, 파일 내용을 표시합니다. 마지막으로 앱에서 Blob 및 컨테이너를 삭제합니다.

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Files Shares storage v12 - C++ quickstart sample
Creating files share: sample-share
Uploading file: sample-file
Listing file metadata...
key1:value1
key2:value2
Downloaded file contents: Hello Azure!
Deleting file: sample-file
Deleting files share: sample-share
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 C++를 사용하여 파일을 업로드, 다운로드 및 나열하는 방법을 알아보았습니다. 또한 Azure Storage 파일 공유를 만들고 삭제하는 방법을 알아보았습니다.

Blob Storage 샘플을 보려면 다음으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [C++용 Azure Storage 파일 공유 SDK v12 샘플](https://github.com/Azure/azure-sdk-for-cpp/tree/main/sdk/storage/azure-storage-files-shares/samples)
