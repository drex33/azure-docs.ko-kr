---
title: Microsoft Sentinel 쿼리 및 활동 감사 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel에서 수행된 쿼리 및 활동을 감사하는 방법을 설명합니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: a505aae6b1001d043b78e9f1acd7254506bb0a24
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758171"
---
# <a name="audit-microsoft-sentinel-queries-and-activities"></a>Microsoft Sentinel 쿼리 및 활동 감사

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 SOC(보안 작업) 작업 영역의 내부 및 외부 규정 준수 요구 사항과 같이 Microsoft Sentinel 작업 영역에서 수행된 작업 및 쿼리 실행에 대한 감사 데이터를 보는 방법을 설명합니다.

Microsoft Sentinel은 다음 액세스 권한을 제공합니다.

- 경고 규칙 편집과 같이 Microsoft Sentinel에서 수행된 모든 작업에 대한 세부 정보를 제공하는 **AzureActivity** 테이블입니다. **AzureActivity** 테이블은 특정 쿼리 데이터를 로그하지 않습니다. 자세한 내용은 [Azure 활동 로그를 사용하여 감사](#auditing-with-azure-activity-logs)를 참조하세요.

- **LAQueryLogs** 테이블은 Microsoft Sentinel에서 실행되는 쿼리를 포함하여 Log Analytics에서 실행되는 쿼리에 대한 세부 정보를 제공합니다. 자세한 내용은 [LAQueryLogs를 사용하여 감사](#auditing-with-laquerylogs)를 참조하세요.

> [!TIP]
> Microsoft Sentinel은 이 문서에 설명된 수동 쿼리 외에도 SOC 환경의 활동을 감사하는 데 도움이 되는 기본 제공 통합 문서를 제공합니다.
>
> Microsoft Sentinel **통합 문서** 영역에서 작업 **영역 감사** 통합 문서를 검색합니다.

## <a name="auditing-with-azure-activity-logs"></a>Azure 활동 로그를 사용하여 감사

Microsoft Sentinel의 감사 로그는 Azure [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 유지 관리됩니다. **여기서 AzureActivity** 테이블에는 Microsoft Sentinel 작업 영역에서 수행된 모든 작업이 포함됩니다.

Microsoft Sentinel을 사용하여 SOC 환경에서 활동을 감사할 때 **AzureActivity** 테이블을 사용할 수 있습니다.

**AzureActivity 테이블을 쿼리하려면** 다음을 수행합니다.

1. [Azure 활동](./data-connectors-reference.md#azure-activity) 데이터 원본을 연결하여 AzureActivity라는 **로그** 화면의 새 테이블에 감사 이벤트 스트리밍을 시작합니다.

1. 그런 다음 다른 테이블과 같이 KQL을 사용하여 데이터를 쿼리합니다.

    **AzureActivity** 테이블에는 Microsoft Sentinel을 비롯한 많은 서비스의 데이터가 포함되어 있습니다. Microsoft Sentinel의 데이터만 필터링하려면 다음 코드로 쿼리를 시작합니다.

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    예를 들어, 특정 분석 규칙을 편집하는 마지막 사용자를 확인하려면 다음 쿼리를 사용합니다(확인하려는 규칙의 규칙 ID로 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 대체).

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

보고해야 하는 항목에 따라 더 많은 매개 변수를 쿼리에 추가하여 **AzureActivities** 테이블을 자세히 살펴보세요. 다음 섹션에서는 **AzureActivity** 테이블 데이터를 사용하여 감사할 때 사용할 다른 샘플 쿼리를 제공합니다.

자세한 내용은 [Azure 활동 로그에 포함된 Microsoft Sentinel 데이터를 참조하세요.](#microsoft-sentinel-data-included-in-azure-activity-logs)

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>지난 24시간 동안 특정 사용자가 수행한 모든 활동 찾기

다음 **AzureActivity** 테이블 쿼리는 지난 24시간 동안 특정 Azure AD 사용자가 수행한 모든 활동을 나열합니다.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>삭제 작업 모두 찾기

다음 **AzureActivity** 테이블 쿼리는 Microsoft Sentinel 작업 영역에서 수행된 모든 삭제 작업을 나열합니다.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
```

### <a name="microsoft-sentinel-data-included-in-azure-activity-logs"></a>Azure 활동 로그에 포함된 Microsoft Sentinel 데이터

Microsoft Sentinel의 감사 로그는 Azure [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 유지 관리되며 다음과 같은 유형의 정보를 포함합니다.

|작업  |정보 유형  |
|---------|---------|
|**만든 날짜**     |경고 규칙 <br> 사례 설명 <br>인시던트 설명 <br>저장된 검색<br>관심 목록    <br>통합 문서     |
|**삭제됨**     |경고 규칙 <br>책갈피 <br>데이터 커넥터 <br>인시던트 <br>저장된 검색 <br>설정 <br>위협 인텔리전스 보고서 <br>관심 목록      <br>통합 문서 <br>워크플로  |
|**업데이트됨**     |  경고 규칙<br>책갈피 <br> 사례 <br> 데이터 커넥터 <br>인시던트 <br>인시던트 설명 <br>위협 인텔리전스 보고서 <br> 통합 문서 <br>워크플로       |
|     |         |

Azure 활동 로그를 이용하여 사용자 권한 부여 및 라이선스를 확인할 수도 있습니다.

예를 들어 다음 표에는 로그 데이터를 가져온 특정 리소스와 Azure 활동 로그에서 찾은 선택한 작업이 나열되어 있습니다.

|작업 이름| 리소스 종류|
|----|----|
|통합 문서 만들기 또는 업데이트 |Microsoft.Insights/workbooks|
|통합 문서 삭제 |Microsoft.Insights/workbooks|
|워크플로 설정 |Microsoft.Logic/workflows|
|워크플로 삭제 |Microsoft.Logic/workflows|
|저장된 검색 만들기 |Microsoft.OperationalInsights/workspaces/savedSearches|
|저장된 검색 삭제 |Microsoft.OperationalInsights/workspaces/savedSearches|
|경고 규칙 업데이트 |Microsoft.SecurityInsights/alertRules|
|경고 규칙 삭제 |Microsoft.SecurityInsights/alertRules|
|경고 규칙 응답 활동 업데이트 |Microsoft.SecurityInsights/alertRules/actions|
|경고 규칙 응답 활동 삭제 |Microsoft.SecurityInsights/alertRules/actions|
|책갈피 업데이트 |Microsoft.SecurityInsights/bookmarks|
|책갈피 삭제 |Microsoft.SecurityInsights/bookmarks|
|사례 업데이트 |Microsoft.SecurityInsights/Cases|
|사례 조사 업데이트 |Microsoft.SecurityInsights/Cases/investigations|
|사례 설명 만들기 |Microsoft.SecurityInsights/Cases/comments|
|데이터 커넥터 업데이트 |Microsoft.SecurityInsights/dataConnectors|
|데이터 커넥터 삭제 |Microsoft.SecurityInsights/dataConnectors|
|업데이트 설정 |Microsoft.SecurityInsights/settings|
| | |

자세한 내용은 [Azure 활동 로그 이벤트 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요.

## <a name="auditing-with-laquerylogs"></a>LAQueryLogs를 사용하여 감사

**LAQueryLogs** 테이블은 Log Analytics에서 실행되는 로그 쿼리에 대한 세부 정보를 제공합니다. Log Analytics는 Microsoft 센티널의 기본 데이터 저장소로 사용 되므로 Microsoft 센티널 작업 영역에서 LAQueryLogs 데이터를 수집 하도록 시스템을 구성할 수 있습니다.

LAQueryLogs 데이터에 포함되는 정보는 다음과 같습니다.

- 쿼리가 실행된 시기
- Log Analytics에서 쿼리를 실행한 사람
- Microsoft 센티널과 같은 Log Analytics에서 쿼리를 실행 하는 데 사용 된 도구
- 쿼리 텍스트 자체
- 각 쿼리 실행의 성능 데이터

> [!NOTE]
> - **Laquerylogs** 테이블은 Microsoft 센티널의 로그 블레이드에서 실행 된 쿼리만 포함 합니다. 여기에는 **조사 Graph** 또는 Microsoft 센티널 **구하기** 페이지를 사용 하 여 예약 된 분석 규칙에 의해 실행 되는 쿼리가 포함 되지 않습니다.
> - 쿼리가 실행되는 시간 사이에 약간의 지연이 있을 수 있으며, 데이터가 **LAQueryLogs** 테이블에 채워집니다. 감사 데이터에 대한 **LAQueryLogs** 테이블을 쿼리하려면 5분 정도 대기하는 것이 좋습니다.

**LAQueryLogs 테이블을 쿼리하려면** 다음을 수행합니다.

1. Log Analytics 작업 영역에서 **LAQueryLogs** 테이블은 기본적으로 사용하도록 설정되어 있지 않습니다. Microsoft 센티널에서 감사할 때 **laquerylogs** 데이터를 사용 하려면 먼저 Log Analytics 작업 영역의 **진단 설정** 영역에서 **laquerylogs** 를 사용 하도록 설정 합니다.

    자세한 내용은 [Azure Monitor 로그의 쿼리 감사](../azure-monitor/logs/query-audit.md)를 참조하세요.

1. 그런 다음 다른 테이블과 같이 KQL을 사용하여 데이터를 쿼리합니다.

    예를 들어, 다음 쿼리는 지난 주에 실행된 쿼리 수를 날짜별로 보여 줍니다.

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

다음 섹션에서는 Microsoft 센티널를 사용 하 여 SOC 환경에서 작업을 감사할 때 **Laquerylogs** 테이블에서 실행 하는 더 많은 샘플 쿼리를 보여 줍니다.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>응답이 "OK"가 아닌 경우 실행되는 쿼리 수

다음 **LAQueryLogs** 테이블 쿼리는 **200 OK** 의 HTTP 응답 이외의 다른 응답을 수신한, 실행된 쿼리 수를 보여 줍니다. 예를 들어 이 수에는 실행하지 못한 쿼리가 포함됩니다.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>CPU를 많이 사용하는 쿼리에 대한 사용자 표시

다음 **LAQueryLogs** 테이블 쿼리는 사용한 CPU 및 쿼리 시간 길이를 기준으로 CPU를 가장 많이 사용하는 쿼리를 실행한 사용자를 나열합니다.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>지난 주에 가장 많은 쿼리를 실행한 사용자 표시

다음 **LAQueryLogs** 테이블 쿼리는 지난 주에 가장 많은 쿼리를 실행한 사용자를 나열합니다.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-microsoft-sentinel-activities"></a>Microsoft 센티널 활동에 대 한 경고 구성

Microsoft 센티널 감사 리소스를 사용 하 여 사전 경고를 만들 수 있습니다.

예를 들어 Microsoft 센티널 작업 영역에 중요 한 테이블이 있는 경우 다음 쿼리를 사용 하 여 해당 테이블이 쿼리 될 때마다 사용자에 게 알립니다.

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```

## <a name="monitor-microsoft-sentinel-with-workbooks-rules-and-playbooks"></a>통합 문서, 규칙 및 플레이 북으로 Microsoft 센티널 모니터링

Microsoft 센티널의 고유한 기능을 사용 하 여 Microsoft 센티널 내에서 발생 하는 이벤트 및 작업을 모니터링할 수 있습니다.

- **통합 문서를 사용하여 모니터링** 작업 영역 활동을 모니터링하기 위해 다음 통합 문서가 빌드되었습니다.

  - **작업 영역 감사** 환경에서 작업을 수행하는 사용자, 수행된 작업 등에 대한 정보를 포함합니다.
  - **분석 효율성** 사용 중인 분석 규칙, 가장 많이 사용되는 MITRE 전술 및 규칙에서 생성된 인시던트에 대한 인사이트를 제공합니다.
  - **보안 운영 효율성** SOC 팀 성능, 열린 인시던트, 종결되는 인시던트 등에 대한 메트릭을 제공합니다. 이 통합 문서를 사용하면 팀 성능을 표시하고 주의가 필요한 영역을 강조 표시할 수 있습니다.
  - **데이터 수집 상태 모니터링** 중단되거나 중지된 수집을 감시하는 데 도움이 됩니다.

  자세한 내용은 [일반적으로 사용 되는 Microsoft 센티널 통합 문서](top-workbooks.md)를 참조 하세요.

- **수집 지연 감시**  수집 지연에 대해 궁금한 사항이 있는 경우 [분석 규칙에서 변수를 설정](ingestion-delay.md)하여 지연을 나타냅니다.

  예를 들어 다음 분석 규칙은 결과에 중복이 포함되지 않도록 하고, 규칙을 실행할 때 로그가 누락되지 않도록 하는 데 도움이 될 수 있습니다.

  ```kusto
  let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
  - Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
  ```

  자세한 내용은 [automation 규칙을 사용 하 여 Microsoft 센티널에서 인시던트 처리 자동화](automate-incident-handling-with-automation-rules.md)를 참조 하세요.

- [커넥터 상태 푸시 알림 솔루션](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) 플레이북을 사용하여 **데이터 커넥터 상태를 모니터링** 하여 중단 또는 중지된 수집을 감시하고, 커넥터가 데이터 수집을 중지하거나 머신에서 보고를 중지한 경우 알림을 보냅니다.

## <a name="next-steps"></a>다음 단계

Microsoft 센티널에서 **작업 영역 감사** 통합 문서를 사용 하 여 SOC 환경에서 작업을 감사 합니다.

자세한 내용은 [데이터 시각화 및 모니터링](monitor-your-data.md)을 참조하세요.
