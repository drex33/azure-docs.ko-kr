---
title: JavaScript(Node.js)를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리
description: JavaScript용 Azure Storage Data Lake 클라이언트 라이브러리를 사용하여 HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정에서 ACL(액세스 제어 목록)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: cf09d9956a2cc7cea6831b52df45abc7f0fbb1b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555454"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2에서 ACL 관리

이 문서에서는 Node.js를 사용하여 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정하고, 업데이트하는 방법을 설명합니다.

[패키지(노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 자세한 내용은 [Azure 평가판 받기를 참조하세요.](https://azure.microsoft.com/pricing/free-trial/)

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당된 프로비저닝된 Azure AD(Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal).

  - ACL 설정을 적용하려는 대상 컨테이너 또는 디렉터리를 소유한 사용자. ACL을 재귀적으로 설정하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함합니다.

  - 스토리지 계정 키.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure AD(Active Directory)를 사용하여 연결

> [!NOTE]
> Azure AD(Azure Active Directory)를 사용하여 액세스에 권한을 부여하는 경우 보안 주체가 [스토리지 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당되었는지 확인합니다. ACL 사용 권한을 적용하는 방법 및 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델을 참조하세요.](./data-lake-storage-access-control-model.md)

Azure AD를 사용하여 애플리케이션을 인증하는 데 [JS용 Azure ID 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/identity)를 사용할 수 있습니다.

클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 가져옵니다. 이 과정을 수행하려면 [클라이언트 애플리케이션의 요청에 대한 권한 부여를 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요. 해당 프로세스의 일부로 다음 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어) 역할 중 하나를 보안 주체에 할당해야 합니다.

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유하는 디렉터리와 파일만 해당.|

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

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
> 이 권한 부여 방법은 Node.js 애플리케이션에만 적용됩니다. 브라우저에서 코드를 실행하려는 경우 Azure AD(Active Directory)를 사용하여 권한을 부여할 수 있습니다.

## <a name="get-and-set-a-directory-acl"></a>디렉터리 ACL 가져오기 및 설정

이 예제에서는 `my-directory`라는 디렉터리의 ACL을 가져온 다음 설정합니다. 이 예제에서는 담당 사용자에게 읽기, 쓰기, 실행 권한을 부여하고 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 사용자에게는 읽기 권한을 부여합니다.

> [!NOTE]
> 애플리케이션에서 Azure AD(Azure Active Directory)를 사용하여 액세스 권한을 부여하는 경우 애플리케이션에서 액세스 권한을 부여하는 데 사용하는 보안 주체가 [Storage Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당되었는지 확인합니다. ACL 사용 권한을 적용하는 방법 및 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를 참조하세요.](./data-lake-storage-access-control.md)

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

컨테이너의 루트 디렉터리에 대한 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 빈 문자열(`/`)을 **DataLakeFileSystemClient.getDirectoryClient** 메서드에 전달합니다.

### <a name="get-and-set-a-file-acl"></a>파일 ACL 가져오기 및 설정

이 예제에서는 `upload-file.txt`라는 파일의 ACL을 가져온 다음 설정합니다. 이 예제에서는 담당 사용자에게 읽기, 쓰기, 실행 권한을 부여하고 소유 그룹에는 읽기 및 실행 권한만 제공하며, 다른 모든 사용자에게는 읽기 권한을 부여합니다.

> [!NOTE]
> 애플리케이션에서 Azure AD(Azure Active Directory)를 사용하여 액세스 권한을 부여하는 경우 애플리케이션에서 액세스 권한을 부여하는 데 사용하는 보안 주체가 [Storage Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당되었는지 확인합니다. ACL 사용 권한을 적용하는 방법 및 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를 참조하세요.](./data-lake-storage-access-control.md)

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);
}
```

## <a name="see-also"></a>참고 항목

- [패키지(Node 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 ACL](data-lake-storage-access-control.md)(액세스 제어 목록)
