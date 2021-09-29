---
title: '빠른 시작: Azure Blob Storage 클라이언트 라이브러리 - Ruby'
description: Azure Blob Storage에서 스토리지 계정 및 컨테이너를 만듭니다. Ruby용 스토리지 클라이언트 라이브러리를 사용하여 컨테이너에서 Blob을 만들고, 다운로드하고, 나열합니다.
author: normesta
ms.author: normesta
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c34c042598e94cb2a86d1a068caca40bd3240b29
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587483"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>빠른 시작: Ruby용 Azure Blob Storage 클라이언트 라이브러리

Ruby를 사용하여 Microsoft Azure Blob Storage의 컨테이너에서 Blob을 만들고, 다운로드하고, 나열하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

다음 추가 필수 구성 요소를 설치했는지 확인합니다.

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby용 Azure Storage 라이브러리](https://github.com/azure/azure-storage-ruby)([RubyGem 패키지](https://rubygems.org/gems/azure-storage-blob) 사용)

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작 가이드에서 사용되는 [샘플 애플리케이션](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)은 기본 Ruby 애플리케이션입니다.

[Git](https://git-scm.com/)을 사용하여 애플리케이션 복사본을 개발 환경에 다운로드합니다. 이 명령은 리포지토리를 로컬 컴퓨터에 복제합니다.

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

*storage-blobs-ruby-quickstart* 폴더로 이동하여 코드 편집기에서 *example.rb* 파일을 엽니다.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

애플리케이션에 대한 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 인스턴스를 만들려면 스토리지 계정 이름과 계정 키를 제공합니다.

*example.rb* 파일의 다음 코드는 새 [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 개체를 인스턴스화합니다. *accountname* 및 *accountkey* 값을 해당하는 계정 이름과 키로 바꿉니다.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>샘플 실행

이 샘플은 컨테이너를 Blob Storage에 만들고, 새 Blob을 컨테이너에 만들고, 컨테이너의 Blob을 나열하고, Blob을 로컬 파일에 다운로드합니다.

예제를 실행합니다. 애플리케이션을 실행한 결과에 대한 출력 예는 다음과 같습니다.

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Enter 키를 눌러 계속하면 샘플 프로그램에서 스토리지 컨테이너와 로컬 파일을 삭제합니다. 계속하기 전에 다운로드한 파일의 *Documents* 폴더를 확인합니다.

또한 [Azure Storage Explorer](https://storageexplorer.com)를 사용하여 스토리지 계정의 파일을 확인할 수 있습니다. Azure Storage Explorer는 스토리지 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다.

파일이 확인되면 Enter 키를 눌러 테스트 파일을 삭제하고 데모를 종료합니다. *example.rb* 파일을 열어 코드를 확인합니다.

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해할 수 있도록 샘플 코드를 살펴봅니다.

### <a name="get-references-to-the-storage-objects"></a>스토리지 개체에 대한 참조 가져오기

가장 먼저 수행할 작업은 Blob Storage에 액세스하고 관리하는 데 사용되는 개체의 인스턴스를 만드는 것입니다. 이러한 개체는 서로를 기반으로 합니다. 각각은 목록의 그 다음 개체에서 사용됩니다.

- Azure Storage [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) 개체의 인스턴스를 만들어 연결 자격 증명을 설정합니다.
- 액세스하는 컨테이너를 나타내는 [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) 개체를 만듭니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

컨테이너 개체가 있으면 관심 있는 특정 Blob을 가리키는 [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) Blob 개체를 만들 수 있습니다. [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) 개체를 사용하여 Blob을 만들고, 다운로드하고, 복사합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 BLOB 이름에 대한 자세한 내용은 [컨테이너, BLOB, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

다음 예제 코드에서 수행하는 작업은 다음과 같습니다.

- 새 컨테이너를 만듭니다.
- Blob이 공개되도록 컨테이너에 대한 권한을 설정합니다. 컨테이너는 고유 ID가 추가된 *quickstartblobs* 라고 합니다.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>컨테이너에서 Blob 만들기

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. Blob을 만들려면 Blob에 대한 데이터를 전달하는 [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) 메서드를 호출합니다.

다음 예제에서는 이전에 만든 컨테이너에서 고유 ID와 *.txt* 파일 확장명을 사용하여 *QuickStart_* 라는 Blob을 만듭니다.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

블록 Blob은 최대 4.7TB일 수 있으며, 스프레드시트에서 큰 비디오 파일에 이르기까지 다양할 수 있습니다. 페이지 Blob은 주로 IaaS 가상 머신을 지원하는 VHD 파일에 사용됩니다. 추가 Blob은 일반적으로 파일에 쓴 다음, 더 많은 정보를 계속 추가하려는 경우와 같이 로깅에 사용됩니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) 메서드를 사용하여 컨테이너의 파일 목록을 가져옵니다. 다음 코드는 Blob 목록을 검색한 다음, 해당 이름을 표시합니다.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Blob 다운로드

[get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) 메서드를 사용하여 Blob을 로컬 디스크에 다운로드합니다. 다음 코드는 이전 섹션에서 만든 Blob을 다운로드합니다.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>리소스 정리

Blob이 더 이상 필요하지 않은 경우 [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method)을 사용하여 제거합니다. [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) 메서드를 사용하여 전체 컨테이너를 삭제합니다. 컨테이너를 삭제하면 컨테이너에 저장된 Blob도 모두 삭제됩니다.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>BLOB을 사용하여 Ruby 애플리케이션을 개발하기 위한 리소스

Ruby 개발을 위한 다음 추가 리소스를 참조하세요.

- GitHub에서 Azure Storage용 [Ruby 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-ruby)를 검색 및 다운로드하세요.
- Ruby 클라이언트 라이브러리를 사용하여 작성된 [Azure 샘플](/samples/browse/?products=azure&languages=ruby)을 살펴봅니다.
- [샘플: Ruby에서 Azure Storage 시작](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Ruby를 사용하여 Azure Blob Storage와 로컬 디스크 간에 파일을 전송하는 방법을 알아보았습니다. Blob Storage를 사용하는 방법에 대해 자세히 알아보려면 Storage 계정 개요로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Storage 계정 개요](../common/storage-account-overview.md)

Storage Explorer 및 Blob에 대한 자세한 내용은 [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
