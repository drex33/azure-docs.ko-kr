---
title: Azure Cache for Redis의 고가용성
description: Azure Cache for Redis의 고가용성 기능 및 옵션에 대해 알아보기
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 8796dd088df0ff297cd7ac99271bfe410fdb506b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620847"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Azure Cache for Redis의 고가용성

Azure Cache for Redis에는 기본 제공된 고가용성 기능이 있습니다.  고가용성 아키텍처는 중단이 계획된 중단과 계획되지 않은 중단 모두에 기본 VM(가상 머신)에 영향을 주는 경우에도 관리되는 Redis 인스턴스가 작동하는지 확인하는 데 사용됩니다. 단일 VM에서 Redis를 호스트하여 가능한 것보다 훨씬 더 높은 비율을 제공합니다.

Azure Cache for Redis는 캐시에 대해 *노드* 라고 하는 여러 VM을 사용하여 고가용성을 구현합니다. 노드는 데이터 복제 및 장애 조치(failover)가 안정적으로 발생하도록 구성됩니다. 고가용성은 Redis 소프트웨어 패치와 같은 유지 관리 작업도 지원합니다. 표준, 프리미엄 및 엔터프라이즈 계층에서는 다양한 고가용성 옵션을 사용할 수 있습니다.

| 옵션 | Description | 가용성 | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [표준 복제](#standard-replication)| 자동 장애 조치(failover)를 사용하는 단일 데이터 센터의 이중 노드 복제 구성 | 99.9%([세부 정보](https://azure.microsoft.com/support/legal/sla/cache/v1_1/) 참조) |✔|✔|-|
| [영역 중복](#zone-redundancy) | 자동 장애 조치(failover)를 사용하여 AZ간 다중 노드 복제 구성 | 최대 99.99%([세부 정보](https://azure.microsoft.com/support/legal/sla/cache/v1_1/) 참조) |-|✔|✔|
| [지역에서 복제](#geo-replication) | 두 지역에 연결된 캐시 인스턴스(사용자 제어 장애 조치(failover) 포함) | 최대 99.999%([세부 정보](https://azure.microsoft.com/support/legal/sla/cache/v1_1/) 참조) |-|✔|미리 보기|

## <a name="standard-replication"></a>표준 복제

표준 또는 프리미엄 계층의 Azure Cache for Redis는 기본적으로 Redis 서버 쌍에서 실행됩니다. 두 서버는 전용 VM에서 호스트됩니다. 오픈 소스 Redis를 사용하면 하나의 서버에서만 데이터 쓰기 요청을 처리할 수 있습니다. 이 서버는 *주* 노드이며 다른 서버는 *복제본* 입니다. 서버 노드를 프로비저닝한 후 Azure Cache for Redis는 주 역할과 복제본 역할을 할당합니다. 일반적으로 주 노드는 Redis 클라이언트의 쓰기 및 읽기 요청 서비스를 담당합니다. 쓰기 작업의 경우 새 키와 키 업데이트가 내부 메모리에 커밋되고 즉시 클라이언트에 응답합니다. 작업을 복제본에 비동기적으로 전달합니다.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="데이터 복제 설정":::

>[!NOTE]
>일반적으로 Redis 클라이언트는 모든 읽기 및 쓰기 요청에 대해 Redis 캐시의 주 노드와 통신합니다. 특정 Redis 클라이언트는 복제 노드에서 읽도록 구성할 수 있습니다.
>
>

Redis 캐시의 주 노드를 사용할 수 없는 경우 복제본이 자동으로 새로운 주 노드가 되도록 스스로 수준을 올립니다. 이 프로세스를 *장애 조치(failover)* 라고 합니다. 주 노드가 신속하게 복구되는 경우 전환되기 전에 복제본은 충분히 오랜 시간 동안 대기합니다. 장애 조치(failover)가 발생하면 Azure Cache for Redis는 새 VM을 프로비저닝하고 이를 복제 노드로 캐시에 조인합니다. 복제본은 캐시 데이터의 다른 복사본이 포함되도록 주 노드와 전체 데이터 동기화를 수행합니다.

주 노드는 Redis 소프트웨어 또는 운영 체제 업데이트와 같은 계획된 유지 관리 작업의 영향으로 작동하지 않을 수 있습니다. 기본 하드웨어, 소프트웨어 또는 네트워크의 오류와 같은 계획되지 않은 이벤트로 인해 작동이 중지될 수도 있습니다. [Azure Cache for Redis의 장애 조치(failover) 및 패치](cache-failover.md)는 Redis 장애 조치(failover) 유형에 대한 자세한 설명을 제공합니다. Azure Cache for Redis는 해당 수명 동안 많은 장애 조치(failover)를 수행합니다. 고가용성 아키텍처의 디자인은 캐시 내에서 가능한 한 클라이언트에 투명 하 게 이러한 변경을 수행 합니다.

또한 Azure Cache for Redis는 프리미엄 계층에서 추가 복제본 노드를 제공합니다. [다중 복제본 캐시](cache-how-to-multi-replicas.md)는 최대 3개의 복제본 노드를 사용하여 구성할 수 있습니다. 복제본이 많을수록 주 노드를 백업하는 노드가 있으므로 일반적으로 복원력이 향상됩니다. 복제본이 많은 경우에도 Azure Cache for Redis 인스턴스는 데이터 센터 또는 AZ 수준 중단으로 심각한 영향을 받을 수 있습니다. [영역 중복성](#zone-redundancy)이 있는 여러 복제본을 사용하여 캐시 가용성을 높일 수 있습니다.

## <a name="zone-redundancy"></a>영역 중복

Azure Cache for Redis는 프리미엄 및 엔터프라이즈 계층에서 영역 중복 구성을 지원합니다. [영역 중복 캐시](cache-how-to-zone-redundancy.md)는 동일한 지역에 있는 다양한 [Azure 가용성 영역](../availability-zones/az-overview.md)에 노드를 둘 수 있습니다. 데이터 센터 또는 AZ 중단을 단일 실패 지점으로서 제거하고 캐시의 전체 가용성을 높입니다.

### <a name="premium-tier"></a>프리미엄 계층

다음 다이어그램은 프리미엄 계층에 대한 영역 중복 구성을 보여 줍니다.

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="영역 중복성 설정":::

Azure Cache for Redis는 선택한 AZ에 대해 라운드 로빈 방식으로 영역 중복 캐시에서 노드를 배포합니다. 또한 초기에 주로 사용할 노드를 결정합니다.

영역 중복 캐시는 자동 장애 조치(failover)를 제공합니다. 현재 주 노드를 사용할 수 없는 경우 복제본 중 하나가 사용됩니다. 새로운 주 노드가 다른 AZ에 있는 경우 애플리케이션의 캐시 응답 시간이 더 길어질 수 있습니다. AZ는 지리적으로 구분됩니다. 한 AZ에서 다른 AZ로 전환하면 애플리케이션과 캐시가 호스트되는 위치 간의 물리적인 거리가 변경됩니다. 이 변경은 애플리케이션에서 캐시로의 왕복 네트워크 대기 시간에 영향을 줍니다. 추가 대기 시간은 대부분의 애플리케이션에 허용되는 범위에 포함될 것으로 예상됩니다. 애플리케이션을 테스트하여 영역 중복 캐시에서 제대로 작동하는지 확인하는 것이 좋습니다.

### <a name="enterprise-tiers"></a>Enterprise 계층

두 엔터프라이즈 계층의 캐시는 Redis 엔터프라이즈 클러스터에서 실행됩니다. 쿼럼을 구성하려면 항상 홀수 개의 서버 노드가 필요합니다. 기본적으로 각각 전용 VM에서 호스트되는 세 개의 노드가 있습니다. 엔터프라이즈 캐시에는 크기가 같은 *데이터 노드* 두 개와 그보다 작은 *쿼럼 노드* 하나가 있습니다. Enterprise Flash 캐시에는 크기가 같은 세 개의 데이터 노드가 있습니다. 엔터프라이즈 클러스터는 Redis 데이터를 내부적으로 파티션으로 나눕니다. 각 파티션에는 *주* 노드와 하나 이상의 *복제본* 이 있습니다. 각 데이터 노드는 하나 이상의 파티션을 보유합니다. 엔터프라이즈 클러스터는 모든 파티션에 대한 주 노드 및 복제본이 동일한 데이터 노드에서 공동 배치되지 않도록 합니다. 파티션은 주 노드에서 해당 복제본으로 데이터를 비동기적으로 복제합니다.

데이터 노드를 사용할 수 없게 되거나 네트워크 분할이 발생하면 [표준 복제](#standard-replication)에 설명된 것과 유사한 장애 조치(failover)가 발생합니다. 엔터프라이즈 클러스터는 쿼럼 기반 모델을 사용하여 새 쿼럼에 참여하게 될 남아 있는 노드를 결정합니다. 또한 필요에 따라 이러한 노드 내의 복제본 파티션을 주 노드로 승격합니다.

## <a name="geo-replication"></a>지역에서 복제

[지역에서 복제](cache-how-to-geo-replication.md)는 일반적으로 두 개의 Azure 지역에 걸쳐 둘 이상의 Azure Cache for Redis 인스턴스를 연결하는 메커니즘입니다.

### <a name="premium-tier"></a>프리미엄 계층

>[!NOTE]
>프리미엄 계층의 지역에서 복제는 주로 재해 복구를 위해 설계되었습니다.
>
>

다른 지역에 캐시 데이터를 백업할 수 있도록 [지역 복제](cache-how-to-geo-replication.md)를 통해 두 개의 프리미엄 계층 캐시 인스턴스를 연결할 수 있습니다. 함께 연결되면 하나의 인스턴스는 기본 연결 캐시로 명명되며 다른 인스턴스는 보조 연결 캐시로 명명됩니다. 주 캐시만 읽기 및 쓰기 요청을 허용합니다. 기본 캐시에 작성된 데이터는 보조 캐시에 복제됩니다.

애플리케이션은 주 캐시 및 보조 캐시에 대한 별도의 엔드포인트를 통해 캐시에 액세스합니다. 애플리케이션은 여러 Azure 지역에 배포될 때 주 캐시로 모든 쓰기 요청을 보내야 합니다. 주 캐시 또는 보조 캐시에서 읽을 수 있습니다. 일반적으로 대기 시간을 줄이기 위해 애플리케이션의 계산 인스턴스가 가장 가까운 캐시를 읽도록 합니다. 두 캐시 인스턴스 간의 데이터 전송은 TLS로 보호됩니다.

지역 복제는 애플리케이션의 나머지 부분이 주 지역에 유지되는 경우 지역 간에 추가된 네트워크 왕복 시간에 대한 문제로 인해 자동 장애 조치(failover)를 제공하지 않습니다. 보조 캐시의 연결을 해제하여 장애 조치(failover)를 관리하고 시작해야 합니다. 연결을 해제하면 새로운 주 인스턴스로 승격됩니다.

### <a name="enterprise-tiers"></a>Enterprise 계층

>[!NOTE]
>이는 미리 보기로 제공됩니다.
>
>

엔터프라이즈 계층은 보다 고급 형태의 지역 복제를 지원합니다. 이를 [활성 지역 복제](cache-how-to-active-geo-replication.md)라고 합니다. Redis 엔터프라이즈 소프트웨어는 충돌 없이 복제된 데이터 형식을 사용하여 여러 캐시 인스턴스에 대한 쓰기를 지원하고 변경 내용 병합 및 충돌 해결을 처리합니다. 서로 다른 Azure 지역에 있는 둘 이상의 엔터프라이즈 계층 캐시 인스턴스를 조인하여 활성 지역 복제 캐시를 형성할 수 있습니다. 

이러한 캐시를 사용하는 애플리케이션은 해당 엔드포인트를 통해 지역에 분산된 캐시 인스턴스를 읽고 쓸 수 있습니다. 캐시는 각 컴퓨팅 인스턴스에 가장 가까운 것을 사용하여 대기 시간을 최소화해야 합니다. 또한 애플리케이션은 캐시 인스턴스를 모니터링하고 인스턴스 중 하나를 사용할 수 없게 되는 경우 다른 지역으로 전환해야 합니다. 활성 지역 복제의 작동 방식에 대한 자세한 내용은 [활성-활성 지역 분산(CRDT 기반)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

고가용성 옵션을 위해 Azure Cache for Redis를 구성하는 방법에 대해 자세히 알아보세요.

* [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)
* [Azure Cache for Redis에 복제본 추가](cache-how-to-multi-replicas.md)
* [Azure Cache for Redis에 대한 영역 중복 사용](cache-how-to-zone-redundancy.md)
* [Azure Cache for Redis에 대한 지역 복제 설정](cache-how-to-geo-replication.md)
