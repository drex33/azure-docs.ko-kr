---
title: Azure Monitor 모범 사례-분석 및 시각화
description: Azure Monitor의 표준 분석 기능 외에 시각화를 사용자 지정 하기 위한 지침과 권장 사항입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: c9f46404e1984c7f9eb4e8cea231229f31ffa8ad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231669"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Azure Monitor 모범 사례-데이터 분석 및 시각화
이 문서는 [Azure Monitor를 구성](best-practices.md)하는 권장 사항 시나리오의 일부입니다. 이 예에서는 수집 된 데이터를 분석 하기 위한 Azure Monitor의 기본 기능 및 조직의 여러 사용자에 대 한 요구 사항을 충족 하는 사용자 지정 시각화를 만들기 위한 옵션에 대해 설명 합니다. 차트 및 그래프와 같은 시각화를 통해 모니터링 데이터를 분석 하 여 문제를 드릴 다운 하 고 패턴을 식별할 수 있습니다.


## <a name="builtin-analysis-features"></a>기본 제공 분석 기능
다음 섹션에서는 구성 없이 수집 된 데이터에 대 한 분석을 제공 하는 Azure Monitor 기능에 대해 설명 합니다.
### <a name="overview-page"></a>개요 페이지
대부분의 Azure 서비스에는 중요 메트릭에 대 한 최근 차트를 표시 하는 차트를 포함 하는 **모니터** 섹션을 포함 하는 Azure Portal의 **개요** 페이지가 표시 됩니다. 이는 개별 서비스의 소유자가 리소스의 성능을 신속 하 게 평가 하기 위한 것입니다. 이 페이지는 자동으로 수집 되는 플랫폼 메트릭을 기반으로 하기 때문에이 기능에 대 한 구성이 필요 하지 않습니다.

### <a name="metrics-explorer"></a>메트릭 탐색기
메트릭 탐색기를 사용 하면 사용자가 메트릭 데이터를 대화형으로 작업 하 고 메트릭 경고를 만들 수 있습니다. 대부분의 사용자는 최소한의 학습으로 메트릭 탐색기를 사용할 수 있지만 분석 하려는 메트릭에 대해 잘 알고 있어야 합니다. 데이터 수집이 구성 된 후에는이 기능에 필요한 구성이 없습니다. Azure 리소스에 대 한 플랫폼 메트릭이 자동으로 제공 됩니다. 가상 컴퓨터에 대 한 게스트 메트릭은 Azure Monitor 에이전트가 배포 될 때 사용할 수 있으며, Application Insights 구성 된 경우에는 응용 프로그램 메트릭을 사용할 수 있습니다.


### <a name="log-analytics"></a>Log Analytics
사용자는 Log Analytics를 사용 하 여 로그 데이터를 대화형으로 작업 하 고 로그 쿼리 경고를 만들 수 있는 로그 쿼리를 만들 수 있습니다. 일반적인 요구 사항에 대해 미리 작성 된 쿼리를 사용할 수 있지만 사용자가 쿼리 언어를 익히는 데 필요한 몇 가지 교육이 있습니다. 또한 조직에 고유한 쿼리를 사용 하 여 [쿼리 팩](logs/query-packs.md) 을 추가할 수 있습니다. 이렇게 하면 쿼리 언어에 익숙한 사용자가 조직의 다른 사용자에 대 한 쿼리를 작성할 수 있습니다.


## <a name="workbooks"></a>통합 문서
[통합 문서](./visualize/workbooks-overview.md) 는 데이터 분석을 위한 유연한 캔버스를 제공 하 고 풍부한 시각적 보고서를 만드는 데 사용할 수 있는 시각화 플랫폼입니다. 통합 문서를 사용 하면 Azure에서 여러 데이터 원본을 탭 하 여 통합 된 대화형 환경에 통합할 수 있습니다. 여러 Azure 리소스에서 E2E 모니터링 보기를 준비 하는 데 특히 유용 합니다.

Insights 미리 작성 된 통합 문서를 사용 하 여 특정 서비스에 대 한 중요 한 상태 및 성능 정보를 사용자에 게 제공 합니다. Azure Monitor 메뉴의 **통합** 문서 탭에서 추가 통합 문서 갤러리에 액세스 하 고 다른 사용자의 요구 사항을 충족 하는 사용자 지정 통합 문서를 만들 수 있습니다.

![페이지 보기, 사용 및 페이지에 소요 된 시간에 대 한 분석을 비롯 하 여 통합 문서에서 세 페이지의 스크린샷을 보여 주는 다이어그램입니다.](media/visualizations/workbook.png)

통합 문서에 대 한 일반적인 시나리오는 다음과 같습니다.

- 테이블의 요소를 선택할 때 연결 된 차트 및 시각화를 동적으로 업데이트 하는 매개 변수를 사용 하 여 대화형 보고서를 만듭니다.
- 조직의 다른 사용자와 보고서를 공유 합니다.
- 공용 GitHub 기반 템플릿 갤러리를 사용 하 여 조직에서 다른 통합 문서 작성자와 공동 작업 합니다.



## <a name="azure-dashboards"></a>Azure 대시보드
Azure [대시보드](../azure-portal/azure-portal-dashboards.md) 는 azure 인프라 및 서비스에 대 한 단일 창을 제공 하는 데 유용 합니다. 통합 문서가 더 풍부한 기능을 제공 하는 반면, 대시보드는 Azure Monitor 데이터를 다른 Azure 서비스의 데이터와 결합할 수 있습니다.

![사용자 지정 가능한 정보를 포함 하는 Azure 대시보드의 예를 보여 주는 스크린샷](media/visualizations/dashboard.png)

대시보드를 만드는 비디오 연습은 다음과 같습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

대시보드의 일반적인 시나리오는 다음과 같습니다.

- 관련 서비스에 대 한 작업 데이터를 사용 하 여 메트릭 그래프와 로그 쿼리 결과를 결합 하는 대시보드를 만듭니다.
- Azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md)와 통합 하 여 서비스 소유자와 대시보드를 공유 합니다.
  

Azure Monitor 로그의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Log Analytics 데이터의 대시보드 만들기 및 공유](visualize/tutorial-logs-dashboards.md)를 참조하세요. Application Insights의 데이터를 포함하는 대시보드를 만드는 방법에 대한 자세한 내용은 [Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드 만들기](app/tutorial-app-dashboards.md)를 참조하세요. 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 은 장기적인 KPI 추세를 분석 하는 보고서와 함께 비즈니스 중심 대시보드 및 보고서를 만드는 데 유용 합니다. [로그 쿼리의 결과](./logs/log-powerbi.md) 를 Power BI 데이터 집합으로 가져온 다음 다른 원본의 데이터를 결합 하 고 웹 및 모바일 장치에서 보고서를 공유 하는 등의 기능을 활용할 수 있습니다.

![I T 연산을 위한 Power B I 보고서 예제를 보여 주는 스크린샷](media/visualizations/power-bi.png)

Power BI에 대 한 일반적인 시나리오는 다음과 같습니다.

- 다양하게 시각화할 수 있습니다.
- 확대/축소 및 교차 필터링을 비롯 한 광범위 한 상호 작용.
- 조직 전체의 공유 용이성
- 여러 데이터 원본의 다른 데이터와 통합됩니다.
- 결과가 큐브에 캐시되므로 성능이 향상됩니다.



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/)는 뛰어난 운영 대시보드를 제공하는 개방형 플랫폼입니다. Azure 및 비 Azure 데이터 원본 (온-프레미스, 타사 도구 및 기타 클라우드의 데이터 저장소 포함)의 시각화를 결합 하 여 운영 인시던트를 검색, 격리 및 심사 하는 데 유용 합니다. Grafana에는 사용자가 다른 도구에서 수집 된 스택의 상위에서 다른 메트릭과 함께 Azure 플랫폼 데이터를 시각화할 수 있게 해 주는 Dynatrace, 새 유물 및 앱 Dynamics와 같은 APM 도구를 위한 인기 있는 플러그 인 및 대시보드 템플릿이 있습니다. 또한 단일 창에서 다중 클라우드 모니터링에 대 한 AWS CloudWatch 및 GCP를 위한 쿼리 플러그 인이 있습니다.




[Grafana에 대 한 데이터 원본 플러그](visualize/grafana-plugin.md) 인을 azure 구독에 Azure Monitor 추가 하 여 azure 메트릭 데이터를 시각화할 수 있습니다.

![Grafana 시각화를 보여 주는 스크린샷](media/visualizations/grafana.png)


Grafana에 대 한 일반적인 시나리오는 다음과 같습니다.

- 단일 시각화 패널에서 시계열 및 이벤트 데이터를 결합 합니다.
- 사용자가 선택한 동적 변수를 기반으로 동적 대시보드를 만듭니다.
- 커뮤니티에서 만들고 지원 되는 템플릿에서 대시보드를 만듭니다.
- 모든 클라우드 공급자 또는 온-프레미스에서 실행 되는 공급 업체에 독립적인 BCDR 시나리오를 만듭니다.

## <a name="azure-monitor-partners"></a>Azure Monitor 파트너
일부 Azure Monitor 파트너는 시각화 기능을 제공 합니다. Microsoft에서 평가한 파트너 목록은 [Azure Monitor 파트너 통합](./partners.md)을 참조 하세요. Azure Monitor 파트너는 시간을 절약 하는 기본 시각화를 제공할 수 있습니다. 하지만 이러한 솔루션에는 추가 비용이 발생할 수 있습니다.


## <a name="custom-application"></a>사용자 지정 애플리케이션
그런 다음 REST API를 통해 액세스 Azure Monitor에서 메트릭 및 로그 데이터를 사용 하 여 사용자 지정 웹 사이트 및 응용 프로그램을 빌드할 수 있습니다. 이를 통해 UI, 시각화, 대화형 작업 및 기능을 완벽 하 게 수행할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- Azure Monitor 데이터에서 경고 및 자동화 된 작업을 정의 하려면 [경고 및 자동화 된 작업](best-practices-alerts.md) 을 참조 하세요.