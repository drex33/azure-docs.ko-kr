---
title: Azure Monitor 데이터 플랫폼 | Microsoft Docs
description: Azure Monitor에서 수집된 데이터 모니터링은 간단한 메트릭으로 구분되며 고급 분석에 사용되는 실시간 시나리오 및 로그를 지원할 수 있습니다.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: b97f7fdfb64f80fa1e132b0bdbd1dbb6d1382de1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279199"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 데이터 플랫폼

클라우드 및 온-프레미스 서비스를 모두 사용하는 분산 애플리케이션을 실행하는 오늘날의 복잡한 컴퓨팅 환경에서 가시성을 사용하도록 설정하려면 분산 시스템의 모든 계층 및 모든 구성 요소의 작업 데이터를 수집해야 합니다. 이 데이터에 대한 심층 분석을 수행하고 조직에서 다수의 관련자를 지원하는 다양한 관점을 갖춘 단일 창으로 통합할 수 있어야 합니다.

[Azure Monitor](overview.md)는 다양한 원본의 데이터를 수집한 후 분석, 시각화 및 경고 생성에 사용할 수 있는 공통 데이터 플랫폼으로 집계합니다. 이 기능은 여러 원본의 데이터를 기준으로 일관된 환경을 제공하고, 모니터링되는 모든 리소스와 Azure Monitor에 데이터를 저장하는 다른 서비스의 데이터에에 대한 심층 인사이트를 제공합니다.


![Azure Monitor 개요](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor의 가시성 데이터
메트릭, 로그 및 분산 추적을 일반적으로 가시성의 세 핵심 요소라고 합니다. 모니터링 대상 시스템의 충분한 가시성을 제공하기 위해 모니터링 도구에서 수집하고 분석해야 하는 다양한 종류의 데이터를 나타냅니다. 가시성은 여러 핵심 요소의 데이터를 상호 연결하고 모니터링되는 전체 리소스 세트에서 데이터를 집계하여 달성할 수 있습니다. Azure Monitor는 여러 원본의 데이터를 함께 저장하므로 공통된 도구 세트를 사용하여 데이터를 상호 연결하고 분석할 수 있습니다. 또한 다른 서비스용 데이터를 호스트하는 것 외에도 여러 Azure 구독 및 테넌트 간에 데이터를 상호 연결합니다.

Azure 리소스는 상당한 양의 모니터링 데이터를 생성합니다. Azure Monitor는 이 데이터를 다른 원본의 모니터링 데이터와 함께 메트릭 또는 로그 플랫폼으로 통합합니다. 각각은 특정 모니터링 시나리오에 맞게 최적화되며, Azure Monitor의 다양한 기능을 지원합니다. 데이터 분석, 시각화, 경고 등의 기능을 위해서는 필요한 시나리오를 가장 효율적이고 비용 효율적인 방식으로 구현할 수 있도록 차이점을 이해해야 합니다. [Application Insights](app/app-insights-overview.md) 또는 [VM Insights](vm/vminsights-overview.md) 와 같은 Azure Monitor Insights에는 두 가지 유형의 데이터 간의 차이점을 이해 하지 않고도 특정 모니터링 시나리오에 집중할 수 있는 분석 도구가 있습니다. 


### <a name="metrics"></a>메트릭
[메트릭](essentials/data-platform-metrics.md)은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 이러한 항목은 일정한 간격으로 수집되며 타임스탬프, 이름, 값 및 하나 이상의 정의 레이블로 식별됩니다. 이러한 메트릭은 다른 메트릭과 비교하여 다양한 알고리즘을 사용하여 집계되고 시간에 따른 추세가 분석될 수 있습니다. 

Azure Monitor 메트릭은 타임스탬프 데이터 분석에 최적화된 시계열 데이터베이스에 저장됩니다. 따라서 메트릭은 이슈를 경고하고 빠르게 검색하는 데 특히 적합합니다. 이를 통해 시스템의 작동 성능을 알 수 있지만 일반적으로 이슈의 근본 원인을 파악하기 위해서는 로그와 결합해야 합니다.

[Azure 메트릭 탐색기](essentials/metrics-getting-started.md)를 사용하여 Azure Portal의 대화형 분석에 메트릭을 사용할 수 있습니다. 이러한 메트릭을 다른 데이터와의 시각화를 위해 [Azure 대시보드](app/tutorial-app-dashboards.md)에 추가하고 근 실시간으로 [경고](alerts/alerts-metric.md)에 사용할 수 있습니다.

[Azure Monitor의 메트릭](essentials/data-platform-metrics.md)에서 데이터 원본이 포함된 Azure Monitor 메트릭에 대해 자세히 알아보세요.

### <a name="logs"></a>로그
[로그](logs/data-platform-logs.md)는 시스템 내에서 발생한 이벤트입니다. 다른 종류의 데이터를 포함할 수 있으며 구조화되거나 타임스탬프가 있는 자유 형식 텍스트일 수 있습니다. 환경의 이벤트가 로그 항목을 생성할 때 로그가 산발적으로 만들어질 수 있으며, 부하가 높은 시스템은 일반적으로 더 많은 로그 볼륨을 생성합니다.

Azure Monitor의 로그는 강력한 분석 엔진과 [풍부한 쿼리 언어](/azure/kusto/query/)를 제공하는 [Azure 데이터 탐색기](/azure/data-explorer/)를 기준으로 하는 Log Analytics 작업 영역에 저장됩니다. 일반적으로 로그는 식별되는 이슈의 전체 컨텍스트를 제공하기 위한 충분한 정보를 제공하며 이슈의 근본 사례를 식별하는 데 중요합니다.

> [!NOTE]
> Azure에서 Azure Monitor 로그와 로그 데이터의 원본을 구분하는 것이 중요합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그](essentials/platform-logs-overview.md)에 기록됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그](essentials/platform-logs-overview.md)에 작업 정보를 기록합니다. Azure Monitor 로그는 다른 모니터링 데이터와 함께 활동 로그 및 리소스 로그를 수집하여 전체 리소스 집합에 대한 심층 분석을 제공하는 로그 데이터 플랫폼입니다.


 Azure Portal에서 [Log Analytics](logs/log-query-overview.md)와 대화형으로 [로그 쿼리](logs/log-query-overview.md)를 사용하거나 다른 데이터와의 시각화를 위해 [Azure 대시보드](app/tutorial-app-dashboards.md)에 결과를 추가할 수 있습니다. 일정 쿼리의 결과에 따라 경고를 트리거할 [로그 경고](alerts/alerts-log.md)를 만들 수도 있습니다.

[Azure Monitor의 로그](logs/data-platform-logs.md)에서 데이터 원본이 포함된 Azure Monitor 로그에 대해 자세히 알아보세요.

### <a name="distributed-traces"></a>분산 추적
추적은 사용자 요청을 따라 분산 시스템을 이동하는 일련의 관련 이벤트입니다. 애플리케이션 코드의 동작과 다른 트랜잭션의 성능을 결정하는 데 사용할 수 있습니다. 로그는 분산 시스템의 개별 구성 요소에 의해 생성되는 반면, 추적은 전체 구성 요소 세트에서 애플리케이션의 작업 및 성능을 측정합니다.

Azure Monitor의 분산 추적은 [Application Insights SDK](app/distributed-tracing.md)에서 사용할 수 있으며, 추적 데이터는 Application Insights에서 수집한 다른 애플리케이션 로그 데이터와 함께 저장됩니다. 따라서 로그 쿼리, 대시보드 및 경고를 비롯한 다른 로그 데이터와 동일한 분석 도구에서 추적 데이터를 사용할 수 있습니다.

[분산 추적이란?](app/distributed-tracing.md)에서 분산 추적에 대해 자세히 알아보세요.


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor 메트릭 및 로그 비교

다음 표에서는 Azure Monitor의 메트릭과 로그를 비교해서 설명합니다.

| attribute  | 메트릭 | 로그 |
|:---|:---|:---|
| 이점 | 간단하며 근 실시간 시나리오(예: 경고)를 지원할 수 있습니다. 이슈를 신속하게 검색하는 데 적합합니다. | 풍부한 쿼리 언어를 사용하여 분석됩니다. 심층 분석 및 근본 원인을 식별하는 데 적합합니다. |
| 데이터 | 숫자 값만 | 텍스트 또는 숫자 데이터 |
| 구조 | 샘플 시간, 모니터링되는 리소스, 숫자 값을 포함하는 표준 속성 세트입니다. 일부 메트릭에는 추가 정의를 위한 여러 차원이 포함됩니다. | 로그 유형에 따른 고유한 속성 세트입니다. |
| 컬렉션 | 정기적으로 수집됩니다. | 이벤트가 생성될 레코드를 트리거할 때 산발적으로 수집될 수 있습니다. |
| Azure Portal에서 보기 | 메트릭 탐색기 | Log Analytics |
| 데이터 원본에 포함되는 항목 | Azure 리소스에서 수집되는 플랫폼 메트릭<br>Application Insights에서 모니터링하는 애플리케이션<br>애플리케이션 또는 API에서 정의된 사용자 지정 | 애플리케이션 및 리소스 로그<br>모니터링 솔루션<br>에이전트 및 VM 확장<br>애플리케이션 요청 및 예외<br>클라우드 용 Microsoft Defender.<br>데이터 수집기 API |

## <a name="collect-monitoring-data"></a>모니터링 데이터 수집
여러 다른 [Azure Monitor 데이터 원본](agents/data-sources.md)이 Log Analytics 작업 영역(로그) 또는 Azure Monitor 메트릭 데이터베이스(메트릭) 중 하나 또는 둘 다에 기록됩니다. 일부 원본은 이러한 데이터 저장소에 직접 기록되고 다른 원본은 Azure Storage 등의 다른 위치에 기록될 수 있으며, 로그 또는 메트릭을 채우기 위한 구성이 필요할 수도 있습니다. 

각 유형을 채우는 여러 데이터 원본의 목록은 [Azure Monitor의 메트릭](essentials/data-platform-metrics.md) 및 [Azure Monitor의 로그](logs/data-platform-logs.md)를 참조하세요.


## <a name="stream-data-to-external-systems"></a>외부 시스템으로 데이터 스트리밍
Azure에서 도구를 사용하여 모니터링 데이터를 분석하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달하기 위한 요구 사항이 있을 수 있습니다. 이 전달은 일반적으로 [Azure Event Hubs](../event-hubs/index.yml)를 통해 모니터링된 리소스에서 직접 수행됩니다. 논리 앱 등의 다른 프로세스를 사용하여 필요한 데이터를 검색할 수 있는 반면, 일부 원본은 이벤트 허브로 직접 데이터를 보내도록 구성할 수 있습니다. 자세한 내용은 [이벤트 허브로 Azure 모니터링 데이터를 스트리밍하여 외부 도구에서 사용](essentials/stream-monitoring-data-event-hubs.md)을 참조하세요.



## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 메트릭](essentials/data-platform-metrics.md)에 대해 읽어보세요.
- [Azure Monitor의 로그](logs/data-platform-logs.md)에 대해 읽어보세요.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](agents/data-sources.md)에 대해 알아봅니다.
