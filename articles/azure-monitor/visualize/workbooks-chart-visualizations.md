---
title: Azure Monitor 통합 문서 차트 시각화
description: 모든 Azure Monitor 통합 문서 차트 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 59f42867ec4dce29d136ec96df6e7642b7329911
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129285"
---
# <a name="chart-visualizations"></a>차트 시각화

통합 문서를 사용하면 모니터링 데이터를 차트로 표시할 수 있습니다. 지원되는 차트 종류에는 꺾은선형, 막대형, 막대형 범주, 영역형, 분산형, 원형 및 시간 차트가 포함됩니다. 작성자는 차트의 높이, 너비, 색상표, 범례, 제목, 데이터 없음 메시지 등을 사용자 지정하고, 차트 설정을 사용하여 축 유형과 계열 색을 사용자 지정하도록 선택할 수 있습니다.

통합 문서는 로그와 메트릭 데이터 원본의 차트를 모두 지원합니다.

## <a name="log-charts"></a>로그 차트

Azure Monitor 로그는 리소스 소유자에게 앱 및 인프라의 작동에 대한 자세한 정보를 제공합니다. 메트릭과 달리 로그 정보는 기본적으로 수집되지 않으며 특정 유형의 수집을 탑재해야 합니다. 그러나 있는 경우 로그는 리소스의 상태에 대한 많은 정보와 진단에 유용한 데이터를 제공합니다. 통합 문서를 사용하면 로그 데이터를 사용자 분석을 위한 시각적 차트로 표시할 수 있습니다.

### <a name="adding-a-log-chart"></a>로그 차트 추가

아래 예제에서는 이전 날짜 동안의 앱에 대한 요청의 추세를 보여 줍니다.

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **쿼리 추가** 링크를 사용하여 통합 문서에 로그 쿼리 컨트롤을 추가합니다.
3. **로그**, 리소스 형식(예: Application Insights) 및 대상 리소스로 쿼리 유형을 선택합니다.
4. 쿼리 편집기를 사용하여 분석의 [KQL](/azure/kusto/query/)(예: 요청 추세)을 입력합니다.
5. **영역형**, **막대형**, **막대(범주형)** , **꺾은선형**, **원형**, **분산형** 또는 **시간** 중 하나로 시각화를 설정합니다.
6. 필요한 경우 시간 범위, 시각화, 크기, 색상표, 범례와 같은 다른 매개 변수를 설정합니다.

[![편집 모드의 로그 차트 스크린샷](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>로그 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Query Type` | 사용할 쿼리 유형입니다. | 로그, Azure Resource Graph 등 |
| `Resource Type` | 대상으로 지정할 리소스 종류입니다. | Application Insights, Log Analytics 또는 Azure-first |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyApp1 |
| `Time Range` | 로그 차트를 볼 기간입니다. | 지난 1시간, 지난 24시간 등 |
| `Visualization` | 사용할 시각화입니다. | 영역형, 막대형, 꺾은선형, 원형, 분산형, 시간, 막대 범주형 |
| `Size` | 컨트롤의 수직 크기입니다. | 작음, 중간, 큼 또는 전체 |
| `Color palette` | 차트에 사용할 색상표입니다. 다중 메트릭 또는 분할 모드에서는 무시됩니다. | 파랑, 녹색, 빨강 등 |
| `Legend` | 범례에 사용할 집계 함수입니다. | 값의 합계 또는 평균, 최댓값, 최솟값, 처음 값, 마지막 값 |
| `Query` | 차트 시각화에 필요한 형식으로 데이터를 반환하는 KQL 쿼리입니다. | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="time-series-charts"></a>시계열 차트

영역형, 막대형, 꺾은선형, 분산형, 시간과 같은 시계열 차트는 통합 문서의 쿼리 컨트롤을 사용하여 쉽게 만들 수 있습니다. 중요한 것은 결과 집합에 시간 및 메트릭 정보가 있어야 한다는 것입니다.

#### <a name="simple-time-series"></a>단순 시계열

아래 쿼리는 *timestamp* 와 *Requests* 라는 2개의 열이 있는 테이블을 반환합니다. 쿼리 컨트롤은 X축에 *timestamp* 를, Y축에 *Requests* 를 사용합니다.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![단순 시계열 로그 꺾은선형 차트의 스크린샷.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>여러 메트릭이 있는 시계열

아래 쿼리는 *timestamp*, *Requests*, *Users* 라는 3개의 열이 있는 테이블을 반환합니다. 쿼리 컨트롤은 X축에 *timestamp* 를, Y축에서 *Requests* & *Users* 를 별도의 계열로 사용합니다.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![여러 메트릭 로그 꺾은선형 차트가 있는 시계열의 스크린샷.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>분할 시계열

아래 쿼리는 *timestamp*, *Requests*, *RequestName* 이라는 3개의 열이 있는 테이블을 반환합니다. 여기서 *RequestName* 은 요청의 이름이 있는 범주형 열입니다. 여기 있는 쿼리 컨트롤은 X축에 *timestamp* 를 사용하고 *RequestName* 값마다 계열을 추가합니다.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![분할 시계열 로그 꺾은선형 차트의 스크린샷.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>summarize와 make-series 비교

이전 섹션의 예제에서는 `summarize` 연산자가 사용되는데, 이해하기 더 쉽기 때문입니다. 하지만 summarize는 버킷에 항목이 없는 경우 결과 행을 생략하는 중대한 제한이 있습니다. 빈 버킷이 시간 범위 앞에 있는지 뒤에 있는지에 따라 차트 기간이 이동하는 효과가 있을 수 있습니다.

일반적으로 빈 버킷의 기본값을 제공하는 옵션이 있는 `make-series` 연산자를 사용하여 시계열 데이터를 만드는 것이 더 좋습니다.

다음 쿼리는 `make-series` 연산자를 사용합니다.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

아래 쿼리는 `summarize` 연산자를 사용하여 비슷한 차트를 보여 줍니다.

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

기본 결과 집합이 다른 경우에도 차트가 비슷합니다. 사용자가 시각화를 영역형, 꺾은선형, 막대형 또는 시간으로 설정하기만 하면 통합 문서가 나머지 작업을 처리합니다.

[![make-series 쿼리에서 만든 로그 꺾은선형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>범주형 막대 차트 또는 히스토그램

사용자는 범주형 차트를 사용하여 차트의 X축에 차원 또는 열을 나타낼 수 있습니다. 이는 히스토그램에서 특히 유용합니다. 아래 예제는 결과 코드를 기준으로 요청 분포를 보여 줍니다.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

쿼리는 *Requests* 메트릭과 *Result* 범주라는 2개의 열을 반환합니다. *Result* 열의 각 값은 차트에서 *Requests* 메트릭에 비례하는 높이의 자체 막대를 갖습니다.

[![결과 코드 기준 requests의 범주 막대형 차트 스크린샷](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>원형 차트

원형 차트에서는 숫자 비율의 시각화를 사용할 수 있습니다. 아래 예제는 결과 코드를 기준으로 요청의 비율을 보여 줍니다.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

쿼리는 *Requests* 메트릭과 *Result* 범주라는 2개의 열을 반환합니다. *Result* 열의 각 값은 원형 차트에서 *Requests* 메트릭에 비례하는 크기의 자체 조각을 갖습니다.

[![결과 코드를 나타내는 조각이 있는 원형 차트의 스크린샷](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>메트릭 차트

대부분의 Azure 리소스는 상태에 대한 메트릭 데이터(예: CPU 사용률, 스토리지 가용성, 데이터베이스 트랜잭션 수, 실패한 앱 요청 등)를 내보냅니다. 통합 문서를 사용하면 이 데이터를 시계열 차트로 시각화할 수 있습니다.

### <a name="adding-a-metric-chart"></a>메트릭 차트 추가

다음 예제는 이전 시간 동안 스토리지 계정에서의 트랜잭션 수를 보여 줍니다. 이를 통해 스토리지 소유자는 트랜잭션 추세를 보고 동작의 변칙을 찾을 수 있습니다.

1. **편집** 도구 모음 항목을 선택하여 통합 문서를 편집 모드로 전환합니다.
2. **메트릭 추가** 링크를 사용하여 통합 문서에 메트릭 컨트롤을 추가합니다.
3. 리소스 종류(예: 스토리지 계정), 대상으로 지정할 리소스, 메트릭 네임스페이스 및 이름, 사용할 집계를 선택합니다.
4. 필요한 경우 시간 범위, 분할 기준, 시각화, 크기, 색상표와 같은 다른 매개 변수를 설정합니다.

[![편집 모드의 메트릭 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>메트릭 차트 매개 변수

| 매개 변수 | 설명 | 예제 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 대상으로 지정할 리소스 종류입니다. | 스토리지 또는 가상 머신입니다. |
| `Resources` | 메트릭 값을 가져올 리소스 집합입니다. | MyStorage1 |
| `Namespace` | 메트릭이 있는 네임스페이스입니다. | Storage > Blob |
| `Metric` | 시각화할 메트릭입니다. | Storage > Blob > Transactions |
| `Aggregation` | 메트릭에 적용할 집계 함수입니다. | 합계, 개수, 평균 등 |
| `Time Range` | 메트릭을 볼 기간입니다. | 지난 1시간, 지난 24시간 등 |
| `Visualization` | 사용할 시각화입니다. | 영역형, 막대형, 꺾은선형, 분산형, 그리드 |
| `Split By` | 필요에 따라 차원에서 메트릭을 분할합니다. | 지역 유형별 트랜잭션 |
| `Size` | 컨트롤의 수직 크기입니다. | 소형, 중형, 또는 대형 |
| `Color palette` | 차트에 사용할 색상표입니다. `Split by` 매개 변수가 사용되는 경우 무시됩니다. | 파랑, 녹색, 빨강 등 |

### <a name="examples"></a>예

API 이름을 기준으로 분할된 트랜잭션(꺾은선형 차트):

[![API 이름을 기준으로 분할된 스토리지 트랜잭션의 메트릭 꺾은선형 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

응답 유형을 기준으로 분할된 트랜잭션(큰 막대형 차트):

[![응답 유형을 기준으로 분할된 스토리지 트랜잭션의 큰 메트릭 막대형 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

평균 대기 시간(분산형 차트):

[![스토리지 대기 시간의 메트릭 분산형 차트 스크린샷](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>차트 설정

작성자는 차트 설정을 사용하여 차트 축에 사용되는 필드, 축 단위, 사용자 지정 서식, 범위, 그룹화 동작, 범례 및 계열 색을 사용자 지정할 수 있습니다.

### <a name="the-settings-tab"></a>설정 탭

설정 탭은 다음을 제어합니다.

- 필드 및 사용자가 숫자 서식을 축 값 및 사용자 지정 범위로 설정할 수 있는 사용자 지정 서식을 포함한 축 설정.
- 필드 및 ‘기타’ 그룹이 만들어지기 전의 제한을 포함한 그룹화 설정.
- 아래쪽에 표시되는 메트릭(계열 이름, 색 및 숫자) 및 범례(계열 이름 및 색)를 포함한 범례 설정.

![차트 설정의 스크린샷.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>사용자 지정 서식

숫자 서식 옵션은 다음과 같습니다.

| 서식 지정 옵션             | 설명                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 열 단위 - 백분율, 개수, 시간, 바이트, 개수/시간, 바이트/시간 등에 대한 다양한 옵션입니다. 예를 들어 값 1234에 대한 단위를 밀리초로 설정하면 값은 1.234s로 렌더링됩니다.                                  |
| `Style`                      | 렌더링할 형식 - 10진수, 통화, 백분율.                                               |
| `Show group separator`       | 그룹 구분 기호를 표시하기 위한 확인란입니다. 미국에서는 1234를 1,234로 렌더링합니다.                                    |
| `Minimum integer digits`     | 사용할 최소 정수 자릿수(기본값 1)입니다.                                                   |
| `Minimum fractional digits`  | 사용할 최소 소수 자릿수(기본값은 0)입니다.                                                |
| `Maximum fractional digits`  | 사용할 최대 소수 자릿수입니다.                                                            |
| `Minimum significant digits` | 사용할 최소 유효 자릿수(기본값 1)입니다.                                               |
| `Maximum significant digits` | 사용할 최대 유효 자릿수입니다.                                                           |

![X축 설정의 스크린샷.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>계열 탭

계열 설정 탭에서는 차트의 계열에 대해 표시되는 레이블과 색을 조정할 수 있습니다.

- `Series name` 필드를 사용하여 데이터의 계열을 일치시키고 일치하는 경우 표시 레이블 및 색이 표시됩니다.
- `Comment` 필드는 번역자가 이 주석을 사용하여 표시 레이블을 지역화할 수 있으므로 템플릿 작성자에게 유용합니다.

![계열 설정의 스크린샷.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 타일](workbooks-tile-visualizations.md)을 만드는 방법을 알아봅니다.
- [대화형 통합 문서](workbooks-interactive.md)를 만드는 방법을 알아봅니다.