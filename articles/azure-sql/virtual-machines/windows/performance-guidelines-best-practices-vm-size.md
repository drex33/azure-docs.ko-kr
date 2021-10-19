---
title: 'VM 크기: 성능 모범 사례 및 지침'
description: Azure VM(Virtual Machine)에서 SQL Server의 성능을 최적화하기 위한 VM 크기 모범 사례와 지침을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: pamela
ms.openlocfilehash: c80cf00bf7beb98b8548f0eebfd1cfa241bd2136
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161482"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>VM 크기: Azure VM의 SQL Server에 대한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VMs(Virtual Machines)에서 SQL Server의 성능을 최적화하기 위한 일련의 VM 크기 지침 및 모범 사례를 제공합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크로드가 적은 경우 모든 권장 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

자세한 내용은 이 시리즈의 다른 문서([검사 목록](performance-guidelines-best-practices-checklist.md), [스토리지](performance-guidelines-best-practices-storage.md), [보안](security-considerations-best-practices.md), [HADR 구성](hadr-cluster-best-practices.md), [기준 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조하세요. 


## <a name="checklist"></a>검사 목록

이 문서의 나머지 부분에서 자세히 설명하는 VM 크기 모범 사례에 대한 간략한 개요를 보려면 다음 검사 목록을 검토하세요. 

- [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) 또는 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 이상과 같이 vCPU가 4개 이상인 VM 크기를 사용합니다. 
- SQL Server 워크로드의 최고 성능을 위해 [메모리가 최적화된](../../../virtual-machines/sizes-memory.md) 가상 머신 크기를 사용합니다. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 시리즈, [M](../../../virtual-machines/m-series.md), [Mv2](../../../virtual-machines/mv2-series.md) 시리즈는 OLTP 워크로드에 필요한 최적의 메모리 대 vCore 비율을 제공합니다. 두 M 시리즈 VMs는 중요 업무용 워크로드에 필요한 가장 높은 메모리 대 vCore 비율을 제공하며, 데이터 웨어하우스 워크로드에도 적합합니다. 
- 중요 업무용 및 데이터 웨어하우스 워크로드에 대한 메모리 대 vCore 비율을 높이는 것이 좋습니다. 
- 최적의 SQL Server 성능을 위해 SQL Server 설정 및 스토리지 옵션이 구성되었으므로 Azure Virtual Machine 마켓플레이스 이미지를 사용합니다. 
- 대상 워크로드의 성능 특성을 수집하고 수집한 정보를 사용하여 비즈니스에 적합한 VM 크기를 결정합니다.
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) [SKU 권장 사항](/sql/dma/dma-sku-recommend-sql-db) 도구를 사용하여 기존 SQL Server 워크로드에 적합한 VM 크기를 찾습니다.

VM 크기 검사 목록을 다른 목록과 비교하려면 포괄적인 [성능 모범 사례 검사 목록](performance-guidelines-best-practices-checklist.md)을 참조하세요. 

## <a name="overview"></a>개요

Azure VM에서 SQL Server를 만들 때 필요한 워크로드 유형을 신중하게 고려해야 합니다. 기존 환경을 마이그레이션하는 경우 Azure VM 요구 사항에 대한 SQL Server를 확인하기 위해 [성능 기준을 수집](performance-guidelines-best-practices-collect-baseline.md)합니다. 새 VM인 경우 공급 업체 요구 사항에 따라 새 SQL Server VM를 만듭니다. 

클라우드용으로 빌드된 새 애플리케이션을 사용하여 새 SQL Server VM을 만드는 경우 데이터 및 사용량 요구 사항이 증가함에 따라 SQL Server VM 크기를 쉽게 지정할 수 있습니다.
하위 계층 D 시리즈, B 시리즈 또는 Av2 시리즈를 사용하여 개발 환경을 시작하고 시간이 지남에 따라 환경을 확장합니다. 

포털의 스토리지 구성과 함께 SQL Server VM 마켓플레이스 이미지를 사용합니다. 이렇게 하면 워크로드에 필요한 크기, IOPS 및 처리량을 얻는 데 필요한 스토리지 풀을 보다 쉽게 만들 수 있습니다. 프리미엄 스토리지 및 프리미엄 스토리지 캐싱을 지원하는 SQL Server VM을 선택하는 것이 중요합니다. 자세히 알아보려면 [스토리지](performance-guidelines-best-practices-storage.md) 문서를 참조하세요. 

프로덕션 OLTP 환경에 권장되는 최소값은 4개의 vCore, 32GB의 메모리 및 메모리 대 vCore 비율 8입니다. 새 환경의 경우, 데이터 및 계산 요구 사항이 변경될 때 4개의 vCore 머신에서 시작하여 8, 16, 32개 이상의 Vcore로 확장합니다. OLTP 처리량의 경우 모든 vCore에 대해 5000 IOPS를 포함하는 SQL Server VM을 대상으로 합니다. 

SQL Server 데이터 웨어하우스와 업무상 중요한 환경에는 메모리 대 vCore 비율이 8 이상으로 확장해야 하는 경우가 많습니다. 중간 규모 환경의 경우에는 더 큰 데이터 웨어하우스 환경에 대해 16의 메모리 대 vCore 비율 및 32의 메모리 대 vCore 비율을 선택하는 것이 좋습니다. 

SQL Server 데이터 웨어하우스 환경은 많은 머신의 병렬 처리를 활용하는 경우가 많습니다. 이러한 이유로 M 시리즈 및 Mv2 시리즈는 대규모 데이터 웨어하우스 환경에서 강력한 옵션입니다.

현재 온-프레미스 SQL Server 데이터베이스를 Azure VM의 SQL Server로 마이그레이션하기 위한 기준으로 원본 머신의 vCPU 및 메모리 구성을 사용합니다. Azure에 대한 핵심 라이선스를 사용하여 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 활용하고 SQL Server 라이선스 비용을 절감할 수 있습니다.

## <a name="memory-optimized"></a>메모리 최적화

[메모리에 최적화된 가상 머신 크기](../../../virtual-machines/sizes-memory.md)는 SQL Server VM의 기본 목표이며 Microsoft에서 권장하는 선택입니다. 메모리가 최적화된 가상 머신은 더 강력한 메모리와 CPU 비율을 제공하고 중대형 규모의 캐시 옵션을 제공합니다. 

### <a name="m-mv2-and-mdsv2-series"></a>M, Mv2 및 Mdsv2 시리즈

[M 시리즈](../../../virtual-machines/m-series.md)는 가장 큰 SQL Server 워크로드 중 일부에 대한 vCore 개수와 메모리를 제공합니다.  

[Mv2 시리즈](../../../virtual-machines/mv2-series.md)는 최고 vCore 수 및 메모리를 포함하고 있으며 중요 업무 및 데이터 웨어하우스 워크로드에 권장됩니다. Mv2 시리즈 인스턴스는 메모리에 최적화된 VM 크기로, 탁월한 컴퓨팅 성능을 바탕으로 대규모 메모리 내 데이터베이스 및 워크로드를 지원하며, 메모리 대 CPU 비율이 높아 관계형 데이터베이스 서버, 대규모 캐시, 메모리 내 분석에 적합합니다.

예를 들어, [Standard_M64ms](../../../virtual-machines/m-series.md)는 28의 메모리 대 vCore 비율을 갖습니다.

[Mdsv2 중간 메모리 시리즈](../../..//virtual-machines/msv2-mdsv2-series.md)는 현재 [미리 보기](https://aka.ms/Mv2MedMemoryPreview)로 제공되는 새로운 M 시리즈로, 중간 계층 메모리 제품을 포함하는 여러 개의 M 시리즈 수준 Azure Virtual Machines를 제공합니다. 해당 머신은 최소 10개에서 30개까지 메모리 대 vCore 지원을 제공하는 SQL Server 워크로드에 적합합니다.

SQL Server 성능에 적합한 M 및 Mv2 시리즈의 기능 중 일부에는 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md) 및 [프리미엄 스토리지 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching) 지원, [울트라 디스크](../../../virtual-machines/disks-enable-ultra-ssd.md) 지원 및 [쓰기 가속화](../../../virtual-machines/how-to-enable-write-accelerator.md)가 포함됩니다.

### <a name="edsv4-series"></a>Edsv4 시리즈

[Edsv4 시리즈](../../../virtual-machines/edv4-edsv4-series.md)는 메모리 집약적 애플리케이션을 위해 설계되었습니다. 해당 VM에는 큰 로컬 스토리지 SSD 용량, 강력한 로컬 디스크 IOPS, 최대 504GiB RAM이 있습니다. 가상 머신 대부분에서 vCore당 8GiB의 메모리를 거의 일관되게 사용할 수 있으며, 이는 표준 SQL Server 워크로드에 이상적입니다. 

이 그룹에는 80 vCore, 504GB 메모리, 메모리 대 vCore 비율이 6인 [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md)가 있는 새 가상 머신이 포함되어 있습니다. 이 가상 머신은 [격리](../../../virtual-machines/isolation.md) 되어 있으므로 호스트에서 실행 되는 유일한 가상 머신으로 보장 되므로 다른 고객 워크 로드와 격리 되기 때문에 중요합니다. 이는 SQL Server에 권장되는 것 보다 낮은 메모리 대 vCore 비율을 사용하므로, 격리가 필요한 경우에만 사용해야 합니다.

Edsv4 시리즈 가상 머신은 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md) 및 [프리미엄 스토리지 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 지원합니다.

### <a name="dsv2-series-11-15"></a>DSv2 시리즈 11-15

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)에는 이전 D 시리즈와 동일한 메모리 및 디스크 구성이 있습니다. 이 시리즈는 모든 가상 머신에서 7의 메모리 대 CPU 비율을 일관성 있게 유지합니다. 이는 메모리 최적화 시리즈의 가장 작은 값으로, 입문용 SQL Server 워크로드에 좋은 저렴한 옵션입니다.

[DSv2 시리즈 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)는 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md) 및 [프리미엄 스토리지 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 지원하며 이는 최적의 성능을 위해 적극 권장됩니다.

## <a name="general-purpose"></a>범용

[범용 가상 머신 크기](../../../virtual-machines/sizes-general.md)는 개발 및 테스트, 웹 서버 및 더 작은 데이터베이스 서버와 같은 더 작은 입문 수준 워크로드에 대해 분산된 메모리 대 vCore 비율을 제공하도록 설계되었습니다. 

범용 가상 머신을 사용하는 메모리 대 vCore 비율이 낮을수록 메모리 기반 성능 카운터를 신중하게 모니터링하여 SQL Server에서 필요한 버퍼 캐시 메모리를 사용할 수 있는지 확인하는 것이 중요합니다. 자세한 내용은 [메모리 성능 기준](performance-guidelines-best-practices-collect-baseline.md#memory)을 참조하세요. 

프로덕션 워크로드에 대한 시작 권장 사항은 메모리 대 vCore 비율 8이기 때문에 SQL Server를 실행하는 범용 VM에 대한 최소 권장 구성은 4개의 Vcore 및 32GB의 메모리입니다. 

### <a name="ddsv4-series"></a>Ddsv4 시리즈

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md)는 vCPU, 메모리 및 임시 디스크의 고른 조합을 제공하지만 더 적은 메모리 대 vCPU를 지원합니다. 

Ddsv4 VM에는 짧은 대기 시간과 고속 로컬 스토리지가 포함됩니다.

이러한 머신은 임시 스토리지와 부서별 관계형 데이터베이스에 빠르게 액세스해야 하는 병렬 SQL과 앱 배포에 적합합니다. 이 시리즈의 모든 가상 머신에서 표준 메모리 대 vCore 비율 4가 있습니다. 

이러한 이유로 이 시리즈에서는 8 개의 vCores 및 32GB의 메모리를 포함하는 스타터 가상 머신으로 D8ds_v4를 활용하는 것이 좋습니다. 가장 큰 컴퓨터는 64개 vCore 및 256GB의 메모리를 포함하는 D64ds_v4입니다.

[Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md) 가상 머신은 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md) 및 [프리미엄 스토리지 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 지원합니다.

> [!NOTE]
> [Ddsv4 시리즈](../../../virtual-machines/ddv4-ddsv4-series.md)는 SQL Server 워크로드에 권장되는 8의 메모리 대 vCore 비율을 포함하지 않습니다. 따라서 이러한 가상 머신을 더 적은 애플리케이션과 개발 워크로드에만 사용합니다.

### <a name="b-series"></a>B 시리즈

[버스트 가능 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기는 개념 증명 및 매우 작은 애플리케이션과 개발 서버와 같이 일관된 성능을 요구하지 않는 워크로드에 이상적입니다. 

[버스트 가능 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md) 가상 머신 크기의 대부분은 메모리 대 vCore 비율이 4입니다. 이러한 머신 중 가장 큰 값은 20개 vCore 및 80GB의 메모리가 있는 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md)입니다.

이 시리즈는 머신 크기에 따라 변화하는 버스트 가능 크레딧을 사용하여 업무 시간 동안 **버스트** 하는 기능을 제공하는 기능이 있는 고유한 앱입니다. 

크레딧을 모두 사용한 경우, VM은 기준 머신 성능으로 돌아옵니다.

B 시리즈의 장점은 다른 시리즈의 다른 VM 크기와 비교했을 때 계산을 절약할 수 있는 것이며 특히 하루 종일 처리 능력을 많이 필요로 하는 경우에 해당합니다.

이 시리즈는 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md)를 지원하지만 [프리미엄 스토리지 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)은 **지원하지 않습니다**.

> [!NOTE] 
> [버스트 가능 B 시리즈](../../../virtual-machines/sizes-b-series-burstable.md)는 SQL Server 워크로드에 권장되는 8의 메모리 대 vCore 비율을 포함하지 않습니다. 따라서 이러한 가상 머신을 더 적은 애플리케이션과 웹 서버, 개발 워크로드에만 사용해야 합니다.

### <a name="av2-series"></a>Av2 시리즈

[Av2 시리즈](../../../virtual-machines/av2-series.md) VM은 개발 및 테스트, 낮은 트래픽 웹 서버, 중소 규모의 앱 데이터베이스, 개념 증명 등의 입문 수준 워크로드에 가장 적합합니다.

[Standard_A2m_v2](../../../virtual-machines/av2-series.md)(2개 vCore 및 16GB 메모리), [Standard_A4m_v2](../../../virtual-machines/av2-series.md)(4개 vCore 및 32GB 메모리) 및 [Standard_A8m_v2](../../../virtual-machines/av2-series.md)(8개 vCore 및 64GB 메모리)는 이러한 상위 3개 가상 머신에 대한 메모리 대 vCore 비율 8의 양호한 비율이 있습니다. 

이러한 가상 머신은 소규모 개발 및 테스트 SQL Server 머신에서 모두 좋은 옵션입니다. 

8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md)는 소형 애플리케이션과 웹 서버에 좋은 옵션일 수도 있습니다.

> [!NOTE] 
> Av2 시리즈는 프리미엄 스토리지를 지원하지 않으므로, 메모리 대 vCore 비율이 8인 가상 머신을 사용하더라도 프로덕션 SQL Server 워크로드에는 권장되지 않습니다.

## <a name="storage-optimized"></a>스토리지 최적화

[VM 크기에 최적화된 스토리지](../../../virtual-machines/sizes-storage.md)는 특정 사용 사례를 위한 것입니다. 이러한 가상 머신은 최적화된 디스크 처리량 및 IO로 특별히 설계되었습니다. 

### <a name="lsv2-series"></a>Lsv2 시리즈

[Lsv2 시리즈](../../../virtual-machines/lsv2-series.md)는 높은 처리량, 짧은 대기 시간 및 로컬 NVMe 스토리지 기능을 제공합니다. Lsv2 시리즈 VM은 지속형 데이터 디스크를 사용하는 대신 VM에 직접 연결된 노드의 로컬 디스크를 사용하도록 최적화되었습니다. 

이러한 가상 머신은 빅 데이터, 데이터 웨어하우스, 보고 및 ETL 워크로드에 대한 강력한 옵션입니다. 로컬 NVMe 스토리지의 높은 처리량 및 IOPS는 데이터베이스에 로드되는 파일을 처리하는 데 적합한 사용 사례이며, 데이터를 원본 시스템이나 Azure Blob 스토리지 또는 Azure Data Lake 같은 다른 리포지토리에서 다시 만들 수 있는 기타 시나리오입니다. [Lsv2 시리즈](../../../virtual-machines/lsv2-series.md) VM은 한 번에 최대 30분 동안 디스크 성능을 버스트할 수도 있습니다.

이러한 가상 머신의 크기는 vCPU 당 8GiB 메모리를 포함하는 8~80개 vCPU, 8개 vCpu 마다 1.92TB의 NVMe SSD를 포함합니다. 즉, 이 시리즈의 가장 큰 VM, [L80s_v2](../../../virtual-machines/lsv2-series.md)은 10x1.92TB의 NVMe 스토리지를 사용하는 80 vCPU 및 640BiB의 메모리가 있습니다.  모든 가상 머신에서 8의 메모리 대 vCore 비율이 일관성 있게 유지됩니다.

NVMe 스토리지는 가상 머신의 할당을 취소하거나 서비스 복구를 위해 다른 호스트로 이동하는 경우 이러한 디스크에서 데이터가 손실될 수 있다는 것을 의미합니다.

Lsv2 및 Ls 시리즈는 [프리미엄 스토리지](../../../virtual-machines/premium-storage-performance.md)를 지원하지만 프리미엄 스토리지 캐싱은 지원하지 않습니다. IOPs를 늘리기 위해 로컬 캐시를 만드는 것은 지원되지 않습니다. 

> [!WARNING]
> 임시 NVMe 스토리지에 데이터 파일을 저장하면 VM 할당이 취소될 때 데이터 손실이 발생할 수 있습니다. 

## <a name="constrained-vcores"></a>제한된 vCore

고성능 SQL Server 워크로드에는 vCore 수가 많지 않아도 더 높은 용량의 메모리, I/O가 필요한 경우가 많습니다. 

대부분의 OLTP 워크로드는 다수의 작은 트랜잭션으로 구동되는 애플리케이션 데이터베이스입니다. OLTP 워크로드를 사용하는 경우 소량의 데이터를 읽거나 수정하지만 사용자 수를 기반으로 하는 트랜잭션 볼륨이 훨씬 더 높습니다. 캐시 계획에 사용할 수 있는 SQL Server 메모리가 있어야 하고, 성능을 위해 최근에 액세스한 데이터를 저장하고, 물리적 읽기를 메모리로 빠르게 읽어올 수 있는지 확인하는 것이 중요합니다. 

해당 OLTP 환경에는 더 많은 양의 메모리, 빠른 스토리지 및 최적으로 수행하는 데 필요한 I/O 대역폭이 필요합니다. 

더 높은 SQL Server 라이선스 비용 없이 이 수준의 성능을 유지하기 위해 Azure는 [제한된 vCPU](../../../virtual-machines/constrained-vcpu.md) 수를 포함하는 VM 크기를 제공합니다. 

그러면 상위 가상 머신의 동일한 메모리, 스토리지 및 I/O 대역폭을 유지하면서 사용 가능한 vCore를 줄임으로써 라이선스 비용을 제어할 수 있습니다.

vCPU 수를 원래 VM 크기의 절반이나 1/4로 제한할 수 있습니다. 가상 머신에서 사용할 수 있는 vCore를 줄이면 메모리 대 vCore 비율을 높일 수 있지만 계산 비용은 동일하게 유지됩니다.

새 VM 크기에는 더 쉽게 구분할 수 있도록 활성 vCPU 수를 지정하는 접미사가 붙습니다. 

예를 들어, [M64-32ms](../../../virtual-machines/constrained-vcpu.md)는 [M64ms](../../../virtual-machines/m-series.md)의 메모리, I/O 및 처리량을 포함하는 32개 SQL Server vCore 라이선스만 필요하고, [M64-16ms](../../../virtual-machines/constrained-vcpu.md)에는 vCore 16개만 사용하면 됩니다.  [M64-16ms](../../../virtual-machines/constrained-vcpu.md)는 M64ms의 SQL Server 라이선스 비용의 4분의 1이지만 가상 머신의 계산 비용은 동일합니다.

> [!NOTE] 
> - 중대형 데이터 웨어하우스 워크로드는 [제한된 vCore VM](../../../virtual-machines/constrained-vcpu.md)을 활용할 수 있지만, 데이터 웨어하우스 워크로드는 더 적은 사용자를 사용하고 병렬 실행되는 쿼리 계획을 통해 더 많은 양의 데이터를 처리하는 프로세스를 사용하는 특징이 있습니다. 
> - 운영 체제 라이선싱을 포함하는 계산 비용은 상위 가상 머신과 동일하게 유지됩니다. 



## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 시리즈의 다른 문서를 참조하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [스토리지](performance-guidelines-best-practices-storage.md)
- [보안](security-considerations-best-practices.md)
- [HADR 설정](hadr-cluster-best-practices.md)
- [기준 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](security-considerations-best-practices.md)을 참조하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
