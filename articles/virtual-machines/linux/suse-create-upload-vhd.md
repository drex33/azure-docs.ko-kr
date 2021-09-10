---
title: Azure에서 SUSE Linux VHD 만들기 및 업로드
description: SUSE Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 30246166650cddc1c89fa3a2ca377c163fb5847b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697753"
---
# <a name="prepare-a-sles-or-opensuse-leap-virtual-machine-for-azure"></a>Azure용 SLES 또는 openSUSE Leap 가상 머신 준비

**적용 대상:** :heavy_check_mark: Linux VM: heavy_check_mark: 유연한 확장 집합 **적용 대상:** heavy_check_mark: 단일 확장 집합

이 문서에서는 가상 하드 디스크에 SUSE 또는 openSUSE Leap Linux 운영 체제를 이미 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있습니다(예: Hyper-V와 같은 가상화 솔루션). 자세한 내용은 [Hyper-V 역할 설치 및 Virtual Machine 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))을 참조하십시오.

## <a name="sles--opensuse-leap-installation-notes"></a>SLES / openSUSE Leap 설치 참고 사항
* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes) 을 참조하세요.
* VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD** 만 지원됩니다.  Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 또는 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)를 사용할 수 있습니다.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다.  여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

## <a name="use-suse-studio"></a>SUSE Studio 사용
[SUSE Studio](https://studioexpress.opensuse.org/)에서는 Azure와 Hyper-V용 SLES 및 openSUSE Leap 이미지를 쉽게 만들고 관리할 수 있습니다. 고유한 SLES 및 openSUSE Leap 이미지를 사용자 지정할 때는 이 방식을 사용하는 것이 좋습니다.

또한 SUSE는 [VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)에 SLES용 BYOS(Bring Your Own Subscription) 이미지도 게시하므로 VHD를 직접 작성하는 대신 이 이미지를 사용할 수 있습니다.

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Azure용 SUSE Linux Enterprise Server 준비
1. Hyper-V 관리자의 가운데 창에서 가상 머신을 선택합니다.
2. **연결** 을 클릭하여 가상 머신 창을 엽니다.
3. 업데이트를 다운로드하고 패키지를 설치할 수 있도록 SUSE Linux Enterprise 시스템을 등록합니다.
4. 모든 최신 패치로 시스템을 업데이트합니다.

    ```console
    # sudo zypper update
    ```
    
5. Azure Linux 에이전트 및 cloud-init를 설치합니다.

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. waagent 및 cloud-init가 부팅 시 시작하도록 설정합니다.

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. waagent 및 cloud-init 구성을 업데이트합니다.

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. /etc/default/grub 파일을 편집하여 콘솔 로그가 직렬 포트로 전송되는지 확인한 다음 grub2-mkconfig -o /boot/grub2/grub.cfg를 사용하여 주 구성 파일을 업데이트합니다.

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다.
    
9. /etc/fstab 파일이 UUID(by-uuid)를 사용하여 디스크를 참조하는지 확인합니다.
         
10. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 수정합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킬 수 있습니다.

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. "/etc/sysconfig/network/dhcp" 파일을 편집하여 `DHCLIENT_SET_HOSTNAME` 매개 변수를 다음과 같이 변경하는 것이 좋습니다.

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. "/etc/sudoers"에서 다음 줄이 있으면 주석 처리하거나 제거합니다.

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

14. 스왑 구성
 
    운영 체제 디스크에 스왑 공간을 만들지 마세요.

    이전에는 Azure Linux 에이전트가 Azure에서 가상 머신을 프로비저닝한 후에 가상 머신에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있었습니다. 그러나 이제 해당 작업은 cloud-init에서 처리되므로, Linux 에이전트를 사용하여 리소스 디스크의 형식을 지정해 스왑 파일을 생성해서는 **안 되며**, `/etc/waagent.conf`에서 다음 매개 변수를 알맞게 수정합니다.

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    스왑을 생성, 탑재, 형식 지정하고자 하는 경우 다음 방법 중 하나를 수행합니다.
    * VM을 만들 때마다 cloud-init 구성으로 해당 작업을 전달합니다.
    * VM이 생성될 때마다 해당 작업을 수행할 이미지에 베이킹된 cloud-init 지시문을 사용합니다.

        ```console
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

15. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Hyper-V 관리자에서 **작업 -> 종료** 를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

---
## <a name="prepare-opensuse-152"></a>openSUSE 15.2+ 준비
1. Hyper-V 관리자의 가운데 창에서 가상 머신을 선택합니다.
2. **연결** 을 클릭하여 가상 머신 창을 엽니다.
3. 셸에서 '`zypper lr`' 명령을 실행합니다. 이 명령에서 다음과 유사한 출력이 반환되는 경우 리포지토리가 예상대로 구성된 것입니다. 이 경우 아무 것도 조정할 필요가 없습니다(버전 번호는 다를 수 있음).

   | # | Alias                 | 이름                  | 사용 | 새로 고침
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud:Tools_15.2      | Cloud:Tools_15.2      | 예     | 예
   | 2 | openSUSE_15.2_OSS     | openSUSE_15.2_OSS     | 예     | 예
   | 3 | openSUSE_15.2_Updates | openSUSE_15.2_Updates | 예     | 예

    명령이 "정의된 리포지토리가 없습니다..."를 반환하면 다음 명령을 사용하여 해당 리포지토리를 추가합니다.

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_15.2 Cloud:Tools_15.2
    # sudo zypper ar -f https://download.opensuse.org/distribution/15.2/repo/oss openSUSE_15.2_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/15.2 openSUSE_15.2_Updates
    ```

    그런 다음 '`zypper lr`' 명령을 다시 실행하여 리포지토리가 추가되었는지 확인할 수 있습니다. 관련된 업데이트 리포지토리 중 하나가 사용되도록 설정되어 있지 않은 경우 다음 명령을 사용하여 사용하도록 설정합니다.

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. 커널을 사용 가능한 최신 버전으로 업데이트합니다.

    ```console
    # sudo zypper up kernel-default
    ```

    또는 모든 최신 패치로 시스템을 업데이트합니다.

    ```console
    # sudo zypper update
    ```

5. Azure Linux 에이전트를 설치합니다.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 작업을 수행하려면 "/boot/grub/menu.lst"를 텍스트 편집기에서 열고 다음 매개 변수가 기본 커널에 포함되어 있는지 확인합니다.

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데 도움이 될 수 있습니다. 또한 커널 부팅 줄에 다음 매개 변수가 있는 경우 해당 매개 변수를 제거합니다.

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. "/etc/sysconfig/network/dhcp" 파일을 편집하여 `DHCLIENT_SET_HOSTNAME` 매개 변수를 다음과 같이 변경하는 것이 좋습니다.

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **중요:** "/etc/sudoers"에서 다음 줄이 있으면 주석 처리하거나 제거합니다.

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.
10. OS 디스크에 스왑 공간을 만들지 마십시오.

    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. 시작할 때 Azure Linux 에이전트가 실행되는지 확인합니다.

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Hyper-V 관리자에서 **작업 -> 종료** 를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 SUSE Linux 가상 하드 디스크를 사용하여 Azure에서 새 가상 머신을 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.
