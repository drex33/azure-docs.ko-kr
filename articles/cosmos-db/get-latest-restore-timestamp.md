---
title: Azure Cosmos DB 연속 백업 계정에 대한 복원 가능한 최신 타임스탬프 얻기
description: 연속 백업 모드로 설정된 계정에 대해 복원 가능한 최신 타임스탬프를 얻는 방법을 알아봅니다. Azure PowerShell 및 Azure CLI 사용하여 SQL 컨테이너 및 MongoDB 컬렉션에 대해 복원 가능한 최신 시간을 얻는 방법을 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/18/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: fdebe0032bb90c5170239eefaf813d2a864c430b
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943452"
---
# <a name="get-the-latest-restorable-timestamp-for-continuous-backup-accounts"></a>연속 백업 계정에 대한 복원 가능한 최신 타임스탬프 받기
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

이 문서에서는 연속 백업 모드로 설정된 계정에 대해 [복원 가능한 최신 타임스탬프를](latest-restore-timestamp-continuous-backup.md) 얻는 방법을 설명합니다. Azure PowerShell 및 Azure CLI 사용하여 SQL 컨테이너 및 MongoDB 컬렉션에 대해 복원 가능한 최신 시간을 얻는 방법을 설명합니다. PowerShell 및 CLI 명령에 대한 요청 및 응답 형식을 볼 수 있습니다.

## <a name="sql-container"></a>SQL 컨테이너

### <a name="powershell"></a>PowerShell

```powershell
Get-AzCosmosDBSqlContainerBackupInformation -AccountName <System.String> `
  -DatabaseName <System.String> `
  [-DefaultProfile <Microsoft.Azure.Commands.Common.Authentication.Abstractions.Core.IAzureContextContainer>] `
  -Location <System.String> `
  -Name <System.String> `
  -ResourceGroupName <System.String> [<CommonParameters>]
```

**샘플 요청:**

```powershell
Get-AzCosmosDBSqlContainerBackupInformation -ResourceGroupName "rg" `
  -AccountName "sqlpitracc" `
  -DatabaseName "db1" `
  -Name "container1" `
  -Location "eastus"
```

**샘플 응답(UTC 형식):**

```console
LatestRestorableTimestamp
-------------------------
10/26/2021 6:24:25 PM
```

### <a name="cli"></a>CLI

```azurecli
az cosmosdb sql retrieve-latest-backup-time -g {resourcegroup} \
  -a {accountname} \
  -d {db_name} \
  -c {container_name} \
  -l {location}
```

**샘플 요청:**

```azurecli
az cosmosdb sql retrieve-latest-backup-time -g "rg" \
  -a "sqlpitracc" \
  -d "db1" \
  -c "container1" \
  -l "eastus"
```

**샘플 응답(UTC 형식):**

```console
{
  "continuousBackupInformation": {
    "latestRestorableTimestamp": "10/26/2021 5:27:45 PM"
  }
}
```

## <a name="sql-database"></a>SQL Database

다음 스크립트를 사용하여 데이터베이스에 대해 복원 가능한 최신 타임스탬프를 가져옵니다. 이 스크립트는 지정된 데이터베이스 내의 모든 컨테이너를 반복하고 모든 컨테이너의 복원 가능한 최소 타임스탬프를 반환합니다.

```powershell
Function Get-LatestRestorableTimestampForSqlDatabase {
[CmdletBinding()]
param(
  [Parameter(Position = 0, mandatory = $true)]
  [string] $resourceGroupName,
  [Parameter(Position = 1, mandatory = $true)]
  [string] $accountName,
  [Parameter(Position = 2, mandatory = $true)]
  [string] $databaseName,
  [Parameter(Position = 3, mandatory = $true)]
  [string] $location)

Write-Host
Write-Host "Latest restorable timestamp for a database is minimum of restorable timestamps of all the underlying containers"
Write-Host

Write-Debug "Listing all containers in database: $databaseName"
$containers = Get-AzCosmosDBSqlContainer -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $databaseName

If (-Not $containers) {
    throw "Error: Found no containers to restore in the given database."
}

Write-Debug "Found $($containers.Length) containers under database $databaseName"

$latestRestorableTimestampForDatabase = [DateTime]::MaxValue
foreach ($container in $containers) {
    Write-Debug "Getting latest restorable timestamp for container: $($container.Name)"

    $latestRestorableTimestampForContainer = Get-AzCosmosDBSqlContainerBackupInformation -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $databaseName -Name $container.Name -Location $location

    Write-Debug "Latest Restorable Timestamp for container $($container.Name): $($latestRestorableTimestampForContainer.LatestRestorableTimestamp)"

    $latestRestorableTimestampForContainerDateTime = [DateTime]$latestRestorableTimestampForContainer.LatestRestorableTimestamp

    If ($latestRestorableTimestampForContainerDateTime -lt $latestRestorableTimestampForDatabase) {
        Write-Debug "Latest Restorable Timestamp for container $($container.Name) is less than current database restorable timestamp: $latestRestorableTimestampForDatabase"

        $latestRestorableTimestampForDatabase = $latestRestorableTimestampForContainerDateTime
    }

    Write-Debug "Latest Restorable Timestamp for database so far: $latestRestorableTimestampForDatabase"
}

if ($latestRestorableTimestampForDatabase -eq [DateTime]::MaxValue) {
    throw "Error: Failed to retrieve latest backup timestamp for database: $databaseName"
}

Write-Debug "Latest Restorable Timestamp in UTC for database $($databaseName): $latestRestorableTimestampForDatabase"

return $latestRestorableTimestampForDatabase
}
```

**구문:**

```powershell
Get-LatestRestorableTimestampForSqlDatabase `
  -ResourceGroupName <resourceGroup> `
  -AccountName <accountName> `
  -DatabaseName <databaseName> `
  -Location <location>
```

**샘플 요청:**

```powershell
Import-Module .\LatestRestorableDatabaseForSqlDatabase.ps1
Get-LatestRestorableTimestampForSqlDatabase `
  -ResourceGroupName rg `
  -AccountName sqlpitracc `
  -DatabaseName db1 `
  -Location eastus
```

**샘플 응답(UTC 형식):**

```console
Latest restorable timestamp for a database is minimum of restorable timestamps of all the underlying containers
Wednesday, November 3, 2021 8:02:44 PM
```

## <a name="sql-account"></a>SQL 계정

다음 스크립트를 사용하여 데이터베이스에 대해 복원 가능한 최신 타임스탬프를 가져옵니다. 이 스크립트는 이 데이터베이스 내의 모든 컨테이너를 반복하고 모든 컨테이너의 복원 가능한 최신 타임스탬프의 최소값을 반환합니다.

```powershell
Function Get-LatestRestorableTimestampForSqlAccount {
[CmdletBinding()]
param(
    [Parameter(Position = 0, mandatory = $true)]
    [string] $resourceGroupName,
    [Parameter(Position = 1, mandatory = $true)]
    [string] $accountName,
    [Parameter(Position = 2, mandatory = $true)]
    [string] $location)

Write-Host
Write-Host "Latest restorable timestamp for an account is minimum of restorable timestamps of all the underlying containers"
Write-Host

Write-Debug "Listing all databases in account: $accountName"

$databases = Get-AzCosmosDBSqlDatabase -ResourceGroupName $resourceGroupName -AccountName $accountName
Write-Debug "Found $($databases.Length) databases under account $accountName"

$latestRestorableTimestampForAccount = [DateTime]::MaxValue

foreach ($database in $databases) {
    Write-Debug "Listing all containers in database: $($database.Name)"
    $containers = Get-AzCosmosDBSqlContainer -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $($database.Name)

    If (-Not $containers) {
        throw "Error: Found no containers to restore in the given database."
    }

    Write-Debug "Found $($containers.Length) containers under database $($database.Name)"

    foreach ($container in $containers) {
        Write-Debug "Getting latest restorable timestamp for container: $($container.Name)"

        $latestRestorableTimestampForContainer = Get-AzCosmosDBSqlContainerBackupInformation -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $database.Name -Name $container.Name -Location $location

        Write-Debug "Latest Restorable Timestamp for container $($container.Name): $($latestRestorableTimestampForContainer.LatestRestorableTimestamp)"

        $latestRestorableTimestampForContainerDateTime = [DateTime]$latestRestorableTimestampForContainer.LatestRestorableTimestamp

        If ($latestRestorableTimestampForContainerDateTime -lt $latestRestorableTimestampForAccount) {
            Write-Debug "Latest Restorable Timestamp for container $($container.Name) is less than current database restorable timestamp: $latestRestorableTimestampForAccount"

            $latestRestorableTimestampForAccount = $latestRestorableTimestampForContainerDateTime
        }

        Write-Debug "Latest Restorable Timestamp for database so far: $latestRestorableTimestampForAccount"
    }
}

if ($latestRestorableTimestampForAccount -eq [DateTime]::MaxValue) {
    throw "Error: Failed to retrieve latest backup timestamp for account: $accountName"
}
    
Write-Debug "Latest Restorable Timestamp in UTC for account $($accountName): $latestRestorableTimestampForAccount"

return $latestRestorableTimestampForAccount
}
```

**구문:**

```powershell
Get-LatestRestorableTimestampForSqlAccount `
  -ResourceGroupName <resourceGroupName> `
  -AccountName <accountName> `
  -Location <location>
```

**샘플 요청:**

```powershell
Import-Module .\LatestRestorableTimestampForSqlAccount.ps1

Get-LatestRestorableTimestampForSqlAccount `
  -ResourceGroupName rg `
  -AccountName sqlpitracc `
  -location eastus
```

**샘플 응답(UTC 형식):**

```console
Latest restorable timestamp for an account is minimum of restorable timestamps of all the underlying containers
Wednesday, November 3, 2021 8:11:03 PM
```

## <a name="mongodb-collection"></a>MongoDB 컬렉션

### <a name="powershell"></a>PowerShell

```powershell
Get-AzCosmosDBMongoDBCollectionBackupInformation `
  -AccountName <System.String> `
  -DatabaseName <System.String> `
  [-DefaultProfile <Microsoft.Azure.Commands.Common.Authentication.Abstractions.Core.IAzureContextContainer>] `
  -Location <System.String> `
  -Name <System.String> `
  -ResourceGroupName <System.String> [<CommonParameters>]
```

**샘플 요청:**

```powershell
Get-AzCosmosDBMongoDBCollectionBackupInformation `
  -ResourceGroupName "rg" `
  -AccountName "mongodbpitracc" `
  -DatabaseName "db1" `
  -Name "col1" `
  -Location "eastus"
```

**샘플 응답(UTC 형식):**

```console
LatestRestorableTimestamp
-------------------------
10/26/2021 6:27:22 PM
```

### <a name="cli"></a>CLI

```azurecli
az cosmosdb mongodb retrieve-latest-backup-time \
  -g {resourcegroup} \
  -a {accountname} \
  -d {db_name} \
  -c {collection_name} \
  -l {location}
```

**샘플 요청:**

```azurecli
az cosmosdb mongodb retrieve-latest-backup-time \
  -g "rg" \
  -a "mongodbpitracc" \
  -d "db1" \
  -c "col1" \
  -l "eastus"
```

**샘플 응답:**

```console
{
  "continuousBackupInformation": {
    "latestRestorableTimestamp": "10/26/2021 5:27:45 PM"
  }
}
```

## <a name="mongodb-database"></a>MongoDB 데이터베이스

다음 스크립트를 사용하여 데이터베이스에 대해 복원 가능한 최신 타임스탬프를 가져옵니다. 이 스크립트는 이 데이터베이스 내의 모든 컨테이너를 반복하고 모든 컨테이너의 복원 가능한 최소 타임스탬프를 반환합니다.

```powershell
Function Get-LatestRestorableTimestampForMongoDBDatabase {
[CmdletBinding()]
param(
    [Parameter(Position = 0, mandatory = $true)]
    [string] $resourceGroupName,
    [Parameter(Position = 1, mandatory = $true)]
    [string] $accountName,
    [Parameter(Position = 2, mandatory = $true)]
    [string] $databaseName,
    [Parameter(Position = 3, mandatory = $true)]
    [string] $location)
    
Write-Host
Write-Host "Latest restorable timestamp for a database is minimum of restorable timestamps of all the underlying collections"
Write-Host

Write-Debug "Listing all collections in database: $databaseName"
$collections = Get-AzCosmosDBMongoDBCollection -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $databaseName

If (-Not $collections) {
    throw "Error: Found no collections to restore in the given database."
}

Write-Debug "Found $($collections.Length) collections under database $databaseName"

$latestRestorableTimestampForDatabase = [DateTime]::MaxValue
foreach ($collection in $collections) {
    Write-Debug "Getting latest restorable timestamp for collection: $($collection.Name)"

    $latestRestorableTimestampForCollection = Get-AzCosmosDBMongoDBCollectionBackupInformation -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $databaseName -Name $collection.Name -Location $location

    Write-Debug "Latest Restorable Timestamp for collection $($collection.Name): $($latestRestorableTimestampForCollection.LatestRestorableTimestamp)"

    $latestRestorableTimestampForCollectionDateTime = [DateTime]$latestRestorableTimestampForCollection.LatestRestorableTimestamp

    If ($latestRestorableTimestampForCollectionDateTime -lt $latestRestorableTimestampForDatabase) {
        Write-Debug "Latest Restorable Timestamp for collection $($collection.Name) is less than current database restorable timestamp: $latestRestorableTimestampForDatabase"

        $latestRestorableTimestampForDatabase = $latestRestorableTimestampForCollectionDateTime
    }

    Write-Debug "Latest Restorable Timestamp for database so far: $latestRestorableTimestampForDatabase"
}

if ($latestRestorableTimestampForDatabase -eq [DateTime]::MaxValue) {
    throw "Error: Failed to retrieve latest backup timestamp for database: $databaseName"
}

Write-Debug "Latest Restorable Timestamp in UTC for database $($databaseName): $latestRestorableTimestampForDatabase"

return $latestRestorableTimestampForDatabase
}
```

**구문:**

```powershell
Get-LatestRestorableTimestampForMongoDBDatabase `
  -ResourceGroupName <resourceGroup> `
  -AccountName <account> `
  -DatabaseName <database> `
  -Location <location>
```

**샘플 요청:**

```powershell
Import-Module .\LatestRestorableTimestampForMongoDBDatabase.ps1
Get-LatestRestorableTimestampForMongoDBDatabase -ResourceGroupName rg -accountName mongopitracc -databaseName db1 -location eastus
```

**샘플 응답(UTC 형식):**

```console
Latest restorable timestamp for a database is minimum of restorable timestamps of all the underlying collections
Wednesday, November 3, 2021 8:31:27 PM
```

## <a name="mongodb-account"></a>MongoDB 계정

에서는 다음 스크립트를 사용하여 데이터베이스에 대해 복원 가능한 최신 타임스탬프를 얻을 수 있습니다. 이 스크립트는 이 데이터베이스 내의 모든 컨테이너를 반복하고 모든 컨테이너의 복원 가능한 최소 타임스탬프를 반환합니다.

```powershell
Function Get-LatestRestorableTimestampForMongoDBAccount {
[CmdletBinding()]
param(
    [Parameter(Position = 0, mandatory = $true)]
    [string] $resourceGroupName,
    [Parameter(Position = 1, mandatory = $true)]
    [string] $accountName,
    [Parameter(Position = 2, mandatory = $true)]
    [string] $location)

Write-Host
Write-Host "Latest restorable timestamp for an account is minimum of restorable timestamps of all the underlying collections"
Write-Host

Write-Debug "Listing all databases in account: $accountName"

$databases = Get-AzCosmosDBMongoDBDatabase -ResourceGroupName $resourceGroupName -AccountName $accountName
Write-Debug "Found $($databases.Length) databases under account $accountName"

$latestRestorableTimestampForAccount = [DateTime]::MaxValue

foreach ($database in $databases) {
    Write-Debug "Listing all collections in database: $($database.Name)"
    $collections = Get-AzCosmosDBMongoDBCollection -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $($database.Name)

    If (-Not $collections) {
        throw "Error: Found no collections to restore in the given database."
    }

    Write-Debug "Found $($collections.Length) collections under database $($database.Name)"

    foreach ($collection in $collections) {
        Write-Debug "Getting latest restorable timestamp for collection: $($collection.Name)"

        $latestRestorableTimestampForCollection = Get-AzCosmosDBMongoDBCollectionBackupInformation -ResourceGroupName $resourceGroupName -AccountName $accountName -DatabaseName $database.Name -Name $collection.Name -Location $location

        Write-Debug "Latest Restorable Timestamp for collection $($collection.Name): $($latestRestorableTimestampForCollection.LatestRestorableTimestamp)"

        $latestRestorableTimestampForCollectionDateTime = [DateTime]$latestRestorableTimestampForCollection.LatestRestorableTimestamp

        If ($latestRestorableTimestampForCollectionDateTime -lt $latestRestorableTimestampForAccount) {
            Write-Debug "Latest Restorable Timestamp for collection $($collection.Name) is less than current database restorable timestamp: $latestRestorableTimestampForAccount"

            $latestRestorableTimestampForAccount = $latestRestorableTimestampForCollectionDateTime
        }

        Write-Debug "Latest Restorable Timestamp for database so far: $latestRestorableTimestampForAccount"
    }
}

if ($latestRestorableTimestampForAccount -eq [DateTime]::MaxValue) {
    throw "Error: Failed to retrieve latest backup timestamp for account: $accountName"
}

Write-Debug "Latest Restorable Timestamp in UTC for account $($accountName): $latestRestorableTimestampForAccount"

return $latestRestorableTimestampForAccount
}
```

**구문:**

```powershell
Get-LatestRestorableTimestampForMongoDBAccount `
  -ResourceGroupName <resourceGroupName> `
  -AccountName <accountName> `
  -Location <location>
```

**샘플 요청:**

```powershell
Import-Module .\LatestRestorableTimestampForMongoDBAccount.ps1
Get-LatestRestorableTimestampForMongoDBAccount `
  -ResourceGroupName rg `
  -AccountName mongopitracc `
  -Location eastus
```

**샘플 응답(UTC 형식):**

```console
Latest restorable timestamp for an account is minimum of restorable timestamps of all the underlying collections
Wednesday, November 3, 2021 8:33:49 PM
```

## <a name="next-steps"></a>다음 단계

* [지정 시간 복원을 사용하는 지속적인 백업 모드 소개](continuous-backup-restore-introduction.md)

* [지속적인 백업 모드 리소스 모델](continuous-backup-restore-resource-model.md)

* Azure Portal 사용하여 [연속 백업 모드를 구성하고 관리합니다.](continuous-backup-restore-portal.md)
