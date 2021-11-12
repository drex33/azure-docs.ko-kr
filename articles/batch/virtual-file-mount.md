---
title: 풀에서 가상 파일 시스템 탑재
description: Batch 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 11/11/2021
ms.openlocfilehash: ed83f5771a451f92c69ba5f80de7bfa7d8388778
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369420"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Batch 풀에서 가상 파일 시스템 탑재

Azure Batch에서는 Windows 또는 Linux 컴퓨팅 노드의 Batch 풀에서 클라우드 스토리지 또는 외부 파일 시스템 탑재를 지원합니다. 컴퓨팅 노드가 풀에 참가하면 가상 파일 시스템이 탑재되고 해당 노드에서 로컬 드라이브처럼 취급됩니다. 

다음과 같은 파일 시스템을 탑재할 수 있습니다. 

- Azure 파일
- Azure Blob Storage
- [Avere vFXT cache](../avere-vfxt/avere-vfxt-overview.md) 를 비롯 한 NFS (네트워크 파일 시스템)
- CIFS (Common Internet File System)

이 문서에서는 [.NET용 Batch 관리 라이브러리](/dotnet/api/overview/azure/batch)를 사용하여 컴퓨팅 노드 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.

> [!NOTE]
> 가상 파일 시스템 탑재는 2019년 8월 8일부터 생성되는 Batch 풀에 한해 지원됩니다. 이 날짜 이전에 만들어진 Batch 풀은 이 기능을 지원하지 않습니다.

## <a name="benefits-of-mounting-on-a-pool"></a>풀에 탑재 시 이점

태스크가 대량 데이터 세트에서 자신의 고유 데이터를 검색하도록 허용하지 않고 풀에 파일 시스템을 탑재하면 태스크가 더 쉽고 더 효율적인 방식으로 필요한 데이터에 액세스할 수 있습니다.

동영상 렌더링과 같이 여러 태스크에 공통 데이터 세트에 대한 액세스가 필요하다고 가정해보세요. 각 태스크는 장면 파일로부터 한 번에 하나 이상의 프레임을 렌더링합니다. 장면 파일이 포함된 드라이브를 탑재하면 컴퓨팅 노드가 더 쉽게 공유 데이터에 액세스할 수 있습니다.

또한 데이터에 동시에 액세스하는 컴퓨팅 노드 수에 따라 필요한 성능 및 규모(처리량 및 IOPS)를 기준으로 기본 파일 시스템을 독립적으로 선택하고 확장할 수 있습니다. 예를 들어, [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distributed 메모리 내 캐시를 사용 하 여 수천 개의 동시 렌더링 노드를 사용 하 여 온-프레미스의 원본 데이터에 액세스 하는 대규모의 그림 크기 조정 렌더링을 지원할 수 있습니다. 대신, 이미 클라우드 기반 Blob storage에 있는 데이터의 경우 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 를 사용 하 여이 데이터를 로컬 파일 시스템으로 탑재할 수 있습니다. blobfuse는 Linux 노드에서만 사용할 수 있습니다. 그렇지만 [Azure Files](../storage/files/storage-files-introduction.md)는 비슷한 워크플로를 제공하며, Windows와 Linux에서 모두 사용할 수 있습니다.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>풀에서 가상 파일 시스템 탑재  

풀에 가상 파일 시스템을 탑재하면 해당 풀의 모든 컴퓨팅 노드에서 파일 시스템을 사용할 수 있습니다. 계산 노드가 풀에 연결 되거나 다시 시작 되거나 이미지로 다시 설치 될 때 파일 시스템에 대 한 구성이 수행 됩니다.

풀에 파일 시스템을 탑재하려면 `MountConfiguration` 개체를 만듭니다. `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` 또는 `CifsMountConfiguration`과 같이 가상 파일 시스템에 적합한 개체를 선택합니다.

모든 탑재 구성 개체에는 다음과 같은 기본 매개 변수가 필요합니다. 일부 탑재 구성에는 사용 중인 파일 시스템별 매개 변수가 포함되며 이에 대해서는 코드 예제에서 더 자세히 다뤄집니다.

- **계정 이름 또는 원본**: 가상 파일 공유를 탑재하려면 스토리지 계정 또는 해당 원본의 이름이 필요합니다.
- **상대 탑재 경로 또는 원본**:를 통해 노드에서 액세스할 수 있는 표준 디렉터리를 기준으로 계산 노드에 탑재 된 파일 시스템의 위치 `fsmounts` `AZ_BATCH_NODE_MOUNTS_DIR` 입니다. 정확한 위치는 노드에 사용된 운영 체제에 따라 달라집니다. 예를 들어 Ubuntu 노드의 물리적 위치는에 매핑됩니다 `mnt\batch\tasks\fsmounts` . CentOS 노드에서 위치는에 매핑됩니다 `mnt\resources\batch\tasks\fsmounts` .
- **탑재 옵션 또는 blobfuse 옵션**: 이러한 옵션은 파일 시스템 탑재를 위한 특정 매개 변수에 대해 설명합니다.

`MountConfiguration` 개체가 생성된 다음, 풀을 만들 때 개체를 `MountConfigurationList` 속성에 할당합니다. 노드가 풀에 조인 되거나 다시 시작 되거나 이미지로 다시 설치 될 때 파일 시스템에 대 한 탑재가 발생 합니다.

파일 시스템이 탑재 되 면 `AZ_BATCH_NODE_MOUNTS_DIR` 탑재 된 파일 시스템 및 로그 파일의 위치를 가리키는 환경 변수가 만들어지며,이는 문제 해결 및 디버깅에 유용 합니다. 로그 파일에 대해서는 [탑재 오류 진단](#diagnose-mount-errors) 섹션에서 더 자세히 설명합니다.  

> [!IMPORTANT]
> 풀에 탑재되는 파일 시스템의 최대 수는 10입니다. 자세한 내용 및 기타 제한 사항은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#other-limits)을 참조하세요.

## <a name="mount-azure-file-share-with-powershell"></a>PowerShell을 사용 하 여 Azure 파일 공유 탑재

[Azure PowerShell](/powershell/) 또는 [Azure Cloud Shell](../cloud-shell/overview.md)를 사용 하 여 Batch 풀에 Azure 파일 공유를 탑재할 수 있습니다.

# <a name="windows"></a>[Windows](#tab/windows)

1. Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Batch 계정에 대 한 컨텍스트를 가져옵니다.
    
    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. 다음 설정을 사용 하 여 Batch 풀을 만듭니다. 필요에 따라 샘플 값을 사용자의 정보로 바꿉니다.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "Storage-Account-key")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
    ```

1. 드라이브의 직접 경로를 사용 하 여 탑재 파일에 액세스 합니다. 예를 들면 다음과 같습니다.

    ```powershell
    cmd /c "more S:\folder1\out.txt & timeout /t 90 > NULL"
    ```

1. 출력 파일이 올바른지 확인 하십시오.

1. RDP (원격 데스크톱 프로토콜) 또는 SSH를 사용 하는 경우 드라이브에 직접 액세스 하는 자격 증명을 추가 `S` 합니다. Azure Batch 에이전트는 Windows의 Azure Batch 태스크에 대 한 액세스 권한만 부여 합니다. RDP를 통해 노드에 연결 하는 경우 사용자 계정이 탑재 드라이브에 자동으로 액세스할 수 없습니다. 

    `cmdkey`을 사용 하 여 자격 증명을 추가 합니다. 샘플 값을 사용자의 정보로 바꿉니다.

    ```powershell
    cmdkey /add:"<storage-account-name>.file.core.windows.net" /user:"Azure\<storage-account-name>" /pass:"<storage-account-key>"
    ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Batch 계정에 대 한 컨텍스트를 가져옵니다.

    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. 다음 설정을 사용 하 여 Batch 풀을 만듭니다. 필요에 따라 샘플 값을 사용자의 정보로 바꿉니다.

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "<Storage-Account-key>", "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
     
    ```

1. 환경 변수를 사용 하 여 탑재 파일에 액세스 합니다 `AZ_BATCH_NODE_MOUNTS_DIR` . 예를 들면 다음과 같습니다.

    ```bash
    /bin/bash -c 'more $AZ_BATCH_NODE_MOUNTS_DIR/S/folder1/out.txt; sleep 20s'
    ```

    필요에 따라 직접 경로를 사용 하 여 탑재 파일에 액세스할 수도 있습니다.

1. 출력 파일이 올바른지 확인 하십시오.

1. RDP 또는 SSH를 사용 하는 경우 수동으로 드라이브에 직접 액세스할 수 있습니다 `S` . 경로를 사용 `/mnt/batch/tasks/fsmounts/S` 합니다.

---

### <a name="troubleshoot-powershell-mounting"></a>PowerShell 탑재 문제 해결

PowerShell 또는 Cloud Shell를 사용 하 여 Batch 풀에 Azure 파일 공유를 탑재 하는 경우 다음과 같은 오류가 표시 될 수 있습니다.

```text
Mount Configuration Error | An error was encountered while configuring specified mount(s)
Message: System error (out of memory, cannot fork, no more loop devices)
MountConfigurationPath: S
```

이 오류가 발생 하면 RDP 또는 SSH를 노드에 연결 하 여 관련 로그 파일을 확인 합니다. 일괄 처리 에이전트는 Windows 및 Linux에서 탑재를 다르게 구현 합니다. Linux에서 Batch는 패키지를 설치 `cifs-utils` 합니다. 그런 다음 Batch에서 mount 명령을 실행 합니다. Windows에서 batch는를 사용 하 여 `cmdkey` batch 계정 자격 증명을 추가 합니다. 그런 다음 Batch는를 통해 탑재 명령을 실행 `net use` 합니다. 다음은 그 예입니다.

```powershell
net use S: \\<storage-account-name>.file.core.windows.net\<fileshare> /u:AZURE\<storage-account-name> <storage-account-key>
```

# <a name="windows"></a>[Windows](#tab/windows)

1. RDP를 통해 노드에 커넥트 합니다.

1. 로그 파일를 엽니다 `fshare-S.log` . 파일 경로가 `D:\batch\tasks\fsmounts`인 경우

1. 오류 메시지를 검토 합니다. 예를 들면 다음과 같습니다.

    ```text
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. [Windows SMB (서버 메시지 블록)에서 Azure Files 문제 해결을](../storage/files/storage-troubleshoot-windows-file-connection-problems.md)사용 하 여 문제를 해결 합니다.

# <a name="linux"></a>[Linux](#tab/linux)

1. SSH를 통해 노드에 커넥트 합니다.

1. 로그 파일를 엽니다 `fshare-S.log` . 파일 경로가 `/mnt/batch/tasks/fsmounts`인 경우

1. 오류 메시지를 검토 합니다. 예: `mount error(13): Permission denied`.

1. [Linux (SMB)에서 Azure Files 문제 해결](../storage/files/storage-troubleshoot-linux-file-connection-problems.md)을 사용 하 여 문제를 해결 합니다.

---

RDP 또는 SSH를 사용 하 여 노드의 로그 파일을 확인할 수 없는 경우 Batch 로그를 직접 확인 합니다. Windows 및 Linux 로그 모두에이 방법을 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 창에서 **Batch 계정** 을 입력 하 고 선택 합니다.

1. **Batch 계정** 페이지에서 batch 풀이 있는 계정을 선택 합니다.

1. Batch 계정 페이지의 메뉴에 있는 **기능** 에서 **풀** 을 선택 합니다.

1. 풀의 이름을 선택 합니다.

1. Batch 풀 페이지의 메뉴에 있는 **일반** 에서 **노드** 를 선택 합니다.

1. 노드 이름을 선택 합니다.

1. 노드에 대 한 **개요** 페이지에서 **일괄 처리 로그 업로드** 을 선택 합니다.

1. 업로드 **일괄 처리 로그** 창에서 Azure Storage 컨테이너를 선택합니다. 그런 다음 스토리지 **컨테이너 선택을** 선택합니다.

1. 스토리지 컨테이너에서 로그 파일을 선택하고 다운로드합니다. 

1. `agent-debug.log`를 엽니다.

1. 오류 메시지를 검토합니다. 예를 들면 다음과 같습니다. 

    ```text
    ..20210322T113107.448Z.00000000-0000-0000-0000-000000000000.ERROR.agent.mount.filesystems.basefilesystem.basefilesystem.py.run_cmd_persist_output_async.59.2912.MainThread.3580.Mount command failed with exit code: 2, output:
    
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. [SMB(Windows Azure Files 문제 해결) 또는 SMB(Linux)에서](../storage/files/storage-troubleshoot-windows-file-connection-problems.md) Azure Files 문제 해결을 사용하여 [문제를 해결합니다.](../storage/files/storage-troubleshoot-linux-file-connection-problems.md)

여전히 오류의 원인을 찾을 수 없는 경우 [PowerShell을 사용하여 파일 공유를 수동으로 탑재할](#manually-mount-file-share-with-powershell) 수 있습니다.

### <a name="manually-mount-file-share-with-powershell"></a>PowerShell을 통해 수동으로 파일 공유 탑재

PowerShell을 사용하여 탑재 오류를 진단하거나 수정할 수 없는 경우 파일 공유를 수동으로 탑재할 수 있습니다.

# <a name="windows"></a>[Windows](#tab/windows)

1. 탑재 구성 없이 풀을 만듭니다. 예를 들면 다음과 같습니다.

    ```powershell
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1  -BatchContext $Context
    ```

1. 노드가 **유휴** 상태가 될 때까지 기다립니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 표시줄에서 Storage 계정을 입력하고 **선택합니다.**

1. 파일 공유가 있는 스토리지 계정의 이름을 선택합니다.

1. 스토리지 계정 페이지의 메뉴에 있는 **데이터 스토리지** 아래에서 파일 **공유** 를 선택합니다.

1. 파일 **공유** 페이지에서 파일 공유의 이름을 선택합니다.

1. 파일 공유의 **개요** 페이지에서 **커넥트** 선택합니다.

1. **커넥트** 창에서 **Windows** 탭을 선택합니다.

1. **드라이브 문자** 에 사용할 드라이브를 입력합니다. 기본값은 `Z`입니다.

1. **인증 방법** 의 경우 파일 공유에 연결할 방법을 선택합니다.

1. 파일 공유를 탑재하기 위한 PowerShell 명령을 복사합니다.

1. RDP를 통해 노드에 커넥트.

1. 복사한 명령을 실행하여 파일 공유를 탑재합니다.

1. 출력의 오류 메시지를 기록해 둡다. 이 정보를 사용하여 네트워킹 관련 문제를 해결할 수 있습니다.

# <a name="linux"></a>[Linux](#tab/linux)


1. 탑재 구성 없이 풀을 만듭니다. 예를 들면 다음과 같습니다.

    ```bash
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -BatchContext $Context
    ```

1. 노드가 **유휴** 상태가 될 때까지 기다립니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 표시줄에서 Storage 계정을 입력하고 **선택합니다.**

1. 파일 공유가 있는 스토리지 계정의 이름을 선택합니다.

1. 스토리지 계정 페이지의 메뉴에 있는 **데이터 스토리지** 아래에서 파일 **공유** 를 선택합니다.

1. 파일 **공유** 페이지에서 파일 공유의 이름을 선택합니다.

1. 파일 공유의 **개요** 페이지에서 **커넥트** 선택합니다.

1. **커넥트** 창에서 **Linux** 탭을 선택합니다.

1. 사용하려는 **탑재 지점을** 입력합니다.

1. 파일 공유를 탑재하기 위해 Linux 명령을 복사합니다.

1. SSH를 통해 노드에 커넥트.

1. 복사한 명령을 실행하여 파일 공유를 탑재합니다.

1. 출력의 오류 메시지를 기록해 둡다. 이 정보를 사용하여 네트워킹 관련 문제를 해결할 수 있습니다.


---

## <a name="examples"></a>예제

다음 코드 예제에서는 컴퓨팅 노드 풀에 다양한 파일 공유를 탑재하는 방법을 보여 줍니다.

### <a name="azure-files-share"></a>Azure Files 공유

Azure Files는 표준 Azure 클라우드 파일 시스템 제품입니다. 코드 샘플의 매개 변수에 대한 자세한 내용은 [Azure Files 공유 사용 - SMB](../storage/files/storage-how-to-use-files-windows.md) 또는 [NFS에서 Azure Files 공유 사용을 참조하세요.](../storage/files/storage-files-how-to-create-nfs-shares.md)

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

또 다른 옵션은 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 통해 Azure Blob 스토리지를 사용하는 것입니다. Blob 파일 시스템을 탑재하려면 스토리지 계정에 액세스할 수 있는 , 또는 가 `AccountKey` `SasKey` `Managed Identity` 필요합니다.

이러한 키를 얻는 데 대한 자세한 내용은 다음을 참조하세요. 

- [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)
- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../storage/common/storage-sas-overview.md)
- [Batch 풀에서 관리 ID를 구성합니다.](managed-identity-pools.md) 

blobfuse 사용에 대한 자세한 정보와 팁은 [blobfuse 프로젝트](https://github.com/Azure/azure-storage-fuse)를 참조하세요.

blobfuse에 탑재된 디렉터리에 대한 기본 액세스 권한을 얻으려면 작업을 **관리자** 로 실행합니다. Blobfuse는 사용자 공간에 디렉터리를 탑재하고 풀을 만들 때 루트로 탑재됩니다. Linux에서 모든 **관리자** 작업은 루트입니다. FUSE 모듈의 모든 옵션은 [FUSE 참조 페이지](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)에 설명되어 있습니다.

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
>관리 ID를 사용하는 경우 탑재를 수행하는 VM에서 사용할 수 있도록 ID가 [풀에 할당되었는지](managed-identity-pools.md) 확인합니다. Id가 제대로 작동 하려면 `Storage Blob Data Contributor` 역할이 있어야 합니다.

### <a name="network-file-system"></a>네트워크 파일 시스템

NFS(네트워크 파일 시스템)는 풀 노드에 탑재될 수 있으며, Azure Batch가 기존 파일 시스템에 액세스할 수 있도록 해 줍니다. 이 설정은 클라우드에 배포된 단일 NFS 서버 또는 가상 네트워크를 통해 액세스되는 온-프레미스 NFS 서버일 수 있습니다. NFS 탑재는 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)지원합니다. Avere vFXT 데이터 집약적인 HPC(고성능 컴퓨팅) 작업 및 기타 표준 NFS 규격 인터페이스를 위한 분산 메모리 내 캐시 솔루션입니다. 예를 들어 [Azure Blob용 NFS](../storage/blobs/network-file-system-protocol-support.md) 및 [Azure Files NFS입니다.](../storage/files/storage-files-how-to-mount-nfs-shares.md)

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

[CIFS(Common Internet File system)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 풀 노드에 탑재하는 것은 기존 파일 시스템에 대한 액세스 권한을 제공하는 또 다른 방법입니다. CIFS는 네트워크 서버 파일 및 서비스를 요청하기 위해 개방형의 플랫폼 범용 메커니즘을 제공하는 파일 공유 프로토콜입니다. CIFS는 인터넷 및 인트라넷 파일 공유를 위한 [SMB 프로토콜](/windows-server/storage/file-server/file-server-smb-overview)의 향상된 버전을 기반으로 합니다.

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
  - Vm https://packages.microsoft.com 은 blobfuse 및 gpg 패키지를 다운로드 하려면에 대 한 액세스 권한이 있어야 합니다. 구성에 따라 더 많은 패키지를 다운로드 하기 위해 다른 Url에 액세스 해야 할 수도 있습니다.
- **NFS(네트워크 파일 시스템)** :
  - 포트 2049에 대 한 액세스가 필요 합니다 (기본적으로 구성에 다른 요구 사항이 있을 수 있음).
  - Vm은 적절 한 패키지 관리자에 대 한 액세스 권한이 있어야 `nfs-common` (Debian 또는 Ubuntu 용) 또는 `nfs-utils` (CentOS) 패키지를 다운로드할 수 있어야 합니다. 이 URL은 OS 버전에 따라 달라질 수 있습니다. 구성에 따라 다른 패키지를 다운로드 하기 위해 다른 Url에 액세스 해야 할 수도 있습니다.
  - NFS를 통해 Azure Blob 또는 Azure Files를 탑재 하면 네트워킹 요구 사항이 추가로 발생할 수 있습니다. 예를 들어 저장소 계정과 동일한 가상 네트워크 서브넷을 공유 하는 계산 노드가 필요할 수 있습니다.
- **CIFS(Common Internet File System)** :
  - TCP 포트 445에 대한 액세스 권한이 필요합니다.
  - 패키지를 다운로드 하려면 Vm에 적절 한 패키지 관리자에 대 한 액세스 권한이 있어야 합니다 `cifs-utils` . 이 URL은 OS 버전에 따라 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Windows](../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux](../storage/files/storage-how-to-use-files-linux.md)에서 Azure Files 공유 탑재에 대해 자세히 알아보세요.
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 가상 파일 시스템 사용 및 탑재에 대해 자세히 알아보세요.
- [네트워크 파일 시스템 개요](/windows-server/storage/nfs/nfs-overview)를 보고 NFS 및 해당 애플리케이션에 대해 자세히 알아보세요.
- [Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 보고 CIFS에 대해 자세히 알아보세요.