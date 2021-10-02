---
title: 전용 SQL 풀에서 잘못 분류된 워크로드 문제 해결
description: 워크로드가 Azure Synapse Analytics 전용 SQL 풀의 의도하지 않은 워크로드 그룹으로 잘못 분류되는 시나리오를 식별하고 문제를 해결합니다.
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 1fa0394f3e711ec172f5a3ad32288f63bcf55dce
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391148"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>Azure Synapse Analytics 잘못 분류된 워크로드 문제 해결

이 문서에서는 잘못 분류된 워크로드 문제를 해결하는 방법 및 전용 SQL 풀의 워크로드에 대한 분류 이유를 식별하는 방법에 대한 지침을 설명합니다.

Azure Synapse Analytics [워크로드를 적절한 워크로드 그룹으로 분류하고,](sql-data-warehouse-workload-classification.md) [중요도를 할당하고, SLA를](sql-data-warehouse-workload-importance.md)충족하도록 [리소스를 격리하는](sql-data-warehouse-workload-isolation.md) 등의 워크로드 관리 기능을 제공합니다. 

그러나 일부 시나리오에서는 이러한 기능의 조합으로 인해 사용자 의도를 반영하지 않는 워크로드 분류가 발생할 수 있습니다. 이 문서에서는 이러한 일반적인 시나리오 및 문제 해결 방법을 나열합니다. 먼저 잘못 분류된 워크로드 시나리오 문제를 해결하기 위한 기본 정보를 쿼리해야 합니다.

> [!NOTE]
> 이 문서는 Azure Synapse Analytics 서버리스 SQL 풀에는 적용되지 않습니다.

## <a name="basic-troubleshooting-information"></a>기본 문제 해결 정보

잘못 분류된 워크로드 시나리오의 문제를 해결하려면 다음 정보가 필요합니다.

- 모든 [작업 그룹](#workload-groups) 목록
- 모든 [워크로드 분류자](#workload-classifiers) 및 관련 작업 그룹 목록
- 사용자 [및 매핑된 작업 그룹](#users-and-mapped-resource-classes) 목록(시스템 및 사용자 정의) 
- 요청의 [작업 그룹 및 분류자 세부 정보](#workload-group-and-classifier-details-of-a-request) 목록


### <a name="workload-groups"></a>워크로드 그룹

#### <a name="azure-portal"></a>Azure portal

Azure Portal 모든 작업 그룹(시스템 작업 그룹 포함) 및 관련 세부 정보 목록을 얻으려면 다음을 수행합니다.

1. 관심 있는 전용 SQL 풀이 만들어진 Azure Synapse 작업 영역으로 이동합니다.
1. 왼쪽 창에는 작업 영역 아래에 생성된 모든 풀 유형이 나열됩니다. 분석 풀 섹션에서 **SQL** **풀을** 선택합니다.
1. 관심 있는 전용 SQL 풀을 선택합니다.
1. 왼쪽 창의 **설정** 아래에서 **워크로드 관리를** 선택합니다.
1. **작업 그룹** 섹션 아래에 모든 워크로드가 나열됩니다. 기본적으로 **사용자 정의 작업 그룹만** 나열됩니다. 사용자 정의 작업 그룹 및 시스템 정의 작업 그룹의 목록을 보려면 필터를 편집하고 **모두를 선택합니다.**

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="작업 그룹 목록 필터" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

T SQL 사용하여 작업 그룹을 보려면 [SSMS(SQL Server Management Studio)를 사용하여 전용 SQL 풀에 연결하고](../sql/get-started-ssms.md) 다음 쿼리를 발급합니다.
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

자세한 내용은 [sys.workload_management_workload_groups.](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql)


### <a name="workload-classifiers"></a>워크로드 분류자

#### <a name="azure-portal"></a>Azure portal

Azure Portal 작업 그룹별로 모든 워크로드 분류자(시스템 정의 분류자 포함)를 나열하려면 작업 그룹 테이블의 **분류자** 열에 나열된 숫자를 참조하세요(이전 섹션 참조).

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="작업 그룹 분류자 목록" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

T-SQL 사용하여 모든 워크로드 분류자(시스템 정의 분류자 포함) 목록을 얻으려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

자세한 내용은 [sys.workload_management_workload_classifiers.](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql)

### <a name="users-and-mapped-resource-classes"></a>사용자 및 매핑된 리소스 클래스

T-SQL 시스템 정의 리소스 클래스 및 매핑된 사용자 목록을 얻으려면 다음 쿼리를 사용합니다.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>요청의 워크로드 그룹 및 분류자 세부 정보

잘못 분류된 워크로드 문제를 해결하는 첫 번째 단계는 쿼리에 대한 워크로드 그룹 및 워크로드 분류자를 식별하는 것입니다. 동적 관리 뷰 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 사용하여 제출된 쿼리 및 해당 분류를 볼 수 있습니다.

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

자세한 내용은 [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-catalog-views/sys-dm-pdw-exec-requests-transact-sql)

## <a name="common-scenarios-of-misclassified-workloads"></a>잘못 분류된 워크로드의 일반적인 시나리오

다음은 의도하지 않은 잘못 분류된 워크로드가 발생할 수 있는 몇 가지 일반적인 시나리오입니다.

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>리소스 클래스 및 사용자 정의 워크로드 관리의 혼합 사용

리소스 클래스와 워크로드 그룹을 함께 사용하는 시나리오에서는 리소스 클래스 매핑에 대한 사용자 역할이 워크로드 분류자 규칙과 충돌하여 의도하지 않은 쿼리 분류가 발생할 수 있습니다. 

다음 시나리오를 고려합니다.

1. 데이터베이스 사용자 DBAUser는 프로시저를 사용하여 largerc 리소스 클래스 역할에 `sp_addrolemember` 할당됩니다.
1. DBAUser는 워크로드 관리를 사용하여 새 작업 그룹 및 분류자를 만들었습니다.
1. 새로 만든 워크로드 분류자에서 데이터베이스 역할 DBARole을 중요도가 높은 mediumrc 리소스 클래스에 매핑합니다. 
1. DBAUser는 DBARole 데이터베이스 역할의 멤버로 만들어집니다.
1. DBAUser가 쿼리를 실행하는 경우 쿼리는 워크로드 분류자 기반의 mediumrc에서 실행될 것으로 예상됩니다. 대신 **사용자** 매핑이 분류자로의 **역할 멤버 자격** 매핑보다 우선하기 때문에 largerc에 할당됩니다.

리소스 클래스와 워크로드 관리 그룹을 혼합하여 워크로드 관리를 수행하지 않는 것이 가장 좋습니다. 리소스 클래스를 워크로드로 변환하는 단계에 대한 자세한 내용은 [리소스 클래스를 작업 그룹으로 변환을 참조하세요.](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) 

그러나 리소스 클래스와 워크로드 관리를 함께 사용해야 하는 상황이 있을 수 있습니다. 이러한 시나리오에서는 오분류 문제 해결을 간소화하기 위해 워크로드 분류자를 만들 때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다. 아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환합니다. 해당하는 리소스 클래스에서 반환된 각각의 멤버 이름에 대해 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 실행합니다.

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>일부 관리자가 항상 smallrc 작업 그룹에 매핑됩니다.

Azure Synapse 작업 영역 SQL 관리자 로그인, Azure Synapse Azure Active Directory 관리자(사용자 또는 그룹 구성원) 또는 데이터베이스 소유자에 대한 시나리오를 고려합니다. 이러한 사용자는 여전히 워크로드 분류자가 있거나 smallrc 이외의 리소스 클래스 역할에 추가되었을 수 있습니다. 사용자가 다른 리소스 클래스 또는 작업 그룹에 매핑되더라도 이러한 사용자가 실행하는 모든 쿼리는 smallrc 리소스 클래스에서 계속 실행됩니다. 

**권장 사항:** 이러한 관리자가 기본 작업 그룹을 변경할 수 없습니다. 자세한 내용은 리소스 클래스를 통해 [워크로드 관리를 참조하세요.](resource-classes-for-workload-management.md#default-resource-class) 중요 또는 성능에 민감한 워크로드는 전용 SQL 풀에서 이러한 관리 사용자 중 하나로 실행되지 않는 것이 좋습니다.

Azure Synapse 작업 영역 SQL 관리자 로그인 및 Azure Synapse Azure Active Directory 관리자(사용자 또는 그룹 구성원)는 Azure Portal 지정됩니다.

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="작업 영역 SQL 관리자 로그인 필드를 확인하여 서비스 관리자 식별" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

마찬가지로 데이터베이스 소유자(dbo) 및 **db_owner** 데이터베이스 역할은 기본 리소스 클래스를 변경할 수 없습니다. 사용자가 데이터베이스 소유자이거나 **db_owner** 데이터베이스 역할에 추가된 경우 사용자가 실행한 모든 쿼리는 기본적으로 smallrc로 이동합니다. 이러한 역할은 smallrc 이외의 리소스 클래스에 추가할 수 없습니다. 그러나 이 역할의 일부인 사용자가 쿼리를 다른 작업 그룹으로 분류하려는 경우 [워크로드 분류자 정의](sql-data-warehouse-workload-classification.md)에서 MEMBERNAME 옵션을 사용할 수 있습니다.


### <a name="use-workload-group-precedence-for-better-classification"></a>더 나은 분류를 위해 작업 그룹 우선 순위 사용

워크로드 분류자가 여러 작업 그룹에 매핑되거나 사용자가 여러 리소스 클래스에 매핑되는 시나리오에서는 우선 순위에 따라 선택한 작업 그룹 및 리소스 클래스가 결정됩니다. 우선 순위 규칙 목록: 

> [!NOTE]
> 리소스 클래스 [및 사용자 정의 워크로드 관리 혼합](#mixed-usage-of-resource-classes-and-user-defined-workload-management) 섹션에서 설명한 대로 리소스 클래스 및 사용자 정의 작업 그룹/클래스의 사용을 결합하는 것은 권장되지 않습니다.

#### <a name="if-resource-classes-are-being-used"></a>리소스 클래스를 사용하는 경우:

리소스 클래스가 사용되는 시나리오에서는 실행 중인 각 워크로드 유형에 대한 전용 사용자를 만드는 것이 가장 좋습니다. 그러나 사용자가 여러 리소스 클래스의 멤버인 경우 리소스 클래스 우선 [순위에](resource-classes-for-workload-management.md#resource-class-precedence) 설명된 우선 순위 규칙이 적용됩니다.

1. 동적 리소스 클래스가 정적 리소스 클래스보다 우선합니다. 예를 들어 사용자가 mediumrc(동적) 및 staticrc80(static)의 멤버인 경우 사용자 쿼리는 mediumrc를 통해 실행됩니다.
1. 더 작은 리소스 클래스보다 더 큰 리소스 클래스가 선호됩니다. 예를 들어 사용자가 staticrc20 및 staticrc80의 멤버인 경우 사용자 쿼리는 staticrc80을 통해 실행됩니다.

#### <a name="if-workload-management-capabilities-are-used"></a>워크로드 관리 기능이 사용되는 경우

WLM은 동일한 사용자 또는 작업 그룹에 대해 여러 워크로드 분류자를 만드는 기능을 제공합니다. 분류자 정의 문에는 들어오는 요청이 워크로드에 할당되는 매개 변수가 여러 가지 있습니다. 이러한 매개 변수는 아래와 같이 **가중치** 점수를 가지며 이 점수는 우선 순위 순서를 결정합니다. 

|**분류자 매개 변수** |**Weight**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

예제를 사용하여 이러한 우선 순위 규칙이 작동하는 것을 확인해 보겠습니다. 사용자가 다음과 같이 두 개의 워크로드 분류자를 만드는 경우:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

User-1에서 제출한 쿼리는 두 분류자를 통해 제출할 수 있습니다. 오후 6시에서 오전 7시 UTC 사이의 'dimension_loads' 레이블을 사용하여 User-1에서 실행하는 `wgDashboards` 쿼리는 WLM_LABEL 가중치 점수가 START_TIME/END_TIME 보다 높기 때문에 에 할당됩니다. 의 가중치는 `CLASSIFIER-1` 80(사용자의 경우 64, WLM_LABEL 경우 16)입니다. 의 가중치는 `CLASSIFIER-2` 68(사용자의 경우 64, START_TIME/END_TIME 4)입니다. 

워크로드 분류에 대한 자세한 내용은 [분류 가중치](sql-data-warehouse-workload-classification.md#classification-weighting) 및 [쿼리 레이블을 참조하세요.](../sql/develop-label.md)

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>워크로드 분류의 우선 순위가 동률로 이어질 경우 어떻게 되나요?

워크로드 분류자 우선 순위가 적용된 후에도 쿼리를 여러 작업 그룹으로 분류할 수 있습니다. 예를 들어 다음 분류자를 고려해 보십시오.

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

사용자가 를 사용하여 쿼리를 실행하는 경우 `OPTION (LABEL = 'dimension_loads')` 우선 순위 규칙에 따라 또는 로 분류할 수 `wgDataLoad` `wgUserqueries` 있습니다. 하지만 어떤 워크로드 그룹이 선택되나요?

#### <a name="workload-group-and-classifier-tie-breakers"></a>워크로드 그룹 및 분류자 연결 분리기

워크로드 그룹 또는 분류자에서 동률이 있는 경우 다음 우선 순위가 적용됩니다.

1. 리소스 할당이 가장 높은 워크로드 그룹이 선택됩니다. 이 동작은 로그인이 여러 리소스 클래스의 멤버인 시나리오에서 성능을 최적화합니다. 이 동작은 이전 버전과의 호환성도 보장합니다.  
    
다음 두 개의 작업 그룹 및 워크로드 분류자를 고려합니다.
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
사용자가 를 통해 쿼리를 실행하는 경우 `OPTION (LABEL = 'dimension_loads')` 쿼리는 두 분류자 모두에 대한 조건을 충족합니다. 그러나 사용자 요청은 `wgUserQueries` 요청당 최소 리소스 할당이 더 높기 때문에 작업 그룹으로 라우팅됩니다.

2. 다음으로, 각 워크로드 그룹에 대해 동시성 설정 및 사용 가능한 동시성이 동률 분리기에 고려됩니다. 요청이 도착한 시점에 사용 가능한 동시성이 가장 높은 작업 그룹은 쿼리 실행 가능성이 가장 높습니다. 

다음 두 개의 워크로드 그룹 및 워크로드 분류자를 고려합니다.

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
사용자가 를 통해 쿼리를 실행하는 경우 `OPTION (LABEL = 'dimension_loads')` 쿼리가 둘 다에 대한 조건을 충족하기 때문에 두 분류자 모두 동률을 갖습니다. 사용자가 쿼리를 제출할 때 5개의 동시 쿼리가 그룹에서 실행되고 `wgUserqueries` 10개의 쿼리가 그룹에서 실행되는 시나리오를 `wgDataLoad` 고려합니다. 사용자가 `wgUserqueries` `CLASSIFIER-2` 쿼리를 제출할 때 작업 그룹에 사용 가능한 동시성이 더 높기 때문에 사용자 요청은 를 사용하여 `wgUserqueries` 그룹으로 라우팅됩니다.

3. 다음으로, 요청의 중요도 설정은 동률 분리기에 대해 고려됩니다. 우선 순위 규칙을 사용하여 워크로드 분류에 동률이 있는 경우 요청은 중요도가 가장 높은 워크로드 그룹으로 라우팅됩니다. 자세한 내용은 [워크로드 중요도를 참조하세요.](sql-data-warehouse-workload-importance.md)

4. 마지막으로, 작업 그룹의 생성 시간은 동률 분리기에 대해 고려됩니다. 사용자 요청은 가장 최근에 만든 작업 그룹으로 라우팅됩니다. 


## <a name="next-steps"></a>다음 단계
- 워크로드 분류에 대한 자세한 내용은 [워크로드 분류를](sql-data-warehouse-workload-classification.md)참조하세요.
- 워크로드 중요도에 대한 자세한 내용은 [워크로드 중요도를 참조하세요.](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [워크로드 중요도 구성으로 이동](sql-data-warehouse-how-to-configure-workload-importance.md)합니다.
 
