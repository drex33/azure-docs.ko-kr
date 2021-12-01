---
title: 콘텐츠 참조 구성 및 관리
description: Azure SQL Database를 구성하고 관리하는 데 도움이 되는 콘텐츠 참조를 찾습니다.
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: LitKnd
ms.author: kendralittle
ms.date: 01/14/2020
ms.openlocfilehash: 1b61fad9a46e54ca1583b36b0b3d75bc5c68ce15
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133362402"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>콘텐츠 참조 구성 및 관리 - Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 Azure SQL Database를 관리하고 구성하는 데 도움이 될 수 있는 다양한 가이드, 스크립트 및 설명에 대한 콘텐츠 참조를 찾을 수 있습니다. 

## <a name="load-data"></a>데이터 로드

- [SQL Database로 마이그레이션](migrate-to-database-from-sql-server.md)
- [마이그레이션 후 SQL Database 관리](manage-data-after-migrating-to-database.md)하는 방법을 알아봅니다.
- [데이터베이스 복사](database-copy.md)
- [BACPAC에서 DB 가져오기](database-import.md)
- [BACPAC로 DB 내보내기](database-export.md)
- [BCP를 사용하여 데이터 로드](../load-from-csv-with-bcp.md)
- [ADF를 사용하여 데이터 로드](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>기능 구성

- [Azure AD(Azure Active Directory) 인증 구성](authentication-aad-configure.md)
- [조건부 액세스 구성](conditional-access-configure.md)
- [Multi-Factor Azure AD 인증](authentication-mfa-ssms-overview.md)
- [다단계 인증 구성](authentication-mfa-ssms-configure.md)
- [임시 보존 정책 구성](temporal-tables-retention-policy.md)
- [BYOK를 사용하여 TDE 구성](transparent-data-encryption-byok-configure.md)
- [TDE BYOK 키 순환](transparent-data-encryption-byok-key-rotation.md)
- [TDE 보호기 제거](transparent-data-encryption-byok-remove-tde-protector.md)
- [메모리 내 OLTP 구성](../in-memory-oltp-configure.md)
- [Azure Automation 구성](automation-manage.md)
- 데이터베이스 간에 날짜를 복제하도록 [트랜잭션 복제를 구성](replication-to-sql-database.md)합니다.
- Azure SQL Database가 SQL 삽입과 같은 의심스러운 활동이나 의심스러운 위치에서의 액세스를 식별할 수 있도록 [위협 감지를 구성](threat-detection-configure.md)합니다.
- 중요한 데이터를 보호하도록 [동적 데이터 마스킹을 구성](dynamic-data-masking-configure-portal.md)합니다.
- Azure Blob Storage에 백업을 유지하도록 데이터베이스에 대해 [백업 보존을 구성](long-term-backup-retention-configure.md)합니다. 
- 데이터베이스의 복제본을 다른 지역에 유지하도록 [지역에서 복제를 구성](active-geo-replication-overview.md)합니다.
- [지역 복제본을 위한 보안 구성](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>데이터베이스 모니터링 및 튜닝

- [수동 조정](performance-guidance.md)
- [DMV를 사용하여 성능 모니터링](monitoring-with-dmvs.md)
- [쿼리 저장소를 사용하여 성능 모니터링](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [자동 튜닝을 사용하도록 설정](automatic-tuning-enable.md)하여 Azure SQL Database에서 워크로드의 성능을 최적화할 수 있도록 합니다.
- [자동 튜닝에 대해 이메일 알림을 사용하도록 설정](automatic-tuning-email-notifications-configure.md)하여 튜닝 권장 구성에 대한 정보를 받습니다.
- [성능 권장 사항을 적용](database-advisor-find-recommendations-portal.md)하고 데이터베이스를 최적화합니다.
- [경고를 만들어](alerts-insights-configure-portal.md) Azure SQL Database에서 알림을 받습니다.
- 애플리케이션과 데이터베이스 간에 연결 문제가 발생할 경우 [연결 문제를 해결](troubleshoot-common-errors-issues.md)합니다. [연결 문제에 대해 Resource Health를 사용](resource-health-to-troubleshoot-connectivity.md)할 수도 있습니다.
- [Intelligent Insights로 성능 문제 해결](intelligent-insights-troubleshoot-performance.md)
- [파일 공간을 관리](file-space-manage.md)하여 데이터베이스의 스토리지 사용량을 모니터링합니다.
- [Intelligent Insights 진단 로그 사용](intelligent-insights-use-diagnostics-log.md)
- [메모리 내 OLTP 공간 모니터링](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>확장 이벤트

- [확장 이벤트](xevent-db-diff-from-svr.md)
- [확장 이벤트를 이벤트 파일에 저장](xevent-code-event-file.md)
- [확장 이벤트를 링 버퍼에 저장](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>분산 데이터 쿼리

- 여러 데이터베이스에서 [수직 분할된 데이터를 쿼리](elastic-query-getting-started-vertical.md)합니다.
- [스케일 아웃된 데이터 계층에서 보고를 수행](elastic-query-horizontal-partitioning.md)합니다.
- [여러 스키마를 사용하여 테이블에서 쿼리](elastic-query-vertical-partitioning.md)합니다.

### <a name="data-sync"></a>데이터 동기화

- [SQL 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)
- [데이터 동기화 에이전트](sql-data-sync-agent-overview.md)
- [스키마 변경 내용 복제](sql-data-sync-update-sync-schema.md)
- [OMS로 모니터링](./monitor-tune-overview.md)
- [데이터 동기화의 모범 사례](sql-data-sync-best-practices.md)
- [데이터 동기화 문제 해결](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>탄력적 데이터베이스 작업

- PowerShell을 사용하여 Elastic Database 작업을 [만들고 관리](elastic-jobs-powershell-create.md)합니다.
- Transact-SQL을 사용하여 Elastic Database 작업을 [만들고 관리](elastic-jobs-tsql-create-manage.md)합니다.
- [오래된 탄력적 작업부터 마이그레이션](elastic-jobs-migrate.md)합니다.

## <a name="database-sharding"></a>분할된 데이터베이스

- [탄력적 데이터베이스 클라이언트 라이브러리를 업그레이드](elastic-scale-upgrade-client-library.md)합니다.
- [분할된 앱을 만듭니다](elastic-scale-get-started.md).
- [수평 분할된 데이터를 쿼리](elastic-query-getting-started.md)합니다.
- [다중 분할된 쿼리](elastic-scale-multishard-querying.md)를 실행합니다.
- [분할된 데이터를 이동](elastic-scale-configure-deploy-split-and-merge.md)합니다.
- 분할된 데이터베이스에서 [보안을 구성](elastic-scale-split-merge-security-configuration.md)합니다.
- 현재 분할된 데이터베이스 세트에 [분할된 데이터베이스를 추가](elastic-scale-add-a-shard.md)합니다.
- [분할된 맵 문제를 해결](elastic-database-recovery-manager.md)합니다.
- [분할된 DB 마이그레이션](elastic-convert-to-use-elastic-tools.md)합니다.
- [카운터를 만듭니다](elastic-database-perf-counters.md).
- [Entity Framework를 사용하여](elastic-scale-use-entity-framework-applications-visual-studio.md) 분할된 데이터를 쿼리합니다.
- [Dapper 프레임워크를 사용하여](elastic-scale-working-with-dapper.md) 분할된 데이터를 쿼리합니다.

## <a name="develop-applications"></a>애플리케이션 개발

- [연결](connect-query-content-reference-guide.md#libraries)
- [Spark 커넥터 사용](spark-connector.md)
- [앱 인증](application-authentication-get-client-id-keys.md)
- [성능 향상을 위해 일괄 처리 사용](../performance-improve-use-batching.md)
- [연결 지침](troubleshoot-common-connectivity-issues.md)
- [DNS 별칭](dns-alias-overview.md)
- [DNS 별칭 PowerShell 설정](dns-alias-powershell-create.md)
- [포트 - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C 및 C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>애플리케이션 디자인

- [재해 복구를 위한 디자인](designing-cloud-solutions-for-disaster-recovery.md)
- [탄력적 풀을 위한 디자인](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [앱 업그레이드를 위한 디자인](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>다중 테넌트 SaaS(software as a service) 애플리케이션 디자인

- [SaaS 디자인 패턴](saas-tenancy-app-design-patterns.md)
- [SaaS 비디오 사용](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 앱 보안](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>다음 단계

- [Azure SQL Managed Instance용 방법 가이드](../managed-instance/how-to-content-reference-guide.md)에 대해 자세히 알아보세요