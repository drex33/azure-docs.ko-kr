---
title: Azure IaaS VM에 대한 디스크 유형 선택 - 관리 디스크
description: 울트라 디스크, 프리미엄 SSD, 표준 SSD, 표준 HDD를 포함하여 가상 머신에 사용 가능한 Azure 디스크 유형을 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 11/03/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5e4eb581f8cf9b95e9a8ba4dffd442efc6c055ef
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505444"
---
# <a name="azure-managed-disk-types"></a>Azure 관리 디스크 유형

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure managed disks는 현재 특정 고객 시나리오를 해결 하기 위한 4 개의 디스크 유형을 제공 합니다.

- Ultra disks
- Premium Ssd (반도체 드라이브)
- 표준 SSD
- 표준 Hdd (하드 디스크 드라이브)

## <a name="disk-type-comparison"></a>디스크 유형 비교

다음 표에서는 사용할 방법을 결정 하는 데 도움이 되는 4 가지 디스크 유형을 비교 하 여 보여 줍니다.

|         | Ultra disk | 프리미엄 SSD | 표준 SSD | <nobr>표준 HDD</nobr> |
| ------- | ---------- | ----------- | ------------ | ------------ |
| **디스크 유형** | SSD | SSD | SSD | HDD |
| **시나리오**  | IO 집약적 워크로드 - [SAP HANA](workloads/sap/hana-vm-operations-storage.md), 최상위 계층 데이터베이스(예: SQL, Oracle) 및 다른 트랜잭션 집약적 워크로드 | 프로덕션 및 성능이 중요한 워크로드 | 웹 서버, 조금 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트 | 백업, 중요하지 않음, 가끔 액세스 |
| **최대 디스크 크기** | 65536기비바이트(GiB) | 32,767GiB | 32,767GiB | 32,767GiB |
| **최대 처리량** | 4000 m b/초 | 900MB/s | 750MB/s | 500MB/s |
| **최대 IOPS** | 160,000 | 20,000 | 6,000 | 2,000 |

## <a name="ultra-disks"></a>Ultra disks

Azure ultra disks는 Azure Vm (가상 머신)에 대해 가장 높은 성능의 저장소 옵션입니다. Vm을 다시 시작 하지 않고도 ultra disk의 성능 매개 변수를 변경할 수 있습니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적인 워크 로드와 같은 데이터를 많이 사용 하는 워크 로드에 적합 합니다.

Ultra disks는 데이터 디스크로 사용 해야 하며 빈 디스크로만 만들 수 있습니다. 프리미엄 Ssd (반도체 드라이브)를 OS (운영 체제) 디스크로 사용 하는 것이 좋습니다.

### <a name="ultra-disk-size"></a>Ultra disk 크기

Azure 울트라 디스크는 기본적으로 구독당 지역별 최대 32TiB를 제공하지만, 울트라 디스크는 요청에 따라 더 많은 용량을 지원합니다. 용량 증가를 요청하려면 할당량 증가를 요청하거나 Azure 지원에 문의하세요.

다음 표에서는 사용할 수 있는 크기를 결정 하는 데 도움이 되는 디스크 크기와 성능 캡을 비교 합니다.

|디스크 크기(GiB)  |IOPS 한도  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,400         |
|64     |19,200         |4,000         |
|128     |38,400         |4,000         |
|256     |76,800         |4,000         |
|512     |153,600         |4,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |4,000         |

Ultra disks는 이전 테이블의 99.99%에 설명 된 submillisecond 대기 시간 및 대상 IOPS 및 처리량을 제공 하도록 설계 되었습니다.

### <a name="ultra-disk-performance"></a>Ultra disk 성능

Ultra disks는 디스크를 프로 비전 하기 전후에 IOPS 및 처리량을 독립적으로 구성할 수 있도록 하는 유연한 성능 구성 모델입니다. 울트라 디스크는 4 GiB 최대 64 TiB에 이르는 여러 고정 크기를 제공 합니다.

### <a name="ultra-disk-iops"></a>Ultra disk IOPS

Ultra disks는 디스크당 300 iops/16만 GiB의 IOPS 제한을 지원 합니다. 디스크의 대상 IOPS를 얻으려면 선택한 디스크 IOPS가 VM IOPS 제한 보다 적은지 확인 합니다.

현재 일반 공급 크기의 단일 VM에 대한 IOPS의 최대 한도는 80,000입니다. IOPS가 더 큰 울트라 디스크를 공유 디스크로 사용하여 여러 VM을 지원할 수 있습니다.

디스크당 보장 되는 최소 IOPS는 1 IOPS/GiB 이며 전체 기준이 최소 100 IOPS입니다. 예를 들어 GiB 울트라 디스크 4 개를 프로 비전 한 경우 해당 디스크의 최소 IOPS는 8이 아니라 100입니다.

IOPS에 대 한 자세한 내용은 [가상 컴퓨터 및 디스크 성능](disks-performance.md)을 참조 하세요.

### <a name="ultra-disk-throughput"></a>Ultra disk 처리량

단일 ultra disk의 처리량 제한은 각 프로 비전 된 4000 IOPS에 대해 256 KiB/s입니다 (초당 MBps = 10 ^ 6 바이트). 디스크당 보장되는 최소 처리량은 프로비저닝된 각 IOPS당 4KiB/s이고, 전체 기준은 1MBps 이상입니다.

가상 머신에서 디스크를 분리 하지 않고도 런타임에 ultra disk IOPS 및 처리량 성능을 조정할 수 있습니다. 디스크에서 성능 크기 조정 작업이 실행 된 후 변경 내용이 적용 되는 데 최대 한 시간이 걸릴 수 있습니다. 24 시간 동안 최대 4 개의 성능 크기 조정 작업을 수행할 수 있습니다.

성능 대역폭 용량이 부족 하 여 성능 크기 조정 작업이 실패할 수 있습니다.

### <a name="ultra-disk-limitations"></a>Ultra disk 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

Ultra disks 사용을 시작 하려는 경우 [Azure ultra disks 사용](disks-enable-ultra-ssd.md)에 대 한 문서를 참조 하세요.

## <a name="premium-ssds"></a>프리미엄 SSD

Azure 프리미엄 SSD는 IO(입출력) 집약적 워크로드가 있는 VM(가상 머신)에 대기 시간이 짧은 고성능 디스크를 지원합니다. Premium Ssd의 속도와 성능을 활용 하기 위해 기존 VM 디스크를 premium Ssd로 마이그레이션할 수 있습니다. Premium Ssd는 중요 업무용 프로덕션 응용 프로그램에 적합 하지만 호환 되는 VM 시리즈에만 사용할 수 있습니다.

프리미엄 저장소에 대 한 크기 호환성을 비롯 하 여 Windows 또는 Linux의 개별 Azure VM 유형 및 크기에 대 한 자세한 내용은 [Azure의 가상 머신 크기](sizes.md)를 참조 하세요. 각 개별 VM 크기 문서를 확인 하 여 premium storage와 호환 되는지 확인 해야 합니다.

### <a name="premium-ssd-size"></a>Premium SSD 크기
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

프리미엄 저장소 디스크가 프로 비전 되 면 용량, IOPS 및 처리량이 보장 됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 스토리지 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 애플리케이션에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다. Premium Ssd는 앞의 표 99.9%에 설명 된 한 자리 밀리초 대기 시간, 대상 IOPS 및 처리량을 제공 하도록 설계 되었습니다.

### <a name="premium-ssd-bursting"></a>Premium SSD 버스트

Premium Ssd는 IO 패턴의 예기치 않은 변경에 대 한 더 높은 허용 오차를 제공 하는 디스크 버스트를 제공 합니다. 디스크 버스트는 OS 디스크 부팅 중에 특히 급증 트래픽이 있는 응용 프로그램에 유용 합니다. Azure 디스크의 버스팅 작동 방식에 관한 자세한 내용은 [디스크 수준 버스팅](disk-bursting.md#disk-level-bursting)을 참조하세요.

### <a name="premium-ssd-transactions"></a>Premium SSD 트랜잭션

Premium Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다.

## <a name="standard-ssds"></a>표준 SSD

Azure standard Ssd는 낮은 IOPS 수준에서 일관 된 성능을 필요로 하는 워크 로드에 최적화 되어 있습니다. 이는 온-프레미스 HDD (하드 디스크 드라이브) 솔루션에서 지 원하는 다양 한 워크 로드를 사용 하는 고객에 게 특히 적합 합니다. 표준 Hdd에 비해 표준 Ssd는 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공합니다. 표준 Ssd는 웹 서버, 낮은 IOPS 응용 프로그램 서버, 가벼운 사용 엔터프라이즈 응용 프로그램 및 비프로덕션 워크 로드에 적합 합니다. Standard Hdd와 마찬가지로 standard Ssd는 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="standard-ssd-size"></a>표준 SSD 크기

[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

표준 Ssd는 1 자리 밀리초 대기 시간 및 IOPS 및 처리량을 앞의 표 99%에 설명된 제한까지 제공하도록 설계되었습니다. 트래픽 패턴에 따라 실제 IOPS 및 처리량은 달라질 수 있습니다. 표준 SSD는 HDD 디스크보다 더 일관적인 성능과 더 짧은 대기 시간을 제공합니다.

### <a name="standard-ssd-transactions"></a>표준 SSD 트랜잭션

표준 SSD의 경우 256 KiB 미만의 처리량에 해당하는 각 I/O 작업은 단일 I/O 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다. 이러한 트랜잭션은 청구 가능한 비용을 발생 시킵니다.

### <a name="standard-ssd-bursting"></a>표준 SSD 버스트

표준 Ssd는 디스크 버스트를 제공 하므로 예측할 수 없는 IO 패턴 변경에 대 한 더 나은 허용 오차를 제공 합니다. OS 부팅 디스크 및 응용 프로그램의 트래픽 급증 가능성은 모두 디스크 버스트를 활용 합니다. Azure 디스크의 버스팅 작동 방식에 관한 자세한 내용은 [디스크 수준 버스팅](disk-bursting.md#disk-level-bursting)을 참조하세요.

## <a name="standard-hdds"></a>표준 HDD

Azure standard Hdd는 대기 시간을 허용 하는 워크 로드를 실행 하는 Vm에 대해 안정적인 저렴 한 디스크 지원을 제공 합니다. Standard storage를 사용 하는 경우 데이터가 Hdd에 저장 되 고 성능이 SSD 기반 디스크 보다 더 광범위 하 게 달라질 수 있습니다. 표준 Hdd는 대부분의 IO 작업에 대해 10 밀리초 미만의 쓰기 대기 시간 및 20 밀리초 미만의 읽기 대기 시간을 제공 하도록 설계 되었습니다. 그러나 실제 성능은 IO 크기 및 워크 로드 패턴에 따라 달라질 수 있습니다. VM을 사용할 때 표준 HDD 디스크는 개발/테스트 시나리오 및 비교적 중요하지 않은 워크로드에 사용할 수 있습니다. Standard Hdd는 모든 Azure 지역에서 사용할 수 있으며 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="standard-hdd-size"></a>표준 HDD 크기
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="standard-hdd-transactions"></a>표준 HDD 트랜잭션

표준 Hdd 경우 각 i/o 작업은 i/o 크기와 상관 없이 단일 트랜잭션으로 간주 됩니다. 이러한 트랜잭션은 청구에 영향을 미칩니다.

## <a name="billing"></a>결제

관리 디스크를 사용할 때 적용되는 청구 고려 사항은 다음과 같습니다.

- 디스크 유형
- 관리 디스크 크기
- 스냅샷
- 아웃바운드 데이터 전송
- 트랜잭션 수

**관리 디스크 크기**: 관리 디스크는 프로 비전 된 크기에 따라 요금이 청구 됩니다. Azure에서 프로비저닝된 크기(반올림)는 가장 가깝게 제안되는 디스크 크기에 매핑됩니다. 제안되는 디스크 크기에 대한 자세한 내용은 이전 표를 참조하세요. 각 디스크는 지원 되는 프로 비전 된 디스크 크기 제공에 매핑되고 그에 따라 요금이 청구 됩니다. 예를 들어 200 GiB 표준 SSD를 프로 비전 한 경우 E15 (256 GiB)의 디스크 크기 제안에 매핑됩니다. 프로비저닝된 디스크에 대한 청구는 스토리지 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로 비전 하 고 20 시간 사용 후 삭제 합니다. 이 경우 디스크에 기록 되는 데이터 양에 관계 없이 20 시간으로 비례 하는 E10 제품에 대해 요금이 청구 됩니다.

**스냅샷**: 스냅샷 요금은 사용된 크기에 따라 청구됩니다. 예를 들어 프로 비전 된 용량이 64 GiB이 고 실제 사용 된 데이터 크기가 10 GiB 인 관리 디스크의 스냅숏을 만듭니다. 이 경우 스냅숏은 10 GiB의 사용 된 데이터 크기에 대해서만 요금이 청구 됩니다.

스냅샷에 대한 자세한 내용은 [관리형 디스크 개요](managed-disks-overview.md#managed-disk-snapshots)의 스냅샷 섹션을 참조하세요.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용량에 대해 청구가 발생합니다.

**트랜잭션**: 표준 관리 디스크에서 수행 되는 트랜잭션 수에 대 한 요금이 청구 됩니다. 표준 SSD의 경우 256 KiB 미만의 처리량에 해당하는 각 I/O 작업은 단일 I/O 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다. 표준 Hdd 경우 각 IO 작업은 i/o 크기와 상관 없이 단일 트랜잭션으로 간주 됩니다.

관리 디스크 (트랜잭션 비용 포함)의 가격 책정에 대 한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks)을 참조 하세요.

### <a name="ultra-disks-vm-reservation-fee"></a>Ultra disks VM 예약 요금

Azure VM은 울트라 디스크와 호환되는지 여부를 나타낼 수 있습니다. Ultra 디스크 호환 VM은 계산 VM 인스턴스와 블록 저장소 크기 조정 단위 간에 전용 대역폭 용량을 할당 하 여 성능을 최적화 하 고 대기 시간을 줄입니다. VM에이 기능을 추가 하면 예약 요금이 발생 합니다. 예약 요금은 ultra disk를 연결 하지 않고 VM에서 ultra disk 기능을 사용 하도록 설정한 경우에만 적용 됩니다. 울트라 디스크가 ultra disk 호환 VM에 연결 되 면 예약 요금이 적용 되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다.

> [!Note]
> [제한된 코어 VM 크기](constrained-vcpu.md)의 경우 예약 요금은 제한된 코어가 아닌 실제 vCPU 수를 기준으로합니다. Standard_E32 8s_v3 경우 예약 요금은 32코어 기준입니다.

울트라 디스크 가격 책정 세부 정보는 [Azure Disks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="azure-disk-reservation"></a>Azure 디스크 예약

디스크 예약은 1 년간의 디스크 저장소에 대 한 사전 구매에 대 한 할인을 제공 하므로 총 비용을 절감할 수 있습니다. 디스크 예약을 구매할 때 대상 지역에서 특정 디스크 SKU를 선택 합니다. 예를 들어 미국 중부 지역에서 1 년 동안 5 개의 P30 (1 TiB) premium Ssd를 선택할 수 있습니다. 디스크 예약 환경은 Azure 예약 VM 인스턴스와 유사 합니다. VM 및 디스크 예약을 번들로 묶어 절감 효과를 극대화할 수 있습니다. 현재 Azure 디스크 예약은 모든 프로덕션 지역의 P30 (1 TiB)에서 P80 (32 TiB)까지 프리미엄 SSD Sku에 대 한 1 년 약정 계획을 제공 합니다. 예약 된 디스크 가격 책정에 대 한 자세한 내용은 [Azure 디스크 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 [관리 디스크 가격](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.
