---
title: .NET을 사용한 Azure Data Lake Storage Gen2의 데이터 관리
description: .NET용 Azure Storage 클라이언트 라이브러리를 이용해 계층 구조 네임스페이스를 사용하도록 설정한 스토리지 계정의 디렉터리와 파일을 관리합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e13e8127377e99faea8da89912ee6f3d9fb1f20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590331"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 .NET을 사용하여 디렉터리 및 파일 관리

이 문서에서는 .NET을 사용하여 계층 구조 네임스페이스가 있는 스토리지 계정에서 디렉터리 및 파일을 만들고 관리하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL(액세스 제어 목록)을 가져오거나 설정하고 업데이트하는 방법에 대한 자세한 내용은 [.NET을 사용한 Azure Data Lake Storage Gen2의 ACL 관리](data-lake-storage-acl-dotnet.md)를 참조하세요.

[패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API 참조](/dotnet/api/azure.storage.files.datalake) | [Gen1에서 Gen2로 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작하려면 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지를 설치합니다.

NuGet 패키지 설치 방법에 대한 자세한 내용은 [Visual Studio에서 NuGet 패키지 관리자를 사용하여 패키지 설치 및 관리](/nuget/consume-packages/install-use-packages-visual-studio)를 참조하세요.

코드 파일 맨 위에 다음 using 문을 추가합니다.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용하려면 스토리지 계정을 나타내는 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

계정에 연결하는 가장 쉬운 방법입니다.

이 예시에서는 계정 키를 사용하여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)를 사용하여 연결

[.NET용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)를 사용하여 Azure AD로 애플리케이션을 인증할 수 있습니다.

이 예시에서는 클라이언트 ID, 클라이언트 암호, 테넌트 ID를 사용하여 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 애플리케이션의 요청에 대한 권한 부여를 위해 Azure AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조하세요.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [.NET용 Azure ID 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일의 파일 시스템 역할을 합니다. [DataLakeServiceClient.CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) 메서드를 호출하여 만들 수 있습니다.

다음 예제에서는 `my-file-system`라는 컨테이너를 만듭니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>디렉터리 만들기

[DataLakeFileSystemClient.CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) 메서드를 호출하여 디렉터리 참조를 만듭니다.

이 예시에서는 `my-directory`라는 디렉터리를 컨테이너에 추가한 다음 `my-subdirectory`라는 하위 디렉터리를 추가합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

[DataLakeDirectoryClient.RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리의 경로를 매개 변수로 전달합니다.

이 예제에서는 한 하위 디렉터리를 `my-subdirectory-renamed`라는 이름으로 바꿉니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

이 예제에서는 `my-subdirectory-renamed`라는 디렉터리를 `my-directory-2`라는 디렉터리의 하위 디렉터리로 이동합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>디렉터리 삭제

[DataLakeDirectoryClient.Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) 메서드를 호출하여 디렉터리를 삭제합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 호출하여 파일을 업로드합니다. [DataLakeFileClient.FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) 메서드를 호출하여 업로드가 완료됐는지 확인합니다.

다음 예제에서는 한 텍스트 파일을 `my-directory`라는 디렉터리에 업로드합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> 파일 크기가 크다면 코드는 [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)를 여러 번 호출해야 합니다. 대신 [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드 사용을 고려해 보세요. 이 방법으로 단일 호출에서 전체 파일을 업로드할 수 있습니다.
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 큰 파일 업로드

[DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 여러 번 호출하지 않아도 [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용해서 큰 파일을 업로드할 수 있습니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저, 다운로드하려는 파일을 나타내는 [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) 인스턴스를 만듭니다. [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) 메서드를 사용하고 반환 값을 구문 분석하여 [스트림](/dotnet/api/system.io.stream) 개체를 획득합니다. .NET 파일 처리 API를 이용해서 스트림에서 바이트를 파일에 저장합니다.

이 예시에서는 [BinaryReader](/dotnet/api/system.io.binaryreader)와 [FileStream](/dotnet/api/system.io.filestream)을 사용해 바이트를 파일에 저장합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

[FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) 메서드를 호출하고 결과를 열거해 디렉터리 콘텐츠를 나열합니다.

이 예시에서는 `my-directory`라는 디렉터리에 있는 각 파일의 이름을 인쇄합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/dotnet/api/azure.storage.files.datalake)
- [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 제공](https://github.com/Azure/azure-sdk-for-net/issues)
