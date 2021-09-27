---
title: 시각화
description: Azure Synapse Notebook을 사용하여 데이터 시각화
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: b52f599dd3430f963b03b5fdba41f71abf11ee43
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128707670"
---
# <a name="visualize-data"></a>데이터 시각화
Azure Synapse는 통합 분석 서비스로, 데이터 웨어하우스와 빅 데이터 분석 시스템 전체에서 인사이트를 얻는 시간을 앞당길 수 있습니다. 데이터 시각화는 데이터에 대한 통찰력을 얻을 수 있는 핵심 구성 요소입니다. 이를 통해 사람이 크고 작은 데이터를 더 쉽게 이해할 수 있습니다. 또한 데이터 그룹의 패턴, 추세, 이상값을 더 쉽게 검색할 수 있습니다. 

Azure Synapse Analytics에서 Apache Spark를 사용하는 경우 Synapse Notebook 차트 옵션, 인기 있는 오픈 소스 라이브러리에 대한 액세스 권한, Synapse SQL 및 Power BI와의 통합 등 데이터를 시각화하는 데 도움이 되는 다양한 기본 제공 옵션이 있습니다.

## <a name="notebook-chart-options"></a>Notebook 차트 옵션
Azure Synapse Notebook을 사용하는 경우 차트 옵션을 통해 테이블 형식 결과 보기를 사용자 지정된 차트로 전환할 수 있습니다. 여기에서 코드를 작성하지 않고도 데이터를 시각화할 수 있습니다. 

### <a name="displaydf-function"></a>display(df) 함수
```display``` 함수를 사용하면 SQL 쿼리와 Apache Spark 데이터프레임, RDD를 풍부한 데이터 시각화로 전환할 수 있습니다. ```display``` 함수는 PySpark, Scala, Java 및 .NET에서 만든 데이터프레임 또는 RDD에서 사용될 수 있습니다.

차트 옵션에 액세스하려면 다음을 수행합니다.
1. ```%%sql``` 매직 명령의 출력은 기본적으로 렌더링된 테이블 뷰에 표시됩니다. 또한 Spark DataFrames 또는 RDD(Resilient Distributed Datasets) 함수에서 ```display(df)```를 호출하여 렌더링된 테이블 보기를 생성할 수 있습니다. 
   
2. 렌더링된 테이블 보기가 있으면 차트 보기로 전환합니다.
   ![built-in-charts](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. 이제 다음 값을 지정하여 시각화를 사용자 지정할 수 있습니다.

   | 구성 | Description |
   |--|--| 
   | 차트 종류 | ```display``` 함수는 막대형 차트, 산점도, 선 그래프 등 광범위한 차트 종류를 지원합니다. |
   | 키 | x-축의 값 범위를 지정합니다.|
   | 값 | y-축 값의 값 범위를 지정합니다. |
   | 계열 그룹 | 집계 그룹을 결정하는 데 사용 됩니다. | 
   | 집계 | 시각화에서 데이터를 집계하는 방법| 
   
   
    > [!NOTE]
    > 기본적으로 ```display(df)``` 함수는 데이터의 처음 1000행만 사용하여 차트를 렌더링합니다. **모든 결과에 대한 집계** 를 확인하고 **적용** 단추를 클릭하여 전체 데이터 세트에서 차트 생성을 적용합니다. Spark 작업은 차트 설정이 변경될 때 트리거됩니다. 계산을 완료하고 차트를 렌더링하는 데 몇 분 정도 걸릴 수 있습니다.
   
4. 완료되면 최종 시각화를 보고 이와 상호 작용할 수 있습니다.

### <a name="displaydf-statistic-details"></a>display(df) 통계 세부 정보
<code>display(df, summary = true)</code>를 사용하여 열 이름, 열 형식, 고유 값, 각 열에 누락된 값을 포함하는 지정된 Apache Spark DataFrame의 통계 요약을 확인할 수 있습니다. 또한 특정 열을 선택하여 최솟값, 최댓값, 평균값, 표준 편차를 볼 수 있습니다.
    ![built-in-charts-summary](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtml-option"></a>displayHTML () 옵션
Azure Synapse Analytics Notebooks는 ```displayHTML``` 함수를 사용하여 HTML 그래픽을 지원합니다.

다음 이미지는 [D3.js](https://d3js.org/)를 사용하여 시각화를 만드는 방법의 예제입니다.

   ![d3-js-example](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

다음 코드를 실행하여 위의 시각화를 만듭니다.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>인기 있는 라이브러리
데이터 시각화와 관련하여 Python은 다양한 기능이 포함된 여러 그래프 라이브러리를 제공합니다. 기본적으로 Azure Synapse Analytics의 모든 Apache Spark 풀에는 큐레이팅된 인기 오픈 소스 라이브러리 집합이 포함되어 있습니다. 또한 Azure Synapse Analytics 라이브러리 관리 기능을 사용하여 추가 라이브러리와 버전을 추가하거나 관리할 수 있습니다. 

### <a name="matplotlib"></a>Matplotlib
각 라이브러리에 기본 제공되는 렌더링 함수를 사용하여 Matplotlib 같은 표준 그리기 라이브러리를 렌더링할 수 있습니다.

다음 이미지는 **Matplotlib** 를 사용하여 막대형 차트를 만드는 방법의 예제입니다.
   ![선 그래프 예제입니다.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

위의 이미지를 그리려면 다음 샘플 코드를 실행합니다.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
```displayHTML(df)```을 사용하여 **bokeh** 같은 대화형 라이브러리나 HTML을 렌더링할 수 있습니다. 

다음 이미지는 **빛망울** 을 사용하여 지도 위에 문자 모양을 그리는 예입니다.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

위의 이미지를 그리려면 다음 샘플 코드를 실행합니다.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
**displayHTML()** 을 사용하여 **Plotly** 같은 대화형 라이브러리 또는 HTML을 렌더링할 수 있습니다.

다음 샘플 코드를 실행하여 아래의 이미지를 그립니다.

   ![plotly-example](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly
import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```
### <a name="pandas"></a>Pandas

Pandas 데이터 프레임의 html 출력을 기본 출력으로 볼 수 있습니다. 노트북은 스타일이 지정 된 html 콘텐츠를 자동으로 표시 합니다. 

   ![Panda 그래프 예제입니다.](./media/apache-spark-data-viz/support-panda.png#lightbox)

```python
import pandas as pd 
import numpy as np 

df = pd.DataFrame([[38.0, 2.0, 18.0, 22.0, 21, np.nan],[19, 439, 6, 452, 226,232]], 

                  index=pd.Index(['Tumour (Positive)', 'Non-Tumour (Negative)'], name='Actual Label:'), 

                  columns=pd.MultiIndex.from_product([['Decision Tree', 'Regression', 'Random'],['Tumour', 'Non-Tumour']], names=['Model:', 'Predicted:'])) 

df 
```


### <a name="additional-libraries"></a>추가 라이브러리 
이러한 라이브러리 외에도 Azure Synapse Analytics 런타임에는 데이터 시각화에 자주 사용되는 다음과 같은 라이브러리 집합도 포함되어 있습니다.

- [Seaborn](https://seaborn.pydata.org/) 

사용할 수 있는 라이브러리와 버전에 대한 최신 데이터 정보는 Azure Synapse Analytics 런타임 [설명서](./spark/../apache-spark-version-support.md)를 참조하세요.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Apache Spark 및 SQL 주문형을 사용하여 Power BI에 연결
Azure Synapse Analytics는 데이터 엔지니어가 분석 솔루션을 빌드할 수 있도록 Power BI와 긴밀하게 통합됩니다.

Azure Synapse Analytics를 사용하면 다른 작업 영역 계산 엔진에서 Spark 풀과 서버리스 SQL 풀 간에 데이터베이스와 테이블을 공유할 수 있습니다. [공유 메타데이터 모델](../metadata/overview.md)을 사용하면 SQL 주문형으로 Apache Spark 테이블을 쿼리할 수 있습니다. 완료되면 SQL 주문형 엔드포인트를 Power BI에 연결하여 동기화된 Spark 테이블을 쉽게 쿼리할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- Spark SQL DW 커넥터를 설정하는 방법에 대한 자세한 내용: [Synapse SQL 커넥터](./spark/../synapse-spark-sql-pool-import-export.md)
- 기본 라이브러리 보기: [Azure Synapse Analytics 런타임](../spark/apache-spark-version-support.md)