---
title: Azure Migrate 에이전트 없는 마이그레이션을 위한 컴퓨터 준비
description: Azure Migrate를 사용하여 에이전트 없는 마이그레이션을 위해 온-프레미스 컴퓨터를 준비하는 방법을 알아봅니다.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 7030cb8329481e833a7dc34815da7b70b6c5482e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538186"
---
# <a name="prepare-for-vmware-agentless-migration"></a>VMware 에이전트 없는 마이그레이션에 대한 준비

이 문서에서는 Azure Migrate: 서버 마이그레이션 도구를 사용하여 [에이전트 없는 마이그레이션 방법을 통해 VMware VM을 Azure로 마이그레이션](./tutorial-migrate-vmware.md)할 때 수행되는 변경 내용에 대해 간략하게 설명합니다.

온-프레미스 VM을 Azure로 마이그레이션하기 전에 VM을 Azure에 맞게 준비하기 위해 몇 가지 변경이 필요할 수 있습니다. 이러한 변경은 마이그레이션된 VM이 Azure에서 성공적으로 부팅되고 Azure VM에 대한 연결을 설정할 수 있도록 하는 데 중요합니다.
Azure Migrate에서는 Linux 및 Windows 모두의 아래에 언급된 운영 체제 버전에 대해 이러한 구성 변경이 자동으로 처리됩니다. 이러한 프로세스를 *하이드레이션* 이라고 합니다.

**하이드레이션이 지원되는 운영 체제 버전**

- Windows Server 2008 이상
- Red Hat Enterprise Linux 8, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x
- CentOS 8, 7.7, 7.6, 7.5, 7.4, 6.x
- SUSE Linux Enterprise Server 15 SP0, 15 SP1, 12
- Ubuntu 20.04, 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS
- Ubuntu 18.04LTS, 16.04LTS
- Debian 9, 8, 7
- Oracle Linux 6, 7.7, 7.7-CI

이 문서를 사용하여 위에 나열되지 않은 운영 체제 버전에 대해 Azure로 마이그레이션할 VM을 수동으로 준비할 수도 있습니다. 개략적으로 이러한 변경 사항은 다음과 같습니다.

- 필요한 드라이버가 있는지 확인
- 직렬 콘솔을 사용하도록 설정
- 네트워크 설정 구성
- VM 게스트 에이전트 설치

## <a name="hydration-process"></a>하이드레이션 프로세스

마이그레이션된 VM이 Azure에서 제대로 작동하도록 하려면 마이그레이션하기 전에 VM 구성을 일부 변경해야 합니다. Azure Migrate는 하이드레이션(*hydration*) 프로세스를 통해 이러한 구성 변경을 처리합니다. 하이드레이션 프로세스는 위에 언급된 Azure 지원 운영 체제 버전에 대해서만 수행됩니다. 위에 나열되지 않은 다른 운영 체제 버전의 경우 마이그레이션하기 전에 필요한 변경을 수동으로 수행해야 할 수 있습니다. 필요한 변경을 수행하지 않고 VM을 마이그레이션하면 VM이 부팅되지 않거나 마이그레이션된 VM에 연결되지 않을 수 있습니다. 다음 다이어그램은 Azure Migrate에서 하이드레이션 프로세스가 수행되는 것을 보여줍니다.

 [![하이드레이션 단계](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

사용자가 테스트 마이그레이션(*Test Migrate*) 또는 마이그레이션(*Migrate*)을 트리거하면 Azure Migrate는 Azure로의 마이그레이션을 위해 온-프레미스 VM을 준비하는 하이드레이션 프로세스를 수행합니다.
하이드레이션 프로세스를 설정하기 위해 Azure Migrate는 임시 Azure VM을 만들고 원본 VM의 디스크를 연결하여 원본 VM이 Azure에 맞게 준비되도록 변경 작업을 수행합니다. 임시 Azure VM은 마이그레이션된 최종 VM이 만들어지기 전에 마이그레이션 프로세스 중에 만들어지는 중간 VM입니다. 임시 VM은 마켓플레이스 OS 이미지 중 하나를 사용하여 유사한 OS 유형(Windows/Linux)으로 만들어집니다. 온-프레미스 VM이 Windows를 실행하는 경우 변경 작업을 수행하기 위해 온-프레미스 VM의 운영 체제 디스크가 임시 VM에 데이터 디스크로 연결됩니다. Linux 서버인 경우 온-프레미스 VM에 연결된 모든 디스크가 임시 Azure VM에 데이터 디스크로 연결됩니다.

Azure Migrate는 임시 VM을 호스트할 네트워크 인터페이스, 새 가상 네트워크, 서브넷 및 NSG(네트워크 보안 그룹)를 만듭니다. 이러한 리소스는 고객의 구독에 만들어집니다. 네트워크 아티팩트를 만들지 못하게 하는 충돌하는 정책이 있는 경우 Azure Migrate는 복제 대상 설정 옵션의 일부로 제공된 가상 네트워크 및 서브넷에 임시 Azure VM을 만들려고 시도합니다.

가상 머신이 만들어지면 Azure Migrate는 Azure Virtual Machine REST API를 사용하여 임시 VM에서 [사용자 지정 스크립트 확장](../virtual-machines/extensions/custom-script-windows.md)을 호출합니다. 사용자 지정 스크립트 확장 유틸리티는 임시 Azure VM에 연결된 온-프레미스 VM 디스크에서 Azure 준비에 필요한 구성이 포함된 준비 스크립트를 실행합니다. 준비 스크립트는 Azure Migrate 소유 스토리지 계정에서 다운로드됩니다. 가상 네트워크의 네트워크 보안 그룹 규칙은 임시 Azure VM이 스크립트를 호출하기 위해 Azure Migrate 스토리지 계정에 액세스할 수 있도록 구성됩니다.

 ![마이그레이션 단계](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>하이드레이션 프로세스 중에 수행된 변경 내용

준비 스크립트는 마이그레이션할 원본 VM의 OS 유형에 따라 다음과 같은 변경 내용을 실행합니다. 또한 이 섹션을 가이드로 사용하여 하이드레이션이 지원되지 않는 운영 체제 버전의 마이그레이션을 위해 VM을 수동으로 준비할 수도 있습니다.

### <a name="changes-performed-on-windows-servers"></a>Windows 서버에서 수행되는 변경 내용

1. **Windows OS 볼륨 검색 및 준비**

   관련 구성 변경을 수행하기 전에 준비 스크립트는 마이그레이션에 알맞은 OS 디스크가 선택되었는지 확인합니다. 준비 스크립트는 시스템에 표시되는 연결된 볼륨을 모두 살펴보고 SYSTEM 레지스트리 하이브 파일 경로를 찾아 원본 OS 볼륨을 찾습니다.

   이 단계에서 수행되는 작업은 다음과 같습니다.
   - 임시 VM에 연결된 OS 디스크에 각 파티션을 탑재합니다.

   - 파티션을 탑재한 후 \Windows\System32\Config\System 레지스트리 파일을 찾습니다.
   - 파일을 찾을 수 없으면 파티션이 분리되고 올바른 파티션에 대한 검색이 계속됩니다.
   - 파일이 파티션에 없으면 잘못된 OS 디스크가 선택되었거나 OS 디스크가 손상되었음을 나타낼 수 있습니다. Azure Migrate는 마이그레이션 프로세스에 실패하고 적절한 오류가 표시됩니다.

   >[!NOTE]
   >마이그레이션을 위해 서버를 수동으로 준비하는 경우에는 이 단계가 관련이 없습니다.

1. **부팅 및 연결 관련 변경**

   원본 OS 볼륨 파일이 검색되면 준비 스크립트는 SYSTEM 레지스트리 하이브를 임시 Azure VM의 레지스트리 편집기에 로드하고 VM 부팅 및 연결을 보장하기 위해 다음 변경을 수행합니다. OS 버전에서 하이드레이션이 지원되지 않는 경우 이러한 설정을 수동으로 구성해야 합니다.

   1. **필요한 드라이버가 있는지 확인**
      
      필요한 드라이버가 설치되어 있고 **부팅 시작** 시 로드되도록 설정되어 있는지 확인합니다. 이러한 Windows 드라이버를 통해 서버가 하드웨어 및 기타 연결된 디바이스와 통신할 수 있습니다.

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **SAN(Storage Area Network) 정책을 모두 온라인으로 설정**

      이렇게 하면 Azure VM의 Windows 볼륨이 온-프레미스 VM과 동일한 드라이브 문자 할당을 사용합니다. Azure VM에는 기본적으로 임시 스토리지로 사용할 D 드라이브가 할당됩니다. 이 드라이브 할당으로 인해 다른 모든 스토리지 드라이브 할당이 한 문자씩 증가합니다. 이 자동 할당을 방지하고 Azure에서 사용 가능한 다음 드라이브 문자를 임시 볼륨에 할당하도록 하려면 SAN(Storage Area Network) 정책을 모두 온라인으로 설정합니다.

      이 설정을 수동으로 구성하려면:

      - 온-프레미스 서버에서 상승된 권한으로 명령 프롬프트를 열고 **diskpart** 를 입력합니다.

        ![수동 구성](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - SAN을 입력합니다. 게스트 운영 체제의 드라이브 문자가 유지되지 않으면 모두 오프라인 또는 공유 디스크만 오프라인이 반환됩니다.

      - DISKPART 프롬프트에서 SAN Policy=OnlineAll을 입력합니다. 이 설정을 사용하면 디스크가 온라인 상태가 되고 두 디스크 모두에서 읽고 쓸 수 있습니다.

        ![관리자 명령 프롬프트 diskpart 온라인 정책](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **DHCP 시작 유형 설정**

   준비 스크립트는 DHCP 서비스 시작 유형도 자동으로 설정합니다. 이렇게 하면 마이그레이션된 VM이 마이그레이션 후 IP 주소를 얻고 연결을 설정할 수 있습니다. DHCP 서비스가 구성되어 있고 상태가 실행 중인지 확인합니다.

    ![DHCP 시작 유형 설정](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   DHCP 시작 설정을 수동으로 편집하려면 Windows PowerShell에서 다음 예제를 실행합니다.

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **VMware Tools 사용 안 함**

   "VMware Tools" 서비스 시작 유형이 있는 경우 Azure의 VM에는 필요하지 않으므로 사용하지 않도록 설정합니다.

   >[!NOTE]                        
   >Windows Server 2003 VM에 연결하려면 Azure VM에 Hyper-V 통합 서비스를 설치해야 합니다. Windows Server 2003 컴퓨터에는 기본적으로 설치되어 있지 않습니다. 설치하고 마이그레이션을 위해 준비하려면 이 [문서](./prepare-windows-server-2003-migration.md)를 참조하세요.

1. **Windows Azure 게스트 에이전트 설치**

    Azure Migrate는 Azure 패브릭 컨트롤러와의 VM(가상 머신) 상호 작용을 관리하는 안전하고 간단한 프로세스인 Microsoft Azure VM 에이전트(가상 머신 에이전트) 설치를 시도합니다. VM 에이전트는 VM의 배포 후 구성(예: 소프트웨어 설치 및 구성)을 가능하게 하는 Azure 가상 머신 확장을 사용하도록 설정하고 실행하는 데 주된 역할을 합니다. Azure Migrate는 Windows Server 2008 R2 이상 버전에 Windows VM 에이전트를 자동으로 설치합니다.

    Windows VM 에이전트는 Windows 설치 관리자 패키지를 사용하여 수동으로 설치할 수 있습니다. Windows VM 에이전트를 수동으로 설치하려면 [VM 에이전트 설치 관리자를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=394789). [GitHub Windows IaaS VM 에이전트 릴리스](https://github.com/Azure/WindowsVMAgent/releases)에서 특정 버전을 검색할 수도 있습니다. VM 에이전트는 Windows Server 2008(64비트) 이상에서 지원됩니다.

    Azure VM 에이전트가 성공적으로 설치되었는지 확인하려면 작업 관리자를 열고 **세부 정보** 탭을 선택한 다음, 프로세스 이름 *WindowsAzureGuestAgent.exe* 를 찾습니다. 이 프로세스가 있으면 VM 에이전트가 설치되어 있는 것입니다. [PowerShell을 사용하여 VM 에이전트를 검색](../virtual-machines/extensions/agent-windows.md#powershell)할 수도 있습니다.

    ![Azure VM 에이전트 설치 성공](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    앞서 언급한 변경이 수행되며 시스템 파티션이 언로드됩니다. 이제 VM을 마이그레이션할 준비가 되었습니다.
    [Windows Server에 대한 변경 내용을 자세히 알아보세요.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

### <a name="changes-performed-on-linux-servers"></a>Linux 서버에서 수행되는 변경 내용

1. **Linux OS 파티션 검색 및 탑재**  

   관련 구성 변경을 수행하기 전에 준비 스크립트는 마이그레이션에 알맞은 OS 디스크가 선택되었는지 확인합니다. 이 스크립트는 모든 파티션, 해당 UUID 및 탑재 지점에 대한 정보를 수집합니다. 스크립트는 이러한 표시되는 파티션을 모두 살펴보고 /boot 및 /root 파티션을 찾습니다.

   이 단계에서 수행되는 작업은 다음과 같습니다.

   - /root 파티션 검색:
     - 표시되는 각 파티션을 탑재하고 etc/fstab을 찾습니다.
      - fstab 파일을 찾을 수 없으면 파티션이 분리되고 올바른 파티션에 대한 검색이 계속됩니다.
      - fstab 파일을 찾으면 fstab 콘텐츠를 읽고 루트 디바이스를 식별하여 베이스 탑재 지점으로 탑재합니다.
   - /boot 및 기타 시스템 파티션 검색:
     - fstab 콘텐츠를 사용하여 /boot가 별도의 파티션인지 확인합니다. 별도의 파티션이면 fstab 콘텐츠에서 부팅 파티션 디바이스 이름을 얻거나 부팅 플래그가 있는 파티션을 찾습니다.
     - 스크립트는 "/mnt/azure_sms_root"에서 /boot 및 기타 필요한 파티션을 검색하여 탑재하고 chroot jail에 필요한 루트 파일 시스템 트리를 빌드합니다. 기타 필요한 파티션에는 /boot/grub/menu.lst, /boot/grub/grub.conf, /boot/grub2/grub.cfg, /boot/grub/grub.cfg, /boot/efi(UEFI 부팅의 경우), /var, /lib, /etc, /usr 등이 있습니다.

1. **OS 버전 검색**

   루트 파티션이 검색되면 스크립트는 아래 파일을 사용하여 Linux 운영 체제 배포 및 버전을 확인합니다.

   - RHEL/CentOS: etc/redhat-release
   - OL: etc/oracle-release
   - SLES: etc/SuSE-release
   - Ubuntu: etc/lsb-release
   - Debian: etc/debian_version

1. **Hyper-V Linux 통합 서비스 설치 및 커널 이미지 다시 생성**  

   다음 단계는 커널 이미지를 검사하고 Linux 초기화 이미지를 다시 빌드하여 초기 ramdisk에 필요한 Hyper-V 드라이버(**hv_vmbus, hv_storvsc, hv_netvsc**)가 포함되도록 하는 것입니다. 초기화 이미지가 다시 빌드하면 VM이 Azure에서 부팅됩니다.

   Azure는 Hyper-v 하이퍼바이저에서 실행됩니다. 따라서 Linux에는 Azure에서 실행되는 특정 커널 모듈이 필요합니다. Linux 이미지를 준비하려면 initrd를 다시 빌드해야 합니다. 그래야 초기 ramdisk에서 최소한 hv_vmbus 및 hv_storvsc 커널 모듈을 사용할 수 있습니다. initrd 또는 initramfs 이미지를 다시 작성하는 메커니즘은 배포에 따라 다를 수 있습니다. 적절한 절차에 대해서는 배포판의 설명서를 참조하거나 고객 지원팀에 문의하세요. 다음은 mkinitrd 유틸리티를 사용하여 initrd를 다시 빌드하는 예제입니다.

   1. 시스템에 설치된 커널 목록 찾기(/lib/modules)
   1. 각 모듈에 대해 Hyper-V 드라이버가 이미 포함되어 있는지 검사합니다.
   1. 이러한 드라이버가 없는 경우 필요한 드라이버를 추가하고 해당 커널 버전에 대한 이미지를 다시 생성합니다.

      >[!NOTE]
      >기본적으로 Hyper-V 드라이버가 내장되어 있으므로 Ubuntu 및 Debian VM에는 이 단계가 적용되지 않을 수 있습니다. [변경 내용에 대해 자세히 알아보세요.](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)

      initrd 다시 빌드를 설명하는 예시

      - 기존 initrd 이미지를 백업합니다.

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - hv_vmbus 및 hv_storvsc 커널 모듈을 사용하여 initrd를 다시 작성합니다.

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   이 기능은 대부분의 새 Linux 배포판 버전에 기본적으로 포함되어 있습니다. 포함되지 않은 경우 앞서 언급한 단계를 사용하여 위에 언급된 버전을 제외한 모든 버전에 대해 수동으로 설치합니다.

1. **Azure 직렬 콘솔 로깅을 사용하도록 설정**

   그러면 스크립트가 Azure 직렬 콘솔 로깅을 사용하도록 변경합니다. 콘솔 로깅을 사용하도록 설정하면 Azure VM에서 문제를 해결하는 데 유용합니다. Linux용 Azure 직렬 콘솔에 대한 자세한 내용은 [Linux용 Azure 직렬 콘솔 - Virtual Machines | Microsoft Docs](/troubleshoot/azure/virtual-machines/serial-console-linux)를 참조하세요.

   다음 매개 변수를 포함하도록 GRUB 또는 GRUB2의 커널 부팅 줄을 수정하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송됩니다. 이러한 메시지는 Azure에서 모든 문제를 디버그하는 데 도움이 될 수 있습니다.

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   또한 다음 매개 변수가 있는 경우 이를 제거하는 것이 좋습니다.

   ```
   rhgb quiet crashkernel=auto
   ```
    구체적인 변경 내용은 [이 문서를 참조](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements)하세요.

1. **연결을 위한 네트워크 변경**

   이 스크립트는 OS 버전에 따라 마이그레이션된 VM에 연결하는 데 필요한 네트워크 변경을 수행합니다. 변경 사항에는 다음이 포함됩니다.

   1. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure에서 가상 머신을 복제하는 경우 문제를 일으킵니다.

      RedHat 서버에 대한 설명 예시

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. 필요한 경우 네트워크 관리자를 제거합니다. 네트워크 관리자는 일부 OS 버전의 Azure Linux 에이전트를 방해할 수 있습니다. RedHat 및 Ubuntu 배포를 실행하는 서버에 대해 이러한 변경을 수행하는 것이 좋습니다.

   1. 다음 명령을 실행하여 이 패키지를 제거합니다.
    
      RedHat 서버에 대한 설명 예시

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. 기존 NIC 설정을 백업하고 DHCP 설정을 사용하여 eth0 NIC 구성 파일을 만듭니다. 이렇게 하기 위해 스크립트는 /etc/sysconfig/network-scripts/ifcfg-eth0 파일을 만들거나 편집하고 다음 텍스트를 추가합니다.

      RedHat 서버에 대한 설명 예시

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. etc/sysconfig/network 파일을 다음과 같이 다시 설정합니다.

      RedHat 서버에 대한 설명 예시

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **Fstab 유효성 검사**

    Azure Migrate는 fstab 파일 항목의 유효성을 검사하고 필요한 경우 fstab 항목을 영구 볼륨 식별자, UUID로 바꿉니다. 이렇게 하면 연결된 시스템에 관계없이 드라이브/파티션 이름이 일관되게 유지됩니다.

   - 디바이스 이름이 표준 디바이스 이름(예: /dev/sdb1)인 경우:
     - 루트 또는 부팅 파티션이면 UUID로 대체됩니다.
     - 파티션이 동일한 디스크의 표준 파티션으로 루트 또는 부팅 파티션과 공존하면 UUID로 대체됩니다.
   - 디바이스 이름이 UUID/LABEL/LV이면 변경이 수행되지 않습니다.
   - 네트워크 디바이스(nfs, cifs, smbfs 등)이면 스크립트는 항목을 주석으로 처리합니다. 액세스하려면 동일한 주석을 제거하고 Azure VM을 다시 부팅하면 됩니다.

1. **Linux Azure 게스트 에이전트 설치**

    Azure Migrate는 Linux 및 FreeBSD 프로비저닝 및 Azure Fabric Controller와의 VM 상호 작용을 관리하는 안전하고 간단한 프로세스인 Microsoft Azure Linux 에이전트(waagent)를 설치하려고 시도합니다.  Linux 에이전트를 통해 Linux 및 FreeBSD IaaS 배포에 사용할 수 있는 기능에 대해 [자세히 알아보세요](../virtual-machines/extensions/agent-linux.md).

    Linux VM 에이전트를 설치하는 데 [필요한 패키지](../virtual-machines/extensions/agent-linux.md#requirements) 목록을 검토합니다. 에이전트 없는 VMware 마이그레이션 방법을 사용할 때, RHEL 8/7/6, CentOS 8/7/6, Ubuntu 14.04/16.04/18.04/19.04/19.10/20.04, SUSE 15 SP0/15 SP1/12, Debian 9/8/7, Oracle 7의 경우 Azure Migrate에서 Linux VM 에이전트를 자동으로 설치합니다. 다른 OS 버전에 대해 [Linux 에이전트를 수동으로 설치](../virtual-machines/extensions/agent-linux.md#installation)하려면 지침을 따르세요.

    명령을 사용하여 Azure Linux 에이전트의 서비스 상태를 확인하여 실행되고 있는지 확인할 수 있습니다. 서비스 이름은 **walinuxagent** 또는 **waagent** 일 수 있습니다.
    하이드레이션 변경이 완료되면 스크립트는 탑재된 모든 파티션을 분리하고, 볼륨 그룹을 비활성화한 다음, 디바이스를 플러시합니다.

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [Linux 서버의 변경 내용에 대해 자세히 알아보세요.](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>임시 VM 정리

필요한 변경이 수행되면 Azure Migrate는 임시 VM을 스핀다운하고 연결된 OS 디스크(및 데이터 디스크)를 해제합니다. 이것이 *하이드레이션 프로세스* 의 끝을 나타냅니다.     

그런 다음, 수정된 OS 디스크와 복제된 데이터가 포함된 데이터 디스크가 복제됩니다. 대상 지역, 가상 네트워크 및 서브넷에 새 가상 머신이 만들어지고 복제된 디스크가 가상 머신에 연결됩니다. 이것이 마이그레이션 프로세스의 완료를 나타냅니다.

## <a name="learn-more"></a>자세한 정보

- [Azure로 마이그레이션하기 위한 온-프레미스 컴퓨터를 준비합니다.](./prepare-for-migration.md)
