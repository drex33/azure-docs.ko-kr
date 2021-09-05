---
title: Azure Monitor의 Log Analytics 개요
description: Azure Portal에서 Azure Monitor 로그의 데이터를 분석하기 위해 로그 쿼리를 편집하고 실행하는 데 사용되는 도구인 Log Analytics에 대해 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: bcdfb35fc8bf854746b4bde0ea3af5f8cadc7989
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217794"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Azure Monitor의 Log Analytics 개요
Log Analytics는 Azure Portal에서 Azure Monitor 로그의 데이터로 로그 쿼리를 편집하고 실행하는 데 사용되는 도구입니다. 레코드 집합을 반환하는 간단한 쿼리를 작성한 다음 Log Analytics 기능을 사용하여 정렬, 필터링, 분석할 수 있습니다. 또는 통계 분석을 수행하고 차트에서 결과를 시각화하여 특정 트렌드를 파악하는 고급 쿼리를 작성할 수 있습니다. 쿼리 결과를 대화형으로 처리하든, 쿼리 경고 또는 통합 문서 로그와 같은 다른 Azure Monitor 기능과 함께 사용하든, 쿼리 작성 및 테스트에 Log Analytics가 사용됩니다. 


> [!TIP]
> 이 문서에서는 Log Analytics와 각 기능에 대한 설명을 제공합니다. 자습서로 바로 이동하려면 [Log Analytics 자습서](./log-analytics-tutorial.md)를 참조하세요.



## <a name="starting-log-analytics"></a>Log Analytics 시작
Azure Portal의 **Azure Monitor** 메뉴에 있는 **로그** 에서 Log Analytics를 시작합니다. 대부분의 Azure 리소스에 대한 메뉴에서 이 옵션을 찾을 수도 있습니다. 어디서 시작하든 Log Analytics 도구는 동일합니다. 하지만 Log Analytics를 시작하는 데 사용하는 메뉴에 따라 사용 가능한 데이터가 결정됩니다. **Azure Monitor** 메뉴 또는 **Log Analytics 작업 영역** 메뉴에서 시작하는 경우 작업 영역에 있는 모든 레코드에 액세스할 수 있습니다. 다른 유형의 리소스에서 **로그** 를 선택할 경우 해당 리소스에 대한 로그 데이터로 데이터가 제한됩니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](./scope.md)를 참조하세요.

[![Log Analytics 시작](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Log Analytics를 시작할 때 가장 먼저 표시되는 것은 [예제 쿼리](../logs/queries.md)를 포함하는 대화 상자입니다. 예제 쿼리는 솔루션별로 분류되며, 특정 요구 사항과 일치하는 쿼리를 찾아보거나 검색할 수 있습니다. 원하는 작업을 정확하게 수행하는 쿼리를 찾거나, 편집기에 로드하여 필요에 따라 수정할 수 있습니다. 예제 쿼리를 검색하면 고유한 쿼리를 작성하는 방법을 쉽게 배울 수 있습니다. 

물론 빈 스크립트로 시작한 후 쿼리를 직접 작성하려면 예제 쿼리를 닫을 수 있습니다. 쿼리를 다시 가져오려면 화면 위쪽에서 **쿼리** 를 클릭하기만 하면 됩니다.

## <a name="log-analytics-interface"></a>Log Analytics 인터페이스
다음 이미지는 Log Analytics의 여러 구성 요소를 식별합니다.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. 위쪽 작업 모음
쿼리 창에서 쿼리 작업을 하기 위한 컨트롤

| 옵션 | 설명 |
|:---|:---|
| 범위 | 쿼리에 사용되는 데이터의 범위를 지정합니다. Log Analytics 작업 영역에 있는 모든 데이터 또는 여러 작업 영역에 있는 특정 리소스에 대한 데이터일 수 있습니다. [쿼리 범위](./scope.md)를 참조하세요. |
| 실행 단추 | 쿼리 창에서 선택한 쿼리를 실행하려면 클릭합니다. Shift + Enter를 눌러 쿼리를 실행할 수도 있습니다. |
| 시간 선택기 | 쿼리에 사용할 수 있는 데이터의 시간 범위를 선택합니다. 쿼리에 시간 필터를 포함하는 경우 이는 무시됩니다. [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](./scope.md)를 참조하세요. |
| 저장 단추 | 작업 영역에 대한 쿼리 탐색기에 쿼리를 저장합니다. |
 복사 단추 | 쿼리, 쿼리 텍스트 또는 쿼리 결과에 대한 링크를 클립보드에 복사합니다. |
| 새 경고 규칙 단추 | 빈 쿼리를 사용하여 새 탭을 만듭니다. |
| 내보내기 단추 | 쿼리 결과를 CSV 파일 또는 쿼리로 내보내 Power BI에 사용할 수 있도록 파워 쿼리 수식 언어 형식으로 내보냅니다. |
| 대시보드에 고정 창 단추 | Azure 대시보드에 쿼리 결과를 추가합니다. |
| 쿼리 서식 지정 단추 | 가독성을 위해 선택한 텍스트를 정렬합니다. |
| 예제 쿼리 단추 | Log Analytics를 처음 열 때 표시되는 예제 쿼리 대화 상자를 엽니다. |
| 쿼리 탐색기 단추 | 작업 영역에서 저장된 쿼리에 대한 액세스를 제공하는 **쿼리 탐색기** 를 엽니다. |


### <a name="2-sidebar"></a>2. 사이드바
현재 쿼리에 대한 작업 영역, 샘플 쿼리 및 필터 옵션에 나오는 테이블 목록입니다.

| 탭 | Description |
|:---|:---|
| 테이블 | 선택한 범위에 속하는 테이블을 나열합니다. **그룹화 기준** 을 선택하여 테이블 그룹화를 변경합니다. 테이블 이름 위로 마우스를 이동하면 테이블에 대한 설명이 포함된 대화 상자가 나타나 설명서를 확인하고 데이터를 미리 볼 수 있습니다. 테이블을 펼쳐 해당 열을 확인합니다. 테이블 또는 열 이름을 더블클릭하면 쿼리에 추가됩니다. |
| 쿼리 | 쿼리 창에서 열 수 있는 예제 쿼리 목록입니다. Log Analytics를 열 때 표시되는 것과 동일한 목록입니다. **그룹화 기준** 을 선택하여 쿼리 그룹화를 변경합니다. 쿼리를 더블클릭하여 쿼리 창에 추가하거나 마우스를 쿼리 위로 이동하여 다른 옵션을 사용합니다. |
| Assert | 쿼리 결과에 따라 필터 옵션을 만듭니다. 쿼리를 실행하면 결과와 다른 값이 포함된 열이 표시됩니다. 하나 이상의 값을 선택하고 **적용 및 실행** 을 클릭하여 쿼리에 **where** 명령을 추가하고 다시 실행합니다. |

### <a name="3-query-window"></a>3. 쿼리 창
쿼리 창에서 쿼리를 편집합니다. 여기에는 가독성을 높이기 위한 KQL 명령 및 색 구분용 IntelliSense가 포함됩니다. 다른 탭을 열려면 창 맨 위에 있는 **+** 를 클릭합니다.

하나의 창에 여러 쿼리가 포함될 수 있습니다. 쿼리에는 빈 줄이 포함될 수 없으므로 창의 여러 쿼리를 하나 이상의 빈 줄로 구분할 수 있습니다. 현재 쿼리는 커서가 위치해 있는 쿼리입니다.

현재 쿼리를 실행하려면 **실행** 단추를 클릭하거나 Shift+Enter를 누릅니다.

### <a name="4-results-window"></a>4. 결과 창
쿼리 결과가 결과 창에 표시됩니다. 기본적으로 결과는 테이블로 표시됩니다. 차트로 표시하려면 결과 창에서 **차트** 를 선택하거나 쿼리에 **render** 명령을 추가합니다.

#### <a name="results-view"></a>Results view(결과 보기)
쿼리 결과가 열과 행으로 구성된 테이블로 표시됩니다. 행의 왼쪽을 클릭하여 값을 펼칩니다. **열** 드롭다운을 클릭하여 열 목록을 변경합니다. 열 이름을 클릭하여 결과를 정렬합니다. 열 이름 옆의 깔때기 아이콘을 클릭하여 결과를 필터링합니다. 쿼리를 다시 실행하여 필터를 삭제하고 정렬을 초기화합니다.

**그룹 열** 을 선택하면 쿼리 결과 위에 그룹화 표시줄이 나타납니다. 결과를 표시줄로 끌어와 열의 결과를 그룹화합니다. 열을 더 추가하여 결과에 중첩된 그룹을 만듭니다. 

#### <a name="chart-view"></a>차트 보기
결과가 사용 가능한 여러 차트 종류 중 하나로 표시됩니다. 쿼리의 **render** 명령에서 차트 형식을 지정하거나 **시각화 유형** 드롭다운에서 차트 형식을 선택할 수 있습니다.

| 옵션 | Description |
|:---|:---|
| **시각화 형식** | 표시할 차트의 형식입니다. |
| **X축** | X축에 사용할 결과의 열 
| **Y축** | Y축에 사용할 결과의 열입니다. 일반적으로 숫자 열입니다. |
| **분할 기준** | 차트에서 계열을 정의하는 결과의 열입니다. 열의 각 값에 대해 계열이 만들어집니다. |
| **집계** | Y축의 숫자 값에 대해 수행할 집계 형식입니다. |

## <a name="relationship-to-azure-data-explorer"></a>Azure Data Explorer와의 관계
Azure Data Explorer Web UI에 이미 익숙하다면 Log Analytics가 친숙해 보일 것입니다. Log Analytics는 Azure Data Explorer를 기반으로 하여 구축되며, 동일한 KQL(Kusto 쿼리 언어)을 사용하기 때문입니다. Log Analytics는 시간 범위별 필터링 및 쿼리에서 경고 규칙을 만드는 기능과 같은 Azure Monitor의 기능을 추가합니다. 두 도구 모두 사용 가능한 테이블의 구조를 통해 검색할 수 있는 탐색기가 포함되지만, Azure Data Explorer Web UI는 주로 Azure Data Explorer 데이터베이스의 테이블에서 작동하고, Log Analytics는 Log Analytics 작업 영역의 테이블에서 작동합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 Log Analytics를 사용하는 방법에 관한 자습서](./log-analytics-tutorial.md)를 참조하세요.
- [쿼리 작성에 관한 자습서](./get-started-queries.md)를 참조하세요.
