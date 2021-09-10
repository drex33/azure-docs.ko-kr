---
title: 풀에서 가상 파일 시스템 탑재
description: Batch 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/18/2021
ms.openlocfilehash: 7057a982fb3a4b59b8716a373c2ed5172e392cb2
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568151"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Batch 풀에서 가상 파일 시스템 탑재

Azure Batch에서는 Windows 또는 Linux 컴퓨팅 노드의 Batch 풀에서 클라우드 스토리지 또는 외부 파일 시스템 탑재를 지원합니다. 컴퓨팅 노드가 풀에 참가하면 가상 파일 시스템이 탑재되고 해당 노드에서 로컬 드라이브처럼 취급됩니다. Azure Files, Azure Blob 스토리지, [Avere vFXT 캐시](../avere-vfxt/avere-vfxt-overview.md)를 포함한 NFS(네트워크 파일 시스템) 또는 CIFS(Common Internet File System)와 같은 파일 시스템을 탑재합니다.

이 문서에서는 [.NET용 Batch 관리 라이브러리](/dotnet/api/overview/azure/batch)를 사용하여 컴퓨팅 노드 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.

> [!NOTE]
> 가상 파일 시스템 탑재는 2019년 8월 8일부터 생성되는 Batch 풀에 한해 지원됩니다. 이 날짜 이전에 만들어진 Batch 풀은 이 기능을 지원하지 않습니다.

## <a name="benefits-of-mounting-on-a-pool"></a>풀에 탑재 시 이점

태스크가 대량 데이터 세트에서 자신의 고유 데이터를 검색하도록 허용하지 않고 풀에 파일 시스템을 탑재하면 태스크가 더 쉽고 더 효율적인 방식으로 필요한 데이터에 액세스할 수 있습니다.

동영상 렌더링과 같이 여러 태스크에 공통 데이터 세트에 대한 액세스가 필요하다고 가정해보세요. 각 태스크는 장면 파일로부터 한 번에 하나 이상의 프레임을 렌더링합니다. 장면 파일이 포함된 드라이브를 탑재하면 컴퓨팅 노드가 더 쉽게 공유 데이터에 액세스할 수 있습니다.

또한 데이터에 동시에 액세스하는 컴퓨팅 노드 수에 따라 필요한 성능 및 규모(처리량 및 IOPS)를 기준으로 기본 파일 시스템을 독립적으로 선택하고 확장할 수 있습니다. 예를 들어 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)로 분산된 메모리 내 캐시를 사용하면 온-프레미스에 있는 소스 데이터에 액세스하여 수천 개의 동시 렌더링 노드가 있는 대규모 영화급 렌더링을 지원할 수 있습니다. 또는 이미 클라우드 기반 Blob 스토리지에 있는 데이터의 경우, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 사용하여 이 데이터를 로컬 파일 시스템으로 탑재할 수 있습니다. blobfuse는 Linux 노드에서만 사용할 수 있습니다. 그렇지만 [Azure Files](../storage/files/storage-files-introduction.md)는 비슷한 워크플로를 제공하며, Windows와 Linux에서 모두 사용할 수 있습니다.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>풀에서 가상 파일 시스템 탑재  

풀에 가상 파일 시스템을 탑재하면 해당 풀의 모든 컴퓨팅 노드에서 파일 시스템을 사용할 수 있습니다. 파일 시스템은 컴퓨팅 노드가 풀에 참가할 때 또는 노드가 다시 시작되거나 이미지로 다시 설치될 때 구성됩니다.

풀에 파일 시스템을 탑재하려면 `MountConfiguration` 개체를 만듭니다. `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` 또는 `CifsMountConfiguration`과 같이 가상 파일 시스템에 적합한 개체를 선택합니다.

모든 탑재 구성 개체에는 다음과 같은 기본 매개 변수가 필요합니다. 일부 탑재 구성에는 사용 중인 파일 시스템별 매개 변수가 포함되며 이에 대해서는 코드 예제에서 더 자세히 다뤄집니다.

- **계정 이름 또는 원본**: 가상 파일 공유를 탑재하려면 스토리지 계정 또는 해당 원본의 이름이 필요합니다.
- **상대적 탑재 경로 또는 원본**: `AZ_BATCH_NODE_MOUNTS_DIR`을 통해 노드에서 액세스할 수 있는 표준 `fsmounts` 디렉터리를 기준으로 하는 컴퓨팅 노드에 탑재된 파일 시스템의 위치입니다. 정확한 위치는 노드에 사용된 운영 체제에 따라 달라집니다. 예를 들어 Ubuntu 노드에서 실제 위치는 `mnt\batch\tasks\fsmounts`로 매핑되고 CentOS 노드에서는 `mnt\resources\batch\tasks\fsmounts`로 매핑됩니다.
- **탑재 옵션 또는 blobfuse 옵션**: 이러한 옵션은 파일 시스템 탑재를 위한 특정 매개 변수에 대해 설명합니다.

`MountConfiguration` 개체가 생성된 다음, 풀을 만들 때 개체를 `MountConfigurationList` 속성에 할당합니다. 노드가 풀에 참가하거나 노드가 다시 시작 또는 이미지로 다시 설치될 때 파일 시스템이 탑재됩니다.

파일 시스템이 탑재될 때는 로그 파일은 물론 탑재된 파일 시스템의 위치를 나타내어 문제 해결과 디버깅에 도움이 되는 환경 변수 `AZ_BATCH_NODE_MOUNTS_DIR`이 생성됩니다. 로그 파일에 대해서는 [탑재 오류 진단](#diagnose-mount-errors) 섹션에서 더 자세히 설명합니다.  

> [!IMPORTANT]
> 풀에 탑재되는 파일 시스템의 최대 수는 10입니다. 자세한 내용 및 기타 제한 사항은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#other-limits)을 참조하세요.

## <a name="examples"></a>예

다음 코드 예시는 컴퓨팅 노드 풀에 다양한 파일 공유를 탑재하는 방법을 보여줍니다.

### <a name="azure-files-share"></a>Azure Files 공유

Azure Files는 표준 Azure 클라우드 파일 시스템 제품입니다. 탑재 구성 코드 샘플에서 매개 변수를 가져오는 방법에 대한 자세한 내용은 [Azure Files 공유 사용 - SMB](../storage/files/storage-how-to-use-files-windows.md) 또는 [Azure Files 공유 사용 - NFS](../storage/files/storage-files-how-to-create-nfs-shares.md)를 참조하세요.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Azure Blob 컨테이너

또 다른 옵션은 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 통해 Azure Blob 스토리지를 사용하는 것입니다. Blob 파일 시스템 탑재를 위해서는 해당 스토리지 계정에 대한 `AccountKey` 또는 `SasKey` 또는 `Managed Identity`이 필요합니다.

이러한 키 가져오기에 대한 자세한 내용은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md), [공유 액세스 서명(SAS)을 사용하여 Azure Storage 리소스에 대한 제한된 액세스 부여](../storage/common/storage-sas-overview.md) 및 [일괄 관리 ID 구성을 Batch 풀](managed-identity-pools.md)에서 참조하세요. blobfuse 사용에 대한 자세한 정보와 팁은 [blobfuse 프로젝트](https://github.com/Azure/azure-storage-fuse)를 참조하세요.

blobfuse에 탑재된 디렉터리에 대한 기본 액세스 권한을 얻으려면 작업을 **관리자** 로 실행합니다. Blobfuse는 사용자 공간에 디렉터리를 탑재하고, 풀 생성 시 루트로 탑재됩니다. Linux에서 모든 **관리자** 태스크는 루트입니다. FUSE 모듈의 모든 옵션은 [FUSE 참조 페이지](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)에 설명되어 있습니다.

blobfuse 사용에 대한 자세한 정보와 팁을 알아보려면 [문제 해결 FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)를 검토하세요. 또한 [blobfuse 리포지토리의 GitHub 문제](https://github.com/Azure/azure-storage-fuse/issues)를 검토하여 현재 blobfuse 문제와 해결 방법을 확인할 수 있습니다.

> [!NOTE]
> 아래 예제에서는`AccountKey`,`SasKey` 및 `IdentityReference`를 보여주지만 함께 사용할 수는 없습니다. 하나만 지정할 수 있습니다.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>관리 id를 사용 하는 경우 탑재를 수행 하는 VM에서 사용할 수 있도록 ID가 [풀에 할당](managed-identity-pools.md) 되었는지 확인합니다. Id가 제대로 작동 하려면 `Storage Blob Data Contributor` 역할이 있어야 합니다.

### <a name="network-file-system"></a>네트워크 파일 시스템

NFS(네트워크 파일 시스템)는 풀 노드에 탑재될 수 있으며, Azure Batch가 기존 파일 시스템에 액세스할 수 있도록 해 줍니다. 이것은 클라우드에 배포되는 단일 NFS 서버이거나 가상 네트워크를 통해 액세스되는 온-프레미스 NFS 서버일 수 있습니다. NFS 탑재는 데이터 집약적인 HPC(고성능 컴퓨팅) 작업을 위한 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)로 분산된 메모리 캐시 솔루션뿐만 아니라 [Azure Blob용 NFS](../storage/blobs/network-file-system-protocol-support.md) 및 [Azure Files용 NFS](../storage/files/storage-files-how-to-mount-nfs-shares.md)와 같은 기타 표준 NFS 호환 인터페이스를 지원합니다.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

[CIFS(Common Internet File system)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 풀 노드에 탑재하는 것은 기존 파일 시스템에 대한 액세스 권한을 제공하는 또 다른 방법입니다. CIFS는 네트워크 서버 파일 및 서비스를 요청하기 위해 개방형의 플랫폼 범용 메커니즘을 제공하는 파일 공유 프로토콜입니다. CIFS는 인터넷 및 인트라넷 파일 공유를 위한 [SMB(서버 메시지 블록)](/windows-server/storage/file-server/file-server-smb-overview) 프로토콜의 향상된 버전을 기반으로 합니다.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>탑재 오류 진단

탑재 구성에 실패하면 풀의 컴퓨팅 노드에서 실패하고 노드 상태가 `unusable`(으)로 설정됩니다. 탑재 구성 오류를 진단하려면 [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) 속성에서 자세한 오류 정보를 조사합니다.

디버그를 위해 로그 파일을 가져오려면 [OutputFiles](batch-task-output-files.md)를 사용하여 `*.log` 파일을 업로드합니다. `*.log` 파일에는 `AZ_BATCH_NODE_MOUNTS_DIR` 위치에 있는 파일 시스템 탑재에 대한 정보가 포함됩니다. 탑재 로그 파일에는 각 탑재에 대해 `<type>-<mountDirOrDrive>.log` 형식이 사용됩니다. 예를 들어 `test`라는 탑재 디렉터리에서 `cifs` 탑재에는 `cifs-test.log`라는 탑재 로그 파일이 사용됩니다.

## <a name="support-matrix"></a>지원 매트릭스

Azure Batch는 각 게시자 및 제품에 대해 생성된 노드 에이전트에 대해 다음과 같은 가상 파일 시스템 형식을 지원합니다.

| OS 유형 | Azure Files 공유 | Azure Blob 컨테이너 | NFS 탑재 | CIFS 탑재 |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>네트워킹 요구 사항

[가상 네트워크에서 Azure Batch 풀](batch-virtual-network.md)과 함께 가상 파일 탑재를 사용하는 경우 다음 요구 사항을 염두에 두고 필요한 트래픽이 차단되지 않도록 해야 합니다.

- **Azure 파일 공유**:
  - "스토리지" 서비스 태그와 주고받는 트래픽에 대한 TCP 포트 445가 열려 있어야 합니다. 자세한 내용은 [Windows에서 Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md#prerequisites)을 참조하세요.
- **Azure Blob 컨테이너**:
  - "스토리지" 서비스 태그와 주고받는 트래픽에 대한 TCP 포트 443이 열려 있어야 합니다.
  - VM에서 blobfuse 및 gpg 패키지를 다운로드하려면 https://packages.microsoft.com 에 대한 액세스 권한이 있어야 합니다. 구성에 따라 추가 패키지를 다운로드하려면 다른 URL에 액세스해야 할 수도 있습니다.
- **NFS(네트워크 파일 시스템)** :
  - 포트 2049에 대한 액세스 권한이 필요합니다 (기본적으로 구성에 다른 요구 사항이 있을 수도 있음).
  - nfs-common(Debian이나 Ubuntu) 또는 nfs-utils(CentOS) 패키지를 다운로드하려면 VM이 적절한 패키지 관리자에 대한 액세스 권한을 갖추고 있어야 합니다. 이 URL은 OS 버전에 따라 달라질 수 있습니다. 구성에 따라 추가 패키지를 다운로드하려면 다른 URL에 액세스해야 할 수도 있습니다.
  - NFS를 통해 Azure Blob 또는 Azure Files를 탑재하려면 스토리지 계정과 동일한 가상 네트워크의 지정된 서브넷을 공유하는 컴퓨팅 노드와 같은 추가 네트워킹 요구 사항이 필요할 수 있습니다.
- **CIFS(Common Internet File System)** :
  - TCP 포트 445에 대한 액세스 권한이 필요합니다.
  - cifs-utils 패키지를 다운로드하려면 VM이 적절한 패키지 관리자에 대한 액세스 권한을 갖추고 있어야 합니다. 이 URL은 OS 버전에 따라 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Windows](../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux](../storage/files/storage-how-to-use-files-linux.md)에서 Azure Files 공유 탑재에 대해 자세히 알아보세요.
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 가상 파일 시스템 사용 및 탑재에 대해 자세히 알아보세요.
- [네트워크 파일 시스템 개요](/windows-server/storage/nfs/nfs-overview)를 보고 NFS 및 해당 애플리케이션에 대해 자세히 알아보세요.
- [Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 보고 CIFS에 대해 자세히 알아보세요.