---
title: 에이전트 기반 Hybrid Runbook Worker 문제 해결 Azure Automation
description: 이 문서에서는 Azure Automation 에이전트 기반 Hybrid Runbook Worker에서 발생 하는 문제를 해결 하는 방법을 설명 합니다.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: v-ssudhir
ms.date: 10/18/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05a791a2b30be5d722f72a05d270212a190a6dce
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400569"
---
# <a name="troubleshoot-agent-based-hybrid-runbook-worker-issues-in-automation"></a>Automation의 에이전트 기반 Hybrid Runbook Worker 문제 해결

이 문서에서는 에이전트 기반 Hybrid Runbook Worker Azure Automation 문제를 해결 하 고 문제를 해결 하는 방법에 대 한 정보를 제공 합니다. 확장 기반 작업자의 문제 해결에 대 한 자세한 내용은 [자동화에서 확장 기반 Hybrid Runbook Worker 문제 해결](./extension-based-hybrid-runbook-worker.md)을 참조 하세요. 일반 정보는 [Hybrid Runbook Worker 개요](../automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="general"></a>일반

Hybrid Runbook Worker는 에이전트를 사용하여 Azure Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고합니다. Windows의 경우 이 에이전트는 Windows용 Log Analytics 에이전트입니다. Linux의 경우에는 Linux용 Log Analytics 에이전트입니다.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>시나리오: Runbook 실행 실패

#### <a name="issue"></a>문제

Runbook 실행에 실패하고 다음 오류 메시지가 표시됩니다.

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Runbook이 3회 실행을 시도한 직후 일시 중단됩니다. Runbook 완료를 방해할 수 있는 조건이 있습니다. 관련 오류 메시지에 추가 정보가 포함되어 있지 않을 수 있습니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.
* Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.
* Hybrid Runbook Worker를 실행하도록 구성된 컴퓨터가 최소 하드웨어 요구 사항을 충족하지 않습니다.

#### <a name="resolution"></a>해결 방법

**.azure-automation.net\*** 에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

Hybrid Runbook Worker가 실행되는 컴퓨터는 작업자가 이 기능을 호스트하도록 구성하기 전에, 최소 하드웨어 요구 사항을 충족해야 합니다. Runbook 및 여기에 사용되는 백그라운드 프로세스로 인해 시스템이 과도하게 사용되어 Runbook 작업이 지연되거나 시간이 초과될 수 있습니다.

Hybrid Runbook Worker 기능을 실행할 컴퓨터가 최소 하드웨어 요구 사항을 충족 하는지 확인 합니다. 충족하는 경우 CPU 및 메모리 사용을 모니터링하여 Hybrid Runbook Worker 프로세스의 성능과 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 메모리나 CPU가 부족하면 리소스를 업그레이드해야 할 수도 있습니다. 최소 요구 사항을 지 원하는 다른 계산 리소스를 선택 하 고 워크 로드 요구가 증가를 나타내는 경우 크기를 조정할 수도 있습니다.

**Microsoft-SMA** 이벤트 로그에 `Win32 Process Exited with code [4294967295]` 설명이 포함된 해당 이벤트가 있는지 확인합니다. 이 오류의 원인은 Runbook에 인증을 구성하지 않았거나 Hybrid Runbook Worker 그룹에 대해 실행 자격 증명을 지정하지 않았기 때문일 수 있습니다. [Hybrid Runbook Worker에서 Runbook 실행](../automation-hrw-run-runbooks.md)에서 Runbook 사용 권한을 검토하고 Runbook에 대한 인증을 제대로 구성했는지 확인합니다.

### <a name="scenario-runbooks-fail-with-gateway-error"></a>시나리오: Runbook이 게이트웨이 오류로 인해 실패 함

#### <a name="issue"></a>문제

Log Analytics 게이트웨이 서버를 통해 통신할 때 Hybrid Runbook Worker 작업을 새로 고치지 못했으며 반환 된 오류는 다음과 유사 합니다. `Spool operation id does not exist (spool ID): see attachment for job details and exact exception messages.`

#### <a name="resolution"></a>해결 방법

Log Analytics Gateway 서버가 온라인 상태이 고 Hybrid Runbook Worker 역할을 호스트 하는 컴퓨터에서 액세스할 수 있는지 확인 합니다. 추가 문제 해결 정보는 [Log Analytics Gateway 문제 해결](../../azure-monitor/agents/gateway.md#troubleshooting)을 참조 하세요.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>시나리오: Hybrid Runbook Worker의 이벤트 15011

#### <a name="issue"></a>문제

Hybrid Runbook Worker가 쿼리 결과가 유효하지 않음을 나타내는 이벤트 15011을 수신합니다. 작업자가 [SignalR 서버](/aspnet/core/signalr/introduction)와의 연결을 열려고 하면 다음 오류가 나타납니다.

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>원인

Hybrid Runbook Worker가 자동화된 기능 배포(예: 업데이트 관리)에 맞게 올바르게 구성되지 않았습니다. 배포에는 VM을 Log Analytics 작업 영역에 연결하는 부분이 포함되어 있습니다. PowerShell 스크립트는 제공된 이름을 사용하여 구독에서 작업 영역을 찾습니다. 이 경우 Log Analytics 작업 영역이 다른 구독에 있습니다. 스크립트가 작업 영역을 찾지 못하여 만들려고 시도하지만 해당 이름이 이미 사용되었습니다. 그 결과 배포가 실패합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하는 옵션은 두 가지입니다.

* 다른 구독에서 Log Analytics 작업 영역을 찾도록 PowerShell 스크립트를 수정합니다. 향후 Hybrid Runbook Worker 머신을 많이 배포할 계획이면 이 방법이 좋은 해결책입니다.

* 작업자 머신이 Orchestrator 샌드박스에서 실행되도록 수동으로 구성합니다. 그런 다음, 작업자의 Azure Automation 계정에서 생성된 Runbook을 실행하여 기능을 테스트합니다.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>시나리오: Windows Azure VM이 Hybrid Worker 그룹에서 자동으로 삭제됨

#### <a name="issue"></a>문제

작업자 머신을 오래 꺼두면 Hybrid Runbook Worker나 VM을 볼 수 없습니다.

#### <a name="cause"></a>원인

Hybrid Runbook Worker 머신이 30일 넘게 Azure Automation에 ping하지 않았습니다. 그 결과, Automation이 Hybrid Runbook Worker 그룹 또는 시스템 작업자 그룹을 제거했습니다. 

#### <a name="resolution"></a>해결 방법

작업자 머신을 시작한 다음, Azure Automation에 다시 등록합니다. Runbook 환경을 설치하고 Azure Automation에 연결하는 방법에 대한 지침은 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md)를 참조하세요.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>시나리오: Hybrid Runbook Worker의 인증서 저장소에서 인증서를 찾을 수 없음

#### <a name="issue"></a>문제

Hybrid Runbook Worker에서 실행되는 Runbook이 실패하고 다음 오류 메시지가 표시됩니다.

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>원인

이 오류는 실행 계정 인증서가 없는 Hybrid Runbook Worker에서 실행되는 Runbook에서 [실행 계정](../automation-security-overview.md#run-as-accounts)을 사용하려고 시도할 때 발생합니다. Hybrid Runbook Worker는 기본적으로 인증서 자산이 로컬에 없습니다. 실행 계정이 제대로 작동하려면 이 자산이 필요합니다.

#### <a name="resolution"></a>해결 방법

Hybrid Runbook Worker가 Azure VM이면 [관리 ID로 Runbook 인증](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities)을 대신 사용할 수 있습니다. 이 시나리오는 실행 계정 대신 Azure VM의 관리 ID를 사용하여 Azure 리소스에 인증할 수 있기 때문에 인증이 간소화됩니다. Hybrid Runbook Worker가 온-프레미스 머신인 경우 머신에 실행 계정 인증서를 설치해야 합니다. 인증서를 설치하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](../automation-hrw-run-runbooks.md)에서 PowerShell Runbook **Export-RunAsCertificateToHybridWorker** 를 실행하는 단계를 참조하세요.

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>시나리오: Hybrid Runbook Worker 등록 중 오류 403

#### <a name="issue"></a>문제

작업자의 초기 등록 단계가 실패하고 다음 오류(403)가 표시됩니다.

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>원인

원인이 될만한 문제는 다음과 같습니다.

* 에이전트 설정에 잘못 입력한 작업 영역 ID 또는 작업 영역 키(기본)가 있습니다. 
* Hybrid Runbook Worker가 구성을 다운로드할 수 없어서 계정 연결 오류가 발생했습니다. Azure가 머신에서 기능을 사용하도록 설정하는 경우, Log Analytics 작업 영역과 Automation 계정을 연결하는 데 필요한 특정 지역만 지원합니다. 컴퓨터에 잘못된 날짜나 시간이 설정되어 있을 수도 있습니다. 이 시간이 현재 시간에서 +/- 15분이면 기능 배포에 실패합니다.

#### <a name="resolution"></a>해결 방법

##### <a name="mistyped-workspace-id-or-key"></a>잘못 입력한 작업 영역 ID 또는 키
에이전트의 작업 영역 ID 또는 작업 영역 키를 잘못 입력했는지 확인하려면 Windows 에이전트의 경우 [작업 영역 추가 또는 제거 – Windows 에이전트](../../azure-monitor/agents/agent-manage.md#windows-agent), Linux 에이전트의 경우 [작업 영역 추가 또는 제거 – Linux 에이전트](../../azure-monitor/agents/agent-manage.md#linux-agent)를 참조하세요. Azure Portal에서 주의 깊게 전체 문자열을 선택하고 복사하여 붙여 넣어야 합니다.

##### <a name="configuration-not-downloaded"></a>구성이 다운로드되지 않음

Log Analytics 작업 영역과 Automation 계정은 연결된 지역에 있어야 합니다. 지원되는 지역 목록은 [Azure Automation 및 Log Analytics 작업 영역 매핑](../how-to/region-mappings.md)을 참조하세요.

컴퓨터의 날짜 또는 표준 시간대를 업데이트해야 할 수도 있습니다. 사용자 지정 시간 범위를 선택하는 경우 범위가 UTC(현지 표준 시간대와 다를 수 있음)인지 확인합니다.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>시나리오: Hybrid Runbook Worker에서 Set-AzStorageBlobContent가 실패 

#### <a name="issue"></a>문제

Runbook이 `Set-AzStorageBlobContent`를 실행하려고 할 때 실패하고 다음 오류 메시지가 표시됩니다.

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>원인

 이 오류는 UNC 경로를 추가 하는에 대 한 호출의 긴 파일 이름 동작으로 인해 발생 `[System.IO.Path]::GetFullPath()` 합니다.

#### <a name="resolution"></a>해결 방법

문제를 해결하기 위해 다음 콘텐츠를 사용하여 `OrchestratorSandbox.exe.config`라는 구성 파일을 만들 수 있습니다.

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

해당 파일을 `OrchestratorSandbox.exe` 실행 파일과 동일한 폴더에 두세요. 예제:

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> 에이전트를 업그레이드하는 경우 해당 구성 파일이 삭제되며 다시 만들어야 합니다.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker는 [Linux용 Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)를 사용하여 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고합니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>시나리오: Linux Hybrid Runbook Worker가 Runbook에 서명할 때 암호를 묻는 메시지를 받음

#### <a name="issue"></a>문제

Linux Hybrid Runbook Worker에 대해 `sudo` 명령을 실행하면 예기치 않게 암호를 묻는 프롬프트가 표시됩니다.

#### <a name="cause"></a>원인

Linux용 Log Analytics 에이전트의 **nxautomationuser** 계정이 **sudoers** 파일에서 올바르게 구성되지 않았습니다. Hybrid Runbook Worker에 적절한 계정 권한 구성 및 기타 데이터가 있어야 Linux Runbook Worker의 Runbook에 서명할 수 있습니다.

#### <a name="resolution"></a>해결 방법

* Hybrid Runbook Worker 머신에 GnuPG(GPG) 실행 파일이 있는지 확인합니다.

* **sudoers** 파일에서 **nxautomationuser** 계정의 구성을 확인합니다. [Hybrid Runbook Worker에서 Runbook 실행](../automation-hrw-run-runbooks.md)을 참조하세요.

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>시나리오: Linux용 Log Analytics 에이전트가 실행되고 있지 않음

#### <a name="issue"></a>문제

Linux용 Log Analytics 에이전트가 실행되지 있지 않습니다.

#### <a name="cause"></a>원인

에이전트가 실행되고 있지 않으면 Linux Hybrid Runbook Worker가 Azure Automation과 통신할 수 없습니다. 다양한 이유로 에이전트가 실행되지 않을 수 있습니다.

#### <a name="resolution"></a>해결 방법

 `ps -ef | grep python` 명령을 입력하여 에이전트가 실행 중인지 확인합니다. 다음과 유사한 결과가 표시됩니다. Python은 **nxautomation** 사용자 계정을 사용하여 프로세스를 진행합니다. Azure Automation 기능을 사용하도록 설정되어 있지 않으면 다음 프로세스가 모두 실행되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 모두 /var/opt/microsoft/omsagent/state/automationworker/ 디렉터리에 있습니다.

* **oms.conf**: 작업자 관리자 프로세스입니다. DSC에서 직접 시작됩니다.
* **worker.conf**: 자동 등록된 Hybrid Worker 프로세스입니다. 작업자 관리자에서 시작됩니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 머신에서 업데이트 관리를 사용하도록 설정되어 있지 않으면 이 프로세스가 존재하지 않습니다.
* **diy/worker.conf**: DIY Hybrid Worker 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 자동 등록된 Hybrid Worker 프로세스와는 다른 구성을 사용하는 키 정보만 다릅니다. Azure Automation이 비활성화되어 있고 DIY Linux Hybrid Worker가 등록되어 있지 않으면 이 프로세스는 존재하지 않습니다.

에이전트가 실행되고 있지 않으면 `sudo /opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 서비스를 시작합니다.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>시나리오: 지정된 클래스가 없음

**/var/opt/microsoft/omsconfig/omsconfig.log** 에 `The specified class does not exist..`라는 오류 메시지가 보이면 Linux용 Log Analytics 에이전트를 업데이트해야 합니다. 다음 명령을 실행하여 에이전트를 다시 설치합니다.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker는 [Windows용 Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)를 사용하여 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고합니다. 작업자 등록이 실패하면 이 섹션에 가능한 이유가 포함됩니다.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>시나리오: Windows용 Log Analytics 에이전트가 실행되고 있지 않음

#### <a name="issue"></a>문제

`healthservice`가 Hybrid Runbook Worker 머신에서 실행되고 있지 않습니다.

#### <a name="cause"></a>원인

Windows용 Log Analytics 서비스가 실행되고 있지 않으면 Hybrid Runbook Worker가 Azure Automation과 통신할 수 없습니다.

#### <a name="resolution"></a>해결 방법

PowerShell에서 `Get-Service healthservice` 명령을 입력하여 에이전트가 실행 중인지 확인합니다. 서비스가 중지된 경우 PowerShell에서 `Start-Service healthservice` 명령을 입력하여 서비스를 시작하세요.

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>시나리오: Operations Manager 로그의 이벤트 4502

#### <a name="issue"></a>문제

**Application and Services Logs\Operations Manager** 이벤트 로그에 이벤트 4502와 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`가 포함된 이벤트 메시지가 보이며 다음 설명이 있습니다.<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>원인

이 문제는 프록시 또는 네트워크 방화벽이 Microsoft Azure와의 통신을 차단하기 때문일 수 있습니다. **.azure-automation.net\*** 에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

#### <a name="resolution"></a>해결 방법

로그는 각 Hybrid Worker의 로컬에 저장되며 위치는 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes입니다. **Application and Services Logs\Microsoft-SMA\Operations** 및 **Application and Services Logs\Operations Manager** 이벤트 로그에 경고나 오류가 있는지 확인할 수 있습니다. 이러한 로그에는 Azure Automation 역할을 활성화하는 데 영향을 주는 연결이나 기타 문제 유형 또는 정상적인 작업 하에 발생한 문제가 표시됩니다. Log Analytics 에이전트 관련 문제 해결에 대한 자세한 도움말은 [Log Analytics Windows 에이전트 관련 문제 해결을](../../azure-monitor/agents/agent-windows-troubleshoot.md)참조하세요.

Hybrid Worker는 클라우드에서 실행되는 Runbook 작업이 출력과 메시지를 보내는 것과 동일한 방식으로 Azure Automation에 [Runbook 출력 및 메시지](../automation-runbook-output-and-messages.md)를 보냅니다. Runbook을 활성화하듯이 세부 정보 표시 및 진행률 스트림을 활성화할 수 있습니다.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>시나리오: Orchestrator.Sandbox.exe가 프록시를 통해 Microsoft 365에 연결할 수 없음

#### <a name="issue"></a>문제

Windows Hybrid Runbook Worker에서 실행되는 스크립트를 예정대로 오케스트레이터 샌드박스의 Microsoft 365에 연결할 수 없습니다. 이 스크립트는 연결에 [Connect-MsolService](/powershell/module/msonline/connect-msolservice)를 사용하고 있습니다. 

**Orchestrator.Sandbox.exe.config** 를 조정하여 프록시와 바이패스 목록을 설정해도 샌드박스가 제대로 연결되지 않습니다. 프록시 및 바이패스 목록 설정이 동일한 **Powershell_ise.exe.config** 파일이 예상대로 작동하는 것 같습니다. SMA(Service Management Automation) 로그 및 PowerShell 로그는 프록시에 대한 정보를 제공하지 않습니다.

#### <a name="cause"></a>원인

서버의 AD FS(Active Directory Federation Services)에 대한 연결은 프록시를 무시할 수 없습니다. PowerShell 샌드박스는 로그온한 사용자로 실행됩니다. 하지만 Orchestrator 샌드박스를 과도하게 사용자 지정하여 **Orchestrator.Sandbox.exe.config** 파일 설정이 무시될 수도 있습니다. 머신 또는 Log Analytics 에이전트 프록시 설정을 처리하는 데 사용하는 특수 코드가 있지만 기타 사용자 지정 프록시 설정을 처리하는 데 사용하는 코드는 없습니다. 

#### <a name="resolution"></a>해결 방법

PowerShell cmdlet용 MSOnline 모듈 대신 Azure Active Directory 모듈을 사용하도록 스크립트를 마이그레이션하여 Orchestrator 샌드박스의 문제를 해결할 수 있습니다. 자세한 내용은 [Orchestrator에서 Azure Automation으로 마이그레이션(베타)](../automation-orchestrator-migration.md)을 참조하세요.

MSOnline 모듈 cmdlet을 계속 사용하려면 [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command)를 사용하도록 스크립트를 변경합니다. `ComputerName` 및 `Credential` 매개 변수에 대한 값을 지정합니다. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

코드를 이렇게 변경하면 지정된 자격 증명의 컨텍스트에서 완전히 새로운 PowerShell 세션이 시작됩니다. 활성 사용자를 인증하는 프록시 서버를 통해 트래픽이 흐를 수 있도록 합니다.

>[!NOTE]
>이 해결 방법을 사용하면 샌드박스 구성 파일을 조작할 필요가 없습니다. 스크립트와 작동하는 구성 파일을 만드는 데 성공해도 Hybrid Runbook Worker 에이전트가 업데이트될 때마다 파일이 지워집니다.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>시나리오: Hybrid Runbook Worker가 보고하지 않음

#### <a name="issue"></a>문제

Hybrid Runbook Worker 머신을 실행 중인데 이 머신에 대한 하트비트 데이터가 작업 영역에 보이지 않습니다.

다음 예제 쿼리는 작업 영역의 시스템과 해당 마지막 하트비트를 보여 줍니다.

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>원인

이 문제는 Hybrid Runbook Worker의 손상된 캐시로 인해 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 Hybrid Runbook Worker에 로그인하고 다음 스크립트를 실행합니다. 이 스크립트는 Windows용 Log Analytics 에이전트를 중지하고 캐시를 제거한 후 서비스를 다시 시작합니다. 이 작업을 통해 Hybrid Runbook Worker는 강제로 Azure Automation에서 해당 구성을 다시 다운로드하게 됩니다.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>시나리오: Windows Hybrid Runbook Worker를 추가할 수 없음

#### <a name="issue"></a>문제

`Add-HybridRunbookWorker` cmdlet을 사용하여 Hybrid Runbook Worker를 추가하려고 하면 다음과 같은 메시지 표시됩니다.

`Machine is already registered`

#### <a name="cause"></a>원인

이 문제는 머신이 이미 다른 Automation 계정에 등록되어 있거나 머신에서 Hybrid Runbook Worker를 제거한 후 다시 추가하려고 하는 경우에 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 레지스트리 키를 제거하고 `HealthService`를 다시 시작한 후 `Add-HybridRunbookWorker` cmdlet을 다시 시도해보세요.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>시나리오: Linux Hybrid Runbook Worker를 추가할 수 없음

#### <a name="issue"></a>문제

`sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python 스크립트를 사용하여 Hybrid Runbook Worker를 추가하려고 하면 다음과 같은 메시지가 표시됩니다.

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

또한 `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` Python 스크립트를 사용하여 Hybrid Runbook Worker 등록을 취소하려고 하면 다음과 같은 메시지가 표시됩니다.

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>원인

해당 문제는 머신에서 다른 Automation 계정으로 이미 등록되어 있거나 Azure Hybrid Worker 그룹이 삭제되었거나 머신에서 Hybrid Runbook Worker를 제거한 후 다시 추가하려고 하는 경우 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면:

1. `sudo sh onboard_agent.sh --purge` 에이전트를 제거합니다.

1. 다음 명령을 실행하세요.

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` 에이전트를 다시 온보딩합니다.

1. `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` 폴더가 채워질 때까지 기다립니다.

1. `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python 스크립트를 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

여기에서 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 시도하여 추가 지원을 요청하세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.