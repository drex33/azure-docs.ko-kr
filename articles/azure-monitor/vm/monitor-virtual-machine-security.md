---
title: 'Azure Monitor를 사용하여 가상 머신 모니터링: 보안'
description: 가상 머신의 보안을 모니터링하는 서비스와 이러한 서비스가 Azure Monitor와 어떻게 관련되는지를 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674283"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Azure Monitor를 사용하여 가상 머신 모니터링: 보안 모니터링
이 문서는 [Azure Monitor에서 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md) 시나리오의 일부입니다. 가상 머신의 보안을 모니터링하는 Azure 서비스와 이러한 서비스가 Azure Monitor와 어떻게 관련되는지를 설명합니다. Azure Monitor는 가상 머신 및 기타 클라우드 리소스의 가용성과 성능을 모니터링하도록 설계되었습니다. Azure Monitor에 저장된 운영 데이터는 보안 인시던트를 조사하는 데 유용할 수 있지만 Azure의 다른 서비스는 보안을 모니터링하도록 설계되었습니다. 

> [!IMPORTANT]
> 보안 서비스는 Azure Monitor와 독립적인 자체 비용이 있습니다. 이러한 서비스를 구성하기 전에 가격 책정 정보를 참조하여 사용량에 대한 적절한 투자를 결정하세요.

## <a name="azure-services-for-security-monitoring"></a>보안 모니터링을 위한 Azure 서비스
Azure Monitor는 Windows 이벤트(보안 이벤트 제외), 성능 카운터, 로그 및 Syslog를 포함하여 활동 로그, 메트릭 및 Log Analytics 지원 소스와 같은 운영 데이터에 중점을 두고 있습니다. Azure의 보안 모니터링은 Azure Security Center 및 Azure Sentinel에 의해 수행됩니다. 이러한 서비스마다 추가 비용이 부과되므로 구현하기 전에 사용자 환경에서 해당 값을 확인해야 합니다.

[Azure Security Center](../../security-center/security-center-introduction.md)는 Azure 리소스 및 하이브리드 서버에 대한 정보를 수집합니다. Security Center는 보안 이벤트를 수집할 수는 있지만 인벤토리 데이터, 평가 검사 결과 및 정책 감사를 수집하여 취약성을 강조하고 정정 작업을 권장하는 데 중점을 둡니다. 주목할만한 기능으로는 대화형 네트워크 맵, JIT(Just-In-Time) VM 액세스, 적응형 네트워크 강화, 의심스러운 실행 파일을 차단하는 적응형 애플리케이션 제어가 있습니다.

[Azure Defender for Servers](../../security-center/azure-defender.md)는 Security Center에서 제공하는 서버 평가 솔루션입니다. Defender for Servers는 Log Analytics에 Windows 보안 이벤트를 보낼 수 있습니다. Security Center는 경고 또는 분석에 Windows 보안 이벤트를 사용하지 않습니다. 이 기능을 사용하면 조사 또는 기타 목적을 위해 중앙 집중식으로 이벤트를 보관할 수 있습니다.

[Azure Sentinel](../../sentinel/overview.md)은 SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동 대응) 솔루션입니다. Sentinel은 다양한 Microsoft 및 타사 소스에서 보안 데이터를 수집하여 경고, 시각화 및 자동화를 제공합니다. 이 솔루션은 Windows 보안 이벤트를 포함하여 최대한 많은 보안 로그를 통합하는 데 중점을 둡니다. 또한 Azure Sentinel은 Windows 보안 이벤트 로그를 수집하고 일반적으로 Log Analytics 작업 영역을 Security Center와 일반적으로 공유할 수도 있습니다. 보안 이벤트는 동일한 작업 영역을 공유하는 경우 Azure Sentinel 또는 Security Center에서만 수집할 수 있습니다. Security Center와 달리 보안 이벤트는 Azure Sentinel에서 경고 및 분석의 주요 구성 요소입니다.

[Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)는 엔터프라이즈 네트워크가 지능형 위협을 방지, 감지, 조사 및 대응하는 데 도움이 되도록 설계된 엔터프라이즈 엔드포인트 보안 플랫폼입니다. 이는 주로 Windows 사용자 디바이스를 보호하는 데 중점을 두고 설계되었습니다. Defender for Endpoint는 다양한 운영 체제를 사용하는 워크스테이션, 서버, 태블릿 및 휴대폰에서 보안 문제와 취약성을 모니터링합니다. Defender for Endpoint는 Microsoft Endpoint Manager와 긴밀하게 연계되어 데이터를 수집하고 보안 평가를 제공합니다. 데이터 수집은 주로 ETW 추적 로그를 기반으로 하며 격리된 작업 영역에 저장됩니다.

## <a name="integration-with-azure-monitor"></a>Azure Monitor와의 통합
다음 표에서는 Azure Monitor와 보안 서비스와의 통합 지점이 나와 있습니다. 모든 서비스는 동일한 Log Analytics 에이전트를 사용하므로 가상 머신에 배포되는 다른 구성 요소가 없기 때문에 복잡성이 줄어듭니다. Security Center 및 Azure Sentinel은 로그 쿼리를 사용하여 다른 서비스에서 수집한 데이터의 상호 관계를 지정할 수 있도록 Log Analytics 작업 영역에 데이터를 저장합니다. 보안 데이터와 가용성 및 성능 데이터를 단일 보기에 결합하는 사용자 지정 통합 문서를 만들 수도 있습니다.

| 통합 지점       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender for Endpoint |
|:---|:---|:---|:---|:---|
| 보안 이벤트 수집     |   | X | X | X |
| Log Analytics 작업 영역에 데이터 저장 | X | X | X |   | 
| Log Analytics 에이전트 사용     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>작업 영역 디자인 고려 사항
[Azure Monitor를 사용하여 가상 머신 모니터링: 모니터링 구성](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace)에 설명된 대로 Azure Monitor 및 보안 서비스에는 Log Analytics 작업 영역이 필요합니다. 특정 요구 사항에 따라 공통 작업 영역을 공유하거나 보안 데이터에서 가용성 및 성능 데이터를 구분할 수 있습니다. 작업 영역 구성을 설계할 때 고려해야 하는 논리에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../logs/design-logs-deployment.md)을 참조하세요.

## <a name="agent-deployment"></a>에이전트 배포
Azure 가상 머신에 Log Analytics 에이전트를 자동으로 배포하도록 Security Center를 구성할 수 있습니다. 이는 동일한 에이전트를 배포하는 Azure Monitor에서 중복되는 것처럼 보일 수 있지만, 각각 고유한 구성을 수행하므로 둘 다 사용하도록 설정하는 것이 좋습니다. 예를 들어, Security Center가 Azure Monitor에서 이미 모니터링하고 있는 머신을 프로비저닝하려는 경우 이미 설치되어 있는 에이전트를 사용하여 Security Center 작업 영역에 대한 구성을 추가합니다.

## <a name="next-steps"></a>다음 단계

* [가상 머신에 대해 수집된 모니터링 데이터 분석](monitor-virtual-machine-analyze.md)
* [수집된 데이터에서 경고 만들기](monitor-virtual-machine-alerts.md)
