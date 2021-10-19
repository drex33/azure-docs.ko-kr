---
title: 새로운 기능은 무엇입니까?
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 대한 새로운 기능 및 설명서 개선에 대해 알아봅니다.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 89adb496452a0aa0351c5ac1f544a415c7cf9efc
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178636"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance 새로운 내용
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 [Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance)최신 릴리스의 새로운 기능 및 향상된 기능과 관련된 설명서 변경 내용을 요약합니다. Azure SQL Managed Instance 대한 자세한 내용은 [개요를](sql-managed-instance-paas-overview.md)참조하세요.


Azure SQL Database 새로운 [을](../database/doc-changes-updates-release-notes-whats-new.md)참조하세요.


## <a name="preview"></a>미리 보기

다음 표에서는 현재 미리 보기로 있는 Azure SQL Managed Instance 기능을 나열합니다.


| 기능 | 세부 정보 |
| ---| --- |
| [SQL Managed Instance 범용에 대한 16TB 지원](resource-limits.md)| SQL Managed Instance 범용에서 최대 16TB 공간 할당을 지원합니다. |
| [Azure SQL 대한 Azure Active Directory 전용 인증](../database/authentication-azure-ad-only-authentication.md) |  이제 Azure SQL Managed Instance 대한 인증을 Azure Active Directory 사용자로만 제한할 수 있습니다. |
| [탄력적 트랜잭션](../database/elastic-transactions-overview.md) | 탄력적 트랜잭션을 사용하면 Azure SQL Database 및 Azure SQL Managed Instance 클라우드 데이터베이스에서 분산 트랜잭션을 실행할 수 있습니다. |
| [인스턴스 풀](instance-pools-overview.md) | 더 작은 SQL Server 인스턴스를 클라우드로 마이그레이션하는 편리하고 비용 효율적인 방법입니다. |
| [Log Replay Service를 사용한 마이그레이션](log-replay-service-migrate.md) | Log Replay Service를 사용하여 SQL Server의 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. |
| [유지 관리 기간](../database/maintenance-window.md)| 유지 관리 기간 기능을 사용하면 Azure SQL Managed Instance 대한 유지 관리 일정을 구성할 수 있습니다. |
| [장기 백업 보존](long-term-backup-retention-configure.md) | Azure SQL Managed Instance에서 최대 10년 동안 장기 백업 보존을 지원합니다. |
| [인스턴스 간 메시지 교환 Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | Azure SQL Managed Instance Service Broker 사용하여 인스턴스 간 메시지 교환을 지원합니다. |
| [SQL 인사이트](../../azure-monitor/insights/sql-insights-overview.md) | SQL 인사이트는 AZURE SQL 제품군의 모든 제품을 모니터링하는 포괄적인 솔루션입니다. SQL 인사이트는 동적 관리 뷰를 사용하여 상태를 모니터링하고 문제를 진단하고 성능을 조정하는 데 필요한 데이터를 공개합니다. |
| [트랜잭션 복제](replication-transactional-overview.md) | 테이블의 변경 내용을 SQL Managed Instance, SQL Database 또는 SQL Server의 다른 데이터베이스로 복제합니다. 또는 SQL Managed Instance 또는 SQL Server의 다른 인스턴스에서 일부 행이 변경될 때 테이블을 업데이트합니다. 자세한 내용은 [Azure SQL Managed Instance에서 복제 구성](replication-between-two-instances-configure-tutorial.md)을 참조하세요. |
| [위협 감지](threat-detection-configure.md) | 위협 탐지는 데이터베이스에 대해 검색된 보안 위협을 알리고, |
| [쿼리 저장소 힌트](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true) | 쿼리 힌트를 사용하여 OPTION 절을 통해 쿼리 실행을 최적화합니다. |
|||

## <a name="general-availability-ga"></a>GA(일반 공급)

다음 표에서는 지난 12개월 동안 미리 보기에서 GA(일반 공급)로 전환된 Azure SQL Managed Instance 기능을 나열합니다. 

| 기능 | GA 월 | 세부 정보 |
| ---| --- |--- |
| [Machine Learning Service](machine-learning-services-overview.md) | 2021년 3월 | Machine Learning Services는 Python 및 R 스크립트를 모두 지원하는 데이터베이스 내 기계 학습을 제공하는 Azure SQL Managed Instance의 기능입니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft Python 및 R 패키지가 포함됩니다. |
| [동적 데이터 마스킹에 대한 세분화된 권한](../database/dynamic-data-masking-overview.md)| 2021년 3월 | 동적 데이터 마스킹을 사용하면 고객이 애플리케이션 계층에 미치는 영향을 최소화하고 표시할 중요한 데이터의 양을 지정할 수 있게 하여 중요한 데이터에 대한 무닥 액세스를 방지할 수 있습니다. 데이터베이스의 데이터는 변경되지 않으면서 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이제 동적으로 마스킹된 데이터에 대해 세분화된 권한을 할당할 수 있습니다. 자세한 내용은 [동적 데이터 마스킹을 참조하세요.](../database/dynamic-data-masking-overview.md#permissions) |
| [관리 작업 감사](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  2021년 3월 | Azure SQL 감사 기능을 사용하면 지원 요청 중에 SQL 자산에 액세스해야 할 때 Microsoft 지원 엔지니어가 수행한 작업을 감사하여 직원의 투명성을 높일 수 있습니다. | 
||| 

## <a name="documentation-changes"></a>설명서 변경 내용

Azure SQL Managed Instance 설명서의 중요한 변경 내용에 대해 알아봅니다.

### <a name="october-2021"></a>2021년 10월

| 변경 | 세부 정보 |
| --- | --- |
|**새로운 내용 분할** | 이전에 결합된 **새로운** 기능 문서는 제품별로 분할되었습니다. [- SQL Database 새로운](../database/doc-changes-updates-release-notes-whats-new.md) 기능 및 SQL Managed Instance 새로운 [기능](doc-changes-updates-release-notes-whats-new.md)을 통해 현재 미리 보기로 제공되는 기능, 일반 제공 기능 및 중요한 설명서 변경 내용을 쉽게 식별할 수 있습니다. 또한 SQL Managed Instance [콘텐츠의 알려진 문제가](doc-changes-updates-known-issues.md) 자체 페이지로 이동되었습니다.  | 


### <a name="june-2021"></a>2021년 6월

| 변경 | 세부 정보 |
| --- | --- |
|**범용 대한 16 TB 지원** | SQL Managed Instance 범용 최대 16 TB의 공간을 할당하기 위한 지원이 추가되었습니다. 자세한 내용은 [리소스 제한을](resource-limits.md) 참조하세요. 이 인스턴스 제안은 현재 미리 보기로 제공됩니다. | 
| **병렬 백업** | 이제 범용 계층에서 SQL Managed Instance 위해 병렬로 백업을 사용할 수 있으므로 백업 속도가 빨라집니다. 자세한 내용은 [성능 향상을 위한 병렬 백업](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762) 블로그 항목을 참조하세요. |
| **Azure AD 전용 인증** | 이제 Azure SQL Managed Instance 대한 인증을 Azure Active Directory 사용자로만 제한할 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. 자세한 내용은 [Azure AD 전용 인증을](../database/authentication-azure-ad-only-authentication.md)참조하세요. | 
| **Resource Health 모니터** | Resource Health 사용하여 Azure SQL Managed Instance 상태를 모니터링합니다. 자세한 내용은 [리소스 상태를](../database/resource-health-to-troubleshoot-connectivity.md) 참조하세요. |
| **데이터 마스킹 GA에 대한 세분화된 권한** | 이제 Azure SQL Managed Instance 대한 동적 데이터 마스킹에 대한 세분화된 사용 권한이 GA(일반 제공)됩니다. 자세한 내용은 [동적 데이터 마스킹을 참조하세요.](../database/dynamic-data-masking-overview.md#permissions) | 
|  | |


### <a name="april-2021"></a>2021년 4월

| 변경 | 세부 정보 |
| --- | --- |
| **UDR(사용자 정의 경로) 테이블** | Azure SQL Managed Instance 대한 서비스 지원 서브넷 구성은 이제 UDR(사용자 정의 경로) 테이블에 서비스 태그를 사용합니다. 자세한 내용은 [연결 아키텍처를](connectivity-architecture-overview.md) 참조하세요. |
|  |  |


### <a name="march-2021"></a>2021년 3월

| 변경 | 세부 정보 |
| --- | --- |
| **관리 작업 감사** | SQL Managed Instance 작업을 감사하는 기능은 이제 GA(일반 제공)입니다. | 
| **로그 재생 서비스** | 이제 Log Replay Service를 사용하여 데이터베이스를 SQL Server Azure SQL Managed Instance 마이그레이션할 수 있습니다. 자세한 내용은 Log Replay Service를 사용하여 [마이그레이션을 참조하세요.](log-replay-service-migrate.md) 이 기능은 현재 미리 보기로 제공됩니다. | 
| **장기 백업 보존** | Azure SQL Managed Instance에서 최대 10년 동안 장기 백업 보존을 지원합니다. 자세한 내용은 [장기 백업 보존을 참조하세요.](long-term-backup-retention-configure.md)|
| **Machine Learning 서비스 GA** | Azure SQL Managed Instance Machine Learning Services는 이제 GA(일반 제공)입니다. 자세한 내용은 [SQL Managed Instance Machine Learning Services를](machine-learning-services-overview.md)참조하세요.| 
| **유지 관리 기간** | 유지 관리 기간 기능을 사용하면 현재 미리 보기 상태인 Azure SQL Managed Instance 대한 유지 관리 일정을 구성할 수 있습니다. 자세한 내용은 [유지 관리 기간을 참조하세요.](/database/maintenance-window.md)|
| **Service Broker 메시지 교환** | Azure SQL Managed Instance Service Broker 구성 요소를 사용하면 서비스에 연결된 데이터베이스 간에 안정적이고 안전한 메시지 교환에 대한 기본 지원을 제공하여 독립적인 자체 포함 서비스에서 애플리케이션을 작성할 수 있습니다. 현재 미리 보기로 제공됩니다. 자세한 내용은 [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker)참조하세요.
| **SQL 인사이트** | SQL 인사이트는 AZURE SQL 제품군의 모든 제품을 모니터링하는 포괄적인 솔루션입니다. SQL 인사이트는 동적 관리 뷰를 사용하여 상태를 모니터링하고 문제를 진단하고 성능을 조정하는 데 필요한 데이터를 공개합니다. 자세한 내용은 [인사이트 SQL 참조하세요.](../../azure-monitor/insights/sql-insights-overview.md) | 
||| 

### <a name="2020"></a>2020

2020년에 SQL Managed Instance 및 설명서에 다음과 같은 변경 내용이 추가되었습니다. 

| 변경 | 세부 정보 |
| --- | --- |
| **지원 작업 감사** | Microsoft 지원 작업 기능의 감사를 사용하면 감사 로그 대상(미리 보기)에 대한 지원 요청 중에 서버 및/또는 데이터베이스에 액세스해야 하는 경우 Microsoft 지원 작업을 감사할 수 있습니다. 자세한 내용은 [지원 작업 감사를 참조하세요.](../database/auditing-overview.md#auditing-of-microsoft-support-operations)|
| **탄력적 트랜잭션** | 탄력적 트랜잭션은 Azure SQL Database 및 Azure SQL Managed Instance 여러 데이터베이스에 걸쳐 분산 데이터베이스 트랜잭션을 허용합니다. 탄력적 트랜잭션은 수직 또는 수평 분할된 데이터베이스 아키텍처(미리 보기)를 사용하는 최신 다중 테넌트 애플리케이션 개발뿐만 아니라 기존 애플리케이션의 마찰 없는 마이그레이션을 가능하게 하기 위해 추가되었습니다. 자세한 내용은 [분산 트랜잭션을 참조하세요.](../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance) | 
| **구성 가능한 백업 스토리지 중복성** | 이제 백업 스토리지 중복성을 위해 LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지) 옵션을 구성하여 더 많은 유연성과 선택을 제공할 수 있습니다. 자세한 내용은 [백업 스토리지 중복 구성을 참조하세요.](../database/automated-backups-overview.md?tabs=managed-instance#configure-backup-storage-redundancy)|
| **TDE 암호화 백업 성능 향상** | 이제 PITR(Point-In-Time Restore) 백업 보존 기간을 설정할 수 있으며, TDE(투명한 데이터 암호화)로 암호화된 백업의 자동화된 압축은 이제 백업 스토리지 공간을 사용하는 데 30% 더 효율적이며 최종 사용자의 비용을 절감할 수 있습니다. 자세한 내용은 [PITR 변경을](../database/automated-backups-overview.md?tabs=managed-instance#change-the-short-term-retention-policy) 참조하세요. |
| **Azure AD 인증 개선** | Azure AD 애플리케이션을 사용하여 사용자 만들기를 자동화하고 개별 Azure AD 게스트 사용자(미리 보기)를 만듭니다. 자세한 내용은 [Azure AD의 디렉터리 읽기 권한자 참조하세요.](../database/authentication-aad-directory-readers-role.md)|
| **글로벌 VNet 피어링 지원** | 글로벌 가상 네트워크 피어링 지원이 SQL Managed Instance 추가되어 지역 복제 환경이 개선되었습니다. [관리되는 인스턴스 간의 지역 복제를 참조하세요.](../database/auto-failover-group-overview.md?tabs=azure-powershell#enabling-geo-replication-between-managed-instances-and-their-vnets) |
| **SSRS 카탈로그 데이터베이스 호스팅** | SQL 이제 Managed Instance 지원되는 모든 버전의 SQL Server Reporting Services(SSRS)에 대한 카탈로그 데이터베이스를 호스트할 수 있습니다. | 
| **주요 성능 향상** | 트랜잭션 로그 쓰기 처리량 향상, 중요 비즈니스용 인스턴스에 대한 향상된 데이터 및 로그 IOPS, 향상된 TempDB 성능 등 SQL Managed Instance 성능 개선 자세한 내용은 [향상된 성능](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256) 기술 커뮤니티 블로그를 참조하세요. 
| **향상된 관리 환경** | 이제 새 [OPERATIONS API](/rest/api/sql/2021-02-01-preview/managed-instance-operations)를 사용하여 장기 실행 인스턴스 작업의 진행 상황을 확인할 수 있습니다. 자세한 내용은 [관리 작업](management-operations-overview.md?tabs=azure-portal)을 참조하세요.
| **기계 학습 지원** | Machine Learning R 및 Python 언어를 지원하는 서비스에는 이제 Azure SQL Managed Instance(미리 보기)에 대한 미리 보기 지원이 포함됩니다. 자세한 내용은 [SQL Managed Instance 사용하여 기계 학습을](machine-learning-services-overview.md)참조하세요. | 
| **사용자가 시작한 장애 조치(failover)** | 이제 사용자 시작 장애 조치(failover)가 일반 공급되어 PowerShell, CLI 명령 및 API 호출을 사용하여 자동 장애 조치(failover)를 수동으로 시작하는 기능을 제공하여 애플리케이션 복원력을 향상합니다. 자세한 내용은 [복원력 테스트를 참조하세요.](../database/high-availability-sla.md#testing-application-fault-resiliency) 
|  |  |



## <a name="known-issues"></a>알려진 문제

알려진 문제 콘텐츠는 SQL Managed Instance 문서에서 알려진 전용 [문제로](doc-changes-updates-known-issues.md) 이동되었습니다. 


## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
