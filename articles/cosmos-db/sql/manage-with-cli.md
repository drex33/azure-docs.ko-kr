---
title: Azure CLI를 사용하여 Azure Cosmos DB Core(SQL) API 리소스 관리
description: Azure CLI를 사용하여 Azure Cosmos DB Core(SQL) API 리소스를 관리합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/13/2021
ms.author: mjbrown
ms.openlocfilehash: c2b383a64699d6e4b497d8653e69f401886db21a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117185"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos Core (SQL) API 리소스 관리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure CLI를 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너 관리를 자동화하는 일반 명령에 대해 설명합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](/cli/azure/cosmosdb)에서 제공됩니다. 더 많은 예제는 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 확인할 수 있습니다. 여기에는 MongoDB, Gremlin, Cassandra 및 Table API에 대한 Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기 및 관리 방법이 포함되어 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서대로 하려면 Azure CLI 버전 2.22.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

다른 API에 대한 Azure CLI 샘플은 [Cassandra용 CLI 샘플](../cassandra/cli-samples.md), [MongoDB API용 CLI 샘플](../mongodb/cli-samples.md), [Gremlin용 CLI 샘플](../graph/cli-samples.md), [Table용 CLI 샘플](../table/cli-samples.md)을 참조하세요.

> [!IMPORTANT]
> Azure Resource Manager가 리소스 URI와 함께 작동하는 방식을 위반하므로 Azure Cosmos DB 리소스의 이름을 바꿀 수 없습니다.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 계정

다음 섹션에서는 아래 작업을 포함하여 Azure Cosmos 계정을 관리하는 방법을 보여 줍니다.

* [Azure Cosmos 계정 만들기](#create-an-azure-cosmos-db-account)
* [영역 추가 또는 제거](#add-or-remove-regions)
* [다중 영역 쓰기 사용](#enable-multiple-write-regions)
* [지역 장애 조치(failover) 우선 순위 설정](#set-failover-priority)
* [자동 장애 조치(failover) 사용](#enable-automatic-failover)
* [수동 장애 조치(failover) 트리거](#trigger-manual-failover)
* [계정 키 나열](#list-account-keys)
* [읽기 전용 계정 키 나열](#list-read-only-account-keys)
* [연결 문자열 나열](#list-connection-strings)
* [계정 키 다시 생성](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

미국 서부 2 및 미국 동부 2 지역에서 SQL API, 세션 일관성을 사용하여 Azure Cosmos DB 계정을 만듭니다.

> [!IMPORTANT]
> Azure Cosmos 계정 이름은 소문자여야 하고 44자 미만이어야 합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>영역 추가 또는 제거

두 개의 지역이 있는 Azure Cosmos 계정을 만들고 지역을 추가하고 지역을 제거합니다.

> [!NOTE]
> `locations` 영역을 추가하거나 제거하면서 동시에 Azure Cosmos 계정에 대한 다른 속성을 변경할 수 없습니다. 지역 수정은 계정 리소스에 대한 다른 변경 사항보다 별도의 작업으로 수행해야 합니다.
> [!NOTE]
> 이 명령을 사용하면 지역을 추가 및 제거할 수 있지만 장애 조치(failover) 우선 순위를 수정하거나 수동 장애 조치(failover)를 트리거할 수 없습니다. [장애 조치(failover) 우선 순위 설정](#set-failover-priority) 및 [수동 장애 조치(failover) 트리거](#trigger-manual-failover)를 참조하세요.
> [!TIP]
> 새 지역이 추가되면 해당 지역이 사용 가능으로 표시되기 전에 모든 데이터를 완전히 복제하고 새 지역에 커밋해야 합니다. 이 작업에 걸리는 시간은 해당 계정 내에 저장된 데이터 양에 따라 달라집니다.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>다중 쓰기 지역 사용

Cosmos 계정에 대한 다중 지역 쓰기 사용

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>장애 조치(failover) 우선 순위 설정

자동 장애 조치(failover)를 위해 구성된 Azure Cosmos 계정에 대한 장애 조치(failover) 우선 순위 설정

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a> 자동 장애 조치(failover) 사용

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>수동 장애 조치(failover) 트리거

> [!CAUTION]
> 우선 순위가 0인 지역을 변경하면 Azure Cosmos 계정에 대한 수동 장애 조치(failover)가 트리거됩니다. 다른 우선 순위 변경은 장애 조치(failover)를 트리거하지 않습니다.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> 모든 계정 키 나열

Cosmos 계정에 대한 모든 키를 가져옵니다.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>읽기 전용 계정 키 나열

Cosmos 계정에 대한 읽기 전용 키를 가져옵니다.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>연결 문자열 나열

Cosmos 계정에 대한 연결 문자열을 가져옵니다.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>계정 키 다시 생성

Cosmos 계정의 새 키를 다시 생성합니다.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB 데이터베이스

다음 섹션에서는 Azure Cosmos DB 데이터베이스를 관리하는 방법을 보여 줍니다.

* [데이터베이스 만들기](#create-a-database)
* [공유 처리량을 사용하여 데이터베이스 만들기](#create-a-database-with-shared-throughput)
* [데이터베이스를 자동 크기 조정 처리량으로 마이그레이션](#migrate-a-database-to-autoscale-throughput)
* [데이터베이스 처리량 변경](#change-database-throughput)
* [데이터베이스 삭제 방지](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>데이터베이스 만들기

Cosmos 데이터베이스를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>공유 처리량을 사용하여 데이터베이스 만들기

공유 처리량을 사용하여 Azure Cosmos 데이터베이스를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>데이터베이스를 자동 크기 조정 처리량으로 마이그레이션

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>데이터베이스 처리량 변경

Cosmos 데이터베이스의 처리량을 1000RU/s로 늘립니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>데이터베이스가 삭제되지 않도록 방지

데이터베이스에 대한 Azure 리소스 삭제 잠금을 설정하여 삭제되지 않도록 합니다. 이 기능을 사용하려면 데이터 평면 SDK에서 Cosmos 계정을 변경하지 못하도록 잠가야 합니다. 자세한 내용은 [SDK에서 변경 방지](../role-based-access-control.md#prevent-sdk-changes)를 참조하세요. Azure 리소스 잠금은 `ReadOnly` 잠금 유형을 지정하여 리소스가 변경되는 것을 방지할 수도 있습니다. Cosmos 데이터베이스의 경우 처리량이 변경되는 것을 방지하는 데 사용할 수 있습니다.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB 컨테이너

다음 섹션에서는 Azure Cosmos DB 컨테이너를 관리하는 방법을 보여 줍니다.

* [컨테이너 만들기](#create-a-container)
* [자동 크기 조정을 사용하여 컨테이너 만들기](#create-a-container-with-autoscale)
* [TTL이 사용하도록 설정된 컨테이너 만들기](#create-a-container-with-ttl)
* [사용자 지정 정책을 사용하여 컨테이너 만들기](#create-a-container-with-a-custom-index-policy)
* [컨테이너 처리량 변경](#change-container-throughput)
* [컨테이너를 자동 크기 조정 처리량으로 마이그레이션](#migrate-a-container-to-autoscale-throughput)
* [컨테이너 삭제 방지](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>컨테이너 만들기

기본 인덱스 정책, 파티션 키 및 400의 RU/s를 사용하여 Cosmos 컨테이너를 만듭니다.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>자동 크기 조정을 사용하여 컨테이너 만들기

기본 인덱스 정책, 파티션 키 및 자동 크기 조정 4000의 RU/s를 사용하여 Cosmos 컨테이너를 만듭니다.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>TTL로 컨테이너 만들기

TTL이 사용하도록 설정된 Cosmos 컨테이너를 만듭니다.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>사용자 지정 정책을 사용하여 컨테이너 만들기

사용자 지정 인덱스 정책, 공간 인덱스, 복합 인덱스, 파티션 키 및 400의 RU/s를 사용하여 Cosmos 컨테이너를 만듭니다.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>컨테이너 처리량 변경

Cosmos 컨테이너의 처리량을 1000RU/s로 늘립니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>컨테이너를 자동 크기 조정 처리량으로 마이그레이션

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>컨테이너가 삭제되지 않도록 방지

컨테이너에 Azure 리소스 삭제 잠금을 설정하여 삭제되지 않도록 합니다. 이 기능을 사용하려면 데이터 평면 SDK에서 Cosmos 계정을 변경하지 못하도록 잠가야 합니다. 자세한 내용은 [SDK에서 변경 방지](../role-based-access-control.md#prevent-sdk-changes)를 참조하세요. Azure 리소스 잠금은 `ReadOnly` 잠금 유형을 지정하여 리소스가 변경되는 것을 방지할 수도 있습니다. Cosmos 컨테이너의 경우 이를 사용하여 처리량 또는 다른 속성이 변경되는 것을 방지할 수 있습니다.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CLI 설치](/cli/azure/install-azure-cli)
* [Azure CLI 참조](/cli/azure/cosmosdb)
* [Azure Cosmos DB에 대한 Azure CLI 추가 샘플](cli-samples.md)