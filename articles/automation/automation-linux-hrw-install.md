---
title: Automation에서 에이전트 기반 Linux Hybrid Runbook Worker 배포
description: 이 문서에서는 에이전트 기반 Hybrid Runbook Worker를 설치 하 여 로컬 데이터 센터 또는 클라우드 환경의 Linux 기반 컴퓨터에서 runbook을 실행 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5ae0f393dca05e748f4919a84223131034fee81
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272127"
---
# <a name="deploy-an-agent-based-linux-hybrid-runbook-worker-in-automation"></a>Automation에서 에이전트 기반 Linux Hybrid Runbook Worker 배포

Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용하여 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)에 등록된 서버를 포함하여 Azure 또는 비 Azure 머신에서 직접 Runbook을 실행할 수 있습니다. 역할을 호스트하는 머신 또는 서버에서 직접 Runbook을 실행하고 환경의 리소스에 대해 해당 로컬 리소스를 관리할 수 있습니다.

Linux Hybrid Runbook Worker는 Runbook을 승격이 필요한 명령을 실행하도록 승격할 수 있는 특수 사용자로 실행합니다. Azure Automation는 runbook을 저장 하 고 관리 한 다음 하나 이상의 선택한 컴퓨터에 전달 합니다. 이 문서에서는 Linux 컴퓨터에 Hybrid Runbook Worker를 설치 하 고, 작업자를 제거 하 고, Hybrid Runbook Worker 그룹을 제거 하는 방법을 설명 합니다. 사용자 Hybrid Runbook worker의 경우 [Automation에서 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포](./extension-based-hybrid-runbook-worker-install.md) 를 참조 하세요.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 항목이 있는지 확인합니다.

### <a name="a-log-analytics-workspace"></a>Log Analytics 작업 영역

Hybrid Runbook Worker 역할은 역할을 설치하고 구성하는 Azure Monitor Log Analytics 작업 영역에 따라 다릅니다. [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)를 통해 만들거나 [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)을 통해 만들거나 [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)에서 만들 수 있습니다.

Azure Monitor Log Analytics 작업 영역이 아직 없는 경우 작업 영역을 만들기 전에 [Azure Monitor 로그 디자인 지침](../azure-monitor/logs/design-logs-deployment.md)을 살펴보세요.

### <a name="log-analytics-agent"></a>Log Analytics 에이전트

Hybrid Runbook Worker 역할에는 지원되는 Linux 운영 체제에 대한 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)가 필요합니다. Azure 외부에서 호스트되는 서버 또는 머신의 경우 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하여 Log Analytics 에이전트를 설치할 수 있습니다.

> [!NOTE]
> Linux용 Log Analytics 에이전트를 설치한 후에는 `sudoers.d` 폴더의 권한이나 그 소유권을 변경해서는 안 됩니다. Hybrid Runbook Worker이 실행되는 사용자 컨텍스트인 **nxautomation** 계정에는 Sudo 권한이 필요합니다. 권한은 제거할 수 없습니다. 이를 특정 폴더 또는 명령으로 제한하면 호환성이 손상되는 변경이 발생할 수 있습니다.
>

### <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

Hybrid Runbook Worker 기능은 다음 배포를 지원합니다. 모든 운영 체제는 x64로 간주됩니다. x86은 모든 운영 체제에서 지원 되지 않습니다.

* Amazon Linux 2012.09~2015.09
* CentOS Linux 5, 6, 7 및 8
* Oracle Linux 6, 7 및 8
* Red Hat Enterprise Linux Server 5, 6, 7 및 8
* Debian GNU/Linux 6, 7, 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS, 18.04 및 20.04 LTS
* SUSE Linux Enterprise Server 12, 15 및 15.1 (SUSE 릴리스 버전 번호 13 또는 14)

> [!IMPORTANT]
> 시스템 Hybrid Runbook Worker 역할에 따라 달라지는 업데이트 관리 기능을 사용하도록 설정하기 전에 [여기](update-management/operating-system-requirements.md)에서 지원하는 배포를 확인합니다.

### <a name="minimum-requirements"></a>최소 요구 사항

Linux 시스템 및 사용자 Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

| **필수 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 라이브러리| 2.5-12 |
|Openssl| OpenSSL 라이브러리 | 1.0(TLS 1.1 및 TLS 1.2가 지원됨)|
|Curl | cURL 웹 클라이언트 | 7.15.5|
|Python-ctypes | Python용 외장 함수 라이브러리| Python 2.x 또는 Python 3.x 필요 |
|PAM | 플러그형 인증 모듈|

| **선택적 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | PowerShell Runbook을 실행하려면 PowerShell Core를 설치해야 합니다. 설치하는 방법을 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/install/installing-powershell-core-on-linux)를 참조하세요. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker 그룹에 머신 추가

Automation 계정 중 하나에 있는 Hybrid Runbook Worker 그룹에 작업자 머신을 추가할 수 있습니다. 업데이트 관리에 의해 관리되는 시스템 Hybrid Runbook Worker를 호스트하는 컴퓨터는 Hybrid Runbook Worker 그룹에 추가할 수 있습니다. 단, 업데이트 관리 및 Hybrid Runbook Worker 그룹 멤버 자격 모두에 대해 동일한 Automation 계정을 사용해야 합니다.

> [!NOTE]
> Azure Automation [업데이트 관리](./update-management/overview.md)는 업데이트 관리에 대해 사용하도록 설정된 Azure 또는 비 Azure 머신에서 시스템 Hybrid Runbook Worker를 자동으로 설치합니다. 그러나 이 작업자는 Automation 계정의 어떠한 Hybrid Runbook Worker 그룹에도 등록되지 않습니다. 이러한 머신에서 Runbook을 실행하려면 Hybrid Runbook Worker 그룹에 추가해야 합니다. [Linux Hybrid Runbook Worker 설치](#install-a-linux-hybrid-runbook-worker) 섹션에 있는 4단계에 따라 그룹에 추가합니다.

## <a name="supported-linux-hardening"></a>지원되는 Linux 강화

다음은 아직 지원 되지 않습니다.

* CIS

## <a name="supported-runbook-types"></a>지원되는 Runbook 유형

Linux Hybrid Runbook Worker는 Azure Automation의 제한 된 runbook 형식 집합을 지원 하며 다음 표에 설명 되어 있습니다.

|Runbook 형식 | 지원 여부 |
|-------------|-----------|
|Python 3(미리 보기)|예, 다음에만 필요 합니다. SUSE LES 15, RHEL 8 및 CentOS 8|
|Python 2 |예, Python 3이 필요하지 않은 모든 배포판의 경우<sup>1</sup> |
|PowerShell |예<sup>2</sup> |
|PowerShell 워크플로 |아니요 |
|그래픽 |아니요 |
|그래픽 PowerShell 워크플로 |아니요 |

<sup>1</sup>[지원되는 Linux 운영 체제](#supported-linux-operating-systems) 참조

<sup>2</sup>PowerShell Runbook을 사용하려면 Linux 머신에 PowerShell Core를 설치해야 합니다. 설치하는 방법을 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/install/installing-powershell-core-on-linux)를 참조하세요.

### <a name="network-configuration"></a>네트워크 구성

Hybrid Runbook Worker에 대한 네트워킹 요구 사항의 경우 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 설치

Hybrid Runbook Worker은 두 가지 방법으로 배포할 수 있습니다. Azure Portal의 Runbook 갤러리에서 runbook을 가져오거나 실행 하거나 일련의 PowerShell 명령을 수동으로 실행할 수 있습니다.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook 갤러리에서 Runbook 가져오기

가져오기 절차는 [Azure Portal을 사용하여 GitHub에서 Runbook 가져오기](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)에 자세히 설명되어 있습니다. 가져올 Runbook의 이름은 **Automation Linux HybridWorker 만들기** 입니다.

Runbook은 다음 매개 변수를 사용합니다.

| 매개 변수 | 상태 | Description |
| ------- | ----- | ----------- |
| `Location` | 필수 | Log Analytics 작업 영역의 위치입니다. |
| `ResourceGroupName` | 필수 | Automation 계정에 대한 리소스 그룹입니다. |
| `AccountName` | 필수 | Hybrid Run Worker가 등록되는 Automation 계정 이름입니다. |
| `CreateLA` | 필수 | True인 경우 `WorkspaceName`의 값을 사용하여 Log Analytics 작업 영역을 만듭니다. False인 경우 `WorkspaceName`의 값이 기존 작업 영역을 참조해야 합니다. |
| `LAlocation` | 선택 사항 | Log Analytics 작업 영역이 생성되는 위치 또는 이미 존재하는 위치입니다. |
| `WorkspaceName` | 선택 사항 | 만들거나 사용할 Log Analytics 작업 영역의 이름입니다. |
| `CreateVM` | 필수 | True인 경우 `VMName`의 값을 새 VM의 이름으로 사용합니다. False인 경우 `VMName`을 사용하여 기존 VM을 찾아 등록합니다. |
| `VMName` | 선택 사항 | `CreateVM`의 값에 따라 만들거나 등록한 가상 머신의 이름입니다. |
| `VMImage` | 선택 사항 | 만들 VM 이미지의 이름입니다. |
| `VMlocation` | 선택 사항 | 만들거나 등록한 VM의 위치입니다. 이 위치를 지정 하지 않으면 값 `LAlocation` 이 사용 됩니다. |
| `RegisterHW` | 필수 | True인 경우 VM을 하이브리드 작업자로 등록합니다. |
| `WorkerGroupName` | 필수 | Hybrid Worker 그룹의 이름입니다. |

### <a name="manually-run-powershell-commands"></a>PowerShell 명령 수동 실행

Linux Hybrid Runbook Worker를 설치 및 구성하려면 다음 단계를 수행합니다.

1. 관리자 권한 PowerShell 명령 프롬프트에서 또는 [Azure Portal](https://portal.azure.com)의 Cloud Shell에서 다음 명령을 실행하여 Log Analytics 작업 영역에서 Azure Automation 솔루션을 사용하도록 설정합니다.

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Log Analytics 에이전트를 대상 머신에 배포합니다.

    * Azure VM의 경우 [Linux용 가상 머신 확장](../virtual-machines/extensions/oms-linux.md)을 사용하여 Linux용 Log Analytics 에이전트를 설치합니다. 확장 버전은 Azure 가상 머신에 Log Analytics 에이전트를 설치하고 기존 Log Analytics 작업 영역에 가상 머신을 등록합니다. Azure Resource Manager 템플릿, Azure CLI 또는 Azure Policy를 사용하여 [*Linux* 또는 *Windows* VM에 대한 Log Analytics 에이전트 배포](../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 할당할 수 있습니다. 에이전트가 설치되면 머신을 Automation 계정의 Hybrid Runbook Worker 그룹에 추가할 수 있습니다.

    * 비 Azure 머신의 경우 [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하여 Log Analytics 에이전트를 설치할 수 있습니다. Arc 지원 서버는 다음 방법을 사용한 Log Analytics 에이전트 배포를 지원합니다.

        - VM 확장 프레임워크 사용.

            Azure Arc 지원 서버의 이 기능을 사용하면 Log Analytics 에이전트 VM 확장을 비 Azure Windows 및/또는 Linux 서버에 배포할 수 있습니다. VM 확장은 하이브리드 컴퓨터 또는 Arc 사용 서버에 의해 관리되는 서버에서 다음 방법을 이용하여 관리할 수 있습니다.

            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager 템플릿](../azure-arc/servers/manage-vm-extensions-template.md)

        - Azure Policy 사용.

            이 방식을 사용하면 [Linux 또는 Windows Azure Arc 머신에 Log Analytics 에이전트 배포](../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책 정의를 사용하여 Arc 지원 서버에 Log Analytics 에이전트가 설치되어 있는지 감사합니다. 에이전트가 설치 되지 않은 경우에는 재구성 작업을 사용 하 여 에이전트를 자동으로 배포 합니다. VM용 Azure Monitor를 사용 하 여 컴퓨터를 모니터링 하려는 경우 [VM용 Azure Monitor 이니셔티브 사용](../governance/policy/samples/built-in-initiatives.md#monitoring) 을 대신 사용 하 여 Log Analytics 에이전트를 설치 하 고 구성 합니다.

        Azure Policy를 사용하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치하는 것이 좋습니다.

    > [!NOTE]
    > DSC(Desired State Configuration)로 Hybrid Runbook Worker 역할을 지원하는 머신의 구성을 관리하려면 머신을 DSC 노드로 추가해야 합니다.

    > [!NOTE]
    > Linux Hybrid Worker 설치 도중 해당 sudo 권한이 포함된 [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)이 있어야 합니다. 작업자를 설치하려고 시도했는데 계정이 없거나 계정에 적절한 권한이 없는 경우 설치가 실패합니다.

3. 에이전트가 작업 영역에 보고하는지 확인.

    Windows용 Log Analytics 에이전트는 Azure Monitor Log Analytics 작업 영역에 머신을 연결합니다. 머신에 에이전트를 설치하고 작업 영역에 연결하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드됩니다.

    몇 분 후에 에이전트가 Log Analytics 작업 영역에 성공적으로 연결 되 면 다음 쿼리를 실행 하 여 작업 영역에 하트 비트 데이터를 보내고 있는지 확인할 수 있습니다.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    검색 결과에는 컴퓨터에 대 한 하트 비트 레코드가 표시 됩니다 .이 레코드는 서비스에 연결 되 고 보고 되 고 있음을 나타냅니다. 기본적으로 모든 에이전트는 하트비트 레코드를 할당된 작업 영역으로 전달합니다.

4. 다음 명령을 실행하여 Hybrid Runbook Worker 그룹에 머신을 추가합니다. `-w`, `-k`, `-g` 및 `-e` 매개 변수의 값을 지정합니다.

    Automation 계정의 **키** 페이지에서 `-k` 및 `-e` 매개 변수에 필요한 정보를 가져올 수 있습니다. 페이지 왼쪽의 **계정 설정** 섹션에서 **키** 를 선택합니다.

    ![키 관리 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e` 매개 변수의 경우 **URL** 에 대한 값을 복사합니다.

    * `-k` 매개 변수의 경우 **주 액세스 키** 에 대한 값을 복사합니다.

    * `-g` 매개 변수의 경우 새 Linux Hybrid Runbook Worker가 조인해야 하는 Hybrid Runbook Worker 그룹의 이름을 지정합니다. 이 그룹이 Automation 계정에 이미 있으면 현재 머신이 추가됩니다. 이 그룹이 없으면 해당 이름을 사용 하 여 만들어집니다.

    * `-w` 매개 변수에 대해 Log Analytics 작업 영역을 ID를 지정합니다.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. 스크립트가 완료된 후 배포를 확인합니다. Automation 계정의 **Hybrid Runbook Worker 그룹** 페이지에서 **사용자 Hybrid Runbook Worker 그룹** 탭에 새 그룹 또는 기존 그룹과 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. 페이지의 목록에서 그룹을 선택하고 왼쪽 메뉴에서 **Hybrid Workers** 를 선택합니다. **Hybrid Workers** 페이지에서 나열된 그룹의 각 멤버를 확인할 수 있습니다.

    > [!NOTE]
    > Azure VM에 대해 Linux용 Log Analytics 가상 머신 확장을 사용 중인 경우 자동 버전 업그레이드로 인해 Hybrid Runbook Worker에 문제가 발생할 수 있으므로 `autoUpgradeMinorVersion`을 `false`로 설정하는 것이 좋습니다. 수동으로 확장을 업그레이드하는 방법은 [Azure CLI 배포](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)를 참조하세요.

## <a name="turn-off-signature-validation"></a>서명 유효성 검사 해제

기본적으로 Linux Hybrid Runbook Worker는 서명 유효성 검사를 요구합니다. 작업자에 대한 서명되지 않은 Runbook을 실행하는 경우 `Signature validation failed` 오류가 표시됩니다. 서명 유효성 검사를 해제하려면 다음 명령을 실행합니다. 두 번째 매개 변수를 Log Analytics 작업 영역 ID로 바꿉니다.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Hybrid Runbook Worker 제거

Hybrid Runbook Worker에서 `ls /var/opt/microsoft/omsagent` 명령을 사용하여 작업 영역 ID를 가져올 수 있습니다. 작업 영역 ID를 사용하여 이름이 지정된 폴더가 만들어집니다.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 이 스크립트는 머신에서 Linux용 Log Analytics 에이전트를 제거하지 않습니다. Hybrid Runbook Worker 역할의 기능 및 구성만 제거합니다.

## <a name="remove-a-hybrid-worker-group"></a>Hybrid Worker 그룹 제거

Linux 머신의 Hybrid Runbook Worker 그룹을 제거하려면 Windows Hybrid Worker 그룹의 경우와 동일한 단계를 사용합니다. [Hybrid Worker 그룹 제거](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결 - Linux](troubleshoot/hybrid-runbook-worker.md#linux)를 참조하세요.
