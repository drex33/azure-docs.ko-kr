---
title: CentOS 기반 Linux VHD 생성 및 업로드
description: CentOS 기반 Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: c447f84e4e35997106341ceafb41e7c2244f7d05
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724001"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Azure용 CentOS 기반 가상 머신 준비

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

CentOS 기반 Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.

* [Azure용 CentOS 6.x 가상 머신 준비](#centos-6x)
* [Azure용 CentOS 7.0 이상 가상 머신 준비](#centos-70)


## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 가상 하드 디스크에 CentOS 또는 그와 비슷한 파생 Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 자세한 내용은 [Hyper-V 역할 설치 및 Virtual Machine 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))을 참조하십시오.

**CentOS 설치 참고 사항**

* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes) 을 참조하세요.
* VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD** 만 지원됩니다.  Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. VirtualBox를 사용하면 디스크를 만들 때 기본적으로 동적 할당되지 않고 **고정 크기** 가 선택됩니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 *좋습니다*. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 데이터 디스크에서 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 또는 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)를 사용할 수 있습니다.
* **UDF 파일 시스템 탑재에 대한 커널 지원이 필요합니다.** Azure에서 처음 부팅 시 프로비저닝 구성이 게스트에 연결된 UDF 형식의 미디어를 통해 Linux VM에 전달됩니다. Azure Linux 에이전트는 해당 구성을 읽고 VM을 프로비전하기 위해 UDF 파일 시스템을 탑재할 수 있어야 합니다.
* 2.6.37 버전 미만의 Linux 커널은 VM 크기가 더 클 때 Hyper-V에서 NUMA를 지원하지 않습니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하는 이전 배포에 영향을 주며 RHEL 6.6(kernel-2.6.32-504)에서는 해결되었습니다. 2.6.37보다 오래된 사용자 지정 커널 또는 2.6.32-504보다 오래된 RHEL 기반 커널을 실행하는 시스템의 경우 grub.conf의 커널 명령줄에 부트 매개 변수 `numa=off` 자세한 내용은 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)을 참조하세요.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

## <a name="centos-6x"></a>CentOS 6.x

1. Hyper-V 관리자에서 가상 머신을 선택합니다.

2. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.

3. CentOS 6에서 NetworkManager는 Azure Linux 에이전트 작동을 방해할 수 있습니다. 다음 명령을 실행하여 이 패키지를 제거합니다.

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 수정합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킬 수 있습니다.

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

    ```bash
    sudo chkconfig network on
    ```

8. Azure 데이터 센터 내에서 호스트되는 OpenLogic 미러를 사용하려면 `/etc/yum.repos.d/CentOS-Base.repo` 파일을 다음 리포지토리로 바꿉니다.  그러면 Azure Linux 에이전트와 같은 추가 패키지가 포함된 **[openlogic]** 리포지토리도 추가됩니다.

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > 이 가이드의 나머지 부분에서는 최소한 `[openlogic]` 리포지토리를 사용한다고 가정합니다. 아래 단계에서는 이 리포지토리를 사용하여 Azure Linux 에이전트를 설치합니다.

9. /etc/yum.conf에 다음 줄을 추가합니다.

    ```console
    http_caching=packages
    ```

10. 다음 명령을 실행하여 현재 yum 메타데이터를 지우고 최신 패키지로 시스템을 업데이트합니다.

    ```bash
    yum clean all
    ```

    이전 버전의 CentOS에 대한 이미지를 만드는 경우가 아니면 모든 패키지를 최신으로 업데이트하는 것이 좋습니다.

    ```bash
    sudo yum -y update
    ```

    이 명령을 실행한 후 다시 부팅해야 할 수 있습니다.

11. (선택 사항) LIS(Linux 통합 서비스)용 드라이버를 설치합니다.

    > [!IMPORTANT]
    > 이 단계는 CentOS 6.3 및 이전 버전의 경우는 **필수** 이고 최신 릴리스의 경우는 선택 사항입니다.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    또는 [LIS 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=55106)의 수동 설치 지침에 따르고 VM에 RPM을 설치할 수 있습니다.

12. Azure Linux 에이전트 및 종속성을 설치합니다. Waagent 서비스를 시작하고 사용하도록 설정합니다.

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    WALinuxAgent 패키지는 3단계에서 설명한 대로 NetworkManager 및 NetworkManager-gnome 패키지가 아직 제거되지 않은 경우 이러한 패키지를 제거합니다.

13. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 `/boot/grub/menu.lst` 를 텍스트 편집기에서 열고 기본 커널이 다음 매개 변수를 포함하는지 확인합니다.

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다.

    위의 작업을 수행하는 동시에 다음 매개 변수도 *제거* 하는 것이 좋습니다.

    ```console
    rhgb quiet crashkernel=auto
    ```

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.  원하는 경우에는 `crashkernel` 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

    > [!Important]
    > 또한 CentOS 6.5 및 이전 버전에서는 커널 매개 변수 `numa=off`를 설정해야 합니다. Red Hat [KB 436883](https://access.redhat.com/solutions/436883)을 참조하세요.

14. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

15. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) `/etc/waagent.conf` 에서 다음 매개 변수를 적절하게 수정합니다.

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Hyper-V 관리자에서 **작업 -> 종료** 를 클릭합니다. 이제 Linux VHD를 [Azure에 업로드할](./upload-vhd.md#option-1-upload-a-vhd)준비가 되었습니다.


## <a name="centos-70"></a>CentOS 7.0 이상

**CentOS 7 및 유사한 파생 버전의 변경 내용**

Azure용으로 CentOS 7 가상 컴퓨터를 준비하는 작업은 CentOS 6과 매우 비슷하지만 다음과 같은 몇 가지 중요한 차이점이 있습니다.

* NetworkManager 패키지가 더 이상 Azure Linux 에이전트와 충돌하지 않습니다. 이 패키지는 기본적으로 설치되며 제거하지 않는 것이 좋습니다.
* 이제 GRUB2가 기본 부팅 로더로 사용되므로 커널 매개 변수를 편집하는 절차가 변경되었습니다(아래 참조).
* 이제 XFS가 기본 파일 시스템입니다. 원하는 경우에는 ext4 파일 시스템도 계속 사용할 수 있습니다.

**구성 단계**

1. Hyper-V 관리자에서 가상 머신을 선택합니다.

2. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.

3. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 수정합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킬 수 있습니다.

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Azure 데이터 센터 내에서 호스트되는 OpenLogic 미러를 사용하려면 `/etc/yum.repos.d/CentOS-Base.repo` 파일을 다음 리포지토리로 바꿉니다.  그러면 Azure Linux 에이전트의 패키지가 포함된 **[openlogic]** 리포지토리도 추가됩니다.

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > 이 가이드의 나머지 부분에서는 최소한 `[openlogic]` 리포지토리를 사용한다고 가정합니다. 아래 단계에서는 이 리포지토리를 사용하여 Azure Linux 에이전트를 설치합니다.

7. 다음 명령을 실행하여 현재 yum 메타데이터를 지우고 모든 업데이트를 설치합니다.

    ```bash
    sudo yum clean all
    ```

    이전 버전의 CentOS에 대한 이미지를 만드는 경우가 아니면 모든 패키지를 최신으로 업데이트하는 것이 좋습니다.

    ```bash
    sudo yum -y update
    ```

    이 명령을 실행한 후 다시 부팅해야 할 수 있습니다.

8. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 `/etc/default/grub`를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. NIC에 대한 새 CentOS 7 명명 규칙도 해제합니다. 위의 작업을 수행하는 동시에 다음 매개 변수도 *제거* 하는 것이 좋습니다.

    ```console
    rhgb quiet crashkernel=auto
    ```

    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원하는 경우에는 `crashkernel` 옵션을 구성한 상태로 유지할 수도 있지만 이 매개 변수를 사용하는 경우 VM에서 사용 가능한 메모리의 양이 128MB 이상 감소하므로 VM 크기가 작은 경우 문제가 될 수 있습니다.

9. 위의 설명에 따라 `/etc/default/grub` 편집을 완료한 후에는 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. **VMware VirtualBox 또는 KVM** 에서 이미지를 빌드하는 경우 Hyper-V 드라이버가 initramfs에 포함되어 있는지 확인합니다.

    `/etc/dracut.conf`를 편집하고 콘텐츠를 추가합니다.

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    initramfs를 다시 빌드합니다.

    ```bash
    sudo dracut -f -v
    ```

11. Azure VM 확장에 대한 Azure Linux 에이전트 및 종속성을 설치합니다.

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. 프로비저닝을 처리하기 위해 cloud-init 설치

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. 스왑 구성은 운영 체제 디스크에 스왑 공간을 만들지 않습니다.

    이전에는 Azure Linux 에이전트가 Azure에서 가상 머신을 프로비저닝한 후에 가상 머신에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있었습니다. 그러나 이제 해당 작업은 cloud-init에서 처리되므로, Linux 에이전트를 사용하여 리소스 디스크의 형식을 지정해 스왑 파일을 생성해서는 **안 되며**, `/etc/waagent.conf`에서 다음 매개 변수를 알맞게 수정합니다.

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    스왑을 생성, 탑재, 형식 지정하고자 하는 경우 다음 방법 중 하나를 수행합니다.
    * VM을 만들 때마다 cloud-init 구성으로 해당 작업을 전달합니다.
    * VM이 생성될 때마다 해당 작업을 수행할 이미지에 베이킹된 cloud-init 지시문을 사용합니다.

        ```console
        echo 'DefaultEnvironment="CLOUD_CFG=/etc/cloud/cloud.cfg.d/00-azure-swap.cfg"' >> /etc/systemd/system.conf  
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

14. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

     **참고:** 특정 가상 머신을 마이그레이션하는 경우 일반화 된 이미지를 만들지 않으려면 프로 비전 해제 단계를 건너뜁니다.

    ```console
    # sudo rm -f /var/log/waagent.log
    # sudo cloud-init clean
    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

15. Hyper-V 관리자에서 **작업 -> 종료** 를 클릭합니다. 이제 Linux VHD를 [Azure에 업로드할](./upload-vhd.md#option-1-upload-a-vhd)준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

이제 CentOS Linux 가상 하드 디스크를 사용하여 Azure에서 새 가상 머신을 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.