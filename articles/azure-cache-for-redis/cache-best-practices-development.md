---
title: 개발을 위한 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis에 대한 코드를 개발하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: 1c0c6b5f79e38dbd400dc048e2cd14cd2f52d35a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850986"
---
# <a name="development"></a>개발

## <a name="connection-resilience-and-server-load"></a>연결 복원력 및 서버 부하

클라이언트 애플리케이션을 개발할 때 [연결 복원력](cache-best-practices-connection.md) 및 [서버 부하 관리](cache-best-practices-server-load.md)와 관련된 모범 사례를 고려합니다.

## <a name="consider-more-keys-and-smaller-values"></a>더 많은 키와 더 작은 값 고려

Redis 용 Azure Cache는 작은 값에 가장 잘 작동 합니다. 데이터를 여러 키에 분산시키려면 더 큰 데이터 청크를 더 작은 청크로 나누는 것을 고려합니다. 이상적인 값 크기에 대한 자세한 내용은 이 [문서](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)를 참조하세요.

## <a name="large-request-or-response-size"></a>큰 요청 또는 응답 크기

큰 요청/응답으로 시간 초과가 발생할 수 있습니다. 예를 들어 클라이언트에 구성된 시간 제한 값이 1초라고 가정하겠습니다. 애플리케이션이 동시에 2개의 키(예: 'A' 및 'B')를 요청합니다(동일한 실제 네트워크 연결 사용). 대부분의 클라이언트에서는 요청 ‘A’와 ‘B’가 둘 다 응답을 기다리지 않고 연달아 전송되는 요청 “파이프라인”을 지원합니다. 서버는 응답을 동일한 순서로 전송합니다. 응답 ‘A’의 크기가 크면 후속 요청을 위한 시간 제한에서 대부분의 시간을 사용할 수 있습니다.

다음 예제에서는 요청 ‘A’와 ‘B’가 서버로 신속하게 전송됩니다. 서버는 신속하게 응답 ‘A’와 ‘B’를 전송하기 시작합니다. 데이터 전송 시간 때문에 응답 ‘B’는 서버가 신속하게 응답하는 경우에도 응답 ‘A’의 시간 제한이 종료될 때까지 기다려야 합니다.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

이 요청/응답은 측정하기 어렵습니다. 크기가 큰 요청 및 응답을 추적하도록 클라이언트 코드를 계측할 수 있습니다.

크기가 큰 응답 문제를 해결하는 방법은 다음과 같이 다양합니다.

- 적은 수의 큰 값이 아니라 많은 수의 작은 값에 대해 애플리케이션을 최적화합니다.
    - 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다.
    - 더 작은 값이 권장되는 이유에 대한 자세한 내용은 [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)(Redis에 이상적인 값 크기 범위는 얼마인가요? 100KB는 너무 큰가요?) 게시물을 참조하세요.
- VM 크기를 늘려 대역폭 기능을 더 높입니다.
    - 클라이언트 또는 서버 VM의 대역폭이 증가하면 크기가 큰 응답의 데이터 전송 시간을 줄일 수 있습니다.
    - 두 머신의 현재 네트워크 사용량과 현재 VM 크기 한도를 비교해 보세요. 서버에서만 또는 클라이언트에서만 대역폭이 증가하는 것으로는 충분하지 않을 수 있습니다.
- 애플리케이션에서 사용하는 연결 개체 수를 늘립니다.
    - 라운드 로빈 방식을 사용하여 여러 연결 개체에 대한 요청을 수행할 수 있습니다.

## <a name="key-distribution"></a>키 배포

Redis 클러스터링을 사용할 계획이라면 먼저 [키를 사용한 Redis 클러스터링 모범 사례](https://redislabs.com/blog/redis-clustering-best-practices-with-keys/)를 읽으세요.

## <a name="use-pipelining"></a>파이프라인 사용

[Redis 파이프라인](https://redis.io/topics/pipelining)을 지원하는 Redis 클라이언트를 선택해 보세요. 파이프라인은 네트워크를 효율적으로 사용하고 가능한 최상의 처리량을 얻는 데 도움이 됩니다.

## <a name="avoid-expensive-operations"></a>비용이 많이 드는 작업 방지

[KEYS](https://redis.io/commands/keys) 명령과 같은 일부 Redis 작업은 비용이 많이 들기 때문에 피해야 합니다. 장기 실행 명령에 대한 몇 가지 고려 사항은 [장기 실행 명령](cache-troubleshoot-server.md#long-running-commands)을 참조하세요.

## <a name="choose-an-appropriate-tier"></a>적절한 계층 선택

프로덕션 시스템에 대해 표준 또는 프리미엄 계층을 사용합니다.  프로덕션에서 기본 계층을 사용하지 마세요. 기본 계층은 데이터 복제 및 SLA가 없는 단일 노드 시스템입니다. 또한 C1 이상의 캐시를 사용합니다. C0 캐시는 다음과 같은 이유로 단순한 개발/테스트 시나리오에만 사용됩니다.

- CPU 코어 공유
- 적은 메모리 사용
- *노이지 네이버* 문제가 발생하기 쉬움

성능 테스트를 통해 올바른 계층을 선택하고 연결 설정의 유효성을 검사하는 것이 좋습니다. 자세한 내용은 [성능 테스트](cache-best-practices-performance.md)를 참조하세요.

## <a name="client-in-same-region-as-cache"></a>캐시와 동일한 지역의 클라이언트

캐시 인스턴스와 애플리케이션을 항상 동일한 지역에 배치합니다. 다른 지역의 캐시에 연결하면 대기 시간이 크게 증가하고 안정성이 떨어질 수 있습니다.  

Azure 외부에서 연결할 수 있지만 *특히 Redis를 캐시로 사용* 하지 않는 것이 좋습니다.  Redis 서버를 키/값 저장소로만 사용하는 경우에는 대기 시간이 중요하지 않을 수 있습니다.

## <a name="use-tls-encryption"></a>TLS 암호화 사용

Azure Cache for Redis에는 기본적으로 TLS 암호화 통신이 필요합니다. 현재 TLS 버전 1.0, 1.1, 1.2가 지원됩니다. 그러나 TLS 1.0 및 1.1은 업계 전반에서 사용하지 않도록 하는 과정에 있으므로 가능하다면 1.2를 사용합니다.

클라이언트 라이브러리 또는 도구에서 TLS를 지원하지 않는 경우 [Azure Portal](cache-configure.md#access-ports) 또는 [관리 API](/rest/api/redis/redis/update)를 통해 암호화되지 않은 연결을 사용하도록 설정할 수 있습니다. 암호화된 연결을 사용할 수 없는 경우에는 캐시 및 클라이언트 애플리케이션을 가상 네트워크에 배치하는 것이 좋습니다. 가상 네트워크 캐시 시나리오에서 사용되는 포트에 대한 자세한 내용은 다음 [표](cache-how-to-premium-vnet.md#outbound-port-requirements)를 참조하세요.

## <a name="client-library-specific-guidance"></a>클라이언트 라이브러리 관련 참고 자료

- [StackExchange.Redis(.NET)](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis)
- [Java - 어떤 클라이언트를 사용해야 하나요?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
- [Lettuce(Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
- [Jedis(Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
- [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
- [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
- [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
- [ASP.NET 세션 상태 제공자](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)

## <a name="next-steps"></a>다음 단계

- [Azure Cache for Redis 개발 FAQ](cache-development-faq.yml)
- [성능 테스트](cache-best-practices-performance.md)
- [Azure Cache for Redis를 위한 장애 조치(failover) 및 패치](cache-failover.md)
