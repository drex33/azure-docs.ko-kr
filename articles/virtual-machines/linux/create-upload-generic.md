---
title: Linux VHD 만들기 및 업로드
description: Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: c0ab2da9f6e301f1a39ee1dde65e9e0cb6a5d05b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689838"
---
# <a name="information-for-non-endorsed-distributions"></a>비보증 배포에 대한 정보

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

[보증 배포판](endorsed-distros.md) 중 하나를 사용하는 경우에만 Linux OS를 실행하는 가상 머신에 Azure 플랫폼 SLA가 적용됩니다. 이러한 보증 배포판의 경우 미리 구성된 Linux 이미지가 Azure Marketplace에 제공됩니다.

* [Azure의 Linux - 보증 배포판](endorsed-distros.md)
* [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/kb/2941892)

Azure에서 실행되는 모든 배포에는 여러 가지 필수 구성 요소가 있습니다. 모든 배포가 다르기 때문에 이 문서는 포괄적일 수 없습니다. 아래의 모든 조건을 충족하더라도 Linux 시스템이 제대로 작동하려면 Linux 시스템을 크게 조정해야 할 수도 있습니다.

[Azure의 Linux 보증 배포판](endorsed-distros.md) 중 하나로 시작하는 것이 좋습니다. 다음 문서에서는 Azure에서 지원되는 다양한 Linux 보증 배포판을 준비하는 방법을 보여 줍니다.

- [CentOS 기반 배포](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar 컨테이너 Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 및 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

이 문서에서는 Azure에서 Linux 배포판을 실행하기 위한 일반 지침에 대해 중점적으로 설명합니다.

## <a name="general-linux-installation-notes"></a>일반 Linux 설치 참고 사항
* Azure에서는 *고정 VHD* 만 지원하며, VHDX(Hyper-V 가상 하드 디스크) 형식은 지원하지 않습니다.  Hyper-V 관리자 또는 [Convert-VHD cmdlet](/powershell/module/hyper-v/convert-vhd)을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. VirtualBox를 사용하는 경우 디스크를 만들 때 기본값(동적 할당) 대신 **고정 크기** 를 선택합니다.
* Azure는 1세대(BIOS 부팅) 및 2세대(UEFI 부팅) 가상 머신을 지원합니다.
* VHD에 허용되는 최대 크기는 1,023GB입니다.
* Linux 시스템을 설치하는 경우 대부분의 설치에 대한 기본값인 LVM(논리 볼륨 관리자) 대신 표준 파티션을 사용하는 것이 좋습니다. 표준 파티션을 사용하면 특히 문제를 해결하기 위해 OS 디스크가 동일한 다른 VM에 연결되는 경우에도 LVM 이름이 복제된 VM과 충돌하지 않습니다. 데이터 디스크에서 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 또는 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)를 사용할 수 있습니다.
* UDF 파일 시스템을 탑재하기 위한 커널 지원이 필요합니다. Azure에서 처음 부팅할 때 게스트에 연결된 UDF 형식의 미디어를 사용하여 Linux VM에 프로비전 구성이 전달됩니다. Azure Linux 에이전트는 해당 구성을 읽고 VM을 프로비전하기 위해 UDF 파일 시스템을 탑재해야 합니다.
* 2\.6.37 이전 버전의 Linux 커널은 더 큰 VM 크기의 Hyper-V에서 NUMA를 지원하지 않습니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하는 이전 배포에 영향을 미치며, RHEL(Red Hat Enterprise Linux) 6.6(kernel-2.6.32-504)에서 해결되었습니다. 2.6.37 이전의 사용자 지정 커널 또는 2.6.32-504 이전의 RHEL 기반 커널을 실행하는 시스템의 경우 grub.conf의 커널 명령줄에 `numa=off` 부트 매개 변수를 설정해야 합니다. 자세한 내용은 [Red Hat KB 436883](https://access.redhat.com/solutions/436883)을 참조하세요.
* OS 디스크에 스왑 파티션을 구성하지 않습니다. Linux 에이전트는 다음 단계에서 설명한 대로 임시 리소스 디스크에 스왑 파일을 만들도록 구성할 수 있습니다.
* Azure의 모든 VHD에는 1MB로 정렬된 가상 크기가 있어야 합니다. 원시 디스크에서 VHD로 변환하는 경우 다음 단계에서 설명한 대로 변환하기 전에 먼저 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다.

### <a name="installing-kernel-modules-without-hyper-v"></a>Hyper-V 없이 커널 모듈 설치
Azure는 Hyper-V 하이퍼바이저에서 실행되므로 Linux에는 Azure에서 실행되는 특정 커널 모듈이 필요합니다. Hyper-V 외부에서 만든 VM이 있는 경우 VM이 Hyper-V 환경에서 실행되는 것으로 감지하지 않는 한 Linux 설치 관리자는 초기 ramdisk(initrd 또는 initramfs)에 Hyper-V용 드라이버를 포함하지 않을 수 있습니다. 다른 가상화 시스템(예: VirtualBox, KVM 등)을 사용하여 Linux 이미지를 준비하는 경우 초기 ramdisk에서 적어도 hv_vmbus 및 hv_storvsc 커널 모듈을 사용할 수 있도록 initrd를 다시 빌드해야 할 수 있습니다.  이와 같이 알려진 문제는 업스트림 Red Hat 배포판을 기반으로 하는 시스템 및 다른 시스템과 관련된 것입니다.

initrd 또는 initramfs 이미지를 다시 작성하는 메커니즘은 배포에 따라 다를 수 있습니다. 적절한 절차에 대해서는 배포판의 설명서를 참조하거나 고객 지원팀에 문의하세요.  다음은 `mkinitrd` 유틸리티를 사용하여 initrd를 다시 작성하는 예제입니다.

1. 먼저 기존 initrd 이미지를 백업합니다.

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. hv_vmbus 및 hv_storvsc 커널 모듈을 사용하여 initrd를 다시 작성합니다.

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>VHD 크기 조정
Azure의 VHD 이미지에는 1MB로 조정된 가상 크기가 있어야 합니다.  일반적으로 Hyper-V를 사용하여 만든 VHD는 올바르게 정렬되어 있습니다.  VHD가 올바르게 정렬되지 않으면 VHD에서 이미지를 만들려고 할 때 다음과 비슷한 오류 메시지가 나타날 수 있습니다.

* http:\//\<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd VHD의  21475270656 바이트는 지원되지 않는 가상 크기입니다. 크기는 정수(MB 단위)여야 합니다.

이 경우 Hyper-V 관리자 콘솔 또는 [Resize-VHD](/powershell/module/hyper-v/resize-vhd) PowerShell cmdlet을 사용하여 VM 크기를 조정합니다.  Windows 환경에서 실행하지 않는 경우 `qemu-img`를 사용하여 VHD를 변환하고(필요한 경우) 크기를 조정하는 것이 좋습니다.

> [!NOTE]
> [2.2.1 이상의 qemu-img 버전](https://bugs.launchpad.net/qemu/+bug/1490611)에는 VHD 형식이 잘못 지정되는 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. 2.2.0 이하 또는 2.6 이상의 `qemu-img`를 사용하는 것이 좋습니다.
> 

1. `qemu-img` 또는 `vbox-manage`와 같은 도구를 사용하여 직접 VHD 크기를 조정하면 VHD가 부팅되지 않을 수도 있습니다.  먼저 VHD를 RAW 디스크 이미지로 변환하는 것이 좋습니다.  VM 이미지가 RAW 디스크 이미지(KVM과 같은 일부 하이퍼바이저의 경우 기본값)로 만들어진 경우에는 이 단계를 건너뛸 수 있습니다.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. 가상 크기가 1MB 단위로 정렬되도록 필요한 디스크 이미지 크기를 계산합니다.  다음 bash 셸 스크립트는 `qemu-img info`를 사용하여 디스크 이미지의 가상 크기를 확인한 다음, 크기를 다음 1MB로 계산합니다.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. 위에서 설정한 대로 `$rounded_size`를 사용하여 RAW 디스크의 크기를 조정합니다.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. 이제 RAW 디스크를 고정 크기 VHD로 다시 변환합니다.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   또는 qemu 버전 2.6 이상인 경우 `force_size` 옵션을 포함시킵니다.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux 커널 요구 사항

Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 업스트림 Linux 커널로 직접 제공됩니다. 최신 Linux 커널 버전(예: 3.x)이 포함된 많은 배포판에서 이러한 드라이버를 이미 사용할 수 있으며, 그렇지 않은 경우 이러한 드라이버의 백 포트 버전이 커널에 제공됩니다.  이러한 드라이버는 업스트림 커널에서 새로운 수정과 기능으로 지속적으로 업데이트되므로, 가능한 경우 이러한 수정과 업데이트가 포함된 [보증 배포판](endorsed-distros.md)을 실행하는 것이 좋습니다.

Red Hat Enterprise Linux 버전 6.0-6.3의 변형을 실행하는 경우 [Hyper-V용 최신 LIS 드라이버](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)를 설치해야 합니다. RHEL 6.4 이상(및 파생 버전)부터 LIS 드라이버는 이미 커널에 포함되어 있으므로 추가 설치 패키지는 필요하지 않습니다.

사용자 지정 커널이 필요한 경우 최신 커널 버전(예: 3.8 이상)을 사용하는 것이 좋습니다. 자체 커널을 유지 관리하는 배포업체 또는 공급업체의 경우 LIS 드라이버를 업스트림 커널에서 사용자 지정 커널로 정기적으로 백 포팅해야 합니다.  비교적 최신의 커널 버전을 이미 실행하고 있는 경우에도 LIS 드라이버의 모든 업스트림 수정을 추적하고 필요에 따라 백 포팅하는 것이 좋습니다. LIS 드라이버 원본 파일의 위치는 Linux 커널 원본 트리의 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) 파일에 지정됩니다.
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
커널에 포함되어야 패치는 다음과 같습니다. 이 목록은 모든 배포에 대해 완전할 수 없습니다.

* [ata_piix: 기본적으로 Hyper-V 드라이버로 디스크 연기](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: RESET 경로의 전송 중인 패킷용 계정](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME을 사용하지 마세요.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID 및 가상 호스트 어댑터 드라이버에 대한 WRITE SAME 해제](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL 포인터 역참조 수정](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: 링 버퍼 오류로 I/O가 중지됨](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: __scsi_remove_device 이중 실행 방지](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux 에이전트
[Azure Linux 에이전트는](../extensions/agent-linux.md) Azure에서 Linux 가상 머신을 프로비전합니다. [Linux 에이전트 GitHub 리포지토리](https://github.com/Azure/WALinuxAgent)에서 최신 버전을 가져오거나, 문제를 제기하거나, 끌어오기 요청을 제출할 수 있습니다.

* Linux 에이전트는 Apache 2.0 라이선스 하에서 릴리스되었습니다. 여러 배포판에서 이미 에이전트용 RPM 또는 .deb 패키지를 제공하며, 이러한 패키지는 쉽게 설치 및 업데이트할 수 있습니다.
* Azure Linux 에이전트를 사용하려면 Python v2.6 이상이 필요합니다.
* 또한 에이전트에는 python-pyasn1 모듈도 필요합니다. 대부분의 배포판에서 이 모듈을 설치할 별도의 패키지로 제공합니다.
* 경우에 따라 Azure Linux 에이전트가 NetworkManager와 호환되지 않을 수도 있습니다. 배포판에서 제공되는 많은 RPM/deb 패키지는 NetworkManager를 waagent 패키지와의 충돌로 구성합니다. 이러한 경우 Linux 에이전트 패키지를 설치하면 NetworkManager가 제거됩니다.
* Azure Linux 에이전트는 [지원되는 최소 버전](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) 이상이어야 합니다.

## <a name="general-linux-system-requirements"></a>일반 Linux 시스템 요구 사항

1. 다음 매개 변수를 포함하도록 GRUB 또는 GRUB2의 커널 부팅 줄을 수정하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송됩니다. 이러한 메시지는 Azure에서 모든 문제를 디버그하는 데 도움이 될 수 있습니다.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    또한 다음 매개 변수가 있는 경우 이를 *제거* 하는 것이 좋습니다.
    ```  
    rhgb quiet crashkernel=auto
    ```
    모든 로그를 직렬 포트로 전송하려는 클라우드 환경에서는 그래픽 및 자동 부팅이 유용하지 않습니다. `crashkernel` 옵션은 필요한 경우 구성한 상태로 유지할 수 있지만, 이 매개 변수는 VM에서 사용 가능한 메모리 양을 128MB 이상 줄여주므로 VM 크기가 이 크기보다 작은 경우 문제가 될 수 있습니다.

1. Azure Linux 에이전트를 설치합니다.
  
    Azure에서 Linux 이미지를 프로비전하려면 Azure Linux 에이전트가 필요합니다.  많은 배포판에서 에이전트를 RPM 또는 .deb 패키지(일반적으로 'WALinuxAgent' 또는 'walinuxagent'라고 함)로 제공합니다.  [Linux 에이전트 가이드](../extensions/agent-linux.md)의 단계를 수행하여 에이전트를 수동으로 설치할 수도 있습니다.

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  일반적으로 이 구성이 기본값입니다.

1. OS 디스크에 스왑 공간을 만들지 않습니다.
  
    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며, VM의 프로비전을 해제할 때 비울 수 있습니다. Azure Linux 에이전트를 설치한 후(위의 2단계) 필요에 따라 /etc/waagent.conf에서 다음 매개 변수를 수정합니다.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제합니다.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Virtualbox에서 `[Errno 5] Input/output error`라는 `waagent -force -deprovision`을 실행한 후에 다음 오류가 표시될 수 있습니다. 이 오류 메시지는 중요하지 않으므로 무시할 수 있습니다.

* 가상 머신을 종료하고 Azure에 VHD를 업로드합니다.