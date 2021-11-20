---
title: Azure Monitor 모범 사례-계획
description: Azure Monitor를 배포 하기 전에 계획 및 디자인을 위한 지침과 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 06d4cfc0b614b46c8aa19c9dd0b94238236a8b15
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132862288"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Azure Monitor 모범 사례-모니터링 전략 및 구성 계획
이 문서는 [Azure Monitor를 구성](best-practices.md)하는 권장 사항 시나리오의 일부입니다. 구현을 시작 하기 전에 고려해 야 하는 계획을 설명 합니다. 이렇게 하면 선택한 구성 옵션이 특정 비즈니스 요구 사항을 충족할 수 있습니다.

모니터링 개념에 대해 잘 모르는 경우 [Azure에 대 한 Microsoft 클라우드 채택 프레임 워크](/azure/cloud-adoption-framework/)의 일부인 [클라우드 모니터링 가이드](/azure/cloud-adoption-framework/manage/monitor) 부터 시작 하세요. 이 가이드는 모니터링의 개략적인 개념을 정의 하 고 모니터링 환경 및 지원 프로세스에 대 한 요구 사항을 정의 하는 지침을 제공 합니다. 이 문서에서는 특정 계획 단계와 관련 된이 가이드의 섹션을 참조 합니다.
## <a name="understand-azure-monitor-costs"></a>Azure Monitor 비용 이해
모니터링 전략의 핵심 목표는 비용을 최소화 하는 것입니다. Azure Monitor의 일부 데이터 수집 및 기능에는 비용이 없지만, 특정 구성, 수집 된 데이터 양 또는 실행 빈도에 따라 비용이 부과 됩니다. 이 시나리오의 문서에서는 비용을 포함 하는 권장 사항을 식별 하지만 비용 최적화를 위해 구현을 디자인 하는 경우 Azure Monitor 가격 책정에 대해 잘 알고 있어야 합니다. Azure Monitor 가격 책정에 대 한 자세한 내용 및 지침은 다음을 참조 하세요.

- [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)
- [Azure Monitor의 사용량 및 예상 비용 모니터링](usage-estimated-costs.md)
- [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)
- [Application Insights의 사용량 및 비용 관리](app/pricing.md)

## <a name="define-strategy"></a>전략 정의
모니터링 솔루션을 설계 하 고 구현 하기 전에 계획의 목표와 요구 사항을 이해할 수 있도록 모니터링 전략을 수립 해야 합니다. 전략은 특정 요구 사항, 이러한 요구 사항을 가장 잘 충족 하는 구성, 모니터링 환경을 활용 하 여 응용 프로그램의 성능과 안정성을 극대화 하는 프로세스를 정의 합니다. Azure Monitor에 대해 선택 하는 구성 옵션은 전략과 일치 해야 합니다.

모니터링 전략을 개발할 때 고려해 야 하는 여러 요소에 대 한 [모니터링 전략](/azure/cloud-adoption-framework/strategy/monitoring-strategy) 작성을 참조 하세요. 또한 완벽 한 클라우드 기반 모니터링과 하이브리드 모델을 비교 하는 데 도움이 되는 [클라우드 배포 모델에 대 한 모니터링 전략](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) 을 참조 해야 합니다. 

## <a name="gather-required-information"></a>필요한 정보 수집
구현에 대 한 세부 정보를 확인 하기 전에 해당 세부 정보를 정의 하는 데 필요한 정보를 수집 해야 합니다. 다음 섹션에서는 Azure Monitor의 완전 한 구현에 일반적으로 필요한 정보를 설명 합니다.

 ### <a name="what-needs-to-be-monitored"></a>무엇을 모니터링 해야 하나요?
 모든 클라우드 리소스에 대 한 전체 모니터링을 구성 하는 것은 아니지만 중요 한 응용 프로그램 및 해당 응용 프로그램에 종속 된 구성 요소에 집중 하는 것이 좋습니다. 이렇게 하면 모니터링 비용을 줄일 수 있을 뿐 아니라 모니터링 환경의 복잡성도 줄일 수 있습니다. 필요한 데이터 정의에 대 한 지침은 [클라우드 모니터링 가이드: 올바른 데이터 수집](/azure/cloud-adoption-framework/manage/monitor/data-collection) 을 참조 하세요.

### <a name="who-needs-to-have-access-and-be-notified"></a>Who에 액세스할 수 있어야 하며 알림이 필요 합니다.
모니터링 환경을 구성할 때 모니터링 데이터에 액세스 해야 하는 사용자와 문제가 검색 될 때 사용자에 게 알릴 사용자를 결정 해야 합니다. 응용 프로그램 및 리소스 소유자 이거나, 중앙 집중식 모니터링 팀이 있을 수 있습니다. 이 정보는 경고에 대 한 데이터 액세스 및 알림에 대 한 사용 권한을 구성 하는 방법을 결정 합니다. 사용자 지정 통합 문서에서 특정 정보 집합을 다른 사용자에 게 제공 해야 할 수도 있습니다.

### <a name="service-level-agreements"></a>서비스 수준 계약 
조직에는 응용 프로그램의 성능 및 작동 시간에 대 한 약정을 정의 하는 Sla가 있을 수 있습니다. 이러한 Sla는 경고와 같은 Azure Monitor의 시간이 중요 한 기능을 구성 해야 하는 방법을 결정할 수 있습니다. 또한 모니터링 시나리오의 응답성 및 Sla를 충족 하는 기능에 영향을 주므로 [Azure Monitor의 데이터 대기 시간](logs/data-ingestion-time.md) 을 이해 해야 합니다.

## <a name="identify-monitoring-services-and-products"></a>모니터링 서비스 및 제품 식별
Azure Monitor는 상태 및 상태 모니터링을 해결 하도록 설계 되었습니다. 전체 모니터링 솔루션은 일반적으로 여러 Azure 서비스와 잠재적으로 다른 제품을 포함 합니다. 추가 솔루션이 필요할 수 있는 다른 모니터링 목표는 [기본 모니터링 목표](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements)의 클라우드 모니터링 가이드에 설명 되어 있습니다. 

다음 섹션에서는 Azure Monitor와 함께 사용할 수 있는 다른 서비스 및 제품에 대해 설명 합니다. 이 시나리오는 현재 이러한 솔루션을 구현 하는 데 대 한 지침을 포함 하지 않으므로 해당 설명서를 참조 해야 합니다.

### <a name="security-monitoring"></a>보안 모니터링
Azure Monitor에 저장된 운영 데이터는 보안 인시던트를 조사하는 데 유용할 수 있지만 Azure의 다른 서비스는 보안을 모니터링하도록 설계되었습니다. Azure의 보안 모니터링은 클라우드 및 Microsoft 센티널 용 Microsoft Defender에 의해 수행 됩니다.

- [Microsoft Defender For Cloud는](../security-center/security-center-introduction.md) Azure 리소스 및 하이브리드 서버에 대 한 정보를 수집 합니다. 보안 이벤트를 수집할 수는 있지만 Defender for Cloud는 인벤토리 데이터 수집, 평가 검사 결과 및 정책 감사를 중심으로 취약성을 강조 하 고 정정 작업을 권장 합니다. 주목할만한 기능으로는 대화형 네트워크 맵, JIT(Just-In-Time) VM 액세스, 적응형 네트워크 강화, 의심스러운 실행 파일을 차단하는 적응형 애플리케이션 제어가 있습니다.

- [서버에 대 한 Microsoft defender](../security-center/azure-defender.md) 는 Defender for Cloud에서 제공 하는 서버 평가 솔루션입니다. 서버에 대 한 Defender는 Log Analytics에 Windows 보안 이벤트를 보낼 수 있습니다. Defender for Cloud는 경고 또는 분석에 Windows 보안 이벤트를 사용 하지 않습니다. 이 기능을 사용하면 조사 또는 기타 목적을 위해 중앙 집중식으로 이벤트를 보관할 수 있습니다.

- [Microsoft 센티널](../sentinel/overview.md) 은 siem (보안 정보 이벤트 관리) 및 대화 충성도 (security orchestration 자동화 된 응답) 솔루션입니다. Sentinel은 다양한 Microsoft 및 타사 소스에서 보안 데이터를 수집하여 경고, 시각화 및 자동화를 제공합니다. 이 솔루션은 Windows 보안 이벤트를 포함하여 최대한 많은 보안 로그를 통합하는 데 중점을 둡니다. Microsoft 센티널은 Windows 보안 이벤트 로그를 수집 하 고 일반적으로 클라우드 용 Defender와 Log Analytics 작업 영역을 공유할 수도 있습니다. 보안 이벤트는 동일한 작업 영역을 공유 하는 경우 Microsoft 센티널 또는 Defender for Cloud 에서만 수집할 수 있습니다. 클라우드의 Defender와 달리 보안 이벤트는 Microsoft 센티널에서 경고 및 분석의 주요 구성 요소입니다.

- [Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)는 엔터프라이즈 네트워크가 지능형 위협을 방지, 감지, 조사 및 대응하는 데 도움이 되도록 설계된 엔터프라이즈 엔드포인트 보안 플랫폼입니다. 이는 주로 Windows 사용자 디바이스를 보호하는 데 중점을 두고 설계되었습니다. Defender for Endpoint는 다양한 운영 체제를 사용하는 워크스테이션, 서버, 태블릿 및 휴대폰에서 보안 문제와 취약성을 모니터링합니다. Defender for Endpoint는 Microsoft Endpoint Manager와 긴밀하게 연계되어 데이터를 수집하고 보안 평가를 제공합니다. 데이터 수집은 주로 ETW 추적 로그를 기반으로 하며 격리된 작업 영역에 저장됩니다.


### <a name="system-center-operations-manager"></a>System Center Operations Manager
온-프레미스 리소스와 가상 머신에서 실행 되는 워크 로드를 모니터링 하는 System Center Operations Manager에 기존 투자가 있을 수 있습니다. [이 모니터링을 Azure Monitor로 마이그레이션하거나](azure-monitor-operations-manager.md) 하이브리드 구성에서 두 제품을 함께 사용 하도록 선택할 수 있습니다. 두 제품의 비교는  [클라우드 모니터링 가이드: 플랫폼 모니터링 개요](/azure/cloud-adoption-framework/manage/monitor/platform-overview) 를 참조 하세요. 하이브리드 구성에서 두 가지를 사용 하 고 환경에 가장 적합 한 모델을 결정 하는 방법에 대 한 지침은 [클라우드 배포 모델에 대 한 모니터링 전략](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) 을 참조 하세요.



## <a name="next-steps"></a>다음 단계

- Azure Monitor에서 데이터 수집을 구성 하는 단계 및 권장 사항은 [데이터 수집 구성](best-practices-data-collection.md) 을 참조 하세요.
