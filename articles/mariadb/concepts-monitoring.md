---
title: 모니터링 - Azure Database for MariaDB
description: 이 문서에서는 CPU, 스토리지, 연결 통계를 포함하여 Azure Database for MariaDB에 대해 모니터링 및 경고를 제공하기 위한 메트릭을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 36b3c29872c73122a0dc7913499f8a5a10df94e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065174"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 모니터링
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for MariaDB는 서버의 동작에 대한 인사이트를 제공하는 다양한 메트릭을 제공합니다.

## <a name="metrics"></a>메트릭
모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../azure-monitor/data-platform.md)를 참조하세요.

단계별 지침은 [How to set up alerts](howto-alert-metric.md)(경고를 설정하는 방법)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MariaDB에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|백분율|사용 중인 IO의 비율 (기본 계층 서버에는 적용되지 않음)|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|개수|서버에 대해 실패한 연결 수|
|seconds_behind_master|복제 지연 시간(초)|개수|원본 서버에 대해 복제본 서버가 지연되는 시간(초)입니다. (기본 계층 서버에는 적용되지 않음)|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|사용된 백업 스토리지|바이트|사용된 백업 스토리지 양. 이 메트릭은 서버에 설정된 백업 보존 기간에 따라 보존된 모든 전체 데이터베이스 백업, 차등 백업, 로그 백업에 사용된 스토리지의 합계를 나타냅니다. 백업 빈도는 서비스로 관리되며 [개념 문서](concepts-backup.md)에 설명되어 있습니다. 지리적 중복 스토리지의 경우 백업 스토리지 사용량은 로컬 중복 스토리지의 두 배입니다.|

## <a name="server-logs"></a>서버 로그

서버에 느린 쿼리 로그를 사용할 수 있습니다. 이러한 로그는 Azure Monitor 로그, Event Hubs 및 스토리지 계정의 Azure 진단 로그를 통해 사용할 수도 있습니다. 로깅에 대한 자세한 내용은 [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소

[쿼리 저장소](concepts-query-store.md)는 쿼리 런타임 통계 및 대기 이벤트를 포함하여 시간 경과에 따른 쿼리 성능을 추적합니다. 이 기능은 **mysql** 스키마에서 쿼리 런타임 성능 정보를 유지합니다. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. Query Performance Insight는 Azure Database for MariaDB 서버 포털 페이지의 **인텔리전트 성능** 섹션에서 액세스할 수 있습니다.

## <a name="planned-maintenance-notification"></a>계획된 유지 관리 알림

[계획된 유지 관리 알림](./concepts-planned-maintenance-notification.md)을 통해 예정된 유지 관리에 대한 경고를 Azure Database for MariaDB로 받을 수 있습니다. 이러한 알림은 [Service Health](../service-health/overview.md)의 계획된 유지 관리와 통합되며 알림을 통해 구독에 대해 예약된 모든 유지 관리를 한 곳에서 볼 수 있습니다. 또한 다양한 리소스를 담당하는 여러 연락처가 있을 수 있으므로 다양한 리소스 그룹에 대해 올바른 대상으로 알림을 확장하는 데 도움이 됩니다. 이벤트 72시간 전에 예정된 유지 관리에 대한 알림을 받게 됩니다.

[계획된 유지 관리 알림](./concepts-planned-maintenance-notification.md) 문서에서 알림을 설정하는 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../azure-monitor/data-platform.md)를 참조하세요.
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-metric.md)을 참조하세요.
- Azure Database for MariaDB의 [계획된 유지 관리 알림](./concepts-planned-maintenance-notification.md)에 대해 자세히 알아봅니다.