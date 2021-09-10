---
title: Log Analytics 자습서
description: 이 자습서에서는 Azure Monitor의 Log Analytics 기능을 사용하여 로그 쿼리를 작성 및 실행하고 Azure Portal에서 결과를 분석하는 방법을 알아봅니다.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729341"
---
# <a name="log-analytics-tutorial"></a>Log Analytics 자습서
Log Analytics는 Azure Monitor Logs에서 수집된 데이터에서 로그 쿼리를 편집 및 실행하고 그 결과를 대화형으로 분석하는 Azure Portal의 도구입니다. Log Analytics 쿼리를 사용하여 특정 조건과 일치하는 레코드를 검색하고, 추세를 식별하고, 패턴을 분석하고, 데이터에 대한 다양한 인사이트를 제공할 수 있습니다. 

이 자습서에서는 Log Analytics 인터페이스를 안내하고 몇 가지 기본 쿼리를 시작하며 결과를 사용하는 방법을 보여줍니다. 다음과 같은 내용을 알아봅니다.

> [!div class="checklist"]
> * 로그 데이터 스키마 이해
> * 간단한 쿼리 작성 및 실행, 쿼리 시간 범위 수정
> * 쿼리 결과 필터링, 정렬 및 그룹화
> * 쿼리 결과의 시각적 개체 보기, 수정 및 공유
> * 쿼리 및 결과 로드, 내보내기 및 복사

> [!IMPORTANT]
> 이 자습서에서는 Log Analytics 기능을 사용하여 쿼리를 하나 작성하고 또 다른 예제 쿼리를 사용합니다. 쿼리 구문을 알아보고 쿼리 자체를 직접 편집할 준비가 되면 [Kusto 쿼리 언어 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)를 읽어 보세요. 이 자습서에서는 Log Analytics에서 편집하고 실행할 수 있는 몇 가지 예제 쿼리를 살펴봅니다. 이 자습서에서 배우게 될 여러 가지 기능이 사용됩니다.


## <a name="prerequisites"></a>필수 구성 요소
이 자습서에서는 샘플 쿼리를 지원하는 샘플 데이터가 많이 포함된 [Log Analytics 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 사용합니다. 자체 Azure 구독을 사용할 수도 있지만, 아마도 같은 테이블 안에 데이터가 없을 것입니다.

## <a name="open-log-analytics"></a>Log Analytics 열기
[Log Analytics 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 열거나 구독의 Azure Monitor 메뉴에서 **로그** 를 선택합니다. 이 단계에서는 쿼리가 Log Analytics 작업 영역의 모든 데이터에서 선택하도록 초기 범위를 이 작업 영역으로 설정합니다. Azure 리소스의 메뉴에서 **로그** 를 선택하면 범위가 해당 리소스의 레코드로만 설정됩니다. 범위에 대한 자세한 내용은 [로그 쿼리 범위](./scope.md)를 참조하세요.

범위는 화면의 왼쪽 위 모서리에서 볼 수 있습니다. 자체 환경을 사용하는 경우에는 다른 범위를 선택하는 옵션이 표시됩니다. 데모 환경에서는 이 옵션을 사용할 수 없습니다.

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="데모의 Log Analytics 범위를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="view-table-information"></a>테이블 정보 보기
화면 왼쪽에는 현재 범위에서 사용할 수 있는 테이블을 검사할 수 있는 **테이블** 탭이 있습니다. 이러한 테이블은 기본적으로 **솔루션** 별로 그룹화되지만 그룹화를 변경하거나 필터링할 수 있습니다. 

**로그 관리** 솔루션을 확장하여 **AppRequests** 테이블을 찾습니다. 테이블을 펼쳐서 해당 스키마를 보거나, 이름을 마우스로 가리켜서 추가 정보를 표시할 수 있습니다. 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="테이블 보기를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/table-details.png":::

**유용한 링크** 아래의 링크를 선택하면 각 테이블과 해당 열을 문서화하는 테이블 참조로 이동합니다. **데이터 미리 보기** 를 선택하면 테이블에서 최근 레코드 몇 개를 빠르게 살펴볼 수 있습니다. 이 미리 보기는 쿼리를 실행하기 전에 예상했던 데이터가 맞는지 확인하는 데 유용할 수 있습니다.

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="샘플 데이터를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>쿼리 작성
**AppRequests** 테이블을 사용하여 쿼리를 작성해 보겠습니다. 이름을 두 번 클릭하여 쿼리 창에 추가합니다. 창에 직접 값을 입력할 수도 있습니다. 현재 범위 및 KQL(Kusto 워리 언어)명령에서 테이블 이름을 완성하는 데 유용한 IntelliSense를 받을 수도 있습니다.

작성할 수 있는 가장 간단한 쿼리입니다. 테이블의 모든 레코드를 반환합니다. **실행** 단추를 선택하거나 쿼리 텍스트의 아무 곳에 커서를 두고 Shift + Enter 키를 선택하여 실행합니다.

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="쿼리 결과를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results.png":::

결과가 있는 것이 보입니다. 쿼리에서 반환된 레코드 수는 오른쪽 아래 모서리에 표시됩니다. 

## <a name="filter-query-results"></a>쿼리 결과 필터링

쿼리에 필터를 추가하여 반환되는 레코드 수를 줄이겠습니다. 왼쪽 창에서 **필터** 탭을 선택합니다. 이 탭에는 결과를 필터링하는 데 사용할 수 있는 쿼리 결과의 여러 열이 표시됩니다. 이러한 열의 상위 값은 해당 값이 있는 레코드 수와 함께 표시됩니다. **ResultCode** 에서 **200** 을 선택한 다음, **적용 및 실행** 을 선택합니다. 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="쿼리 창을 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-pane.png":::

**where** 문이 선택한 값과 함께 쿼리에 추가됩니다. 이제 해당 값이 있는 레코드만 결과에 포함되기 때문에 레코드 수가 줄어드는 것을 볼 수 있습니다.

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="필터링된 쿼리 결과를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>시간 범위
Log Analytics 작업 영역의 모든 테이블에는 **TimeGenerated** 라는 열이 있으며, 이것은 레코드가 생성된 시간입니다. 모든 쿼리에는 결과를 해당 범위 내의 **TimeGenerated** 값이 있는 레코드로 제한하는 시간 범위가 있습니다. 시간 범위는 쿼리에서 설정하거나 화면 맨 위에 있는 선택기를 사용하여 설정할 수 있습니다.

기본적으로 쿼리는 지난 24시간의 레코드를 반환합니다. 아직 일부 결과만 볼 수 있다는 메시지가 표시됩니다. Log Analytics는 최대 30,000개의 레코드를 반환할 수 있습니다. 이는 쿼리에서 그보다 많은 레코드를 반환했기 때문입니다. **시간 범위** 드롭다운 목록을 선택하고 값을 **12시간** 으로 변경합니다. **실행** 을 다시 선택하여 결과를 반환합니다. 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="시간 범위를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>여러 쿼리 조건
다른 필터 조건을 추가하여 결과를 더 줄여 보겠습니다. 쿼리에는 원하는 만큼 필터를 포함하여 원하는 레코드 집합을 정확하게 목표할 수 있습니다. **이름** 아래에서 **홈/인덱스 가져오기** 를 선택한 다음, **적용 및 실행** 을 선택합니다. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="여러 필터가 있는 쿼리 결과를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>결과 분석
Log Analytics는 쿼리 작성 및 실행을 돕는 것 외에도 결과로 작업할 수 있는 기능을 제공합니다. 먼저 레코드를 펼쳐서 모든 열의 값을 볼 수 있습니다.

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="레코드 확장을 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/expand-record.png":::

열 이름을 선택하여 해당 열을 기준으로 결과를 정렬합니다. 옆에 있는 필터 아이콘을 선택하여 필터 조건을 제공합니다. 쿼리를 다시 실행하면 필터가 지워진다는 점을 제외하면, 쿼리 자체에 필터 조건을 추가하는 것과 유사합니다. 대화형 분석의 일환으로 레코드 세트를 빠르게 분석하려는 경우 이 방법을 사용합니다.

예를 들어 **DurationMs** 열에 필터를 설정하여 **100** 밀리초가 넘게 걸리는 레코드로 제한합니다. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="쿼리 결과 필터를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-filter.png":::

결과를 필터링하는 대신 특정 열을 기준으로 레코드를 그룹화할 수 있습니다. 방금 만든 필터를 지운 다음, **열 그룹화** 토글을 켭니다. 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="열 그룹화를 켜는 것을 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

**Url** 열을 그룹화 행으로 끕니다. 이 열을 기준으로 결과가 구성되며, 각 그룹을 접으면 분석하는 데 유용할 수 있습니다.

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="그룹화된 쿼리 결과를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>차트 작업
차트에서 볼 수 있는 숫자 데이터를 사용하는 쿼리를 살펴보겠습니다. 쿼리를 작성하는 대신 예제 쿼리를 선택합니다.

왼쪽 창에서 **쿼리** 를 선택합니다. 이 창에는 쿼리 창에 추가할 수 있는 예제 쿼리가 포함되어 있습니다. 자체 작업 영역을 사용하는 경우에는 여러 범주의 다양한 쿼리가 있을 수 있습니다. 데모 환경을 사용하는 경우에는 단일 **Log Analytics 작업 영역** 범주만 보일 것입니다. 이 범주를 펼치면 범주의 쿼리가 보입니다.

**애플리케이션** 범주에서 **함수 오류율** 이라는 쿼리를 선택합니다. 이 단계에서는 쿼리 창에 쿼리를 추가합니다. 새 쿼리는 빈 줄로 다른 쿼리와 분리됩니다. KQL의 쿼리는 빈 줄이 있으면 종료되므로 별도의 쿼리로 간주됩니다. 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="새 쿼리를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/example-query.png":::

현재 쿼리는 커서가 있는 쿼리입니다. 첫 번째 쿼리가 현재 쿼리라고 강조 표시된 것을 볼 수 있습니다. 새 쿼리의 아무 곳을 클릭하여 선택한 다음, **실행** 단추를 선택하여 실행합니다.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="쿼리 결과 표를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

그래프로 결과를 보려면 결과 창에서 **차트** 를 선택합니다.  차트를 작업하는 다양한 옵션(예: 차트를 다른 형식으로 변경)이 있습니다.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="쿼리 결과 차트를 보여주는 스크린샷" lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>다음 단계

Log Analytics를 사용하는 방법을 파악했으면 이제 로그 쿼리를 사용하는 방법에 대한 자습서를 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Monitor 로그 쿼리 작성](get-started-queries.md)
