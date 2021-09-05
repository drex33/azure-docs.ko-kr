---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/16/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a2009a1898abd8390b4a747efb10db01964f55a7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538314"
---
공유 디스크 활성화는 디스크 유형의 하위 집합에서만 사용할 수 있습니다. 현재는 울트라 디스크, 프리미엄 SSD 및 표준 SSD만 공유 디스크를 활성화할 수 있습니다. 공유 디스크를 사용할 수 있는 각 관리 디스크에는 디스크 유형별로 구성된 다음 제한 사항이 적용됩니다.

### <a name="ultra-disks"></a>Ultra disks

Ultra disks에는 공유 디스크와 무관한 별도의 제한 사항 목록이 있습니다. Ultra disk 제한 사항은 [Azure ultra disks 사용](../articles/virtual-machines/disks-enable-ultra-ssd.md)을 참조하세요.

Ultra disks를 공유하는 경우 다음과 같은 추가 제한 사항이 있습니다.

- 일부 버전의 Windows Server 장애 조치(failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치(Failover) 클러스터링 하드웨어 요구 사항 및 스토리지 옵션](/windows-server/failover-clustering/clustering-requirements)을 참조하세요.
- [서버 쪽 암호화](../articles/virtual-machines/disk-encryption.md)만 지원되고 [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md)은 현재 지원되지 않습니다.

공유 ultra disks는 기본적으로 ultra disks를 지원하는 모든 지역에서 사용 가능하며, 사용을 위한 액세스 등록이 필요 없습니다.

### <a name="premium-ssds"></a>프리미엄 SSD

- OS 디스크가 아닌 데이터 디스크에서만 사용할 수 있습니다.
- `maxShares>1`인 프리미엄 SSD에는 **ReadOnly** 호스트 캐싱을 사용할 수 없습니다.
- `maxShares>1`인 프리미엄 SSD에는 디스크 버스팅을 사용할 수 없습니다.
- Azure 공유 디스크와 함께 가용성 집합과 가상 머신 확장 집합을 사용하는 경우 공유 데이터 디스크에 대해 가상 머신 장애 도메인과의 [스토리지 장애 도메인 정렬](../articles/virtual-machines/availability.md)이 적용되지 않습니다.
- [PPG(근접 배치 그룹)](../articles/virtual-machines/windows/proximity-placement-groups.md)를 사용하는 경우 디스크를 공유하는 모든 가상 머신은 동일한 PPG에 속해야 합니다.
- 일부 버전의 Windows Server 장애 조치(failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치(Failover) 클러스터링 하드웨어 요구 사항 및 스토리지 옵션](/windows-server/failover-clustering/clustering-requirements)을 참조하세요.
- Azure Site Recovery 지원은 아직 사용할 수 없습니다.
- Azure Backup은 [Azure Disk Backup](../articles/backup/disk-backup-overview.md)을 통해 사용할 수 있습니다.
- [서버 쪽 암호화](../articles/virtual-machines/disk-encryption.md)만 지원되고 [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md)은 현재 지원되지 않습니다.

#### <a name="premium-ssd-regional-availability"></a>프리미엄 SSD 지역 가용성

모든 프리미엄 SSD 크기의 공유 디스크는 관리 디스크를 사용할 수 있는 모든 지역에서 사용할 수 있습니다.


### <a name="standard-ssds"></a>표준 SSD

- OS 디스크가 아닌 데이터 디스크에서만 사용할 수 있습니다.
- Azure 공유 디스크와 함께 가용성 집합과 가상 머신 확장 집합을 사용하는 경우 공유 데이터 디스크에 대해 가상 머신 장애 도메인과의 [스토리지 장애 도메인 정렬](../articles/virtual-machines/availability.md)이 적용되지 않습니다.
- [PPG(근접 배치 그룹)](../articles/virtual-machines/windows/proximity-placement-groups.md)를 사용하는 경우 디스크를 공유하는 모든 가상 머신은 동일한 PPG에 속해야 합니다.
- 일부 버전의 Windows Server 장애 조치(failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치(Failover) 클러스터링 하드웨어 요구 사항 및 스토리지 옵션](/windows-server/failover-clustering/clustering-requirements)을 참조하세요.
- Azure Site Recovery 지원은 아직 사용할 수 없습니다.
- Azure Backup은 [Azure Disk Backup](../articles/backup/disk-backup-overview.md)을 통해 사용할 수 있습니다.
- [서버 쪽 암호화](../articles/virtual-machines/disk-encryption.md)만 지원되고 [Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md)은 현재 지원되지 않습니다.

#### <a name="standard-ssd-regional-availability"></a>표준 SSD 지역 가용성

모든 표준 SSD 크기의 공유 디스크는 관리 디스크를 사용할 수 있는 모든 지역에서 사용할 수 있습니다.
