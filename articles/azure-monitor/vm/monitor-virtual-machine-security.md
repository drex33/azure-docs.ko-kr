---
title: 'Azure Monitor를 사용하여 가상 머신 모니터링: 보안'
description: 가상 머신의 보안을 모니터링하는 서비스와 이러한 서비스가 Azure Monitor와 어떻게 관련되는지를 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 793c4a365f2dc04d0bb138e816a6b31aa68042e8
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177387"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Azure Monitor를 사용하여 가상 머신 모니터링: 보안 모니터링
이 문서는 [Azure Monitor에서 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md) 시나리오의 일부입니다. 가상 머신의 보안을 모니터링하는 Azure 서비스와 이러한 서비스가 Azure Monitor와 어떻게 관련되는지를 설명합니다. Azure Monitor는 가상 머신 및 기타 클라우드 리소스의 가용성과 성능을 모니터링하도록 설계되었습니다. Azure Monitor에 저장된 운영 데이터는 보안 인시던트를 조사하는 데 유용할 수 있지만 Azure의 다른 서비스는 보안을 모니터링하도록 설계되었습니다. 

> [!IMPORTANT]
> 보안 서비스는 Azure Monitor와 독립적인 자체 비용이 있습니다. 이러한 서비스를 구성하기 전에 가격 책정 정보를 참조하여 사용량에 대한 적절한 투자를 결정하세요.

## <a name="azure-services-for-security-monitoring"></a>보안 모니터링을 위한 Azure 서비스
Azure Monitor는 Windows 이벤트(보안 이벤트 제외), 성능 카운터, 로그 및 Syslog를 포함하여 활동 로그, 메트릭 및 Log Analytics 지원 소스와 같은 운영 데이터에 중점을 두고 있습니다. Azure의 보안 모니터링은 Azure Security Center 및 Azure Sentinel에 의해 수행됩니다. 이러한 서비스마다 추가 비용이 부과되므로 구현하기 전에 사용자 환경에서 해당 값을 확인해야 합니다.


## <a name="integration-with-azure-monitor"></a>Azure Monitor와의 통합
다음 표에서는 Azure Monitor와 보안 서비스와의 통합 지점이 나와 있습니다. 모든 서비스는 동일한 Log Analytics 에이전트를 사용하므로 가상 머신에 배포되는 다른 구성 요소가 없기 때문에 복잡성이 줄어듭니다. Security Center 및 Azure Sentinel은 로그 쿼리를 사용하여 다른 서비스에서 수집한 데이터의 상호 관계를 지정할 수 있도록 Log Analytics 작업 영역에 데이터를 저장합니다. 보안 데이터와 가용성 및 성능 데이터를 단일 보기에 결합하는 사용자 지정 통합 문서를 만들 수도 있습니다.

| 통합 지점       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender for Endpoint |
|:---|:---|:---|:---|:---|
| 보안 이벤트 수집     |   | X | X | X |
| Log Analytics 작업 영역에 데이터 저장 | X | X | X |   | 
| Log Analytics 에이전트 사용     | X | X | X | X | 



## <a name="agent-deployment"></a>에이전트 배포
Azure 가상 머신에 Log Analytics 에이전트를 자동으로 배포하도록 Security Center를 구성할 수 있습니다. 이는 동일한 에이전트를 배포하는 Azure Monitor에서 중복되는 것처럼 보일 수 있지만, 각각 고유한 구성을 수행하므로 둘 다 사용하도록 설정하는 것이 좋습니다. 예를 들어, Security Center가 Azure Monitor에서 이미 모니터링하고 있는 머신을 프로비저닝하려는 경우 이미 설치되어 있는 에이전트를 사용하여 Security Center 작업 영역에 대한 구성을 추가합니다.

## <a name="next-steps"></a>다음 단계

* [가상 머신에 대해 수집된 모니터링 데이터 분석](monitor-virtual-machine-analyze.md)
* [수집된 데이터에서 경고 만들기](monitor-virtual-machine-alerts.md)
