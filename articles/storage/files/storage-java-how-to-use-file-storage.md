---
title: Java를 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 Java 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/26/2021
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 97d774ca0a9b5b18bc10495640d92df0cfde115f
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115663"
---
# <a name="develop-for-azure-files-with-java"></a>Java를 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Azure Files를 사용하여 데이터를 저장하는 Java 애플리케이션 개발에 대한 기본 사항을 알아봅니다. 콘솔 애플리케이션을 만들고 Azure Files API를 사용하는 기본 작업을 알아봅니다.

- Azure 파일 공유 만들기 및 삭제
- 디렉터리 만들기 및 삭제
- Azure 파일 공유의 파일 및 디렉터리 열거
- 파일 업로드, 다운로드 및 삭제

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

샘플을 빌드하려면 JDK(Java 개발 키트)와 [Java용 Azure Storage SDK](https://github.com/azure/azure-sdk-for-java)가 필요합니다. Azure Storage 계정도 만들었어야 합니다.

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정

Azure Files API를 사용하려면 Azure Files에 액세스하려는 Java 파일 맨 위에 다음 코드를 추가합니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 설정

Azure Files를 사용하려면 Azure Storage 계정에 연결해야 합니다. 연결 문자열을 구성하고 사용하여 스토리지 계정에 연결합니다. 연결 문자열을 저장할 정적 변수를 정의합니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

*\<storage_account_name\>* 및 *\<storage_account_key\>* 를 스토리지 계정의 실제 값으로 바꿉니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

*your_storage_account_name* 및 *your_storage_account_key* 를 스토리지 계정의 실제 값으로 바꿉니다.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Azure 파일 공유에 액세스

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Azure Files에 액세스하려면 [ShareClient](/java/api/com.azure.storage.file.share.shareclient) 개체를 만듭니다. [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) 클래스를 사용하여 새 **ShareClient** 개체를 빌드합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

스토리지 계정에 연결하려면 **CloudStorageAccount** 개체를 사용하여 연결 문자열을 **구문 분석** 메서드로 전달합니다.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 는 InvalidKeyException을 발생시키므로 try/catch 블록 안에 넣어야 합니다.

---

## <a name="create-a-file-share"></a>파일 공유 만들기

Azure Files의 모든 파일과 디렉터리는 공유라고 하는 컨테이너에 저장됩니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

공유가 이미 있으면 [ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) 메서드에서 예외가 발생합니다. 호출을 `try/catch` 블록의 **create** 에 넣고 예외를 처리합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

공유 및 해당 콘텐츠에 대한 액세스 권한을 가져오려면 Azure Files 클라이언트를 만듭니다.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure Files 클라이언트를 사용하면 공유에 대한 참조를 가져올 수 있습니다.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

실제로 공유를 만들려면 **CloudFileShare** 개체의 **createIfNotExists** 메서드를 사용합니다.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

이때 **공유** 는 **샘플 공유** 라는 공유에 대한 참조를 저장합니다.

---

## <a name="delete-a-file-share"></a>파일 공유 삭제

다음 샘플 코드에서는 파일 공유를 삭제합니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

[ShareClient.delete](/java/api/com.azure.storage.file.share.shareclient.delete) 메서드를 호출하여 공유를 삭제합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

**CloudFileShare** 개체에서 **deleteIfExists** 메서드를 호출하여 공유를 삭제합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>디렉터리 만들기

루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에 파일을 놓아 스토리지를 구성합니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

다음 코드에서는 [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)를 호출하여 디렉터리를 만듭니다. 예제 메서드는 디렉터리를 성공적으로 만들었는지 여부를 나타내는 `Boolean` 값을 반환합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

다음 코드에서는 루트 디렉터리 아래에 **sampledir** 라는 하위 디렉터리를 만듭니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>디렉터리 삭제

디렉터리 삭제 작업은 간단합니다. 여전히 파일이나 하위 디렉터리가 포함된 디렉터리를 삭제할 수 없습니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

디렉터리가 존재하지 않거나 비어 있지 않으면 [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) 메서드에서 예외가 발생합니다. 호출을 `try/catch` 블록의 **delete** 에 넣고 예외를 처리합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

[ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories)를 호출하여 파일과 디렉터리의 목록을 가져옵니다. 메서드는 반복할 수 있는 [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) 개체 목록을 반환합니다. 다음 코드에서는 *dirName* 매개 변수에서 지정한 디렉터리에 있는 파일과 디렉터리를 나열합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

**CloudFileDirectory** 참조에서 **listFilesAndDirectories** 를 호출하여 파일과 디렉터리의 목록을 가져옵니다. 메서드는 반복할 수 있는 **ListFileItem** 개체 목록을 반환합니다. 다음 코드에서는 루트 디렉터리에 있는 파일과 디렉터리를 나열합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>파일 업로드

로컬 스토리지에서 파일을 업로드하는 방법을 알아봅니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

다음 코드에서는 [ShareFileClient.uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) 메서드를 호출하여 로컬 파일을 Azure Files에 업로드합니다. 다음 예제 메서드는 지정된 파일이 성공적으로 업로드됐는지 여부를 나타내는 `Boolean` 값을 반환합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

공유 개체에서 **getRootDirectoryReference** 메서드를 호출하여 파일을 업로드할 디렉터리에 대한 참조를 가져옵니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

이제 공유의 루트 디렉터리에 대한 참조를 가졌으므로 다음 코드를 사용하여 파일을 업로드할 수 있습니다.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>파일 다운로드

자주 수행하는 작업 중 하나는 Azure 파일 공유에서 파일을 다운로드하는 것입니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

다음 예제에서는 지정된 파일을 *destDir* 매개 변수에 지정된 로컬 디렉터리로 다운로드합니다. 예제 메서드는 날짜와 시간을 앞에 추가하여 다운로드한 파일의 고유 이름을 만듭니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

다음 예제에서는 SampleFile.txt를 다운로드하고 해당 콘텐츠를 표시합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>파일 삭제

다른 일반적인 Azure Files 작업은 파일 삭제입니다.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

다음 코드에서는 지정된 파일을 삭제합니다. 먼저 예제에서는 *dirName* 매개 변수에 따라 [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient)를 만듭니다. 그런 다음, 코드는 *fileName* 매개 변수에 따라 디렉터리 클라이언트에서 [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient)를 가져옵니다. 마지막으로 예제 메서드에서 [ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete)를 호출하여 파일을 삭제합니다.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v8"></a>[Azure Java SDK v8](#tab/java8)

다음 코드는 **sampledir** 라는 이름의 디렉터리 안에 저장된 SampleFile.txt라는 이름의 파일을 삭제합니다.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>다음 단계

다른 Azure Storage API에 대해 더 알아보려면 다음 링크를 따르십시오.

- [Java 개발자용 Azure](/azure/developer/java)
- [Java용 Azure SDK](https://github.com/azure/azure-sdk-for-java)
- [Android용 Azure SDK](https://github.com/azure/azure-sdk-for-android)
- [Java SDK용 Azure 파일 공유 클라이언트 라이브러리 참조](/java/api/overview/azure/storage-file-share-readme)
- [Azure Storage 서비스 REST API](/rest/api/storageservices/)
- [Azure Storage 팀 블로그](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy-v10.md)
- [Azure Files 문제 해결 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
