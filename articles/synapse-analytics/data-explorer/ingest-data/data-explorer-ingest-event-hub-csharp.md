---
title: C \# 수집을 사용하여 이벤트 허브에서 Azure Synapse 데이터 탐색기(미리 보기)로 데이터 수집
description: C를 사용하여 \# Event Hub에서 Azure Synapse 데이터 탐색기 데이터를 수집(로드)하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 9e8cf21f2cb54246152015e42d6c0e5b2cabcad5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482989"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-c-preview"></a>C(미리 보기)를 사용하여 Azure Synapse 데이터 탐색기 대한 이벤트 허브 데이터 연결 만들기 \#

> [!div class="op_single_selector"]
> * [포털](data-explorer-ingest-event-hub-portal.md)
> * [한 번 클릭](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

이 문서에서는 C 를 사용하여 Azure Synapse 데이터 탐색기 대한 이벤트 허브 데이터 연결을 \# 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [수집할 데이터가 있는 Event Hub](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> Synapse 작업 영역에서 데이터 유출 방지를 사용하도록 설정된 관리형 가상 네트워크를 사용하는 경우 이벤트 허브에서 데이터 탐색기 풀로 데이터를 수집할 수 없습니다.

- Visual Studio 2019, **평가판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하여 사용합니다. Visual Studio 설정 중에 **Azure 개발** 을 사용하도록 설정합니다.

<!-- * Set [database and table policies](database-table-policies-csharp.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Event Hub 데이터 연결 추가

다음 예에서는 프로그래밍 방식으로 Event Hub 데이터 연결을 추가하는 방법을 보여 줍니다. Azure Portal 사용하여 [Event Hub](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub) 데이터 연결을 추가하는 자세한 내용은 Event Hub에 연결을 참조하세요.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The Event Hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var compression = "None";
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat, compression: compression));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 리소스를 만드는 데 사용하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 암호입니다.|
| resourceGroupName | *testrg* | 클러스터가 포함된 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| dataConnectionName | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| tableName | *StormEvents* | 대상 데이터베이스의 대상 테이블 이름입니다.|
| mappingRuleName | *StormEvents_CSV_Mapping* | 대상 테이블과 관련된 열 매핑의 이름입니다.|
| dataFormat | *csv* | 메시지의 데이터 형식입니다.|
| eventHubResourceId | *리소스 ID* | 수집할 데이터를 보유하는 Event Hub의 리소스 ID입니다. |
| consumerGroup | *$Default* | Event Hub의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|
| 압축 | *Gzip* 또는 *없음* | 데이터 압축 형식입니다. |

## <a name="generate-data"></a>데이터 생성

데이터를 생성하고 이벤트 허브로 보내는 [샘플 앱을](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 참조하세요.

이벤트는 크기 한도까지 하나 이상의 레코드를 포함할 수 있습니다. 다음 샘플에서는 각각 5개의 레코드가 추가된 두 개의 이벤트를 보냅니다.

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) };
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../includes/data-explorer-data-connection-clean-resources-csharp.md)]

## <a name="next-steps"></a>다음 단계

- [데이터 탐색기 사용하여 분석](../../get-started-analyze-data-explorer.md)
- [데이터 탐색기 풀 모니터링](../data-explorer-monitor-pools.md)
