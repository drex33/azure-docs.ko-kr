---
title: Azure Linux 가상 머신에 Oracle ASM 설정 | Microsoft Docs
description: Azure 환경에서 Oracle ASM을 빠르게 준비하여 실행합니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 7bd7b8e13ef6c44952f682497a4f4a5cdc2ebc3f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688089"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Azure Linux 가상 머신에 Oracle ASM 설정

**적용 대상:** :heavy_check_mark: Linux VM 

Azure Virtual Machines는 완전하게 구성할 수 있고 유연한 컴퓨팅 환경을 제공합니다. 이 자습서에서는 Oracle ASM(Automated Storage Management) 설치 및 구성과 결합된 기본 Azure 가상 머신 배포에 대해 설명합니다.  다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Oracle 데이터베이스 VM 만들기 및 연결
> * Oracle Automated Storage Management 설치 및 구성
> * Oracle Grid 인프라 설치 및 구성
> * Oracle ASM 설치 초기화
> * ASM에서 관리하는 Oracle DB 만들기


CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="prepare-the-environment"></a>환경 준비

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들려면 [az group create](/cli/azure/group) 명령을 사용합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>VM 만들기

Oracle Database 이미지에 따라 가상 머신을 만들고 Oracle ASM을 사용하도록 구성하려면 [az vm create](/cli/azure/vm) 명령을 사용합니다. 

다음 예에서는 각 50GB인 네 개의 연결된 데이터 디스크를 포함한 Standard_DS2_v2 크기의 myVM이라는 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다.  특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. `publicIpAddress`에 대한 값을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>VM에 연결

VM으로 SSH 세션을 만들고 추가 설정을 구성하려면 다음 명령을 사용합니다. 해당 IP 주소를 VM의 `publicIpAddress` 값으로 바꿉니다.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM 설치

Oracle ASM을 설치하려면 다음 단계를 완료합니다. 

Oracle ASM 설치에 대한 자세한 내용은 [Oracle Linux 6에 대한 Oracle ASMLib 다운로드](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)를 참조하세요.  

1. ASM 설치를 계속하기 위해 루트로 로그인해야 합니다.

   ```bash
   sudo su -
   ```
   
2. Oracle ASM 구성 요소를 설치하기 위해 이러한 추가 명령을 실행합니다.

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Oracle ASM이 설치되어 있는지 확인합니다.

   ```bash
   rpm -qa |grep oracleasm
   ```

    이 명령의 출력은 다음 구성 요소를 나열해야 합니다.

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM이 제대로 작동하기 위해 특정 사용자 및 역할이 필요합니다. 다음 명령은 필수 구성 요소 사용자 계정 및 그룹을 만듭니다. 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. 사용자 및 그룹이 올바르게 생성되었는지 확인합니다.

   ```bash
   id grid
   ```

    이 명령의 출력은 다음과 같은 사용자 및 그룹을 나열해야 합니다.

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. *grid* 사용자를 위한 폴더를 만들고 소유자를 변경합니다.

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM 설정

이 자습서에서는 기본 사용자가 *grid* 이며, 기본 그룹은 *asmadmin* 입니다. *oracle* 사용자가 asmadmin 그룹의 구성원인지 확인합니다. Oracle ASM 설치를 설정하려면 다음 단계를 완료합니다.

1. Oracle ASM 라이브러리 드라이버를 설정하는 작업에는 드라이브를 부팅하기 시작(y 선택)하고 부팅 시 디스크를 검색(y 선택)하도록 구성할 뿐만 아니라 기본 사용자(그리드) 및 기본 그룹(asmadmin)을 정의하는 작업이 포함됩니다. 다음 명령에서 표시되는 메시지에 답해야 합니다.

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   이 명령의 출력은 다음 출력과 유사해야 하며 표시되는 메시지에 답하면 중지됩니다.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. 디스크 구성을 확인합니다.

   ```bash
   cat /proc/partitions
   ```

   이 명령의 출력은 다음 사용 가능한 디스크 목록과 유사해야 합니다.

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. 다음 명령을 실행하고 표시되는 메시지에 답하여 */dev/sdc* 디스크를 포맷합니다.
   - 새 파티션의 경우 *n*
   - 주 파티션의 경우 *p*
   - 첫 번째 파티션을 선택하려면 *1*
   - 첫 번째 기본 실린더의 경우 `enter` 키를 누름
   - 최근 기본 실린더의 경우 `enter` 키를 누름
   - 파티션 테이블에 변경 내용을 쓰려면 *w* 키를 누름  

   ```bash
   fdisk /dev/sdc
   ```
   
   `fdisk` 명령에 대한 출력은 위의 답변을 사용하여 다음과 같이 표시됩니다.

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. `/dev/sdd`, `/dev/sde` 및 `/dev/sdf`의 경우 위의 `fdisk` 명령을 반복합니다.

5. 디스크 구성을 확인합니다.

   ```bash
   cat /proc/partitions
   ```

   명령 출력은 다음과 같습니다.

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Oracle ASM 서비스 상태를 확인하고 Oracle ASM 서비스를 시작합니다.

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   명령 출력은 다음과 같습니다.

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM 디스크를 만듭니다.

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   명령 출력은 다음과 같습니다.

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM 디스크를 나열합니다.

   ```bash
   service oracleasm listdisks
   ```

   이 명령의 출력은 다음 Oracle ASM 디스크를 나열합니다.

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. 루트, oracle 및 그리드 사용자에 대한 암호를 변경합니다. 나중에 설치하는 동안 사용하도록 **이러한 새 암호를 기록** 합니다.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. 폴더 사용 권한을 변경합니다.

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure 다운로드 및 준비

Oracle Grid Infrastructure 소프트웨어를 다운로드 및 준비하려면 다음 단계를 완료합니다.

1. [Oracle ASM 다운로드 페이지](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html)에서 Oracle Grid Infrastructure를 다운로드합니다. 

   다운로드 제목 **Linux x86-64용 Oracle Database 12c 릴리스 1 Grid Infrastructure(12.1.0.2.0)** 에서 두 개의 .zip 파일을 다운로드합니다.

2. 클라이언트 컴퓨터에 .zip 파일을 다운로드한 후 SCP(보안 복사 프로토콜)를 사용하여 파일을 VM에 복사합니다.

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. .zip 파일을 /opt 폴더로 이동하기 위해 Azure에서 Oracle VM에 다시 SSH합니다. 그런 다음 파일의 소유자를 변경합니다.

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. 파일의 압축을 풉니다. (Linux 압축 풀기 도구가 설치되어 있지 않은 경우 이를 설치합니다.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. 사용 권한을 변경합니다.

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. 구성된 스왑 공간을 업데이트합니다. Oracle Grid 구성 요소에서 Grid를 설치하려면 적어도 6.8GB의 스왑 공간이 필요합니다. Azure에서 Oracle Linux 이미지에 대한 기본 스왑 파일 크기는 2048MB입니다. 업데이트된 설정을 적용하기 위해 `/etc/waagent.conf` 파일에서 `ResourceDisk.SwapSizeMB`를 늘리고 WALinuxAgent 서비스를 다시 시작해야 합니다. 읽기 전용 파일이기 때문에 쓰기 액세스를 사용할 수 있도록 파일 사용 권한을 변경해야 합니다.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   `ResourceDisk.SwapSizeMB`를 검색하고 값을 **8192** 로 변경합니다. `insert`를 눌러 삽입 모드를 입력하고 **8192** 값을 입력한 다음 `esc` 키를 눌러 명령 모드로 돌아가야 합니다. 변경 내용을 쓰고 파일을 종료하려면 `:wq`를 입력하고 `enter` 키를 누릅니다.
   
   > [!NOTE]
   > 성능을 최대화하기 위해 스왑 공간이 항상 로컬 사용 후 삭제 디스크(임시 디스크)에 만들어지도록 `WALinuxAgent`를 사용하여 스왑 공간을 구성하는 것이 좋습니다. 자세한 내용은 [Linux Azure Virtual Machines에 스왑 파일을 추가하는 방법](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)을 참조합니다.

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>로컬 클라이언트와 VM에서 X11 실행 준비
Oracle ASM를 구성하려면 설치 및 구성을 완료할 그래픽 인터페이스가 필요합니다. X11 프로토콜을 사용하여 이 설치를 용이하게 합니다. X11 기능을 사용하도록 설정하고 구성한 클라이언트 시스템(Mac 또는 Linux)을 사용하는 경우 Windows 컴퓨터에만 독점적인 이 구성 및 설정을 건너뛰어도 됩니다. 

1. Windows 컴퓨터에 [PuTTY를 다운로드](https://www.putty.org/)하고 [Xming](https://xming.en.softonic.com/)을 다운로드합니다. 계속하기 전에 기본 값으로 이러한 애플리케이션의 설치를 완료해야 합니다.

2. PuTTY를 설치한 후에 명령 프롬프트를 열고, PuTTY 폴더(예: C:\Program Files\PuTTY)로 변경하고 키를 생성하기 위해 `puttygen.exe`를 실행합니다.

3. PuTTY 키 생성기에서,
   
   1. `Generate` 단추를 선택하여 키를 생성합니다.
   2. 키의 콘텐츠를 복사(Ctrl + C)합니다.
   3. `Save private key` 단추를 선택합니다.
   4. 암호를 사용하여 키를 보호하는 방법에 대한 경고를 무시한 다음 `OK`을 선택합니다.

   ![PuTTY 키 생성기의 스크린샷](./media/oracle-asm/puttykeygen.png)

4. VM에서 다음 명령을 실행합니다.

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. `authorized_keys`라는 파일을 만듭니다. 키의 콘텐츠를 이 파일에 붙여넣은 다음 파일을 저장합니다.

   > [!NOTE]
   > 키에는 문자열 `ssh-rsa`가 포함되어야 합니다. 또한 키의 콘텐츠는 한 줄 텍스트여야 합니다.
   >  

6. 클라이언트 컴퓨터에서 PuTTY를 시작합니다. **범주** 창에서 **연결** > **SSH** > **인증** 으로 이동합니다. **인증에 대한 프라이빗 키 파일** 상자에서 앞서 생성한 키를 찾아봅니다.

   ![SSH 인증 옵션의 스크린샷](./media/oracle-asm/setprivatekey.png)

7. **카테고리** 창에서 **연결** > **SSH** > **X11** 로 이동합니다. **X11 전달을 사용하도록 설정** 확인란을 선택합니다.

   ![SSH X11 전달 옵션의 스크린샷](./media/oracle-asm/enablex11.png)

8. **카테고리** 창에서 **세션** 으로 이동합니다. 호스트 이름 대화 상자에서 Oracle ASM VM `<publicIPaddress>`를 입력하고 새 `Saved Session` 이름을 채운 다음 `Save`을 클릭합니다.  저장하면 `open`를 클릭하여 Oracle ASM 가상 컴퓨터에 연결합니다.  처음 연결하면 원격 시스템이 레지스트리에서 캐시되지 않는다는 경고가 표시됩니다. `yes`를 클릭하고 계속 추가합니다.

   ![PuTTY 세션 옵션의 스크린샷](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure 설치

Grid Infrastructure를 설치하려면 다음 단계를 완료합니다.

1. **그리드** 로 로그인합니다. (로그인할 때 암호 입력 화면이 나타나지 않아야 합니다.) 

   > [!NOTE]
   > Windows를 실행하는 경우 설치를 시작하기 전에 Xming을 시작해야 합니다.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c 릴리스 1 설치 관리자가 열립니다. (설치 관리자를 시작하는 데 몇 분 정도 걸릴 수 있습니다.)

2. **설치 옵션 선택** 페이지에서 **독립 실행형 서버에 대한 Oracle Grid Infrastructure 설치 및 구성** 을 선택합니다.

   ![설치 관리자 설치 옵션 선택 페이지의 스크린샷](./media/oracle-asm/install01.png)

3. **제품 언어 선택** 페이지에서 **영어** 또는 원하는 언어를 선택했는지 확인합니다.  `next`을 클릭합니다.

4. **ASM 디스크 그룹 만들기** 페이지에서,
   - 디스크 그룹에 사용할 이름을 입력합니다.
   - **중복** 에서 **외부** 를 선택합니다.
   - **할당 단위 크기** 에서 **4** 를 선택합니다.
   - **디스크 추가** 에서 **ORCLASMSP** 를 선택합니다.
   - `next`을 클릭합니다.

5. **ASM 암호 지정** 페이지에서 **이러한 계정에 대해 동일한 암호 사용** 옵션을 선택하고 암호를 입력합니다.

   ![설치 관리자 ASM 암호 지정 페이지의 스크린샷](./media/oracle-asm/install04.png)

6. **관리 옵션 지정** 페이지에서는 EM 클라우드 컨트롤을 구성하는 옵션이 있습니다. 이 옵션을 무시하려면 `next`을 클릭하여 계속합니다. 

7. **권한있는 운영 체제 그룹** 페이지에서 기본 설정을 사용합니다. 계속하려면 `next`을 클릭합니다.

8. **설치 위치 지정** 페이지에서 기본 설정을 사용합니다. 계속하려면 `next`을 클릭합니다.

9. **인벤토리 만들기** 페이지에서 인벤토리 디렉터리를 `/u01/app/grid/oraInventory`로 변경합니다. 계속하려면 `next`을 클릭합니다.

   ![설치 관리자 인벤토리 만들기 페이지의 스크린샷](./media/oracle-asm/install08.png)

10. **루트 스크립트 실행 구성** 페이지에서 **구성 스크립트 자동으로 실행** 확인란을 선택합니다. 그런 다음 **"root" 사용자 자격 증명 사용** 옵션을 선택하고 루트 사용자 암호를 입력합니다.

    ![설치 관리자 루트 스크립트 실행 구성 페이지의 스크린샷](./media/oracle-asm/install09.png)

11. **필수 요소 확인 수행** 페이지에서 오류가 발생하여 현재 설치에 실패합니다. 이는 정상적인 동작입니다. `Fix & Check Again`를 선택합니다.

12. **스크립트 수정** 대화 상자에서 `OK`을 클릭합니다.

13. **요약** 페이지에서 선택한 설정을 검토한 다음 `Install`를 클릭합니다.

    ![설치 관리자 요약 페이지의 스크린샷](./media/oracle-asm/install12.png)

14. 구성 스크립트를 권한 있는 사용자로 실행해야 한다는 경고 대화 상자가 표시됩니다. 계속하려면 `Yes`을 클릭합니다.

15. **마침** 페이지에서 `Close`를 클릭하여 설치를 마칩니다.

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM 설치 설정

Oracle ASM 설치를 설정하려면 다음 단계를 완료합니다.

1. X11 세션에서 **그리드** 로 로그인되었는지 확인합니다. `enter` 키를 눌러서 터미널을 복구할 수도 있습니다. Oracle Automated Storage Management Configuration Assistant를 시작합니다.

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM 구성 도우미가 열립니다.

2. **ASM 구성: 디스크 그룹** 대화 상자에서 `Create` 단추를 클릭한 다음 `Show Advanced Options`을 클릭합니다.

3. **디스크 그룹 만들기** 대화 상자에서 다음을 수행합니다.

   - 디스크 그룹 이름 **DATA** 를 입력합니다.
   - **멤버 디스크 선택** 에서 **ORCL_DATA** 및 **ORCL_DATA1** 을 선택합니다.
   - **할당 단위 크기** 에서 **4** 를 선택합니다.
   - `ok`을 클릭하여 디스크 그룹을 만듭니다.
   - `ok`을 클릭하여 확인 창을 닫습니다.

   ![디스크 그룹 만들기 대화 상자의 스크린샷](./media/oracle-asm/asm02.png)

4. **ASM 구성: 디스크 그룹** 대화 상자에서 `Create` 단추를 클릭한 다음 `Show Advanced Options`을 클릭합니다.

5. **디스크 그룹 만들기** 대화 상자에서 다음을 수행합니다.

   - 디스크 그룹 이름 **FRA** 를 입력합니다.
   - **중복** 에서 **외부(없음)** 를 선택합니다.
   - **멤버 디스크 선택** 에서 **ORCL_FRA** 를 선택합니다.
   - **할당 단위 크기** 에서 **4** 를 선택합니다.
   - `ok`을 클릭하여 디스크 그룹을 만듭니다.
   - `ok`을 클릭하여 확인 창을 닫습니다.

   ![디스크 그룹 만들기 대화 상자 및 외부(없음) 옵션이 강조 표시된 스크린샷.](./media/oracle-asm/asm04.png)

6. **끝내기** 를 선택하여 ASM 구성 도우미를 닫습니다.

   ![끝내기 단추가 있는 ASM 구성: 디스크 그룹 대화 상자의 스크린샷](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>데이터베이스 생성

Oracle 데이터베이스 소프트웨어는 이미 Azure Marketplace 이미지에 설치되어 있습니다. 데이터베이스를 만들려면 다음 단계를 완료합니다.

1. 사용자를 Oracle superuser로 전환하고, 다음을 로깅하기 위해 수신기를 초기화합니다.

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   데이터베이스 구성 도우미가 열립니다.

2. **데이터베이스 작업 페이지** 에서 `Create Database`를 클릭합니다.

3. **생성 모드** 페이지에서,

   - 데이터베이스에 사용할 이름을 입력합니다.
   - **스토리지 형식** 의 경우 **ASM(자동 스토리지 관리)** 을 선택하도록 합니다.
   - **데이터베이스 파일 위치** 의 경우 위치를 제안하는 ASM 기본값을 사용합니다.
   - **빠른 복구 영역** 의 경우 위치를 제안하는 ASM 기본값을 사용합니다.
   - **관리 암호** 및 **암호 확인** 을 입력합니다.
   - `create as container database`를 선택했는지 확인합니다.
   - `pluggable database name` 값을 입력합니다.

4. **요약** 페이지에서 선택한 설정을 검토한 다음 `Finish`을 클릭하여 데이터베이스를 만듭니다.

   ![요약 페이지의 스크린샷](./media/oracle-asm/createdb03.png)

5. 데이터베이스가 생성되었습니다. **마침** 페이지에서는 추가 계정의 잠금을 해제하여 이 데이터베이스를 사용하고 암호를 변경하는 옵션이 있습니다. 작업을 수행하려는 경우 **암호 관리** 를 선택하고 그렇지 않으면 `close`를 클릭합니다.

## <a name="delete-the-vm"></a>VM 삭제

Azure Marketplace의 Oracle DB 이미지에서 Oracle Automated Storage Management를 성공적으로 구성했습니다.  더 이상 VM이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[자습서: Oracle DataGuard 구성](configure-oracle-dataguard.md)

[자습서: Oracle GoldenGate 구성](Configure-oracle-golden-gate.md)

[Oracle DB 설계](oracle-design.md) 검토
