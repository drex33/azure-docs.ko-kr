---
title: Azure Automation에서 Runbook 실행
description: 이 문서에서는 Azure Automation에서 Runbook을 처리하는 방법에 대한 개요를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 08/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 026adbac5ad66772711bcd3988a9c95da49ae6a1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837483"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure Automation의 프로세스 자동화를 사용하면 PowerShell, PowerShell 워크플로 및 그래픽 Runbook을 만들고 관리할 수 있습니다. 자세한 내용은 [Azure Automation Runbook](automation-runbook-types.md)을 참조하세요. 

Automation은 Runbook 내에 정의된 논리를 기준으로 Runbook을 실행합니다. Runbook이 중단되면 처음부터 다시 시작됩니다. 이 동작을 수행하려면 일시적인 문제가 발생한 경우 다시 시작을 지원하는 방식으로 Runbook을 작성해야 합니다.

Azure Automation에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업은 Azure 구독에 대한 연결을 설정하여 Azure 리소스에 액세스합니다. 작업은 데이터 센터의 리소스가 퍼블릭 클라우드에서 액세스할 수 있는 경우에만 데이터 리소스에 액세스할 수 있습니다.

Azure Automation은 Runbook 실행 중에 각 작업에 작업자를 할당합니다. 작업자가 많은 Automation 계정에서 공유되지만 다른 Automation 계정의 작업은 서로 격리됩니다. 작업에서 어느 작업자 서비스를 요청할지는 사용자가 제어할 수 없습니다.

Azure Portal에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 각 작업의 상태가 표시됩니다. Azure Automation은 최대 30일 동안 작업 로그를 저장합니다.

다음 다이어그램은 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks), [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 및 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)에 대한 Runbook 작업의 수명 주기를 보여 줍니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook 실행 환경

Azure Automation의 Runbook은 Azure 샌드박스 또는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행할 수 있습니다. 

Runbook이 Azure의 리소스에 대해 인증되고 실행되도록 설계된 경우 Azure 샌드박스에서 실행됩니다. Azure Automation은 샌드박스에서 Runbook을 실행하는 동안 각 작업을 실행할 작업자를 할당합니다. 작업자가 많은 Automation 계정에서 공유되지만 다른 Automation 계정의 작업은 서로 격리됩니다.  동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다. Azure 샌드박스 환경은 대화형 작업을 지원하지 않으며 모든 Out of Process COM 서버에 대한 액세스를 방지하고 Runbook의 Win32 공급자에 대한 [WMI 호출](/windows/win32/wmisdk/wmi-architecture)을 지원하지 않습니다.  이러한 시나리오는 Windows Hybrid Runbook Worker에서 Runbook을 실행하는 경우에만 지원됩니다.

역할을 호스트하는 컴퓨터에서, 그리고 환경의 리소스에 대해 Runbook을 직접 실행하는 데 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용할 수도 있습니다. Azure Automation은 Runbook을 저장 및 관리한 후 하나 이상의 할당된 컴퓨터로 전달합니다.

[Azure Storage](../storage/common/storage-network-security.md), [Azure Key Vault](../key-vault/general/network-security.md) 또는 [Azure SQL](../azure-sql/database/firewall-configure.md)에서 Azure Firewall을 사용하도록 설정하면 해당 서비스에 대한 Azure Automation Runbook의 액세스가 차단됩니다. Automation이 신뢰할 수 있는 서비스 목록에 포함되어 있지 않기 때문에, 신뢰할 수 있는 Microsoft 서비스를 허용하는 방화벽 예외가 사용하도록 설정된 경우에도 액세스가 차단됩니다. 방화벽을 사용하도록 설정한 경우에는 Hybrid Runbook Worker 및 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 통해서만 액세스할 수 있습니다.

>[!NOTE]
>Linux Hybrid Runbook Worker에서 실행하려면 스크립트에 서명이 되어 있고 작업자가 적절하게 구성되어 있어야 합니다. 또는 [서명 유효성 검사가 해제](automation-linux-hrw-install.md#turn-off-signature-validation)되어 있어야 합니다.

다음 표에는 몇 가지 Runbook 실행 태스크와 각각의 권장 실행 환경이 나와 있습니다.

|Task|권장|메모|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스트되며, 인증이 더 간단합니다. Azure VM에서 Hybrid Runbook Worker를 사용하는 경우 [관리 ID를 통한 Runbook 인증을 사용](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)할 수 있습니다.|
|Azure 리소스 관리를 위한 최적의 성능 달성|Azure 샌드박스|스크립트가 동일한 환경에서 실행되므로 대기 시간이 줄어듭니다.|
|운영 비용 최소화|Azure 샌드박스|컴퓨팅 오버헤드가 없고 VM이 필요하지 않습니다.|
|장기 실행 스크립트 실행|Hybrid Runbook Worker|Azure 샌드박스에는 [리소스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 머신에 직접 액세스하거나, 다른 클라우드 환경 또는 온-프레미스 환경에 있는 리소스에 직접 액세스합니다. |
|타사 소프트웨어 및 실행 파일 요구|Hybrid Runbook Worker|운영 체제를 관리하고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|Hybrid Runbook Worker에서 [감시자 태스크](./automation-scenario-using-watcher-task.md)를 사용합니다.|
|리소스를 많이 사용하는 스크립트 실행|Hybrid Runbook Worker| Azure 샌드박스에는 [리소스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|특정 요구 사항이 있는 모듈 사용| Hybrid Runbook Worker|예는 다음과 같습니다.</br> WinSCP - winscp.exe에 대한 종속성 </br> IIS 관리 - IIS 사용 또는 관리에 대한 종속성|
|설치 관리자를 사용하여 모듈 설치|Hybrid Runbook Worker|샌드박스용 모듈은 복사를 지원해야 합니다.|
|4\.7.2와 다른 .NET Framework 버전이 필요한 Runbook 또는 모듈 사용|Hybrid Runbook Worker|Azure 샌드박스는 .NET Framework 4.7.2를 지원하며, 다른 버전으로의 업그레이드는 지원되지 않습니다.|
|권한 상승이 필요한 스크립트 실행|Hybrid Runbook Worker|샌드박스에서는 권한 상승이 허용되지 않습니다. Hybrid Runbook Worker를 사용하면 권한 상승이 필요한 명령을 실행할 때 UAC를 해제하고 [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command)를 사용할 수 있습니다.|
|WMI(Windows Management Instrumentation)에 액세스해야 하는 스크립트 실행|Hybrid Runbook Worker|클라우드의 샌드박스에서 실행되는 작업은 WMI 공급자에 액세스할 수 없습니다. |

## <a name="temporary-storage-in-a-sandbox"></a>샌드박스의 임시 스토리지

Runbook 논리의 일부로 임시 파일을 만들어야 하는 경우 Azure에서 실행되는 Runbook에 대해 Azure 샌드박스에서 임시 폴더(즉, `$env:TEMP`)를 사용할 수 있습니다. 유일한 제한 사항은 1GB를 초과하는 디스크 공간(각 샌드박스에 대한 할당량)을 사용할 수 없다는 것입니다. Powershell 워크플로를 사용하는 경우 PowerShell 워크플로에서 검사점을 사용하고 다른 샌드박스에서 스크립트를 다시 시도할 수 있으므로 이 시나리오에서 문제가 발생할 수 있습니다.

하이브리드 샌드박스를 사용하면 Hybrid Runbook Worker의 스토리지 가용성에 따라 `C:\temp`를 사용할 수 있습니다. 그러나 Azure VM 권장 사항에 따라 유지해야 하는 데이터에 대해 Windows 또는 Linux에서 [임시 디스크](../virtual-machines/managed-disks-overview.md#temporary-disk)를 사용해서는 안 됩니다.

## <a name="resources"></a>리소스

Runbook은 [리소스](/rest/api/resources/resources)(예: VM, 네트워크, 네트워크의 리소스)를 처리하는 논리를 포함해야 합니다. 리소스는 Azure 구독에 연결되어 있으며, Runbook에서 리소스에 액세스하려면 적절한 자격 증명이 필요합니다. Runbook에서 리소스를 처리하는 예제는 [리소스 처리](manage-runbooks.md#handle-resources)를 참조하세요.

## <a name="security"></a>보안

Azure Automation은 [ASC(Azure Security Center)](../security-center/security-center-introduction.md)를 사용하여 리소스에 대한 보안을 제공하고 Linux 시스템에서 보안 침해를 감지합니다. 리소스는 Azure에 있는지 여부와 관계없이 모든 워크로드에 보안이 제공됩니다. [Azure Automation의 인증 소개](automation-security-overview.md)를 참조하세요.

ASC는 VM에서 서명되었거나 서명되지 않은 스크립트를 실행할 수 있는 사용자에게 제약 조건을 적용합니다. VM에 대한 루트 액세스 권한이 있는 사용자는 디지털 서명을 사용하여 머신을 명시적으로 구성하거나 해제해야 합니다. 그러지 않으면 Automation 계정을 만들고 적절한 기능을 사용하도록 설정한 후에만 스크립트를 실행하여 운영 체제 업데이트를 적용할 수 있습니다.

## <a name="subscriptions"></a>Subscriptions

Azure [구독](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)은 요금이 청구되는 하나 이상의 클라우드 기반 서비스를 사용하도록 지원한다는 Microsoft와의 계약입니다. Azure Automation의 각 구독은 Azure Automation 계정에 연결되어 있으며, 계정에서 [여러 구독을 만들 수 있습니다](manage-runbooks.md#work-with-multiple-subscriptions).

## <a name="credentials"></a>자격 증명

Runbook을 사용하려면 Azure 또는 타사 시스템의 리소스에 액세스하기 위한 적절한 [자격 증명](shared-resources/credentials.md)이 필요합니다. 자격 증명은 Azure Automation, 키 자격 증명 모음 등에 저장됩니다.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation은 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 머신 작업을 모니터링합니다. 작업에는 Log Analytics 작업 영역과 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)가 필요합니다.

### <a name="log-analytics-agent-for-windows"></a>Windows용 Log Analytics 에이전트

[Windows용 Log Analytics 에이전트](../azure-monitor/agents/agent-windows.md)는 Azure Monitor와 함께 작동하여 Windows VM과 물리적 컴퓨터를 관리합니다. 머신은 Azure에서 실행될 수도 있고 로컬 데이터 센터와 같은 비 Azure 환경에서 실행될 수도 있습니다.

>[!NOTE]
>Windows용 Log Analytics 에이전트의 이전 명칭은 MMA(Microsoft Monitoring Agent)입니다.

### <a name="log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트

[Linux용 Log Analytics 에이전트](../azure-monitor/agents/agent-linux.md)는 Linux 컴퓨터를 Azure Monitor에 연결해 준다는 점을 제외하면 Windows용 에이전트와 비슷하게 작동합니다. 에이전트는 Hybrid Runbook Worker 등에서 루트 권한이 필요한 명령을 실행할 수 있도록 지원하는 **nxautomation** 사용자 계정을 사용하여 설치됩니다. **nxautomation** 계정은 암호를 요구하지 않는 시스템 계정입니다.

[Linux Hybrid Runbook Worker](automation-linux-hrw-install.md)를 설치할 때는 해당 sudo 권한이 있는 **nxautomation** 계정이 있어야 합니다. 작업자를 설치하려고 시도했는데 계정이 없거나 계정에 적절한 권한이 없는 경우 설치가 실패합니다.

`sudoers.d` 폴더 또는 해당 소유권의 사용 권한을 변경하면 안 됩니다. **nxautomation** 계정에는 Sudo 권한이 필요하며 사용 권한은 제거하면 안 됩니다. 이를 특정 폴더 또는 명령으로 제한하면 호환성이 손상되는 변경이 발생할 수 있습니다.

Log Analytics 에이전트 및 **nxautomation** 계정에서 사용할 수 있는 로그는 다음과 같습니다.

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics 에이전트 로그
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - Automation 작업자 로그

>[!NOTE]
>업데이트 관리의 일부로 사용하도록 설정된 **nxautomation** 사용자는 서명된 Runbook만 실행합니다.

## <a name="runbook-permissions"></a>Runbook 사용 권한

Runbook이 자격 증명을 통해 Azure에 대해 인증되려면 권한이 필요합니다. [Azure Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.

## <a name="modules"></a>모듈

Azure Automation은 다음 PowerShell 모듈을 포함합니다.

* Orchestrator.AssetManagement.Cmdlets - Azure 샌드박스 환경 또는 Windows Hybrid Runbook Worker에서 Notebook을 실행할 때에만 사용 가능한 여러 내부 cmdlet이 포함되어 있습니다. 이러한 cmdlet은 Azure PowerShell cmdlet 대신 사용하여 Automation 계정 리소스와 상호 작용하도록 설계되었습니다.
* Az.Automation - AzureRM Automation 모듈을 대체하는 Azure Automation과 상호 작용하는 권장 PowerShell 모듈입니다. Az.Automation 모듈은 Automation 계정을 만들 때 자동으로 포함되지 않으며 이를 수동으로 가져와야 합니다. 
* AzureRM.Automation - Automation 계정을 만들 때 기본적으로 설치됩니다. 

또한 Runbook과 DSC 구성에 필요한 cmdlet에 따라 설치 가능한 모듈도 지원됩니다. Runbook 및 DSC 구성에서 사용할 수 있는 모듈에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.

## <a name="certificates"></a>인증서

Azure Automation은 [인증서](shared-resources/certificates.md)를 사용하여 Azure에 대해 인증하거나 Azure 또는 타사 리소스에 추가합니다. 인증서는 Runbook과 DSC 구성에서 액세스할 수 있도록 안전하게 저장됩니다.

Runbook은 자체 서명된 인증서를 사용할 수 있습니다. 자체 서명된 인증서는 CA(인증 기관)가 서명하지 않은 인증서입니다. [새 인증서 만들기](shared-resources/certificates.md#create-a-new-certificate)를 참조하세요.

## <a name="jobs"></a>작업

Azure Automation은 동일한 Automation 계정에서 작업을 실행하는 환경을 지원합니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다. 

동일한 샌드박스 프로세스에서 실행되는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예로 [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) cmdlet을 실행하는 것을 들 수 있습니다. 이 cmdlet을 실행하면 공유 샌드박스 프로세스에서 각 Runbook 작업의 연결이 끊어집니다. 이 시나리오를 사용하는 예제는 [동시 작업 방지](manage-runbooks.md#prevent-concurrent-jobs)를 참조하세요.

>[!NOTE]
>Azure 샌드박스에서 실행되는 Runbook에서 시작된 PowerShell 작업은 전체 [PowerShell 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)에서 실행되지 않을 수 있습니다.

### <a name="job-statuses"></a>작업 상태

다음 표에서는 작업의 가능한 여러 상태를 설명합니다. Azure Portal에서 모든 Runbook 작업의 상태 요약을 보거나 특정 Runbook 작업의 세부 정보로 드릴할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. Azure Monitor 로그와의 통합에 대한 자세한 내용은 [Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요. Runbook의 상태를 사용하는 예제는 [작업 상태 가져오기](manage-runbooks.md#obtain-job-statuses)를 참조하세요.

| 상태 | Description |
|:--- |:--- |
| 활성화 |작업이 활성화되는 중입니다. |
| Completed |작업이 완료되었습니다. |
| 실패 |그래픽 또는 PowerShell 워크플로 Runbook을 컴파일하지 못했습니다. PowerShell Runbook을 시작하지 못했거나 작업에서 예외가 발생했습니다. [Azure Automation Runbook 형식](automation-runbook-types.md)을 참조하세요.|
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |작업이 시작될 수 있도록 Automation 작업자의 리소스가 사용 가능한 상태가 되기를 기다리고 있습니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 공평 분배 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |[그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md)에만 적용됩니다. 작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없으면 처음부터 시작됩니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 시스템은 예외가 발생한 경우에만 Runbook을 일시 중단합니다. 기본적으로 `ErrorActionPreference` 변수는 계속으로 설정되어 있습니다. 이는 오류가 발생해도 작업이 계속 시행됨을 나타냅니다. 기본 설정 변수가 중지로 설정된 경우 오류가 발생하면 작업이 일시 중단됩니다.  |
| Suspending |[그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md)에만 적용됩니다. 시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 지난 경우 완료되어야만 일시 중단할 수 있습니다. |

## <a name="activity-logging"></a>활동 로깅

Azure Automation에서 Runbook을 실행하면 Automation 계정의 활동 로그에 세부 정보가 기록됩니다. 로그 사용에 대한 자세한 내용은 [활동 로그에서 세부 정보 검색](manage-runbooks.md#retrieve-details-from-activity-log)을 참조하세요.

## <a name="exceptions"></a>예외

이 섹션에서는 Runbook에서 예외 또는 일시적인 문제를 처리하는 몇 가지 방법을 설명합니다. 한 가지 예로 WebSocket 예외를 들 수 있습니다. 올바른 예외 처리를 통해 일시적인 네트워크 오류가 Runbook의 실패를 유발하는 일을 방지할 수 있습니다.

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 변수는 PowerShell이 종료되지 않는 오류에 대응하는 방식을 결정합니다. 종료 오류는 항상 종료되며 `ErrorActionPreference`의 영향을 받지 않습니다.

Runbook에서 `ErrorActionPreference`를 사용하는 경우, [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) cmdlet의 `PathNotFound`와 같은 일반적으로 종료되지 않는 오류는 Runbook이 완료되는 것을 막습니다. 다음 예제에서는 `ErrorActionPreference`의 사용을 보여 줍니다. 스크립트가 중지되므로 [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) 명령이 실행되지 않습니다.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)는 PowerShell 스크립트에서 종료 오류를 처리하는 데 사용됩니다. 스크립트는 이 메커니즘을 사용하여 특정 예외나 일반 예외를 catch할 수 있습니다. 오류를 추적하거나 처리하려고 시도하려면 `catch` 문을 사용해야 합니다. 다음 예제에서는 존재하지 않는 파일을 다운로드하려고 시도합니다. `System.Net.WebException` 예외는 catch하고 그 밖의 다른 예외의 경우 마지막 값을 반환합니다.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw)는 종료 오류를 생성하는 데 사용될 수 있습니다. 이 메커니즘은 Runbook에서 자체 논리를 정의할 때 유용할 수 있습니다. 스크립트가 중지 조건에 도달한 경우 `throw` 문을 사용하여 중지할 수 있습니다. 다음 예제에서는 이 문을 사용하여 필요한 함수 매개 변수를 표시합니다.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>오류

Runbook에서는 오류를 처리해야 합니다. Azure Automation은 두 가지 유형의 PowerShell 오류(종료 오류와 종료되지 않는 오류)를 지원합니다. 

종료 오류는 발생 시 Runbook 실행을 중지합니다. Runbook은 작업 상태가 실패로 설정되고 중지됩니다.

종료되지 않는 오류를 사용하면 오류 발생 후에도 스크립트가 계속 진행됩니다. 종료되지 않는 오류의 한 가지 예로 Runbook이 존재하지 않는 경로로 `Get-ChildItem` cmdlet을 사용하는 경우를 들 수 있습니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 경우 오류는 Runbook 작업 상태를 실패로 설정하지 않고, 경우에 따라 작업이 완료될 수도 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `ErrorAction Stop`을 사용할 수 있습니다.

## <a name="calling-processes"></a>호출 프로세스

Azure 샌드박스에서 실행되는 Runbook은 실행 파일( **.exe** 파일)이나 하위 프로세스와 같은 호출 프로세스를 지원하지 않습니다. 그 이유는 Azure 샌드박스는 컨테이너에서 실행되는 공유 프로세스로, 모든 기본 API에 액세스하지 못할 수 있기 때문입니다. 타사 소프트웨어나 하위 프로세스에 대한 호출이 필요한 시나리오에서는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 Runbook을 실행해야 합니다.

## <a name="device-and-application-characteristics"></a>디바이스 및 애플리케이션 특성

Azure 샌드박스의 Runbook 작업은 디바이스나 애플리케이션 특성에 액세스할 수 있습니다. Windows에서 성능 메트릭을 쿼리하는 데 가장 자주 사용되는 API는 WMI입니다. 자주 사용되는 메트릭에는 메모리와 CPU 사용량이 있습니다. 그러나 클라우드에서 실행되는 작업은 WBEM(Web-Based Enterprise Management)의 Microsoft 구현에 액세스할 수 없으므로 어느 API를 사용하는지는 관계가 없습니다. 이 플랫폼은 CIM(Common Information Model)을 기반으로 빌드되었기 대문에 디바이스 및 애플리케이션 특성 정의를 위한 업계 표준을 제공합니다.

## <a name="webhooks"></a>Webhook

Azure DevOps Services나 GitHub와 같은 외부 서비스는 Azure Automation에서 Runbook을 시작할 수 있습니다. 서비스는 이러한 유형의 시작을 수행하기 위해 단일 HTTP 요청을 통해 [웹후크](automation-webhooks.md)를 사용합니다. 웹후크를 사용하면 전체 Azure Automation 기능을 구현하지 않고도 Runbook을 시작할 수 있습니다.

## <a name="shared-resources"></a><a name="fair-share"></a>공유 리소스

Azure 클라우드에 있는 모든 Runbook리소스를 공유할 수 있도록 공평 분배라는 개념을 사용합니다. Azure는 공평 분배를 사용하여 3시간 이상 실행된 작업을 일시적으로 언로드하거나 중지합니다. [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [Python Runbook](automation-runbook-types.md#python-runbooks)의 작업은 중지된 후 다시 시작되지 않으며 작업 상태는 중지됨으로 설정됩니다.

장기 실행 Azure Automation 작업의 경우 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 사용하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. Hybrid Runbook Worker는 3시간으로 제한되지 않지만 예기치 않은 로컬 인프라 문제로 인한 다시 시작을 지원하는 작업자에서 실행할 Runbook을 개발해야 합니다.

또 다른 옵션은 자식 Runbook을 사용하여 Runbook을 최적화하는 것입니다. 예를 들어 Runbook이 여러 리소스에서 동일한 함수를 반복하는 경우(예: 여러 데이터베이스의 데이터베이스 작업에서) 해당 함수를 [자식 Runbook](automation-child-runbooks.md)으로 이동한 다음 Runbook에서 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)을 사용하여 이 함수를 호출하도록 할 수 있습니다. 자식 Runbook은 별도의 프로세스에서 병렬로 실행됩니다.

자식 Runbook을 사용하면 부모 Runbook이 완료되는 데 소요되는 총 시간이 줄어듭니다. Runbook은 자식이 완료된 후 남아 있는 작업이 있으면 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) cmdlet을 사용하여 자식 Runbook의 작업 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* PowerShell Runbook을 시작하려면 [자습서: PowerShell Runbook 만들기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
* Runbook을 사용하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [PowerShell 문서](/powershell/scripting/overview)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation#automation)을 참조하세요.