---
title: Azure Cache for Redis의 크기를 조정하는 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis의 크기를 조정하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 01fbebb06ba408232add1f0f05c5b715816e9e69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117074"
---
# <a name="scaling"></a>크기 조정

## <a name="scaling-under-load"></a>부하 시 크기 조정

로드 상태에서 캐시의 크기를 조정하는 동안 시스템 응답성을 개선하기 위해 maxmemory-reserved 설정을 구성합니다. 자세한 내용은 [maxmemory-reserved 설정 구성](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)을 참조하세요.

## <a name="scaling-clusters"></a>클러스터 크기 조정

클러스터형 캐시를 스케일 인하거나 스케일 아웃하기 전에 캐시에서 데이터를 최대한 줄이세요. 데이터를 줄이면 더 적은 양의 데이터를 이동해야 하므로 크기 조정 작업에 필요한 시간이 줄어듭니다. 크기 조정 시점에 대한 자세한 내용은 [크기 조정 시점](cache-how-to-scale.md#when-to-scale)을 참조하세요.

## <a name="scale-before-load-is-too-high"></a>부하가 너무 높기 전에 크기 조정

서버 부하나 메모리 사용량이 너무 높아지기 전에 크기 조정을 시작합니다. 너무 높으면 Redis 서버가 사용 중임을 의미합니다. 바쁜 Redis 서버에는 데이터의 크기를 조정하고 재배포할 리소스가 충분하지 않습니다.

## <a name="cache-sizes"></a>캐시 크기

TLS를 사용 중이고 연결 수가 많은 경우 더 많은 코어에 부하를 분산할 수 있도록 스케일 아웃을 고려합니다. 일부 캐시 크기는 4개 이상의 코어가 있는 VM에서 호스트됩니다.

TLS 암호화/암호 해독 및 TLS 연결/연결 끊기 워크로드를 여러 코어에 분산하여 캐시 VM의 전체 CPU 사용량을 줄입니다. 자세한 내용은 [VM 크기 및 코어에 대한 세부 정보](./cache-planning-faq.yml#azure-cache-for-redis-performance)를 참조하세요.
