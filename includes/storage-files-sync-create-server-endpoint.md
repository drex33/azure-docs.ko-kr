---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 47b520fb4817f8fb8e780d52ef7a040617fdd759
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768589"
---
# <a name="portal"></a>[포털](#tab/azure-portal)
서버 엔드포인트를 추가하려면 새로 만든 동기화 그룹으로 이동한 후 **서버 엔드포인트 추가** 를 클릭합니다.

![동기화 그룹 창에서 새 서버 엔드포인트 추가](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

**서버 엔드포인트 추가** 브레이드가 열리면 다음 정보를 입력하여 서버 엔드포인트를 만듭니다.

- **등록된 서버**: 서버 엔드포인트를 만들 서버 또는 클러스터의 이름입니다.
- **경로**: 동기화 그룹의 일부분으로 동기화할 Windows Server 경로입니다.
- **클라우드 계층화**: 클라우드 계층화를 사용하거나 사용하지 않도록 설정할 스위치입니다. 클라우드 계층화를 사용하면 드물게 사용하거나 액세스하는 파일은 Azure 파일로 계층화할 수 있습니다.
- **사용 가능한 볼륨 공간**: 서버 엔드포인트가 있는 볼륨에서 예약할 여유 공간의 크기입니다. 예를 들어 서버 엔드포인트가 하나만 있는 볼륨에서 사용 가능한 볼륨 공간을 50%로 설정하는 경우 데이터 양의 절반 정도가 Azure Files로 계층화됩니다. 클라우드 계층화를 사용하도록 설정할지 여부에 관계없이, Azure 파일 공유는 항상 동기화 그룹에 데이터의 전체 복사본을 유지합니다.
- **초기 다운로드 모드**: Azure 파일 공유에는 파일이 있지만 서버에는 없는 경우 유용할 수 있는 선택적 선택 사항입니다. 예를 들어 동기화 그룹에 다른 지점 서버를 추가하기 위해 서버 엔드포인트를 만들거나 오류가 발생한 서버를 재해 복구하는 경우 이러한 상황이 발생할 수 있습니다. 클라우드 계층화를 사용하는 경우 기본적으로 처음에 파일 콘텐츠가 없는 네임스페이스만 회수합니다. 이는 사용자 액세스 요청이 서버에 회수되는 파일 콘텐츠를 결정해야 하는 경우 유용합니다. 클라우드 계층화를 사용하지 않는 경우 기본값은 네임스페이스가 먼저 다운로드된 다음 로컬 수용작업량에 도달할 때까지 마지막으로 수정된 타임스탬프를 기반으로 파일을 회수하는 것입니다. 그러나 초기 다운로드 모드를 네임스페이스로만 변경할 수 있습니다. 세 번째 모드는 이 서버 엔드포인트에 대해 클라우드 계층화를 사용하지 않는 경우에만 사용할 수 있습니다. 이 모드는 네임스페이스를 먼저 회수하지 않습니다. 파일은 완전히 다운로드할 수 있는 경우에만 로컬 서버에 표시됩니다. 이 모드는 예를 들어 애플리케이션에 전체 파일이 있어야 하고 네임스페이스의 계층화된 파일을 허용할 수 없는 경우에 유용합니다.

서버 엔드포인트를 추가하려면 **만들기** 를 선택합니다. 이제 Azure 파일 공유 및 Windows Server에서 파일이 동기화 상태로 유지됩니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 명령을 실행하여 서버 엔드포인트를 만들고 `<your-server-endpoint-path>`, `<your-volume-free-space>`를 원하는 값으로 바꾸고 선택적 [초기 다운로드](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section) 및 [초기 업로드](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section) 정책에 대한 옵션 설정을 확인합니다.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly
$initialUploadPolicy = Merge
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) 명령을 사용하여 새 서버 엔드포인트를 만듭니다.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---