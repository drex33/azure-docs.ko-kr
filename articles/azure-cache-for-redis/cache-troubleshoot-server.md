---
title: Azure Cache for Redis 서버 쪽 문제 해결
description: 메모리 압력, 높은 CPU, 장기 실행 명령 또는 대역폭 제한과 같은 Redis 용 Azure Cache를 사용하여 일반적인 서버 쪽 문제를 해결하는 방법에 대해 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 603529c5a10745dfdd1bb2a7eec1758f1e49ebd9
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218171"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Azure Cache for Redis 서버 쪽 문제 해결

이 섹션에서는 Azure Cache for Redis 또는 이를 호스팅하는 가상 머신(들)의 조건으로 인해 발생하는 문제의 해결 방안을 논의합니다.

- [Redis 서버의 메모리 압력](#memory-pressure-on-redis-server)
- [높은 CPU 사용량 또는 서버 부하](#high-cpu-usage-or-server-load)
- [장기 실행 명령](#long-running-commands)
- [서버 쪽 대역폭 제한](#server-side-bandwidth-limitation)

> [!NOTE]
> 이 가이드에 나오는 문제 해결 단계 중 몇 가지는 Redis 명령을 실행하고 다양한 성능 메트릭을 모니터링하는 것을 포함합니다. 자세한 내용 및 지침은 [추가 정보](#additional-information) 섹션의 문서를 참조합니다.
>

## <a name="memory-pressure-on-redis-server"></a>Redis 서버의 메모리 압력

서버 쪽의 메모리 부족은 요청 처리를 지연시킬 수 있는 온갖 종류의 성능 문제를 일으킵니다. 메모리 압력에 직면하면 시스템에서 데이터를 디스크에 페이징할 수 있습니다. 이 _페이지 폴트_ 가 시스템이 크게 느려진 원인입니다. 이 메모리 부족의 몇 가지 가능한 원인은 다음과 같습니다.

- 캐시는 자체 최대 용량에 가까운 데이터로 채워집니다.
- Redis가 상위 메모리 조각화를 표시합니다. 이러한 조각화는 Redis는 작은 개체에 최적화되어 있는데 큰 개체를 저장하여 발생하는 경우가 가장 많습니다.

Redis는 이러한 문제를 식별하는 데 도움을 주는 [INFO](https://redis.io/commands/info) 명령을 통해 "used_memory" 및 "used_memory_rss"의 두 가지 통계를 노출합니다. 포털을 사용하여 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer) 수 있습니다.

메모리 사용량의 정상 유지를 위해 몇 가지 가능한 변경은 다음과 같습니다.

- [메모리 정책을 구성](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 하고 키에 만료 시간을 설정합니다. 조각화가 있을 경우 이 정책으로는 부족할 수 있습니다.
- [maxmemory-reserved 값을 구성합니다](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) .
- 크게 캐시된 개체를 더 작은 관련 개체로 분할합니다.
- 사용 중인 메모리와 같은 메트릭에 대한 [경고를 생성](cache-how-to-monitor.md#alerts)하여 잠재적인 영향에 대해 조기에 알림을 받을 수 있습니다.
- 더 많은 메모리 용량을 사용하여 더 큰 캐시 크기로 [조정합니다](cache-how-to-scale.md).
- 더 많은 메모리 용량을 사용하여 더 큰 캐시 크기로 [조정합니다](cache-how-to-scale.md). 자세한 내용은 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.

## <a name="high-cpu-usage-or-server-load"></a>높은 CPU 사용량 또는 서버 부하

서버 부하 또는 CPU 사용량이 높으면 서버가 요청을 적시에 처리할 수 없음을 의미합니다. 서버의 응답 속도가 느려지고 요청 속도를 따라가지 못할 수 있습니다.

CPU 또는 서버 부하와 같은 [메트릭을 모니터링합니다](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer). CPU 사용량에서 제한 시간에 해당 하는 급증을 확인합니다.

높은 서버 부하를 완화하기 위해 수행할 수 있는 몇 가지 변경 사항은 다음과 같습니다.

- 아래에 명시된 [장기 실행 명령](#long-running-commands) 또는 상위 메모리 압력으로 인한 페이지 폴트와 같은 CPU 급증 원인을 조사하십시오.
- CPU 또는 서버 부하와 같은 메트릭에 대한 [경고를 생성](cache-how-to-monitor.md#alerts)하여 잠재적인 영향에 대해 조기에 알림을 받을 수 있습니다.
- 더 많은 분할된 데이터베이스로 [스케일 아웃](cache-how-to-scale.md)하여 여러 Redis 프로세스에 부하를 분산하거나, 더 많은 CPU 코어가 있는 대규모 캐시 크기로 스케일 업합니다. 자세한 내용은 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.

## <a name="long-running-commands"></a>장기 실행 명령

일부 Redis 명령 실행은 다른 명령에 비해 비용이 많이 듭니다. [Redis 명령 설명서](https://redis.io/commands)는 각 명령의 시간 복잡성을 보여 줍니다. Redis 명령 처리는 단일 스레드이므로, 실행하는 데 시간이 걸리는 명령은 그 다음에 오는 다른 모든 명령을 차단합니다. Redis 서버에 실행하는 명령을 검토하여 성능에 미치는 영향을 파악합니다. 예를 들어, [KEYS](https://redis.io/commands/keys) 명령은 O(N) 작업이라고 알지 못해도 자주 사용됩니다. [SCAN](https://redis.io/commands/scan)을 사용하여 CPU 급증을 줄이는 KEYS를 방지할 수 있습니다.

[SLOWLOG GET](https://redis.io/commands/slowlog-get) 명령을 사용 하 여 서버에 대해 실행 되는 비용이 많이 드는 명령을 측정할 수 있습니다.
 

## <a name="server-side-bandwidth-limitation"></a>서버 쪽 대역폭 제한

캐시 크기가 다르면 네트워크 대역폭 용량도 다릅니다. 서버가 사용 가능한 대역폭을 초과하면 데이터가 클라이언트로 신속하게 전송되지 않습니다. 서버에서 클라이언트로 데이터를 충분히 빠르게 푸시할 수 없어서 클라이언트 요청이 시간 초과될 수 있습니다.

"Cache Read" 및 "Cache Write" 메트릭을 사용하여 서버 쪽 대역폭의 사용량을 확인할 수 있습니다. 포털을 사용하여 [이러한 메트릭을 볼](cache-how-to-monitor.md#view-metrics-with-azure-monitor-metrics-explorer) 수 있습니다.

네트워크 대역폭 사용량이 최대 용량에 근접하는 상황을 완화하려면 다음을 수행합니다.

- 네트워크 수요를 줄이기 위해 클라이언트 호출 동작을 변경합니다.
- 캐시 읽기 또는 캐시 쓰기와 같은 메트릭에 잠재적 영향을 초기에 알리도록 [경고를 만듭니다](cache-how-to-monitor.md#alerts).
- 더 많은 네트워크 대역폭 용량을 사용하여 더 큰 캐시 크기로 [조정합니다](cache-how-to-scale.md). 자세한 내용은 [Azure Cache for Redis 계획 FAQ](./cache-planning-faq.yml)를 참조하세요.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis 클라이언트 쪽 문제 해결](cache-troubleshoot-client.md)
- [올바른 계층 선택](cache-overview.md#choosing-the-right-tier)
- [내 캐시의 성능을 어떻게 벤치마크 및 테스트할 수 있나요?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
- [Azure Cache for Redis를 모니터링하는 방법](cache-how-to-monitor.md)
- [어떻게 Redis 명령을 실행할 수 있나요?](cache-development-faq.yml#how-can-i-run-redis-commands-)
