---
title: 모니터링 모범 사례 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL을 모니터링하는 모범 사례에 대해 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 11/23/2020
ms.openlocfilehash: 351f4d0c6e2c920e22f1f21c2ef3f44add3cb145
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642149"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버 모니터링에 대한 모범 사례

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

데이터베이스 작업을 모니터링하고 데이터 크기가 증가함에 따라 성능이 손상되지 않도록 하는 데 사용할 수 있는 모범 사례에 대해 알아봅니다. 플랫폼에 새로운 기능을 추가하는 경우 이 섹션에서 자세히 설명된 모범 사례를 계속 구체화합니다.

## <a name="layout-of-the-current-monitoring-toolkit"></a>현재 모니터링 도구 키트의 레이아웃

Azure Database for MySQL은 사용을 쉽게 모니터링하고, 리소스(예: CPU, 메모리 또는 I/O)를 추가 또는 제거하고, 잠재적인 문제를 해결하고, 데이터베이스의 성능을 향상시키는 데 사용할 수 있는 도구와 방법을 제공합니다. 정기적으로 [성능 메트릭을 모니터링](concepts-monitoring.md#metrics)하여 다양한 시간 범위에 대한 평균, 최대값 및 최소값을 확인할 수 있습니다.

메트릭 임계값에 대한 [경고를 설정](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal)하면 서버가 해당 제한에 도달했는지 확인하고 적절한 조치를 취합니다.

데이터베이스 서버를 모니터링하여 데이터베이스에 할당된 리소스가 애플리케이션 워크로드를 처리할 수 있는지 확인합니다. 데이터베이스가 리소스 제한에 도달하는 경우

* 리소스를 많이 사용하는 상위 쿼리의 식별 및 최적화하고
* 서비스 계층을 업그레이드하여 리소스를 더 추가하는 것을 고려합니다.

### <a name="cpu-utilization"></a>CPU 사용률

CPU 사용량을 모니터링하고, 데이터베이스가 CPU 리소스를 소모하고 있는지 여부를 모니터링합니다. CPU 사용량이 90% 이상이면 vCores의 수를 늘리거나 다음 가격 책정 계층으로 확장하여 계산을 확장해야 합니다.  CPU를 확장/축소할 때 처리량 또는 동시성이 예상대로 작동하는지 확인합니다. 

### <a name="memory"></a>메모리

데이터베이스 서버에 사용할 수 있는 메모리 양은 [vCores 수](concepts-pricing-tiers.md)에 비례합니다. 메모리가 워크로드에 충분한지 확인합니다. 애플리케이션을 부하 테스트하여 읽기 및 쓰기 작업을 위한 메모리가 충분한지 확인합니다. 데이터베이스 메모리 사용량이 정의된 임계값을 초과하여 자주 증가하면 vCore 또는 고성능 계층을 늘려 인스턴스를 업그레이드해야 합니다. [쿼리 저장소](concepts-query-store.md), [쿼리 성능 권장 사항](concepts-performance-recommendations.md)을 사용하여 가장 긴 지속 시간, 가장 많이 실행된 쿼리를 식별합니다. 최적화할 수 있는 기회를 살펴보세요. 

### <a name="storage"></a>스토리지

MySQL 서버에 대해 프로비전된 [스토리지의 양](howto-create-manage-server-portal.md#scale-compute-and-storage)에 따라 서버에 대한 IOP가 결정됩니다. 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그, 서버 로그 및 백업 스냅샷이 포함됩니다. 사용된 디스크 공간이 총 프로비전된 디스크 공간의 85%를 지속적으로 초과하지 않도록 합니다. 이 경우에는 데이터베이스 서버에서 데이터를 삭제하거나 보관하여 공간을 확보해야 합니다. 

### <a name="network-traffic"></a>네트워크 트래픽

**네트워크 수신 처리량, 네트워크 전송 처리량** – MySQL 인스턴스와 주고받는 네트워크 트래픽의 속도(초당 메가바이트)입니다. 서버에 대한 처리량 요구 사항을 평가하고, 처리량이 예상보다 낮을 경우 트래픽을 지속적으로 모니터링해야 합니다. 

### <a name="database-connections"></a>데이터베이스 연결

**데이터베이스 연결** - Azure Database for MySQL에 연결된 클라이언트 세션 수는 [선택한 SKU 크기에 대한 연결 제한](concepts-server-parameters.md#max_connections)과 맞춰야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL의 성능에 대한 모범 사례](concept-performance-best-practices.md)
* [Azure Database for MySQL을 사용한 서버 작업에 대한 모범 사례](concept-operation-excellence-best-practices.md)
