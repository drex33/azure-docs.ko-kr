---
title: Azure Cache for Redis 클라이언트 쪽 문제 해결
description: Redis 클라이언트 메모리 압력, 트래픽 버스트, 높은 CPU, 제한된 대역폭, 크기가 큰 요청 또는 응답 등 Azure Cache for Redis와 관련된 일반적인 클라이언트 쪽 문제를 해결하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: f7510d82330fb0d505f819a6babf7c2823dcfdff
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133097401"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Azure Cache for Redis 클라이언트 쪽 문제 해결

이 섹션에서는 애플리케이션에서 사용하는 Redis 클라이언트에 대한 조건 때문에 발생하는 문제를 해결하는 방법을 설명합니다.

- [Redis 클라이언트의 메모리 압력](#memory-pressure-on-redis-client)
- [트래픽 버스트](#traffic-burst)
- [클라이언트의 높은 CPU 사용량](#high-client-cpu-usage)
- [클라이언트 쪽 대역폭 제한](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

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

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
 
## <a name="high-client-connections"></a>높은 클라이언트 연결

클라이언트 연결에서 캐시에 대 한 최대 크기에 도달 하면 클라이언트 요청에서 최대를 초과 하 여 연결에 대 한 오류가 발생할 수 있으며, 반복 되는 다시 연결 시도를 처리 하기 때문에 캐시에서 서버 CPU 사용량이 많이 발생할 수도 있습니다.

클라이언트 연결이 높으면 클라이언트 코드에서 연결 누수가 발생할 수 있습니다.  연결이 다시 사용 되거나 제대로 닫히지 않았을 수 있습니다.  클라이언트 코드를 사용 하 여 연결을 검토 합니다.

높은 연결이 모든 합법적인 클라이언트 연결이 고 필요한 경우에는 더 높은 연결 제한이 있는 크기로 캐시를 업그레이드 해야 할 수 있습니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
