---
title: 인덱서 실행 예약
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서 예약으로 콘텐츠를 주기적으로 또는 특정 시간에 인덱싱합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: a9cfaf6cf3e45a790c5235153691e301779f5c83
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133129218"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서 예약 방법

인덱서는 만든 직후 한 번 실행됩니다. 이후에는 Azure Portal, [인덱서 실행(REST)](/rest/api/searchservice/run-indexer) 또는 Azure SDK를 사용하여 요청 시 다시 실행할 수 있습니다. 또는 일정에 따라 실행되도록 인덱서를 구성할 수도 있습니다. 인덱서 일정 작성이 유용한 경우는 다음과 같습니다.

* 원본 데이터는 시간이 지남에 따라 변하며, 검색 인덱서가 자동으로 차이를 처리하기를 원합니다.

* 원본 데이터가 매우 크고 시간에 따라 인덱서 처리를 분산하고자 합니다. 인덱서 작업은 정규 데이터 원본의 경우 최대 실행 시간이 24시간, 기술 세트를 사용하는 인덱서의 경우 2시간입니다. 최대 간격 내에 인덱싱을 완료할 수 없는 경우 2시간마다 실행되는 일정을 구성할 수 있습니다. 인덱서는 인덱싱이 마지막으로 종료된 위치를 표시하는 내부 상위 워터마크에 따라 마지막으로 종료된 위치를 자동으로 찾을 수 있습니다. 2시간 반복 일정으로 인덱서를 실행하면 단일 작업에 대해 허용되는 간격 이상의 매우 큰 데이터 세트(수백만 개의 문서)을 처리할 수 있습니다. 대용량 데이터 볼륨을 인덱싱하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에서 대용량 데이터 세트를 인덱싱하는 방법](search-howto-large-index.md)을 참조하세요.

* 여러 데이터 원본에서 검색 인덱스가 채워지고, 인덱서를 서로 다른 시간에 실행하여 충돌을 줄이려고 합니다.

시각적으로 일정은 1월 1일에 시작하여 50분마다 실행되는 것처럼 보일 수 있습니다.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> 스케줄러는 Azure Cognitive Search의 기본 제공 기능입니다. 외부 스케줄러는 지원되지 않습니다.

## <a name="schedule-property"></a>schedule 속성

일정은 인덱서 정의의 일부입니다. **schedule** 속성이 생략되면 인덱서가 생성된 직후에 한 번만 실행됩니다. **schedule** 속성을 추가하는 경우 두 부분을 지정합니다.

| 속성 | 설명 |
|----------|-------------|
|**간격** | (필수) 두 번의 연속된 인덱서 실행 시작 사이의 시간 간격입니다. 허용되는 가장 작은 간격은 5분이고, 가장 긴 간격은 1,440분(24시간)입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. <br/><br/>예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.|
| **시작 시간(UTC)** | (선택 사항) 일정이 지정된 실행이 시작되어야 하는 시기를 나타냅니다. 생략된 경우 현재 UTC 시간이 사용됩니다. 이 시간은 과거의 시간일 수 있습니다. 이 경우 첫 번째 실행은 인덱서가 원래 **startTime** 이후에 지속적으로 실행된 것처럼 예약됩니다.<br/><br/>예: `2021-01-01T00:00:00Z`는 1월 1일 자정에 시작하고, `2021-01-05T22:28:00Z`는 1월 5일, 오후 10시 28분에 시작합니다.|

## <a name="scheduling-behavior"></a>일정 예약 동작

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 인덱서가 이미 실행 중일 때 다음 실행이 예약된 경우 다음 예약 시간까지 실행이 연기됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. **간격** 이 매시간이고 **시작 시간** 이 2019년 6월 1일 오전 8시(UTC)인 인덱서 예약을 구성한다고 가정합니다. 인덱서 실행 시간이 1시간 이상 걸리면 다음과 같은 일이 발생할 수 있습니다.

* 첫 번째 인덱서 실행이 2019년 6월 1일 오전 8시(UTC)경에 시작됩니다. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
* 두 번째 실행은 2019년 6월 1일 오전 9시(UTC)경에 시작됩니다. 이 실행에 70분(1시간 초과)이 걸리고 오전 10시 10분(UTC)까지 완료되지 않는다고 가정합니다.
* 세 번째 실행은 오전 10시(UTC)에 시작하도록 예약되어 있지만, 이전 실행이 계속해서 실행되고 있습니다. 이 경우 예약된 실행을 건너뜁니다. 인덱서의 다음 실행은 오전 11시(UTC)가 되어서야 시작됩니다.

> [!NOTE]
> 인덱서가 특정 일정으로 설정되어 있지만 매번 같은 문서를 반복적으로 실패하는 경우 인덱서는 성공적으로 다시 진행될 때까지 빈도가 낮은 간격(최대 24시간마다 한 번 이상)으로 실행을 시작합니다. 문제를 해결했다고 생각되면 인덱서를 수동으로 실행할 수 있으며, 인덱싱이 성공하면 인덱서는 일반 일정으로 돌아갑니다.

## <a name="schedule-using-rest"></a>REST를 사용하여 예약

인덱서를 만들거나 업데이트할 때 **schedule** 속성을 지정합니다.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>.NET을 사용하여 예약

다음 C# 예제에서는 미리 정의된 데이터 원본 및 인덱스를 사용하여 Azure SQL 데이터베이스 인덱서를 만들고, 지금부터 시작하여 매일 한 번 실행되도록 일정을 설정합니다.

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

일정은 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)를 사용하여 인덱서를 만들거나 업데이트할 때 [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) 클래스를 사용하여 정의됩니다. **IndexingSchedule** 생성자에는 **TimeSpan** 개체를 사용하여 지정된 **Interval** 매개 변수가 필요합니다. 앞에서 설명한 것처럼 허용되는 가장 작은 간격 값은 5분이고, 가장 큰 값은 24시간입니다. **DateTimeOffset** 개체로 지정되는 두 번째 **StartTime** 매개 변수는 선택 사항입니다.

## <a name="next-steps"></a>다음 단계

일정에 따라 실행되는 인덱서의 경우 검색 서비스에서 상태를 검색하여 작업을 모니터링하거나 진단 로깅을 사용하도록 설정하여 자세한 정보를 얻을 수 있습니다.

* [검색 인덱서 상태 모니터](search-howto-monitor-indexers.md)
* [로그 데이터 수집 및 분석](monitor-azure-cognitive-search.md)