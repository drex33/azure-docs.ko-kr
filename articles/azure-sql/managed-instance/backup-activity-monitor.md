---
title: 백업 작업 모니터링
titleSuffix: Azure SQL Managed Instance
description: 확장 이벤트를 사용하여 Azure SQL Managed Instance 백업 작업을 모니터링하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: mode-other
ms.devlang: ''
ms.topic: quickstart
author: Misliplavo
ms.author: mlazic
ms.reviewer: mathoma
ms.date: 12/14/2018
ms.openlocfilehash: 86bf5b7db5a5c28e74b87d2d626b0299943586c9
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133044765"
---
# <a name="monitor-backup-activity-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 백업 작업 모니터링 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)에 대한 백업 작업을 모니터링하도록 확장 이벤트(XEvent) 세션을 구성하는 방법을 설명합니다. 

## <a name="overview"></a>개요

Azure SQL Managed Instance는 보고 목적으로 백업 작업 중에 [이벤트(확장 이벤트 또는 XEvents](../database/xevent-db-diff-from-svr.md)라고도 함)를 내보냅니다. 백업 상태, 백업 유형, 크기, 시간 및 msdb 데이터베이스 내의 위치와 같은 정보를 추적하도록 XEvent 세션을 구성합니다. 이 정보는 백업 모니터링 소프트웨어와 통합될 수 있으며 엔터프라이즈 감사 목적으로도 사용될 수 있습니다. 

엔터프라이즈 감사에는 성공적인 백업, 백업 시간 및 백업 기간에 대한 증명이 필요할 수 있습니다.

## <a name="configure-xevent-session"></a>XEvent 세션 구성

확장 이벤트 `backup_restore_progress_trace`를 사용하여 SQL Managed Instance 백업의 진행 상황을 기록합니다. 필요에 따라 XEvent 세션을 수정하여 비즈니스에 관심이 있는 정보를 추적합니다. 이러한 T-SQL 조각은 XEvent 세션을 링 버퍼에 저장하지만 [Azure Blob Storage](../database/xevent-code-event-file.md)에 쓸 수도 있습니다. 링 버퍼에 데이터를 저장하는 XEvent 세션의 메시지 제한은 약 1000개이므로 최근 활동을 추적하는 데만 사용해야 합니다. 또한 장애 조치(failover) 시 링 버퍼 데이터가 손실됩니다. 따라서 백업 기록 레코드의 경우 대신 이벤트 파일에 씁니다. 

### <a name="simple-tracking"></a>간단한 추적

전체 백업에 대한 간단한 이벤트를 캡처하도록 간단한 XEvent 세션을 구성합니다. 이 스크립트는 데이터베이스 이름, 처리된 총 바이트 수 및 백업이 완료된 시간을 수집합니다.

T-SQL(Transact-SQL)을 사용하여 간단한 XEvent 세션을 구성합니다. 


```sql
CREATE EVENT SESSION [Simple backup trace] ON SERVER
ADD EVENT sqlserver.backup_restore_progress_trace(
WHERE operation_type = 0
AND trace_message LIKE '%100 percent%')
ADD TARGET package0.ring_buffer
WITH(STARTUP_STATE=ON)
GO
ALTER EVENT SESSION [Simple backup trace] ON SERVER
STATE = start;
```



### <a name="verbose-tracking"></a>자세한 추적

자세한 XEvent 세션을 구성하여 백업 작업에 대한 자세한 정보를 추적합니다. 이 스크립트는 전체, 차등 및 로그 백업의 시작과 완료를 캡처합니다. 이 스크립트는 더 상세하기 때문에 링 버퍼를 더 빠르게 채우므로 간단한 스크립트보다 항목이 더 빠르게 재활용될 수 있습니다. 

T-SQL(Transact-SQL)을 사용하여 자세한 XEvent 세션을 구성합니다. 

```sql
CREATE EVENT SESSION [Verbose backup trace] ON SERVER 
ADD EVENT sqlserver.backup_restore_progress_trace(
    WHERE (
              [operation_type]=(0) AND (
              [trace_message] like '%100 percent%' OR 
              [trace_message] like '%BACKUP DATABASE%' OR [trace_message] like '%BACKUP LOG%'))
       )
ADD TARGET package0.ring_buffer
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS,
       MAX_DISPATCH_LATENCY=30 SECONDS,MAX_EVENT_SIZE=0 KB,MEMORY_PARTITION_MODE=NONE,
       TRACK_CAUSALITY=OFF,STARTUP_STATE=ON)

ALTER EVENT SESSION [Verbose backup trace] ON SERVER
STATE = start;

```

## <a name="monitor-backup-progress"></a>백업 진행률 모니터링 

XEvent 세션을 만든 후 T-SQL(Transact-SQL)을 사용하여 링 버퍼 결과를 쿼리하고 백업 진행률을 모니터링할 수 있습니다. XEvent가 시작되면 모든 백업 이벤트를 수집하므로 약 5~10분마다 항목이 세션에 추가됩니다.  

### <a name="simple-tracking"></a>간단한 추적

다음 T-SQL(Transact-SQL) 코드는 간단한 XEvent 세션을 쿼리하고 데이터베이스 이름, 처리된 총 바이트 수 및 백업이 완료된 시간을 반환합니다. 

```sql 
WITH
a AS (SELECT xed = CAST(xet.target_data AS xml)
FROM sys.dm_xe_session_targets AS xet
JOIN sys.dm_xe_sessions AS xe
ON (xe.address = xet.event_session_address)
WHERE xe.name = 'Backup trace'),
b AS(SELECT
d.n.value('(@timestamp)[1]', 'datetime2') AS [timestamp],
ISNULL(db.name, d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)')) AS database_name,
d.n.value('(data[@name="trace_message"]/value)[1]', 'varchar(4000)') AS trace_message
FROM a
CROSS APPLY  xed.nodes('/RingBufferTarget/event') d(n)
LEFT JOIN master.sys.databases db
ON db.physical_database_name = d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)'))
SELECT * FROM b
```

다음 스크린샷은 위 쿼리의 출력 예제를 보여줍니다. 

![xEvent 출력의 스크린샷](./media/backup-activity-monitor/present-xevents-output.png)

이 예제에서는 2시간 30분 동안 5개의 데이터베이스가 자동으로 백업되었으며 XEvent 세션에는 130개의 항목이 있습니다. 

### <a name="verbose-tracking"></a>자세한 추적 

다음 T-SQL(Transact-SQL) 코드는 자세한 XEvent 세션을 쿼리하고 데이터베이스 이름과 전체, 차등 및 로그 백업의 시작 및 완료를 반환합니다. 


```sql
WITH
a AS (SELECT xed = CAST(xet.target_data AS xml)
FROM sys.dm_xe_session_targets AS xet
JOIN sys.dm_xe_sessions AS xe
ON (xe.address = xet.event_session_address)
WHERE xe.name = 'Verbose backup trace'),
b AS(SELECT
d.n.value('(@timestamp)[1]', 'datetime2') AS [timestamp],
ISNULL(db.name, d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)')) AS database_name,
d.n.value('(data[@name="trace_message"]/value)[1]', 'varchar(4000)') AS trace_message
FROM a
CROSS APPLY  xed.nodes('/RingBufferTarget/event') d(n)
LEFT JOIN master.sys.databases db
ON db.physical_database_name = d.n.value('(data[@name="database_name"]/value)[1]', 'varchar(200)'))
SELECT * FROM b
```

다음 스크린샷은 XEvent 세션에서 전체 백업의 예제를 보여줍니다.

:::image type="content" source="media/backup-activity-monitor/output-with-full.png" alt-text="전체 백업을 보여주는 XEvent 출력":::

다음 스크린샷은 XEvent 세션에서 차등 백업의 출력 예제를 보여줍니다.

:::image type="content" source="media/backup-activity-monitor/output-with-differential.png" alt-text="차등 백업을 보여주는 XEvent 출력":::


## <a name="next-steps"></a>다음 단계

백업이 완료되면 [특정 시점으로 복원](point-in-time-restore.md)하거나 [장기 보존 정책을 구성](long-term-backup-retention-configure.md)할 수 있습니다. 

자세한 내용은 [자동 백업](../database/automated-backups-overview.md)을 참조하세요. 
