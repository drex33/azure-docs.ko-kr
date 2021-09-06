---
title: Azure Monitor 로그와 Azure Automation State Configuration 통합
description: 이 문서에서는 Azure Automation State Configuration에서 Azure Monitor 로그로 Desired State Configuration 보고 데이터를 보내는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: fb968d527bea4b9daeed4e12f0fdea16158d90c4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530647"
---
# <a name="integrate-azure-automation-state-configuration-with-azure-monitor-logs"></a>Azure Monitor 로그와 Azure Automation State Configuration 통합

Azure Automation State Configuration은 노드 상태 데이터를 30일 동안 유지합니다. 더 오랜 기간 동안 이 데이터를 보존하려면 노드 상태 데이터를 [Azure Monitor 로그](../azure-monitor/logs/data-platform-logs.md)로 보낼 수 있습니다. Azure Portal에서 또는 PowerShell을 사용하여 노드 및 노드 구성의 개별 DSC 리소스에 대한 준수 상태를 볼 수 있습니다.

Azure Monitor 로그는 Automation State Configuration 데이터의 작동을 보다 정확히 이해하도록 하며 인시던트를 더 빠르게 해결하도록 도와줍니다. Azure Monitor 로그를 사용하여 다음을 수행할 수 있습니다.

- 관리되는 노드 및 개별 리소스에 대한 준수 정보를 가져옵니다.
- 준수 상태를 기준으로 이메일 또는 경고를 트리거합니다.
- 관리되는 노드에서 고급 쿼리를 작성합니다.
- Automation 계정에서 준수 상태를 상호 연결합니다.
- 사용자 지정 보기와 검색 쿼리를 사용하여 Runbook 결과, Runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭을 시각화합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항

Automation State Configuration 보고서를 Azure Monitor 로그로 보내려면 다음이 필요합니다.

* PowerShell [Az 모듈](/powershell/azure/new-azureps-module-az)이 설치되었습니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.
- Azure Automation 계정. 자세한 내용은 [Azure Automation에 대한 소개](automation-intro.md)를 참조하세요.
- Log Analytics 작업 영역. 자세한 내용은 [Azure Monitor 로그 개요](../azure-monitor/logs/data-platform-logs.md)를 참조하세요.
- 적어도 하나 이상의 Azure Automation 상태 구성 노드 자세한 내용은 [Azure Automation State Configuration을 통해 관리를 위한 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 모듈, 버전 2.7.0.0 이상 설치 단계는 [Azure Automation Desired State Configuration 문제 해결](./troubleshoot/desired-state-configuration.md)을 참조하세요.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Monitor 로그와 통합 설정

Azure Automation State Configuration에서 Azure Monitor 로그로 데이터 가져오기를 시작하려면 다음 단계를 완료합니다. 포털을 사용하는 단계는 [Azure Monitor 로그에 Azure Automation 작업 데이터 전달](./automation-manage-send-joblogs-log-analytics.md)을 참조하세요.

1. 머신에서 PowerShell [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. `automationAccount` 변수에 대해 Automation 계정의 실제 이름을 사용하고, `workspaceName` 변수에 대해 Log Analytics 작업 영역의 실제 이름을 사용하여 적절한 값을 제공합니다. 그런 다음 스크립트를 실행합니다.

    ```powershell
    $automationAccount = "automationAccount"
    $law = "workspaceName"
    ```

1. 다음 PowerShell 명령을 실행하여 Automation 계정의 리소스 ID를 가져옵니다.

   ```powershell
   # Find the ResourceId for the Automation account
   $AutomationResourceId = (Get-AzResource `
      -ResourceType 'Microsoft.Automation/automationAccounts' | 
      WHERE {$_.Name -eq $automationAccount}).ResourceId
   ```

1. 다음 PowerShell 명령을 실행하여 Log Analytics 작업 영역의 리소스 ID를 가져옵니다.

   ```powershell
    # Find the ResourceId for the Log Analytics workspace
    $WorkspaceResourceId = (Get-AzResource `
        -ResourceType 'Microsoft.OperationalInsights/workspaces' | 
        WHERE {$_.Name -eq $law}).ResourceId
   ```

1. Automation 계정에서 DSC 노드 상태 로그 데이터를 Azure Monitor 로그로 전달하도록 진단 설정을 구성하려면 다음 PowerShell cmdlet은 해당 대상을 사용하여 진단 설정을 만듭니다.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $true `
        -Category 'DscNodeStatus'
   ```

   Automation State Configuration에서 Azure Monitor 로그 데이터 전달을 중지하려면 다음 PowerShell cmdlet을 실행합니다.

   ```powershell
    Set-AzDiagnosticSetting `
        -ResourceId $AutomationResourceId `
        -WorkspaceId $WorkspaceResourceId `
        -Enabled $false `
        -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>상태 구성 로그 보기

Azure Monitor 로그에서 검색하여 DSC 작업에 대한 State Configuration 로그를 검색할 수 있습니다. Automation State Configuration 데이터에 대한 Azure Monitor 로그와의 통합을 설정한 후 [Azure Portal](https://portal.azure.com/)에서 Automation 계정으로 이동합니다. 그런 다음, **모니터링** 에서 **로그** 를 선택합니다.

![로그](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

**쿼리** 대화 상자를 닫습니다. Automation 계정 리소스로 범위가 지정된 쿼리 영역으로 로그 검색 창이 열립니다. DSC 작업에 대한 레코드는 `AzureDiagnostics` 테이블에 저장됩니다. 호환되지 않는 노드를 찾으려면 다음 쿼리를 입력합니다.

```Kusto
AzureDiagnostics
| where Category == "DscNodeStatus"
| where OperationName contains "DSCNodeStatusData"
| where ResultType != "Compliant"
```

필터링 세부 정보:

- `DscNodeStatusData`를 필터링하여 각 State Configuration 노드에 대한 작업을 반환합니다.
- `DscResourceStatusData`를 필터링하여 해당 리소스에 적용된 노드 구성에서 호출된 각 DSC 리소스에 대한 작업을 반환합니다.
- `DscResourceStatusData`를 필터링하여 실패한 모든 DSC 리소스에 대한 오류 정보를 반환합니다.

데이터를 찾기 위한 로그 쿼리를 구성하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)를 참조하세요.

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>상태 구성 준수 확인이 실패할 경우 이메일 보내기

1. 이전에 만든 쿼리로 돌아갑니다.

1. '+ 새 경고 규칙' 단추를 눌러 경고 생성 흐름을 시작합니다.

1. 아래 쿼리에서 `NODENAME`을 관리형 노드의 실제 이름으로 바꾼 다음, 수정된 쿼리를 **검색 쿼리** 텍스트 상자에 붙여넣습니다.

    ```kusto
    AzureDiagnostics
    | where Category == "DscNodeStatus"
    | where NodeName_s == "NODENAME"
    | where OperationName == "DscNodeStatusData"
    | where ResultType == "Failed"
    ```

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. `DscNodeStatusData`의 로그 검색 결과에 있는 `Resource` 속성에서 Automation 계정 이름을 파생합니다.

1. 나머지 단계를 완료하려면 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md)를 검토합니다.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>모든 노드에서 실패한 DSC 리소스 찾기

Azure Monitor 로그를 사용할 때의 한 가지 이점은 노드에서 실패한 검사를 검색할 수 있다는 것입니다. 실패한 DSC 리소스의 모든 인스턴스를 찾으려면 다음 쿼리를 사용합니다.

```kusto
AzureDiagnostics 
| where Category == "DscNodeStatus"
| where OperationName == "DscResourceStatusData"
| where ResultType == "Failed"
```

### <a name="view-historical-dsc-node-status"></a>DSC 노드 상태 기록 보기

시간별 DSC 노드 상태 기록을 시각화하려면 다음 쿼리를 사용할 수 있습니다.

```kusto
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.AUTOMATION" 
| where Category == "DscNodeStatus"
| where ResultType != "started"
| summarize count() by ResultType
``````

이 쿼리는 시간별 노드 상태 차트를 표시합니다.

## <a name="azure-monitor-logs-records"></a>Azure Monitor 로그 레코드

Azure Automation 진단은 Azure Monitor 로그에 두 가지 범주의 레코드를 만듭니다.

* 노드 상태 데이터(`DscNodeStatusData`)
* 리소스 상태 데이터(`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 속성 | Description |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |`DscNodeStatusData`. |
| ResultType |노드가 준수인지 여부를 나타내는 값입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| NodeComplianceStatus_s |노드가 준수인지 여부를 지정하는 상태 값입니다. |
| DscReportStatus |준수 확인이 성공적으로 실행되었는지 여부를 나타내는 상태 값입니다. |
| ConfigurationMode | 구성을 노드에 적용하는 데 사용되는 모드입니다. 가능한 값은 다음과 같습니다. <ul><li>`ApplyOnly`: DSC는 구성을 적용하며, 새 구성이 대상 노드에 푸시되지 않거나 서버에서 새 구성을 가져올 때 아무 작업도 수행하지 않습니다. 새 구성의 애플리케이션이 초기에 적용된 후 DSC는 이전에 구성된 상태에서 달라졌는지 여부를 확인하지 않습니다. DSC는 `ApplyOnly` 값이 적용되기 전에 성공할 때까지 구성을 적용하려고 합니다. </li><li>`ApplyAndMonitor`: 이것은 기본값입니다. LCM는 새 구성을 적용합니다. 새 구성의 애플리케이션이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고합니다. DSC는 `ApplyAndMonitor` 값이 적용되기 전에 성공할 때까지 구성을 적용하려고 합니다.</li><li>`ApplyAndAutoCorrect`: DSC에서 모든 새 구성을 적용합니다. 새 구성의 애플리케이션이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고하고 현재 구성을 다시 적용합니다.</li></ul> |
| HostName_s | 관리되는 노드의 이름입니다. |
| IPAddress | 관리되는 노드의 IPv4 주소입니다. |
| Category | `DscNodeStatus`. |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g | 관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g | 보고서를 식별하는 GUID입니다. |
| LastSeenTime_t | 보고서를 마지막으로 본 날짜 및 시간입니다. |
| ReportStartTime_t | 보고서가 시작된 날짜 및 시간입니다. |
| ReportEndTime_t | 보고서가 완료된 날짜 및 시간입니다. |
| NumberOfResources_d | 노드에 적용된 구성에서 호출된 DSC 리소스의 수입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집한 방법을 식별하는 원본 시스템입니다. Azure 진단의 경우 항상 `Azure`입니다. |
| ResourceId |Azure Automation 계정의 리소스 식별자입니다. |
| ResultDescription | 이 작업에 대한 리소스 설명입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | 준수 보고서의 상관 관계 식별자인 GUID입니다. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 속성 | Description |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |`DscResourceStatusData`.|
| ResultType |리소스가 규정을 준수하는지 여부입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| Category | DscNodeStatus. |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g |관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g |보고서를 식별하는 GUID입니다. |
| DscResourceId_s |DSC 리소스 인스턴스의 이름입니다. |
| DscResourceName_s |DSC 리소스의 이름입니다. |
| DscResourceStatus_s |DSC 리소스가 규정을 준수하는지 여부입니다. |
| DscModuleName_s |DSC 리소스를 포함하는 PowerShell 모듈의 이름입니다. |
| DscModuleVersion_s |DSC 리소스를 포함하는 PowerShell 모듈의 버전입니다. |
| DscConfigurationName_s |노드에 적용되는 구성의 이름입니다. |
| ErrorCode_s | 리소스가 실패한 경우의 오류 코드입니다. |
| ErrorMessage_s |리소스가 실패한 경우의 오류 메시지입니다. |
| DscResourceDuration_d |DSC 리소스가 실행된 시간(초)입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집하는 방법입니다. Azure 진단의 경우 항상 `Azure`입니다. |
| ResourceId |Azure Automation 계정의 식별자입니다. |
| ResultDescription | 이 작업에 대한 설명입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 ID(GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |준수 보고서의 상관 관계 ID인 GUID입니다. |

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure Automation State Configuration 개요](automation-dsc-overview.md)를 참조하세요.
- 시작하려면 [Azure Automation State Configuration 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation State Configuration에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation State Configuration 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation State Configuration을 사용하는 예제는 [Chocolatey를 사용한 지속적인 배포 설정](automation-dsc-cd-chocolatey.md)을 참조하세요.
- Azure Monitor 로그를 사용하여 여러 검색 쿼리를 작성하고 Automation State Configuration 로그를 검토하는 방법에 대한 자세한 내용은 [Azure Monitor 로그의 로그 검색](../azure-monitor/logs/log-query-overview.md)을 참조하세요.
- Azure Monitor 로그 및 데이터 수집 소스에 대해 자세히 알아보려면 [Azure Monitor 로그에서 Azure 스토리지 데이터 수집 개요](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)를 참조하세요.
