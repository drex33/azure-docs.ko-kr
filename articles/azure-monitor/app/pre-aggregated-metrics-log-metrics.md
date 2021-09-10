---
title: Azure Application Insights의 로그 기반 및 사전 집계 메트릭 | Microsoft Docs
description: Azure Application Insights에서 로그 기반과 사전 집계 메트릭을 사용하는 이유 비교
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3b26cd01c125b18727ec3176b6c1ea932083be86
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318544"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Azure Application Insights의 로그 기반 및 사전 집계 메트릭

이 문서에서는 로그를 기반으로 하는 “전통적” Application Insights 메트릭과 사전 집계 메트릭 사이의 차이점을 설명합니다. 두 메트릭 모두 Application Insights 사용자에게 제공되며 애플리케이션 상태 모니터링, 진단 및 분석에서 각기 고유의 장점이 있습니다. 애플리케이션을 계측하는 개발자는 애플리케이션 크기, 예상되는 원격 분석 데이터 크기, 메트릭 정확도 및 경고에 대한 비즈니스 요구 사항에 따라 특정 시나리오에 가장 적합한 메트릭 유형을 선택할 수 있습니다.

## <a name="log-based-metrics"></a>로그 기반 메트릭

과거에 Application Insights의 애플리케이션 모니터링 원격 분석 데이터 모델은 요청, 예외, 종속성 호출, 페이지 보기 등의 미리 정의된 소수의 이벤트 유형에만 기반했습니다. 개발자는 SDK를 사용하여 SDK를 명시적으로 호출하는 코드를 작성함으로써 관련 이벤트를 수동으로 내보내거나 자동 계측을 통해 이벤트를 자동 수집할 수 있습니다. 두 경우 모두 Application Insights 백 엔드가 모든 수집된 이벤트를 로그로 저장하고, 로그의 이벤트 기반 데이터를 시각화하기 위해 Azure Portal의 Application Insights 블레이드가 분석 및 진단 도구 역할을 합니다.

일련의 이벤트 전체를 유지하기 위해 로그를 사용하면 뛰어난 분석 및 진단 값을 제공할 수 있습니다. 예를 들어, 특정 URL에 대한 정확한 요청 수와, 이러한 호출을 수행한 고유 사용자 수를 파악할 수 있습니다. 또는 사용자 세션에 대한 예외와 종속성 호출을 포함하여 상세 진단 추적을 얻을 수 있습니다. 이러한 정보 유형이 있으면 애플리케이션 상태 및 사용에 대한 가시성이 크게 향상되어 앱 문제 진단에 필요한 시간을 줄일 수 있습니다.

동시에, 많은 원격 분석 데이터를 생성하는 애플리케이션의 경우 전체 이벤트 집합을 수집하는 것이 실용적이지 못하거나 심지어 불가능할 수 있습니다. 이벤트 규모가 너무 큰 상황에서는 Application Insights가 [샘플링](./sampling.md), [필터링](./api-filtering-sampling.md)처럼 수집 및 저장되는 이벤트 수를 줄이는 여러 원격 분석 데이터 규모 절감 기법을 구현합니다. 그렇지만 저장된 이벤트 수가 줄면 그 뒤의 메트릭 정확도도 낮아지기 때문에 로그에 저장된 이벤트의 쿼리 시간 집계를 수행해야 합니다.

> [!NOTE]
> Application Insights에서 로그에 저장된 이벤트 및 측정값의 쿼리 시간 집계를 기준으로 하는 메트릭을 로그 기반 메트릭이라 합니다. 일반적으로 이러한 메트릭은 이벤트 속성으로부터 여러 차원을 갖기 때문에 분석에 아주 적합하지만 샘플링과 필터링이 이 메트릭의 정확도에 부정적인 영향을 미칩니다.

## <a name="pre-aggregated-metrics"></a>사전 집계 메트릭

로그 기반 메트릭 외에도 2018년 후반에 Application Insights 팀은 시계열에 최적화된 특수 리포지토리에 저장되는 메트릭의 퍼블릭 미리 보기를 발표했습니다. 이 새 메트릭은 이제 속성이 많은 개별 이벤트로 유지되지 않습니다. 그 대신 사전 집계 시계열로 저장되며 키 차원만 있습니다. 이 때문에 쿼리 시간에서는 새 메트릭이 우수합니다. 데이터 검색이 훨씬 빨라지고 컴퓨팅 능력이 적게 필요하기 때문입니다. 결과적으로 [메트릭 차원에 대한 거의 실시간 경고, ](../alerts/alerts-metric-near-real-time.md)응답성이 더 우수한 [대시보드](./overview-dashboard.md) 등, 새로운 시나리오가 가능해집니다.

> [!IMPORTANT]
> Application Insights에는 로그 기반과 사전 집계 메트릭이 함께 있습니다. 이 둘을 구분하기 위해 Application Insights UX에서는 이제 사전 집계 메트릭을 “표준 메트릭(미리 보기)”라 하고 전통적 이벤트 메트릭의 이름을 “로그 기반 메트릭”으로 변경했습니다.

최신 SDK(.NET용 [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK 이상)에서는 수집 중에 메트릭을 사전 집계합니다. 이는 [기본적으로 전송되는 표준 메트릭](../essentials/metrics-supported.md#microsoftinsightscomponents)에 적용되므로 정확성이 샘플링 또는 필터링의 영향을 받지 않습니다. 또한 [GetMetric](./api-custom-events-metrics.md#getmetric)을 사용하여 전송되는 사용자 지정 메트릭에도 적용되므로 데이터 수집량이 줄고 비용이 절감됩니다.

사전 집계를 구현하지 않는 SDK의 경우(즉 Application Insights SDK 구 버전 또는 브라우저 계측) 지속적으로 Application Insights 백 엔드가 Application Insights 이벤트 수집 엔드포인트에서 수신한 이벤트를 집계하여 새 메트릭을 입력합니다. 즉 실제 전송되는 데이터 규모는 줄지 않지만 사전 집계 메트릭을 사용할 수 있고, 수집 중에 메트릭을 사전 집계하지 않는 SDK에서 거의 실시간에 근접한 차원 경고 지원과 성능 향상을 경험할 수 있습니다.

컬렉션 엔드포인트는 수집 샘플링 이전에 사전 집계를 수행하므로, 애플리케이션에 사용 중인 SDK 버전에 관계없이 [수집 샘플링](./sampling.md)이 사전 집계 메트릭의 정확도에 영향을 미치지 않습니다.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>SDK에서 지원하는 사전 집계 메트릭 테이블

| 현재 프로덕션 SDK | 표준 메트릭(SDK 사전 집계) | 사용자 지정 메트릭(SDK 사전 집계 미사용) | 사용자 지정 메트릭(SDK 사전 집계 사용)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core 및 .NET Framework | 지원됨(V2.13.1 이상)| [TrackMetric](api-custom-events-metrics.md#trackmetric)을 통해 지원됨| [GetMetric](get-metric.md)을 통해 지원됨(V2.7.2 이상) |
| Java                         | 지원되지 않음       | [TrackMetric](api-custom-events-metrics.md#trackmetric)을 통해 지원됨| 지원되지 않음                           |
| Node.js                      | 지원됨(V2.0.0 이상) | [TrackMetric](api-custom-events-metrics.md#trackmetric)을 통해 지원됨| 지원되지 않음                           |
| Python                       | 지원되지 않음       | 지원됨                                 | [OpenCensus.stats](opencensus-python.md#metrics)를 통해 부분적으로 지원됨 |  

> [!NOTE]
>  OpenCensus.stats를 사용하는 Python용 메트릭 구현은 GetMetric과 다릅니다. 자세한 내용은 [메트릭에 대한 Python 설명서](./opencensus-python.md#metrics)를 참조하세요.

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>코드리스가 지원되는 사전 집계 메트릭 테이블

| 현재 프로덕션 SDK | 표준 메트릭(SDK 사전 집계) | 사용자 지정 메트릭(SDK 사전 집계 미사용) | 사용자 지정 메트릭(SDK 사전 집계 사용)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | 지원됨 <sup>1<sup>    | 지원되지 않음                             | 지원되지 않음                           |
| ASP.NET Core            | 지원됨 <sup>2<sup>    | 지원되지 않음                             | 지원되지 않음                           |
| Java                    | 지원되지 않음            | 지원되지 않음                             | [지원됨](java-in-process-agent.md#metrics) |
| Node.js                 | 지원되지 않음            | 지원되지 않음                             | 지원되지 않음                           |

1. App Service에 대한 ASP.NET의 코드리스 연결은 “전체” 모니터링 모드의 메트릭만 내보냅니다. App Service, VM/VMSS, 온-프레미스에 대한 ASP.NET의 코드리스 연결은 차원 없는 표준 메트릭을 내보냅니다. 모든 차원에는 SDK가 필요합니다.
2. App Service에 대한 ASP.NET Core의 코드리스 연결은 차원 없는 표준 메트릭을 내보냅니다. 모든 차원에는 SDK가 필요합니다.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Application Insights 사용자 지정 메트릭에 사전 집계 사용

사용자 지정 메트릭에 사전 집계를 사용할 수 있습니다. 두 가지 주요 이점은 사용자 지정 메트릭의 차원을 구성 및 경고할 수 있다는 것과, SDK가 Application Insights 수집 엔드포인트로 보내는 데이터의 크기가 줄어든다는 점입니다.

[Application Insights SDK에서 사용자 지정 메트릭을 보내는 방법](./api-custom-events-metrics.md)은 몇 가지가 있습니다. SDK 버전에서 [GetMetric 및 TrackValue](./api-custom-events-metrics.md#getmetric) 메서드를 제공한다면 사용자 지정 메트릭 보내기에 이 방법을 사용하는 것이 좋습니다. 이 경우에는 사전 집계가 SDK 내부에서 발생하기 때문에 Azure에 저장되는 데이터 크기뿐 아니라 SDK에서 Application Insights에 전송하는 데이터 규모도 줄어듭니다. 그렇지 않은 경우 데이터 수집 중 메트릭 이벤트를 사전 집계하는 [trackMetric](./api-custom-events-metrics.md#trackmetric) 메서드를 사용합니다.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>사용자 지정 메트릭 차원과 사전 집계

[trackMetric](./api-custom-events-metrics.md#trackmetric) 또는 [GetMetric 및 TrackValue](./api-custom-events-metrics.md#getmetric) API 호출을 사용하여 보내는 모든 메트릭은 자동으로 로그와 메트릭 저장소에 모두 저장됩니다. 그러나 사용자 지정 메트릭의 로그 기반 버전은 모든 차원을 유지하는 반면 사전 집계 메트릭 버전은 기본적으로 차원을 저장하지 않습니다. [사용량 및 예상 비용](./pricing.md) 탭에서 “사용자 지정 메트릭 차원에 경고 사용”을 선택하여 사용자 지정 메트릭의 차원 수집을 실행할 수 있습니다. 

![사용량 및 예상 비용](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="quotas"></a>할당량

사전 집계된 메트릭은 Azure Monitor 시계열로 저장되며 [사용자 지정 메트릭에 대한 Azure Monitor 할당량](../essentials/metrics-custom-overview.md#quotas-and-limits)이 적용됩니다.

> [!NOTE]
> 할당량을 초과하면 의도하지 않은 결과가 발생할 수 있습니다. Azure Monitor는 사용자의 구독 또는 지역에서 불안정해질 수 있습니다. 할당량 초과를 방지하는 방법을 알아보려면 [디자인 제한 사항 및 고려 사항](../essentials/metrics-custom-overview.md#design-limitations-and-considerations)을 참조하세요.
  
## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>사용자 지정 메트릭 차원의 수집이 기본적으로 꺼진 이유는 무엇인가요?

앞으로 차원이 있는 사용자 지정 메트릭을 저장하면 Application Insights와 별도로 요금이 청구될 것이지만 차원이 없는 사용자 지정 메트릭은 무료(할당량까지)이기 때문에 사용자 지정 메트릭 차원 수집이 기본적으로 꺼져 있습니다. 공식 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에서 앞으로의 가격 책정 모델 변경에 대해 확인할 수 있습니다.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>차트 만들기 및 로그 기반 및 표준 사전 집계 메트릭 탐색

[Azure Monitor 메트릭 탐색기](../essentials/metrics-getting-started.md)를 사용하여 사전 집계 및 로그 기반 메트릭으로부터 차트를 만들고 차트로 대시보드를 작성합니다. 원하는 Application Insights 리소스를 선택한 다음, 네임스페이스 선택기를 사용하여 표준(미리 보기)과 로그 기반 메트릭 사이를 전환하거나 사용자 지정 메트릭 네임스페이스를 선택합니다.

![메트릭 네임스페이스](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Application Insights 메트릭에 대한 가격 책정 모델

Application Insights에 대한 로그 기반 또는 사전 집계 메트릭은 [여기](./pricing.md#pricing-model)에 설명된 대로 수집된 데이터의 크기에 따라 비용을 발생시킵니다. 모든 차원을 포함하여 사용자 지정 메트릭은 항상 Application Insights 로그 저장소에 저장됩니다. 또한 차원이 없는 사용자 지정 메트릭의 사전 집계 버전이 기본적으로 메트릭 저장소로 전달됩니다.

[사용자 지정 메트릭 차원에서 경고 사용](#custom-metrics-dimensions-and-pre-aggregation) 옵션을 선택하여 메트릭 저장소에 사전 집계 메트릭의 모든 차원을 저장하는 경우 [사용자 지정 메트릭 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 따라 **추가** 비용이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [거의 실시간 메트릭](../alerts/alerts-metric-near-real-time.md)
* [GetMetric 및 TrackValue](./api-custom-events-metrics.md#getmetric)
