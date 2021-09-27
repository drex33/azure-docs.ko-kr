---
title: SQL 인사이트 문제 해결(미리 보기)
description: Azure Monitor SQL 인사이트 문제를 해결하는 방법을 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: b76e08103b7af7591e7f71d3fe40e1e018a45665
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610658"
---
# <a name="troubleshoot-sql-insights-preview"></a>SQL 인사이트 문제 해결(미리 보기)
SQL 인사이트에서 데이터 수집 문제를 해결하려면 **프로필 관리** 탭에서 모니터링 머신의 상태를 확인합니다. 상태는 다음과 같습니다.

- **수집** 
- **수집 중 아님** 
- **수집 오류 발생** 
 
상태를 선택하여 문제를 해결하는 데 도움이 될 수 있는 로그 및 자세한 정보를 확인합니다. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="모니터링 머신 상태를 보여 주는 스크린샷.":::

## <a name="status-not-collecting"></a>상태: 수집 중이 아닙니다. 
최근 10분 동안 SQL *대한 InsightsMetrics에* 데이터가 없는 경우 모니터링 머신의 상태는 **수집되지 않음입니다.** 

> [!NOTE]
> [지원되는 버전의 SQL](sql-insights-overview.md#supported-versions)에서 데이터를 수집해야 합니다. 예를 들어 유효한 프로필 및 연결 문자열이 있지만 지원되지 않는 버전의 Azure SQL Database 데이터를 수집하려고 하면 **수집되지 않음 상태가 발생합니다.**

SQL 인사이트는 다음 쿼리를 사용하여 이 정보를 검색합니다.

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Telegraf의 로그가 문제의 근본 원인을 식별하는 데 도움이 되는지 확인합니다. 로그 항목이 있는 경우 **수집 안 을** 선택하고 로그를 확인하고 일반적인 문제에 대한 문제 해결 정보를 확인할 수 있습니다. 

로그 항목이 없는 경우 모니터링 가상 머신의 로그에서 두 개의 가상 머신 확장에 의해 설치된 다음 서비스에 대한 로그를 확인해야 합니다.

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - 서비스: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - 서비스: wli 
  - 서비스: ms-telegraf 
  - 서비스: td-agent-bit-wli 
  - 설치 실패를 확인하는 확장 로그: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>wli 서비스 로그 

서비스 로그: `/var/log/wli.log`

최근 로그를 보려면 `tail -n 100 -f /var/log/wli.log`를 실행합니다.
 
다음 오류 로그가 표시되면 mdsd 서비스에 문제가 있습니다.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Telegraf 서비스 로그 

서비스 로그: `/var/log/ms-telegraf/telegraf.log`

최근 로그를 보려면 `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`를 실행합니다.

최근 오류 및 경고 로그를 보려면 다음을 수행합니다. `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

telegraf에서 사용하는 구성은 wli 서비스에 의해 생성되고 다음 위치에 배치됩니다. `/etc/ms-telegraf/telegraf.d/wli`
 
잘못된 구성이 생성되면 ms-telegraf 서비스가 시작되지 않을 수 있습니다. 다음 명령을 사용하여 ms-telegraf 서비스가 실행 중인지 확인합니다. `service ms-telegraf status`

telegraf 서비스에서 오류 메시지를 보려면 다음 명령을 사용하여 수동으로 실행합니다. 

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

수집 안 **됨** 상태의 한 가지 원인은 모니터링 가상 머신에 대한 잘못된 구성입니다. 기본 구성은 다음과 같습니다.

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

이 구성은 모니터링 가상 머신의 프로필 구성에 사용할 대체 토큰을 지정합니다. 또한 Azure Key Vault 비밀을 참조할 수 있으므로 비밀 값을 구성에 유지할 필요가 없습니다(강력하게 권장됨).

#### <a name="secrets"></a>비밀
비밀은 Azure Key Vault에서 런타임에 값이 검색되는 토큰입니다. 비밀은 키 자격 증명 모음 참조 및 비밀 이름 쌍으로 정의됩니다. 이 정의를 사용하면 Azure Monitor 비밀의 동적 값을 얻고 다운스트림 구성 참조에서 사용할 수 있습니다.

별도의 키 자격 증명 모음에 저장된 비밀을 포함하여 구성에서 필요한 만큼 비밀을 정의할 수 있습니다.

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

키 자격 증명 모음에 액세스할 수 있는 권한은 모니터링 가상 머신의 관리 ID에 제공됩니다. Azure Monitor 키 자격 증명 모음이 가상 머신에 대한 권한을 얻기 위해 적어도 비밀을 제공해야 합니다. Azure Portal, PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿에서 사용하도록 설정할 수 있습니다.

#### <a name="parameters"></a>매개 변수
매개 변수는 JSON 템플릿을 통해 프로필 구성에서 참조할 수 있는 토큰입니다. 매개 변수에는 이름과 값이 있습니다. 값은 개체 및 배열을 포함한 모든 JSON 형식일 수 있습니다. 매개 변수는 이 규칙의 이름을 통해 프로필 구성에서 `.Parameters.<name>` 참조됩니다.

매개 변수는 동일한 규칙을 사용하여 Key Vault 비밀을 참조할 수 있습니다. 예를 들어 는 `sqlAzureConnections` 규칙을 사용하여 비밀을 `telegrafPassword` `$telegrafPassword` 참조합니다.

런타임에 모든 매개 변수와 비밀이 확인되고 프로필 구성과 병합되어 컴퓨터에서 사용할 실제 구성을 생성합니다.

> [!NOTE]
> 일부 매개 변수에 대한 연결 문자열을 제공하지 않더라도 , 및 의 매개 변수 이름은 `sqlAzureConnections` `sqlVmConnections` 모두 `sqlManagedInstanceConnections` 구성에 필요합니다.


## <a name="status-collecting-with-errors"></a>상태: 오류와 함께 수집
하나 이상의 *InsightsMetrics* 로그가 있지만 *작업* 테이블에 오류도 있는 경우 모니터링 컴퓨터에는 **오류와 함께 수집** 중 상태가 표시됩니다.

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
> 데이터 형식에 데이터가 표시되지 않으면 `WorkloadDiagnosticLogs` 이 데이터를 저장하도록 모니터링 프로필을 업데이트해야 할 수 있습니다.  SQL 인사이트 UX 내에서 **프로필 관리 프로필** 편집 모니터링  >  **프로필**  >  **업데이트를** 선택합니다.

일반적인 경우에는 로그 보기에서 문제 해결 정보를 제공합니다. 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="문제 해결 로그 보기.":::

## <a name="next-steps"></a>다음 단계

- [SQL 인사이트 사용](sql-insights-enable.md)을 통해 세부 정보를 확인하세요.
