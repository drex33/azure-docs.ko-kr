---
title: 로그 구성 및 액세스 - 유연한 서버 - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 유연한 서버의 데이터베이스 로그에 액세스하는 방법
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: cbc4b05b4ebf9d48a8d1933b77fe21cf021a6b45
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271140"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버에서 로그 구성 및 액세스



PostgreSQL 로그는 유연한 서버의 모든 노드에서 사용할 수 있습니다. 스토리지 서버 또는 분석 서비스에 로그를 보낼 수 있습니다. 로그는 구성 오류 및 비정상적인 성능 문제를 인지하고, 해결하며, 복구하는 데 사용됩니다.

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

Azure Portal, CLI, REST API 및 PowerShell을 사용하여 Postgres 서버에 진단 설정을 사용할 수 있습니다. 선택할 로그 범주는 **PostgreSQLLogs** 입니다.

Azure Portal을 사용하여 리소스 로그를 사용하려면 다음 단계를 수행합니다.

1. 포털에서 Postgres 서버의 탐색 메뉴에서 *진단 설정* 으로 이동합니다.
   
2. *진단 설정 추가* 를 선택합니다.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="진단 설정 추가 단추":::

3. 이 설정의 이름을 정합니다. 

4. 선호하는 엔드포인트(스토리지 계정, 이벤트 허브, 로그 분석)을 선택합니다. 

5. 로그 유형 **PostgreSQLLogs** 를 선택합니다.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="PostgreSQL 로그 선택":::

7. 설정을 저장합니다.

PowerShell, CLI 또는 REST API를 사용하여 리소스 로그를 사용하려면 [진단 설정](../../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.

### <a name="access-resource-logs"></a>리소스 로그 액세스

로그에 액세스하는 방법은 선택한 엔드포인트에 따라 다릅니다. Azure Storage의 경우에는 [로그 스토리지 계정](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) 문서를 참조하세요. Event Hub의 경우에는 [Azure 로그 스트리밍](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) 문서를 참조하세요.

Azure Monitor 로그의 경우 선택한 작업 영역으로 로그가 전송됩니다. Postgres 로그는 **AzureDiagnostics** 컬렉션 모드를 사용하므로, AzureDiagnostics 테이블에서 쿼리될 수 있습니다. 테이블의 필드는 아래에 설명되어 있습니다. 쿼리와 경고에 대한 자세한 정보는 [Azure Monitor 로그 쿼리](../../azure-monitor/logs/log-query-overview.md) 개요에서 알아보세요.

다음은 시작해볼 수 있는 쿼리입니다. 쿼리를 기반으로 경고를 구성할 수 있습니다.

마지막 날에 특정 서버에 대한 모든 Postgres 로그를 검색합니다

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

모든 localhost가 아닌 연결 시도를 검색합니다

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

위의 쿼리는 이 작업 영역에 있는 Postgres 서버 로깅에 대해 최근 6시간 동안의 결과를 표시합니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics 쿼리 시작](../../azure-monitor/logs/log-analytics-tutorial.md)
- [Azure Event Hubs](../../event-hubs/event-hubs-about.md)에 대해 알아봅니다