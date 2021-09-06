---
title: 확장 이벤트
description: Azure SQL Database의 확장 이벤트(XEvents)와 Microsoft SQL Server의 이벤트 세션 간 차이점에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 79226ed8fa4d4e78120a0c91b672d4cfa23712fc
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675117"
---
# <a name="extended-events-in-azure-sql-database"></a>Azure SQL 데이터베이스의 확장 이벤트 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Azure SQL Database의 확장 이벤트 기능 집합은 SQL Server 및 Azure SQL Managed Instance 기능의 견고한 하위 집합입니다.

*XEvents* 는 블로그 및 기타 비공식 위치에서 '확장 이벤트'를 가리키는 비공식적 별명입니다.

확장 이벤트에 대한 추가 정보는 다음에서 확인할 수 있습니다.

- [빠른 시작: SQL Server의 확장 이벤트](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [확장 이벤트](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 다음 항목에 대한 지식이 이미 있다고 가정합니다.

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [확장 이벤트](/sql/relational-databases/extended-events/extended-events)

- 확장 이벤트에 대한 설명서의 대부분은 SQL Server, Azure SQL Database 및 Azure SQL Managed Instance에 적용됩니다.

이벤트 파일을 [대상](#AzureXEventsTargets)으로 선택할 경우 다음 항목에 대한 사전 지식이 있으면 도움이 됩니다.

- [Azure Storage 서비스](https://azure.microsoft.com/services/storage/)

- [Azure Storage와 함께 Azure PowerShell](/powershell/module/az.storage/)

## <a name="code-samples"></a>코드 샘플

관련 문서에서는 다음 두 가지 코드 샘플을 제공합니다.

- [Azure SQL Database의 확장 이벤트에 대한 링 버퍼 대상 코드](xevent-code-ring-buffer.md)

  - 짧고 간단한 Transact-SQL 스크립트.
  - 코드 샘플 문서에서는 링 버퍼 대상을 마칠 때 alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` 문을 실행하여 해당 리소스를 해제해야 함을 강조합니다. 나중에 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`(으)로 링 버퍼의 다른 인스턴스를 추가할 수 있습니다.

- [Azure SQL Database의 확장 이벤트에 대한 이벤트 파일 대상 코드](xevent-code-event-file.md)

  - 1단계는 Azure Storage 컨테이너를 만드는 PowerShell입니다.
  - 2단계는 Azure Storage 컨테이너를 사용하는 Transact-SQL입니다.

## <a name="transact-sql-differences"></a>Transact-SQL 차이점

- SQL Server에서 [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) 명령을 사용하는 경우 **ON SERVER** 절을 사용합니다. 하지만 Azure SQL Database에서는 대신 **ON DATABASE** 절을 사용합니다.
- **ON DATABASE** 절은 [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) 및 [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql) Transact-SQL 명령에도 적용됩니다.

- **CREATE EVENT SESSION** 또는 **ALTER EVENT SESSION** 문에 **STARTUP_STATE = ON** 의 이벤트 세션 옵션을 포함하는 것이 가장 좋습니다.
  - **= ON** 값은 장애 조치(failover)로 인해 논리적 데이터베이스를 재구성한 다음 자동 재시작을 지원합니다.

## <a name="new-catalog-views"></a>새 카탈로그 뷰

확장 이벤트 기능은 여러 [카탈로그 뷰](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)에서 지원합니다. 카탈로그 뷰를 통해 현재 데이터베이스에서 사용자가 만든 이벤트 세션의 *메타데이터 또는 정의* 를 확인할 수 있습니다. 뷰는 활성 이벤트 세션의 인스턴스에 대한 정보를 반환하지 않습니다.

| 카탈로그 뷰의<br/>이름 | 설명 |
|:--- |:--- |
| `sys.database_event_session_actions` |이벤트 세션의 각 이벤트의 동작에 대해 한 행을 반환합니다. |
| `sys.database_event_session_events` |이벤트 세션의 각 이벤트에 대한 행을 반환합니다. |
| `sys.database_event_session_fields` |이벤트 및 대상에 명시적으로 설정된 사용자 지정 가능한 각 열에 대한 행을 반환합니다. |
| `sys.database_event_session_targets` |이벤트 세션의 각 이벤트 대상에 대해 한 행을 반환합니다. |
| `sys.database_event_sessions` |데이터베이스의 각 이벤트 세션에 대한 행을 반환합니다. |

Microsoft SQL Server에서 유사한 카탈로그 뷰의 이름에는 *.database\_* 가 아닌 *.server\_* 가 포함됩니다. 이름 패턴은 `sys.server_event_%`와 같습니다.

## <a name="new-dynamic-management-views-dmvs"></a>새로운 [DMV](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Azure SQL Database에는 확장 이벤트를 지원하는 [DMV(동적 관리 뷰)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views)가 있습니다. DMV를 통해 *활성* 이벤트 세션을 확인할 수 있습니다.

| DMV의 이름 | 설명 |
|:--- |:--- |
| `sys.dm_xe_database_session_event_actions` |이벤트 세션 동작에 대한 정보를 반환합니다. |
| `sys.dm_xe_database_session_events` |세션 이벤트에 대한 정보를 반환합니다. |
| `sys.dm_xe_database_session_object_columns` |세션에 바인딩된 개체의 구성 값을 표시합니다. |
| `sys.dm_xe_database_session_targets` |세션 작업에 대한 정보를 반환합니다. |
| `sys.dm_xe_database_sessions` |현재 데이터베이스로 범위가 한정된 각 이벤트 세션에 대한 행을 반환합니다. |

Microsoft SQL Server에서 유사한 카탈로그 뷰는 다음과 같이 이름에 *\_database* 부분이 없습니다.

- `sys.dm_xe_sessions` 대신 `sys.dm_xe_database_sessions` 사용

### <a name="dmvs-common-to-both"></a>둘 다에 공통적인 DMV

확장 이벤트에는 Azure SQL Database, Azure SQL Managed Instance 및 Microsoft SQL Server에 공통적인 추가 DMV가 있습니다.

- `sys.dm_xe_map_values`
- `sys.dm_xe_object_columns`
- `sys.dm_xe_objects`
- `sys.dm_xe_packages`

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>사용 가능한 확장 이벤트, 동작, 대상 찾기

사용 가능한 이벤트, 작업 및 대상 목록을 가져오려면 샘플 쿼리를 사용합니다.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Azure SQL Database 이벤트 세션의 대상

Azure SQL Database에서 이벤트 세션의 결과를 캡처할 수 있는 대상은 다음과 같습니다.

- [링 버퍼 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#ring_buffer-target) - 이벤트 데이터를 메모리에 잠시 보관합니다.
- [이벤트 카운터 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_counter-target) - 확장 이벤트 세션 동안 발생하는 모든 이벤트의 수를 계산합니다.
- [이벤트 파일 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target) - Azure Storage 컨테이너에 전체 버퍼를 기록합니다.

[ETW(Windows 이벤트 추적)](/dotnet/framework/wcf/samples/etw-tracing) API는 Azure SQL Database의 확장 이벤트에서 사용할 수 없습니다.

## <a name="restrictions"></a>제한

Azure SQL Database의 클라우드 환경에 적합한 몇 가지 보안 관련 차이점이 있습니다.

- 확장 이벤트는 단일 테넌트 격리 모델에서 찾을 수 있습니다. 한 데이터베이스의 이벤트 세션은 다른 데이터베이스의 데이터 또는 이벤트에 액세스할 수 없습니다.
- `master` 데이터베이스 컨텍스트에서는 `CREATE EVENT SESSION` 문을 실행할 수 없습니다.
    
## <a name="permission-model"></a>권한 모델

`CREATE EVENT SESSION` 문을 실행하려면 데이터베이스에 **제어** 권한이 있어야 합니다. 데이터베이스 소유자(dbo)는 **제어** 권한이 있습니다.

### <a name="storage-container-authorizations"></a>스토리지 컨테이너 권한

Azure Storage 컨테이너에 대해 만드는 SAS 토큰은 권한에 대해 **rwl** 을 지정해야 합니다. **rwl** 값은 다음과 같은 권한을 제공합니다.

- 읽기
- 쓰기
- 목록

## <a name="performance-considerations"></a>성능 고려 사항

확장 이벤트를 집중적으로 사용할 경우 전체 시스템에 안정적인 메모리보다 더 많은 활성 메모리가 누적되는 시나리오가 있습니다. 따라서 Azure SQL Database는 이벤트 세션이 누적할 수 있는 활성 메모리의 양에 대한 한도를 동적으로 설정 및 조정합니다. 많은 요소가 동적 계산에 활용됩니다.

Azure SQL Database의 XEvent 세션에서 사용할 수 있는 메모리에 대한 한도가 있습니다.
  - DTU 구매 모델의 단일 Azure SQL Database에서 각 데이터베이스는 최대 128MB를 사용할 수 있습니다. 이는 프리미엄 계층에서만 256MB로 증가합니다.
  - vCore 구매 모델의 단일 Azure SQL Database에서 각 데이터베이스는 최대 128MB를 사용할 수 있습니다.
  - 탄력적 풀에서 개별 데이터베이스는 단일 데이터베이스 제한에 의해 제한되며 총 512MB를 초과할 수 없습니다.

메모리 최대 한도가 적용되었다는 오류 메시지가 표시될 경우 다음과 같은 방법으로 해결할 수 있습니다.

- 실행하는 동시 이벤트 세션 수를 줄입니다.
- 이벤트 세션에 대한 **CREATE** 및 **ALTER** 문을 통해 **MAX\_MEMORY** 절에 지정하는 메모리의 양을 줄입니다.

### <a name="network-latency"></a>네트워크 대기 시간

Azure Storage BLOB에 데이터를 유지하는 동안 **이벤트 파일** 대상에서 네트워크 지연 또는 오류가 발생할 수 있습니다. 네트워크 통신이 완료될 때까지 기다리는 동안 Azure SQL Database의 다른 이벤트가 지연될 수 있습니다. 이 지연으로 인해 워크로드가 느려질 수 있습니다.

- 이러한 성능 위험을 줄이려면 이벤트 세션 정의에서 **EVENT_RETENTION_MODE** 옵션을 **NO_EVENT_LOSS** 로 설정하지 마십시오.

## <a name="related-links"></a>관련 링크

- [Azure Storage Cmdlet](/powershell/module/Azure.Storage)
- [Azure Storage와 함께 Azure PowerShell 사용](/powershell/module/az.storage/)
- [.NET에서 Blob Storage를 사용하는 방법](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL(Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION(Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- Azure SQL Database에 대한 매개 변수로 범위가 좁혀지는 Azure *서비스 업데이트* 웹 페이지:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->