---
title: Azure Data Explorer Insights| Microsoft Docs
description: 이 문서에서는 Azure Data Explorer Insights 사용하는 방법을 설명합니다.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: fc40608b7c9faa4acdef999fdef787d787aca720
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132819"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer 인사이트

Azure Data Explorer 인사이트는 클러스터 성능, 작업, 사용량, 오류에 대한 통합 보기를 통해 포괄적인 클러스터 모니터링을 제공합니다.

다음을 제공합니다.

-    **대규모 큐브 뷰 입니다.** 클러스터의 기본 메트릭에 대한 스냅샷 보기를 사용하면 쿼리, 검색 및 내보내기 작업의 성능을 추적할 수 있습니다.
-   **드릴다운 분석.** 특정 Azure Data Explorer 클러스터로 드릴다운하여 자세한 분석을 수행할 수 있습니다.
-    **사용자 지정**. 보려는 메트릭을 변경하고, 제한에 맞는 임계값을 수정하거나 설정하고, 고유한 사용자 지정 통합 문서를 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.

이 문서는 Azure Data Explorer 인사이트를 온보딩하고 사용하는 방법을 이해하는 데 도움이 됩니다.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor 보기(대규모 큐브 뷰)

Azure Monitor 클러스터에 대한 기본 성능 메트릭을 볼 수 있습니다. 이러한 메트릭에는 구독의 여러 클러스터에서 쿼리, 검색 및 내보내기 작업에 대한 정보가 포함됩니다. 성능 문제를 식별하는 데 도움이 될 수 있습니다.

모든 구독에서 클러스터의 성능을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 창에서 **모니터를** 선택합니다. Insights **Hub** 섹션에서 **클러스터 Azure Data Explorer** 선택합니다.

![Azure Data Explorer 클러스터의 성능을 보기 위한 선택 항목의 스크린샷.](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>개요 탭

선택한 구독의 **개요** 탭에서, 테이블은 구독 내에 그룹화된 Azure Data Explorer 클러스터 모음의 대화형 메트릭을 표시합니다. 다음 드롭다운 목록에서 선택한 옵션에 따라 결과를 필터링할 수 있습니다.

* **구독:** Azure Data Explorer 클러스터가 있는 구독만 나열됩니다.

* **Azure Data Explorer 클러스터:** 기본적으로 최대 5개의 클러스터가 미리 선택되어 있습니다. 범위 선택기에서 모든 또는 여러 클러스터를 선택하면 최대 200개의 클러스터가 반환됩니다.

* **시간 범위:** 기본적으로 테이블에는 해당 선택 항목에 따라 지난 24시간 동안의 정보가 표시됩니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독에서 Azure Data Explorer 클러스터의 총 수를 제공하고 선택한 개수를 보여 줍니다. 열에 대한 조건부 색 코딩이 있습니다. **Keep alive,** **CPU**, **Ingestion Utilization**, 및 **Cache Utilization**. 주황색으로 코딩된 셀에는 클러스터에 유지 가능하지 않은 값이 있습니다. 

각 메트릭이 무엇을 의미하는지 보다 잘 이해하려면 [Azure Data Explorer 메트릭](/azure/data-explorer/using-metrics#cluster-metrics)에 대한 설명서를 참조하는 것이 좋습니다.

### <a name="query-performance-tab"></a>쿼리 성능 탭

**쿼리 성능** 탭에는 쿼리 기간, 총 동시 쿼리 수 및 총 제한 쿼리 수가 표시됩니다.

![쿼리 성능 탭의 스크린샷.](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>수집 성능 탭

**집계 성능** 탭에는 이벤트 허브 및 IoT Hub에 대해 처리된 이벤트, 성공한 검색 결과, 실패한 검색 결과, 검색 볼륨 및 이벤트가 표시됩니다.

[![집계 성능 탭의 스크린샷.](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>스트리밍 수집 성능 탭

**스트리밍 수집 성능** 탭은 평균 데이터 속도, 평균 기간 및 요청 속도에 대한 정보를 제공합니다.

### <a name="export-performance-tab"></a>내보내기 성능 탭

**내보내기 성능** 탭은 연속 내보내기 작업의 내보낸 레코드, 대기, 보류 중인 수 및 사용률 비율에 대한 정보를 제공합니다.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure Data Explorer 클러스터 리소스에서 보기(드릴다운 분석)

Azure Data Explorer 클러스터에서 직접 Azure Data Explorer Insights 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Data Explorer 클러스터** 를 선택합니다.

2. 목록에서 Azure Data Explorer 클러스터를 선택합니다. 모니터링 섹션에서 **Insights** 선택합니다.

Azure Monitor 인사이트 보기 내에서 Azure Data Explorer 클러스터의 리소스 이름을 선택하여 이러한 보기에 액세스할 수도 있습니다.

> [!NOTE]
> Azure Data Explorer 인사이트는 로그와 메트릭을 결합하여 글로벌 모니터링 솔루션을 제공합니다. 로그 기반 시각화를 포함하려면 사용자가 Azure Data Explorer [클러스터의 진단 로깅을 사용하도록 설정하고 Log Analytics 작업 영역으로 보내야 합니다.](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 사용하도록 설정해야 하는 진단 로그는 **Command**, **Query**, **SucceededIngestion**, **FailedIngestion**, **IngestionBatching,** **TableDetails** 및 **TableUsageStatistics 입니다.** **SucceededIngestion** 로그를 사용하도록 설정하면 비용이 많이 들 수 있습니다. 성공적인 복제를 모니터링해야 하는 경우에만 사용하도록 설정합니다.)

![모니터링을 위해 로그를 구성하는 단추의 스크린샷.](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>개요 탭

**개요** 탭에는 다음이 표시됩니다.

- 빠른 상태 평가를 위해 클러스터의 가용성 및 전반적인 상태를 강조 표시한 메트릭 타일입니다.

- 활성 [Azure Advisor 권장 사항](/azure/data-explorer/azure-advisor) 및 리소스 [상태에](/azure/data-explorer/monitor-with-resource-health) 대한 요약입니다.

- 상위 CPU 및 메모리 소비자와 시간별 고유 사용자 수를 보여 주는 차트입니다.

[![Azure Data Explorer 클러스터 리소스의 보기 스크린샷.](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>주요 메트릭 탭

**주요 메트릭** 탭에는 클러스터 메트릭 중 일부에 대한 통합 보기가 표시됩니다. 일반 메트릭, 쿼리 관련 메트릭, 검색 관련 메트릭 및 스트리밍 검색 관련 메트릭으로 그룹화됩니다.

[![주요 메트릭 탭의 그래프 스크린샷.](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>사용량 탭

사용자는 **사용량** 탭에서 클러스터의 명령 및 쿼리 성능에 대해 자세히 알아볼 수 있습니다. 이 탭에서는 다음을 수행할 수 있습니다.
 
- 가장 많은 쿼리를 보내거나 CPU 및 메모리를 가장 많이 사용하는 작업 그룹, 사용자 및 애플리케이션을 확인합니다. 그런 다음, 처리할 클러스터에 대해 가장 많은 쿼리를 제출하는 워크로드를 이해할 수 있습니다.
- 실패한 쿼리를 통해 상위 워크로드 그룹, 사용자 및 애플리케이션을 식별합니다.
- 작업 그룹, 사용자 및 애플리케이션별로 과거 일별 평균(지난 16일)에 비해 쿼리 수의 최근 변경 내용을 식별합니다.
- 작업 그룹, 사용자, 애플리케이션 및 명령 유형별로 쿼리, 메모리 및 CPU 사용량의 추세와 최대 사용량을 식별합니다.

**사용 탭에는** 사용자가 직접 수행하는 작업이 포함됩니다. 내부 클러스터 작업은 이 탭에 포함되지 않습니다.

[![명령 및 쿼리와 관련된 도넛형 차트가 있는 작업 보기의 스크린샷.](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![쿼리 및 메모리와 관련된 선형 차트가 있는 작업 보기의 스크린샷.](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>테이블 탭

**테이블** 탭에는 클러스터에 있는 테이블의 최신 및 기록 속성이 표시됩니다. 공간을 가장 많이 사용하는 테이블을 확인할 수 있습니다. 테이블 크기, 핫 데이터 및 시간에 따른 행 수별로 증가 기록을 추적할 수도 있습니다.

### <a name="cache-tab"></a>캐시 탭

**캐시** 탭을 사용하면 사용자가 실제 쿼리의 조회 창 패턴을 분석하고 구성된 캐시 정책(각 테이블에 대해)과 비교할 수 있습니다. 쿼리되지 않은 대부분의 쿼리 및 테이블에서 사용하는 테이블을 식별하고 이에 따라 캐시 정책을 적용할 수 있습니다. 

Azure Advisor 특정 테이블에 대한 캐시 정책 권장 사항을 얻을 수 있습니다. 현재 캐시 권장 사항은 [기본 Azure Advisor 대시보드](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)에서만 사용할 수 있습니다. 지난 30일 동안 실제 쿼리의 조회 기간과 쿼리의 95% 이상에 대한 최적이 아닌 캐시 정책을 기반으로 합니다. 

Azure Advisor 캐시 감소 권장 사항은 "데이터에 의해 제한되는" 클러스터에 사용할 수 있습니다. 즉, 클러스터의 CPU 및 수집 사용률이 낮지만 데이터 용량이 높기 때문에 클러스터를 스케일 인하거나 축소할 수 없습니다.

[![캐시 세부 정보의 스크린샷.](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>클러스터 경계 탭

**클러스터 경계 탭은** 사용량에 따라 클러스터 경계를 표시합니다. 이 탭에서 CPU, 집계 및 캐시 사용률을 검사할 수 있습니다. 이러한 메트릭은 **낮음,** **중간** 또는 **높음으로** 점수가 매칭됩니다. 이러한 메트릭 및 점수는 클러스터에 대한 최적의 SKU 및 인스턴스 수를 결정할 때 중요합니다. Azure Advisor SKU/크기 권장 사항에서 고려됩니다. 

이 탭에서 메트릭 타일 및 심층 분석 을 선택하여 추세와 점수가 결정되는 방식을 이해할 수 있습니다. 클러스터에 대한 Azure Advisor SKU/크기 권장 사항을 볼 수도 있습니다. 예를 들어 다음 이미지에서는 모든 메트릭의 점수가 **낮음으로** 표시됩니다. 클러스터는 규모를 축소/축소하고 비용을 절감할 수 있는 비용 권장 사항을 받습니다.

> [!div class="mx-imgBorder"]
> [![클러스터 경계의 스크린샷.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>Azure 대시보드에 고정

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 메트릭 섹션 중 하나("대규모" 관점)를 Azure 대시보드에 고정할 수 있습니다.

![선택한 고정 아이콘의 스크린샷.](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Azure Data Explorer 인사이트 사용자 지정

통합 문서를 편집하여 데이터 분석 요구 사항을 지원하도록 사용자 지정할 수 있습니다.
* 항상 특정 구독 또는 Azure Data Explorer 클러스터를 선택하도록 통합 문서의 범위를 지정합니다.
* 표에서 메트릭을 변경합니다.
* 임계값 또는 색 렌더링/코딩을 변경합니다.

위쪽 도구 모음에서 사용자 지정 단추를 선택하여 **사용자 지정을** 시작할 수 있습니다.

![사용자 지정 단추의 스크린샷.](./media/data-explorer/customize.png)

사용자 지정은 게시된 통합 문서의 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 리소스 그룹 내에서 프라이빗 위치인 **내 보고서** 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후 통합 문서 갤러리로 이동하여 엽니다.

![통합 문서 갤러리의 스크린샷.](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>문제 해결

일반적인 문제 해결 지침은 통합 문서 기반 인사이트 문제 해결 문서를 [참조하세요.](troubleshoot-workbooks.md)

다음 섹션에서는 Azure Data Explorer Insights 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 됩니다.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>구독 선택기에 내 구독이 전부 표시되지 않는 이유는 무엇인가요?

Azure Data Explorer Insights 선택한 구독 필터에서 선택한 Azure Data Explorer 클러스터가 포함된 구독만 표시합니다. Azure Portal **디렉터리 + 구독에서 구독** 필터를 선택합니다.

![구독 필터를 선택하는 스크린샷.](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>사용량, 테이블 또는 캐시 섹션에 Azure Data Explorer 클러스터에 대한 데이터가 표시되지 않는 이유는 무엇인가요?

로그 기반 데이터를 보려면 모니터링하려는 각 Azure Data Explorer 클러스터에 [대해 진단 로그를 사용하도록 설정해야](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 합니다. 각 클러스터에 대한 진단 설정에서 이 작업을 수행할 수 있습니다. Log Analytics 작업 영역으로 데이터를 보내야 합니다. 사용하도록 설정해야 하는 진단 로그는 **Command**, **Query**, **TableDetails** 및 **TableUsageStatistics 입니다.**

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>내 Azure Data Explorer 클러스터에 대한 로그를 이미 사용하도록 설정했습니다. 명령 및 쿼리에서 내 데이터를 여전히 볼 수 없는 이유는 무엇인가요?

현재 진단 로그는 소급하여 작동하지 않습니다. Azure Data Explorer 작업을 수행하면 데이터가 나타나기 시작합니다. Azure Data Explorer 클러스터의 활성 정도에 따라 시간에서 하루까지 다소 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

통합 문서가 지원하도록 설계된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등은 Azure Monitor 통합 문서를 사용하여 [대화형 보고서 만들기를 검토하여 알아봅니다.](../visualize/workbooks-overview.md)
