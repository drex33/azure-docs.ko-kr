---
title: 자습서 - Azure 가상 머신에서 게스트 로그 및 메트릭 수집
description: Azure 가상 머신에서 게스트 로그 및 메트릭을 수집하는 데이터 수집 규칙을 만듭니다.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/08/2021
ms.openlocfilehash: 5bd3b557bf7bc9ada4e75979e593c7fb0c338dd4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352690"
---
# <a name="tutorial-collect-guest-logs-and-metrics-from-azure-virtual-machine"></a>자습서: Azure 가상 머신에서 게스트 로그 및 메트릭 수집
[VM 인사이트를 사용하여 모니터링을 사용하도록 설정하면](tutorial-monitor-vm-enable.md)Log Analytics 에이전트를 사용하여 성능 데이터를 수집합니다. 게스트 운영 체제에서 로그를 수집하고 성능 데이터를 Azure Monitor 메트릭으로 보내려면 [Azure Monitor 에이전트를](../agents/azure-monitor-agent-overview.md) 설치하고 수집할 데이터와 보낼 위치를 정의하는 DCR(데이터 [수집 규칙)을](../agents/data-collection-rule-overview.md) 만듭니다. 

> [!NOTE]
> Azure Monitor 에이전트 이전에는 AZURE 가상 머신에 대한 게스트 메트릭이 WAD(Windows) 및 LAD(Linux)용 [Azure 진단 확장을](../agents/diagnostics-extension-overview.md) 사용하여 수집되었습니다. 이러한 에이전트는 계속 사용할 수 있으며 가상 머신에 대한 **진단 설정** 메뉴 항목으로 구성할 수 있지만 Azure Monitor 에이전트로 교체 중입니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 게스트 성능 데이터를 Azure Monitor 메트릭에 보내고 이벤트를 Azure Monitor 로그에 기록하는 데이터 수집 규칙을 만듭니다. 
> * 메트릭 탐색기에서 게스트 메트릭을 봅니다.
> * Log Analytics에서 게스트 로그를 봅니다.

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 가상 머신입니다.


## <a name="create-data-collection-rule"></a>데이터 수집 규칙 만들기
[의 데이터 수집 규칙은](../agents/data-collection-rule-overview.md) Azure Monitor 수집할 데이터와 전송할 위치를 정의합니다. Azure Portal 사용하여 데이터 수집 규칙을 정의할 때 적용해야 하는 가상 머신을 지정합니다. Azure Monitor 에이전트가 없는 가상 머신에 자동으로 설치됩니다.

> [!NOTE]
> 현재 Azure Portal **모니터** 메뉴에서 Azure Monitor 에이전트를 설치해야 합니다. 이 기능은 아직 가상 머신의 메뉴에서 사용할 수 없습니다. 

Azure Portal **모니터** 메뉴에서 **데이터 수집 규칙,** **만들기를** 선택하여 새 데이터 수집 규칙을 만듭니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-create.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-create.png" alt-text="데이터 수집 규칙 만들기":::

기본 **탭에서** Azure Portal 표시되는  규칙의 이름인 규칙 이름을 제공합니다. DCR 및 해당 연결이 저장될 **구독,** **리소스 그룹** 및 **지역을** 선택합니다. 모니터링되는 리소스와 동일할 필요는 없습니다. **플랫폼 유형은** DCR의 나머지 부분을 정의할 때 사용할 수 있는 옵션을 정의합니다. 해당 리소스만 연결될 경우 *Windows* 또는 *Linux를* 선택하고, 두 형식 모두에 연결될 경우 *사용자 지정을* 선택합니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-basics.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-basics.png" alt-text="데이터 수집 규칙 기본 사항":::

## <a name="select-resources"></a>리소스 선택
**리소스** 탭에서 데이터 수집 규칙이 적용될 하나 이상의 가상 머신을 식별합니다. Azure Monitor 에이전트는 에이전트가 없는 모든 에이전트에 설치됩니다. **리소스 추가를** 클릭하고 가상 머신 또는 가상 머신이 있는 리소스 그룹 또는 구독을 선택합니다. 데이터 수집 규칙은 선택한 범위의 모든 가상 머신에 적용됩니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-resources.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-resources.png" alt-text="데이터 수집 규칙 리소스":::

## <a name="select-data-sources"></a>데이터 원본 선택
단일 데이터 수집 규칙에는 여러 데이터 원본이 있을 수 있습니다. 이 자습서에서는 동일한 규칙을 사용하여 게스트 메트릭과 게스트 로그를 모두 수집합니다. 메트릭 탐색기와 Log Analytics를 모두 사용하여 분석할 수 있도록 메트릭을 Azure Monitor 메트릭 및 Azure Monitor 로그에 보냅니다.

수집 **및 배달** 탭에서 **데이터 원본 추가를** 클릭합니다. 데이터 **원본 유형** 에 **대해 성능 카운터 를 선택합니다.** **기본** 설정을 그대로 두고 수집할 카운터를 선택합니다. **사용자 지정을** 사용하면 개별 메트릭 값을 선택할 수 있습니다. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-metric.png" alt-text="데이터 수집 규칙 메트릭 데이터 원본":::

**대상** 탭을 선택합니다. **Azure Monitor 메트릭이** 이미 나열되어 있어야 합니다. **대상 추가를** 클릭하여 다른 대상을 추가합니다. **대상 유형에** 대해 **Azure Monitor 로그를** 선택합니다. **계정 또는 네임스페이스에** 대한 Log Analytics 작업 영역을 선택합니다. **데이터 원본 추가를** 클릭하여 데이터 원본을 저장합니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-metric.png" alt-text="데이터 수집 규칙 대상":::

**데이터 원본 추가를** 다시 클릭하여 데이터 수집 규칙에 로그를 추가합니다. 데이터 **원본 유형에** 대해 **Windows 이벤트 로그** 또는 **Linux syslog 를** 선택합니다. 수집할 로그 데이터의 유형을 선택합니다. 

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-windows.png" alt-text="데이터 수집 규칙 Windows 로그 데이터 원본":::

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-data-source-logs-linux.png" alt-text="데이터 수집 규칙 Linux 로그 데이터 원본":::

**대상** 탭을 선택합니다. Azure Monitor **대상 유형에** 대해 **로그가** 이미 선택되어 있어야 합니다. **계정 또는 네임스페이스에** 대한 Log Analytics 작업 영역을 선택합니다. 작업 영역이 없는 경우 자동으로 만들어지는 구독의 기본 작업 영역을 선택할 수 있습니다. **데이터 원본 추가를** 클릭하여 데이터 원본을 저장합니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-destination-logs.png" alt-text="데이터 수집 규칙 로그 대상":::

**검토 + 만들기를** 클릭하여 데이터 수집 규칙을 만들고 선택한 가상 머신에 Azure Monitor 에이전트를 설치합니다.

:::image type="content" source="media/tutorial-monitor-vm/data-collection-rule-save.png" lightbox="media/tutorial-monitor-vm/data-collection-rule-save.png" alt-text="데이터 수집 규칙 저장":::

## <a name="viewing-logs"></a>로그 보기
KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 Log Analytics 작업 영역에서 데이터가 검색됩니다. 미리 생성된 쿼리 집합을 가상 머신에 사용할 수 있지만, 간단한 쿼리를 사용하여 수집 중인 이벤트를 살펴보겠습니다. 

가상 머신의 메뉴에서 **로그를** 선택합니다. 범위가 해당 컴퓨터로 설정된 빈 쿼리 창이 있는 Log Analytics가 열립니다. 모든 쿼리에는 해당 컴퓨터에서 수집된 레코드만 포함됩니다. 

> [!NOTE]
> Log **Analytics를** 열면 쿼리 창이 열릴 수 있습니다. 여기에는 사용할 수 있는 미리 만들어진 쿼리가 포함됩니다. 지금은 간단한 쿼리를 수동으로 만들 예정이므로 이 창을 닫습니다.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics.png" lightbox="media/tutorial-monitor-vm/log-analytics.png" alt-text="Log Analytics":::


빈 쿼리 창에서 `Event` `Syslog` 컴퓨터가 Windows 또는 Linux를 실행하고 있는지 여부에 따라 또는 를 입력한 **다음, 실행을** 클릭합니다. **시간 범위** 내에서 수집된 이벤트가 표시됩니다.

> [!NOTE]
> 쿼리에서 데이터를 반환하지 않는 경우 수집할 가상 머신에 이벤트가 생성될 때까지 몇 분 정도 기다려야 할 수 있습니다. 추가 범주의 이벤트를 포함하도록 데이터 수집 규칙에서 데이터 원본을 수정해야 할 수도 있습니다.

:::image type="content" source="media/tutorial-monitor-vm/log-analytics-query.png" lightbox="media/tutorial-monitor-vm/log-analytics-query.png" alt-text="쿼리 결과가 있는 Log Analytics":::

Log Analytics를 사용하여 로그 데이터를 분석하는 자습서는 [Log Analytics 자습서 를](../logs/log-analytics-tutorial.md)참조하세요. 로그 데이터에서 경고 규칙을 만드는 자습서는 [자습서: Azure 리소스에 대한 로그 쿼리 경고 만들기를 참조하세요.](../alerts/tutorial-log-alert.md)

## <a name="view-guest-metrics"></a>게스트 메트릭 보기
[다른 Azure 리소스](../essentials/tutorial-metrics.md)와 마찬가지로 데이터 수집 규칙 없이 메트릭 탐색기를 사용하여 호스트 가상 머신에 대한 메트릭을 볼 수 있습니다. 하지만 데이터 수집 규칙을 사용하면 메트릭 탐색기를 사용하여 호스트 메트릭 외에도 게스트 메트릭을 볼 수 있습니다.

가상 머신의 메뉴에서 **메트릭을** 선택합니다. 범위가 가상 머신으로 설정된 메트릭 탐색기가 열립니다. **메트릭 네임스페이스** 를 클릭하고 **Virtual Machine 게스트** 를 선택합니다. 

> [!NOTE]
> **Virtual Machine 게스트** 가 표시되지 않으면 에이전트가 배포되고 데이터 수집이 시작될 때까지 몇 분 정도 더 기다려야 할 수 있습니다.


:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer.png" lightbox="media/tutorial-monitor-vm/metrics-explorer.png" alt-text="메트릭 탐색기":::

사용 가능한 게스트 메트릭이 표시됩니다. 차트에 추가할 **메트릭을** 선택합니다.

:::image type="content" source="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" lightbox="media/tutorial-monitor-vm/metrics-explorer-guest-metrics.png" alt-text="게스트 메트릭이 있는 메트릭 탐색기":::

자습서: Azure 리소스에 대한 메트릭 분석 및 [자습서: Azure 리소스에 대한](../essentials/tutorial-metrics.md) 메트릭 [경고 만들기에서](../alerts/tutorial-metric-alert.md)메트릭 탐색기를 사용하여 메트릭 데이터를 보고 분석하는 전체 자습서를 확인할 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이제 가상 머신에 대한 게스트 메트릭을 수집하기 때문에 사용 가능한 메모리 및 논리 디스크 공간과 같은 게스트 메트릭을 기반으로 메트릭 경고를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Monitor 메트릭 경고 만들기](../alerts/tutorial-metric-alert.md)


