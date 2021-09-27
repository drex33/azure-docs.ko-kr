---
title: 새로운 기능은 무엇입니까?
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance의 새로운 기능 및 설명서 개선 사항에 대해 알아봅니다.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: c73546c23a619f1d38caf10383097b4ded638581
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057287"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance의 새로운 기능은 무엇인가요?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에는 현재 공개 미리 보기로 제공되는 Azure SQL Database 및 Azure SQL Managed Instance 기능이 나열되어 있습니다. SQL Database 및 SQL Managed Instance 업데이트 및 개선 사항은 [SQL Database 및 SQL Managed Instance 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조하세요. 다른 Azure 서비스에 대한 개선 사항 및 업데이트는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="whats-new"></a>새로운 기능

Azure SQL Database 및 Azure SQL Managed Instance에 대한 설명서는 별도의 섹션으로 분할되었습니다. 또한 *Azure SQL Database Managed Instance* 에서 관리되는 인스턴스를 참조하는 방법을 *Azure SQL Managed Instance* 로 업데이트했습니다.

단일 데이터베이스와 관리되는 인스턴스 간에 일부 기능이 크게 다르기 때문에 이 작업을 수행했으며 개별 공유 문서에서 Azure SQL Database와 Azure SQL Managed Instance 간의 복잡하고 미묘한 차이를 설명하는 것이 점점 더 어려워졌습니다.

서로 다른 Azure SQL 제품을 명확히 구분하려면 Azure의 SQL Server 데이터베이스 엔진을 사용하는 프로세스를 간소화해야 합니다. 여기에는 Azure SQL Database의 단일 관리되는 데이터베이스, Azure SQL Managed Instance에서 여러 데이터베이스를 호스트하는 완전히 관리되는 인스턴스 또는 Azure의 가상 머신에서 호스트되는 친숙한 온-프레미스 SQL Server 제품이 있습니다.

이 작업은 진행 중인 작업이며 모든 문서가 아직 업데이트되지 않은 것을 고려해야 합니다. 예를 들어 T-SQL(Transact-SQL) 문, 저장 프로시저 및 Azure SQL Database와 Azure SQL Managed Instance 간에 공유되는 많은 기능에 대한 설명서가 아직 완료되지 않았으므로 콘텐츠를 계속 명확히 작성하는 동안 기다려 주시기 바랍니다. 

이 표는 용어 변경에 대한 빠른 비교를 제공합니다. 


|**새 용어**  | **이전 용어**  |**설명** |
|---------|---------|---------|
|**Azure SQL Managed Instance** | Azure SQL Database *Managed Instance*| Azure SQL Managed Instance는 Azure SQL Database 내의 배포 옵션이 아니라 Azure SQL 제품군 내의 자체 제품입니다. | 
|**Azure SQL Database**|Azure SQL Database *단일 데이터베이스*| 별도로 명시적으로 지정하지 않는 한 Azure SQL Database 제품 이름에는 단일 데이터베이스와 탄력적 풀에 배포된 데이터베이스가 모두 포함됩니다. |
|**Azure SQL Database**|Azure SQL Database *탄력적 풀*| 별도로 명시적으로 지정하지 않는 한 Azure SQL Database 제품 이름에는 단일 데이터베이스와 탄력적 풀에 배포된 데이터베이스가 모두 포함됩니다.  |
|**Azure SQL Database** |Azure SQL Database | 용어는 동일하게 유지되지만 이제는 단일 데이터베이스 및 탄력적 풀 배포에만 적용되며 관리되는 인스턴스는 포함되지 않습니다. |
| **Azure SQL**| 해당 없음 | 이는 Azure에서 사용할 수 있는 SQL Server 데이터베이스 엔진 제품군인 Azure SQL Database, Azure SQL Managed Instance 및 Azure VM의 SQL Server를 나타냅니다. | 

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 탄력적 데이터베이스 작업 | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리](elastic-jobs-overview.md)를 참조하세요. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요](elastic-query-overview.md)를 참조하세요. |
| 탄력적 트랜잭션 | 자세한 내용은 [클라우드 데이터베이스의 분산 트랜잭션](elastic-transactions-overview.md)을 참조하세요. |
| Azure Portal의 쿼리 편집기 |자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용하여 데이터 연결 및 쿼리](connect-query-portal.md)를 참조하세요.|
| SQL 분석|자세한 내용은 [Azure SQL 분석](../../azure-monitor/insights/azure-sql.md)을 참조하세요.|
| 쿼리 저장소 힌트 | 자세한 내용은 [쿼리 저장소 힌트](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true)를 참조하세요.|
| ---| --- |

### <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| [SQL Managed Instance 범용에 대한 16TB 지원](https://techcommunity.microsoft.com/t5/azure-sql/increased-storage-limit-to-16-tb-for-sql-managed-instance/ba-p/2421443) | SQL Managed Instance 범용에서 최대 16TB 공간 할당을 지원합니다. |
| [Azure SQL에 대한 Azure Active Directory 전용 인증](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) | Azure SQL Managed Instance의 Azure Active Directory 전용 인증 퍼블릭 미리 보기입니다. |
| [Log Replay Service를 사용한 마이그레이션](../managed-instance/log-replay-service-migrate.md) | Log Replay Service를 사용하여 SQL Server의 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. |
| [유지 관리 기간](./maintenance-window.md)| 유지 관리 기간 기능을 사용하여 유지 관리 일정을 구성할 수 있습니다. |
| [Azure SQL Managed Instance에 대한 Service Broker 인스턴스 간 메시지 교환](https://azure.microsoft.com/updates/service-broker-message-exchange-for-azure-sql-managed-instance-in-public-preview/) | Azure SQL Managed Instance에서 인스턴스 간 메시지 교환을 지원합니다. |
| [Azure SQL Managed Instance에 대한 장기 백업 보존](https://azure.microsoft.com/updates/longterm-backup-retention-ltr-for-azure-sql-managed-instance-in-public-preview/) | Azure SQL Managed Instance에서 최대 10년 동안 장기 백업 보존을 지원합니다. |
| [Azure SQL Managed Instance에 대한 Azure Monitor SQL 인사이트](https://azure.microsoft.com/updates/azure-monitor-sql-insights-for-azure-sql-in-public-preview/) | 퍼블릭 미리 보기로 제공되는 Azure SQL Managed Instance에 대한 Azure Monitor SQL 인사이트입니다. |
| [분산 트랜잭션](./elastic-transactions-overview.md) | 관리되는 인스턴스 간의 분산 트랜잭션 |
| [인스턴스 풀](../managed-instance/instance-pools-overview.md) | 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리하고 비용 효율적인 방법입니다. |
| [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) | 테이블의 변경 내용을 SQL Managed Instance, SQL Database 또는 SQL Server의 다른 데이터베이스로 복제합니다. 또는 SQL Managed Instance 또는 SQL Server의 다른 인스턴스에서 일부 행이 변경될 때 테이블을 업데이트합니다. 자세한 내용은 [Azure SQL Managed Instance에서 복제 구성](../managed-instance/replication-between-two-instances-configure-tutorial.md)을 참조하세요. |
| 위협 탐지 |자세한 내용은 [Azure SQL Managed Instance에서 위협 탐지 구성](../managed-instance/threat-detection-configure.md)을 참조하세요.|
| 쿼리 저장소 힌트 | 자세한 내용은 [쿼리 저장소 힌트](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true)를 참조하세요.|
| ---| --- |

---

## <a name="new-features"></a>새로운 기능

### <a name="sql-managed-instance-h1-2021-updates"></a>SQL Managed Instance H1 2021 업데이트

- [SQL Managed Instance 범용에 대한 16TB 지원 퍼블릭 미리 보기](https://techcommunity.microsoft.com/t5/azure-sql/increased-storage-limit-to-16-tb-for-sql-managed-instance/ba-p/2421443) - SQL Managed Instance 범용에서 최대 16TB 공간 할당을 지원합니다(퍼블릭 미리 보기).
- [SQL Managed Instance 범용의 성능 향상을 위한 병렬 백업](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762) - SQL Managed Instance 범용에서 더 빠른 백업을 지원합니다.
- [Azure SQL에 대한 Azure Active Directory 전용 인증](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) - Azure SQL Managed Instance의 Azure Active Directory 전용 인증 퍼블릭 미리 보기입니다.
- [Resource Health를 사용하여 Azure SQL Managed Instance의 상태 모니터링](resource-health-to-troubleshoot-connectivity.md) - Azure SQL Managed Instance에서 Resource Health 모니터링을 지원합니다.
- [이제 Azure SQL Managed Instance에 대한 서비스 지원 서브넷 구성에서 사용자 정의 경로에 서비스 태그 사용](../managed-instance/connectivity-architecture-overview.md) - 사용자 정의 경로(UDR) 테이블을 지원합니다.
- [Log Replay Service를 사용하여 Managed Instance로 마이그레이션](../managed-instance/log-replay-service-migrate.md) - Log Replay Service(퍼블릭 미리 보기)를 사용하여 SQL Server의 데이터베이스를 SQL Managed Instance로 마이그레이션할 수 있습니다.
- [유지 관리 기간](./maintenance-window.md) - 유지 관리 기간 기능을 사용하여 유지 관리 일정을 구성할 수 있습니다. [유지 관리 기간 알림](https://techcommunity.microsoft.com/t5/azure-sql/maintenance-window-for-azure-sql-database-and-managed-instance/ba-p/2174835)(퍼블릭 미리 보기)을 참조하세요.
- [Azure SQL Managed Instance의 Machine Learning Services 출시](https://azure.microsoft.com/updates/machine-learning-services-on-azure-sql-managed-instance-now-generally-available/) - Azure SQL Managed Instance의 Machine Learning Services가 출시되었습니다.
- [Azure SQL Managed Instance에 대한 Service Broker 인스턴스 간 메시지 교환](https://azure.microsoft.com/updates/service-broker-message-exchange-for-azure-sql-managed-instance-in-public-preview/) - 인스턴스 간 메시지 교환을 지원합니다.
- [Azure SQL Managed Instance에 대한 장기 백업 보존](https://azure.microsoft.com/updates/longterm-backup-retention-ltr-for-azure-sql-managed-instance-in-public-preview/) - Azure SQL Managed Instance에서 최대 10년 동안 장기 백업 보존을 지원합니다.
- [Azure SQL Managed Instance에 대한 동적 데이터 마스킹 세분화된 권한](dynamic-data-masking-overview.md) - Azure SQL Managed Instance에 대한 동적 데이터 마스킹 세분화된 권한이 출시되었습니다. 
- [Microsoft 작업에 대한 Azure SQL Managed Instance 감사](https://azure.microsoft.com/updates/azure-sql-auditing-of-microsoft-operations-is-now-generally-available/) - Microsoft 작업에 대한 Azure SQL Managed Instance 감사가 출시되었습니다.
- [Azure SQL Managed Instance에 대한 Azure Monitor SQL 인사이트](https://azure.microsoft.com/updates/azure-monitor-sql-insights-for-azure-sql-in-public-preview/) - 퍼블릭 미리 보기로 제공되는 Azure SQL Managed Instance에 대한 Azure Monitor SQL 인사이트입니다.

### <a name="sql-managed-instance-h2-2020-updates"></a>SQL Managed Instance H2 2020 업데이트

- [퍼블릭 미리 보기: Azure SQL DB와 Azure SQL MI에서 Microsoft 지원 작업 감사](https://azure.microsoft.com/updates/auditing-of-microsoft-support-operations-in-azure-sql-db-and-azure-sql-mi/) - Microsoft 지원 작업 감사 기능을 사용하면 감사 로그 대상에 대한 지원 요청 중에 서버 및/또는 데이터베이스에 액세스해야 하는 경우 Microsoft 지원 작업을 감사할 수 있습니다(퍼블릭 미리 보기).
- [Azure SQL Managed Instances에 걸친 분산 데이터베이스 트랜잭션](https://azure.microsoft.com/updates/distributed-database-transactions-spanning-multiple-azure-sql-managed-instances/) - Azure SQL Managed Instances에 걸친 분산 데이터베이스 트랜잭션이 추가되어 기존 애플리케이션의 원활한 마이그레이션과 수직 또는 수평 분할된 데이터베이스 아키텍처를 사용하는 최신 다중 테넌트 애플리케이션 개발이 가능합니다(퍼블릭 미리 보기).
- [Azure SQL Managed Instance에 대한 구성 가능한 백업 스토리지 중복성 옵션](https://azure.microsoft.com/updates/configurable-backup-storage-redundancy-option-for-azure-sql-managed-instance-2/) - LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지) 옵션이 백업 스토리지 중복성에 추가되어 더 많은 유연성과 선택 사항을 제공합니다. 
- [Azure SQL Database 및 Managed Instance에 대한 백업 스토리지 비용 절감](https://azure.microsoft.com/updates/backup-storage-cost-savings-for-azure-sql-database-and-managed-instance/) - 사용자가 TDE(투명한 데이터 암호화)를 사용하는 데이터베이스에 대해 PITR 백업 보존 기간과 백업 자동 압축을 설정할 수 있으므로 이제 백업 스토리지 공간이 최대 30% 더 효율적으로 사용됩니다.
- [Azure SQL MI에 대한 Azure AD 인증 기능](https://azure.microsoft.com/updates/azure-ad-authentication-features-for-azure-sql-db-azure-synapse-analytics-and-azure-sql-managed-instance/) - 이 기능을 사용하면 Azure AD 애플리케이션을 통해 사용자 생성을 자동화하고, SQL Managed Instance에서 개별 Azure AD 게스트 사용자를 만들 수 있습니다(퍼블릭 미리 보기).
- [Azure SQL Managed Instance에 대한 글로벌 가상 네트워크 피어링 지원](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)
- [Azure SQL Managed Instance에서 지원되는 모든 SSRS 버전의 카탈로그 데이터베이스 호스팅](https://azure.microsoft.com/updates/hosting-catalog-databases-for-all-supported-versions-of-ssrs-in-azure-sql-managed-instance/) - Azure SQL Managed Instance는 지원되는 모든 SSRS(SQL Server Reporting Services) 버전의 카탈로그 데이터베이스를 호스트할 수 있습니다.
- [Azure SQL Database Managed Instance의 주요 성능 개선 사항](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256) 
- [Azure SQL Managed Instance에 대한 향상된 관리 환경](https://azure.microsoft.com/updates/enhanced-management-experience-for-azure-sql-managed-instance/)
- [미리 보기로 제공되는 Azure SQL Managed Instance의 Machine Learning](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256) - 이제 Azure SQL Managed Instance에서 R 및 Python 언어를 지원하는 Machine Learning Services 미리 보기가 지원됩니다(퍼블릭 미리 보기).
- [Azure SQL Managed Instance의 애플리케이션 오류 복원력을 위해 사용자가 시작하는 장애 조치(failover)](https://azure.microsoft.com/updates/userinitiated-failover-for-application-fault-resiliency-in-azure-sql-managed-instance-is-now-generally-available/) - 이제 사용자가 시작하는 장애 조치(failover)가 출시되어 PowerShell, CLI 명령, API 호출을 사용하여 자동 장애 조치(failover)를 수동으로 시작하는 기능을 제공합니다.


### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance H2 2019 업데이트

- [서비스 보조 서브넷 구성](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) 데이터 트래픽을 제어하는 서브넷 구성을 관리하는 안전하고 편리한 방법이며, SQL Managed Instance가 중단 없는 관리 트래픽 흐름을 보장합니다.
- [BYOK(Bring Your Own Key)를 통한 TDE(투명한 데이터 암호화)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/)를 사용하면 미사용 데이터 보호를 위한 BYOK(Bring-Your-Own-Key) 시나리오가 가능하고 조직에서 키와 데이터에 대한 관리 업무를 분리할 수 있습니다.
- [자동 장애 조치(failover) 그룹](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)을 사용하면 주 인스턴스의 모든 데이터베이스를 다른 지역의 보조 인스턴스로 복제할 수 있습니다.
- [글로벌 추적 플래그](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)를 사용하면 SQL Managed Instance 동작을 구성할 수 있습니다.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance H1 2019 업데이트

다음 기능은 H1 2019의 SQL Managed Instance 배포 모델에서 사용할 수 있습니다.
  - <a href="/azure/azure-sql/managed-instance/resource-limits">Visual Studio 구독자를 위한 Azure 월간 크레딧</a>으로 구독이 가능하며 [지역별 한도](../managed-instance/resource-limits.md#regional-resource-limitations)가 증가되었습니다.
  - <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 및 SharePoint 2019</a>와 <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a>을 지원합니다.
  - <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">인스턴스 수준 데이터 정렬</a> 및 원하는 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">시간대</a>를 사용하여 관리되는 인스턴스를 만듭니다.
  - Managed Instance가 이제 [기본 제공 방화벽](../managed-instance/management-endpoint-verify-built-in-firewall.md)으로 보호됩니다.
  - SQL Managed Instance가 [퍼블릭 엔드포인트](../managed-instance/public-endpoint-configure.md), 네트워크 성능 향상을 위한 [프록시 무시](connectivity-architecture.md#connection-policy) 연결, <a href="https://aka.ms/four-cores-sql-mi-update">Gen5 하드웨어 세대 기반 vCore 4개</a>를 사용하도록 구성하거나 특정 시점 복원을 위한 <a href="/azure/azure-sql/database/automated-backups-overview">백업 보존을 최대 35일로 구성</a>합니다. [장기 백업 보존](long-term-retention-overview.md)(최대 10년)은 현재 공개 미리 보기로 제공됩니다.  
  - 새로운 기능을 통해 <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell을 사용하여 데이터베이스를 다른 데이터 센터에 지리적 복원하고</a>, [데이터베이스 이름을 바꾸고](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [가상 클러스터를 삭제](../managed-instance/virtual-cluster-delete.md)할 수 있습니다.
  - 기본 제공되는 새로운 [인스턴스 기여자 역할](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)을 통해 보안 원칙에 따른 SoD(업무 분리) 준수 및 엔터프라이즈 표준 준수가 가능합니다.
  - SQL Managed Instance는 다음 Azure Government 지역에 일반 공급되며(US Gov 텍사스, US Gov 애리조나) 중국 북부 2와 중국 동부 2에서 사용할 수 있습니다. 다음 공용 지역에서도 사용할 수 있습니다. 오스트레일리아 중부, 오스트레일리아 중부 2, 브라질 남부, 프랑스 남부, 아랍에미리트 중부, 아랍에미리트 북부, 남아프리카 공화국 북부, 남아프리카 공화국 서부.

## <a name="known-issues"></a>알려진 문제

|문제  |발견된 날짜  |상태  |해결된 날짜  |
|---------|---------|---------|---------|
|[서비스 주체의 재작성을 제안 하 Azure Portal 오류 메시지가 잘못 되었습니다.](#misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal)|9 월 2021|||
|[연결 유형을 변경해도 장애 조치(failover) 그룹 엔드포인트를 통한 연결에는 영향을 주지 않습니다.](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|2021년 1월|해결 방법 있음||
|[ @query매개 변수를 사용할 때 sp_send_dbmail 프로시저가 일시적으로 실패할 수 있습니다.](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|2021년 1월|해결 방법 있음||
|[서버 신뢰 그룹에서 관리되는 인스턴스를 제거한 후 분산 트랜잭션을 실행할 수 있습니다.](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020년 10월|해결 방법 있음||
|[관리되는 인스턴스 확장 작업 후에 분산 트랜잭션을 실행할 수 없습니다.](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020년 10월|해결 방법 있음||
|[이전에 삭제한 논리 서버와 동일한 이름으로 SQL Managed Instance를 만들 수 없습니다.](#cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted)|2020년 8월|해결 방법 있음||
|Azure SQL의 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)/[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) 및 Managed Instance의 `BACKUP`/`RESTORE` 문은 Azure AD Manage ID를 사용하여 Azure 스토리지에 인증할 수 없습니다.|2020년 9월|해결 방법 있음||
|[서비스 주체는 Azure AD 및 AKV에 액세스할 수 없음](#service-principal-cannot-access-azure-ad-and-akv)|2020년 8월|해결 방법 있음||
|[CHECKSUM 없는 수동 백업 복원이 실패할 수 있음](#restoring-manual-backup-without-checksum-might-fail)|2020년 5월|해결됨|2020년 6월|
|[기존 작업을 수정하면 또는 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않음](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020년 5월|해결됨|2020년 6월|
|[리소스 그룹에 대한 권한이 SQL Managed Instance에 적용되지 않음](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020년 2월|해결됨|2020년 11월|
|[장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020년 1월|해결 방법 있음||
|[SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한 필요](#in-memory-oltp-memory-limits-are-not-applied)|2019년 12월|해결 방법 있음||
|[에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019년 12월|해결됨|2020년 3월|
|[Azure AD 로그인 및 사용자가 SSDT에서 지원되지 않음](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019년 11월|해결 방법 없음||
|[메모리 내 OLTP 메모리 제한이 적용되지 않음](#in-memory-oltp-memory-limits-are-not-applied)|2019년 10월|해결 방법 있음||
|[비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019년 10월|해결 방법 있음||
|[진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019년 9월|해결 방법 있음||
|[장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019년 9월|해결 방법 있음||
|[서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화를 다시 초기화해야 함](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019년 8월|해결 방법 있음||
|[Azure AD 로그인 유형의 가장이 지원되지 않음](#impersonation-of-azure-ad-login-types-is-not-supported)|2019년 7월|해결 방법 없음||
|[@query 매개 변수가 sp_send_db_mail에서 지원되지 않음](#-parameter-not-supported-in-sp_send_db_mail)|2019년 4월|해결됨|2021년 1월|
|[지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019년 3월|해결 방법 없음||
|[복원 작업 중에 임시 데이터베이스가 사용됨](#temporary-database-is-used-during-restore-operation)||해결 방법 있음||
|[TEMPDB 구조와 콘텐츠가 다시 생성됨](#tempdb-structure-and-content-is-re-created)||해결 방법 없음||
|[작은 데이터베이스 파일로 스토리지 공간 초과](#exceeding-storage-space-with-small-database-files)||해결 방법 있음||
|[데이터베이스 이름 대신 GUID 값이 표시됨](#guid-values-shown-instead-of-database-names)||해결 방법 있음||
|[오류 로그가 지속되지 않음](#error-logs-arent-persisted).||해결 방법 없음||
|[동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||해결 방법 있음|2020년 3월|
|[CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||해결 방법 있음||
|Azure Blob Storage에서 데이터베이스를 복원한 후 DBCC CHECKDB를 사용하여 데이터베이스 일관성을 확인하지 못함||해결됨|2019년 11월|
|원본 데이터베이스에 메모리 내 OLTP 개체가 포함되어 있으면 중요 비즈니스용 계층에서 범용 계층으로 특정 시점 데이터베이스 복원에 실패함||해결됨|2019년 10월|
|보안 연결을 사용하는 외부(비 Azure) 메일 서버와 데이터베이스 메일 기능||해결됨|2019년 10월|
|포함된 데이터베이스는 SQL Managed Instance에서 지원되지 않습니다.||해결됨|2019년 8월|

### <a name="misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal"></a>서비스 주체의 재작성을 제안 하 Azure Portal 오류 메시지가 잘못 되었습니다.

서비스 주체가 이미 존재 하는 경우에도 Azure SQL Managed Instance에 대 한 Azure Portal의 _Active Directory 관리자_ 블레이드가 다음 오류 메시지를 표시 하 고 있을 수 있습니다.

"Managed Instance Azure Active Directory에 액세스 하려면 서비스 주체가 필요 합니다. 서비스 주체를 만들려면 여기를 클릭 하십시오. "

관리 되는 인스턴스의 서비스 주체가 이미 존재 하는 경우이 오류 메시지를 무시 하 고 관리 되는 인스턴스의 AAD 인증을 사용할 수 있습니다. 

서비스 사용자가 있는지 여부를 확인 하려면 Azure Portal의 응용 프로그램 _Enterprise_ 페이지로 이동 하 고 _응용 프로그램 종류_ 드롭다운 목록에서 _관리 id_ 를 선택한 다음 _적용_ 을 클릭 하 고 검색 상자에 관리 되는 인스턴스의 이름을 입력 합니다. 인스턴스 이름이 결과 목록에 표시 되는 경우 서비스 주체는 이미 존재 하며 추가 작업이 필요 하지 않습니다.

오류 메시지의 지침을 이미 따르고 오류 메시지에서 링크를 클릭 한 경우 관리 되는 인스턴스의 서비스 사용자가 다시 생성 되었습니다. 이 경우 Azure AD 인증이 제대로 작동 하려면 새로 만든 서비스 사용자에 게 Azure AD 읽기 권한을 할당 하세요. 다음 [지침](./authentication-aad-configure.md?tabs=azure-powershell#powershell)에 따라 Azure PowerShell를 통해이 작업을 수행할 수 있습니다.

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>연결 유형을 변경해도 장애 조치(failover) 그룹 엔드포인트를 통한 연결에는 영향을 주지 않습니다.

인스턴스가 [자동 장애 조치(failover) 그룹](./auto-failover-group-overview.md)에 참여하는 경우 인스턴스의 [연결 유형](../managed-instance/connection-types-overview.md)을 변경해도 장애 조치(failover) 그룹 수신기 엔드포인트를 통해 설정된 연결에는 적용되지 않습니다.

**해결 방법**: 연결 유형을 변경한 후 자동 장애 조치(failover) 그룹을 삭제하고 다시 만듭니다.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>@query 매개 변수를 사용하면 sp_send_dbmail 프로시저가 일시적으로 실패할 수 있음

`@query` 매개 변수를 사용하면 `sp_send_dbmail` 프로시저가 일시적으로 실패할 수 있음 이 문제가 발생하면 sp_send_dbmail 프로시저의 두 번째 실행이 `Msg 22050, Level 16, State 1` 오류 및 `Failed to initialize sqlcmd library with error number -2147467259` 메시지와 함께 실패합니다. 이 오류를 제대로 확인하려면 `@exclude_query_output` 매개 변수에 대해 기본값 0으로 프로시저를 호출해야 합니다. 그러지 않으면 오류가 전파되지 않습니다.
이 문제는 `sp_send_dbmail`이 가장 및 연결 풀링을 사용하는 방법과 관련된 알려진 버그로 인해 발생합니다.
이 문제를 해결하려면 출력 매개 변수 `@mailitem_id`에 의존하는 다시 시도 논리로 이메일을 보내기 위한 랩 코드를 사용합니다. 실행이 실패하면 매개 변수 값이 NULL이 되어 이메일을 성공적으로 전송하기 위해 `sp_send_dbmail`을 한 번 더 호출해야 함을 나타냅니다. 다음은 이 다시 시도 논리의 예입니다.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>서버 트러스트 그룹에서 Managed Instance를 제거한 후 분산 트랜잭션을 실행할 수 있습니다.

[서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)은 [분산 트랜잭션](./elastic-transactions-overview.md)을 실행하기 위한 전제 조건인 관리되는 인스턴스 간에 신뢰를 설정하는 데 사용됩니다. 서버 트러스트 그룹에서 Managed Instance를 제거하거나 그룹을 삭제한 후에도 분산 트랜잭션을 실행할 수 있습니다. 분산 트랜잭션이 사용하지 않도록 설정되어 있고 Managed Instance에서 [사용자가 시작한 수동 장애 조치(failover) ](../managed-instance/user-initiated-failover.md)를 확인하기 위해 적용할 수 있는 해결 방법이 있습니다.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>관리되는 인스턴스 확장 작업 후 분산 트랜잭션을 실행할 수 없습니다.

서비스 계층 또는 vCore 수 변경을 포함하는 관리되는 인스턴스 확장 작업은 백 엔드에서 서버 신뢰 그룹 설정을 다시 설정하고 [분산 트랜잭션](./elastic-transactions-overview.md) 실행을 사용하지 않도록 설정합니다. 해결 방법으로 Azure Portal에서 새 [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)을 삭제하고 만듭니다.

### <a name="cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted"></a>이전에 삭제한 논리 서버와 동일한 이름으로 SQL Managed Instance를 만들 수 없습니다.

[논리 서버](./logical-servers.md)가 삭제된 후 이름이 레코드에서 해제되기까지 7일 임계 기간이 있습니다. 해당 기간에는 동일한 이름의 SQL Managed Instance를 만들 수 없습니다. 해결 방법으로, SQL Managed Instance에 다른 이름을 사용하거나 논리 서버 이름을 해제하기 위한 지원 티켓을 만들어야 합니다.

### <a name="bulk-insert-and-backuprestore-statements-should-use-sas-key-to-access-azure-storage"></a>BULK INSERT 및 BACKUP/RESTORE 문은 SAS 키를 사용하여 Azure Storage에 액세스해야 합니다.

현재 관리 ID와 함께 `DATABASE SCOPED CREDENTIAL` 구문을 사용하여 Azure Storage에 인증할 수 없습니다. 대량 삽입, `BACKUP` 및 `RESTORE` 문 또는 `OPENROWSET` 함수를 위해Azure Storage에 액세스할 때 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-credential-transact-sql#d-creating-a-credential-using-a-sas-token)에 [공유 액세스 서명](../../storage/common/storage-sas-overview.md)을 사용하는 것이 좋습니다. 예를 들면 다음과 같습니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL sas_cred WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/invoices', CREDENTIAL= sas_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

SAS 키와 함께 `BULK INSERT`를 사용하는 또 다른 예는 [스토리지 인증을 위한 공유 액세스 서명](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)을 참조하세요. 

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>서비스 주체는 Azure AD 및 AKV에 액세스할 수 없습니다.

경우에 따라 Azure AD 및 AKV(Azure Key Vault) 서비스에 액세스하는 데 사용되는 서비스 주체에 문제가 있을 수 있습니다. 따라서 이 문제는 SQL Managed Instance를 사용하여 Azure AD 인증 및 TDE(투명한 데이터베이스 암호화) 사용에 영향을 줍니다. 이는 일시적인 연결 문제 또는 다음과 같은 문을 실행할 수 없는 경우 발생할 수 있습니다. `CREATE LOGIN/USER FROM EXTERNAL PROVIDER` 또는 `EXECUTE AS LOGIN/USER`. 새 Azure SQL Managed Instance에서 고객 관리형 키로 TDE를 설정하는 것도 일부 상황에서 작동하지 않을 수 있습니다.

**해결 방법**: 업데이트 명령을 실행하기 전에 SQL Managed Instance에서 이 문제가 발생하지 않도록 하거나 업데이트 명령 후에 이미 이 문제가 발생한 경우 Azure Portal로 이동하여 SQL Managed Instance [Active Directory 관리 페이지](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal)에 액세스합니다. 다음 오류 메시지가 표시되는지 확인합니다. "Azure Active Directory에 액세스하려면 관리되는 인스턴스에 서비스 주체가 필요합니다. 서비스 주체를 만들려면 여기를 클릭하세요." 이 오류 메시지가 발생한 경우 해당 메시지를 클릭하고 이 오류가 해결될 때까지 제공된 단계별 지침을 따르세요.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM 없는 수동 백업 복원이 실패할 수 있음

특정 상황에서 CHECKSUM 없이 관리형 인스턴스에서 수행한 데이터베이스 수동 백업이 복원되지 않을 수 있습니다. 이러한 경우 성공할 때까지 백업 복원을 다시 시도하세요.

**해결 방법**: CHECKSUM을 사용하도록 설정한 상태로 관리형 인스턴스에서 데이터베이스 수동 백업을 수행합니다.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>기존 작업을 수정하면 또는 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않음

특정 상황에서 기존 작업을 수정하거나 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않을 수 있습니다. 이 문제는 감지되면 자동으로 완화되어 에이전트 프로세스가 다시 시작됩니다.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>리소스 그룹에 대한 권한이 SQL Managed Instance에 적용되지 않음

SQL Managed Instance 기여자 Azure 역할이 RG(리소스 그룹)에 적용되면 SQL Managed Instance에 적용되지 않으며 효과가 없습니다.

**해결 방법**: 사용자용 SQL Managed Instance 기여자 역할을 구독 수준에서 설정합니다.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항

장애 조치(failover) 그룹이 서로 다른 Azure 구독이나 리소스 그룹의 인스턴스에 걸쳐 있으면 장애 조치(failover) 그룹의 주 인스턴스에서 수동 장애 조치(failover)를 시작할 수 없습니다.

**해결 방법**: 지리적 보조 인스턴스에서 포털을 통해 장애 조치(failover)를 시작합니다.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한이 필요함

비 sysadmin 로그인이 [SQL 에이전트 고정 데이터베이스 역할](/sql/ssms/agent/sql-server-agent-fixed-database-roles)에 추가되어 있는 경우 해당 로그인이 작동하려면 master 데이터베이스의 세 개 저장 프로시저에 명시적인 EXECUTE 권한을 부여해야 하는 문제가 있습니다. 이런 문제가 발생하면 "개체 <object_name>에 대한 EXECUTE 권한이 거부되었습니다(Microsoft SQL Server, 오류: 229)" 오류 메시지가 표시됩니다.

**해결 방법**: SQL 에이전트 고정 데이터베이스 역할인 SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole에 로그인을 추가하면 이러한 역할에 추가된 각 로그인에 대해 아래 T-SQL 스크립트를 실행하여 나열된 저장 프로시저에 EXECUTE 권한을 명시적으로 부여합니다.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음

**(2020년 3월에 해결됨)** SQL 에이전트는 작업이 시작될 때마다 새 세션을 생성하여 메모리 사용이 점차적으로 증가합니다. 예약된 작업 실행이 차단되는 내부 메모리 제한에 도달하지 않도록 메모리 사용량이 임계값에 도달하면 에이전트 프로세스가 다시 시작됩니다. 이로 인해 다시 시작될 때 실행 중인 작업이 중단될 수 있습니다.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>메모리 내 OLTP 메모리 제한이 적용되지 않음

중요 비즈니스용 서비스 계층이 [메모리 최적화 개체에 대한 최대 메모리 제한](../managed-instance/resource-limits.md#in-memory-oltp-available-space)을 올바르게 적용하지 않는 경우가 있습니다. SQL Managed Instance를 사용하면 워크로드가 메모리 내 OLTP 작업에 메모리를 더 많이 사용할 수 있기 때문에 인스턴스의 가용성과 안정성에 영향을 줄 수 있습니다. 한도에 도달한 메모리 내 OLTP 쿼리는 즉시 실패하지 않을 수도 있습니다. 이 문제는 곧 해결될 예정입니다. 메모리 내 OLTP 메모리를 더 많이 사용하는 쿼리는 [한도](../managed-instance/resource-limits.md#in-memory-oltp-available-space)에 도달하면 더 빨리 실패합니다.

**해결 방법:** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring)를 사용하여 [메모리 내 OLTP 스토리지 사용량을 모니터링](../in-memory-oltp-monitor-space.md)하여 워크로드가 사용 가능한 메모리보다 많은 메모리를 사용하지 않도록 합니다. vCore 수에 따라 메모리 한도를 늘리거나 메모리를 덜 사용하도록 워크로드를 최적화합니다.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨

SQL Server 및 SQL Managed Instance에서는 [사용자가 비어 있지 않은 파일을 삭제할 수 없습니다](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). `ALTER DATABASE REMOVE FILE` 문을 사용하여 비어 있지 않은 데이터 파일을 삭제하려고 하면 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 오류가 즉시 반환되지 않습니다. SQL Managed Instance에서 파일을 삭제하려고 계속 시도하며, 30분이 지나면 `Internal server error`로 인해 작업이 실패합니다.

**해결 방법**: `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 명령을 사용하여 파일의 콘텐츠를 제거합니다. 파일 그룹에 연결된 테이블 또는 파티션에서 데이터를 삭제해야 하는 이 파일 그룹의 유일한 파일인 경우에는 파일을 축소하기 전에 필요에 따라 해당 데이터를 다른 테이블/파티션에 로드합니다.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨

`RESTORE` 문, Data Migration Service 마이그레이션 프로세스, 기본 제공 특정 시점 복원을 실행 중이면 복원 프로세스가 완료될 때까지 서비스 계층 업데이트 또는 기존 인스턴스 크기 조정 및 새 인스턴스 만들기가 차단됩니다. 

복원 프로세스는 복원 프로세스를 실행 중인 동일한 서브넷의 Managed Instance 및 인스턴스 풀에서 이러한 작업을 차단합니다. 인스턴스 풀의 인스턴스는 영향을 받지 않습니다. 서비스 계층 생성 또는 변경 작업이 실패하거나 시간 초과되지 않고 복원 프로세스가 완료되거나 취소되면 계속 진행됩니다.

**해결 방법**: 복원 프로세스가 완료될 때까지 기다리거나, 서비스 계층 만들기 또는 업데이트 작업의 우선 순위가 더 높으면 복원 프로세스를 취소합니다.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음

사용자 워크로드에 할당된 리소스를 제한할 수 있는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 기능이 장애 조치(failover) 후에 또는 사용자가 시작한 서비스 계층 변경(예: 최대 vCore 변경 또는 최대 인스턴스 스토리지 크기 변경) 후에 일부 사용자 워크로드를 잘못 분류할 수 있습니다.

**해결 방법**: `ALTER RESOURCE GOVERNOR RECONFIGURE`를 정기적으로 실행하거나 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)를 사용하는 경우에는 인스턴스가 시작될 때 SQL 작업을 실행하는 SQL 에이전트 작업의 일부로 실행합니다.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화를 다시 초기화해야 함

데이터베이스 간 Service Broker dialog는 서비스 계층 변경 작업 후에 다른 데이터베이스의 서비스로 메시지를 배달하지 않습니다. 메시지는 *손실되지 않으며* 보낸 사람 큐에서 찾을 수 있습니다. SQL Managed Instance에서 vCore 또는 인스턴스 스토리지 크기를 변경하면 모든 데이터베이스에 대해 [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 뷰의 `service_broke_guid` 값이 변경됩니다. 다른 데이터베이스의 Service Broker를 참조하는 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 문을 사용하여 생성된 `DIALOG`가 있으면 대상 서비스에 메시지 배달이 중지됩니다.

**해결 방법**: 서비스 계층을 업데이트하기 전에 데이터베이스 간 Service Broker 대화를 사용하는 작업을 중지했다가 다시 초기화합니다. 서비스 계층 변경 후 배달되지 않은 메시지가 남아 있으면 소스 큐에서 메시지를 읽어서 대상 큐로 다시 보냅니다.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD 로그인 유형의 가장이 지원되지 않음

다음 Azure AD(Azure Active Directory) 주체의 `EXECUTE AS USER` 또는 `EXECUTE AS LOGIN`을 사용한 가장은 지원되지 않습니다.
-   별칭이 지정된 Azure AD 사용자. 이 경우 다음과 같은 오류가 반환됩니다. `15517`.
- Azure AD 애플리케이션 또는 서비스 주체를 기반으로 하는 Azure AD 로그인 및 사용자. 이 경우 다음과 같은 오류가 반환됩니다. `15517` 및 `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query 매개 변수가 sp_send_db_mail에서 지원되지 않음

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저의 `@query` 매개 변수가 작동하지 않습니다.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함

자동 장애 조치(failover) 그룹의 데이터베이스에서 트랜잭션 복제를 사용하도록 설정된 경우에는 다른 지역으로 장애 조치(failover)가 발생한 후 SQL Managed Instance 관리자가 모든 게시를 이전 주 데이터베이스에서 정리하고 새로운 주 데이터베이스에서 다시 구성해야 합니다. 자세한 내용은 [복제](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)를 참조하세요.

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD 로그인 및 사용자가 SSDT에서 지원되지 않음

SQL Server Data Tools는 Azure AD 로그인 및 사용자를 완전히 지원하지 않습니다.

### <a name="temporary-database-is-used-during-restore-operation"></a>복원 작업 중에 임시 데이터베이스가 사용됨

데이터베이스가 SQL Managed Instance에서 복원되는 경우 복원 서비스는 먼저 원하는 이름으로 빈 데이터베이스를 만들고 인스턴스에 이름을 할당합니다. 잠시 후 이 데이터베이스는 삭제되고 실제 데이터베이스 복원이 시작됩니다. 

*복원 중* 상태인 데이터베이스는 임시로 이름 대신 무작위 GUID 값을 갖습니다. 임시 이름은 복원 프로세스가 끝나면 `RESTORE` 문에 지정된 원하는 이름으로 변경됩니다. 

초기 단계에서 사용자는 빈 데이터베이스에 액세스할 수 있고 이 데이터베이스에 테이블을 만들거나 데이터를 로드할 수도 있습니다. 임시 데이터베이스는 복원 서비스가 두 번째 단계를 시작하면 삭제됩니다.

**해결 방법**: 복원이 완료된 것을 볼 때까지 복원 중인 데이터베이스에 액세스하지 마십시오.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조와 콘텐츠가 다시 생성됨

`tempdb` 데이터베이스는 항상 12개의 데이터 파일로 분할되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기는 변경할 수 없으며, `tempdb`에 새 파일을 추가할 수 없습니다. `Tempdb`는 인스턴스가 시작되거나 장애 조치(failover)될 때 항상 빈 데이터베이스로 다시 생성되며 `tempdb`에서 변경한 내용은 유지되지 않습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, `RESTORE DATABASE` 문은 인스턴스가 Azure Storage 제한에 도달할 수 있기 때문에 실패할 수 있습니다.

각 범용 SQL Managed Instance의 인스턴스에는 최대 35TB의 스토리지가 Azure 프리미엄 디스크 공간용으로 예약되어 있습니다. 각 데이터베이스 파일은 별도의 실제 디스크에 배치됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크에서 사용되지 않은 공간은 요금이 부과되지 않지만 Azure 프리미엄 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요하지 않은 관리형 인스턴스는 내부 조각화로 인해 스토리지 크기에 대한 35TB Azure 제한을 초과할 수 있습니다.

예를 들어 SQL Managed Instance의 범용 인스턴스에는 4TB 디스크에 크기가 1.2TB인 대용량 파일이 하나 있을 수 있습니다. 또한 각각 1GB이고 별도의 128GB 디스크에 있는 248개의 파일이 있을 수 있습니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예는 특정 상황에서 특정 파일 배포로 인해 SQL Managed Instance의 인스턴스가 연결되지 않은 Azure 프리미엄 디스크에 예약되어 있는 35TB 한도에 도달할 수 있음을 보여 줍니다.

이 예에서 기존 데이터베이스는 계속 작동하며, 새 파일이 추가되지 않는 한 문제 없이 커질 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브에 대한 충분한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환되는 오류가 명확하지 않습니다.

시스템 뷰를 사용하여 [남은 파일 수를 식별](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)할 수 있습니다. 이 제한에 도달하면 [DBCC SHRINKFILE 문을 사용하여 작은 파일 중 일부를 비우고 삭제](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)해보거나 [이런 제한이 없는 중요 비즈니스용 계층](../managed-instance/resource-limits.md#service-tier-characteristics)으로 전환합니다.

### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 GUID 값이 표시됨

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체되기 때문에 사용하지 마세요.

**해결 방법**: `sys.databases` 뷰를 사용하여 실제 데이터베이스 이름에서 GUID 데이터베이스 식별자 형식으로 지정된 실제 데이터베이스 이름을 확인합니다.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4;
```

### <a name="error-logs-arent-persisted"></a>오류 로그가 지속되지 않음

SQL Managed Instance에서 사용할 수 있는 오류 로그는 유지되지 않으며, 해당 크기는 최대 스토리지 한도에 포함되지 않습니다. 장애 조치(failover)가 발생하면 오류 로그가 자동으로 지워질 수 있습니다. 여러 가상 머신에서 SQL Managed Instance가 여러 번 이동했기 때문에 오류 로그 기록에 차이가 있을 수 있습니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음

**(2020년 3월에 해결됨)** 동일한 트랜잭션 범위의 동일한 인스턴스 내에서 데이터베이스 두 개에 쿼리 두 개를 보내면 .NET의 `TransactionScope` 클래스가 작동하지 않습니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**해결 방법(2020년 3월 이후 필요하지 않음):** [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)를 사용하여 두 연결을 사용하는 대신 연결 컨텍스트에서 다른 데이터베이스를 사용합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음

SQL Managed Instance 및 연결된 서버 또는 현재 인스턴스를 참조하는 분산 쿼리의 CLR 모듈에서 로컬 인스턴스의 IP를 확인할 수 없는 경우가 있습니다. 이 오류는 일시적인 문제입니다.

**해결 방법**: 가능한 경우 CLR 모듈에서 컨텍스트 연결을 사용합니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조하세요.

모든 Azure 서비스에 대한 개선 사항 및 업데이트는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
