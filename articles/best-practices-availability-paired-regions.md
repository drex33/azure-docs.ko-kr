---
title: Azure 쌍을 이루는 지역을 사용하여 비즈니스 연속성 및 재해 복구 보장
description: Azure 쌍을 이루는 지역을 사용하여 애플리케이션 복원력 보장
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 030197a39ef10193887e3fc9966acd1979137291
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233127"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역

## <a name="what-are-paired-regions"></a>쌍을 이루는 지역이란?

Azure 지역은 대기 시간이 정의된 경계 내에 배포되고 대기 시간이 짧은 전용 네트워크를 통해 연결되는 여러 데이터 센터로 구성됩니다.  이를 통해 Azure 지역 내의 Azure 서비스가 최상의 성능과 보안을 제공할 수 있습니다.  

Azure 지리는 하나 이상의 Azure 지역을 포함하는 전 세계 영역을 정의합니다. 지리는 일반적으로 두 개 이상의 지역을 포함하는 별개의 시장을 정의하며 데이터 보존 및 규정 준수 경계를 유지합니다.  Azure 글로벌 인프라에 대한 자세한 내용은 [여기](https://azure.microsoft.com/global-infrastructure/regions/)를 참조하세요.

지역 쌍은 동일한 지리 내의 두 지역으로 구성됩니다. Azure는 지역 쌍에서 플랫폼 업데이트(계획된 유지 관리)를 직렬화하여 한 번에 각 쌍의 한 지역만 업데이트되도록 합니다. 중단이 여러 영역에 영향을 미치는 경우 각 쌍에서 하나 이상의 지역에 복구 우선 순위가 지정됩니다.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

일부 Azure 서비스는 비즈니스 연속성을 보장하고 데이터 손실로부터 보호하기 위해 쌍을 이루는 지역의 이점을 추가로 활용합니다.  Azure는 쌍을 이루는 지역을 활용하여 데이터 가용성을 보장하는 여러 [스토리지 솔루션](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)을 제공합니다. 예를 들어 [Azure GRS(지역 중복 스토리지)](./storage/common/storage-redundancy.md#geo-redundant-storage)는 데이터를 보조 지역에 자동으로 복제하여 주 지역을 복구할 수 없는 경우에도 데이터가 오래 유지되도록 보장합니다. 

모든 Azure 서비스가 자동으로 데이터를 복제하는 것은 아니며, 모든 Azure 서비스가 실패한 지역에서 해당 서비스 쌍으로 자동 대체되는 것도 아닙니다.  이러한 경우 복구 및 복제는 고객이 구성해야 합니다.

## <a name="can-i-select-my-regional-pairs"></a>지역 쌍을 선택할 수 있나요?

아니요. 일부 Azure 서비스는 Azure [중복 스토리지](./storage/common/storage-redundancy.md)와 같은 지역 쌍을 사용합니다. 이러한 서비스에서는 새 지역 쌍을 만들 수 없습니다.  마찬가지로 Azure는 지역 쌍에 대해 계획된 유지 관리 및 복구 우선 순위 지정을 제어하기 때문에 이러한 서비스를 활용하기 위해 사용자 자신의 지역 쌍을 정의할 수 없습니다. 그러나 여러 지역에 서비스를 빌드하고 Azure 서비스를 활용하여 이를 페어링함으로써 사용자 고유의 재해 복구 솔루션을 만들 수 있습니다. 

예를 들어 [AzCopy](./storage/common/storage-use-azcopy-v10.md)와 같은 Azure 서비스를 사용하여 다른 지역의 스토리지 계정에 대한 데이터 백업을 예약할 수 있습니다.  [Azure DNS 및 Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)를 사용하여 고객은 주 지역의 손실을 해결할 수 있는 애플리케이션에 대해 복원력 있는 아키텍처를 디자인할 수 있습니다.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>내 지역 쌍 내에서 서비스를 사용하는 데 제한이 있나요?

아니요. 특정 Azure 서비스가 지역 쌍을 사용하면 비즈니스 요구를 충족하는 모든 지역에서 다른 서비스를 호스트할 수 있습니다.  Azure GRS 스토리지 솔루션은 미국 동부에 위치한 컴퓨팅 리소스를 사용하는 동안 캐나다 중부의 데이터를 캐나다 동부의 피어와 쌍으로 연결할 수 있습니다.  

## <a name="must-i-use-azure-regional-pairs"></a>Azure 지역 쌍을 사용해야 하나요?

아니요. 고객은 Azure 서비스를 활용하여 Azure의 지역 쌍을 사용하지 않고도 복원력 있는 서비스를 설계할 수 있습니다.  그러나 지역 쌍 간에 BCDR(비즈니스 연속성 재해 복구)을 구성하여 [격리](./security/fundamentals/isolation-choices.md)의 이점을 활용하고 [가용성](./availability-zones/az-overview.md)을 향상하는 것이 좋습니다. 여러 활성 지역을 지원하는 애플리케이션의 경우 가능하면 한 지역 쌍의 두 지역을 모두 사용하는 것이 좋습니다. 이를 통해 애플리케이션에 대한 최적 가용성을 보장하고 재해 발생 시 복구 시간을 최소화할 수 있습니다. 가능하면 언제든지 애플리케이션을 설계하여 [최대 복원력](/azure/architecture/framework/resiliency/app-design)과 [재해 복구](/azure/architecture/framework/resiliency/backup-and-recovery)의 편이성을 경험하세요.

## <a name="azure-regional-pairs"></a>Azure 지역 쌍

| Geography | 지역 쌍 A | 지역 쌍 B  |
|:--- |:--- |:--- |
| 아시아 태평양 |동아시아(홍콩) | 동남 아시아(싱가포르) |
| 오스트레일리아 |오스트레일리아 동부 |오스트레일리아 남동부 |
| 오스트레일리아 |오스트레일리아 중부 |오스트레일리아 중부 2* |
| 브라질 |브라질 남부 |미국 중남부 |
| 브라질 |브라질 남동부* |브라질 남부 |
| Canada |캐나다 중부 |캐나다 동부 |
| 중국 |중국 북부 |중국 동부|
| 중국 |중국 북부 2 |중국 동부 2|
| 유럽 |북유럽(아일랜드) |서유럽(네덜란드) |
| 프랑스 |프랑스 중부|프랑스 남부*|
| 독일 |독일 중서부 |독일 북부* |
| 인도 |인도 중부 |인도 남부 |
| 인도 |인도 서부 |인도 남부 |
| 일본 |일본 동부 |일본 서부 |
| 한국 |한국 중부 |한국 남부 |
| 북아메리카 |미국 동부 |미국 서부 |
| 북아메리카 |미국 동부 2 |미국 중부 |
| 북아메리카 |미국 중북부 |미국 중남부 |
| 북아메리카 |미국 서부 2 |미국 중서부 |
| 북아메리카 |미국 서부 3 |미국 동부 |
| 노르웨이 | 노르웨이 동부 | 노르웨이 서부* |
| 남아프리카 | 남아프리카 북부 |남아프리카 공화국 서부* |
| 스위스 | 스위스 북부 |스위스 서부* |
| 영국 |영국 서부 |영국 남부 |
| 아랍에미리트연합국 | 아랍에미리트 북부 | 아랍에미리트 중부* |
| 미국 국방부 |US DoD 동부* |US DoD 중부* |
| 미국 정부 |US Gov 애리조나* |US Gov 텍사스* |
| 미국 정부 |US Gov 아이오와* |US Gov 버지니아* |
| 미국 정부 |US Gov 버지니아* |US Gov 텍사스* |

(*) 특정 지역은 특정 고객 시나리오(예: 국내 재해 복구)를 지원하기 위해 액세스가 제한됩니다. 이러한 지역은 [Azure Portal에서 새 지원 요청 만들기](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)를 요청하는 경우에만 사용할 수 있습니다.

> [!Important]
> - 인도 서부는 한 방향으로만 쌍을 이룹니다. 인도 서부의 보조 지역은 인도 남부이지만 인도 남부의 보조 지역은 인도 중부입니다.
> - 브라질 남부는 해당 지리적 위치 외부에 있는 지역과 쌍을 이루었기 때문에 특별합니다. 브라질 남부의 보조 지역은 미국 중남부입니다. 미국 중남부의 보조 지역은 브라질 남부가 아닙니다.


## <a name="an-example-of-paired-regions"></a>쌍을 이루는 지역의 예
아래 이미지는 재해 복구를 위해 지역 쌍을 사용하는 가상 애플리케이션을 보여 줍니다. 녹색 숫자는 세 개의 Azure 서비스(Azure 컴퓨팅, 스토리지, 데이터베이스)의 지역 간 활동 및 지역 간에 복제하도록 구성된 방식을 강조합니다. 쌍을 이루는 지역에 걸친 배포의 고유한 이점은 주황색 숫자로 강조 표시되어 있습니다.

![쌍을 이루는 지역의 이점 개요](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

그림 2 – 가상의 Azure 지역 쌍

## <a name="cross-region-activities"></a>지역 간 활동
그림 2 참조

1. **Azure Compute(IaaS)** – 재해 중에 다른 지역의 리소스를 사용할 수 있도록 사전에 추가 컴퓨팅 리소스를 프로비저닝해야 합니다. 

2. **Azure Storage** - 관리 디스크를 사용하는 경우 Azure Backup을 통해 [지역 간 백업](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)에 대해 알아보고 Azure Site Recovery를 통해 한 지역에서 다른 지역으로의 [VM 복제](./site-recovery/azure-to-azure-tutorial-enable-replication.md)에 대해 알아봅니다. 스토리지 계정을 사용하면 Azure Storage 계정을 만들 때 기본적으로 GRS(지역 중복 스토리지)가 구성됩니다. GRS를 사용하면 주 지역 및 쌍을 이루는 지역에서 각각 세 번씩 데이터가 자동으로 복제됩니다. 자세한 내용은 [Azure Storage 중복 옵션](storage/common/storage-redundancy.md)을 참조하세요.

3. **Azure SQL Database** – Azure SQL Database 지역 복제 기능을 사용하면 전 세계 모든 지역으로 트랜잭션 비동기 복제를 구성할 수 있습니다. 그러나 대부분의 재해 복구 시나리오에서는 쌍을 이루는 지역에 이러한 리소스를 배포하는 것이 좋습니다. 자세한 내용은 [Azure SQL Database의 지역에서 복제](./azure-sql/database/auto-failover-group-overview.md)를 참조하세요.

4. **Azure Resource Manager** - Resource Manager는 본질적으로 지역 간에 구성 요소를 논리적으로 격리합니다. 따라서 하나의 지역에서 발생한 논리적 오류가 다른 지역에 영향을 줄 가능성이 거의 없습니다.

## <a name="benefits-of-paired-regions"></a>쌍을 이루는 지역의 이점

5. **물리적 격리** – 가능한 경우 Azure는 지역 쌍 내 데이터 센터 간에 약 483Km(300마일) 이상 격리하는 것이 좋습니다. 물론 모든 지리적 위치에서 이러한 격리가 가능한 것은 아닙니다. 물리적 데이터 센터 격리는 자연 재해, 내전, 정전 또는 물리적 네트워크 중단으로 인해 두 지역 모두가 동시에 영향을 받을 수 있는 가능성을 줄여 줍니다. 격리는 지리적 위치 내의 제약 조건(지리적 크기, 전력/네트워크 인프라 가용성, 규정 등)에 따라 달라집니다.  

6. **플랫폼에서 제공하는 복제** - 지역 중복 스토리지와 같은 일부 서비스는 쌍을 이루는 지역에 자동 복제 기능을 제공합니다.

7. **지역 복구 순서** – 광범위한 중단 시 모든 쌍에서 한 지역에 대한 복구 우선 순위가 지정됩니다. 쌍을 이루는 지역에 배포된 애플리케이션은 지역 중 하나에서 우선 복구됩니다. 쌍을 이루지 않는 지역에 애플리케이션이 배포된 경우에는 복구가 지연될 수 있으며, 최악의 경우 선택한 지역이 마지막에 복구되는 두 지역이 될 수 있습니다.

8. **순차적 업데이트** – 계획된 Azure 시스템 업데이트는 가동 중지 시간, 버그의 영향, 흔치 않은 불량 업데이트 시의 논리적 오류를 최소화하기 위해 쌍을 이루는 지역으로 순차적으로(동시가 아님) 롤아웃됩니다.

9. **데이터 보존** – 지역은 세금 및 법률 집행 관할 구역의 데이터 보존 요구 사항을 충족하기 위해 동일한 지리적 위치 내에 쌍으로 상주합니다(브라질 남부 제외).
