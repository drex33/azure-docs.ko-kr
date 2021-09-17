---
title: Azure NetApp Files에 대한 Azure 애플리케이션 일치 스냅샷 도구를 사용하여 재해 복구 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구를 사용하는 경우 재해 복구를 수행하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 1fbf8052a3852e010fc002116cab131ea4f371b8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438105"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅샷 도구를 사용하여 재해 복구

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구를 사용하는 경우 재해 복구를 수행하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 작업은 **Azure 대규모 인스턴스** 에만 적용됩니다.

## <a name="introduction"></a>소개

Azure 대규모 인스턴스 플랫폼에는 스토리지 볼륨 스냅샷을 복제할 수 있는 재해 복구 사이트가 구성되어 있을 수도 있습니다.  해당 설정으로 스냅샷이 올바르게 구성된 경우 이 사이트에서 재해 복구를 수행할 수 있습니다.  이 문서는 해당 설치에 대한 재해 복구를 수행하는 방법을 안내합니다.

## <a name="prerequisites-for-disaster-recovery-setup"></a>재해 복구 설치를 위한 필수 구성 요소

재해 복구 장애 조치(failover)를 계획하려면 먼저 다음 필수 구성 요소를 충족해야 합니다.

- DR 사이트에서 프로비저닝된 DR 노드가 있습니다. DR에는 두 가지 옵션이 있습니다. 일반적인 DR과 다목적 DR입니다.
- 스토리지 복제가 작동 중입니다. Microsoft 운영 팀은 DR 프로비저닝 시 스토리지 복제 설정을 자동으로 수행합니다. DR 사이트에서 `azacsnap -c details --details replication` 명령을 사용하여 스토리지 복제를 모니터링할 수 있습니다.
- 기본 위치에서 스토리지 스냅샷을 설정하고 구성했습니다.
- 주 인스턴스와 동일한 SID를 가진 기본용 DR 사이트에 HANA 인스턴스가 설치되어 있습니다.
- [Azure의 SAP HANA(대규모 인스턴스) 고가용성 및 재해 복구](../virtual-machines/workloads/sap/hana-failover-procedure.md)에 설명된 DR 장애 조치(Failover) 절차를 읽고 이해했습니다.
- DR 위치에서 스토리지 스냅샷을 설정하고 구성했습니다.
- DR 스토리지 볼륨 및 DR 서버에 있는 관련 정보를 사용하여 구성 파일(예: `DR.json`)을 만들었습니다.
- DR 사이트에서 다음 단계를 완료했습니다.
  - 스토리지와의 통신을 사용하도록 설정합니다.
  - SAP HANA와의 통신을 사용하도록 설정합니다.

## <a name="set-up-disaster-recovery"></a>재해 복구 설정

Microsoft는 DR 복구에 대한 스토리지 수준 복제를 지원합니다. DR을 설정하는 방법으로 두 가지가 있습니다.

하나는 **일반적인** DR이며 다른 하나는 **다목적** DR입니다. **일반적인** DR에는 DR 위치에 장애 조치(failover)를 위한 전용 인스턴스가 있습니다. **다목적** DR 시나리오에서는 DR 사이트의 HANA 대규모 인스턴스 단위에서 실행되는 다른 QA 또는 개발 HANA 인스턴스가 있습니다. 그러나 유휴 상태이며, 해당 HANA 대규모 인스턴스 단위로 장애 조치(failover)하려는 HANA 인스턴스와 동일한 SID를 가진 미리 설치된 HANA 인스턴스 또한 설치했습니다. Microsoft 운영 팀은 온보딩 시에 서비스 요청 양식(SRF)에 제공된 입력값을 기반으로 하는 스토리지 복제를 포함하여 환경을 설정합니다.

> [!IMPORTANT]
> DR 설정에 대해 모든 필수 구성 요소를 충족하는지 확인합니다.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>기본 사이트에서 DR 사이트로의 데이터 복제 모니터링

Microsoft 운영 팀은 이미 기본 사이트에서 DR 사이트로의 DR 링크를 관리 및 모니터링하고 있습니다.
스냅샷 명령 `azacsnap -c details --details replication`을 사용하여 주 서버에서 DR 서버로의 데이터 복제를 모니터링할 수 있습니다.

## <a name="perform-a-failover-to-dr-site"></a>DR 사이트로 장애 조치(failover) 수행

DR 사이트(`azacsnap -c restore --restore revertvolume`)에서 장애 조치(failover) 명령을 실행합니다.

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume` 명령은 프로덕션 사이트에서 DR 사이트로의 스토리지 복제를 중단합니다. 복제를 다시 설정하려면 Microsoft 운영 팀에 문의해야 합니다. 복제를 다시 사용하도록 설정하면 해당 SID에 대한 DR 스토리지의 모든 데이터가 초기화됩니다. 장애 조치(failover)를 수행하는 명령을 사용하면 가장 최근에 복제된 스토리지 스냅샷을 사용할 수 있습니다. 이전 스냅샷으로 복원해야 하는 경우, 운영 팀이 DR 사이트에서 복원된 이전 스냅샷을 제공할 수 있도록 지원 요청을 시작합니다.

높은 수준에서 DR 장애 조치(failover)를 위해 수행해야 하는 단계는 다음과 같습니다.

- **기본** 사이트에서 HANA 인스턴스를 종료해야 합니다. 이 작업은 데이터 불일치가 발생하지 않도록 실제로 DR 사이트로 장애 조치(failover)를 수행하는 경우에만 필요합니다.
- 프로덕션 SID에 대한 DR 노드의 HANA 인스턴스를 종료합니다.
- 복구하려는 SID를 사용하여 DR 노드에서 명령 `azacsnap -c restore --restore revertvolume`을 실행합니다.
  - 해당 명령은 기본 사이트에서 DR 사이트로의 스토리지 복제 링크를 중단합니다.
  - 해당 명령은 /data 및 /logbackups 볼륨만을 복원합니다. /shared 볼륨은 복구되지 않으며, 대신 DR 위치에 있는 SID에 대한 기존 /shared를 사용합니다.
  - /data 및 /logbackups 볼륨을 탑재하고 fstab 파일에 추가했는지 확인합니다.
- HANA SYSTEMDB 스냅샷을 복원합니다. HANA studio는 명령 `azacsnap -c restore --restore revertvolume` 실행의 일부로 복원된 스토리지 스냅샷에서 사용 가능한 최신 HANA 스냅샷만을 표시합니다.
- 테넌트 데이터베이스를 복구합니다.
- 프로덕션 SID에 대한 DR 사이트에서 HANA 인스턴스를 시작합니다(예: 이 경우 H80).
- 테스트를 수행합니다.

### <a name="example-performing-disaster-recovery"></a>재해 복구 수행 예제

이 하위 섹션에서는 재해 복구 사이트로 장애 조치(failover)하는 자세한 단계에 대해 설명합니다.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>1단계: DR 노드의 볼륨 세부 정보 가져오기

명령 `df –h`을 실행하여 장애 조치(failover) 후에 참조할 파일 시스템 및 관련 볼륨을 나열합니다.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>2단계: 기본 사이트에서 HANA 종료

프로덕션 워크로드의 전체 장애 조치(failover)를 수행하며, 기본 프로덕션 사이트에 연결할 수 있는 경우, DR로 장애 조치(failover)되는 SAP HANA 인스턴스를 종료합니다.

예를 들어 루트로 로그인한 경우 다음 예제에서 SAP HANA를 종료할 수 있는 방법을 확인할 수 있습니다.  \<sid\>을 SAP HANA SID로 바꿉니다.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>3단계: DR 사이트에서 HANA 종료

볼륨을 복원하기 전에 DR 사이트에서 SAP HANA를 종료하는 것이 중요합니다.

예를 들어 루트로 로그인한 경우 다음 예제에서 SAP HANA를 종료할 수 있는 방법을 확인할 수 있습니다.  \<sid\>을 SAP HANA SID로 바꿉니다.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> 볼륨을 복원하기 전에 DR 사이트의 HANA 인스턴스가 오프라인 상태인지 확인합니다.

#### <a name="step-4-restore-the-volumes"></a>4단계: 볼륨 복원

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**‘DR 장애 조치(failover) 명령의 출력’** 입니다.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> DR 장애 조치(failover)에 대한 스토리지 준비를 완료하려면 콘솔 표시의 끝에 있는 단계를 수행해야 합니다.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>5단계: 불필요한 파일 시스템 분리

`umount` 명령을 실행하여 불필요한 파일 시스템/볼륨을 분리합니다.

```bash
umount <Mount point>
```

데이터 및 로그 백업 탑재 지점을 분리합니다. 스케일 아웃 시나리오의 경우 데이터 탑재 지점이 여러 곳일 수 있습니다.

#### <a name="step-6-configure-the-mount-points"></a>6단계: 탑재 지점 구성

`/etc/fstab` 파일을 수정하여 주 SID에 대한 데이터 및 로그 백업 항목을 주석으로 처리하고(이 예에서는 SID=H80) 기본 사이트 DR 볼륨으로 만든 새 탑재 지점 항목을 추가합니다. 새 탑재 지점 항목이 명령 출력에 제공됩니다.

- `#` 문자를 사용하여 DR 사이트에서 실행 중인 기존 탑재 위치를 주석으로 처리합니다.

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- `/etc/fstab`에 다음 줄을 추가합니다.
  > 이는 명령과 동일한 출력이어야 합니다.

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>7단계: 복구 볼륨 탑재

`mount –a` 명령을 실행하여 모든 탑재 지점을 탑재합니다.

```bash
mount -a
```

이제 `df –h`를 실행하면 `*_dp` 볼륨이 탑재된 것을 확인할 수 있습니다.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>8단계: SYSTEMDB 복구

HANA 스튜디오에서 SYSTEMDB 인스턴스를 마우스 오른쪽 단추로 클릭하고 “백업 및 복구”를 선택한 다음 “시스템 데이터베이스 복구”를 선택합니다.

스냅샷에서 데이터베이스(특히 SYSTEMDB)를 복구하는 방법에 대한 가이드를 참조하세요.

#### <a name="step-9-recover-the-tenant-database"></a>9단계: 테넌트 데이터베이스 복구

HANA 스튜디오에서 SYSTEMDB 인스턴스를 마우스 오른쪽 단추로 클릭하고 “백업 및 복구”를 선택한 다음 “테넌트 데이터베이스 복구”를 선택합니다.

스냅샷에서 데이터베이스(특히 테넌트 데이터베이스)를 복구하는 방법에 대한 가이드를 참조하세요.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>DR 사이트에서 `azacsnap -c backup` 실행

DR 사이트에서 스냅샷 기반 백업을 실행하는 경우 DR 사이트의 `azacsnap` 구성 파일에 구성된 HANA 서버 이름이 프로덕션 서버 이름과 동일해야 합니다.

> [!IMPORTANT]
> `azacsnap -c backup`를 실행하면 DR 사이트에 스토리지 스냅샷이 생성될 수 있으며, 이러한 스냅샷은 자동으로 다른 사이트에 복제되지 않습니다.  원본 프로덕션 사이트로의 파일 또는 데이터 반환에 대해 더 자세히 이해하려면 Microsoft 운영팀에 지원을 요청하세요.

## <a name="next-steps"></a>다음 단계

- [스냅샷 정보 가져오기](azacsnap-cmd-ref-details.md)
- [백업 만들기](azacsnap-cmd-ref-backup.md)
