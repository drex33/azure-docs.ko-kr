---
title: 리소스 파일 생성 및 사용
description: 다양한 입력 소스에서 Batch 리소스 파일을 만드는 방법을 알아봅니다. 이 문서에서는 이를 만들고 VM에 추가하는 몇 가지 일반적인 방법을 설명합니다.
ms.date: 08/18/2021
ms.topic: how-to
ms.openlocfilehash: a4939cc6c60d226d8b75569ab08447973968735a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568306"
---
# <a name="creating-and-using-resource-files"></a>리소스 파일 생성 및 사용

Azure Batch 태스크를 처리하려면 일부 데이터 형식이 필요한 경우가 많습니다. 리소스 파일을 사용하면 태스크를 통해 Batch VM(가상 머신)에 이러한 데이터를 제공할 수 있습니다. 모든 유형의 태스크는 태스크, 시작 태스크, 작업 준비 태스크, 작업 릴리스 태스크 등의 리소스 파일을 지원합니다. 이 문서에서는 리소스 파일을 만들고 VM에 추가하는 몇 가지 일반적인 방법을 설명합니다.  

리소스 파일은 Batch VM에 데이터를 추가하지만 데이터 유형과 사용 방식은 자유롭습니다. 그러나 다음과 같은 몇 가지 일반적인 사용 사례가 있습니다.

- 시작 작업에서 리소스 파일을 사용하여 각 VM에 공용 파일 프로비전
- 작업으로 처리할 입력 데이터 프로비전

예를 들어 공용 파일은 태스크가 실행하는 애플리케이션을 설치하는 데 사용되는 시작 태스크의 파일일 수 있습니다. 입력 데이터는 원시 이미지 또는 동영상 데이터이거나 Batch가 처리할 정보일 수 있습니다.

## <a name="types-of-resource-files"></a>리소스 파일 형식

리소스 파일을 생성하는 데 사용할 수 있는 몇 가지 옵션이 있으며, 이 옵션은 각각 고유한 [수단](/dotnet/api/microsoft.azure.batch.resourcefile#methods)을 사용합니다. 리소스 파일 생성 프로세스는 원래 데이터가 저장되는 위치와 여러 파일이 생성되어야 하는지 여부에 따라 달라집니다.

- [스토리지 컨테이너 URL](#storage-container-url): Azure의 모든 스토리지 컨테이너에서 리소스 파일을 생성합니다.
- [스토리지 컨테이너 이름](#storage-container-name-autostorage): 배치 계정(autostorage 계정)에 연결된 Azure storage 계정의 컨테이너 이름으로 리소스 파일을 생성합니다.
- [웹 엔드포인트의 단일 리소스 파일](#single-resource-file-from-web-endpoint): 모든 유효한 HTTP URL에서 단일 리소스 파일을 생성합니다.

### <a name="storage-container-url"></a>스토리지 컨테이너 URL

스토리지 컨테이너 URL을 사용하면 올바른 권한을 가지고 Azure의 모든 스토리지 컨테이너 파일에 액세스할 수 있습니다.

이 C# 예제에서는 Blob Storage인 Azure 스토리지 컨테이너에 파일이 이미 업로드되었습니다. 리소스 파일을 만드는 데 필요한 데이터에 액세스하려면 먼저 스토리지 컨테이너에 액세스할 수 있어야 합니다. 이 작업은 몇 가지 방법으로 수행할 수 있습니다.

#### <a name="shared-access-signature"></a>공유 액세스 서명

스토리지 컨테이너에 액세스할 수 있는 올바른 권한을 사용하여 SAS(공유 액세스 서명) URI를 만듭니다. SAS의 만료 시간 및 사용 권한을 설정합니다. 이 경우 시작 시간은 지정되지 않으므로 SAS가 즉시 유효해지고 생성 후 2시간이 지나면 만료됩니다.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 컨테이너 액세스의 경우 `Read` 및 `List` 권한이 모두 있어야 하지만 Blob 액세스 권한이 있으면 `Read` 권한만 있으면 됩니다.

사용 권한이 구성되면 SAS 토큰을 만들고 스토리지 컨테이너에 액세스할 수 있도록 SAS URL의 형식을 지정합니다. 스토리지 컨테이너용으로 형식이 지정된 SAS URL을 사용하여 [FromStorageContainerUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl)을 통해 리소스 파일을 생성합니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

원하는 경우 [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) 속성을 사용하여 이름이 지정된 접두사로 시작하는 BLOB로만 다운로드를 제한할 수 있습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl, blobPrefix = yourPrefix);
```

#### <a name="managed-identity"></a>관리 ID

[사용자 할당 관리 ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 만들어 Azure Storage 컨테이너에 대한 `Storage Blob Data Reader` 역할에 할당합니다. 그런 다음, VM이 해당 ID에 액세스할 수 있도록 [풀에 관리 ID를 할당](managed-identity-pools.md)합니다. 마지막으로, Batch에서 사용할 ID를 지정하여 컨테이너의 파일에 액세스할 수 있습니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(container.Uri, identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name" });
```

#### <a name="public-access"></a>퍼블릭 액세스

SAS URL을 생성하거나 관리 ID를 사용하는 대신 컨테이너 및 Azure Blob Storage의 해당 Blob에 대한 익명의 공용 읽기 권한을 사용하도록 설정할 수 있습니다. 이렇게 하면 계정 키를 공유하지 않고 SAS를 요구하지 않고도 이러한 리소스에 대해 읽기 전용 권한을 부여할 수 있습니다. 퍼블릭 액세스 권한은 일반적으로 특정 Blob을 항상 익명 읽기 액세스에 사용할 수 있게 하려는 경우에 사용됩니다. 이 시나리오가 솔루션에 적합한 경우 [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 액세스 구성](../storage/blobs/anonymous-read-access-configure.md)에서 Blob 데이터에 대한 액세스 관리에 대해 자세히 알아보세요.

### <a name="storage-container-name-autostorage"></a>사용자 스토리지 컨테이너 이름(autostorage)

SAS URL을 구성하고 만드는 대신 Azure Storage 컨테이너의 이름을 사용하여 Blob 데이터에 액세스할 수 있습니다. 사용하는 스토리지 컨테이너는 배치 계정에 연결된 Azure Storage 계정에 있어야 하며, 간혹 *autostorage 계정* 으로 참조됩니다. autostorage 컨테이너를 사용하면 SAS URL을 구성하고 생성하지 않고도 스토리지 컨테이너에 액세스할 수 있습니다. 대신 연결된 스토리지 계정에서 스토리지 컨테이너의 이름을 제공합니다.

autostorage 계정이 없는 경우 [배치 계정 만들기](batch-account-create-portal.md)에서 계정을 만들고 스토리지 계정을 연결하는 방법에 대한 단계별 세부 정보를 참조하세요.

다음 예제에서는 [AutoStorageContainer](/dotnet/api/microsoft.azure.batch.resourcefile.fromautostoragecontainer)를 사용하여 autostorage 계정의 데이터에서 파일을 생성합니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

스토리지 컨테이너 URL과 마찬가지로 [blobPrefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) 속성을 사용하여 다운로드할 BLOB을 지정할 수 있습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName, blobPrefix = yourPrefix);
```

### <a name="single-resource-file-from-web-endpoint"></a>웹 엔드포인트의 단일 리소스 파일

단일 리소스 파일을 만들려면 입력 데이터를 포함하는 유효한 HTTP URL을 지정합니다. 이 URL이 Batch API에 제공되면 데이터를 사용하여 리소스 파일이 생성됩니다. 이 메서드는 리소스 파일을 만드는 데이터가 Azure Storage에 있거나 GitHub 엔드포인트와 같은 기타 웹 위치에 있는 모든 경우에 사용할 수 있습니다.

다음 예제에서는 [FromUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromurl)을 사용하여 유효한 URL이 포함된 문자열에서 파일을 검색한 후 작업에서 사용할 리소스 파일을 생성합니다. 이 경우에는 자격 증명이 필요하지 않습니다. (BLOB 컨테이너의 공용 읽기 권한을 사용하도록 설정하지 않은 경우 BLOB Storage를 사용하려면 자격 증명이 필요합니다.)

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURL, filePath);
```

URL로 정의한 문자열(또는 함께 파일에 대한 전체 URL을 만드는 문자열 조합)을 만들 수도 있습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourDomain + yourFile, filePath);
```

파일이 Azure Storage에 있는 경우 리소스 파일에 대한 공유 액세스 서명을 생성하는 대신 관리 ID를 사용할 수 있습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURLFromAzureStorage, 
    identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"},
    filePath: filepath
);
```

> [!Note]
> 관리 ID 인증은 Azure Storage의 파일에만 적용됩니다. 관리 ID에는 파일이 들어 있는 컨테이너에 대해 `Storage Blob Data Reader` 역할 할당이 필요하며 [Batch 풀에도 할당](managed-identity-pools.md)되어야 합니다.

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

Azure Batch 작업은 여러 가지 방법으로 파일을 사용할 수 있습니다 .이 경우 Batch는 작업에서 파일을 관리하기 위한 다양한 옵션을 제공합니다. 다음 시나리오는 포괄적이지는 않지만 몇 가지 일반적인 상황에서의 권장 사항을 제공합니다.

### <a name="many-resource-files"></a>여러 리소스 파일

일괄 작업에서 일반적인 작업 파일을 여러 작업에서 공유하는 경우 [애플리케이션 패키지](batch-application-packages.md)를 사용하여 해당 파일을 포함할 수 있습니다. 애플리케이션 패키지는 다운로드 속도를 최적화하고, 애플리케이션 패키지의 데이터는 작업 간에 캐시 됩니다. 애플리케이션 패키지를 사용하면 여러 리소스 파일을 수동으로 관리하거나 SAS URL을 생성하여 Azure Storage의 파일에 액세스할 필요가 없습니다. Batch는 Azure Storage와 함께 백그라운드에서 작동하여 애플리케이션 패키지를 저장하고 컴퓨팅 노드에 배포합니다. 작업 파일이 자주 변경되지 않는다면 애플리케이션 패키지가 솔루션에 적합할 수 있습니다.

반대로, 해당 작업에 고유한 파일이 여러 개 있는 경우 리소스 파일을 선택하는 것이 최고의 옵션입니다. 각 작업에 고유한 파일이 많은 경우 고유한 파일을 사용하는 태스크를 자주 업데이트하거나 교체해야 해서 애플리케이션 패키지 콘텐츠를 사용하기 쉽지 않으므로 리소스 파일이 가장 좋습니다. 리소스 파일은 개별 파일의 업데이트, 추가 또는 편집에 대한 추가 유연성을 제공합니다.

### <a name="number-of-resource-files-per-task"></a>태스크당 리소스 파일 수

작업에서 수백 개의 리소스 파일을 지정하는 경우 Batch에서 작업량이 너무 많아 거부할 수 있습니다. 태스크 자체에서 리소스 파일 수를 최소화하여 태스크를 작게 유지하는 것이 가장 좋습니다.

태스크에 필요한 파일 수를 최소화할 방법이 없는 경우 리소스 파일의 스토리지 컨테이너를 참조하는 단일 리소스 파일을 만들어 태스크를 최적화할 수 있습니다. 이렇게 하려면 리소스 파일을 Azure Storage 컨테이너에 넣고 위에서 설명한 방법 중 하나를 사용하여 필요에 따라 리소스 파일을 생성합니다.

## <a name="next-steps"></a>다음 단계

- 리소스 파일의 대체 옵션인 [애플리케이션 패키지](batch-application-packages.md)에 대해 알아봅니다.
- 리소스 파일에 [컨테이너를 사용하는 방법](batch-docker-container-workloads.md)을 알아봅니다.
- 작업에서 출력 데이터를 수집하고 저장하는 방법을 알아보려면 [작업 및 태스크 출력 수집 및 유지](batch-task-output.md)를 참조하세요.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
