---
title: Azure Monitor 모범 사례 - 분석 및 시각화
description: Azure Monitor 표준 분석 기능 외에 시각화를 사용자 지정하기 위한 지침 및 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 3f4b671237c842c156e06281926be02ee3cdeb4a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182371"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Azure Monitor 모범 사례 - 데이터 분석 및 시각화
이 문서는 [Azure Monitor 구성을 위한 시나리오 권장 사항](best-practices.md)일부입니다. 수집된 데이터를 분석하기 위한 Azure Monitor 기본 제공 기능과 조직의 여러 사용자 요구 사항에 맞게 사용자 지정 시각화를 만드는 옵션을 설명합니다. 차트 및 그래프와 같은 시각화는 모니터링 데이터를 분석하여 문제를 드릴다운하고 패턴을 식별하는 데 도움이 될 수 있습니다.


## <a name="builtin-analysis-features"></a>기본 제공 분석 기능
다음 섹션에서는 구성 없이 수집된 데이터를 분석하는 Azure Monitor 기능에 대해 설명합니다.
### <a name="overview-page"></a>개요 페이지
대부분의 Azure 서비스에는 중요한 메트릭에 대한 최근 차트를 보여 주는 차트가 있는 **모니터** 섹션이 포함된 Azure Portal **개요** 페이지가 있습니다. 이는 개별 서비스 소유자가 리소스의 성능을 신속하게 평가하기 위한 것입니다. 이 페이지는 자동으로 수집되는 플랫폼 메트릭을 기반으로 하며 이 기능에 필요한 구성은 없습니다.

### <a name="metrics-explorer"></a>메트릭 탐색기
메트릭 탐색기를 사용하면 사용자가 메트릭 데이터를 대화형으로 작업하고 메트릭 경고를 만들 수 있습니다. 대부분의 사용자는 최소한의 학습으로 메트릭 탐색기를 사용할 수 있지만 분석하려는 메트릭을 잘 알고 있어야 합니다. 데이터 수집이 구성된 후에는 이 기능에 필요한 구성이 없습니다. Azure 리소스에 대한 플랫폼 메트릭을 자동으로 사용할 수 있습니다. 가상 머신에 대한 게스트 메트릭은 Azure Monitor 에이전트가 배포된 경우 사용할 수 있으며, 애플리케이션 Insights 구성된 경우 애플리케이션 메트릭을 사용할 수 있습니다.


### <a name="log-analytics"></a>Log Analytics
Log Analytics를 사용하면 로그 쿼리를 만들어 로그 데이터를 대화형으로 작업하고 로그 쿼리 경고를 만들 수 있습니다. 사용자가 일반적인 요구 사항에 미리 작성된 쿼리를 사용할 수 있지만 쿼리 언어에 익숙해지려면 몇 가지 학습이 필요합니다. 조직에 고유한 [쿼리가](logs/query-packs.md) 있는 쿼리 팩을 추가할 수도 있습니다. 이렇게 하면 쿼리 언어에 익숙한 사용자가 조직의 다른 사용자에 대한 쿼리를 작성할 수 있습니다.


## <a name="workbooks"></a>통합 문서
[통합 문서](./visualize/workbooks-overview.md) 는 데이터 분석 및 풍부한 시각적 보고서 생성을 위한 유연한 캔버스를 제공하는 Azure에서 선택하는 시각화 플랫폼입니다. 통합 문서를 사용하면 Azure에서 여러 데이터 원본을 탭하고 통합 대화형 환경으로 결합할 수 있습니다. 여러 Azure 리소스에서 E2E 모니터링 보기를 준비하는 데 특히 유용합니다.

Insights 미리 제공된 통합 문서를 사용하여 특정 서비스에 대한 중요한 상태 및 성능 정보를 사용자에게 제공합니다. Azure Monitor 메뉴의 **통합 문서** 탭에서 추가 통합 문서 갤러리에 액세스하고 다른 사용자의 요구 사항에 맞게 사용자 지정 통합 문서를 만들 수 있습니다.

![페이지 뷰 분석, 사용량 및 페이지에서 소요된 시간을 포함하여 통합 문서의 세 페이지 스크린샷을 보여 주는 다이어그램.](media/visualizations/workbook.png)

통합 문서에 대한 일반적인 시나리오는 다음과 같습니다.

- 테이블에서 요소를 선택하면 연결된 차트 및 시각화가 동적으로 업데이트되는 매개 변수가 있는 대화형 보고서를 만듭니다.
- 조직의 다른 사용자와 보고서를 공유합니다.
- 공용 GitHub 기반 템플릿 갤러리를 사용하여 조직의 다른 통합 문서 작성자와 협업합니다.



## <a name="azure-dashboards"></a>Azure 대시보드
[Azure 대시보드는 Azure](../azure-portal/azure-portal-dashboards.md) 인프라 및 서비스에 단일 창을 제공하는 데 유용합니다. 통합 문서에서는 다양한 기능을 제공하지만 대시보드는 Azure Monitor 데이터를 다른 Azure 서비스의 데이터와 결합할 수 있습니다.

![사용자 지정 가능한 정보가 포함된 Azure 대시보드의 예를 보여 주는 스크린샷.](media/visualizations/dashboard.png)

대시보드를 만드는 비디오 연습은 다음과 같습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

대시보드의 일반적인 시나리오는 다음과 같습니다.

- 메트릭 그래프와 로그 쿼리 결과를 관련 서비스에 대한 운영 데이터와 결합하는 대시보드를 만듭니다.
- [Azure RBAC(Azure 역할 기반 액세스 제어)와의](../role-based-access-control/overview.md)통합을 통해 서비스 소유자와 대시보드를 공유합니다.
  

Azure Monitor 로그의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Log Analytics 데이터의 대시보드 만들기 및 공유](visualize/tutorial-logs-dashboards.md)를 참조하세요. Application Insights의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드 만들기](app/tutorial-app-dashboards.md)를 참조하세요. 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 장기 KPI 추세를 분석하는 보고서와 함께 비즈니스 중심 대시보드 및 보고서를 만드는 데 유용합니다. 로그 [쿼리 결과를](visualize/powerbi.md) Power BI 데이터 세트로 가져온 다음, 다양한 원본의 데이터를 결합하고 웹 및 모바일 디바이스에서 보고서를 공유하는 등의 기능을 활용할 수 있습니다.

![I T 작업에 대한 Power B 보고서 예제를 보여 주는 스크린샷.](media/visualizations/power-bi.png)

Power BI 일반적인 시나리오는 다음과 같습니다.

- 다양하게 시각화할 수 있습니다.
- 확대 및 교차 필터링을 포함하여 광범위한 대화형입니다.
- 조직 전체에서 쉽게 공유할 수 있습니다.
- 여러 데이터 원본의 다른 데이터와 통합됩니다.
- 결과가 큐브에 캐시되므로 성능이 향상됩니다.



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/)는 뛰어난 운영 대시보드를 제공하는 개방형 플랫폼입니다. 온-프레미스, 타사 도구 및 다른 클라우드의 데이터 저장소를 포함하여 Azure 및 비 Azure 데이터 원본의 시각화를 결합하여 운영 인시던트 검색, 격리 및 심사에 유용합니다. Grafana에는 사용자가 다른 도구에서 수집한 스택의 다른 메트릭과 함께 Azure 플랫폼 데이터를 시각화할 수 있는 Dynatrace, New Relic 및 App Dynamics와 같은 APM 도구에 대한 인기 있는 플러그 인 및 대시보드 템플릿이 있습니다. 또한 단일 창에 다중 클라우드 모니터링을 위한 AWS CloudWatch 및 GCP BigQuery 플러그 인이 있습니다.




[Grafana용 Azure Monitor 데이터 원본 플러그 인을](visualize/grafana-plugin.md) Azure 구독에 추가하여 Azure 메트릭 데이터를 시각화할 수 있습니다.

![Grafana 시각화를 보여 주는 스크린샷.](media/visualizations/grafana.png)


Grafana의 일반적인 시나리오는 다음과 같습니다.

- 단일 시각화 패널에서 시간 계열 및 이벤트 데이터를 결합합니다.
- 동적 변수의 사용자 선택에 따라 동적 대시보드를 만듭니다.
- 만들고 지원하는 커뮤니티 템플릿에서 대시보드를 만듭니다.
- 모든 클라우드 공급자 또는 온-프레미스에서 실행되는 공급업체에 구애받지 않는 BCDR 시나리오를 만듭니다.

## <a name="azure-monitor-partners"></a>Azure Monitor 파트너
일부 Azure Monitor 파트너는 시각화 기능을 제공합니다. Microsoft에서 평가한 파트너 목록은 Azure Monitor [파트너 통합을 참조하세요.](./partners.md) Azure Monitor 파트너는 시간을 절약하기 위해 새로운 시각화를 제공할 수 있지만 이러한 솔루션에는 추가 비용이 발생할 수 있습니다.


## <a name="custom-application"></a>사용자 지정 애플리케이션
그런 다음 REST API 통해 액세스되는 Azure Monitor 메트릭 및 로그 데이터를 사용하여 사용자 지정 웹 사이트 및 애플리케이션을 빌드할 수 있습니다. 이를 통해 UI, 시각화, 대화형 및 기능의 완전한 유연성을 제공합니다.


## <a name="next-steps"></a>다음 단계
- Azure Monitor 데이터에서 경고 및 자동화된 작업을 정의하려면 경고 및 [자동화된](best-practices-alerts.md) 작업을 참조하세요. 