---
title: Azure Cache for Redis 클라이언트 쪽 문제 해결
description: Redis 클라이언트 메모리 압력, 트래픽 버스트, 높은 CPU, 제한된 대역폭, 크기가 큰 요청 또는 응답 등 Azure Cache for Redis와 관련된 일반적인 클라이언트 쪽 문제를 해결하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: a41329da9171014b0495498f8757007dbef008ef
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537426"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Azure Cache for Redis 클라이언트 쪽 문제 해결

이 섹션에서는 애플리케이션에서 사용하는 Redis 클라이언트에 대한 조건 때문에 발생하는 문제를 해결하는 방법을 설명합니다.

- [Redis 클라이언트의 메모리 압력](#memory-pressure-on-redis-client)
- [트래픽 버스트](#traffic-burst)
- [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
- [클라이언트 쪽 대역폭 제한](#client-side-bandwidth-limitation)
- [크기가 큰 요청 또는 응답](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 클라이언트의 메모리 압력

클라이언트 머신의 메모리 압력은 캐시의 응답 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리 압력에 직면하면 시스템에서 데이터를 디스크에 페이징할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다.

클라이언트의 메모리 압력을 검색하려면 다음을 수행합니다.

- 머신의 메모리 사용량을 모니터링하여 사용 가능한 메모리를 초과하지 않는지 확인합니다.
- 클라이언트의 `Page Faults/Sec` 성능 카운터를 모니터링합니다. 정상적인 작업 중에도 대부분의 시스템에는 어느 정도의 페이지 폴트가 있습니다. 요청 시간 제한에 해당하는 페이지 폴트의 급증은 메모리 부족을 나타낼 수 있습니다.

클라이언트의 높은 메모리 압력은 여러 가지 방법으로 완화할 수 있습니다.

- 메모리 사용 패턴을 자세히 알아보고 클라이언트의 메모리 소비를 줄입니다.
- 메모리가 더 많은 더 큰 크기로 클라이언트 VM을 업그레이드합니다.

## <a name="traffic-burst"></a>트래픽 버스트

나쁜 `ThreadPool` 설정과 결합된 트래픽 폭주는 Redis 서버에서 이미 보냈으나 클라이언트 쪽에서 아직 소비되지 않은 데이타의 처리 지연이 발생할 수 있습니다.

[예제 `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)를 사용하여 `ThreadPool` 통계가 시간에 따라 어떻게 바뀌는지 모니터링합니다. 아래와 같이 StackExchange.Redis에서 `TimeoutException` 메시지를 사용하여 자세히 조사할 수 있습니다.

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

앞의 예외에는 몇 가지 흥미로운 문제가 있습니다.

- `IOCP` 섹션과 `WORKER` 섹션에 `Min` 값보다 큰 `Busy` 값이 있습니다. 이러한 차이는 `ThreadPool` 설정을 조정해야 한다는 것을 나타냅니다.
- 또한 `in: 64221`도 볼 수 있습니다: 이 값은 64,211바이트를 클라이언트의 커널 소켓 계층에서 받았지만 애플리케이션에서 아직 읽지 않았음을 나타냅니다. 이 차이는 일반적으로 애플리케이션(예: StackExchange.Redis)이 데이터를 서버에서 보내는 만큼 빠르게 네트워크로부터 읽지 못하고 있음을 의미합니다.

[`ThreadPool` 설정을 구성](cache-management-faq.yml#important-details-about-threadpool-growth)하여 버스트 시나리오에서 스레드 풀이 신속하게 스케일 업할 수 있도록 합니다.

## <a name="high-client-cpu-usage"></a>클라이언트의 높은 CPU 사용량

클라이언트의 높은 CPU 사용량은 시스템이 수행하도록 요청받은 작업을 수행하지 못하는 것을 나타냅니다. 캐시가 신속하게 응답을 전송해도 클라이언트가 제시간에 응답을 처리하지 못할 수 있습니다.

Azure Portal에서 제공하는 메트릭을 사용하거나 머신의 성능 카운터를 통해 클라이언트의 시스템 전체 CPU 사용량을 모니터링하세요. 단일 프로세스는 CPU 사용량이 낮지만 시스템 전체 CPU 사용량은 높을 수 있으므로 ‘프로세스’ CPU를 모니터링하지 않도록 주의합니다. CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다. [트래픽 버스트](#traffic-burst) 섹션에 설명된 대로 높은 CPU는 `TimeoutException` 오류 메시지의 `in: XXX` 값을 높이는 원인이 될 수도 있습니다.

> [!NOTE]
> StackExchange.Redis 1.1.603 이상은 `TimeoutException` 오류 메시지에 `local-cpu` 매트릭을 포함합니다. [StackExchange.Redis NuGet 패키지](https://www.nuget.org/packages/StackExchange.Redis/)최신 버전을 사용 중인지 확인합니다. 제한 시간에 더욱 견고하도록 만들기 위해 코드 속의 버그를 지속적으로 수정하고 있으므로 최신 버전을 갖는 것이 중요합니다.
>

클라이언트의 높은 CPU 사용량을 완화하려면 다음을 수행합니다.

- CPU 스파이크의 원인을 조사합니다.
- CPU 용량이 더 많은 더 큰 VM 크기로 클라이언트를 업그레이드합니다.

## <a name="client-side-bandwidth-limitation"></a>클라이언트 쪽 대역폭 제한

클라이언트 컴퓨터의 아키텍처에 따라, 클라이언트 컴퓨터는 어느 정도의 네트워크 대역폭을 사용할 수 있는지에 제한이 있습니다. 클라이언트가 네트워크 용량을 오버로드하여 사용 가능한 대역폭을 초과하면 서버에서 보내는 만큼 신속하게 클라이언트 쪽에서 데이터가 처리되지 않습니다. 이 경우 시간이 초과될 수 있습니다.

[예제 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)를 사용하여 대역폭 사용량이 시간에 따라 어떻게 바뀌는지 모니터링합니다. 이 코드는 (Azure 웹 사이트와 같이) 권한이 제한된 일부 환경에서 성공적으로 실행되지 않을 수도 있습니다.

이 문제를 완화하려면 네트워크 대역폭 소비를 줄이거나 클라이언트 VM 크기를 네트워크 용량이 더 많은 크기로 늘리세요.

## <a name="large-request-or-response-size"></a>크기가 큰 요청 또는 응답

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

1. 적은 수의 큰 값이 아니라 많은 수의 작은 값에 대해 애플리케이션을 최적화합니다.
    - 선호하는 해결 방법은 데이터를 더 작은 값으로 분할하는 것입니다.
    - 더 작은 값이 권장되는 이유에 대한 자세한 내용은 [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)(Redis에 이상적인 값 크기 범위는 얼마인가요? 100KB는 너무 큰가요?) 게시물을 참조하세요.
1. VM 크기를 늘려 대역폭 기능을 더 높입니다.
    - 클라이언트 또는 서버 VM의 대역폭이 증가하면 크기가 큰 응답의 데이터 전송 시간을 줄일 수 있습니다.
    - 두 머신의 현재 네트워크 사용량과 현재 VM 크기 한도를 비교해 보세요. 서버에서만 또는 클라이언트에서만 대역폭이 증가하는 것으로는 충분하지 않을 수 있습니다.
1. 애플리케이션에서 사용하는 연결 개체 수를 늘립니다.
    - 라운드 로빈 방식을 사용하여 여러 연결 개체에 대한 요청을 수행할 수 있습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
