---
title: 연결 복원력을 위한 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis 연결을 복원력 있게 만드는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 11/3/2021
ms.author: shpathak
ms.openlocfilehash: d8e5f95e78db7c46ad1c52401b938acc37af6b4f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850948"
---
# <a name="connection-resilience"></a>연결 복원력

## <a name="retry-commands"></a>다시 시도 명령

지수 백오프로 명령을 다시 시도하도록 클라이언트 연결을 구성합니다. 자세한 내용은 [다시 시도 참고 자료](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis)를 참조하세요.

## <a name="test-resiliency"></a>복원력 테스트

[다시 부팅](cache-administration.md#reboot)을 사용하여 연결 중단에 대한 시스템의 복원력을 테스트하여 패치를 시뮬레이션합니다. 성능 테스트에 대한 자세한 내용은 [성능 테스트](cache-best-practices-performance.md)를 참조하세요.

## <a name="tcp-settings-for-linux-hosted-client-applications"></a>Linux에서 호스트 되는 클라이언트 응용 프로그램에 대 한 TCP 설정

일부 Linux 버전은 기본적으로 낙관적 TCP 설정을 사용 합니다. TCP 설정은 Redis 서버가 응답을 중지 하 고 연결을 정상적으로 종료 하는 동안 캐시에 대 한 클라이언트 연결을 다시 설정할 수 없는 상황을 만들 수 있습니다. Azure Cache For Redis의 주 노드를 사용할 수 없게 되 면 (예: 계획 되지 않은 유지 관리) 연결을 다시 설정 하지 못할 수 있습니다.

이러한 TCP 설정은 다음과 같이 설정 하는 것이 좋습니다.

|설정  |값 |
|---------|---------|
| *net.ipv4.tcp_retries2*   | 5 |

이 시나리오에 대 한 자세한 내용은 [Linux에서 실행 하는 경우 연결을 15 분 동안 다시 설정 하지 않음](https://github.com/StackExchange/StackExchange.Redis/issues/1848#issuecomment-913064646)을 참조 하세요. 이 문서에서는 Redis 라이브러리에 대해 설명 하지만, Linux에서 실행 되는 다른 클라이언트 라이브러리도 영향을 받습니다. 설명은 여전히 유용 하 고 다른 라이브러리로 일반화할 수 있습니다.

## <a name="using-forcereconnect-with-stackexchangeredis"></a>ForceReconnect와 함께 Redis 사용

드문 경우 지만 연결을 삭제 한 후 Redis가 다시 연결 되지 않습니다. 이러한 경우 클라이언트를 다시 시작 하거나 새를 만들면 `ConnectionMultiplexer` 문제가 해결 됩니다. 앱이 주기적으로 다시 연결 `ConnectionMultiplexer` 하도록 허용 하는 동시에 단일 패턴을 사용 하는 것이 좋습니다. 응용 프로그램에서 사용 하는 프레임 워크 및 플랫폼과 가장 일치 하는 빠른 시작 샘플 프로젝트를 살펴보세요. 빠른 시작에서이 코드 패턴의 예제를 볼 수 [있습니다.](https://github.com/Azure-Samples/azure-cache-redis-samples)

사용자는 `ConnectionMultiplexer` `ObjectDisposedException` 이전 항목을 삭제 한 결과로 발생할 수 있는 모든 오류를 처리 해야 합니다.

`ForceReconnectAsync()`및에 대해를 호출 `RedisConnectionExceptions` `RedisSocketExceptions` 합니다. 을 (를) `ForceReconnectAsync()` `RedisTimeoutExceptions` 사용 하 고 있는 경우에만를 호출할 수도 있습니다 `ReconnectMinInterval` `ReconnectErrorThreshold` . 그렇지 않으면 새 연결을 설정 하면 이미 오버 로드 되었기 때문에 시간이 초과 된 서버에서 cascade 오류가 발생할 수 있습니다.

## <a name="configure-appropriate-timeouts"></a>적절한 시간 제한 구성

연결 복원 력 및 [명령 제한](#command-timeout)시간에는 두 가지 시간 제한 값이 중요 [합니다.](#connect-timeout)

### <a name="connect-timeout"></a>Connect timeout

는 `connect timeout` 클라이언트가 Redis 서버와의 연결을 설정 하기 위해 대기 하는 시간입니다. 5 초를 사용 하도록 클라이언트 라이브러리를 구성 하 여 `connect timeout` 더 높은 CPU 상태 에서도 연결할 수 있는 충분 한 시간을 시스템에 제공 합니다.

`connection timeout`값이 작으면 해당 시간 프레임에 연결이 설정 되는 것을 보장 하지 않습니다. 문제가 발생 하는 경우 (높은 클라이언트 CPU, 높은 서버 CPU 등), short 값을 설정 하면 `connection timeout` 연결 시도가 실패 합니다. 이 동작으로 인해 상황이 더 나빠지기도 합니다. 시간 제한이 더 짧으면 도움이 되기보다 오히려 문제를 악화시킵니다. 시스템이 다시 연결을 시도하는 프로세스를 강제로 다시 시작하여 *연결 -> 실패 -> 다시 시도* 루프로 이어질 수 있습니다.

### <a name="command-timeout"></a>명령 시간 제한

대부분의 클라이언트 라이브러리에는에 대 한 또 다른 시간 제한 구성이 있습니다 `command timeouts` .이는 클라이언트가 Redis 서버에서 응답을 기다리는 시간입니다. 초기 설정은 5 초 미만 이지만 `command timeout` 사용자의 시나리오 및 캐시에 저장 된 값의 크기에 따라 더 높거나 낮게 설정 하는 것이 좋습니다.

`command timeout`가 너무 작으면 연결이 불안정 해질 수 있습니다. 그러나 `command timeout` 가 너무 클 경우 응용 프로그램에서 시간이 초과 되는지 여부를 확인 하는 데 오랜 시간이 걸릴 수 있습니다.

## <a name="avoid-client-connection-spikes"></a>클라이언트 연결 급증 방지

연결이 끊어진 후 다시 연결할 때 동시에 많은 연결을 만들지 마세요. [짧은 연결 시간 제한](#configure-appropriate-timeouts)이 더 긴 중단을 초래할 수 있는 것과 유사하게, 동시에 많은 다시 연결 시도를 시작하면 서버 부하가 증가하고 모든 클라이언트가 성공적으로 다시 연결하는 데 걸리는 시간이 늘어날 수 있습니다.

많은 클라이언트 인스턴스를 다시 연결하는 경우 연결된 클라이언트 수가 급증하지 않도록 새 연결에는 시차를 두는 것이 좋습니다.

> [!NOTE]
> `StackExchange.Redis` 클라이언트 라이브러리를 사용하는 경우 연결 문자열에서 `abortConnect`를 `false`로 설정합니다.  `ConnectionMultiplexer`에서 다시 연결을 처리하도록 하는 것이 좋습니다. 자세한 내용은 [StackExchange.Redis 모범 사례](./cache-management-faq.yml#stackexchangeredis-best-practices)를 참조하세요.

## <a name="avoid-leftover-connections"></a>연결 남기지 않기

캐시에는 캐시 계층당 클라이언트 연결 수에 대한 제한이 있습니다. 클라이언트 애플리케이션이 연결을 다시 만들 때 기존 연결을 닫고 제거하는지 확인합니다.

## <a name="advance-maintenance-notification"></a>고급 유지 관리 알림

알림을 사용하여 예정된 유지 관리에 대해 알아봅니다. 자세한 내용은 [계획된 유지 관리에 앞서 알림을 받을 수 있나요](cache-failover.md#can-i-be-notified-in-advance-of-planned-maintenance)를 참조하세요.

## <a name="schedule-maintenance-window"></a>유지 관리 기간 예약

유지 관리에 맞게 캐시 설정을 조정합니다. 캐시에 대한 부정적인 영향을 줄이기 위해 유지 관리 기간을 만드는 방법에 대한 자세한 내용은 [업데이트 예약](cache-administration.md#schedule-updates)을 참조하세요.

## <a name="more-design-patterns-for-resilience"></a>복원력을 위한 추가 디자인 패턴

복원력을 위한 디자인 패턴을 적용합니다. 자세한 내용은 [복원력 있는 애플리케이션을 만드는 방법](cache-failover.md#how-do-i-make-my-application-resilient)을 참조하세요.

## <a name="idle-timeout"></a>유휴 상태 시간 제한

Azure Cache for Redis에는 현재 연결에 대한 10분의 유휴 시간 제한이 있으므로 클라이언트 애플리케이션의 유휴 시간 제한 설정은 10분 미만이어야 합니다. 대부분의 일반적인 클라이언트 라이브러리에는 클라이언트 라이브러리가 Redis `PING` 명령을 Redis 서버에 자동으로 주기적으로 보낼 수 있도록 하는 구성 설정이 있습니다. 그러나 이러한 유형의 설정 없이 클라이언트 라이브러리를 사용하는 경우 고객 애플리케이션 자체에서 연결을 유지해야 합니다.

## <a name="next-steps"></a>다음 단계

- [개발을 위한 모범 사례](cache-best-practices-development.md)
- [Azure Cache for Redis 개발 FAQ](cache-development-faq.yml)
- [장애 조치(failover) 및 패치](cache-failover.md)