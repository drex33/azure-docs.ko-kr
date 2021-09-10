---
title: Linux VM에서 데이터 디스크 분리 - Azure
description: Azure CLI 또는 Azure Portal을 사용하여 Azure의 가상 머신에서 데이터 디스크를 분리하는 방법을 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 19c048613fa1f3e97382264be58da62f2eade286
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694732"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Linux 가상 머신에서 데이터 디스크를 분리하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

가상 머신에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 머신에서 디스크가 제거되지만, 스토리지에서는 제거되지 않습니다. 이 문서에서는 Ubuntu LTS 16.04 배포판으로 작업합니다. 다른 배포판을 사용하는 경우 디스크 탑재 해제 지침이 다를 수 있습니다.

> [!WARNING]
> 디스크를 분리해도 자동으로 삭제되지 않습니다. Premium Storage를 구독하는 경우 디스크에 대한 스토리지 요금이 계속 부과됩니다. 자세한 내용은 [Premium Storage 사용 시 가격 책정 및 청구](https://azure.microsoft.com/pricing/details/storage/page-blobs/)를 참조하세요.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 머신에 다시 연결할 수 있습니다.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>VM에 연결하여 디스크 탑재 해제

CLI 또는 포털을 사용하여 디스크를 분리하려면 먼저 디스크를 탑재 해제하고 fstab 파일에서 해당 참조를 제거해야 합니다.

VM에 연결합니다. 이 예제에서 VM의 공용 IP 주소는 *10.0.1.4* 이고 사용자 이름은 *azureuser* 입니다. 

```bash
ssh azureuser@10.0.1.4
```

먼저, 분리할 데이터 디스크를 찾습니다. 다음 예제에서는 dmesg를 사용하여 SCSI 디스크를 필터링합니다.

```bash
dmesg | grep SCSI
```

다음 예제와 유사하게 출력됩니다.

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

여기서 *sdc* 는 분리할 디스크입니다. 또한 디스크의 UUID를 알아야 합니다.

```bash
sudo -i blkid
```

출력은 다음 예제와 유사합니다.

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


*/etc/fstab* 파일을 편집하여 디스크에 대한 참조를 제거합니다. 

> [!NOTE]
> **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

텍스트 편집기에서 다음과 같이 */etc/fstab* 파일을 엽니다.

```bash
sudo vi /etc/fstab
```

이 예제에서는 */etc/fstab* 파일에서 다음 줄에서 삭제해야 합니다.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

`umount`를 사용하여 디스크를 탑재 해제합니다. 다음 예제에서는 */datadrive* 탑재 지점에서 */dev/sdc1* 파티션을 탑재 해제합니다.

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Azure CLI를 사용하여 데이터 디스크 분리 

이 예제에서는 *myResourceGroup* 의 VM *myVM* 에서 *myDataDisk* 디스크를 분리합니다.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.


## <a name="detach-a-data-disk-using-the-portal"></a>포털을 사용하여 데이터 디스크 분리

1. 왼쪽 메뉴에서 **Virtual Machines** 을 선택합니다.
1. 가상 컴퓨터 블레이드에서 **디스크** 를 선택합니다.
1. 디스크를 분리하려면 **디스크** 블레이드에서 분리할 데이터 디스크의 맨 오른쪽에 있는 **X** 단추를 선택합니다.
1. 디스크를 제거한 후에 블레이드 상단에서 **저장** 을 선택합니다.

디스크가 스토리지에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다. 디스크가 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계
데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](add-disk.md)할 수 있습니다.

디스크를 삭제하여 더 이상 스토리지 비용이 발생하지 않도록 하려면 [연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제 - Azure Portal](../disks-find-unattached-portal.md)을 참조하세요.