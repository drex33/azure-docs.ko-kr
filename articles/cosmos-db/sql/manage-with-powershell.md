---
title: PowerShell을 사용하여 Azure Cosmos DB Core (SQL) API 리소스 관리
description: PowerShell을 사용하여 Azure Cosmos DB Core (SQL) API 리소스 관리.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/13/2021
ms.author: mjbrown
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: b6d1a8ddcbf7982c4b8c2a44bbd23c8bb78676df
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214172"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos DB Core (SQL) API 리소스 관리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

다음 참고 자료는 PowerShell을 사용하여 Cosmos 계정, 데이터베이스, 컨테이너 및 처리량을 포함한 Azure Cosmos DB Core (SQL) API 리소스의 관리를 스크립팅하고 자동화하는 방법에 대해 설명합니다. 다른 API에 대한 PowerShell cmdlet은 [Cassandra용 PowerShell 샘플](../cassandra/powershell-samples.md), [MongoDB API용 PowerShell 샘플](../mongodb/powershell-samples.md), [Gremlin용 PowerShell 샘플](../graph/powershell-samples.md), [Table용 PowerShell 샘플](../table/powershell-samples.md)을 참조하세요.

> [!NOTE]
> 이 문서의 샘플에서는 [Az.CosmosDB](/powershell/module/az.cosmosdb) 관리 cmdlet을 사용합니다. 최신 변경 내용은 [Az.CosmosDB](/powershell/module/az.cosmosdb) API 참조 페이지를 참조하세요.

Azure Cosmos DB의 플랫폼 간 관리를 위해 [플랫폼 간 PowerShell](/powershell/scripting/install/installing-powershell) 외에도 [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api] 또는 [Azure Portal](create-sql-api-dotnet.md#create-account)을 통해 `Az` 및 `Az.CosmosDB` cmdlet을 사용할 수 있습니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>시작하기

[Azure PowerShell을 설치하고 구성하는 방법][powershell-install-configure]의 지침에 따라 PowerShell에서 Azure 계정을 설치하고 로그인합니다.

> [!IMPORTANT]
> Azure Resource Manager가 리소스 URI와 함께 작동하는 방식을 위반하므로 Azure Cosmos DB 리소스의 이름을 바꿀 수 없습니다.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 계정

다음 섹션에서는 아래 작업을 포함하여 Azure Cosmos 계정을 관리하는 방법을 보여 줍니다.

* [Azure Cosmos 계정 만들기](#create-account)
* [Azure Cosmos 계정 업데이트](#update-account)
* [구독의 모든 Azure Cosmos 계정 나열](#list-accounts)
* [Azure Cosmos 계정 가져오기](#get-account)
* [Azure Cosmos 계정 삭제](#delete-account)
* [Azure Cosmos 계정에 대한 태그 업데이트](#update-tags)
* [Azure Cosmos 계정에 대한 키 나열](#list-keys)
* [Azure Cosmos 계정에 대한 키 다시 생성](#regenerate-keys)
* [Azure Cosmos 계정에 대한 연결 문자열 나열](#list-connection-strings)
* [Azure Cosmos 계정에 대한 장애 조치 우선 순위 수정](#modify-failover-priority)
* [Azure Cosmos 계정에 대한 수동 장애 조치 트리거](#trigger-manual-failover)
* [Azure Cosmos DB 계정에 대한 리소스 잠금 나열](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Azure Cosmos 계정 만들기

다음 명령은 [다중 지역][distribute-data-globally], [자동 장애 조치](../how-to-manage-database-account.md#automatic-failover) 및 제한된 부실 [일관성 정책](../consistency-levels.md)을 사용하여 Azure Cosmos DB 데이터베이스 계정을 만듭니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Cosmos 계정을 배포할 Azure 리소스 그룹입니다. 이 리소스 그룹은 이미 있어야 합니다.
* `$locations` 데이터베이스 계정에 대한 지역이고, `FailoverPriority 0`가 있는 지역은 쓰기 지역입니다.
* `$accountName` Azure Cosmos 계정의 이름입니다. 고유한 소문자여야 하며, 영숫자 및 '-' 문자만 포함하고, 3-31자여야 합니다.
* `$apiKind` 만드는 Cosmos 계정의 유형입니다. 자세한 내용은 [Cosmos DB의 API](../introduction.md#simplified-application-development)를 참조하세요.
* `$consistencyPolicy`, `$maxStalenessInterval` 및 `$maxStalenessPrefix` Azure Cosmos 계정의 기본 일관성 수준 및 설정입니다. 자세한 내용은 [Azure Cosmos DB의 일관성 수준](../consistency-levels.md)을 참조하세요.

Azure Cosmos 계정은 IP 방화벽, Virtual Network 서비스 엔드포인트 및 프라이빗 엔드포인트를 사용하여 구성할 수 있습니다. Azure Cosmos DB에 대해 IP 방화벽을 구성하는 방법에 대한 자세한 내용은 [IP 방화벽 구성](../how-to-configure-firewall.md)을 참조하세요. 서비스 엔드포인트를 Azure Cosmos DB에 사용하도록 설정하는 방법에 대한 자세한 내용은 [가상 네트워크에서 액세스 구성](../how-to-configure-vnet-service-endpoint.md)을 참조하세요. 프라이빗 엔드포인트를 Azure Cosmos DB에 사용하도록 설정하는 방법에 대한 자세한 내용은 [프라이빗 엔드포인트에서 액세스 구성](../how-to-configure-private-endpoints.md)을 참조하세요.

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> 리소스 그룹의 모든 Azure Cosmos 계정 나열

다음 명령은 리소스 그룹의 모든 Azure Cosmos 계정을 나열합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Azure Cosmos 계정의 속성 가져오기

이 명령을 사용하면 기존 Azure Cosmos 계정의 속성을 가져올 수 있습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Azure Cosmos 계정 업데이트

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정 속성을 업데이트할 수 있습니다. 업데이트할 수 있는 속성은 다음과 같습니다.

* 지역 추가 또는 제거
* 기본 일관성 정책 변경
* IP 범위 필터 변경
* Virtual Network 구성 변경
* 다중 지역 쓰기 사용

> [!NOTE]
> 영역(`locations`)을 추가하거나 제거하면서 동시에 Azure Cosmos 계정에 대한 다른 속성을 변경할 수 없습니다. 지역을 수정하는 작업은 계정에 대한 다른 변경과는 별도의 작업으로 수행해야 합니다.
> [!NOTE]
> 이 명령을 사용하면 지역을 추가 및 제거할 수 있지만 장애 조치(failover) 우선 순위를 수정하거나 수동 장애 조치(failover)를 트리거할 수 없습니다. [장애 조치(failover) 우선 순위 수정](#modify-failover-priority) 및 [수동 장애 조치(failover) 트리거](#trigger-manual-failover)를 참조하세요.
> [!TIP]
> 새 지역이 추가되면 해당 지역이 사용 가능으로 표시되기 전에 모든 데이터를 완전히 복제하고 새 지역에 커밋해야 합니다. 이 작업에 걸리는 시간은 해당 계정 내에 저장된 데이터 양에 따라 달라집니다.

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Azure Cosmos 계정에 여러 쓰기 지역 사용

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Azure Cosmos 계정 삭제

다음 명령은 기존 Azure Cosmos 계정을 삭제합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Azure Cosmos 계정의 태그 업데이트

다음 명령은 Azure Cosmos 계정에 대한 [Azure 리소스 태그][azure-resource-tags]를 설정합니다. 태그는 `New-AzCosmosDBAccount`를 사용하여 계정을 만들 때와 `Update-AzCosmosDBAccount`를 사용하여 계정을 업데이트할 때 설정할 수 있습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> 계정 키 나열

Azure Cosmos 계정이 만들어지면 서비스에서 Azure Cosmos 계정에 액세스할 때 인증하는 데 사용할 수 있는 두 개의 기본 액세스 키를 생성합니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 생성합니다.
두 개의 액세스 키를 제공하면 Azure Cosmos DB를 통해 Azure Cosmos 계정을 중단하지 않고 한 번에 하나의 키를 다시 생성하고 회전시킬 수 있습니다.
Cosmos DB 계정에는 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> 연결 문자열 나열

다음 명령은 앱을 Cosmos DB 계정에 연결하기 위한 연결 문자열을 검색합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> 계정 키 다시 생성

Azure Cosmos 계정에 대한 액세스 키는 연결을 안전하게 유지할 수 있도록 정기적으로 다시 생성해야 합니다. 기본 및 보조 액세스 키가 계정에 할당됩니다. 이렇게 하면 한 번에 하나의 키가 다시 생성되는 동안 클라이언트에서 액세스를 유지할 수 있습니다.
Azure Cosmos 계정에는 네 가지 유형의 키(Primary, Secondary, PrimaryReadonly 및 SecondaryReadonly)가 있습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadonly, secondaryReadonly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> 자동 장애 조치(failover) 사용

다음 명령은 주 지역을 사용할 수 없는 경우 보조 지역으로 자동으로 장애 조치하도록 Cosmos DB 계정을 설정합니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> 장애 조치 우선 순위 수정

자동 장애 조치(failover)를 사용하여 구성된 계정의 경우 주 복제본을 사용할 수 없게 될 때 Cosmos가 보조 복제본을 주 복제본으로 승격하는 순서를 변경할 수 있습니다.

아래 예제에서는 현재 장애 조치 우선 순위가 `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`이라고 가정합니다. 명령은 이를 `West US 2 = 0`, `South Central US = 1`, `East US 2 = 2`로 변경합니다.

> [!CAUTION]
> `failoverPriority=0`의 위치를 변경하면 Azure Cosmos 계정에 대한 수동 장애 조치가 트리거됩니다. 다른 우선 순위 변경은 장애 조치를 트리거하지 않습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> 수동 장애 조치(failover) 트리거

수동 장애 조치로 구성된 계정의 경우 `failoverPriority=0`으로 수정하여 모든 보조 복제본을 주 복제본으로 장애 조치하고 승격시킬 수 있습니다. 이 작업은 재해 복구 계획을 테스트하기 위한 재해 복구 훈련을 시작하는 데 사용할 수 있습니다.

아래 예제에서는 계정의 현재 장애 조치(failover) 우선순위가 `West US 2 = 0` 및 `East US 2 = 1`이라고 가정하고 지역을 대칭 이동시킵니다.

> [!CAUTION]
> `failoverPriority=0`에 대한 `locationName`을 변경하면 Azure Cosmos 계정에 대한 수동 장애 조치가 트리거됩니다. 다른 우선 순위 변경은 장애 조치(failover)를 트리거하지 않습니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Azure Cosmos DB 계정에 대한 리소스 잠금 나열

데이터베이스 및 컬렉션을 비롯한 Azure Cosmos DB 리소스에 리소스 잠금을 배치할 수 있습니다. 아래 예제에서는 Azure Cosmos DB 계정에서 모든 Azure 리소스 잠금을 나열하는 방법을 보여줍니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB 데이터베이스

다음 섹션에서는 Azure Cosmos DB 데이터베이스를 관리하는 방법을 보여 줍니다.

* [Azure Cosmos DB 데이터베이스 만들기](#create-db)
* [공유 처리량을 사용하여 Azure Cosmos DB 데이터베이스 만들기](#create-db-ru)
* [Azure Cosmos DB 데이터베이스의 처리량 가져오기](#get-db-ru)
* [데이터베이스 처리량을 자동 크기 조정으로 마이그레이션](#migrate-db-ru)
* [계정의 모든 Azure Cosmos DB 데이터베이스 나열](#list-db)
* [단일 Azure Cosmos DB 데이터베이스 가져오기](#get-db)
* [Azure Cosmos DB 데이터베이스 삭제](#delete-db)
* [삭제를 방지하기 위해 Azure Cosmos DB 데이터베이스에서 리소스 잠금 만들기](#create-db-lock)
* [Azure Cosmos DB 데이터베이스에서 리소스 잠금 제거](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Azure Cosmos DB 데이터베이스 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>공유 처리량을 사용하여 Azure Cosmos DB 데이터베이스 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Azure Cosmos DB 데이터베이스의 처리량 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>데이터베이스 처리량을 자동 크기 조정으로 마이그레이션

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>계정의 모든 Azure Cosmos DB 데이터베이스 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>단일 Azure Cosmos DB 데이터베이스 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Azure Cosmos DB 데이터베이스 삭제

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>삭제를 방지하기 위해 Azure Cosmos DB 데이터베이스에서 리소스 잠금 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Azure Cosmos DB 데이터베이스에서 리소스 잠금 제거

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB 컨테이너

다음 섹션에서는 Azure Cosmos DB 컨테이너를 관리하는 방법을 보여 줍니다.

* [Azure Cosmos DB 컨테이너 만들기](#create-container)
* [자동 크기 조정을 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-autoscale)
* [대용량 파티션 키를 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-big-pk)
* [Azure Cosmos DB 컨테이너의 처리량 가져오기](#get-container-ru)
* [컨테이너 처리량을 자동 크기 조정으로 마이그레이션](#migrate-container-ru)
* [사용자 지정 인덱싱을 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-custom-index)
* [해제된 인덱싱을 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-no-index)
* [고유 키와 TTL을 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-unique-key-ttl)
* [충돌 해결을 사용하여 Azure Cosmos DB 컨테이너 만들기](#create-container-lww)
* [데이터베이스의 모든 Azure Cosmos DB 컨테이너 나열](#list-containers)
* [데이터베이스의 단일 Azure Cosmos DB 컨테이너 가져오기](#get-container)
* [Azure Cosmos DB 컨테이너 삭제](#delete-container)
* [삭제를 방지하기 위해 Azure Cosmos DB 컨테이너에서 리소스 잠금 만들기](#create-container-lock)
* [Azure Cosmos DB 컨테이너에서 리소스 잠금 제거](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>자동 크기 조정을 사용하여 Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>대용량 파티션 키 크기를 사용하여 Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Azure Cosmos DB 컨테이너의 처리량 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>컨테이너 처리량을 자동 크기 조정으로 마이그레이션

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>사용자 지정 인덱스 정책을 사용하여 Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>해제된 인덱싱을 사용하여 Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>고유 키 정책과 TTL을 사용하여 Azure Cosmos DB 컨테이너 만들기

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>충돌 해결을 사용하여 Azure Cosmos DB 컨테이너 만들기

모든 충돌을 개별적으로 ConflictsFeed에 기록하고 처리하려면 `-Type "Custom" -Path ""`를 전달합니다.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

저장 프로시저를 사용하는 충돌 해결 정책을 만들려면 `New-AzCosmosDBSqlConflictResolutionPolicy`를 호출하고 `-Type` 및 `-ConflictResolutionProcedure` 매개 변수를 전달합니다.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>데이터베이스의 모든 Azure Cosmos DB 컨테이너 나열

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>데이터베이스의 단일 Azure Cosmos DB 컨테이너 가져오기

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Azure Cosmos DB 컨테이너 삭제

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>삭제를 방지하기 위해 Azure Cosmos DB 컨테이너에서 리소스 잠금 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Azure Cosmos DB 컨테이너에서 리소스 잠금 제거

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>다음 단계

* [모든 PowerShell 샘플](powershell-samples.md)
* [Azure Cosmos 계정을 관리하는 방법](../how-to-manage-database-account.md)
* [Azure Cosmos DB 컨테이너 만들기](how-to-create-container.md)
* [Azure Cosmos DB에서 TTL(Time to Live) 구성](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: ../distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: ../distribute-data-globally.md
[azure-resource-groups]: ../../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/
