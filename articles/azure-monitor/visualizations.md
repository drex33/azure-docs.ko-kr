---
title: Azure Monitor | 데이터 시각화 Microsoft Docs
description: Azure Monitor 저장된 메트릭 및 로그 데이터를 시각화하는 데 사용할 수 있는 메서드의 요약을 얻습니다.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612354"
---
# <a name="visualize-data-from-azure-monitor"></a>Azure Monitor 데이터 시각화
이 문서에서는 Azure Monitor 저장된 메트릭 및 로그 데이터를 시각화하는 데 사용할 수 있는 방법을 요약합니다.

차트 및 그래프와 같은 시각화를 사용하면 모니터링 데이터를 분석하여 문제를 드릴다운하고 패턴을 식별할 수 있습니다. 사용하는 도구에 따라 시각화를 조직 내부 및 외부의 다른 사용자와 공유할 수도 있습니다.

## <a name="workbooks"></a>통합 문서
[통합 문서](./visualize/workbooks-overview.md)는 팀 내 데이터, 조사, 협업에 대한 깊이 있는 인사이트를 제공하는 대화형 문서입니다. 통합 문서가 문제 해결 가이드 및 인시던트 사후 분석에서 특히 유용합니다.

![페이지 뷰 분석, 사용량 및 페이지에서 소요된 시간을 포함하여 통합 문서의 세 페이지 스크린샷을 보여 주는 다이어그램.](media/visualizations/workbook.png)

통합 문서의 장점은 다음과 같습니다.

- 메트릭 및 로그에 대한 지원.
- 대화형 보고서를 사용하도록 설정하는 매개 변수를 지원합니다. 여기서 테이블에서 요소를 선택하면 연결된 차트 및 시각화가 동적으로 업데이트됩니다.
- 문서와 유사한 흐름입니다.
- 개인 또는 공유 통합 문서에 대한 옵션입니다.
- 쉽고 공동 작업적인 제작 환경.
- 공용 GitHub 기반 템플릿 갤러리를 지원하는 템플릿입니다.


## <a name="azure-dashboards"></a>Azure 대시보드
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)는 Azure의 주요 대시보드 기술입니다. Azure 인프라 및 서비스에 대한 단일 창을 제공하는 데 유용하므로 중요한 문제를 신속하게 식별할 수 있습니다.

![사용자 지정 가능한 정보가 포함된 Azure 대시보드의 예를 보여 주는 스크린샷.](media/visualizations/dashboard.png)

대시보드를 만드는 비디오 연습은 다음과 같습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

대시보드의 장점은 다음과 같습니다.

- Azure에 긴밀하게 통합됩니다. 시각화는 [메트릭 탐색기,](essentials/metrics-charts.md) [Log Analytics](logs/log-analytics-overview.md)및 [애플리케이션](app/app-insights-overview.md)Insights 을 비롯한 여러 Azure 페이지의 대시보드에 고정할 수 있습니다.
- 메트릭 및 로그에 대한 지원.
- 메트릭 탐색기, [로그 쿼리](logs/log-query-overview.md)및 [맵의](app/app-map.md)출력을 포함하여 여러 [원본의](essentials/metrics-charts.md)데이터를 결합하는 기능과 [Application Insights](app/app-insights-overview.md)의 가용성입니다.
- [Azure RBAC(Azure 역할 기반 액세스 제어)와](../role-based-access-control/overview.md)통합을 통해 개인 또는 공유 대시보드에 대한 옵션입니다.
- 자동으로 새로 고침됩니다. 메트릭 새로 고침은 시간 범위(최소 5분)에 따라 달라집니다. 로그는 1시간마다 새로 고쳐집니다. 시각화에서 새로 고침 아이콘을 선택하거나 전체 대시보드를 새로 고쳐 필요에 따라 수동으로 **새로 고칠** 수 있습니다.
- 타임스탬프를 사용하여 매개 변수가 있는 메트릭 대시보드 및 사용자 지정 매개 변수.
- 유연한 레이아웃 옵션을 제공합니다.
- 전체 화면 모드입니다.


제한은 다음과 같습니다.

- 데이터 테이블을 지원하지 않고 로그 시각화를 제한합니다. 데이터 계열의 총 수는 50개로 제한되며 추가 데이터 계열은 _다른_ 버킷 아래에 그룹화됩니다.
- 로그 차트에 대한 사용자 지정 매개 변수가 지원되지 않습니다.
- 로그 차트의 제한된 기간(지난 30일)입니다.
- 로그 차트를 공유 대시보드에 고정해야 한다는 요구 사항입니다.
- 대시보드 데이터의 대화형 작업은 지원되지 않습니다.
- 상황별 드릴다운이 제한됩니다.

## <a name="grafana"></a>Grafana
[Grafana는](https://grafana.com/) 운영 대시보드에서 뛰어난 성능을 발휘하고 성능 및 가용성 데이터를 시각화하는 개방형 플랫폼입니다. 운영 인시던트 검색, 격리 및 심사에 유용합니다. Grafana는 Azure Monitor 대한 기본 제공 지원을 포함하며 [Azure Monitor 플러그 인을](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) 기본으로 포함합니다. 다음 세 가지 Azure 서비스의 데이터 시각화를 지원합니다.
- Azure 리소스의 데이터에서 숫자 계열 데이터에 대한 메트릭을 Azure Monitor. 
- 강력한 KQL(Kusto 쿼리 언어)을 사용하여 쿼리할 수 있는 Azure 리소스의 로그 및 성능 데이터에 대한 로그를 Azure Monitor.
- Azure Resource Graph 구독에서 Azure 리소스를 신속하게 쿼리하고 식별할 수 있습니다.

Grafana는 광범위한 데이터 원본과 함께 작동하며 Azure, 온-프레미스 또는 기타 클라우드의 리소스에서 모니터링 데이터를 결합하는 대시보드 및 패널을 만들 수 있습니다. 

![Grafana 시각화를 보여 주는 스크린샷](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 11은 Grafana 버전 이전 v6.0에서만 완전히 [지원됩니다. Grafana에 지원되는 브라우저를](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)참조하세요.

Grafana의 장점은 다음과 같습니다.

- 다양하게 시각화할 수 있습니다.
- 비 Azure 데이터 원본에 대한 지원을 포함하여 풍부한 데이터 원본 에코시스템.
- 확대를 포함한 데이터 대화형입니다.
- 이벤트 데이터를 그래프에 통합할 수 있도록 하는 주석입니다.
- [Azure Monitor 변수를](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/)비롯한 매개 변수 지원

제한은 다음과 같습니다.

- Azure Resource Manager 통해 대시보드 및 모델을 관리할 수 없습니다.
- 추가 Grafana 인프라를 지원하는 비용 또는 Grafana Cloud에 대한 추가 비용입니다.
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 장기 KPI 추세를 분석하는 보고서와 함께 비즈니스 중심 대시보드 및 보고서를 만드는 데 유용합니다. 로그 [쿼리 결과를](visualize/powerbi.md) Power BI 데이터 세트로 가져온 다음, 다양한 원본의 데이터를 결합하고 웹 및 모바일 디바이스에서 보고서를 공유하는 등의 기능을 활용할 수 있습니다.

![I T 작업에 대한 Power B 보고서 예제를 보여 주는 스크린샷.](media/visualizations/power-bi.png)

Power BI 이점은 다음과 같습니다.

- 다양하게 시각화할 수 있습니다.
- 확대 및 교차 필터링을 포함하여 광범위한 대화형입니다.
- 조직 전체에서 쉽게 공유할 수 있습니다.
- 여러 데이터 원본의 다른 데이터와 통합됩니다.
- 결과가 큐브에 캐시되므로 성능이 향상됩니다.

제한은 다음과 같습니다.

- 로그를 지원하지만 메트릭은 지원하지 않습니다.
- Azure에 통합되지 않습니다. Azure Resource Manager 통해 대시보드 및 모델을 관리할 수 없습니다.
- 구성을 위해 쿼리 결과를 Power BI 모델로 가져와야 합니다. 
- 제한된 결과 크기 및 새로 고침.
- 제한된 데이터 새로 고침(하루에 8회)


## <a name="azure-monitor-partners"></a>Azure Monitor 파트너
일부 Azure Monitor 파트너는 시각화 기능을 제공합니다. Microsoft에서 평가한 파트너 목록은 Azure Monitor [파트너 통합을 참조하세요.](./partners.md) 

Azure Monitor 파트너는 시간을 절약하기 위해 첫 번째 시각화를 제공할 수 있습니다. 

제한은 다음과 같습니다.

- 추가 비용이 발생할 수 있습니다.
- 파트너 제품을 연구하고 평가하는 데 시간이 필요합니다.

## <a name="your-own-custom-application"></a>사용자 고유의 사용자 지정 애플리케이션
REST 클라이언트를 사용하여 API를 통해 Azure Monitor 메트릭 및 로그 데이터에 액세스할 수 있습니다. 그런 다음 사용자 고유의 사용자 지정 웹 사이트 및 애플리케이션을 빌드할 수 있습니다.

사용자 지정 애플리케이션을 빌드할 때의 이점은 다음과 같습니다.

- UI, 시각화, 대화형 작업 및 기능에서 완벽한 유연성이 확보됩니다.
- 메트릭 및 로그 데이터를 다른 데이터 원본과 결합하는 기능입니다.

한 가지 중요한 단점은 엔지니어링 작업이 필요하다는 것입니다.

## <a name="azure-monitor-views"></a>Azure Monitor 보기

> [!IMPORTANT]
> 뷰는 점점 사용하지 않고 있습니다. 보기를 통합 문서로 변환하는 지침은 [전환 가이드](visualize/view-designer-conversion-overview.md)를 참조하세요.

[Azure Monitor 뷰를](visualize/view-designer.md) 사용하면 로그 데이터를 사용하여 사용자 지정 시각화를 만들 수 있습니다. [모니터링 솔루션은](insights/solutions.md) 보기를 사용하여 수집하는 데이터를 표시합니다.


![컨테이너 모니터링 솔루션 타일 및 선택할 때 열리는 자세한 Azure Monitor 보기를 보여 주는 스크린샷.](media/visualizations/view.png)

보기의 장점은 다음과 같습니다.

- 로그 데이터를 다양하게 시각화할 수 있습니다.
- 다른 리소스 그룹 및 구독으로 뷰를 전송 하기 위해 뷰를 내보내고 가져올 수 있습니다.
- 작업 영역 및 모니터링 솔루션을 사용 하 여 Azure Monitor 관리 모델로 통합 합니다.
- 사용자 지정 매개 변수에 대해 [필터링](visualize/view-designer-filters.md)할 수 있습니다.
- 상호 작용-다단계 드릴스루 (다른 뷰로 드릴 하는 보기)를 지원 합니다.

제한 사항은 다음과 같습니다.

- 로그는 지원 하지만 메트릭은 지원 하지 않습니다.
- 개인 뷰가 없습니다. 작업 영역에 액세스할 수 있는 모든 사용자가 볼 수 있습니다.
- 자동으로 새로 고침되지 않습니다.
- 제한된 레이아웃 옵션을 제공합니다.
- 여러 작업 영역 또는 Application Insights 애플리케이션에서 쿼리가 지원되지 않습니다.
- 쿼리 응답 크기 (8mb)와 쿼리 실행 시간 (110 초)이 제한 됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Monitor에서 수집 하는 데이터](data-platform.md)에 대해 알아봅니다.
- [Azure 대시보드](../azure-portal/azure-portal-dashboards.md)에 대해 알아봅니다.
- [메트릭 탐색기](essentials/metrics-getting-started.md)에 대해 알아봅니다.
- [통합 문서](./visualize/workbooks-overview.md)에 대해 알아봅니다.
- [Power BI로 로그 데이터를 가져오는](./visualize/powerbi.md)방법에 대해 알아봅니다.
- [Grafana에 대 한 Azure Monitor 데이터 원본 플러그](./visualize/grafana-plugin.md)인에 대해 알아봅니다.
- [Azure Monitor의 보기](visualize/view-designer.md)에 대해 알아봅니다.
