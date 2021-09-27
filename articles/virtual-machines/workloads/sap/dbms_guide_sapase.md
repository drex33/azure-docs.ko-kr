---
title: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/15/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aabb53a573ee8a3ccc5d98ab8316fee560dbf625
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603907"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포

이 문서에서는 Azure IaaS에서 SAP ASE를 배포할 때 고려할 여러 가지 영역을 다룹니다. 이 문서의 사전 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](./get-started.md)의 다른 가이드를 읽어야 합니다. 이 문서에서는 Linux 및 Windows 운영 체제에서 실행되는 SAP ASE에 대해 설명합니다. Azure에서 지원되는 최소 릴리스는 SAP ASE 16.0.02(릴리스 16 지원 팩 2)입니다. 최신 SAP 버전 및 최신 패치 수준을 배포하는 것이 좋습니다.  최소 SAP ASE 16.0.03.07(릴리스 16 지원 팩 3 패치 수준 7)이 권장됩니다.  최신 버전의 SAP는 [대상 ASE 16.0 릴리스 일정 및 CR 목록 정보](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)에서 확인할 수 있습니다.

SAP 애플리케이션의 릴리스 지원 또는 설치 미디어 위치에 대한 추가 정보는 다음 위치의 SAP 제품 가용성 매트릭스에서 확인할 수 있습니다.

- [SAP support note #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP support note #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP support note #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP support note #1973241](https://launchpad.support.sap.com/#/notes/1973241)

설명: SAP 환경 내부 및 외부의 설명서 전체에서 제품 이름은 Sybase ASE, SAP ASE 또는 경우에 따라 둘 모두로 참조됩니다. 일관성을 유지하기 위해 이 설명서에서는 **SAP ASE** 라는 이름을 사용합니다.

## <a name="operating-system-support"></a>운영 체제 지원
SAP 제품 가용성 매트릭스에는 각 SAP 애플리케이션에 지원되는 운영 체제 및 SAP 커널 조합이 포함되어 있습니다.  Linux 배포판 SUSE 12.x, SUSE 15.x, Red Hat 7.x는 완전히 지원됩니다.  Oracle Linux는 SAP ASE용 운영 체제로 지원되지 않습니다.  사용 가능한 최신 Linux 릴리스를 사용하는 것이 좋습니다. Windows 고객은 Windows Server 2016 또는 Windows Server 2019 릴리스를 사용해야 합니다.  Windows 2012와 같은 이전 릴리스의 Windows는 기술적으로 지원되지만 항상 최신 Windows 버전이 권장됩니다.


## <a name="specifics-to-sap-ase-on-windows"></a>Windows의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 애플리케이션을 Azure Virtual Machines로 마이그레이션할 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 이러한 애플리케이션을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 애플리케이션에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Microsoft Azure는 가장 작은 SAP 시스템과 환경부터 수천 명의 사용자가 있는 대규모 SAP 시스템과 환경까지 실행할 수 있는 다양한 가상 머신 유형을 제공합니다. 다양한 SAP 인증 VM SKU의 SAP 크기 조정 SAPS 번호는 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에서 제공됩니다.

SAP ASE를 Windows에 설치하기 위한 설명서는 [Windows용 SAP ASE 설치 가이드](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)에서 찾을 수 있습니다.

[메모리의 페이지 잠금]은 SAP ASE 데이터베이스 버퍼가 페이지 아웃되지 않도록 방지하는 설정입니다. 이 설정은 메모리와 사용량이 많은 대규모 시스템에 유용합니다. 자세한 내용은 BC-DB-SYB에 문의하세요. 


## <a name="linux-operating-system-specific-settings"></a>Linux 운영 체제 관련 설정
Linux VM에서 SAP-ASE Linux Huge Pages 프로필을 사용하여 `saptune`을 사용하도록 설정해야 하며, 명령을 사용하여 확인할 수 있습니다.  

`cat /proc/meminfo` 

페이지 크기는 일반적으로 2,048KB입니다. 자세한 내용은 [Huge Pages on Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 문서를 참조하세요. 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>SAP ASE 배포를 위한 VM 및 디스크 구조에 대한 권장 사항

SAP NetWeaver 애플리케이션용 SAP ASE는 [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 나열된 모든 VM 유형에서 지원되며, 중간 크기의 SAP ASE 데이터베이스 서버에 사용되는 일반적인 VM 유형에는 Esv3이 포함됩니다.  대규모 다중 테라바이트 데이터베이스는 M 시리즈 VM 유형을 활용할 수 있습니다. M 시리즈 쓰기 가속기를 사용하도록 설정하여 SAP ASE 트랜잭션 로그 디스크 쓰기 성능을 향상시킬 수 있습니다. SAP ASE에서 [로그 쓰기]를 수행하는 방식으로 인해 SAP ASE에서 쓰기 가속기를 신중하게 테스트해야 합니다.  [SAP support note #2816580](../../how-to-enable-write-accelerator.md)을 검토하고 성능 테스트를 실행하는 것이 좋습니다.  
쓰기 가속기는 트랜잭션 로그 디스크 전용입니다. 디스크 수준 캐시는 NONE으로 설정해야 합니다. Azure 쓰기 가속기가 다른 DBMS와 비슷한 향상된 기능이 표시 되지 않는 경우에도 걱정할 필요가 없습니다. SAP ASE에서 트랜잭션 로그에 쓰는 방식에 따라 Azure 쓰기 가속기에서 가속이 거의 또는 전혀 발생하지 않을 수 있습니다.
데이터 디바이스 및 로그 디바이스에는 별도의 디스크를 사용하는 것이 좋습니다.  sybsecurity 및 `saptools` 시스템 데이터베이스에는 전용 디스크가 필요하지 않으며, SAP 데이터베이스 데이터 및 로그 디바이스가 포함된 디스크에 이를 배치할 수 있습니다. 

![SAP ASE용 스토리지 구성](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>파일 시스템, 스트라이프 크기 및 IO 분산 
SAP ASE는 별도로 구성하지 않는 한 데이터를 디스크 스토리지 디바이스에 순차적으로 씁니다. 즉, 4개의 디바이스가 있는 빈 SAP ASE 데이터베이스는 첫 번째 디바이스에만 데이터를 씁니다.  다른 디스크 디바이스는 첫 번째 디바이스가 꽉 찬 경우에만 씁니다.  각 SAP ASE 디바이스에 대한 읽기 및 쓰기 IO의 양이 다를 수 있습니다. 사용 가능한 모든 Azure 디스크에서 디스크 IO의 균형을 유지하려면 Windows 스토리지 공간 또는 Linux LVM2를 사용해야 합니다. Linux에서는 XFS 파일 시스템을 사용하여 디스크를 포맷하는 것이 좋습니다. LVM 스트라이프 크기는 성능 테스트를 사용하여 테스트해야 합니다. 128KB 스트라이프 크기가 적절한 시작 지점입니다. Windows에서는 NTFS AUS(할당 단위 크기)를 테스트해야 합니다. 64KB를 시작 값으로 사용할 수 있습니다. 

[SAP Adaptive Server Enterprise에서 자동 데이터베이스 공간 확장 구성](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) 및 [SAP support note #1815695](https://launchpad.support.sap.com/#/notes/1815695) 문서에서 설명한 대로 자동 데이터베이스 확장을 구성하는 것이 좋습니다. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>SAP ASE on Azure 가상 머신, 디스크 및 파일 시스템 구성 샘플 
아래 템플릿에는 Linux 및 Windows 모두에 대한 샘플 구성이 나와 있습니다. 가상 컴퓨터 및 디스크 구성을 확인 하기 전에 개별 VM의 네트워크 및 저장소 대역폭 할당량이 비즈니스 요구 사항을 충족 하기에 충분 한지 확인 합니다. 또한 Azure VM 유형에 따라 VM에 연결할 수 있는 최대 디스크 수가 다릅니다. 예를 들어 E4s_v3 VM의 스토리지 IO 처리량은 48MB/초로 제한됩니다. 데이터베이스 백업 작업에 필요한 스토리지 처리량이 48MB/초를 초과하는 경우 더 많은 스토리지 대역폭 처리량을 갖춘 더 큰 VM 유형을 사용해야 합니다. Azure 스토리지를 구성하는 경우, 특히 [Azure 프리미엄 스토리지](../../premium-storage-performance.md)를 사용하는 경우 처리량 및 용량 GB당 IOPS가 변경된다는 점도 고려해야 합니다. 이 항목에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md) 문서를 참조하세요. 특정 Azure VM 유형에 대한 할당량은 [메모리 최적화 가상 머신 크기](../../sizes-memory.md) 및 이에 연결된 문서에 설명되어 있습니다. 

> [!NOTE]
>  DBMS 시스템을 온-프레미스에서 Azure로 이동하는 경우 VM에 대한 모니터링을 수행하고 CPU, 메모리, IOPS 및 스토리지 처리량을 평가하는 것이 좋습니다. 위에서 언급한 문서에 설명된 VM 할당량 제한과 관찰된 최고 값을 비교합니다.

아래에서 제공하는 예는 설명을 위한 것이며 개별 요구 사항에 따라 수정할 수 있습니다. SAP ASE의 설계로 인해 데이터 디바이스의 수는 다른 데이터베이스의 경우와 같이 중요하지 않습니다. 이 문서에서 자세히 설명하는 데이터 디바이스 수는 지침일 뿐입니다. 

SAP 솔루션 관리자와 같이 데이터베이스 크기가 50~250GB인 소규모 SAP ASE DB 서버에 대한 구성의 예는 다음과 같습니다.

| 구성 | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E4s_v3(4개 vCPU/32GB RAM) | E4s_v3(4개 vCPU/32GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 디바이스 수 | 4 | 4 | ---|
| 로그 디바이스 수 | 1 | 1 | --- |
| 임시 디바이스 수 | 1 | 1 | SAP BW 워크로드에 대한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 포맷 블록 크기 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| 데이터 디스크 수 및 유형 | 프리미엄 스토리지: 2 x P10(RAID0) | 프리미엄 스토리지: 2 x P10(RAID0)| 캐시 = 읽기 전용 |
| 로그 디스크 수 및 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 디바이스 수 | 4 | 4| --- |
| 백업 디스크 수 및 유형 | 1 | 1 | --- |


소규모 SAP Business Suite 시스템과 같이 데이터베이스 크기가 250~750GB인 중간 규모 SAP ASE DB 서버 구성에 대한 예는 다음과 같습니다.

| 구성 | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E16s_v3(16개 vCPU/128GB RAM) | E16s_v3(16개 vCPU/128GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 디바이스 수 | 8 | 8 | ---|
| 로그 디바이스 수 | 1 | 1 | --- |
| 임시 디바이스 수 | 1 | 1 | SAP BW 워크로드에 대한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 포맷 블록 크기 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| 데이터 디스크 수 및 유형 | 프리미엄 스토리지: 4 x P20(RAID0) | 프리미엄 스토리지: 4 x P20(RAID0)| 캐시 = 읽기 전용 |
| 로그 디스크 수 및 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 디바이스 수 | 4 | 4| --- |
| 백업 디스크 수 및 유형 | 1 | 1 | --- |

대규모 SAP Business Suite 시스템과 같이 데이터베이스 크기가 750~2,000GB인 소규모 SAP ASE DB 서버 구성에 대한 예는 다음과 같습니다.

| 구성 | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E64s_v3(64개 vCPU/432GB RAM) | E64s_v3(64개 vCPU/432GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 디바이스 수 | 16 | 16 | ---|
| 로그 디바이스 수 | 1 | 1 | --- |
| 임시 디바이스 수 | 1 | 1 | SAP BW 워크로드에 대한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 포맷 블록 크기 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| 데이터 디스크 수 및 유형 | 프리미엄 스토리지: 4 x P30(RAID0) | 프리미엄 스토리지: 4 x P30(RAID0)| 캐시 = 읽기 전용 |
| 로그 디스크 수 및 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 디바이스 수 | 4 | 4| --- |
| 백업 디스크 수 및 유형 | 1 | 1 | --- |


전체적으로 사용되는 대규모 SAP Business Suite 시스템과 같이 데이터베이스 크기가 2TB 이상인 소규모 SAP ASE DB 서버 구성에 대한 예는 다음과 같습니다.

| 구성 | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | M 시리즈(1.0 ~4.0TB RAM)  | M 시리즈(1.0 ~4.0TB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 디바이스 수 | 32 | 32 | ---|
| 로그 디바이스 수 | 1 | 1 | --- |
| 임시 디바이스 수 | 1 | 1 | SAP BW 워크로드에 대한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 포맷 블록 크기 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| 데이터 디스크 수 및 유형 | 프리미엄 스토리지: 4개 이상 x P30(RAID0) | 프리미엄 스토리지: 4개 이상 x P30(RAID0)| 캐시 = 읽기 전용, Azure Ultra Disk 고려 |
| 로그 디스크 수 및 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음, Azure Ultra Disk 고려 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 디바이스 수 | 16 | 16 | --- |
| 백업 디스크 수 및 유형 | 4 | 4 | LVM2/스토리지 공간 사용 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>SAP ASE on Azure에 대한 백업 및 복원 고려 사항
데이터 및 백업 디바이스의 수를 늘리면 백업 및 복원 성능이 향상됩니다. 위 표와 같이 SAP ASE 백업 디바이스를 호스팅하는 Azure 디스크는 스트라이프하는 것이 좋습니다. 백업 디바이스와 디스크의 수를 균형을 유지하고 백업 처리량이 총 VM 처리량 할당량의 40~50%를 초과하지 않도록 주의해야 합니다. 기본적으로 SAP 백업 압축을 사용하는 것이 좋습니다. 자세한 내용은 다음 문서를 참조하세요.

- [SAP support note #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP support note #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP support note #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

D:\ 드라이브 또는 /temp 공간을 데이터베이스 또는 로그 덤프 대상으로 사용하지 마세요.

### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/O 대역폭이 제한 요소가 될 수 있는 구성에서 IOPS를 줄이는 측정은 Azure와 같은 IaaS 시나리오에서 실행할 수 있는 워크로드를 늘리는 데 도움이 될 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인하는 것이 좋습니다.

Azure에 업로드하기 전에 압축을 적용하는 것이 좋은 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure Virtual Machines에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용되고 있는지 확인하는 방법에 대한 자세한 내용은 [SAP support note 1750510](https://launchpad.support.sap.com/#/notes/1750510)을 참조하세요. SAP ASE 데이터베이스를 압축하는 방법에 대한 자세한 내용은 [SAP support note #2121797](https://launchpad.support.sap.com/#/notes/2121797)을 참조하세요.

## <a name="high-availability-of-sap-ase-on-azure"></a>SAP ASE on Azure의 고가용성 
HADR 사용자 가이드는 2 노드 SAP ASE "Always on" 솔루션의 설정 및 구성에 대해 자세히 설명 합니다.  또한 세 번째 재해 복구 노드도 지원됩니다. SAP ASE는 공유 디스크 및 기본 OS 클러스터링 (예: Pacemaker 및 Windows Server 장애 조치 (Failover) 클러스터)을 비롯 한 다양 한 고가용성 [구성을](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.4.1/en-US/9b40a3c038a34cbda1064312aa8d25a4.html) 지원 합니다. Azure에는 SAP ASE에 대해 지원 되는 두 가지 고가용성 구성이 있습니다.

- 오류 관리자를 사용한 HA 인식-SAP 커널은 "HA 인식" 응용 프로그램으로 기본 및 보조 SAP ASE 서버를 알고 있습니다. SAP ASE "HA 인식" 솔루션과 Azure 간에는 밀접 하 게 통합 되지 않으므로 Azure 내부 부하 분산 장치는 사용 되지 않습니다.  이 솔루션은 [SAP ASE HADR 사용자 가이드](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 에 설명 되어 있습니다.
- 오류 관리자를 사용 하는 부동 IP-이 솔루션은 SAP Business Suite 및 비 SAP 비즈니스 제품군 응용 프로그램에 사용할 수 있습니다.  이 솔루션은 Azure ILB를 활용 하 고 SAP ASE 데이터베이스 엔진은 프로브 포트를 제공 합니다.  오류 관리자는 SAPHostAgent를 호출 하 여 ASE 호스트에서 보조 부동 IP를 시작 하거나 중지 합니다.  이 솔루션은 [SAP note #3086679-SYB: 오류 관리자: Microsoft Azure의 부동 IP 주소](https://launchpad.support.sap.com/#/notes/3086679) 에 설명 되어 있습니다.


> [!NOTE]
> 장애 조치 (failover) 시간 및 HA 인식 또는 부동 IP 솔루션의 기타 특성은 비슷합니다.  이러한 두 솔루션을 결정할 때 고객은 계획 되거나 계획 되지 않은 장애 조치 (failover) 시간 및 기타 운영 절차와 같은 요소를 포함 하 여 자체 테스트 및 평가를 수행 해야 합니다.  

### <a name="third-node-for-disaster-recovery"></a>재해 복구를 위한 세 번째 노드
SAP ASE Always-On을 로컬 고가용성에 사용하는 것 외에도 구성을 다른 Azure 지역에 있는 비동기적으로 복제된 노드로 확장할 수 있습니다. 자세한 내용은 [Sybase 16. 3 패치 레벨 3 Always-on + Suse 12.3의 DR 설치 절차](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)를 참조하세요.

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE 데이터베이스 암호화 및 SSL 
SAP SWPM(Software Provisioning Manager)은 설치 중에 데이터베이스를 암호화하는 옵션을 제공합니다.  암호화를 사용하려면 SAP 전체 데이터베이스 암호화를 사용하는 것이 좋습니다.  자세한 내용은 다음 문서를 참조하세요.

- [SAP support note #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP support note #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP support note #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP support note #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> SAP ASE 데이터베이스가 암호화되어 있으면 백업 덤프 압축이 작동하지 않습니다. [SAP support note #2680905](https://launchpad.support.sap.com/#/notes/2680905)도 참조하세요. 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE on Azure 배포 검사 목록
 
- SAP ASE 16.0.03.07 이상 배포
- FaultManager 및 SAPHostAgent의 최신 버전 및 패치로 업데이트
- 사용 가능한 최신 인증 OS(예: Windows 2019, Suse 15.1 또는 Redhat 7.6 이상)에 배포
- SAP 인증 VM 사용 – 대용량 메모리 Azure VM SKU(예: Es_v3) 또는 x 대규모 시스템의 경우 M 시리즈 VM SKU가 권장됨
- VM의 디스크 IOPS 및 총 VM 집계 처리량 할당량을 디스크 설계와 일치  충분한 수의 디스크 배포
- 올바른 스트라이프 크기와 파일 시스템이 있는 Windows 스토리지 공간 또는 Linux LVM2를 사용하는 디스크 집계
- 데이터, 로그, 임시 및 백업용으로 충분한 수의 디바이스 만들기
- x 대규모 시스템에 Ultra Disk 사용 고려 
- Linux OS에서 `saptune` SAP-ASE 실행 
- DB 암호화를 사용하여 데이터베이스 보호 – 수동으로 Azure Key Vault에 키 저장 
- [SAP on Azure 검사 목록](./sap-deployment-checklist.md) 완료 
- 로그 백업 및 전체 백업 구성 
- HA/DR 테스트, 백업/복원 및 스트레스/볼륨 테스트 수행 
- 자동 데이터베이스 확장이 작동하는지 확인 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit을 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. [SAP support note #1245200](https://launchpad.support.sap.com/#/notes/1245200)에 따라 webdynpro를 사용하도록 설정하고 필요한 항목을 생성합니다. 위 Note의 지침을 따르는 경우 http 및 https 연결에 사용할 포트와 함께 `ICM`(Internet Communication Manager)도 구성합니다. HTTP의 기본 설정은 다음과 같이 표시됩니다.

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

그리고 트랜잭션 DBACockpit에서 생성된 링크는 다음과 같이 표시됩니다.

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP 시스템을 호스트하는 Azure Virtual Machine이 AD 및 DNS에 연결된 방법에 따라 ICM이 DBACockpit를 여는 머신에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수를 기반으로 하여 정규화된 호스트 이름을 확인하는 방법을 이해하고 필요한 경우 icm/host_name_full 매개 변수를 명시적으로 설정하려면 [SAP support note #773830](https://launchpad.support.sap.com/#/notes/773830)을 참조하세요.

온-프레미스와 Azure 사이의 온-프레미스 간 연결이 없는 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소 및 `domainlabel`을 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같이 표시됩니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 


SAP 프로필 매개 변수 icm/host_name_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure Portal의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [SAP support note #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP support note #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP support note #1882376](https://launchpad.support.sap.com/#/notes/1882376)

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [SAP support note #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP support note #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP support note #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP support note #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP support note #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP support note #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP support note #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP support note #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>SAP ASE에 대한 유용한 링크, 참고 사항 및 백서
[SAP ASE 16.0.03.07 설명서](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)의 시작 페이지에서는 다음과 같은 문서를 포함하여 다양한 문서에 대한 링크를 제공합니다.

- SAP ASE 학습 과정 - 관리 및 모니터링
- SAP ASE 학습 과정 - 설치 및 업그레이드

이는 유용한 문서입니다. 또 다른 유용한 문서는 [SAP Applications on SAP Adaptive Server Enterprise 마이그레이션 및 런타임 모범 사례](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)입니다.

기타 유용한 SAP support note는 다음과 같습니다.

- [SAP support note #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP support note #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP support note #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP support note #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP support note #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP support note #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP support note #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP support note #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP support note #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP support note #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP support note #1588316](https://launchpad.support.sap.com/#/notes/158831) 

기타 정보는 다음에 게시됩니다. 

- [SAP Applications on SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE 정보 센터](http://infocenter.sybase.com/help/index.jsp) 
- [세 번째 DR 노드가 설치된 SAP ASE Always-on](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

월간 뉴스레터는 [SAP support note #2381575](https://launchpad.support.sap.com/#/notes/2381575)를 통해 게시됩니다. 


## <a name="next-steps"></a>다음 단계
[Azure의 SAP 워크로드: 계획 및 배포 검사 목록](./sap-deployment-checklist.md)을 확인합니다.
