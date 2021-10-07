---
title: Azure 빠른 시작 - Go를 사용하여 개체 스토리지에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 스토리지에서 스토리지 계정 및 컨테이너를 만듭니다. 그런 다음, Go용 스토리지 클라이언트 라이브러리를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
author: normesta
ms.author: normesta
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 85a13c04085190622d90a804fa164136ac3dcc49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622496"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>빠른 시작: Go를 사용하여 BLOB 업로드, 다운로드 및 나열

이 빠른 시작에서 Go 프로그래밍 언어를 사용하여 Azure Blob Storage의 컨테이너에 블록 Blob을 업로드하고, 다운로드하고, 나열하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

다음 추가 필수 구성 요소를 설치했는지 확인합니다.

- [Go 1.8 이상](https://golang.org/dl/)
- [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/): 다음 명령 사용

    `go get -u github.com/Azure/azure-storage-blob-go/azblob`

    > [!NOTE]
    > URL에서 `Azure`를 대문자로 표시하여 SDK로 작업할 때 대/소문자 관련 가져오기 문제가 발생하지 않도록 합니다. 또한 import 문에서도 `Azure`를 대문자로 표시합니다.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작에서 사용되는 [샘플 애플리케이션](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)은 기본 Go 애플리케이션입니다.

[git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Blob Storage에 대한 Go 샘플을 열려면 storage-quickstart.go 파일을 검색합니다.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

이 솔루션에서는 샘플을 실행하는 컴퓨터에 로컬인 환경 변수에 스토리지 계정 이름과 키를 안전하게 저장해야 합니다. 운영 체제에 따라 아래 예제 중 하나를 따라 환경 변수를 만듭니다.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```shell
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>샘플 실행

이 샘플에서는 현재 폴더에 테스트 파일을 만들고, Blob Storage에 테스트 파일을 업로드하고, 컨테이너에 Blob을 나열하고, 파일을 버퍼에 다운로드합니다.

샘플을 사용하여 다음 명령을 실행합니다.

`go run storage-quickstart.go`

다음 출력은 애플리케이션 실행 시 반환되는 출력의 예제입니다.

```output
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```

키를 눌러 계속하면 샘플 프로그램이 스토리지 컨테이너 및 파일을 삭제합니다.

> [!TIP]
> [Azure Storage Explorer](https://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage Explorer는 스토리지 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다.
>

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL 및 BlobURL 개체 만들기

먼저 Blob 스토리지에 액세스하고 관리하는 데 사용되는 ContainerURL 및 BlobURL 개체에 대한 참조를 만듭니다. 이러한 개체는 REST API를 실행하는 Create, Upload 및 Download 같은 하위 수준 API를 제공합니다.

- [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) 구조체를 사용하여 자격 증명을 저장합니다.

- 자격 증명 및 옵션을 사용하여 [**파이프라인**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline)을 만듭니다. 파이프라인은 다시 시도 정책, 로깅, HTTP 응답 페이로드의 deserialization과 같은 항목을 지정합니다.

- 새 [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) 및 새 [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) 개체를 인스턴스화하여 컨테이너(Create) 및 Blob(Upload 및 Download)에서 작업을 실행합니다.

ContainerURL이 있으면 Blob을 가리키는 **BlobURL** 개체를 인스턴스화하고, 업로드, 다운로드 및 복사 등의 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 새 컨테이너를 만듭니다. 컨테이너는 **quickstartblobs-[random string]** 라고 합니다.

```go
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.

Blob에 파일을 업로드하려면 **os.Open** 을 사용하여 파일을 엽니다. 그런 다음, Upload(PutBlob), StageBlock/CommitBlockList(PutBlock/PutBlockList)와 같은 REST API 중 하나를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다.

또는 SDK가 하위 수준 REST API를 기반으로 하는 [고급 수준의 API](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go)를 제공합니다. 예를 들어 ***UploadFileToBlockBlob*** 함수는 처리량을 최적화하기 위해 StageBlock(PutBlock) 작업을 사용하여 동시에 청크에서 파일을 업로드합니다. 파일이 256MB보다 작은 경우 대신 Upload(PutBlob)을 사용하여 단일 트랜잭션에서 전송을 완료합니다.

다음 예제에서는 **quickstartblobs-[randomstring]** 이라는 컨테이너에 파일을 업로드합니다.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize: 4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**ContainerURL** 에서 **ListBlobs** 메서드를 사용하여 컨테이너의 파일 목록을 가져옵니다. ListBlobs는 지정된 **표식** 에서 시작하여 Blob의 단일 세그먼트(최대 5000개)를 반환합니다. 빈 표식을 사용하여 시작 부분에서 열거형을 시작합니다. Blob 이름은 사전적 순서대로 반환됩니다. 세그먼트를 가져온 후에 처리한 다음, 이전에 반환된 표식을 다시 전달하여 ListBlobs를 호출합니다.

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print("    Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Blob 다운로드

BlobURL에서 **Download** 하위 수준 함수를 사용하여 Blob을 다운로드합니다. 그러면 **DownloadResponse** 구조체가 반환됩니다. 구조체에서 **Body** 함수를 실행하여 데이터 읽기를 위한 **RetryReader** 스트림을 가져옵니다. 읽는 동안 연결에 실패하면 연결을 다시 수립하여 읽기를 계속하기 위한 추가적인 요청을 수행합니다. RetryReaderOption의 MaxRetryRequests 설정을 0(기본값)으로 지정하면 원래의 응답 본문이 반환되며 다시 시도하지 않습니다. 또는 고급 수준의 Api **DownloadBlobToBuffer** 또는 **DownloadBlobToFile** 을 사용하여 코드를 간소화합니다.

다음 코드는 **Download** 함수를 사용하여 Blob을 다운로드합니다. Blob의 콘텐츠를 버퍼에 작성하고 콘솔에 표시합니다.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 가이드에서는 업로드된 blob이 더 이상 필요하지 않으므로 **Delete** 메서드를 사용하여 전체 컨테이너를 삭제해도 됩니다.

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>BLOB을 사용하여 Go 애플리케이션을 개발하기 위한 리소스

Blob Storage를 사용하여 Go 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

- GitHub에서 Azure Storage용 [Go 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-blob-go)를 검색 및 설치하세요.
- Go 클라이언트 라이브러리를 사용하여 작성된 [Blob Storage 샘플](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples)을 탐색하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Go를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. Azure Storage Blob SDK에 대한 자세한 내용은 [소스 코드](https://github.com/Azure/azure-storage-blob-go/) 및 [API 참조](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob)를 참조하세요.
