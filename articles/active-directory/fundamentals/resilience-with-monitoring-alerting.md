---
title: Azure AD B2C를 사용한 모니터링 및 분석을 통한 복원력 | Microsoft Docs
description: Azure AD B2C를 사용한 모니터링 및 분석을 통한 복원력
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccb3876799c0a1473f06c1838536df17eab03398
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754386"
---
# <a name="resilience-through-monitoring-and-analytics"></a>모니터링 및 분석을 통한 복원력

모니터링은 애플리케이션 및 서비스의 가용성과 성능을 극대화합니다. 모니터링은 인프라와 애플리케이션에서 원격 분석 데이터를 수집, 분석하고 작업을 수행하기 위한 포괄적인 솔루션을 제공합니다. 경고는 서비스 또는 애플리케이션에서 문제가 발견되면 사전에 알려 줍니다. 서비스의 최종 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features)는 감사 로그와 로그인 로그를 분석 및 검색하고 사용자 지정 보기를 빌드하는 데 도움이 됩니다.

Azure Monitor를 사용하여 Azure AD B2C에서 모니터링 및 보고를 설정하는 방법을 알아보려면 이 비디오를 시청하세요.

>[!Video https://www.youtube.com/embed/Mu9GQy-CbXI]

## <a name="monitor-and-get-notified-through-alerts"></a>모니터링 및 경고를 통해 알림 받기

시스템 및 인프라 모니터링은 서비스의 전반적인 상태를 확인하는 데 매우 중요합니다. 모니터링은 새 사용자 도착, 최종 사용자의 인증 비율, 전환과 같은 비즈니스 메트릭의 정의로 시작합니다. 모니터링할 이러한 지표를 구성합니다. 프로모션 또는 휴일 트래픽으로 인해 예상되는 급증에 대비한 계획을 세우는 경우 특히 이벤트의 추정치와 해당하는 비즈니스 메트릭 벤치마크를 수정합니다. 이벤트 후에는 이전 벤치마크로 대체합니다.

마찬가지로 실패 또는 성능 문제를 검색하기 위해 적절한 기준을 설정한 후 경고를 정의하는 것은 새로운 문제에 신속하게 대응하는 데 필수적인 방법입니다.

![모니터링 및 분석 구성 요소를 보여 주는 이미지](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>모니터링 및 경고를 구현하는 방법

- **모니터링**: [Azure Monitor](../../active-directory-b2c/azure-monitor.md)를 사용하여 주요 SLO(서비스 수준 목표)를 기준으로 상태를 지속적으로 모니터링하고 중요한 변경이 발생할 때마다 알림을 받습니다. 먼저 SLO를 유지하기 위해 상태를 모니터링해야 하는 비즈니스의 중요한 구성 요소로 Azure AD B2C 정책 또는 애플리케이션을 식별합니다. SLO와 일치하는 주요 지표를 식별합니다.
예를 들어 갑작스러운 하락이 비즈니스 손실로 이어지는 다음 메트릭을 추적합니다.

  - **총 요청**: Azure AD B2C 정책에 전송된 총 "n"개의 요청 수입니다.

  - **성공률(%)** : 성공한 요청/총 요청입니다.

  Azure AD B2C 정책 기반 로그, [감사 로그](../../active-directory-b2c/analytics-with-application-insights.md), 로그인 로그가 저장되는 [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md)의 [주요 지표](../../active-directory-b2c/view-audit-logs.md)에 액세스합니다.  

   - **시각화**: Log Analytics 빌드 대시보드를 사용하여 주요 지표를 시각적으로 모니터링합니다.

   - **현재 기간**: 현재 기간(예: 이번 주)의 총 요청 및 성공률(%) 변화를 보여 주는 임시 차트를 만듭니다.

   - **이전 기간**: 참조 목적으로 특정 이전 기간(예: 지난 주)의 총 요청 및 성공률(%) 변화를 보여 주는 임시 차트를 만듭니다.

- **경고**: Log Analytics를 사용하여 주요 지표가 갑자기 변화할 때 트리거되는 [경고](../../azure-monitor/alerts/alerts-log.md)를 정의합니다. 이러한 변화는 SLO에 부정적인 영향을 줄 수 있습니다. 경고는 이메일, SMS, 웹후크를 비롯한 다양한 형태의 알림 방법을 사용합니다. 먼저 경고가 트리거되는 임계값 역할을 하는 기준을 정의합니다. 예를 들면 다음과 같습니다.
  - 총 요청의 급락에 대한 경고: 총 요청 수가 급락할 때 경고를 트리거합니다. 예를 들어 이전 기간과 비교하여 총 요청 수가 25% 하락하면 경고를 발생시킵니다.  
  - 성공률(%)의 유의미한 하락에 대한 경고: 선택한 정책의 성공률이 유의미하게 하락할 때 경고를 트리거합니다.
  - 경고가 수신되면 [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md) 및 Azure AD B2C용 [VS Code 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)을 사용하여 문제를 해결합니다. 문제를 해결하고 업데이트된 애플리케이션 또는 정책을 배포한 후 정상 범위로 돌아갈 때까지 주요 지표를 계속 모니터링합니다.

- **서비스 경고**: 서비스 [Azure AD B2C 수준 경고](../../service-health/service-health-overview.md)를 사용하여 서비스 문제, 계획된 유지 관리, 상태 자문, 보안 권고에 대한 알림을 받습니다.

- **보고**: [Log Analytics를 사용](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)하여 사용자 인사이트, 기술 과제, 성장 기회를 이해하는 데 도움이 되는 보고서를 작성합니다.
  - **상태 대시보드**: [Azure 대시보드 기능을 사용하여 사용자 지정 대시보드](../../azure-monitor/app/tutorial-app-dashboards.md)를 만듭니다. 이 기능은 Log Analytics 쿼리를 사용하여 차트 추가를 지원합니다. 예를 들어 요청을 수행하는 데 사용된 디바이스에 대한 성공 및 실패한 로그인, 실패 원인, 원격 분석 패턴을 식별합니다.
  - **Azure AD B2C 사용자 경험 중단**: [통합 문서](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys)를 사용하여 사용자가 로그인 또는 가입 과정을 시작했지만 완료하지 않은 중단된 Azure AD B2C 사용자 경험 목록을 추적합니다. 이는 정책 ID에 대한 세부 정보 및 사용자가 사용자 경험을 중단하기 전에 수행한 단계의 분석 결과를 제공합니다.
  - **Azure AD B2C 모니터링 통합 문서**: Azure AD B2C 대시보드, MFA(다단계 인증) 작업, 조건부 액세스 보고서, correlationId별 검색 로그를 포함하는 [모니터링 통합 문서](https://github.com/azure-ad-b2c/siem)를 사용하여 Azure AD B2C 환경의 상태에 대한 정보를 더 잘 파악할 수 있습니다.
  
## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원력 리소스](resilience-b2c.md)
  - [복원력 있는 최종 사용자 환경](resilient-end-user-experience.md)
  - [외부 프로세스를 사용하는 복원력 있는 인터페이스](resilient-external-processes.md)
  - [개발자 모범 사례를 통한 복원력](resilience-b2c-developer-best-practices.md)
- [인증 인프라에 복원력 구축](resilience-in-infrastructure.md)
- [애플리케이션에서 인증 및 권한 부여 복원력 향상](resilience-app-development-overview.md)
