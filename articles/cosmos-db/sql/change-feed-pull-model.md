---
title: 변경 피드 풀 모델
description: Azure Cosmos DB 변경 피드 끌어오기 모델을 사용하여 끌어오기 모델과 변경 피드 프로세서의 차이점 및 변경 피드를 읽는 방법 알아보기
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/02/2021
ms.reviewer: sngun
ms.openlocfilehash: 06345674b17b790cadf69a2b10383015fdaaa134
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116369"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드 끌어오기 모델
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

변경 피드 끌어오기 모델을 사용하면 Azure Cosmos DB 변경 피드를 원하는 속도로 사용할 수 있습니다. [변경 피드 프로세서](change-feed-processor.md)로 이미 수행하듯이, 변경 피드 끌어오기 모델을 사용하여 다수의 변경 피드 소비자에 대한 변경 처리를 병렬화할 수 있습니다.

## <a name="comparing-with-change-feed-processor"></a>변경 피드 프로세서와 비교

많은 시나리오에서 [변경 피드 프로세서](change-feed-processor.md) 또는 끌어오기 모델을 사용하여 변경 피드를 처리할 수 있습니다. 끌어오기 모델의 연속 토큰 및 변경 피드 프로세서의 임대 컨테이너는 변경 피드에서 마지막으로 처리된 항목(또는 항목의 모음)에 대한 "책갈피"입니다.

하지만 연속 토큰을 임대 컨테이너로(또는 그 반대로) 변환할 수 없습니다.

> [!NOTE]
> 대부분의 경우 변경 피드에서 읽어야 하는 경우 가장 간단한 옵션은 [변경 피드 프로세서](change-feed-processor.md)를 사용하는 것입니다.

다음 시나리오에서는 끌어오기 모델 사용을 고려해야 합니다.

- 특정 파티션 키에서 변경 내용 읽기
- 클라이언트에서 변경 내용을 받아 처리하는 속도 제어
- 변경 피드에서 기존 데이터를 한 번 읽음(예: 데이터 마이그레이션 수행)

변경 피드 프로세서와 끌어오기 모델의 주요 차이는 다음과 같습니다.

|기능  | 변경 피드 프로세서| 끌어오기 모델 |
| --- | --- | --- |
| 처리 중인 변경 피드의 현재 지점 추적 | 임대(Azure Cosmos DB 컨테이너에 저장됨) | 연속 토큰(메모리에 저장되거나 수동으로 유지됨) |
| 이전 변경 내용 재생 가능 여부 | 예, 밀어넣기 모델 사용 | 예, 끌어오기 모델 사용|
| 이후 변경 내용 폴링 | 사용자 지정 `WithPollInterval`에 기반하여 변경 내용을 자동으로 확인 | 설명서 |
| 새로운 변경 내용이 없는 동작 | 자동으로 `WithPollInterval`을 기다렸다가 다시 확인 | 상태를 확인하고 수동으로 다시 확인 |
| 전체 컨테이너의 변경 내용 처리 | 예, 동일한 컨테이너에서 사용하는 여러 스레드/머신을 자동으로 병렬화| 예, FeedRange를 사용하여 수동으로 병렬화 |
| 단일 파티션 키의 변경 내용 처리 | 지원되지 않음 | 예|

> [!NOTE]
> 변경 피드 프로세서를 사용하여 읽을 때와 달리 새로운 변경 내용이 없는 경우를 명시적으로 처리해야 합니다. 

## <a name="consuming-an-entire-containers-changes"></a>전체 컨테이너의 변경 내용 사용

끌어오기 모델을 사용하여 변경 피드를 처리하는 `FeedIterator`를 만들 수 있습니다. `FeedIterator`를 처음 만들 때는 변경 사항을 읽기 위한 시작 위치와 원하는 `FeedRange`로 구성된 필수 `ChangeFeedStartFrom` 값을 지정해야 합니다. `FeedRange`는 파티션 키 값의 범위이며, 특정 `FeedIterator`를 사용하여 변경 피드에서 읽을 항목을 지정합니다.

필요에 따라 `ChangeFeedRequestOptions`를 지정하여 `PageSizeHint`를 설정할 수 있습니다. 설정된 후 이 속성은 페이지당 수신되는 최대 항목 수를 설정합니다. 모니터링되는 컬렉션의 작업이 저장 프로시저를 통해 수행되는 경우 변경 피드에서 항목을 읽을 때 트랜잭션 범위가 유지됩니다. 결과적으로, 수신한 항목 수가 지정된 값보다 높을 수 있어 동일한 트랜잭션에 의해 변경된 항목이 하나의 원자성 일괄 처리의 일부로 반환됩니다.

`FeedIterator`는 두 가지 버전으로 제공됩니다. 엔터티 개체를 반환하는 아래 예제 외에, `Stream` 지원을 통해 응답을 얻을 수도 있습니다. 스트림을 사용하면 데이터를 먼저 역직렬화하지 않고도 읽을 수 있어서, 클라이언트 리소스를 절약할 수 있습니다.

엔터티 개체(이 경우, `User` 개체)를 반환하는 `FeedIterator`를 가져오는 예는 다음과 같습니다.

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

`Stream`을 반환하는 `FeedIterator`를 가져오는 예는 다음과 같습니다.

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

`FeedIterator`에 `FeedRange`를 제공하지 않으면 전체 컨테이너의 변경 피드를 고유한 속도로 처리할 수 있습니다. 다음은 현재 시간부터 모든 변경 내용 읽기를 시작하는 예제입니다.

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForTheEntireContainer.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else 
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

변경 피드는 사실상 향후의 모든 쓰기 및 업데이트를 포괄하는 항목의 무한 목록이기 때문에`HasMoreResults`의 값은 항상 true입니다. 변경 피드를 읽으려고 할 때 사용 가능한 새 변경 내용이 없는 경우 `NotModified` 상태의 응답을 받게 됩니다. 위의 예제에서 변경 사항을 다시 확인하기 전에 5초 동안 대기하여 처리합니다.

## <a name="consuming-a-partition-keys-changes"></a>파티션 키의 변경 내용 사용

특정 파티션 키의 변경 내용만 처리하려는 경우가 있습니다. 특정 파티션 키에 대한 `FeedIterator`를 가져와서 전체 컨테이너와 같은 방식으로 변경 내용을 처리할 수 있습니다.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    FeedResponse<User> response = await iteratorForThePartitionKey.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>병렬 처리에 FeedRange 사용

[변경 피드 프로세서](change-feed-processor.md)에서 작업은 여러 소비자에게 자동으로 분산됩니다. 변경 피드 끌어오기 모델에서 `FeedRange`를 사용하여 변경 피드에 대한 처리를 병렬화할 수 있습니다. `FeedRange`는 파티션 키 값의 범위를 나타냅니다.

컨테이너에 대한 범위 목록을 가져오는 방법을 보여주는 예는 다음과 같습니다.

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

컨테이너에 대한 FeedRanges 목록을 가져올 때 [실제 파티션](../partitioning-overview.md#physical-partitions)당 하나의 `FeedRange`를 가져옵니다.

`FeedRange`를 사용하면 여러 머신 또는 스레드의 변경 피드 처리를 병렬화하는 `FeedIterator`를 만들 수 있습니다. 전체 컨테이너에 또는 단일 파티션 키에 대해 `FeedIterator`를 얻는 방법을 보여주는 이전 예제와 달리, FeedRanges를 사용하면 변경 피드를 병렬로 처리할 수 있는 여러 FeedIterators를 얻을 수 있습니다.

FeedRanges를 사용하려는 경우에는 FeedRanges를 가져와서 해당 머신에 배포하는 오케스트레이터 프로세스가 필요합니다. 이러한 배포는 다음과 같을 수 있습니다.

* `FeedRange.ToJsonString`을 사용하고 이 문자열 값을 배포합니다. 소비자는 이 값을 `FeedRange.FromJsonString`과 함께 사용할 수 있습니다.
* 배포를 처리 중이면 `FeedRange` 개체 참조를 전달하는 것입니다.

다음은 병렬로 읽는 개별 가상 머신 두 대를 사용하여 컨테이너의 변경 피드 시작 부분부터 읽는 방법을 보여주는 샘플입니다.

머신 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

머신 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    FeedResponse<User> response = await iteratorA.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}
```

## <a name="saving-continuation-tokens"></a>연속 토큰 저장

연속 토큰을 획득하여 `FeedIterator`의 위치를 저장할 수 있습니다. 연속 토큰은 FeedIterator가 마지막으로 처리한 변경 내용의 지점을 추적하고 나중에 이 지점에서 `FeedIterator`을 다시 시작할 수 있도록 하는 문자열 값입니다. 다음은 컨테이너를 만든 이후의 변경 피드를 읽는 코드입니다. 변경 내용이 더 이상 없으면 연속 토큰을 유지하여 변경 피드 사용을 나중에 재개할 수 있도록 합니다.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
    FeedResponse<User> response = await iterator.ReadNextAsync();

    if (response.StatusCode == HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        continuation = response.ContinuationToken;
        // Stop the consumption since there are no new changes
        break;
    }
    else
    {
        foreach (User user in response)
        {
            Console.WriteLine($"Detected change for user with id {user.id}");
        }
    }
}

// Some time later when I want to check changes again
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Cosmos 컨테이너가 여전히 존재하는 한 FeedIterator의 연속 토큰은 만료되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [변경 피드 개요](../change-feed.md)
* [변경 피드 프로세서 사용](change-feed-processor.md)
* [Azure Functions 트리거](change-feed-functions.md)
