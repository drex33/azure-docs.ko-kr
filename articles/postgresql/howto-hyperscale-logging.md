---
title: 로그 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 데이터베이스 로그에 액세스하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/20/2021
ms.openlocfilehash: c65d2947e6e0f9505f1827ec8dbb32f59855d332
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633902"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL의 로그 – 하이퍼스케일(Citus)

PostgreSQL 데이터베이스 서버 로그는 하이퍼스케일(Citus) 서버 그룹의 모든 노드에서 사용할 수 있습니다. 스토리지 서버 또는 분석 서비스에 로그를 보낼 수 있습니다. 로그는 구성 오류 및 비정상적인 성능 문제를 인지하고, 해결하며, 복구하는 데 사용됩니다.

## <a name="capturing-logs"></a>로그 캡처

하이퍼스케일(Citus) 코디네이터 또는 작업자 노드에 대한 PostgreSQL 로그에 액세스하려면 PostgreSQLLogs 진단 설정을 사용하도록 설정해야 합니다. Azure Portal에서 **진단 설정** 을 열고 **+ 진단 설정 추가** 를 선택합니다.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="진단 설정 추가 단추":::

새 진단 설정의 이름을 선택한 후 **PostgreSQLLogs** 상자와 **Log Analytics 작업 영역에 보내기** 상자를 차례로 선택합니다.  그런 다음 **저장** 을 선택합니다.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL 로그 선택":::

## <a name="viewing-logs"></a>로그 보기

로그를 보고 필터링하려면 Kusto 쿼리를 사용합니다. Azure Portal에서 하이퍼스케일(Citus) 서버 그룹용 **로그** 를 엽니다. 쿼리 선택 대화 상자가 나타나면 닫습니다.

:::image type="content" source="media/howto-hyperscale-logging/logs-dialog.png" alt-text="대화 상자가 열려 있는 로그 페이지":::

그러면 쿼리를 입력할 수 있는 입력란이 표시됩니다.

:::image type="content" source="media/howto-hyperscale-logging/logs-query.png" alt-text="로그를 쿼리하는 입력란":::

다음 쿼리를 입력하고 **실행** 단추를 선택합니다.

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
```

위의 쿼리는 모든 노드의 심각도 및 타임스탬프와 함께 로그 메시지를 나열합니다. `where` 절을 추가하여 결과를 필터링할 수 있습니다. 예를 들어, 코디네이터 노드의 오류만 보려면 다음과 같이 오류 수준 및 서버 이름을 필터링합니다.

```kusto
AzureDiagnostics
| project TimeGenerated, Message, errorLevel_s, LogicalServerName_s
| where LogicalServerName_s == 'example-server-group-c'
| where errorLevel_s == 'ERROR'
```

위의 예에 있는 서버 이름을 사용 중인 서버 이름으로 바꿉니다. 코디네이터 노드 이름에는 `-c` 접미사가 있고 작업자 노드는 `-w0`, `-w1` 등의 접미사를 사용하여 이름이 지정됩니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics 쿼리 시작](../azure-monitor/logs/log-analytics-tutorial.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)에 대해 알아봅니다
