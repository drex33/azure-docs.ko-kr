---
title: Azure CLI를 사용하여 Linux VM에 데이터 디스크 추가하기
description: Azure CLI를 사용하여 Linux VM에 영구 데이터 디스크를 추가하는 방법 알아보기
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 05/12/2021
ms.author: cynthn
ms.openlocfilehash: 8d518bde3dc86c871f66f64dec8f39fc8b68e763
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692269"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM에 디스크 추가

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 유지 관리 또는 크기 조정으로 인해 VM이 다시 프로비전되더라도 데이터를 유지할 수 있도록 VM에 영구 디스크를 연결하는 방법을 보여 줍니다.


## <a name="attach-a-new-disk-to-a-vm"></a>VM에 새 디스크 연결

VM에 새 빈 데이터 디스크를 추가하려는 경우 [az vm disk attach](/cli/azure/vm/disk) 명령에 `--new` 매개 변수를 사용합니다. VM이 가용성 영역에 있는 경우 VM과 동일한 영역에 디스크가 자동으로 생성됩니다. 자세한 내용은 [가용성 영역 개요](../../availability-zones/az-overview.md)를 참조하세요. 다음 예제에서는 크기가 50GB이고 이름이 *myDataDisk* 인 디스크를 만듭니다.

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>기존 디스크 연결

기존 디스크를 연결하려면 디스크 ID를 찾아 [az vm disk attach](/cli/azure/vm/disk) 명령에 ID를 전달합니다. 다음 예제에서는 *myResourceGroup* 에서 이름이 *myDataDisk* 인 디스크를 쿼리한 다음 이름이 *myVM* 인 VM에 연결합니다.

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>디스크 포맷 및 탑재하기

Linux VM에서 사용할 수 있도록 새 디스크를 분할, 포맷 및 탑재하려면 VM에 SSH합니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](mac-create-ssh-keys.md)을 참조하세요. 다음 예제에서는 *azureuser* 를 사용자 이름으로 하는 공용 IP 주소 *10.123.123.25* 를 사용하여 VM에 연결합니다.

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>디스크 찾기

VM에 연결되면 디스크를 찾아야 합니다. 이 예제에서는 `lsblk`을 사용하여 디스크를 나열합니다. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

다음 예제와 유사하게 출력됩니다.

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

여기서는 50G 용량인 디스크 `sdc`이 우리가 찾는 디스크입니다. 여러 디스크를 추가하고 크기만으로 어떤 디스크인지 확실하지 않은 경우 포털에서 VM 페이지로 이동하여 **디스크** 를 선택하고 **데이터 디스크** 에서 디스크의 LUN 번호를 확인할 수 있습니다. 포털의 LUN 번호를 LUN인 출력의 **HTCL** 부분의 마지막 번호와 비교합니다.


### <a name="format-the-disk"></a>디스크 포맷하기

디스크를 `parted`로 포맷하는 경우 디스크 크기가 2TiB(tebibytes) 이상일 경우 GPT 분할을 사용해야 하며, 2TiB 미만이면 MBR이나 GPT 분할을 사용할 수 있습니다. 

> [!NOTE]
> 배포판에 사용할 수 있는 최신 버전 `parted`을 사용하는 것이 좋습니다.
> 디스크 용량이 2TiB 이상인 경우 GPT 분할을 사용해야 합니다. 디스크 용량이 2TiB 미만이라면 MBR 또는 GPT 분할을 사용할 수 있습니다.  


다음 예제에서는 대부분의 VM에서 첫 번째 데이터 디스크가 일반적으로 사용하는 환경인 `/dev/sdc`에서 `parted`을 사용합니다. `sdc`을 디스크에 맞는 옵션으로 바꿉니다. [XFS](https://xfs.wiki.kernel.org/) 파일 시스템을 사용하여 형식을 지정하기도 합니다.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

[`partprobe`](https://linux.die.net/man/8/partprobe) 유틸리티를 사용하여 커널이 새 파티션 및 파일 시스템을 인식하는지 확인합니다. `partprobe`을 사용하지 않으면 blkid 또는 lslbk 명령이 새 파일 시스템의 UUID를 즉시 반환하지 않을 수 있습니다.


### <a name="mount-the-disk"></a>디스크 탑재

이제 `mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다. 다음 예제에서는 `/datadrive`에 디렉터리를 만듭니다.

```bash
sudo mkdir /datadrive
```

`mount`를 사용하여 파일 시스템을 탑재합니다. 다음 예제에서는 `/dev/sdc1` 파티션을 `/datadrive` 탑재 지점에 탑재합니다.

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>탑재 유지

다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 */etc/fstab* 파일에 추가해야 합니다. 또한 */etc/fstab* 의 UUID(Universally Unique Identifier)를 사용하여, 디바이스 이름(예: */dev/sdc1*) 대신 드라이브를 조회하는 편이 좋습니다. 부팅하는 동안 OS에서 디스크 오류를 검색하는 경우 UUID를 사용하여 지정된 위치에 탑재되어 있는 잘못된 디스크를 회피합니다. 그런 다음, 남아 있는 데이터 디스크를 동일한 디바이스 ID에 할당합니다. 새 드라이브의 UUID를 찾으려면 `blkid` 유틸리티를 사용합니다.

```bash
sudo blkid
```

출력은 다음 예제와 유사합니다.

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

텍스트 편집기에서 다음과 같이 */etc/fstab* 파일을 엽니다.

```bash
sudo nano /etc/fstab
```

이 예제에서는 이전 단계에서 만든 `/dev/sdc1` 디바이스의 UUID 값과 탑재 지점 `/datadrive`를 사용합니다. `/etc/fstab` 파일의 끝에 다음 줄을 추가합니다.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

이 예제에서는 나노 편집기를 사용하므로 파일 편집 완료 시 `Ctrl+O`을 사용하여 파일을 작성하고 `Ctrl+X`로 편집기를 종료합니다.

> [!NOTE]
> 나중에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 대부분의 배포는 *nofail* 및/또는 *nobootwait* fstab 옵션을 제공합니다. 이러한 옵션을 사용하면 디스크가 부팅 시 탑재되지 않더라도 시스템을 부팅할 수 있습니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.
>
> *nofail* 옵션은 파일 시스템이 손상되었거나 디스크가 부팅 시 존재하지 않더라도 VM이 시작되도록 합니다. 이 옵션이 없으면 [FSTAB 오류로 인해 Linux에 SSH를 사용할 수 없음](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)(영문)에 설명되어 있는 동작이 발생할 수 있습니다.
>
> Fstab을 수정하여 부팅 실패가 발생한 경우 Azure VM 직렬 콘솔을 사용하여 VM에 대한 콘솔 액세스를 사용할 수 있습니다. 기타 세부 정보는 [직렬 콘솔 설명서](/troubleshoot/azure/virtual-machines/serial-console-linux)를 참조하세요.

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure에서 Linux에 대한 TRIM/UNMAP 지원

일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이 기능은 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 Azure에 알리기 위해 표준 스토리지에서 주로 유용하며, 큰 파일을 만들고 삭제하는 경우 비용을 절약할 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

- */etc/fstab* 에 `discard` 탑재 옵션을 사용합니다. 예:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```

- 경우에 따라 `discard` 옵션을 사용하면 성능이 저하될 수 있습니다. 또는 `fstrim` 명령을 명령줄에서 수동으로 실행하거나, 또는 정기적으로 실행하기 위해 crontab에 추가할 수 있습니다.

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>다음 단계

* Linux VM을 올바르게 구성했는지 확인하려면 [Linux 컴퓨터 성능 최적화](/previous-versions/azure/virtual-machines/linux/optimization) 권장 사항을 검토합니다.
* 디스크를 추가하여 스토리지 용량을 확장하고 추가 성능이 필요할 경우 [RAID를 구성](/previous-versions/azure/virtual-machines/linux/configure-raid)합니다.