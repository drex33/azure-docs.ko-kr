---
title: Azure Site Recovery를 사용한 Azure VM 재해 복구에 대한 지원 매트릭스
description: Azure Site Recovery를 사용한 보조 지역으로 Azure VM 재해 복구에 대한 지원을 요약합니다.
ms.topic: article
ms.date: 11/29/2020
author: Sharmistha-Rai
ms.author: sharrai
ms.openlocfilehash: a960064aa9ee23c5d82c605aa551cdc0a4e07c49
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363454"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure 지역 간 Azure VM 재해 복구에 대한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 한 Azure 지역에서 다른 지역으로의 Azure VM 재해 복구를 위한 지원 및 필수 구성 요소를 요약합니다.


## <a name="deployment-method-support"></a>배포 방법 지원 여부

**배포** |  **지원**
--- | ---
**Azure Portal** | 지원됩니다.
**PowerShell** | 지원됩니다. [자세히 알아보기](azure-to-azure-powershell.md)
**REST API** | 지원됩니다.
**CLI** | 현재 지원되지 않음


## <a name="resource-support"></a>리소스 지원

**리소스 작업** | **세부 정보**
--- | ---
**리소스 그룹 간 자격 증명 모음 이동** | 지원되지 않음
**리소스 그룹 간에 계산/스토리지/네트워크 리소스 이동** | 지원되지 않습니다.<br/><br/> VM 복제 후 VM 또는 스토리지/네트워크와 같은 관련 구성 요소를 이동하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
**재해 복구를 위해 한 구독에서 다른 구독으로 Azure VM 복제** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**지원되는 지역별 클러스터 내의 여러 지역 간에 VM 마이그레이션(구독 내/구독 간)** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**동일한 지역 내에서 VM 마이그레이션** | 지원되지 않습니다.
**Azure Dedicated Host** | 지원되지 않습니다.

## <a name="region-support"></a>지역 지원

Azure Site Recovery를 사용하여 글로벌 재해 복구를 수행할 수 있습니다. 전 세계의 두 Azure 지역 간에 VM을 복제 및 복구할 수 있습니다. 데이터 주권에 관한 문제가 있는 경우 특정 지리적 클러스터 내에서 복제를 제한하도록 선택할 수 있습니다. 다양한 지리적 클러스터는 다음과 같습니다.

**지리적 클러스터** | **Azure 지역**
-- | --
America | 캐나다 동부, 캐나다 중부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 서부 3, 미국 중부, 미국 중북부
유럽 | 영국 서부, 영국 남부, 북유럽, 서유럽 남아프리카 공화국 서부, 남아프리카 공화국 북부, 노르웨이 동부, 프랑스 중부, 스위스 북부, 독일 중서부, 아랍에미리트 북부, 아랍에미리트 중부(아랍에미리트는 유럽 지역 클러스터의 일부로 처리됨)
아시아 | 인도 남부, 인도 중부, 인도 서부, 동남 아시아, 동아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부
JIO | JIO 인도 서부
오스트레일리아    | 오스트레일리아 동부, 오스트레일리아 남동부, 오스트레일리아 중부, 오스트레일리아 중부 2
Azure Government    | US Gov 버지니아, US Gov 아이오와, US Gov 애리조나, US Gov 텍사스, US DoD 동부, US DoD 중부
독일    | 독일 중부, 독일 북동부
중국 | 중국 동부, 중국 북부, 중국 북부 2, 중국 동부 2
브라질 | 브라질 남부
국내 재해 복구를 위해 예약된 제한된 지역 |스위스 북부용으로 예약된 스위스 서부, 프랑스 중부용으로 예약된 프랑스 남부, 노르웨이 동부 고객용으로 노르웨이 서부, JIO 인도 서부 고객용 JIO 인도 중부, 브라질 남부 고객용 브라질 남동부, 남아프리카 공화국 북부 고객용 남아프리카 공화국 서부, 독일 중서부 고객용 독일 북부

>[!NOTE]
>
> - **브라질 남부** 의 경우 브라질 남동부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중북부 지역으로 복제하고 장애 조치(fail over)할 수 있습니다.
> - 브라질 남부는 VM이 Site Recovery를 사용하여 복제할 수 있는 원본 지역으로만 사용할 수 있습니다. 대상 지역으로 사용할 수는 없습니다. 브라질 남부를 원본 지역으로 대상 지역에 장애 조치(failover)하는 경우 대상 지역에서 브라질 남부로 장애 복구(failback)가 지원됩니다. 브라질 남동부는 대상 지역으로만 사용할 수 있습니다.
> - 자격 증명 모음을 만들 지역이 표시되지 않는 경우 구독에 해당 지역에서 리소스를 만들 수 있는 권한이 있는지 확인하세요.
> - 복제를 설정할 때 지리적 클러스터 내에 지역이 표시되지 않는 경우 구독에 해당 지역에서 VM을 만들 수 있는 권한이 있는지 확인하세요.

## <a name="cache-storage"></a>캐시 스토리지

이 표에서는 복제하는 동안 Site Recovery에서 사용하는 캐시 스토리지 계정에 대한 지원을 요약해서 보여 줍니다.

**설정** | **지원** | **세부 정보**
--- | --- | ---
범용 V2 스토리지 계정(핫 및 쿨 계층) | 지원됨 | V2의 트랜잭션 비용은 V1 스토리지 계정보다 훨씬 높기 때문에 GPv2는 사용하지 않는 것이 좋습니다.
Premium Storage | 지원되지 않음 | 표준 스토리지 계정은 캐시 스토리지에 사용되며 비용을 최적화하는 데 도움이 됩니다.
지역 |  가상 머신과 동일한 지역  | 캐시 스토리지 계정은 보호 중인 가상 머신과 동일한 지역에 있어야 합니다.
구독  | 원본 가상 머신과 다를 수 있음 | 캐시 스토리지 계정은 원본 가상 머신과 동일한 구독에 있지 않아도 됩니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 방화벽 지원 캐시 스토리지 계정 또는 대상 스토리지 계정을 사용하는 경우 ['신뢰할 수 있는 Microsoft 서비스 허용'](../storage/common/storage-network-security.md#exceptions)을 선택해야 합니다.<br></br>또한 원본 Vnet의 하나 이상의 서브넷에 대한 액세스를 허용해야 합니다.<br></br>참고: Site Recovery에 사용된 스토리지 계정에 대한 가상 네트워크 액세스를 제한하지 마십시오. '모든 네트워크'에서 액세스를 허용해야 합니다.

아래 표에는 단일 스토리지 계정으로 복제할 수 있는 디스크 수에 대한 제한이 나와 있습니다.

**스토리지 계정 유형**    |    **변동 = 디스크당 4MBps**    |    **변동 = 디스크당 8MBps**
---    |    ---    |    ---
V1 스토리지 계정    |    300개의 디스크    |    150개의 디스크
V2 스토리지 계정    |    750개의 디스크    |    375개의 디스크

디스크의 평균 변동이 늘어나면 스토리지 계정이 지원할 수 있는 디스크 수가 줄어듭니다. 위의 표는 프로비저닝해야 하는 스토리지 계정 수에 대한 결정을 내리는 데 지침으로 사용할 수 있습니다.

위의 제한 사항은 Azure 간 및 영역 간 DR 시나리오에만 해당됩니다.

## <a name="replicated-machine-operating-systems"></a>복제된 컴퓨터 운영 체제

Site Recovery는 이 섹션에 나열된 운영 체제를 실행하는 Azure VM의 복제를 지원합니다. 이미 복제 중인 머신이 이후 다른 주 커널로 업그레이드(또는 다운그레이드)된 경우에는 복제를 해제하고 업그레이드한 후 복제를 다시 설정해야 합니다.

### <a name="windows"></a>Windows


**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2016  | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2 | 지원됩니다.
Windows Server 2012 | 지원됩니다.
Windows Server 2008 R2 SP1/SP2 | 지원됩니다.<br/><br/> Azure VM용 Mobility Service 확장의 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터 Windows Server 2008 R2 SP1/SP2를 실행하는 머신에서 Windows [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.
Windows 10(x64) | 지원됩니다.
Windows 8.1(x64) | 지원됩니다.
Windows 8(x64) | 지원됩니다.
Windows 7(x64) SP1 이상 | Azure VM용 Mobility Service 확장의 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터 Windows 7 SP1을 실행하는 머신에서 Windows [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 9월 2019에서 지원되지 않으며, SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.



#### <a name="linux"></a>Linux

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8](https://support.microsoft.com/help/4564347/), [7.9 GA 이전 버전](https://support.microsoft.com/help/4578241/), 7.9 GA 버전은 9.37 핫픽스 패치에서 지원됨** </br> 8.0, 8.1, [8.2](https://support.microsoft.com/en-us/help/4570609), [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | 모든 14.04.*x* 버전에 대한 지원 포함, [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines),
Ubuntu 16.04 LTS Server | 모든 16.04.*x* 버전에 대한 지원 포함, [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 VM을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 VM의 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 암호 기반 로그인을 다시 사용하도록 설정할 수 있습니다.
Ubuntu 18.04 LTS Server | 모든 18.04.*x* 버전에 대한 지원 포함, [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 VM을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 VM의 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 암호 기반 로그인을 다시 사용하도록 설정할 수 있습니다.
Ubuntu 20.04 LTS 서버 | 모든 20.04.*x* 버전에 대한 지원 포함, [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | 모든 7에 대한 지원을 포함합니다. *x* 버전 [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | 모든 8에 대한 지원을 포함합니다. *x* 버전 [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | 9\.1~9.13에 대한 지원을 포함합니다. Debian 9.0은 지원되지 않습니다. [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5[(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 복제 컴퓨터를 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터를 업그레이드한 경우 복제를 사용하지 않도록 설정하고 업그레이드 후에 다시 사용하도록 설정해야 합니다.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/), [8.2](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [8.3](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)(Red Hat 호환 커널 또는 Unbreakable Enterprise Kernel Release 3, 4, 5, 6(UEK3, UEK4, UEK5, UEK6) 실행)<br/><br/>8.1(모든 UEK 커널 및 RedHat 커널 <= 3.10.0-1062.*는 [9.35](https://support.microsoft.com/help/4573888/)에서 지원. RedHat 커널의 나머지 부분에 대한 지원은 [9.36](https://support.microsoft.com/help/4578241/)에서 사용할 수 있음)

> [!NOTE]
> Linux 버전의 경우 Azure Site Recovery에서 사용자 지정 OS 커널을 지원하지 않습니다. 배포의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.

**참고: 릴리스로부터 15일 이내에 최신 Linux 커널을 지원하기 위해 Azure Site Recovery는 최신 모바일 에이전트 버전 위에 핫픽스 패치를 출시합니다. 해당 픽스는 두 주 버전 릴리스 사이에 출시됩니다. 최신 버전의 모바일 에이전트(핫픽스 패치 포함)로 업데이트하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명된 단계를 따르세요. 해당 패치는 현재 Azure 간 DR 시나리오에 사용되는 모바일 에이전트를 위해 출시되었습니다.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6), [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.4.0-21-generic ~ 4.4.0-206-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic ~ 4.15.0-140-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure ~ 4.15.0-1111-azure|
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic에서 4.4.0-201-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-133-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1106-azure <br/> 4.4.0-203-generic, 4.4.0-204-generic, 4.4.0-206-generic, 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 4.15.0-1108-azure, 4.15.0-1109-azure, 4.15.0-1110-azure, 4.15.0-1111-azure ~ 9.41 핫픽스 패치**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic에서 4.4.0-197-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-128-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1102-azure </br> 4.15.0-132-generic, 4.4.0-200-generic, 4.15.0-1106-azure, 4.15.0-133-generic, 4.4.0-201-generic through 9.40 핫픽스 패치**|
|||
18.04 LTS | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 4.15.0-153-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic </br> 4.15.0-153-generic </br> 5.4.0-1056-azure </br> 5.4.0-81-generic </br> 4.15.0-1122-azure </br> 4.15.0-154-generic |
18.04 LTS | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 4.15.0-153-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic |
18.04 LTS |[9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.15.0-20-generic ~ 4.15.0-140-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic ~ 5.3.0-72-generic </br> 5.4.0-37-generic ~ 5.4.0-70-generic </br> 4.15.0-1009-azure ~ 4.15.0-1111-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure ~ 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic에서 4.15.0-135-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-65-generic </br> 5.3.0-19-generic에서 5.3.0-70-generic </br> 5.4.0-37-generic에서 5.4.0-59-generic</br> 5.4.0-60-generic에서 5.4.0-65-generic </br> 4.15.0-1009-azure에서 4.15.0-1106-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure에서 5.4.0-1039-azure </br> 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 5.3.0-72-generic, 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 4.15.0-1108-azure, 4.15.0-1111-azure, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure, 4.15.0-1109-azure, 4.15.0-1110-azure ~ 9.41 핫픽스 패치**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic에서 4.15.0-129-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-63-generic </br> 5.3.0-19-generic에서 5.3.0-69-generic </br> 5.4.0-37-generic에서 5.4.0-59-generic</br> 4.15.0-1009-azure에서 4.15.0-1103-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0-1035-azure </br> 5.4.0-1020-azure에서 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 4.15.0-1106-azure, 4.15.0-134-generic, 4.15.0-135-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic through 9.40 핫픽스 패치**|
|||
20.04 LTS |[9.44](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 5.4.0-26-generic ~ 5.4.0-60-generic </br> 5.4.0-1010-azure ~ 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 5.4.0-81-generic </br> 5.4.0-1056-azure |
20.04 LTS |[9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 5.4.0-26-generic ~ 5.4.0-60-generic </br> 5.4.0-1010-azure ~ 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
20.04 LTS |[9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)| 5.4.0-26-generic ~ 5.4.0-60-generic </br> 5.4.0-1010-azure ~ 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic ~ 5.4.0-65-generic </br> 5.4.0-1010-azure ~ 5.4.0-1039-azure </br> 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure through 9.41 핫픽스 패치**|
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic ~ 5.4.0-59-generic </br> 5.4.0-1010-azure ~ 5.4.0-1035-azure </br> 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic |

**참고: 릴리스로부터 15일 이내에 최신 Linux 커널을 지원하기 위해 Azure Site Recovery는 최신 모바일 에이전트 버전 위에 핫픽스 패치를 출시합니다. 해당 픽스는 두 주 버전 릴리스 사이에 출시됩니다. 최신 버전의 모바일 에이전트(핫픽스 패치 포함)로 업데이트하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명된 단계를 따르세요. 해당 패치는 현재 Azure 간 DR 시나리오에 사용되는 모바일 에이전트를 위해 출시되었습니다.

**참고: Ubuntu 20.04의 경우 커널 5.8에 대한 지원을 처음으로 롤아웃했습니다.* 하지만 이후 이 커널의 지원에 관한 문제를 발견하여 우선 지원 문에서 이러한 커널을 수정했습니다.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Debian 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) , [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533), [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8), [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6), [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.16.0-4-amd64에서 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.9.0-1-amd64 ~ 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 ~ 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 ~ 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64에서 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64에서 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64에서 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0-0.bpo.16-amd64, 4.19.0-0.bpo.16-cloud-amd64 ~ 9.41 핫픽스 패치**
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64에서 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64에서 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64에서 4.19.0-0.bpo.13-cloud-amd64
|||
Debian 10 | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.19.0-5-amd64 ~ 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.19.0-5-amd64 ~ 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.19.0-5-amd64 ~ 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 ~ 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64에서 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64에서 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-cloud-amd64 ~ 9.41 핫픽스 패치**
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64에서 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64에서 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

**참고: 릴리스로부터 15일 이내에 최신 Linux 커널을 지원하기 위해 Azure Site Recovery는 최신 모바일 에이전트 버전 위에 핫픽스 패치를 출시합니다. 해당 픽스는 두 주 버전 릴리스 사이에 출시됩니다. 최신 버전의 모바일 에이전트(핫픽스 패치 포함)로 업데이트하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명된 단계를 따르세요. 해당 패치는 현재 Azure 간 DR 시나리오에 사용되는 모바일 에이전트를 위해 출시되었습니다.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 12 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.56-azure </br> 4.12.14-16.65-azure </br> 4.12.14-16.68-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.56-azure </br> 4.12.14-16.65-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure ~ 4.12.14-16.56-azure |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure에서 4.12.14-16.44-azure </br> 4.12.14-16.47-azure ~ 9.41 핫픽스 패치**|
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 모든 [스톡 SUSE 12 SP1,SP2,SP3,SP4, SP5 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.43-azure </br> 4.12.14-16.7-azure에서 4.12.14-16.38-azure </br> 4.12.14-16.41-azure through 9.40 핫픽스 패치**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 15 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.44](https://support.microsoft.com/en-us/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.43](https://support.microsoft.com/en-us/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.42](https://support.microsoft.com/en-us/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure ~ 5.3.18-18.47-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure에서 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure에서 5.3.18-18.35-azure </br> 5.3.18-18.38-azure ~ 9.41 핫픽스 패치**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 기본적으로 모든 [스톡 SUSE 15, SP1, SP2 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure ~ 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure에서 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure through 9.40 핫픽스 패치**

**참고: 릴리스로부터 15일 이내에 최신 Linux 커널을 지원하기 위해 Azure Site Recovery는 최신 모바일 에이전트 버전 위에 핫픽스 패치를 출시합니다. 해당 픽스는 두 주 버전 릴리스 사이에 출시됩니다. 최신 버전의 모바일 에이전트(핫픽스 패치 포함)로 업데이트하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명된 단계를 따르세요. 해당 패치는 현재 Azure 간 DR 시나리오에 사용되는 모바일 에이전트를 위해 출시되었습니다.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>복제된 컴퓨터 - Linux 파일 시스템/게스트 스토리지

* 파일 시스템: ext3, ext4, XFS, BTRFS
* 볼륨 관리자: LVM2

> [!NOTE]
> 다중 경로 소프트웨어는 지원되지 않습니다.


## <a name="replicated-machines---compute-settings"></a>복제된 머신 - 컴퓨팅 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machine 크기](../virtual-machines/sizes.md)를 확인합니다.
RAM | Azure Site Recovery 드라이버는 RAM의 6%를 소비합니다.
가용성 집합 | 지원됨 | 기본 옵션을 사용하여 Azure VM에 대해 복제를 사용하도록 설정하면 원본 지역 설정에 따라 가용성 집합이 자동으로 생성됩니다. 이러한 설정을 수정할 수 있습니다.
가용성 영역 | 지원됨 |
HUB(하이브리드 사용 혜택) | 지원됨 | 원본 VM에 활성 HUB 라이선스가 있는 경우 테스트 장애 조치(failover) 또는 장애 조치(failover)된 VM에서도 HUB 라이선스를 사용합니다.
가상 머신 크기 집합 | 지원되지 않음 |
Azure 갤러리 이미지 - Microsoft 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하여 마이그레이션된 VM | 지원됨 | VMware VM 또는 물리적 컴퓨터가 Site Recovery를 사용하여 Azure에 마이그레이션되면 컴퓨터에서 실행되는 이전 버전의 모바일 서비스를 제거하고 컴퓨터를 다시 시작한 후 다른 Azure 지역에 복제해야 합니다.
Azure RBAC 정책 | 지원되지 않음 | VM에 대한 Azure RBAC(Azure 역할 기반 액세스 제어) 정책은 대상 지역의 장애 조치(failover) VM에 복제되지 않습니다.
확장 | 지원되지 않음 | 확장은 대상 지역의 장애 조치(failover) VM에 복제되지 않습니다. 장애 조치(failover) 후 수동으로 설치해야 합니다.
근접 배치 그룹 | 지원 여부 | 근접 배치 그룹 내부에 있는 가상 머신은 Site Recovery를 사용하여 보호할 수 있습니다.
태그  | 지원 여부 | 원본 가상 머신에 적용된 사용자 생성 태그는 테스트 장애 조치(failover) 또는 장애 조치(failover) 후에 대상 가상 머신으로 전달됩니다. VM의 태그는 대상 지역에 VM이 있는 경우 24시간마다 한 번씩 복제됩니다.


## <a name="replicated-machines---disk-actions"></a>복제된 컴퓨터 - 디스크 작업

**동작** | **세부 정보**
-- | ---
복제된 VM에서 디스크 크기 조정 | 원본 VM의 크기를 조정 하는 작업이 지원 됩니다. 원본 VM에서 크기를 조정 하는 것은 지원 되지 않습니다. 크기 조정은 장애 조치 (failover) 전에 수행 해야 합니다. 복제를 해제/다시 설정할 필요가 없습니다.<br/><br/> 장애 조치(failover) 후 원본 VM을 변경하면 변경 내용이 캡처되지 않습니다.<br/><br/> 장애 조치(failover) 후 Azure VM에서 디스크 크기를 변경하는 경우 Site Recovery에서 변경 내용이 캡처되지 않으며 원래 VM 크기로 장애 복구(failback)가 발생합니다.<br/><br/> >= 4TB로 크기를 조정 하는 경우 [디스크 캐싱에 대](../virtual-machines/premium-storage-performance.md)한 Azure 지침을 참조 하세요. 
복제된 VM에 디스크 추가 | 지원됨
보호된 디스크에 대한 오프라인 변경 | 디스크 연결을 끊고 오프라인으로 수정하려면 전체 다시 동기화를 트리거해야 합니다.
디스크 캐싱 | 4TiB 이상의 디스크에서 디스크 캐싱은 지원되지 않습니다. 여러 디스크가 VM에 연결되어 있는 경우 4TiB보다 작은 디스크는 캐싱을 지원합니다. Azure 디스크의 캐시 설정을 변경하면 대상 디스크가 분리되었다가 다시 연결됩니다. 운영 체제 디스크인 경우 VM이 다시 시작됩니다. 디스크 캐시 설정을 변경하기 전에 이 중단의 영향을 받을 수 있는 모든 애플리케이션/서비스를 중지합니다. 이러한 권장 사항을 따르지 않으면 데이터가 손상될 수 있습니다.

## <a name="replicated-machines---storage"></a>복제된 컴퓨터 - 스토리지

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다.

- 성능 문제를 방지하려면 VM 디스크 한도 및 [관리 디스크](../virtual-machines/disks-scalability-targets.md) 대상을 관찰하는 것이 중요합니다.
- 기본 설정을 사용하여 배포하는 경우 Site Recovery는 원본 설정에 따라 디스크 및 스토리지 계정을 자동으로 만듭니다.
- 사용자 지정하는 경우 지침을 따릅니다.

**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/managed-disks-overview.md).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 복제에서 제외됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 마세요. [자세히 알아보기](../virtual-machines/managed-disks-overview.md).
데이터 디스크 최대 크기 | 관리 디스크의 경우 32TB<br></br>관리되지 않는 디스크의 경우 4TB|
데이터 디스크 최소 크기 | 비관리 디스크는 제한이 없습니다. 관리 디스크의 경우 2GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/sizes.md).
데이터 디스크 변경 비율 | 프리미엄 스토리지의 경우 디스크당 최대 20MBps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 계속해서 최대값보다 큰 경우 복제가 처리되지 않습니다.<br/><br/>  그러나 최대값을 산발적으로 초과하는 경우 복제가 처리될 수 있지만 복구 지점이 약간 지연될 수 있습니다.
데이터 디스크 - Standard Storage 계정 | 지원됨 |
데이터 디스크 - Premium Storage 계정 | 지원됨 | VM의 디스크가 Premium Storage 계정과 표준 스토리지 계정에 분산된 경우 대상 지역의 스토리지를 동일하게 구성하기 위해 각 디스크에 대해 서로 다른 대상 스토리지 계정을 선택할 수 있습니다.
Managed Disk - Standard | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
Managed Disk - Premium | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
디스크 구독 제한 | 구독당 보호 디스크 최대 3,000개 | 원본 또는 대상 구독에 3,000개가 넘는 Azure Site Recovery 보호 디스크(데이터/OS 모두)가 없는지 확인합니다.
표준 SSD | 지원됨 |
중복 | LRS 및 GRS가 지원됩니다.<br/><br/> ZRS는 지원되지 않습니다.
콜드 및 핫 스토리지 | 지원되지 않음 | VM 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
스토리지 공간 | 지원됨 |
NVMe 스토리지 인터페이스 | 지원되지 않음
호스트에서 암호화 | 지원 여부 | 호스트에서 암호화를 통해 엔드투엔드 암호화를 사용하는 VM을 만들려면 [자세한 정보를 참조](../virtual-machines/disks-enable-host-based-encryption-portal.md)하세요.
미사용 암호화(SSE) | 지원됨 | SSE은 스토리지 계정의 기본 설정입니다.
미사용 암호화(CMK) | 지원됨 | 관리 디스크에는 소프트웨어 및 HSM 키가 모두 지원됩니다.
미사용 이중 암호화 | 지원 여부 | [Windows](../virtual-machines/disk-encryption.md) 및 [Linux](../virtual-machines/disk-encryption.md)를 지원하는 지역에 대해 자세히 알아보기
FIPS 암호화 | 지원되지 않음
Windows OS용 ADE(Azure Disk Encryption) | 관리 디스크를 사용하는 VM을 지원합니다. | 비관리 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM 보호 키는 지원되지 않습니다. <br/><br/> 단일 디스크에서 개별 볼륨 암호화는 지원되지 않습니다. |
Linux OS용 ADE(Azure Disk Encryption) | 관리 디스크를 사용하는 VM을 지원합니다. | 비관리 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM 보호 키는 지원되지 않습니다. <br/><br/> 단일 디스크에서 개별 볼륨 암호화는 지원되지 않습니다. <br><br> 복제 사용에 대해 알려진 문제입니다. [자세한 정보](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS 키 회전 | 지원되지 않음 | 스토리지 계정에 대한 SAS 키를 회전하는 경우 고객은 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다. |
호스트 캐싱 | 지원 여부
핫 추가    | 지원됨 | 복제된 Azure VM에 추가하는 데이터 디스크에 대한 복제 설정은 관리 디스크를 사용하는 VM을 지원합니다. <br/><br/> Azure VM에 한 번에 하나의 디스크만 핫으로 추가할 수 있습니다. 여러 디스크의 병렬 추가는 지원되지 않습니다. |
디스크 핫 제거    | 지원되지 않음 | VM에서 데이터 디스크를 제거하는 경우 해당 VM에 대해 복제를 해제했다가 다시 설정해야 합니다.
디스크 제외 | 지원됨. [PowerShell](azure-to-azure-exclude-disks.md)을 사용하여 구성해야 합니다. |    임시 디스크는 기본적으로 제외됩니다.
직접 스토리지 공간  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
스케일 아웃 파일 서버  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
DRBD | DRBD 설치 프로그램의 일부인 디스크는 지원되지 않습니다. |
LRS | 지원됨 |
GRS | 지원됨 |
RA-GRS | 지원됨 |
ZRS | 지원되지 않음 |
콜드 및 핫 스토리지 | 지원되지 않음 | 가상 머신 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 [신뢰할 수 있는 Microsoft 서비스 허용](../storage/common/storage-network-security.md#exceptions)을 사용하도록 설정합니다.
범용 V2 스토리지 계정(핫 및 쿨 계층 모두) | 지원됨 | 범용 V1 Storage 계정에 비해 상당한 트랜잭션 비용 증가
2세대(UEFI 부팅) | 지원됨
NVMe 디스크 | 지원되지 않음
Azure 공유 디스크 | 지원되지 않음
보안 전송 옵션 | 지원 여부
쓰기 가속기 사용 설정 디스크 | 지원되지 않음
태그  | 지원 여부 | 사용자가 생성한 태그는 24시간마다 복제됩니다.

>[!IMPORTANT]
> 성능 문제를 방지하려면 VM 디스크 확장성 및 [관리 디스크](../virtual-machines/disks-scalability-targets.md)에 대한 성능 목표를 따라야 합니다. 기본 설정을 사용하는 경우 Site Recovery가 원본 구성에 따라 필요한 디스크 및 스토리지 계정을 만듭니다. 사용자 고유의 설정을 사용자 지정하고 선택하는 경우 소스 VM의 디스크 확장성 및 성능 목표를 따릅니다.

## <a name="limits-and-data-change-rates"></a>제한 및 데이터 변경률

다음 표에는 Site Recovery 제한이 요약되어 있습니다.

- 이러한 제한은 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 다룰 수는 없습니다.
- 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.
- 디스크 데이터 변동별 및 가상 머신 데이터 변동별로 두 가지 제한을 고려해야 합니다.
- 가상 머신 데이터 변동에 따른 현재 한도는 크기와 상관없이 54MB/s입니다.

**스토리지 대상** | **평균 원본 디스크 I/O** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB

## <a name="replicated-machines---networking"></a>복제된 컴퓨터 - 네트워킹
**설정** | **지원** | **세부 정보**
--- | --- | ---
NIC | 특정 Azure VM 크기에 대해 지원되는 최대 수 | 장애 조치(failover) 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 장애 조치(failover) VM의 NIC 수는 복제를 사용하도록 설정한 당시에 원본 VM의 NIC 수에 따라 다릅니다. 복제를 사용하도록 설정한 후에 NIC를 추가하거나 제거하는 경우 장애 조치(failover) 후 복제된 VM의 NIC 수에는 영향을 주지 않습니다. <br/><br/> 또한 장애 조치(failover) 후 NIC 순서는 원래 순서와 동일하지 않을 수 있습니다. <br/><br/> 조직의 명명 규칙에 따라 대상 지역의 NIC 이름을 변경할 수 있습니다. NIC 이름 변경은 PowerShell을 사용하여 지원됩니다.
인터넷 부하 분산 장치 | 지원되지 않음 | 주 지역에서 공용/인터넷 부하 분산 장치를 설정할 수 있습니다. 그러나 DR 지역의 Azure Site Recovery는 공용/인터넷 부하 분산 장치를 지원하지 않습니다.
내부 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
공용 IP 주소 | 지원됨 | 기존 공용 IP 주소를 NIC에 연결합니다. 또는 공용 IP 주소를 만들고 복구 계획에서 Azure Automation 스크립트를 사용하여 NIC에 연결합니다.
NIC의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결합니다.
서브넷의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 서브넷에 연결합니다.
예약된(고정) IP 주소 | 지원됨 | 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에서 동일한 IP 주소를 사용할 수 있는 경우 해당 IP가 장애 조치(Failover)된 VM에 할당됩니다.<br/><br/> 대상 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 서브넷의 사용 가능한 IP 주소 중 하나가 이 VM용으로 예약됩니다.<br/><br/> **복제된 항목** > **설정** > **계산 및 네트워크** > **네트워크 인터페이스** 에서 고정 IP 주소 및 서브넷을 지정할 수도 있습니다.
동적 IP 주소 | 지원됨 | 원본의 NIC에 동적 IP 주소가 있는 경우 장애 조치(failover)된 VM의 NIC도 기본적으로 동적으로 설정됩니다.<br/><br/> 필요한 경우 이 주소를 고정 IP 주소로 수정할 수 있습니다.
여러 IP 주소 | 지원되지 않음 | 여러 IP 주소가 할당된 NIC가 있는 VM을 장애 조치(failover)하는 경우 원본 지역의 NIC의 기본 IP 주소만 유지됩니다. 여러 IP 주소를 할당하려면 [복구 계획](recovery-plan-overview.md)에 VM을 추가하고 스크립트를 연결하여 계획에 추가 IP 주소를 할당하거나, 장애 조치(failover) 후 수동으로 또는 스크립트를 사용해 변경할 수 있습니다.
Traffic Manager     | 지원됨 | 트래픽이 평소에는 원본 지역의 엔드포인트로 라우팅되고 장애 조치(Failover) 시에는 대상 지역의 엔드포인트로 라우팅되도록 Traffic Manager를 미리 구성할 수 있습니다.
Azure DNS | 지원됨 |
사용자 지정 DNS    | 지원됨 |
인증되지 않은 프록시 | 지원됨 | [자세히 알아보기](./azure-to-azure-about-networking.md)
인증된 프록시 | 지원되지 않음 | VM에서 아웃바운드 연결에 인증된 프록시를 사용하는 경우 Azure Site Recovery를 사용하여 VM을 복제할 수 없습니다.
온-프레미스에 대한 VPN 사이트 간 연결<br/><br/>(ExpressRoute 유무에 관계 없음)| 지원됨 | Site Recovery 트래픽이 온-프레미스로 라우팅되지 않도록 UDR 및 NSG가 구성되어 있는지 확인합니다. [자세히 알아보기](./azure-to-azure-about-networking.md)
VNet 간 연결    | 지원됨 | [자세히 알아보기](./azure-to-azure-about-networking.md)
Virtual Network 서비스 엔드포인트 | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스할 수 있는지 확인합니다.
가속된 네트워킹 | 지원됨 | 원본 VM에서 가속 네트워킹을 사용하도록 설정해야 합니다. [자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto 네트워크 어플라이언스 | 지원되지 않음 | 타사 어플라이언스를 사용하는 경우 공급자가 가상 머신 내부에서 적용하는 제한 사항이 있는 경우가 흔합니다. Azure Site Recovery가 에이전트, 확장 및 아웃바운드 연결을 사용할 수 있어야 합니다. 하지만 어플라이언스는 가상 머신 내에서 아웃바운드 작업을 구성하는 것을 허용하지 않습니다.
IPv6  | 지원되지 않음 | IPv4 및 IPv6을 모두 포함하는 혼합 구성도 지원되지 않습니다. Site Recovery 작업을 수행하기 전에 IPv6 범위의 서브넷을 확보하세요.
Site Recovery 서비스에 대한 프라이빗 링크 액세스 | 지원됨 | [자세히 알아보기](azure-to-azure-how-to-enable-replication-private-endpoints.md)
태그  | 지원 여부 | NIC의 사용자 생성 태그는 24시간마다 복제됩니다.



## <a name="next-steps"></a>다음 단계

- Azure VM 복제를 위한 [네트워킹 지침](./azure-to-azure-about-networking.md)에 대해 알아봅니다.
- [Azure VM을 복제](./azure-to-azure-quickstart.md)하여 재해 복구를 배포합니다.
