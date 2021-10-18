---
title: Azure Monitor 통합 문서 그리드 시각화
description: 모든 Azure Monitor 통합 문서 그리드 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 91b0933102c166657c237e7a7c9511a22a28de5c
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132496"
---
# <a name="grid-visualizations"></a>그리드 시각화

그리드 또는 테이블은 사용자에게 데이터를 표시하는 일반적인 방법입니다. 통합 문서를 사용하면 사용자가 그리드의 열에 대해 개별적으로 스타일을 지정하여 보고서에 대한 풍부한 UI를 제공할 수 있습니다.

아래 예제에서는 아이콘, 열 지도 및 스파크 막대를 결합하여 복잡한 정보를 제공하는 그리드를 보여 줍니다. 통합 문서에는 정렬, 검색 상자 및 분석으로 이동 단추도 제공됩니다.

[![로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>로그 기반 그리드 추가

1. **편집** 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **쿼리 추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스를 선택합니다.
4. 쿼리 편집기를 사용하여 분석을 위한 KQL을 입력할 수 있습니다(예: 메모리가 임계값 미만인 VM).
5. 시각화를 **그리드** 로 설정합니다.
6. 필요한 경우 시간 범위, 크기, 색상표, 범례와 같은 다른 매개 변수를 설정합니다.

[![로그 기반 그리드 쿼리의 스크린샷](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형입니다. | 로그, Azure Resource Graph 등 |
| `Resource Type` | 대상으로 지정할 리소스 종류입니다. | Application Insights, Log Analytics 또는 Azure-first |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyApp1 |
| `Time Range` | 로그 차트를 볼 기간입니다. | 지난 1시간, 지난 24시간 등 |
| `Visualization` | 사용할 시각화입니다. | 그리드 |
| `Size` | 컨트롤의 수직 크기입니다. | 작음, 중간, 큼 또는 전체 |
| `Query` | 차트 시각화에 필요한 형식으로 데이터를 반환하는 KQL 쿼리입니다. | _requests \| summarize Requests = count() by name_ |

## <a name="simple-grid"></a>기본 그리드

통합 문서는 KQL 결과를 간단한 테이블로 렌더링할 수 있습니다. 아래 표에서는 앱의 요청 유형별 요청 수 및 개별 사용자 수를 보여 줍니다.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![로그 기반 그리드의 편집 모드를 보여 주는 스크린샷](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>그리드 스타일 지정

일반 테이블은 데이터를 보여 주는 반면 읽기 어려울 수 있으며 정보가 항상 명확히 드러나는 것은 아닙니다. 그리드 스타일을 지정하면 데이터를 보다 쉽게 읽고 해석할 수 있습니다.

다음은 이전 섹션과 동일한 그리드가 열 지도 스타일로 지정된 예입니다.

[![열이 열 지도 스타일로 지정된 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

다음은 동일한 그리드가 막대형 스타일로 지정된 예입니다. [![열 스타일이 막대형으로 지정된 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>그리드 열 스타일 지정

1. 쿼리 컨트롤 도구 모음에서 **열 설정** 단추를 선택합니다.
2. '열 설정 편집'에서 스타일을 지정할 열을 선택합니다.
3. 열 렌더러(예: 열 지도, 막대형, 아래 막대형 등) 및 관련 설정을 선택하여 열 스타일을 지정합니다.

다음은 '요청' 열을 막대형 스타일로 지정하는 예제입니다.

[![요청 열이 막대형 스타일로 지정된 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>열 렌더러

| 열 렌더러 | 설명 | 추가 옵션 |
|:------------- |:-------------|:-------------|
| `Automatic` | 기본값 - 열 유형을 기반으로 가장 적합한 렌더러를 사용합니다.  |  |
| `Text` | 열 값을 텍스트로 렌더링합니다. | |
| `Right Aligned` | 오른쪽 맞춤이라는 점을 제외하고 텍스트와 유사합니다. | |
| `Date/Time` | 읽을 수 있는 날짜 시간 문자열을 렌더링합니다. | |
| `Heatmap` | 셀의 값을 기준으로 그리드 셀의 색을 설정합니다. | 크기 조정에 사용되는 색상표 및 최소/최대 값입니다. |
| `Bar` | 셀의 값을 기준으로 셀 옆에 막대를 렌더링합니다. | 크기 조정에 사용되는 색상표 및 최소/최대 값입니다. |
| `Bar underneath` | 셀의 값을 기준으로 셀의 아래쪽 근처에 막대를 렌더링합니다. | 크기 조정에 사용되는 색상표 및 최소/최대 값입니다. |
| `Composite bar` | 해당 행에 지정된 열을 사용하여 복합 막대를 렌더링합니다. 자세한 내용은 [복합 막대](workbooks-composite-bar.md)를 참조하세요. | 막대를 렌더링하는 데 사용되는 색이 있고 막대의 위쪽에 레이블이 표시되는 열입니다. |
| `Spark bars` | 셀의 동적 배열 값을 기준으로 셀에 스파크 막대를 렌더링합니다. 예를 들어 위쪽 스크린샷의 추세 열입니다. | 크기 조정에 사용되는 색상표 및 최소/최대 값입니다. |
| `Spark lines` | 셀의 동적 배열 값을 기준으로 셀에서 스파크 라인을 렌더링합니다. | 크기 조정에 사용되는 색상표 및 최소/최대 값입니다. |
| `Icon` | 셀의 텍스트 값을 기준으로 아이콘을 렌더링합니다. 지원되는 값은 `cancelled`, `critical`, `disabled`, `error`, `failed`, `info`, `none`, `pending`, `stopped`, `question`, `success`, `unknown`, `warning` `uninitialized`, `resource`, `up`, `down`, `left`, `right`, `trendup`, `trenddown`, `4`, `3`, `2`, `1`, `Sev0`, `Sev1`, `Sev2`, `Sev3`, `Sev4`, `Fired`, `Resolved`, `Available`, `Unavailable`, `Degraded`, `Unknown`, `Blank`입니다.|  |
| `Link` | 클릭하면 링크를 렌더링하거나 구성 가능한 작업을 수행합니다. 항목으로 링크만 사용하려면 이 렌더러를 선택합니다.  다른 유형 또한 `Make this item a link` 설정을 사용하여 링크가 될 수 있습니다. 자세한 내용은 아래의 [링크 작업](#link-actions)을 참조하세요. |  |
| `Location` | 지역 ID를 기반으로 친숙한 Azure 지역 이름을 렌더링합니다. |  |
| `Resource type` | 리소스 종류 ID를 기반으로 친숙한 리소스 종류 문자열을 렌더링합니다.  |  |
| `Resource` | 리소스 ID를 기반으로 친숙한 리소스 이름 및 링크를 렌더링합니다.  | 리소스 종류 아이콘을 표시하는 옵션  |
| `Resource group` | 리소스 그룹 ID를 기반으로 친숙한 리소스 그룹 이름 및 링크를 렌더링합니다. 셀의 값이 리소스 그룹이 아닌 경우 1로 변환됩니다.  | 리소스 그룹 아이콘을 표시하는 옵션  |
| `Subscription` | 구독 ID를 기반으로 친숙한 구독 이름 및 링크를 렌더링합니다. 셀의 값이 구독이 아닌 경우 1로 변환됩니다.  | 구독 아이콘을 표시하는 옵션입니다.  |
| `Hidden` | 그리드에서 열을 숨깁니다. 기본 쿼리가 필요한 것보다 많은 열을 반환하지만 project-away를 원하지 않는 경우에 유용합니다. |  |

### <a name="link-actions"></a>링크 작업

`Link` 렌더러를 선택하거나 '이 항목을 링크로 만들기' 확인란을 선택하면 작성자가 셀을 선택할 때 발생하는 링크 작업을 구성할 수 있습니다. 그러면 일반적으로 사용자가 셀에서 나오는 컨텍스트를 사용하여 다른 보기로 이동하거나 URL이 열릴 수 있습니다.

### <a name="custom-formatting"></a>사용자 지정 형식 지정

또한 통합 문서를 통해 사용자는 셀 값의 숫자 형식을 설정할 수 있습니다. 사용 가능한 경우 '사용자 지정 형식 지정' 확인란을 클릭하여 이 작업을 수행할 수 있습니다.

| 서식 지정 옵션 | 설명 |
|:------------- |:-------------|
| `Units` | 열 단위 - 백분율, 개수, 시간, 바이트, 개수/시간, 바이트/시간 등에 대한 다양한 옵션입니다. 예를 들어 값 1234에 대한 단위를 밀리초로 설정하면 값은 1.234s로 렌더링됩니다. |
| `Style` | 렌더링할 형식 - 10진수, 통화, 백분율. |
| `Show group separator` | 그룹 구분 기호를 표시하기 위한 확인란입니다. 미국에서는 1234를 1,234로 렌더링합니다. |
| `Minimum integer digits` | 사용할 최소 정수 자릿수(기본값 1)입니다. |
| `Minimum fractional digits` | 사용할 최소 소수 자릿수(기본값은 0)입니다.  |
| `Maximum fractional digits` | 사용할 최대 소수 자릿수입니다. |
| `Minimum significant digits` | 사용할 최소 유효 자릿수(기본값 1)입니다. |
| `Maximum significant digits` | 사용할 최대 유효 자릿수입니다. |
| `Custom text for missing values` | 데이터 요소에 값이 없는 경우 공백 대신 이 사용자 지정 텍스트를 표시합니다. |

### <a name="custom-date-formatting"></a>사용자 지정 날짜 형식

작성자는 열이 날짜/시간 렌더러에서 설정되도록 지정한 경우 '사용자 지정 날짜 형식 지정' 확인란을 사용하여 사용자 지정 날짜 형식 옵션을 지정할 수 있습니다.

| 서식 지정 옵션 | 설명 |
|:------------- |:-------------|
| `Style` | 날짜를 짧은 형식, 긴 형식 또는 전체 형식으로, 또는 시간을 짧은 시간 형식 또는 긴 시간 형식으로 렌더링하는 형식입니다. |
| `Show time as` | 작성자가 현지 시간 표시(기본값) 또는 UTC 표시 중에서 선택할 수 있습니다. 선택한 날짜 형식 스타일에 따라 UTC/표준 시간대 정보를 표시하지 못할 수 있습니다. |

## <a name="custom-column-width-setting"></a>사용자 지정 열 너비 설정

작성자는 '열 설정'의 '사용자 지정 열 너비' 필드를 사용하여 그리드의 열 너비를 사용자 지정할 수 있습니다.

![사용자 지정 열 너비 필드가 빨간색 상자로 표시된 열 설정의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

필드를 빈 칸 그대로 두면 열에 있는 문자 수와 표시되는 열 수를 기준으로 너비가 자동으로 결정됩니다. 기본 단위는 'ch'(문자)입니다.

레이블에서 파란색 **(현재 너비)** 단추를 선택하면 선택한 열의 현재 너비로 텍스트 필드가 채워집니다. 측정 단위가 없는 사용자 지정 너비 필드에 값이 있으면 기본값이 사용됩니다.

사용 가능한 측정 단위는 다음과 같습니다.

| 측정 단위 | 정의           |
|:--------------------|:---------------------|
| ch                  | 문자(기본값) |
| px                  | 픽셀               |
| fr                  | 소수부 단위     |
| %                   | percentage           |

입력 유효성 검사 - 유효성 검사가 실패하는 경우 빨간색 지침 메시지가 필드 아래에 팝업으로 표시되지만 사용자는 여전히 너비를 적용할 수 있습니다. 입력에 값이 있으면 해당 값은 구문 분석됩니다. 유효한 측정 단위가 없으면 기본값이 사용됩니다.

최소/최대 너비는 없으며 작성자가 판단하여 결정합니다. 숨겨진 열에 대해 사용자 지정 열 너비 필드를 사용할 수 없습니다.

## <a name="examples"></a>예

### <a name="spark-lines-and-bar-underneath"></a>스파크 라인 및 아래 막대

아래 예제에서는 요청 이름별 요청 수 및 추세를 보여 줍니다.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![스파크 라인 및 아래 막대가 있는 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>공유 눈금 및 사용자 지정 서식 지정을 사용한 열 지도

이 예제에서는 다양한 요청 기간 메트릭 및 해당 개수를 보여 줍니다. 열 지도 렌더러는 설정에 지정된 최솟값을 사용하거나 열에 대해 최솟값 및 최댓값을 계산하고 열의 최솟값 및 최댓값을 기준으로 한 셀의 값에 따라 셀에 대해 선택한 색상표에서 배경색을 할당합니다.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![열 전체에 공유된 눈금을 사용하는 열 지도가 있는 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

위의 예제에서 공유 색상표(녹색 또는 빨간색) 및 눈금은 열(평균, 중앙값, p80, p95 및 p99)에 색상을 지정하는 데 사용됩니다. 요청 열에 사용되는 별도의 색상표(파란색)입니다.

#### <a name="shared-scale"></a>공유 눈금

공유 눈금을 가져오려면

1. 정규식을 사용하여 설정을 적용할 열을 두 개 이상 선택합니다. 예를 들어 열 이름을 `Mean|Median|p80|p95|p99`로 설정하여 모두 선택합니다.
2. 개별 열에 대한 기본 설정을 삭제합니다.

이렇게 하면 새 다중 열 설정이 해당 설정을 적용하여 공유 눈금을 포함시킵니다.

[![열에서 공유 눈금 가져오기 위한 로그 기반 그리드 설정의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>상태를 나타내는 아이콘

아래 예제에서는 p95 기간을 기준으로 요청의 사용자 지정 상태를 보여 줍니다.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![위의 쿼리를 사용하여 열 전체에 공유된 눈금을 사용하는 열 지도가 있는 로그 기반 그리드의 스크린샷](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

지원되는 아이콘 이름은 `cancelled`, `critical`, `disabled`, `error`, `failed`, `info`, `none`, `pending`, `stopped`, `question`, `success`, `unknown`, `warning` `uninitialized`, `resource`, `up`, `down`, `left`, `right`, `trendup`, `trenddown`, `4`, `3`, `2`, `1`, `Sev0`, `Sev1`, `Sev2`, `Sev3`, `Sev4`, `Fired`, `Resolved`, `Available`, `Unavailable`, `Degraded`, `Unknown` 및 `Blank`입니다.

### <a name="using-thresholds-with-links"></a>링크에 임계값 사용

아래 지침에서는 링크에 임계값을 사용하여 아이콘을 할당하고 다양한 통합 문서를 여는 방법을 보여 줍니다. 그리드의 각 링크는 해당 Application Insights 리소스에 대해 다른 통합 문서 템플릿을 엽니다.

1. 편집 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **추가** 를 선택하고 쿼리 추가를 선택합니다.
3. 데이터 원본을 'JSON'으로 변경하고 시각화를 '그리드'로 변경합니다.
4. 다음 쿼리를 입력합니다.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. 쿼리를 실행합니다.
6. **열 설정** 을 선택하여 설정을 엽니다.
7. 열에서 '이름'을 선택합니다.
8. 열 렌더러에서 '임계값'을 선택합니다.
9.  다음 임계값 설정을 입력하고 선택합니다.
   
    | 연산자 | 값   | 아이콘   |
    |----------|---------|---------|
    | ==       | warning | 경고 |
    | ==       | error   | 실패  |

    ![위의 설정이 포함된 열 설정 편집 탭의 스크린샷](./media/workbooks-grid-visualizations/column-settings.png)

    기본 행을 그대로 유지합니다. 원하는 모든 텍스트를 입력할 수 있습니다. 텍스트 열은 입력으로 문자열 형식을 사용하고 지정된 경우 열 값 및 단위를 사용하여 값을 채웁니다. 예를 들어 경고가 열 값이고 텍스트가 '{0} {1} 링크!'인 경우 '경고 링크!'로 표시됩니다.
10. 이 항목을 링크로 만들기 상자를 선택합니다.
    1. 열 보기에서 '통합 문서(템플릿)'를 선택합니다.
    2. 연결 값 출처 아래에서 '링크'를 선택합니다.
    3. 컨텍스트 블레이드에서 링크 열기 상자를 선택합니다.
    4. 통합 문서 링크 설정에서 다음 설정을 선택합니다.
        1. 템플릿 ID 출처 아래에서 '열'을 선택합니다.
        2. 열 아래에서 '링크'를 선택합니다.

    ![위의 설정이 포함된 링크 설정의 스크린샷](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. 열에서 '링크'를 선택합니다. 열 렌더러 옆의 설정 아래에서 **(열 숨기기)** 를 선택합니다.
1. 'name' 열의 표시 이름을 변경하려면 **레이블** 탭을 선택합니다. 열 ID가 'name'인 행에서 *열 레이블 아래에 표시할 이름을 입력합니다.
2. **적용** 을 선택합니다.

![위의 설정이 포함된 그리드 임계값의 스크린샷](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>소수부 단위 백분율

fr(소수부 단위)은 다양한 유형의 그리드에서 일반적으로 사용되는 동적 측정 단위입니다. 창 크기/해상도가 변경되면 fr 너비도 변경됩니다.

아래 스크린샷에서는 각각 1fr 너비로 너비가 동일한 8개의 열이 있는 테이블을 보여 줍니다. 창 크기가 변경되면 각 열의 너비가 비례적으로 변경됩니다.

[![열 너비 값이 각각 1fr인 그리드 열의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

아래 이미지는 첫 번째 열이 50% 너비로 설정된 것만 제외하고 동일한 테이블을 보여 줍니다. 그러면 열이 전체 그리드 너비의 절반으로 동적으로 설정됩니다. 창 크기를 조정하면 창 크기가 너무 작은 경우를 제외하고는 50% 너비를 계속 유지합니다. 이러한 동적 열은 내용에 따라 최소 너비를 사용합니다. 그리드의 나머지 50%는 8개의 총 소수부 단위로 분할됩니다. 아래의 'kind' 열은 2fr로 설정되어 있으므로 나머지 공간의 1/4을 사용합니다. 다른 열은 각각 1fr이므로 각 열은 그리드 오른쪽 절반의 1/8을 사용합니다.

[![한 열 너비 값이 50%이고 나머지는 각각 1fr인 그리드 열의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

fr, %, px 및 ch 너비를 결합하는 것이 가능하며 위의 예제와 유사하게 작동합니다. 고정 단위(ch 및 px)로 설정된 너비는 창/해상도가 변경되는 경우에도 변경되지 않는 하드 상수입니다. %로 설정된 열은 전체 그리드 너비에 대한 백분율만큼 사용합니다(이전의 최소 너비 때문에 정확하지 않을 수 있음). fr로 설정된 열은 할당된 소수부 단위 수를 기준으로 나머지 그리드 공간을 분할합니다.

[![사용되는 다양한 너비 단위의 모음이 있는 그리드 열의 스크린샷](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 트리](workbooks-tree-visualizations.md)를 만드는 방법을 알아봅니다.
* [통합 문서 텍스트 매개 변수](workbooks-text.md)를 만드는 방법을 알아봅니다.
