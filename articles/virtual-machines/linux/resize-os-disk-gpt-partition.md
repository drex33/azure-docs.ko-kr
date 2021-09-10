---
title: GPT 파티션이 있는 OS 디스크 크기 조정
description: 이 문서에서는 Linux에서 GPT(GUID 파티션 테이블) 파티션이 있는 OS 디스크의 크기를 조정하는 방법에 대한 지침을 제공합니다.
ms.topic: how-to
author: kailashmsft
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: ca7a018accb0410c2656d7aeb4282b42f2756e39
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688226"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT 파티션이 있는 OS 디스크 크기 조정

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

> [!NOTE]
> 이 문서는 GPT(GUID 파티션 테이블) 파티션이 있는 OS 디스크에만 적용됩니다.

이 문서에서는 Linux에서 GPT 파티션이 있는 OS 디스크의 크기를 늘리는 방법을 설명합니다. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS 디스크에 MBR 파티션과 GPT 파티션 중 어느 것이 있는지 확인

`parted` 명령을 사용하여 디스크 파티션이 MBR(마스터 부트 레코드) 파티션과 GPT 파티션 중 어느 것으로 만들어졌는지 확인합니다.

### <a name="mbr-partition"></a>MBR 파티션

다음 출력에서 **Partition Table** 값이 **msdos** 로 표시됩니다. 이 값은 MBR 파티션임을 나타냅니다.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT 파티션

다음 출력에서 **Partition Table** 값이 **gpt** 로 표시됩니다. 이 값은 GPT 파티션임을 나타냅니다.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

VM(가상 머신)의 OS 디스크에 GPT 파티션이 있는 경우 OS 디스크의 크기를 늘립니다.

## <a name="increase-the-size-of-the-os-disk"></a>OS 디스크의 크기 늘리기

다음 지침은 Linux 보증 배포판에 적용됩니다.

> [!NOTE]
> 계속하기 전에 VM의 백업 복사본을 만들거나 OS 디스크의 스냅샷을 만드세요.

### <a name="ubuntu"></a>Ubuntu

Ubuntu 16.*x* 및 18.*x* 에서 OS 디스크의 크기를 늘리려면 다음을 수행합니다.

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 다시 시작한 다음, **루트** 사용자로 VM에 로그인합니다.
1. OS 디스크에 늘어난 파일 시스템 크기가 표시되는지 확인합니다.

다음 예에서는 포털에서 OS 디스크의 크기가 100GB로 조정되었습니다. **/** 에 탑재된 **/dev/sda1** 파일 시스템이 이제 97GB로 표시됩니다.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

SUSE 12 SP4, SAP용 SUSE SLES 12, SUSE SLES 15, SAP용 SUSE SLES 15에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 다시 시작합니다.

VM이 다시 시작되면 다음 단계를 완료합니다.

1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.

   ```
   # sudo -i
   ```

1. 다음 명령을 사용하여 파티션 크기를 조정하는 데 사용할 **growpart** 패키지를 설치합니다.

   ```
   # zypper install growpart
   ```

1. `lsblk` 명령을 사용하여 파일 시스템( **/** )의 루트에 탑재된 파티션을 찾습니다. 이 경우 디바이스 **sda** 의 파티션 4가 **/** 에 탑재된 것을 볼 수 있습니다.

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `growpart` 명령 및 이전 단계에서 확인된 파티션 번호를 사용하여 필요한 파티션의 크기를 조정합니다.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. `lsblk` 명령을 다시 실행하여 파티션이 늘어났는지 확인합니다.

   다음 출력에서 **/dev/sda4** 파티션의 크기가 46.5GB로 조정된 것을 볼 수 있습니다.
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `lsblk` 명령을 `-f` 플래그와 함께 사용하여 OS 디스크의 파일 시스템 유형을 식별합니다.

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. 파일 시스템 유형을 기준으로 적절한 명령을 사용하여 파일 시스템의 크기를 조정합니다.
   
   **xfs** 에 다음 명령을 사용합니다.
   
   ```
   #xfs_growfs /
   ```
   
   예제 출력:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   **ext4** 에 다음 명령을 사용합니다.
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. 다음 명령을 사용하여 **df -Th** 의 파일 시스템 크기가 늘어났는지 확인합니다.
   
   ```
   #df -Thl
   ```
   
   예제 출력:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   앞의 예제에서 OS 디스크의 파일 시스템 크기가 늘어난 것을 확인할 수 있습니다.

### <a name="rhel-with-lvm"></a>LVM을 사용하는 RHEL

1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. `lsblk` 명령을 사용하여 파일 시스템( **/** )의 루트에 탑재된 논리 볼륨(LV)을 확인합니다. 이 경우 **rootvg-rootlv** 가 **/** 에 탑재된 것을 알 수 있습니다. 다른 파일 시스템을 원하는 경우 이 문서 전체에서 LV 및 탑재 지점을 대체합니다.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. 루트 파티션이 포함된 LVM 볼륨 그룹(VG)에 사용 가능한 공간이 있는지 확인합니다. 사용 가능한 공간이 있으면 12단계로 건너뜁니다.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   이 예제에서 **여유 PE/크기** 줄은 볼륨 그룹에 38.02GB의 여유 공간이 있음을 보여 줍니다. 볼륨 그룹에 공간을 추가하기 전에 디스크 크기를 조정할 필요가 없습니다.

1. LVM을 사용하는 RHEL 7.*x* 에서 OS 디스크의 크기를 늘리려면 다음을 수행합니다.

   1. VM을 중지합니다.
   1. 포털에서 OS 디스크의 크기를 늘립니다.
   1. VM을 시작합니다.

1. VM이 다시 시작되면 다음 단계를 완료합니다.

   - **cloud-utils-growpart** 패키지를 설치하여 OS 디스크의 크기와 GPT 디스크 레이아웃의 gdisk 처리기를 늘리는 데 필요한 **growpart** 명령을 제공합니다. 이러한 패키지는 대부분의 마켓플레이스 이미지에 미리 설치되어 있습니다.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. `pvscan` 명령을 사용하여 **rootvg** 라는 볼륨 그룹의 LVM 물리적 볼륨(PV)을 포함하는 디스크 및 파티션을 결정합니다. 대괄호( **[** 및 **]** ) 사이에 나열된 크기와 사용 가능한 공간을 확인합니다.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. `lsblk`를 사용하여 파티션의 크기를 확인합니다. 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. `growpart`, 디바이스 이름 및 파티션 번호를 사용하여 이 PV가 포함된 파티션을 확장합니다. 이렇게 하면 지정된 파티션이 디바이스에서 사용 가능한 모든 인접 공간을 사용하도록 확장됩니다.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. `lsblk` 명령을 다시 사용하여 파티션이 올바른 크기로 조정되었는지 확인합니다. 예제에서 **sda4** 가 63GB에서 95GB로 변경되었습니다.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 새로 확장된 파티션의 나머지 부분을 사용하도록 PV를 확장합니다.

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. PV의 새 크기를 원래 **[크기/여유]** 값과 비교하여 올바른 크기인지 확인합니다.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. 원하는 논리 볼륨(LV)을 원하는 양만큼 확장합니다. 볼륨 그룹에서 사용 가능한 모든 공간만큼 확장할 필요는 없습니다. 다음 예제에서는 **/dev/mapper/rootvg-rootlv** 의 크기가 2GB에서 12GB로 조정됩니다(10GB 증가). 이 명령은 파일 시스템의 크키도 조정합니다.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   예제 출력:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. 이 `lvresize` 명령은 LV에서 파일 시스템에 대한 적절한 크기 조정 명령을 자동으로 호출합니다. 다음 명령을 사용하여 **/** 에 탑재된 **/dev/mapper/rootvg-rootlv** 의 파일 시스템 크기가 늘어났는지 확인합니다.

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   예제 출력:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> 동일한 절차를 사용하여 다른 논리 볼륨의 크기를 조정하려면 12단계에서 LV 이름을 변경하세요.


### <a name="rhel-raw"></a>RHEL RAW

RHEL RAW 파티션에서 OS 디스크의 크기를 늘리려면 다음을 수행합니다.

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 시작합니다.

VM이 다시 시작되면 다음 단계를 완료합니다.

1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. VM이 다시 시작되면 다음 단계를 완료합니다.

   - **cloud-utils-growpart** 패키지를 설치하여 OS 디스크의 크기와 GPT 디스크 레이아웃의 gdisk 처리기를 늘리는 데 필요한 **growpart** 명령을 제공합니다. 이 패키지는 대부분의 마켓플레이스 이미지에 미리 설치되어 있습니다.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. **lsblk -f** 명령을 사용하여 루트( **/** ) 파티션을 포함하는 파티션 및 파일 시스템 형식을 확인합니다.

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. 확인을 위해 먼저 **gdisk** 를 사용하여 sda 디스크의 파티션 테이블을 나열합니다. 이 예제에서는 29.0GiB의 파티션 2가 있는 48GB 디스크를 살펴봅니다. Azure Portal에서 디스크가 30GB에서 48GB로 확장되었습니다.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. **growpart** 명령을 사용하여 루트의 파티션(이 경우 sda2)을 확장합니다. 이 명령을 사용하면 디스크의 모든 인접 공간을 사용하도록 파티션이 확장됩니다.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. 이제 다시 **gdisk** 를 사용하여 새 파티션 테이블을 출력합니다.  파티션 2가 47.0GiB로 확장되었습니다.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. **xfs_growfs** 를 사용하여 파티션의 파일 시스템을 확장합니다. 이는 표준 마켓플레이스에서 생성한 RedHat 시스템에 적합합니다.

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. **df** 명령을 사용하여 새 크기가 반영되었는지 확인합니다.

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
