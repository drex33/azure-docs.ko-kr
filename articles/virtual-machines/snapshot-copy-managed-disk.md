---
title: 가상 하드 디스크의 Azure 스냅숏 만들기
description: Azure VM의 복사본을 만들어 백업으로 사용 하거나 포털, PowerShell 또는 CLI를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: b2dadea22326f8b4bf2af6a55d90e392feea8c7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624769"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>가상 하드 디스크의 스냅숏 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

스냅숏은 VHD (가상 하드 디스크)의 완전 한 읽기 전용 복사본입니다. 스냅숏을 지정 시간 백업으로 사용 하거나 VM (가상 컴퓨터) 문제를 해결 하는 데 도움이 될 수 있습니다. OS (운영 체제) 또는 데이터 디스크 Vhd의 스냅숏을 만들 수 있습니다.

## <a name="create-a-snapshot-of-a-vhd"></a>VHD의 스냅샷 만들기

스냅숏을 사용 하 여 새 VM을 만들려면 먼저 VM을 완전히 종료 해야 합니다. 이 작업을 수행 하면 진행 중인 모든 프로세스가 지워집니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal를 사용 하 여 스냅숏을 만들려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. **스냅샷** 을 검색하고 선택합니다.
1. **스냅샷** 창에서 만들기 **만들기** 를 선택합니다. **스냅샷 만들기** 창이 나타납니다.
1. **리소스 그룹** 에 대해 기존 리소스 그룹을 선택 하거나 새 [리소스 그룹](/azure/azure-resource-manager/management/overview#resource-groups) 의 이름을 입력 합니다.
1. **이름을** 입력 한 다음 새 스냅숏의 **지역** 및 **스냅숏 유형을** 선택 합니다. 영역 복원 저장소에 스냅숏을 저장 하려는 경우 [가용성 영역](/azure/availability-zones/az-overview)을 지 원하는 지역을 선택 해야 합니다. 지원 지역 목록은 [가용성 영역을 사용 하는 Azure 지역](/azure/availability-zones/az-region#azure-regions-with-availability-zones)을 참조 하세요.
1. **원본 구독** 에서 백업할 관리 디스크를 포함 하는 구독을 선택 합니다.
1. **원본 디스크** 에서 스냅샷을 만들 관리 디스크를 선택합니다.
1. **Storage 형식** 의 경우 스냅숏에 대 한 영역 중복 저장소 또는 고성능 저장소를 요구 하지 않는 한 **표준 HDD** 를 선택 합니다.
1. 필요한 경우 **암호화**, **네트워킹** 및 **태그** 탭에서 설정을 구성 합니다. 그렇지 않으면 스냅숏에 기본 설정이 사용 됩니다.
1. **검토 + 만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예에서는 [Cloud Shell](https://shell.azure.com/bash) 를 사용 하거나 [Azure CLI](/cli/azure/) 을 설치 해야 합니다.

다음 단계를 수행 하 여 및 cmdlet을 사용 하 여 스냅숏을 만듭니다 `New-AzSnapshotConfig` `New-AzSnapshot` . 이 예에서는 *Myvm* 리소스 그룹에 *MYVM* 이라는 vm이 있다고 가정 합니다. 제공 된 코드 샘플은 동일한 리소스 그룹 및 원본 VM과 동일한 지역 내에 스냅숏을 만듭니다.

먼저 [AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) cmdlet을 사용 하 여 구성 가능한 스냅숏 개체를 만듭니다. 그런 다음 [AzSnapshot](/powershell/module/az.compute/new-azsnapshot) cmdlet을 사용 하 여 디스크의 스냅숏을 만들 수 있습니다.

1. 필수 매개 변수를 설정 합니다. 사용자 환경에 맞게 값을 업데이트 합니다.

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. [New-azvm](/powershell/module/az.compute/get-azvm) cmdlet을 사용 하 여 복사 하려는 VHD가 포함 된 VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. 스냅샷 구성을 만듭니다. 이 예제에서 스냅숏은 OS 디스크입니다. 기본적으로 스냅숏은 로컬 중복 standard storage를 사용 합니다. 부모 디스크 또는 대상 디스크의 저장소 유형에 상관 없이 premium storage 대신 표준 저장소에 스냅숏을 저장 하는 것이 좋습니다. Premium 스냅숏에는 추가 비용이 발생 합니다.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   스냅숏을 영역 복원 저장소에 저장 하려는 경우 [availability zones] (/azure/availability-zones/az-overview 및 include 매개 변수를 지 원하는 지역에 스냅숏을 만들어야 합니다 `-SkuName Standard_ZRS` . 가용성 영역을 지 원하는 지역 목록은 [가용성 영역을 사용 하는 Azure 지역](/azure/availability-zones/az-region#azure-regions-with-availability-zones)을 참조 하세요.

1. 스냅샷을 만듭니다.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. [AzSnapshot](/powershell/module/az.compute/get-azsnapshot) cmdlet을 사용 하 여 스냅숏이 있는지 확인 합니다.

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

이 예에서는 [Cloud Shell](https://shell.azure.com/bash) 를 사용 하거나 [Azure CLI](/cli/azure/) 을 설치 해야 합니다.

명령 및 매개 변수를 사용 하 여 스냅숏을 만들려면 다음 단계를 수행 `az snapshot create` `--source-disk` 합니다. 이 예에서는 *Myvm* 리소스 그룹에 *MYVM* 이라는 vm이 있다고 가정 합니다. 제공 된 코드 샘플은 동일한 리소스 그룹 및 원본 VM과 동일한 지역 내에 스냅숏을 만듭니다.

1. [Az vm show](/cli/azure/vm#az_vm_show)를 사용 하 여 디스크 ID를 가져옵니다.

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. [az snapshot create](/cli/azure/snapshot#az_snapshot_create)를 사용하여 *osDisk-backup* 이라는 스냅샷을 만듭니다. 이 예제에서 스냅숏은 OS 디스크입니다. 기본적으로 스냅숏은 로컬 중복 standard storage를 사용 합니다. 부모 디스크 또는 대상 디스크의 저장소 유형에 상관 없이 premium storage 대신 표준 저장소에 스냅숏을 저장 하는 것이 좋습니다. Premium 스냅숏에는 추가 비용이 발생 합니다.

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    스냅숏을 영역 복원 저장소에 저장 하려는 경우 [가용성 영역](/azure/availability-zones/az-overview) 을 지원 하 고 선택적 매개 변수를 포함 하는 지역에 만들어야 `--sku Standard_ZRS` 합니다. [가용성 영역](/azure/availability-zones/az-region#azure-regions-with-availability-zones) 목록은 여기에서 찾을 수 있습니다.
    
1. [Az snapshot list](/cli/azure/snapshot#az_snapshot_list) 를 사용 하 여 스냅숏이 있는지 확인 합니다.
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>다음 단계

스냅숏에서 가상 컴퓨터를 배포 합니다. 스냅숏에서 관리 디스크를 만든 다음 새 관리 되는 디스크를 OS 디스크로 연결 합니다.

# <a name="portal"></a>[포털](#tab/portal)

자세한 내용은 [Azure Portal를 사용 하 여 VHD에서 VM 만들기](windows/create-vm-specialized-portal.md)의 예제를 참조 하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

자세한 내용은 [PowerShell을 사용 하 여 특수 디스크에서 Windows VM 만들기](windows/create-vm-specialized.md)의 예제를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

자세한 내용은 [Azure CLI를 사용 하 여 전체 Linux 가상 컴퓨터 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)의 예제를 참조 하세요.

---
