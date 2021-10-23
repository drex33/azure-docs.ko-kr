---
title: Automation에서 에이전트 기반 Windows Hybrid Runbook Worker 배포
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 컴퓨터에서 runbook을 실행 하는 데 사용할 수 있는 에이전트 기반 Hybrid Runbook Worker를 배포 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2588864b2a6bdba913c9a7fb0bbc7af62e8c4c1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248567"
---
# <a name="deploy-an-agent-based-windows-hybrid-runbook-worker-in-automation"></a>Automation에서 에이전트 기반 Windows Hybrid Runbook Worker 배포

Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용하여 [Azure Arc 사용 서버](../azure-arc/servers/overview.md)에 등록된 서버를 포함하여 Azure 또는 비 Azure 머신에서 직접 Runbook을 실행할 수 있습니다. 역할을 호스트하는 머신 또는 서버에서 해당 로컬 리소스를 관리할 수 있도록 해당 서버에 대해 또는 환경의 리소스에 대해 Runbook을 직접 실행할 수 있습니다.

Azure Automation는 runbook을 저장 하 고 관리 한 다음 하나 이상의 선택한 컴퓨터에 전달 합니다. 이 문서에서는 Windows 머신에 사용자 Hybrid Runbook Worker를 배포하는 방법, 작업자를 제거하는 방법 및 Hybrid Runbook Worker 그룹을 제거하는 방법을 설명합니다. 사용자 Hybrid Runbook worker의 경우 [Automation에서 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포](./extension-based-hybrid-runbook-worker-install.md) 를 참조 하세요.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인합니다.

### <a name="a-log-analytics-workspace"></a>Log Analytics 작업 영역

Hybrid Runbook Worker 역할은 역할을 설치하고 구성하는 Azure Monitor Log Analytics 작업 영역에 따라 다릅니다. [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)를 통해 만들거나 [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)에서 만들 수 있습니다.

Azure Monitor Log Analytics 작업 영역이 아직 없는 경우 작업 영역을 만들기 전에 [Azure Monitor 로그 디자인 지침](../azure-monitor/logs/design-logs-deployment.md)을 살펴보세요.

### <a name="log-analytics-agent"></a>Log Analytics 에이전트

Hybrid Runbook Worker 역할에는 지원되는 Windows 운영 체제에 대한 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)가 필요합니다. Azure 외부에서 호스트되는 서버 또는 머신의 경우 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하여 Log Analytics 에이전트를 설치할 수 있습니다.

### <a name="supported-windows-operating-system"></a>지원되는 Windows 운영 체제

Hybrid Runbook Worker 기능은 다음과 같은 운영 체제를 지원합니다.

* Windows Server 2019(Server Core 포함)
* Windows Server 2016, 버전 1709 및 1803(Server Core 제외)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2(x64), 2008 R2
* Windows 10 Enterprise(다중 세션 포함) 및 Pro
* Windows 8 Enterprise 및 Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>최소 요구 사항

Windows 시스템 및 사용자 Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* Windows PowerShell 5.1([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core는 지원 되지 않습니다.
* .NET Framework 4.6.2 이상
* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

### <a name="network-configuration"></a>네트워크 구성

Hybrid Runbook Worker에 대한 네트워킹 요구 사항의 경우 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>머신을 Hybrid Runbook Worker 그룹에 추가

Automation 계정 중 하나에 있는 Hybrid Runbook Worker 그룹에 작업자 머신을 추가할 수 있습니다. 업데이트 관리에 의해 관리되는 시스템 Hybrid Runbook Worker를 호스트하는 컴퓨터는 Hybrid Runbook Worker 그룹에 추가할 수 있습니다. 단, 업데이트 관리 및 Hybrid Runbook Worker 그룹 멤버 자격 모두에 대해 동일한 Automation 계정을 사용해야 합니다.

>[!NOTE]
>Azure Automation [업데이트 관리](./update-management/overview.md)는 업데이트 관리에 대해 사용하도록 설정된 Azure 또는 비 Azure 머신에서 시스템 Hybrid Runbook Worker를 자동으로 설치합니다. 그러나 이 작업자는 Automation 계정의 어떠한 Hybrid Runbook Worker 그룹에도 등록되지 않습니다. 이러한 머신에서 Runbook을 실행하려면 Hybrid Runbook Worker 그룹에 추가해야 합니다. [수동 배포](#manual-deployment) 섹션에 있는 6단계를 수행하여 그룹에 추가합니다.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Azure Automation State Configuration을 통한 관리 사용

머신에 Azure Automation State Configuration을 통한 관리 사용에 관한 정보는 [머신에 Azure Automation State Configuration을 통한 관리 사용](automation-dsc-onboarding.md)을 참조하세요.

> [!NOTE]
> DSC(Desired State Configuration)로 Hybrid Runbook Worker 역할을 지원하는 머신의 구성을 관리하려면 머신을 DSC 노드로 추가해야 합니다.

## <a name="installation-options"></a>설치 옵션

Windows 사용자 Hybrid Runbook Worker를 설치 및 구성하려면 다음 방법 중 하나를 사용하면 됩니다.

* 제공된 PowerShell 스크립트를 사용하여 하나 이상의 Windows 머신 구성 프로세스를 완전히 [자동화](#automated-deployment)합니다. 이는 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에 권장되는 방법입니다.
* 수동으로 Automation 솔루션을 가져오고, Windows용 Log Analytics 에이전트를 설치하고, 머신에서 작업자 역할을 구성합니다.

## <a name="automated-deployment"></a>자동화된 배포

Hybrid Runbook Worker를 자동으로 배포하는 두 가지 방법이 있습니다. Azure Portal의 Runbook 갤러리에서 Runbook을 가져와서 실행하거나, PowerShell Gallery에서 스크립트를 수동으로 다운로드할 수 있습니다.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook 갤러리에서 Runbook 가져오기

가져오기 절차는 [Azure Portal을 사용하여 GitHub에서 Runbook 가져오기](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)에 자세히 설명되어 있습니다. 가져올 Runbook의 이름은 **Automation Windows HybridWorker 만들기** 입니다.

Runbook은 다음 매개 변수를 사용합니다.

| 매개 변수 | 상태 | Description |
| ------- | ----- | ----------- |
| `Location` | 필수 | 스크립트가 실행되는 자동화 계정의 위치입니다. |
| `ResourceGroupName` | 필수 | Automation 계정에 대한 리소스 그룹입니다. |
| `AccountName` | 필수 | Hybrid Run Worker가 등록되는 Automation 계정 이름입니다. |
| `CreateLA` | 필수 | True인 경우 `WorkspaceName`의 값을 사용하여 Log Analytics 작업 영역을 만듭니다. False인 경우 `WorkspaceName`의 값이 기존 작업 영역을 참조해야 합니다. |
| `LAlocation` | 선택 사항 | Log Analytics 작업 영역이 생성되는 위치 또는 이미 존재하는 위치입니다. |
| `WorkspaceName` | 선택 사항 | 사용할 Log Analytics 작업 영역의 이름입니다. |
| `CreateVM` | 필수 | True인 경우 `VMName`의 값을 새 VM의 이름으로 사용합니다. False인 경우 `VMName`을 사용하여 기존 VM을 찾아 등록합니다. |
| `VMName` | 선택 사항 | `CreateVM`의 값에 따라 만들거나 등록한 가상 머신의 이름입니다. |
| `VMImage` | 선택 사항 | 만들 VM 이미지의 이름입니다. |
| `VMlocation` | 선택 사항 | 만들거나 등록한 VM의 위치입니다. 이 위치를 지정 하지 않으면 값 `LAlocation` 이 사용 됩니다. |
| `RegisterHW` | 필수 | True인 경우 VM을 하이브리드 작업자로 등록합니다. |
| `WorkerGroupName` | 필수 | Hybrid Worker 그룹의 이름입니다. |

### <a name="download-a-script-from-the-powershell-gallery"></a>PowerShell 갤러리에서 스크립트 다운로드

이 자동화된 배포 방법은 PowerShell 스크립트 **New-OnPremiseHybridWorker.ps1** 을 사용하여 Windows Hybrid Runbook Worker 역할을 자동화하고 구성합니다. 다음과 같이 수행됩니다.

* 필요한 패키지 설치
* Azure 계정으로 로그인
* 지정된 리소스 그룹 및 Automation 계정이 있는지 확인
* Automation 계정 특성에 대한 참조 만들기
* 지정되지 않은 경우 Azure Monitor Log Analytics 작업 영역 만들기
* 작업 영역에서 Azure Automation 솔루션 사용하도록 설정
* Windows용 Log Analytics 에이전트 다운로드 및 설치
* Hybrid Runbook Worker로 머신 등록

다음 단계를 수행하여 스크립트를 사용하여 Windows 머신에서 역할을 설치합니다.

1. [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)에서 **New-OnPremiseHybridWorker.ps1** 스크립트를 다운로드합니다. 스크립트를 다운로드한 후 대상 컴퓨터에서 복사하거나 실행합니다. 스크립트는 다음과 같은 매개 변수를 사용합니다.

    | 매개 변수 | 상태 | Description |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | 필수 | Automation 계정과 연결된 리소스 그룹의 이름입니다. |
    | `AutomationAccountName` | 필수 | Automation 계정의 이름입니다.
    | `Credential` | 옵션 | Azure 환경에 로그인할 때 사용할 자격 증명입니다. |
    | `HybridGroupName` | 필수 | 이 시나리오를 지원하는 Runbook에 대한 대상으로 지정할 Hybrid Runbook Worker 그룹의 이름입니다. |
    | `OMSResourceGroupName` | 옵션 | Log Analytics 작업 영역에 대한 리소스 그룹의 이름입니다. 이 리소스 그룹을 지정하지 않으면 `AAResourceGroupName` 값이 사용됩니다. |
    | `SubscriptionID` | 필수 | Automation 계정과 연결된 Azure 구독의 식별자입니다. |
    | `TenantID` | 옵션 | Automation 계정과 연결된 테넌트 조직의 식별자입니다. |
    | `WorkspaceName` | 옵션 | Log Analytics 작업 영역 이름입니다. Log Analytics 작업 영역이 없는 경우 스크립트에서 하나를 만들어 구성합니다. |

1. 관리자 권한 64비트 PowerShell 명령 프롬프트를 엽니다.

1. PowerShell 명령 프롬프트에서 다운로드한 스크립트가 포함된 폴더를 찾습니다. `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` 및 `WorkspaceName` 매개 변수의 값을 변경합니다. 그런 다음, 스크립트를 실행합니다.

    스크립트를 실행한 후에 Azure 인증을 요청하는 메시지가 나타납니다. **구독 관리자** 역할의 멤버이자 구독의 공동 관리자인 계정으로 로그인해야 합니다.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. NuGet 설치에 동의하고 Azure 자격 증명으로 인증을 받도록 요청하는 메시지가 표시됩니다. 최신 NuGet 버전을 사용하지 않는 경우에는 [사용 가능한 NuGet 배포 버전](https://www.nuget.org/downloads)에서 다운로드할 수 있습니다.

1. 스크립트가 완료된 후 배포를 확인합니다. Automation 계정의 **Hybrid Runbook Worker 그룹** 페이지에서 **사용자 Hybrid Runbook Worker 그룹** 탭에 새 그룹 및 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. 페이지의 목록에서 그룹을 선택하고 왼쪽 메뉴에서 **Hybrid Workers** 를 선택합니다. **Hybrid Workers** 페이지에서 나열된 그룹의 각 멤버를 확인할 수 있습니다.

## <a name="manual-deployment"></a>수동 배포

Windows Hybrid Runbook Worker를 설치 및 구성하려면 다음 단계를 수행합니다.

1. 관리자 권한 PowerShell 명령 프롬프트에서 또는 [Azure Portal](https://portal.azure.com)의 Cloud Shell에서 다음 명령을 실행하여 Log Analytics 작업 영역에서 Azure Automation 솔루션을 사용하도록 설정합니다.

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Log Analytics 에이전트를 대상 머신에 배포합니다.

    - Azure VM의 경우 [Windows용 가상 머신 확장](../virtual-machines/extensions/oms-windows.md)을 사용하여 Windows용 Log Analytics 에이전트를 설치합니다. 확장 버전은 Azure 가상 머신에 Log Analytics 에이전트를 설치하고 기존 Log Analytics 작업 영역에 가상 머신을 등록합니다. Azure Resource Manager 템플릿, PowerShell 또는 Azure Policy를 사용하여 [*Linux* 또는 *Windows* VM에 대한 Log Analytics 에이전트 배포](../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 할당할 수 있습니다. 에이전트가 설치되면 머신을 Automation 계정의 Hybrid Runbook Worker 그룹에 추가할 수 있습니다.
    
    - 비 Azure 머신의 경우 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하여 Log Analytics 에이전트를 설치할 수 있습니다. Azure Arc 지원 서버는 다음 방법을 사용하여 Log Analytics 에이전트 배포를 지원합니다.
    
      - VM 확장 프레임워크 사용.
        
        Azure Arc 지원 서버의 이 기능을 사용하면 비 Azure Windows 또는 Linux 서버에 Log Analytics 에이전트 VM 확장을 배포할 수 있습니다. VM 확장은 하이브리드 컴퓨터 또는 Arc 사용 서버에 의해 관리되는 서버에서 다음 방법을 이용하여 관리할 수 있습니다.
        
        - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
        - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
        - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
        - Azure [Resource Manager 템플릿](../azure-arc/servers/manage-vm-extensions-template.md)
        
      - Azure Policy 사용.
        
      이 방식을 사용하면 [Linux 또는 Windows Azure Arc 머신에 Log Analytics 에이전트 배포](../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 사용하여 Arc 지원 서버에 Log Analytics 에이전트가 설치되어 있는지 감사합니다. 에이전트가 설치되지 않은 경우 수정 작업을 사용하여 자동으로 배포합니다. VM용 Azure Monitor 사용하여 머신을 모니터링하려는 경우 대신 [VM용 Azure Monitor 사용](../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 사용하여 Log Analytics 에이전트를 설치하고 구성합니다.

    Azure Policy를 사용하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치하는 것이 좋습니다.

1. 에이전트가 작업 영역에 보고하는지 확인

    Windows용 Log Analytics 에이전트는 Azure Monitor Log Analytics 작업 영역에 머신을 연결합니다. 머신에 에이전트를 설치하고 작업 영역에 연결하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드됩니다.

    몇 분 후에 에이전트가 Log Analytics 작업 영역에 성공적으로 연결되면 다음 쿼리를 실행하여 하트비트 데이터를 작업 영역으로 보내고 있는지 확인할 수 있습니다.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    검색 결과에서 머신에 대한 하트비트 레코드가 표시되며, 이는 머신이 연결되어 서비스에 보고하고 있음을 나타냅니다. 기본적으로 모든 에이전트는 하트비트 레코드를 할당된 작업 영역으로 전달합니다. 다음 단계를 수행하여 에이전트 설치 및 설정을 완료합니다.

1. Log Analytics 에이전트를 호스트하는 머신에서 Hybrid Runbook Worker 버전을 확인하고 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\`을 찾아 **버전** 하위 폴더를 확인합니다. 이 폴더는 작업 영역에서 솔루션을 사용하도록 설정하고 몇 분 후 머신에 표시됩니다.

1. Runbook 환경을 설치하고 Azure Automation에 연결합니다. Log Analytics 작업 영역에 보고하고 **Automation** 솔루션을 가져오도록 에이전트를 구성하면 솔루션은 `HybridRegistration` PowerShell 모듈을 푸시 다운합니다. 이 모듈에는 `Add-HybridRunbookWorker` cmdlet이 포함되어 있습니다. 이 cmdlet을 사용하여 머신에 Runbook 환경을 설치하고 Azure Automation에 등록합니다.

    관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. `Url`, `Key` 및 `GroupName` 매개 변수의 값을 지정하는 `Add-HybridRunbookWorker` cmdlet을 실행합니다.

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Automation 계정의 **키** 페이지에서 `Url` 및 `Key` 매개 변수에 필요한 정보를 가져올 수 있습니다. 페이지 왼쪽의 **계정 설정** 섹션에서 **키** 를 선택합니다.

    ![키 관리 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `Url` 매개 변수의 경우 **URL** 에 대한 값을 복사합니다.

    * `Key` 매개 변수의 경우 **주 액세스 키** 에 대한 값을 복사합니다.

    * `GroupName` 매개 변수에 Hybrid Runbook Worker 그룹의 이름을 사용합니다. 이 그룹이 Automation 계정에 이미 있으면 현재 머신이 추가됩니다. 그룹이 없으면 추가됩니다.

    * 필요한 경우 `Verbose` 설치에 대한 세부 정보를 받도록 매개 변수를 설정합니다.

1. 명령이 완료된 후 배포를 확인합니다. Automation 계정의 **Hybrid Runbook Worker 그룹** 페이지에서 **사용자 Hybrid Runbook Worker 그룹** 탭에 새 그룹 또는 기존 그룹과 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. 페이지의 목록에서 그룹을 선택하고 왼쪽 메뉴에서 **Hybrid Workers** 를 선택합니다. **Hybrid Workers** 페이지에서 나열된 그룹의 각 멤버를 확인할 수 있습니다.

## <a name="install-powershell-modules"></a>PowerShell 모듈 설치

Runbook은 Azure Automation 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다. 이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 않으므로 수동으로 설치해야 합니다. Azure 모듈은 예외입니다. 이 모듈은 기본적으로 설치되며 Azure Automation의 모든 Azure 서비스 및 활동에 사용되는 cmdlet에 대한 액세스를 제공합니다.

Hybrid Runbook Worker의 주 목적은 로컬 리소스를 관리하는 것이므로 이러한 리소스를 지원하는 모듈(특히, `PowerShellGet` 모듈)을 설치해야 할 수 있습니다. Windows PowerShell 모듈 설치에 대한 자세한 내용은 [Windows PowerShell](/powershell/scripting/developer/windows-powershell)을 참조하세요.

설치된 모듈은 Hybrid Worker가 자동으로 가져올 수 있도록 `PSModulePath` 환경 변수가 참조하는 위치에 있어야 합니다. 자세한 내용은 [PSModulePath에서 모듈 설치](/powershell/scripting/developer/module/installing-a-powershell-module)를 참조하세요.

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Hybrid Runbook Worker 제거

1. Azure Portal에서 Automation 계정으로 이동합니다.

1. **계정 설정** 에서 **키** 를 선택하고 **URL** 및 **기본 액세스 키** 의 값을 확인합니다.

1. 관리자 모드에서 PowerShell 세션을 열고 URL 및 기본 액세스 키 값을 사용하여 다음 명령을 실행합니다. 제거 프로세스에 대한 자세한 로그를 보려면 `Verbose` 매개 변수를 사용합니다. Hybrid Worker 그룹에서 부실한 컴퓨터를 제거하려면 선택적 `machineName` 매개 변수를 사용합니다.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Hybrid Worker 그룹 제거

Hybrid Runbook Worker 그룹을 제거하려면 먼저 그룹의 멤버인 모든 머신에서 Hybrid Runbook Worker를 제거해야 합니다. 이후 다음 단계를 수행하여 그룹을 제거합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

1. **프로세스 자동화** 에서 **Hybrid Worker 그룹** 을 선택합니다. 삭제할 그룹을 선택합니다. 해당 그룹에 대한 속성 페이지가 표시됩니다.

   ![속성 페이지](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 선택한 그룹에 대한 속성 페이지에서 **삭제** 를 선택합니다. 이 작업의 확인을 요청하는 메시지가 나타납니다. 계속하려면 **예** 를 선택합니다.

   ![확인 메시지](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   이 프로세스를 마치는 데 몇 초 정도 걸릴 수 있습니다. 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.