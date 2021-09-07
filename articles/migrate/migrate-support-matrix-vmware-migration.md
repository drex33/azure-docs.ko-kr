---
title: Azure Migrate의 VMware 마이그레이션 지원
description: Azure Migrate의 VMware VM 마이그레이션 지원에 대해 알아보세요.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 6365499aff4508e40d8e2aa2ed61471f4fbff2fe
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721254"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)으로 VMware VM을 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약합니다. Azure로의 마이그레이션을 위해 VMware VM을 평가하는 방법에 대한 정보를 찾고 있는 경우 [평가 지원 매트릭스](migrate-support-matrix-vmware.md)를 검토하세요.


## <a name="migration-options"></a>마이그레이션 옵션

몇 가지 VMware VM 다운로드 방법이 있습니다.

- **에이전트 없는 마이그레이션 사용**: 다른 제품을 설치할 필요 없이 VM을 마이그레이션합니다. 에이전트 없는 마이그레이션을 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포합니다.
- **에이전트 기반 마이그레이션 사용**: 복제를 위해 VM에 에이전트를 설치합니다. 에이전트 기반 마이그레이션의 경우, [복제 어플라이언스](migrate-replication-appliance.md)를 배포합니다.

[이 문서](server-migrate-overview.md)를 검토하여 사용하려는 방법을 파악합니다.

## <a name="agentless-migration"></a>에이전트 없는 마이그레이션

이 섹션에서는 에이전트 없이 VMware VM을 Azure로 마이그레이션하는 데 필요한 요구 사항을 요약합니다.

### <a name="vmware-requirements-agentless"></a>VMware 요구 사항(에이전트가 없는 경우)

이 표에는 VMware 하이퍼바이저 요구 사항이 요약되어 있습니다.

**VMware** | **세부 정보**
--- | ---
**VMware vCenter Server** | 버전 5.5, 6.0, 6.5, 6.7, 7.0.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5, 6.7, 7.0.
**vCenter Server 권한** | 에이전트 없는 마이그레이션은 [마이그레이션 어플라이언스](migrate-appliance.md)를 사용합니다. 이 어플라이언스는 vCenter Server에서 다음 권한이 필요합니다.<br/><br/> - **Datastore.Browse** (데이터 저장소 -> 데이터 저장소 검색): 스냅샷 만들기와 삭제 문제를 해결하기 위해 VM 로그 파일의 검색을 허용합니다.<br/><br/> - **Datastore.FileManagement** (데이터 저장소 -> 낮은 수준 파일 작업): 데이터 저장소 브라우저에서 읽기/쓰기/삭제/이름 바꾸기 작업을 허용하여 스냅샷 만들기 및 삭제 문제를 해결합니다.<br/><br/> - **VirtualMachine.Config.ChangeTracking**(가상 머신 -> 디스크 변경 내용 추적): VM 디스크의 변경 내용 추적을 사용하거나 사용하지 않도록 설정하여 스냅샷 간에 변경된 데이터 블록을 끌어오도록 허용합니다.<br/><br/> - **VirtualMachine.Config.DiskLease**(가상 머신-> 디스크 임대): VMware vSphere VDDK(가상 디스크 개발 키트)를 사용하여 VM의 디스크 임대 작업에서 디스크를 읽도록 허용합니다.<br/><br/> - **VirtualMachine.Provisioning.DiskRandomRead**(가상 머신 -> 프로비저닝 -> 읽기 전용 디스크 액세스 허용): VM에서 디스크를 열어 VDDK를 사용하여 디스크를 읽도록 허용합니다.<br/><br/> - **VirtualMachine.Provisioning.DiskRandomAccess**(가상 머신 -> 프로비저닝 -> 디스크 액세스 허용): VM에서 디스크를 열어 VDDK를 사용하여 디스크를 읽도록 허용합니다.<br/><br/> - **VirtualMachine.Provisioning.GetVmFiles**(가상 머신 -> 프로비저닝 -> 가상 머신 다운로드 허용): VM과 연관된 파일에 대한 읽기 작업에서 로그를 다운로드하고 실패 발생 시 문제를 해결하도록 허용합니다.<br/><br/> - **VirtualMachine.State.\* *_ (가상 머신 -> 스냅샷 관리): 복제의 VM 스냅샷을 만들고 관리하도록 허용합니다.<br/><br/> - _* VirtualMachine.Interact.PowerOff** (가상 머신 -> 상호 작용 -> 전원 끄기): Azure로 마이그레이션하는 중 VM의 전원을 끄도록 허용합니다.



### <a name="vm-requirements-agentless"></a>VM 요구 사항(에이전트가 없는 경우)

이 표에는 VMware VM에 대한 에이전트 없는 마이그레이션 요구 사항이 요약되어 있습니다.

**지원** | **세부 정보**
--- | ---
**지원되는 운영 체제** | Azure에서 지원하는 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](../virtual-machines/linux/endorsed-distros.md) 운영 체제를 마이그레이션할 수 있습니다.
**Azure의 Windows VM** | 마이그레이션하기 전에 VM에서 [일부를 변경](prepare-for-migration.md#verify-required-changes-before-migrating)해야 할 수도 있습니다.
**Azure의 Linux VM** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다.<br/><br/> Linux의 경우, Azure Migrate는 다음 운영 체제에 대해 자동으로 변경합니다.<br/> - Red Hat Enterprise Linux  8, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x <br/> - Cent OS 8, 7.7, 7.6, 7.5, 7.4, 6.x</br> SUSE Linux Enterprise Server 15 SP0, 15 SP1, 12, 11 <br/>- Ubuntu 20.04, 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8, 9 <br/> Oracle Linux 6, 7.7, 7.7-CI<br/> 다른 운영 체제의 경우 [필요한 변경](prepare-for-migration.md#verify-required-changes-before-migrating) 작업을 수동으로 수행합니다.
**부팅 요구 사항** | /boot가 전용 파티션에 있는 경우, OS 디스크에 상주해야 하며 여러 디스크에 분산되면 안 됩니다.<br/> /boot가 루트(/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 포함되지 않아야 합니다.
**UEFI 부팅** | 지원됨. UEFI 기반 VM은 Azure 2세대 VM으로 마이그레이션됩니다.
**디스크 크기** | 1세대 VM 및 2세대 VM의 경우 최대 2TB OS 디스크, 데이터 디스크의 경우 32TB입니다.
**디스크 제한** |  VM당 최대 60 디스크
**암호화된 디스크/볼륨** | 암호화된 디스크/볼륨이 있는 VM은 마이그레이션을 지원하지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원되지 않습니다.
**RDM/통과 디스크** | VM에 RDM 또는 통과 디스크가 있는 경우 이러한 디스크는 Azure에 복제되지 않습니다.
**NFS** | VM에 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.
**iSCSI 대상** | iSCSI 대상을 사용하는 VM은 에이전트 없는 마이그레이션에 대해 지원되지 않습니다.
**다중 경로 IO** | 지원되지 않습니다.
**스토리지 vMotion** | 지원되지 않습니다. VM에서 스토리지 vMotion를 사용하는 경우 복제가 작동하지 않습니다.
**팀 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.
**대상 디스크** | VM은 Azure에서 관리 디스크(표준 HDD, 표준 SSD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**동시 복제** | 1개 어플라이언스를 사용하여 vCenter Server당 최대 300개의 VM을 동시에 복제합니다. 추가 [스케일 아웃 어플라이언스](./how-to-scale-out-for-migration.md)를 배포할 때 VCenter Server당 VM을 동시에 최대 500개까지 복제합니다.
**Azure VM 에이전트(Windows 및 Linux 에이전트)의 자동 설치** | Windows Server 2008 R2 이후부터 지원됩니다. <br/> RHEL6, RHEL7, CentOS7, Ubuntu 14.04, Ubuntu 16.04, Ubuntu18.04, Ubuntu 19.04, Ubuntu 19.10, Ubuntu 20.04에 대해 지원됩니다.

> [!Note]
> Linux VM의 경우 Microsoft Azure Linux 에이전트(waagent)를 성공적으로 설치하려면 인터넷 연결 외에, 다음 패키지가 설치되어 있는지 확인합니다.
>- Python 2.6 이상
>- Python-setuptool
>- systemmd
>- OpenSSL 1.0 이상
>- OpenSSH 5.3 이상
>- 파일 시스템 유틸리티: sfdisk, fdisk, mkfs, parted
>- 암호 도구: chpasswd, sudo
>- 텍스트 처리 도구: sed, grep
>- 네트워크 도구: ip-route
>- 원본 VM에서 rc.local 서비스 사용

> [!TIP]
>  Azure Portal를 사용하여 복제를 구성하기 위해 한 번에 최대 10개의 VM을 선택할 수 있습니다. 더 많은 VM을 복제하기 위해 포털을 사용하고 10개 VM의 여러 일괄 처리에서 복제하도록 VM을 추가하거나 Azure Migrate PowerShell 인터페이스를 사용하여 복제를 구성할 수 있습니다. 동시 복제를 위해 지원되는 최대 VM 수를 초과하 여 동시 복제를 구성하지 않도록 합니다.

### <a name="appliance-requirements-agentless"></a>어플라이언스 요구 사항(에이전트가 없는 경우)

에이전트 없는 마이그레이션은 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. vCenter Server로 가져온 OVA 템플릿을 사용하거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 VMWare VM으로 배포할 수 있습니다.

- VMware에 대한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware)에 대해 알아봅니다.
- 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- Azure Government에서는 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.

### <a name="port-requirements-agentless"></a>포트 요구 사항(에이전트가 없는 경우)

**디바이스** | **연결**
--- | ---
어플라이언스 | 포트 443의 아웃바운드 연결을 사용하여 복제된 데이터를 Azure에 업로드하고, Azure Migrate 서비스와 통신하여 복제와 마이그레이션을 오케스트레이션합니다.
vCenter Server | 어플라이언스에서 복제를 오케스트레이션할 수 있도록 허용하는 포트 443의 인바운드 연결 -스냅샷 만들기, 데이터 복사, 스냅샷 릴리스
vSphere/ESXI 호스트 | 어플라이언스의 TCP 포트 902에 대한 인바운드는 스냅샷에서 데이터를 복제합니다. ESXi 호스트의 아웃바운드 포트 902

## <a name="agent-based-migration"></a>에이전트 기반 마이그레이션


이 섹션에서는 에이전트 기반 마이그레이션의 요구 사항을 요약합니다.


### <a name="vmware-requirements-agent-based"></a>VMware 요구 사항(에이전트 기반)

이 표에는 VMware 가상화 서버에 대한 평가 지원 및 제한이 요약되어 있습니다.

**VMware 요구 사항** | **세부 정보**
--- | ---
**VMware vCenter Server** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7입니다.
**vCenter Server 권한** | VCenter Server에 대한 읽기 전용 계정입니다.

### <a name="vm-requirements-agent-based"></a>VM 요구 사항(에이전트 기반)

이 표에는 에이전트 기반 마이그레이션을 사용하여 마이그레이션하려는 VMware VM에 대한 VMware VM 지원이 요약되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate는 지원되는 머신에서 실행되는 워크로드(예: Active Directory, SQL Server)의 마이그레이션을 지원합니다.
**운영 체제** | 최신 정보는 Site Recovery에 대한 [운영 체제 지원](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)을 검토하세요. Azure Migrate는 동일한 VM 운영 체제를 지원합니다.
**Linux 파일 시스템/게스트 스토리지** | 최신 정보는 Site Recovery에 대한 [Linux 파일 시스템 지원](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)을 검토하세요. Azure Migrate는 동일한 Linux 파일 시스템을 지원합니다.
**네트워크/스토리지** | 최신 정보는 Site Recovery에 대한 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [스토리지](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 요소를 검토하세요. Azure Migrate는 동일한 네트워크/스토리지 요구 사항을 제공합니다.
**Azure 요구 사항** | 최신 정보는 Site Recovery에 대한 [Azure 네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [스토리지](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) 및 [컴퓨팅](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 요구 사항을 검토하세요. Azure Migrate는 VMware 마이그레이션에 대한 요구 사항이 동일합니다.
**모바일 서비스** | 마이그레이션하려는 각 VM에 모바일 서비스 에이전트가 설치되어 있어야 합니다.
**UEFI 부팅** | 지원됨. UEFI 기반 VM은 Azure 2세대 VM으로 마이그레이션됩니다.
**UEFI - 보안 부팅**         | 마이그레이션에 지원되지 않습니다.
**대상 디스크** | VM은 Azure에서 관리 디스크(표준 HDD, 표준 SSD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 1세대 VM의 경우 최대 2TB OS 디스크, 2세대 VM의 경우 최대 4TB OS 디스크, 데이터 디스크의 경우 32TB입니다.
**디스크 제한** |  VM당 최대 63개의 디스크
**암호화된 디스크/볼륨** | 암호화된 디스크/볼륨이 있는 VM은 마이그레이션을 지원하지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원됨.
**통과 디스크** | 지원됨.
**NFS** | VM에 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.
**iSCSI 대상** | 지원됨.
**다중 경로 IO** | 지원되지 않습니다.
**스토리지 vMotion** | 지원됨
**팀 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.




### <a name="appliance-requirements-agent-based"></a>어플라이언스 요구 사항(에이전트 기반)

Azure Migrate 허브에 제공된 OVA 템플릿을 사용하여 복제 어플라이언스를 설정하는 경우 어플라이언스는 Windows Server 2016을 실행하고 지원 요구 사항을 준수합니다. 실제 서버에서 복제 어플라이언스를 수동으로 설정하는 경우, 요구 사항에 부합하는지 확인하세요.

- VMware에 대한 [복제 어플라이언스 요구 사항](migrate-replication-appliance.md#appliance-requirements)에 대해 알아봅니다.
- MySQL은 어플라이언스에 설치되어야 합니다. [설치 옵션](migrate-replication-appliance.md#mysql-installation)에 대해 알아봅니다.
- 복제 어플라이언스가 [퍼블릭](migrate-replication-appliance.md#url-access) 및 [정부](migrate-replication-appliance.md#azure-government-url-access) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- 복제 어플라이언스 머신에서 액세스해야 하는 [포트](migrate-replication-appliance.md#port-access)를 검토하세요.

### <a name="port-requirements-agent-based"></a>포트 요구 사항(에이전트 기반)

**디바이스** | **연결**
--- | ---
VM | VM에서 실행되는 Mobility Service는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스(구성 서버)와 통신합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃바운드 포트에서 Azure와 복제를 오케스트레이션합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고, 이를 최적화 및 암호화하며, 443 아웃바운드 포트를 통해 Azure Storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행됩니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure에 복제되는 모든 온-프레미스 VM(에이전트 없는 마이그레이션 또는 에이전트 기반 마이그레이션 사용)은 이 표에 요약된 Azure VM 요구 사항을 충족해야 합니다.

**구성 요소** | **Requirements**
--- | --- | ---
게스트 운영 체제 | 마이그레이션에 지원되는 VMware VM 운영 체제를 확인합니다.<br/> 지원되는 운영 체제에서 실행되는 모든 워크로드를 마이그레이션할 수 있습니다.
게스트 운영 체제 아키텍처 | 64비트.
운영 체제 디스크 크기 | 최대 2,048GB.
운영 체제 디스크 수 | 1
데이터 디스크 수 | 64개 이하.
데이터 디스크 크기 | 최대 32TB.
네트워크 어댑터 | 여러 어댑터가 지원됩니다.
공유 VHD | 지원되지 않습니다.
FC 디스크 | 지원되지 않습니다.
BitLocker | 지원되지 않습니다.<br/><br/> 머신을 마이그레이션하기 전에 BitLocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행하는 Azure VM에 연결하려면:<br/><br/> - 마이그레이션하기 전에 온-프레미스 VM에서 RDP를 사용하도록 설정합니다.<br/><br/> **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱** 에서 모든 프로필에 대해 허용되는지 확인합니다.<br/><br/> 사이트 간 VPN 액세스의 경우, RDP를 사용하도록 설정하고, **도메인 및 프라이빗** 네트워크에 대하여 **Windows 방화벽** -> **허용되는 앱과 기능** 에서 RDP를 허용합니다.<br/><br/> 운영 체제의 SAN 정책이 **OnlineAll** 로 설정되어 있는지 확인합니다. [자세히 알아보기](prepare-for-migration.md).
마이그레이션 후 연결-Linux | 마이그레이션한 후 SSH를 사용하여 Azure VM에 연결해야 합니다.<br/><br/> 마이그레이션 전에 온-프레미스 머신에서 Secure Shell 서비스가 시작으로 설정되어 있고 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.<br/><br/> 장애 조치(failover) 후 Azure VM에서 장애 조치된 VM의 네트워크 보안 그룹 규칙 및 연결되어 있는 Azure 서브넷에 대한 SSH 포트로 들어오는 연결을 허용합니다.<br/><br/> 또한, VM에 대한 공용 IP 주소를 추가하세요.  


## <a name="next-steps"></a>다음 단계

VMware 마이그레이션 옵션을 [선택](server-migrate-overview.md)하세요.
