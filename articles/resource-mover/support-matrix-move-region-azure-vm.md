---
title: Azure Resource Mover를 사용하여 Azure VM을 다른 지역으로 이동하기 위한 지원 매트릭스
description: Azure Resource Mover를 사용하여 지역 간에 Azure VM을 이동하는 경우에 대한 지원을 검토합니다.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: ced0213a155bee2926a77d0b8f4126b7b97232fb
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618955"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Azure 지역 간에 Azure VM 이동 시 지원

이 문서에는 Resource Mover를 사용하여 Azure 지역 전반에서 가상 머신 및 관련 네트워크 리소스를 이동할 때 제공되는 지원 및 사전 요구 사항이 요약되어 있습니다.

## <a name="windows-vm-support"></a>Windows VM 지원

Resource Mover는 이러한 Windows 운영 체제를 실행하는 Azure VM을 지원합니다.

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2016  | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2 | 지원됩니다.
Windows Server 2012 | 지원됩니다.
Windows Server 2008 R2 SP1/SP2 | 지원됩니다.<br/><br/> Windows Server 2008 R2 SP1/SP2를 실행하는 컴퓨터의 경우 Windows [서비스 스택 업데이트(SSU)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.
Windows 10(x64) | 지원됩니다.
Windows 8.1(x64) | 지원됩니다.
Windows 8(x64) | 지원됩니다.
Windows 7(x64) SP1 이상 | Windows 7 SP1을 실행하는 컴퓨터에 Windows [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치합니다.  SHA-1은 2019년 9월부터 지원되지 않습니다. SHA-2 코드 서명을 사용하도록 설정하지 않으면 '준비' 단계가 실패합니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.


## <a name="linux-vm-support"></a>Linux VM 지원

Resource Mover는 이러한 Linux 운영 체제를 실행하는 Azure VM을 지원합니다.

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions)<br/><br/> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 VM을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 VM의 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 암호 기반 로그인을 다시 사용하도록 설정할 수 있습니다.
Ubuntu 18.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions).
Debian 7 | [(지원되는 커널 버전)](#supported-debian-kernel-versions).
Debian 8 | [(지원되는 커널 버전)](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4 [지원되는 커널 버전](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 및 15 SP1 [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Red Hat 호환 커널 또는 UEK(Unbreakable Enterprise Kernel Release) 3, 4 및 5를 실행


### <a name="supported-ubuntu-kernel-versions"></a>지원되는 Ubuntu 커널 버전

**릴리스** | **커널 버전** 
--- | --- 
14.04 LTS |  3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic에서 4.4.0-171-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-74-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic에서 4.15.0-74-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-37-generic </br> 5.3.0-19-generic에서 5.3.0-24-generic </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>지원되는 Debian 커널 버전 

**릴리스** |  **커널 버전** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>지원되는 SUSE Linux Enterprise Server 12 커널 버전 

**릴리스** | **커널 버전** 
--- |  --- 
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4) |  모든 [Stock SUSE 12 SP1,SP2,SP3,SP4 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>지원되는 SUSE Linux Enterprise Server 15 커널 버전

**릴리스** | **커널 버전** |
--- |  --- |
SUSE Linux Enterprise Server 15 및 15 SP1 |  모든 Stock SUSE 15 및 15 커널이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>지원되는 Linux 파일 시스템/게스트 스토리지

* 파일 시스템: ext3, ext4, XFS, BTRFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 디바이스 매퍼


## <a name="supported-vm-compute-settings"></a>지원되는 VM 컴퓨팅 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machine 크기](../virtual-machines/sizes-general.md)를 확인합니다.
가용성 집합 | 지원됨 | 지원됨.
가용성 영역 | 지원됨 | 대상 지역 지원에 따라 지원됩니다.
Azure 갤러리 이미지(Microsoft 게시) | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지(타사 게시)  | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지(타사 게시)| 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하는 VM | 지원되지 않음 | 백 엔드에서 Site Recovery를 사용하여 VM의 지역 간에 리소스를 이동합니다. 이미 Site Recovery를 사용하고 있는 경우 복제를 사용하지 않도록 설정한 다음 준비 프로세스를 시작합니다.
Azure RBAC 정책 | 지원되지 않음 | VM에 대한 Azure RBAC(Azure 역할 기반 액세스 제어) 정책은 대상 지역의 VM에 복제되지 않습니다.
확장 | 지원되지 않음 | 확장은 대상 지역의 VM에 복사되지 않습니다. 이동이 완료된 후 수동으로 설치합니다.


## <a name="supported-vm-storage-settings"></a>지원되는 VM 스토리지 설정

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다. 성능 문제를 방지하려면 VM 디스크 한도 및 [관리 디스크](../virtual-machines/disks-scalability-targets.md) 대상을 관찰하는 것이 중요합니다.

> [!NOTE]
> 대상 VM 크기는 원본 VM과 같거나 커야 합니다. 유효성 검사에 사용되는 매개 변수는 데이터 디스크 수, NIC 개수, 사용 가능한 CPU, 메모리(GB)입니다. 그렇지 않으면 오류가 발생합니다.


**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/managed-disks-overview.md).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 준비 프로세스에서 제외됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 마세요. [자세히 알아보기](../virtual-machines/managed-disks-overview.md#temporary-disk).
데이터 디스크 최대 크기 | 관리 디스크의 경우 8,192GB
데이터 디스크 최소 크기 |  관리 디스크의 경우 2GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/sizes.md).
데이터 디스크 변경 비율 | Premium Storage는 디스크당 최대 10MBps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 계속해서 최대값보다 큰 경우 준비가 처리되지 않습니다.<br/><br/>  그러나 최대값을 산발적으로 초과하는 경우 준비가 처리될 수 있지만 복구 지점이 약간 지연될 수 있습니다.
데이터 디스크(Standard Storage 계정) | 지원되지 않습니다. | 스토리지 유형을 관리 디스크로 변경한 다음, VM을 이동해 보세요.
데이터 디스크(Premium Storage 계정) | 지원되지 않음 | 스토리지 유형을 관리 디스크로 변경한 다음, VM을 이동해 보세요.
관리 디스크(표준) | 지원됨  |
관리 디스크(프리미엄) | 지원됨 |
표준 SSD | 지원됨 |
2세대(UEFI 부팅) | 지원됨
부팅 진단 스토리지 계정 | 지원되지 않음 | VM을 대상 지역으로 이동한 후 다시 활성화합니다.
Azure 디스크 암호화를 사용하도록 설정된 VM | 지원됨 | [자세히 알아보기](tutorial-move-region-encrypted-virtual-machines.md)
고객 관리형 키로 서버 쪽 암호화를 사용하는 VM | 지원됨 | [자세히 알아보기](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>제한 및 데이터 변경률

다음 표에는 테스트에 따른 제한 사항이 요약됩니다. 가능한 모든 애플리케이션 I/O 조합을 다루고 있지 않습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라집니다. 디스크 데이터 변동별 및 VM 데이터 변동별로 두 가지 제한을 고려해야 합니다.

**스토리지 대상** | **평균 원본 디스크 I/O** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB

## <a name="supported-vm-networking-settings"></a>지원되는 SQL VM 네트워킹 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
NIC | 지원됨 | 대상 지역에 기존 리소스를 지정하거나 준비 프로세스 중에 새 리소스를 만듭니다. 
내부 부하 분산 장치 | 지원됨 | 대상 지역에 기존 리소스를 지정하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
공용 부하 분산 장치 | 지원됨 | 대상 지역에 기존 리소스를 지정하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
공용 IP 주소 | 지원됨 | 대상 지역에 기존 리소스를 지정하거나 준비 프로세스 중에 새 리소스를 만듭니다.<br/><br/> 공용 IP 주소는 지역별로 지정되며 이동 후 대상 지역에 유지되지 않습니다. 대상 위치에서 네트워킹 설정(부하 분산 규칙 포함)을 수정할 때는 이 점을 염두에 두어야 합니다.
네트워크 보안 그룹 | 지원됨 | 대상 지역에 기존 리소스를 지정하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
예약된(고정) IP 주소 | 지원됨 | 현재 이를 구성할 수 없습니다. 기본값은 원본 값이 됩니다. <br/><br/> 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에서 동일한 IP 주소를 사용할 수 있는 경우 해당 IP가 대상 VM에 할당됩니다.<br/><br/> 대상 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 VM에 대한 이동 시작이 실패합니다.
동적 IP 주소 | 지원됨 | 현재 이를 구성할 수 없습니다. 기본값은 원본 값이 됩니다.<br/><br/> 원본의 NIC에 동적 IP 주소가 있는 경우 대상 VM의 NIC도 기본적으로 동적으로 설정됩니다.
IP 구성 | 지원됨 | 현재 이를 구성할 수 없습니다. 기본값은 원본 값이 됩니다.

## <a name="outbound-access-requirements"></a>아웃바운드 액세스 요구 사항

이동하려는 Azure VM에 아웃바운드 액세스가 필요합니다.


### <a name="url-access"></a>URL 액세스

 URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.

**이름** | **Azure 퍼블릭 클라우드** | **세부 정보** 
--- | --- | --- 
스토리지 | `*.blob.core.windows.net`  | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다. 
Azure Active Directory | `login.microsoftonline.com`  | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. 
복제 | `*.hypervrecoverymanager.windowsazure.com` | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. 
Service Bus | `*.servicebus.windows.net` | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. 

## <a name="nsg-rules"></a>NSG 규칙
NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 [서비스 태그 규칙](../virtual-network/service-tags-overview.md)을 만듭니다. 각 규칙은 HTTPS(443)에 대한 아웃 바운드 액세스를 허용해야 합니다.
- 원본 지역에 대한 스토리지 태그 규칙을 만듭니다.
- 모든 지역의 Site Recovery 서비스에 대한 액세스를 허용하는 *AzureSiteRecovery* 태그 규칙을 만듭니다. 이 태그는 이러한 다른 태그에 종속되므로 다음과 같은 규칙을 만들어야 합니다.
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- 비프로덕션 환경에서 규칙을 테스트하는 것이 좋습니다. [몇 가지 예제를 검토](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)합니다. 


## <a name="next-steps"></a>다음 단계

Resource Mover를 사용하여 다른 지역으로 [Azure VM 이동](tutorial-move-region-virtual-machines.md)을 시도합니다.
