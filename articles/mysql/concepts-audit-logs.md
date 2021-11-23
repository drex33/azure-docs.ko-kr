---
title: Audit logs - Azure Database for MySQL
description: Azure Database for MySQL에서 사용할 수 있는 감사 로그와, 로깅 수준을 활성화하는 데 사용할 수 있는 매개 변수에 대해 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: b33394821ce71d9ee9269a9d381af68d83e955ee
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957320"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL의 감사 로그

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL에서는 사용자에게 감사 로그를 제공합니다. 감사 로그는 데이터베이스 수준 작업을 추적하는 데 사용할 수 있으며, 일반적으로 규정 준수에 사용됩니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않고 최소 데이터 양이 수집 되도록 감사 목적에 필요한 이벤트 유형과 사용자를 기록 하는 것이 좋습니다. 

기본값으로 감사 로그는 비활성화됩니다. 사용하려면 `audit_log_enabled`을 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- `audit_log_events`: 이벤트가 기록되도록 제어합니다. 특정 감사 이벤트는 아래 표를 참조하세요.
- `audit_log_include_users`: 로깅에 포함할 MySQL 사용자입니다. 이 매개 변수의 기본값은 비어 있으며, 로깅에 모든 사용자가 포함됩니다. 이는 `audit_log_exclude_users`보다 우선 순위가 높습니다. 매개 변수의 최대 길이는 512자입니다.
- `audit_log_exclude_users`: 로깅에서 제외할 MySQL 사용자입니다. 매개 변수의 최대 길이는 512자입니다.

> [!NOTE]
> `audit_log_include_users`는 `audit_log_exclude_users`보다 우선 순위가 높습니다. 예를 들어 `audit_log_include_users` = `demouser` 및 `audit_log_exclude_users` = `demouser`인 경우 `audit_log_include_users` 의 우선 순위가 높으므로 사용자가 감사 로그에 포함됩니다.

| **이벤트** | **설명** |
|---|---|
| `CONNECTION` | - 연결 시작(성공 또는 실패) <br> - 사용자가 세션 중에 다른 사용자/암호를 사용하여 다시 인증 <br> - 연결 종료 |
| `DML_SELECT`| SELECT 쿼리 |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE 쿼리 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | “DROP DATABASE”와 같은 쿼리 |
| `DCL` | “GRANT PERMISSION”과 같은 쿼리 |
| `ADMIN` | “SHOW STATUS”와 같은 쿼리 |
| `GENERAL` | DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 ADMIN의 모든 항목 |
| `TABLE_ACCESS` | - MySQL 5.7 및 MySQL 8.0에서 사용 가능 <br> - SELECT 또는 INSERT INTO ... SELECT와 같은 테이블 읽기 문 <br> - DELETE 또는 TRUNCATE TABLE과 같은 테이블 삭제 문 <br> - INSERT 또는 REPLACE와 같은 테이블 삽입 문 <br> - UPDATE와 같은 테이블 업데이트 문 |

## <a name="access-audit-logs"></a>감사 로그 액세스

감사 로그는 Azure Monitor 진단 로그와 통합됩니다. MySQL 서버에서 감사 로그를 사용하도록 설정하면 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 감사 로그를 내보낼 수 있습니다. Azure Portal에서 진단 로그를 사용하도록 설정하는 방법에 대한 자세한 내용은 [감사 로그 포털 문서](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)를 참조하세요.

## <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

다음 섹션에서는 이벤트 유형을 기반으로 하는 MySQL 감사 로그로 출력되는 것을 설명합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

### <a name="connection"></a>연결

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER`(MySQL 5.7에서만 사용 가능) |
| `connection_id_d` | MySQL에 의해 생성된 고유 연결 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행하는 사용자의 이름 |
| `db_s` | 연결 대상 데이터베이스의 이름 |
| `\_ResourceId` | 리소스 URI |

### <a name="general"></a>일반

아래 스키마는 GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL 및 ADMIN 이벤트 유형에 적용됩니다.

> [!NOTE]
> `sql_text`의 경우 로그가 2048자를 초과하면 잘립니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT`(MySQL 5.6에서만 사용 가능) |
| `event_time` | 쿼리 시작 시간(UTC 타임스탬프 기준) |
| `error_code_d` | 쿼리가 실패한 경우의 오류 코드. `0` 은 오류가 없음을 의미합니다. |
| `thread_id_d` | 쿼리를 실행한 스레드의 ID |
| `host_s` | 비어 있음 |
| `ip_s` | MySQL에 연결하는 클라이언트의 IP 주소 |
| `user_s` | 쿼리를 실행하는 사용자의 이름 |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

### <a name="table-access"></a>테이블 액세스

> [!NOTE]
> 테이블 액세스 로그는 MySQL 5.7에 대해서만 출력됩니다.<br>`sql_text`의 경우 로그가 2048자를 초과하면 잘립니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 서버의 이름 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` 또는 `DELETE` |
| `connection_id_d` | MySQL에 의해 생성된 고유 연결 ID |
| `db_s` | 액세스된 데이터베이스의 이름 |
| `table_s` | 액세스된 테이블의 이름 |
| `sql_text_s` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor 로그에서 로그 분석

감사 로그가 진단 로그를 통해 Azure Monitor 로그로 파이프되면 감사된 이벤트에 대한 추가 분석을 수행할 수 있습니다. 다음은 시작하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 아래를 서버 이름으로 업데이트해야 합니다.

- 특정 서버에 대한 GENERAL 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 특정 서버에 대한 CONNECTION 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 특정 서버에서 감사된 이벤트 요약

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 특정 서버의 감사 이벤트 유형 분포 그래프 작성

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 감사 로그를 사용하도록 설정된 진단 로그를 사용하는 모든 MySQL 서버에서 감사된 이벤트 나열

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 감사 로그를 구성하는 방법](howto-configure-audit-logs-portal.md)
