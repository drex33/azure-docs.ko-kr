---
title: Azure의 SLES에서 Pacemaker 설정 | Microsoft Docs
description: Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.custom: subject-rbac-steps
ms.date: 09/08/2021
ms.author: radeltch
ms.openlocfilehash: 63c7def5a76fba19eeef5192ebdfacd6225fbaa1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784302"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Azure에서 Pacemaker 클러스터를 설정하는 옵션에는 두 가지가 있습니다. Azure API를 통해 실패한 노드를 다시 시작하는 펜싱 에이전트를 사용하거나 SBD 디바이스를 사용할 수 있습니다.

SBD 디바이스에는 iSCSI 대상 서버 역할을 하고 SBD 디바이스를 제공하는 추가 가상 머신이 하나 이상 필요합니다. 단, 이러한 iSCSI 대상 서버를 다른 Pacemaker 클러스터와 공유할 수 있습니다. SBD 디바이스를 사용하는 이점은 온-프레미스에서 SBD 디바이스를 사용하는 경우에는 Pacemaker 클러스터의 작동 방식을 변경할 필요가 없습니다. 예를 들어, iSCSI 대상 서버의 OS 패치 동안 Pacemaker 클러스터에서 SBD 디바이스를 사용할 수 없게 하도록 하려면 최대 3개의 SBD 디바이스를 사용할 수 있습니다. Pacemaker당 2개 이상의 SBD 디바이스를 사용하려는 경우 여러 iSCSI 대상 서버를 배포하고 각 iSCSI 대상 서버에서 하나의 SBD를 연결해야 합니다. SBD 디바이스를 1개 또는 3개 사용하는 것이 좋습니다. SBD 디바이스를 2개만 구성한 상태에서 하나를 사용할 수 없게 되면 Pacemaker는 클러스터 노드를 자동으로 방어할 수 없게 됩니다. 하나의 iSCSI 대상 서버가 다운되었을 때 방어하려면 3개의 SBD 디바이스, 즉 SBD를 사용할 때 가장 탄력적 구성인 3개의 iSCSI 대상 서버를 사용해야 합니다.

Azure Fence 에이전트는 추가 가상 머신을 배포할 필요가 없습니다.   

![SLES의 Pacemaker 개요](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Linux Pacemaker 클러스터 노드 및 SBD 디바이스를 계획하고 배포할 경우, 전체 클러스터 구성의 전체적인 안정성을 제공하려면 관련된 VM과 SBD 디바이스를 호스트하는 VM 간의 라우팅이 [NVA](https://azure.microsoft.com/solutions/network-appliances/)와 같은 다른 디바이스를 통과하지 않아야 합니다. 그렇지 않으면 NVA에 관련된 문제 및 유지 관리 이벤트가 전체 클러스터 구성의 안정성에 부정적인 영향을 줄 수 있습니다. 이러한 장애가 발생하지 않도록, Linux Pacemaker 클러스터 노드 및 SBD 디바이스를 계획 및 배포할 때 클러스터 노드와 SBD 디바이스 간의 트래픽을 NVA 및 유사한 디바이스를 통해 전달하는 NVA 회람 규칙 또는 [사용자 정의 회람 규칙](../../../virtual-network/virtual-networks-udr-overview.md)을 정의하지 않습니다. 
>

## <a name="sbd-fencing"></a>SBD 펜싱

펜싱에 SBD 디바이스를 사용하려면 다음 단계를 수행하세요.

### <a name="set-up-iscsi-target-servers"></a>iSCSI 대상 서버 설정

먼저 iSCSI 대상 가상 머신을 만들어야 합니다. iSCSI 대상 서버는 여러 Pacemaker 클러스터와 공유할 수 있습니다.

1. 새 SLES 12 SP1 이상의 가상 머신을 배포하고 ssh를 통해 머신에 연결합니다. 컴퓨터가 클 필요는 없습니다. Standard_E2s_v3 또는 Standard_D2s_v3과 같은 가상 머신 크기이면 충분합니다. OS 디스크에 대해 Premium Storage를 사용해야 합니다.

모든 **iSCSI 대상 가상 머신** 에 대해 다음 명령을 실행합니다.

1. SLES 업데이트

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > OS를 업그레이드 또는 업데이트한 후 OS를 다시 부팅해야 할 수도 있습니다. 

1. 패키지 제거

   targetcli 및 SLES 12 SP3의 알려진 문제를 방지하려면 다음 패키지를 제거합니다. 찾을 수 없는 패키지에 대한 오류는 무시해도 됩니다.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. iSCSI 대상 패키지 설치

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. iSCSI 대상 서비스를 사용하도록 설정

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>iSCSI 대상 서버에 iSCSI 디바이스 만들기

모든 **iSCSI 대상 가상 머신** 에 대해 다음 명령을 실행하여 SAP 시스템에서 사용하는 클러스터에 대해 iSCSI 디스크를 만듭니다. 다음 예제에서는 여러 클러스터에 대한 SBD 디바이스가 만들어집니다. 또한 여러 클러스터에 대해 하나의 iSCSI 대상 서버를 사용하는 방법을 보여 줍니다. SBD 디바이스는 OS 디스크에 배치됩니다. 충분한 공간이 있는지 확인합니다.

**`nfs`** 는 NFS 클러스터를, **ascsnw1** 은 **NW1** 의 ASCS 클러스터를, **dbnw1** 은 **NW1** 의 데이터베이스 클러스터를 식별하는 데 사용됩니다. **nfs-0** 및 **nfs-1** 은 NFS 클러스터 노드의 호스트 이름, **nw1-xscs-0** 및 **nw1-xscs-1** 은 **NW1** ASCS 클러스터 노드의 호스트 이름, **nw1-db-0** 및 **nw1-db-1** 은 데이터베이스 클러스터 노드의 호스트 이름입니다. 이러한 이름을 클러스터 노드의 호스트 이름과 SAP 시스템의 SID로 바꿉니다.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

모든 항목이 올바르게 설정되었는지 확인할 수 있습니다.

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD 디바이스 설정

클러스터의 마지막 단계에서 만든 iSCSI 디바이스에 연결합니다.
새로 만들 클러스터의 노드에서 다음 명령을 실행합니다.
다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** iSCSI 디바이스에 연결

   먼저 iSCSI 및 SBD 서비스를 사용하도록 설정합니다.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 첫 번째 노드에서 초기자 이름 변경

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI 대상 서버에서 iSCSI 디바이스를 만들 때 사용한 ACL과 일치하도록 파일의 콘텐츠를 변경합니다(예: NFS 서버).

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** 두 번째 노드에서 초기자 이름 변경

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   iSCSI 대상 서버에서 iSCSI 디바이스를 만들 때 사용한 ACL과 일치하도록 파일의 콘텐츠 변경

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** iSCSI 서비스 다시 시작

   이제 iSCSI 서비스를 다시 시작하여 변경 내용 적용

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   iSCSI 디바이스를 연결합니다. 아래 예에서 10.0.0.17은 iSCSI 대상 서버의 IP 주소이고 3260은 기본 포트입니다. <b>iqn.2006-04.nfs.local:nfs</b>는 아래의 첫 번째 명령(iscsiadm -m discovery)을 실행할 때 나열되는 대상 이름 중 하나입니다.

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   iSCSI 디바이스를 사용할 수 있는지 확인하고 디바이스 이름을 기록합니다(다음 예제의 경우 /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   이제 iSCSI 디바이스의 ID를 검색합니다.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   이 명령은 모든 SBD 디바이스의 세 가지 디바이스 ID를 나열합니다. scsi-3으로 시작하는 ID를 사용하는 것이 좋습니다. 위의 예에서 해당 ID는 다음과 같습니다.

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** SBD 디바이스 만들기

   iSCSI 디바이스의 디바이스 ID를 사용하여 첫 번째 클러스터 노드에 새 SBD 디바이스를 만듭니다.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** SBD 구성 조정

   SBD 구성 파일 열기

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   SBD 디바이스의 속성을 변경하고, Pacemaker 통합을 활성화하고, SBD의 시작 모드를 변경합니다.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   `softdog` 구성 파일 만들기

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   모듈 로드하기

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>클러스터 설치

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** SLES 업데이트

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** 설치 구성 요소, 클러스터 리소스에 필요

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** 설치 azure-lb 구성 요소, 클러스터 리소스에 필요

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > 패키지 리소스 에이전트의 버전을 확인하고 최소 버전 요구 사항이 충족되었는지 확인합니다.  
   > - SLES 12 SP4/SP5의 경우 버전은 resource-agents-4.3.018.a7fb5035-3.30.1 이상이어야 합니다.  
   > - SLES 15/15 SP1의 경우 버전은 resource-agents-4.3.0184.6ee15eb2-4.13.1 이상이어야 합니다.  

1. **[A]** 운영 체제 구성

   경우에 따라 Pacemaker는 많은 프로세스를 만들게 되므로 허용되는 프로세스 수가 고갈됩니다. 이러한 경우 클러스터 노드 간 하트비트가 실패하고 리소스가 장애 조치(Failover)될 수 있습니다. 다음 매개 변수를 설정하여 허용되는 최대 프로세스 수를 늘리는 것이 좋습니다.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   더티 캐시의 크기를 줄입니다. 자세한 내용은 [큰 RAM이 있는 SLES 11/12 서버의 쓰기 성능 저하](https://www.suse.com/support/kb/doc/?id=7010287)를 참조하세요.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** HA 클러스터에 대한 cloud-netconfig-azure 구성

   >[!NOTE]
   > **zypper info cloud-netconfig-azure** 를 실행하여 설치된 **cloud-netconfig-azure** 패키지의 버전을 확인합니다. 사용자 환경의 버전이 1.3 이상인 경우, 더 이상 클라우드 네트워크 플러그 인에 의한 네트워크 인터페이스의 관리를 억제하지 않아도 됩니다. 1\.3 미만 버전에서는 **cloud-netconfig-azure** 패키지를 사용 가능한 최신 버전으로 업데이트하는 것이 좋습니다.  

   클라우드 네트워크 플러그 인이 가상 IP 주소를 제거하지 않도록 아래와 같이 네트워크 인터페이스에서 구성 파일을 변경합니다(Pacemaker가 VIP 할당을 제어해야 함). 자세한 내용은 [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)을 참조하세요. 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** ssh 액세스 사용

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** ssh 액세스 사용

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** ssh 액세스 사용

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Azure Fence 에이전트를 기반으로 하는 STONITH 디바이스를 사용하는 경우 Fence 에이전트 패키지를 설치합니다.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > 클러스터 노드를 차단해야 하는 경우 Azure Fence 에이전트를 사용하여 더 빠른 장애 조치(failover) 시간의 이점을 얻으려면 설치된 패키지 **펜스 에이전트** 버전이 **4.4.0** 이상이어야 합니다. 더 낮은 버전을 실행하는 경우 패키지를 업데이트하는 것이 좋습니다.  


1. **[A]** Azure Python SDK 설치 
   - SLES 12 SP4 또는 SLES 12 SP5에서
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - SLES 15 이상에서 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >버전 및 이미지 유형에 따라 Azure Python SDK를 설치하기 전에 OS 릴리스에 대한 퍼블릭 클라우드 확장을 활성화해야 할 수 있습니다.
   >SUSEConnect---list-extensions를 실행하여 확장을 확인할 수 있습니다.  
   >Azure Fence 에이전트를 사용하여 더 빠른 장애 조치(failover) 시간을 달성하려면 다음을 수행합니다.
   > - SLES 12 SP4 또는 SLES 12 SP5에 python-azure-mgmt-compute 패키지 버전 **4.6.2** 이상 설치  
   > - SLES 15.X에서 패키지 python **3**-azure-mgmt-compute의 **4.6.2** 버전을 설치하지만 그 이상은 아닙니다. 패키지 python **3**-azure-mgmt-compute의 버전 17.0.0-6.7.1은 Azure Fence Agent와 호환되지 않는 변경 사항을 포함하므로 사용하지 마세요.    
     
1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.

   >[!IMPORTANT]
   > 클러스터 구성에 호스트 이름을 사용하는 경우 신뢰할 수 있는 호스트 이름 확인을 사용하는 것이 매우 중요합니다. 이름을 사용할 수 없고, 그로 인해 클러스터 장애 조치(failover) 지연이 발생할 수 있는 경우, 클러스터 통신은 실패합니다.
   > /etc/hosts를 사용하는 장점은 클러스터가 단일 실패 지점이 될 수 있는 DNS와 무관하다는 점입니다.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** 클러스터 설치
- 펜싱에 SBD 디바이스를 사용하는 경우
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- 펜싱에 SBD 디바이스를 *사용하지 않는* 경우
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** 클러스터에 노드 추가

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** hacluster 암호를 동일한 암호로 변경

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** corosync 설정을 조정합니다.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   값이 없거나 다른 경우 파일에 다음과 같이 굵게 표시된 콘텐츠를 추가합니다. 메모리 보존 유지 관리를 허용하도록 토큰을 30000으로 변경해야 합니다. 자세한 내용은 [Linux][virtual-machines-linux-maintenance] 또는 [Windows용 이 문서][virtual-machines-windows-maintenance]를 참조하세요.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   그런 다음 corosync 서비스를 다시 시작합니다.

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD에 대한 기본 Pacemaker 구성

SBD STONITH를 사용하는 경우에만 이 섹션의 구성을 적용할 수 있습니다.  

1. **[1]** STONITH 디바이스를 사용하도록 설정하고 방어 지연 설정

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure Fence 에이전트 STONITH 디바이스 만들기

설명서의 이 섹션은 Azure Fence 에이전트를 기반으로 STONITH를 사용하는 경우에만 적용됩니다.
STONITH 디바이스에서는 서비스 주체를 사용하여 Microsoft Azure에 대해 권한을 부여합니다. 다음 단계에 따라 서비스 주체를 만듭니다.

1. [https://editor.swagger.io](<https://portal.azure.com>) 으로 이동합니다.
1. Azure Active Directory 블레이드 열기  
   속성으로 이동하여 Directory ID 기록 이 ID는 **테넌트 ID** 입니다.
1. 앱 등록 클릭
1. 새 등록 클릭
1. 이름을 입력하고 “이 조직 디렉터리의 계정만” 선택 
2. “웹” 애플리케이션 유형을 선택한 후 로그온 URL(예: http:\//localhost)을 입력하고 추가 클릭  
   로그온 URL이 사용되지 않으며, 이 URL은 임의의 올바른 URL이 될 수 있음
1. 인증서 및 암호를 선택한 다음, 새 클라이언트 암호 클릭
1. 새 키의 설명을 입력하고 “만료되지 않음”을 선택한 다음, 추가 클릭
1. 값을 기록해 둡니다. 서비스 주체의 **암호** 로 사용됨
1. 개요를 선택합니다. 애플리케이션 ID를 적어둡니다. 서비스 주체의 사용자 이름으로 사용됩니다.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 펜스 에이전트에 대한 사용자 지정 역할 만들기

서비스 주체에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다. 서비스 주체에 클러스터의 모든 가상 머신을 시작 및 중지(할당 취소)하기 위한 권한을 제공해야 합니다. 사용자 지정 역할을 아직 만들지 않은 경우 [PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) 또는 [Azure CLI](../../../role-based-access-control/custom-roles-cli.md)를 사용하여 만들 수 있습니다.

입력 파일에 다음 콘텐츠를 사용합니다. 구독에 맞게 콘텐츠를 조정해야 합니다. 즉 c276fc76-9cd4-44c9-99a7-4fd71546436e 및 e91d47c4-76f3-4271-a796-21b4ecfe3624를 구독의 ID로 교체해야 합니다. 구독이 하나만 있는 경우 AssignableScopes에서 두 번째 항목을 제거합니다.

```json
{
      "Name": "Linux Fence Agent Role",
      "description": "Allows to power-off and start virtual machines",
      "assignableScopes": [
              "/subscriptions/e663cc2d-722b-4be1-b636-bbd9e4c60fd9",
              "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
      ],
      "actions": [
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/powerOff/action",
              "Microsoft.Compute/virtualMachines/start/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 서비스 주체에 사용자 지정 역할 할당

마지막 단원에서 만든 사용자 지정 역할인 "Linux 펜스 에이전트 역할"을 서비스 주체에 할당합니다. 소유자 역할을 더 이상 사용하지 마십시오! 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../../role-based-access-control/role-assignments-portal.md)을 참조하세요.   
두 클러스터 노드에 대해 역할을 할당해야 합니다.    

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH 디바이스 만들기

가상 머신의 권한을 편집하고 나면 클러스터의 STONITH 디바이스를 구성할 수 있습니다.

> [!NOTE]
> 호스트 이름과 Azure VM 이름이 동일하지 않은 경우 'pcmk_host_map' 옵션은 명령에만 필요합니다. **hostname:vm-name** 형식으로 매핑을 지정합니다.
> 명령에서 굵은 섹션을 참조하세요.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group of the VM, tenant ID, service principal application ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>application ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> 모니터링 및 펜싱 작업은 역직렬화됩니다. 결과적으로, 모니터링 작업이 더 오래 실행되고 펜싱 이벤트를 동시에 실행하는 경우, 이미 실행 중인 모니터링 작업으로 인해 클러스터 장애 조치(failover)는 지연되지 않습니다.

> [!TIP]
>Azure Fence 에이전트는 [표준 ILB를 사용하는 VM에 대한 공용 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)에서 가능한 솔루션과 함께 설명한 대로 공용 엔드포인트에 대한 아웃바운드 연결이 필요합니다.  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Azure 예약된 이벤트에 대한 Pacemaker 구성

Azure는 [예약된 이벤트](../../linux/scheduled-events.md)를 제공합니다. 예약된 이벤트는 메타 데이터 서비스를 통해 제공되며 애플리케이션이 VM 종료, VM 다시 배포 등의 이벤트를 준비할 시간을 허용합니다. 리소스 에이전트 **[azure-events](https://github.com/ClusterLabs/resource-agents/pull/1161)** 는 예약된 Azure 이벤트를 모니터합니다. 이벤트가 검색되고 리소스 에이전트가 사용 가능한 다른 클러스터 노드가 있음을 확인하는 경우 azure-events 에이전트는 클러스터가 보류 상태의 [Azure Scheduled Events](../../linux/scheduled-events.md)로 VM에서 리소스를 강제로 마이그레이션하기 위해 대상 클러스터 노드를 대기 모드로 설정합니다. 이렇게 하려면 추가 Pacemaker 리소스를 구성해야 합니다. 

1. **[A]** **azure-events** 에이전트에 대한 패키지가 이미 설치되어 있고 최신인지 확인합니다. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Pacemaker에서 리소스를 구성합니다. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > azure-events 에이전트에 대한 Pacemaker 리소스를 구성한 후 클러스터를 유지 관리 모드로 설정하거나 모드에서 해제할 때 다음과 같은 경고가 표시될 수 있습니다.  
     경고: cib-bootstrap-options: 알 수 없는 특성 'hostName_  <strong>hostname</strong>'  
     경고: cib-bootstrap-options: 알 수 없는 특성 'azure-events_globalPullState'  
     경고: cib-bootstrap-options: 알 수 없는 특성 'hostName_ <strong>hostname</strong>'  
   > 이러한 경고 메시지는 무시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성][sles-nfs-guide]
* [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성][sles-guide]
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
