---
title: MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 최적화
description: 감사 및 활동 로그 외에도 Azure Metrics를 사용하여 서버 성능을 모니터링할 수도 있습니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 982001fa8150efaa1e5a268e7044f9b50694c767
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958623"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-optimization"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 최적화

### <a name="monitoring-hardware-and-query-performance"></a>하드웨어 및 쿼리 성능 모니터링

감사 및 활동 로그 외에도 [Azure Metrics](../../azure-monitor/essentials/data-platform-metrics.md)를 사용하여 서버 성능을 모니터링할 수도 있습니다. Azure 메트릭은 1분 간격으로 제공되며 경고를 구성할 수 있습니다. 모니터링할 수 있는 메트릭의 종류에 대한 자세한 내용은 [Azure Database for MySQL 모니터링](../concepts-monitoring.md)을 참조하세요.

앞서 언급했듯이 데이터베이스 계층 업그레이드를 결정할 때 cpu\_퍼센트 또는 메모리\_퍼센트와 같은 메트릭을 모니터링하는 것이 중요할 수 있습니다. 지속적으로 높은 값은 계층 업그레이드가 필요함을 나타낼 수 있습니다.

또한 CPU와 메모리가 문제가 아닌 것 같으면 관리자는 성능이 낮은 쿼리에 대한 인덱싱 및 쿼리 수정과 같은 데이터베이스 기반 옵션을 탐색할 수 있습니다.

성능이 낮은 쿼리를 찾으려면 다음을 실행하세요.

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s | top 5 by query\_time\_d desc
```

### <a name="query-performance-insight"></a>쿼리

기본 서버 모니터링 측면 외에도 Azure는 애플리케이션 쿼리 성능을 모니터링하는 도구를 제공합니다. 쿼리를 수정하거나 개선하면 쿼리 처리량이 크게 증가할 수 있습니다. [Query Performance Insight 도구](../concepts-query-performance-insight.md)를 사용하여 가장 오래 실행되는 쿼리를 분석하고 해당 항목이 정해진 기간 내에 결정적일 경우 캐시할 수 있는지 확인하거나 쿼리를 수정하여 성능을 높일 수 있습니다.

MySQL 로그 파일에 느린 쿼리를 표시하도록 `slow\_query\_log`를 설정할 수 있습니다(기본값은 OFF). `long\_query\_time` 서버 매개 변수는 긴 쿼리 시간(기본값은 10초)에 대해 사용자에게 알릴 수 있습니다.

### <a name="upgrading-the-tier"></a>계층 업그레이드

Azure Portal을 사용하여 `General Purpose`에서 `Memory Optimized` 사이로 크기를 조정할 수 있습니다. `Basic` 계층을 선택한 경우 나중에 계층을 `General Purpose` 또는 `Memory Optimized`로 업그레이드할 수 있는 옵션이 없습니다. 그러나 다른 기술을 활용하여 새로운 Azure Database for MySQL 인스턴스로 마이그레이션/업그레이드를 수행할 수 있습니다.

기본에서 다른 서버 계층으로 마이그레이션하는 스크립트의 예는 [Azure Database for MySQL의 기본 계층에서 범용 또는 메모리 최적화 계층으로 업그레이드](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)를 참조하세요.

### <a name="scale-the-server"></a>서버 크기 조정

계층 내에서 코어와 메모리를 해당 계층에서 허용되는 최소 및 최대 제한으로 크기 조정할 수 있습니다. 모니터링에서 CPU 또는 메모리가 지속적으로 최대화되는 것으로 표시되면 단계에 따라 [수요를 충족하도록 스케일 업합니다.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)

### <a name="moving-regions"></a>영역 이동

데이터베이스를 다른 Azure 지역으로 이동하는 것은 접근 방식과 아키텍처에 따라 다릅니다. 접근 방식에 따라 시스템 가동 중지 시간이 발생할 수 있습니다.

권장되는 프로세스는 유지 관리 장애 조치(failover)를 위해 읽기 복제본을 활용하는 것과 동일합니다. 그러나 위에서 언급한 계획된 유지 관리 방법에 비해 애플리케이션에 장애 조치(failover) 계층이 구현된 경우 장애 조치(failover) 속도가 훨씬 빠릅니다. 애플리케이션은 읽기 복제본 장애 조치(failover) 프로세스 중에 잠시 동안만 다운되어야 합니다. 자세한 내용은 비즈니스 연속성 및 재해 복구 섹션에서 다룹니다.

### <a name="wwi-scenario"></a>WWI 시나리오

WWI 비즈니스 및 애플리케이션 사용자는 주문형 데이터베이스 크기 조정 기능에 대해 높은 관심을 표했습니다. 또한 Query Performance Insight를 사용하여 장기 실행 쿼리 성능을 해결해야 하는지 여부를 결정하는 데 관심이 있었습니다.

잠재적인 장애 조치(failover) 또는 읽기 전용이 필요한 시나리오에 대해 읽기 복제본 서버를 활용하기로 결정했습니다.

Azure 엔지니어와 협력하는 마이그레이션 팀은 KQL 쿼리를 MySQL 서버 성능과 관련된 잠재적 문제를 모니터링하도록 설정합니다. KQY 쿼리는 데이터베이스 및 회의 팀과의 이메일 이벤트 문제에 대한 경고로 설정되었습니다.

현재 잠재적인 문제를 모니터링하고 필요한 경우 나중에 Azure Automation Runbook을 구현하여 운영 효율성을 개선하기로 결정했습니다.

### <a name="optimization-checklist"></a>최적화 검사 목록

  - 느린 쿼리를 모니터링합니다.

  - Performance Insight 대시보드를 주기적으로 검토합니다.

  - 모니터링을 활용하여 계층 업그레이드 및 크기 조정 결정을 추진합니다.

  - 사용자 또는 애플리케이션을 변경해야 하는 경우 영역 이동을 고려하세요.  


> [!div class="nextstepaction"]  
> [BCDR(비즈니스 연속성 및 재해 복구)](./business-continuity-and-disaster-recovery.md)