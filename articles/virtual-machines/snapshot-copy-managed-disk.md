---
title: 가상 하드 디스크의 Azure 스냅샷 만들기
description: 백업으로 사용하거나 포털, PowerShell 또는 CLI를 사용하여 문제를 해결하기 위해 사용할 Azure VM의 복사본을 만드는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: 0a63e0f346fedac9b7178f25a19177a9908bc6a4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223010"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>가상 하드 디스크의 스냅샷 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

스냅샷은 VHD(가상 하드 디스크)의 전체 읽기 전용 복사본입니다. 스냅샷을 시점 백업으로 사용하거나 VM(가상 머신) 문제를 해결하는 데 도움이 될 수 있습니다. OS(운영 체제) 또는 데이터 디스크 VHD의 스냅샷을 만들 수 있습니다.

## <a name="create-a-snapshot-of-a-vhd"></a>VHD의 스냅샷 만들기

스냅샷을 사용하여 새 VM을 만들려면 먼저 VM을 완전히 종료해야 합니다. 이 작업은 진행 중인 모든 프로세스를 지웁니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal 사용하여 스냅샷을 만들려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. **스냅샷** 을 검색하고 선택합니다.
1. **스냅샷** 창에서 만들기 **만들기** 를 선택합니다. **스냅샷 만들기** 창이 나타납니다.
1. **리소스 그룹의** 경우 기존 [리소스 그룹을](../azure-resource-manager/management/overview.md#resource-groups) 선택하거나 새 리소스 그룹의 이름을 입력합니다.
1. **이름** 을 입력한 다음, 새 **스냅샷에** 대한 **지역** 및 스냅샷 유형을 선택합니다. 스냅샷을 영역 복원 스토리지에 저장하려면 가용성 영역을 지원하는 지역을 선택해야 [합니다.](../availability-zones/az-overview.md) 지원 지역 목록은 [가용성 영역이 있는 Azure 지역을 참조하세요.](../availability-zones/az-region.md#azure-regions-with-availability-zones)
1. **원본 구독의** 경우 백업할 관리 디스크가 포함된 구독을 선택합니다.
1. **원본 디스크** 에서 스냅샷을 만들 관리 디스크를 선택합니다.
1. **Storage 형식의** 경우 스냅샷에 영역 중복 스토리지 또는 고성능 스토리지가 필요하지 않은 경우 **표준 HDD** 선택합니다.
1. 필요한 경우 **암호화,** **네트워킹** 및 **태그** 탭에서 설정을 구성합니다. 그렇지 않으면 스냅샷에 기본 설정이 사용됩니다.
1. **검토 + 만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예제에서는 [Cloud Shell](https://shell.azure.com/bash) 사용하거나 [Azure CLI](/cli/azure/) 설치해야 합니다.

및 cmdlet을 사용하여 스냅샷을 생성하려면 다음 단계를 `New-AzSnapshotConfig` `New-AzSnapshot` 수행합니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM이라는 VM이* 있다고 가정합니다. 제공된 코드 샘플은 원본 VM과 동일한 지역 내에서 동일한 리소스 그룹에 스냅샷을 만듭니다.

먼저 [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) cmdlet을 사용하여 구성 가능한 스냅샷 개체를 만듭니다. 그런 다음 [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) cmdlet을 사용하여 디스크의 스냅샷을 만들 수 있습니다.

1. 필요한 매개 변수를 설정합니다. 환경을 반영하도록 값을 업데이트합니다.

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet을 사용하여 복사하려는 VHD가 포함된 VM을 얻습니다.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. 스냅샷 구성을 만듭니다. 예제에서 스냅샷은 OS 디스크입니다. 기본적으로 스냅샷은 로컬 중복 표준 스토리지를 사용합니다. 부모 디스크 또는 대상 디스크의 스토리지 유형에 관계없이 Premium Storage 대신 표준 스토리지에 스냅샷을 저장하는 것이 좋습니다. Premium 스냅샷에는 추가 비용이 발생합니다.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   스냅샷을 영역 복원 스토리지에 저장하려면 [가용성 영역](/azure/availability-zones/az-overview)을 지원하는 지역에 스냅샷을 만들고 매개 변수를 포함해야 `-SkuName Standard_ZRS` 합니다. 가용성 영역을 지원하는 지역 목록은 가용성 [영역이 있는 Azure 지역을 참조하세요.](../availability-zones/az-region.md#azure-regions-with-availability-zones)

1. 스냅샷을 만듭니다.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) cmdlet을 사용하여 스냅샷이 있는지 확인합니다.

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

이 예제에서는 [Cloud Shell](https://shell.azure.com/bash) 사용하거나 [Azure CLI](/cli/azure/) 설치해야 합니다.

다음 단계에 따라 명령 및 매개 변수를 사용하여 `az snapshot create` 스냅샷을 `--source-disk` 생성합니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM이라는 VM이* 있다고 가정합니다. 제공된 코드 샘플은 원본 VM과 동일한 지역 내에서 동일한 리소스 그룹에 스냅샷을 만듭니다.

1. [az vm show 를 통해](/cli/azure/vm#az_vm_show)디스크 ID를 가져옵니다.

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. [az snapshot create](/cli/azure/snapshot#az_snapshot_create)를 사용하여 *osDisk-backup* 이라는 스냅샷을 만듭니다. 예제에서 스냅샷은 OS 디스크입니다. 기본적으로 스냅샷은 로컬 중복 표준 스토리지를 사용합니다. 부모 디스크 또는 대상 디스크의 스토리지 유형에 관계없이 Premium Storage 대신 표준 스토리지에 스냅샷을 저장하는 것이 좋습니다. Premium 스냅샷에는 추가 비용이 발생합니다.

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    영역 복원 스토리지에 스냅샷을 저장하려면 가용성 영역을 지원하고 선택적 매개 변수를 포함하는 지역에 [스냅샷을](../availability-zones/az-overview.md) 만들어야 `--sku Standard_ZRS` 합니다. [가용성 영역](../availability-zones/az-region.md#azure-regions-with-availability-zones) 목록은 여기에서 찾을 수 있습니다.
    
1. [az snapshot list를](/cli/azure/snapshot#az_snapshot_list) 사용하여 스냅샷이 있는지 확인합니다.
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>다음 단계

스냅샷에서 가상 머신을 배포합니다. 스냅샷에서 관리 디스크를 만든 다음 새 관리 디스크를 OS 디스크로 연결합니다.

# <a name="portal"></a>[포털](#tab/portal)

자세한 내용은 Azure Portal 사용하여 [VHD에서 VM 만들기의 예제를 참조하세요.](windows/create-vm-specialized-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

자세한 내용은 [PowerShell을 사용하여 특수 디스크에서 Windows VM 만들기의](windows/create-vm-specialized.md)예제를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

자세한 내용은 Azure CLI 사용하여 [전체 Linux 가상 머신 만들기의 예제를 참조하세요.](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)

---