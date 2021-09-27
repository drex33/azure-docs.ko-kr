---
title: Azure Blob Storage의 변경 피드 처리 | Microsoft Docs
description: .NET 클라이언트 애플리케이션에서 변경 피드 로그를 처리하는 방법 알아보기
author: tamram
ms.author: tamram
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d43b91fcebff017d6d18ee736cfddc858650fc7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620201"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Azure Blob Storage에서 변경 피드 처리

변경 피드는 스토리지 계정의 Blob 및 Blob 메타데이터에 발생하는 모든 변경 내용에 대한 트랜잭션 로그를 제공합니다. 이 문서에서는 Blob 변경 피드 프로세서 라이브러리를 사용하여 변경 피드 레코드를 읽는 방법을 보여 줍니다.

변경 피드에 대한 자세한 내용은 [Azure Blob Storage 변경 피드](storage-blob-change-feed.md)를 참조하세요.

## <a name="get-the-blob-change-feed-processor-library"></a>Blob 변경 피드 프로세서 라이브러리 가져오기

1. 명령 창을 엽니다(예: Windows PowerShell).
2. 프로젝트 디렉터리에서 [**Azure.Storage.Blobs.Changefeed** NuGet 패키지](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/)를 설치합니다.

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```


## <a name="read-records"></a>레코드 읽기

> [!NOTE]
> 변경 피드는 스토리지 계정에 있는 변경할 수 없는 읽기 전용 엔터티입니다. 제한 없는 애플리케이션에서 편리한 방식으로 동시에 또는 독립적으로 변경 피드를 읽고 처리할 수 있습니다. 애플리케이션에서 레코드를 읽어도 레코드는 변경 피드에서 제거되지 않습니다. 사용하는 각 판독기의 읽기 또는 반복 상태는 독립적이며 애플리케이션에서만 유지 관리됩니다.

이 예제에서는 변경 피드의 모든 레코드를 반복하고, 목록에 추가한 다음, 해당 목록을 호출자에게 반환합니다.

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

이 예제에서는 목록의 각 레코드에서 몇 가지 값을 콘솔에 출력합니다.

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>저장된 위치에서 레코드 읽기 계속

변경 피드에 읽기 위치를 저장한 다음, 나중에 레코드 반복을 다시 시작하도록 선택할 수 있습니다. 변경 피드 커서를 가져와 읽기 위치를 저장할 수 있습니다. 커서는 **문자열** 이며 애플리케이션은 애플리케이션의 디자인에 적합한 방식(예: 파일 또는 데이터베이스)으로 해당 문자열을 저장할 수 있습니다.

이 예제에서는 변경 피드의 모든 레코드를 반복하고, 목록에 추가하고, 커서를 저장합니다. 목록 및 커서가 호출자에게 반환됩니다.

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {

        changeFeedEvents.Add(changeFeedEvent);             
    }

    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>레코드 처리 스트리밍

변경 피드에 커밋될 때 변경 피드 레코드를 처리하도록 선택할 수 있습니다. [사양](storage-blob-change-feed.md#specifications)을 참조하세요. 변경 이벤트는 평균 60초 동안 변경 피드에 게시됩니다. 폴링 간격을 지정할 때 이 기간의 새로운 변경 내용을 폴링하는 것이 좋습니다.

이 예제에서는 정기적으로 변경 내용을 폴링합니다.  변경 레코드가 있는 경우 이 코드는 해당 레코드를 처리하고 변경 피드 커서를 저장합니다. 이러한 방식으로 프로세스가 중지되었다가 다시 시작되는 경우 애플리케이션은 커서를 사용하여 마지막으로 중지했던 레코드의 처리를 다시 시작할 수 있습니다. 이 예제에서는 커서를 로컬 애플리케이션 구성 파일에 저장하지만 애플리케이션은 시나리오에 가장 적합한 형식으로 커서를 저장할 수 있습니다.

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }

                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>시간 범위 내에서 레코드 읽기

특정 시간 범위 내에 속하는 레코드를 읽을 수 있습니다. 이 예제에서는 변경 피드에서 2020년 3월 2일 오후 3시부터 2020년 7월 7일 오전 2시 사이의 모든 레코드를 반복한 후, 목록에 추가하고 해당 목록을 호출자에게 반환합니다.

### <a name="selecting-segments-for-a-time-range"></a>시간 범위에 대한 세그먼트 선택

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

사용자가 제공하는 시작 시간은 가장 가까운 시간으로 반내림되고 종료 시간은 가장 가까운 시간으로 반올림됩니다. 사용자는 시작 시간 이전에 발생한 이벤트와 종료 시간 이후에 발생한 이벤트를 볼 수 있습니다. 또한 시작 시간과 종료 시간 사이에 발생하는 일부 이벤트는 표시되지 않을 수 있습니다. 이벤트가 시작 시간 이전 시간 또는 종료 시간 이후 시간 동안 기록될 수 있기 때문입니다.

## <a name="next-steps"></a>다음 단계

변경 피드 로그에 대해 자세히 알아봅니다. [Azure Blob Storage의 변경 피드](storage-blob-change-feed.md)를 참조하세요.
