---
title: Azure IaaS VM에 대한 디스크 유형 선택 - 관리 디스크
description: 울트라 디스크, 프리미엄 SSD, 표준 SSD, 표준 HDD를 포함하여 가상 머신에 사용 가능한 Azure 디스크 유형을 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ea55522714beea9274f27bec36232d9e50c90332
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688051"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure에서 사용할 수 있는 디스크 유형

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure Managed Disks는 현재 4가지 디스크 유형을 제공하며, 각 유형은 특정 고객 시나리오를 대상으로합니다.

## <a name="disk-comparison"></a>디스크 비교

다음 표에서는 사용할 디스크를 결정하는 데 도움을 주기 위해 관리 디스크로 사용할 수 있는 울트라 디스크, 프리미엄 SSD(반도체 드라이브)(미리 보기), 표준 SSD 및 표준 HDD(하드 디스크 드라이브)를 비교하고 있습니다.

| 세부 정보 | Ultra disk | 프리미엄 SSD | 표준 SSD | 표준 HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|디스크 유형   |SSD   |SSD   |SSD   |HDD   |
|시나리오   |IO 집약적 워크로드 - [SAP HANA](workloads/sap/hana-vm-operations-storage.md), 최상위 계층 데이터베이스(예: SQL, Oracle) 및 다른 트랜잭션 집약적 워크로드   |프로덕션 및 성능이 중요한 워크로드   |웹 서버, 조금 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트   |백업, 중요하지 않음, 가끔 액세스   |
|최대 디스크 크기   |65536기비바이트(GiB)    |32,767GiB    |32,767GiB   |32,767GiB   |
|최대 처리량   |2,000MB/s    |900MB/s   |750MB/s   |500MB/s   |
|최대 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra disk

Azure ultra disks는Azure IaaS VM에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. 울트라 디스크의 몇 가지 추가 이점에는 VM(가상 머신)을 다시 시작하지 않고도 워크로드와 함께 디스크의 성능을 동적으로 변경할 수 있다는 것이 포함됩니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다. Ultra disks는 데이터 디스크로만 사용할 수 있습니다. 프리미엄 SSD는 OS 디스크로 사용하는 것이 좋습니다.

### <a name="performance"></a>성능

울트라 SSD를 프로비저닝할 때 디스크의 용량과 성능을 독립적으로 구성할 수 있습니다. 울트라 디스크는 4GiB~64TiB의 몇 가지 고정된 크기로 제공되며, IOPS와 처리량을 독립적으로 구성할 수 있는 유연한 성능 구성 모델을 갖추고 있습니다.

울트라 디스크의 몇가지 주요 기능은 다음과 같습니다.

- 디스크 용량: 울트라 디스크 용량의 범위는 4 GiB에서 64 TiB까지입니다.
- 디스크 IOPS: Ultra 디스크는 디스크당 최대 160K IOPS의 300IOPS/GiB의 IOPS 제한을 지원합니다. 프로비저닝한 IOPS를 달성하려면 선택한 디스크 IOPS가 VM IOPS 제한보다 작은지 확인합니다. 디스크당 최소 보장 IOPS는 1 IOPS/GiB이며 전체 기준은 최소 100 IOPS입니다. 예를 들어 4 GiB 울트라 디스크가 있는 경우 8 IOPS 대신 최소 100 IOPS가 있습니다.
- 디스크 처리량: Ultra 디스크를 사용하여 단일 디스크의 처리량 한도는 디스크당 최대 2000MBps로 각 프로비전된 IOPS에 대해 256KiB/s입니다(여기서 MBps = 초당 10^6바이트). 디스크당 보장되는 최소 처리량은 프로비저닝된 각 IOPS당 4KiB/s이고, 전체 기준은 1MBps 이상입니다.
- Ultra Disks는 가상 머신에서 디스크를 분리하지 않고 런타임에 디스크 성능 특성(IOPS 및 처리량)을 조정할 수 있습니다. 디스크 성능 크기 조정 작업이 디스크에서 실행된 후 변경 내용이 실제로 적용되려면 최대 한 시간이 걸릴 수 있습니다. 24시간 동안 4개의 성능 크기 조정 작업으로 제한됩니다. 성능 대역폭 용량 부족으로 인해 성능 크기 조정 작업이 실패할 수 있습니다.

### <a name="disk-size"></a>디스크 크기

|디스크 크기(GiB)  |IOPS 한도  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |153,600         |2,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |2,000         |

울트라 디스크는 밀리초 이하의 대기 시간과 이전 표 99.99%에 설명된 대상 IOPS 및 처리량을 제공하도록 설계되었습니다.

### <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


울트라 디스크 사용을 시작하려면 [Azure 울트라 디스크 사용](disks-enable-ultra-ssd.md)이라는 주제에 대한 문서를 참조하세요.

## <a name="premium-ssd"></a>프리미엄 SSD

Azure 프리미엄 SSD는 IO(입출력) 집약적 워크로드가 있는 VM(가상 머신)에 대기 시간이 짧은 고성능 디스크를 지원합니다. 프리미엄 스토리지 디스크의 속도와 성능을 활용하기 위해 기존 VM 디스크를 프리미엄 SSD로 마이그레이션할 수 있습니다. 프리미엄 SSD는 중요 업무용 프로덕션 애플리케이션에 적합합니다. 프리미엄 SSD는 Premium Storage와 호환되는 VM 시리즈에서만 사용할 수 있습니다.

프리미엄 저장소와 호환되는 크기를 포함하여 Windows 또는 Linux용 Azure의 개별 VM 유형 및 크기에 대한 자세한 내용은 [Azure의 가상 머신 크기](sizes.md)를 참조하세요. 이 문서에서는 각 개별 VM 크기 문서를 확인하여 Premium Storage와 호환되는지 확인해야 합니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Premium Storage 디스크를 프로비전하면 표준 스토리지와 달리, 해당 디스크의 용량, IOPS 및 처리량이 보장됩니다. 예를 들어 P50 디스크를 만들면 Azure에서 해당 디스크에 스토리지 용량 4,095GB, 7,500 IOPS, 250MB/초 처리량이 프로비전됩니다. 애플리케이션에서 용량 및 성능의 전체 또는 일부를 사용할 수 있습니다. 프리미엄 SSD는 한자리 밀리초 미만의 낮은 지연 시간과 시간의 99.9% 동안 이전 표에서 설명한 목표 IOPS와 처리량을 제공하도록 설계되었습니다.

## <a name="bursting"></a>버스팅

프리미엄 SSD는 디스크 버스팅을 제공합니다. 디스크 버스트는 IO 패턴의 예기치 않은 변경에 대한 더 나은 허용 오차를 제공합니다. 급증 트래픽을 사용하는 OS 디스크 부팅 및 애플리케이션에 대해 가장 잘 활용할 수 있습니다. Azure 디스크의 버스팅 작동 방식에 관한 자세한 내용은 [디스크 수준 버스팅](disk-bursting.md#disk-level-bursting)을 참조하세요.

### <a name="transactions"></a>트랜잭션

Premium Ssd의 경우 256 KiB 처리량 보다 작거나 같은 각 i/o 작업은 단일 i/o 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다.

## <a name="standard-ssd"></a>표준 SSD

Azure 표준 SSD는 더 낮은 IOPS 수준에서 일관된 성능이 필요한 워크로드에 최적화된 비용 효율적 스토리지 옵션입니다. 특히 표준 SSD는 온-프레미스 HDD 솔루션에서 실행 중인 다양한 워크로드에 문제가 발생해서 클라우드로 전환하고자 하는 경우에 적합한 입문용 환경을 제공합니다. 표준 Hdd에 비해 표준 Ssd는 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공합니다. 표준 SSD는 웹 서버, 낮은 IOPS 애플리케이션 서버, 가끔 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트 워크로드에 적합합니다. Standard Hdd와 마찬가지로 standard Ssd는 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

표준 Ssd는 1 자리 밀리초 대기 시간 및 IOPS 및 처리량을 앞의 표 99%에 설명된 제한까지 제공하도록 설계되었습니다. 트래픽 패턴에 따라 실제 IOPS 및 처리량은 달라질 수 있습니다. 표준 SSD는 HDD 디스크보다 더 일관적인 성능과 더 짧은 대기 시간을 제공합니다.

### <a name="transactions"></a>트랜잭션

표준 SSD의 경우 256 KiB 미만의 처리량에 해당하는 각 I/O 작업은 단일 I/O 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다. 이러한 트랜잭션은 청구에 영향을 미칩니다.

### <a name="bursting"></a>버스팅

표준 SSD는 디스크 버스팅을 제공합니다. 디스크 버스트는 IO 패턴의 예기치 않은 변경에 대한 더 나은 허용 오차를 제공합니다. 급증 트래픽을 사용하는 OS 디스크 부팅 및 애플리케이션에 대해 가장 잘 활용할 수 있습니다. Azure 디스크의 버스팅 작동 방식에 관한 자세한 내용은 [디스크 수준 버스팅](disk-bursting.md#disk-level-bursting)을 참조하세요.

## <a name="standard-hdd"></a>표준 HDD

Azure 표준 HDD는 대기 시간에 민감하지 않은 워크로드를 실행하는 VM에 안정적이고 저렴한 디스크를 지원합니다. 표준 스토리지를 사용하는 경우 데이터는 HDD(하드 디스크 드라이브)에 저장됩니다. 표준 HDD 디스크의 대기 시간, IOPS 및 처리량은 SSD 기반 디스크에 비해 크게 달라질 수 있습니다. 표준 HDD 디스크는 대부분의 IO 작업에서 20ms 아래의 10ms 및 읽기 대기 시간에 쓰기 대기 시간을 제공하도록 설계되었지만 실제 성능은 IO 크기 및 워크로드 패턴에 따라 달라질 수 있습니다. VM을 사용할 때 표준 HDD 디스크는 개발/테스트 시나리오 및 비교적 중요하지 않은 워크로드에 사용할 수 있습니다. Standard Hdd는 모든 Azure 지역에서 사용할 수 있으며 모든 Azure Vm에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>트랜잭션

표준 HDD의 경우 각 IO 작업은 I/O 크기에 관계없이 단일 트랜잭션으로 간주됩니다. 이러한 트랜잭션은 청구에 영향을 미칩니다.

## <a name="billing"></a>결제

관리 디스크를 사용할 때 적용되는 청구 고려 사항은 다음과 같습니다.

- 디스크 유형
- 관리 디스크 크기
- 스냅샷
- 아웃바운드 데이터 전송
- 트랜잭션 수

**관리 디스크 크기:** 관리 디스크의 요금은 프로비저닝된 크기에 따라 청구됩니다. Azure에서 프로비저닝된 크기(반올림)는 가장 가깝게 제안되는 디스크 크기에 매핑됩니다. 제안되는 디스크 크기에 대한 자세한 내용은 이전 표를 참조하세요. 각 디스크는 지원되는 프로비저닝된 디스크 크기 제안에 매핑되고 이에 따라 요금이 청구됩니다. 예를 들어 200GiB 표준 SSD를 프로비저닝한 경우 E15(256GiB)의 디스크 크기 제안에 매핑됩니다. 프로비저닝된 디스크에 대한 청구는 스토리지 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 E10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양에 관계없이 적용됩니다.

**스냅샷**: 스냅샷 요금은 사용된 크기에 따라 청구됩니다. 예를 들어 프로비저닝된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 관리 디스크의 스냅샷을 만들면 사용된 10GiB의 데이터 크기에 대해서만 스냅샷 요금이 청구됩니다.

스냅샷에 대한 자세한 내용은 [관리형 디스크 개요](managed-disks-overview.md)의 스냅샷 섹션을 참조하세요.

**아웃바운드 데이터 전송**: [아웃바운드 데이터 전송](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 데이터 센터에서 데이터 전송) 시 대역폭 사용량에 대해 청구가 발생합니다.

**트랜잭션**: 표준 관리 디스크에서 수행하는 트랜잭션 수에 대해 비용이 청구됩니다. 표준 SSD의 경우 256 KiB 미만의 처리량에 해당하는 각 I/O 작업은 단일 I/O 작업으로 간주됩니다. 처리량 256 KiB보다 큰 I/O 작업은 크기가 256 KiB인 여러 I/O로 간주됩니다. 표준 HDD의 경우 각 IO 작업은 I/O 크기에 관계없이 단일 트랜잭션으로 간주됩니다.

트랜잭션 비용을 포함하여 Managed Disks 가격에 대한 자세한 내용은 [Managed Disks 가격](https://azure.microsoft.com/pricing/details/managed-disks)을 참조하세요.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra disk VM 예약 요금

Azure VM은 울트라 디스크와 호환되는지 여부를 나타낼 수 있습니다. 울트라 디스크 호환 VM은 컴퓨팅 VM 인스턴스와 블록 스토리지 배율 단위 간에 전용 대역폭 용량을 할당하여 성능을 최적화하고 대기 시간을 줄입니다. VM에 이 기능을 추가하면 울트라 디스크를 연결하지 않고 VM에서 울트라 디스크 기능을 활성화한 경우에만 적용되는 예약 요금이 발생합니다. 울트라 디스크 호환 VM에 울트라 디스크가 연결되면 이 요금이 적용되지 않습니다. 이 요금은 VM에서 프로비전되는 vCPU당 부과됩니다. 

> [!Note]
> [제한된 코어 VM 크기](constrained-vcpu.md)의 경우 예약 요금은 제한된 코어가 아닌 실제 vCPU 수를 기준으로합니다. Standard_E32 8s_v3 경우 예약 요금은 32코어 기준입니다. 

울트라 디스크 가격 책정 세부 정보는 [Azure Disks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="azure-disk-reservation"></a>Azure 디스크 예약

디스크 예약은 1년 동안 사용할 디스크 스토리지를 미리 구입하여 총 비용을 절감할 수 있는 옵션입니다. 디스크 예약을 구매할 때 대상 지역에서 특정 디스크 SKU를 선택합니다(예: 1년 동안 미국 동부 2 지역의 10 P30(1TiB) 프리미엄 SSD). 예약 환경은 예약된 VM(가상 머신) 인스턴스와 비슷합니다. VM 및 디스크 예약을 번들로 묶어 절감 효과를 극대화할 수 있습니다. 현재 Azure Disks 예약은 모든 프로덕션 지역에서 P30(1TiB)에서 P80(32TiB)으로 프리미엄 SSD SUS에 대한 1년 약정 계획을 제공합니다. 예약 디스크 가격에 대한 자세한 내용은 [Azure Disks 가격 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 [관리 디스크 가격](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.