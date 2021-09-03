---
title: Azure Data Explorer 인사이트(ADX 인사이트) | Microsoft Docs
description: 이 문서에서는 ADX 인사이트(Azure Data Explorer 인사이트)에 대해 설명합니다.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 872c1e29b6c85f24c4e9841dca359a9429b92321
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458122"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer 인사이트

Azure Data Explorer 인사이트는 클러스터 성능, 작업, 사용량, 오류에 대한 통합 보기를 통해 포괄적인 클러스터 모니터링을 제공합니다.
이 문서는 Azure Data Explorer 인사이트를 온보딩하고 사용하는 방법을 이해하는 데 도움이 됩니다.

## <a name="introduction-to-azure-data-explorer-insights"></a>Azure Data Explorer 인사이트 소개

이 환경을 살펴보기 전에, 이 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다.
-    **대규모 관점**, 쿼리, 수집 및 내보내기 작업의 성능을 쉽게 추적할 수 있도록 클러스터의 기본 메트릭에 대한 스냅샷 보기를 보여 줍니다.
-   **드릴다운 분석**, 특정 Azure Data Explorer 클러스터에 있으며 세부적인 분석을 수행하는 데 도움됩니다.
-    **사용자 지정 가능**, 표시할 메트릭을 변경하고, 제한에 맞도록 임계값을 수정 또는 설정하고, 사용자 고유의 사용자 지정 통합 문서를 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor 보기(대규모 관점)

Azure Monitor에서 클러스터에 대한 주요 성능 메트릭(구독의 여러 클러스터의 쿼리, 수집 및 내보내기 작업에 대한 메트릭 포함)을 확인하고 성능 문제를 쉽게 파악할 수 있습니다.

모든 구독의 클러스터 성능을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터링** 을 선택한 다음, 인사이트 허브 섹션에서 **Azure Data Explorer 클러스터** 를 선택합니다.

![여러 그래프가 있는 개요 환경의 스크린샷](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>개요 탭

선택한 구독의 **개요** 탭에서, 테이블은 구독 내에 그룹화된 Azure Data Explorer 클러스터 모음의 대화형 메트릭을 표시합니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* 구독 – Azure Data Explorer 클러스터가 있는 구독만 나열됩니다.

* Azure Data Explorer 클러스터 – 기본적으로 최대 5개의 클러스터만 미리 선택됩니다. 범위 선택기에서 모든 또는 여러 클러스터를 선택하면 최대 200개의 클러스터가 반환됩니다.

* 시간 범위 - 기본적으로는 선택한 항목을 기준으로 지난 24시간의 정보를 표시합니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독의 총 Azure Data Explorer 클러스터 개수를 롤업하고 선택된 개수를 반영합니다. 연결 유지, CPU, 수집 사용률 및 캐시 사용률과 같은 열에 대한 조건부 색 코딩이 있습니다. 주황색으로 코딩된 셀에는 클러스터에 유지 가능하지 않은 값이 있습니다. 

각 메트릭이 무엇을 의미하는지 보다 잘 이해하려면 [Azure Data Explorer 메트릭](/azure/data-explorer/using-metrics#cluster-metrics)에 대한 설명서를 참조하는 것이 좋습니다.

### <a name="query-performance-tab"></a>쿼리 성능 탭

이 탭에는 쿼리 기간, 총 동시 쿼리 수 및 제한된 쿼리의 총 수가 표시됩니다.

![쿼리 성능 탭의 스크린샷](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>수집 성능 탭

이 탭은 수집 대기 시간, 성공 수집 결과, 실패한 수집 결과, 수집 볼륨 및 Event/IoT Hub에 대해 처리된 이벤트를 표시합니다.

[![수집 성능 탭의 스크린샷](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>스트리밍 수집 성능 탭

이 탭에는 평균 데이터 속도, 평균 기간 및 요청 속도에 대한 정보가 제공됩니다.

### <a name="export-performance-tab"></a>내보내기 성능 탭

이 탭은 연속 내보내기 작업의 내보낸 레코드, 지연, 보류 중인 개수 및 사용률 비율에 대한 정보를 제공합니다.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure Data Explorer 클러스터 리소스 보기(드릴다운 분석)

Azure Data Explorer 클러스터에서 직접 Azure Data Explorer 인사이트에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Data Explorer 클러스터** 를 선택합니다.

2. 목록에서 Azure Data Explorer 클러스터를 선택합니다. 모니터링 섹션에서 **인사이트** 를 선택합니다.

Azure Monitor 인사이트 보기에서 Azure Data Explorer 클러스터의 리소스 이름을 선택하여 이런 보기에 액세스할 수도 있습니다.

Azure Data Explorer 인사이트는 로그와 메트릭을 결합하여 글로벌 모니터링 솔루션을 제공합니다. 로그 기반 시각화를 포함하면 사용자는 [Azure Data Explorer 클러스터의 진단 로깅을 사용하도록 설정하고 Log Analytics 작업 영역으로 보내야 합니다](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). 사용해야 하는 진단 로그는 **Command**, **Query**, **tabledetails** 및 **TableUsageStatistics** 입니다.

!["모니터링용 로그 사용" 텍스트를 표시하는 파란색 단추의 스크린샷](./media/data-explorer/enable-logs.png)


 **개요** 탭에는 다음이 표시됩니다.

- 메트릭 타일은 클러스터의 가용성 및 전반적인 상태를 강조 표시하여 상태를 빠르게 평가합니다.

- 활성 [Advisor 권장 사항](/azure/data-explorer/azure-advisor) 및 [리소스 상태](/azure/data-explorer/monitor-with-resource-health)에 대한 요약입니다.

- 시간이 지남에 따라 상위 CPU 및 메모리 소비자와 고유한 사용자 수를 보여 주는 차트


[![Azure Data Explorer 클러스터 리소스 보기의 스크린샷](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

**주요 메트릭** 탭에는 일반 메트릭, 쿼리 관련, 수집 관련 및 스트리밍 수집 관련 메트릭에 따라 그룹화된 일부 클러스터 메트릭에 대한 통합 보기가 표시됩니다.

[![실패 보기의 스크린샷](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

사용자는 **사용량** 탭에서 클러스터의 명령 및 쿼리 성능에 대해 자세히 알아볼 수 있습니다. 이 페이지에서는 다음을 수행할 수 있습니다.
 
 - 가장 많은 쿼리를 전송하거나 가장 많은 CPU와 메모리를 사용하는 작업 그룹, 사용자 및 애플리케이션을 확인하여 클러스터에서 처리하기에 가장 무거운 쿼리를 전송하는 작업을 파악할 수 있습니다.
 - 실패한 쿼리의 상위 작업 그룹, 사용자 및 애플리케이션을 식별합니다.
 - 작업 그룹, 사용자 및 애플리케이션을 기준으로 지난 16일간의 일별 평균 기록과 비교하여 쿼리 수의 최근 변경 사항을 확인합니다.
 - 작업 그룹, 사용자, 애플리케이션 및 명령 유형별로 쿼리 수, 메모리 및 CPU 사용량의 추세 및 최대치를 식별합니다.

[![명령 및 쿼리 수 기준 상위 애플리케이션, 명령 및 쿼리 수 기준 상위 주체, 명령 및 쿼리 수 기준 상위 명령에 대한 도넛형 차트가 포함된 작업 보기의 스크린샷](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![애플리케이션별 쿼리 수, 애플리케이션별 총 메모리 및 애플리케이션별 총 CPU를 보여 주는 꺾은선형 차트에 대한 작업 보기의 스크린샷](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

**테이블** 탭에서는 클러스터에 있는 테이블의 최신 및 기록 속성을 표시합니다. 가장 많은 공간을 사용하는 테이블을 확인하고, 테이블 크기, 핫 데이터 및 행 수를 기준으로 시간이 지남에 따른 증가 기록을 추적할 수 있습니다.

사용자는 **캐시** 탭을 통해 실제 쿼리의 조회 기간 패턴을 분석하고 각 테이블에 대해 구성된 캐시 정책과 비교할 수 있습니다. 쿼리되지 않은 대부분의 쿼리 및 테이블에서 사용하는 테이블을 식별하고 이에 따라 캐시 정책을 적용할 수 있습니다. 지난 30일 동안의 실제 쿼리 조회 기간과 최소 95% 쿼리에 대한 최적화되지 않은 캐시 정책을 기반으로 하여 Azure Advisor의 특정 테이블에 대한 특정 캐시 정책 권장 사항을 확인할 수 있습니다. 현재는 [기본 Azure Advisor 대시보드](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)에서만 캐시 권장 사항을 사용할 수 있습니다. Azure Advisor의 캐시 감소 권장 사항은 "데이터에 제한된" 클러스터에 사용할 수 있습니다. 이는 클러스터의 CPU와 수집 사용률이 낮다는 것을 의미하며, 데이터 용량이 높기 때문에 클러스터가 스케일 인하거나 스케일 다운할 수 없습니다.

[![캐시 세부 정보의 스크린샷](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

**클러스터 경계** 탭에서는 사용량에 따라 클러스터 경계를 표시합니다. 이 탭에서 CPU, 수집 및 캐시 사용률을 검사할 수 있습니다. 이러한 메트릭은 "낮음", "중간" 또는 "높음"으로 점수가 매겨집니다. 이러한 메트릭과 점수는 클러스터에 대한 최적의 SKU 및 인스턴스 수를 결정할 때 중요하며, 이를 고려하여 Azure Advisor SKU/크기를 권장합니다. 이 탭에서 메트릭 타일 및 심층 분석을 선택하여 추세와 점수를 결정하는 방법을 파악할 수 있습니다. 클러스터에 대한 Azure Advisor SKU/크기 권장 사항을 볼 수도 있습니다. 예를 들어 다음 이미지에서는 모든 메트릭이 "낮음"으로 점수가 매겨진 것을 확인할 수 있으며, 이에 따라 클러스터는 규모를 스케일 인/스케일 다운하고 비용을 절감할 수 있는 비용 권장 사항을 받게 됩니다.

> [!div class="mx-imgBorder"]
> [![클러스터 경계의 스크린샷.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Azure 대시보드에 고정

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 "대규모" 관점의 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

![고정 아이콘이 선택된 스크린샷](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Azure Data Explorer 인사이트 사용자 지정

이 섹션에서는 데이터 분석 요구 사항을 지원하도록 사용자 지정하기 위해 통합 문서를 편집하는 일반적인 시나리오를 중점적으로 설명합니다.
* 항상 특정 구독 또는 하나 이상의 Azure Data Explorer 클러스터를 선택하도록 통합 문서 범위를 지정합니다.
* 그리드에서 메트릭 변경
* 임계값 또는 색 렌더링/코딩 변경

위쪽 도구 모음에서 **사용자 지정** 단추를 선택하여 편집 모드를 사용하도록 설정하면 사용자 지정을 시작할 수 있습니다.

![사용자 지정 단추의 스크린샷](./media/data-explorer/customize.png)

사용자 지정 항목은 게시된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 리소스 그룹 내에서 프라이빗 위치인 [내 보고서] 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 [공유 보고서] 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 통합 문서를 시작해야 합니다.

![통합 문서 갤러리의 스크린샷](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>문제 해결

일반적인 문제 해결 지침은 전용 통합 문서 기반 인사이트 [문제 해결 문서](troubleshoot-workbooks.md)를 참조하세요.

이 섹션에서는 Azure Data Explorer 인사이트를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 되는 정보를 제공합니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>구독 선택기에 내 구독이 전부 표시되지 않는 이유는 무엇인가요?

선택한 구독 필터에서 선택한 Azure Data Explorer 클러스터가 포함된 구독만 표시됩니다. 구독 필터는 Azure Portal 헤더의 "디렉터리 + 구독"에서 선택합니다.

![구독 필터의 스크린샷](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Azure Data Explorer 클러스터에 대한 데이터가 사용량, 테이블 또는 캐시 섹션에 표시되지 않는 이유는 무엇인가요?

로그 기반 데이터를 보려면 모니터링하려는 Azure Data Explorer 클러스터마다 [진단 로그를 사용하도록 설정](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)해야 합니다. 이 작업은 각 클러스터의 진단 설정에서 수행할 수 있습니다. Log Analytics 작업 영역으로 데이터를 보내야 합니다. 사용해야 하는 진단 로그는 Command, Query, TableDetails 및 TableUsageStatistics입니다.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Azure Data Explorer 클러스터에 대한 로그를 이미 사용하도록 설정했습니다. Commands 및 Queries 아래 데이터가 표시되지 않는 이유는 무엇인가요?

현재 진단 로그는 소급하여 작동하지 않으므로 키 Azure Data Explorer에 대한 작업이 수행된 후에만 데이터가 표시되기 시작합니다. 따라서 Azure Data Explorer 클러스터를 활성화하는 방법에 따라 몇 시간에서 하루까지 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

[Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../visualize/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
