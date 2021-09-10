---
title: Linux용 Azure에서 SAP BusinessObjects BI 플랫폼 배포 | Microsoft Docs
description: Linux용 Azure에서 SAP BusinessObjects BI 플랫폼 배포 및 구성
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: bf1ca9de6d7ee8ac6c7e87bd444cbd1d98b56f51
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463408"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure의 Linux용 SAP BusinessObjects BI 플랫폼 배포 가이드

이 문서에서는 Linux용 Azure에서 SAP BusinessObjects BI(BOBI) 플랫폼 배포 전략을 설명합니다. 이 예제에서는 프리미엄 SSD(반도체 드라이브) 관리 디스크를 설치 디렉터리로 사용하는 가상 머신을 두 개 구성합니다. 사용자는 CMS 데이터베이스용 Azure Database for MySQL을 사용하고 두 서버에서 파일 리포지토리 서버용 Azure NetApp Files를 공유합니다. 두 가상 머신에 기본 Tomcat Java 웹 애플리케이션과 BI 플랫폼 애플리케이션이 함께 설치됩니다. 사용자 요청의 부하를 분산하려면 기본 TLS/SSL 오프로딩 기능이 포함된 Azure Application Gateway를 사용합니다.

이 유형의 아키텍처는 소규모 배포 또는 비프로덕션 환경에서 사용하기에 효과적입니다. 대규모 배포 또는 프로덕션 환경의 경우 별도의 웹 애플리케이션용 호스트를 사용할 수 있습니다. 또한 여러 BOBI 애플리케이션 호스트를 사용할 수 있어 서버에서 자세한 정보를 처리할 수 있습니다.

![Linux용 Azure의 SAP BOBI 배포 다이어그램](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

다음은 이 예제에 사용되는 제품 버전과 파일 시스템 레이아웃입니다.

- SAP BusinessObjects 플랫폼 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL(버전: 8.0.15)
- MySQL C API Connector - libmysqlclient(버전: 6.1.11)

| 파일 시스템        | Description                                                                                                               | 크기(GB)             | 소유자  | 그룹  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | SAP BOBI 인스턴스, 기본 Tomcat 웹 애플리케이션 및 데이터베이스 드라이버(필요한 경우)를 설치하기 위한 파일 시스템 | SAP 크기 조정 지침 | bl1adm | sapsys | 관리형 프리미엄 디스크 - SSD |
| /usr/sap/frsinput  | 탑재 디렉터리는 입력 파일 리포지토리 디렉터리로 사용되는 모든 BOBI 호스트의 공유 파일을 위한 것입니다.  | 비즈니스 요구         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | 탑재 디렉터리는 출력 파일 리포지토리 디렉터리로 사용되는 모든 BOBI 호스트의 공유 파일을 위한 것입니다. | 비즈니스 요구         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure Portal을 통해 Linux 가상 머신 배포

이 섹션에서는 SAP BOBI 플랫폼용 Linux 운영 체제 이미지를 사용하여 가상 머신을 두 개 만듭니다. 가상 머신을 만드는 대략적인 단계는 다음과 같습니다.

1. [리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)을 만듭니다.

2. [가상 네트워크](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만듭니다.

   - SAP BI 플랫폼 배포의 모든 Azure 서비스에 단일 서브넷을 사용하지 마세요. SAP BI 플랫폼 아키텍처에 따라 서브넷을 여러 개 만들어야 합니다. 이 배포에서는 애플리케이션, 파일 리포지토리 저장소, Application Gateway에 하나씩 세 개의 서브넷을 만듭니다.
   - Azure에서 Application Gateway와 Azure NetApp Files는 항상 별도의 서브넷에 있어야 합니다. 자세한 내용은 [Azure Application Gateway](../../../application-gateway/configuration-overview.md) 및 [Azure NetApp Files 네트워크 계획 지침](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)을 참조하세요.

3. 가용성 집합을 만듭니다. 다중 인스턴스 배포의 각 계층에서 중복도를 달성하려면 가용성 집합의 계층마다 가상 머신을 배치합니다. 아키텍처에 따라 각 계층의 가용성 집합을 분리해야 합니다.

4. **(azusbosl1)** 이라는 가상 머신 1을 만듭니다.

   - 사용자 지정 이미지를 사용해도 되고 Azure Marketplace에서 이미지를 선택해도 됩니다. 자세한 내용은 [SAP용 Azure Marketplace에서 VM 배포](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) 또는 [SAP용 사용자 지정 이미지를 사용하여 VM 배포](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap)를 참조하세요.

5. **(azusbosl2)** 라는 가상 머신 2를 만듭니다.
6. 프리미엄 SSD 디스크를 하나 추가합니다. 이를 SAP BOBI 설치 디렉터리로 사용합니다.

## <a name="provision-azure-netapp-files"></a>Azure NetApp Files 프로비저닝

Azure NetApp Files 설정을 계속 진행하기 전에 [Azure NetApp Files 설명서](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 숙지하세요.

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용 가능합니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공하는지 확인하세요.

[Azure 지역별 Azure NetApp Files 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)을 사용하여 지역별 Azure NetApp Files 가용성을 확인합니다.

Azure NetApp Files를 배포하기 전에 [Azure NetApp Files 지침의 등록](../../../azure-netapp-files/azure-netapp-files-register.md)을 참조하세요.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포

다음 지침에서는 [Azure 가상 네트워크](../../../virtual-network/virtual-networks-overview.md)를 이미 배포했다고 가정합니다. Azure NetApp Files 리소스가 탑재될 Azure NetApp Files 리소스와 VM을 동일하거나 피어링된 Azure Virtual Network에 배포해야 합니다.

1. 아직 리소스를 배포하지 않은 경우 [Azure NetApp Files를 등록](../../../azure-netapp-files/azure-netapp-files-register.md)합니다.

2. 선택한 Azure 지역에서 [Azure NetApp Files 계정을 만듭니다](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. [Azure NetApp Files 용량 풀을 설정합니다](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md). 이 문서에 제공된 SAP BI 플랫폼 아키텍처는 프리미엄 서비스 수준에서 단일 Azure NetApp Files 용량 풀을 사용합니다. Azure의 SAP BI 파일 리포지토리 서버에는 Azure NetApp Files 프리미엄 또는 울트라 [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)을 사용하는 것이 좋습니다.

4. [Azure NetApp Files에 서브넷을 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)합니다.

5. [Azure NetApp Files용 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) 지침에 따라 Azure NetApp Files 볼륨을 배포합니다.

   두 프로토콜이 모두 SAP BOBI 플랫폼에서 지원되므로, 볼륨을 NFSv3 및 NFSv4.1로 배포할 수 있습니다. 각각의 Azure NetApp Files 서브넷에 볼륨을 배포합니다. Azure NetApp Files 볼륨의 IP 주소는 자동으로 할당됩니다.

Azure NetApp Files 리소스와 Azure VM은 동일하거나 피어링된 Azure Virtual Network에 있어야 합니다. 예를 들어 *azusbobi-frsinput*, *azusbobi-frsoutput* 은 볼륨 이름이고 *nfs://10.31.2.4/azusbobi-frsinput*, *nfs://10.31.2.4/azusbobi-frsoutput* 은 Azure NetApp Files 볼륨의 파일 경로입니다.

- 볼륨 azusbobi-frsinput(nfs://10.31.2.4/azusbobi-frsinput)
- 볼륨 azusbobi-frsoutput(nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>중요 고려 사항

SAP BOBI 플랫폼 파일 리포지토리 서버에 사용할 Azure NetApp Files를 만들 때 다음 사항을 고려해야 합니다.

- 최소 용량 풀은 4TiB(테비바이트)입니다.
- 최소 볼륨 크기는 100GiB(기비바이트)입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재될 모든 가상 머신은 동일한 Azure 가상 네트워크에 있거나, 동일한 지역에서 [피어링된 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md)에 있어야 합니다. 동일한 지역에서 가상 네트워크 피어링을 통한 Azure NetApp Files 액세스가 지원됩니다. 글로벌 피어링을 통한 Azure NetApp 액세스는 현재 지원되지 않습니다.
- 선택한 가상 네트워크에 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- Azure NetApp Files [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 사용하면 허용된 클라이언트, 액세스 유형(예: 읽기-쓰기, 읽기 전용)을 제어할 수 있습니다.
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 이 기능은 Azure 지역의 일부 가용성 영역에만 배포되어 있습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.
- Azure NetApp Files 볼륨은 NFSv3 또는 NFSv4.1 볼륨으로 배포할 수 있습니다. 두 프로토콜 모두 SAP BI 플랫폼 애플리케이션을 지원합니다.

## <a name="configure-file-systems-on-linux-servers"></a>Linux 서버에서 파일 시스템 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

**[A]** : 이 단계는 모든 호스트에 적용됩니다.

### <a name="format-and-mount-the-sap-file-system"></a>SAP 파일 시스템 포맷 및 탑재

1. **[A]** 연결된 모든 디스크를 나열합니다.

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to virtual machine, whose device name is sdc
    ```

2. **[A]** /usr/sap에 대한 블록 디바이스를 포맷합니다.

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 탑재 디렉터리를 만듭니다.

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 블록 디바이스의 UUID를 가져옵니다.

    ```bash
    sudo blkid

    # It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab에 파일 시스템 탑재 항목을 유지 관리합니다.

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 파일 시스템을 탑재합니다.

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** 탑재 디렉터리를 만듭니다.

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** NFSv4.1 탑재를 지원하도록 클라이언트 운영 체제를 구성합니다(NFSv4.1을 사용하는 경우에만 해당).

   NFSv4.1 프로토콜이 있는 Azure NetApp Files 볼륨을 사용하는 경우 Azure NetApp Files NFSv4.1 볼륨을 탑재해야 하는 모든 VM에서 다음 구성을 실행합니다.

   이 단계에서는 NFS 도메인 설정을 확인해야 합니다. 도메인이 기본 Azure NetApp Files 도메인(`defaultv4iddomain.com`)으로 구성되어 있고 매핑이 `nobody`로 설정되어 있는지 확인합니다.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   > VM의 /etc/idmapd.conf에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성(`defaultv4iddomain.com`)과 일치하도록 설정합니다. 일치하지 않으면 VM에 탑재된 Azure NetApp Files 볼륨의 파일 사용 권한이 `nobody`로 표시됩니다.

   `nfs4_disable_idmapping`를 확인합니다. `Y`로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** 탑재 항목을 추가합니다.

   NFSv3을 사용하는 경우:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv4.1을 사용하는 경우:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS 볼륨을 탑재합니다.

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-azure-database-for-mysql"></a>Azure Database for MySQL 구성

이 섹션에서는 Azure Portal을 사용하여 Azure Database for MySQL을 프로비저닝하는 방법을 자세히 설명합니다. 여기에는 SAP BOBI 플랫폼용 CMS 및 감사 데이터베이스를 만들고 데이터베이스에 액세스하기 위한 사용자 계정을 만드는 방법에 대한 지침도 나와 있습니다.

이 지침은 Azure Database for MySQL을 사용하는 경우에만 적용됩니다. 다른 데이터베이스의 경우 SAP 또는 데이터베이스별 설명서의 지침을 참조하세요.

### <a name="create-a-database"></a>데이터베이스 만들기

Azure Portal에 로그인하고 [빠른 시작: Azure Portal을 사용하여 Azure Database for MySQL 서버 만들기](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)의 단계를 따릅니다. 다음은 Azure Database for MySQL을 프로비저닝할 때 주의할 몇 가지 사항입니다.

- SAP BI 플랫폼 애플리케이션 서버가 실행되고 있는 Azure Database for MySQL과 동일한 지역을 선택합니다.

- 사용 중인 SAP BOBI 버전의 [SAP BI용 PAM(제품 가용성 매트릭스)](https://support.sap.com/pam)에 따라 지원되는 데이터베이스 버전을 선택합니다.

- **컴퓨팅+스토리지** 에서 **서버 구성** 을 선택하고 크기 조정 출력에 따라 적절한 가격 책정 계층을 선택합니다.

- **스토리지 자동 증가** 는 기본적으로 사용됩니다. [스토리지](../../../mysql/concepts-pricing-tiers.md#storage)는 스케일 다운이 아닌 스케일 업만 가능합니다.

- 기본적으로 **백업 보존 기간** 은 7일입니다. 최대 35일까지 [필요에 따라 구성](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)할 수 있습니다.

- Azure Database for MySQL 백업은 기본적으로 로컬에 중복됩니다. 지역 중복 스토리지에서 서버 백업을 사용하려면 **백업 중복 옵션** 에서 **지역 중복** 을 선택합니다.

>[!Important]
>서버를 만든 후에는 [백업 중복 옵션](../../../mysql/concepts-backup.md#backup-redundancy-options)을 변경할 수 없습니다.

>[!Note]
>프라이빗 링크 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에만 사용할 수 있습니다. 데이터베이스 서버가 이러한 가격 책정 계층 중 하나에 해당되는지 확인하세요.

### <a name="configure-azure-private-link"></a>Azure Private Link 구성

이 섹션에서는 SAP BOBI 가상 머신이 프라이빗 엔드포인트를 통해 Azure Database for MySQL 서비스에 연결할 수 있게 해 주는 프라이빗 링크를 만듭니다. Azure Private Link는 개인 프라이빗 가상 네트워크 내에 Azure 서비스를 제공합니다.

1. 이전 섹션에서 만든 데이터베이스를 선택합니다.
2. **보안** > **프라이빗 엔드포인트 연결** 로 이동합니다.
3. **프라이빗 엔드포인트 연결** 에서 **프라이빗 엔드포인트** 를 선택합니다.
4. **구독** > **리소스 그룹** > **위치** 를 선택합니다.
5. 프라이빗 엔드포인트의 **이름** 을 입력합니다.
6. **리소스** 섹션에서 다음을 지정합니다.
   - 리소스 종류: Microsoft.DBforMySQL/servers
   - 리소스: 위의 섹션에서 만든 MySQL 데이터베이스
   - 대상 하위 리소스: mysqlServer
7. **네트워킹** 섹션에서 SAP BOBI 애플리케이션이 배포된 **가상 네트워크** 및 **서브넷** 을 선택합니다.
   >[!NOTE]
   >서브넷에서 NSG(네트워크 보안 그룹)를 사용하도록 설정한 경우, 이 서브넷의 프라이빗 엔드포인트에 한해 NSG가 사용되지 않습니다. 서브넷의 다른 리소스에는 계속 NSG가 적용됩니다.
8. **프라이빗 DNS 영역과의 통합** 에는 **기본값(예)** 을 적용합니다.
9.  드롭다운 목록에서 **프라이빗 DNS 영역** 을 선택합니다.
10. **검토+만들기** 를 선택하고 프라이빗 엔드포인트를 만듭니다.

자세한 내용은 [Azure Database for MySQL용 프라이빗 링크](../../../mysql/concepts-data-access-security-private-link.md)를 참조하세요.

### <a name="create-the-cms-and-audit-databases"></a>CMS 및 감사 데이터베이스 만들기

1. [MySQL 웹 사이트](https://dev.mysql.com/downloads/workbench/)에서 MySQL Workbench를 다운로드하여 설치합니다. Azure Database for MySQL에 액세스할 수 있는 서버에 MySQL Workbench를 설치해야 합니다.

2. MySQL Workbench를 사용하여 서버에 연결합니다. [연결 정보 가져오기](../../../mysql/connect-workbench.md#get-connection-information)의 지침을 따릅니다. 연결 테스트에 성공하면 다음 메시지가 표시됩니다.

   ![MySQL Workbench의 메시지 스크린샷.](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. SQL 쿼리 탭에서 다음 쿼리를 실행하여 CMS 및 감사 데이터베이스에 대한 스키마를 만듭니다.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```

4. 스키마에 연결할 사용자 계정을 만듭니다.

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. MySQL 사용자 계정의 권한과 역할을 확인하려면 다음을 수행합니다.

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-on-a-linux-server"></a>Linux 서버에 MySQL C API 커넥터 설치

SAP BOBI 애플리케이션 서버에서 데이터베이스에 액세스하려면 데이터베이스 클라이언트 드라이버가 필요합니다. CMS 및 감사 데이터베이스에 액세스하려면 Linux용 MySQL C API 커넥터를 사용해야 합니다. CMS 데이터베이스에 ODBC 연결은 지원되지 않습니다. 이 섹션에서는 Linux에서 MySQL C API 커넥터를 설정하는 방법에 대한 지침을 제공합니다.

1. [Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구](../../../mysql/concepts-compatibility.md)를 참조하세요. 이 문서에서 **MySQL 커넥터/C(libmysqlclient)** 드라이버를 확인합니다.

2. 드라이버를 다운로드하려면 [MySQL 제품 보관](https://downloads.mysql.com/archives/c-c/)을 참조하세요.

3. 운영 체제를 선택하고 MySQL 커넥터의 공유 구성 요소 rpm 패키지를 다운로드합니다. 이 예제에서는 mysql-connector-c-shared-6.1.11 버전을 사용합니다.

4. 모든 SAP BOBI 애플리케이션 인스턴스에 커넥터를 설치합니다.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. libmysqlclient.so의 경로를 확인합니다.

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. 설치에 사용할 사용자 계정의 `/usr/lib64` 디렉터리를 가리키도록 `LD_LIBRARY_PATH`를 설정합니다.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>서버 준비

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

**[A]** : 이 단계는 모든 호스트에 적용됩니다.

1. **[A]** Linux 버전에 따라(SLES 또는 RHEL) 커널 매개 변수를 설정하고 필요한 라이브러리를 설치해야 합니다. [Unix용 비즈니스 인텔리전스 플랫폼 설치 가이드](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3)의 ‘시스템 요구 사항’ 섹션을 참조하세요.

2. **[A]** 머신의 표준 시간대가 올바르게 설정되었는지 확인합니다. 설치 가이드에서 [추가 Unix 및 Linux 요구 사항](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/46b143336e041014910aba7db0e91070.html)을 참조하세요.

3. **[A]** 소프트웨어의 백그라운드 프로세스를 실행할 수 있는 사용자 계정(**bl1** adm) 및 그룹(sapsys)을 만듭니다. 이 계정을 사용하여 설치 작업 및 소프트웨어를 실행합니다. 이 계정에는 루트 권한이 필요하지 않습니다.

4. **[A]** 지원되는 UTF-8 로캘을 사용하도록 사용자 계정(**bl1** adm) 환경을 설정하고, 콘솔 소프트웨어가 UTF-8 문자 세트를 지원하는지 확인합니다. 운영 체제에서 올바른 로캘을 사용하도록 `LC_ALL` 및 `LANG` 환경 변수를 해당(**bl1** adm) 사용자 환경의 기본 로캘로 설정합니다.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** 사용자 계정(**bl1** adm)을 구성합니다.

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. SAP Service Marketplace에서 SAP BusinessObjects BI 플랫폼용 미디어를 다운로드하고 압축을 풉니다.

## <a name="installation"></a>설치

서버의 **bl1** adm 사용자 계정에 대한 로캘을 확인합니다.

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

SAP BOBI 플랫폼의 미디어로 이동하고 **bl1** adm 사용자로 다음 명령을 실행합니다.

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

사용 중인 버전에 해당하는 Unix용 [SAP BOBI 플랫폼](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) 설치 가이드의 지침을 따릅니다. 다음은 SAP BOBI 플랫폼을 설치하는 동안 몇 가지 주의해야 할 사항입니다.

- **제품 등록 구성** 화면에서 SAP Note [1288121](https://launchpad.support.sap.com/#/notes/1288121)의 SAP BusinessObjects 솔루션용 임시 라이선스 키를 사용할 수도 있고, SAP Service Marketplace에서 라이선스 키를 생성할 수도 있습니다.

- **설치 유형 선택** 의 첫 번째 서버(`azusbosl1`)에서 **전체** 설치를 선택합니다. 기타 서버(`azusbosl2`)에서는 기존 BOBI 설정을 확장하는 **사용자 지정/확장** 을 선택합니다.

- **기본값 또는 기존 데이터베이스 선택** 에서 **기존 데이터베이스 구성** 을 선택합니다. 그러면 CMS 및 감사 데이터베이스를 선택하라는 메시지가 표시됩니다. 이러한 데이터베이스 형식으로 **MySQL** 을 선택합니다.

  설치 중에 감사를 구성하지 않으려면 **감사 데이터베이스 없음** 을 선택해도 됩니다.

- **Java 웹 애플리케이션 서버 선택 화면** 에서 SAP BOBI 아키텍처에 따라 적절한 옵션을 선택합니다. 이 예제에서는 동일한 SAP BOBI 플랫폼에 tomcat 서버를 설치하는 옵션 1을 선택했습니다.

- **CMS 리포지토리 데이터베이스 구성 - MySQL** 에서 CMS 데이터베이스 정보를 입력합니다. 다음 예제에는 Linux를 설치하기 위한 CMS 데이터베이스 정보의 입력 사항이 나와 있습니다. Azure Database for MySQL은 기본 포트 3306에서 사용됩니다.
  
  ![Linux의 SAP BOBI 배포 - CMS 데이터베이스를 보여 주는 스크린샷.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (선택 사항) **감사 리포지토리 데이터베이스 구성 - MySQL** 에서 감사 데이터베이스 정보를 입력합니다. 다음 예제에는 Linux를 설치하기 위한 감사 데이터베이스 정보의 입력 사항이 나와 있습니다.

  ![Linux에서 SAP BOBI 배포 - 감사 데이터베이스를 보여 주는 스크린샷.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 지침을 따르고 설치를 완료하는 데 필요한 정보를 입력합니다.

다중 인스턴스 배포의 경우 두 번째 호스트(`azusbosl2`)에서 설치 설정을 실행합니다. **설치 유형 선택** 에서 기존 BOBI 설정을 확장하는 **사용자 지정/확장** 을 선택합니다.

Azure Database for MySQL의 게이트웨이에서 서버 인스턴스로 연결을 리디렉션합니다. 연결이 설정되면 MySQL 클라이언트는 MySQL Server 인스턴스에서 실행 중인 실제 버전이 아닌 게이트웨이에서 설정된 MySQL 버전을 표시합니다. MySQL Server 인스턴스의 버전을 확인하려면 MySQL 프롬프트에서 `SELECT VERSION();` 명령을 사용합니다. 자세한 내용은 [지원되는 Azure Database for MySQL 서버 버전](../../../mysql/concepts-supported-versions.md)을 참조하세요.

![Linux에서 SAP BOBI 배포 - CMC 설정을 보여 주는 스크린샷.](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>설치 후

SAP BOBI 플랫폼의 다중 인스턴스를 설치한 후 추가 사후 구성 단계를 수행하여 애플리케이션 고가용성을 지원하도록 해야 합니다.

### <a name="configure-the-cluster-name"></a>클러스터 이름 구성

SAP BOBI 플랫폼의 다중 인스턴스 배포에서는 하나의 클러스터에서 여러 CMS 서버를 함께 실행하려고 합니다. 클러스터는 공통 CMS 시스템 데이터베이스에 대해 함께 작동하는 둘 이상의 CMS 서버로 구성됩니다. CMS에서 실행되는 노드가 실패하면 다른 CMS가 있는 노드에서 BI 플랫폼 요청을 계속 처리합니다. 기본적으로 SAP BOBI 플랫폼에서 클러스터 이름은 설치한 첫 번째 CMS의 호스트 이름을 반영합니다.

Linux에서 클러스터 이름을 구성하려면 [SAP Business Intelligence 플랫폼 관리자 가이드](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3)의 지침을 따르세요. 클러스터 이름을 구성한 후 SAP Note [1660440](https://launchpad.support.sap.com/#/notes/1660440)에 따라 CMC 또는 BI 실행 패드 로그인 페이지에서 기본 시스템 항목을 설정합니다.

### <a name="configure-input-and-output-filestore-location-to-azure-netapp-files"></a>Azure NetApp Files에 입력/출력 파일 저장소 위치 구성

파일 저장소는 실제 SAP BusinessObjects 파일이 있는 디스크 디렉터리를 나타냅니다. SAP BOBI 플랫폼용 파일 리포지토리 서버의 기본 위치는 로컬 설치 디렉터리에 있습니다. 다중 인스턴스 배포에서는 Azure NetApp Files 같은 공유 스토리지에 파일 저장소를 설정하는 것이 중요합니다. 이렇게 하면 모든 스토리지 계층 서버에서 파일 저장소에 액세스할 수 있습니다.

1. NFS 볼륨을 아직 만들지 않은 경우 Azure NetApp Files에 만듭니다. 이전 섹션 ‘Azure NetApp Files 프로비전’의 지침을 따릅니다.

2. NFS 볼륨을 탑재합니다. 이전 섹션 ‘Azure NetApp Files 볼륨 탑재’의 지침을 따릅니다.

3. SAP Note [2512660](https://launchpad.support.sap.com/#/notes/0002512660)에 따라 파일 리포지토리의 경로(입력/출력)를 변경합니다.

### <a name="session-replication-in-tomcat-clustering"></a>Tomcat 클러스터링의 세션 복제

Tomcat은 세션 복제 및 장애 조치(failover)를 위해 둘 이상의 애플리케이션 서버를 클러스터링하는 기능을 지원합니다. SAP BOBI 플랫폼 세션이 직렬화되었으므로, 애플리케이션 서버에서 오류가 발생하더라도 사용자 세션이 Tomcat의 다른 인스턴스로 원활하게 장애 조치(failover)될 수 있습니다.

예로, 사용자가 SAP BI 애플리케이션에서 폴더 계층을 탐색하는 동안 실패하는 웹 서버에 연결된 경우를 들 수 있습니다. 올바르게 구성된 클러스터를 통해 사용자가 로그인 페이지로 리디렉션되지 않고 폴더 계층 구조를 계속 탐색할 수 있습니다.

SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)에는 멀티캐스트를 사용하여 Tomcat 클러스터링을 구성하는 단계를 참조하세요. 그러나 Azure는 멀티캐스트를 지원하지 않습니다. 따라서 Tomcat 클러스터가 Azure에서 작동하려면 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership)(SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907))를 사용해야 합니다. 자세한 내용은 블로그 게시물, [SAP BusinessObjects BI 플랫폼에 정적 멤버 자격을 사용하는 Tomcat 클러스터링](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)을 참조하세요.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 플랫폼의 웹 계층 부하 분산

SAP BOBI 다중 인스턴스 배포에서는 Java 웹 애플리케이션 서버(웹 계층)가 두 개 이상의 호스트에서 실행됩니다. 사용자 부하를 웹 서버 간에 균등하게 분산하려면 최종 사용자와 웹 서버 간에 부하 분산 장치를 사용하면 됩니다. Azure에서 Azure Load Balancer 또는 Azure Application Gateway를 사용하여 웹 애플리케이션 서버에 대한 트래픽을 관리할 수 있습니다. 각 제품에 대한 세부 정보는 다음 섹션에 설명되어 있습니다.

#### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)는 대기 시간이 짧은 고성능 계층 4(TCP, UDP) 부하 분산 장치입니다. 이는 정상 VM(가상 머신) 간에 트래픽을 분산합니다. 부하 분산 장치 상태 프로브가 각 VM에 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다. 인터넷에서 SAP BI 플랫폼에 액세스할 수 있도록 할 것인지 여부에 따라 공용 부하 분산 장치 또는 내부 부하 분산 장치 중에서 선택할 수 있습니다. 이는 영역이 중복되며, 가용성 영역에서 고가용성을 보장합니다.

다음 다이어그램에서 내부 Load Balancer 섹션을 참조하세요. 웹 애플리케이션 서버는 기본 Tomcat HTTP 포트인 8080 포트에서 실행되고, 상태 프로브에서 모니터링됩니다. 최종 사용자로부터 들어오는 모든 요청이 웹 애플리케이션 서버(`azusbosl1` 또는 `azusbosl2`)로 리디렉션됩니다. Load Balancer는 TLS/SSL 종료(TLS/SSL 오프로딩이라고도 함)를 지원하지 않습니다. Load Balancer를 사용하여 웹 서버 간에 트래픽을 분산하는 경우 표준 Load Balancer를 사용합니다.

> [!NOTE]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Load Balancer의 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하도록 추가 구성하지 않는 한 아웃바운드 인터넷에 연결되지 않습니다. 자세한 내용은 [SAP 고가용성 시나리오에서 Azure Standard Load Balancer를 사용하는 가상 머신에 대한 퍼블릭 엔드포인트 연결](high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.

![웹 서버 간에 트래픽을 분산하는 Azure Load Balancer 보여 주는 다이어그램.](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway"></a>Azure Application Gateway

[Azure Application Gateway](../../../application-gateway/overview.md)는 ADC(애플리케이션 제공 컨트롤러)를 서비스로 제공합니다. 이 서비스는 애플리케이션에서 하나 이상의 웹 애플리케이션 서버로 사용자 트래픽을 전달하는 데 사용됩니다. 여기에서는 TLS/SSL 오프로딩, WAF(웹 애플리케이션 방화벽), 쿠키 기반 세션 선호도 같은 다양한 계층 7 부하 분산 기능을 제공합니다.

SAP BI 플랫폼에서 Application Gateway는 애플리케이션 웹 트래픽을 지정된 리소스, `azusbosl1` 또는 `azusbos2`로 전달합니다. 사용자는 수신기를 포트에 할당하고, 규칙을 만들며, 풀에 리소스를 추가합니다. 다음 다이어그램의 Application Gateway에는 사용자의 진입점 역할을 하는 개인 IP 주소(10.31.3.20)가 있습니다. 이는 들어오는 TLS/SSL(HTTPS - TCP/443) 연결을 처리하고, TLS/SSL의 암호를 해독하며, 암호화되지 않은 요청(HTTP - TCP/8080)을 서버에 전달합니다. 또한 Application Gateway에서 TLS/SSL 인증서를 하나만 유지 관리하는 작업을 간소화합니다.

![웹 서버 간에 트래픽을 분산하는 Application Gateway를 보여 주는 다이어그램.](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

SAP BOBI 웹 서버에 대한 Application Gateway를 구성하려면 블로그 게시물, [Azure Application Gateway를 사용하여 SAP BOBI 웹 서버 부하 분산](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)을 참조하세요.

> [!NOTE]
> 웹 서버에 트래픽의 부하를 분산하려면 Azure Application Gateway를 사용하는 것이 좋습니다. 여기에서는 SSL 오프로딩, 서버의 암호화 및 암호 해독 오버헤드를 줄이기 위한 중앙 집중식 SSL 관리, 트래픽을 분산하는 라운드 로빈 알고리즘, WAF 기능 및 고가용성 같은 유용한 기능을 제공합니다.

## <a name="sap-bobi-platform-reliability-on-azure"></a>Azure의 SAP BOBI 플랫폼 안정성

SAP BOBI 플랫폼에는 특정 작업 및 연산에 최적화된 다양한 계층이 있습니다. 한 계층의 구성 요소를 사용할 수 없게 되면 SAP BOBI 애플리케이션에 액세스할 수 없거나 해당 기능이 제한됩니다. 비즈니스 중단 없이 애플리케이션이 계속 작동하도록 각 계층을 안정적으로 설계해야 합니다.

이 가이드에서는 SAP BOBI 플랫폼 구성과 함께 Azure의 기본 기능이 SAP 배포의 가용성을 개선하는 방법을 살펴봅니다. 이 섹션에서는 다음 옵션을 중점적으로 다룹니다.

- **백업 및 복원**: 별도의 위치에 데이터와 애플리케이션의 복사본을 주기적으로 만드는 프로세스입니다. 원래 데이터 또는 애플리케이션이 손실되거나 손상된 경우 이전 상태로 복원 또는 복구할 수 있습니다.

- **고가용성:** 서버 중 하나를 사용할 수 없게 되어도 애플리케이션이 계속 작동하도록 고가용성 플랫폼은 Azure 지역 내에 두 개 이상의 서버를 운영합니다.
- **재해 복구:** 자연 재해 때문에 전체 Azure 지역이 사용할 수 없게 되는 경우 등 치명적인 손실이 발생하면 애플리케이션 기능을 복원하는 프로세스입니다.

이 솔루션의 구현 방법은 Azure에서 설정하는 시스템 특성에 따라 다릅니다. 비즈니스 요구 사항에 따라 백업/복원, 고가용성 및 재해 복구 솔루션을 조정합니다.

## <a name="backup-and-restore"></a>백업 및 복원

백업 및 복원은 모든 비즈니스 재해 복구 전략의 필수 구성 요소입니다. SAP BOBI 플랫폼에 포괄적인 전략을 개발하려면 시스템 가동 중지 시간 또는 애플리케이션 중단을 야기하는 구성 요소를 확인합니다. 애플리케이션을 보호하려면 SAP BOBI 플랫폼에서 다음 구성 요소를 백업해야 합니다.

- SAP BOBI 설치 디렉터리(관리형 프리미엄 디스크)
- 파일 리포지토리 서버(Azure NetApp Files 또는 Azure Premium Files)
- CMS 데이터베이스(Azure Database for MySQL 또는 Azure Virtual Machines의 데이터베이스)

다음 섹션에서는 이러한 각 구성 요소에 대한 백업 및 복원 전략을 구현하는 방법을 설명합니다.

### <a name="backup-and-restore-for-sap-bobi-installation-directory"></a>SAP BOBI 설치 디렉터리의 백업 및 복원

Azure에서 애플리케이션 서버 및 연결된 모든 디스크를 백업하는 가장 간단한 방법은 [Azure Backup](../../../backup/backup-overview.md)을 사용하는 것입니다. 이 서비스는 VM에서 의도하지 않은 데이터 소멸을 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 기본 제공되는 복구 지점 관리 기능을 통해 복구 지점 자격 증명 모음에 저장됩니다. 구성 및 스케일링은 간단하고, 백업은 최적화되며, 필요한 경우 쉽게 복원할 수 있습니다.

백업 프로세스 중에 프로덕션 워크로드에 영향을 주지 않고 스냅샷이 생성되고 데이터가 자격 증명 모음으로 전송됩니다. 자세한 내용은 [스냅샷 일관성](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)을 참조하세요. 선택적 디스크 백업 및 복원 기능을 사용하여 VM에서 데이터 디스크의 하위 집합을 백업하도록 선택할 수도 있습니다. 자세한 내용은 [Azure VM 백업](../../../backup/backup-azure-vms-introduction.md) 및 [FAQ - Azure VM 백업](../../../backup/backup-azure-vm-backup-faq.yml)을 참조하세요.

### <a name="backup-and-restore-for-file-repository-server"></a>파일 리포지토리 서버의 백업 및 복원

Linux의 SAP BOBI 배포에 따라 Azure NetApp Files를 SAP BOBI 플랫폼의 파일 저장소로 사용할 수 있습니다. 파일 저장소에서 사용하는 스토리지에 따라 다음의 백업 및 복원 옵션 중에서 선택합니다.

- **Azure NetApp Files**: 스냅샷 정책을 사용하여 주문형 스냅샷을 만들고 자동 스냅샷을 예약할 수 있습니다. 스냅샷 복사는 볼륨의 특정 시점 복사본을 제공합니다. 자세한 내용은 [Azure NetApp Files를 사용하여 스냅샷 관리](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)를 참조하세요.

- 별도의 NFS 서버를 만든 경우 동일한 서버에 대해 백업 및 복원 전략을 구현해야 합니다.

### <a name="backup-and-restore-for-cms-and-audit-databases"></a>CMS 및 감사 데이터베이스의 백업 및 복원

Linux VM의 CMS 및 감사 데이터베이스는 지원되는 모든 데이터베이스에서 실행할 수 있습니다. 자세한 내용은 [지원 매트릭스](businessobjects-deployment-guide.md#support-matrix)를 참조하세요. CMS 및 감사 데이터 저장소에 사용되는 데이터베이스에 따라 백업 및 복원 전략을 채택하는 것이 중요합니다.

- Azure Database for MySQL은 자동으로 서버 백업을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. Azure Database for MySQL은 데이터 파일과 트랜잭션 로그를 백업합니다. 지원되는 최대 스토리지 크기에 따라 전체/차등 백업(최대 4TB 스토리지 서버) 또는 스냅샷 백업(최대 16TB 스토리지 서버)을 수행합니다. 백업을 사용하면 서버를 구성된 백업 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 기본 백업 보존 기간은 7일이며 [필요에 따라 3일까지 구성](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다. 이러한 백업 파일은 사용자에게 노출되지 않으며 내보낼 수 없습니다. 이러한 백업은 Azure Database for MySQL의 복원 작업에만 사용할 수 있습니다. [mysqldump](../../../mysql/concepts-migrate-dump-restore.md)를 사용하여 데이터베이스를 복사할 수 있습니다. 자세한 내용은 [Azure Database for MySQL의 백업 및 복원](../../../mysql/concepts-backup.md)을 참조하세요.

- Azure 가상 컴퓨터에 설치된 데이터베이스의 경우 지원되는 데이터베이스에 대한 표준 백업 도구 또는 [Azure Backup](../../../backup/sap-hana-db-about.md)을 사용할 수 있습니다. 또한 모든 SAP BOBI 플랫폼 구성 요소를 위한 백업 및 복구 에이전트를 제공하도록 지원되는 타사 백업 도구를 사용할 수 있습니다.

## <a name="high-availability"></a>고가용성

*고가용성* 은 애플리케이션 및 서비스의 비즈니스 연속성을 제공하여 IT 중단을 최소화할 수 있는 일련의 기술을 일컫습니다. 이는 동일한 데이터 센터 내의 중복, 내결함성 또는 장애 조치(failover) 보호 구성 요소를 통해 얻게 됩니다. Microsoft의 경우에는 데이터 센터가 한 Azure 지역 내에 있습니다. 자세한 내용은 [SAP에 대한 고가용성 아키텍처 및 시나리오](sap-high-availability-architecture-scenarios.md)를 참조하세요.

SAP BOBI 플랫폼의 크기 조정 결과에 따라 환경을 디자인하고 Azure Virtual Machines 및 서브넷에 BI 구성 요소를 어떻게 분산할지 결정해야 합니다. 분산 아키텍처의 중복도 수준은 비즈니스에 필요한 RTO(복구 시간 목표) 및 RPO(복구 지점 목표)에 따라 달라집니다. SAP BOBI 플랫폼에는 다양한 계층이 있으며, 각 계층의 구성 요소는 중복도를 달성할 수 있도록 설계되어야 합니다. 예를 들면 다음과 같습니다.

- BI 애플리케이션 서버 및 웹 서버와 같은 중복 애플리케이션 서버
- CMS 데이터베이스, 파일 리포지토리 서버, Load Balancer 등의 고유한 구성 요소

다음 섹션에서는 SAP BOBI 플랫폼의 각 구성 요소에서 고가용성을 달성하는 방법을 설명합니다.

### <a name="high-availability-for-application-servers"></a>애플리케이션 서버의 고가용성

중복도를 적용하여 애플리케이션 서버의 고가용성을 달성할 수 있습니다. 이렇게 하려면 다양한 Azure VM에서 BI 및 웹 서버의 다양한 인스턴스를 구성합니다.

하나 이상의 이벤트로 인한 가동 중지 시간의 영향을 줄이려면 다음을 수행하는 것이 좋습니다.

- 가용성 영역을 사용하여 데이터 센터 오류 방지
- 중복도를 달성하도록 가용성 집합에서 다양한 가상 머신 구성
- 가용성 집합의 VM에 관리 디스크 사용
- 각 애플리케이션 계층을 별도의 가용성 집합으로 구성합니다.

자세한 내용은 [Linux 가상 머신의 가용성 관리](../../availability.md)를 참조하세요.

>[!Important]
>Azure 가용성 영역과 Azure 가용성 집합의 개념은 함께 사용할 수 없습니다. 한 쌍 또는 여러 VM을 특정 가용성 영역이나 가용성 집합에 배포할 수 있지만, 둘 다에 배포할 수는 없습니다.

### <a name="high-availability-for-a-cms-database"></a>CMS 데이터베이스의 고가용성

CMS 및 감사 데이터베이스에 Azure Database for MySQL을 사용하는 경우 기본적으로 로컬 중복 고가용성 프레임워크를 갖추고 있습니다. 추가 구성 요소를 구성할 필요 없이 지역을 선택하고 고유한 고가용성, 중복도 및 복원력 기능을 제공하기만 하면 됩니다. SAP BOBI 플랫폼의 배포 전략이 가용성 영역에 적용되면 CMS 및 감사 데이터베이스에 대한 영역 중복도를 달성해야 합니다. 자세한 내용은 [Azure Database for MySQL의 고가용성](../../../mysql/concepts-high-availability.md)과 [Azure SQL Database의 고가용성](../../../azure-sql/database/high-availability-sla.md)을 참조하세요.

기타 CMS 데이터베이스 배포는 [SAP 워크로드에 대한 DBMS 배포 가이드](dbms_guide_general.md)의 고가용성 정보를 참조하세요.

### <a name="high-availability-for-filestore"></a>파일 저장소의 고가용성

파일 저장소는 보고서, 유니버스, 연결 등의 콘텐츠가 저장되는 디스크 디렉터리를 나타내며 해당 시스템의 모든 애플리케이션 서버에서 공유됩니다. 따라서 다른 SAP BOBI 플랫폼 구성 요소와 함께 고가용성이 달성되는지 확인해야 합니다.

Linux에서 실행되는 SAP BOBI 플랫폼의 경우, 파일 공유 시 기본적으로 고가용성과 높은 내구성을 제공하도록 설계된 [Azure Premium Files](../../../storage/files/storage-files-introduction.md)나 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 선택할 수 있습니다. 자세한 내용은 Azure Files의 [중복도](../../../storage/files/storage-files-planning.md#redundancy)를 참조하세요.

> [!Important]
> Azure Files용 SMB 프로토콜은 일반 공급되지만 Azure Files용 NFS 프로토콜 지원은 현재 미리 보기로 제공됩니다. 자세한 내용은 [현재 미리 보기로 제공되는 Azure Files에 대한 NFS 4.1 지원](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)을 참조하세요.

이 파일 공유 서비스를 모든 지역에서 사용할 수는 없습니다. 최신 정보를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요. 현재 지역에서 이 서비스를 사용할 수 없는 경우 파일 시스템을 SAP BOBI 애플리케이션에 공유할 수 있는 NFS 서버를 만들면 됩니다. 하지만 고가용성도 고려해야 합니다.

### <a name="high-availability-for-load-balancer"></a>Load Balancer의 고가용성

웹 서버에서 트래픽을 분산하려면 Azure Load Balancer 또는 Azure Application Gateway를 사용하면 됩니다. 이러한 두 부하 분산 장치의 중복도는 배포 시 선택하는 SKU를 기반으로 달성할 수 있습니다.

- Azure Load Balancer의 경우 표준 Load Balancer를 영역 중복으로 구성하여 중복도를 달성할 수 있습니다. 자세한 내용은 [표준 Load Balancer 및 가용성 영역](../../../load-balancer/load-balancer-standard-availability-zones.md)을 참조하세요.

- Application Gateway의 경우 배포 중에 선택하는 계층 종류에 따라 고가용성을 달성할 수 있습니다.
   -  v1 SKU는 둘 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원합니다. 모든 인스턴스가 동시에 실패하는 일이 없도록, Azure는 이러한 인스턴스를 업데이트 및 장애 도메인에 배포합니다. 영역 내에서 중복도를 달성합니다.
   -  v2 SKU를 선택하면 새 인스턴스가 여러 장애 도메인과 업데이트 도메인에 자동으로 분산됩니다. 영역 중복을 선택하면 영역 장애 시의 복원력을 제공하기 위해 최신 인스턴스도 여러 가용성 영역에 분산됩니다. 자세한 내용은 [자동 스케일링 및 영역 중복 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

### <a name="reference-high-availability-architecture-for-sap-bobi-platform"></a>SAP BOBI 플랫폼의 고가용성 참조 아키텍처

다음 다이어그램에는 Linux 서버에서 실행되는 가용성 집합을 사용하는 경우의 SAP BOBI 플랫폼 설정이 나와 있습니다. 아키텍처는 Azure Application Gateway, Azure NetApp Files, Azure Database for MySQL 같이 다양한 Azure 서비스의 용도를 보여 줍니다. 이러한 서비스는 기본 제공 중복도를 제공하여 다양한 고가용성 솔루션 관리 업무의 복잡성을 줄여 줍니다.

들어오는 트래픽(HTTPS - TCP/443)은 둘 이상의 인스턴스에 배포하면 고가용성을 제공하는 Azure Application Gateway v1 SKU를 사용하여 부하를 분산합니다. 웹 서버, 관리 서버 및 처리 서버의 여러 인스턴스가 중복도를 달성하기 위해 별도의 가상 머신에 배포되며, 각 계층은 별도의 가용성 집합에 배포됩니다. Azure NetApp Files는 데이터 센터 내에서 기본 중복도를 제공하므로, 파일 리포지토리 서버용 Azure NetApp Files 볼륨의 고가용성이 제공됩니다. CMS 데이터베이스는 본질적으로 고가용성을 갖춘 Azure Database for MySQL에 프로비저닝됩니다. 자세한 내용은 [Azure Database for MySQL에 대한 고가용성](../../../mysql/concepts-high-availability.md)을 참조하세요.

![가용성 집합을 있는 SAP BusinessObjects BI 플랫폼의 중복도를 보여 주는 다이어그램.](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

앞의 아키텍처에는 Azure에서 SAP BOBI를 배포하는 방법에 대한 인사이트가 나와 있습니다. 하지만 가능한 모든 구성 옵션을 다루지는 않습니다. 비즈니스 요구 사항에 따라 배포를 조정할 수 있습니다.

여러 Azure 지역에서 가용성 영역을 사용할 수 있습니다. 즉 전원, 냉각 및 네트워크를 독자적으로 공급하여 활용할 수 있습니다. 따라서 고객이 두세 개의 가용성 영역에 애플리케이션을 배포할 수 있습니다. 가용성 영역에서 고가용성을 달성하려면 가용성 영역에 SAP BOBI 플랫폼을 배포하고 애플리케이션의 각 구성 요소에서 영역이 중복되도록 합니다.

## <a name="disaster-recovery"></a>재해 복구

이 섹션에서는 Linux에서 실행되는 SAP BOBI 플랫폼에 대한 재해 복구 보호 기능의 제공 전략을 설명합니다. 전체 SAP 재해 복구 접근 방식의 기본 리소스를 나타내는 [SAP의 재해 복구](../../../site-recovery/site-recovery-sap.md) 문서를 보완합니다. SAP BOBI의 경우 재해 복구 환경을 안전하게 구현하는 다음의 방법을 설명하는 SAP Note [2056228](https://launchpad.support.sap.com/#/notes/2056228)을 참조하세요.

- 수명 주기 관리 또는 페더레이션을 완전히 또는 선택적으로 사용하여 기본 시스템의 콘텐츠를 승격/배포합니다.
- 정기적으로 CMS 및 파일 리포지토리 서버 콘텐츠를 복사합니다.

이 가이드에서는 두 번째 옵션을 중점적으로 설명합니다. 재해 복구에 사용할 수 있는 모든 구성 옵션을 다루지는 않지만, SAP BOBI 플랫폼 구성과 함께 기본 Azure 서비스를 제공하는 솔루션을 설명합니다.

>[!Important]
>SAP BOBI 플랫폼에서 각 구성 요소의 가용성은 보조 지역에서 팩터링되어야 하며, 전체 재해 복구 전략을 철저히 테스트해야 합니다.

### <a name="reference-disaster-recovery-architecture-for-sap-bobi-platform"></a>SAP BOBI 플랫폼의 재해 복구 참조 아키텍처

이 참조 아키텍처는 애플리케이션 서버가 중복되는 SAP BOBI 플랫폼의 다중 인스턴스 배포를 실행합니다. 재해 복구 시 SAP BOBI 플랫폼의 모든 구성 요소를 보조 지역으로 장애 조치(failover)해야 합니다. 다음 다이어그램에서 Azure NetApp Files는 파일 저장소로, Azure Database for MySQL은 CMS 및 감사 리포지토리로, Azure Application Gateway는 부하 분산 장치로 사용됩니다. 구성 요소마다 재해 복구 보호를 달성하는 전략이 서로 다른데, 이러한 차이점은 다음 섹션에 설명되어 있습니다.

![SAP BusinessObjects BI 플랫폼 재해 복구를 보여 주는 다이어그램.](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

### <a name="load-balancer"></a>부하 분산 장치

부하 분산 장치는 SAP BOBI 플랫폼의 웹 애플리케이션 서버 간에 트래픽을 분산하는 데 사용됩니다. Azure에서는 이러한 목적으로 Azure Load Balancer 또는 Azure Application Gateway를 사용할 수 있습니다. 부하 분산 서비스에 대한 재해 복구를 달성하려면 보조 지역에서 다른 Azure Load Balancer 또는 Azure Application Gateway를 구현해야 합니다. 재해 복구 장애 조치(failover) 후 동일한 URL을 유지하려면 DNS에서 항목을 변경하여 보조 지역에서 실행되는 부하 분산 서비스를 가리켜야 합니다.

### <a name="vms-running-web-and-bi-application-servers"></a>웹 및 BI 애플리케이션 서버를 실행하는 VM

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용하여 웹 및 BI 애플리케이션 서버를 실행하는 VM을 보조 지역에 복제합니다. 보조 지역에 연결된 모든 관리 디스크와 서버를 복제하므로, 재해와 중단이 발생하는 경우 복제된 환경으로 쉽게 장애 조치(failover)하여 작업을 계속 진행할 수 있습니다. 모든 SAP 애플리케이션 VM을 Azure 재해 복구 데이터 센터로 복제하는 작업을 시작하려면 [Azure로 가상 머신 복제](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)의 지침을 따릅니다.

### <a name="file-repository-servers"></a>파일 리포지토리 서버

파일 저장소는 보고서, BI 문서와 같은 실제 파일이 저장되는 디스크 디렉터리입니다. 파일 저장소의 모든 파일이 재해 복구 지역에 동기화되는 것이 중요합니다. Linux에서 실행되는 SAP BOBI 플랫폼용으로 사용하는 파일 공유 서비스의 유형에 따라 콘텐츠를 동기화하려면 적절한 재해 복구 전략을 채택해야 합니다.

- **Azure NetApp Files** 는 NFS 및 SMB 볼륨을 제공하므로, 모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 다른 지역의 볼륨을 복사하는 방법에 대한 자세한 내용은 [Azure NetApp Files에 대한 FAQ](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)를 참조하세요.

  현재 [미리 보기](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)로 제공되는 Azure NetApp Files 지역 간 복제를 사용할 수 있습니다. 변경된 블록만이 압축된 효과적인 형태로 네트워크를 통해 전송됩니다. 이는 지역 간에 복제하는 데 필요한 데이터의 양을 최소화하여 데이터 전송 비용을 절약해 줍니다. 복제 시간이 단축되어 더 작은 RPO도 달성할 수 있습니다. 자세한 내용은 [지역 간 복제 사용에 대한 요구 사항 및 고려 사항](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)을 참조하세요.

- **Azure Premium Files** 는 LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지)만 지원합니다. 재해 복구 전략의 경우 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 또는 [Azure PowerShell](/powershell/module/az.storage/)을 사용하여 다른 지역의 다른 스토리지 계정에 파일을 복사할 수 있습니다. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치(failover)](../../../storage/common/storage-disaster-recovery-guidance.md)를 참조하세요.

   > [!Important]
   > Azure Files용 SMB 프로토콜은 일반 공급되지만 Azure Files용 NFS 프로토콜 지원은 현재 미리 보기로 제공됩니다. 자세한 내용은 [현재 미리 보기로 제공되는 Azure Files에 대한 NFS 4.1 지원](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)을 참조하세요.

### <a name="cms-database"></a>CMS 데이터베이스

재해 복구 지역의 CMS 및 감사 데이터베이스는 주 지역에서 실행되는 데이터베이스의 복사본이어야 합니다. 데이터베이스 유형에 따라 비즈니스에 필요한 RTO 및 RPO를 기준으로 재해 복구 지역에 데이터베이스를 복사하는 것이 중요합니다.

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL는 재해 발생 시 데이터베이스를 복구하는 옵션을 다양하게 제공합니다. 비즈니스에 사용하기에 적합한 옵션을 선택하세요.

- 지역 간 읽기 복제본을 사용하여 비즈니스 연속성 및 재해 복구 계획을 향상합니다. 원본 서버에서 최대 5개의 복제본으로 복제할 수 있습니다. 읽기 복제본은 MySQL의 이진 로그 복제 기술을 사용하여 비동기적으로 업데이트됩니다. 복제본은 Azure Database for MySQL의 일반 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 자세한 내용은 [Azure Database for MySQL의 읽기 복제본](../../../mysql/concepts-read-replicas.md)을 참조하세요.

- 지역 복원 기능 사용 시 지역 중복 백업을 이용하여 서버를 복원합니다. 이러한 백업은 서버를 호스트하는 지역이 오프라인인 경우에도 액세스가 가능합니다. 이러한 백업에서 다른 지역으로 복원하여 서버를 다시 온라인 상태로 만들 수 있습니다.

  > [!NOTE]
  > 지역 복원은 지역 중복 백업 스토리지로 서버를 프로비전한 경우에만 가능합니다. 서버를 만든 후에는 **백업 중복 옵션** 을 변경할 수 없습니다. 자세한 내용은 [백업 중복성](../../../mysql/concepts-backup.md#backup-redundancy-options)을 참조하세요.

다음 표에는 이 예제에 사용된 각 계층의 재해 복구에 대한 권장 사항이 나와 있습니다.

| SAP BOBI 플랫폼 계층   | 권장                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | 보조 지역에서 Application Gateway 병렬 설정                                             |
| 웹 애플리케이션 서버   | Azure Site Recovery를 사용하여 복제                                                                  |
| BI 애플리케이션 서버    | Site Recovery를 사용하여 복제                                                                        |
| Azure NetApp Files        | 보조 지역으로 또는 지역 간 복제를 사용하여 데이터를 복제하는 파일 기반 복사 도구      |
| Azure Database for MySQL  | 지역 간 읽기 복제본 또는 지역 중복 백업에서 백업 복원                                |

## <a name="next-steps"></a>다음 단계

- [다중 계층 SAP 앱 배포를 위한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
- [SAP용 Azure Virtual Machines DBMS 배포](./dbms_guide_general.md)
