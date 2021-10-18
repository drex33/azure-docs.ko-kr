---
title: Azure Monitor 통합 문서 타일 시각화
description: 모든 Azure Monitor 통합 문서 타일 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: fa486647f124e374087abab959ced16cbddad539
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131641"
---
# <a name="tile-visualizations"></a>타일 시각화

타일은 통합 문서에 요약 데이터를 표시하는 데 유용한 방법입니다. 아래 이미지는 상세 그리드 위에 앱 수준 요약이 있는 타일의 일반적인 사용 사례를 보여 줍니다.

[![타일 요약 보기의 스크린샷](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

통합 문서 타일은 제목, 부제목, 큰 텍스트, 아이콘, 메트릭 기반 그라데이션, spark 선/막대, 바닥글 등을 표시하는 기능을 지원합니다.

## <a name="adding-a-tile"></a>타일 추가

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 링크를 선택한 다음 쿼리 추가를 선택하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. 크기를 **전체** 로 설정합니다.
6. 시각화를 **타일** 로 설정합니다.
7. **타일 설정** 단추를 선택하여 설정 창을 엽니다.
    1. 제목에서 다음을 설정합니다.
        * 다음 열 사용: `name`.
    2. 왼쪽에서 다음을 설정합니다.
        * 다음 열 사용: `Requests`.
        * 열 렌더러: `Big Number`
        * 색상표: `Green to Red`
        * 최솟값: `0`.
    3. 아래쪽에서 다음을 설정합니다.
        * 다음 열 사용: `appName`.
8. 창 맨 아래에 있는 **저장 및 닫기** 단추를 선택합니다.

[![위의 쿼리 및 타일 설정이 포함된 타일 요약 보기의 스크린샷.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

읽기 모드의 타일:

[![읽기 모드의 타일 요약 보기 스크린샷.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>타일의 Spark 선

1. 편집 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. `TimeRange`라는 시간 범위 매개 변수를 추가합니다.
    1. **추가** 를 선택한 다음 매개 변수를 추가합니다.
    2. 매개 변수 컨트롤에서 **매개 변수 추가** 를 선택합니다.
    3. 매개 변수 이름 필드에 `TimeRange`를 입력하고 매개 변수 형식으로 `Time range picker`를 선택합니다.
    4. 창 맨 위에서 **저장** 을 선택한 다음 매개 변수 컨트롤에서 **편집 완료** 를 선택합니다.
3. **추가** 를 선택한 다음 쿼리 추가를 선택하여 매개 변수 컨트롤 아래에 로그 쿼리 컨트롤을 추가합니다.
4. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스로 쿼리 유형을 선택합니다.
5. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력합니다.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. **쿼리 실행** 을 선택합니다. (쿼리를 실행하기 전에 `TimeRange`를 선택한 값으로 설정해야 합니다.)
7. 시각화를 ‘타일’로 설정합니다.
8. 크기를 ‘전체’로 설정합니다.
9. **타일 설정** 을 선택합니다.
    1. 타일에서 다음을 설정합니다.
        * 다음 열 사용: `name`.
    2. 하위 타일에서 다음을 설정합니다.
        *  다음 열 사용: `appNAme`.
    3. 왼쪽에서 다음을 설정합니다.
        *  다음 열 사용: `Requests`.
        * 열 렌더러: `Big Number`
        * 색상표: `Green to Red`
        * 최솟값: `0`.
    4. 아래쪽에서 다음을 설정합니다.
        * 다음 열 사용: `Trend`.
        * 열 렌더러: `Spark line`
        * 색상표: `Green to Red`
        * 최솟값: `0`.
10. 페이지 맨 아래에서 **저장 및 닫기** 를 선택합니다.

![Spark 선이 있는 타일 시각화의 스크린샷](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>타일 크기

작성자에게는 타일 설정에서 타일 너비를 설정하는 옵션이 있습니다.

* `fixed`(기본값)

    타일의 기본 동작은 약 160픽셀인 동일한 고정 너비에 타일 주위 공간을 더한 것입니다.

    ![고정 너비 타일을 표시하는 스크린샷](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    각 제목은 내용에 맞게 축소 또는 확대되지만 타일은 타일 보기의 너비로 제한됩니다(가로 스크롤 없음).

    ![자동 너비 타일을 표시하는 스크린샷](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    각 제목은 항상 타일 보기의 전체 너비가 되며, 한 줄당 제목 하나가 표시됩니다.

     ![전체 크기 너비 타일을 표시하는 스크린샷](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>다음 단계

* 타일은 복합 막대 렌더러도 지원합니다. 자세히 알아보려면 [복합 막대 설명서](workbooks-composite-bar.md)를 참조하세요.
* `TimeRange`와 같은 시간 매개 변수에 대한 자세한 내용은 [통합 문서 시간 매개 변수 설명서](workbooks-time.md)를 참조하세요.