---
title: SQL 인사이트 문제 해결(미리 보기)
description: Azure Monitor SQL 인사이트 문제를 해결하는 방법을 알아봅니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/03/2021
ms.openlocfilehash: 14531aefb95fa24adfe969b17a0d00db4b287d5f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756631"
---
# <a name="troubleshoot-sql-insights-preview"></a>SQL 인사이트 문제 해결(미리 보기)
SQL 인사이트에서 데이터 수집 문제를 해결하려면 **프로필 관리** 탭에서 모니터링 머신의 상태를 확인합니다. 상태는 다음과 같습니다.

- **수집** 
- **수집 중 아님** 
- **수집 오류 발생** 
 
상태를 선택하여 문제를 해결하는 데 도움이 될 수 있는 로그 및 자세한 정보를 확인합니다. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="모니터링 머신 상태를 보여 주는 스크린샷.":::

## <a name="status-not-collecting"></a>상태: 수집 중이 아닙니다. 
최근 10분 동안 SQL 대한 *InsightsMetrics에* 데이터가 없는 경우 모니터링 머신의 상태는 **수집되지 않음입니다.** 

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

로그 항목이 없는 경우 모니터링 가상 머신의 로그에서 두 개의 가상 머신 확장에 의해 설치된 다음 서비스를 확인합니다.

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - 서비스: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - 서비스: wli 
  - 서비스: ms-telegraf 
  - 서비스: td-agent-bit-wli 
  - 설치 오류를 확인하는 확장 로그: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

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

수집 안 **됨** 상태의 한 가지 원인은 모니터링 가상 머신에 대한 잘못된 구성입니다. 구성의 가장 간단한 형태는 다음과 같습니다.

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

이 구성에서 데이터베이스 연결 문자열에는 `$telegrafPassword` 대체 토큰이 포함됩니다. SQL Insights 이 토큰을 Key Vault 검색된 SQL 인증 암호로 대체합니다. Key Vault URI는 아래의 `telegrafPassword` 구성 섹션에 지정됩니다. `secrets`

#### <a name="secrets"></a>비밀
비밀은 Azure Key Vault에서 런타임에 값이 검색되는 토큰입니다. 비밀은 키 자격 증명 모음 URI 및 비밀 이름을 포함하는 값 쌍으로 정의됩니다. 이 정의를 사용하면 SQL Insights 런타임에 비밀 값을 얻고 다운스트림 구성에서 사용할 수 있습니다.

여러 키 자격 증명 모음에 저장된 비밀을 포함하여 필요한 만큼 비밀을 정의할 수 있습니다.

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

키 자격 증명 모음에 액세스할 수 있는 권한은 모니터링 가상 머신의 관리 ID에 제공됩니다. 이 관리 ID에는 모니터링 프로필 구성에서 참조되는 모든 Key Vault 비밀에 대한 Get 권한이 부여되어야 합니다. 이 작업은 Azure Portal, PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿에서 수행할 수 있습니다.

#### <a name="parameters"></a>매개 변수
매개 변수는 JSON 템플릿을 통해 프로필 구성에서 참조할 수 있는 토큰입니다. 매개 변수에는 이름과 값이 있습니다. 값은 개체 및 배열을 포함한 모든 JSON 형식일 수 있습니다. 매개 변수는 규칙을 사용하여 프로필 구성에서 이름으로 `.Parameters.<name>` 참조됩니다.

매개 변수는 동일한 규칙을 사용하여 Key Vault 비밀을 참조할 수 있습니다. 예를 들어 는 `sqlAzureConnections` 규칙을 사용하여 비밀을 `telegrafPassword` `$telegrafPassword` 참조합니다.

런타임에 모든 매개 변수와 비밀이 확인되고 프로필 구성과 병합되어 컴퓨터에서 사용할 실제 구성을 생성합니다.

> [!NOTE]
> 일부 매개 변수에 대한 연결 문자열을 제공하지 않더라도 , 및 의 매개 변수 이름은 `sqlAzureConnections` `sqlVmConnections` 모두 `sqlManagedInstanceConnections` 구성에 필요합니다.

## <a name="status-collecting-with-errors"></a>상태: 오류와 함께 수집
모니터링 머신은 최근 *InsightsMetrics* 로그가 하나 이상 있지만 *작업* 테이블에 오류가 있는 경우 **오류와 함께 수집** 중 상태가 됩니다.

SQL Insights 다음 쿼리를 사용하여 이 정보를 검색합니다.

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
> 에 데이터가 표시되지 않으면 `WorkloadDiagnosticLogs` 모니터링 프로필을 업데이트해야 할 수 있습니다. Azure Portal SQL Insights 내에서 **프로필 관리 프로필** 편집 모니터링  >  **프로필**  >  **업데이트를** 선택합니다.

일반적인 경우 로그 보기에서 문제 해결 팁을 제공합니다. 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="문제 해결 로그 보기.":::

## <a name="known-issues"></a>알려진 문제

SQL Insights 미리 보기 중에 다음과 같은 알려진 문제가 발생할 수 있습니다.

* **서버 또는 데이터베이스에 연결하는 동안 '로그인하지 못했습니다.' 오류가 발생했습니다.** 모니터링 VM 구성 또는 Key Vault 저장된 SQL 인증 암호에 특정 특수 문자를 사용하면 모니터링 VM이 SQL 서버 또는 데이터베이스에 연결되지 않을 수 있습니다. 이 문자 집합에는 괄호, 정사각형 및 중괄호, 달러 기호, 슬래시 및 점( `[ { ( ) } ] $ \ / .` )이 포함됩니다.

## <a name="best-practices"></a>모범 사례

* **모니터링 VM에서 Key Vault 액세스해야 합니다.** Key Vault 사용하여 SQL 인증 암호를 저장하는 경우(강력하게 권장) 네트워크 및 보안 구성을 통해 모니터링 VM이 Key Vault 액세스할 수 있도록 해야 합니다. 자세한 내용은 [방화벽 뒤에 Azure Key Vault 액세스](/azure/key-vault/general/access-behind-firewall) 및 [Azure Key Vault 네트워킹 설정 구성을 참조하세요.](/azure/key-vault/general/how-to-azure-key-vault-network-security) 모니터링 VM이 Key Vault 액세스할 수 있는지 확인하려면 VM에 연결된 SSH 세션에서 다음 명령을 실행할 수 있습니다. 액세스 토큰 및 비밀을 성공적으로 검색할 수 있어야 합니다. `[YOUR-KEY-VAULT-URL]`, `[YOUR-KEY-VAULT-SECRET]` 및 를 실제 `[YOUR-KEY-VAULT-ACCESS-TOKEN]` 값으로 대체합니다.

  ```bash
  # Get an access token for accessing Key Vault secrets
  curl 'http://[YOUR-KEY-VAULT-URL]/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true
  
  # Get Key Vault secret
  curl 'https://[YOUR-KEY-VAULT-URL]/secrets/[YOUR-KEY-VAULT-SECRET]?api-version=2016-10-01' -H "Authorization: Bearer [YOUR-KEY-VAULT-ACCESS-TOKEN]"
  ```

* **모니터링 VM에서 소프트웨어를 업데이트합니다.** 모니터링 VM에서 운영 체제 및 확장을 주기적으로 업데이트하는 것이 좋습니다. 확장에서 자동 업그레이드를 지원하는 경우 해당 옵션을 사용하도록 설정합니다.

* **이전 구성을 저장합니다.** 모니터링 프로필 또는 모니터링 VM 구성을 변경하려면 먼저 구성 데이터의 작업 복사본을 저장하는 것이 좋습니다. Azure Portal SQL Insights 페이지에서 **프로필 관리 프로필** 편집을 선택하고 현재 모니터링  >  **프로필** **구성의** 텍스트를 파일로 복사합니다. 마찬가지로 모니터링 VM에 대한 **프로필 관리**  >  **구성을** 선택하고 **현재 모니터링 구성의** 텍스트를 파일로 복사합니다. 구성 변경 후 데이터 수집 오류가 발생하는 경우 텍스트 diff 도구를 사용하여 새 구성을 알려진 작업 구성과 비교하여 컬렉션에 영향을 줄 수 있는 변경 내용을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [SQL 인사이트 사용](sql-insights-enable.md)을 통해 세부 정보를 확인하세요.
