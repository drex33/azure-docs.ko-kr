---
title: Azure Site Recovery를 사용하여 Hyper-V VM과 Azure 간 재해 복구 지원
description: Azure Site Recovery를 사용한 Hyper-V VM과 Azure 간 재해 복구를 위해 지원되는 구성 요소 및 요구 사항이 요약되어 있습니다.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
author: Sharmistha-Rai
ms.author: sharrai
ms.openlocfilehash: b8771e26f51dd54595ebefdb5ad5230c7257907a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536669"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>온-프레미스 Hyper-V VM과 Azure 간 재해 복구를 위한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 Hyper-V VM을 Azure로 재해 복구하는 데 지원되는 구성 요소와 설정의 요약 정보를 제공합니다.

>[!NOTE]
> Site Recovery는 원본 머신에 대해 재해 복구가 설정된 대상 지역에서 고객 데이터를 이동하거나 저장하지 않습니다. 고객이 원하는 경우 다른 지역에서 Recovery Services 자격 증명 모음을 선택할 수 있습니다. Recovery Services 자격 증명 모음에는 메타데이터가 포함되지만 실제 고객 데이터는 포함되지 않습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

**시나리오** | **세부 정보**
--- | ---
Hyper-V(Virtual Machine Manager 있음) <br> <br>| System Center Virtual Machine Manager 패브릭에서 관리되는 Hyper-V 호스트에서 실행 중인 VM에 대해 Azure로의 재해 복구를 수행할 수 있습니다.<br/><br/> Azure Portal에서 또는 PowerShell을 사용하여 이 시나리오를 배포할 수 있습니다.<br/><br/> Virtual Machine Manager에서 Hyper-V 호스트를 관리하는 경우에는 보조 온-프레미스 사이트로의 재해 복구도 수행할 수 있습니다. 이 시나리오에 대해 자세히 알아보려면 [이 자습서](hyper-v-vmm-disaster-recovery.md)를 읽어보세요.
Hyper-V(Virtual Machine Manager 없음) | Virtual Machine Manager에서 관리하지 않는 Hyper-V 호스트에서 실행 중인 VM에 대해 Azure로의 재해 복구를 수행할 수 있습니다.<br/><br/> Azure Portal에서 또는 PowerShell을 사용하여 이 시나리오를 배포할 수 있습니다.

> [!NOTE]
> 동일한 Hyper-V 호스트에서 Azure Backup과 Azure Site Recovery를 모두 구성하면 복제와 관련된 문제가 발생할 수 있으며 지원되지 않습니다.

## <a name="on-premises-servers"></a>온-프레미스 서버

**서버** | **요구 사항** | **세부 정보**
--- | --- | ---
Hyper-V(Virtual Machine Manager 없이 실행 중) |  최신 업데이트가 포함된 Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 <br/><br/> **참고:** 이러한 운영 체제의 Server Core 설치도 지원됩니다. | Azure Site Recovery 및 플랜을 사용하여 Windows Server 2012 R2 또는 SCVMM 2012 R2를 이미 구성했고 OS를 업그레이드할 계획이라면 지침 [설명서](upgrade-2012R2-to-2016.md)를 따르세요.
Hyper-V(Virtual Machine Manager로 실행 중) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **참고:** 이러한 운영 체제의 Server Core 설치도 지원됩니다.  | Virtual Machine Manager를 사용하는 경우 Windows Server 2019 호스트는 Virtual Machine Manager 2019에서 관리되어야 합니다. 마찬가지로 Windows Server 2016 호스트는 Virtual Machine Manager 2016에서 관리되어야 합니다.

> [!NOTE]
> 온-프레미스 서버에 .NET Framework 4.6.2 이상이 있는지 확인합니다.

## <a name="replicated-vms"></a>복제된 VM


다음 표에는 VM 지원 정보가 요약되어 있습니다. Site Recovery는 지원되는 운영 체제에서 실행되는 모든 워크로드를 지원합니다.

 **구성 요소** | **세부 정보**
--- | ---
VM 구성 | Azure로 복제하는 VM은 [Azure 요구 사항](#azure-vm-requirements)을 충족해야 합니다.
게스트 운영 체제 | [Azure에 지원되는](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases) 모든 게스트 OS.<br/><br/> Windows Server 2016 Nano Server는 지원되지 않습니다.


## <a name="vmdisk-management"></a>VM/디스크 관리

**동작** | **세부 정보**
--- | ---
복제된 Hyper-V VM에서 디스크 크기 조정 | 지원 안 됨 복제를 사용하지 않도록 설정하고, 변경한 다음, VM에 복제를 사용하도록 다시 설정합니다.
복제된 Hyper-V VM에서 디스크 추가 | 지원 안 됨 복제를 사용하지 않도록 설정하고, 변경한 다음, VM에 복제를 사용하도록 다시 설정합니다.

## <a name="hyper-v-network-configuration"></a>Hyper-V 네트워크 구성

**구성 요소** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
호스트 네트워크: NIC 팀 | 예 | 예
호스트 네트워크: VLAN | 예 | 예
호스트 네트워크: IPv4 | 예 | 예
호스트 네트워크: IPv6 | 예 | 예
게스트 VM 네트워크: NIC 팀 | 예 | 예
게스트 VM 네트워크: IPv4 | 예 | 예
게스트 VM 네트워크: IPv6 | 예 | 예
게스트 VM 네트워크: 고정 IP(Windows) | 예 | 예
게스트 VM 네트워크: 고정 IP(Linux) | 예 | 예
게스트 VM 네트워크: 다중 NIC | 예 | 예
Https 프록시 | 예 | 예
Site Recovery 서비스에 대한 프라이빗 링크 액세스 | 예. [자세한 정보를 알아보세요](hybrid-how-to-enable-replication-private-endpoints.md). | 예. [자세한 정보를 알아보세요](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>장애 조치(failover) 이후의 Azure VM 네트워크 구성

**구성 요소** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
Azure ExpressRoute | 예 | 예
ILB | 예 | 예
ELB | 예 | 예
Azure Traffic Manager | 예 | 예
다중 NIC | 예 | 예
예약된 IP | 예 | 예
IPv4 | 예 | 예
원본 IP 주소 유지 | 예 | 예
Azure Virtual Network 서비스 엔드포인트<br/> (Azure Storage 방화벽 없음) | 예 | 예
가속 네트워킹 | 예 | 예


## <a name="hyper-v-host-storage"></a>Hyper-V 호스트 스토리지

**스토리지** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
NFS | 해당 없음 | 해당 없음
SMB 3.0 | 예 | 예
SAN(ISCSI) | 예 | 예
다중 경로(MPIO). 테스트에 사용된 소프트웨어:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | 예 | 예

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM 게스트 스토리지

**스토리지** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
VMDK | 해당 없음 | 해당 없음
VHD/VHDX | 예 | 예
2세대 VM | 예 | 예
EFI/UEFI<br></br>Azure에서 마이그레이션된 VM은 BIOS 부팅 VM으로 자동으로 변환됩니다. VM은 Windows Server 2012 이상만 실행해야 합니다. OS 디스크에는 최대 5개의 파티션이 있어야 하고 OS 디스크의 크기는 300GB 미만이어야 합니다.| 예 | 예
공유 클러스터 디스크 | 예 | 예
암호화된 디스크 | 예 | 예
NFS | 해당 없음 | 해당 없음
SMB 3.0 | 예 | 예
RDM | 해당 없음 | 해당 없음
디스크 1TB 이상 | 예, 최대 4,095GB | 예, 최대 4,095GB
디스크: 4K 논리/실제 섹터 | 미지원: 1세대/2세대 | 미지원: 1세대/2세대
디스크: 4K 논리/512바이트 물리 섹터 | 예 |  예
논리 볼륨 관리(LVM)입니다. LVM은 데이터 디스크에서만 지원됩니다. Azure는 단일 OS 디스크만 제공합니다. | 예 | 예
스트라이프 디스크 포함 볼륨 1TB 이상 | 예 | 예
스토리지 공간 | 예 | 예
디스크 핫 추가/제거 | 예 | 예
디스크 제외 | 예 | 예
다중 경로(MPIO) | 예 | 예

## <a name="azure-storage"></a>Azure Storage

**구성 요소** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
로컬 중복 스토리지 | 예 | 예
지역 중복 스토리지 | 예 | 예
읽기 액세스 지역 중복 스토리지 | 예 | 예
영역 중복 스토리지 | 예 | 예
쿨 스토리지 | 예 | 예
핫 스토리지| 예 | 예
블록 Blob | 예 | 예
미사용 암호화(SSE)| 예 | 예
미사용 암호화(CMK) <br></br> (관리 디스크로의 장애 조치(failover)에만 해당)| 예(PowerShell Az 3.3.0 모듈 이상을 통해) | 예(PowerShell Az 3.3.0 모듈 이상을 통해)
미사용 이중 암호화 <br></br> (관리 디스크로의 장애 조치(failover)에만 해당) <br></br> [Windows](../virtual-machines/disk-encryption.md) 및 [Linux](../virtual-machines/disk-encryption.md)를 지원하는 지역에 대해 자세히 알아보기 | 예(PowerShell Az 3.3.0 모듈 이상을 통해) | 예(PowerShell Az 3.3.0 모듈 이상을 통해)
Premium Storage | 예 | 예
Standard Storage | 예 | 예
가져오기/내보내기 서비스 | 예 | 예
방화벽이 사용되는 Azure Storage 계정 | 예. 대상 스토리지 및 캐시 | 예. 대상 스토리지 및 캐시
저장소 계정 수정 | 아니요. 복제를 사용하도록 설정한 후에는 대상 Azure Storage 계정을 수정할 수 없습니다. 수정하려면 재해 복구를 사용하지 않도록 설정했다가 다시 사용하도록 설정합니다. | 예
보안 전송 옵션 | 예 | 예
UEFI 보안 부팅 | 아니요 | 아니요

## <a name="azure-compute-features"></a>Azure 컴퓨팅 기능

**기능** | **Hyper-V(Virtual Machine Manager 있음)** | **Hyper-V(Virtual Machine Manager 없음)**
--- | --- | ---
가용성 집합 | 예 | 예
가용성 영역 | 예 | 예
HUB | 예 | 예  
관리 디스크 | 예, 장애 조치(failover)용<br/><br/> 관리 디스크 장애 복구(failback)는 지원되지 않음 | 예, 장애 조치(failover)용<br/><br/> 관리 디스크 장애 복구(failback)는 지원되지 않음

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제하는 온-프레미스 VM은 이 표에 요약되어 있는 Azure VM 요구 사항을 충족해야 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | Site Recovery에서는 [Azure에서 지원](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))하는 모든 운영 체제를 지원합니다.  | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
게스트 운영 체제 아키텍처 | 32비트(Windows Server 2008)/64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
운영 체제 디스크 크기 | 1세대 VM의 경우 최대 2,048GB<br/><br/> 2세대 VM의 경우 최대 300GB  | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
데이터 디스크 수 | 16개 이하  | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
데이터 디스크 VHD 크기 | 최대 4,095GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
FC 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
하드 디스크 형식 | VHD <br/><br/>  VHDX | Azure로의 장애 조치(failover)를 수행하면 Site Recovery는 VHDX를 VHD로 자동 변환합니다. 온-프레미스에 장애 복구 시 가상 머신에서 계속해서 VHDX 형식을 사용합니다.
BitLocker | 지원되지 않음 | VM의 복제를 사용하도록 설정하기 전에 BitLocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. VM 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | Site Recovery에서 VM 속성의 값을 업데이트합니다.
VM 형식 | 1세대<br/><br/> 2세대--Windows | 기본 OS 디스크 형식이 있는 2세대 VM(VHDX로 포맷된 한 개 또는 두 개의 데이터 볼륨을 포함) 및 300GB 미만의 디스크 공간이 지원됩니다.<br></br>Linux 2세대 VM은 지원되지 않습니다. [자세한 정보를 알아보세요](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services 자격 증명 모음 작업

**동작** |  **Hyper-V(VMM 포함)** | **VMM을 사용하지 않는 Hyper-V**
--- | --- | ---
리소스 그룹 간 자격 증명 모음 이동<br/><br/> 구독 내 및 구독 간 | 예 | 예
스토리지 그룹 간 스토리지, 네트워크, Azure VM 이동<br/><br/> 구독 내 및 구독 간 | 예 | 예

> [!NOTE]
> 온-프레미스에서 Azure로 Hyper-VM을 복제할 때 하나의 특정 환경(해당하는 경우 Hyper-V 사이트 또는 VMM을 사용하는 Hyper-V)에서 하나의 AD 테넌트에만 복제할 수 있습니다.


## <a name="provider-and-agent"></a>공급자 및 에이전트

배포가 이 문서에 나와 있는 설정과 호환되도록 하려면 최신 공급자 및 에이전트 버전을 실행 중인지 확인합니다.

**이름** | **설명** | **세부 정보**
--- | --- | ---
Azure Site Recovery 공급자 | 온-프레미스 서버와 Azure 간 통신 조정 <br/><br/> Virtual Machine Manager가 있는 Hyper-V : Virtual Machine Manager 서버에 설치됨<br/><br/> Virtual Machine Manager가 없는 Hyper-V: Hyper-V 호스트에 설치됨| [최신 기능 및 수정](./site-recovery-whats-new.md)
Microsoft Azure Recovery Services 에이전트 | Hyper-V VM과 Azure 간 복제 조정<br/><br/> 온-프레미스 Hyper-V 서버에 설치됨(Virtual Machine Manager 있음/없음) | 포털에서 사용할 수 있는 최신 에이전트






## <a name="next-steps"></a>다음 단계
온-프레미스 Hyper-V VM 재해 복구용으로 [Azure를 준비](tutorial-prepare-azure.md)하는 방법을 알아봅니다.
