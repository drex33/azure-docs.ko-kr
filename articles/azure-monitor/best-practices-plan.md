---
title: Azure Monitor 모범 사례 - 계획
description: Azure Monitor 배포하기 전에 계획 및 디자인에 대한 지침 및 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b04b73cfd7cd2482e14485556df5a477e47a9334
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347977"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Azure Monitor 모범 사례 - 모니터링 전략 및 구성 계획
이 문서는 [Azure Monitor 구성을 위한 시나리오 권장 사항](best-practices.md)일부입니다. 구현을 시작하기 전에 고려해야 할 계획에 대해 설명합니다. 이렇게 하면 선택한 구성 옵션이 특정 비즈니스 요구 사항을 충족합니다.

모니터링 개념에 익숙하지 않은 경우 [Azure용 Microsoft 클라우드 채택 프레임워크](/cloud-adoption-framework/)일부인 [클라우드 모니터링 가이드를](/azure/cloud-adoption-framework/manage/monitor) 시작하세요. 이 가이드에서는 높은 수준의 모니터링 개념을 정의하고 모니터링 환경 및 지원 프로세스에 대한 요구 사항을 정의하기 위한 지침을 제공합니다. 이 문서에서는 특정 계획 단계와 관련된 해당 가이드의 섹션을 참조합니다.
## <a name="understand-azure-monitor-costs"></a>Azure Monitor 비용 이해
모니터링 전략의 핵심 목표는 비용을 최소화하는 것입니다. Azure Monitor 일부 데이터 수집 및 기능에는 비용이 들지 않지만 다른 데이터 수집 및 기능은 특정 구성, 수집된 데이터 양 또는 실행 빈도에 따라 비용이 듭니다. 이 시나리오의 문서에서는 비용이 포함된 권장 사항을 식별하지만, 비용 최적화를 위해 구현을 디자인할 때 Azure Monitor 가격 책정에 대해 잘 알고 있어야 합니다. Azure Monitor 가격 책정에 대한 자세한 내용 및 지침은 다음을 참조하세요.

- [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)
- [Azure Monitor 사용량 및 예상 비용 모니터링](usage-estimated-costs.md)
- [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)
- [Application Insights의 사용량 및 비용 관리](app/pricing.md)

## <a name="define-strategy"></a>전략 정의
모니터링 솔루션을 디자인하고 구현하기 전에 계획의 목표와 요구 사항을 이해할 수 있도록 모니터링 전략을 수립해야 합니다. 전략은 특정 요구 사항, 해당 요구 사항을 가장 잘 충족하는 구성 및 모니터링 환경을 활용하여 애플리케이션의 성능과 안정성을 최대화하는 프로세스를 정의합니다. Azure Monitor 위해 선택하는 구성 옵션은 전략과 일치해야 합니다.

[클라우드 모니터링 가이드: 모니터링 전략을](/azure/cloud-adoption-framework/strategy/monitoring-strategy) 개발할 때 고려해야 하는 다양한 요인에 대한 모니터링 전략 작성을 참조하세요. 또한 완전히 클라우드 기반 모니터링을 하이브리드 [모델과](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) 비교하는 데 도움이 되는 클라우드 배포 모델에 대한 모니터링 전략을 참조해야 합니다. 

## <a name="gather-required-information"></a>필요한 정보 수집
구현의 세부 정보를 확인하기 전에 해당 세부 정보를 정의하는 데 필요한 정보를 수집해야 합니다. 다음 섹션에서는 Azure Monitor 전체 구현에 일반적으로 필요한 정보를 설명했습니다.

 ### <a name="what-needs-to-be-monitored"></a>모니터링해야 하는 것은 무엇인가요?
 모든 클라우드 리소스에 대한 전체 모니터링을 구성할 필요는 없지만 중요한 애플리케이션과 해당 애플리케이션이 의존하는 구성 요소에 집중해야 합니다. 이렇게 하면 모니터링 비용을 줄일 뿐만 아니라 모니터링 환경의 복잡성도 줄어듭니다. 필요한 데이터 정의에 대한 [지침은 클라우드 모니터링 가이드: 올바른](/azure/cloud-adoption-framework/manage/monitor/data-collection) 데이터 수집을 참조하세요.

### <a name="who-needs-to-have-access-and-be-notified"></a>Who 액세스 권한이 있어야 하고 알림을 받아야 합니다.
모니터링 환경을 구성할 때 모니터링 데이터에 액세스할 수 있어야 하는 사용자와 문제가 검색될 때 알림을 받아야 하는 사용자를 결정해야 합니다. 애플리케이션 및 리소스 소유자이거나 중앙 집중식 모니터링 팀이 있을 수 있습니다. 이 정보는 경고에 대한 데이터 액세스 및 알림에 대한 권한을 구성하는 방법을 결정합니다. 다른 사용자에게 특정 정보 집합을 제공하려면 사용자 지정 통합 문서가 필요할 수도 있습니다.

### <a name="service-level-agreements"></a>서비스 수준 계약 
조직에는 애플리케이션의 성능 및 작동 시간에 대한 약정을 정의하는 SLA가 있을 수 있습니다. 이러한 SLA는 경고와 같은 Azure Monitor 시간에 민감한 기능을 구성하는 방법을 결정할 수 있습니다. 또한 모니터링 시나리오의 응답성과 SLA를 충족하는 기능에 영향을 주므로 [Azure Monitor 데이터 대기 시간을](logs/data-ingestion-time.md) 이해해야 합니다.

## <a name="identify-monitoring-services-and-products"></a>모니터링 서비스 및 제품 식별
Azure Monitor 상태 모니터링을 처리하도록 설계되었습니다. 전체 모니터링 솔루션에는 일반적으로 여러 Azure 서비스 및 잠재적으로 다른 제품이 포함됩니다. 추가 솔루션이 필요할 수 있는 다른 모니터링 목표는 클라우드 모니터링 가이드의 기본 모니터링 목표 에 [설명되어 있습니다.](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements) 

다음 섹션에서는 Azure Monitor 함께 사용할 수 있는 다른 서비스 및 제품에 대해 설명합니다. 이 시나리오에는 현재 이러한 솔루션 구현에 대한 지침이 포함되어 있지 않으므로 해당 설명서를 참조해야 합니다.

### <a name="security-monitoring"></a>보안 모니터링
Azure Monitor에 저장된 운영 데이터는 보안 인시던트를 조사하는 데 유용할 수 있지만 Azure의 다른 서비스는 보안을 모니터링하도록 설계되었습니다. Azure의 보안 모니터링은 Microsoft Defender for Cloud 및 Microsoft Sentinel에서 수행됩니다.

- [Microsoft Defender for Cloud는](../security-center/security-center-introduction.md) Azure 리소스 및 하이브리드 서버에 대한 정보를 수집합니다. 보안 이벤트를 수집할 수 있지만 Defender for Cloud는 인벤토리 데이터, 평가 검사 결과 및 정책 감사를 수집하여 취약성을 강조 표시하고 수정 작업을 권장하는 데 중점을 둡니다. 주목할만한 기능으로는 대화형 네트워크 맵, JIT(Just-In-Time) VM 액세스, 적응형 네트워크 강화, 의심스러운 실행 파일을 차단하는 적응형 애플리케이션 제어가 있습니다.

- [서버용 Microsoft Defender는](../security-center/azure-defender.md) Defender for Cloud에서 제공하는 서버 평가 솔루션입니다. 서버용 Defender는 Windows 보안 이벤트를 Log Analytics로 보낼 수 있습니다. Defender for Cloud는 경고 또는 분석을 위해 Windows 보안 이벤트에 의존하지 않습니다. 이 기능을 사용하면 조사 또는 기타 목적을 위해 중앙 집중식으로 이벤트를 보관할 수 있습니다.

- [Microsoft Sentinel은](../sentinel/overview.md) SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Sentinel은 다양한 Microsoft 및 타사 소스에서 보안 데이터를 수집하여 경고, 시각화 및 자동화를 제공합니다. 이 솔루션은 Windows 보안 이벤트를 포함하여 최대한 많은 보안 로그를 통합하는 데 중점을 둡니다. Microsoft Sentinel은 Windows 보안 이벤트 로그를 수집하고 일반적으로 Defender for Cloud와 Log Analytics 작업 영역을 공유합니다. 보안 이벤트는 동일한 작업 영역을 공유하는 경우에만 Microsoft Sentinel 또는 Defender for Cloud에서 수집할 수 있습니다. Defender for Cloud와 달리 보안 이벤트는 Microsoft Sentinel에서 경고 및 분석의 주요 구성 요소입니다.

- [Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)는 엔터프라이즈 네트워크가 지능형 위협을 방지, 감지, 조사 및 대응하는 데 도움이 되도록 설계된 엔터프라이즈 엔드포인트 보안 플랫폼입니다. 이는 주로 Windows 사용자 디바이스를 보호하는 데 중점을 두고 설계되었습니다. Defender for Endpoint는 다양한 운영 체제를 사용하는 워크스테이션, 서버, 태블릿 및 휴대폰에서 보안 문제와 취약성을 모니터링합니다. Defender for Endpoint는 Microsoft Endpoint Manager와 긴밀하게 연계되어 데이터를 수집하고 보안 평가를 제공합니다. 데이터 수집은 주로 ETW 추적 로그를 기반으로 하며 격리된 작업 영역에 저장됩니다.


### <a name="system-center-operations-manager"></a>System Center Operations Manager
가상 머신에서 실행되는 온-프레미스 리소스 및 워크로드를 모니터링하기 위해 System Center Operations Manager 기존 투자를 할 수 있습니다. [이 모니터링을 Azure Monitor 마이그레이션하거나](azure-monitor-operations-manager.md) 하이브리드 구성에서 두 제품을 계속 함께 사용하도록 선택할 수 있습니다. 두 제품을 비교하려면  [클라우드 모니터링 가이드: 플랫폼 모니터링 개요를](/azure/cloud-adoption-framework/manage/monitor/platform-overview) 참조하세요. 하이브리드 구성에서 두 가지를 사용하고 환경에 가장 적합한 모델을 결정하는 지침은 클라우드 배포 모델에 대한 [모니터링 전략을](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview) 참조하세요.



## <a name="next-steps"></a>다음 단계

- [Azure Monitor 데이터 수집을](best-practices-data-collection.md) 구성하는 단계 및 권장 사항은 데이터 수집 구성을 참조하세요.
