---
title: Azure Backup을 사용하여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업 및 복구
description: Azure Backup 서비스를 사용하여 Oracle Database 19c 데이터베이스를 백업 및 복구하는 방법을 알아봅니다.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 31bb35a096845c489b0f141a601ee604253e6ef9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566906"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Azure Backup을 사용하여 Azure Linux VM에서 Oracle Database 19c 데이터베이스 백업 및 복구

이 문서에서는 Azure Backup을 사용하여 데이터베이스 파일 및 빠른 복구 영역을 포함하는 VM 디스크의 디스크 스냅샷을 만드는 방법을 보여 줍니다. Azure Backup을 사용하면 [Recovery Services 자격 증명 모음](../../../backup/backup-azure-recovery-services-vault-overview.md)에 저장되는 백업으로 적합한 전체 디스크 스냅샷을 만들 수 있습니다.  또한 Azure Backup은 데이터를 복원하기 위한 추가 수정이 필요하지 않은 애플리케이션 일치 백업도 제공합니다. 애플리케이션 일치 데이터를 복원하면 실행 상태로 빠르게 돌아갈 수 있으므로 복원 시간을 줄여줍니다.

> [!div class="checklist"]
>
> * 애플리케이션 일치 백업을 사용하여 데이터베이스 백업
> * 복구 지점에서 데이터베이스 복원 및 복구
> * 복구 지점에서 VM 복원

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

백업 및 복구 프로세스를 수행하려면 먼저 Oracle Database 19c의 인스턴스가 설치되어 있는 Linux VM을 만들어야 합니다. 현재 VM을 만드는 데 사용되는 Marketplace 이미지는 **Oracle:oracle-database-19-3:oracle-database-19-0904:latest** 입니다. 이 자습서를 완료하려면 [Oracle 데이터베이스 만들기 빠른 시작](./oracle-database-quick-create.md)의 단계에 따라 Oracle 데이터베이스를 만듭니다.


## <a name="prepare-the-environment"></a>환경 준비

환경을 준비하려면 다음 단계를 완료합니다.

1. [VM에 연결합니다](#connect-to-the-vm).
1. [Azure Files Storage 설정](#setup-azure-files-storage-for-the-oracle-archived-redo-log-files)
1. [데이터베이스를 준비합니다](#prepare-the-databases).

### <a name="connect-to-the-vm"></a>VM에 연결

1. VM으로 SSH(Secure Shell) 세션을 만들려면 다음 명령을 사용합니다. IP 주소 및 호스트 이름 조합을 VM에 대한 `<publicIpAddress>` 값으로 바꿉니다.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. *루트* 사용자로 전환합니다.

   ```bash
   sudo su -
   ```
    
1. oracle 사용자를 */etc/sudoers* 파일에 추가합니다.

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="setup-azure-files-storage-for-the-oracle-archived-redo-log-files"></a>Oracle 보관된 다시 실행 로그 파일용 Azure Files Storage 설정

Oracle 데이터베이스 보관 다시 실행 로그 파일은 과거에 생성된 데이터베이스 스냅샷에서 롤포워드하는 데 필요한 커밋된 트랜잭션을 저장하므로 데이터베이스 복구에서 중요한 역할을 합니다. archivelog 모드에 있을 때 데이터베이스는 온라인 다시 실행 로그 파일이 가득 차고 전환될 때 내용을 보관합니다. 백업과 함께 데이터베이스가 손실되었을 때 지정 시간 복구를 수행하는 데 필요합니다.  
   
Oracle은 다시 실행 로그 파일을 다른 위치에 보관하는 기능을 제공하며 업계 모범 사례에서는 이러한 대상 중 하나 이상이 원격 스토리지에 있으므로 호스트 스토리지와 분리되고 독립적인 스냅샷으로 보호할 것을 권장합니다. Azure Files는 이러한 요구 사항에 매우 적합합니다.

Azure Files 파일 공유는 SMB 또는 NFS(미리 보기) 프로토콜을 사용하여 Linux 또는 Windows VM에 일반 파일 시스템 구성 요소로 연결할 수 있는 스토리지입니다. 
   
보관 로그 스토리지로 사용하기 위해 SMB 3.0 프로토콜(권장)을 사용하여 Linux에서 Azure Files 파일 공유를 설정하려면 [Linux에서 Azure Files 사용 안내 가이드](../../../storage/files/storage-how-to-use-files-linux.md)를 따르세요. 설치를 완료했으면 이 가이드로 돌아가 나머지 단계를 모두 완료합니다.

### <a name="prepare-the-databases"></a>데이터베이스 준비

이 단계에서는 [Oracle에서 데이터베이스 만들기 빠른 시작](./oracle-database-quick-create.md)에 따라 `oratest1`라는 Oracle 인스턴스가 있고, `vmoracle19c`라는 VM에서 실행되며, 표준 Oracle ‘oraenv’ 스크립트를 표준 Oracle 구성 파일 ‘/etc/oratab’에 대한 종속성과 함께 사용하여 셸 세션에서 환경 변수를 설정한다고 가정합니다.

VM의 각 데이터베이스에서 다음 단계를 수행합니다.

1. 사용자를 *oracle* 사용자로 전환합니다.
 
   ```bash
    sudo su - oracle
    ```
    
1. 연결하기 전에 ORACLE_SID 이름을 입력하라는 `oraenv` 스크립트를 실행하여 ORACLE_SID 환경 변수를 설정해야 합니다.
    
    ```bash
    $ . oraenv
    ```

1.   추가 데이터베이스 보관 로그 파일 대상으로 Azure Files 공유 추가
     
     이 단계에서는 Linux VM에 Azure Files 공유를 구성 및 탑재(예: `/backup`라는 탑재 지점 디렉터리 아래)한 것으로 가정합니다.

     VM에 설치된 각 데이터베이스에서 다음을 예로 사용하여 데이터베이스 SID의 이름을 딴 하위 디렉터리를 만듭니다.
     
     이 예에서 탑재 지점 이름은 `/backup`이고 SID는 `oratest1`이므로, `/backup/oratest1` 하위 디렉터리를 만들고 소유권을 oracle 사용자로 변경합니다. 탑재 지점 이름과 데이터베이스 SID를 **/backup/SID** 로 바꾸세요. 

     ```bash
     sudo mkdir /backup/oratest1
     sudo chown oracle:oinstall /backup/oratest1
     ```
    
1. 데이터베이스에 연결합니다.
    
   ```bash
   sqlplus / as sysdba
   ```

1.  아직 실행되지 않은 경우 데이터베이스를 시작합니다. 
   
    ```bash
    SQL> startup
    ```
   
1. 데이터베이스의 첫 번째 보관 로그 대상을 앞서 만든 파일 공유 디렉터리로 설정합니다.

   ```bash
   SQL> alter system set log_archive_dest_1='LOCATION=/backup/oratest1' scope=both;
   ```

1. 데이터베이스에 대한 RPO(복구 지점 목표)를 정의합니다.

    일관된 RPO를 얻으려면 온라인 다시 실행 로그 파일이 보관되는 빈도를 고려해야 합니다. 보관 로그 생성 빈도는 다음에 의해 제어됩니다.
    - 온라인 다시 실행 로그 파일의 크기입니다. 온라인 로그 파일이 가득 차면 전환되고 보관됩니다. 온라인 로그 파일이 클수록 채우는 데 더 오래 걸리므로 보관 생성 빈도가 감소합니다.
    - ARCHIVE_LAG_TARGET 매개 변수의 설정은 현재 온라인 로그 파일이 전환되고 보관되어야 하기 전에 허용되는 최대 시간(초)을 제어합니다. 

    수반되는 검사점 작업과 함께 스위칭 및 보관 빈도를 최소화하기 위해 Oracle 온라인 다시 실행 로그 파일은 일반적으로 크기가 상당히 커집니다(1024M, 4096M, 8192M 등). 바쁜 데이터베이스 환경에서 로그는 여전히 몇 초 또는 몇 분마다 전환 및 보관될 가능성이 있지만 덜 활성화된 데이터베이스에서는 가장 최근 트랜잭션이 보관되기 몇 시간 또는 며칠 전에 이동하여 보관 빈도를 크게 줄일 수 있습니다. 따라서 일관된 RPO를 달성하려면 ARCHIVE_LAG_TARGET을 설정하는 것이 좋습니다. 5분(300초)의 설정은 ARCHIVE_LAG_TARGET에 대한 적절한 값으로, 모든 데이터베이스 복구 작업이 실패 시간의 5분 이내로 복구될 수 있도록 합니다.

    ARCHIVE_LAG_TARGET을 설정하려면:

    ```bash 
    SQL> alter system set archive_lag_target=300 scope=both;
    ```

    RPO가 없는 Azure에서 고가용성 Oracle 데이터베이스를 배포하는 방법을 더 잘 이해하려면 [Oracle 데이터베이스용 참조 아키텍처](./oracle-reference-architecture.md)를 참조하세요.

1.  온라인 백업을 사용하도록 설정하려면 데이터베이스가 보관 로그 모드에 있는지 확인합니다.

     먼저 로그 보관 상태를 확인합니다.

     ```bash
     SQL> SELECT log_mode FROM v$database;

     LOG_MODE
     ------------
     NOARCHIVELOG
     ```

     NOARCHIVELOG 모드인 경우 다음 명령을 실행합니다.

     ```bash
     SQL> SHUTDOWN IMMEDIATE;
     SQL> STARTUP MOUNT;
     SQL> ALTER DATABASE ARCHIVELOG;
     SQL> ALTER DATABASE OPEN;
     SQL> ALTER SYSTEM SWITCH LOGFILE;
     ```
1. 백업 및 복원 작업을 테스트할 테이블을 만듭니다.
   ```bash
   SQL> create user scott identified by tiger quota 100M on users;
   SQL> grant create session, create table to scott;
   SQL> connect scott/tiger
   SQL> create table scott_table(col1 number, col2 varchar2(50));
   SQL> insert into scott_table VALUES(1,'Line 1');
   SQL> commit;
   SQL> quit
   ```
## <a name="using-azure-backup"></a>Azure Backup 사용 

Azure Backup 서비스는 데이터를 백업하고 Microsoft Azure 클라우드에서 복구할 수 있는 간단하고, 안전하며, 비용 효율적인 솔루션을 제공합니다. Azure Backup은 실수로 데이터가 삭제되지 않도록 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점에 대한 기본 제공 관리를 사용하여 Recovery Services 자격 증명 모음에 저장됩니다. 구성 및 확장성이 간단하고, 백업이 최적화되어 있으며, 필요에 따라 쉽게 복원할 수 있습니다.

Azure Backup 서비스는 Oracle과 MySQL 같은 다양한 애플리케이션에 대해 Windows 및 Linux VM을 백업하는 동안 애플리케이션 일관성을 달성할 수 있는 [프레임워크](../../../backup/backup-azure-linux-app-consistent.md)를 제공합니다. 여기에는 디스크 스냅샷을 만들기 전에 사전 스크립트(애플리케이션 정지)를 호출하고 스냅샷이 완료된 후 사후 스크립트(애플리케이션 정지 해제)를 호출하는 작업이 포함됩니다. 

이제 Oracle처럼 선택한 애플리케이션에 패키지된 사전/사후 스크립트가 Azure Backup 서비스에서 제공되고 Linux 이미지에 미리 로드되므로 설치할 필요가 없도록 프레임워크가 향상되었습니다. Azure Backup 사용자는 애플리케이션 이름만 지정하면 됩니다. 그러면 Azure VM 백업에서 관련 사전/사후 스크립트를 자동으로 호출합니다. 패키지된 사전 스크립트 및 사후 스크립트는 Azure Backup 팀에서 유지 관리하므로 사용자는 이러한 스크립트의 지원, 소유권 및 유효성을 보장할 수 있습니다. 현재 향상된 프레임워크에 지원되는 애플리케이션은 *Oracle* 및 *MySQL* 입니다.

> [!Note]
> 향상된 프레임워크는 백업이 실행될 때마다 VM에 설치된 모든 Oracle 데이터베이스에서 사전/사후 스크립트를 실행합니다. 
>
> **workload.conf** 파일의 `configuration_path` 매개 변수는 Oracle /etc/oratab 파일(또는 oratab 구문을 따르는 사용자 정의 파일)의 위치를 가리킵니다. 자세한 내용은 [애플리케이션 일치 백업 설정](#set-up-application-consistent-backups)을 참조하세요.
> 
> Azure Backup은 #(주석으로 처리) 또는 +ASM(Oracle 자동 스토리지 관리 인스턴스)으로 시작하는 줄을 제외하고, configuration_path에서 가리키는 파일에 나열된 각 데이터베이스에 대해 사전/사후 백업 스크립트를 실행합니다.
> 
> Azure Backup 향상된 프레임워크는 ARCHIVELOG 모드에서 작동하는 Oracle 데이터베이스의 온라인 백업을 수행합니다. 사전/사후 스크립트는 ALTER DATABASE BEGIN/END BACKUP 명령을 사용하여 애플리케이션 일관성을 달성합니다. 
>
> 데이터베이스 백업이 일관성을 유지하려면 스냅샷이 시작되기 전에 NOARCHIVELOG 모드의 데이터베이스를 완전히 종료해야 합니다.


이 섹션에서는 Azure Backup 프레임워크를 사용하여 실행되는 VM 및 Oracle 데이터베이스의 애플리케이션 일치 스냅샷을 만듭니다. Azure Backup에서 VM 디스크의 스냅샷을 만드는 동안 트랜잭션에서 일관된 온라인 백업을 수행할 수 있도록 데이터베이스가 백업 모드로 전환됩니다. 스냅샷은 증분 또는 쓰기 중 복사 스냅샷이 아니라 스토리지의 전체 복사본이므로 데이터베이스를 복원할 수 있는 효과적인 매체입니다. Azure Backup 애플리케이션 일치 스냅샷을 사용하는 이점은 데이터베이스의 크기에 관계없이 스냅샷을 매우 빠르게 만들 수 있으며, Recovery Services 자격 증명 모음으로 전송될 때까지 기다릴 필요 없이 해당 스냅샷을 만드는 즉시 복원 작업에 사용할 수 있다는 데 있습니다.

Azure Backup을 사용하여 데이터베이스를 백업하려면 다음 단계를 완료합니다.

1. [애플리케이션 일치 백업 환경을 준비합니다](#prepare-the-environment-for-an-application-consistent-backup).
1. [애플리케이션 일치 백업을 설정합니다](#set-up-application-consistent-backups).
1. [VM의 애플리케이션 일치 백업을 트리거합니다](#trigger-an-application-consistent-backup-of-the-vm).

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>애플리케이션 일치 백업 환경 준비

> [!Note] 
> Oracle 데이터베이스는 최소 권한을 사용하여 업무를 분리하기 위해 작업 역할 분리를 사용합니다. 이는 별도의 운영 체제 그룹을 별도의 데이터베이스 관리 역할과 연결하여 수행됩니다. 그러면 운영 체제 사용자는 운영 체제 그룹의 구성원에 따라 부여된 다른 데이터베이스 권한을 가질 수 있습니다. 
>
> `SYSBACKUP` 데이터베이스 역할(일반 이름 OSBACKUPDBA)은 데이터베이스에서 백업 작업을 수행하는 제한된 권한을 제공하는 데 사용되며 Azure Backup에 필요합니다.
>
> Oracle 설치 중에 SYSBACKUP 역할과 연관시킬 권장 운영 체제 그룹 이름은 `backupdba`이지만 모든 이름을 사용할 수 있으므로 먼저 Oracle SYSBACKUP 역할을 나타내는 운영 체제 그룹의 이름을 판별해야 합니다.

1. *oracle* 사용자로 전환합니다.
   ```bash
   sudo su - oracle
   ```

1. Oracle 환경 설정:
   ```bash
   export ORACLE_SID=oratest1
   export ORAENV_ASK=NO
   . oraenv
   ```

1. Oracle SYSBACKUP 역할을 나타내는 운영 체제 그룹의 이름을 결정합니다.
   ```bash
   grep "define SS_BKP" $ORACLE_HOME/rdbms/lib/config.c
   ```
   출력은 다음과 유사합니다. 
   ```output
   #define SS_BKP_GRP "backupdba"
   ```

   출력에서 큰 따옴표로 묶인 값(이 예에서 `backupdba`)은 Oracle SYSBACKUP 역할이 외부에서 인증되는 Linux 운영 체제 그룹의 이름입니다. 이 값을 적어둡니다.

1. 다음 명령을 실행하여 운영 체제 그룹이 있는지 확인합니다. \<group name\>를 이전 명령에서 반환된 값(따옴표 제외)으로 대체합니다.
   ```bash
   grep <group name> /etc/group
   ```
   출력은 다음과 유사합니다. 이 예에서는 `backupdba`가 사용됩니다. 
   ```output
   backupdba:x:54324:oracle
   ```

   > [!IMPORTANT] 
   > 출력이 3단계에서 검색한 Oracle 운영 체제 그룹 값과 일치하지 않는 경우 Oracle SYSBACKUP 역할을 나타내는 운영 체제 그룹을 생성해야 합니다. 3단계에서 검색한 그룹 이름을 `<group name>`으로 대체합니다.
   >   ```bash
   >   sudo groupadd <group name>
   >   ```

1. 이전 단계에서 확인하거나 작성한 운영 체제 그룹에 속하는 새 백업 사용자 `azbackup`을 작성합니다. 확인된 그룹의 이름을 \<group name\>으로 바꾸세요.

   ```bash
   sudo useradd -g <group name> azbackup
   ```

1. 새 백업 사용자에 대한 외부 인증을 설정합니다. 

   백업 사용자 `azbackup`은 암호로 인한 문제가 발생하지 않도록 외부 인증을 사용하여 데이터베이스에 액세스할 수 있어야 합니다. 이를 수행하려면 `azbackup`을 통해 외부적으로 인증하는 데이터베이스 사용자를 작성해야 합니다. 데이터베이스는 찾아야 하는 사용자 이름에 접두사를 사용합니다.

   VM에 설치된 각 데이터베이스에서 다음 단계를 수행합니다.
 
   sqlplus를 사용하여 데이터베이스에 로그인하고, 외부 인증에 대한 기본 설정을 확인합니다.
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   출력은 데이터베이스 사용자 이름 접두사로 `ops$`를 표시하는 다음 예와 유사해야 합니다. 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   `azbackup` 사용자에게 외부 인증을 위한 데이터베이스 사용자 ***ops$azbackup*** 을 만들고 sysbackup 권한을 부여합니다.
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > `GRANT` 문을 실행할 때 `ORA-46953: The password file is not in the 12.2 format.` 오류가 발생하면 다음 단계에 따라 orapwd 파일을 12.2 형식으로 마이그레이션합니다. VM의 모든 Oracle 데이터베이스에 대해 이 작업을 수행해야 합니다.
   >
   > 1. sqlplus를 종료합니다.
   > 1. 이전 형식의 암호 파일을 새 이름으로 이동합니다.
   > 1. 암호 파일을 마이그레이션합니다.
   > 1. 이전 파일을 제거합니다.
   > 1. 다음 명령을 실행합니다.
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapworatest1 $ORACLE_HOME/dbs/orapworatest1.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapworatest1 input_file=$ORACLE_HOME/dbs/orapworatest1.tmp
   >    rm $ORACLE_HOME/dbs/orapworatest1.tmp
   >    ```
   >
   > 1. sqlplus에서 `GRANT` 작업을 다시 실행합니다.
   >
   
1. 백업 메시지를 데이터베이스 경고 로그에 기록하는 저장 프로시저를 만듭니다.

   VM에 설치된 각 데이터베이스에서 다음을 수행합니다.

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```

### <a name="set-up-application-consistent-backups"></a>애플리케이션 일치 백업 설정  

1. 먼저 루트 사용자로 전환합니다.
   ```bash
   sudo su -
   ```

1. "/etc/azure" 폴더를 확인합니다. 없는 경우 애플리케이션 일치 백업 작업 디렉터리를 만듭니다.

   ```bash
   if [ ! -d "/etc/azure" ]; then
      mkdir /etc/azure
   fi
   ```

1. 폴더 내에 "workload.conf"가 있는지 확인합니다. 해당 파일이 없는 경우 다음과 같은 내용이 포함된 *workload.conf* 라는 파일을 */etc/azure* 디렉터리에 파일을 만듭니다. 이 파일은 `[workload]`로 시작해야 합니다. 파일이 이미 있는 경우 다음 내용과 일치하도록 필드를 편집합니다. 그렇지 않으면 다음 명령에서 파일을 만들고 내용을 채웁니다.

   ```bash
   echo "[workload]
   workload_name = oracle
   configuration_path = /etc/oratab
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

   > [!IMPORTANT]
   > workload.conf에서 사용하는 형식은 다음과 같습니다.
   > * **workload_name** 매개 변수는 Azure Backup에서 데이터베이스 워크로드 유형을 결정하는 데 사용됩니다. 이 경우 oracle로 설정하면 Azure Backup이 Oracle 데이터베이스에 대해 올바른 사전 및 사후 일관성 명령을 실행할 수 있습니다.
   > * **timeout** 매개 변수는 각 데이터베이스가 스토리지 스냅샷을 완료해야 하는 최대 시간(초)을 나타냅니다.
   > * **linux_user** 매개 변수는 Azure Backup에서 데이터베이스 정지 작업을 실행하는 데 사용할 Linux 사용자 계정을 나타냅니다. 이전에 `azbackup` 사용자를 작성했습니다.
   > * 매개 변수 **configuration_path** 는 각 행이 VM에서 실행 중인 데이터베이스 인스턴스를 나열하는 VM의 텍스트 파일에 대한 절대 경로 이름을 나타냅니다. 이는 일반적으로 데이터베이스 설치 중에 Oracle에서 생성하는 `/etc/oratab` 파일이지만 사용자가 선택한 이름의 모든 파일일 수도 있으며 다음 형식 규칙을 따라야 합니다.
   >   * 콜론(`:`)으로 구분된 각 필드가 있는 텍스트 파일
   >   * 각 줄의 첫 번째 필드는 ORACLE_SID의 이름입니다.
   >   * 각 줄의 두 번째 필드는 해당 ORACLE_SID에 대한 ORACLE_HOME의 절대 경로 이름입니다.
   >   * 처음 두 필드 다음의 모든 텍스트는 무시됩니다.
   >   * 줄이 파운드/해시 문자(`#`)로 시작하면 전체 줄이 주석이 되어 무시됩니다.
   >   * 첫 번째 필드에 Automatic Storage Management 인스턴스를 표시하는 `+ASM` 값이 있으면 무시됩니다. 


### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>VM의 애플리케이션 일치 백업 트리거

# <a name="portal"></a>[포털](#tab/azure-portal)

1.  Azure Portal에서 **rg-oracle** 리소스 그룹으로 이동하여 **vmoracle19c** Virtual Machine을 클릭합니다.

1.  **백업** 블레이드에서 새 **Recovery Services 자격 증명 모음** 을 **myVault** 라는 이름으로 **rg-oracle** 리소스 그룹에 만듭니다.
    **백업 정책 선택** 에 대해 **(신규) DailyPolicy** 를 사용합니다. 백업 빈도 또는 보존 범위를 변경하려면 **새 정책 만들기** 를 대신 선택합니다.

    ![Recovery Services 자격 증명 모음 추가 페이지](./media/oracle-backup-recovery/recovery-service-01.png)

1.  계속하려면 **백업 사용** 을 클릭합니다.

    > [!IMPORTANT]
    > **백업 사용** 을 클릭한 후, 백업 프로세스는 예정된 시간이 만료될 때까지 시작되지 않습니다. 즉시 백업을 시작하려면 다음 단계를 완료합니다.

1. 리소스 그룹 페이지에서 새로 만든 **myVault** Recovery Services 자격 증명 모음을 클릭합니다. 힌트: 페이지를 보려면 새로 고쳐야 할 수 있습니다.

1.  **myVault-Backup 항목** 블레이드의 **Backup 항목 수** 에서 백업 항목 수를 선택합니다.

    ![Recovery Services 자격 증명 모음 myVault 세부 정보 페이지](./media/oracle-backup-recovery/recovery-service-02.png)

1.  **Backup 항목(Azure Virtual Machine)** 블레이드에서 페이지의 오른쪽에 있는 줄임표(**...**) 단추를 클릭한 다음 **지금 Backup** 을 클릭합니다.

    ![Recovery Services 자격 증명 모음 지금 Backup 명령](./media/oracle-backup-recovery/recovery-service-03.png)

1.  기본 [다음까지 백업 보존] 값을 적용하고, **확인** 단추를 클릭합니다. 백업 프로세스가 완료될 때까지 기다립니다. 

    백업 작업의 상태를 보려면 **백업 작업** 을 클릭합니다.

    ![Recovery Services 자격 증명 모음 작업 페이지](./media/oracle-backup-recovery/recovery-service-04.png)

    백업 작업의 상태가 다음 이미지에 표시됩니다.

    ![상태가 표시된 Recovery Services 자격 증명 모음 작업 페이지](./media/oracle-backup-recovery/recovery-service-05.png)
    
    스냅샷을 실행하는 데 몇 초밖에 걸리지 않지만, 해당 스냅샷을 자격 증명 모음으로 전송하는 데는 시간이 걸릴 수 있으며 전송이 완료될 때까지 백업 작업이 완료되지 않습니다.

1. 애플리케이션 일치 백업의 경우 로그 파일에서 오류를 해결 합니다. 로그 파일은 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log에 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Recovery Services 자격 증명 모음을 만듭니다.

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

1. 백업 보호를 VM에 사용하도록 설정합니다.

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

1. 백업이 기본 일정(오전 5시 UTC)에 트리거될 때까지 기다리지 않고 지금 실행되도록 백업을 트리거합니다. 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   `az backup job list` 및 `az backup job show`를 사용하여 백업 작업의 진행률을 모니터링할 수 있습니다.

---

## <a name="recovery"></a>복구

데이터베이스를 복구하려면 다음 단계를 완료합니다.

1. [데이터베이스 파일을 제거합니다](#remove-the-database-files).
1. [Recovery Services 자격 증명 모음에서 복원 스크립트를 생성합니다](#generate-a-restore-script-from-the-recovery-services-vault).
1. [복원 지점을 탑재합니다](#mount-the-restore-point).
1. [복구를 수행합니다](#perform-recovery).

### <a name="remove-the-database-files"></a>데이터베이스 파일 제거 

이 문서의 뒷부분에서는 복구 프로세스를 테스트하는 방법을 배웁니다. 복구 프로세스를 테스트하려면 먼저 데이터베이스 파일을 제거해야 합니다.

1.  oracle 사용자로 다시 전환합니다.
    ```bash
    su - oracle
    ```

1. Oracle 인스턴스를 종료합니다.

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

1.  데이터베이스 데이터 파일과 제어 파일을 제거하여 오류를 시뮬레이션합니다.

    ```bash
    cd /u02/oradata/ORATEST1
    rm -f *.dbf *.ctl
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 복원 스크립트 생성

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 *myVault* Recovery Services 자격 증명 모음 항목을 검색하여 선택합니다.

    ![Recovery Services 자격 증명 모음 myVault 백업 항목](./media/oracle-backup-recovery/recovery-service-06.png)

1. **개요** 블레이드에서 **백업 항목** 을 선택하고, 0이 아닌 [백업 항목 수]가 나열되어야 하는 **Azure Virtual Machine** 을 선택합니다.

    ![Recovery Services 자격 증명 모음 Azure Virtual Machine 백업 항목 수](./media/oracle-backup-recovery/recovery-service-07.png)

1. [백업 항목(Azure Virtual Machines)] 페이지에 **vmoracle19c** VM이 나열됩니다. 오른쪽에 있는 줄임표를 클릭하여 메뉴를 표시하고, **파일 복구** 를 선택합니다.

    ![Recovery Services 자격 증명 모음 파일 복구 페이지의 스크린샷](./media/oracle-backup-recovery/recovery-service-08.png)

1. **파일 복구(미리 보기)** 창에서 **스크립트 다운로드** 를 클릭합니다. 그런 다음, 다운로드 파일(.py)을 클라이언트 컴퓨터의 폴더에 저장합니다. 스크립트를 실행하기 위한 암호가 생성됩니다. 나중에 사용할 수 있도록 암호를 파일에 복사합니다. 

    ![다운로드한 스크립트 파일 저장 옵션](./media/oracle-backup-recovery/recovery-service-09.png)

1. .py 파일을 VM에 복사합니다.

    다음 예제에서는 파일을 VM으로 이동하는 안전한 복사(scp) 명령을 사용하는 방법을 보여 줍니다. 콘텐츠를 클립보드로 복사한 다음 VM에서 설정한 새 파일로 붙여넣을 수 있습니다.

    > [!IMPORTANT]
    > 다음 예제에서는 IP 주소와 폴더 값을 업데이트했는지 확인합니다. 값은 파일이 저장된 폴더를 매핑해야 합니다.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

VM에 대한 복구 지점을 나열하려면 az backup recovery point list를 사용합니다. 이 예에서는 myVault라는 Recovery Services Vault에서 보호되는 vmoracle19c라는 VM의 최신 복구 지점을 선택합니다.

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

복구 지점을 VM에 연결하거나 탑재하는 스크립트를 가져오려면 az backup restore files mount-rp를 사용합니다. 다음 예에서는 myVault라는 Recovery Services 자격 증명 모음에서 보호되는 vmoracle19c라는 VM에 대한 스크립트를 가져옵니다.

myRecoveryPointName을 이전 명령에서 가져온 복구 지점 이름으로 바꿉니다.

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

다음 예제와 같이 스크립트가 다운로드되고 암호가 표시됩니다.

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

.py 파일을 VM에 복사합니다.

다음 예제에서는 파일을 VM으로 이동하는 안전한 복사(scp) 명령을 사용하는 방법을 보여 줍니다. 콘텐츠를 클립보드로 복사한 다음 VM에서 설정한 새 파일로 붙여넣을 수 있습니다.

> [!IMPORTANT]
> 다음 예제에서는 IP 주소와 폴더 값을 업데이트했는지 확인합니다. 값은 파일이 저장된 폴더를 매핑해야 합니다.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>복원 지점 탑재

1. 루트 사용자로 전환합니다.
   ```bash
   sudo su -
   ``````
1. 복원 탑재 지점을 만들고, 스크립트를 해당 지점에 복사합니다.

    다음 예제에서는 스냅샷을 탑재할 */restore* 디렉터리를 만들고, 파일을 디렉터리로 이동하고, 루트 사용자가 소유하고 실행 파일이 되도록 파일을 변경합니다.

    ```bash 
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    이제 스크립트를 실행하여 백업을 복원합니다. Azure Portal에서 생성된 암호를 입력하라는 메시지가 표시됩니다. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    다음 예제에서는 이전 스크립트를 실행한 후 표시되는 내용이 나옵니다. 계속할 것인지 묻는 메시지가 표시되면 **Y** 를 입력합니다.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

1. 탑재된 볼륨에 대한 액세스가 확인되었습니다.

    종료하려면 **q** 를 입력한 다음, 탑재된 볼륨을 검색합니다. 추가된 볼륨의 목록을 만들려면 명령 프롬프트에서 **df -h** 를 입력합니다.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>복구 수행
VM의 각 데이터베이스에서 다음 단계를 수행합니다.

1. 누락된 데이터베이스 파일을 해당 위치로 복원합니다.

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/oradata/ORATEST1
    cp * /u02/oradata/ORATEST1
    cd /u02/oradata/ORATEST1
    chown -R oracle:oinstall *
    ```
1. oracle 사용자로 다시 전환
   ```bash
   sudo su - oracle
   ```
1. 데이터베이스 인스턴스를 시작하고 읽기를 위해 제어 파일을 탑재합니다.
   ```bash
   sqlplus / as sysdba
   SQL> startup mount
   SQL> quit
   ```

1. sysbackup을 사용하여 데이터베이스에 연결합니다.
   ```bash
   sqlplus / as sysbackup
   ```
1. 자동 데이터베이스 복구 시작:

   ```bash
   SQL> recover automatic database until cancel using backup controlfile;
   ```
   > [!IMPORTANT]
   > 복원된 데이터베이스 제어 파일에 기록된 Oracle SCN(시스템 변경 번호)에서 복구가 중지되지 않도록 RECOVER AUTOMATIC DATABASE 명령에 알리기 위해 USING BACKUP CONTROLFILE 구문을 지정하는 것이 중요합니다. 복원된 데이터베이스 제어 파일은 나머지 데이터베이스와 함께 스냅샷이었으며 그 안에 저장된 SCN은 스냅샷의 특정 시점에서 가져온 것입니다. 이 시점 이후에 기록된 트랜잭션이 있을 수 있으며 데이터베이스에 커밋된 마지막 트랜잭션의 특정 시점으로 복구하려고 합니다.

   복구가 성공적으로 완료되면 `Media recovery complete` 메시지가 표시됩니다. 그러나 BACKUP CONTROLFILE 절을 사용하는 경우 recover 명령은 온라인 로그 파일을 무시하고 특정 시점 복구를 완료하는 데 필요한 현재 온라인 다시 실행 로그에 변경 사항이 있을 수 있습니다. 이 상황에서 다음과 유사한 메시지가 표시될 수 있습니다.

   ```output
   SQL> recover automatic database until cancel using backup controlfile;
   ORA-00279: change 2172930 generated at 04/08/2021 12:27:06 needed for thread 1
   ORA-00289: suggestion :
   /u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc
   ORA-00280: change 2172930 for thread 1 is in sequence #13
   ORA-00278: log file
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc' no
   longer needed for this recovery
   ORA-00308: cannot open archived log
   '/u02/fast_recovery_area/ORATEST1/archivelog/2021_04_08/o1_mf_1_13_%u_.arc'
   ORA-27037: unable to obtain file status
   Linux-x86_64 Error: 2: No such file or directory
   Additional information: 7

   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   ```
   
   > [!IMPORTANT]
   > 현재 온라인 다시 실행 로그가 손실되거나 손상되어 사용할 수 없는 경우 이 시점에서 복구를 취소할 수 있습니다. 

   이 문제를 해결하기 위해 보관되지 않은 현재 온라인 로그를 식별하고 프롬프트에 완전한 파일 이름을 제공할 수 있습니다.


   새 SSH 연결 열기 
   ```bash
   ssh azureuser@<IP Address>
   ```
   Oracle 사용자로 전환하고 Oracle SID를 설정합니다.
   ```bash
   sudo su - oracle
   export ORACLE_SID=oratest1
   ```
   
   데이터베이스에 연결하고 다음 쿼리를 실행하여 온라인 로그 파일을 찾습니다. 
   ```bash
   sqlplus / as sysdba
   SQL> column member format a45
   SQL> set linesize 500  
   SQL> select l.SEQUENCE#, to_char(l.FIRST_CHANGE#,'999999999999999') as CHK_CHANGE, l.group#, l.archived, l.status, f.member
   from v$log l, v$logfile f
   where l.group# = f.group#;
   ```

   출력은 다음과 비슷합니다. 
   ```output
   SEQUENCE#  CHK_CHANGE           GROUP# ARC STATUS            MEMBER
   ---------- ---------------- ---------- --- ---------------- ---------------------------------------------
           13          2172929          1 NO  CURRENT          /u02/oradata/ORATEST1/redo01.log
           12          2151934          3 YES INACTIVE         /u02/oradata/ORATEST1/redo03.log
           11          2071784          2 YES INACTIVE         /u02/oradata/ORATEST1/redo02.log
   ```
   CURRENT 온라인 로그에 대한 로그 파일 경로 및 파일 이름을 복사합니다. 이 예에서는 `/u02/oradata/ORATEST1/redo01.log`입니다. recover 명령을 실행하는 ssh 세션으로 다시 전환하고 로그 파일 정보를 입력한 다음 Return 키를 누릅니다.

   ```bash
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   ```

   로그 파일이 적용되고 복구가 완료된 것을 볼 수 있습니다. CANCEL을 입력하여 복구 명령을 종료합니다.
   ```output
   Specify log: {<RET>=suggested | filename | AUTO | CANCEL}
   /u02/oradata/ORATEST1/redo01.log
   Log applied.
   Media recovery complete.
   ```

1. 데이터베이스 열기
   
   > [!IMPORTANT]
   > RECOVER 명령이 USING BACKUP CONTROLFILE 옵션을 사용하는 경우 RESETLOGS 옵션이 필요합니다. RESETLOGS는 다시 실행 기록을 처음으로 다시 설정하여 데이터베이스의 새 구현을 생성합니다. 이전 데이터베이스 구현 중 복구에서 건너뛴 양을 확인할 방법이 없기 때문입니다.

   ```bash
   SQL> alter database open resetlogs;
   ```

   
1. 데이터베이스 콘텐츠가 완전히 복구되었는지 확인합니다.

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

1. 복원 지점을 분리합니다.

   VM의 모든 데이터베이스가 복구되면 복원 지점을 분리할 수 있습니다. 이 작업은 `unmount` 명령을 사용하여 VM에서 또는 파일 복구 블레이드의 Azure Portal에서 수행할 수 있습니다. **-clean** 옵션을 사용하여 python 스크립트를 다시 실행하여 복구 볼륨을 분리할 수도 있습니다.

   분리를 사용하는 VM에서:
   ```bash
   sudo umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Azure Portal의 **파일 복구(미리 보기)** 블레이드에서 **디스크 분리** 를 클릭합니다.

    ![디스크 분리 명령](./media/oracle-backup-recovery/recovery-service-10.png)
    


## <a name="restore-the-entire-vm"></a>전체 VM 복원

Recovery Services 자격 증명 모음에서 삭제된 파일을 복원하는 대신 전체 VM을 복원할 수 있습니다.

전체 VM을 복원하려면 다음 단계를 완료합니다.

1. [VM을 중지 후 삭제합니다](#stop-and-delete-the-vm).
1. [VM을 복구합니다](#recover-the-vm).
1. [공용 IP 주소를 설정합니다](#set-the-public-ip-address).
1. [데이터베이스 복구를 수행합니다](#perform-database-recovery).

### <a name="stop-and-delete-the-vm"></a>VM 중지 후 삭제

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 **vmoracle19c** Virtual Machine으로 이동한 다음, **중지** 를 선택합니다.

1. Virtual Machine이 더 이상 실행되지 않으면 **삭제** 를 선택한 다음, **예** 를 선택합니다.

   ![자격 증명 모음 삭제 명령](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM을 중지하고 할당을 취소합니다.

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

1. VM을 삭제합니다. 메시지가 표시되면 'y'를 입력합니다.

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>VM 복구

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 준비를 위한 스토리지 계정을 만듭니다.

   1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고, **스토리지 계정** 을 검색하여 선택합니다.
    
      ![리소스를 만들 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. [스토리지 계정 만들기] 페이지에서 기존 **rg-oracle** 리소스 그룹을 선택하고, 스토리지 계정 이름을 **oracrestore** 로 지정하고, [계정 종류]에 대해 **Storage V2(범용 v2)** 를 선택합니다. [복제]를 **LRS(로컬 중복 스토리지)** 로 변경하고, [성능]을 **표준** 으로 설정합니다. 위치가 리소스 그룹의 다른 모든 리소스와 동일한 지역으로 설정되어 있는지 확인합니다. 
    
      ![스토리지 계정 추가 페이지](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. [검토 + 만들기]를 클릭한 다음, [만들기]를 클릭합니다.

1. Azure Portal에서 *myVault* Recovery Services 자격 증명 모음 항목을 검색하여 클릭합니다.

    ![Recovery Services 자격 증명 모음 myVault 백업 항목](./media/oracle-backup-recovery/recovery-service-06.png)
    
1.  **개요** 블레이드에서 **백업 항목** 을 선택하고, 0이 아닌 [백업 항목 수]가 나열되어야 하는 **Azure Virtual Machine** 을 선택합니다.

    ![Recovery Services 자격 증명 모음 Azure Virtual Machine 백업 항목 수](./media/oracle-backup-recovery/recovery-service-07.png)

1.  [백업 항목(Azure Virtual Machines)]에 **vmoracle19c** VM이 나열됩니다. VM 이름을 클릭합니다.

    ![VM 복구 페이지](./media/oracle-backup-recovery/recover-vm-02.png)

1.  **vmoracle19c** 블레이드에서 일관성 유형이 **애플리케이션 일치** 인 복원 지점을 선택하고, 오른쪽에 있는 줄임표( **...** )를 클릭하여 메뉴를 표시합니다.  메뉴에서 **VM 복원** 을 클릭합니다.

    ![VM 복원 명령](./media/oracle-backup-recovery/recover-vm-03.png)

1.  **가상 머신 복원** 블레이드에서 **새로 만들기** 및 **새 가상 머신 만들기** 를 선택합니다. **vmoracle19c** 가상 머신 이름을 입력하고, **vmoracle19cVNET** VNet을 선택합니다. 그러면 VNet 선택 항목에 따라 서브넷이 자동으로 채워집니다. VM 복원 프로세스에는 동일한 리소스 그룹 및 지역에 있는 Azure 스토리지 계정이 필요합니다. 이전에 설정한 **orarestore** 스토리지 계정을 선택할 수 있습니다.

    ![복원 구성 값](./media/oracle-backup-recovery/recover-vm-04.png)

1.  VM을 복원하려면 **복원** 단추를 클릭합니다.

1.  복원 프로세스의 상태를 보려면 **작업** 을 클릭한 다음, **백업 작업** 을 클릭합니다.

    ![Backup 작업 상태 명령](./media/oracle-backup-recovery/recover-vm-05.png)

    **진행 중** 복원 작업을 클릭하여 복원 프로세스의 상태를 표시합니다.

    ![복원 프로세스의 상태](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

스토리지 계정 및 파일 공유를 설정하려면 Azure CLI에서 다음 명령을 실행합니다.

1. 스토리지 계정을 VM과 동일한 리소스 그룹 및 위치에 만듭니다.

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

1. 사용 가능한 복구 지점 목록을 검색합니다. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

1. 복구 지점을 스토리지 계정에 복원합니다. `<myRecoveryPointName>`을 이전 단계에서 생성된 목록의 복구 지점으로 바꿉니다.

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

1. 복원 작업 세부 정보를 검색합니다. 다음 명령은 템플릿 URI를 검색하는 데 필요한 해당 이름을 포함하여 트리거된 복원 작업에 대한 자세한 정보를 가져옵니다. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   출력은 다음과 비슷합니다(`(Note down the name of the restore job)`).

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

1. VM을 다시 만드는 데 사용할 URI의 세부 정보를 검색합니다. `<RestoreJobName>`을 이전 단계의 복원 작업 이름으로 바꿉니다.

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   출력은 다음과 비슷합니다.

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Template Blob Uri(템플릿 Blob URI)의 끝에 있는 템플릿 이름(이 예제에서는 `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`)과 Blob 컨테이너 이름(`vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`)이 나열됩니다. 

   다음 명령에서 이러한 값을 사용하여 VM 만들기를 준비하는 변수를 할당합니다. 스토리지 컨테이너에 대한 SAS 키가 30분 동안 생성됩니다.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   이제 템플릿을 배포하여 VM을 만듭니다.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   VM 이름을 제공하라는 메시지가 표시됩니다.

---

### <a name="set-the-public-ip-address"></a>공용 IP 주소 설정

VM이 복원되면 원래 IP 주소를 새 VM에 다시 할당해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1.  Azure Portal에서 **vmoracle19c** Virtual Machine으로 이동합니다. vmoracle19c-nic-XXXXXXXXXXXX와 비슷한 새 공용 IP 및 NIC가 할당되었지만 DNS 주소가 없음을 알 수 있습니다. 원래 VM이 삭제되었으면 해당 공용 IP와 NIC가 유지되고 다음 단계에서 새 VM에 다시 연결됩니다.

    ![공용 IP 주소의 목록](./media/oracle-backup-recovery/create-ip-01.png)

1.  VM을 중지합니다.

    ![IP 주소 만들기](./media/oracle-backup-recovery/create-ip-02.png)

1.  **네트워킹** 으로 이동합니다.

    ![IP 주소 연결](./media/oracle-backup-recovery/create-ip-03.png)

1.  **네트워크 인터페이스 연결** 을 클릭하고, 원래 공용 IP 주소가 여전히 연결되어 있는 원래 NIC(**vmoracle19cVMNic)를 선택한 다음, **확인** 을 클릭합니다.

    ![리소스 종류 및 NIC 값 선택](./media/oracle-backup-recovery/create-ip-04.png)

1.  이제 VM 복원 작업을 사용하여 만든 NIC가 기본 인터페이스로 구성되어 있으므로 이를 분리해야 합니다. **네트워크 인터페이스 분리** 를 클릭하고 **vmoracle19c-nic-XXXXXXXXXXXX** 와 비슷한 새 NIC를 선택한 다음, **확인** 을 클릭합니다.

    ![네트워크 인터페이스 분리를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/oracle-backup-recovery/create-ip-05.png)
    
    이제 다시 만든 VM에 원래 IP 주소 및 네트워크 보안 그룹 규칙과 연결된 원래 NIC가 있습니다.
    
    ![IP 주소 값](./media/oracle-backup-recovery/create-ip-06.png)
    
1.  **개요** 로 돌아가서 **시작** 을 클릭합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM을 중지하고 할당을 취소합니다.

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

1. 현재 복원 생성된 VM NIC를 나열합니다.

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   출력은 다음과 비슷하며, 복원 생성된 NIC 이름이 `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`로 나열됩니다.

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

1. 이름이 `<VMName>VMNic`여야 하는 원래 NIC(이 경우 `vmoracle19cVMNic`)를 연결합니다. 원래 공용 IP 주소는 이 NIC에 계속 연결되어 있으며, NIC가 다시 연결되면 VM에 복원됩니다. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

1. 복원 생성된 NIC를 분리합니다.

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```
1. VM을 시작합니다.

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="perform-database-recovery"></a>데이터베이스 복구 수행
먼저 VM에 다시 연결합니다.

```azurecli
ssh azureuser@<publicIpAddress>
```

전체 VM을 복원한 경우 각각에서 다음 단계를 수행하여 VM의 각 데이터베이스를 복구하는 것이 중요합니다.

1. VM 부팅 시 자동 시작에서 데이터베이스를 시작하려고 할 때 인스턴스가 실행되고 있음을 알 수 있습니다. 그러나 데이터베이스는 복구해야 하고 탑재 단계에만 있을 수 있으므로, 준비 종료가 먼저 실행된 후 탑재 단계가 시작됩니다.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    ```
    
1. 데이터베이스 복구 수행
   > [!IMPORTANT]
   > 복원된 데이터베이스 제어 파일에 기록된 Oracle SCN(시스템 변경 번호)에서 복구가 중지되지 않도록 RECOVER AUTOMATIC DATABASE 명령에 알리기 위해 USING BACKUP CONTROLFILE 구문을 지정하는 것이 중요합니다. 복원된 데이터베이스 제어 파일은 나머지 데이터베이스와 함께 스냅샷이었으며 그 안에 저장된 SCN은 스냅샷의 특정 시점에서 가져온 것입니다. 이 시점 이후에 기록된 트랜잭션이 있을 수 있으며 데이터베이스에 커밋된 마지막 트랜잭션의 특정 시점으로 복구하려고 합니다.
    
    ```bash
    SQL> recover automatic database using backup controlfile until cancel;
    ```
   마지막으로 사용 가능한 보관 로그 파일을 `CANCEL` 형식에 적용하여 복구를 종료합니다.

1. 데이터베이스 열기
   > [!IMPORTANT]
   > RECOVER 명령이 USING BACKUP CONTROLFILE 옵션을 사용하는 경우 RESETLOGS 옵션이 필요합니다. RESETLOGS는 다시 실행 기록을 처음으로 다시 설정하여 데이터베이스의 새 구현을 생성합니다. 이전 데이터베이스 구현 중 복구에서 건너뛴 양을 확인할 방법이 없기 때문입니다.
   
    ```bash 
    SQL> alter database open resetlogs;
    ```
   

1. 데이터베이스 콘텐츠가 복구되었는지 확인합니다.

    ```bash
    SQL> select * from scott.scott_table;
    ```

이제 Azure Linux VM에서 Oracle Database 19c 데이터베이스의 백업 및 복구가 완료되었습니다.

Oracle 명령 및 개념에 대한 자세한 내용은 다음을 포함하여 Oracle 문서에서 찾을 수 있습니다.

   * [전체 데이터베이스의 Oracle 사용자 관리 백업 수행](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-database-backups.html#GUID-65C5E03A-E906-47EB-92AF-6DC273DBD0A8)
   * [완벽한 사용자 관리 데이터베이스 복구 수행](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-66D07694-533F-4E3A-BA83-DD461B68DB56)
   * [Oracle STARTUP 명령](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/STARTUP.html#GUID-275013B7-CAE2-4619-9A0F-40DB71B61FE8)
   * [Oracle RECOVER 명령](https://docs.oracle.com/en/database/oracle/oracle-database/19/bradv/user-managed-flashback-dbpitr.html#GUID-54B59888-8683-4CD9-B144-B0BB68887572)
   * [Oracle ALTER DATABASE 명령](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ALTER-DATABASE.html#GUID-8069872F-E680-4511-ADD8-A4E30AF67986)
   * [Oracle LOG_ARCHIVE_DEST_n 매개 변수](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/LOG_ARCHIVE_DEST_n.html#GUID-10BD97BF-6295-4E85-A1A3-854E15E05A44)
   * [Oracle ARCHIVE_LAG_TARGET 매개 변수](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/ARCHIVE_LAG_TARGET.html#GUID-405D335F-5549-4E02-AFB9-434A24465F0B)


## <a name="delete-the-vm"></a>VM 삭제

더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

1. 자격 증명 모음에서 백업 일시 삭제 사용 안 함

    ```azurecli
    az backup vault backup-properties set --name myVault --resource-group rg-oracle --soft-delete-feature-state disable
    ```

1. VM에 대한 보호 중지 및 백업 삭제

    ```azurecli
    az backup protection disable --resource-group rg-oracle --vault-name myVault --container-name vmoracle19c --item-name vmoracle19c --delete-backup-data true --yes
    ```

1. 모든 리소스를 포함한 리소스 그룹 제거

    ```azurecli
    az group delete --name rg-oracle
    ```

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
