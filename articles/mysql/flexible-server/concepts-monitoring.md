---
title: 모니터링 - Azure Database for MySQL - 유연한 서버
description: 이 문서에서는 CPU, 스토리지, 연결 통계를 포함하여 Azure Database for MySQL 유연한 서버를 모니터링하고 경고하는 메트릭을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 4cc2230856690477597d147a9600b22194823318
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442233"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>기본 제공 메트릭을 사용하여 Azure Database for MySQL 유연한 서버 모니터링

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버는 Azure Monitor를 통해 서버 모니터링을 제공합니다. 메트릭은 특정 시간에 서버 내 리소스의 일부 측면을 설명하는 숫자 값입니다. 서버 리소스를 모니터링하면 사용자에게 가장 중요한 것을 모니터링할 수 있도록 지원함으로써 워크로드의 문제를 해결하고 최적화하는 데 도움이 됩니다. 올바른 메트릭을 모니터링하면 서버 및 애플리케이션의 성능, 안정성 및 가용성을 유지하는 데 도움이 됩니다.

이 문서에서는 서버의 동작에 대한 인사이트를 제공하는 유연한 서버에 사용할 수 있는 다양한 메트릭에 대해 알아봅니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Database for MySQL 유연한 서버는 다양한 메트릭을 제공하여 워크로드의 성능을 파악하고, 사용자는 해당 데이터를 기반으로 서버 및 애플리케이션에 미치는 영향을 확인할 수 있습니다. 예를 들어 유연한 서버에서 **호스트 CPU 백분율**, **활성 연결**, **IO 백분율**, **호스트 메모리 백분율** 을 모니터링하여 성능에 영향을 미치는 상황을 식별할 수 있습니다. 여기에서 워크로드를 최적화하거나, 컴퓨팅 계층을 변경하여 수직으로 스케일링하거나, 읽기 복제본을 사용하여 수평으로 스케일링할 수 있습니다.

모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 단계별 지침은 [경고 설정 방법](./how-to-alert-on-metric.md)을 참조하세요. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../../azure-monitor/data-platform.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MySQL에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭 표시 이름|메트릭|단위|설명|
|---|---|---|---|
|호스트 CPU 백분율|cpu_percent|백분율|고객 워크로드와 Azure MySQL 프로세스의 CPU 사용률이 전부 포함된 서버의 CPU 사용 백분율|
|호스트 네트워크 입력 |network_bytes_ingress|바이트|고객 데이터베이스와 Azure MySQL 기능(예: 복제, 모니터링, 로그)의 트래픽을 전부 포함하여 서버에 들어오는 네트워크 트래픽|
|호스트 네트워크 출력|network_bytes_egress|바이트|고객 데이터베이스와 Azure MySQL 기능(예: 복제, 모니터링, 로그)의 트래픽을 전부 포함하여 서버에서 나가는 네트워크 트래픽|
|복제 지연|replication_lag|초|마지막으로 재생된 트랜잭션 이후 경과된 시간입니다. 이 메트릭은 복제본 서버에만 사용할 수 있습니다.|
|활성 연결 수|active_connection|개수|서버에 대한 활성 연결 수|
|사용된 백업 스토리지|backup_storage_used|바이트|사용된 백업 스토리지 양.|
|IO 백분율|io_consumption_percent|백분율|사용 중인 IO의 비율|
|호스트 메모리 백분율|memory_percent|백분율|고객 워크로드 및 Azure MySQL 프로세스의 메모리 사용률을 전부 포함하여 서버에서 사용 중인 메모리 백분율|
|스토리지 제한|storage_limit|바이트|이 서버의 최대 스토리지|
|스토리지 비율|storage_percent|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|사용된 스토리지|storage_used|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|총 연결|total_connections|개수|서버에 대해 실패한 연결 수|
|중단된 연결|aborted_connections|개수|MySQL에 대해 실패한 연결 시도(예: 잘못된 자격 증명으로 인한 연결 실패)의 횟수입니다.|
|쿼리|쿼리|개수|초당 쿼리 수|

## <a name="next-steps"></a>다음 단계
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](./how-to-alert-on-metric.md)을 참조하세요.
- 성능 향상을 위해 [IOPS 스케일링](./concepts/../concepts-compute-storage.md#iops)에 대해 자세히 알아보세요.