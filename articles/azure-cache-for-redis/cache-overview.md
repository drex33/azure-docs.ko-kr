---
title: Azure Cache for Redis란?
description: 캐시 배제, 콘텐츠 캐싱, 사용자 세션 캐싱, 작업 및 메시지 큐, 분산 트랜잭션을 활성화하는 Azure Cache for Redis에 대해 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: fada77ef2409e4e92082ee48e98a459dd26a42f9
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964133"
---
# <a name="about-azure-cache-for-redis"></a>Azure Cache for Redis 소개

Azure Cache for Redis는 [Redis](https://redis.io/) 소프트웨어를 기반으로 하는 메모리 내 데이터 저장소를 제공합니다. Redis는 백 엔드 데이터 저장소에서 많이 사용하는 애플리케이션의 성능과 스케일링 가능성을 개선합니다. 자주 액세스하는 데이터를 빠르게 쓰고 읽을 수 있는 서버 메모리에 보관하여 대량의 애플리케이션 요청을 처리할 수 있습니다. Redis는 최신 애플리케이션에 매우 짧은 대기 시간 및 높은 처리량의 데이터 스토리지 솔루션을 제공합니다.

Azure Cache for Redis는 Redis 오픈 소스(OSS Redis)와 Redis Labs(Redis Enterprise)의 상용 제품을 관리형 서비스로 제공합니다. 안전한 전용 Redis 서버 인스턴스와 전체 Redis API 호환성을 제공합니다. 이 서비스는 Microsoft에서 운영되고, Azure에서 호스트되며, Azure 내부 또는 외부의 모든 애플리케이션에서 사용할 수 있습니다.

Azure Cache for Redis는 분산 데이터 또는 콘텐츠 캐시, 세션 저장소, 메시지 브로커 등으로 사용할 수 있습니다. 독립 실행형으로 배포할 수 있습니다. 또는 Azure SQL 또는 Cosmos DB와 같은 다른 Azure 데이터베이스 서비스와 함께 배포할 수 있습니다.

## <a name="key-scenarios"></a>주요 시나리오

Azure Cache for Redis는 일반적인 애플리케이션 아키텍처 패턴을 지원하여 애플리케이션 성능을 향상시킵니다. 가장 일반적인 패턴의 일부는 다음과 같습니다.

| 패턴      | 설명                                        |
| ------------ | -------------------------------------------------- |
| [데이터 캐시](cache-web-app-cache-aside-leaderboard.md) | 데이터베이스는 너무 커서 캐시로 직접 로드할 수 없습니다. [캐시 배제](/azure/architecture/patterns/cache-aside) 패턴을 사용하여 필요할 때만 캐시에 데이터를 로드하는 것이 일반적입니다. 시스템에서 데이터를 변경하는 경우 시스템은 바로 그때 다른 클라이언트로 배포되는 캐시를 업데이트할 수도 있습니다. 또한 시스템에서 데이터에 대한 만료 날짜를 설정하거나 제거 정책을 사용하여 데이터 업데이트를 캐시로 트리거할 수 있습니다.|
| [콘텐츠 캐시](cache-aspnet-output-cache-provider.md) | 헤더, 바닥글, 배너와 같은 정적 콘텐츠를 사용하는 템플릿에서 많은 웹 페이지가 생성됩니다. 이러한 정적 항목은 자주 변경되지 않습니다. 메모리 내 캐시를 사용하면 백 엔드 데이터 저장소와 비교할 때 정적 콘텐츠에 빠르게 액세스할 수 있습니다. 이 패턴은 처리 시간 및 서버 부하를 줄여 웹 서버 응답성을 높일 수 있습니다. 이를 통해 로드를 처리하는 데 필요한 서버 수를 줄일 수 있습니다. Azure Cache for Redis는 ASP.NET을 사용하여 이 패턴을 지원할 수 있도록 Redis 출력 캐시 공급자를 제공합니다.|
| [세션 저장소](cache-aspnet-session-state-provider.md) | 이 패턴은 일반적으로 웹 애플리케이션에서 사용자 쿠키와 연결할 수 있는 쇼핑 카트 및 다른 사용자 기록 데이터와 함께 사용됩니다. 쿠키에 너무 많이 저장하면 쿠키 크기가 증가하고 모든 요청에서 전달 및 유효성 검사가 수행되므로 성능이 저하될 수 있습니다. 일반적인 해결책은 쿠키를 키로 사용하여 데이터베이스에 데이터를 쿼리하는 것입니다. Azure Cache for Redis와 같은 메모리 내 캐시를 사용하면 전체 관계형 데이터베이스와의 상호 작용보다 훨씬 더 빠르게 사용자와 정보를 연결할 수 있습니다. |
| 작업 및 메시지 큐 | 애플리케이션은 요청과 연결된 작업을 실행하는 데 시간이 걸릴 때 종종 작업을 추가합니다. 오래 실행되는 작업은 종종 다른 서버에 의해 순서대로 처리되도록 큐에 대기됩니다.  이러한 작업 지연 메서드를 태스크 큐라고 합니다. Azure Cache for Redis는 애플리케이션에서 이 패턴을 사용하도록 설정하는 분산 큐를 제공합니다.|
| 분산 트랜잭션 | 애플리케이션에는 단일 원자성 작업으로 실행하기 위해 백 엔드 데이터 저장소에 대한 일련의 명령이 필요할 수 있습니다. 모든 명령은 성공하거나 모두 초기 상태로 롤백되어야 합니다. Azure Cache for Redis는 명령의 일괄 처리 실행을 단일 [트랜잭션](https://redis.io/topics/transactions)으로 지원합니다. |

## <a name="redis-versions"></a>Redis 버전

Azure Cache for Redis는 OSS Redis 버전 4.0.x 및 6.0.x를 지원합니다. 최신 버전을 제공하기 위해 Redis 5.0을 건너뛰기로 결정했습니다. 이전에는 Azure Cache for Redis가 단일 Redis 버전을 유지 관리했습니다. 나중에 최신 주 릴리스 업그레이드와 안정적인 이전 버전이 하나 이상 제공됩니다. 애플리케이션에 가장 적합한 [버전을 선택](cache-how-to-version.md)할 수 있습니다.


## <a name="service-tiers"></a>서비스 계층

Azure Cache for Redis는 다음 계층에서 사용할 수 있습니다.

| 계층 | Description |
|---|---|
| Basic | 단일 VM에서 실행되는 OSS Redis 캐시. 이 계층에는 SLA(서비스 수준 계약)가 없으며 개발/테스트 및 중요하지 않은 작업에 적합합니다. |
| 표준 | 복제된 구성의 두 VM에서 실행되는 OSS Redis 캐시. |
| 프리미엄 | 고성능 OSS Redis 캐시. 이 계층은 더 높은 처리량, 더 짧은 대기 시간, 더 나은 가용성 및 추가 기능을 제공합니다. 프리미엄 캐시는 기본 또는 표준 캐시의 VM에 비해 더 강력한 VM에 배포됩니다. |
| Enterprise | Redis Labs의 Redis Enterprise 소프트웨어로 구동되는 고성능 캐시. 이 계층은 RediSearch, RedisBloom 및 RedisTimeSeries를 포함한 Redis 모듈을 지원합니다. 또한 프리미엄 계층보다 훨씬 더 높은 가용성을 제공합니다. |
| Enterprise Flash | Redis Labs의 Redis Enterprise 소프트웨어로 구동되는 비용 효율적인 대용량 캐시. 이 계층은 Redis 데이터 스토리지를 VM의 DRAM보다 저렴한 비휘발성 메모리로 확장합니다. 전체 GB당 메모리 비용이 줄어듭니다. |

### <a name="feature-comparison"></a>기능 비교

[Azure Cache for Redis 가격 책정](https://azure.microsoft.com/pricing/details/cache/)에서는 각 계층의 자세한 비교를 제공합니다. 다음 표는 계층별로 지원하는 기능 몇 가지를 설명하는 데 도움이 됩니다.

| 기능 설명 | Basic | Standard | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| 데이터 암호화 |✔|✔|✔|✔|✔|
| [네트워크 격리](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [크기 조정](cache-how-to-scale.md) |✔|✔|✔|-|-|
| [OSS 클러스터링](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [데이터 지속성](cache-how-to-premium-persistence.md) |-|-|✔|미리 보기|미리 보기|
| [영역 중복](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [지역에서 복제](cache-how-to-geo-replication.md) |-|-|✔|미리 보기|미리 보기|
| [Redis 모듈](#choosing-the-right-tier) |-|-|-|✔|-|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|-|-|
| [예약된 업데이트](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>올바른 계층 선택

Azure Cache for Redis 계층을 선택하는 경우 다음 옵션을 고려합니다.

* **메모리**: 기본 및 표준 계층은 250MB - 53GB(프리미엄 계층 6GB - 1.2TB, 엔터프라이즈 계층 12GB - 14TB)를 제공합니다.  120GB보다 큰 프리미엄 계층 캐시를 만들려면 Redis OSS 클러스터링을 사용하면 됩니다. 자세한 내용은 [Azure Cache for Redis 가격](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.
* **성능**: 프리미엄 및 엔터프라이즈 계층의 캐시는 프로세서가 더 빠른 하드웨어에 배포되어 기본 또는 표준 계층에 비해 더 나은 성능을 제공합니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다. 자세한 내용은 [Azure Cache for Redis 성능](./cache-planning-faq.yml#azure-cache-for-redis-performance)을 참조하세요.
* **Redis 서버 전용 코어**: C0을 제외한 모든 캐시는 전용 VM 코어를 실행합니다. Redis는 의도적으로 명령 처리에 하나의 스레드만 사용합니다. Azure Cache for Redis는 I/O 처리를 위해 다른 코어를 사용합니다. 코어 수가 많을수록 선형 크기 조정이 생성되지 않더라도 처리량 성능이 향상됩니다. 또한 일반적으로 VM 크기가 클수록 작은 크기보다 대역폭 제한이 높습니다. 이를 통해 애플리케이션에서 시간 초과를 유발하는 네트워크 포화를 방지할 수 있습니다.
* **네트워크 성능**: 높은 처리량이 필요한 워크로드가 있는 경우 프리미엄 또는 엔터프라이즈 계층은 기본 또는 표준에 비해 더 많은 대역폭을 제공합니다. 또한 각 계층 안에서는 캐시를 호스팅하는 기본 VM으로 인해 캐시 크기가 클수록 대역폭이 큽니다. 자세한 내용은 [Azure Cache for Redis 성능](./cache-planning-faq.yml#azure-cache-for-redis-performance)을 참조하세요.
* **최대 클라이언트 연결 수**: 프리미엄 및 엔터프라이즈 계층은 Redis에 연결 가능한 최대 클라이언트 수를 제공하여 더 큰 캐시에 대해 더 많은 수의 연결을 제공합니다. 클러스터링은 클러스터형 캐시에 사용할 수 있는 총 네트워크 대역폭 양을 증가시킵니다.
* **고가용성**: Azure Cache for Redis는 여러 [고가용성](cache-high-availability.md) 옵션을 제공합니다. [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)에 따라 표준, 프리미엄 또는 엔터프라이즈 캐시를 사용할 수 있도록 보장합니다. SLA에서는 캐시 엔드포인트에 대한 연결만 다룹니다. SLA는 데이터 손실에서 보호에는 적용되지 않습니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 및 엔터프라이즈 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다.
* **데이터 지속성**: 프리미엄 및 엔터프라이즈 계층을 사용하면 캐시 데이터를 Azure Storage 계정과 Managed Disk에 각각 유지할 수 있습니다. 기본 인프라 문제로 인해 데이터 손실이 발생할 수 있습니다. 데이터 손실에 대한 복원력을 늘리기 위해 이러한 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Cache for Redis는 RDB 및 AOF(미리 보기) 옵션을 모두 제공합니다. 데이터 지속성은 Azure Portal 및 CLI를 통해 사용하도록 설정할 수 있습니다. 프리미엄 계층의 경우 [프리미엄 Azure Redis Cache에 대한 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.
* **네트워크 격리**: Azure Private Link 및 VNET(Virtual Network) 배포는 Azure Cache for Redis에 대해 향상된 보안 및 트래픽 격리를 제공합니다. VNET을 사용하면 네트워크 액세스 제어 정책을 통해 액세스를 추가로 제한할 수 있습니다. 자세한 내용은 [Azure Private Link가 있는 Azure Cache for Redis](cache-private-link.md) 및 [프리미엄 Azure Cache for Redis에 대한 Virtual Network 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
* **Redis 모듈**: 엔터프라이즈 계층은 [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) 및 [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/)를 지원합니다. 이러한 모듈은 새로운 데이터 형식 및 기능을 Redis에 추가합니다.

캐시를 만든 후 기본 계층에서 프리미엄 계층으로 확장할 수 있습니다. 현재 더 낮은 계층으로 스케일 다운은 지원되지 않습니다. 단계별 크기 조정 지침에 대해서는 [Azure Cache for Redis 크기를 조정하는 방법](cache-how-to-scale.md) 및 [크기 조정 작업을 자동화하는 방법](cache-how-to-scale.md#how-to-automate-a-scaling-operation)을 참조하세요.

### <a name="special-considerations-for-enterprise-tiers"></a>엔터프라이즈 계층에 대한 특별 고려 사항

엔터프라이즈 계층은 Redis Labs의 Redis 상용 변형인 Redis Enterprise를 사용합니다. 고객은 Azure Marketplace 제품을 통해 이 소프트웨어에 대한 라이선스를 취득하고 비용을 지불합니다. Azure Cache for Redis가 라이선스 취득을 관리하므로 사용자가 별도로 관리할 필요가 없습니다. Azure Marketplace에서 구매하려면 다음과 같은 필수 구성 요소가 있어야 합니다.
* Azure 구독에는 유효한 지불 수단이 있습니다. Azure 크레딧 또는 무료 MSDN 구독은 지원되지 않습니다.
* 조직에서 [Azure Marketplace 구매](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)를 허용합니다.
* 프라이빗 Marketplace를 사용하는 경우 Redis Labs Enterprise 제품을 포함해야 합니다.

> [!IMPORTANT]
> Azure Cache for Redis Enterprise에는 캐시 인스턴스와 별도로 청구되는 표준 네트워크 부하 분산 장치가 필요합니다. 자세한 내용은 [Load Balancer 가격 책정](https://azure.microsoft.com/pricing/details/load-balancer/)을 참조하세요.
> Enterprise 캐시가 여러 가용성 영역에 대해 구성된 경우 데이터 전송은 2022년 7월 1일부터 [표준 네트워크 대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.
>
> 또한 데이터 지속성은 Managed Disks를 추가합니다. Enterprise 데이터 지속성의 공개 미리 보기 중에는 이러한 리소스의 사용이 무료입니다. 이는 기능이 일반 공급되면 변경될 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계

* [오픈 소스 Redis 캐시 만들기](quickstart-create-redis.md)
* [Redis Enterprise 캐시 만들기](quickstart-create-redis-enterprise.md)
* [ASP.NET 웹앱에서 Azure Cache for Redis 사용](cache-web-app-howto.md)
* [.NET Core에서 Azure Cache for Redis 사용](cache-dotnet-core-quickstart.md)
* [.NET Framework에서 Azure Cache for Redis 사용](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Node.js에서 Azure Cache for Redis 사용](cache-nodejs-get-started.md)
* [Java에서 Azure Cache for Redis 사용](cache-java-get-started.md)
* [Python에서 Azure Cache for Redis 사용](cache-python-get-started.md)
