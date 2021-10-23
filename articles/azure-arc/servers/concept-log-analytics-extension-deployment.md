---
title: Arc 지원 서버에 Log Analytics 에이전트 배포
description: 이 문서에서는 로컬 데이터 센터 또는 다른 클라우드 환경에서 Azure Arc 지원 서버에 등록된 Windows 및 Linux 기반 머신에 Log Analytics 에이전트를 배포하는 다양한 방법을 검토합니다.
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 150b0c032108cd6d0aad84b6bcadf1b7101c7ef1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273735"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Azure Arc 지원 서버의 Log Analytics 에이전트에 대한 배포 옵션 이해

Azure Monitor Log Analytics 에이전트를 설치하고 Azure Arc 사용 가능한 서버에 등록된 컴퓨터 또는 서버를 서비스에 연결하는 여러 가지 방법을 지원합니다. Azure Arc 지원 서버는 배포 후 구성 및 자동화 작업을 제공하는 Azure VM 확장 프레임워크를 지원하므로 Azure VM과 마찬가지로 하이브리드 머신의 관리를 간소화할 수 있습니다.

다음을 수행하려면 Log Analytics 에이전트가 필요합니다.

* [VM 인사이트를](../../azure-monitor/vm/vminsights-overview.md)사용하여 운영 체제, 컴퓨터 또는 서버에서 실행되는 모든 워크로드를 모니터링합니다. [Azure Monitor](../../azure-monitor/overview.md)다른 기능을 사용하여 추가로 분석하고 경고합니다.
* Azure Security Center [또는](../../sentinel/overview.md) [Azure Sentinel](../../security-center/security-center-introduction.md) 사용하여 Azure에서 보안 모니터링을 수행합니다.
* [Azure Automation 업데이트 관리](../../automation/update-management/overview.md)사용하여 운영 체제 업데이트를 관리합니다.
* [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md)사용하여 인벤토리를 수집하고 변경 내용을 추적합니다.
* [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)사용하여 컴퓨터 및 환경의 리소스에 대해 직접 Automation Runbook을 실행합니다.

이 문서에서는 사용자 환경의 여러 프로덕션 물리적 서버 또는 가상 머신에서 Log Analytics 에이전트 VM 확장에 대한 배포 방법을 검토하여 조직에 가장 적합한 방법을 결정하는 데 도움을 줍니다. 새 Azure Monitor 에이전트에 관심이 있고 자세한 비교를 보려면 Azure Monitor 에이전트 개요 문서를 [검토하세요.](../../azure-monitor//agents/agents-overview.md)  

## <a name="installation-options"></a>설치 옵션

다른 방법을 사용하여 하나의 방법 또는 조합을 사용하여 VM 확장을 설치할 수 있습니다. 이 섹션에서는 고려해야 할 각 방법을 설명합니다.

### <a name="using-arc-enabled-servers"></a>Arc 지원 서버 사용

이 메서드는 [PowerShell,](manage-vm-extensions-powershell.md)Azure CLI 또는 [ARM(Azure Resource Manager) 템플릿을](manage-vm-extensions-template.md)사용하여 [Azure Portal](manage-vm-extensions-portal.md)VM 확장의 [](manage-vm-extensions-cli.md)설치, 관리 및 제거를 지원합니다.

#### <a name="advantages"></a>장점

* 테스트를 위해 유용할 수 있습니다.
* 관리할 컴퓨터가 몇 대 있는 경우에 유용합니다.

#### <a name="disadvantages"></a>단점

* Azure Resource Manager 템플릿을 사용하는 경우 자동화가 제한되며, 그렇지 않으면 시간이 많이 걸립니다.
* 여러 인스턴스가 아닌 단일 Arc 지원 서버에만 집중할 수 있습니다.
* 보고할 단일 작업 영역 지정만 지원합니다. PowerShell 또는 Azure CLI 사용하여 최대 4개의 작업 영역에 보고하도록 Log Analytics Windows 에이전트 VM 확장을 구성해야 합니다.
* 포털에서 종속성 에이전트 배포를 지원하지 않습니다. PowerShell, Azure CLI 또는 ARM 템플릿만 사용할 수 있습니다.

### <a name="using-azure-policy"></a>Azure Policy 사용

Azure Policy 사용하여 Log Analytics 에이전트 VM 확장을 사용자 환경의 머신에 대규모로 배포하고 구성 준수를 유지할 수 있습니다. 이렇게 하려면 **Azure Arc 사용하도록 설정된 Linux 서버에서 Log Analytics 확장 구성 Azure Arc 사용하도록 설정된** Windows  /  **서버에서 Log Analytics 확장 구성** 정책 정의 또는 VM용 Azure Monitor 정책 **사용** 이니셔티브를 사용합니다.

Azure Policy에는 Azure Monitor와 관련된 몇 가지 미리 작성된 정의가 포함되어 있습니다. **모니터링** 범주에 있는 기본 제공 정책의 전체 목록은 [Azure Monitor 대한 Azure Policy 기본 제공 정의를 참조하세요.](../../azure-monitor/policy-reference.md)

#### <a name="advantages"></a>장점

* VM 확장이 제거되면 정책 평가 후에 VM 확장을 다시 설치합니다.
* 새 Azure Arc 지원 서버가 Azure에 등록될 때 VM 확장을 식별하고 설치합니다.
* 보고할 단일 작업 영역 지정만 지원합니다. PowerShell 또는 Azure CLI 사용하여 최대 4개의 작업 영역에 보고하도록 Log Analytics Windows 에이전트 VM 확장을 구성해야 합니다.

#### <a name="disadvantages"></a>단점

* Azure Arc 사용하도록 설정된 *운영 체제* **서버에서** **Log Analytics 확장 구성** 정책은 Log Analytics VM 확장만 설치하고 지정된 Log Analytics 작업 영역에 보고하도록 에이전트를 구성합니다. 운영 체제 성능을 모니터링하고 실행 중인 프로세스 및 다른 리소스에 대한 의존도를 매핑하는 데 VM 인사이트에 관심이 있는 경우 정책 이니셔티브 **VM용 Azure Monitor 사용을** 적용해야 합니다. Log Analytics VM 확장과 종속성 에이전트 VM 확장을 모두 설치하고 구성합니다. 이 확장은 필수입니다.
* 표준 규정 준수 평가 주기는 24시간마다 한 번입니다. 구독 또는 리소스 그룹에 대한 평가 검사는 Azure CLI, Azure PowerShell, REST API에 대한 호출 또는 Azure Policy 준수 검사 GitHub 작업을 통해 시작할 수 있습니다. 자세한 내용은 [평가 트리거를 참조하세요.](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

### <a name="using-azure-automation"></a>Azure Automation 사용

Azure Automation 프로세스 자동화 운영 환경과 PowerShell 및 Python Runbook에 대한 지원을 통해 Log Analytics 에이전트 VM 확장을 사용자 환경의 머신에 대규모로 배포하는 작업을 자동화할 수 있습니다.

#### <a name="advantages"></a>장점

* 스크립팅된 메서드를 사용하여 익숙한 스크립팅 언어를 사용하여 배포 및 구성을 자동화할 수 있습니다.
* 정의하고 제어하는 일정에 따라 실행됩니다.
* 관리 ID를 사용하여 Automation 계정에서 Arc 지원 서버에 안전하게 인증합니다.

#### <a name="disadvantages"></a>단점

* Azure Automation 계정이 필요합니다.
* Azure Automation Runbook 작성 및 관리 경험
* 대상 운영 체제에 따라 PowerShell 또는 Python을 기반으로 Runbook을 만듭니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation 업데이트 관리 사용하여 운영 체제 업데이트를 관리하려면 [Automation 계정에서 사용을](../../automation/update-management/enable-from-automation-account.md) 검토한 다음 단계에 따라 작업 영역에 보고하는 머신을 사용하도록 설정합니다.

* Azure Automation 변경 내용 추적 및 인벤토리 사용하여 변경 내용을 추적하려면 [Automation 계정에서 사용을](../../automation/change-tracking/enable-from-automation-account.md) 검토한 다음 단계에 따라 작업 영역에 보고하는 머신을 사용하도록 설정합니다.

* Azure Automation 사용자 Hybrid Runbook Worker 기능을 사용하여 Arc 지원 서버에 등록된 서버 또는 컴퓨터에서 Runbook을 직접 실행할 수 있습니다. Hybrid Runbook Worker [VM 확장 배포](../../automation/extension-based-hybrid-runbook-worker-install.md) 문서를 참조하세요.

* Azure Sentinel 사용하여 보안 관련 이벤트 수집을 시작하려면 Azure Sentinel [온보딩을](scenario-onboard-azure-sentinel.md)참조하거나 Azure Security Center 사용하여 수집하려면 [Azure Security Center 온보딩을](../../security-center/quickstart-onboard-machines.md)참조하세요.

* 컴퓨터가 얼마나 잘 수행되고 있는지 확인하고 검색된 애플리케이션 구성 요소를 보려면 VM 인사이트 [성능 모니터링](../../azure-monitor/vm/vminsights-performance.md) 및 [맵 의존성](../../azure-monitor/vm/vminsights-maps.md) 문서를 참조하세요.