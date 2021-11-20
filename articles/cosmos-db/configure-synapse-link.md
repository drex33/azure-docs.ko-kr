---
title: Azure Cosmos DB용 Azure Synapse Link 구성 및 사용
description: Azure Cosmos DB 계정에 대해 Synapse 링크를 사용하도록 설정하고, 분석 저장소가 사용하도록 설정된 컨테이너를 만들고, Azure Cosmos 데이터베이스를 Synapse 작업 영역에 연결하고, 쿼리를 실행하는 방법을 알아봅니다.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 4ec6a93a8954b995aece5110da39635be3e350cc
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868398"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Synapse Link 구성 및 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)는 클라우드 네이티브 HTAP(하이브리드 트랜잭션 및 분석 처리) 기능으로, 이를 통해 Microsoft Azure Cosmos DB의 작동 데이터에 대해 근 실시간 분석을 실행할 수 있습니다. Synapse Link를 통해 Azure Cosmos DB와 Azure Synapse Analytics가 긴밀하게 통합됩니다.

Azure Synapse 링크는 Azure Cosmos DB SQL api 또는 mongo DB 계정에 대 한 Azure Cosmos DB api에 사용할 수 있습니다. Azure Cosmos DB용 Azure Synapse Link를 사용하여 분석 쿼리를 실행하려면 다음 단계를 수행합니다.

* [Azure Cosmos DB 계정에 대해 Azure Synapse 링크를 사용 하도록 설정](#enable-synapse-link)
* [분석 저장소 지원 컨테이너 생성](#create-analytical-ttl)
* [기존 컨테이너에서 분석 저장소 사용](#update-analytical-ttl)
* [선택 사항-컨테이너에 대 한 분석 저장소 ttl 업데이트](#update-analytical-ttl)
* [azure Synapse 작업 영역에 azure Cosmos 데이터베이스 커넥트](#connect-to-cosmos-database)
* [Azure Synapse Spark 풀을 사용 하 여 분석 저장소 쿼리](#query-analytical-store-spark)
* [Azure Synapse 서버 리스 SQL 풀을 사용 하 여 분석 저장소 쿼리](#query-analytical-store-sql-on-demand)
* [Azure Synapse 서버 리스 SQL 풀을 사용 하 여 Power BI에서 데이터 분석 및 시각화](#analyze-with-powerbi)

[Azure Cosmos DB용 Azure Synapse Link를 구성](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/)하는 방법에 대한 학습 모듈을 확인할 수도 있습니다.

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos DB 계정에 대해 Azure Synapse Link를 사용하도록 설정

> [!NOTE]
> Azure Synapse Link에서 고객 관리형 키를 사용하려면 계정에서 Synapse Link를 사용하기 전에 Azure Key Vault 액세스 정책에서 계정의 관리 ID를 구성해야 합니다. 자세한 내용은 [Azure Cosmos DB 계정의 관리 ID를 사용하여 고객 관리형 키 구성](how-to-setup-cmk.md#using-managed-identity) 문서를 참조하세요.

> [!NOTE]
> SQL(CORE) API 계정에 대한 전체 충실도 스키마를 사용하려는 경우 Azure Portal에서 Synapse Link를 사용하도록 설정할 수 없습니다. 계정에서 Synapse Link가 사용하도록 설정된 후에는 이 옵션을 변경할 수 없으며 이를 설정하려면 Azure CLI 또는 PowerShell을 사용해야 합니다. 자세한 내용은 [분석 저장소 스키마 표현 문서](analytical-store-introduction.md#schema-representation)를 확인하세요. 

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos DB 계정을 선택합니다.

1. Azure Cosmos DB 계정으로 이동하여 **기능** 창을 엽니다.

1. 기능 목록에서 **Synapse Link** 를 선택합니다.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Synapse Link 기능 찾기":::

1. 그런 다음, 계정에서 synapse link를 사용하도록 설정하라는 메시지를 표시합니다. **사용** 을 선택합니다. 이 프로세스는 완료하는 데 1-5분 정도 걸릴 수 있습니다.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Synapse Link 기능 사용":::

1. 이제 계정이 Synapse Link를 사용하도록 설정되었습니다. 다음으로 트랜잭션 저장소에서 분석 저장소로 작업 데이터 복제를 자동으로 시작하도록 분석 저장소 사용 컨테이너를 만드는 방법을 참조하세요.

> [!NOTE]
> Synapse Link를 켜도 분석 저장소가 자동으로 켜지지 않습니다. Cosmos DB 계정에서 Synapse 링크를 사용 하도록 설정 했으면 컨테이너에서 분석 저장소를 사용 하도록 설정 하 여 Synapse 링크를 시작 합니다. 

### <a name="command-line-tools"></a>명령줄 도구

Azure CLI 또는 PowerShell을 사용 하 여 Cosmos DB SQL api 또는 MongoDB api 계정에서 Synapse 링크를 사용 하도록 설정 합니다.

#### <a name="azure-cli"></a>Azure CLI

`--enable-analytical-storage true` **만들기** 또는 **업데이트** 작업 모두에 사용 합니다. 또한 표현 스키마 유형을 선택 해야 합니다. SQL API 계정의 경우 `--analytical-storage-schema-type` 또는 값과 함께 사용할 수 `FullFidelity` 있습니다 `WellDefined` . MongoDB API 계정의 경우 항상를 사용 `--analytical-storage-schema-type FullFidelity` 합니다.

* [Synapse Link가 사용으로 설정된 새 Azure Cosmos DB 계정 만들기](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [Synapse Link를 사용하도록 기존 Azure Cosmos DB 계정 업데이트](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

#### <a name="powershell"></a>PowerShell

`EnableAnalyticalStorage true` **만들기** 또는 **업데이트** 작업 모두에 사용 합니다. 또한 표현 스키마 유형을 선택 해야 합니다. SQL API 계정의 경우 `--analytical-storage-schema-type` 또는 값과 함께 사용할 수 `FullFidelity` 있습니다 `WellDefined` . MongoDB API 계정의 경우 항상를 사용 `-AnalyticalStorageSchemaType FullFidelity` 합니다.

* [Synapse Link가 사용으로 설정된 새 Azure Cosmos DB 계정 만들기](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [Synapse Link를 사용하도록 기존 Azure Cosmos DB 계정 업데이트](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


## <a name="create-an-analytical-store-enabled-container"></a><a id="create-analytical-ttl"></a> 분석 저장소를 사용 하도록 설정 된 컨테이너 만들기

다음 옵션 중 하나를 사용 하 여 Azure Cosmos DB 컨테이너를 만들 때 분석 저장소를 켤 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. **새 컨테이너** 를 선택하고 데이터베이스, 컨테이너, 파티션 키 및 처리량 정보에 대한 이름을 입력합니다. **분석 저장소** 옵션을 설정합니다. 분석 저장소를 사용하도록 설정하면 `analytical TTL` 속성이 기본값 -1(무한 보존)로 설정된 컨테이너가 만들어집니다. 이 분석 저장소는 레코드의 모든 기록 버전을 유지 하 고 나중에 변경할 수 있습니다.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos DB 컨테이너에 대 한 분석 저장소 설정":::

1. 이 계정에 대해 이전에 Synapse Link를 사용하도록 설정하지 않은 경우 분석 저장소를 사용하도록 설정된 컨테이너를 만들기 위한 필수 구성 요소이므로 그렇게 하라는 메시지가 표시됩니다. 메시지가 표시되면 **Synapse Link 사용** 을 선택합니다. 이 프로세스는 완료하는 데 1-5분 정도 걸릴 수 있습니다.

1. **확인** 을 선택 하 여 Azure Cosmos DB 컨테이너를 사용 하도록 설정 된 분석 저장소를 만듭니다.

1. 컨테이너를 만든 다음, 데이터 탐색기의 문서 바로 아래에 있는 **설정** 을 클릭하여 분석 저장소가 사용되었는지 확인하고 **분석 저장소 TTL(Time To Live)** 옵션이 켜져 있는지 확인합니다.

### <a name="azure-cosmos-db-sdks"></a>Azure Cosmos DB SDK

속성을 `analytical TTL` 필요한 값으로 설정 하 여 분석 저장소를 사용 하도록 설정 된 컨테이너를 만듭니다. 허용 되는 값 목록은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조 하세요.

#### <a name="net-sdk"></a>.NET SDK

다음 코드는 .NET SDK를 사용하여 분석 저장소와 함께 컨테이너를 만듭니다. 속성을 `AnalyticalStoreTimeToLiveInSeconds` 필요한 값 (초)으로 설정 하거나 `-1` 무한 보존에 사용 합니다. 이 설정은 나중에 변경할 수 있습니다.

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

다음 코드는 Java V4 SDK를 사용하여 분석 저장소와 함께 컨테이너를 만듭니다. 속성을 `AnalyticalStoreTimeToLiveInSeconds` 필요한 값 (초)으로 설정 하거나 `-1` 무한 보존에 사용 합니다. 이 설정은 나중에 변경할 수 있습니다.


```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

#### <a name="python-v4-sdk"></a>Python V4 SDK

다음 코드는 Python V4 SDK를 사용 하 여 분석 저장소를 사용 하 여 컨테이너를 만듭니다. 속성을 `analytical_storage_ttl` 필요한 값 (초)으로 설정 하거나 `-1` 무한 보존에 사용 합니다. 이 설정은 나중에 변경할 수 있습니다.

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos as cosmos
import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

# Client
client = cosmos_client.CosmosClient(HOST,  KEY )

# Database client
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled
try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```
### <a name="command-line-tools"></a>명령줄 도구

속성을 `analytical TTL` 필요한 값으로 설정 하 여 분석 저장소를 사용 하도록 설정 된 컨테이너를 만듭니다. 허용 되는 값 목록은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조 하세요.

#### <a name="azure-cli"></a>Azure CLI

다음 옵션은 Azure CLI를 사용 하 여 분석 저장소가 있는 컨테이너를 만듭니다. 속성을 `--analytical-storage-ttl` 필요한 값 (초)으로 설정 하거나 `-1` 무한 보존에 사용 합니다. 이 설정은 나중에 변경할 수 있습니다.

* [Azure Cosmos DB MongoDB collection 만들기](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Azure Cosmos DB SQL API 컨테이너 만들기](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) 

#### <a name="powershell"></a>PowerShell

다음 옵션은 PowerShell을 사용 하 여 분석 저장소가 있는 컨테이너를 만듭니다. 속성을 `-AnalyticalStorageTtl` 필요한 값 (초)으로 설정 하거나 `-1` 무한 보존에 사용 합니다. 이 설정은 나중에 변경할 수 있습니다.

* [Azure Cosmos DB MongoDB collection 만들기](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Azure Cosmos DB SQL API 컨테이너 만들기](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer)


## <a name="enable-analytical-store-on-an-existing-container"></a><a id="update-analytical-ttl"></a> 기존 컨테이너에서 분석 저장소 사용

> [!NOTE]
> 단기 용량 제약 조건으로 인해 기존 컨테이너에서 Synapse 링크를 사용 하도록 등록 해야 합니다. 보류 중인 요청에 따라이 요청을 승인 하는 일은 한 주에서 1 주일까지 걸릴 수 있습니다. 요청 상태를 확인하는 지침은 다음과 같습니다. 이 단계는 구독당 한 번씩 필요하며 모든 새 데이터베이스 계정에서도 이 기능을 사용할 수 있습니다. 기존 컨테이너 기능을 사용하도록 요청을 등록하려면 구독에 대한 **기여자** 또는 **관리자** Azure 기본 제공 역할이 필요합니다. 문제 또는 질문이 있는 경우 에 [cosmosdbsynapselink@microsoft.com](mailto:cosmosdbsynapselink@microsoft.com) 문의하세요.

> [!NOTE]
> 기존 Azure Cosmos DB SQL API 컨테이너에서 분석 저장소를 설정할 수 있습니다. 이 기능은 일반 사용 가능하며 프로덕션 워크로드에 사용할 수 있습니다.

 기존 컨테이너에서 Synapse Link 사용하도록 설정할 때 다음 세부 정보를 확인하세요.

* 분석 저장소 자동 동기화 프로세스의 동일한 성능 격리는 초기 동기화에 적용되며 OLTP 워크로드에 성능에 영향을 미치지 않습니다.

* 분석 저장소와 컨테이너의 초기 동기화 총 시간은 데이터 볼륨 및 문서 복잡성에 따라 달라집니다. 이 프로세스는 몇 초에서 며칠까지 걸릴 수 있습니다. Azure Portal 사용하여 마이그레이션 진행 상황을 모니터링하세요.

* 컨테이너 또는 데이터베이스 계정의 처리량도 총 초기 동기화 시간에 영향을 줍니다. 이 마이그레이션에는 RU/s가 사용되지 않지만 사용 가능한 총 RU/s는 프로세스의 성능에 영향을 미칩니다. 프로세스 속도를 높이기 위해 환경의 사용 가능한 CPU를 일시적으로 늘릴 수 있습니다.

* 해당 컨테이너에서 Synapse Link 사용하는 동안에는 기존 컨테이너의 분석 저장소를 쿼리할 수 없습니다. OLTP 워크로드는 영향을 받지 않으며 데이터를 정상적으로 읽을 수 있습니다. 초기 동기화가 시작된 후 수집된 데이터는 일반 분석 저장소 자동 동기화 프로세스에 의해 분석 저장소에 병합됩니다.

* 현재 기존 MongoDB API 컬렉션은 지원되지 않습니다. 대신 분석 저장소가 켜져 있는 새 컬렉션으로 데이터를 마이그레이션할 수 있습니다.
 
> [!NOTE]
> 현재는 컨테이너에서 분석 저장소를 해제할 수 없습니다. 분석 저장소 가격 책정에 대한 자세한 내용을 보려면 [여기를](analytical-store-introduction.md#analytical-store-pricing) 클릭하세요.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.
2. Azure Cosmos DB 계정으로 이동하고 통합 섹션에서 **Synapse Link"** **탭을** 엽니다. 이 탭에서는 다음을 수행할 수 있습니다.
3. **등록을** 클릭하여 구독에 대한 승인을 요청합니다. 요청 상태를 보려면 동일한 포털 창으로 돌아가세요.
4. 승인되면 계정의 컨테이너 목록이 표시되고 분석 저장소를 사용하도록 설정할 컨테이너 목록을 선택할 수 있습니다.
5. 필요에 따라 **통합** 섹션의 **Power BI** 탭으로 이동하여 Synapse Link 사용하도록 설정된 컨테이너에 Power BI 대시보드를 만들 수 있습니다.


### <a name="command-line-tools"></a>명령줄 도구

속성을 `analytical TTL` 필요한 값으로 설정하여 분석 저장소 사용 컨테이너를 만듭니다. 허용되는 값 목록은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.


### <a name="azure-cli"></a>Azure CLI

Azure CLI 사용하여 기존 컨테이너에서 분석 저장소를 사용하도록 설정하려면 다음 단계를 사용합니다. 속성을 `--analytical-storage-ttl` 필요한 값(초)으로 설정하거나 `-1` 무한 보존에 사용합니다. 이 설정은 나중에 변경할 수 있습니다.

* [을](/cli/azure/feature/registration) 사용하여 승인을 `az feature registration create --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration` 등록합니다. 
* [를](/cli/azure/feature/registration) 사용하여 요청 상태를 확인합니다. `az feature registration show --namespace Microsoft.DocumentDB --name AnalyticalStoreMigration`
* 요청 승인 후 [분석 ttl을](/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_update&preserve-view=true) 로 업데이트합니다. `-1`
* Azure Portal 마이그레이션 상태를 확인합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 기존 컨테이너에서 분석 저장소를 사용하도록 설정하려면 다음 단계를 사용합니다. 속성을 `-AnalyticalStorageTtl` 필요한 값(초)으로 설정하거나 `-1` 무한 보존에 사용합니다. 이 설정은 나중에 변경할 수 있습니다.

* [을](/powershell/module/az.resources/register-azproviderfeature) 사용하여 승인을 `Register-AzProviderFeature -ProviderName "Microsoft.DocumentDB" -FeatureName "AnalyticalStoreMigration"` 등록합니다.
* [요청 상태를 확인합니다.](/powershell/module/az.resources/get-azproviderfeature)
* 요청 승인 후 [분석 ttl을](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer) 로 업데이트합니다. `-1`
* Azure Portal 마이그레이션 상태를 확인합니다.



## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 선택 사항 - 분석 저장소 TTL(Time to Live) 업데이트

분석 저장소를 특정 TTL 값으로 사용하도록 설정한 후에는 다른 유효한 값으로 업데이트할 수 있습니다. Azure portal, Azure CLI, PowerShell 또는 Cosmos DB SDK를 사용하여 값을 업데이트할 수 있습니다. 다양한 분석 TTL 구성 옵션에 대한 자세한 내용은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.


### <a name="azure-portal"></a>Azure portal

Azure Portal 통해 분석 저장소 사용 컨테이너를 만든 경우 기본값이 `analytical TTL` `-1` 포함됩니다. 다음 단계를 사용하여 이 값을 업데이트합니다.

1. [Azure Portal](https://portal.azure.com/) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.
1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.
1. 분석 저장소가 사용하도록 설정된 기존 컨테이너를 선택합니다. 확장하고 다음 값을 수정합니다.
   1. **배율 및 설정** 창을 엽니다.
   1. **설정** 에서 **분석 스토리지 Time to Live** 를 찾습니다.
   1. **설정(기본값 없음)** 를 선택하거나 **켜기** 를 선택하고 TTL 값을 설정합니다.
   1. **저장** 을 클릭하여 변경 내용을 저장합니다.


### <a name="net-sdk"></a>.NET SDK

다음 코드는 .NET SDK를 사용하여 분석 저장소에 대한 TTL을 업데이트하는 방법을 보여 줍니다.

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

다음 코드에서는 Java V4 SDK를 사용하여 분석 저장소에 대한 TTL을 업데이트하는 방법을 보여 줍니다.

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

현재 지원되지 않습니다.


### <a name="azure-cli"></a>Azure CLI

다음 링크는 Azure CLI를 사용하여 컨테이너 분석 TTL을 업데이트하는 방법을 보여 줍니다.

* [Azure Cosmos DB API for Mongo DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

다음 링크는 PowerShell을 사용하여 컨테이너 분석 TTL을 업데이트하는 방법을 보여 줍니다.

* [Azure Cosmos DB API for Mongo DB](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [Azure Cosmos DB SQL API](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Synapse 작업 영역에 연결

Azure Synapse Link를 사용하여 Azure Synapse Analytics Studio에서 Azure Cosmos DB 데이터베이스에 액세스하는 방법에 대해 [Azure Synapse Link에 연결](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md)의 지침을 사용합니다.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Azure Synapse Analytics용 Apache Spark를 사용하여 분석 저장소 쿼리

Synapse Spark 3를 사용하여 쿼리하는 방법에 대해 [Spark 3를 사용하여 Azure Cosmos DB 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md) 문서의 지침을 사용합니다. 이 문서에서는 Synapse 제스처에서 분석 저장소와 상호 작용하는 방법에 대한 몇 가지 예를 제공합니다. 컨테이너를 마우스 오른쪽 단추로 클릭하면 해당 제스처가 표시됩니다. 제스처를 사용하면 코드를 빠르게 생성하고 필요에 맞게 조정할 수 있습니다. 제스처는 한 번의 클릭으로 데이터를 검색하는 데에도 적합합니다.

Spark 2 통합의 경우 [Spark 2를 사용하여 Azure Cosmos DB 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) 문서에 있는 지침을 사용하세요.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 분석 저장소 쿼리

서버리스 SQL 풀을 사용하면 Azure Synapse Link로 활성화된 Azure Cosmos DB 컨테이너의 데이터를 쿼리하고 분석할 수 있습니다. 트랜잭션 워크로드의 성능에 영향을 주지 않고 근 실시간으로 데이터를 분석할 수 있습니다. 분석 저장소에서 데이터를 쿼리하는 친숙한 T-SQL 구문을 제공하고 T-SQL 인터페이스를 통해 광범위한 BI 및 임시 쿼리 도구에 대한 통합 연결을 제공합니다. 자세한 내용은 [서버리스 SQL 풀을 사용하여 분석 저장소 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) 문서를 참조하세요.

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>서버리스 SQL 풀을 사용하여 Power BI에서 데이터 분석 및 시각화

Azure Cosmos DB용 Synapse Link를 통해 서버리스 SQL 풀 데이터베이스와 보기를 만들 수 있습니다. 나중에 Azure Cosmos DB 컨테이너를 쿼리한 다음, 해당 쿼리를 반영하기 위해 해당 보기에 대해 Power BI 사용하여 모델을 빌드할 수 있습니다. 트랜잭션 워크로드에 대한 성능 또는 비용 영향은 없으며 ETL 파이프라인 관리의 복잡성도 없습니다. [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 또는 [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 모드를 사용할 수 있습니다. 자세한 내용은 [Synapse Link로 Azure Cosmos DB 데이터를 분석하기 위한 서버리스 SQL 풀](synapse-link-power-bi.md) 사용 방법 문서를 참조하세요.

## <a name="configure-custom-partitioning"></a>사용자 지정 분할 구성

사용자 지정 분할을 사용하면 분석 쿼리에서 일반적으로 필터로 사용되는 필드에 분석 저장소 데이터를 분할하여 쿼리 성능을 개선할 수 있습니다.자세한 내용은 [사용자 지정 분할 소개](custom-partitioning-analytical-store.md) 및 [사용자 지정 분할 구성 방법](configure-custom-partitioning.md) 문서를 참조하세요.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

[Azure Resource Manager 템플릿](./manage-with-templates.md#azure-cosmos-account-with-analytical-store)은 SQL API용 Synapse Link 사용 Azure Cosmos DB 계정을 만듭니다. 이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 통해 한 지역에 Core(SQL) API 계정을 만듭니다. 이 템플릿을 배포하려면 추가 정보 페이지에서 **Azure에 배포** 를 클릭합니다.

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Azure Synapse Link 시작 - 샘플

[GitHub](https://aka.ms/cosmosdb-synapselink-samples)에서 Azure Synapse Link를 시작하기 위한 샘플을 찾을 수 있습니다. 이 샘플은 IoT 및 소매 시나리오를 사용하여 엔드투엔드 솔루션을 보여 줍니다. [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) 폴더에 있는 같은 리포지토리의 Azure Cosmos DB API for MongoDB에 해당하는 샘플도 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Cosmos DB용 Azure Synapse Link 구성](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/) 방법에 대한 학습 모듈을 확인하세요.

* [Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)

* [Azure Cosmos DB용 Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)

* [Azure Synapse Analytics의 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics의 서버리스 SQL 풀 런타임 지원](../synapse-analytics/sql/on-demand-workspace-overview.md).
