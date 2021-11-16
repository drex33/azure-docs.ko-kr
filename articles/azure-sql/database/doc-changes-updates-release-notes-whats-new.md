---
title: 새로운 기능은 무엇입니까?
titleSuffix: Azure SQL Database
description: Azure SQL Database에 대 한 새로운 기능 및 설명서 개선 사항에 대해 알아봅니다.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: e57adde4df10a942baff7b7032706a991d46050c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556176"
---
# <a name="whats-new-in-azure-sql-database"></a>Azure SQL Database의 새로운 기능
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 최신 릴리스 [Azure SQL Database](https://azure.microsoft.com/products/azure-sql/database/)의 새로운 기능 및 향상 된 기능에 대 한 설명서 변경 내용을 요약 합니다. Azure SQL Database에 대 한 자세한 내용은 [개요](sql-database-paas-overview.md)를 참조 하세요. 

Azure SQL Managed Instance는 [새로운 기능](../managed-instance/doc-changes-updates-release-notes-whats-new.md)을 참조 하세요.

> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)



## <a name="preview"></a>미리 보기 

다음 표에서는 현재 미리 보기 상태인 Azure SQL Database의 기능을 보여 줍니다. 

| 기능 | 세부 정보 |
| ---| --- |
| [변경 데이터 캡처](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | CDC (변경 데이터 캡처)를 사용 하면 데이터베이스에서 발생 하는 모든 변경 내용을 추적할 수 있습니다. 이 기능은 SQL Server에 사용할 수 있지만, Azure SQL Database와 함께 사용 하는 것은 현재 미리 보기 상태입니다. |
| [탄력적 작업](elastic-jobs-overview.md) | 탄력적 작업 기능은 PaaS 제품으로 Azure SQL Database에 대 한 SQL Server 에이전트 교체입니다.  |
| [탄력적 쿼리](elastic-query-overview.md) | 탄력적 쿼리 기능을 사용 하면 Azure SQL Database에서 데이터베이스 간 쿼리를 수행할 수 있습니다. |
| [탄력적 트랜잭션](elastic-transactions-overview.md) | 탄력적 트랜잭션을 사용 하면 Azure SQL Database에서 클라우드 데이터베이스 간에 분산 되는 트랜잭션을 실행할 수 있습니다. |
| [원장](ledger-overview.md) | Azure SQL Database 원장 기능을 사용 하면 데이터의 변조를 방지 하는 감사자 또는 다른 비즈니스 당사자와 같은 다른 당사자에 게 암호화를 증명할 수 있습니다. | 
| [유지 관리 기간](maintenance-window.md)| 유지 관리 기간 기능을 사용 하 여 Azure SQL Database에 대 한 유지 관리 일정을 구성할 수 있습니다. |
| [Azure Portal의 쿼리 편집기](connect-query-portal.md) | 포털의 쿼리 편집기를 사용 하면 [Azure Portal](https://portal.azure.com)에서 직접 Azure SQL Database에 대해 쿼리를 실행할 수 있습니다.|
| [쿼리 저장소 힌트](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | 쿼리 힌트를 사용 하 여 OPTION 절을 통해 쿼리 실행을 최적화할 수 있습니다. |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md)|Azure SQL 분석은 모든 Azure SQL 데이터베이스의 성능을 대규모로 단일 보기에서 여러 구독에 걸쳐 모니터링하기 위한 고급 클라우드 모니터링 솔루션입니다. Azure SQL 분석은 성능 문제 해결을 위한 기본 제공 인텔리전스를 사용하여 주요 성능 메트릭을 수집하고 시각화합니다.|
| [SQL 인사이트](../../azure-monitor/insights/sql-insights-overview.md) |  SQL 인사이트는 AZURE SQL 제품군의 모든 제품을 모니터링하는 포괄적인 솔루션입니다. SQL 인사이트는 동적 관리 뷰를 사용하여 상태를 모니터링하고 문제를 진단하고 성능을 조정하는 데 필요한 데이터를 공개합니다.|
| [범용 계층에 대 한 영역 중복 구성](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | 영역 중복 구성 기능은 [Azure 가용성 영역](../../availability-zones/az-overview.md#availability-zones) 를 활용 하 여 Azure 지역 내의 여러 물리적 위치에서 데이터베이스를 복제 합니다. [영역 중복성](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)을 선택 하면 응용 프로그램 논리를 변경 하지 않고도 치명적인 데이터 센터 중단을 포함 하 여 훨씬 더 큰 오류 집합에 대해 범용 데이터베이스 및 탄력적 풀의 복원 력을 향상 시킬 수 있습니다. 이 기능은 현재 일반 용도의 계층 에서만 사용할 수 있습니다. | 
|||

## <a name="general-availability-ga"></a>GA(일반 공급)

다음 표에서는 지난 12 개월 내에 미리 보기에서 GA (일반 공급)로 전환 된 Azure SQL Database의 기능을 보여 줍니다. 

| 기능 | GA 월 | 세부 정보 |
| ---| --- |--- |
| [Azure Active Directory 전용 인증](authentication-azure-ad-only-authentication.md) | 2021년 11월 | Azure Active Directory 에서만 인증을 허용 하도록 Azure SQL Database를 구성할 수 있습니다. | 
| [AAD 서비스 주체](authentication-aad-service-principal.md) |  2021년 9월 | azure ad (Azure Active Directory)는 azure ad 응용 프로그램 (서비스 사용자) 대신 Azure SQL Database에서 사용자 만들기를 지원 합니다.| 
| [관리 작업 감사](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  2021년 3월 | Azure SQL 감사 기능을 사용 하면 지원 요청 중에 SQL 자산에 액세스 해야 하는 경우 Microsoft 지원 엔지니어가 수행 하는 작업을 감사 하 여 직원 들이 더 많은 투명도를 사용할 수 있습니다. | 
|||| 


## <a name="documentation-changes"></a>설명서 변경 내용

Azure SQL Database 설명서의 주요 변경 내용에 대해 알아봅니다.

### <a name="november-2021"></a>2021년 11월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure AD 전용 인증** | 사용자 Azure SQL Database에 대 한 인증을 Azure Active Directory 사용자로만 제한 하는 것이 이제 일반 공급 됩니다. 자세히 알아보려면 [AZURE AD 전용 인증](../database/authentication-azure-ad-only-authentication.md)을 참조 하세요. | 
| | | 


### <a name="october-2021"></a>2021년 10월

| 변경 | 세부 정보 |
| --- | --- |
|**새로운 기능 분할** | 이전에 결합 한 **새로운** 기능 문서는 [SQL Database의 새로운](doc-changes-updates-release-notes-whats-new.md) 기능 및 [SQL Managed Instance의 새로운](../managed-instance/doc-changes-updates-release-notes-whats-new.md)기능으로 분할 되어 현재 미리 보기에 포함 된 기능, 일반적으로 사용 가능한 기능 및 중요 한 문서 변경을 쉽게 식별할 수 있도록 합니다. 또한 [SQL Managed Instance 콘텐츠의 알려진 문제가](../managed-instance/doc-changes-updates-known-issues.md) 자체 페이지로 이동 되었습니다.  | 

### <a name="september-2021"></a>2021년 9월

| 변경 | 세부 정보 |
| --- | --- |
| **가용성 영역에 대 한 유지 관리 기간 지원** | 이제 Azure SQL Database 가용성 영역에 배포 된 경우 [유지 관리 기간 기능](maintenance-window.md) 을 사용할 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다.  | 
|||


### <a name="july-2021"></a>2021년 7월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure AD 전용 인증** | 이제 사용자를 Azure Active Directory Azure SQL Database에 대 한 인증을 제한할 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. 자세히 알아보려면 [AZURE AD 전용 인증](authentication-azure-ad-only-authentication.md)을 참조 하세요. | 
|||

### <a name="june-2021"></a>2021년 6월

| 변경 | 세부 정보 |
| --- | --- |
| **쿼리 저장소 힌트** | 이제 쿼리 힌트를 사용 하 여 OPTION 절을 통해 쿼리 실행을 최적화할 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. 자세히 알아보려면 [쿼리 저장소 힌트](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true)를 참조 하세요. | 
|||

### <a name="may-2021"></a>2021년 5월

| 변경 | 세부 정보 |
| --- | --- |
| **변경 데이터 캡처** | Azure SQL Database에서 CDC (변경 데이터 캡처)를 사용 하는 것은 현재 미리 보기 상태입니다. 자세히 알아보려면 [변경 데이터 캡처](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)를 참조 하세요. | 
| **SQL Database 원장** | SQL Database 원장은 미리 보기 상태 이며, 감사자 또는 다른 비즈니스 당사자와 같이 데이터가 변조 되지 않은 다른 당사자에 게 암호화를 증명 하는 기능이 도입 되었습니다. 자세히 알아보려면 [원장](ledger-overview.md)을 참조 하세요. | 
|||

### <a name="march-2021"></a>2021년 3월

| 변경 | 세부 정보 |
| --- | --- |
 | **유지 관리 기간** | 유지 관리 기간 기능을 사용 하면 현재 미리 보기로 제공 되는 Azure SQL Database에 대 한 유지 관리 일정을 구성할 수 있습니다. 자세한 내용은 [유지 관리 기간](maintenance-window.md)을 참조 하세요.|
| **SQL 인사이트** | SQL 인사이트는 AZURE SQL 제품군의 모든 제품을 모니터링하는 포괄적인 솔루션입니다. SQL 인사이트는 동적 관리 뷰를 사용하여 상태를 모니터링하고 문제를 진단하고 성능을 조정하는 데 필요한 데이터를 공개합니다. 자세히 알아보려면 [SQL insights](../../azure-monitor/insights/sql-insights-overview.md)를 참조 하세요. | 
||| 

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
