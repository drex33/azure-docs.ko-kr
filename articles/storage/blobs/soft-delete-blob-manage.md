---
title: 일시 삭제된 Blob 관리 및 복원
titleSuffix: Azure Storage
description: Azure Portal 또는 Azure Storage 클라이언트 라이브러리를 사용하여 일시 삭제된 Blob 및 스냅샷을 관리하고 복원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c66b970a99ad6dd69b9336d1638b2c156868324
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627941"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>일시 삭제된 Blob 관리 및 복원

Blob 일시 삭제는 지정된 기간 동안 시스템에서 삭제된 데이터를 유지하여 개별 Blob 및 해당 버전, 스냅샷 및 메타데이터를 실수로 삭제하거나 덮어쓰지 않도록 보호합니다. 보존 기간 중에는 삭제 시 Blob을 해당 상태로 복원할 수 있습니다. 보존 기간이 만료되면 Blob이 영구적으로 삭제됩니다. Blob 일시 삭제에 대한 자세한 내용은 [Blob 일시 삭제](soft-delete-blob-overview.md)를 참조하세요.

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="manage-soft-deleted-blobs"></a>일시 삭제된 Blob 관리

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure Portal을 사용하여 일시 삭제된 Blob 관리

Azure Portal을 사용하여 일시 삭제된 Blob 및 스냅샷을 보고 복원할 수 있습니다.

#### <a name="view-deleted-blobs"></a>삭제된 Blob 보기

Blob이 일시 삭제되면 기본적으로 Azure Portal에 표시되지 않습니다. 일시 삭제된 Blob을 보려면 컨테이너의 **개요** 페이지로 이동하고 **삭제된 Blob 표시** 설정을 토글합니다. 일시 삭제된 Blob는 **삭제됨** 상태로 표시됩니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob을 나열하는 방법을 보여 주는 스크린샷":::

그런 다음, Blob 목록에서 삭제된 Blob을 선택하 여 해당 속성을 표시합니다. **개요** 탭 아래에서 Blob의 상태가 **삭제됨** 으로 설정되어 있는지 확인합니다. 또한 포털은 Blob이 영구적으로 삭제될 때까지 남은 일 수를 표시합니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob의 속성을 보여주는 스크린샷":::

#### <a name="view-deleted-snapshots"></a>삭제된 스냅샷 표시

Blob을 삭제하면 Blob과 연결된 모든 스냅샷은 삭제됩니다. 일시 삭제된 Blob에 스냅샷이 있으면 삭제된 스냅샷이 포털에 표시될 수도 있습니다. 일시 삭제된 Blob의 속성을 표시한 다음, **스냅샷** 탭으로 이동하고 **삭제된 스냅샷 표시** 를 토글합니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="예를 보여 주는 스크린샷":::

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>버전 관리를 사용하지 않는 경우 일시 삭제된 개체 복원하기

Blob 버전 관리를 사용하지 않는 경우에 Azure Portal에서 일시 삭제된 Blob을 복원하려면, 먼저 Blob의 속성을 표시한 다음, **개요** 탭에서 **삭제 취소** 단추를 선택합니다. 또한 Blob을 복원하면 일시 삭제 보존 기간 동안 삭제된 모든 스냅샷이 복원됩니다.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob을 복원하는 방법을 보여 주는 스크린샷":::

일시 삭제된 스냅샷을 기본 Blob으로 승격하려면, 먼저 Blob의 일시 삭제된 스냅샷이 복원되었는지 확인합니다. 기본 Blob 자체가 일시 삭제되지 않은 경우에도 Blob의 일시 삭제된 스냅샷을 복원하려면 **삭제 취소** 단추를 선택합니다. 그런 다음, 승격할 스냅샷을 선택하고 **스냅샷 승격** 단추를 사용하여 기본 Blob을 스냅샷의 콘텐츠로 덮어씁니다.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="스냅샷을 기본 Blob로 승격하는 방법을 보여 주는 스크린샷":::

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>버전 관리를 사용하는 경우 일시 삭제된 Blob 복원하기

버전 관리를 사용하는 경우 Azure Portal에서 일시 삭제된 Blob을 복원하려면, 일시 삭제된 blob을 선택하여 해당 속성을 표시한 다음, **버전** 탭을 선택합니다. 현재 버전으로 승격하려는 버전을 선택하고 **현재 버전 만들기** 를 선택합니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Azure Portal에서 Blob을 복원하기 위해 버전을 승격하는 방법을 보여 주는 스크린샷":::

버전 관리를 사용하는 경우 삭제된 버전이나 스냅샷을 복원하려면 Blob의 속성을 표시한 다음, **개요** 탭에서 **삭제 취소** 단추를 선택합니다.

> [!NOTE]
> 버전 관리를 사용하는 경우 삭제된 Blob에서 **삭제 취소** 단추를 선택하면 일시 삭제된 버전이나 스냅샷이 복원되지만 기본 Blob은 복원되지 않습니다. 기본 Blob을 복원하려면 이전 버전을 승격해야 합니다.

### <a name="manage-soft-deleted-blobs-with-code"></a>코드를 사용하여 일시 삭제된 Blob 관리

Azure Storage 클라이언트 라이브러리를 사용하여 일시 삭제된 Blob 또는 스냅샷을 복원할 수 있습니다. 다음 예제에서는 .NET 클라이언트 라이브러리 사용 방법을 보여 줍니다.

#### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>버전 관리를 사용하지 않는 경우 일시 삭제된 개체 복원하기

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

버전 관리를 사용하지 않을 때 삭제된 Blob을 복원하려면 해당 Blob에 대한 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출합니다. **Blob 삭제 취소** 작업은 일시 삭제된 Blob 및 해당 Blob와 연결된 삭제된 스냅샷을 복원합니다.

삭제되지 않은 Blob에 대한 **Blob 삭제 취소** 호출은 효과가 없습니다. 다음 예제에서는 컨테이너의 모든 Blob에 대한 **Blob 삭제 취소** 를 호출하고 일시 삭제된 Blob 및 해당 스냅샷을 복원합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

일시적으로 삭제된 특정 스냅샷을 복원하려면 먼저 기본 Blob에서 **Blob 삭제 취소** 작업을 호출한 다음, 기본 Blob에 원하는 스냅샷을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 생성된 스냅샷으로 복원합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

버전 관리를 사용하지 않을 때 삭제된 Blob을 복원하려면 해당 Blob에 대한 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출합니다. **Blob 삭제 취소** 작업은 일시 삭제된 Blob 및 해당 Blob와 연결된 삭제된 스냅샷을 복원합니다.

삭제되지 않은 Blob에 대한 **Blob 삭제 취소** 호출은 효과가 없습니다. 다음 예제에서는 컨테이너의 모든 Blob에 대한 **Blob 삭제 취소** 를 호출하고 일시 삭제된 Blob 및 해당 스냅샷을 복원합니다.

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

일시적으로 삭제된 특정 스냅샷을 복원하려면 먼저 기본 Blob에서 **Blob 삭제 취소** 작업을 호출한 다음, 기본 Blob에 원하는 스냅샷을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 생성된 스냅샷으로 복원합니다.

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

#### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>버전 관리를 사용하는 경우 일시 삭제된 Blob 복원하기

버전 관리를 사용하는 경우 일시 삭제된 Blob을 복원하려면 [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업을 사용하여 기본 Blob을 통해 이전 버전을 복사합니다.

##### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

##### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

해당 사항 없음 Blob 버전 관리는 Azure Storage 클라이언트 라이브러리 버전 12.x 이상에서만 지원됩니다.

---

## <a name="manage-soft-deleted-blobs-and-directories-hierarchical-namespace"></a>일시 삭제된 Blob 및 디렉터리 관리(계층 구조 네임스페이스)

계층 구조 네임스페이스가 있는 계정에서 일시 삭제된 Blob 및 디렉터리를 복원할 수 있습니다.

> [!IMPORTANT]
> 계층 구조 네임스페이스 기능을 사용하도록 설정한 계정의 일시 삭제는 현재 미리 보기 상태로 지원되며 모든 Azure 지역에서 전역적으로 사용할 수 있습니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)을 참조하세요.

### <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure Portal을 사용하여 일시 삭제된 Blob 관리

Azure Portal을 사용하여 일시 삭제된 Blob 및 디렉터리를 보고 복원할 수 있습니다.

#### <a name="view-deleted-blobs-and-directories"></a>삭제된 Blob 및 디렉터리 보기

Blob 또는 디렉터리가 일시 삭제되면 기본적으로 Azure Portal에 표시되지 않습니다. 일시 삭제된 Blob 및 디렉터리를 보려면 컨테이너의 **개요** 페이지로 이동하고 **삭제된 Blob 표시** 설정을 토글합니다. 일시 삭제된 Blob 및 디렉터리는 **삭제됨** 상태로 표시됩니다. 다음 이미지는 일시 삭제된 디렉터리를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 일시 삭제된 Blob을 나열하는 방법(계층 구조 네임스페이스 사용 계정)을 보여주는 스크린샷](media/soft-delete-blob-manage/soft-deleted-blobs-list-portal-hns.png)

> [!NOTE]
> 일시 삭제된 항목(하위 디렉터리 및 Blob)을 포함하는 디렉터리의 이름을 바꾸면 일시 삭제된 해당 항목이 디렉터리에서 연결 해제되므로 **삭제된 Blob 표시** 설정을 전환할 때 Azure Portal에 표시되지 않습니다. Azure Portal에서 보려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다.

그런 다음, 목록에서 삭제된 디렉터리 또는 Blob을 선택하여 해당 속성을 표시합니다. **개요** 탭 아래에서 상태가 **삭제됨** 으로 설정되어 있는지 확인합니다. 또한 포털은 Blob이 영구적으로 삭제될 때까지 남은 일 수를 표시합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal(계층 구조 네임스페이스 사용 계정)에서 일시 삭제된 Blob의 속성을 보여주는 스크린샷](media/soft-delete-blob-manage/soft-deleted-blob-properties-portal-hns.png)

#### <a name="restore-soft-delete-blobs-and-directories"></a>일시 삭제된 Blob 및 디렉터리 복원

Azure Portal에서 일시 삭제된 Blob 또는 디렉터리를 복원하려면 먼저 Blob 또는 디렉터리의 속성을 표시한 다음, **개요** 탭에서 **삭제 취소** 단추를 선택합니다. 다음 이미지는 일시 삭제된 디렉터리의 삭제 취소 단추를 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal(계층 구조 네임스페이스 사용 계정)에서 일시 삭제된 Blob을 복원하는 방법을 보여주는 스크린샷](media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal-hns.png)

### <a name="restore-soft-deleted-blobs-and-directories-by-using-powershell"></a>PowerShell을 사용하여 일시 삭제된 Blob 및 디렉터리 복원

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.

1. **Az.Storage** 미리 보기 모듈이 설치되어 있는지 확인합니다. 자세한 내용은 [PowerShell을 통해 Blob 일시 삭제 사용을](soft-delete-blob-enable.md?tabs=azure-powershell#enable-blob-soft-delete-hierarchical-namespace)참조하세요.

2. 스토리지 계정 키, 연결 문자열 또는 Azure AD(Azure Active Directory)를 사용하여 스토리지 계정 권한을 부여합니다. 자세한 내용은 [계정에 커넥트 참조하세요.](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)

   다음 예제에서는 스토리지 계정 키를 사용하여 권한을 부여합니다.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

3. 일시 삭제된 항목을 복원하려면 `Restore-AzDataLakeGen2DeletedItem` 명령을 사용합니다.

   ```powershell
   $filesystemName = "my-file-system"
   $dirName="my-directory"
   $deletedItems = Get-AzDataLakeGen2DeletedItem -Context $ctx -FileSystem $filesystemName -Path $dirName
   $deletedItems | Restore-AzDataLakeGen2DeletedItem
   ```

   일시 삭제된 항목을 포함하는 디렉터리의 이름을 바꾸면 해당 항목은 디렉터리에서 분리됩니다. 해당 항목을 복원하려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그러지 않고 일시 삭제된 해당 항목을 복원하려고 하면 오류가 표시됩니다.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-azure-cli"></a>Azure CLI를 사용하여 일시 삭제된 Blob 및 디렉터리 복원

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.

1. `storage-preview` 확장이 설치되어 있는지 확인합니다. 자세한 내용은 [PowerShell을 사용하여 Blob 일시 삭제 사용을](soft-delete-blob-enable.md?tabs=azure-CLI#enable-blob-soft-delete-hierarchical-namespace)참조하세요.

2. 삭제한 항목의 목록을 가져옵니다.

   ```azurecli
   $filesystemName = "my-file-system"
   az storage fs list-deleted-path -f $filesystemName --auth-mode login
   ```

3. 항목을 복원하려면 `az storage fs undelete-path` 명령을 사용합니다.

   ```azurecli
   $dirName="my-directory"
   az storage fs undelete-path -f $filesystemName --deleted-path-name $dirName -deletion-id "<deletionId>" --auth-mode login
   ```

   일시 삭제된 항목을 포함하는 디렉터리의 이름을 바꾸면 해당 항목은 디렉터리에서 분리됩니다. 해당 항목을 복원하려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그러지 않고 일시 삭제된 해당 항목을 복원하려고 하면 오류가 표시됩니다.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-net"></a>.NET을 사용하여 일시 삭제된 Blob 및 디렉터리 복원

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.

1. 명령 프롬프트를 열고 디렉터리(`cd`)를 프로젝트 폴더로 변경합니다. 예는 다음과 같습니다.

   ```console
   cd myProject
   ```

2. `dotnet add package` 명령을 사용하여 [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지의 `Azure.Storage.Files.DataLake -v 12.7.0` 버전을 설치합니다.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v -v 12.7.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 코드 파일 맨 위에 다음 using 문을 추가합니다.

    ```csharp
    using Azure;
    using Azure.Storage;
    using Azure.Storage.Files.DataLake;
    using Azure.Storage.Files.DataLake.Models;
    using NUnit.Framework;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

4. 다음 코드는 디렉터리를 삭제한 다음, 일시 삭제된 디렉터리를 복원합니다.

   이 메서드는 [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들었다고 가정합니다. [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient)  인스턴스를 만드는 방법을 알아보려면 [계정에 연결](data-lake-storage-directory-file-acl-dotnet.md#connect-to-the-account)을 참조하세요.

   ```csharp
      public void RestoreDirectory(DataLakeServiceClient serviceClient)
      {
          DataLakeFileSystemClient fileSystemClient =
             serviceClient.GetFileSystemClient("my-container");

          DataLakeDirectoryClient directory =
              fileSystem.GetDirectoryClient("my-directory");

          // Delete the Directory
          await directory.DeleteAsync();

          // List Deleted Paths
          List<PathHierarchyDeletedItem> deletedItems = new List<PathHierarchyDeletedItem>();
          await foreach (PathHierarchyDeletedItem deletedItem in fileSystemClient.GetDeletedPathsAsync())
          {
            deletedItems.Add(deletedItem);
          }

          Assert.AreEqual(1, deletedItems.Count);
          Assert.AreEqual("my-directory", deletedItems[0].Path.Name);
          Assert.IsTrue(deletedItems[0].IsPath);

          // Restore deleted directory.
          Response<DataLakePathClient> restoreResponse = await fileSystemClient.RestorePathAsync(
          deletedItems[0].Path.Name,
          deletedItems[0].Path.DeletionId);

      }

   ```

   일시 삭제된 항목을 포함하는 디렉터리의 이름을 바꾸면 해당 항목은 디렉터리에서 분리됩니다. 해당 항목을 복원하려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그러지 않고 일시 삭제된 해당 항목을 복원하려고 하면 오류가 표시됩니다.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-java"></a>Java를 사용하여 일시 삭제된 Blob 및 디렉터리 복원

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.

1. 시작하려면 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-storage-file-datalake</artifactId>
     <version>12.6.0</version>
   </dependency>
   ```

2. 그런 다음, 이러한 imports 문을 코드 파일에 추가합니다.

   ```java
   Put imports here
   ```

3. 다음 코드 조각은 `my-file`이라는 일시 삭제된 파일을 복원합니다.

   이 메서드는 **DataLakeServiceClient** 인스턴스를 만들었다고 가정합니다. **DataLakeServiceClient**  인스턴스를 만드는 방법을 알아보려면 [계정에 연결](data-lake-storage-directory-file-acl-java.md#connect-to-the-account)을 참조하세요.

   ```java

   public void RestoreFile(DataLakeServiceClient serviceClient){

       DataLakeFileSystemClient fileSystemClient =
           serviceClient.getFileSystemClient("my-container");

       DataLakeFileClient fileClient =
           fileSystemClient.getFileClient("my-file");

       String deletionId = null;

       for (PathDeletedItem item : fileSystemClient.listDeletedPaths()) {

           if (item.getName().equals(fileClient.getFilePath())) {
              deletionId = item.getDeletionId();
           }
       }

       fileSystemClient.restorePath(fileClient.getFilePath(), deletionId);
    }

   ```

   일시 삭제된 항목을 포함하는 디렉터리의 이름을 바꾸면 해당 항목은 디렉터리에서 분리됩니다. 해당 항목을 복원하려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그러지 않고 일시 삭제된 해당 항목을 복원하려고 하면 오류가 표시됩니다.

### <a name="restore-soft-deleted-blobs-and-directories-by-using-python"></a>Python을 사용하여 일시 삭제된 Blob 및 디렉터리 복원

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.

1. [pip](https://pypi.org/project/pip/)를 사용하여 Python용 Azure Data Lake Storage 클라이언트 라이브러리를 `12.4.0` 이상 버전을 설치합니다. 이 명령은 Python용 Azure Data Lake Storage 클라이언트 라이브러리의 최신 버전을 설치합니다.

   ```
   pip install azure-storage-file-datalake
   ```

2. 코드 파일 맨 위에 다음 import 문을 추가합니다.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.storage.filedatalake import FileSystemClient
   ```

3. 다음 코드는 디렉터리를 삭제한 다음, 일시 삭제된 디렉터리를 복원합니다.

   아래 코드 예제에는 **DataLakeServiceClient** 형식의 `service_client`라는 개체가 포함되어 있습니다. **DataLakeServiceClient** 인스턴스를 만드는 방법의 예제를 보려면 [계정에 연결](data-lake-storage-directory-file-acl-python.md#connect-to-the-account)을 참조하세요.

    ```python
    def restoreDirectory():

        try:
            global file_system_client

            file_system_client = service_client.create_file_system(file_system="my-file-system")

            directory_path = 'my-directory'
            directory_client = file_system_client.create_directory(directory_path)
            resp = directory_client.delete_directory()

            restored_directory_client = file_system_client.undelete_path(directory_client, resp['deletion_id'])
            props = restored_directory_client.get_directory_properties()

            print(props)

        except Exception as e:
            print(e)

    ```

   일시 삭제된 항목을 포함하는 디렉터리의 이름을 바꾸면 해당 항목은 디렉터리에서 분리됩니다. 해당 항목을 복원하려는 경우 디렉터리의 이름을 원래 이름으로 다시 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그러지 않고 일시 삭제된 해당 항목을 복원하려고 하면 오류가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Blob Storage에 대한 일시 삭제](soft-delete-blob-overview.md)
- [Blob에 일시 삭제를 사용하도록 설정](soft-delete-blob-enable.md)
- [Blob 버전 관리](versioning-overview.md)
