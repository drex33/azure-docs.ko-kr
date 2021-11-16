---
title: Azure Arc 지원 서버 개요
description: Azure Arc 지원 서버를 사용하여 Azure 리소스처럼 Azure 외부에서 호스트된 머신을 관리하는 방법을 알아봅니다.
ms.date: 09/30/2021
ms.topic: overview
ms.openlocfilehash: c390cbbb6f08f4f9082b0764125ab9a14407de95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287270"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Azure Arc 지원 서버란?

Azure Arc 지원 서버를 사용하면 Azure ‘외부’의 회사 네트워크 또는 다른 클라우드 공급자에서 호스트된 Windows 및 Linux 물리적 서버와 가상 머신을 관리할 수 있습니다. 이 관리 환경은 원시 Azure 가상 머신을 관리하는 방법과 일치하도록 설계되었습니다. 하이브리드 머신은 Azure에 연결되면 연결된 머신이 되어 Azure에서 리소스로 취급됩니다. 연결된 각 컴퓨터에는 리소스 ID가 있어 컴퓨터가 리소스 그룹에 포함될 수 있습니다. 이제 표준 Azure 구성(예: Azure Policy 및 태그 적용)의 이점을 제공합니다. 고객의 온-프레미스 인프라를 관리하는 서비스 공급자는 현재 네이티브 Azure 리소스를 관리하는 방법과 마찬가지로 [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md)를 사용하여 여러 고객 환경의 하이브리드 컴퓨터를 관리할 수 있습니다.

하이브리드 컴퓨터에서 이 환경을 제공하려면 각 컴퓨터에 Azure Connected Machine 에이전트를 설치해야 합니다. 이 에이전트는 다른 기능을 제공하지 않으며, Azure [Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)를 대체하지 않습니다. 다음과 같은 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.

* 컴퓨터에서 실행되는 OS 및 워크로드를 사전에 모니터링하려는 경우
* Automation Runbook 또는 솔루션(예: 업데이트 관리)을 사용하여 관리하려는 경우
* [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)와 같은 다른 Azure 서비스를 사용하는 경우

## <a name="supported-cloud-operations"></a>지원되는 클라우드 작업 

컴퓨터를 Azure Arc 지원 서버에 연결하면 다음 표에 설명된 대로 다음과 같은 작동 기능을 수행할 수 있습니다.

|작업 함수 |설명 | 
|--------------------|------------|
|**거버넌스** ||
| Azure Policy |[Azure Policy 게스트 구성](../../governance/policy/concepts/guest-configuration.md)을 할당하여 컴퓨터 내의 설정을 감사합니다. Arc 지원 서버에서 Azure Policy 게스트 구성 정책을 사용하는 비용을 파악하려면 Azure Policy [가격 책정 가이드](https://azure.microsoft.com/pricing/details/azure-policy/)를 참조하세요.|
|**보호** ||
| Microsoft Defender for Cloud | 위협 탐지, 취약성 관리 및 잠재적 보안 위협을 사전에 모니터링하는 [Microsoft Defender for Cloud](../../security-center/defender-for-servers-introduction.md)를 통해 포함된 [엔드포인트용 Microsoft Defender](/microsoft-365/security/defender-endpoint)를 사용하여 비 Azure 서버를 보호합니다. Microsoft Defender for Cloud는 검색된 위협의 경고 및 수정 제안을 표시합니다. |
| Microsoft Sentinel | Arc 지원 서버에 연결된 컴퓨터는 [Microsoft Sentinel](scenario-onboard-azure-sentinel.md) 사용하여 보안 관련 이벤트를 수집하고 다른 데이터 원본과 상호 연결하도록 구성할 수 있습니다. |
|**구성** ||
| Azure Automation |PowerShell 및 Python [runbook](../../automation/automation-runbook-execution.md)을 사용하여 자주 및 시간이 오래 걸리는 관리 작업을 자동화합니다.<br> [변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md)를 사용하여, 설치된 소프트웨어, Microsoft 서비스, Windows 레지스트리 및 파일, Linux 디먼에 대한 구성 변경을 평가합니다.<br> [업데이트 관리](../../automation/update-management/overview.md)를 사용하여 Windows 및 Linux 서버의 운영 체제 업데이트를 관리합니다. |
| Azure Automanage(미리 보기) | [ARC 지원 서버에 Automanage Machine](../../automanage/automanage-arc.md)을 사용할 때는 Azure 서비스 집합을 온보딩합니다. |
| VM 확장 | 비 Azure Windows 또는 Linux 컴퓨터에 대해 지원되는 [Arc 지원 서버 VM 확장](manage-vm-extensions.md)을 사용하여 배포 후 구성 및 자동화 작업을 제공합니다. |
|**모니터**|
| Azure Monitor | 연결된 컴퓨터 게스트 운영 체제 성능을 모니터링하고 애플리케이션 구성 요소를 검색하여 애플리케이션에서 [VM 인사이트](../../azure-monitor/vm/vminsights-overview.md)를 사용해 통신하는 다른 리소스와의 종속성 및 프로세스를 모니터링합니다. [Log Analytics 에이전트](../../azure-monitor/agents/agents-overview.md#log-analytics-agent)를 사용하여 컴퓨터에서 실행되는 운영 체제 또는 워크로드에서 성능 데이터 및 이벤트와 같은 다른 로그 데이터를 수집합니다. 데이터는 [Log Analytics 작업 영역](../../azure-monitor/logs/design-logs-deployment.md)의 테이블에 저장됩니다. |

> [!NOTE]
> 지금은 Azure Arc 지원 서버에서 직접 Azure Automation 업데이트 관리를 사용할 수 없습니다. 요구 사항과 서버를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Automation 계정에서 업데이트 관리 사용](../../automation/update-management/enable-from-automation-account.md)을 참조하세요.

하이브리드 컴퓨터에서 Log Analytics 작업 영역에 수집되어 저정된 로그 데이터에는 이제 [resource-context](../../azure-monitor/logs/design-logs-deployment.md#access-mode) 로그 액세스를 지원하기 위해 리소스 ID와 같은 컴퓨터 특정 속성이 포함되어 있습니다.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

Azure Arc 지원 서버를 사용하여 하이브리드 환경과 다중 클라우드 환경에서 Azure 모니터링, 보안 및 업데이트 서비스를 구현하는 방법에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/mJnmXBrU1ao]

## <a name="supported-regions"></a>지원되는 지역

Azure Arc 지원 서버가 지원되는 지역의 정확한 목록은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 페이지를 참조하세요.

대부분의 경우 설치 스크립트를 만들 때 선택한 위치는 머신의 위치와 지리적으로 가장 가까운 Azure 지역이어야 합니다. 미사용 데이터는 사용자가 지정한 지역이 포함된 Azure 지리 내에 저장되며, 데이터 상주 요구 사항이 있는 경우 선택한 지역에 영향을 줄 수도 있습니다. 머신이 연결된 Azure 지역이 작동 중단의 영향을 받는 경우 연결된 머신에는 영향을 주지 않지만 Azure를 사용하는 관리 작업은 완료하지 못할 수 있습니다. 지역 가동 중단이 발생하고 지리적으로 중복된 서비스를 지원하는 여러 위치가 있는 경우 각 위치의 머신을 다른 Azure 지역에 연결하는 것이 가장 좋습니다.

연결된 머신에 대한 다음 메타데이터 정보는 Azure Arc 머신 리소스가 구성된 지역에서 수집 및 저장됩니다.

- 운영 체제 이름 및 버전
- 컴퓨터 이름
- 컴퓨터 FQDN(정규화된 도메인 이름)
- Connected Machine 에이전트 버전

예를 들어 머신이 미국 동부 지역에 있는 Azure Arc에 등록된 경우 이 데이터는 미국 지역에 저장됩니다.

## <a name="supported-environments"></a>지원되는 환경

Azure Arc 지원 서버에서 Azure *외부* 에서 호스트된 물리적 서버와 가상 머신을 관리할 수 있습니다. VM을 호스트하는 하이브리드 클라우드 환경에 대한 구체적인 세부 정보는 [Connected Machine 에이전트 사전 요구 사항](agent-overview.md#supported-environments)을 참조하세요.

> [!NOTE]
> Azure Arc 지원 서버는 Azure에서 실행되는 가상 머신을 관리하도록 설계되지 않았거나 이를 지원하지 않습니다.

## <a name="agent-status"></a>에이전트 상태

Connected Machine 에이전트는 5분마다 정기적인 하트비트 메시지를 서비스에 보냅니다. 서비스가 머신에서 이러한 하트비트 메시지 수신을 중지하면 해당 머신은 오프라인으로 간주되며 15~30분 내에 포털에서 상태가 자동으로 **연결 끊김** 으로 변경됩니다. Connected Machine 에이전트로부터 후속 하트비트 메시지를 받으면 상태가 자동으로 **연결됨** 으로 변경됩니다.

## <a name="service-limits"></a>서비스 제한

Azure Arc 지원 서버에는 각 리소스 그룹에서 만들 수 있는 인스턴스 수에 대한 제한이 있습니다. 구독 또는 서비스 수준에는 제한이 없습니다. 존재하는 리소스 종류 제한에 대한 자세한 내용은 [리소스 인스턴스 제한](../../azure-resource-manager/management/resources-without-resource-group-limit.md#microsofthybridcompute) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 여러 하이브리드 컴퓨터에서 Azure Arc 지원 서버를 평가하거나 사용하기 전에 [Connected Machine 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 파악합니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.
