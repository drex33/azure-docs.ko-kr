---
title: 성능 시험 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis의 성능을 시험하는 방법에 대해 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 748e7e09332e9feded9af0bc6cfa9a38d27086f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615209"
---
# <a name="performance-testing"></a>성능 테스트

1. 사용자 고유의 성능 테스트를 작성하기 전에, 먼저 `redis-benchmark.exe`을 사용하여 캐시의 일반적인 처리량 및 대기 시간 특성을 확인합니다. 자세한 내용은 [Redis-Benchmark](#redis-benchmark-utility)를 참조하세요.

1. 테스트에 사용되는 클라이언트 VM은 Redis 캐시 인스턴스와 *동일한 지역* 에 있어야 합니다.

1. 사용하는 클라이언트 VM의 *컴퓨팅 및 대역폭 수준이 적어도* 테스트 중인 캐시 만큼인지 확인합니다.

1. 안정적인 상태 조건에서만 캐시의 성능을 테스트하지 않는 것이 중요합니다. *장애 조치 조건에서도 테스트하고* 해당 시간 동안 캐시에서 CPU/서버 부하를 측정합니다. [주 노드를 다시 부팅](cache-administration.md#reboot)하여 장애 조치를 시작할 수 있습니다. 장애 조치 조건에서 테스트하면 장애 조치 상태 중 애플리케이션의 처리량과 대기 시간을 확인할 수 있습니다. 장애 조치는 업데이트 중 또는 계획되지 않은 이벤트 중에 발생할 수 있습니다. 이상적으로는 성능에 영향을 줄 수 있는 장애 조치 중에도 CPU/서버 부하 최대치가 80%를 넘지 않는 것이 좋습니다.

1. Redis 인스턴스용 Premium 계층 Azure Cache를 사용하는 것이 좋습니다. 이 캐시 크기는 CPU 및 네트워크 둘 다에 대해 더 나은 하드웨어에서 실행되므로 더 나은 네트워크 대기 시간 및 처리량을 제공합니다.

   > [!NOTE]
   > 관찰된 성능 결과가 참조를 위해 [여기에 게시](./cache-planning-faq.yml#azure-cache-for-redis-performance)됩니다. 또한 SSL/TLS에서는 약간의 오버헤드가 더해지므로, 전송 암호화를 사용할 경우 대기 시간 및/또는 처리량이 다를 수 있습니다.

## <a name="redis-benchmark-utility"></a>Redis-벤치마크 유틸리티

**Redis 벤치마크** 설명서는 [여기에서 확인](https://redis.io/topics/benchmarks)할 수 있습니다.

`redis-benchmark.exe`는 TLS를 지원하지 않습니다. 테스트를 실행하기 전에 [포털을 통해 비-TLS 포트를 사용하도록 설정](cache-configure.md#access-ports)해야 합니다. redis-benchmark.exe의 Windows 호환 버전은 [여기](https://github.com/MSOpenTech/redis/releases)에서 찾을 수 있습니다.

## <a name="redis-benchmark-examples"></a>Redis 벤치마크 예제

**사전 테스트 설정**: 대기 시간 및 처리량 테스트에 필요한 데이터를 사용하여 캐시 인스턴스를 준비합니다.

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024
```

**대기 시간 테스트 방법**: 1k 페이로드를 사용하여 GET 요청을 테스트합니다.

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4
```

**처리량 테스트 방법:** 1k 페이로드를 사용하여 GET 요청을 파이프라인으로 연결합니다.

```dos
redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
```

## <a name="next-steps"></a>다음 단계

- [개발](cache-best-practices-development.md)
- [Azure Cache for Redis 개발 FAQ](cache-development-faq.yml)
- [Azure Cache for Redis를 위한 장애 조치(failover) 및 패치](cache-failover.md)