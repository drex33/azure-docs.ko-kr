---
title: Azure Arc 지원 서버 개요
description: Azure Arc 지원 서버를 사용하여 Azure 리소스처럼 Azure 외부에서 호스트된 머신을 관리하는 방법을 알아봅니다.
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, 업데이트 관리, 변경 내용 추적, 인벤토리, Runbook, Python, 그래픽, 하이브리드
ms.date: 07/16/2021
ms.topic: overview
ms.openlocfilehash: ed764b3eb743f29c3df033fa36893e8b3eebbc43
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457508"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Azure Arc 지원 서버란?

Azure Arc 지원 서버를 사용하면 Azure ‘외부’의 회사 네트워크 또는 다른 클라우드 공급자에서 호스트된 Windows 및 Linux 물리적 서버와 가상 머신을 관리할 수 있습니다. 이 관리 환경은 원시 Azure 가상 머신을 관리하는 방법과 일치하도록 설계되었습니다. 하이브리드 머신은 Azure에 연결되면 연결된 머신이 되어 Azure에서 리소스로 취급됩니다. 연결된 각 머신에는 리소스 ID가 있고 리소스 그룹에 포함되며, Azure Policy 및 태그 적용과 같은 표준 Azure 구성의 이점을 활용할 수 있습니다. 고객의 온-프레미스 인프라를 관리하는 서비스 공급자는 현재 네이티브 Azure 리소스를 관리하는 방법과 마찬가지로 Azure Arc와 함께 [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md)를 사용하여 여러 고객 환경의 하이브리드 머신을 관리할 수 있습니다.

Azure 외부에 호스팅되는 하이브리드 머신에 이 환경을 제공하려면 Azure에 연결하려는 각 머신에 Azure Connected Machine 에이전트를 설치해야 합니다. 이 에이전트는 다른 기능을 제공하지 않으며, Azure [Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)를 대체하지 않습니다. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-introduction.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.

>[!NOTE]
> 현재 미리 보기로 제공되는 [AMA(Azure Monitor 에이전트)](../../azure-monitor/agents/azure-monitor-agent-overview.md)는 연결된 머신 에이전트를 대체하지 않습니다. Azure Monitor 에이전트는 Windows 및 Linux 머신 모두에 대한 Log Analytics 에이전트, 진단 확장 및 Telegraf 에이전트를 대체합니다. 자세한 내용은 새 에이전트에 대한 Azure Monitor 설명서를 검토하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

Azure Arc 지원 서버에 머신을 연결하면 다음과 같은 구성 관리 및 모니터링 작업을 수행할 수 있습니다.
- Azure 가상 머신의 정책 할당과 동일한 환경을 사용하여 [Azure Policy 게스트 구성](../../governance/policy/concepts/guest-configuration.md)을 할당합니다. 오늘날 대부분의 게스트 구성 정책은 구성을 적용하지 않고 머신 내의 설정만 감사합니다. Arc 지원 서버에서 Azure Policy 게스트 구성 정책을 사용하는 비용을 파악하려면 Azure Policy [가격 책정 가이드](https://azure.microsoft.com/pricing/details/azure-policy/)를 참조하세요.

- [서버용 Azure Defender](../../security-center/defender-for-servers-introduction.md)를 사용하도록 설정한 서버의 경우, Azure Automation [변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md) 및 [Azure Security Center 파일 무결성 모니터링](../../security-center/security-center-file-integrity-monitoring.md)을 사용하여 모니터링되는 서버에 설치된 소프트웨어, Microsoft 서비스, Windows 레지스트리 및 파일, Linux 디먼에 대한 구성 변경을 보고합니다.

- 연결된 머신 게스트 운영 체제 성능을 모니터링하고 애플리케이션 구성 요소를 검색하여 애플리케이션에서 [VM 인사이트](../../azure-monitor/vm/vminsights-overview.md)를 사용해 통신하는 다른 리소스의 프로세스와 종속성을 모니터링합니다.

- 비 Azure Windows 또는 Linux 머신에서 지원되는 [Azure VM 확장](manage-vm-extensions.md)을 사용하여 Azure Monitor Log Analytics 작업 영역과 같은 다른 Azure 서비스로 배포를 간소화합니다. 여기에는 사용자 지정 스크립트 확장을 사용하여 배포 후 구성 또는 소프트웨어 설치를 수행하는 작업이 포함됩니다.

- Azure Automation의 [업데이트 관리](../../automation/update-management/overview.md)를 사용하여 Windows 및 Linux 서버의 운영 체제 업데이트를 관리합니다.

    > [!NOTE]
    > 지금은 Arc 지원 서버에서 직접 업데이트 관리를 사용하도록 설정할 수 없습니다. 요구 사항과 서버를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Automation 계정에서 업데이트 관리 사용](../../automation/update-management/enable-from-automation-account.md)을 참조하세요.

- 지능형 위협 탐지를 위한 비 Azure 서버를 포함하고 [Azure Security Center](../../security-center/security-center-introduction.md) 또는 [Azure Defender](../../security-center/azure-defender.md)를 사용하여 잠재적인 보안 위협을 사전에 모니터링합니다.

- 위협 탐지, 취약성 관리 및 잠재적 보안 위협을 사전에 모니터링하는 [Azure Defender](../../security-center/azure-defender.md)를 통해 포함된 [엔드포인트용 Microsoft Defender](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)를 사용하여 비 Azure 서버를 보호합니다.

하이브리드 머신에서 Log Analytics 작업 영역에 수집되어 저정된 로그 데이터에는 이제 리소스 ID와 같은 머신 관련 속성이 포함되어 있습니다. [resource-context](../../azure-monitor/logs/design-logs-deployment.md#access-mode) 로그 액세스를 지원하는 데 사용할 수 있습니다.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>지원되는 지역

Azure Arc 지원 서버가 지원되는 지역의 정확한 목록은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 페이지를 참조하세요.

대부분의 경우 설치 스크립트를 만들 때 선택한 위치는 머신의 위치와 지리적으로 가장 가까운 Azure 지역이어야 합니다. 미사용 데이터는 사용자가 지정한 지역이 포함된 Azure 지리 내에 저장되며, 데이터 상주 요구 사항이 있는 경우 선택한 지역에 영향을 줄 수도 있습니다. 머신이 연결된 Azure 지역이 작동 중단의 영향을 받는 경우 연결된 머신에는 영향을 주지 않지만 Azure를 사용하는 관리 작업은 완료하지 못할 수 있습니다. 지역 가동 중단이 발생하고 지리적으로 중복된 서비스를 지원하는 여러 위치가 있는 경우 각 위치의 머신을 다른 Azure 지역에 연결하는 것이 가장 좋습니다.

연결된 머신에 대한 다음 메타데이터 정보는 Azure Arc 머신 리소스가 구성된 지역에서 수집 및 저장됩니다.

- 운영 체제 이름 및 버전
- 컴퓨터 이름
- 컴퓨터 FQDN(정규화된 도메인 이름)
- Connected Machine 에이전트 버전

예를 들어 머신이 미국 동부 지역에 있는 Azure Arc에 등록된 경우 이 데이터는 미국 지역에 저장됩니다.

### <a name="supported-environments"></a>지원되는 환경

Arc 지원 서버는 Azure ‘외부’에서 호스트된 물리적 서버와 가상 머신의 관리를 지원합니다. VM을 호스트하는 하이브리드 클라우드 환경에 대한 구체적인 세부 정보는 [Connected Machine 에이전트 사전 요구 사항](agent-overview.md#supported-environments)을 참조하세요.

> [!NOTE]
> Arc 지원 서버는 Azure에서 실행되는 가상 머신을 관리할 수 있도록 설계되지 않았거나 지원하지 않습니다.

### <a name="agent-status"></a>에이전트 상태

Connected Machine 에이전트는 5분마다 정기적인 하트비트 메시지를 서비스에 보냅니다. 서비스가 머신에서 이러한 하트비트 메시지 수신을 중지하면 해당 머신은 오프라인으로 간주되며 15~30분 내에 포털에서 상태가 자동으로 **연결 끊김** 으로 변경됩니다. Connected Machine 에이전트로부터 후속 하트비트 메시지를 받으면 상태가 자동으로 **연결됨** 으로 변경됩니다.

## <a name="next-steps"></a>다음 단계

* 여러 하이브리드 머신에서 Arc 지원 서버를 평가하거나 사용하도록 설정하기 전에 [연결된 머신 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보, 배포 방법을 파악합니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.
