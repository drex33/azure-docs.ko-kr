---
title: '자습서: Azure Cosmos DB에서 데이터를 분석하고 시각화할 수 있는 Notebook 만들기'
description: '자습서: 기본 제공 Jupyter Notebook을 사용하여 데이터를 Azure Cosmos DB에 가져오고, 데이터를 분석하고, 출력을 시각화하는 방법을 알아봅니다.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 18bd28fd9a987763397ba77affce897b11e58ddd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123118144"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>자습서: Azure Cosmos DB에서 데이터를 분석하고 시각화할 수 있는 Notebook 만들기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 기본 제공 Jupyter Notebook을 사용하여 소매 데이터 샘플을 Azure Cosmos DB에 가져오는 방법에 대해 설명합니다. SQL 및 Azure Cosmos DB 매직 명령을 사용하여 쿼리를 실행하고, 데이터를 분석하고, 결과를 시각화하는 방법을 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Cosmos 계정에서 Notebooks 사용](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>리소스 만들기 및 데이터 가져오기
 
이 섹션에서는 Azure Cosmos 데이터베이스와 컨테이너를 만들고 소매 데이터를 컨테이너로 가져옵니다.

1. Azure Cosmos 계정으로 이동하여 **데이터 탐색기** 를 엽니다.

1. **Notebook** 탭으로 이동하여 **내 Notebook** 옆에 있는 `…`를 선택하고, **새 Notebook** 을 만듭니다. 기본 커널로 **Python 3** 을 선택합니다.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="새 Notebook 만들기":::

1. 새 Notebook이 만들어지면 **VisualizeRetailData.ipynb** 와 같은 이름으로 바꿀 수 있습니다.

1. 다음으로, 소매 데이터를 저장할 "RetailDemo"라는 데이터베이스와 "WebsiteData"라는 컨테이너를 만듭니다. 파티션 키로 /CartID를 사용할 수 있습니다. 다음 코드를 복사하여 Notebook의 새 셀에 붙여넣고 실행합니다.

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   셀을 실행하려면 `Shift + Enter`를 선택하거나 셀을 선택하고, 데이터 탐색기 탐색 모음에서 **활성 셀 실행** 옵션을 선택합니다.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="활성 셀 실행":::

   데이터베이스와 컨테이너는 현재 Azure Cosmos 계정에 만들어집니다. 컨테이너는 400개 RU/초로 프로비저닝됩니다. 데이터베이스와 컨테이너가 만들어지면 다음과 같은 출력이 표시됩니다. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   또한 **데이터** 탭을 새로 고쳐 새로 만든 리소스를 볼 수도 있습니다.

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="데이터 탭을 새로 고쳐 새 컨테이너 보기":::

1. 다음으로, 소매 데이터 샘플을 Azure Cosmos 컨테이너로 가져옵니다. 소매 데이터의 항목 형식은 다음과 같습니다.

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   자습서의 목적에 맞게 소매 데이터 샘플은 Azure Blob 스토리지에 저장됩니다. 다음 코드를 새 셀에 붙여넣어 Azure Cosmos 컨테이너로 가져올 수 있습니다. 항목 수를 선택하는 쿼리를 실행하여 데이터를 성공적으로 가져오는지 확인할 수 있습니다.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   앞의 쿼리를 실행하면 다음 출력이 반환됩니다.

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>DataFrame에 데이터 가져오기

쿼리를 실행하여 데이터를 분석하기 전에 분석을 위해 데이터를 컨테이너에서 [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)으로 읽을 수 있습니다. 다음 sql 매직 명령을 사용하여 데이터를 DataFrame으로 읽습니다.

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

자세한 내용은 [Azure Cosmos DB의 기본 제공 Notebook 명령 및 기능](use-python-notebook-features-and-commands.md) 문서를 참조하세요. `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` 쿼리를 실행합니다. 결과는 df_cosmos라는 Pandas DataFrame에 저장됩니다. 다음 명령을 새 Notebook 셀에 붙여넣고 실행합니다.

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

새 Notebook 셀에서 다음 코드를 실행하여 출력에서 처음 10개의 항목을 읽습니다.

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="쿼리를 실행하여 상위 10개 항목 가져오기":::

## <a name="run-queries-and-analyze-your-data"></a>쿼리 실행 및 데이터 분석

이 섹션에서는 검색된 데이터에 대해 몇 가지 쿼리를 실행합니다.

* **쿼리 1**: DataFrame에 대해 Group by query를 실행하여 각 국가/지역의 총 매출 수익 합계를 가져오고 결과에서 5개 항목을 표시합니다. 새 Notebook 셀에서 다음 코드를 실행합니다.

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="총 매출 수익 출력":::

* **쿼리 2:** 구매한 상위 5개 항목의 목록을 가져오려면 새 Notebook 셀을 열고 다음 코드를 실행합니다.

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="상위 5개 구매 항목":::

## <a name="visualize-your-data"></a>데이터 시각화  

1. 이제 Azure Cosmos 컨테이너의 수익에 대한 데이터가 있으므로 선택한 시각화 라이브러리를 사용하여 데이터를 시각화할 수 있습니다. 이 자습서에서는 Bokeh 라이브러리를 사용합니다. 새 Notebook 셀을 열고, 다음 코드를 실행하여 Bokeh 라이브러리를 설치합니다. 모든 요구 사항이 충족되면 라이브러리가 설치됩니다.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. 다음으로, 데이터를 지도에 그릴 준비를 합니다. Azure Cosmos DB의 데이터를 Azure Blob 스토리지에 있는 국가/지역 정보와 조인하고, 결과를 GeoJSON 형식으로 변환합니다. 다음 코드를 새 Notebook 셀에 복사하여 실행합니다.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. 새 Notebook 셀에서 다음 코드를 실행하여 세계 지도에서 여러 국가/지역의 매출 수익을 시각화합니다.

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   출력에 여러 색을 사용한 세계 지도가 표시됩니다. 색이 짙을수록 더 높은 수익의 국가/지역을 나타냅니다.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="국가/지역별 수익 지도 시각화":::

1. 데이터 시각화의 또 다른 사례를 살펴보겠습니다. WebsiteData 컨테이너에는 항목을 보고, 카트에 추가하고, 해당 항목을 구매한 사용자의 레코드가 있습니다. 구매한 항목의 전환율을 그려보겠습니다. 새 셀에서 다음 코드를 실행하여 각 항목의 전환율을 시각화합니다.

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="구매 전환율 시각화":::

## <a name="next-steps"></a>다음 단계

* Python Notebook 명령에 대한 자세한 내용은 [Azure Cosmos DB에서 기본 제공 Notebook 명령과 기능을 사용하는 방법](use-python-notebook-features-and-commands.md) 문서를 참조하세요.
