---
title: Azure Disk Encryption을 사용하여 암호화된 디스크 크기를 조정하는 방법
description: 이 문서에서는 논리 볼륨 관리를 사용하여 ADE 암호화된 디스크 크기를 조정하는 방법을 안내합니다.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: bd23b2381fb042d80297c7b29fad382787ef1281
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697180"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Azure Disk Encryption을 사용하는 논리 볼륨 관리 디바이스의 크기를 조정하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 Azure Disk Encryption을 사용하는 데이터 디스크의 크기를 조정하는 방법을 알아봅니다. 디스크 크기를 조정하려면 Linux에서 LVM(논리 볼륨 관리)을 사용합니다. 이러한 단계는 여러 시나리오에 적용됩니다.

다음 환경에서 이 크기 조정 프로세스를 사용할 수 있습니다.

- Linux 배포:
    - Red Hat Enterprise Linux(RHEL) 7.0 이상
    - Ubuntu 16 이상
    - SUSE 12 이상
- Azure Disk Encryption 버전: 
    - 단일 패스 확장
    - 이중 패스 확장

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- 기존 LVM 구성. 자세한 내용은 [Linux VM에서 LVM 구성](/previous-versions/azure/virtual-machines/linux/configure-lvm)을 참조하세요.

- Azure Disk Encryption으로 이미 암호화된 디스크. 자세한 내용은 [암호화된 디바이스에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md)을 참조하세요.

- Linux 및 LVM을 사용 경험.

- Azure의 데이터 디스크에 대해 */dev/disk/scsi1/* 경로 사용 경험. 자세한 내용은 [Linux VM 디바이스 이름 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)을 참조하세요. 

## <a name="scenarios"></a>시나리오

이 문서의 절차는 다음 시나리오에 적용됩니다.

- 기존 LVM 및 LVM-on-crypt 구성
- 기존 LVM 암호화 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>기존 LVM 및 LVM-on-crypt 구성

기존 LVM 및 LVM-on-crypt 구성은 볼륨 그룹(VG)에 사용 가능한 공간이 있는 경우 논리 볼륨(LV)을 확장합니다.

### <a name="traditional-lvm-encryption"></a>기존 LVM 암호화 

기존 LVM 암호화에서 LV가 암호화됩니다. 전체 디스크가 암호화되지 않습니다.

기존 LVM 암호화를 사용하여 다음을 수행할 수 있습니다.

- 새 실제 볼륨(PV)을 추가하면 LV를 확장합니다.
- 기존 PV의 크기를 조정하면 LV를 확장합니다.

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

디스크 암호화에 권장되는 방식은 LVM-on-crypt입니다. 이 방식을 사용하면 LV뿐 아니라 전체 디스크가 암호화됩니다.

LVM-on-crypt를 사용하여 다음을 수행할 수 있습니다. 

- 새 PV를 추가하면 LV를 확장합니다.
- 기존 PV의 크기를 조정하면 LV를 확장합니다.

> [!NOTE]
> 동일한 VM에서 기존 LVM 암호화와 LVM-on-crypt를 조합하지 않는 것이 좋습니다.

다음 섹션에서는 LVM과 LVM-on-crypt를 사용하는 방법의 예시를 보여 줍니다. 예시에서는 디스크, PV, VG, LV, 파일 시스템, UUID(범용 고유 식별자), 탑재 지점에 기존 값을 사용합니다. 해당 값을 사용자 환경에 맞게 사용자 고유의 값으로 바꿉니다.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>VG에 사용 가능한 공간이 있는 경우 LV 확장

LV의 크기를 조정하는 기존 방법은 VG에 사용 가능한 공간이 있으면 LV를 확장하는 것입니다. 암호화되지 않은 디스크, 기존 LVM 암호화 볼륨, LVM-on-crypt 구성에 이 방식을 사용할 수 있습니다.

1. 확장하려는 파일 시스템의 현재 크기를 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. VG에 LV를 늘릴 수 있는 충분한 공간이 있는지 확인합니다.

    ``` bash
    vgs
    ```

    ![VG의 공간을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    `vgdisplay`를 사용할 수도 있습니다.

    ``` bash
    vgdisplay vgname
    ```

    ![VG의 공간을 확인하는 VG 디스플레이 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 크기를 조정해야 하는 LV를 식별합니다.

    ``` bash
    lsblk
    ```

    ![l s b l k 명령의 결과를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    LVM-on-crypt에서의 차이점은, 이 출력값을 통해 암호화된 레이어가 디스크 수준에 있음을 알 수 있다는 점입니다.

    ![l s b l k 명령의 결과를 보여 주는 스크린샷. 출력이 강조 표시됩니다. 암호화된 레이어를 보여 줍니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. LV 크기를 확인합니다.

    ``` bash
    lvdisplay lvname
    ```

    ![논리 볼륨 크기를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. `-r`을 사용하여 LV 크기를 확장함으로써 온라인으로 파일 시스템 크기를 조정합니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![논리 볼륨의 크기를 확장하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. LV 및 파일 시스템의 새로운 크기를 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![LV 및 파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    크기 출력은 LV 및 파일 시스템의 크기가 성공적으로 조정되었음을 보여 줍니다.

LV 정보를 다시 확인하여 LV 수준에서 변경 내용을 확인할 수 있습니다.

``` bash
lvdisplay lvname
```

![새 크기를 확인하는 코드를 보여 주는 스크린샷. 크기가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>새 PV를 추가하여 기존 LVM 볼륨 확장

새 디스크를 추가하여 VG 크기를 늘려야 하는 경우 새 PV를 추가하여 기존 LVM 볼륨을 확장합니다.

1. 확장하려는 파일 시스템의 현재 크기를 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 현재 크기를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 현재 PV 구성을 확인합니다.

    ``` bash
    pvs
    ```

    ![현재 PV 구성을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 현재 VG 정보를 확인합니다.

    ``` bash
    vgs
    ```

    ![현재 볼륨 그룹 정보를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 현재 디스크 목록을 확인합니다. */dev/disk/azure/scsi1/* 에서 디바이스를 확인하여 데이터 디스크를 식별합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![현재 디스크 목록을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. `lsblk` 출력을 확인합니다. 

    ``` bash
    lsbk
    ```

    ![l s b l k의 출력을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. [Linux VM에 데이터 디스크 연결](attach-disk-portal.md)의 지침에 따라 새 디스크를 VM에 연결합니다.

7. 디스크 목록에서 새 디스크를 확인합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![디스크 목록을 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![l s b l k를 사용하여 디스크 목록을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 새 데이터 디스크 위에 새 PV를 만듭니다.

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![새 PV를 만드는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    이 메서드는 파티션이 없는 PV로 전체 디스크를 사용합니다. 또는 `fdisk`를 사용하여 파티션을 만든 다음 파티션을 `pvcreate`에 사용할 수 있습니다.

9. PV 목록에 PV가 추가되었는지 확인합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨 목록을 보여 주는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 새 PV를 추가하여 VG를 확장합니다.

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![볼륨 그룹을 확장하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 새 VG 크기를 확인합니다.

    ``` bash
    vgs
    ```

    ![볼륨 그룹 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. `lsblk`를 사용하여 크기를 조정해야 하는 LV를 식별합니다.

    ``` bash
    lsblk
    ```

    ![크기를 조정해야 하는 로컬 볼륨을 식별하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. `-r`을 사용하여 LV 크기를 확장함으로써 온라인으로 파일 시스템 크기를 늘립니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![온라인으로 파일 시스템의 크기를 늘리는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. LV 및 파일 시스템의 새 크기를 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![로컬 볼륨 및 파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >기존 LVM 구성에서 Azure Data Encryption을 사용하는 경우 암호화된 레이어는 디스크 수준이 아닌 LV 수준에서 만들어집니다.
    >
    >이때 암호화된 레이어가 새 디스크로 확장됩니다. 실제 데이터 디스크에는 플랫폼 수준의 암호화 설정이 없으므로 암호화 상태가 업데이트되지 않습니다.
    >
    >따라서 LVM-on-crypt 방식을 사용하는 것이 좋습니다. 

15. 포털에서 암호화 정보를 확인합니다.

    ![포털의 암호화 정보를 보여 주는 스크린샷. 디스크 이름 및 암호화가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    디스크의 암호화 설정을 업데이트하려면 새 LV를 추가하고 VM에 확장을 사용 설정합니다.
    
16. 새 LV를 추가하고 파일 시스템을 만든 다음 `/etc/fstab`에 추가합니다.

17. 암호화 확장을 다시 설정합니다. 이번에는 플랫폼 수준에서 새 데이터 디스크에 대한 암호화 설정을 스탬프합니다. CLI 예제는 다음과 같습니다.

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 포털에서 암호화 정보를 확인합니다.

    ![포털의 암호화 정보를 보여 주는 스크린샷. 디스크 이름 및 암호화 정보가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

암호화 설정이 업데이트되면 새 LV를 삭제할 수 있습니다. 또한 만든 `/etc/fstab` 및 `/etc/crypttab`에서 항목을 삭제합니다.

![새 논리 볼륨을 삭제하는 코드를 보여 주는 스크린샷. 삭제된 F S 탭 및 crypt 탭이 강조 표시됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

다음 단계에 따라 정리를 완료합니다.

1. LV를 분리합니다.

    ``` bash
    umount /mountpoint
    ```

1. 볼륨의 암호화된 레이어를 닫습니다.

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. LV를 삭제합니다.

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>기존 PV의 크기를 조정하여 기존 LVM 볼륨 확장

일부 시나리오에서는 제한 사항에 따라 기존 디스크의 크기를 조정해야 할 수 있습니다. 방법은 다음과 같습니다.

1. 암호화된 디스크를 식별합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![암호화된 디스크를 식별하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![암호화된 디스크를 식별하는 다른 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. PV 정보를 확인합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    결과 이미지는 모든 PV의 모든 공간이 현재 사용 중임을 보여 줍니다.

3. VG 정보를 확인합니다.

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![볼륨 그룹에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 디스크 크기를 확인합니다. `fdisk` 또는 `lsblk`를 사용하여 드라이브 크기를 나열할 수 있습니다.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![디스크 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    여기에서 `lsblk -fs`를 사용하여 어떤 PV가 어떤 LV와 연결되는지 파악할 수 있습니다. `lvdisplay`를 실행하여 연결을 식별할 수 있습니다.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![실제 볼륨과 로컬 볼륨의 연결을 식별하는 다른 방법을 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    이 경우 4개의 데이터 드라이브는 모두 동일한 VG 및 단일 LV의 일부입니다. 구성이 다를 수 있습니다.

5. 현재 파일 시스템 사용률을 확인합니다.

    ``` bash
    df -h /datalvm*
    ```

    ![파일 시스템 사용률을 확인하는 코드를 보여 주는 스크린샷. 명령 및 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. [Azure 관리 디스크 확장](expand-disks.md#expand-an-azure-managed-disk)의 안내에 따라 데이터 디스크의 크기를 조정합니다. 포털, CLI 또는 PowerShell을 사용할 수 있습니다.

    >[!IMPORTANT]
    >VM을 실행하는 동안에는 가상 디스크의 크기를 조정할 수 없습니다. 이 단계를 위해 VM의 할당을 취소합니다.

7. VM을 시작하고 `fdisk`를 사용하여 새 크기를 확인합니다.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![디스크 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    이 경우에는 `/dev/sdd` 크기가 5G에서 20G로 조정되었습니다.

8. 현재 PV 크기를 확인합니다.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![PV의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    디스크 크기가 조정되었지만 PV의 크기는 이전 상태로 유지됩니다.

9. PV의 크기를 조정합니다.

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![실제 볼륨의 크기를 조정하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. PV 크기를 확인합니다.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![실제 볼륨의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    크기를 조정하려는 모든 디스크에 동일한 절차를 적용합니다.

11. VG 정보를 확인합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    이제 VG에 LV에 할당할 충분한 공간이 있습니다.

12. LV 크기를 조정합니다.

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![LV의 크기를 조정하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 파일 시스템의 크기를 확인합니다.

    ``` bash
    df -h /datalvm2
    ```

    ![파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>새 PV를 추가하여 LVM-on-crypt 볼륨 확장

새 PV를 추가하여 LVM-on-crypt 볼륨을 확장할 수도 있습니다. 이 방식은 [암호화된 디바이스에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md#general-steps)의 단계를 따릅니다. 새 디스크를 추가하고 LVM-on-crypt 구성에서 설정하는 방법을 설명하는 섹션을 참조하세요.

이 방식을 사용하여 기존 LV에 공간을 추가할 수 있습니다. 또는 새 VG 또는 LV를 만들 수 있습니다.

1. VG의 현재 크기를 확인합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 확장하려는 파일 시스템 및 LV의 크기를 확인합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![로컬 볼륨의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. VM에 새 데이터 디스크를 추가하고 식별합니다.

    새 디스크를 추가하기 전에 디스크를 확인합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![디스크 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    새 디스크를 추가하기 전에 디스크를 확인하는 또 다른 방법은 다음과 같습니다.

    ``` bash
    lsblk
    ```

    ![디스크 크기를 확인하는 다른 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    새 디스크를 추가하기 위해 PowerShell, Azure CLI 또는 Azure Portal을 사용할 수 있습니다. 자세한 내용은 [Linux VM에 데이터 디스크 추가](attach-disk-portal.md)를 참조하세요.

    새로 추가되는 디바이스에는 커널 이름 체계가 적용됩니다. 새 드라이브에는 일반적으로 다음으로 사용 가능한 문자가 할당됩니다. 이 경우, 추가된 디스크는 `sdd`입니다.

4. 디스크를 확인하여 새 디스크가 추가되었는지 확인합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![디스크를 나열하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![출력에 새로 추가된 디스크가 표시된 스크린샷](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 최근 추가된 디스크를 기반으로 파일 시스템을 만듭니다. `/dev/disk/azure/scsi1/`에서 연결된 디바이스와 디스크를 비교합니다.

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![파일 시스템을 만드는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![파일 시스템을 만들고 디스크를 연결된 장치와 비교하는 추가 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 새로 추가된 디스크의 임시 탑재 지점을 만듭니다.

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 최근에 만든 파일 시스템을 `/etc/fstab`에 추가합니다.

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 새로 만든 파일 시스템을 탑재합니다.

    ``` bash
    mount -a
    ```

9. 새 파일 시스템이 탑재되어 있는지 확인합니다.

    ``` bash
    df -h
    ```

    ![파일 시스템이 탑재되었는지 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![파일 시스템이 탑재되었는지 확인하는 추가 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 데이터 드라이브에 대해 이전에 시작한 암호화를 다시 시작합니다.

    >[!TIP]
    >LVM-on-crypt의 경우에는 `EncryptFormatAll`을 사용하는 것이 좋습니다. 그렇지 않으면 추가 디스크 설정 시 이중 암호화가 발생할 수 있습니다.
    >
    >자세한 내용은 [암호화된 디바이스에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md)을 참조하세요.

    예를 들면 다음과 같습니다.

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    암호화가 완료되면 새로 추가된 디스크의 crypt 레이어가 표시됩니다.

    ``` bash
    lsblk
    ```

    ![crypt 레이어를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 새 디스크의 암호화된 레이어를 분리합니다.

    ``` bash
    umount ${newmount}
    ```

12. 현재 PV 정보를 확인합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 디스크의 암호화된 레이어를 기반으로 PV를 만듭니다. 이전 `lsblk`명령에서 디바이스 이름을 가져옵니다. 디바이스 이름 앞에 `/dev/` 매퍼를 추가하여 PV를 만듭니다.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![암호화된 레이어에 실제 볼륨을 만드는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    현재 `ext4 fs` 서명 지우기에 대한 경고가 표시됩니다. 이 경고는 정상적인 것입니다. 이 질문에 `y`로 대답합니다.

14. LVM 구성에 새 PV가 추가되었는지 확인합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨이 LVM 구성에 추가되었는지 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 확장해야 하는 VG에 새 PV를 추가합니다.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![볼륨 그룹에 실제 볼륨을 추가하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. VG의 새 크기 및 사용 가능한 공간을 확인합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹의 크기 및 사용 가능한 공간을 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    `Total PE`개수와 `Free PE / Size`의 증가를 확인합니다.

17. LV 및 파일 시스템의 크기를 확장합니다. `lvextend`에서 `-r` 옵션을 사용합니다. 이 예제에서는 지정된 LV에 VG의 사용 가능한 총 공간을 추가합니다.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![로컬 볼륨 및 파일 시스템의 크기를 확장하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

다음 단계를 수행하여 변경 내용을 확인합니다.

1. LV의 크기를 확인합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![로컬 볼륨의 새 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. 파일 시스템의 새 크기를 확인합니다.

    ``` bash
    df -h mountpoint
    ```

    ![파일 시스템의 새 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. 암호화된 레이어에 LVM 레이어가 있는지 확인합니다.

    ``` bash
    lsblk
    ```

    ![암호화된 레이어에 LVM 레이어가 있는지 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    옵션 없이 `lsblk`를 사용하면 탑재 지점이 여러 번 표시됩니다. 명령은 디바이스 및 LV를 기준으로 정렬합니다. 

    `lsblk -fs`를 사용하는 것이 좋습니다. 이 명령에서는 탑재 지점이 한 번만 표시되도록 `-fs` 정렬 순서를 반대로 바꿉니다. 디스크가 여러 번 표시됩니다.

    ``` bash
    lsblk -fs
    ```

    ![암호화된 레이어에 LVM 레이어가 있는지 확인하는 다른 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>기존 PV 크기를 조정하여 crypt 볼륨에서 LVM 확장

1. 암호화된 디스크를 식별합니다.

    ``` bash
    lsblk
    ```

    ![암호화된 디스크를 식별하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![암호화된 디스크를 식별하는 다른 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. PV 정보를 확인합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. VG 정보를 확인합니다.

    ``` bash
    vgs
    ```

    ![볼륨 그룹에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. LV 정보를 확인합니다.

    ``` bash
    lvs
    ```

    ![로컬 볼륨에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 파일 시스템 사용률을 확인합니다.

    ``` bash
    df -h /mountpoint(s)
    ```

    ![사용 중인 파일 시스템의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 디스크 크기를 확인합니다.

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![디스크 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 데이터 디스크 크기를 조정합니다. Portal, CLI 또는 PowerShell을 사용할 수 있습니다. 자세한 내용은 [Linux VM에서 가상 하드 디스크 확장](expand-disks.md#expand-an-azure-managed-disk)의 디스크 크기 조정 섹션을 참조하세요. 

    >[!IMPORTANT]
    >VM을 실행하는 동안에는 가상 디스크의 크기를 조정할 수 없습니다. 이 단계를 위해 VM의 할당을 취소합니다.

8. 디스크 크기를 확인합니다.

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![디스크 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    이 경우 두 디스크의 크기가 2GB에서 4GB로 조정되었습니다. 하지만 파일 시스템, LV, PV의 크기는 동일하게 유지됩니다.

9. 현재 PV 크기를 확인합니다. LVM-on-crypt에서 PV는 `/dev/sd*` 디바이스가 아니라 `/dev/mapper/` 디바이스입니다.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![현재 실제 볼륨의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. PV의 크기를 조정합니다.

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![실제 볼륨의 크기를 조정하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 새 PV 크기를 확인합니다.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![실제 볼륨의 크기를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. PV에서 암호화된 레이어의 크기를 조정합니다.

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    크기를 조정하려는 모든 디스크에 동일한 절차를 적용합니다.

13. VG 정보를 확인합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    이제 VG에 LV에 할당할 충분한 공간이 있습니다.

14. LV 정보를 확인합니다.

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![로컬 볼륨에 대한 정보를 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 파일 시스템 사용률을 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 사용률을 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. LV 크기를 조정합니다.

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![로컬 볼륨의 크기를 조정하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    여기서 `-r` 옵션을 사용하여 파일 시스템의 크기도 조정합니다.

17. LV 정보를 확인합니다.

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![로컬 볼륨에 대한 정보를 가져오는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 파일 시스템 사용률을 확인합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템 사용률을 확인하는 코드를 보여 주는 스크린샷. 결과가 강조 표시됩니다.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

크기 조정이 필요한 다른 모든 LV에 동일한 크기 조정 절차를 적용합니다.

## <a name="next-steps"></a>다음 단계

[Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)