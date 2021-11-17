---
title: 모니터링 및 성능 튜닝
description: Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝 기능과 방법론에 대한 개요입니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: AlainDormehlMSFT
ms.author: aldorme
ms.reviewer: mathoma, urmilano, wiassaf
ms.date: 03/17/2021
ms.openlocfilehash: 842093f7cf74416e4bfb7b8ee3ed1faf83818675
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546991"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 데이터베이스의 성능을 모니터링하려면 먼저 특정 서비스 계층 및 성능 수준을 선택할 때 선택한 데이터베이스 성능 수준을 기준으로 워크로드에서 사용하는 CPU 및 IO 리소스를 모니터링합니다. 이를 수행하기 위해 Azure SQL Database 및 Azure SQL Managed Instance는 Azure Portal에서 볼 수 있거나 이러한 SQL Server 관리 도구 [Azure Data Studio](/sql/azure-data-studio/what-is) 또는 SSMS([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)) 중 하나를 사용하여 볼 수 있는 리소스 메트릭을 내보냅니다.

Azure SQL Database는 지능형 성능 튜닝 권장 사항 및 성능 향상을 위한 자동 튜닝 옵션을 제공하기 위한 다양한 데이터베이스 관리자를 제공합니다. 또한 Query Performance Insight에는 단일 및 풀링된 데이터베이스에 대한 가장 많은 CPU 및 IO 사용을 담당하는 쿼리에 대한 세부 정보가 표시됩니다.

Azure SQL Database 및 Azure SQL Managed Instance는 인공 지능으로 지원되는 고급 모니터링 및 튜닝 기능을 제공하여 데이터베이스 및 솔루션의 성능을 극대화하고 문제를 해결하는 데 도움을 줍니다. 특히 [SQL Analytics](../../azure-monitor/insights/azure-sql.md)를 사용하여 소비 및 분석을 위해 이러한 [Intelligent Insights](intelligent-insights-overview.md) 및 기타 데이터베이스 리소스 로그 및 메트릭의 [스트리밍 내보내기](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)를 여러 대상 중 하나로 구성하도록 선택할 수 있습니다. Azure SQL Analytics는 모든 데이터베이스의 성능을 대규모로 단일 보기에서 여러 구독에 걸쳐 모니터링하기 위한 고급 클라우드 모니터링 솔루션입니다. 내보낼 수 있는 로그 및 메트릭 목록은 [내보내기를 위한 진단 원격 분석](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)을 참조하세요.

SQL Server에는 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 및 [DMV(동적 관리 뷰)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)와 같이 SQL Database 및 SQL Managed Instance가 활용하는 자체 모니터링 및 진단 기능이 있습니다. 다양한 성능 문제를 모니터링하는 스크립트는 [DMV를 사용한 모니터링](monitoring-with-dmvs.md)을 참조하세요.

> [!div class="nextstepaction"]
> [Azure SQL 개선하기 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure Portal의 모니터링 및 튜닝 기능

Azure Portal에서 Azure SQL Database 및 Azure SQL Managed Instance는 리소스 메트릭 모니터링을 제공합니다. Azure SQL Database는 데이터베이스 관리자를 제공하고 Query Performance Insight는 쿼리 튜닝 권장 사항 및 쿼리 성능 분석을 제공합니다. Azure Portal에서 [논리 SQL Server](logical-servers.md)와 해당 단일 및 풀링된 데이터베이스에 대해 자동 튜닝을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 사용량이 매우 적은 데이터베이스는 실제 사용량보다 적은 양으로 포털에 표시될 수 있습니다. double 값을 가장 가까운 정수로 변환할 때 원격 분석을 내보내는 방식으로 인해 0.5 미만의 특정 사용량은 0으로 반올림되어 내보낸 원격 분석의 세분성이 손실됩니다. 자세한 내용은 [낮은 데이터베이스 및 탄력적 풀 메트릭을 0으로 반올림](#low-database-and-elastic-pool-metrics-rounding-to-zero)을 참조하세요.

### <a name="monitor-with-sql-insights"></a>SQL 인사이트로 모니터링

[Azure Monitor SQL 인사이트](../../azure-monitor/insights/sql-insights-overview.md)는 Azure SQL VM에서 Azure SQL Managed Instance, Azure SQL Database 및 SQL Server 인스턴스를 모니터링하기 위한 도구입니다. 이 서비스는 원격 에이전트를 사용하여 DMV(동적 관리 뷰)에서 데이터를 캡처하고 데이터를 모니터링 및 분석할 수 있는 Azure Log Analytics로 라우팅합니다. 제공된 보기의 [Azure Monitor](../../azure-monitor/overview.md)에서 이 데이터를 보거나 로그 데이터에 직접 액세스하여 쿼리를 실행하고 추세를 분석할 수 있습니다. Azure Monitor SQL 인사이트 사용을 시작하려면 [SQL 인사이트 사용](../../azure-monitor/insights/sql-insights-enable.md)을 참조하세요.

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database 및 Azure SQL Managed Instance 리소스 모니터링

**메트릭** 보기의 Azure Portal에서 다양한 리소스 메트릭을 빠르게 모니터링할 수 있습니다. 이러한 메트릭을 통해 데이터베이스가 프로세서, 메모리 또는 IO 리소스의 100%에 도달하는지 확인할 수 있습니다. 높은 DTU 또는 프로세서 백분율과 높은 IO 백분율은 워크로드에 더 많은 CPU 또는 IO 리소스가 필요할 수 있음을 나타냅니다. 최적화해야 하는 쿼리를 나타낼 수도 있습니다.

  ![리소스 메트릭](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스 관리자

Azure SQL Database에는 단일 및 풀링된 데이터베이스에 대한 성능 튜닝 권장 사항을 제공하는 [데이터베이스 관리자](database-advisor-implement-performance-recommendations.md)가 포함되어 있습니다. 이러한 권장 사항은 [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor)을 사용하는 것뿐만 아니라 Azure Portal에서도 사용할 수 있습니다. Azure SQL Database가 이러한 튜닝 권장 사항을 자동으로 구현할 수 있도록 [자동 튜닝](automatic-tuning-overview.md)을 사용하도록 설정할 수도 있습니다.

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL Database에서 Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md)는 단일 및 풀링된 데이터베이스에 대해 가장 많이 소비되고 가장 오래 실행되는 쿼리의 Azure Portal 성능을 보여 줍니다.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>낮은 데이터베이스 및 탄력적 풀 메트릭이 0으로 반올림됩니다.

2020년 9월부터 사용량이 매우 적은 데이터베이스가 포털에서 실제 사용량보다 적게 표시될 수 있습니다. double 값을 가장 가까운 정수로 변환할 때 원격 분석을 내보내는 방식으로 인해 0.5 미만의 특정 사용량은 0으로 반올림되어 내보낸 원격 분석의 세분성이 손실됩니다.

예: 0.1, 0.1, 0.1, 0.1의 네 가지 데이터 포인트가 있는 1분 기간을 고려합니다. 이러한 낮은 값은 0, 0, 0, 0으로 내림되고 평균 0을 나타냅니다. 데이터 포인트가 0.5보다 큰 경우(예: 0.1, 0.1, 0.9, 0.1) 0, 0, 1, 0으로 반올림되고 평균 0.25가 표시됩니다.

영향을 받는 데이터베이스 메트릭:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

영향을 받는 탄력적 풀 메트릭:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>성능 문제에 대한 지능형 평가 생성

Azure SQL Database 및 Azure SQL Managed Instance에 대한 [Intelligent Insights](intelligent-insights-overview.md)는 기본 제공 인텔리전스를 사용하여 지속적으로 인공 지능을 통해 데이터베이스 사용량을 모니터링하고 성능 저하의 원인이 되는 중단 이벤트를 검색합니다. Intelligent Insights는 쿼리 실행 대기 시간, 오류, 시간 제한을 기준으로 데이터베이스의 성능 문제를 자동으로 감지합니다. 일단 감지되면 [문제에 대한 지능적인 평가](intelligent-insights-troubleshoot-performance.md)와 함께 리소스 로그(SQLInsights라고 함)를 만드는 자세한 분석이 수행됩니다. 이 평가는 데이터베이스 성능 문제의 근본 원인 분석과 성능 개선을 위한 가능한 권장 사항이 있는 위치로 구성되어 있습니다.

Intelligent Insights는 다음 값을 제공하는 Azure의 기본 제공 인텔리전스의 고유 기능입니다.

- 사전 모니터링
- 맞춤형 성능 정보
- 데이터베이스 성능 저하의 초기 검색
- 검색된 문제의 근본 원인 분석
- 성능 개선 권장 사항
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>메트릭 및 리소스 로그의 스트리밍 내보내기를 사용하도록 설정

Intelligent Insights 리소스 로그를 포함하여 여러 대상 중 하나로 [진단 원격 분석의 스트리밍 내보내기](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)를 사용하고 구성할 수 있습니다. [SQL Analytics](../../azure-monitor/insights/azure-sql.md) 및 기타 기능을 사용하여 이 추가 진단 원격 분석을 사용하여 성능 문제를 식별하고 해결합니다.

단일 데이터베이스, 풀링된 데이터베이스, 탄력적 풀, 관리되는 인스턴스 및 인스턴스 데이터베이스를 위한 메트릭 및 리소스 로그 범주를 다음 Azure 리소스 중 하나로 스트림하도록 진단 설정을 구성합니다.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역

[Azure Monitor에서 Log Analytics 작업 영역](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)으로 메트릭 및 리소스 로그를 스트리밍할 수 있습니다. 여기에서 스트리밍된 데이터는 성능 보고서와 경고 및 완화 권장 사항을 포함하는 데이터베이스에 대한 지능형 모니터링을 제공하는 클라우드 전용 모니터링 솔루션인 [SQL Analytics](../../azure-monitor/insights/azure-sql.md)에서 사용할 수 있습니다. Log Analytics 작업 영역으로 스트리밍된 데이터는 수집된 다른 모니터링 데이터로 분석할 수 있으며 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수도 있습니다.

### <a name="azure-event-hubs"></a>Azure Event Hubs

메트릭 및 리소스 로그를 [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)로 스트리밍할 수 있습니다. 진단 원격 분석을 이벤트 허브로 스트리밍하여 다음 기능을 제공합니다.

- **타사 로깅 및 원격 분석 시스템에 로그 스트리밍**

  모든 메트릭 및 리소스 로그를 단일 이벤트 허브로 스트리밍하여 로그 데이터를 타사 SIEM 또는 Log Analytics 도구로 파이프합니다.
- **사용자 지정 원격 분석 또는 로깅 플랫폼 빌드**

  확장성이 뛰어난 이벤트 허브의 게시-구독 특성을 통해 메트릭 및 리소스 로그를 사용자 지정 원격 분석 플랫폼으로 유연하게 수집할 수 있습니다. 자세한 내용은 [Azure Event Hubs에서 글로벌 규모의 원격 측정 플랫폼 설계 및 크기 조정](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)을 참조하세요.
- **Power BI로 데이터를 스트림하여 서비스의 상태 확인**

  Event Hubs, Stream Analytics 및 Power BI를 사용하여 진단 데이터를 Azure 서비스에 대한 거의 실시간 인사이트로 변환합니다. 이 솔루션에 대한 자세한 내용은 [Stream Analytics 및 Power BI: 스트리밍 데이터를 위한 실시간 분석 대시보드](../../stream-analytics/stream-analytics-power-bi-dashboard.md)를 참조하세요.

### <a name="azure-storage"></a>Azure Storage

메트릭 및 리소스 로그를 [Azure 스토리지](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)로 스트리밍합니다. Azure 스토리지를 사용하여 이전의 두 스트리밍 옵션에 대한 비용을 절감하기 위해 방대한 양의 진단 원격 분석을 보관합니다.

## <a name="use-extended-events"></a>확장 이벤트 사용 

또한 고급 모니터링 및 문제 해결을 위해 SQL Server에서 [확장 이벤트](/sql/relational-databases/extended-events/extended-events)를 사용할 수 있습니다. 확장 이벤트 아키텍처를 사용하면 성능 문제를 해결하거나 파악하는 데 필요한 만큼의 데이터를 수집할 수 있습니다. Azure SQL Database의 확장 이벤트 사용에 대한 자세한 내용은 [Azure SQL Database의 확장 이벤트](xevent-db-diff-from-svr.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스 및 풀링된 데이터베이스의 지능형 성능 권장 사항에 대한 자세한 내용은 [데이터베이스 어드바이저 성능 권장 사항](database-advisor-implement-performance-recommendations.md)을 참조하세요.
- 자동화된 진단을 사용하여 데이터베이스 성능을 자동으로 모니터링하는 방법 및 성능 문제의 근본 원인 분석에 대한 자세한 내용은 [Azure SQL Intelligent Insights](intelligent-insights-overview.md)를 참조하세요.