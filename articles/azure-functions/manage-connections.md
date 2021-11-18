---
title: Azure Functions에서 연결 관리
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/23/2021
ms.openlocfilehash: 2c5bd2c29de2aeaece71d99bb6a1269af9b45cf2
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755795"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Functions에서 연결 관리

함수 앱의 함수는 리소스를 공유합니다. 이러한 공유 리소스 중에는 HTTP 연결, 데이터베이스 연결 및 Azure Storage와 같은 서비스 연결이 있습니다. 소비 계획에서 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. 이 문서에서는 필요한 것보다 더 많은 연결을 사용하지 않도록 함수를 코딩하는 방법을 설명합니다.

> [!NOTE]
> 이 문서에 설명된 연결 제한은 [소비 계획](consumption-plan.md)에서 실행할 때만 적용됩니다. 그러나 여기에 설명된 기술은 모든 계획에서 실행할 때 유용할 수 있습니다.

## <a name="connection-limit"></a>연결 제한

이 계획의 함수 앱은 [샌드박스 환경](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)에서 실행되기 때문에 소비 계획에서 사용 가능한 연결 수는 부분적으로 제한됩니다. 샌드박스가 코드에 부과하는 제한 사항 중 하나는 아웃바운드 연결 수에 대한 제한으로, 현재 인스턴스당 활성 연결 600개(총 1,200개)입니다. 이 제한에 도달하면 함수 런타임이 로그에 `Host thresholds exceeded: Connections`라는 메시지를 씁니다. 자세한 내용은 [함수 서비스 제한](functions-scale.md#service-limits)을 참조하세요.

이 제한은 인스턴스당입니다. 더 많은 요청을 처리하기 위해 [크기 조정 컨트롤러가 함수 앱 인스턴스를 추가](event-driven-scaling.md)하는 경우 인스턴스마다 독립적인 연결 제한이 있습니다. 즉, 전역 연결 제한이 없으며 모든 활성 인스턴스에서 600개가 넘는 활성 연결을 가질 수 있습니다.

문제를 해결할 때 함수 앱에 Application Insights를 사용하도록 설정했는지 확인합니다. Application Insights를 사용하면 실행과 같은 함수 앱에 대한 메트릭을 볼 수 있습니다. 자세한 내용은 [Application Insights에서 원격 분석 보기](analyze-telemetry-data.md#view-telemetry-in-application-insights)를 참조하세요.  

## <a name="static-clients"></a>정적 클라이언트

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. 함수를 작성할 수 있는 모든 언어에 대해 클라이언트 연결을 재사용하는 것이 좋습니다. 예를 들어 단일 정적 클라이언트를 사용하는 경우 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) 등의 .NET 클라이언트와 Azure Storage 클라이언트에서 연결을 관리할 수 있습니다.

Azure Functions 애플리케이션에서 서비스 특정 클라이언트를 사용할 때 따라야 할 몇 가지 지침은 다음과 같습니다.

- *금지*: 함수를 호출할 때마다 새 클라이언트를 만듭니다.
- *허용*: 함수를 호출할 때마다 사용할 수 있는 단일 정적 클라이언트를 만듭니다.
- *권장*: 다른 함수에서 동일한 서비스를 사용하는 경우 공유 도우미 클래스에 단일 정적 클라이언트를 만듭니다.

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="http-requests"></a>HTTP 요청

# <a name="c"></a>[C#](#tab/csharp)
다음은 정적 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) 인스턴스를 만드는 C# 함수 코드의 예입니다.

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET의 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)에 대한 일반적인 질문은 “내 클라이언트를 삭제해야 할까요?”입니다. 일반적으로 `IDisposable`을 구현하는 개체의 사용이 완료되면 해당 개체를 삭제합니다. 그러나 함수가 끝날 때 사용이 완료되지 않기 때문에 정적 클라이언트를 삭제하지 않습니다. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

개선된 연결 관리 옵션을 제공하므로 비네이티브 메서드 대신 `node-fetch` 모듈과 같이 네이티브 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 클래스를 사용해야 합니다. 연결 매개변수는 `http.agent` 클래스의 옵션을 통해 구성됩니다. HTTP 에이전트에서 사용할 수 있는 자세한 옵션은 [새 에이전트(\[옵션\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)를 참조하세요.

`http.request()`에서 사용하는 전체 `http.globalAgent` 클래스에는 이러한 모든 값이 해당 기본값으로 설정되어 있습니다. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

```js
http.globalAgent.maxSockets = 200;
```

 다음 예제에서는 해당 요청에 대해서만 사용자 지정 HTTP 에이전트를 사용하여 새 HTTP 요청을 만듭니다.

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
const options = { agent: httpAgent };
http.request(options, onResponseCallback);
```

---

### <a name="azure-cosmos-db-clients"></a>Azure Cosmos DB 클라이언트 

# <a name="c"></a>[C#](#tab/csharp)

[CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient)는 Azure Cosmos DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```cs
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos;

private static Lazy<CosmosClient> lazyClient = new Lazy<CosmosClient>(InitializeCosmosClient);
private static CosmosClient cosmosClient => lazyClient.Value;

private static CosmosClient InitializeCosmosClient()
{
    // Perform any initialization here
    var uri = "https://youraccount.documents.azure.com:443";
    var authKey = "authKey";
   
    return new CosmosClient(uri, authKey);
}

public static async Task Run(string input)
{
    Container container = cosmosClient.GetContainer("database", "collection");
    MyItem item = new MyItem{ id = "myId", partitionKey = "myPartitionKey", data = "example" };
    await container.UpsertItemAsync(document);
   
    // Rest of function
}
```
또한 트리거에 대해 "function.proj"라는 파일을 만들고 아래 콘텐츠를 추가합니다.

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Cosmos" Version="3.23.0" />
    </ItemGroup>
</Project>

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient)는 Azure Cosmos DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

---

## <a name="sqlclient-connections"></a>SqlClient 연결

함수 코드에서 .NET Framework Data Provider for SQL Server([SqlClient](/dotnet/api/system.data.sqlclient))를 사용하여 SQL 관계형 데이터베이스에 연결할 수 있습니다. SqlClient는 [Entity Framework](/ef/ef6/) 등 ADO.NET을 사용하는 데이터 프레임워크의 기본 공급자이기도 합니다. [HttpClient](/dotnet/api/system.net.http.httpclient) 및 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. 그러나 연결이 부족해질 수 있으므로 데이터베이스에 대한 연결을 최적화해야 합니다. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> Entity Framework 등의 일부 데이터 프레임워크는 일반적으로 구성 파일의 **ConnectionStrings** 섹션에서 연결 문자열을 가져옵니다. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-develop-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. 함수 코드에서 [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection)의 인스턴스를 만드는 경우 **애플리케이션 설정** 에 다른 연결과 함께 연결 문자열 값을 저장해야 합니다.

## <a name="next-steps"></a>다음 단계

정적 클라이언트가 권장되는 이유에 대한 자세한 내용은 [부적절한 인스턴스화 안티패턴](/azure/architecture/antipatterns/improper-instantiation/)을 참조하세요.

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
