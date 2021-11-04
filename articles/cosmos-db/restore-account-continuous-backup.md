---
title: 연속 백업 모드를 사용하는 Azure Cosmos DB 계정을 복원합니다.
description: 복원 시간을 식별하고 라이브 또는 삭제된 Azure Cosmos DB 계정을 복원하는 방법을 알아봅니다. 이벤트 피드를 사용하여 복원 시간을 식별하고 Azure Portal, PowerShell, CLI 또는 Resource Manager 템플릿을 사용하여 계정을 복원하는 방법을 보여 줍니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ad6d097b10f0e601e1e727922f1343b04d1d0ca
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504990"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>연속 백업 모드를 사용하는 Azure Cosmos DB 계정 복원
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB의 특정 시점 복원 기능을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 복원 시간을 식별하고 라이브 또는 삭제된 Azure Cosmos DB 계정을 복원하는 방법을 설명합니다. [Azure Portal](#restore-account-portal), [PowerShell](#restore-account-powershell), [CLI](#restore-account-cli) 또는 [Resource Manager 템플릿](#restore-arm-template)을 사용하여 계정을 복원하는 방법을 보여 줍니다.

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal"></a>Azure Portal을 사용하여 계정 복원

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>실수로 수정한 라이브 계정 복원

Azure Portal을 사용하여 라이브 계정이나 전체 라이브 계정으로 선택한 데이터베이스 및 컨테이너를 복원할 수 있습니다. 데이터를 복원하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Cosmos DB 계정으로 이동하여 **특정 시점 복원** 블레이드를 엽니다.

   > [!NOTE]
   > Azure Portal의 복원 블레이드는 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 권한이 있는 경우에만 채워집니다. 이 권한을 설정하는 방법에 대한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조하세요.

1. 복원하려면 다음 세부 정보를 입력합니다.

   * **복원 지점(UTC)** – 지난 30일 이내의 타임스탬프입니다. 해당 타임스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정할 수 있습니다. 복원 지점은 초 단위까지 지정할 수 있습니다. [복원 지점을 식별](#event-feed)하는 방법에 대한 도움말을 보려면 **여기를 클릭** 링크를 선택합니다.

   * **위치** – 계정이 복원되는 대상 지역입니다. 계정이 지정된 타임스탬프에 이 지역(예: 미국 서부 또는 미국 동부)에 있어야 합니다. 계정은 원본 계정이 있었던 지역으로만 복원할 수 있습니다.

   * **리소스 복원** – **전체 계정** 또는 **선택한 데이터베이스/컨테이너** 를 복원하도록 선택할 수 있습니다. 데이터베이스와 컨테이너는 지정된 타임스탬프에 있어야 합니다. 선택한 복원 지점 및 위치에 따라 복원 리소스가 채워지므로 사용자가 복원해야 하는 특정 데이터베이스나 컨테이너를 선택할 수 있습니다.

   * **리소스 그룹** - 대상 계정이 만들어지고 복원되는 리소스 그룹입니다. 이 리소스 그룹은 이미 있어야 합니다.

   * **복원 대상 계정** - 대상 계정 이름입니다. 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 곳과 동일한 지역에 복원 프로세스를 통해 생성됩니다.
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="Azure Portal에서 실수로 수정한 라이브 계정 복원" border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. 위의 매개 변수를 선택한 후 **제출** 단추를 선택하여 복원을 시작합니다. 복원 비용은 선택한 지역의 데이터 크기와 백업 스토리지 비용을 기반으로 하는 일회성 요금입니다. 자세한 내용은 [가격 책정](continuous-backup-restore-introduction.md#continuous-backup-pricing) 섹션을 참조하세요.

복원 작업이 진행 되는 동안 원본 계정을 삭제 하면 복원 오류가 발생할 수 있습니다.

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>이벤트 피드를 사용하여 복원 시간 파악

Azure Portal의 복원 지점 시간을 입력할 때 복원 지점을 파악하는 데 도움이 필요하면 **여기를 클릭** 링크를 선택합니다. 그러면 이벤트 피드 블레이드로 이동합니다. 이벤트 피드에는 원본 계정의 데이터베이스 및 컨테이너에 대한 완전히 신뢰할 수 있는 create, replace, delete 이벤트 목록이 제공됩니다. 

예를 들어 특정 컨테이너를 삭제하거나 업데이트하기 이전의 지점으로 복원하려면 이 이벤트 피드를 확인합니다. 이벤트가 발생한 시간의 내림차순으로 표시되고 최근 이벤트가 위쪽에 표시됩니다. 결과를 찾아보고 이벤트 전후 시간을 선택하여 시간을 더 좁힐 수 있습니다.

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="이벤트 피드를 사용하여 복원 지점 시간 파악" border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> 항목 리소스에 대한 변경 내용은 이벤트 피드에 표시되지 않습니다. 복원에는 항상 지난 30일 이내의 타임스탬프를 수동으로 지정할 수 있습니다(해당 시간에 계정이 있는 경우에 한함).

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>삭제된 계정 복원

삭제된 계정을 삭제일로부터 30일 이내에 Azure Portal을 사용하여 완전히 복원할 수 있습니다. 삭제된 계정을 복원하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 글로벌 검색 창에서 *Azure Cosmos DB* 리소스를 검색합니다. 그러면 기존 계정이 모두 나열됩니다.
1. 그런 다음, **복원** 단추를 선택합니다. 복원 블레이드에는 보존 기간(삭제 시간으로부터 30일) 내에 복원할 수 있는 삭제된 계정의 목록이 표시됩니다.
1. 복원하려는 계정을 선택합니다.

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="Azure Portal에서 삭제된 계정 복원" border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > Azure Portal의 복원 블레이드는 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 권한이 있는 경우에만 채워집니다. 이 권한을 설정하는 방법에 대한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조하세요.

1. 복원할 계정을 선택하고 다음 세부 정보를 입력하여 삭제된 계정을 복원합니다.

   * **복원 지점(UTC)** – 지난 30일 이내의 타임스탬프입니다. 해당 타임스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정합니다. 복원 지점은 초 단위까지 지정할 수 있습니다.

   * **위치** – 계정을 복원해야 하는 대상 지역입니다. 원본 계정이 지정된 타임스탬프에 이 지역에 있어야 합니다. 예를 들면 미국 서부 또는 미국 동부입니다.  

   * **리소스 그룹** - 대상 계정이 만들어지고 복원되는 리소스 그룹입니다. 이 리소스 그룹은 이미 있어야 합니다.

   * **복원 대상 계정** – 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 곳과 동일한 지역에 복원 프로세스를 통해 생성됩니다.

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>복원 작업의 상태 추적

복원 작업을 시작한 후 포털의 오른쪽 상단 모서리에 있는 **알림** 벨 아이콘을 선택합니다. 그러면 복원 중인 계정의 상태를 표시하는 링크가 제공됩니다. 복원이 진행 중인 동안에는 계정 상태가 *생성 중* 이고, 복원 작업이 완료된 후에는 계정 상태가 *온라인* 으로 변경됩니다.

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="&quot; border=&quot;true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::작업이 완료되면 복원된 계정의 상태가 생성 중에서 온라인으로 변경됩니다.

### <a name="get-the-restore-details-from-the-restored-account"></a>복원 된 계정에서 복원 세부 정보 가져오기

복원 작업이 완료 되 면 복원 된 원본 계정 정보 또는 복원 시간을 확인할 수 있습니다.

Azure Portal에서 복원 정보를 가져오려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 복원 된 계정으로 이동 합니다.

1. **템플릿 내보내기** 창으로 이동 합니다. 복원 된 계정에 해당 하는 JSON 템플릿이 열립니다.

1. **Resources**  >  **properties**  >  **restoreParameters** 개체는 복원 세부 정보를 포함 합니다. **RestoreTimestampInUtc** 는 계정이 복원 된 시간을 제공 하 고 **databasesToRestore** 는 계정이 복원 된 특정 데이터베이스 및 컨테이너를 표시 합니다.

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell"></a>Azure PowerShell을 사용하여 계정 복원

계정을 복원하기 전에 [최신 버전의 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 또는 6.2.0보다 높은 버전을 설치하세요. 다음으로 Azure 계정에 연결하고 다음 명령을 사용하여 필요한 구독을 선택합니다.

1. 다음 명령을 사용하여 Azure에 로그인합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

1. 다음 명령을 사용하여 특정 구독을 선택합니다.

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**예제 1:** 전체 계정 복원:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**예제 2:** 특정 컬렉션 및 데이터베이스 복원. 이 예제는 *MyDB1* 에서 컬렉션 *MyCol1*, *MyCol2* 를 복원하고, *MyDB2* 에서 그 아래에 있는 모든 컨테이너를 포함한 전체 데이터베이스를 복원합니다.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="get-the-restore-details-from-the-restored-account"></a>복원 된 계정에서 복원 세부 정보 가져오기

모듈을 가져오고 `Az.CosmosDB` 다음 명령을 실행 하 여 복원 세부 정보를 가져옵니다. RestoreTimestamp는 restoreParameters 개체 아래에 있습니다.

```azurepowershell
Get-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount 
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API에 대해 복원 가능한 리소스 열거

열거형 cmdlet은 다양한 타임스탬프에서 복원이 가능한 리소스를 검색하는 데 도움이 됩니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>현재 구독에서 복원할 수 있는 모든 계정 나열

`Get-AzCosmosDBRestorableDatabaseAccount`PowerShell 명령을 실행하여 현재 구독에서 복원할 수 있는 모든 계정을 나열합니다.

응답에는 복원할 수 있는 모든 데이터베이스 계정(라이브 및 삭제된 계정 모두)과 복원할 수 있는 지역이 포함됩니다.

```json
{
    "accountName": "SampleAccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

계정에 대한 `CreationTime` 또는 `DeletionTime`와 마찬가지로 지역에도 `CreationTime` 또는 `DeletionTime`가 있습니다. 이러한 시간을 사용하여 올바른 지역 및 해당 지역으로 복원할 유효한 시간 범위를 선택할 수 있습니다.

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>라이브 데이터베이스 계정에서 모든 버전의 SQL 데이터베이스를 나열

모든 버전의 데이터베이스를 나열하면 데이터베이스의 실제 존재 시간을 알 수 없는 시나리오에서 올바른 데이터베이스를 선택할 수 있습니다.

다음 PowerShell 명령을 실행하여 모든 버전의 데이터베이스를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `DatabaseAccountInstanceId` 및 `Location` 매개 변수는 `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet의 응답에서 `name` 및 `location` 속성으로부터 가져옵니다. `DatabaseAccountInstanceId` 특성은 복원되는 원본 데이터베이스 계정의 `instanceId` 속성을 참조합니다.

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>라이브 데이터베이스 계정의 데이터베이스에서 모든 SQL 컨테이너 버전을 나열

다음 명령을 사용하여 모든 버전의 SQL 컨테이너를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `DatabaseRId` 매개 변수는 복원하려는 데이터베이스의 `ResourceId`입니다. `Get-AzCosmosdbSqlRestorableDatabase` cmdlet의 응답에 포함된 `ownerResourceid` 특성의 값입니다. 이 응답에는 이 데이터베이스 내의 모든 컨테이너에 대해 수행된 작업의 목록도 포함되어 있습니다.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너 찾기

다음 명령을 사용하여 특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너의 목록을 가져옵니다. 이 명령은 라이브 계정에서만 작동합니다.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>MongoDB용 API에서 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다. 이들 명령은 라이브 계정에 대해서만 작동하며 SQL API 명령과 비슷하지만 명령 이름에 `sql` 대신 `MongoDB`가 들어갑니다.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>라이브 데이터베이스 계정에서 모든 버전의 MongoDB 데이터베이스를 나열

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>라이브 데이터베이스 계정의 데이터베이스에서 모든 버전의 MongoDB 컬렉션을 나열

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>특정 타임스탬프 및 지역에서 복원에 사용할 수 있는 MongoDB 데이터베이스 계정의 모든 리소스를 나열

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>Azure CLI를 사용하여 계정 복원

계정을 복원하기 전에 다음 단계에 따라 Azure CLI를 설치합니다.

1. 최신 버전의 Azure CLI 설치

   * 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 2.26.0 이상 버전을 설치합니다.
   * CLI를 이미 설치한 경우 `az upgrade` 명령을 실행하여 최신 버전으로 업데이트합니다. 이 명령은 2.11 이상 CLI 버전에서만 작동합니다. 이전 버전인 경우 위의 링크를 사용하여 최신 버전을 설치하세요.

1. 로그인하고 구독 선택

   * `az login` 명령을 사용하여 Azure 계정에 로그인합니다.
   * `az account set -s <subscriptionguid>` 명령을 사용하여 필요한 구독을 선택합니다.

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>CLI를 사용하여 복원 작업 트리거

복원을 트리거하는 가장 간단한 방법은 대상 계정의 이름, 원본 계정, 위치, 리소스 그룹, 타임스탬프(UTC) 및 필요에 따라 데이터베이스와 컨테이너 이름을 사용하여 복원 명령을 실행하는 것입니다. 다음은 복원 작업을 트리거하는 몇 가지 예입니다.

1. 기존 계정에서 복원하여 새 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 기존 데이터베이스 계정에서 선택한 데이터베이스 및 컨테이너만 복원하여 새 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="get-the-restore-details-from-the-restored-account"></a>복원된 계정에서 복원 세부 정보 얻기

다음 명령을 실행하여 복원 세부 정보를 얻습니다. restoreTimestamp는 restoreParameters 개체 아래에 있습니다.

```azurecli-interactive
az cosmosdb show --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API에 대해 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>현재 구독에서 복원할 수 있는 모든 계정 나열

다음 CLI 명령을 실행하여 현재 구독에서 복원할 수 있는 모든 계정을 나열합니다.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

응답에는 복원할 수 있는 모든 데이터베이스 계정(라이브 및 삭제된 계정 모두)과 복원할 수 있는 지역이 포함됩니다.

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

계정의 `CreationTime` 또는 `DeletionTime`와 마찬가지로 지역에도 `CreationTime` 또는 `DeletionTime`가 있습니다. 이러한 시간을 사용하여 올바른 지역 및 해당 지역으로 복원할 유효한 시간 범위를 선택할 수 있습니다.

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>라이브 데이터베이스 계정에서 모든 버전의 데이터베이스를 나열

모든 버전의 데이터베이스를 나열하면 데이터베이스의 실제 존재 시간을 알 수 없는 시나리오에서 올바른 데이터베이스를 선택할 수 있습니다.

다음 CLI 명령을 실행하여 모든 버전의 데이터베이스를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `instance-id` 및 `location` 매개 변수는 `az cosmosdb restorable-database-account list` 명령 응답의 `name` 및 `location` 속성에서 가져옵니다. instanceId 특성은 복원되는 원본 데이터베이스 계정의 속성이기도 합니다.

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

이제 이 명령 출력에는 데이터베이스가 만들어지고 삭제된 시간이 표시됩니다.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>라이브 데이터베이스 계정의 데이터베이스에서 모든 SQL 컨테이너 버전을 나열

다음 명령을 사용하여 모든 버전의 SQL 컨테이너를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `database-rid` 매개 변수는 복원하려는 데이터베이스의 `ResourceId`입니다. `az cosmosdb sql restorable-database list` 명령의 응답에 포함된 `ownerResourceid` 특성의 값입니다.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

이 명령 출력에는 이 데이터베이스 내의 모든 컨테이너에 대해 수행된 작업의 목록이 포함되어 있습니다.

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너 찾기

다음 명령을 사용하여 특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너의 목록을 가져옵니다. 이 명령은 라이브 계정에서만 작동합니다.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API 계정에 대해 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다. 이러한 명령은 라이브 계정에서만 작동합니다.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>라이브 데이터베이스 계정에서 모든 버전의 MongoDB 데이터베이스를 나열

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>라이브 데이터베이스 계정의 데이터베이스에서 모든 버전의 MongoDB 컬렉션을 나열

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>특정 타임스탬프 및 지역에서 복원에 사용할 수 있는 MongoDB 데이터베이스 계정의 모든 리소스를 나열

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"></a>Resource Manager 템플릿을 사용하여 복원

Resource Manager 템플릿을 사용하여 계정을 복원할 수도 있습니다. 템플릿을 정의할 때 다음 매개 변수를 포함합니다.

* `createMode` 매개 변수를 *Restore* 로 설정합니다.
* `restoreParameters`를 정의합니다. `restoreSource` 값은 원본 계정에 대한 `az cosmosdb restorable-database-account list` 명령의 출력에서 추출됩니다. 계정 이름에 대한 인스턴스 ID 특성을 사용하여 복원을 수행합니다.
* `restoreMode` 매개 변수를 *PointInTime* 으로 설정하고 `restoreTimestampInUtc` 값을 구성합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

다음으로 Azure PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다. 다음 예제에서는 CLI 명령을 사용하여 템플릿을 배포하는 방법을 보여줍니다.  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)를 사용하여 지속적인 백업을 프로비저닝합니다.
* [정기 백업에서 연속 백업으로 계정으로 마이그레이션하는 방법](migrate-continuous-backup.md).
* [지속적인 백업 모드 리소스 모델](continuous-backup-restore-resource-model.md)
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.
