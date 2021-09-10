---
title: Azure Cache for Redis의 서버 로드 사용 및 모니터링 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis의 서버 로드를 사용하고 모니터링 하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: ceeff68f65ace76f4fe9060edeb775085508c969
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116371"
---
# <a name="manage-server-load-for-azure-cache-for-redis"></a>Azure Cache for Redis의 서버 로드 관리

## <a name="value-sizes"></a>값 크기

클라이언트 애플리케이션의 디자인에 따라 많은 작은 값을 저장해야 하는지 또는 더 적은 수의 큰 값을 저장해야 하는지 여부가 결정됩니다. Redis 서버 관점에서는 값이 작을수록 성능이 향상됩니다. 값 크기를 100kB 보다 작게 유지하는 것이 좋습니다.

디자인 상 Azure Cache for Redis에 더 큰 값을 저장해야 하는 경우 서버 로드가 높아집니다. 이 경우 CPU 사용량이 처리량을 제한하지 않도록 높은 캐시 계층을 사용해야 할 수 있습니다.

캐시에 CPU 용량이 충분한 경우에도 값이 클수록 대기 시간이 증가하므로 [적절한 시간 제한 구성](cache-best-practices-connection.md#configure-appropriate-timeouts)의 참고 자료를 따르세요.

값이 클수록 메모리 조각화의 가능성이 증가하므로 [maxmemory-reserved 설정 구성](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)의 참고 자료를 따라야 합니다.

## <a name="avoid-client-connection-spikes"></a>클라이언트 연결 급증 방지

Redis 서버에 대한 연결을 만들고 닫는 작업은 많은 비용을 필요로 합니다. 클라이언트 애플리케이션이 짧은 시간에 너무 많은 연결을 만들거나 닫으면 Redis 서버에 부담을 줄 수 있습니다.

여러 클라이언트 인스턴스를 인스턴스화하여 한 번에 Redis에 연결하는 경우에는 연결된 클라이언트 수가 급증하지 않도록 새 연결 만들기에 시차를 두는 것을 고려하십시오.

## <a name="memory-pressure"></a>메모리 부족

서버에서 메모리 사용량이 높으면 시스템에서 데이터를 디스크로 페이징해야 하므로 페이지 폴트가 발생하여 시스템의 속도가 상당히 느려질 수 있습니다.

## <a name="avoid-long-running-commands"></a>장기 실행 명령 방지

Redis 서버는 단일 스레드 시스템입니다. 장기 실행 명령을 실행하는 동안에는 서버에서 다른 요청에 응답할 수 없으므로 클라이언트 측에서 대기 시간이나 시간 초과가 발생할 수 있습니다. 더 자세한 정보는 [Azure Cache for Redis 서버측 문제 해결](cache-troubleshoot-server.md)을 참조하세요.  

## <a name="monitor-server-load"></a>모니터 서버 로드

서버 로드 모니터링을 추가하여 높은 서버 로드가 발생하는 경우 알림을 받을 수 있도록 합니다. 모니터링을 통해 애플리케이션의 제약 조건을 이해할 수 있습니다. 그 후 문제를 완화하기 위해 사전에 자동 관리할 수 있습니다. 성능 저하를 방지하려면 서버 로드를 80% 미만으로 유지하는 것이 좋습니다.

## <a name="plan-for-server-maintenance"></a>서버 유지 관리 계획

캐시 서버가 유지 관리 중인 동안 최대 로드를 처리할 수 있는 충분한 서버 용량이 있는지 확인 합니다. 최대 로드 상태에서 노드를 다시 부팅하여 시스템을 테스트 합니다. 패치의 배포를 시뮬레이션 하는 방법에 대한 자세한 정보는 [재부팅](cache-administration.md#reboot)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Cache for Redis 서버 쪽 문제 해결](cache-troubleshoot-server.md)
- [연결 복원력](cache-best-practices-connection.md)
  - [적절한 시간 제한을 구성합니다](cache-best-practices-connection.md#configure-appropriate-timeouts).
- [메모리 관리](cache-best-practices-memory-management.md)
  - [사용자의 maxmemory-reserved 설정 구성](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
