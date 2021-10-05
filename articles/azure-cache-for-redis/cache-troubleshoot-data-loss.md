---
title: Azure Cache for Redis 데이터 손실 문제 해결
description: 'Azure Cache for Redis에서 데이터 손실 문제(예: 키 부분 손실, 키 만료 또는 키 전부 손실)를 해결하는 방법을 알아봅니다.'
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 02424338967d9a8bb376ba72938f30a4e217a83d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537432"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Azure Cache for Redis 데이터 손실 문제 해결

이 문서에서는 Azure Cache for Redis에서 발생할 수 있는 실제 또는 인식된 데이터 손실을 진단하는 방법을 설명합니다.

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="partial-loss-of-keys"></a>키 부분 손실

Azure Cache for Redis는 메모리에 저장된 키를 임의로 삭제하지 않습니다. 그러나 만료 또는 제거 정책에 대한 응답 및 명시적 키 삭제 명령에 따라 키를 제거합니다. 또한 프리미엄 또는 표준 Azure Cache for Redis 인스턴스에서 기본 노드에 기록된 키를 복제본에서 바로 사용할 수 없을 수도 있습니다. 데이터는 비동기 및 비중단 방식으로 기본에서 복제본으로 복제됩니다.

키가 캐시에서 사라진 경우 다음과 같은 가능한 원인을 확인합니다.

| 원인 | Description |
|---|---|
| [키 만료](#key-expiration) | 키에 설정된 시간 제한 때문에 키가 제거됩니다. |
| [키 제거](#key-eviction) | 메모리가 부족하면 키가 제거됩니다. |
| [키 삭제](#key-deletion) | 키는 명시적 delete 명령으로 제거됩니다. |
| [비동기 복제](#async-replication) | 데이터 복제 지연 때문에 복제본에서 키를 사용할 수 없습니다. |

### <a name="key-expiration"></a>키 만료

Azure Cache for Redis는 키에 시간 제한이 할당되고 해당 기간이 경과하면 자동으로 키를 제거합니다. Redis 키 만료에 대한 자세한 내용은 [EXPIRE](https://redis.io/commands/expire) 명령 설명서를 참조하세요. 시간 제한 값은 [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset) 및 기타 **\*STORE** 명령을 사용하여 설정할 수도 있습니다.

만료된 키 수에 대한 통계를 얻으려면 [INFO](https://redis.io/commands/info) 명령을 사용합니다. `Stats` 섹션에는 만료된 키의 총 수가 표시됩니다. `Keyspace` 섹션에는 시간 제한이 있는 키 수와 평균 시간 제한 값에 대한 자세한 정보를 제공합니다.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

캐시에 대한 진단 메트릭을 확인하여 키가 누락된 시간과 만료된 키의 급증 사이에 상관 관계가 있는지 확인할 수도 있습니다. Keyspace 알림 또는 **MONITOR** 를 사용하여 이러한 유형의 문제를 디버그하는 방법에 대한 자세한 내용은 [Redis Keyspace 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)의 부록을 참조하세요.

### <a name="key-eviction"></a>키 제거

Azure Cache for Redis에는 데이터를 저장하기 위한 메모리 공간이 필요합니다. 필요한 경우 사용 가능한 메모리를 확보하기 위해 키를 제거합니다. [INFO](https://redis.io/commands/info) 명령의 **used_memory** 또는 **used_memory_rss** 값이 구성된 **maxmemory** 설정에 접근하면 Azure Cache for Redis는 [캐시 정책](https://redis.io/topics/lru-cache)에 따라 메모리에서 키를 제거하기 시작합니다.

[INFO](https://redis.io/commands/info) 명령을 사용하여 제거된 키 수를 모니터링할 수 있습니다.

```
# Stats

evicted_keys:13224
```

캐시에 대한 진단 메트릭을 확인하여 키가 누락된 시간과 제거된 키의 급증 사이에 상관 관계가 있는지 확인할 수도 있습니다. Keyspace 알림 또는 **MONITOR** 를 사용하여 이러한 유형의 문제를 디버그하는 방법에 대한 자세한 내용은 [Redis Keyspace 누락 디버깅](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)의 부록을 참조하세요.

### <a name="key-deletion"></a>키 삭제

Redis 클라이언트는 [DEL](https://redis.io/commands/del) 또는 [HDEL](https://redis.io/commands/hdel) 명령을 실행하여 Azure Cache for Redis에서 키를 명시적으로 제거할 수 있습니다. [INFO](https://redis.io/commands/info) 명령을 사용하여 삭제 작업의 수를 추적할 수 있습니다. 호출된 **DEL** 또는 **HDEL** 명령은 `Commandstats` 섹션에 나열됩니다.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>비동기 복제

표준 또는 프리미엄 계층의 Azure Cache for Redis 인스턴스는 기본 노드 및 하나 이상의 복제본으로 구성됩니다. 데이터는 백그라운드 프로세스를 통해 기본에서 복제본으로 비동기적으로 복사됩니다. [redis.io](https://redis.io/topics/replication) 웹 사이트에서는 Redis 데이터 복제가 작동하는 방식을 전반적으로 설명합니다. 클라이언트가 자주 Redis에 쓰는 시나리오의 경우 이 복제가 즉시 수행되지 않을 수 있으므로 부분 데이터 손실이 발생할 수 있습니다. 예를 들어, 클라이언트가 키를 쓴 *이후* 이지만 백그라운드 프로세스가 키를 복제본에 보내기 *이전* 에 기본 노드가 다운되는 경우, 복제본이 새 기본으로 전환될 때 키가 손실됩니다.

## <a name="major-or-complete-loss-of-keys"></a>키의 대부분 또는 전부 손실

키가 대부분 또는 모두 캐시에서 사라진 경우 다음과 같은 가능한 원인을 확인합니다.

| 원인 | Description |
|---|---|
| [키 플러시](#key-flushing) | 키가 수동으로 제거되었습니다. |
| [잘못된 데이터베이스 선택](#incorrect-database-selection) | Azure Cache for Redis가 기본이 아닌 데이터베이스를 사용하도록 설정되어 있습니다. |
| [Redis 인스턴스 오류](#redis-instance-failure) | Redis 서버를 사용할 수 없습니다. |

### <a name="key-flushing"></a>키 플러시

클라이언트는 [FLUSHDB](https://redis.io/commands/flushdb) 명령을 호출하여 단일 데이터베이스의 모든 키를 제거하거나 [FLUSHALL](https://redis.io/commands/flushall) 명령을 호출하여 Redis 캐시에 있는 모든 데이터베이스의 모든 키를 제거할 수 있습니다. 키가 플러시되었는지 여부를 확인하려면 [INFO](https://redis.io/commands/info) 명령을 사용합니다. `Commandstats` 섹션에서는 **FLUSH** 명령이 호출되었는지 여부를 보여 줍니다.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>잘못된 데이터베이스 선택

Azure Cache for Redis는 기본적으로 **db0** 데이터베이스를 사용합니다. 다른 데이터베이스(**예: db1**)로 전환하고 키를 읽으려고 하면 Azure Cache for Redis가 키를 찾지 못합니다. 모든 데이터베이스는 논리적으로 분리된 단위이며 다른 데이터 세트를 보유합니다. [SELECT](https://redis.io/commands/select) 명령을 사용하여 다른 사용 가능한 데이터베이스로 전환하며 각 데이터베이스에서 키를 찾습니다.

### <a name="redis-instance-failure"></a>Redis 인스턴스 오류

Redis는 메모리 내 데이터 저장소입니다. 데이터는 Redis 캐시를 호스트하는 물리적 또는 가상 머신에 유지됩니다. 기본 계층의 Azure Cache for Redis 인스턴스는 단일 VM(가상 머신)에서만 실행됩니다. 이 VM이 다운되면 캐시에 저장된 모든 데이터가 손실됩니다. 

표준 및 프리미엄 계층의 캐시는 두 개의 VM을 복제된 구성으로 사용하여 데이터 손실에 훨씬 높은 복원력을 제공합니다. 이러한 캐시의 기본 노드가 실패하면 복제본 노드가 자동으로 데이터를 처리하는 데 사용됩니다. 이러한 VM은 장애 및 업데이트를 위해 별도의 도메인에 위치하여 동시에 사용할 수 없게 될 가능성을 최소화합니다. 그러나 주요 데이터 센터 중단이 발생하는 경우에는 두 VM이 함께 다운될 수 있습니다. 드물지만 이런 경우에는 데이터가 손실됩니다.

[Redis 데이터 지속성](https://redis.io/topics/persistence) 및 [지역 복제](./cache-how-to-geo-replication.md)를 사용하여 이러한 인프라 오류로부터 데이터 보호를 개선하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [올바른 계층 선택](cache-overview.md#choosing-the-right-tier)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-development-faq.yml#how-can-i-run-redis-commands-)