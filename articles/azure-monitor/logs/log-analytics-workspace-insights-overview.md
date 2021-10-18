---
title: Log Analytics 작업 영역 인사이트
description: Log Analytics 작업 영역 인사이트 개요 - 데이터 수집, 사용량, 상태, 에이전트 등
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: c56a90ab18467e67360653c83d32c7cfc6f84078
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133256"
---
# <a name="log-analytics-workspace-insights"></a>Log Analytics 작업 영역 인사이트

Log Analytics 작업 영역 인사이트는 작업 영역 사용량, 성능, 상태, 에이전트, 쿼리, 변경 로그의 통합 보기를 통해 작업 영역에 대한 포괄적인 모니터링을 제공합니다. 이 문서는 Log Analytics 작업 영역 Insights 온보딩하고 사용하는 방법을 이해하는 데 도움이 됩니다.

## <a name="overview-your-log-analytics-workspaces"></a>Log Analytics 작업 영역 개요

Azure Monitor 인사이트를 통해 Log Analytics 작업 영역 인사이트에 액세스하는 경우 ‘대규모’ 큐브 뷰가 표시됩니다. 여기에서 전 세계에 작업 영역을 분산하는 방법을 확인하고, 이들의 보존, 규제, 라이선스 세부 정보를 검토하고, 작업 영역을 선택하여 인사이트를 확인할 수 있습니다.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="작업 영역의 Log Analytics 작업 영역 인사이트 목록을 보여 주는 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


대규모로 Log Analytics 작업 영역 인사이트를 시작하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal 왼쪽 창에서 **모니터를** 선택하고 Insights Hub 섹션에서 **Log Analytics 작업 영역 Insights** 선택합니다.

## <a name="view-insights-for-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 대한 인사이트 보기

특정 작업 영역의 컨텍스트에서 인사이트를 시작하면 작업 영역 성능, 사용량, 상태, 에이전트, 쿼리, 변경 로그에 대한 다양한 데이터와 분석이 표시됩니다.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="Log Analytics 작업 영역 인사이트 개요를 보여 주는 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

Log Analytics 작업 영역 인사이트에 액세스하려면 다음을 수행합니다.

1. Azure Monitor에서 Log Analytics 작업 영역 인사이트 열기(위에 설명되어 있음)

2. 들어갈 작업 영역 선택

또는 

1. Azure Portal에서 **Log Analytics 작업 영역** 을 선택합니다

2. Log Analytics 작업 영역을 선택합니다

3. 작업 영역 메뉴(모니터링 아래)에서 **인사이트** 를 선택합니다

데이터는 탭으로 구성되고 시간 범위(기본값은 24시간)는 모든 탭에 적용됩니다. 일부 차트 및 테이블은 제목에 표시된 것처럼 다른 시간 범위를 사용합니다.


### <a name="overview-tab"></a>개요 탭

**개요** 탭에서 다음을 확인할 수 있습니다.

* 주 통계 및 설정
    - 작업 영역의 월별 수집량
    - 하트비트를 보낸 컴퓨터 수(즉, 선택한 시간 범위 내에서 이 작업 영역에 연결된 컴퓨터)
    - 지난 1시간 동안 하트비트를 보내지 않은 컴퓨터(선택한 시간 범위 내)
    - 데이터 보존 기간 집합
    - 일일 상한 집합 및 최근 일자에 이미 수집된 데이터 양

* 상위 5개 테이블 – 지난 달 동안 가장 많이 수집한 5개 테이블을 분석한 차트입니다.
    - 각 테이블에 수집되는 데이터 양
    - 각 테이블의 일일 수집량 - 급증 또는 하락을 시각적으로 표시
    - 변칙 수집 - 해당 테이블에 대한 수집에서 식별된 급증 및 급감 목록


### <a name="usage-tab"></a>사용량 탭

#### <a name="usage-dashboard"></a>사용량 대시보드

이 탭에서는 작업 영역의 사용량에 대한 정보를 제공합니다. 대시보드 하위 탭에는 테이블별 수집 데이터가 표시되며, 기본적으로 선택한 시간 범위 내에서 가장 많이 수집한 테이블 5개(개요 페이지에 표시된 테이블과 동일)가 표시됩니다. 작업 영역 테이블 드롭다운을 통해 표시할 테이블을 선택할 수 있습니다. 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="작업 영역 사용량 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* 주 그리드 - 여기에서 솔루션별로 그룹화되는 테이블과 각 테이블에 대한 정보를 볼 수 있습니다
    - 선택한 시간 범위 동안 수집된 데이터의 양
    - 선택한 시간 범위 동안 전체 수집량에서 이 테이블이 차지하는 백분율입니다. 이를 통해 수집에 가장 많은 영향을 주는 테이블을 식별할 수 있습니다. 아래 스크린샷에서는 AzureDiagnostics 및 ContainerLog만으로도 이 작업 영역에 수집된 데이터의 2/3 이상(64%)을 차지합니다.
    - 각 테이블에 대한 사용량 통계의 마지막 업데이트인 경우 일반적으로 사용량 통계가 매시간 새로 고쳐질 것으로 예상됩니다. 사용량 통계를 새로 고치는 것은 반복적인 서비스 내부 작업이므로, 데이터를 올바르게 해석하는지를 알 수 있도록 해당 데이터를 새로 고침하는 지연만 기록합니다. 사용자가 수행해야 하는 작업은 없습니다.
    - 청구 가능 - 청구되는 테이블 및 무료인 테이블을 나타냅니다.

* 테이블별 세부 정보

    페이지 맨 아래에서 주 그리드에서 선택한 테이블에 대한 자세한 정보를 볼 수 있습니다.
    - 수집량 - 각 리소스에서 테이블에 수집된 데이터의 양과 시간이 지남에 따라 분산되는 방법을 나타냅니다. 이 테이블에 전송된 총량의 30%에 해당하는 양을 수집하는 리소스는 주목할 수 있도록 경고 기호로 표시됩니다.
    - 수집 대기 시간 - 이 테이블로 전송된 요청의 50, 90, 95번째 백분위수에 대한 수집 소요 시간입니다. 이 영역의 위쪽 차트는 이벤트가 발생한 시간부터 작업 영역에 수집될 때까지 요청의 총 수집 시간(선택한 백분위수에 대한)을 보여 줍니다.
    아래 차트에는 에이전트의 대기 시간(에이전트가 작업 영역에 로그를 전송하는 데 걸린 시간)과 파이프라인의 대기 시간(서비스에서 데이터를 처리하고 작업 영역에 푸시하는 데 걸린 시간)이 별도로 표시됩니다.
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="작업 영역 사용량 수집 대기 시간 하위 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>추가 사용량 쿼리

추가 쿼리 하위 탭은 사용량 메타데이터를 사용하는 대신 매시간 새로 고쳐진 모든 작업 영역 테이블에서 실행되는 쿼리를 노출합니다. 해당 쿼리가 훨씬 더 광범위하고 덜 효율적이기 때문에 이는 자동으로 실행되지 않습니다. 그러나 작업 영역에 대부분의 로그를 보내는 리소스에 대한 흥미로운 정보를 노출하고 청구에 영향을 줄 수 있습니다.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="작업 영역 사용량 추가 쿼리 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

이러한 쿼리 중 하나는 ‘이 작업 영역에 가장 많은 로그를 전송하는 Azure 리소스’(상위 50개 표시)입니다.
데모 작업 영역에서 3개의 Kuberbetes 클러스터가 다른 모든 리소스를 결합한 것보다 훨씬 더 많은 데이터를 보내고, 그 중 일부는 작업 영역을 가장 많이 로드한다는 것을 명확하게 알 수 있습니다.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="추가 쿼리 결과가 포함된 작업 영역 사용량 추가 쿼리 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>상태 탭

이 탭에는 작업 영역 성능 상태, 마지막으로 보고된 시기, 작업 [오류 및 경고](../logs/monitor-workspace.md)(_LogOperation 테이블에서 검색) 등이 표시됩니다. [여기](../logs/monitor-workspace.md#categories)에서 완화 단계뿐만 아니라 나열된 문제에 관한 자세한 내용을 확인할 수 있습니다.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="작업 영역 상태 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>에이전트 탭

이 탭에서는 이 작업 영역으로 로그를 보내는 에이전트에 대한 정보를 제공합니다.
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="작업 영역 에이전트 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* 작업 오류 및 경고 - 특히 에이전트와 관련된 오류 및 경고입니다. 발생할 수 있는 다양한 문제를 보다 명확하게 볼 수 있도록 오류/경고 제목으로 그룹화되지만, 참조하는 정확한 시간과 리소스를 표시하도록 확장할 수 있습니다. 또한 ‘로그에서 쿼리 실행’을 클릭하여 로그 환경을 통해 _LogOperation 테이블을 쿼리하고, 원시 데이터를 확인하고 이를 추가로 분석할 수 있습니다.
* 작업 영역 에이전트 - 선택한 시간 범위 동안 로그를 작업 영역으로 보낸 에이전트입니다. 에이전트의 유형과 성능 상태를 확인할 수 있습니다. 정상으로 표시된 에이전트가 반드시 잘 작동하는 것은 아닙니다. 지난 1시간 동안 하트비트를 전송했음을 나타낼 뿐입니다. 자세한 성능 상태는 아래 그리드에 자세히 설명되어 있습니다.
* 에이전트 작업 - 이 표에는 모든 에이전트, 정상 또는 비정상 에이전트에 대한 정보가 표시됩니다. 여기에서도 “정상”은 에이전트가 지난 1시간 동안 하트비트를 전송했다는 것만을 나타낼 뿐입니다. 상태를 더 잘 이해하려면 그리드에 표시된 추세를 검토하여 이 에이전트가 시간이 지남에 따라 보낸 하트비트 수를 확인합니다. 실제 성능 상태는 모니터링되는 리소스의 작동 방식을 알고 있는 경우에만 유추할 수 있습니다. 예를 들어 컴퓨터가 특정 시간에 의도적으로 종료된 경우 에이전트의 하트비트는 일치하는 패턴으로 간헐적으로 나타날 수 있습니다.


### <a name="query-audit-tab"></a>쿼리 감사 탭

쿼리 감사는 작업 영역에서 쿼리 실행에 대한 로그를 생성합니다. 이 데이터를 사용하도록 설정하면 쿼리 성능, 효율성 및 부하를 이해하고 개선하는 데 매우 유용합니다. 작업 영역에 대한 쿼리 감사를 사용하도록 설정하거나 자세히 알아보려면 [Azure Monitor 로그 쿼리 감사](../logs/query-audit.md)를 참조하세요.

#### <a name="performance"></a>성능 
이 탭에는 다음이 표시됩니다.
* 쿼리 지속 시간 - 95번째 백분위수와 50번째 백분위수(중앙값) 시간(밀리초).
* 반환된 행 수 - 시간에 따른 행 수의 95번째 백분위수와 50번째 백분위수 (중앙값).
* 시간이 지남에 따라 처리된 데이터의 양 - 95번째 백분위수, 50번째 백분위 수 및 모든 요청에서 처리된 데이터의 합계.
* 응답 코드 - 선택한 시간 범위 내의 모든 쿼리에 대한 응답 코드의 배포.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="쿼리 감사 탭, 성능 하위 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>느리고 비효율적인 쿼리 
이 탭에는 재고해야 할 느리고 비효율적인 쿼리를 식별하는 데 도움이 되는 두 개의 그리드가 표시됩니다. 이러한 쿼리는 작업 영역에 불필요한 만성 부하를 만들기 때문에 대시보드 또는 경고에 사용하면 안 됩니다.
* 리소스를 가장 많이 사용하는 쿼리 - 처리된 데이터 양(KB), 각 쿼리의 시간 범위 및 텍스트와 함께 CPU를 가장 많이 사용하는 쿼리 10개.
* 가장 느린 쿼리 - 각 쿼리의 시간 범위 및 텍스트와 함께 가장 느린 쿼리 10개.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="쿼리 감사 탭, 느린 쿼리 하위 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>쿼리 사용자 
이 탭은 이 작업 영역에 대한 사용자 활동을 보여 줍니다.
* 사용자별 쿼리 - 선택한 시간 범위에서 각 사용자가 실행한 쿼리 수
* 제한된 사용자 - 작업 영역을 과도하게 쿼리하기 때문에 제한된 쿼리를 실행한 사용자

### <a name="change-log-tab"></a>변경 로그 탭

이 탭에는 지난 90일 동안(선택한 시간 범위에 상관 없이) 작업 영역에 적용된 구성 변경 내용이 표시되고 누가 해당 변경 작업을 수행했는지 표시됩니다.
데이터 제한, 작업 영역 라이선스와 같은 중요한 작업 영역 설정을 변경하는 사람을 모니터링하는 데 도움을 주기 위한 것입니다.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="작업 영역 변경 로그 탭의 스크린샷" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>다음 단계

[Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../visualize/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
