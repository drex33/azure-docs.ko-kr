---
title: Arc 사용 서버에 Log Analytics 에이전트 배포
description: 이 문서에서는 로컬 데이터 센터 또는 다른 클라우드 환경에서 Azure Arc 사용 서버에 등록 된 Windows 및 Linux 기반 컴퓨터에 Log Analytics 에이전트를 배포 하는 다양 한 방법을 검토 합니다.
ms.date: 10/22/2021
ms.topic: conceptual
ms.openlocfilehash: 173044962dc4ca6a8b01b4e5fbb1fa12c9ba461b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312778"
---
# <a name="understand-deployment-options-for-the-log-analytics-agent-on-azure-arc-enabled-servers"></a>Azure Arc 사용 서버의 Log Analytics 에이전트에 대 한 배포 옵션 이해

Azure Monitor에서는 Log Analytics 에이전트를 설치 하 고 Azure Arc 사용 서버에 등록 된 컴퓨터 또는 서버를 서비스에 연결 하는 여러 방법을 지원 합니다. Azure Arc 사용 서버는 배포 후 구성 및 자동화 작업을 제공 하는 Azure VM 확장 프레임 워크를 지원 하 여 Azure Vm과 같은 하이브리드 컴퓨터의 관리를 간소화할 수 있게 해줍니다.

Log Analytics 에이전트는 다음을 수행 하려는 경우에 필요 합니다.

* [VM insights](../../azure-monitor/vm/vminsights-overview.md)를 사용 하 여 컴퓨터 또는 서버에서 실행 중인 모든 워크 로드와 운영 체제를 모니터링 합니다. [Azure Monitor](../../azure-monitor/overview.md)의 다른 기능을 사용 하 여 추가로 분석 하 고 경고 합니다.
* 클라우드 또는 [Microsoft 센티널](../../sentinel/overview.md) [용 microsoft Defender](../../security-center/security-center-introduction.md) 를 사용 하 여 Azure에서 보안 모니터링을 수행 합니다.
* [Azure Automation 업데이트 관리](../../automation/update-management/overview.md)를 사용 하 여 운영 체제 업데이트를 관리 합니다.
* [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md)를 사용 하 여 인벤토리를 수집 하 고 변경 내용을 추적 합니다.
* [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)를 사용 하 여 컴퓨터에서 직접 또는 환경의 리소스에 대해 자동화 runbook을 실행 합니다.

이 문서에서는 사용자 환경의 여러 프로덕션 물리적 서버 또는 가상 컴퓨터에서 Log Analytics 에이전트 VM 확장에 대 한 배포 방법을 검토 하 여 조직에 가장 적합 한 방식을 결정 하는 데 도움을 줍니다. 새 Azure Monitor 에이전트에 관심이 있고 자세한 비교를 보려는 경우 [Azure Monitor 에이전트 개요](../../azure-monitor//agents/agents-overview.md) 문서를 검토 합니다.  

## <a name="installation-options"></a>설치 옵션

여러 가지 방법을 사용 하 여 하나의 방법 또는 하나의 조합을 사용 하 여 VM 확장을 설치할 수 있습니다. 이 섹션에서는 고려해 야 할 각 항목에 대해 설명 합니다.

### <a name="using-arc-enabled-servers"></a>Arc 사용 서버 사용

이 메서드는 [PowerShell](manage-vm-extensions-powershell.md), [Azure CLI](manage-vm-extensions-cli.md)또는 [ARM (Azure Resource Manager) 템플릿을](manage-vm-extensions-template.md)사용 하 여 [Azure Portal](manage-vm-extensions-portal.md)에서 VM 확장의 설치, 관리 및 제거를 관리 하는 기능을 지원 합니다.

#### <a name="advantages"></a>장점

* 테스트를 위해 유용할 수 있습니다.
* 관리할 컴퓨터가 몇 대 있는 경우 유용 합니다.

#### <a name="disadvantages"></a>단점

* Azure Resource Manager 템플릿을 사용할 때 자동화 기능이 제한 됩니다. 그렇지 않은 경우 시간이 오래 걸립니다.
* 는 단일 원호 사용 서버에만 집중할 수 있으며 여러 인스턴스가 될 수는 없습니다.
* 는 보고할 단일 작업 영역을 지정 하는 것만 지원 합니다. PowerShell 또는 Azure CLI를 사용 하 여 최대 4 개의 작업 영역에 보고 하도록 Log Analytics Windows 에이전트 VM 확장을 구성 해야 합니다.
* 는 포털에서 종속성 에이전트를 배포 하는 것을 지원 하지 않습니다. PowerShell, Azure CLI 또는 ARM 템플릿만 사용할 수 있습니다.

### <a name="using-azure-policy"></a>Azure Policy 사용

Azure Policy를 사용 하 여 사용자 환경의 컴퓨터에 Log Analytics 에이전트 VM 확장을 대규모로 배포 하 고 구성 준수를 유지 관리할 수 있습니다. 이를 위해서는 **azure arc에서 Log Analytics 확장 구성 Linux 서버** 에서  /  **azure arc 사용 Windows 서버 정책 정의에 대 한 Log Analytics 확장을 구성** 하거나 **VM용 Azure Monitor** 정책 이니셔티브를 사용 하도록 설정 합니다.

Azure Policy에는 Azure Monitor와 관련된 몇 가지 미리 작성된 정의가 포함되어 있습니다. **모니터링** 범주의 기본 제공 정책에 대 한 전체 목록은 [Azure Policy 기본 제공 Azure Monitor에 대 한 정의](../../azure-monitor/policy-reference.md)를 참조 하세요.

#### <a name="advantages"></a>장점

* VM 확장이 제거 되 면 정책 평가 후 다시 설치 합니다.
* 새 Azure Arc 사용 서버가 Azure에 등록 될 때 VM 확장을 식별 하 고 설치 합니다.
* 는 보고할 단일 작업 영역을 지정 하는 것만 지원 합니다. PowerShell 또는 Azure CLI를 사용 하 여 최대 4 개의 작업 영역에 보고 하도록 Log Analytics Windows 에이전트 VM 확장을 구성 해야 합니다.

#### <a name="disadvantages"></a>단점

* Azure Arc 사용 *운영 체제* **서버** **에서 LOG ANALYTICS 확장 구성** 정책은 Log Analytics VM 확장만 설치 하 고 지정 된 Log Analytics 작업 영역에 보고 하도록 에이전트를 구성 합니다. VM 정보를 사용 하 여 운영 체제 성능을 모니터링 하 고 실행 중인 프로세스 및 다른 리소스에 대 한 종속성을 매핑하려면 정책 이니셔티브 **VM용 Azure Monitor 사용** 을 적용 해야 합니다. Log Analytics VM 확장 및 필요한 종속성 에이전트 VM 확장을 모두 설치 하 고 구성 합니다.
* 표준 준수 평가 주기는 24 시간 마다 한 번입니다. 구독 또는 리소스 그룹에 대한 평가 검사는 Azure CLI, Azure PowerShell, REST API에 대한 호출 또는 Azure Policy 준수 검사 GitHub 작업을 통해 시작할 수 있습니다. 자세한 내용은 [평가 트리거](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)를 참조 하세요.

### <a name="using-azure-automation"></a>Azure Automation 사용

Azure Automation의 프로세스 자동화 운영 환경과 PowerShell 및 Python runbook에 대 한 지원을 통해 사용자 환경의 컴퓨터에 대 한 Log Analytics 에이전트 VM 확장의 배포를 자동화할 수 있습니다.

#### <a name="advantages"></a>장점

* 는 스크립팅된 스크립팅 언어를 사용 하 여 배포 및 구성을 자동화 하는 데 스크립팅된 메서드를 사용할 수 있습니다.
* 사용자가 정의 하 고 제어 하는 일정에 따라 실행 됩니다.
* 관리 id를 사용 하 여 Automation 계정에서 Arc 사용 서버에 안전 하 게 인증 합니다.

#### <a name="disadvantages"></a>단점

* Azure Automation 계정이 필요 합니다.
* Azure Automation에서 runbook을 작성 하 고 관리 하는 경험을 제공 합니다.
* 대상 운영 체제에 따라 PowerShell 또는 Python을 기반으로 runbook 만들기

## <a name="next-steps"></a>다음 단계

* Azure Automation 업데이트 관리를 사용 하 여 운영 체제 업데이트를 관리 하려면 [Automation 계정에서 사용](../../automation/update-management/enable-from-automation-account.md) 을 검토 한 다음 단계에 따라 작업 영역에 보고 하는 컴퓨터를 사용 하도록 설정 합니다.

* Azure Automation 변경 내용 추적 및 인벤토리를 사용 하 여 변경 내용을 추적 하려면 [자동화 계정에서 사용](../../automation/change-tracking/enable-from-automation-account.md) 을 검토 한 다음 단계에 따라 작업 영역에 보고 하는 컴퓨터를 사용 하도록 설정 합니다.

* Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용 하 여 Arc 사용 서버에 등록 된 서버 또는 컴퓨터에서 직접 runbook을 실행할 수 있습니다. [HYBRID RUNBOOK WORKER VM 확장 배포](../../automation/extension-based-hybrid-runbook-worker-install.md) 문서를 참조 하세요.

* Microsoft 센티널로 보안 관련 이벤트 수집을 시작 하려면 [Microsoft 센티널에](scenario-onboard-azure-sentinel.md)등록 또는 클라우드 용 microsoft defender를 사용 하 여 수집을 참조 하세요. [클라우드 용 microsoft defender에](../../security-center/quickstart-onboard-machines.md)등록을 참조 하세요.

* 컴퓨터가 얼마나 잘 작동 하 고 있는지 확인 하 고 검색 된 응용 프로그램 구성 요소를 확인 하려면 VM insights [모니터 성능](../../azure-monitor/vm/vminsights-performance.md) 및 [맵 종속성](../../azure-monitor/vm/vminsights-maps.md) 문서를 참조 하세요.
