---
title: .NET을 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 데이터를 저장하는 .NET 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f82598d854f323f69d4575a86f0a1808c97906c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258115"
---
# <a name="develop-for-azure-files-with-net"></a>.NET을 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[Azure Files](storage-files-introduction.md)를 사용하여 데이터를 저장하는 .NET 애플리케이션 개발에 대한 기본 사항을 알아봅니다. 이 문서에서는 .NET 및 Azure Files를 사용하여 다음 작업을 수행하는 간단한 콘솔 애플리케이션을 만드는 방법을 보여 줍니다.

- 파일 콘텐츠 가져오기
- 파일 공유의 최대 크기 또는 할당량 설정
- 파일에 대한 SAS(공유 액세스 서명) 만들기
- 동일한 스토리지 계정의 다른 파일로 파일 복사
- 동일한 스토리지 계정의 blob으로 파일 복사
- 파일 공유 스냅샷 만들기
- 공유 스냅샷에서 파일 복원
- 문제 해결을 위해 Azure Storage 메트릭 사용

Azure Files에 대한 자세한 내용은 [Azure Files란?](storage-files-introduction.md)을 참조하세요.

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="understanding-the-net-apis"></a>.NET API 이해

Azure Files는 클라이언트 애플리케이션에 SMB(서버 메시지 블록) 및 REST라는 광범위한 두 가지 방법을 제공합니다. .NET 내에서는 `System.IO` 및 `Azure.Storage.Files.Shares` API가 이 접근 방식을 추상화합니다.

API | 사용 시기 | 메모
----|-------------|------
[System.IO](/dotnet/api/system.io) | 사용자 애플리케이션의 경우: <ul><li>SMB를 사용하여 파일을 읽고 써야 합니다.</li><li>포트 445를 통해 Azure Files 계정에 대한 액세스 권한이 있는 디바이스에서 실행됩니다.</li><li>파일 공유의 관리 설정을 관리할 필요가 없습니다.</li></ul> | SMB를 통해 Azure Files를 사용하여 구현된 파일 I/O는 일반적으로 네트워크 파일 공유 또는 로컬 스토리지 디바이스를 사용하는 I/O와 동일합니다. 파일 I/O를 비롯한 .NET의 다양한 기능에 대한 소개는 [콘솔 애플리케이션](/dotnet/csharp/tutorials/console-teleprompter) 자습서를 참조하세요.
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | 사용자 애플리케이션의 경우: <ul><li>방화벽이나 ISP 제약 조건으로 인해 포트 445에서 SMB를 사용하여 Azure Files에 액세스할 수 없습니다.</li><li>파일 공유 할당량을 설정하거나 공유 액세스 서명을 만들 수 있는 기능 등 관리 기능이 필요합니다.</li></ul> | 이 문서에서는 `Azure.Storage.Files.Shares`를 사용하여 SMB가 아닌 REST로 파일 I/O를 수행하고 파일 공유를 관리하는 방법을 보여줍니다.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>콘솔 애플리케이션 만들기 및 어셈블리 가져오기

모든 유형의 .NET 앱에서 Azure Files 클라이언트 라이브러리를 사용할 수 있습니다. 해당 앱에는 Azure 클라우드, 웹, 데스크톱, 모바일 앱이 포함됩니다. 이 가이드에서는 편의상 콘솔 애플리케이션을 만듭니다.

Visual Studio에서 새로운 Windows 콘솔 애플리케이션을 만듭니다. 다음 단계에서는 Visual Studio 2019에서 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 이러한 단계는 다른 버전의 Visual Studio에서도 비슷합니다.

1. Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고 **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 앱 이름을 입력하고 **만들기** 를 선택합니다.

*Program.cs* 파일의 `Program` 클래스에 이 문서의 모든 코드 예제를 추가합니다.

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet을 사용하여 필요한 패키지 설치

프로젝트에서 다음 패키지를 참조하세요.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

- [.NET용 Azure 코어 라이브러리](https://www.nuget.org/packages/Azure.Core/): 이 패키지는 Azure 클라이언트 파이프라인의 구현입니다.
- [.NET용 Azure Storage Blob 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Storage.Blobs/): 이 패키지는 스토리지 계정의 Blob 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
- [.NET용 Azure Storage 파일 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): 이 패키지는 스토리지 계정의 파일 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
- [.NET용 시스템 Configuration Manager 라이브러리](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): 이 패키지는 구성 파일의 클래스 저장 및 검색 값을 제공합니다.

NuGet을 사용하여 패키지를 가져올 수 있습니다. 다음 단계를 수행합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
1. **NuGet 패키지 관리자** 에서 **찾아보기** 를 선택합니다. 그런 다음, **Azure.Core** 를 검색하여 선택하고 **설치** 를 선택합니다.

   이 단계에서는 패키지 및 해당 종속성을 설치합니다.

1. 다음 패키지를 검색하고 설치합니다.

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

- [.NET용 Microsoft Azure Storage 일반 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): 이 패키지는 스토리지 계정의 일반 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
- [.NET용 Microsoft Azure Storage Blob 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): 이 패키지는 스토리지 계정의 Blob 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
- [.NET용 Microsoft Azure Storage 파일 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): 이 패키지는 스토리지 계정의 파일 리소스에 대한 프로그래밍 방식의 액세스를 제공합니다.
- [.NET용 Microsoft Azure Configuration Manager 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): 이 패키지는 애플리케이션 실행 위치에 관계없이 구성 파일의 연결 문자열을 구문 분석하는 데 사용되는 클래스를 제공합니다.

NuGet을 사용하여 패키지를 가져올 수 있습니다. 다음 단계를 수행합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
1. **NuGet 패키지 관리자** 에서 **찾아보기** 를 선택합니다. 그런 다음, **Microsoft.Azure.Storage.Blob** 을 검색하여 선택하고 **설치** 를 선택합니다.

   이 단계에서는 패키지 및 해당 종속성을 설치합니다.
1. 다음 패키지를 검색하고 설치합니다.

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>App.config 파일에 스토리지 계정 자격 증명 저장

다음에는 프로젝트의 *App.config* 파일에 자격 증명을 저장합니다. **솔루션 탐색기** 에서 `App.config`를 두 번 클릭하여 다음 예제와 유사해지도록 파일을 편집합니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

`myaccount`를 스토리지 계정 이름으로 바꾸고 `mykey`를 스토리지 계정 키로 바꿉니다.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

`myaccount`를 스토리지 계정 이름으로 바꾸고 `StorageAccountKeyEndingIn==`를 스토리지 계정 키로 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> Azure 스토리지 에뮬레이터에서는 현재 Azure Files를 지원하지 않습니다. Azure Files를 사용하려면 연결 문자열이 클라우드에 있는 Azure Storage 계정을 대상으로 해야 합니다.

## <a name="add-using-directives"></a>지시문을 사용하여 추가

**솔루션 탐색기** 에서 *Program.cs* 파일을 열고 지시문을 사용하여 다음을 파일 맨 위에 추가합니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>프로그래밍 방식으로 파일 공유 액세스

*Program.cs* 파일에서 다음 코드를 추가하여 파일 공유에 프로그래밍 방식으로 액세스합니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

다음 메서드는 아직 파일 공유가 없는 경우 파일 공유를 만듭니다. 메서드는 연결 문자열에서 [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) 개체를 만들어 시작합니다. 그러면 샘플이 이전에 만든 파일을 다운로드하려고 시도합니다. `Main()`에서 이 메서드를 호출합니다.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

다음에는 위에 표시된 코드 뒤 `Main()` 메서드에 다음 콘텐츠를 추가하여 연결 문자열을 검색합니다. 이 코드는 이전에 만든 파일에 대한 참조를 가져오고 해당 콘텐츠를 출력합니다.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

콘솔 애플리케이션을 실행하여 출력을 확인합니다.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>파일 공유에 대한 최대 크기 설정

Azure Files 클라이언트 라이브러리 버전 5.x부터 파일 공유에 대한 할당량(또는 최대 크기)을 설정할 수 있습니다. 또한 공유에 현재 저장된 데이터의 양도 확인할 수 있습니다.

공유에 대한 할당량을 설정하여 공유에 저장되는 파일의 총 크기를 제한합니다. 공유에 있는 파일의 총 크기가 할당량을 초과하면 클라이언트는 기존 파일의 크기를 늘릴 수 없습니다. 또한, 파일이 비어 있지 않으면 클라이언트는 새 파일을 만들 수 없습니다.

아래 예제에서는 공유에 대한 현재 사용량을 확인하고 공유에 대해 할당량을 설정하는 방법을 보여 줍니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GiB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GiB.
    // This line sets the quota to be 10 GiB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GiB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>파일 또는 파일 공유에 대한 공유 액세스 서명 생성

Azure Files 클라이언트 라이브러리 버전 5.x부터 파일 공유 또는 개별 파일에 대한 SAS(공유 액세스 서명)를 생성할 수 있습니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

다음 예제 메서드는 지정된 공유에 있는 파일에 대한 SAS를 반환합니다.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

파일 공유에 대한 저장된 액세스 정책을 만들어 공유 액세스 서명을 관리할 수도 있습니다. SAS가 손상되면 SAS를 철회할 수 있으므로 저장된 액세스 정책을 만드는 것이 좋습니다. 다음 예제에서는 공유에 대한 저장된 액세스 정책을 만듭니다. 이 예제에서는 해당 정책을 사용하여 공유에 있는 파일에 대한 SAS 제약 조건을 제공합니다.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

공유 액세스 서명을 만들고 사용하는 방법에 대한 자세한 내용은 [공유 액세스 서명 사용 방법](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)을 참조하세요.

## <a name="copy-files"></a>파일 복사

Azure Files 클라이언트 라이브러리 버전 5.x부터 파일을 다른 파일로 복사하거나 파일을 Blob으로 복사하거나 Blob을 파일로 복사할 수 있습니다.

AzCopy를 사용하여 파일을 다른 파일로 복사하거나 Blob을 파일로 복사하거나 그 반대로 복사할 수도 있습니다. [AzCopy 시작](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

> [!NOTE]
> BLOB을 파일에 복사하거나 파일을 BLOB에 복사하는 경우 두 항목이 동일한 스토리지 계정 내에 있더라도 SAS(공유 액세스 서명)를 사용하여 원본 개체에 대한 액세스를 인증해야 합니다.

### <a name="copy-a-file-to-another-file"></a>파일을 다른 파일에 복사

다음 예제에서는 파일을 동일한 공유의 다른 파일에 복사합니다. [공유 키 인증](/rest/api/storageservices/authorize-with-shared-key)을 사용하여 복사를 수행할 수 있습니다. 이 작업에서는 동일한 스토리지 계정 내에서 파일을 복사하기 때문입니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>파일을 Blob에 복사

다음 예제에서는 파일을 만들고 동일한 스토리지 계정 내의 blob에 복사합니다. 이 예제에서 서비스는 복사 작업 동안 원본 파일에 대한 액세스를 인증하는 데 사용하는 소스 파일용 SAS를 만듭니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

동일한 방식으로 blob을 파일에 복사할 수 있습니다. 원본 개체가 BLOB인 경우 복사 작업 동안 해당 BLOB에 대한 액세스를 인증하는 SAS를 만듭니다.

## <a name="share-snapshots"></a>공유 스냅샷

Azure Files 클라이언트 라이브러리 버전 8.5부터 공유 스냅샷을 만들 수 있습니다. 또한 공유 스냅샷을 나열하거나 찾고 삭제할 수도 있습니다. 만든 공유 스냅샷은 읽기 전용입니다.

### <a name="create-share-snapshots"></a>공유 스냅샷 만들기

다음 예제에서는 파일 공유 스냅샷을 만듭니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>공유 스냅샷 나열

다음 예제에서는 공유의 스냅샷을 나열합니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>공유 스냅샷 내 파일 및 디렉터리 나열

다음 예제에서는 공유 스냅샷 내 파일 및 디렉터리를 찾아봅니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>공유 스냅샷에서 파일 공유 또는 파일 복원

파일 공유 스냅샷을 만들면 개별 파일 또는 전체 파일 공유를 복구할 수 있습니다.

파일 공유의 공유 스냅샷을 쿼리하여 파일 공유 스냅샷의 파일을 복원할 수 있습니다. 그런 다음 특정 공유 스냅샷에 속하는 파일을 검색할 수 있습니다. 해당 버전을 사용하여 파일을 직접 읽거나 복원할 수 있습니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>공유 스냅샷 삭제

다음 예제에서는 파일 공유 스냅샷을 삭제합니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>메트릭을 사용하여 Azure Files 문제 해결<a name="troubleshooting-azure-files-using-metrics"></a>

Azure 스토리지 분석은 Azure Files에 대한 메트릭을 지원합니다. 메트릭 데이터를 사용하여 요청을 추적하고 문제를 진단할 수 있습니다.

[Azure Portal](https://portal.azure.com)에서 Azure Files에 대한 메트릭을 사용하도록 설정할 수 있습니다. REST API 또는 Azure Files 클라이언트 라이브러리의 유사한 기능 중 하나로 [파일 서비스 속성 설정](/rest/api/storageservices/set-file-service-properties) 작업을 호출하여 프로그래밍 방식으로 메트릭을 사용하도록 설정할 수도 있습니다.

다음 코드 예제에서는 .NET 클라이언트 라이브러리를 사용하여 Azure Files에 대한 메트릭을 사용하도록 설정하는 방법을 보여 줍니다.

# <a name="azure-net-sdk-v12"></a>[Azure \.NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \.NET SDK v11](#tab/dotnetv11)

먼저, 위에서 추가한 내용과 함께 다음 `using` 지시문을 *Program.cs* 파일에 추가합니다.

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Azure Blob, Azure Table, Azure Queue는 공유 `ServiceProperties` 형식을 `Microsoft.Azure.Storage.Shared.Protocol` 네임스페이스에서 사용하지만 Azure Files는 고유 형식인 `FileServiceProperties` 형식을 `Microsoft.Azure.Storage.File.Protocol` 네임스페이스에서 사용합니다. 하지만 다음 코드를 컴파일하려면 코드에서 네임스페이스를 둘 다 참조해야 합니다.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

문제가 발생하는 경우 [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)을 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Files에 대한 자세한 내용은 다음 리소스를 참조하세요.

### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오

- [Azure Files: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)(Azure Files: Windows 및 Linux를 위한 원활한 클라우드 SMB 파일 시스템)
- [Linux에서 Azure Files 사용](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage용 도구 지원

- [AzCopy 시작](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Windows에서 Azure Files 문제 해결](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>참조

- [.NET용 Azure Storage API](/dotnet/api/overview/azure/storage)
- [File Service REST API](/rest/api/storageservices/File-Service-REST-API)