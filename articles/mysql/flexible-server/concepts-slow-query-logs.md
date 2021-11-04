---
title: 느린 쿼리 로그 - Azure Database for MySQL - 유연한 서버
description: Azure Database for MySQL 유연한 서버에서 사용할 수 있는 느린 쿼리 로그를 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 15757c3a7e394dcc52c83e8eeef54d8b44b97a34
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468198"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유동 서버에서 느린 쿼리 로그

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버에서 느린 쿼리 로그는 사용자가 구성하고 액세스하는 데 사용할 수 있습니다. 느린 쿼리 로그는 기본적으로 사용하지 않도록 설정되어 있으므로 문제 해결 중 성능 병목 상태를 식별하는 데 도움이 됩니다.

MySQL 느린 쿼리 로그에 대한 자세한 내용은 MySQL 엔진 설명서의 [느린 쿼리 로그 섹션](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)을 참조하세요.

## <a name="configure-slow-query-logging"></a>느린 쿼리 로깅 구성
기본적으로 느린 쿼리 로그는 사용하지 않도록 설정되어 있습니다. 로그를 사용하려면, `slow_query_log` 서버 매개 변수를 *ON* 으로 설정합니다. 이 작업은 Azure Portal 또는 Azure CLI를 사용하여 수행할 수 있습니다. <!-- add link to server parameter-->.

느린 쿼리 로깅 동작을 제어하기 위해 조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 완료하는 데 `long_query_time`(초 단위)보다 오래 걸리면 쿼리를 로그합니다. 기본값은 10초입니다.
- **log_slow_admin_statements**: 관리문(예: `ALTER_TABLE`, `ANALYZE_TABLE`)이 로그되는지 여부를 결정합니다.
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 로그되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**: 느린 쿼리 로그에 쓸 수 있는 인덱싱되지 않은 쿼리의 수를 제한합니다. 이 매개 변수는 `log_queries_not_using_indexes`가 *ON* 으로 설정된 경우에 적용됩니다.

> [!IMPORTANT]
>테이블이 인덱싱되지 않은 경우, 이러한 인덱싱되지 않은 테이블에 대해 실행되는 모든 쿼리가 느린 쿼리 로그에 기록되기 때문에 `log_queries_not_using_indexes`과 `log_throttle_queries_not_using_indexes` 매개 변수를 **ON** 으로 설정하면 MySQL 성능에 영향을 줄 수 있습니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MySQL [느린 쿼리 로그 설명서](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)를 참조하세요.

## <a name="access-slow-query-logs"></a>느린 쿼리 로그 액세스

느린 쿼리 로그는 Azure Monitor 진단 설정과 통합됩니다. MySQL 유연한 서버에서 느린 쿼리 로그를 사용하도록 설정하면 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 느린 로그를 내보낼 수 있습니다. 진단 설정에 관한 자세한 정보는 [진단 로그 설명서](../../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요. Azure Portal에서 진단 설정을 사용하도록 설정하는 방법에 대한 자세한 정보는 [느린 쿼리 로그 포털 문서](tutorial-query-performance-insights.md#set-up-diagnostics)를 참조하세요.

다음 표는 느린 쿼리 로그의 출력에 대해 설명합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics`입니다. |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL`입니다. |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 서버의 이름 |
| `start_time_t` [UTC] | 쿼리가 시작된 시간 |
| `query_time_s` | 쿼리를 실행하는 데 걸린 총 시간(초) |
| `lock_time_s` | 쿼리가 잠긴 총 시간(초) |
| `user_host_s` | 사용자 이름 |
| `rows_sent_s` | 전송된 행 수 |
| `rows_examined_s` | 검사된 행 수 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | ID 삽입 |
| `sql_text_s` | 전체 쿼리 |
| `server_id_s` | 서버 ID |
| `thread_id_s` | 스레드 ID |
| `\_ResourceId` | 리소스 URI |

> [!Note]
> `sql_text_s`의 경우 로그가 2048자를 초과하면 잘립니다.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor 로그에서 로그 분석

느린 쿼리 로그가 진단 로그를 통해 Azure Monitor 로그에 전달되면 느린 쿼리를 추가로 분석할 수 있습니다. 다음은 시작하는 데 도움이 되는 몇 가지 샘플 쿼리입니다. 아래를 서버 이름으로 업데이트해야 합니다.

- 특정 서버에서 10초보다 긴 쿼리

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s
    | where query_time_d > 10
    ```

- 특정 서버에 가장 긴 쿼리 5개 나열

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s
    | order by query_time_d desc
    | take 5
    ```

- 특정 서버에서 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느린 쿼리 요약

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 특정 서버에서 느린 쿼리를 배포하는 그래프

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 진단 로그를 사용하도록 설정된 모든 MySQL 서버에서 10초보다 긴 쿼리 표시

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s
    | where query_time_d > 10
    ```

## <a name="next-steps"></a>다음 단계
- [감사 로그](concepts-audit-logs.md)에 대한 자세한 정보
- [쿼리 성능 Insight](tutorial-query-performance-insights.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->