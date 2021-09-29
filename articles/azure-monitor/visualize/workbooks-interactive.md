---
title: 사용자 지정 매개 변수를 적용한 Azure Monitor 통합 문서
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: fdf7b67a15cf8bd40dadbb709a2829408fa42a57
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230558"
---
# <a name="interactive-workbooks"></a>대화형 Workbooks

통합 문서를 사용하면 작성자가 소비자를 위한 대화형 보고서 및 환경을 만들 수 있습니다. 대화형 작업은 여러 가지 방법으로 지원됩니다.

## <a name="parameter-changes"></a>매개 변수 변경

통합 문서 사용자가 매개 변수를 업데이트하면 매개 변수가 사용하는 모든 컨트롤이 새 상태를 반영하기 위해 자동으로 새로 고쳐지고 다시 그려집니다. Azure Portal 보고서의 대부분은 이런 식으로 대화형 작업을 지원합니다. 통합 문서에서는 사용자 작업을 최소화한 직관적인 방식으로 이를 제공합니다.

[통합 문서의 매개 변수](workbooks-parameters.md)에 대한 자세한 정보

## <a name="grid-tile-chart-selections"></a>그리드, 타일, 차트 선택

통합 문서를 통해 작성자는 그리드의 행을 클릭하면 행의 콘텐츠에 따라 후속 차트가 업데이트되는 시나리오를 구성할 수 있습니다.

예를 들어, 사용자는 요청 목록과, 실패 횟수와 같은 일부 통계를 표시하는 그리드를 만들 수 있습니다. 요청에 해당하는 행을 클릭하면 아래의 상세 차트가 업데이트되어 해당 요청에 대해서만 필터링하도록 설정할 수 있습니다.

### <a name="setting-up-interactivity-on-grid-row-click"></a>그리드 행 클릭 시 대화형 작업 설정

1. _편집_ 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. _쿼리 추가_ 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. _로그_, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` - 결과 확인
6. 쿼리 바닥글에서 _고급 설정_ 아이콘(기어처럼 보이는 아이콘)을 선택합니다. 그러면 고급 설정 창이 열립니다.
7. `When an item is selected, export a parameter` 설정을 확인합니다.
8. 선택한 설정에서 *매개 변수 추가* 를 선택하고 아래 정보를 입력합니다.
    1. 내보낼 필드: `Request`
    2. 매개 변수 이름: `SelectedRequest`
    3. 기본값: `All requests`
9. 저장 선택

    ![필드를 매개 변수로 내보내기 위한 설정이 포함된 고급 편집기를 보여주는 스크린샷.](./media/workbooks-interactive/export-parameters-add.png)

10. `Done Editing`를 선택합니다.
11. 2 및 3단계를 사용하여 다른 쿼리 컨트롤을 추가합니다.
12. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` - 결과 확인
14. _시각화_ 를 `Area chart`로 변경합니다.
15. 첫 번째 그리드에서 선택할 행을 선택합니다. 아래 영역 차트에서 선택한 요청을 필터링하는 방법을 확인합니다.

결과 보고서는 편집 모드에서 다음과 같이 표시됩니다.

![편집 모드의 처음 두 쿼리 스크린샷.](./media/workbooks-interactive//interactivity-grid-create.png)

아래 이미지는 동일한 원칙에 따라 읽기 모드에서 더 정교한 대화형 보고서를 보여줍니다. 보고서는 그리드 클릭을 사용하여 매개 변수를 내보내고, 이것은 다시 두 차트와 텍스트 블록에 사용됩니다.

![읽기 모드에서 그리드 클릭을 사용하는 보고서 스크린샷.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>전체 행의 콘텐츠 내보내기

특정 열이 아니라 선택한 행의 전체 콘텐츠를 내보내는 것이 더 나을 때가 있습니다. 이러한 경우 위의 7.1단계에서 `Field to export` 속성을 설정하지 않은 상태로 둡니다. 통합 문서는 전체 행 콘텐츠를 json 형태로 매개 변수에 내보냅니다.

참조 KQL 컨트롤에서 `todynamic` 함수를 사용하여 json을 구문 분석하고 개별 열에 액세스합니다.

## <a name="grid-cell-clicks"></a>그리드 셀 클릭

통합 문서를 사용하면 작성자가 `link renderer`라는 특수 형식의 그리드 열 렌더러를 통해 대화형 작업을 추가할 수 있습니다. 링크 렌더러는 셀 콘텐츠에 따라 그리드 셀을 하이퍼링크로 변환합니다. 통합 문서는 리소스 개요 블레이드, 속성 모음 뷰어, App Insights 검색, 사용, 트랜잭션 추적 등을 포함하여 다양한 종류의 링크 렌더러를 지원합니다.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>그리드 셀 클릭을 사용한 대화형 작업 설정

1. _편집_ 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. _쿼리 추가_ 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. _로그_, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` - 결과 확인
6. _열 설정_ 을 선택하여 설정 창을 엽니다.
7. _열_ 섹션에서 다음을 설정합니다.
    1. _샘플_ - 열 렌더러: `Link`, 보기 열기: `Cell Details`, 링크 레이블: `Sample`
    2. _개수_ - 열 렌더러: `Bar`, 색상표: `Blue`, 최솟값: `0`
    3. _요청_ -열 렌더러: `Automatic`
    4. _저장 후 닫기_ 를 선택하여 변경 내용을 적용합니다.

    ![열 설정 탭의 스크린샷.](./media/workbooks-interactive/column-settings.png)

8. 그리드에서 `Sample` 링크 중 하나를 클릭합니다. 그러면 샘플링된 요청에 대한 세부 정보가 포함된 창이 열립니다.

    ![샘플링된 요청에 대한 세부 정보 창의 스크린샷.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>렌더러 동작 연결

| 동작 연결 | 클릭 시 동작 |
|:------------- |:-------------|
| `Generic Details` | 속성 그리드 컨텍스트 보기에서 행 값을 표시합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 탭에 셀 값을 표시합니다. 정보를 포함하는 동적 형식(예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함된 경우에 유용합니다. |
| `Cell Details` | 속성 그리드 컨텍스트 탭에 셀 값을 표시합니다. 정보를 포함하는 동적 형식(예: 위치, 역할 인스턴스 등의 요청 속성이 있는 json)이 셀에 포함된 경우에 유용합니다. |
| `Custom Event Details` | 셀에서 사용자 지정 이벤트 ID(`itemId`)를 사용하여 Application Insights 검색 세부 정보를 엽니다. |
| `* Details` | 종속성, 예외, 페이지 보기, 요청 및 추적을 제외하고 사용자 지정 이벤트 세부 정보와 유사합니다. |
| `Custom Event User Flows` | 셀의 사용자 지정 이벤트 이름에서 피벗 된 Application Insights 사용자 흐름 환경을 엽니다. |
| `* User Flows` | 예외, 페이지 보기 및 요청을 제외하고 사용자 지정 이벤트 사용자 흐름과 비슷합니다. |
| `User Timeline` | 셀에서 사용자 ID(user_Id)를 사용하여 사용자 타임라인을 엽니다. |
| `Session Timeline` | 셀 값에 대한 Application Insights 검색 환경을 엽니다. 예를 들어 여기서 abc가 셀의 값이라면 텍스트 'abc'를 검색합니다. |
| `Resource overview` | 셀의 리소스 ID 값을 기반으로 포털에서 리소스의 개요를 엽니다. |

## <a name="conditional-visibility"></a>조건부 표시 유형

사용자는 통합 문서를 사용하여 매개 변수의 값에 따라 특정 컨트롤을 표시하거나 사라지게 할 수 있습니다. 이렇게 하면 작성자가 사용자 입력 또는 원격 분석 상태에 따라 보고서를 다르게 볼 수 있습니다. 예를 들어, 문제가 없으면 소비자에게 요약만 표시하고 문제가 있으면 전체 세부 정보를 표시할 수 있습니다.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>조건부 표시 유형을 사용하여 대화형 작업 설정

1. [그리드 행 클릭 시 대화형 작업 설정](#setting-up-interactivity-on-grid-row-click) 섹션의 단계에 따라 두 개의 대화형 컨트롤을 설정합니다.
2. 맨 위에 새 매개 변수를 추가합니다.
    1. 이름: `ShowDetails`
    2. 매개 변수 형식: `Drop down`
    3. 필수: `checked`
    4. 데이터를 가져오는 출처: `JSON`
    5. JSON 입력: `["Yes", "No"]`
    6. 저장하여 변경 내용을 커밋합니다.

    ![매개 변수 추가 단추를 선택하면 매개 변수 편집 창이 표시됩니다.](./media/workbooks-interactive/edit-parameter.png)

3. 매개 변수 값을 `Yes`로 설정합니다.

    ![완료 편집 단추 위에, 매개 변수 값을 설정할 수 있는 드롭다운이 있습니다.](./media/workbooks-interactive/set-parameter.png)

4. 영역 차트가 있는 쿼리 컨트롤에서 _고급 설정_ 아이콘(기어 아이콘)을 선택합니다.
5. `Make this item conditionally visible` 설정을 확인합니다.
    1. `ShowDetails` 매개 변수 값이 `Yes`와 `equals`이면 이 항목이 표시됩니다.
6. _편집 완료_ 를 선택하여 변경 내용을 커밋합니다.
7. 통합 문서 도구 모음에서 _편집 완료_ 를 선택하여 읽기 모드로 전환합니다.
8. 매개 변수 값을 `ShowDetails`에서 `No`로 전환합니다. 아래 차트가 사라집니다.

아래 이미지는 `ShowDetails`가 `Yes`일 때 표시되는 경우를 나타냅니다.

![차트가 표시되는 조건부 표시 유형을 보여주는 스크린샷](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

아래 이미지는 `ShowDetails`가 `No`일 때 숨겨지는 경우를 나타냅니다.

![차트가 숨겨지는 조건부 표시 유형을 보여주는 스크린샷](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>표 및 차트에서 여러 항목을 선택하는 대화형 작업

쿼리와 메트릭 단계에서는 한 행(또는 여러 행)을 선택했을 때 하나 이상의 매개 변수를 내보낼 수도 있습니다.

![여러 매개 변수가 있는 내보내기 매개 변수 설정을 보여주는 스크린샷. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. 표를 표시하는 쿼리 단계에서 고급 설정으로 이동합니다.
2. `When items are selected, export parameters` 확인란을 선택합니다. 추가 컨트롤이 표시됩니다.
3. `allow selection of multiple values` 확인란을 선택합니다.
    1. 표시된 시각화는 드롭다운 매개 변수를 사용하는 경우처럼, 다중 선택을 허용하며 내보낸 매개 변수의 값은 값의 배열이 됩니다.
    2. 선택하지 않은 경우 표시 시각화는 마지막으로 선택한 항목만 고려합니다. 한 번에 하나의 값만 내보냅니다.
4. 내보내려는 각 매개 변수에 대해 *매개 변수 추가* 단추를 사용합니다. 내보낼 매개 변수에 대 한 설정을 포함 하는 팝업 창이 표시 됩니다.

단일 선택을 사용하도록 설정한 경우 내보낼 원본 데이터의 필드를 작성자가 지정할 수 있습니다. 필드에는 매개 변수 이름, 매개 변수 형식 및 선택한 항목이 없는 경우 사용할 기본값(선택 사항)이 포함됩니다.

다중 선택을 사용하도록 설정한 경우 내보낼 원본 데이터의 필드를 작성자가 지정할 수 있습니다. 필드에는 매개 변수 이름, 매개 변수 형식, 인용 부호와 구분 기호가 포함됩니다. 인용 부호 및 구분 기호 값은 쿼리에서 바뀔 때 화살표 값을 텍스트로 변환하는 데 사용됩니다. 다중 선택에서 값을 선택하지 않으면 기본값은 빈 배열입니다.

> [!NOTE]
> 다중 선택의 경우 고유한 값만 내보냅니다. 즉, "1,1,2,1" 같은 출력 배열 값은 없고 "1,2" 형태의 출력 값이 표시됩니다.

내보내기 설정에서 "내보낼 필드" 설정을 비워 둘 수 있습니다. 이렇게 하면 데이터에서 사용할 수 있는 모든 필드를 키:값 쌍의 문자열 형식 JSON 개체로 내보냅니다. 표 및 제목의 경우 표의 모든 필드가 됩니다. 차트의 경우 사용할 수 있는 필드는 x, y, 계열 및 레이블(차트 유형에 따라 다름)이 됩니다.

기본 동작은 매개 변수를 텍스트로 내보내는 것이지만, 필드가 구독 또는 리소스 ID 인 경우 이를 내보내기 매개 변수 형식으로 사용합니다. 이렇게 하면 해당 매개 변수 형식이 필요한 위치에서 매개 변수를 다운스트림으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 시작 다양한 시각화 옵션을 갖춘 통합 문서에 대해 자세히 알아보기를 [시작하세요](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md) 및 공유합니다.