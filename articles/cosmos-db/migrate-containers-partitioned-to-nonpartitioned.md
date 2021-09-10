---
title: 분할되지 않은 Azure Cosmos 컨테이너를 분할된 컨테이너로 마이그레이션
description: 분할되지 않은 기존의 모든 컨테이너를 분할된 컨테이너로 마이그레이션하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a84c3912a8a58674b58f17a32a48e763693cf9
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037722"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>분할되지 않은 컨테이너를 분할된 컨테이너로 마이그레이션
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB는 파티션 키 없이 컨테이너 만들기를 지원합니다. 현재는 2.x 버전보다 작거나 같은 버전의 Azure CLI 및 Azure Cosmos DB SDK(.Net, Java, NodeJS)를 사용하여 분할되지 않은 컨테이너를 만들 수 있습니다. Azure Portal을 사용하여 분할되지 않은 컨테이너를 만들 수는 없습니다. 하지만 이러한 분할되지 않은 컨테이너는 탄력적이지 않으며 저장소 용량은 20GB로 고정되어 있으며 처리량 제한은 10,000RU/s입니다.

분할되지 않은 컨테이너는 레거시 컨테이너이므로 분할되지 않은 기존 컨테이너를 분할된 컨테이너로 마이그레이션하여 스토리지 및 처리량의 크기를 조정해야 합니다. Azure Cosmos DB는 분할되지 않은 컨테이너를 분할된 컨테이너로 마이그레이션하기 위한 시스템 정의 메커니즘을 제공합니다. 이 문서에서는 기존의 분할되지 않은 모든 컨테이너를 분할된 컨테이너로 자동 마이그레이션하는 방법에 대해 설명합니다. 모든 언어에서 V3 버전의 SDK를 사용하는 경우에만 자동 마이그레이션 기능을 사용할 수 있습니다.

> [!NOTE]
> 현재 이 문서에서 설명한 단계를 통해 Azure Cosmos DB MongoDB 및 Gremlin API 계정을 마이그레이션할 수는 없습니다.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>시스템 정의 파티션 키를 사용하여 컨테이너 마이그레이션

마이그레이션을 지원하기 위해 Azure Cosmos DB는 파티션 키가 없는 모든 컨테이너에서 `/_partitionkey`라는 시스템 정의 파티션 키를 제공합니다. 컨테이너를 마이그레이션한 후에는 파티션 키 정의를 변경할 수 없습니다. 예를 들어, 분할된 컨테이너로 마이그레이션되는 컨테이너에 대한 정의는 다음과 같습니다.

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

컨테이너를 마이그레이션한 후에는 문서의 다른 속성과 함께 `_partitionKey` 속성을 채워서 문서를 만들 수 있습니다. `_partitionKey` 속성은 문서의 파티션 키를 나타냅니다.

프로비전된 처리량을 최적으로 활용하려면 올바른 파티션 키를 선택하는 것이 중요합니다. 자세한 내용은 [파티션 키를 선택하는 방법](partitioning-overview.md) 문서를 참조하세요.

> [!NOTE]
> 모든 언어에서 최신 V3 버전의 SDK를 사용하는 경우에만 시스템 정의 파티션 키를 활용할 수 있습니다.

다음 예제에서는 시스템 정의 파티션 키로 문서를 만들고 해당 문서를 읽는 샘플 코드를 보여줍니다.

**문서의 JSON 표현**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

전체 샘플은 [.Net 샘플][1] GitHub 리포지토리를 참조하세요.
                      
## <a name="migrate-the-documents"></a>문서 마이그레이션

컨테이너 정의는 파티션 키 속성을 사용하여 향상되지만 컨테이너 내의 문서는 자동으로 마이그레이션되지 않습니다. 즉, 시스템 파티션 키 속성 `/_partitionKey` 경로가 기존 문서에 자동으로 추가되지 않습니다. 파티션 키 없이 만든 문서를 읽고 문서에서 `_partitionKey` 속성을 사용하여 다시 작성하여 기존 문서를 다시 분할해야 합니다.

## <a name="access-documents-that-dont-have-a-partition-key"></a>파티션 키가 없는 문서에 액세스

애플리케이션은 "PartitionKey.None"이라는 특수 시스템 속성을 사용하여 파티션 키가 없는 기존 문서에 액세스할 수 있습니다. 이는 마이그레이션되지 않은 문서의 값입니다. 모든 CRUD 및 쿼리 작업에서 이 속성을 사용할 수 있습니다. 다음 예제에서는 NonePartitionKey에서 단일 문서를 읽는 샘플을 보여줍니다. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

문서를 다시 분할하는 방법에 대한 전체 샘플은 [.Net 샘플][1] GitHub 리포지토리를 참조하세요. 

## <a name="compatibility-with-sdks"></a>SDK와의 호환성

이전 버전의 Azure Cosmos DB SDK(예: V2.x.x) 및 V1.x.x는 시스템 정의 파티션 키 속성을 지원하지 않습니다. 따라서 이전 SDK에서 컨테이너 정의를 읽으면 파티션 키 정의가 포함되지 않으며 이러한 컨테이너는 이전과 정확히 동일하게 동작합니다. 이전 버전의 SDK를 사용하여 빌드된 애플리케이션은 변경 없이 그대로 분할되지 않은 상태로 계속 작동합니다. 

마이그레이션된 컨테이너가 SDK의 최신/V3 버전에서 사용되고 새 문서 내에서 시스템 정의 파티션 키를 채우기 시작하면 이전 SDK에서 이러한 문서에 대한 액세스(읽기, 업데이트, 삭제, 쿼리)를 더 이상 할 수 없습니다.

## <a name="known-issues"></a>알려진 문제

**V3 SDK를 사용하여 파티션 키 없이 삽입된 항목 수에 대한 쿼리는 더 높은 처리량 사용을 포함할 수 있습니다.**

V2 SDK를 사용하여 삽입된 항목 또는 `PartitionKey.None` 매개 변수로 V3 SDK를 사용하여 삽입된 항목에 대해 V3 SDK에서 쿼리하는 경우 COUNT 쿼리는 `PartitionKey.None` 매개 변수가 FeedOptions에서 제공되는 경우 더 많은 RU/s를 사용할 수 있습니다. 파티션 키와 함께 삽입된 다른 항목이 없는 경우 `PartitionKey.None` 매개 변수를 제공하지 않는 것이 좋습니다.

파티션 키에 대해 다른 값을 사용하여 새 항목을 삽입하는 경우 `FeedOptions`에서 적절한 키를 전달하여 이러한 항목 수를 쿼리하면 문제가 발생하지 않습니다. 파티션 키를 사용하여 새 문서를 삽입한 후 파티션 키 값을 사용하지 않고 문서 개수만 쿼리해야 하는 경우, 해당 쿼리는 일반 분할된 컬렉션과 비슷하게 높은 RU/s를 초래할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](./account-databases-containers-items.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB Capacity Planner를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration