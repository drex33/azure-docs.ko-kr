---
title: '빠른 시작: Azure Blob Storage 라이브러리 v12 - Python'
description: 이 빠른 시작에서는 Python용 Azure Blob Storage 클라이언트 라이브러리 버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, Blob을 로컬 컴퓨터로 다운로드하는 방법과 컨테이너의 모든 Blob을 나열하는 방법을 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom: devx-track-python, mode-api
ms.openlocfilehash: 4753c10ab472a9369ea937bef766c703fe328950
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008734"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>빠른 시작: Python v12 SDK로 Blob 관리

이 빠른 시작에서는 Python을 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. Blob을 업로드, 다운로드 및 나열하고 컨테이너를 만들고 삭제합니다.

추가 리소스:

- [API 참조 설명서](/python/api/azure-storage-blob)
- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
- [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-blob/)
- [샘플](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7 또는 3.6 이상

## <a name="setting-up"></a>설치

이 섹션에서는 Python용 Azure Blob Storage 클라이언트 라이브러리 v12를 사용하는 프로젝트를 준비합니다.

### <a name="create-the-project"></a>프로젝트 만들기

*blob-quickstart-v12* 라는 Python 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir blob-quickstart-v12
    ```

1. 새로 만든 *blob-quickstart-v12* 디렉터리로 전환합니다.

    ```console
    cd blob-quickstart-v12
    ```

1. *blob-quickstart-v12* 디렉터리에서 *data* 라는 다른 디렉터리를 만듭니다. 이 디렉터리는 Blob 데이터 파일이 생성되고 저장되는 위치입니다.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python용 Azure Blob Storage 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-storage-blob
```

이 명령은 Python용 Azure Blob Storage 클라이언트 라이브러리 패키지와 이 패키지에 사용되는 모든 라이브러리를 설치합니다. 이 경우에는 Python용 Azure 핵심 라이브러리만 설치합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 새 텍스트 파일 열기
1. `import` 문 추가
1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

    코드는 다음과 같습니다.

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. 새 파일을 *blob-quickstart-v12* 디렉터리에 *blob-quickstart-v12.py* 로 저장합니다.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하는 데 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델이나 정의를 따르지 않는 데이터입니다. Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

- 스토리지 계정
- 스토리지 계정의 컨테이너
- 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blobs-introduction/blob1.png)

다음 Python 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

- [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
- [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
- [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각 예제에서는 Python용 Azure Blob Storage 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

- [연결 문자열 가져오기](#get-the-connection-string)
- [컨테이너 만들기](#create-a-container)
- [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
- [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
- [Blob 다운로드](#download-blobs)
- [컨테이너 삭제](#delete-a-container)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 만든 환경 변수에서 스토리지 계정 연결 문자열을 검색합니다.

다음 코드를 `try` 블록 내에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>컨테이너 만들기

새 컨테이너의 이름을 결정합니다. 아래 코드는 컨테이너 이름이 고유해지도록 이름에 UUID 값을 추가합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) 메서드를 호출하여 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-)를 호출하여 스토리지 계정에 컨테이너를 실제로 만듭니다.

이 코드를 `try` 블록의 끝에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. 데이터 파일을 저장할 로컬 디렉터리를 만듭니다.
1. 로컬 디렉터리에 텍스트 파일을 만듭니다.
1. [컨테이너 만들기](#create-a-container) 섹션에서 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)에 대해 [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) 메서드를 호출하여 [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) 개체에 대한 참조를 가져옵니다.
1. [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) 메서드를 호출하여 로컬 텍스트 파일을 Blob에 업로드합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) 메서드를 호출하여 컨테이너의 blob을 나열합니다. 이 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blob 다운로드

[download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) 메서드를 호출하여 이전에 만든 blob을 다운로드합니다. 예제 코드는 로컬 파일 시스템에서 두 파일을 볼 수 있도록 파일 이름에 "DOWNLOAD" 접미사를 추가합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드는 [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) 메서드로 전체 컨테이너를 제거하여 앱이 만든 리소스를 정리합니다. 원하는 경우 로컬 파일을 삭제할 수도 있습니다.

앱은 blob, 컨테이너 및 로컬 파일을 삭제하기 전에 `input()`을 호출하여 사용자 입력을 일시 중지합니다. 리소스를 삭제하기 전에 리소스가 제대로 생성되었는지 확인합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>코드 실행

이 앱은 로컬 폴더에 테스트 파일을 만들고 Azure Blob Storage에 업로드합니다. 그런 다음, 예제에서 컨테이너의 Blob을 나열하고 새 이름으로 파일을 다운로드합니다. 이전 파일과 새 파일을 비교할 수 있습니다.

*blob-quickstart-v12.py* 파일이 포함된 디렉터리로 이동한 후 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python blob-quickstart-v12.py
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

정리 프로세스를 시작하기 전에 *data* 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

파일을 확인한 후에 **Enter** 키를 눌러 테스트 파일을 삭제하고 데모를 완료합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python을 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법을 배웠습니다.

Blob 스토리지 샘플 앱을 보려면 다음을 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 Python 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

- 자세한 내용은 [Python용 Azure Storage 클라이언트 라이브러리](/azure/developer/python/sdk/storage/overview)를 참조하세요.
- 자습서, 샘플, 빠른 시작 및 기타 설명서는 [Python 개발자용 Azure](/azure/python/)를 참조하세요.
