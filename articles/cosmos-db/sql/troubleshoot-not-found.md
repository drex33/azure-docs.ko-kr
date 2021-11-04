---
title: Azure Cosmos DB 찾을 수 없음 예외 문제 해결
description: 찾을 수 없음 예외를 진단하고 해결하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 05/26/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1d95b399718a7d4b7f88daaef31bb11f763fa2d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045172"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Azure Cosmos DB 찾을 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 상태 코드 404는 리소스가 더 이상 존재하지 않음을 나타냅니다.

## <a name="expected-behavior"></a>예상되는 동작
애플리케이션에서 코드 404를 예상하고 시나리오를 올바르게 처리하는 다양한 시나리오를 사용할 수 있습니다.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>존재하거나 존재해야 하는 항목에 대해 찾을 수 없음 예외가 반환되었습니다.
항목이 존재하거나 존재해야 하는 경우 상태 코드 404가 반환되는 이유는 다음과 같습니다.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>읽기 세션을 입력 세션 토큰에 사용할 수 없음

#### <a name="solution"></a>해결책:
1. 현재 SDK를 사용 가능한 최신 버전으로 업데이트합니다. 이 특정 오류의 가장 일반적인 원인은 최신 SDK 버전에서 수정되었습니다.

### <a name="race-condition"></a>경합 조건
여러 SDK 클라이언트 인스턴스가 있으며 쓰기 전에 읽기가 발생했습니다.

#### <a name="solution"></a>해결책:
1. Azure Cosmos DB에 대한 기본 계정 일관성은 세션 일관성입니다. 항목이 만들어지거나 업데이트되면 응답에서 SDK 인스턴스 간에 전달될 수 있는 세션 토큰을 반환하여 읽기 요청이 해당 변경 내용이 있는 복제본을 읽도록 합니다.
1. [일관성 수준](../consistency-levels.md)을 [더 높은 수준](../consistency-levels.md)으로 변경합니다.

### <a name="reading-throughput-for-a-container-or-database-resource"></a>컨테이너 또는 데이터베이스 리소스에 대한 처리량 읽기
PowerShell 또는 Azure CLI를 사용하고 *찾을 수 없음* 오류 메시지를 받습니다.

#### <a name="solution"></a>해결책:
처리량은 데이터베이스 수준, 컨테이너 수준 또는 둘 다에서 프로비저닝할 수 있습니다. *찾을 수 없음* 오류가 발생하면 부모 데이터베이스 리소스 또는 하위 컨테이너 리소스의 처리량을 읽어보세요.

### <a name="invalid-partition-key-and-id-combination"></a>잘못된 파티션 키 및 ID 조합
파티션 키와 ID 조합이 유효하지 않습니다.

#### <a name="solution"></a>해결책:
잘못된 조합을 발생시키는 애플리케이션 논리를 수정합니다. 

### <a name="invalid-character-in-an-item-id"></a>항목 ID의 잘못된 문자
항목 ID에 [잘못된 문자](/dotnet/api/microsoft.azure.documents.resource.id#remarks)가 포함된 항목이 Azure Cosmos DB에 삽입됩니다.

#### <a name="solution"></a>해결책:
ID를 특수 문자를 포함하지 않는 다른 값으로 변경합니다. ID 변경이 옵션이 아닌 경우 ID를 Base64로 인코딩하여 특수 문자를 이스케이프할 수 있습니다. Base64는 대체해야 하는 잘못된 문자 '/'로 이름을 계속 생성할 수 있습니다.

ID에 대해 컨테이너에 이미 삽입된 항목은 이름 기반 참조 대신 RID 값을 사용하여 바꿀 수 있습니다.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
// https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>TTL(Time to Live) 제거
항목에 [TTL(Time to Live)](./time-to-live.md) 속성이 설정되었습니다. TTL 속성이 만료되어 항목이 제거되었습니다.

#### <a name="solution"></a>해결책:
항목이 제거되지 않도록 TTL 속성을 변경합니다.

### <a name="lazy-indexing"></a>지연 인덱싱
[지연 인덱싱](../index-policy.md#indexing-mode)이 catch되지 않았습니다.

#### <a name="solution"></a>해결책:
인덱싱이 인덱싱 정책을 따라잡거나 변경할 때까지 기다리세요.

### <a name="parent-resource-deleted"></a>부모 리소스 삭제됨
항목이 존재하는 데이터베이스 또는 컨테이너가 삭제되었습니다.

#### <a name="solution"></a>해결책:
1. 부모 리소스를 [복원](../configure-periodic-backup-restore.md#request-restore)하거나 리소스를 다시 만듭니다.
1. 삭제된 리소스를 대체할 새 리소스를 만듭니다.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. 컨테이너/컬렉션 이름은 대/소문자를 구분합니다.
컨테이너/컬렉션 이름은 Cosmos DB에서 대/소문자를 구분합니다.

#### <a name="solution"></a>해결책:
Cosmos DB에 연결하는 동안 정확한 이름을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.
