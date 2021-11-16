---
title: RMAN 및 Azure Files를 사용하여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업
description: Azure Files에 Oracle Database 19c 데이터베이스를 백업하는 방법에 대해 알아봅니다.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 2c9128f6e03f039ddc6e56ec9672a7a0a18f9889
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520430"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-files"></a>Azure Files를 사용하여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업 및 복구

**적용 대상:** :heavy_check_mark: Linux VM 

이 문서에서는 Azure VM에서 실행되는 Oracle 데이터베이스를 백업 및 복원하기 위한 미디어로 Azure Files를 사용하는 방법을 보여 줍니다. SMB 프로토콜을 사용하여 VM에 탑재된 Azure 파일 공유에 Oracle RMAN을 사용하여 데이터베이스를 백업합니다. 백업 미디어에 Azure Files를 사용하는 것은 매우 비용 효율적이고 성능이 우수합니다. 그러나 초대형 데이터베이스의 경우 Azure Backup은 더 나은 솔루션을 제공합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- 백업 및 복구 프로세스를 수행하려면 먼저 Oracle Database 19c의 인스턴스가 설치되어 있는 Linux VM을 만들어야 합니다. 현재 VM을 만드는 데 사용되는 Marketplace 이미지는 **Oracle:oracle-database-19-3:oracle-database-19-0904:latest** 입니다. 이 자습서를 완료하려면 [Oracle 데이터베이스 만들기 빠른 시작](./oracle-database-quick-create.md)의 단계에 따라 Oracle 데이터베이스를 만듭니다.

## <a name="prepare-the-database-environment"></a>데이터베이스 환경 준비

1. VM으로 SSH(Secure Shell) 세션을 만들려면 다음 명령을 사용합니다. IP 주소 및 호스트 이름 조합을 VM에 대한 `publicIpAddress` 값으로 바꿉니다.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. ***루트*** 사용자로 전환합니다.
 
   ```bash
   sudo su -
   ```
    
3. oracle 사용자를 ***/etc/sudoers*** 파일에 추가합니다.

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. 이 단계에서는 *vmoracle19c* 라는 VM에서 실행되는 Oracle 인스턴스(test)가 있다고 가정합니다.

   사용자를 *oracle* 사용자로 전환합니다.

   ```bash
   sudo su - oracle
   ```
    
5. 연결하기 전에 환경 변수 ORACLE_SID를 설정해야 합니다.
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   또한 다음 명령을 사용하여 향후 로그인에 사용할 `oracle` 사용자 `.bashrc` 파일에 ORACLE_SID 변수를 추가해야 합니다.

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. 아직 실행되지 않은 경우 Oracle 수신기를 시작합니다.
    
   ```bash
   $ lsnrctl start
   ```

    출력은 다음 예제와 유사하게 표시됩니다.

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  FRA (빠른 복구 영역) 위치를 만듭니다.

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  데이터베이스에 연결합니다.

    ```bash
    sqlplus / as sysdba
    ```

9.  아직 실행되지 않은 경우 데이터베이스를 시작합니다.

    ```bash
    SQL> startup
    ```

10. 빠른 복구 영역에 대한 데이터베이스 환경 변수를 설정합니다.

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. 온라인 백업을 사용하도록 설정하려면 데이터베이스가 보관 로그 모드에 있는지 확인합니다.
    먼저 로그 보관 상태를 확인합니다.

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    또한 NOARCHIVELOG 모드에 있는 경우 sqlplus에서 다음 명령을 실행합니다.

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. 백업 및 복원 작업을 테스트할 테이블을 만듭니다.

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Azure Files에 백업

Azure Files에 백업하려면 다음 단계를 완료합니다.

1. Azure Files를 설정합니다.
1. VM에 Azure 파일 공유 탑재
1. 데이터베이스를 백업합니다.
1. 데이터베이스를 복원하고 복구합니다.

### <a name="set-up-azure-files"></a>Azure Files 설정

이 단계에서는 Oracle Recovery Manager(RMAN)를 사용하여 Azure Files에 Oracle 데이터베이스를 백업합니다. Azure 파일 공유는 클라우드에 있는 완전 관리형 파일 공유입니다. SMB(서버 메시지 블록) 프로토콜 또는 NFS(네트워크 파일 시스템) 프로토콜을 사용하여 액세스할 수 있습니다. 이 단계에서는 SMB 프로토콜을 사용하여 VM에 탑재하는 파일 공유를 만드는 방법을 설명합니다. NFS를 사용하여 탑재하는 방법에 대한 자세한 내용은 [NFS 공유를 만드는 방법을 참조하세요.](../../../storage/files/storage-files-how-to-create-nfs-shares.md)

Azure Files를 탑재하는 경우 `cache=none`을 사용하여 파일 공유 데이터의 캐싱을 사용하지 않도록 설정합니다. 또한 공유에 생성된 파일을 oracle 사용자가 소유하고 있는지 확인하기 위해 `uid=oracle` 및 `gid=oinstall` 옵션도 설정합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

먼저 스토리지 계정을 설정합니다.

1. Azure Portal에서 * **+ 리소스 만들기** _를 선택하고 _ *_스토리지 계정_**을 검색하여 선택합니다.
    
    ![리소스를 만들고 스토리지 계정을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/storage-1.png)
    
2. 스토리지 계정 만들기 페이지에서 기존 리소스 그룹 ***rg-oracle** _을 선택하고, 스토리지 계정 이름을 _*_oracbkup1_*_ 로 지정하고, 계정 종류에 대해 _*_Storage V2(범용 v2)_*_ 를 선택합니다. 복제를 _*_LRS(로컬 중복 스토리지)_*_ 로 변경하고, 성능을 _*_표준_**으로 설정합니다. 위치가 리소스 그룹의 다른 모든 리소스와 동일한 지역으로 설정되어 있는지 확인합니다. 
    
    ![기존 리소스 그룹을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-1.png)
   
3. ***고급** _ 탭을 클릭하고 Azure Files에서 _*_대용량 파일 공유_*_ 를 _*_사용_**으로 설정합니다. 검토 + 만들기를 클릭한 다음, 만들기를 클릭합니다.
    
    ![대용량 파일 공유를 사용으로 설정할 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-2.png)
    
4. 스토리지 계정이 만들어지면 리소스로 이동하여 ***파일 공유*** 를 선택합니다.
    
    ![파일 공유를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-3.png)
    
5. * **+ 파일 공유** _를 클릭하고 _*_새 파일 공유_*_ 블레이드에서 파일 공유의 이름을 _*_orabkup1_*_ 로 지정합니다. _*_할당량_*_ 을 _*_10240_*_ GiB로 설정하고 계층으로 _*_최적화된 트랜잭션_*_ 을 선택합니다. 할당량은 파일 공유를 확장할 수 있는 상한을 반영합니다. 표준 스토리지를 사용하는 것처럼 리소스가 PAYG이고 프로비전되지 않으므로 10 TiB로 설정해도 사용하는 것보다 비용이 발생하지 않습니다. 백업 전략에 더 많은 스토리지가 필요한 경우 모든 백업을 보관할 적절한 수준으로 할당량을 설정해야 합니다.   새 파일 공유 블레이드를 완료했으면 _*_만들기_**를 클릭합니다.
    
    ![새 파일 공유를 추가할 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. 만들어지면 파일 공유 설정 페이지에서 ***orabkup1*** 을 클릭합니다. 
    ***연결** _ 탭을 클릭하여 연결 블레이드를 연 다음, _ *_Linux_** 탭을 클릭합니다. 제공된 명령을 복사하여 SMB 프로토콜을 사용하여 파일 공유를 탑재합니다. 
    
    ![스토리지 계정 추가 페이지](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

스토리지 계정 및 파일 공유를 설정하려면 Azure CLI에서 다음 명령을 실행합니다.

1. 스토리지 계정을 VM과 동일한 리소스 그룹 및 위치에 만듭니다.
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. 10TiB의 할당량으로 스토리지 계정에서 파일 공유를 만듭니다.

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. VM에 파일 공유를 탑재할 때 필요한 스토리지 계정 기본 키(key1)를 검색합니다.

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>VM에 Azure Storage 파일 공유 탑재

1. 탑재 지점을 만듭니다.

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. 자격 증명을 설정합니다.

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   다음 명령을 실행하기 전에 먼저 검색된 스토리지 계정 키에 대해 `<Your Storage Account Key1>`을 대체합니다.
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. 자격 증명 파일에 대한 권한을 변경합니다.

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. /etc/fstab에 탑재를 추가합니다.

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. SMB 프로토콜을 사용하여 Azure Files 스토리지를 탑재하는 명령을 실행합니다.

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   다음과 같은 오류가 발생하는 경우:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   CIFS 패키지가 Linux 호스트에 설치되지 않을 수 있습니다. 
   
   CIS가 설치되어 있는지 확인하려면 다음 명령을 실행합니다.

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   명령이 출력을 반환하지 않으면 다음 명령을 사용하여 CIFS 패키지를 설치합니다.

   ```bash 
   sudo yum install cifs-utils
   ```

   이제 위의 탑재 명령을 다시 실행하여 Azure Files 스토리지를 탑재합니다.

6. 다음 명령을 사용하여 파일 공유가 제대로 탑재되었는지 확인합니다.

   ```bash
   df -h
   ```

   출력은 다음과 비슷한 모양입니다.

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>데이터베이스 백업

이 섹션에서는 Oracle Recovery Manager(RMAN)를 사용하여 데이터베이스의 전체 백업을 수행하고 로그를 보관하고 이전에 탑재된 Azure 파일 공유에 백업 세트로 백업을 작성합니다. 

1. Azure Files 탑재 지점에 백업할 RMAN을 구성합니다.

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Azure 표준 파일 공유의 최대 파일 크기는 1TiB이므로 RMAN 백업 부분의 크기를 1TiB로 제한합니다. (프리미엄 파일 공유의 최대 파일 크기 제한은 4TiB입니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](../../../storage/files/storage-files-scale-targets.md)를 참조하세요.)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. 구성 변경 세부 정보를 확인합니다.

    ```bash
    RMAN> show all;
    ```

4. 이제 백업을 실행합니다. 다음 명령은 보관 로그 파일을 포함하여 전체 데이터베이스 백업을 압축 형식의 백업 세트로 사용합니다.   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

이제 Oracle RMAN을 사용하여 데이터베이스를 온라인으로 백업했으며, 이 백업은 Azure Files에 있습니다. 이 방법은 다른 VM에서 액세스할 수 있는 Azure Files에 백업을 저장하는 동안 RMAN의 기능을 활용하는 장점이 있으므로 데이터베이스를 복제해야 하는 경우에 유용합니다.  
    
데이터베이스 백업에 RMAN 및 Azure Files를 사용하는 경우 많은 장점이 있지만 백업 및 복원 시간은 데이터베이스 크기에 연결되므로 초대형 데이터베이스의 경우 이러한 작업에 상당한 시간이 걸릴 수 있습니다. Azure Backup 애플리케이션 일치 VM 백업을 사용 하는 대체 백업 메커니즘은 스냅샷 기술을 사용하여 백업을 수행합니다. 이 경우 데이터베이스 크기에 관계없이 백업 속도가 매우 빠릅니다. Recovery Services Vault와 통합하면 다른 VM 및 Azure 지역에서 액세스할 수 있는 Azure 클라우드 스토리지의 Oracle Database 백업에 대한 안전한 스토리지를 제공합니다. 

### <a name="restore-and-recover-the-database"></a>데이터베이스 복원 및 복구

1.  Oracle 인스턴스를 종료합니다.

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  데이터베이스 데이터 파일을 제거합니다.

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. 다음 명령은 RMAN을 사용하여 누락된 데이터 파일을 복원하고 데이터베이스를 복구합니다.

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. 데이터베이스 콘텐츠가 완전히 복구되었는지 확인합니다.

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


이제 Azure Linux VM에서 Oracle Database 19c 데이터베이스의 백업 및 복구가 완료되었습니다.

## <a name="delete-the-vm"></a>VM 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
