---
title: SQL 인사이트 문제 해결(미리 보기)
description: Azure Monitor의 SQL 인사이트 문제 해결
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 53940c21a96da9b763a0b2f25400fb13cbba7098
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119682"
---
# <a name="troubleshooting-sql-insights-preview"></a>SQL 인사이트 문제 해결(미리 보기)
SQL 인사이트의 데이터 수집 문제를 해결하려면 **프로필 관리** 탭에서 모니터링 컴퓨터의 상태를 확인합니다. 상태는 다음 중 하나입니다.

- 수집 
- 수집 중 아님 
- 수집 오류 발생 
 
**상태** 를 클릭하여 로그를 확인하고 문제를 해결하는 데 도움이 될 수 있는 자세한 정보를 확인합니다. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="컴퓨터 상태 모니터링":::

## <a name="not-collecting-state"></a>수집 중 아님 상태 
최근 10분 동안 SQL의 *InsightsMetrics* 에 데이터가 없으면 모니터링 컴퓨터의 상태는 *수집 중 아님* 입니다. 

> [!NOTE]
> 지원되는 [SQL 버전](sql-insights-overview.md#supported-versions)에서 데이터를 수집하려고 하는지 확인하세요. 예를 들어 유효한 프로필 및 연결 문자열을 사용하여 데이터를 수집하려고 하지만 지원되지 않는 Azure SQL Database 버전에서 데이터를 수집하려고 하면 수집하지 않음 상태가 됩니다.

SQL 인사이트는 다음 쿼리를 사용하여 이 정보를 검색합니다.

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

문제의 근본 원인을 식별하는 데 도움이 되는 Telegraf 로그가 있는지 확인합니다. 로그 항목이 있는 경우 *수집 중 아님* 을 클릭하고 일반적인 문제에 대한 로그 및 문제 해결 정보를 확인할 수 있습니다. 


로그가 없는 경우 모니터링 가상 머신의 로그에서 두 개의 가상 머신 확장으로 설치된 다음 서비스를 확인해야 합니다.

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - 서비스: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - 서비스: wli 
  - 서비스: ms-telegraf 
  - 서비스: td-agent-bit-wli 
  - 설치 오류를 확인하기 위한 확장 로그: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>wli 서비스 로그 

서비스 로그: `/var/log/wli.log`

최근 로그를 보려면 `tail -n 100 -f /var/log/wli.log`를 실행합니다.
 

다음 오류 로그가 표시되면 **mdsd** 서비스에 문제가 있음을 나타냅니다.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Telegraf 서비스 로그 

서비스 로그: `/var/log/ms-telegraf/telegraf.log`

최근 로그를 보려면 `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`를, 최근 오류 및 경고 로그를 보려면 `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`를 실행합니다.

 Telegraf에서 사용하는 구성은 wli 서비스에서 생성되고 `/etc/ms-telegraf/telegraf.d/wli`에 배치됩니다.
 
잘못된 구성이 생성된 경우 ms-telegraf 서비스가 시작되지 않을 수 있습니다. `service ms-telegraf status` 명령을 사용하여 ms-telegraf 서비스가 실행 중인지 확인합니다.

telegraf service의 오류 메시지를 확인하려면 다음 명령을 사용하여 수동으로 실행합니다. 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd 서비스 로그 

Azure Monitor 에이전트의 [필수 구성 요소](../agents/azure-monitor-agent-install.md#prerequisites)를 확인합니다. 


서비스 로그:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

최근 오류를 보려면 `tail -n 100 -f /var/log/mdsd.err`을 실행하십시오.

 지원을 문의해야 할 경우 다음 정보를 수집하십시오. 

- `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/`의 로그 
- `/var/log/waagent.log`의 로그 
- `/var/log/mdsd*`의 로그
- `/etc/mdsd.d/`의 파일
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>잘못된 모니터링 가상 머신 구성

*수집 중 아님* 상태인 이유 중 한 가지는 모니터링 가상 머신 구성이 잘못된 경우입니다.  기본 구성은 다음과 같습니다.

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

이 구성은 모니터링 가상 머신의 프로필 구성에 사용할 대체 토큰을 지정합니다. 또한 이렇게 하면 Azure Key Vault의 비밀을 참조할 수 있으므로 모든 구성에서 비밀 값을 유지하지 않는 것이 좋습니다.

#### <a name="secrets"></a>비밀
비밀은 Azure Key Vault에서 런타임에 값을 검색하는 토큰입니다. 비밀은 Key Vault 참조 및 비밀 이름 쌍으로 정의됩니다. 이를 통해 Azure Monitor는 비밀의 동적 값을 가져오고 이를 다운스트림 구성 참조로 사용할 수 있습니다.

별도의 Key Vault에 저장된 비밀을 포함하여 구성의 필요에 따른 비밀을 정의할 수 있습니다.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Key Vault에 대한 액세스 권한은 모니터링 가상 머신의 관리 서비스 ID에 제공됩니다. Azure Monitor에는 Key Vault에서 최소한 가상 머신에 대해 제공하는 비밀 권한이 있어야 합니다. Azure Portal, PowerShell, CLI, 또는 Resource Manager 템플릿에서 사용하도록 설정할 수 있습니다.

#### <a name="parameters"></a>매개 변수
매개 변수는 JSON 템플릿을 통해 프로필 구성에서 참조할 수 있는 토큰입니다. 매개 변수에는 이름과 값이 있습니다. 값은 개체 및 배열을 포함하는 모든 JSON 형식일 수 있습니다. 매개 변수는 `.Parameters.<name>`과 같은 이름 규칙을 사용하는 프로필 구성에서 참조됩니다.

매개 변수는 동일한 규칙을 사용하는 Key Vault의 비밀을 참조할 수 있습니다. 예를 들어 `sqlAzureConnections`은 `$telegrafPassword` 규칙을 사용하는 `telegrafPassword` 비밀을 참조합니다.

런타임에서는 모든 매개 변수 및 비밀이 확인되고 프로필 구성과 병합되어 컴퓨터에서 사용할 실제 구성을 생성합니다.

> [!NOTE]
> `sqlAzureConnections`, `sqlVmConnections`, `sqlManagedInstanceConnections`의 매개 변수 이름은 이름에 대해 제공할 연결 문자열이 없더라도 모두 구성에 필요합니다.


## <a name="collecting-with-errors-state"></a>수집 오류 발생 상태
하나 이상의 *InsightsMetrics* 로그가 있지만 *작업* 테이블에 오류가 있는 경우 모니터링 컴퓨터는 상태 수집 중에 *수집 오류 발생* 상태가 됩니다.

SQL 인사이트는 다음 쿼리를 사용하여 이 정보를 검색합니다.

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> 'WorkloadDiagnosticLogs' 데이터 형식에 데이터가 표시되지 않는 경우에는 모니터링 프로필을 업데이트하여 이 데이터를 저장해야 할 수 있습니다.  SQL 인사이트 UX 내에서 '프로필 관리', '프로필 편집', '모니터링 프로필 업데이트'를 차례로 선택합니다.


일반적인 경우에는 로그 보기에서 문제 해결 정보를 제공합니다. 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="문제 해결 로그 보기.":::



## <a name="next-steps"></a>다음 단계

- [SQL 인사이트 사용](sql-insights-enable.md)을 통해 세부 정보를 확인하세요.
