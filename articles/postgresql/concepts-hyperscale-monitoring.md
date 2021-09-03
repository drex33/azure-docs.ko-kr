---
title: 모니터링 및 튜닝 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 모니터링 및 튜닝 기능을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/26/2021
ms.openlocfilehash: cb88998009ab05eb91b8945a138ef935660dac35
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710755"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) 모니터링 및 튜닝

서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. 하이퍼스케일(Citus)은 서버 그룹의 노드 동작에 대한 인사이트를 제공하는 다양한 모니터링 옵션을 제공합니다.

## <a name="metrics"></a>메트릭

하이퍼스케일(Citus)은 서버 그룹의 노드에 대한 메트릭과 그룹 전체에 대한 집계 메트릭을 제공합니다. 메트릭은 지원 리소스의 동작에 대한 인사이트를 제공합니다. 각 메트릭은 1분 빈도로 내보내지며, 최대 30일 동안 기록됩니다.

메트릭의 그래프를 보는 것 외에 경고를 구성할 수 있습니다. 단계별 지침은 [경고 설정 방법](howto-hyperscale-alert-on-metric.md)을 참조하세요.  다른 작업에는 자동화된 작업 설정, 고급 분석 실행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../azure-monitor/data-platform.md)를 참조하세요.

### <a name="per-node-vs-aggregate"></a>노드당 대 집계

기본적으로 Azure Portal은 서버 그룹의 노드에서 하이퍼스케일(Citus) 메트릭을 집계합니다. 그러나 디스크 사용량과 같은 일부 메트릭은 노드당 정보가 더 유용합니다. 노드에 대한 메트릭을 확인하려면 Azure Monitor [서버 이름별 메트릭 분할](../azure-monitor/essentials/metrics-charts.md#metric-splitting)을 사용합니다.

> [!NOTE]
>
> 일부 하이퍼스케일(Citus) 서버 그룹은 메트릭 분할을 지원하지 않습니다. 해당 서버 그룹에서 서버 그룹 **개요** 페이지의 노드 이름을 클릭하면 개별 노드에 대한 메트릭을 볼 수 있습니다. 노드에 대한 **메트릭** 페이지가 열립니다.

### <a name="list-of-metrics"></a>메트릭 목록

이러한 메트릭은 하이퍼스케일(Citus) 노드에 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|IOPS|IOPS|개수|[IOPS 정의](../virtual-machines/premium-storage-performance.md#iops) 및 [하이퍼스케일(Citus) 처리량](concepts-hyperscale-configuration-options.md) 참조|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|

Azure는 클러스터 전체의 집계 메트릭을 제공하지 않지만 여러 노드의 메트릭을 동일한 그래프에 배치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-hyperscale-alert-on-metric.md)을 참조하세요.
- [메트릭 분할](../azure-monitor/essentials/metrics-charts.md#metric-splitting)을 수행하여 서버 그룹의 노드당 메트릭을 검사하는 방법을 알아봅니다.
