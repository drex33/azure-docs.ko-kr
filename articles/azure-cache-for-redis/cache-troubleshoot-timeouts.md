---
title: Azure Cache for Redis 시간 제한 문제 해결
description: Redis 서버 패치, StackExchange.Redis 시간 제한 예외 등 Azure Cache for Redis의 일반적인 시간 제한 문제를 해결하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/3/2021
ms.openlocfilehash: 09f461b5d64b52ded281e338e1307876d7e5f86b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843825"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Azure Cache for Redis 시간 제한 문제 해결

이 섹션에서는 Azure Cache for Redis에 연결하는 경우 발생하는 시간 제한 문제를 해결하는 방법을 설명합니다.

- [Redis 서버 패치](#redis-server-patching)
- [StackExchange.Redis 시간 제한 예외 조사](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="redis-server-patching"></a>Redis 서버 패치

Azure Cache for Redis에서는 제공하는 관리되는 서비스 기능의 일부로 해당 서버 소프트웨어를 정기적으로 업데이트합니다. 이 [패치](cache-failover.md) 작업은 주로 백그라운드에서 이루어집니다. Redis 서버 노드가 패치되는 경우 장애 조치(failover) 중에 이러한 노드에 연결된 Redis 클라이언트는 이러한 노드 간에 연결이 전환될 때 일시적인 시간 초과가 발생할 수 있습니다. 패치가 애플리케이션에 미칠 수 있는 부작용 및 패치 이벤트 처리를 개선하는 방법에 대한 자세한 내용은 장애 [조치(failover)가 클라이언트 애플리케이션에 미치는 영향을 참조하세요.](cache-failover.md#how-does-a-failover-affect-my-client-application)

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 시간 제한 예외 조사

StackExchange.Redis는 기본값이 5000ms인 동기 작업에 `synctimeout`이라는 구성 설정을 사용합니다. 동기 호출이 이 시간 내에 완료되지 않으면 StackExchange.Redis 클라이언트에 다음 예제와 유사한 시간 제한 오류가 발생합니다.

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

이 오류 메시지에는 문제의 원인과 가능한 해결로 이끌 메트릭이 들어 있습니다. 다음 테이블에는 오류 메시지 메트릭에 대한 세부 정보가 있습니다.

| 오류 메시지 메트릭 | 세부 정보 |
| --- | --- |
| `inst` |마지막 시간 조각에서: 0 명령이 발급되었습니다 |
| `mgr` |소켓 관리자가 `socket.select` 작업을 수행하는 중입니다. 즉, 수행할 작업이 있는 소켓을 나타내도록 OS에 요청하는 중입니다. 판독기는 수행할 작업이 없다고 생각하기 때문에 네트워크에서 읽기를 적극적으로 수행하지 않습니다. |
| `queue` |진행 중인 작업이 총 73건 있습니다. |
| `qu` |진행 중인 작업 중 6건은 보내지 않은 큐에 있으며 아직 아웃바운드 네트워크에 작성되지 않았습니다. |
| `qs`|진행 중인 작업 중 67건은 서버로 보내졌지만 아직 응답이 없습니다. 응답은 `Not yet sent by the server` 또는 `sent by the server but not yet processed by the client.`일 수 있습니다. |
| `qc` |진행 중인 작업 중 0개에서 회신이 표시되지만 완료 루프를 기다리고 있으므로 아직 완료로 표시되지 않았습니다. |
| `wr` |활성 기록기(6개의 비전송 요청이 무시되지 않는다는 의미) 바이트/activewriter가 있습니다. |
| `in` |활성 판독기가 없으며 NIC 바이트/activereaders에서 판독하는 데 사용 가능한 바이트는 0입니다. |

앞의 예외 예에서 `IOCP` 및 `WORKER` 섹션은 각각 `Min` 값보다 큰 `Busy` 값을 포함합니다. 차이점은 `ThreadPool` 설정을 조정해야한다는 것을 의미합니다. 버스트 시나리오 하에서 스레드 풀이 신속하게 규모 확장을 하도록 [스레드 풀 설정을 구성](cache-management-faq.yml#important-details-about-threadpool-growth)할 수 있습니다.

다음 단계를 사용하여 가능한 근본 원인을 제거할 수 있습니다.

1. 연결 [복원력](cache-best-practices-connection.md#using-forcereconnect-with-stackexchangeredis)문서에 설명된 대로 ForceReconnect 패턴을 사용하여 중단된 연결을 검색하고 바꾸는 것이 가장 좋습니다.

   StackExchange.Redis 사용에 대한 자세한 내용은 [StackExchange.Redis를 사용하여 캐시에 커넥트 참조하세요.](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) 

1. 서버와 클라이언트 애플리케이션이 Azure의 동일한 지역에 있는지 확인합니다. 예를 들어 캐시는 미국 동부에 있지만 클라이언트는 미국 서부에 있으며 요청이 `synctimeout` 간격 내에 완료되지 않는 경우 시간 제한이 발생할 수 있습니다. 또는 로컬 개발 머신에서 디버그하는 중인 경우 시간 제한이 발생할 수 있습니다.

    동일한 Azure 지역에 캐시와 클라이언트를 두도록 하는 것이 좋습니다. 지역 간 호출이 포함된 시나리오가 있는 경우 연결 문자열에 `synctimeout` 속성을 포함하여 `synctimeout` 간격을 기본 간격인 5000ms보다 높은 값으로 설정해야 합니다. 다음 예에서는 `synctimeout`이 8000ms인 Azure Cache for Redis에서 제공하는 StackExchange.Redis 연결 문자열의 코드 조각을 보여 줍니다.

    ```output
    synctimeout=8000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
1. 요청이 서버 또는 클라이언트의 대역폭 제한에 바인딩되어 있는 경우 요청이 완료되는 데 더 많은 시간이 걸려 시간 제한의 원인이 될 수 있습니다. 시간 제한이 서버의 네트워크 대역폭으로 인해 발생하는지 확인하려면 [서버 쪽 대역폭 제한](cache-troubleshoot-server.md#server-side-bandwidth-limitation)을 참조하세요. 시간 제한이 클라이언트 네트워크 대역폭으로 인해 발생하는지 확인하려면 [클라이언트 쪽 대역폭 제한](cache-troubleshoot-client.md#client-side-bandwidth-limitation)을 참조하세요.
1. CPU가 서버 또는 클라이언트에 바인딩됩니까?

   - CPU가 클라이언트에 바인딩되어 있는지 확인합니다. 높은 CPU는 요청이 `synctimeout` 간격 내에 처리되지 않고 요청에 시간 제한이 발생하는 원인이 될 수 있습니다. 크기가 더 큰 클라이언트로 이동하거나 부하를 분산하면 이 문제를 제어하는 데 도움이 될 수 있습니다.
   - CPU [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링하여 CPU가 서버에 바인딩되어 있는지 확인합니다. Redis가 CPU 바인딩되어 있는 동안 들어오는 요청에는 시간 제한이 발생할 수 있습니다. 이 상태를 해결하기 위해 프리미엄 캐시의 여러 분할에 부하를 분산하거나 더 큰 크기 또는 가격 책정 계층으로 업그레이드할 수 있습니다. 자세한 내용은 [서버 쪽 대역폭 제한](cache-troubleshoot-server.md#server-side-bandwidth-limitation)을 참조하세요.
1. 서버에서 처리하는데 시간이 오래 걸리는 명령이 있습니까? Redis 서버에서 처리하는 데 시간이 오래 걸리는 장기 실행 명령은 시간 제한의 원인이 될 수 있습니다. 장기 실행 명령에 대한 자세한 내용은 [장기 실행 명령](cache-troubleshoot-server.md#long-running-commands)을 참조하세요. redis-cli 클라이언트 또는 [Redis 콘솔](cache-configure.md#redis-console)을 사용하여 Azure Cache for Redis 인스턴스에 연결할 수 있습니다. 그런 다음 [SLOWLOG](https://redis.io/commands/slowlog) 명령을 실행하여 예상 보다 느린 요청이 있는지 확인합니다. Redis 서버와 StackExchange.Redis는 작은 수의 큰 요청보다 많은 수의 작은 요청을 위해 최적화되어 있습니다. 데이터를 더 작은 청크로 분할한다면 다음을 향상시킵니다.

    redis-cli 및 stunnel을 사용하여 캐시 TLS/SSL 엔드포인트에 연결하는 방법에 대한 자세한 내용은 블로그 게시물 [Announcing ASP.NET Session State Provider for Redis Preview Release](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)(Redis 미리 보기 릴리스용 ASP.NET 세션 상태 제공자 발표)를 참조하세요.
1. 높은 Redis 서버 부하로 시간 초과가 발생할 수 있습니다. `Redis Server Load` [캐시 성능 메트릭](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 모니터링함으로써 서버 부하를 모니터링할 수 있습니다. 100(최대 값)이란 서버 부하는 redis 서버가 요청을 처리하느라 유휴 시간이 전혀 없이 사용 중이었음을 나타냅니다. 특정 요청이 서버 기능의 전부를 차지하는지 확인하려면 이전 단락에서 설명한 대로 SlowLog 명령을 실행합니다. 자세한 내용은 높은 CPU 사용량/서버 로드를 참조하세요.
1. 클라이언트 쪽에서 네트워크 문제를 일으킬 만한 이벤트가 있었습니까? 일반적인 이벤트로는 클라이언트 인스턴스 수 스케일 업/다운, 새 버전의 클라이언트 배포, 자동 스케일링 사용이 있습니다. 이 테스트에서는 자동 스케일링 또는 스케일 업/다운으로 인해 아웃바운드 네트워크 연결이 몇 초 동안 손실될 수 있음을 알게 되었습니다. StackExchange.Redis 코드는 이러한 이벤트에 복원력이 있어 다시 연결합니다. 다시 연결하는 동안 큐에 있는 모든 요청에 시간 제한이 발생할 수 있습니다.
1. 시간 제한이 발생한 캐시에 여러 작은 요청에 앞서 큰 요청이 있었나요? 오류 메시지에 있는 `qs` 매개 변수는 클라이언트에서 서버로 보내졌으나 아직 응답을 처리하지 않은 요청 수를 알려줍니다. StackExchange.Redis는 하나의 TCP 연결을 사용하고 한 번에 하나의 응답만 읽을 수 있기 때문에 이 값은 계속 증가할 수 있습니다. 첫 번째 작업에 시간 제한이 발생해도 서버에서 또는 서버로 데이터를 더 보내는 작업이 중지되지 않습니다. 큰 요청이 완료될 때까지 다른 요청은 차단되므로 시간 제한이 발생하는 원인이 될 수 있습니다. 하나의 솔루션은 워크로드를 감당할 수 있게 캐시를 충분히 크게 하고 큰 값을 작은 청크로 분할하여 시간 초과의 가능성을 최소화하는 것입니다. 또 다른 가능한 솔루션은 클라이언트에서 `ConnectionMultiplexer` 개체 풀을 사용하고, 새 요청을 보낼 때 부하가 최소인 `ConnectionMultiplexer`을 선택합니다, 여러 연결 개체에서 로드하면 한 번의 시간 제한이 발생해도 다른 요청에는 시간 제한이 발생하지 않도록 방지합니다.
1. `RedisSessionStateProvider`를 사용하는 경우 다시 시도 시간 제한을 올바르게 설정했는지 확인하세요. `retryTimeoutInMilliseconds`는 `operationTimeoutInMilliseconds`보다 높아야 합니다. 그렇지 않으면 재시도가 발생하지 않습니다. 다음 예제에서 `retryTimeoutInMilliseconds` 가 3000으로 설정됩니다. 자세한 내용은 [Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md) 및 [세션 상태 제공자 및 출력 캐시 공급자의 구성 매개 변수를 사용하는 방법](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)을 참조하세요.

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. `Used Memory RSS`및`Used Memory` 를 [모니터링하여](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) Azure Cache for Redis 서버의 메모리 사용량을 확인합니다. 제거 정책이 구현되었다면, `Used_Memory` 가 캐시 크기에 도달할 때 Redis는 제거 키를 작동합니다. 이상적으로 `Used Memory RSS`는 `Used memory`보다 약간만 높아야 합니다. 큰 차이는 메모리 조각화(내부 또는 외부)가 있음을 의미합니다. `Used Memory RSS`가 `Used Memory`보다 작다면 운영 체제가 캐시 메모리의 일부를 스왑했음을 의미합니다. 이 스왑이 발생하는 경우 일부 상당한 대기 시간을 예상할 수 있습니다. Redis는 할당이 메모리 페이지에 매핑되는 방법을 제어하지 않기 때문에 높은 `Used Memory RSS`는 메모리 사용량의 스파이크 결과인 경우가 많습니다. Redis 서버에서 메모리를 해제하는 경우 할당자는 메모리를 사용하지만 시스템에 메모리를 다시 제공할 수도 있고 제공하지 않을 수도 있습니다. 운영 체제가 보고하는 `Used Memory` 값과 메모리 소비량 사이에 차이가 있을 수 있습니다. 메모리를 Redis에서 사용하고 해제했으나 시스템에 다시 제공하지 않았을 수 있습니다. 메모리 문제를 완화하는 데 도움이 되도록 다음 단계를 수행할 수 있습니다.

   - 캐시를 더 큰 크기로 업그레이드하여 시스템에서 메모리 제한이 발생하지 않도록 합니다.
   - 키에 만료 시간을 설정하여 이전 값이 사전에 제거되게 합니다.
   - `used_memory_rss` 캐시 메트릭을 모니터링합니다. 이 값이 해당 캐시의 크기에 가까워지면 성능 문제가 나타나기 시작할 가능성이 있습니다. 프리미엄 캐시를 사용하거나 더 큰 캐시 크기로 업그레이드하는 경우 여러 분할에 데이터를 배포합니다.

   자세한 내용은 [Redis 서버의 메모리 압력](cache-troubleshoot-server.md#memory-pressure-on-redis-server)을 참조하세요.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 클라이언트 쪽 문제 해결](cache-troubleshoot-client.md)
- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
