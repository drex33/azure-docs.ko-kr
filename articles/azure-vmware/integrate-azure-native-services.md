---
title: Azure 네이티브 서비스를 사용하여 VM 모니터링 및 보호
description: Azure VMware Solution 워크로드를 모니터링하고 관리하기 위해 Microsoft Azure 네이티브 도구를 통합 및 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: fa3a30ce3908494e1fdf0470781f4057279fe001
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714955"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>Azure 네이티브 서비스를 사용하여 VM 모니터링 및 보호

Microsoft Azure 네이티브 서비스를 사용하면 하이브리드 환경(Azure, Azure VMware Solution, 온-프레미스)에서 VM(가상 머신)을 모니터링, 관리, 보호할 수 있습니다. 이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 Azure 네이티브 서비스를 통합합니다. 또한 도구를 사용하여 수명 주기 전체에서 VM을 관리하는 방법을 알아봅니다. 

Azure VMware Solution과 통합할 수 있는 Azure 네이티브 서비스는 다음과 같습니다.

- **Azure Arc** 는 Azure 관리를 Azure VMware Solution, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯한 모든 인프라로 확장합니다. [azure Arc 사용 서버](../azure-arc/servers/overview.md) 를 사용 하면 azure *외부* , 회사 네트워크 또는 다른 클라우드 공급자에서 호스트 되는 Windows 및 Linux 물리적 서버와 가상 컴퓨터를 관리할 수 있습니다. [Azure Arc 지원 Kubernetes](../azure-arc/kubernetes/overview.md)를 사용하여 Azure VMware Solution 환경에서 호스트되는 Kubernetes 클러스터를 연결할 수 있습니다. 

- **Azure Monitor** 는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동합니다. Azure Monitor는 배포가 필요하지 않습니다.  게스트 운영 체제 성능을 모니터링하여 Azure VMware Solution 또는 온-프레미스 VM에 대한 애플리케이션 종속성을 검색하고 매핑할 수 있습니다. Azure Monitor의 Log Analytics 작업 영역에서는 Log Analytics 에이전트 또는 확장을 사용하여 로그 및 성능 카운터를 수집할 수 있습니다. 

   Azure Monitor를 사용하여 [모니터링 및 분석할 다양한 원본](../azure-monitor/agents/data-sources.md)의 데이터와 [분석, 시각화 및 경고를 위한 다양한 유형의 데이터](../azure-monitor/data-platform.md)를 수집할 수 있습니다. 리소스의 많은 사용, 누락된 패치, 디스크 공간 부족, VM의 하트비트와 같은 사용자 환경 문제를 식별하는 경고 규칙을 만들 수도 있습니다. ITSM(IT 서비스 관리) 도구에 경고를 전송하여 검색된 이벤트에 대한 자동화된 응답을 설정할 수 있습니다. 메일을 통해 경고 검색 알림을 보낼 수도 있습니다.

- **Azure Security Center** 는 데이터 센터의 보안을 강화하고 클라우드 또는 온-프레미스에서 하이브리드 워크로드를 통해 지능형 위협 방지 기능을 제공합니다. Azure VMware Solution VM의 취약성을 평가하고, 필요에 따라 경고를 발생시킨 다음, 해결을 위해 Azure Monitor에 전달합니다. 예를 들어 누락된 운영 체제 패치, 잘못된 보안 구성과 [엔드포인트 보호](../security-center/security-center-services.md)를 평가합니다. 또한 [Azure Security Center](azure-security-integration.md)에서 보안 정책을 정의할 수도 있습니다.

- **Azure 업데이트 관리** 는 Azure Automation의 하이브리드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리합니다. 패치 준수를 모니터링하고 패치 편차 경고를 수정하기 위해 Azure Monitor로 전달합니다. 저장된 데이터를 사용하여 VM에 대한 업데이트 상태를 평가하기 위해 Azure 업데이트 관리를 Log Analytics 작업 영역에 연결해야 합니다.

- **Log Analytics 작업 영역** 은 로그 데이터를 저장합니다. 각 작업 영역에는 데이터를 저장할 고유한 데이터 리포지토리 및 구성이 있습니다. Log Analytics 에이전트를 통해 Azure VMware Solution VM을 모니터링할 수 있습니다. Log Analytics 작업 영역에 연결된 머신은 설치된 소프트웨어, Microsoft 서비스, Windows 레지스트리와 파일, 모니터링되는 서버의 Linux 디먼에 대한 변경 내용의 데이터를 수집하기 위해 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)를 사용합니다. 데이터를 사용할 수 있는 경우 에이전트는 처리를 위해 Azure Monitor 로그에 데이터를 보냅니다. Azure Monitor 로그는 수신한 데이터에 논리를 적용하고, 데이터를 기록하고, 분석에 사용할 수 있게 만듭니다. Azure Arc 사용 서버 [VM 확장 지원](../azure-arc/servers/manage-vm-extensions.md)을 사용하여 VM에 Log Analytics 에이전트를 배포합니다. 



## <a name="benefits"></a>이점

- 하이브리드 환경(Azure, Azure VMware Solution 및 온-프레미스)에서 VM을 관리하는 데 Azure 네이티브 서비스를 사용할 수 있습니다.
- Azure, Azure VMware Solution 및 온-프레미스 VM의 통합 모니터링 및 가시성.
- Azure Automation에서 Azure 업데이트 관리를 사용하여 Windows 및 Linux 머신의 운영 체제 업데이트를 관리할 수 있습니다. 
- Azure Security Center는 다음과 같은 고급 위협 방지 기능을 제공합니다.
    - 파일 무결성 모니터링
    - 파일리스 보안 경고
    - 운영 체제 패치 평가
    - 잘못된 보안 구성 평가
    - Endpoint Protection 평가 
- 새 VM 및 기존 VM에 대한 Azure Arc 사용 서버 VM 확장 지원을 사용하여 Log Analytics 에이전트를 쉽게 배포합니다. 
- Azure Monitor의 Log Analytics 작업 영역에서는 Log Analytics 에이전트 또는 확장을 사용하여 로그 및 성능 카운터를 수집할 수 있습니다. 데이터 및 로그를 단일 지점으로 수집하고 해당 데이터를 다른 Azure 네이티브 서비스에 제공합니다. 
- 추가적으로 얻을 수 있는 Azure Monitor의 이점은 다음과 같습니다. 
    - 원활한 모니터링 
    - 더 나은 인프라 가시성 
    - 인스턴트 알림 
    - 자동 해결 
    - 비용 효율성 


## <a name="topology"></a>토폴로지

이 다이어그램은 Azure VMware Solution VM에 대한 통합 모니터링 아키텍처를 보여줍니다.

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="통합 Azure 모니터링 아키텍처를 보여주는 다이어그램." border="false":::

Log Analytics 에이전트는 Azure, Azure VMware Solution 및 온-프레미스 VM에서 로그 데이터를 수집할 수 있도록 합니다. 로그 데이터는 Azure Monitor 로그에 전송되고 Log Analytics 작업 영역에 저장됩니다. 새로운 및 기존의 VM에 대한 Arc 사용 서버 [VM 확장 지원](../azure-arc/servers/manage-vm-extensions.md)을 사용하여 Log Analytics 에이전트를 배포할 수 있습니다. 

Log Analytics 작업 영역에서 로그를 수집하고 나면 Azure Security Center를 사용하여 Log Analytics 작업 영역을 구성하여 Azure VMware 솔루션 VM의 취약성 상태를 평가하고 위험 취약성에 대해 경고를 발생시킬 수 있습니다.  예를 들어 누락된 운영 체제 패치, 잘못된 보안 구성과 [엔드포인트 보호](../security-center/security-center-services.md)를 평가합니다.

경고 검색, 위협 표시 유형, 헌팅 및 위협 응답을 위해 Azure Sentinel을 사용하여 Log Analytics 작업 영역을 구성할 수 있습니다. 위의 다이어그램에서 Azure Security Center는 Azure Security Center 커넥터를 사용하여 Azure Sentinel에 연결됩니다. Azure Security Center는 Azure Sentinel에 환경 취약성을 전달하여 인시던트를 만들고 다른 위협에 매핑합니다. 또한 예약된 규칙 쿼리를 만들어서 원치 않는 활동을 검색하고 이를 인시던트로 변환할 수 있습니다.


## <a name="before-you-start"></a>시작하기 전에

Azure를 처음 접하는 경우 앞에서 언급한 서비스를 잘 모른다면 다음 문서를 검토하세요.

- [Automation 계정 인증 개요](../automation/automation-security-overview.md)
- [Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md) 및 [Azure Monitor](../azure-monitor/overview.md)
- Azure Security Center에 대한 [계획](../security-center/security-center-planning-and-operations-guide.md) 및 [지원 플랫폼](../security-center/security-center-os-coverage.md)
- [VM용 Azure Monitor 사용 개요](../azure-monitor/vm/vminsights-enable-overview.md)
- [Azure Arc 사용 서버란 무엇인가요?](../azure-arc/servers/overview.md) 및 [Azure Arc 사용 Kubernetes란 무엇인가요?](../azure-arc/kubernetes/overview.md)
- [업데이트 관리 개요](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>Azure 업데이트 관리 사용

Azure Automation의 [Azure 업데이트 관리](../automation/update-management/overview.md)는 하이브리드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리합니다. 패치 준수를 모니터링하고 패치 편차 경고를 수정하기 위해 Azure Monitor로 전달합니다. 저장된 데이터를 사용하여 VM에 대한 업데이트 상태를 평가하기 위해 Azure 업데이트 관리를 Log Analytics 작업 영역에 연결해야 합니다.

1. Azure 업데이트 관리에 Log Analytics 작업 영역을 추가하려면 먼저 [Azure Automation 계정을 만들어야](../automation/automation-create-standalone-account.md) 합니다. 

   >[!TIP]
   >[ARM(Azure Resource Manager) 템플릿을 사용하여 Automation 계정을 만들](../automation/quickstart-create-automation-account-template.md) 수 있습니다. ARM 템플릿을 사용하면 다른 배포 방법과 비교하여 단계가 줄어듭니다.

1. [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md). 원하는 경우 [CLI](../azure-monitor/logs/resource-manager-workspace.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 또는 [Azure Resource Manager 템플릿](../azure-monitor/logs/resource-manager-workspace.md)을 통해 작업 영역을 만들 수도 있습니다.

1. [Automation 계정에서 업데이트 관리를 사용하도록 설정합니다.](../automation/update-management/enable-from-automation-account.md) 이 프로세스에서는 Log Analytics 작업 영역을 자동화 계정에 연결합니다. 
 
1. 업데이트 관리를 사용하도록 설정했으면 [VM에 업데이트를 배포하고 결과를 검토](../automation/update-management/deploy-updates.md)할 수 있습니다. 

## <a name="enable-azure-security-center"></a>Azure Security Center 활성화

Azure VMware Solution VM의 취약성을 평가하고 필요에 따라 경고를 발생시킵니다. 해당 보안 경고를 Azure Monitor에 전달하여 해결할 수 있습니다. 자세한 내용은 [VM에 지원되는 기능](../security-center/security-center-services.md)을 참조하세요.

Azure Security Center는 다음을 비롯한 다양한 기능을 제공합니다.
- 파일 무결성 모니터링
- 파일리스 공격 탐지
- 운영 체제 패치 평가 
- 잘못된 보안 구성 평가
- Endpoint Protection 평가

>[!NOTE]
>Azure Security Center는 배포가 필요하지 않은 미리 구성된 도구이지만 Azure Portal에서 사용하도록 설정해야 합니다. 


1. [Security Center에 Azure VMware Solution VM을 추가합니다](azure-security-integration.md#add-azure-vmware-solution-vms-to-security-center). 

2. [Security Center에서 Azure Defender를 사용합니다](../security-center/enable-azure-defender.md). Security Center는 잠재적인 보안 문제에 대해 VM을 평가합니다. 또한 개요 탭에서 [보안 권장 사항](../security-center/security-center-recommendations.md)을 제공합니다. 

3. Azure Security Center에서 [보안 정책을 정의합니다](../security-center/tutorial-security-policy.md). 

자세한 내용은 [Azure VMware Solution과 Azure Security Center 통합](azure-security-integration.md)을 참조하세요.



## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>VM을 Azure Arc 사용 서버에 온보딩

Azure 관리를 Azure VMware Solution, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯한 모든 인프라로 확장합니다.  여러 Windows 또는 Linux VM에 대해 Azure Arc 사용 서버를 사용하도록 설정하는 방법에 대한 자세한 내용은 [대규모로 Azure에 하이브리드 머신 연결](../azure-arc/servers/onboard-service-principal.md)을 참조하세요.



## <a name="onboard-hybrid-kubernetes-clusters-with-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes를 사용하여 하이브리드 Kubernetes 클러스터 온보딩

Azure Arc 사용 Kubernetes를 사용하여 Azure VMware Solution 환경에서 호스트되는 Kubernetes 클러스터를 연결합니다. 자세한 내용은 [Azure Arc 사용 온보딩 서비스 주체 만들기](../azure-arc/kubernetes/create-onboarding-service-principal.md)를 참조하세요.


## <a name="deploy-the-log-analytics-agent"></a>Log Analytics 에이전트 배포

Log Analytics 에이전트를 통해 Azure VMware Solution VM을 모니터링합니다. Log Analytics 작업 영역에 연결된 가상 머신은 설치된 소프트웨어, Microsoft 서비스, Windows 레지스트리와 파일, 모니터링되는 서버의 Linux 디먼에 대한 변경 내용의 데이터를 수집하기 위해 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)를 사용합니다. 데이터를 사용할 수 있는 경우 에이전트는 처리를 위해 Azure Monitor 로그에 데이터를 보냅니다. Azure Monitor 로그는 수신한 데이터에 논리를 적용하고, 데이터를 기록하고, 분석에 사용할 수 있게 만듭니다.

Azure Arc 지원 서버 VM 확장 지원을 사용하여 Log Analytics [에이전트를 배포합니다.](../azure-arc/servers/manage-vm-extensions.md)




## <a name="enable-azure-monitor"></a>Azure Monitor 사용

[모니터링 및 분석할 다양한 원본](../azure-monitor/agents/data-sources.md)의 데이터와 [분석, 시각화 및 경고를 위한 다양한 유형의 데이터](../azure-monitor/data-platform.md)를 수집할 수 있습니다. 리소스의 많은 사용, 누락된 패치, 디스크 공간 부족, VM의 하트비트와 같은 사용자 환경 문제를 식별하는 경고 규칙을 만들 수도 있습니다. ITSM(IT 서비스 관리) 도구에 경고를 전송하여 검색된 이벤트에 대한 자동화된 응답을 설정할 수 있습니다. 메일을 통해 경고 검색 알림을 보낼 수도 있습니다.

게스트 운영 체제 성능을 모니터링하여 Azure VMware Solution 또는 온-프레미스 VM에 대한 애플리케이션 종속성을 검색하고 매핑합니다. Azure Monitor의 Log Analytics 작업 영역에서는 Log Analytics 에이전트 또는 확장을 사용하여 로그 및 성능 카운터를 수집할 수 있습니다. 


1. [Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md)

1. [VM용 Azure Monitor 사용 개요](../azure-monitor/vm/vminsights-enable-overview.md)

1. [VM용 Azure Monitor에 대한 Log Analytics 작업 영역을 구성합니다.](../azure-monitor/vm/vminsights-configure-workspace.md)

1. 환경에서 문제를 식별하는 경고 규칙을 만듭니다.

   - [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md).

   - [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md).

   - 자동화된 작업 및 알림을 설정하는 [작업 규칙](../azure-monitor/alerts/alerts-action-rules.md).

   - [IT 서비스 관리 커넥터를 사용하여 ITSM 도구에 Azure 연결](../azure-monitor/alerts/itsmc-overview.md).


## <a name="next-steps"></a>다음 단계

Azure VMware Solution 네트워크 및 상호 연결 개념을 살펴보았으므로 이제 [Azure VMware Solution과 Azure Security Center 통합](azure-security-integration.md)에 대해 알아볼 수 있습니다.