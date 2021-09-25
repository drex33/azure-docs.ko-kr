---
title: 메모리 관리 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis 메모리를 효과적으로 관리하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 36fe87e03a78a4dee34c2016b8f4723cb8aa95be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598730"
---
# <a name="memory-management"></a>메모리 관리

## <a name="eviction-policy"></a>제거 정책

애플리케이션에 대해 작동하는 [제거 정책](https://redis.io/topics/lru-cache)을 선택합니다. Azure Cache for Redis에 대한 기본 정책은 `volatile-lru`로, TTL 값 설정이 있는 키만 제거 대상이 될 수 있습니다.  키에 TTL 값이 없으면 시스템에서 키를 제거하지 않습니다.  메모리가 부족할 때 시스템에서 키를 제거할 수 있게 하려는 경우 `allkeys-lru` 정책을 고려할 수 있습니다.

## <a name="keys-expiration"></a>키 만료

키에 만료 값을 설정합니다. 만료는 메모리가 부족할 때까지 기다리지 않고 사전에 키를 제거합니다.  메모리 부족으로 인해 제거가 시작되면 서버에 추가 부하가 발생합니다. 자세한 내용은 [EXPIRE](https://redis.io/commands/expire) 및 [EXPIREAT](https://redis.io/commands/expireat) 명령에 대한 설명서를 참조하세요.

## <a name="minimize-memory-fragmentation"></a>메모리 조각화 최소화

값이 크면 제거 시 메모리가 조각화되어 메모리 사용량과 서버 로드가 높아질 수 있습니다.

## <a name="monitor-memory-usage"></a>메모리 사용량 모니터링

메모리 사용량에 대한 모니터링을 추가하여 메모리 부족을 방지하고 문제를 보기 전에 캐시의 크기를 조정할 수 있습니다.

## <a name="configure-your-maxmemory-reserved-setting"></a>maxmemory-reserved 설정 구성

시스템 응답성을 개선하려면 [maxmemory-reserved 설정](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)을 구성합니다.

* 쓰기 작업이 많은 워크로드나, 100KB 이상을 캐시에 저장하는 경우에는 충분한 예약 설정이 특히 중요합니다. 캐시 크기를 10%로 시작하고 쓰기 작업이 많은 로드의 경우 이 비율을 높입니다.

*  `maxmemory-reserved` 설정은 장애 조치(failover) 중 복제와 같은 비캐시 작업을 위해 예약되는 메모리의 양을 클러스터의 인스턴스당 MB 단위로 구성합니다. 이 값을 설정하면 부하가 달라져도 Redis 서버 환경이 더 일관되도록 할 수 있습니다. 많은 양의 데이터를 쓰는 워크로드의 경우이 값을 더 높게 설정해야 합니다. 이러한 작업을 위해 메모리가 예약된 경우 캐시된 데이터의 스토리지에는 사용할 수 없습니다.

*  `maxfragmentationmemory-reserved` 설정은 메모리 조각화를 고려하여 예약된 메모리 양을 클러스터의 인스턴스당 MB 단위로 구성합니다. 이 값을 설정하면 캐시가 가득 차거나 거의 가득 차고 조각화 비율이 높을 때 Redis 서버 환경이 더 일관됩니다. 이러한 작업을 위해 메모리가 예약된 경우 캐시된 데이터의 스토리지에는 사용할 수 없습니다.

* 새 메모리 예약 값(`maxmemory-reserved` 또는 `maxfragmentationmemory-reserved`)을 선택할 때 고려해야 할 한 가지 사항은 이 변경이 대용량 데이터와 함께 이미 실행 중인 캐시에 미치는 영향입니다. 예를 들어 49GB의 데이터가 있는 53GB 캐시가 있는 경우 예약 값을 8GB로 변경하면 시스템에 사용 가능한 최대 메모리가 45GB로 줄어듭니다. 현재 `used_memory` 또는 `used_memory_rss` 값이 새 제한인 45GB보다 높으면 시스템은  `used_memory` 및 `used_memory_rss` 가 모두 45GB 미만이 될 때까지 데이터를 제거해야 합니다. 제거는 서버 부하 및 메모리 조각화를 증가시킬 수 있습니다.  `used_memory` 및 `used_memory_rss`와 같은 캐시 메트릭에 대한 자세한 내용은  [사용 가능한 메트릭 및 보고 간격](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [개발을 위한 모범 사례](cache-best-practices-development.md)
* [Azure Cache for Redis 개발 FAQ](cache-development-faq.yml)
* [maxmemory-예약 된 설정](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)
* [크기 조정에 대 한 모범 사례](cache-best-practices-scale.md)
