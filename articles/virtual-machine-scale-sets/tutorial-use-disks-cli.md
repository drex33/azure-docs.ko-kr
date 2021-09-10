---
title: 자습서 - Azure CLI를 사용하여 확장 집합용 디스크 만들기 및 사용
description: Azure CLI를 사용하여 가상 머신 확장 집합이 있는 관리 디스크를 만들고 사용하는 방법(디스크를 추가, 준비, 나열 및 분리하는 방법 포함)을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: b09b116ad420946b909965d0b0bebe2ab747aa40
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690659"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 가상 머신 확장 집합이 있는 디스크 만들기 및 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

가상 머신 확장 집합은 디스크를 사용하여 VM 인스턴스의 운영 체제, 애플리케이션 및 데이터를 저장합니다. 확장 집합을 만들고 관리할 때 예상 작업에 적합한 디스크 크기와 구성을 선택해야 합니다. 이 자습서에서는 VM 디스크를 만들고 관리하는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.29 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="default-azure-disks"></a>기본 Azure 디스크
확장 집합을 만들거나 크기를 조정하면 두 개의 디스크가 각 VM 인스턴스에 자동으로 연결됩니다.

**운영 체제 디스크** - 운영 체제 디스크는 최대 2TB까지 크기를 지정할 수 있으며, VM 인스턴스의 운영 체제를 호스팅합니다. OS 디스크는 기본적으로 */dev/sda* 로 레이블이 지정됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. 이 구성으로 인해 OS 디스크는 애플리케이션 또는 데이터를 호스트해서는 **안 됩니다**. 애플리케이션 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 문서의 뒷부분에서 자세히 설명합니다.

**임시 디스크** - 임시 디스크는 VM 인스턴스와 동일한 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 이러한 디스크는 고성능 디스크이며, 임시 데이터 처리와 같은 작업에 사용할 수 있습니다. 그러나 VM 인스턴스가 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터가 제거됩니다. 임시 디스크의 크기는 VM 인스턴스 크기에 따라 결정됩니다. 임시 디스크는 */dev/sdb* 로 레이블이 지정되고 탑재 지점은 */mnt* 입니다.

### <a name="temporary-disk-sizes"></a>임시 디스크 크기
| Type | 일반적인 크기 | 최대 임시 디스크 크기(GiB) |
|----|----|----|
| [범용](../virtual-machines/sizes-general.md) | A, B 및 D 시리즈 | 1600 |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md) | F 시리즈 | 576 |
| [메모리에 최적화](../virtual-machines/sizes-memory.md) | D, E, G 및 M 시리즈 | 6144 |
| [Storage에 최적화](../virtual-machines/sizes-storage.md) | L 시리즈 | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N 시리즈 | 1440 |
| [고성능](../virtual-machines/sizes-hpc.md) | A 및 H 시리즈 | 2000 |


## <a name="azure-data-disks"></a>Azure 데이터 디스크
애플리케이션을 설치하고 데이터를 저장해야 하는 경우 추가 데이터 디스크를 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 스토리지가 필요한 상황에 사용해야 합니다. 각 데이터 디스크의 최대 용량은 4TB입니다. VM 인스턴스의 크기에 따라 연결할 수 있는 데이터 디스크 수가 결정됩니다. 각 VM vCPU에 대해 2개의 데이터 디스크를 가상 머신당 최대 64개의 디스크에 연결할 수 있습니다.

## <a name="vm-disk-types"></a>VM 디스크 유형
Azure는 두 가지 유형의 디스크를 제공합니다.

### <a name="standard-disk"></a>표준 디스크
Standard Storage는 HDD에서 지원되며, 비용 효율적인 스토리지 및 성능을 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

### <a name="premium-disk"></a>프리미엄 디스크
프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 이러한 디스크는 프로덕션 작업을 실행하는 VM에 권장됩니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM을 지원합니다. 디스크 크기를 선택하면 값이 다음 형식으로 반올림됩니다. 예를 들어 디스크 크기가 128GB 미만인 경우 디스크 유형은 P10입니다. 디스크 크기가 129GB에서 512GB 사이이면 크기는 P20입니다. 512GB 초과이면 크기는 P30입니다.

### <a name="premium-disk-performance"></a>프리미엄 디스크 성능
|Premium Storage 디스크 유형 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 디스크 크기(반올림) | 32GB | 64GB | 128GB | 512 GB | 1,024GB(1TB) | 2,048GB(2TB) | 4,095GB(4TB) |
| 디스크당 최대 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
디스크당 처리량 | 25MB/초 | 50MB/초 | 100MB/초 | 150MB/초 | 200MB/s | 250MB/초 | 250MB/초 |

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 Standard_GS5 VM은 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [Linux VM 크기](../virtual-machines/sizes.md)를 참조하세요.


## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결
확장 집합을 만들 때 또는 기존 확장 집합을 사용하여 디스크를 만들고 연결할 수 있습니다.

API 버전 `2019-07-01`을 기준으로 [storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) 속성을 사용하여 가상 머신 확장 집합에서 OS 디스크의 크기를 설정할 수 있습니다. 프로비저닝한 후에는 전체 공간을 사용하기 위해 디스크를 확장하거나 다시 분할해야 할 수 있습니다. [여기에서 디스크 확장](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os)에 대해 자세히 알아보세요.

### <a name="attach-disks-at-scale-set-creation"></a>확장 집합을 만들 때 디스크 연결
먼저 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vmss create](/cli/azure/vmss) 명령을 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합을 만들고, SSH 키가 없는 경우 이 키를 생성합니다. `--data-disk-sizes-gb` 매개 변수를 사용하여 두 개의 디스크를 만듭니다. 첫 번째 디스크의 크기는 *64* GB이고, 두 번째 디스크의 크기는 *128* GB입니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

확장 집합 리소스와 VM 인스턴스를 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

### <a name="attach-a-disk-to-existing-scale-set"></a>기존 확장 집합에 디스크 연결
기존 확장 집합에 디스크를 연결할 수도 있습니다. [az vmss disk attach](/cli/azure/vmss/disk)를 사용하여 다른 디스크를 추가하기 위해 이전 단계에서 만든 확장 집합을 사용합니다. 다음 예제에서는 *128* GB 데이터 디스크를 추가로 연결합니다.

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>데이터 디스크 준비
확장 집합 VM 인스턴스에 만들어지고 연결된 디스크는 원시 디스크입니다. 데이터 및 애플리케이션과 함께 사용하려면 먼저 디스크를 준비해야 합니다. 디스크를 준비하려면 파티션을 만들고, 파일 시스템을 만들고, 디스크를 탑재합니다.

확장 집합의 여러 VM 인스턴스에 걸쳐 프로세스를 자동화하려면 Azure 사용자 지정 스크립트 확장을 사용할 수 있습니다. 이 확장은 연결된 데이터 디스크를 준비하는 것과 같이 각 VM 인스턴스에서 스크립트를 로컬로 실행할 수 있습니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../virtual-machines/extensions/custom-script-linux.md)를 참조하세요.

다음 예제에서는 연결된 모든 원시 데이터 디스크를 준비하는 [az vmss extension set](/cli/azure/vmss/extension)을 사용하여 각 VM 인스턴스에서 GitHub 샘플 리포지토리의 스크립트를 실행합니다.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

디스크가 올바르게 준비되었는지 확인하려면 SSH를 VM 인스턴스 중 하나에 연결합니다. [az vmss list-instance-connection-info](/cli/azure/vmss)를 사용하여 확장 집합에 대한 연결 정보를 나열합니다.

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

다음 예제와 같이 사용자 고유의 공용 IP 주소와 포트 번호를 사용하여 첫 번째 VM 인스턴스에 연결합니다.

```console
ssh azureuser@52.226.67.166 -p 50001
```

다음과 같이 VM 인스턴스의 파티션을 검사합니다.

```bash
sudo fdisk -l
```

다음 예제 출력에서는 세 개의 디스크가 */dev/sdc*, */dev/sdd* 및 */dev/sde* VM 인스턴스에 연결되었음을 보여 줍니다. 각 디스크에는 사용 가능한 모든 공간을 사용하는 단일 파티션이 있습니다.

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

다음과 같이 VM 인스턴스의 파일 시스템과 탑재 지점을 검사합니다.

```bash
sudo df -h
```

다음 예제 출력에서는 세 개 디스크의 파일 시스템이 */datadisks* 아래에 올바르게 탑재되었음을 보여 줍니다.

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

확장 집합의 각 VM 인스턴스에 있는 디스크는 동일한 방식으로 자동으로 준비됩니다. 확장 집합이 강화되면 필요한 데이터 디스크가 새 VM 인스턴스에 연결됩니다. 또한 사용자 지정 스크립트 확장도 실행되어 디스크를 자동으로 준비합니다.

VM 인스턴스에 대한 SSH 연결을 닫습니다.

```bash
exit
```


## <a name="list-attached-disks"></a>연결된 디스크 나열
확장 집합에 연결된 디스크에 대한 정보를 보려면 [az vmss show](/cli/azure/vmss)를 사용하고 *virtualMachineProfile.storageProfile.dataDisks* 에 대해 쿼리합니다.

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

디스크 크기, 스토리지 계층 및 LUN(논리 단위 번호)에 대한 정보가 표시됩니다. 다음 예제 출력에서는 확장 집합에 연결된 세 개의 데이터 디스크에 대해 자세히 설명합니다.

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>디스크 분리
지정된 디스크가 더 이상 필요하지 않은 경우 확장 집합에서 디스크를 분리할 수 있습니다. 확장 집합의 모든 VM 인스턴스에서 디스크가 제거됩니다. 확장 집합에서 디스크를 분리하려면 [az vmss disk detach](/cli/azure/vmss/disk)를 사용하고 디스크의 LUN을 지정합니다. LUN은 이전 섹션의 [az vmss show](/cli/azure/vmss) 출력에 표시되어 있습니다. 다음 예제에서는 확장 집합에서 LUN *2* 를 분리합니다.

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 디스크를 제거하려면 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI를 사용하여 확장 집합이 있는 다음과 같은 디스크를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

확장 집합 VM 인스턴스에 대해 사용자 지정 이미지를 사용하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합 VM 인스턴스용 사용자 지정 이미지 사용](tutorial-use-custom-image-cli.md)