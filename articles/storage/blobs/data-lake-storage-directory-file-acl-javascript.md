---
title: JavaScript(Node.js)를 사용하여 Azure Data Lake Storage Gen2의 데이터 관리
description: JavaScript용 Azure Storage Data Lake 클라이언트 라이브러리를 이용해 계층 구조 네임스페이스를 사용하도록 설정한 스토리지 계정의 디렉터리와 파일을 관리합니다.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: b7e753f248e9de1c2812cabed5c1cf432e726f59
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593214"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2의 디렉터리 및 파일 관리

이 문서에서는 Node.js를 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 디렉터리 및 파일을 만들고 관리하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL(액세스 제어 목록)을 가져오거나 설정하고 업데이트하는 방법에 대한 자세한 내용은 [Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-javascript.md)를 참조하세요.

[패키지(노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 자세한 내용은 [Azure 평가판 받기를 참조하세요.](https://azure.microsoft.com/pricing/free-trial/)

- 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Node.js 애플리케이션에서 해당 패키지를 사용 중인 경우 8.0.0 이상의 Node.js가 필요합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

터미널 창을 열고 다음 명령을 입력하여 JavaScript용 Data Lake 클라이언트 라이브러리를 설치합니다.

```javascript
npm install @azure/storage-file-datalake
```

이 명령문을 코드 파일의 맨 위에 배치하여 `storage-file-datalake` 패키지를 가져옵니다.

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용하려면 스토리지 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

계정에 연결하는 가장 쉬운 방법입니다.

이 예제에서는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential =
     new StorageSharedKeyCredential(accountName, accountKey);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;
}

```

> [!NOTE]
> 이 권한 부여 방법은 Node.js 애플리케이션에만 적용됩니다. 브라우저에서 코드를 실행하려는 경우 Azure AD(Azure Active Directory)를 사용하여 권한을 부여할 수 있습니다.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)를 사용하여 연결

[JS용 Azure ID 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/identity)를 사용하여 Azure AD로 애플리케이션을 인증할 수 있습니다.

이 예제에서는 클라이언트 ID, 클라이언트 암호, 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다. 해당 값을 가져오는 방법은 [클라이언트 애플리케이션에서 보낸 요청에 권한을 부여하기 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요.

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);

  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;
}
```

> [!NOTE]
> 더 많은 예제는 [JS용 Azure ID 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/identity) 설명서를 참조하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일의 파일 시스템 역할을 합니다. **FileSystemClient** 인스턴스를 가져온 다음 **FileSystemClient.Create** 메서드를 호출하여 컨테이너를 만들 수 있습니다.

다음 예제에서는 `my-file-system`이라는 컨테이너를 만듭니다.

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";

  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();

}
```

## <a name="create-a-directory"></a>디렉터리 만들기

**DirectoryClient** 인스턴스를 가져온 다음 **DirectoryClient.create** 메서드를 호출하여 디렉터리 참조를 만듭니다.

이 예제에서는 `my-directory`라는 디렉터리를 컨테이너에 추가합니다.

```javascript
async function CreateDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");

  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DirectoryClient.rename** 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리의 경로를 매개 변수로 전달합니다.

이 예제에서는 한 하위 디렉터리를 `my-directory-renamed`라는 이름으로 바꿉니다.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.move("my-directory-renamed");

}
```

이 예제에서는 `my-directory-renamed`라는 디렉터리를 `my-directory-2`라는 디렉터리의 하위 디렉터리로 이동합니다.

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed");
  await directoryClient.move("my-directory-2/my-directory-renamed");

}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**DirectoryClient.delete** 메서드를 호출하여 디렉터리를 삭제합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 파일을 읽습니다. 이 예제에서는 Node.js `fs` 모듈을 사용합니다. 그런 다음 **FileClient** 인스턴스를 만든 후 **FileClient.create** 메서드를 호출하여 대상 디렉터리에 파일 참조를 만듭니다. **FileClient.append** 메서드를 호출하여 파일을 업로드합니다. **FileClient.flush** 메서드를 호출하여 업로드를 완료해야 합니다.

다음 예제에서는 한 텍스트 파일을 `my-directory`라는 디렉터리에 업로드합니다.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs')

  var content = "";

  fs.readFile('mytestfile.txt', (err, data) => {
      if (err) throw err;

      content = data.toString();

  })

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저, 다운로드하려는 파일을 나타내는 **FileSystemClient** 인스턴스를 만듭니다. **FileSystemClient.read** 메서드를 사용하여 파일을 읽습니다. 그런 다음 파일을 작성합니다. 이 예에서는 Node.js `fs` 모듈을 사용하여 해당 작업을 수행합니다.

> [!NOTE]
> 이 파일 다운로드 방법은 Node.js 애플리케이션에만 적용됩니다. 브라우저에서 코드를 실행하려는 경우 [JavaScript용 Azure Storage File Data Lake 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/storage-file-datalake) 추가 정보 파일에서 해당 방법의 예를 참조하세요.

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);

  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }

  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예제에서는 `my-directory`라는 디렉터리에 있는 각 디렉터리와 파일의 이름을 인쇄합니다.

```javascript
async function ListFilesInDirectory(fileSystemClient) {

  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {

    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>참고 항목

- [패키지(Node 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)
