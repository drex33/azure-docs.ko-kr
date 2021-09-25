---
title: Java를 사용한 Azure Data Lake Storage Gen2의 데이터 관리
description: Java용 Azure Storage 라이브러리를 이용하여 계층 구조 네임스페이스를 사용하는 스토리지 계정의 디렉터리와 파일을 관리합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 25002640a81b260ced61c25e2d157a100d6dbc2a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593404"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 Java를 사용하여 디렉터리 및 파일 관리

이 문서에서는 Java를 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하는 방법을 보여 드립니다.

디렉터리 및 파일의 ACL(액세스 제어 목록)을 가져오거나 설정하고 업데이트하는 방법에 대한 자세한 내용은 [Java를 사용한 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-java.md)를 참조하세요.

[패키지(Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API 참조](/java/api/overview/azure/storage-file-datalake-readme) | [Gen1에서 Gen2로 매핑](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 자세한 내용은 [Azure 평가판 받기를 참조하세요.](https://azure.microsoft.com/pricing/free-trial/)

- 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작하려면 [이 페이지](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)를 열고 최신 버전의 Java 라이브러리를 찾습니다. 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 해당 버전을 참조하는 종속성 요소를 추가합니다.

Azure AD(Azure Active Directory)를 사용하여 클라이언트 애플리케이션을 인증하려는 경우 Azure 암호 클라이언트 라이브러리에 종속성을 추가합니다. 자세한 내용은 [프로젝트에 비밀 클라이언트 라이브러리 패키지 추가를 참조하세요.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

그런 다음 이러한 imports 문을 코드 파일에 추가합니다.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용하려면 스토리지 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

계정에 연결하는 가장 쉬운 방법입니다.

이 예시에서는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)를 사용하여 연결

[Java용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)를 사용하여 Azure AD로 애플리케이션을 인증할 수 있습니다.

이 예시에서는 클라이언트 ID, 클라이언트 암호, 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다. 이러한 값을 얻으려면 [클라이언트 애플리케이션의 요청에 대한 권한 부여를 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> 더 많은 예제는 [Java용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)를 참조하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일의 파일 시스템 역할을 합니다. **DataLakeServiceClient.createFileSystem** 메서드를 호출하여 만들 수 있습니다.

다음 예제에서는 `my-file-system`라는 컨테이너를 만듭니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>디렉터리 만들기

**DataLakeFileSystemClient.createDirectory** 메서드를 호출하여 디렉터리 참조를 만듭니다.

이 예시에서는 `my-directory`라는 디렉터리를 컨테이너에 추가한 다음 `my-subdirectory`라는 하위 디렉터리를 추가합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DataLakeDirectoryClient.rename** 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리의 경로를 매개 변수로 전달합니다.

이 예제에서는 한 하위 디렉터리를 `my-subdirectory-renamed`라는 이름으로 바꿉니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

이 예제에서는 `my-subdirectory-renamed`라는 디렉터리를 `my-directory-2`라는 디렉터리의 하위 디렉터리로 이동합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>디렉터리 삭제

**DataLakeDirectoryClient.deleteWithResponse** 메서드를 호출하여 디렉터리를 삭제합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **DataLakeFileClient.append** 메서드를 호출하여 파일을 업로드합니다. **DataLakeFileClient.FlushAsync** 메서드를 호출하여 업로드가 완료됐는지 확인합니다.

다음 예제에서는 한 텍스트 파일을 `my-directory`라는 디렉터리에 업로드합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> 파일 크기가 크다면 코드는 **DataLakeFileClient.append** 메서드를 여러 번 호출해야 합니다. 대신 **DataLakeFileClient.uploadFromFile** 메서드 사용을 고려해 보세요. 이 방법으로 단일 호출에서 전체 파일을 업로드할 수 있습니다.
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 큰 파일 업로드

**DataLakeFileClient.append** 메서드를 여러 번 호출하지 않아도 **DataLakeFileClient.uploadFromFile** 메서드를 사용해서 큰 파일을 업로드할 수 있습니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저, 다운로드하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient.read** 메서드를 사용해 파일을 읽습니다. .NET 파일 처리 API를 이용해서 스트림에서 바이트를 파일에 저장합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예시에서는 `my-directory`라는 디렉터리에 있는 각 파일의 이름을 인쇄합니다.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/java/api/overview/azure/storage-file-datalake-readme)
- [패키지(Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)
