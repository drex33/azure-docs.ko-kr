---
title: 최대 병렬 처리 수준(MAXDOP) 구성
titleSuffix: Azure SQL Database
description: 최대 병렬 처리 수준(MAXDOP)에 대해 알아봅니다.
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: 64eaa7d70d32ce88a98a12a38a5c59e13de58059
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020885"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Azure SQL Database에서 최대 병렬 처리 수준(MAXDOP) 구성
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  이 문서에서는 Azure SQL Database의 **최대 병렬 처리 수준(MAXDOP)** 구성 설정에 대해 설명합니다. 

> [!NOTE]
> **이 콘텐츠는 Azure SQL Database를 중점적으로 다룹니다.** Azure SQL Database는 안정적인 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 하므로 문제 해결 및 구성 옵션은 다르지만 많은 콘텐츠가 비슷합니다. SQL Server의 MAXDOP에 대한 자세한 내용은 [최대 병렬 처리 수준 서버 구성 옵션 구성](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)을 참조하세요.

## <a name="overview"></a>개요
  MAXDOP는 데이터베이스 엔진에서 쿼리 내부 병렬 처리를 제어합니다. 일반적으로 MAXDOP 값이 높을수록 쿼리당 병렬 스레드 수가 많아지고 쿼리 실행이 빨라집니다. 

  Azure SQL Database에서 새 단일 데이터베이스 및 탄력적 풀 데이터베이스 각각에 대한 기본 MAXDOP 설정값은 8입니다. 이 기본값을 사용하면 불필요한 리소스 사용률을 방지하는 동시에 데이터베이스 엔진이 병렬 스레드를 사용하여 쿼리를 더 빠르게 실행할 수 있습니다. 일반적으로 Azure SQL Database 워크로드에서 MAXDOP를 추가로 구성할 필요는 없지만 고급 성능 튜닝 연습으로 유용할 수 있습니다.

> [!Note]
>   2020년 9월, Azure SQL Database 서비스에 대한 수년간의 원격 분석을 기반으로 가장 광범위한 고객 워크로드를 위한 최적의 값으로 MAXDOP 8이 [새 데이터베이스의 기본값](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)이 되었습니다. 이 기본값은 과도한 병렬 처리로 인한 성능 문제를 방지하는 데 도움이 되었습니다. 이전에는 새 데이터베이스에 대한 기본 설정값이 MAXDOP 0이었습니다. 2020년 9월 이전에 만든 기존 데이터베이스에 대해서는 MAXDOP이 자동으로 변경되지 않았습니다.

  일반적으로 데이터베이스 엔진에서 병렬 처리를 사용하여 쿼리를 실행하도록 선택하면 실행 시간이 더 빨라집니다. 그러나 과도한 병렬 처리는 쿼리 성능을 향상시키지 않고 추가 프로세서 리소스를 소비할 수 있습니다. 대규모 과도한 병렬 처리는 동일한 데이터베이스 엔진 인스턴스에서 실행되는 모든 쿼리의 쿼리 성능에 부정적인 영향을 미칠 수 있습니다. 일반적으로 병렬 처리의 상한선을 설정하는 것은 SQL Server 워크로드의 일반적인 성능 튜닝 방식이었습니다.

  다음 표에서는 서로 다른 MAXDOP 값을 사용하여 쿼리를 실행할 때의 데이터베이스 엔진 동작에 대해 설명합니다.

| MAXDOP | 동작 | 
|--|--|
|  = 1 | 데이터베이스 엔진은 단일 직렬 스레드를 사용하여 쿼리를 실행합니다. 병렬 스레드는 사용되지 않습니다. | 
| > 1 | 데이터베이스 엔진은 병렬 스레드에서 사용할 추가 [스케줄러](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)의 수를 MAXDOP 값 또는 총 논리 프로세서 수 중에 더 작은 값으로 설정합니다. |
| = 0 | 데이터베이스 엔진은 병렬 스레드에서 사용할 추가 [스케줄러](/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling)의 수를 총 논리 프로세서 수 또는 64 중에 더 작은 값으로 설정합니다. | 
| | |

> [!Note]
> 각 쿼리는 하나 이상의 스케줄러와 이 스케줄러의 작업자 스레드 하나로 실행됩니다.
>
> 병렬 처리로 실행되는 쿼리는 추가 스케줄러와 추가 병렬 스레드를 사용합니다. 여러 병렬 스레드가 동일한 스케줄러에서 실행될 수 있기 때문에 쿼리를 실행하는 데 사용되는 총 스레드 수는 지정된 MAXDOP 값 또는 총 논리 프로세서 수보다 많을 수 있습니다. 자세한 내용은 [병렬 작업 예약](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)을 참조하세요.

##  <a name="considerations"></a><a name="Considerations"></a> 고려 사항  

-   Azure SQL Database에서 기본 MAXDOP 값을 변경할 수 있습니다.
    -   쿼리 수준에서 **MAXDOP** [쿼리 힌트](/sql/t-sql/queries/hints-transact-sql-query)를 사용합니다.     
    -   데이터베이스 수준에서 **MAXDOP** [데이터베이스 범위 구성](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 사용합니다.

-   장기 SQL Server MAXDOP 고려 사항 및 [권장 사항](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)이 Azure SQL Database에 적용될 수 있습니다. 

-   인덱스를 만들거나 다시 작성하는 인덱스 작업 또는 클러스터형 인덱스를 삭제하는 인덱스 작업은 리소스가 많이 소모됩니다. `CREATE INDEX` 또는 `ALTER INDEX`문에 MAXDOP 인덱스 옵션을 지정하면 인덱스 작업의 데이터베이스 MAXDOP 값을 재정의할 수 있습니다. MAXDOP 값은 실행 시 문에 적용되며 인덱스 메타데이터에 저장되지 않습니다. 자세한 내용은 [병렬 인덱스 작업 구성](/sql/relational-databases/indexes/configure-parallel-index-operations)을 참조하세요.  
  
-   쿼리 및 인덱스 작업 외에도 MAXDOP의 데이터베이스 범위 구성 옵션은 DBCC CHECKTABLE, DBCC CHECKDB 및 DBCC CHECKFILEGROUP과 같은 병렬 실행을 사용할 수 있는 다른 명령문의 병렬 처리도 제어합니다. 

##  <a name="recommendations"></a><a name="Recommendations"></a> 권장 사항  

  데이터베이스에 대한 MAXDOP를 변경하면 쿼리 성능 및 리소스 사용률에 긍정적인 영향과 부정적인 영향을 모두 줄 수 있습니다. 그러나 모든 워크로드에 적합한 단일 MAXDOP 값은 없습니다. MAXDOP 설정 [권장 사항](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)에는 미묘한 차이가 있으며 많은 요인에 따라 달라집니다. 

  사용량이 가장 많을 때의 일부 동시 워크로드는 다른 MAXDOP에서 가장 효과적으로 작동할 수 있습니다. 올바르게 구성된 MAXDOP는 성능 및 가용성 인시던트의 위험을 줄이고, 경우에 따라 불필요한 리소스 사용률을 방지함으로써 비용을 절감하여 더 낮은 서비스 목표로 스케일 다운할 수 있습니다.

### <a name="excessive-parallelism"></a>과도한 병렬 처리

  높은 MAXDOP는 종종 CPU를 많이 사용하는 쿼리 기간을 단축합니다. 그러나 과도한 병렬 처리는 다른 CPU 및 작업자 스레드 리소스 쿼리의 리소스를 사용함으로써 다른 동시 워크로드 성능을 저해할 수 있습니다. 극단적인 경우 과도한 병렬 처리는 모든 데이터베이스 또는 탄력적 풀 리소스를 사용하여 쿼리 시간 제한, 오류 및 애플리케이션 중단을 유발할 수 있습니다. 

> [!Tip]
> 현재 문제를 일으키지 않는 것처럼 보이더라도 MAXDOP를 0으로 설정하지 않는 것이 좋습니다.

  과도한 병렬 처리는 서비스 목표에서 제공하는 CPU 및 작업자 스레드 리소스에서 지원할 수 있는 것보다 더 많은 동시 요청이 있는 경우 가장 문제가 됩니다. 데이터베이스가 스케일 업되거나 이후 Azure SQL Database의 하드웨어 생성이 동일한 데이터베이스 서비스 목표에 더 많은 코어를 제공하는 경우 과도한 병렬 처리로 인해 발생할 수 있는 문제의 위험을 줄이기 위해 MAXDOP 0을 사용하지 않는 것이 좋습니다.

### <a name="modifying-maxdop"></a>MAXDOP 수정 

  기본값과 다른 MAXDOP 설정이 Azure SQL Database 워크로드에 최적이라고 판단되면 `ALTER DATABASE SCOPED CONFIGURATION` T-SQL 문을 사용할 수 있습니다. 예제는 아래의 [Transact-SQL 사용 예제](#examples) 섹션을 참조하세요. 만든 새 데이터베이스 각각에 대해 MAXDOP를 기본값이 아닌 값으로 변경하려면 데이터베이스 배포 프로세스에 이 단계를 추가합니다.

  기본값이 아닌 MAXDOP가 워크로드에서 쿼리의 작은 하위 집합에만 유용한 경우 옵션(MAXDOP) 힌트를 추가하여 쿼리 수준에서 MAXDOP를 재정의할 수 있습니다. 예제는 아래의 [Transact-SQL 사용 예제](#examples) 섹션을 참조하세요. 

  실제 동시 쿼리 로드를 포함하는 부하 테스트로 MAXDOP 구성 변경을 철저히 테스트합니다. 

  읽기-쓰기 및 읽기 전용 워크로드에 대해 서로 다른 MAXDOP 설정이 최적인 경우 주 복제본과 보조 복제본에 대한 MAXDOP를 독립적으로 구성할 수 있습니다. 이는 Azure SQL Database [읽기 확장](read-scale-out.md), [지역에서 복제](active-geo-replication-overview.md) 및 [하이퍼스케일](service-tier-hyperscale.md) 보조 복제본에 적용됩니다. 기본적으로 모든 보조 복제본은 주 복제본의 MAXDOP 구성을 상속합니다.

## <a name="security"></a><a name="Security"></a> 보안  
  
###  <a name="permissions"></a><a name="Permissions"></a> 권한  
  `ALTER DATABASE SCOPED CONFIGURATION` 문은 서버 관리자, `db_owner`데이터베이스 역할의 멤버, 또는 `ALTER ANY DATABASE SCOPED CONFIGURATION` 권한이 부여된 사용자로 실행되어야 합니다.
 
## <a name="examples"></a>예제   

  이 예에서는 Azure SQL Database의 새 단일 데이터베이스에 대해 `SAMPLE` 옵션이 선택된 경우 최신 **AdventureWorksLT** 샘플 데이터베이스를 사용합니다.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP 데이터베이스 범위 구성   

  이 예에서는 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 문을 사용하여 `MAXDOP` 구성을 `2`로 설정하는 방법을 보여줍니다. 설정은 새 쿼리에 즉시 적용됩니다. PowerShell cmdlet [Invoke-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd)는 T-SQL 쿼리를 실행하여 MAXDOP 데이터베이스 범위 구성을 설정하고 반환합니다. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

이 예제는 [읽기 스케일 아웃 복제본 지원](read-scale-out.md), [지역에서 복제](active-geo-replication-overview.md) 및 [Azure SQL Database 하이퍼스케일 보조 복제본](service-tier-hyperscale.md)을 사용하는 Azure SQL Database를 사용합니다. 예를 들어 읽기/쓰기 및 읽기 전용 작업 간에 차이가 있을 수 있다는 것을 예상하여 주 복제본은 보조 복제본과는 다른 기본 MAXDOP으로 설정됩니다.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP_primary + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  [Azure Portal 쿼리 편집기](connect-query-portal.md), [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 Azure SQL Database에 대해 T-SQL 쿼리를 실행할 수 있습니다.

1.  새 쿼리 창을 엽니다.

2.  MAXDOP를 변경하려는 데이터베이스에 연결합니다. master 데이터베이스에서 데이터베이스 범위 구성을 변경할 수 없습니다.
  
3.  다음 예제를 복사하여 쿼리 창에 붙여넣고 **실행** 을 선택합니다. 

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP 데이터베이스 범위 구성

  이 예에서는 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) 시스템 카탈로그 보기를 사용하여 현재 데이터베이스 MAXDOP 데이터베이스 범위 구성을 확인하는 방법을 보여 줍니다.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  이 예에서는 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) 문을 사용하여 `MAXDOP` 구성을 `8`로 설정하는 방법을 보여줍니다. 설정은 즉시 적용됩니다.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

이 예제는 [읽기 확장 복제본 지원](read-scale-out.md), [지역에서 복제](active-geo-replication-overview.md) 및 [하이퍼스케일](service-tier-hyperscale.md) 보조 복제본을 사용하는 Azure SQL Database를 사용합니다. 예를 들어 읽기-쓰기 작업과 읽기 전용 워크로드 간에 차이가 있다는 것을 예상하여 주 복제본은 보조 복제본과 다른 MAXDOP로 설정됩니다. 모든 명령문은 주 복제본에서 실행됩니다. `sys.database_scoped_configurations`의 `value_for_secondary` 열에는 보조 복제본에 대한 설정이 포함됩니다.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP 쿼리 힌트

  이 예에서는 쿼리 힌트를 사용하여 `max degree of parallelism`을 `2`로 설정함으로써 쿼리를 실행하는 방법을 보여 줍니다.  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP 인덱스 옵션

  이 예에서는 인덱스 옵션을 사용하여 `max degree of parallelism`을 `12`로 설정함으로써 인덱스를 다시 빌드하는 방법을 보여 줍니다.  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>참고 항목  
* [데이터베이스 범위 구성 &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [병렬 인덱스 작업 구성](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [쿼리 힌트&#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [인덱스 옵션 설정](/sql/relational-databases/indexes/set-index-options)     
* [Azure SQL Database 차단 문제의 이해 및 해결](understand-resolve-blocking.md)

## <a name="next-steps"></a>다음 단계

* [성능 모니터링 및 튜닝](/sql/relational-databases/performance/monitor-and-tune-for-performance)
