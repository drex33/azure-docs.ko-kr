---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 7b1ac1f3b2fcb8c999276fecec495cf496108c12
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134930"
---
### <a name="on-demand-bursting"></a>주문형 버스팅

디스크 버스팅의 주문형 버스팅 모델을 사용하는 프리미엄 SSD는 워크로드에서 필요한 만큼 자주 원래의 프로비저닝된 목표를 초과하여 최대 버스트 목표까지 버스트할 수 있습니다. 예를 들어 1TiB P30 디스크에서 프로비전된 IOPS는 5,000 IOPS입니다. 이 디스크에서 디스크 버스팅이 사용하도록 설정되면 워크로드에서 이 디스크에 대한 IO를 30,000 IOPS 및 1,000MBps의 최대 버스트 성능까지 실행할 수 있습니다. 지원 되는 각 디스크의 최대 버스트 목표는 [VM 디스크의 확장성 및 성능 목표](../articles/virtual-machines/disks-scalability-targets.md#premium-ssd-managed-disks-per-disk-limits)를 참조 하세요.

워크로드가 프로비전된 성능 목표를 초과하여 자주 실행될 필요가 있는 경우 디스크 버스팅은 비용 효율적이지 않습니다. 이 경우 더 나은 기준 성능을 위해 디스크의 성능 계층을 [더 높은 계층](../articles/virtual-machines/disks-performance-tiers.md)으로 변경하는 것이 좋습니다. 청구 세부 정보를 검토하고 워크로드의 트래픽 패턴과 비교하여 평가합니다.

주문형 버스팅을 사용하도록 설정하기 전에 다음 사항을 이해해야 합니다.

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>결제

주문형 버스팅 모델을 사용하는 프리미엄 SSD에는 시간당 버스트 사용 정액 요금이 부과되며, 프로비저닝된 목표를 초과하는 모든 버스트 트랜잭션에는 트랜잭션 비용이 적용됩니다. 트랜잭션 비용은 프로비전된 목표를 초과하는 읽기 및 쓰기를 모두 포함하여 캐시되지 않은 디스크 IO를 기반으로 하는 종량제 모델을 사용하여 청구됩니다. 청구 시간에 대한 디스크 트래픽 패턴의 예는 다음과 같습니다.

디스크 구성: 프리미엄 SSD – 1TiB(P30), 디스크 버스팅 사용

- 00:00:00 – 00:10:00 디스크 IOPS가 프로비전된 목표(5,000 IOPS) 미만임 
- 00:10:01 – 00:10:10 애플리케이션에서 일괄 작업을 실행하여 디스크 IOPS가 10초 동안 6,000 IOPS로 버스트됨 
- 00:10:11 – 00:59:00 디스크 IOPS가 프로비전된 목표(5,000 IOPS) 미만임 
- 00:59:01 – 01:00:00 애플리케이션에서 다른 일괄 작업을 실행하여 디스크 IOPS가 60초 동안 7,000 IOPS로 버스트됨 

이 청구 시간에서 버스팅 비용은 두 가지 요금으로 구성됩니다.

첫 번째 요금은 $X의 버스트 사용 정액 요금(지역에 따라 결정됨)입니다. 이 정액 요금은 사용하지 않도록 설정될 때까지 연결 상태에 관계없이 디스크에 대해 항상 청구됩니다. 

두 번째는 버스트 트랜잭션 비용입니다. 디스크 버스팅이 두 개의 시간 슬롯에서 발생했습니다. 00:10:01 – 00:10:10에서 누적 버스트 트랜잭션 수는 (6,000 – 5,000) X 10 = 10,000개입니다. 00:59:01 – 01:00:00에서 누적 버스트 트랜잭션 수는 (7,000 – 5,000) X 60 = 120,000개입니다. 총 버스트 트랜잭션 수는 10,000 + 120,000 = 130,000입니다. 버스트 트랜잭션 비용은 10,000개 트랜잭션의 13개 단위를 기준으로 $Y로 청구됩니다(지역별 가격 책정 기준).

따라서 이 청구 시간의 총 디스크 버스팅 비용은 $X + $Y입니다. 프로비전된 목표(MBps)를 초과하는 버스팅에도 동일한 계산이 적용됩니다. 초과분(MB)을 IO 크기가 256KB인 트랜잭션으로 변환합니다. 디스크 트래픽이 프로비전된 IOPS 및 MBps 목표를 모두 초과하는 경우 아래 예를 참조하여 버스트 트랜잭션 수를 계산할 수 있습니다. 

디스크 구성: 프리미엄 SSD – 1TB(P30), 디스크 버스팅 사용.

- 00:00:01 – 00:00:05 애플리케이션에서 일괄 작업을 실행하여 디스크 IOPS가 5초 동안 10,000 IOPS 및 300MBps로 버스트됨
- 00:00:06 – 00:00:10 애플리케이션에서 복구 작업을 실행하여 디스크 IOPS가 5초 동안 6,000 IOPS 및 600MBps로 버스트됨

버스트 트랜잭션은 IOPS 또는 MBps 버스팅의 최대 트랜잭션 수로 계산됩니다. 00:00:01 – 00:00:05에서 누적 버스트 트랜잭션 수는 max ((10,000 – 5,000), (300 - 200) * 1024 / 256)) * 5 = 25,000개 트랜잭션입니다. 00:00:06 – 00:00:10에서 누적 버스트 트랜잭션 수는 max ((6,000 – 5,000), (600 - 200) * 1024 / 256)) * 5 = 8,000개 트랜잭션입니다. 또한 버스트 사용 정액 요금을 포함하여 주문형 기반 디스크 버스팅을 사용하도록 설정하기 위한 총 비용을 얻을 수 있습니다. 

가격 책정에 대한 자세한 내용은 [Managed Disks 가격 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하고, [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 사용하여 워크로드를 평가할 수 있습니다. 


주문형 버스트를 사용하도록 설정하려면 [주문형 버스트 사용](../articles/virtual-machines/disks-enable-bursting.md)을 참조하세요.

### <a name="credit-based-bursting"></a>크레딧 기반 버스팅

프리미엄 SSD의 경우, 크레딧 기반 버스팅은 디스크 크기 P20 이하에서 사용할 수 있습니다. 표준 SSD의 경우, 크레딧 기반 버스팅은 디스크 크기 E30 이하에서 사용할 수 있습니다. 표준 및 프리미엄 SSD의 경우, 크레딧 기반 버스팅은 Azure 퍼블릭, Government, 중국 클라우드의 모든 지역에서 사용할 수 있습니다. 디스크 버스팅은 기본적으로 지원되는 디스크 크기의 모든 새 배포와 기존 배포에서 사용하도록 설정됩니다. VM 수준 버스팅은 크레딧 기반 버스팅만 사용합니다.

## <a name="virtual-machine-level-bursting"></a>가상 머신 수준 버스팅

VM 수준 버스팅은 크레딧 기반 모델만 버스팅에 사용하며, 이를 지원하는 모든 VM에 대해 기본적으로 사용하도록 설정됩니다.

VM 수준 버스팅은 다음 크기로 지원되는 Azure 퍼블릭 클라우드의 모든 지역에서 사용하도록 설정됩니다. 
- [Dsv4 시리즈](../articles/virtual-machines/dv4-dsv4-series.md)
- [Dasv4 시리즈](../articles/virtual-machines/dav4-dasv4-series.md)
- [Ddsv4 시리즈](../articles/virtual-machines/ddv4-ddsv4-series.md)
- [Dasv5 시리즈](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Dadsv5 시리즈](../articles/virtual-machines/dasv5-dadsv5-series.md)
- [Esv4 시리즈](../articles/virtual-machines/ev4-esv4-series.md)
- [Easv4 시리즈](../articles/virtual-machines/eav4-easv4-series.md)
- [Edsv4 시리즈](../articles/virtual-machines/edv4-edsv4-series.md)
- [Easv5 시리즈](../articles/virtual-machines/easv5-eadsv5-series.md)
- [Eadsv5 시리즈](../articles/virtual-machines/easv5-eadsv5-series.md)
- [B 시리즈](../articles/virtual-machines/sizes-b-series-burstable.md)
- [Fsv2 시리즈](../articles/virtual-machines/fsv2-series.md)
- [Dsv3 시리즈](../articles/virtual-machines/dv3-dsv3-series.md)
- [Esv3 시리즈](../articles/virtual-machines/ev3-esv3-series.md)
- [Lsv2 시리즈](../articles/virtual-machines/lsv2-series.md)

## <a name="bursting-flow"></a>버스팅 흐름

버스팅 크레딧 시스템은 VM 수준과 디스크 수준 모두에서 동일한 방식으로 적용됩니다. 리소스(VM 또는 디스크)는 자체 버스트 버킷에 완전히 적립되는 크레딧으로 시작합니다. 이러한 크레딧을 사용하면 최대 버스트 속도로 최대 30분 동안 버스트할 수 있습니다. 리소스의 IOPS 또는 MB/초가 리소스의 성능 목표 미만으로 활용될 때마다 크레딧이 누적됩니다. 버스팅 크레딧이 리소스에 적립되고 추가 성능이 워크로드에 필요한 경우 리소스는 해당 크레딧을 사용하여 워크로드 요구 사항에 맞게 성능 제한을 초과하고 성능을 향상시킬 수 있습니다.

![버스팅 버킷 다이어그램](media/managed-disks-bursting/bucket-diagram.jpg)

사용 가능한 크레딧을 사용하는 방법은 사용자가 결정합니다. 30분의 버스트 크레딧을 하루 동안 연속적 또는 산발적으로 사용할 수 있습니다. 리소스가 배포되면 전체 크레딧이 할당됩니다. 이러한 크레딧이 고갈되는 경우 다시 적립하는 데 하루도 걸리지 않습니다. 크레딧은 원하는 대로 사용할 수 있으며, 리소스를 버스트하기 위해 버스트 버킷을 완전히 채울 필요는 없습니다. 버스트 누적은 사용되지 않은 IOPS와 성능 목표보다 낮은 MB/초를 기반으로 하므로 각 리소스에 따라 달라집니다. 기준 성능이 높은 리소스는 기준 성능이 낮은 리소스보다 더 빠르게 버스팅 크레딧을 적립할 수 있습니다. 예를 들어 P1 디스크 유휴 상태는 초당 120 IOPS가 발생하지만, 유휴 상태인 P20 디스크는 초당 2,300 IOPS가 발생합니다.

## <a name="bursting-states"></a>버스팅 상태
버스팅이 사용하도록 설정된 리소스에는 세 가지 상태가 있을 수 있습니다.
- **발생** – 리소스의 IO 트래픽에서 성능 목표 미만으로 사용하고 있습니다. IOPS 및 MB/초에 대한 버스팅 크레딧 누적은 서로 별도로 수행됩니다. 리소스에서 IOPS 크레딧을 적립하고 MB/초 크레딧을 사용할 수 있으며, 그 반대의 경우도 마찬가지입니다.
- **버스팅** – 리소스의 트래픽에서 성능 목표를 초과하여 사용하고 있습니다. 버스트 트래픽은 IOPS 또는 대역폭의 크레딧을 독립적으로 사용합니다.
- **상수** – 리소스의 트래픽이 정확히 성능 목표에 있습니다.

## <a name="bursting-examples"></a>버스팅 예

다음 예에서는 다양한 VM 및 디스크의 조합에서 버스팅이 작동하는 방식을 보여 줍니다. 예를 쉽게 따를 수 있도록 MB/초에 중점을 두지만 동일한 논리가 IOPS에 독립적으로 적용됩니다.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>버스트 가능한 디스크가 있는 버스트 불가능한 가상 머신
**VM 및 디스크 조합:** 
- Standard_D8as_v4 
    - 캐시되지 않은 MB/초: 192
- P4 OS 디스크
    - 프로비전된 MB/초: 25
    - 최대 버스트 MB/초: 170 
- 2개 P10 데이터 디스크 
    - 프로비전된 MB/초: 100
    - 최대 버스트 MB/초: 170

 VM이 부팅되면 OS 디스크에서 데이터를 검색합니다. OS 디스크는 부팅 중인 VM의 일부이므로 OS 디스크에는 버스팅 크레딧이 완전히 적립되어 있습니다. 이러한 크레딧을 사용하면 OS 디스크에서 시작을 초당 170MB/초로 버스트할 수 있습니다.

![VM은 192MB/초의 처리량에 대한 요청을 OS 디스크에 보내고, OS 디스크는 170MB/초의 데이터로 응답합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

부팅이 완료되면 애플리케이션이 VM에서 실행되고 중요하지 않은 워크로드가 있습니다. 이 워크로드에는 모든 디스크에서 균등하게 분산되는 15MB/초가 필요합니다.

![애플리케이션에서 15MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 요청을 받아 5MB/초에 대한 요청을 각 디스크에 보내고, 각 디스크에서 5MB/초를 반환하고, VM에서 15MB/초를 애플리케이션에 반환합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

그런 다음, 애플리케이션에서 192MB/초가 필요한 일괄 작업을 처리해야 합니다. 2MB/초는 OS 디스크에서 사용하고, 나머지는 데이터 디스크 간에 균등하게 분할됩니다.

![애플리케이션에서 192MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 요청을 받아 대량 요청을 데이터 디스크(각각 95MB/초) 및 OS 디스크(2MB/초)에 보내고, 데이터 디스크에서 이 요구를 충족하기 위해 버스트하고, 모든 디스크에서 요청된 처리량을 VM에 반환하고, VM에서 이를 애플리케이션에 반환합니다.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>버스트 불가능한 디스크가 있는 버스트 가능한 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시되지 않은 MB/초: 160
    - 최대 버스트 MB/초: 1,280
- P50 OS 디스크
    - 프로비전된 MB/초: 250 
- 2개 P50 데이터 디스크 
    - 프로비전된 MB/초: 250

 초기 부팅 후 애플리케이션이 VM에서 실행되고 중요하지 않은 워크로드가 있습니다. 이 워크로드에는 모든 디스크에서 균등하게 분산되는 30MB/초가 필요합니다.
![애플리케이션에서 30MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 요청을 받아 10MB/초에 대한 요청을 각 디스크에 보내고, 각 디스크에서 10MB/초를 반환하고, VM에서 30MB/초를 애플리케이션에 반환합니다.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

그런 다음, 애플리케이션에서 600MB/초가 필요한 일괄 작업을 처리해야 합니다. Standard_L8s_v2에서 버스트하여 이 요구를 충족한 다음, 디스크에 대한 요청이 P50 디스크에서 균등하게 분산됩니다.

![애플리케이션에서 600MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 버스트를 수행하여 요청을 받고 200MB/초에 대한 요청을 각 디스크에 보내고, 각 디스크에서 200MB/초를 반환하고, VM에서 버스트하여 600MB/초를 애플리케이션에 반환합니다.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>버스트 가능한 디스크가 있는 버스트 가능한 가상 머신
**VM 및 디스크 조합:** 
- Standard_L8s_v2 
    - 캐시되지 않은 MB/초: 160
    - 최대 버스트 MB/초: 1,280
- P4 OS 디스크
    - 프로비전된 MB/초: 25
    - 최대 버스트 MB/초: 170 
- 2개 P4 데이터 디스크 
    - 프로비전된 MB/초: 25
    - 최대 버스트 MB/초: 170 

VM이 시작되면 버스트하여 OS 디스크의 버스트 제한(1,280MB/초)을 요청하고, OS 디스크에서 170MB/초의 버스트 성능으로 응답합니다.

![시작할 때 VM에서 버스트하여 1,280MB/초의 요청을 OS 디스크에 보내고, OS 디스크에서 버스트하여 1,280MB/초를 반환합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

시작한 후에는 중요하지 않은 워크로드가 있는 애플리케이션을 시작합니다. 이 애플리케이션에는 모든 디스크에서 균등하게 분산되는 15MB/초가 필요합니다.

![애플리케이션에서 15MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 요청을 받아 5MB/초에 대한 요청을 각 디스크에 보내고, 각 디스크에서 5MB/초를 반환하고, VM에서 15MB/초를 애플리케이션에 반환합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

그런 다음, 애플리케이션에서 360MB/초가 필요한 일괄 작업을 처리해야 합니다. Standard_L8s_v2에서 버스트하여 이 요구를 충족한 다음, 요청합니다. OS 디스크에는 20MB/초만 필요합니다. 나머지 340MB/초는 버스팅 P4 데이터 디스크에서 처리됩니다.

![애플리케이션에서 360MB/초의 처리량에 대한 요청을 VM에 보내고, VM에서 버스트를 수행하여 요청을 받고 OS 디스크의 170MB/초 및 20MB/초에 대한 요청을 각 데이터 디스크에 보내고, 각 디스크에서 요청된 MB/초를 반환하고, VM에서 버스트하여 360MB/초를 애플리케이션에 반환합니다.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)