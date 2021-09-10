---
title: Azure Monitor 통합 문서 개요
description: 통합 문서가 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공하는 방법을 알아봅니다.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c9a535e06278146f89f2c8f6a1844813c2417a1a
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706911"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor 통합 문서

통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이를 통해 Azure에서 여러 데이터 원본을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다.

다음은 통합 문서 만들기에 대한 동영상 설명입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

> [!NOTE]
> 레거시 및 프라이빗 통합 문서가 제거되었습니다. [통합 문서 검색 도구](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/LegacyAI/DeprecatedWorkbookRetrievalTool.md)를 사용하여 이전 통합 문서의 내용을 검색합니다.

## <a name="data-sources"></a>데이터 원본

통합 문서는 Azure 내의 여러 원본에서 데이터를 쿼리할 수 있습니다. 통합 문서 작성자는 이 데이터를 변환하여 기본 구성 요소의 가용성, 성능, 사용량 및 전반적 상태에 대한 인사이트를 제공할 수 있습니다. 예를 들어 가상 머신에서 성능 로그를 분석하여 높은 CPU 사용률 또는 메모리 부족 인스턴스를 식별하고 결과를 대화형 보고서에 그리드 형태로 표시합니다.
  
그러나 통합 문서의 진정한 강점은 단일 보고서 내에서 서로 다른 원본의 데이터를 결합하는 기능입니다. 그러면 복합 리소스 보기를 만들거나 여러 리소스를 결합하여 이 기능이 없을 때보다 풍부한 데이터 및 인사이트를 제공할 수 있습니다.

통합 문서는 현재 다음 데이터 원본과 호환됩니다.

* [로그](../visualize/workbooks-data-sources.md#logs)
* [메트릭](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [경고(미리 보기)](../visualize/workbooks-data-sources.md#alerts-preview)
* [워크로드 상태](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>시각화

통합 문서는 데이터 시각화를 위한 풍부한 기능 집합을 제공합니다. 각 시각화 유형의 자세한 예제를 보려면 아래 링크를 참조하세요.

* [Text](../visualize/workbooks-text-visualizations.md)
* [차트](../visualize/workbooks-chart-visualizations.md)
* [그리드](../visualize/workbooks-grid-visualizations.md)
* [Tile](../visualize/workbooks-tile-visualizations.md)
* [Trees](../visualize/workbooks-tree-visualizations.md)
* [그래프](../visualize/workbooks-graph-visualizations.md)
* [복합 막대](../visualize/workbooks-composite-bar.md)
* [벌집](workbooks-honey-comb.md)
* [Map](workbooks-map-visualizations.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="통합 문서 시각화의 예제" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>시각화 고정

통합 문서에 있는 텍스트, 쿼리, 메트릭 단계는 통합 문서가 고정 모드에 있는 동안 또는 통합 문서 작성자가 해당 요소의 고정 아이콘을 표시하는 설정을 사용하도록 설정한 경우 해당 항목의 고정 단추를 사용하여 고정할 수 있습니다.

고정 모드에 액세스하려면 **편집** 을 선택하여 편집 모드로 전환하고 위쪽 막대에서 파란색 고정 아이콘을 선택합니다. 그러면 개별 고정 아이콘이 화면 오른쪽의 각 해당 통합 문서 파트의 *편집* 상자 위에 표시됩니다.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="PIN 환경의 스크린샷" border="false":::

> [!NOTE]
> 통합 문서 상태는 고정 시점에 저장되며, 기본 통합 문서가 수정되는 경우 대시보드의 고정된 통합 문서는 업데이트되지 않습니다. 고정된 통합 문서 파트를 업데이트하려면 해당 파트를 삭제하고 다시 고정해야 합니다.

## <a name="getting-started"></a>시작

통합 문서 환경을 탐색하려면 먼저 Azure Monitor 서비스로 이동합니다. Azure Portal의 검색 상자에 **Monitor** 를 입력하여 이동할 수 있습니다.

그런 다음 **통합 문서** 를 선택합니다.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="빨간색 상자에 강조 표시된 통합 문서 단추의 스크린샷" border="false":::

### <a name="gallery"></a>갤러리

갤러리를 사용하면 모든 형식의 통합 문서를 편리하게 구성, 정렬 및 관리할 수 있습니다.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="모든 탭의 갤러리 스크린샷" lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>갤러리 탭

갤러리에는 통합 문서 유형을 구성하는 데 도움이 되는 4개의 탭이 있습니다.

| 탭              | Description                                       |
|------------------|---------------------------------------------------|
| 모두 | 각 형식(통합 문서, 공용 템플릿 및 내 템플릿)에 대한 상위 4개 항목을 표시합니다. 통합 문서는 수정된 날짜를 기준으로 정렬되므로 가장 최근에 수정한 8개의 통합 문서가 표시됩니다.|
| 통합 문서 | 사용자가 만들었거나 사용자에게 공유된 모든 사용 가능한 통합 문서 목록을 표시합니다. |
| 퍼블릭 템플릿 | 사용할 수 있는 모든 목록을 표시하고, Microsoft에서 게시한 작동하는 통합 문서 템플릿을 시작합니다. 범주별로 그룹화됩니다. |
| 내 템플릿 | 사용자가 만들었거나 사용자에게 공유된 모든 사용 가능한 배포된 통합 문서 템플릿을 표시합니다. 범주별로 그룹화됩니다. |

#### <a name="features"></a>기능

* 각 탭에는 통합 문서에 대한 정보가 포함된 그리드가 있습니다. 여기에는 설명, 마지막으로 수정한 날짜, 태그, 구독, 리소스 그룹, 지역 및 공유 상태가 포함됩니다. 이 정보를 기준으로 통합 문서를 정렬할 수도 있습니다.
* 리소스 그룹, 구독, 통합 문서/템플릿 이름 또는 템플릿 범주를 기준으로 필터링합니다.
* 삭제 또는 대량 삭제할 통합 문서를 여러 개 선택합니다.
* 각 통합 문서에는 바로 가기 메뉴(마지막의 말줄임표/세 점)가 있으며, 선택하면 빠른 작업 목록이 열립니다.
    * 리소스 보기 - 통합 문서 리소스 탭에 액세스하여 통합 문서의 리소스 ID를 보고, 태그를 추가하고, 잠금을 관리하는 등의 작업을 수행할 수 있습니다.
    * 통합 문서를 삭제하거나 이름을 바꿉니다.
    * 통합 문서를 대시보드에 고정합니다.

### <a name="workbooks-versus-workbook-templates"></a>통합 문서와 통합 문서 템플릿 비교

_통합 문서_ 는 녹색으로 표시되고 여러 _통합 문서 템플릿_ 은 보라색으로 표시됩니다. 템플릿은 여러 사용자 및 팀에서 유연하게 재사용할 수 있도록 디자인된 큐레이팅된 보고서 역할을 합니다. 템플릿을 열면 템플릿의 콘텐츠로 채워진 임시 통합 문서가 만들어집니다.

동료들의 향후 보고 환경을 손상시킬 걱정 없이 템플릿 기반 통합 문서의 매개 변수를 조정하고 분석을 수행할 수 있습니다. 템플릿을 열고 몇 가지를 조정한 다음 저장 아이콘을 선택하면 해당 템플릿이 통합 문서로 저장되고 녹색으로 표시되며, 원래 템플릿은 그대로 유지됩니다.

내부적으로 템플릿은 저장된 통합 문서와도 다릅니다. 통합 문서를 저장하면 연결된 Azure Resource Manager 리소스가 생성되는 반면 템플릿을 열 때 생성된 임시 통합 문서에는 연결된 고유 리소스가 없습니다. 통합 문서에서 액세스 제어를 관리하는 방법에 대한 자세한 내용은 [통합 문서 액세스 제어 문서](../visualize/workbooks-access-control.md)를 참조하세요.

### <a name="exploring-a-workbook-template"></a>통합 문서 템플릿 탐색

**애플리케이션 오류 분석** 을 선택하여 기본 애플리케이션 통합 문서 템플릿 중 하나를 확인합니다.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="애플리케이션 오류 분석 템플릿의 스크린샷" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

앞에서 설명한 것처럼 템플릿을 열면 상호 작용할 수 있는 임시 통합 문서가 만들어집니다. 기본적으로 통합 문서는 원래 템플릿 작성자가 만든 의도된 분석 환경의 정보만 표시하는 읽기 모드로 열립니다.

이 특정 통합 문서의 경우에는 환경이 대화형입니다. 구독, 대상 앱, 표시할 데이터의 시간 범위를 조정할 수 있습니다. 이러한 선택을 하면 HTTP 요청 그리드도 대화형이 되어 개별 행을 선택하면 보고서 아래쪽의 두 차트에서 렌더링되는 데이터가 변경됩니다.

### <a name="editing-mode"></a>편집 모드

이 통합 문서 템플릿이 어떻게 합쳐지는지 이해하려면 **편집** 을 선택하여 편집 모드로 전환해야 합니다.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="통합 문서의 편집 단추 스크린샷" border="false" :::

편집 모드로 전환하면 통합 문서의 각 개별 요소에 해당하는 여러 개의 **편집** 상자가 오른쪽에 표시됩니다.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="편집 단추의 스크린샷" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

요청 데이터의 그리드 바로 아래에 있는 편집 단추를 선택하면 통합 문서의 이 파트가 Application Insights 리소스의 데이터에 대한 Kusto 쿼리로 구성된 것을 볼 수 있습니다.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="기본 Kusto 쿼리의 스크린샷" border="false" lightbox="./media/workbooks-overview/kusto.png":::

오른쪽의 다른 **편집** 단추를 선택하면 markdown 기반 [텍스트 상자](../visualize/workbooks-text-visualizations.md), [매개 변수 선택](../visualize/workbooks-parameters.md) UI 요소 및 기타 [차트/시각화 형식](#visualizations) 등 통합 문서를 구성하는 여러 핵심 구성 요소가 표시됩니다.

편집 모드에서 미리 빌드된 템플릿을 탐색한 다음 요구 사항에 맞게 수정하고 사용자 지정 통합 문서를 저장하는 것이 Azure Monitor 통합 문서로 무엇이 가능한지 알아보는 좋은 방법입니다.

## <a name="dashboard-time-ranges"></a>대시보드 시간 범위

고정된 통합 문서 쿼리 파트는 고정된 항목이 *시간 범위* 매개 변수를 사용하도록 구성된 경우 대시보드의 시간 범위를 사용합니다. 대시보드의 시간 범위 값은 시간 범위 매개 변수의 값으로 사용되고, 대시보드 시간 범위를 변경하면 고정된 항목이 업데이트됩니다. 고정된 파트가 대시보드의 시간 범위를 사용하는 경우 시간 범위가 변경될 때마다 고정된 파트의 부제목이 업데이트되어 대시보드의 시간 범위를 표시하는 것을 볼 수 있습니다.

또한 시간 범위 매개 변수를 사용하여 고정된 통합 문서 파트는 대시보드의 시간 범위에 의해 결정되는 속도로 자동으로 새로 고침됩니다. 쿼리가 마지막으로 실행된 시간이 고정된 파트의 부제목에 표시됩니다.

고정된 단계에 명시적으로 설정된 시간 범위가 있고 시간 범위 매개 변수를 사용하지 않는 경우 대시보드의 설정에 관계없이 대시보드에 항상 해당 시간 범위가 사용됩니다. 고정된 파트의 부제목에는 대시보드의 시간 범위가 표시되지 않으며, 쿼리는 대시보드에서 자동으로 새로 고침되지 않습니다. 부제목은 쿼리가 마지막으로 실행된 시간을 표시합니다.

> [!NOTE]
> *병합* 데이터 원본을 사용하는 쿼리는 현재 대시보드에 고정할 때 지원되지 않습니다.

## <a name="sharing-workbook-templates"></a>통합 문서 템플릿 공유

자체 통합 문서 템플릿을 만든 후 더 광범위한 커뮤니티와 공유할 수 있습니다. 자세히 알아보고 기본 Azure Monitor 갤러리 보기에 포함되지 않은 다른 템플릿을 탐색하려면 [GitHub 리포지토리](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)를 방문하세요. 기존 통합 문서를 찾아보려면 GitHub의 [통합 문서 라이브러리](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks)를 방문하세요.


## <a name="next-step"></a>다음 단계

* 통합 문서의 여러 풍부한 시각화 옵션에 대해 알아보기 [시작](#visualizations)합니다.
* 통합 문서 리소스에 대한 액세스를 [제어](../visualize/workbooks-access-control.md) 및 공유합니다.