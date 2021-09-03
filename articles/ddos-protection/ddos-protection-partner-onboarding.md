---
title: Azure DDoS Protection 표준 파트너 제휴
description: Azure DDoS Protection 표준에서 구현되는 파트너 제휴 기회를 이해합니다.
ms.service: ddos-protection
documentationcenter: na
author: aletheatoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 7f8031f74845e957468ac8f47b7d4661c0f7ce23
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108084"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Azure DDoS Protection 표준 파트너 제휴
이 문서에서는 Azure DDoS Protection 표준에서 구현되는 파트너 제휴 기회에 대해 설명합니다. 이 문서는 제품 관리자 및 비즈니스 개발 역할이 투자 경로를 이해하도록 돕고 파트너 제휴 가치 제안에 대한 통찰을 제공하기 위해 작성되었습니다.

## <a name="background"></a>배경
DDoS(분산 서비스 거부) 공격은 애플리케이션을 클라우드로 전환하는 고객이 우려하는 가장 큰 가용성 및 보안 문제 중 하나입니다. DDoS 공격의 일반적인 동기는 강탈 및 핵티비즘으로, 이러한 공격은 비교적 쉽고 빠르게 시작할 수 있어 유형, 규모, 발생 빈도가 지속적으로 늘고 있습니다.

Azure DDoS Protection은 글로벌 규모의 Azure 네트워킹을 활용하여 가장 정교한 DDoS 위협에 대해서도 대책을 제공합니다. 이 서비스는 가상 네트워크에 배포된 애플리케이션 및 리소스에 대한 향상된 DDoS 완화 기능을 제공합니다.

기술 파트너는 DDoS 공격으로 인한 가용성 및 안정성 우려를 해결하기 위해 기본적으로 Azure DDoS Protection 표준을 사용하여 고객의 리소스를 보호할 수 있습니다.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS Protection 표준 소개
Azure DDoS Protection 표준은 레이어 3 및 레이어 4 DDoS 공격에 대해 향상된 DDoS 완화 기능을 제공합니다. 다음 섹션에서는 Azure DDoS Protection 표준 서비스의 주요 기능을 간략하게 설명합니다.

### <a name="adaptive-real-time-tuning"></a>적응형 실시간 조정
모든 보호되는 애플리케이션에 대해 Azure DDoS Protection 표준은 애플리케이션의 트래픽 프로필 패턴을 기반으로 DDoS 완화 정책 임계값을 자동으로 조정합니다. 서비스에서는 두 개의 정보를 사용하여 이 사용자 지정을 수행합니다.

- 레이어 3 및 4의 고객별(IP별) 트래픽 패턴 자동 학습
- Azure의 크기 조정을 통해 트래픽의 상당 부분을 흡수할 수 있음을 고려하여 거짓 긍정 최소화

![적응형 실시간 조정](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>공격 분석, 원격 분석, 모니터링 및 경고
Azure DDoS Protection은 사용자 개입 없이 DDoS 공격을 식별하여 완화합니다.

- Azure Security Center에서 관리하는 구독에 보호되는 리소스가 있는 경우 DDoS Protection 표준은 보호되는 애플리케이션에 대해 DDoS 공격이 감지되고 완화될 때마다 Security Center에 경고를 자동으로 보냅니다.
- 또는, 보호되는 공용 IP에 대한 활성 완화가 발생할 때 알림을 받으려면 DDoS 공격 진행 여부 메트릭에서 [경고를 구성](alerts.md)합니다.
- 다른 DDoS 메트릭에 대한 경고를 만들고 [공격 원격 분석을 구성](telemetry.md)하여 공격의 규모, 손실되는 트래픽, 공격 벡터, 상위 기여자 및 기타 세부 내용을 이해할 수 있습니다.

![DDoS 메트릭](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DRR(DDoS Rapid Response)
DDoS Protection 표준 고객은 이제 활성 공격 중에 [Rapid Response 팀](ddos-rapid-response.md)에 액세스할 수 있습니다. DRR은 공격 도중 공격 조사뿐 아니라 공격 후 분석에도 도움이 될 수 있습니다.

### <a name="sla-guarantee-and-cost-protection"></a>SLA 보장 및 비용 보호
DDoS Protection 표준 서비스는 99.99% SLA가 적용되며, 비용 보호는 문서화된 공격 중 스케일 아웃에 대한 리소스 크레딧을 제공합니다. 자세한 내용은 [Azure DDoS Protection용 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)를 참조하세요.

## <a name="featured-partner-scenarios"></a>주요 파트너 시나리오
다음은 Azure DDoS Protection 표준과 통합을 통해 얻을 수 있는 주요 이점입니다.

- 파트너가 제공하는 서비스(부하 분산 장치, 웹 애플리케이션 방화벽, 방화벽 등)는 백 엔드에서 Azure DDoS Protection 표준에 의해 자동으로 보호(화이트 레이블)됩니다.
- 파트너는 Azure DDoS Protection 표준 공격 분석 및 원격 분석에 액세스하고 자사 제품과 통합하여 통합된 고객 환경을 제공할 수 있습니다.  
- 파트너는 Azure Rapid Response가 없는 경우에도 DDoS 관련 문제에 대한 DDoS Rapid Response 지원에 액세스할 수 있습니다.
- 파트너의 보호되는 애플리케이션은 DDoS 공격이 발생하는 경우 DDoS SLA 보장 및 비용 보호를 통해 지원됩니다.

## <a name="technical-integration-overview"></a>기술 통합 개요
Azure DDoS Protection 표준 파트너 제휴 기회는 Azure Portal, API 및 CLI/PS를 통해 제공됩니다.

### <a name="integrate-with-ddos-protection-standard"></a>DDoS Protection 표준과 통합
파트너가 Azure DDoS Protection 표준과의 통합을 구성하려면 다음 단계를 수행해야 합니다.
1. 원하는 파트너 구독에서 DDoS 보호 계획을 만듭니다. 단계별 지침은 [DDoS 표준 보호 계획 만들기](manage-ddos-protection.md#create-a-ddos-protection-plan)를 참조하세요.
   > [!NOTE]
   > 지정된 테넌트에 대해 1개의 DDoS 보호 계획만 만들어야 합니다. 
2. 부하 분산 장치, 방화벽, 웹 애플리케이션 방화벽 등 파트너 구독에 퍼블릭 엔드포인트를 사용하여 서비스를 배포합니다. 
3. 첫 번째 단계에서 만든 DDoS 보호 계획을 사용하여 퍼블릭 엔드포인트가 있는 서비스의 가상 네트워크에서 Azure DDoS Protection 표준을 사용하도록 설정합니다. 단계별 지침은 [DDoS 표준 보호 계획 사용](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)을 참조하세요.
   > [!IMPORTANT] 
   > 가상 네트워크에서 Azure DDoS Protection 표준을 사용하도록 설정하면 해당 가상 네트워크 내의 모든 공용 IP가 자동으로 보호됩니다. 이러한 공용 IP의 원본은 Azure 클라이언트 구독 내부 또는 Azure 외부에 있을 수 있습니다. 
4. 필요에 따라 애플리케이션별 고객용 대시보드에서 Azure DDoS Protection 표준 원격 분석과 공격 분석을 통합합니다. 원격 분석 사용에 대한 자세한 내용은 [DDoS 보호 원격 분석 보기 및 구성](telemetry.md)을 참조하세요. 

### <a name="onboarding-guides-and-technical-documentation"></a>온보딩 가이드 및 기술 문서

- [Azure DDoS Protection 제품 페이지](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection 설명서](ddos-protection-overview.md)
- [Azure DDoS Protection API 참조](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 가상 네트워크 API 참조](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>도움말 보기

- Azure DDoS Protection 표준과 애플리케이션, 서비스 또는 제품 통합에 대한 질문이 있는 경우 [Azure 보안 커뮤니티](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)에 문의하세요.
- [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)의 논의를 따르세요.

### <a name="get-to-market"></a>시장 진출

- Microsoft와 파트너 제휴를 위한 기본 프로그램은 [Microsoft 파트너 네트워크](https://partner.microsoft.com/)입니다. – Microsoft Graph 보안 통합은 [MPN ISV(독립 소프트웨어 공급업체)](https://partner.microsoft.com/saas-solution-guide) 트랙에 속합니다.
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1)은 Microsoft 보안 파트너를 위한 특수 프로그램으로, 파트너의 보안 제품을 보강하고 고객이 파트너의 Microsoft 보안 제품 통합을 용이하게 검색하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계
기존 파트너 통합 보기:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Radware의 Azure Cloud WAF](https://www.radware.com/resources/microsoft-azure/)
