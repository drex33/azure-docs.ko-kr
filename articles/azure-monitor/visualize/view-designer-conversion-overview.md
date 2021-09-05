---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 전환 가이드
description: Azure Monitor에서 보기에서 통합 문서로 전환
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: b86e90298bc753cdea5c0fb5f255e88fcc26dc5c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440759"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor 뷰 디자이너에서 통합 문서로 전환 가이드
[뷰 디자이너](view-designer.md)는 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 사용자 지정 뷰를 만들 수 있게 하는 Azure Monitor 기능입니다. Azure Portal 내에서 데이터 분석 및 풍부한 시각적 보고서 생성을 위한 유연한 캔버스를 제공하기 위해 통합 문서로 전환되었습니다. 이 문서는 뷰 디자이너에서 통합 문서로 전환하는 데 도움이 됩니다. 


## <a name="workbooks-overview"></a>통합 문서 개요
[통합 문서](../vm/vminsights-workbooks.md)는 텍스트,  [로그 쿼리](/azure/data-explorer/kusto/query/), 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다. Azure 리소스에 대한 동일한 액세스 권한이 있는 팀 구성원도 통합 문서를 편집할 수 있습니다.

통합 문서는 다음과 같은 시나리오에 유용합니다.

-   관심 있는 메트릭(CPU 사용률, 디스크 공간, 메모리, 네트워크 종속성 등)을 미리 알지 못하는 경우 가상 머신의 사용 현황을 살펴보세요. 다른 사용량 현황 분석 도구와는 달리 통합 문서를 사용하면 이러한 종류의 자유 형식 탐색에 매우 유용하도록 여러 종류의 시각화 및 분석을 결합할 수 있습니다.
-   주요 카운터 및 기타 로그 이벤트에 대한 메트릭을 표시하여 최근에 프로비전된 VM이 수행되는 방식을 팀에 설명합니다.
-   VM의 크기 조정 실험 결과를 다른 멤버와 공유합니다. 텍스트로 실험에 대한 목표를 설명한 다음, 각 메트릭이 위 또는 아래 대상이었는지 여부에 대한 분명한 설명선과 함께 실험을 평가하는 데 사용되는 각 사용 현황 메트릭 및 분석 쿼리를 보여줄 수 있습니다.
-   데이터, 텍스트 설명 및 차후의 중단을 방지하기 위한 다음 단계의 논의를 결합하여 VM의 사용 현황에 대한 중단의 영향을 보고합니다.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>뷰 디자이너 대시보드를 통합 문서로 변환해야 하는 이유

뷰 디자이너는 다른 쿼리 기반 보기 및 시각화를 생성하는 기능을 제공합니다. 그러나 그리드 및 타일 레이아웃 서식 지정 또는 데이터를 나타내는 대체 그래픽 선택과 같은 많은 고급 사용자 지정은 제한되어 있습니다. 뷰 디자이너는 데이터를 나타내는 총 9개의 개별 타일로 제한됩니다.

통합 문서는 데이터의 잠재력을 최대한 활용하는 플랫폼입니다. 통합 문서는 모든 기능을 유지할 뿐만 아니라 텍스트, 메트릭, 매개 변수 등을 통해 추가 기능을 지원합니다. 예를 들어 통합 문서를 통해 사용자는 조밀한 그리드를 통합하고 검색 창을 추가하여 데이터를 쉽게 필터링하고 분석할 수 있습니다. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>뷰 디자이너보다 통합 문서를 사용할 때의 이점

* 로그와 메트릭을 모두 지원합니다.
* 개별 액세스 제어 및 공유 통합 문서 보기에 대한 개인 보기를 모두 허용합니다.
* 탭, 크기 조정 및 크기 조정 컨트롤을 사용하여 레이아웃 옵션을 사용자 지정합니다.
* 여러 Log Analytics 작업 영역, Application Insights 애플리케이션 및 구독에 대한 쿼리를 지원합니다.
* 연결된 차트 및 시각화를 동적으로 업데이트하는 사용자 지정 매개 변수를 사용하도록 설정합니다.
* 퍼블릭 GitHub의 템플릿 갤러리 지원.

이 가이드에서는 일반적으로 사용되는 여러 뷰 디자이너 뷰를 직접 다시 만드는 간단한 단계를 제공하지만 통합 문서를 통해 사용자는 자신의 사용자 지정 시각화 및 메트릭을 자유롭게 만들고 디자인할 수 있습니다. 다음 스크린샷은 [작업 영역 사용 템플릿](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)에서 가져온 것이며, 통합 문서를 만들 수 있는 예제를 보여줍니다.


![통합 문서 애플리케이션의 예](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>통합 문서 사용을 시작하는 방법
통합 문서에서 열린 통합 문서는 뷰 디자이너 위치 바로 아래에 있는 측면 탐색 모음의 항목으로 Log Analytics 작업 영역에서 사용됩니다.

![통합 문서 탐색](media/view-designer-conversion-overview/workbooks-nav.png)

선택하면 작업 영역에 대해 저장된 모든 통합 문서와 템플릿이 나열된 갤러리가 표시됩니다.

![통합 문서 갤러리](media/view-designer-conversion-overview/workbooks-gallery.png)

새 통합 문서를 시작하려면 **빠른 시작** 에서 **빈** 템플릿을 선택하거나 위쪽 탐색 모음에서 **새로 만들기** 아이콘을 선택합니다. 템플릿을 보거나 저장된 통합 문서로 돌아가려면 갤러리에서 항목을 선택하거나 검색 창에서 이름을 검색합니다.

통합 문서를 저장하려면 특정 제목, 구독, 리소스 그룹 및 위치로 보고서를 저장해야 합니다.
통합 문서는 같은 구독, 리소스 그룹을 사용하여 LA 작업 영역과 동일한 설정으로 자동으로 채워지지만 사용자는 이러한 보고서 설정을 변경할 수 있습니다. 통합 문서는 저장할 부모 리소스 그룹에 대한 쓰기 권한이 필요한 공유 리소스입니다.

## <a name="next-steps"></a>다음 단계

- [변환 옵션](view-designer-conversion-options.md)