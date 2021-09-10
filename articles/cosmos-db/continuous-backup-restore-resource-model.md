---
title: Azure Cosmos DB 특정 시점 복원 기능의 리소스 모델.
description: 이 문서에서는 Azure Cosmos DB 특정 시점 복원 기능의 리소스 모델에 대해 설명합니다. SQL 및 MongoDB 계정에 대한 Azure Cosmos DB API에서 복원할 수 있는 지속적인 백업 및 리소스를 지원하는 매개 변수를 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e4fffd12b72b41c45b2718e96c34a03e28eeca29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535959"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Azure Cosmos DB 특정 시점 복원 기능의 리소스 모델
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB 특정 시점 복원 기능의 리소스 모델에 대해 설명합니다. SQL 및 MongoDB 계정에 대한 Azure Cosmos DB API에서 복원할 수 있는 지속적인 백업 및 리소스를 지원하는 매개 변수를 설명합니다.

## <a name="database-accounts-resource-model"></a>데이터베이스 계정의 리소스 모델

데이터베이스 계정의 리소스 모델은 새 복원 시나리오를 지원하기 위해 몇 가지 추가 속성으로 업데이트됩니다. 이 속성은 **BackupPolicy, CreateMode 및 RestoreParameters** 입니다.

### <a name="backuppolicy"></a>BackupPolicy

`backuppolicy` 매개 변수 아래에 있는 `Type`이라는 계정 레벨 백업 정책의 새 속성은 지속적인 백업 및 특정 시점 복원 기능을 사용합니다. 이 모드는 **지속적인 백업** 이라고 합니다. 계정을 만들거나 [계정을 주기적 모드에서 지속적인 모드로 마이그레이션](migrate-continuous-backup.md)하는 동안 이 모드를 설정할 수 있습니다. 지속적인 모드를 사용하도록 설정하면 이 계정에서 생성된 모든 컨테이너와 데이터베이스에는 기본적으로 사용으로 설정된 지속적인 백업 및 특정 시점 복원 기능이 있습니다.

> [!NOTE]
> 현재 지정 시간 복원 기능은 MongoDB용 Azure Cosmos DB API 및 SQL 계정에서 사용할 수 있습니다. 연속 모드를 사용하여 계정을 만들고 나면 정기 모드로 전환할 수 없습니다.

### <a name="createmode"></a>CreateMode

이 속성은 계정을 만든 방법을 나타냅니다. 가능한 값은 *기본값* 과 *복원* 입니다. 복원을 수행하려면 이 값을 *복원* 으로 설정하고 `RestoreParameters` 속성에 적절한 값을 제공합니다.

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters` 리소스에는 계정 ID, 복원 시간 및 복원해야 하는 리소스를 포함한 복원 작업 세부 정보가 포함됩니다.

|속성 이름 |설명  |
|---------|---------|
|restoreMode  | 복원 모드는 *PointInTime* 이어야 함 |
|restoreSource   |  복원이 시작되는 원본 계정의 instanceId입니다.       |
|restoreTimestampInUtc  | 계정을 복원해야 하는 특정 시간(UTC)입니다. |
|databasesToRestore   | 복원해야 하는 데이터베이스 및 컨테이너를 지정하는 `DatabaseRestoreResource` 개체 목록입니다. 각 리소스는 단일 데이터베이스 및 해당 데이터베이스 아래의 모든 컬렉션을 나타냅니다. 자세한 내용은 [복원 가능한 SQL 리소스](#restorable-sql-resources) 섹션을 참조하세요. 이 값이 비어 있으면 전체 계정이 복원됩니다.   |

### <a name="sample-resource"></a>샘플 리소스

다음 JSON은 지속적인 백업을 사용하는 샘플 데이터베이스 계정 리소스입니다.

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    }
}
```

## <a name="restorable-resources"></a>복원 가능한 리소스

리소스에 관한 중요한 정보(예: 복원할 수 있는 리소스, 복원할 수 있는 위치, 해당 리소스에 대해 주요 작업이 수행된 타임스탬프)를 검색하는 데 도움이 되는 새로운 리소스 및 API 세트를 사용할 수 있습니다.

> [!NOTE]
> 해당 리소스를 열거하는 데 사용되는 모든 API에는 다음 권한이 필요합니다.
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>복원 가능한 데이터베이스 계정

이 리소스는 복원할 수 있는 데이터베이스 계정 인스턴스를 포함합니다. 데이터베이스 계정은 삭제된 계정이거나 라이브 계정일 수 있습니다. 복원하려는 원본 데이터베이스 계정을 찾는 데 사용할 수 있는 정보를 포함합니다.

|속성 이름 |설명  |
|---------|---------|
| ID | 리소스의 고유 식별자입니다. |
| accountName | 글로벌 데이터베이스 계정 이름입니다. |
| creationTime | 계정이 생성되었거나 마이그레이션된 시간(UTC)입니다.  |
| deletionTime | 계정이 삭제된 시간(UTC)입니다.  계정이 라이브 상태인 경우 이 값이 비어 있습니다. |
| apiType | Azure Cosmos DB 계정의 API 형식입니다. |
| restorableLocations | 계정이 있던 위치의 목록입니다. |
| restorableLocations: locationName | 지역 계정의 지역 이름입니다. |
| restorableLocations: regionalDatabaseAccountInstanceId | 지역 계정의 GUID입니다. |
| restorableLocations: creationTime | 지역 계정이 생성되었거나 마이그레이션된 시간(UTC)입니다.|
| restorableLocations: deletionTime | 지역 계정이 삭제된 시간(UTC)입니다. 지역 계정이 라이브 상태인 경우 이 값이 비어 있습니다.|

복원 가능한 모든 계정 목록을 얻으려면 [복원 가능한 데이터베이스 계정 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list) 또는 [복원 가능한 데이터베이스 계정 - 위치별 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list-by-location) 문서를 참조하세요.

### <a name="restorable-sql-database"></a>복원 가능한 SQL 데이터베이스

각 리소스에는 SQL Database에서 발생한 생성 및 삭제와 같은 변형 이벤트 정보가 포함되어 있습니다. 이 정보는 데이터베이스가 실수로 삭제된 시나리오 및 사용자가 이벤트가 발생한 시기를 알아야 하는 경우에 도움이 될 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp | 데이터베이스를 만들거나 삭제한 시간(UTC)입니다. |
| ownerId | SQL 데이터베이스의 이름입니다. |
| ownerResourceId | SQL 데이터베이스의 리소스 ID임|
| VM에서 operationType 작업을 | 이 데이터베이스 이벤트의 작업 형식입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>만들기: 데이터베이스 만들기 이벤트</li><li>삭제: 데이터베이스 삭제 이벤트</li><li>바꾸기: 데이터베이스 수정 이벤트</li><li>SystemOperation: 시스템에서 트리거하는 데이터베이스 수정 이벤트입니다. 이 이벤트는 사용자가 시작하지 않음</li></ul> |
| 데이터베이스 |이벤트 발생 시 SQL 데이터베이스의 속성|

모든 데이터베이스 변형 목록을 가져오려면 [복원 가능한 SQL 데이터베이스 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-databases/list) 문서를 참조하세요.

### <a name="restorable-sql-container"></a>복원 가능한 SQL 컨테이너

각 리소스에는 SQL 컨테이너에서 발생한 생성 및 삭제와 같은 변형 이벤트 정보가 포함되어 있습니다. 이 정보는 컨테이너가 수정되었거나 삭제된 시나리오 및 사용자가 해당 이벤트가 발생한 시기를 알아야 하는 경우에 도움이 될 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp    | 이 컨테이너 이벤트가 발생한 시간(UTC)입니다.|
| ownerId| SQL 컨테이너의 이름입니다.|
| ownerResourceId   | SQL 컨테이너의 리소스 ID입니다.|
| VM에서 operationType 작업을 | 이 컨테이너 이벤트의 작업 형식입니다. 가능한 값은 다음과 같습니다. <br/><ul><li>만들기: 컨테이너 만들기 이벤트</li><li>삭제: 컨테이너 삭제 이벤트</li><li>바꾸기: 컨테이너 수정 이벤트</li><li>SystemOperation: 시스템에서 트리거하는 컨테이너 수정 이벤트입니다. 이 이벤트는 사용자가 시작하지 않음</li></ul> |
| container | 이벤트 발생 시 SQL 컨테이너의 속성입니다.|

같은 데이터베이스 아래의 모든 컨테이너 변형 목록을 가져오려면 [복원 가능한 SQL 컨테이너 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-containers/list) 문서를 참조하세요.

### <a name="restorable-sql-resources"></a>복원 가능한 SQL 리소스

각 리소스는 단일 데이터베이스와 해당 데이터베이스의 모든 컨테이너를 나타냅니다.

|속성 이름 |설명  |
|---------|---------|
| databaseName  | SQL 데이터베이스의 이름입니다.
| collectionNames   | 이 데이터베이스에 있는 SQL 컨테이너 목록입니다.|

지정된 타임스탬프와 위치에서 계정에 있는 SQL 데이터베이스와 컨테이너 콤보 목록을 가져오려면 [복원 가능한 SQL 리소스 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-resources/list) 문서를 참조하세요.

### <a name="restorable-mongodb-database"></a>복원 가능한 MongoDB 데이터베이스

각 리소스에는 MongoDB 데이터베이스에서 발생한 생성 및 삭제와 같은 변형 이벤트 정보가 포함되어 있습니다. 이 정보는 데이터베이스가 실수로 삭제된 시나리오 및 사용자가 해당 이벤트가 발생한 시기를 알아야 하는 경우에 도움이 될 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
|eventTimestamp| 이 데이터베이스 이벤트가 발생한 시간(UTC)입니다.|
| ownerId| MongoDB 데이터베이스의 이름입니다. |
| ownerResourceId   | MongoDB 데이터베이스의 리소스 ID입니다. |
| VM에서 operationType 작업을 |   이 데이터베이스 이벤트의 작업 형식입니다. 가능한 값은 다음과 같습니다.<br/><ul><li> 만들기: 데이터베이스 만들기 이벤트</li><li> 삭제: 데이터베이스 삭제 이벤트</li><li> 바꾸기: 데이터베이스 수정 이벤트</li><li> SystemOperation: 시스템에서 트리거하는 데이터베이스 수정 이벤트입니다. 이 이벤트는 사용자가 시작하지 않음 </li></ul> |

모든 데이터베이스 변형 목록을 가져오려면 [복원 가능한 MongoDB 데이터베이스 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-databases/list) 문서를 참조하세요.

### <a name="restorable-mongodb-collection"></a>복원 가능한 MongoDB 컬렉션

각 리소스에는 MongoDB 컬렉션에서 발생한 생성 및 삭제와 같은 변형 이벤트 정보가 포함되어 있습니다. 이 정보는 컬렉션이 수정되었거나 삭제된 시나리오 및 사용자가 해당 이벤트가 발생한 시기를 알아야 하는 경우에 도움이 될 수 있습니다.

|속성 이름 |설명  |
|---------|---------|
| eventTimestamp |이 컬렉션 이벤트가 발생한 시간(UTC)입니다. |
| ownerId| MongoDB 컬렉션의 이름입니다. |
| ownerResourceId   | MongoDB 컬렉션의 리소스 ID입니다. |
| VM에서 operationType 작업을 |이 컬렉션 이벤트의 작업 형식입니다. 가능한 값은 다음과 같습니다.<br/><ul><li>만들기: 컬렉션 만들기 이벤트</li><li>삭제: 컬렉션 삭제 이벤트</li><li>바꾸기: 컬렉션 수정 이벤트</li><li>SystemOperation: 시스템에서 트리거하는 컬렉션 수정 이벤트입니다. 이 이벤트는 사용자가 시작하지 않음</li></ul> |

같은 데이터베이스 아래의 모든 컨테이너 변형 목록을 가져오려면 [복원 가능한 Mongodb 컬렉션 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-collections/list) 문서를 참조하세요.

### <a name="restorable-mongodb-resources"></a>복원 가능한 MongoDB 리소스

각 리소스는 단일 데이터베이스와 해당 데이터베이스의 모든 컬렉션을 나타냅니다.

|속성 이름 |설명  |
|---------|---------|
| databaseName  |MongoDB 데이터베이스의 이름입니다. |
| collectionNames | 이 데이터베이스의 MongoDB 컬렉션 목록입니다. |

지정된 타임스탬프 및 위치에서 계정에 있는 모든 MongoDB 데이터베이스 및 컬렉션 조합 목록을 얻으려면 [복원 가능한 Mongodb 리소스 - 목록](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-resources/list) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) 또는 [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template)를 사용하여 지속적인 백업을 프로비저닝합니다.
* [Azure Portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli) 또는 [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template)를 사용하여 계정을 복원합니다.
* [정기적 백업에서 지속적인 백업으로 계정에 마이그레이션](migrate-continuous-backup.md)합니다.
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.
