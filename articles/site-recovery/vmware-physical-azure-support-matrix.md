---
title: Azure Site Recovery의 VMware/물리적 재해 복구를 위한 지원 매트릭스
description: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 지원이 요약되어 있습니다.
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 084e0b9b0b42bbe2ba3632d54811e738197899f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441479"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM 또는 물리적 서버와 Azure 간 재해 복구를 위한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM과 물리적 서버를 Azure로 재해 복구하는 데 지원되는 구성 요소와 설정의 요약 정보를 제공합니다.

- VMware VM/물리적 서버 재해 복구 아키텍처에 대해 [자세히 알아보세요](vmware-azure-architecture.md).
- 재해 복구를 시도하려면 [자습서](tutorial-prepare-azure.md)를 따르세요.

> [!NOTE]
> Site Recovery는 원본 머신에 대한 재해 복구가 설정된 대상 지역에서 고객 데이터를 이동하거나 저장하지 않습니다. 고객이 원하는 경우 다른 지역에서 Recovery Services 자격 증명 모음을 선택할 수 있습니다. Recovery Services 자격 증명 모음에는 메타데이터가 포함되지만, 실제 고객 데이터는 없습니다.

## <a name="deployment-scenarios"></a>배포 시나리오

**시나리오** | **세부 정보**
--- | ---
VMware VM의 재해 복구 | 온-프레미스 VMware VM을 Azure로 복제. Azure Portal에서 또는 [PowerShell](vmware-azure-disaster-recovery-powershell.md)을 사용하여 이 시나리오를 배포할 수 있습니다.
물리적 서버의 재해 복구 | 온-프레미스 Windows/Linux 실제 서버를 Azure로 복제 Azure Portal에서 이 시나리오를 배포할 수 있습니다. (미리 보기 아키텍처에는 지원되지 않음)

## <a name="on-premises-virtualization-servers"></a>온-프레미스 가상화 서버

**서버** | **요구 사항** | **세부 정보**
--- | --- | ---
vCenter Server | 버전 7.0 및 해당 버전의 후속 업데이트, 6.7, 6.5, 6.0 또는 5.5 | 재해 복구 배포에 vCenter 서버를 사용하는 것이 좋습니다.
vSphere 호스트 | 버전 7.0 및 해당 버전의 후속 업데이트, 6.7, 6.5, 6.0 또는 5.5 | vSphere 호스트와 vCenter 서버가 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 기본적으로 프로세스 서버는 구성 서버에서 실행됩니다. [자세히 알아봅니다](vmware-physical-azure-config-process-server-overview.md).

## <a name="site-recovery-configuration-server"></a>Site Recovery 구성 서버

구성 서버는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하여 Site Recovery 구성 요소를 실행하는 온-프레미스 컴퓨터입니다.

- VMware VM의 경우에는 OVF 템플릿을 다운로드하여 구성 서버를 설정하고 VMware VM을 만듭니다.
- 물리적 서버의 경우 구성 서버 머신을 수동으로 설정합니다.

**구성 요소** | **Requirements**
--- |---
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3개의 디스크<br/><br/> 디스크에는 OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용)가 포함됩니다.
사용 가능한 디스크 공간 | 프로세스 서버 캐시의 경우 600GB 공간
사용 가능한 디스크 공간 | 보존 드라이브의 경우 600GB 공간
운영 체제  | Windows Server 2012 R2 또는 데스크톱 환경의 Windows Server 2016 <br/><br> 장애 복구(failback)를 위해 해당 어플라이언스의 기본 제공 마스터 대상을 사용하려는 계획인 경우 OS 버전이 복제된 항목과 같거나 그 이상인지 확인해야 합니다.|
운영 체제 로케일 | 미국 영어(en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 구성 서버 버전 [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 이상에서는 필요하지 않습니다.
Windows Server 역할 | Active Directory Domain Services 사용하지 않음, IIS(인터넷 정보 서비스) 또는 Hyper-V
그룹 정책| - 명령 프롬프트에 대한 액세스 방지 <br/> - 레지스트리 편집 도구에 대한 액세스 방지 <br/> - 파일 첨부를 위한 트러스트 논리 <br/> - 스크립트 실행 켜기 <br/> - [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | 다음을 확인합니다.<br/><br/> - 기존의 기본 웹 사이트 없음 <br/> - [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br/> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용  <br/> - 포트 443에서 수신 대기하는 기존의 웹 사이트/앱 없음<br/>
NIC 유형 | VMXNET3(VMware VM으로 배포될 경우)
IP 주소 유형 | 정적
포트 | 컨트롤 채널 오케스트레이션에 443 사용<br/>데이터 전송의 경우 9443

> [!NOTE]
> 운영 체제는 영어 로캘로 설치해야 합니다. 설치 후에 로캘을 변환하면 잠재적인 문제가 발생할 수 있습니다.



## <a name="replicated-machines"></a>복제된 컴퓨터

미리보기에서 복제는 Azure Site Recovery 복제 어플라이언스에 의해 수행됩니다. 복제 어플라이언스에 대한 자세한 내용은 [이 문서](deploy-vmware-azure-replication-appliance-preview.md)를 참조하세요.

Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다.

**구성 요소** | **세부 정보**
--- | ---
컴퓨터 설정 | Azure로 복제하는 컴퓨터는 [Azure 요구 사항](#azure-vm-requirements)을 충족해야 합니다.
머신 워크로드 | Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다. [자세히 알아봅니다](./site-recovery-workload.md).
머신 이름 | 머신의 표시 이름이 [Azure 예약 리소스 이름](../azure-resource-manager/templates/error-reserved-resource-name.md)에 포함되지 않는지 확인합니다.<br/><br/> 논리 볼륨 이름은 대/소문자를 구분하지 않습니다. 디바이스에 있는 두 개의 볼륨이 동일한 이름을 가지고 있지 않도록 해야 합니다. 예: 이름이 “voLUME1”, “volume1”인 볼륨은 Azure Site Recovery를 통해 보호할 수 없습니다.

### <a name="for-windows"></a>Windows의 경우

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | [업데이트 롤업 34](https://support.microsoft.com/help/4490016)(Mobility Service 버전 9.22)부터 지원됩니다.
Windows Server 2016 64비트 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2 / Windows Server 2012 | 지원됨.
Windows Server 2008 R2 SP1 이상 | 지원됨.<br/><br/> Mobility Service 에이전트의 [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터는 Windows 2008 R2 SP1 및 이후 버전을 실행하는 머신에서 [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다. SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)에 대해 자세히 알아보세요.
Windows Server 2008 SP2 이상(64비트/32비트) |  마이그레이션에만 지원됩니다. [자세히 알아봅니다](migrate-tutorial-windows-server-2008.md).<br/><br/> Mobility Service 에이전트의 [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터는 Windows 2008 SP2 머신에서 [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4493730) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다. SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)에 대해 자세히 알아보세요.
Windows 10, Windows 8.1, Windows 8 | 64비트 시스템만 지원됩니다. 32비트 시스템은 지원되지 않습니다.
Windows 7 SP1 64비트 | [업데이트 롤업 36](https://support.microsoft.com/help/4503156)(Mobility Service 버전 9.22)부터 지원됩니다. </br></br> Mobility Service 에이전트의 [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터는 Windows 7 SP1 머신에서 [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)에 대해 자세히 알아보세요.

### <a name="for-linux"></a>Linux의 경우

**운영 체제** | **세부 정보**
--- | ---
Linux | 64비트 시스템만 지원됩니다. 32비트 시스템은 지원되지 않습니다.<br/><br/>모든 Linux 서버에는 [LIS(Linux Integration Services) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 설치되어 있어야 합니다. 테스트 장애 조치/장애 조치(failover) 후 Azure에서 서버를 부팅해야 합니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅할 머신에 복제를 사용하도록 설정하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106)를 설치해야 합니다. <br/><br/> Site Recovery는 Azure에서 Linux 서버를 실행하도록 장애 조치(failover)를 오케스트레이션합니다. 그러나 Linux 공급 업체 지원 수명 종료에 해당하지 않는 배포 버전만으로 제한될 수 있습니다.<br/><br/> Linux 배포에서는 배포의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.<br/><br/> 주요 Linux 배포 버전에서 보호된 시스템을 업그레이드하는 것은 지원되지 않습니다. 업그레이드하려면 복제를 사용하지 않도록 설정하고, 운영 체제를 업그레이드한 다음, 복제를 다시 사용하도록 설정합니다.<br/><br/> Azure에서 Linux 및 오픈 소스 기술 지원에 대해 [자세히 알아보세요](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).
Linux Red Hat Enterprise | 5.2~5.11</b><br/> 6.1~6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9 베타 버전](https://support.microsoft.com/help/4578241/), [7.9](https://support.microsoft.com/help/4590304/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609), [8.3](https://support.microsoft.com/help/4597409/) <br/> Red Hat Enterprise Linux 5.2~5.11 및 6.1~6.10을 실행하는 서버의 일부 이전 커널에는 [LIS(Linux Integration Services) 구성 요소](https://www.microsoft.com/download/details.aspx?id=55106)가 미리 설치되어 있지 않습니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅할 머신에 복제를 사용하도록 설정하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106)를 설치해야 합니다.
Linux: CentOS | 5.2~5.11</b><br/> 6.1~6.10</b><br/> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609), [8.3](https://support.microsoft.com/help/4597409/) <br/><br/> CentOS 5.2~5.11 및 6.1~6.10을 실행하는 서버의 일부 이전 커널에는 [LIS(Linux Integration Services) 구성 요소](https://www.microsoft.com/download/details.aspx?id=55106)가 미리 설치되어 있지 않습니다. 빌드된 LIS 구성 요소가 없는 경우 Azure에서 부팅할 머신에 복제를 사용하도록 설정하기 전에 [구성 요소](https://www.microsoft.com/download/details.aspx?id=55106)를 설치해야 합니다.
Ubuntu | Ubuntu 14.04* LTS 서버[(지원되는 커널 버전 검토)](#ubuntu-kernel-versions)<br/>Ubuntu 16.04* LTS 서버[(지원되는 커널 버전 검토)](#ubuntu-kernel-versions) </br> Ubuntu 18.04* LTS 서버[(지원되는 커널 버전 검토)](#ubuntu-kernel-versions) </br> Ubuntu 20.04* LTS 서버[(지원되는 커널 버전 검토)](#ubuntu-kernel-versions) </br> (*14.04.* x *, 16.04.* x *, 18.04.* x *, 20.04.* x* 버전 모두에 대한 지원 포함)
Debian | Debian 7/Debian 8(7. *x*, 8. *x* 버전 모두에 대한 지원 포함), Debian 9(9.1~9.13에 대한 지원 포함, Debian 9.0 미지원), Debian 10[(지원되는 커널 버전 검토)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(지원되는 커널 버전 검토)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1[(지원되는 커널 버전 검토)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [구성 서버에서 최신 모바일 에이전트 설치 관리자를 다운로드해야 합니다](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> SUSE Linux Enterprise Server 11 SP4 </br> **참고**: 복제된 머신을 SUSE Linux Enterprise Server 11 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 업그레이드하려면 복제를 사용하지 않도록 설정하고, 업그레이드 후에 다시 사용하도록 설정합니다. <br/>|
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409/), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/), [8.2](https://support.microsoft.com/topic/b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [8.3](https://support.microsoft.com/topic/b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)  <br/> Red Hat 호환 커널 또는 UEK(Unbreakable Enterprise Kernel Release) 3, 4 및 5를 실행<br/><br/>8.1<br/>모든 UEK 커널 및 3.10.0-1062.* 이하 버전의 RedHat 커널에서의 실행은 [9.35](https://support.microsoft.com/help/4573888/)에서 지원되며 나머지 RedHat 커널은 [9.36](https://support.microsoft.com/help/4578241/)에서 지원됩니다.

> [!Note]
>- 각 Windows 버전에 대해 Azure Site Recovery는 [LTSC(장기 서비스 채널)](/windows-server/get-started/servicing-channels-comparison#long-term-servicing-channel-ltsc) 빌드만 지원합니다.  현재는 [반기 채널](/windows-server/get-started/servicing-channels-comparison#semi-annual-channel) 릴리스가 지원되지 않습니다.
>- Linux 버전의 경우 Azure Site Recovery에서 사용자 지정된 OS 이미지를 지원하지 않는다는 점을 확인하시기 바랍니다. 배포의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.

### <a name="ubuntu-kernel-versions"></a>Ubuntu 커널 버전

**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6), [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic에서 4.4.0-201-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-133-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1106-azure|
16.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic에서 4.4.0-197-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-128-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1102-azure |
|||
18.04 LTS |[9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 4.15.0-1123-azure </br> 5.4.0-1058-azure </br> 4.15.0-156-generic |
18.04 LTS | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic </br> 4.15.0-153-generic </br> 5.4.0-1056-azure </br> 5.4.0-81-generic </br> 4.15.0-1122-azure </br> 4.15.0-154-generic |
18.04 LTS | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic |
18.04 LTS |[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
18.04 LTS | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic에서 4.15.0-135-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic에서 5.3.0-70-generic </br> 5.4.0-37-generic에서 5.4.0-59-generic</br> 5.4.0-60-generic에서 5.4.0-65-generic </br> 4.15.0-1009-azure에서 4.15.0-1106-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure에서 5.4.0-1039-azure|
18.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic에서 4.15.0-129-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-63-generic </br> 5.3.0-19-generic에서 5.3.0-69-generic </br> 5.4.0-37-generic에서 5.4.0-59-generic</br> 4.15.0-1009-azure에서 4.15.0-1103-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure에서 5.4.0-1035-azure|
|||
20.04 LTS |[9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 5.4.0-1058-azure </br> 5.4.0-84-generic |
20.04 LTS |[9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 5.4.0-26-generic ~ 5.4.0-80 </br> 5.4.0-1010-azure~5.4.0-1048-azure </br> 5.4.0-81-generic </br> 5.4.0-1056-azure |
20.04 LTS |[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 5.4.0-26-generic ~ 5.4.0-80 </br> 5.4.0-1010-azure~5.4.0-1048-azure |
20.04 LTS |[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)| 5.4.0-26-generic ~ 5.4.0-60-generic </br> -generic 5.4.0-1010-azure ~ 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
20.04 LTS |[9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic에서 5.4.0-65 </br> -generic 5.4.0-1010-azure에서 5.4.0-1039-azure |
20.04 LTS |[9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic에서 5.4.0-59 </br> -generic 5.4.0-1010-azure에서 5.4.0-1035-azure |

**참고: Ubuntu 20.04의 경우 처음에 커널 5.8에 대한 지원을 롤아웃했습니다.* 하지만 이후 이 커널에 대한 지원과 관련된 문제를 발견했으므로 해당 시간 동안 지원 문서에서 이러한 커널을 수정했습니다.

### <a name="debian-kernel-versions"></a>Debian 커널 버전


**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8),[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6), [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6), [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.16.0-4-amd64에서 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64 </br>
Debian 9.1 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64 </br>
Debian 9.1 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64 </br>
Debian 9.1 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64에서 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64에서 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64에서 4.19.0-0.bpo.14-cloud-amd64 </br>
Debian 9.1 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64에서 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64에서 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64에서 4.19.0-0.bpo.13-cloud-amd64 </br>
|||
Debian 10 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.19.0-6-amd64 to 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.19.0-6-amd64 to 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.19.0-6-amd64 to 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-6-amd64 to 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64에서 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-6-amd64 to 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64에서 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 지원되는 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.65-azure </br> 4.12.14-16.68-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.65-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.56-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure에서 4.12.14-16.44-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure에서 4.12.14-16.38-azure|

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 지원 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.47-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure에서 5.3.18-18.35-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure에서 5.3.18-18.29-azure

## <a name="linux-file-systemsguest-storage"></a>Linux 파일 시스템/게스트 스토리지

**구성 요소** | **지원됨**
--- | ---
파일 시스템 | ext3, ext4, XFS, BTRFS(해당 테이블에 따라 조건 적용)
LVM(논리 볼륨 관리) 프로비저닝| 씩 프로비전 - 예 <br></br> 씬 프로비전 - 아니요
볼륨 관리자 | - LVM이 지원됩니다.<br/> - LVM의/boot는 [업데이트 롤업 31](https://support.microsoft.com/help/4478871/)(Mobility Service 버전 9.20)부터 지원됩니다. 이전 Mobility Service 버전에서는 지원되지 않습니다.<br/> - 여러 OS 디스크는 지원되지 않습니다.
반가상화 스토리지 디바이스 | 반가상화 드라이버에서 내보낸 디바이스는 지원되지 않습니다.
다중 큐 블록 IO 디바이스 | 지원되지 않습니다.
HP CCISS 스토리지 컨트롤러가 있는 물리적 서버 | 지원되지 않습니다.
디바이스/탑재 지점 명명 규칙 | 디바이스 이름과 탑재 지점 이름은 고유해야 합니다.<br/> 대/소문자만 다른 두 개의 디바이스/탑재 지점 이름이 없어야 합니다. 예를 들어 동일한 VM의 두 디바이스에 *device1* 과 *Device1* 로 이름을 지정하는 것은 지원되지 않습니다.
디렉터리 | 버전 9.20([업데이트 롤업 31](https://support.microsoft.com/help/4478871/)에서 릴리스) 이전 버전의 Mobility Service를 실행하는 경우 다음 제한이 적용됩니다.<br/><br/> - /(root), /boot, /usr, /usr/local, /var, /etc 디렉터리(별도의 파티션/파일 시스템으로 설정된 경우)는 원본 서버의 동일한 OS 디스크에 있어야 합니다.</br> - /boot 디렉터리는 디스크 파티션에 있어야 하며 LVM 볼륨이 아니어야 합니다.<br/><br/> 버전 9.20부터는 이러한 제한이 적용되지 않습니다.
루트 디렉터리 | - 부팅 디스크는 GPT 파티션 형식이 아니어야 합니다. 이것은 Azure 아키텍처의 제한 사항입니다. GPT 디스크는 데이터 디스크로 지원됩니다.<br/><br/> VM의 여러 부팅 디스크는 지원되지 않습니다.<br/><br/> - 두 개 이상의 디스크에 걸쳐 있는 LVM 볼륨의 /boot는 지원되지 않습니다.<br/> - 부팅 디스크가 없는 머신은 복제할 수 없습니다.
사용 가능한 공간 요구 사항| /root 파티션: 2GB <br/><br/> 설치 폴더: 250MB
XFSv5 | XFS 파일 시스템의 XFSv5 기능(예: 메타데이터 체크섬)이 지원됩니다(Mobility Service 버전 9.10 이상).<br/> xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인합니다. `ftype`이 1로 설정되면 XFSv5 기능이 사용 중입니다.
BTRFS | BTRFS는 [업데이트 롤업 34](https://support.microsoft.com/help/4490016)(Mobility Service 버전 9.22)부터 지원됩니다. 다음의 경우 BTRFS가 지원되지 않습니다.<br/><br/> - 보호를 사용하도록 설정한 후에 BTRFS 파일 시스템 하위 볼륨이 변경됨</br> - BTRFS 파일 시스템이 여러 디스크에 분산됨</br> - BTRFS 파일 시스템이 RAID를 지원함

## <a name="vmdisk-management"></a>VM/디스크 관리

**동작** | **세부 정보**
--- | ---
복제 된 VM에서 디스크 크기 조정 (미리 보기 아키텍처에는 지원되지 않음)| 원본 VM의 크기를 조정 하는 작업이 지원 됩니다. 원본 VM에서 크기를 조정 하는 것은 지원 되지 않습니다. 크기 조정은 VM 속성에서 직접 장애 조치 (failover) 전에 수행 해야 합니다. 복제를 해제/다시 설정할 필요가 없습니다.<br/><br/> 장애 조치(failover) 후 원본 VM을 변경하면 변경 내용이 캡처되지 않습니다.<br/><br/> 장애 조치(failover) 후 Azure VM에서 디스크 크기를 변경하는 경우 장애 복구(failback)를 수행하면 Site Recovery가 업데이트를 사용하여 새 VM을 만듭니다.
복제된 VM에 디스크 추가 | 지원되지 않습니다.<br/> VM에 대한 복제를 사용하지 않도록 설정하고, 디스크를 추가한 다음, 복제를 다시 사용하도록 설정합니다.

> [!NOTE]
> 디스크 ID를 변경하는 것은 지원되지 않습니다. 예를 들어 디스크 분할이 GPT에서 MBR로 또는 그 반대로 변경된 경우 디스크 ID가 변경됩니다. 해당 시나리오에서는 복제가 중단되고 새로 설치해야 합니다.
> Linux 머신의 경우 디스크 ID에 영향을 주기 때문에 디바이스 이름 변경이 지원되지 않습니다.
> 미리 보기에서 디스크 크기를 원래 크기에서 줄이기 위해 크기를 조정하는 것은 지원되지 않습니다.

## <a name="network"></a>네트워크

**구성 요소** | **지원됨**
--- | ---
호스트 네트워크 NIC 팀 | VMware VM에서 지원됩니다. <br/><br/>물리적 컴퓨터 복제에 지원되지 않습니다.
호스트 네트워크 VLAN | 예.
호스트 네트워크 IPv4 | 예.
호스트 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 NIC 팀 | 아니요.
게스트/서버 네트워크 IPv4 | 예.
게스트/서버 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 정적 IP(Windows) | 예.
게스트/서버 네트워크 정적 IP(Linux) | 예. <br/><br/>VM이 장애 복구(Failback) 시 DHCP를 사용하도록 구성되어 있습니다.
게스트/서버 네트워크 다중 NIC | 예.
Site Recovery 서비스에 대한 프라이빗 링크 액세스 | 예. [자세히 알아보기](hybrid-how-to-enable-replication-private-endpoints.md). (미리 보기 아키텍처에는 지원되지 않음)


## <a name="azure-vm-network-after-failover"></a>Azure VM 네트워크(장애 조치(failover) 후)

**구성 요소** | **지원됨**
--- | ---
Azure ExpressRoute | 예
ILB | 예
ELB | 예
Azure Traffic Manager | 예
다중 NIC | 예
예약된 IP 주소 | 예
IPv4 | 예
원본 IP 주소 유지 | 예
Azure 가상 네트워크 서비스 엔드포인트<br/> | 예
가속화된 네트워킹 | 예

## <a name="storage"></a>스토리지
**구성 요소** | **지원됨**
--- | ---
동적 디스크 | OS 디스크는 기본 디스크여야 합니다. <br/><br/>데이터 디스크는 동적 디스크일 수 있습니다.
Docker 디스크 구성 | 예
호스트 NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요
호스트 SAN(iSCSI/FC) | 예
호스트 vSAN | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
호스트 다중 경로(MPIO) | 예. 테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
호스트 가상 볼륨(VVol) | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 VMDK | 예
게스트/서버 공유 클러스터 디스크 | 예
게스트/서버 암호화된 디스크 | 예
FIPS 암호화 | 예
게스트/서버 NFS | 예
게스트/서버 iSCSI | 마이그레이션의 경우 - 예<br/>재해 복구의 경우 - 아니요, iSCSI는 VM에 연결된 디스크로 장애 복구(failback)합니다.
게스트/서버 SMB 3.0 | 예
게스트/서버 RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 디스크 > 1 TB | 예, 디스크는 1,024MB보다 커야 합니다.<br/><br/>관리 디스크(9.41 버전 이상)에 복제하는 경우 최대 32,767GB<br></br> 스토리지 계정에 복제하는 경우 최대 4,095GB
4K 논리적 및 4k 물리적 섹터 크기 포함 게스트/서버 디스크 | 예
4K 논리적 및 512바이트 물리적 섹터 크기 포함 게스트/서버 디스크 | 예
스트라이프 디스크 포함 게스트/서버 볼륨 4TB 이상 | 예
논리 볼륨 관리(LVM)| 씩 프로비저닝 - 예 <br></br> 씬 프로비저닝 - 아니요
게스트/서버 - 스토리지 공간 | 예
게스트/서버 - NVMe 인터페이스 | 예
게스트/서버 디스크 핫 추가/제거 | 예
게스트/서버 - 디스크 제외 | 예
게스트/서버 다중 경로(MPIO) | 예
게스트/서버 GPT 파티션 | 5개의 파티션은 [업데이트 롤업 37](https://support.microsoft.com/help/4508614/)(Mobility Service 버전 9.25)부터 지원됩니다. 이전 버전에서는 4개가 지원되었습니다.
ReFS | 복원 파일 시스템은 Mobility Service 버전 9.23 이상에서 지원됩니다.
게스트/서버 EFI/UEFI 부팅 | - Site Recovery 모바일 에이전트 9.30 이후 버전이 포함된 모든 [Azure Marketplace UEFI 운영 체제](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace)에 대해 지원됩니다. <br/> - 보안 UEFI 부팅 유형이 지원되지 않습니다. [자세한 정보](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)
RAID 디스크| 예

## <a name="replication-channels"></a>복제 채널

|**복제 유형**   |**지원됨**  |
|---------|---------|
|ODX(오프로드된 데이터 전송)    |       예  |
|오프라인 시드        |   예      |
| Azure Data Box | 예

## <a name="azure-storage"></a>Azure Storage

**구성 요소** | **지원됨**
--- | ---
로컬 중복 스토리지 | 예
지역 중복 스토리지 | 예
읽기 액세스 지역 중복 스토리지 | 예
쿨 스토리지 | 예
핫 스토리지| 예
블록 Blob | 예
미사용 암호화(SSE)| 예
미사용 암호화(CMK)| 예(PowerShell Az 3.3.0 모듈 이상의 버전으로)
미사용 이중 암호화 | 예(PowerShell Az 3.3.0 모듈 이상의 버전으로) [Windows](../virtual-machines/disk-encryption.md) 및 [Linux](../virtual-machines/disk-encryption.md)를 지원하는 지역에 대해 자세히 알아보세요.
Premium Storage | 예
보안 전송 옵션 | 예
Import/Export 서비스 | 예
VNet용 Azure Storage 방화벽 | 예.<br/> 대상 스토리지/캐시 스토리지 계정에 구성됨(복제 데이터 저장에 사용)
범용 v2 스토리지 계정(핫 및 쿨 계층) | 예(V1에 비해 V2의 트랜잭션 비용이 상당히 높음)

## <a name="azure-compute"></a>Azure Compute

**기능** | **지원됨**
--- | ---
가용성 집합 | 예. (미리 보기 아키텍처에는 지원되지 않음)
가용성 영역 | 예
HUB | 예
관리 디스크 | 예

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제된 온-프레미스 VM은 이 표에 요약된 Azure VM 요구 사항을 충족해야 합니다. Site Recovery에서 복제에 대한 필수 구성 요소 검사를 실행할 때 일부 요구 사항이 충족되지 않으면 실패합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 복제된 컴퓨터에 대해 [지원되는 운영 체제](#replicated-machines)를 확인합니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 1세대 머신의 경우 최대 2,048GB <br> 2세대 머신의 경우 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 </br> 서로 다른 디스크의 부팅 및 시스템 파티션이 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 관리 디스크(9.41 버전 이상)에 복제하는 경우 최대 32,767GB<br> 스토리지 계정으로 복제하는 경우 최대 4,095GB </br> 최소 디스크 크기 요구 사항 - 1,024MB 이상 </br> Preview 아키텍처는 최대 8TB의 디스크를 지원합니다.  | 지원되지 않는 경우 확인이 실패합니다.
RAM | ASR 드라이버는 RAM의 6%를 사용합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다. |
VM 이름 | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.

## <a name="resource-group-limits"></a>리소스 그룹 제한

단일 리소스 그룹에서 보호할 수 있는 가상 머신의 수를 확인하려면 [구독 한도 및 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)에 대한 문서를 참조하세요.

## <a name="churn-limits"></a>변동 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다.
- 해당 한도는 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 포함하지는 않습니다.
- 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.
- 최상의 결과를 얻으려면 [Deployment Planner 도구](site-recovery-deployment-planner.md)를 실행하고 테스트 장애 조치(failover)를 사용하여 광범위한 애플리케이션 테스트를 수행한 뒤에 애플리케이션의 실제 성능을 파악하는 것이 좋습니다.

**복제 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB


**원본 데이터 변동률** | **최대 제한**
---|---
VM의 모든 디스크에 대한 최고 데이터 변동률 | 54MB/초
프로세스 서버에서 지원하는 1일 최대 데이터 변동률 | 2TB

- 여기서는 I/O가 30% 겹치고 있다고 가정하는 평균 숫자입니다.
- Site Recovery는 중첩 비율, 더 큰 쓰기 크기 및 실제 워크로드 I/O 동작에 따라 더 높은 처리량을 다룰 수 있습니다.
- 해당 숫자는 약 5분의 일반적인 백로그를 가정합니다. 즉, 데이터를 업로드한 후에 처리되며 5분 내에 복구 지점이 생성됩니다.

## <a name="storage-account-limits"></a>Storage 계정 제한

디스크의 평균 변동이 늘어나면 스토리지 계정이 지원할 수 있는 디스크 수가 줄어듭니다. 프로비저닝해야 하는 스토리지 계정의 수에 대한 결정을 내리는 데 아래 표를 지침으로 사용할 수 있습니다.

**스토리지 계정 유형**    |    **변동 = 디스크당 4MBps**    |    **변동 = 디스크당 8MBps**
---    |    ---    |    ---
V1 스토리지 계정    |    600개의 디스크    |    300개의 디스크
V2 스토리지 계정    |    1,500개의 디스크    |    750개의 디스크

위의 제한은 하이브리드 DR 시나리오에만 적용됩니다.

## <a name="vault-tasks"></a>자격 증명 모음 작업

**동작** | **지원됨**
--- | ---
리소스 그룹 간 자격 증명 모음 이동 | 예
구독 내 및 구독 간 자격 증명 모음 이동 | 예
스토리지 그룹 간 스토리지, 네트워크, Azure VM 이동 | 예
스토리지, 네트워크, Azure VM을 구독 내 및 구독 간에 이동합니다. | 예


## <a name="obtain-latest-components"></a>최신 구성 요소 가져오기

**이름** | **설명** | **세부 정보**
--- | --- | ---
구성 서버 | 온-프레미스에 설치됩니다.<br/> 온-프레미스 VMware 서버 또는 물리적 서버와 Azure 간 통신을 조정합니다. | - 구성 서버에 대해 [자세히 알아봅니다](vmware-physical-azure-config-process-server-overview.md).<br/> - 최신 버전으로 업그레이드하는 방법에 대해 [자세히 알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).<br/> - 구성 서버를 설정하는 방법에 대해 [자세히 알아봅니다](vmware-azure-deploy-configuration-server.md).
프로세스 서버 | 기본적으로 구성 서버에 설치합니다.<br/> 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure로 전송합니다.<br/> 배포가 늘어나면 프로세스 서버를 추가하여 더 큰 복제 트래픽을 처리할 수 있습니다. | - 프로세스 서버에 대해 [자세히 알아봅니다](vmware-physical-azure-config-process-server-overview.md).<br/> - 최신 버전으로 업그레이드하는 방법에 대해 [자세히 알아봅니다](vmware-azure-manage-process-server.md#upgrade-a-process-server).<br/> - 스케일 아웃 프로세스 서버를 설정하는 방법에 대해 [자세히 알아봅니다](vmware-physical-large-deployment.md#set-up-a-process-server).
Mobility Service | 복제하려는 VMware VM 또는 물리적 서버에 설치되어 있습니다.<br/> 온-프레미스 VMware 서버/물리적 서버와 Azure 간의 복제를 조정합니다.| - Mobility Service에 대해[자세히 알아봅니다](vmware-physical-mobility-service-overview.md).<br/> - 최신 버전으로 업그레이드하는 방법에 대해 [자세히 알아봅니다](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).<br/>



## <a name="next-steps"></a>다음 단계
VMware VM의 재해 복구용으로 Azure를 준비하는 방법을 [알아봅니다](tutorial-prepare-azure.md).

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
