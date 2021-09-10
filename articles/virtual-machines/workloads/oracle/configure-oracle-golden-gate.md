---
title: Azure Linux VM에서 Oracle Golden Gate 구현 | Microsoft Docs
description: Oracle Golden Gate를 Azure 환경에서 빠르게 시작하고 실행합니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 69df4491272e849c34edd65c462d37587615ae46
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689703"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Azure Linux VM에서 Oracle Golden Gate 구현 

**적용 대상:** :heavy_check_mark: Linux VM 

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Azure Marketplace 갤러리 이미지에서 Oracle 12c 데이터베이스를 배포하는 방법을 자세히 설명합니다. 

이 문서에서는 Azure VM에서 Oracle Golden Gate를 만들고, 설치 및 구성하는 방법을 단계별로 보여 줍니다. 이 튜토리얼에서는 단일 지역의 가용성 집합에서 두 개의 가상 시스템이 설정됩니다. 동일한 튜토리얼을 사용하여 단일 Azure 지역에서 다른 가용성 영역에서 VM의 OracleGolden Gate를 설정하거나 두 개의 다른 지역에서 VM을 설정할 수 있습니다.

시작하기 전에 Azure CLI가 설치되었는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-the-environment"></a>환경 준비

Oracle Golden Gate 설치를 수행하려면 동일한 가용성 집합에서 두 개의 Azure VM을 만들어야 합니다. VM을 만드는 데 사용하는 Marketplace 이미지는 **Oracle:Oracle-Database-Ee:12.1.0.2:latest** 입니다.

Unix 편집기 vi를 잘 알고 있고 x11(X Windows)을 기본적으로 이해해야 합니다.

다음은 환경 구성에 대한 요약입니다.
> 
> |  | **기본 사이트** | **복제 사이트** |
> | --- | --- | --- |
> | **Oracle 릴리스** |Oracle 12c 릴리스 2 – (12.1.0.2) |Oracle 12c 릴리스 2 – (12.1.0.2)|
> | **컴퓨터 이름** |myVM1 |myVM2 |
> | **운영 체제** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **복제 스키마** |테스트|테스트 |
> | **Golden Gate 소유자/복제** |C##GGADMIN |REPUSER |
> | **Golden Gate 프로세스** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/reference-index) 명령을 사용하여 Azure 구독에 로그인합니다. 그런 다음 화면에 나타나는 지침에 따릅니다.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되며 관리될 수 있는 논리적 컨테이너입니다. 

다음 예제에서는 `westus` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>가용성 집합 만들기

다음 단계는 선택 사항이지만 권장됩니다. 자세한 내용은 [Windows VM에 대한 Azure 가용성 집합 지침](/previous-versions/azure/virtual-machines/windows/infrastructure-example)을 참조하세요.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 `myVM1` 및 `myVM2`라고 하는 VM 두 개를 만듭니다. 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

#### <a name="create-myvm1-primary"></a>myVM1(기본) 만들기:

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`를 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>myVM2(복제) 만들기:

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

VM을 만든 후 `publicIpAddress`도 기록해 둡니다.

### <a name="open-the-tcp-port-for-connectivity"></a>연결에 대한 TCP 포트 열기

다음 단계에서는 Oracle 데이터베이스에 원격으로 액세스할 수 있는 외부 엔드포인트를 구성합니다. 외부 엔드포인트를 구성하려면 다음 명령을 실행합니다.

#### <a name="open-the-port-for-myvm1"></a>myVM1에 대한 포트 열기:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

결과는 다음 응답과 유사하게 나타납니다.

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>myVM2에 대한 포트 열기:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용하여 가상 머신과의 SSH 세션을 만듭니다. 해당 IP 주소를 가상 머신의 `publicIpAddress`로 바꿉니다.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>myVM1(기본)에서 데이터베이스 만들기

Oracle 소프트웨어는 Marketplace 이미지에 이미 설치되어 있으므로 다음 단계에서 데이터베이스를 설치합니다. 

'oracle' 슈퍼 사용자로 소프트웨어 실행:

```bash
sudo su - oracle
```

데이터베이스를 만듭니다.

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

출력은 다음 응답과 유사하게 나타납니다.

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

ORACLE_SID 및 ORACLE_HOME 변수를 설정합니다.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

필요에 따라 .bashrc 파일에 ORACLE_HOME 및 ORACLE_SID를 추가하여 후속 로그인을 위해 이러한 설정을 저장할 수 있습니다.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle 수신기 시작

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>myVM2(복제)에서 데이터베이스 만들기

```bash
sudo su - oracle
```

데이터베이스를 만듭니다.

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

ORACLE_SID 및 ORACLE_HOME 변수를 설정합니다.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

필요에 따라 .bashrc 파일에 ORACLE_HOME 및 ORACLE_SID를 추가하여 후속 로그인을 위해 이러한 설정을 저장할 수 있습니다.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle 수신기 시작

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Golden Gate 구성 
Golden Gate를 구성하려면 이 섹션의 단계를 따릅니다.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>myVM1(기본)에서 보관 로그 모드 사용

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
강제 로깅을 사용하도록 설정하고 하나 이상의 로그 파일이 있는지 확인합니다.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Golden Gate 소프트웨어 다운로드
Oracle Golden Gate 소프트웨어를 다운로드 및 준비하려면 다음 단계를 완료합니다.

1. [Oracle Golden Gate 다운로드 페이지](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html)에서 **fbo_ggs_Linux_x64_shiphome.zip** 파일을 다운로드합니다. 다운로드 제목 **Oracle GoldenGate 12.x.x.x for Oracle Linux x86-64** 아래에 다운로드할 .zip 파일 집합이 있습니다.

2. .zip 파일을 클라이언트 컴퓨터로 다운로드한 후 SCP(Secure Copy Protocol)를 사용하여 파일을 VM으로 복사합니다.

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. .zip 파일을 **/opt** 폴더로 이동합니다. 그다음에 다음과 같이 파일의 소유자를 변경합니다.

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. 파일의 압축을 풉니다. (Linux 압축 풀기 유틸리티가 설치되어 있지 않은 경우 이를 설치합니다.)

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. 사용 권한을 변경합니다.

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>x11을 실행하는 클라이언트 및 VM을 준비합니다(Windows 클라이언트에만 해당).
선택적 단계입니다. Linux 클라이언트를 사용하거나 x11을 이미 설치한 경우 이 단계를 건너뛸 수 있습니다.

1. Windows 컴퓨터에 PuTTY 및 Xming을 다운로드합니다.

   * [PuTTY 다운로드](https://www.putty.org/)
   * [Xming 다운로드](https://xming.en.softonic.com/)

2. PuTTY를 PuTTY 폴더(예: C:\Program Files\PuTTY)에 설치한 후 puttygen.exe(PuTTY 키 생성기)를 실행합니다.

3. PuTTY 키 생성기에서,

   - 키를 생성하려면 **생성** 단추를 선택합니다.
   - 키의 콘텐츠를 복사합니다(**Ctrl + C**).
   - **프라이빗 키 저장** 단추를 선택합니다.
   - 표시되는 경고를 무시하고 **확인** 을 선택합니다.

   ![PuTTY 키 생성기 페이지의 스크린샷](./media/oracle-golden-gate/puttykeygen.png)

4. VM에서 다음 명령을 실행합니다.

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. **authorized_keys** 라는 파일을 만듭니다. 키의 콘텐츠를 이 파일에 붙여넣은 다음 파일을 저장합니다.

   > [!NOTE]
   > 키에는 문자열 `ssh-rsa`가 포함되어야 합니다. 또한 키의 콘텐츠는 한 줄 텍스트여야 합니다.
   >  

6. PuTTY를 시작합니다. **범주** 창에서 **연결** > **SSH** > **인증** 을 선택하세요. **인증용 개인 키 파일** 상자에서 이전에 생성한 키를 찾으세요.

   ![프라이빗 키 설정 페이지의 스크린샷](./media/oracle-golden-gate/setprivatekey.png)

7. **범주** 창에서 **연결** > **SSH** > **X11** 을 선택합니다. 그다음에 **X11 전달을 사용하도록 설정** 상자를 선택합니다.

   ![X11 사용 페이지의 스크린샷](./media/oracle-golden-gate/enablex11.png)

8. **카테고리** 창에서 **세션** 으로 이동합니다. 호스트 정보를 입력한 다음 **열기** 를 선택합니다.

   ![세션 페이지의 스크린샷](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Golden Gate 소프트웨어 설치

Oracle Golden Gate를 설치하려면 다음 단계를 완료합니다.

1. oracle로 로그인합니다. (암호를 입력하라는 메시지가 표시되지 않고 로그인 할 수 있습니다.) 설치를 시작 하기 전에 Xming이 실행되고 있는지 확인하세요.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. 'Oracle GoldenGate for Oracle Database 12c'를 선택합니다. 그리고 **다음** 을 선택하여 계속합니다.

   ![설치 관리자 설치 선택 페이지의 스크린샷](./media/oracle-golden-gate/golden_gate_install_01.png)

3. 소프트웨어 위치를 변경합니다. 그다음에 **관리자 시작** 상자를 선택하고 데이터베이스 위치를 입력합니다. 계속하려면 **다음** 을 선택합니다.

   ![설치 선택 페이지의 스크린샷](./media/oracle-golden-gate/golden_gate_install_02.png)

4. 인벤토리 디렉터리를 변경하고 **다음** 을 선택하여 계속합니다.

   ![설치 디렉터리를 표시하는 설치 선택 페이지 스크린샷](./media/oracle-golden-gate/golden_gate_install_03.png)

5. **요약** 화면에서 **설치** 를 선택하여 계속합니다.

   ![설치 선택 페이지 및 설치 버튼 스크린샷](./media/oracle-golden-gate/golden_gate_install_04.png)

6. 스크립트를 'root'로 실행할지 묻는 메시지가 표시될 수 있습니다. 메시지가 표시되면 별도의 세션, ssh를 VM으로, sudo를 root로 열고 스크립트를 실행합니다. **확인** 을 선택하여 계속합니다.

   ![스크립트 위치 및 구성 스크립트를 실행 방법 스크린샷](./media/oracle-golden-gate/golden_gate_install_05.png)

7. 설치가 완료되면 **닫기** 를 선택하여 프로세스를 완료합니다.

   ![닫기 버튼을 표시하는 설치 선택 페이지 스크린샷](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>myVM1(기본)에서 서비스 설정

1. tnsnames.ora 파일 만들거나 업데이트합니다.

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Golden Gate 소유자 및 사용자 계정을 만듭니다.

   > [!NOTE]
   > 소유자 계정에는 C## 접두사가 있어야 합니다.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Golden Gate 테스트 사용자 계정을 만듭니다.

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. extract 매개 변수 파일을 구성합니다.

   Golden Gate 명령줄 인터페이스(ggsci)를 시작합니다.

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. EXTRACT 매개 변수 파일에 다음을 추가합니다(vi 명령 사용). Esc 키, ':wq!'를 눌러 파일을 저장합니다. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. extract--integrated extract를 등록합니다.

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. extract 검사점을 설정하고 실시간 추출을 시작합니다.

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   이 단계에서는 나중에 다른 섹션에서 사용되는 시작 SCN을 찾습니다.

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>myVM2(복제)에서 서비스 설정


1. tnsnames.ora 파일 만들거나 업데이트합니다.

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. 복제 계정을 만듭니다.

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Golden Gate 테스트 사용자 계정을 만듭니다.

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. 변경 내용을 복제하기 위한 REPLICAT 매개 변수 파일: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   REPORA 매개 변수 파일의 콘텐츠:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. 복제본 검사점 설정:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>복제 설정(myVM1 및 myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. myVM2(복제본)에서 복제 설정

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

다음을 사용하여 파일을 업데이트합니다.

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

관리자 서비스를 다시 시작합니다.

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. myVM1(기본)에서 복제 설정

초기 로드를 시작하고 오류를 확인합니다.

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. myVM2(복제본)에서 복제 설정

이전에 얻은 번호로 SCN 번호를 변경합니다.

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

복제가 시작되면 새 레코드를 TEST 테이블에 삽입하여 테스트할 수 있습니다.


### <a name="view-job-status-and-troubleshooting"></a>작업 상태 보기 및 문제 해결

#### <a name="view-reports"></a>보고서 보기
myVM1에서 보고서를 보려면 다음 명령을 실행합니다.

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
myVM2에서 보고서를 보려면 다음 명령을 실행합니다.

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>상태 및 기록 보기
myVM1에서 상태 및 기록을 보려면 다음 명령을 실행합니다.

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

myVM2에서 상태 및 기록을 보려면 다음 명령을 실행합니다.

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
이렇게 하면 Oracle linux에서 Golden Gate의 설치 및 구성이 완료됩니다.


## <a name="delete-the-virtual-machine"></a>가상 머신 삭제

더 이상 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[고가용성 가상 머신 만들기 자습서](../../linux/create-cli-complete.md)

[VM 배포 CLI 샘플 탐색](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)