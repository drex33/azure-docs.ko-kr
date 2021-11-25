---
title: Azure VM에서 Oracle 데이터베이스 만들기 | Microsoft Docs
description: Azure 환경에서 Oracle Database 12c 데이터베이스를 신속하게 가동하고 실행합니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.custom: mode-other
ms.openlocfilehash: 2bdd3c96eacc1c437c6c29ab86cdec65572dbf12
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056891"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Azure VM에서 Oracle 데이터베이스 만들기

**적용 대상:** :heavy_check_mark: Linux VM 

이 가이드에서는 Oracle 19c 데이터베이스를 만들기 위해 Azure CLI를 사용하여 [Oracle 마켓플레이스 갤러리 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)에서 Azure 가상 머신을 배포하는 방법에 대해 자세히 설명합니다. 서버가 배포된 후 Oracle 데이터베이스를 구성하려면 SSH를 통해 연결합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예에서는 *eastus* 위치에 *rg-oracle* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

VM(가상 머신)을 만들려면 [az vm create](/cli/azure/vm) 명령을 사용합니다. 

다음 예제는 `vmoracle19c`라는 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`에 대한 값을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Oracle 데이터 파일 및 FRA용 새 디스크 새 디스크 만들기 및 연결

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 64 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>연결을 위한 포트 열기
이 작업에서는 VM을 보호하는 Azure Network Security 그룹을 설정하여 데이터베이스 수신기 및 EM Express에서 사용할 일부 외부 엔드포인트를 구성해야 합니다. 

1. Oracle 데이터베이스에 원격으로 액세스하는 데 사용하는 엔드포인트를 열려면 다음과 같이 네트워크 보안 그룹 규칙을 만듭니다.
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Oracle EM Express에 원격으로 액세스하는 데 사용하는 엔드포인트를 열려면 다음과 같이 az network nsg rule create를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. 필요한 경우 다음과 같이 az network public-ip show를 사용하여 VM의 공용 IP 주소를 다시 가져옵니다.

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>VM 환경 준비

1. VM에 연결

   VM으로 SSH 세션을 만들려면 다음 명령을 사용합니다. 해당 IP 주소를 VM의 `publicIpAddress` 값으로 바꿉니다.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. 루트 사용자로 전환

   ```bash
   sudo su -
   ```

3. Oracle 데이터 파일 보관에 사용하기 위해 포맷할 마지막으로 만든 디스크 디바이스를 확인합니다.

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   다음과 유사하게 출력됩니다.
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. 디바이스를 포맷합니다. 
   디바이스에서 루트 사용자 실행이 분리되었으므로 
   
   먼저 디스크 레이블을 만듭니다.
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   그런 다음, 전체 디스크에 걸쳐 주 파티션을 만듭니다.
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   마지막으로 메타데이터를 인쇄하여 디바이스 세부 정보를 확인합니다.
   ```bash
   parted /dev/sdc print
   ```
   출력은 다음과 비슷한 모양입니다.
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. 디바이스 파티션에 파일 시스템 만들기

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. 탑재 지점 만들기
   ```bash
   mkdir /u02
   ```

7. 디스크 탑재

   ```bash
   mount /dev/sdc1 /u02
   ```

8. 탑재 지점에 대한 권한 변경

   ```bash
   chmod 777 /u02
   ```

9. /etc/fstab 파일에 탑재를 추가합니다. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. 공용 IP 및 호스트 이름으로 ***/etc/hosts*** 파일을 업데이트합니다.

    실제 값을 반영하도록 ***공용 IP 및 VMname*** 을 변경합니다.
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. 호스트 이름 파일 업데이트
    
    다음 명령을 사용하여 **/etc/hostname** 파일에 VM의 도메인 이름을 추가합니다. 여기서는 **eastus** 지역에 리소스 그룹 및 VM을 만들었다고 가정합니다.
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. 방화벽 포트 열기
    
    SELinux는 Marketplace 이미지에서 기본적으로 사용되기 때문에 데이터베이스 수신 포트 1521 및 Enterprise Manager Express 포트 5502에 대한 트래픽에 방화벽을 열어야 합니다. 루트 사용자로 다음 명령을 실행합니다.

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>데이터베이스 만들기

Oracle 소프트웨어는 이미 Marketplace 이미지에 설치되어 있습니다. 다음과 같이 샘플 데이터베이스를 만듭니다. 

1.  **oracle** 사용자로 전환합니다.

    ```bash
    sudo su - oracle
    ```
2. 데이터베이스 수신기 시작

   ```bash
   lsnrctl start
   ```
   다음과 유사하게 출력됩니다.
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Oracle 데이터 파일용 데이터 디렉터리 만들기

   ```bash
   mkdir /u02/oradata
   ```
    

3.  데이터베이스 생성 도우미를 실행합니다.

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname oratest1 \
       -sid oratest1 \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    데이터베이스를 만드는 데 몇 분이 걸립니다.

    다음과 유사한 출력이 표시됩니다.

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/oratest1.
       Database Information:
       Global Database Name:oratest1
       System Identifier(SID):oratest1
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/oratest1/oratest1.log" for further details.
    ```

4. Oracle 변수를 설정합니다.

    연결하기 전에 환경 변수 *ORACLE_SID* 를 설정해야 합니다.

    ```bash
        export ORACLE_SID=oratest1
    ```

    또한 다음 명령을 사용하여 향후 로그인에 사용할 `oracle` 사용자 `.bashrc` 파일에 ORACLE_SID 변수를 추가해야 합니다.

    ```bash
    echo "export ORACLE_SID=oratest1" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express 연결

데이터베이스를 탐색할 수 있는 GUI 관리 도구의 경우 Oracle EM Express를 설정합니다. Oracle EM Express에 연결하려면 먼저 Oracle에서 포트를 구성해야 합니다. 

1. sqlplus를 사용하여 데이터베이스에 연결합니다.

    ```bash
    sqlplus sys as sysdba
    ```

2. 연결되면 EM Express에 대해 5502 포트를 설정합니다.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  브라우저에서 EM Express를 연결합니다. 브라우저가 EM Express와 호환되는지 확인합니다(Flash 설치 필요). 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    **SYS** 계정을 사용하여 로그인하고 **as sysdba** 확인란을 선택합니다. 설치 중에 설정한 **OraPasswd1** 암호를 사용합니다. 

    ![Oracle OEM Express 로그인 페이지의 스크린샷](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>데이터베이스 시작 및 종료 자동화

Oracle 데이터베이스는 기본적으로 VM을 다시 시작할 때 자동으로 시작되지 않습니다. Oracle 데이터베이스가 자동으로 시작되도록 설정하려면 먼저 루트로 로그인합니다. 그런 다음 일부 시스템 파일을 만들고 업데이트합니다.

1. 루트로 로그인합니다.

    ```bash
    sudo su -
    ```

2.  다음 명령을 실행하여 `/etc/oratab` 파일에서 자동화된 시작 플래그를 `N`에서 `Y`로 변경합니다.

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  `/etc/init.d/dbora`라는 파일을 만들고 다음 콘텐츠를 붙여 넣습니다.

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  *chmod* 를 사용하여 다음과 같이 파일에 대한 권한을 변경합니다.

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  다음과 같이 시작 및 종료에 대한 기호 링크를 만듭니다.

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  변경 내용을 테스트하려면 VM을 다시 시작합니다.

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 첫 번째 Oracle 데이터베이스 탐색이 끝나고 VM이 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[Oracle 백업 전략](./oracle-database-backup-strategies.md)을 사용하여 Azure에서 데이터베이스를 보호하는 방법 이해

[Azure의 다른 Oracle 솔루션](./oracle-overview.md)에 대해 알아봅니다. 

[Oracle Automated Storage Management 설치 및 구성](configure-oracle-asm.md) 자습서를 사용해 봅니다.
