---
title: Azure에서 Ubuntu Linux VHD 만들기 및 업로드
description: Ubuntu Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/28/2021
ms.author: srijangupta
ms.openlocfilehash: f0417c156de07cd5a6fd45bdd63ed9134078966f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566564"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Azure용 Ubuntu 가상 머신 준비


이제 Ubuntu는 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)에서 다운로드할 수 있도록 공식 Azure VHD를 게시합니다. Azure에 대해 특수한 사용자 고유의 Ubuntu 이미지를 빌드해야 하는 경우 아래 수동 절차 대신 이러한 알려진 작업 VHD를 시작하고 필요에 따라 사용자 지정하는 것이 좋습니다. 최신 이미지 릴리스는 항상 다음 위치에서 제공됩니다.

* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64-azure.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64-azure.vhd.zip)
* Ubuntu 20.04/Focal:  [focal-server-cloudimg-amd64-azure.vhd.zip](https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64-azure.vhd.zip)

## <a name="prerequisites"></a>필수 구성 요소
이 문서에서는 가상 하드 디스크에 Ubuntu Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 자세한 내용은 [Hyper-V 역할 설치 및 Virtual Machine 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))을 참조하십시오.

**Ubuntu 설치 참고 사항**

* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes) 을 참조하세요.
* VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD** 만 지원됩니다.  Hyper-V 관리자 또는 `Convert-VHD` cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 또는 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)를 사용할 수 있습니다.
* OS 디스크에 스왑 파티션 또는 스왑 파일을 구성하지 마세요. 임시 리소스 디스크에 스왑 파일 또는 스왑 파티션을 생성하도록 cloud-init 프로비저닝 에이전트를 구성할 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

## <a name="manual-steps"></a>수동 단계
> [!NOTE]
> Azure에 대해 고유한 사용자 지정 Ubuntu 이미지를 만들기 전에 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)에서 미리 빌드되고 테스트된 이미지를 사용하는 것을 고려하세요.
> 
> 

1. Hyper-V 관리자의 가운데 창에서 가상 머신을 선택합니다.

2. **연결** 을 클릭하여 가상 머신 창을 엽니다.

3. Ubuntu의 Azure 리포지토리를 사용하도록 이미지의 현재 리포지토리를 바꿉니다.

    `/etc/apt/sources.list`를 편집하기 전에 백업을 만드는 것이 좋습니다.

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 18.04 및 Ubuntu 20.04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. 이제 Ubuntu Azure 이미지가 [Azure 맞춤형 커널](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)을 사용하고 있습니다. 다음 명령을 실행하여 운영 체제를 최신 Azure 맞춤형 커널로 업데이트하고 Azure Linux 도구(Hyper-v 종속성 포함)를 설치합니다.

    Ubuntu 18.04 및 Ubuntu 20.04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Azure용 커널 매개 변수를 추가로 포함하려면 Grub의 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 `/etc/default/grub`을 텍스트 편집기에서 열고 `GRUB_CMDLINE_LINUX_DEFAULT` 변수를 찾거나 필요한 경우 추가하여 다음 매개 변수가 포함되도록 편집합니다.

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    이 파일을 저장하고 닫은 다음 `sudo update-grub`을 실행합니다. 이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 기술 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.

6. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

7. cloud-init(프로비저닝 에이전트)와 Azure Linux 에이전트(게스트 확장 처리기)를 설치합니다. 프로비저닝되고 이후에 부팅되는 도중에 Cloud-init은 `netplan`을 사용하여 시스템 네트워크 구성을 구성합니다.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent` 패키지는 `NetworkManager` 및 `NetworkManager-gnome` 패키지가 설치되어 있는 경우 이러한 패키지를 제거할 수 있습니다.

8. Azure에서 cloud-init 프로비저닝과 충돌할 수 있는 cloud-init 기본 구성 및 나머지 `netplan` 아티팩트를 제거합니다.

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg /etc/cloud/cloud.cfg.d/99-installer.cfg /etc/cloud/cloud.cfg.d/subiquity-disable-cloudinit-networking.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Azure 데이터 소스를 사용하여 시스템을 프로비저닝하도록 cloud-init을 구성합니다.

    ```bash
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. cloud-init를 사용하여 프로비저닝하도록 Azure Linux 에이전트를 구성합니다. 해당 옵션에 대한 자세한 내용은 [WALinuxAgent 프로젝트](https://github.com/Azure/WALinuxAgent)를 참조하세요.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Cloud-init과 Azure Linux 에이전트 런타임 아티팩트 및 로그를 정리합니다.

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    > [!NOTE]
    > `sudo waagent -force -deprovision+user` 명령은 시스템을 정리하고 다시 프로비저닝하는 데 적합하도록 하여 이미지를 일반화합니다. `+user` 옵션은 마지막으로 프로비저닝된 사용자 계정 및 관련 데이터를 삭제합니다.

    > [!WARNING]
    > 위의 명령을 사용하여 프로비저닝을 해제하더라도 이미지에서 중요한 정보가 모두 지워져 다시 배포하기에 적합해진다고 보증할 수는 없습니다.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Hyper-V 관리자에서 **작업 -> 종료** 를 클릭합니다.

14. Azure는 고정 크기의 VHD만 허용합니다. VM의 OS 디스크가 고정 크기의 VHD가 아닌 경우 `Convert-VHD` PowerShell cmdlet을 사용하여 `-VHDType Fixed` 옵션을 지정합니다. 여기에서 `Convert-VHD`에 대한 문서를 확인하세요: [변환-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>다음 단계
이제 Ubuntu Linux 가상 하드 디스크를 사용하여 Azure에서 새 가상 머신을 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.
