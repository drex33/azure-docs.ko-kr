---
title: Azure Cosmos DB Python Notebooks(미리 보기)의 기본 제공 Notebook 명령 및 기능 사용
description: 기본 제공 명령 및 기능을 사용하여 Azure Cosmos DB의 기본 제공 Python Notebooks를 사용하는 일반적인 작업을 수행하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: d7afa12102f6a808645c56b9670cb1b407f9f4d3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117193"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Azure Cosmos DB Python Notebooks(미리 보기)의 기본 제공 Notebook 명령 및 기능 사용
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB의 기본 제공 Jupyter Notebook을 사용하여 Azure Portal에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 기본 제공 Notebook 명령 및 기능을 사용하여 Python Notebooks에서 일반적인 작업을 수행하는 방법을 설명합니다.

## <a name="install-a-new-package"></a>새 패키지 설치
Azure Cosmos 계정에 대해 Notebook 지원을 사용하도록 설정한 후 새 Notebook을 열고 패키지를 설치할 수 있습니다.

새 코드 셀에서 다음 코드를 삽입하고 실행하여 ``PackageToBeInstalled``를 원하는 Python 패키지로 바꿉니다.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Azure Cosmos 계정 작업 영역의 모든 Notebook에서 이 패키지를 사용할 수 있습니다. 

> [!TIP]
> Notebook에서 사용자 지정 패키지를 필요로 하는 경우에는 [작업 영역을 다시 설정](#reset-notebooks-workspace)하는 경우 패키지가 제거되므로 Notebook에 셀을 추가하여 패키지를 설치하는 것이 좋습니다.  

## <a name="run-a-sql-query"></a>SQL 쿼리 실행

``%%sql`` 매직 명령을 사용하여 계정의 모든 컨테이너에 대해 [SQL 쿼리](sql-query-getting-started.md)를 실행할 수 있습니다. 다음 구문을 사용합니다.

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` 및 ``{container_id}``를 Cosmos 계정에 있는 데이터베이스 및 컨테이너의 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공하지 않으면 쿼리는 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에 대해 실행됩니다.
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다.

다음은 그 예입니다. 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
셀에서 ```%%sql?```를 실행하여 Notebook의 sql 매직 명령에 대한 도움말 설명서를 확인합니다.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL 쿼리를 실행하고 Pandas 데이터 프레임에 출력합니다.

``%%sql`` 쿼리의 결과를 [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)에 출력할 수 있습니다. 다음 구문을 사용합니다. 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` 및 ``{container_id}``를 Cosmos 계정에 있는 데이터베이스 및 컨테이너의 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공하지 않으면 쿼리는 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에 대해 실행됩니다.
- ``{outputDataFrameVar}``을 결과를 포함하는 데이터 프레임 변수 이름으로 바꿉니다.
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다. 

다음은 그 예입니다.

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>쿼리에 대한 기본 데이터베이스 설정
```%%sql``` 명령에서 Notebook에 사용할 기본 데이터베이스를 설정할 수 있습니다. ```{database_id}```를 해당 데이터베이스의 이름으로 바꿉니다.

```python
%database {database_id}
```
셀에서 ```%database?```를 실행하여 Notebook의 설명서를 확인합니다.

## <a name="set-default-container-for-queries"></a>쿼리에 대한 기본 컨테이너 설정
```%%sql``` 명령에서 Notebook에 사용할 기본 컨테이너를 설정할 수 있습니다. ```{container_id}```를 컨테이너의 이름으로 바꿉니다.

```python
%container {container_id}
```
셀에서 ```%container?```를 실행하여 Notebook의 설명서를 확인합니다.

## <a name="upload-json-items-to-a-container"></a>컨테이너에 JSON 항목 업로드
``%%upload`` 매직 명령을 사용하여 JSON 파일에서 지정된 Azure Cosmos 컨테이너로 데이터를 업로드할 수 있습니다. 다음 명령을 실행하여 다음 항목을 업로드합니다.

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` 및 ``{container_id}``를 Azure Cosmos 계정에 있는 데이터베이스 및 컨테이너의 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공하지 않으면 쿼리는 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에 대해 실행됩니다.
- ``{url_location_of_file}``을 JSON 파일의 위치로 바꿉니다. 파일은 유효한 JSON 개체의 배열이어야 하며 공용 인터넷을 통해 액세스할 수 있어야 합니다.

다음은 그 예입니다.

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
출력 통계를 사용하여 항목을 업로드하는 데 사용되는 유효 RU/s를 계산할 수 있습니다. 예를 들어 38초를 초과하여 25000 RUs를 사용하는 경우 유효 RU/s는 25000RUs/38초 = 658RU/s입니다.

파일(예: CSV 또는 JSON 파일)을 로컬 노트북 작업 영역에 저장할 수 있습니다. 파일을 저장하려면 노트북에 셀을 추가하는 것이 좋습니다. 이러한 파일은 노트북 환경의 통합 터미널에서 볼 수 있습니다. "ls" 명령을 사용하여 저장된 파일을 볼 수 있습니다. 그러나 작업 영역을 초기화하면 이러한 파일이 제거됩니다. 따라서 로컬 작업 영역 대신 GitHub 또는 스토리지 계정과 같은 영구 스토리지를 사용하는 것이 가장 좋습니다.

## <a name="run-another-notebook-in-current-notebook"></a>현재 Notebook에서 다른 Notebook 실행 
``%%run`` 매직 명령을 사용하여 현재 Notebook의 작업 영역에서 다른 Notebook을 실행할 수 있습니다. 다음 구문을 사용합니다.

```python
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}``을 실행하려는 Notebook의 이름으로 바꿉니다. Notebook은 현재 'My Notebooks' 작업 영역에 있어야 합니다. 

## <a name="use-built-in-nteract-data-explorer"></a>기본 제공 nteract 데이터 탐색기 사용
기본 제공 [nteract 데이터 탐색기](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)를 사용하여 데이터 프레임을 필터링하고 시각화할 수 있습니다. 이 기능을 사용하도록 설정하려면 ``pd.options.display.html.table_schema`` 옵션을 ``True``로, ``pd.options.display.max_rows``를 원하는 값으로 설정합니다. 모든 결과를 표시하도록 ``pd.options.display.max_rows``를 ``None``으로 설정할 수 있습니다.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="nteract 데이터 탐색기":::

## <a name="use-the-built-in-python-sdk"></a>기본 제공 Python SDK 사용
[Azure Cosmos DB Python SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 버전 4는 Azure Cosmos 계정에 대한 Notebook 환경에 설치되어 포함됩니다.

기본 제공 ``cosmos_client`` 인스턴스를 사용하여 모든 SDK 작업을 실행합니다. 

다음은 그 예입니다.

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
[Python SDK 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)을 참조하세요. 

> [!IMPORTANT]
> 기본 제공 Python SDK는 SQL(Core) API 계정에 대해서만 지원됩니다. 다른 API의 경우 API에 해당하는 [관련 Python 드라이버를 설치](#install-a-new-package)해야 합니다. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>``cosmos_client``의 사용자 지정 인스턴스 만들기
더 많은 유연성을 위해 다음과 같은 작업을 위해 ``cosmos_client``의 사용자 지정 인스턴스를 만들 수 있습니다.

- [연결 정책](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview) 사용자 지정
- 현재 사용자가 사용 중인 계정이 아닌 다른 Azure Cosmos 계정에 대해 작업 실행

[환경 변수](#access-the-account-endpoint-and-primary-key-env-variables)를 통해 현재 계정의 연결 문자열 및 기본 키에 액세스할 수 있습니다. 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>계정 엔드포인트 및 기본 키 env 변수에 액세스
Notebook이 있는 계정의 기본 제공 엔드포인트 및 키에 액세스할 수 있습니다.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> ``COSMOS.ENDPOINT`` 및 ``COSMOS.KEY`` 변수는 SQL API에만 적용됩니다. 다른 API의 경우 Azure Cosmos 계정의 **연결 문자열** 또는 **키** 블레이드에서 엔드포인트 및 키를 찾습니다.  

## <a name="reset-notebooks-workspace"></a>Notebook 작업 영역 다시 설정
Notebook 작업 영역을 기본 설정으로 다시 설정하려면 명령 모음에서 **작업 영역 다시 설정** 을 선택합니다. 그러면 설치된 모든 사용자 지정 패키지가 제거되고 Jupyter 서버가 다시 시작됩니다. Notebook, 파일 및 Azure Cosmos 리소스는 영향을 받지 않습니다.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Notebook 작업 영역 다시 설정":::

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB Jupyter Notebooks](../cosmosdb-jupyter-notebooks.md)의 이점에 대해 알아보기
- [Azure Cosmos DB Python SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)에 대해 알아보기