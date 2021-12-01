---
title: Azure Functions에 대한 Azure Table Storage 입력 바인딩
description: Azure Functions에서 Azure Table Storage 입력 바인딩을 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 95fc02024b15b44f73ed90025a201af9de72e88a
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133425299"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Azure Functions에 대한 Azure Table Storage 입력 바인딩

Azure Table Storage 입력 바인딩을 사용하여 Azure Storage 계정에서 테이블을 읽을 수 있습니다.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>하나의 엔터티

다음 예제에서는 단일 테이블 행을 읽을 수 있는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다. 큐에 전송된 모든 메시지에 대해 함수가 트리거됩니다.

행 키 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable`은 [Functions v2 이상 런타임](functions-versions.md)에서만 지원됩니다.

Azure Storage SDK를 사용하여 테이블을 읽는 `CloudTable` 메서드 매개 변수를 사용합니다. 다음은 Azure Functions 로그 테이블을 쿼리하는 함수 예제입니다.

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

CloudTable을 사용하는 방법에 대한 자세한 내용은 [Azure Table Storage 시작](../cosmos-db/tutorial-develop-table-dotnet.md)을 참조하세요.

`CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

### <a name="iqueryable"></a>IQueryable

`IQueryable`은 [Functions v1 런타임](functions-versions.md)에서만 지원됩니다.

다음 예제는 `MyPoco` 클래스가 `TableEntity`에서 파생된 여러 테이블 행을 읽는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>하나의 엔터티

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 트리거를 사용하여 단일 테이블 행을 읽습니다. 

*function.json* 파일은 `partitionKey` 및 `rowKey`를 지정합니다. `rowKey` 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable`은 [버전 2.x 이상](functions-versions.md)에 대한 Functions 런타임에서 지원되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 `CloudTable` 메서드 매개 변수를 사용합니다. 다음은 Azure Functions 로그 테이블을 쿼리하는 함수 예제입니다.

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

CloudTable을 사용하는 방법에 대한 자세한 내용은 [Azure Table Storage 시작](../cosmos-db/tutorial-develop-table-dotnet.md)을 참조하세요.

`CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

### <a name="iqueryable"></a>IQueryable

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 메시지에 지정된 파티션 키의 엔터티를 읽습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 엔터티 형식이 `TableEntity`에서 파생할 수 있도록 Azure Storage SDK에 대한 참조를 추가합니다.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="java"></a>[Java](#tab/java)

다음 예제는 Table Storage의 지정된 파티션에 있는 사용자 개체 목록을 반환하는 HTTP 트리거 함수를 보여줍니다. 예제에서 파티션 키는 http 경로에서 추출되고 tableName 및 연결은 함수 설정에서 추출됩니다. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

TableInput 주석은 다음 예제와 같이 요청의 json 본문에서 바인딩을 추출할 수도 있습니다.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

다음 예제에서는 필터를 사용하여 Azure 테이블에서 특정 이름을 가진 사용자를 쿼리하고 가능한 일치 항목 수를 10개의 결과로 제한합니다.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 코드](functions-reference-node.md)에서 테이블 입력 바인딩을 보여줍니다. 함수는 큐 트리거를 사용하여 단일 테이블 행을 읽습니다. 

*function.json* 파일은 `partitionKey` 및 `rowKey`를 지정합니다. `rowKey` 값 "{queueTrigger}"는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 함수는 큐 트리거를 사용하여 단일 테이블 행을 함수에 대한 입력으로 읽습니다.

이 예제에서 바인딩 구성은 테이블의 `partitionKey`에 대해 명시적 값을 지정하고 식을 사용하여 `rowKey`에 전달합니다. `rowKey` 식(`{queueTrigger}`)은 큐 메시지 문자열에서 가져온 행 키를 나타냅니다.

_function.json_ 의 바인딩 구성:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

_run.ps1_ 의 PowerShell 코드:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

다음 함수는 큐 트리거를 사용하여 단일 테이블 행을 함수에 대한 입력으로 읽습니다.

이 예제에서 바인딩 구성은 테이블의 `partitionKey`에 대해 명시적 값을 지정하고 식을 사용하여 `rowKey`에 전달합니다. `rowKey` 식(`{id}`)은 큐 메시지 문자열에서 가져온 행 키를 나타냅니다.

_function.json_ 파일의 바인딩 구성:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

*\_\_init\_\_.py* 파일의 Python 코드:

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

이 간단한 바인딩을 사용하면 행 키 ID가 있는 행을 찾을 수 없는 경우를 프로그래밍 방식으로 처리할 수 없습니다. 보다 세분화된 데이터를 선택하기 위해 [스토리지 SDK](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd)를 사용합니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

 [C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 테이블 입력 바인딩을 구성합니다.

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  특성의 생성자는 테이블 이름, 파티션 키 및 행 키를 사용합니다. 특성은 다음 예제와 같이 `out` 매개 변수 또는 함수의 반환 값에서 사용할 수 있습니다.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  전체 예제는 C# 예제를 참조하세요.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  사용할 스토리지 계정을 지정하는 다른 방법을 제공합니다. 생성자는 스토리지 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

사용할 스토리지 계정은 다음과 같은 순서로 결정됩니다.

* `Table` 특성의 `Connection` 속성
* `Table` 특성과 동일한 매개 변수에 적용된 `StorageAccount` 특성
* 함수에 적용된 `StorageAccount` 특성
* 클래스에 적용된 `StorageAccount` 특성
* 함수 앱의 기본 스토리지 계정("AzureWebJobsStorage" 앱 설정)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Table Storage에서 제공되는 매개 변수에 대한 `@TableInput` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `Table` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `table`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 테이블 또는 엔터티를 나타내는 변수의 이름입니다. | 
|**tableName** | **TableName** | 테이블의 이름입니다.| 
|**partitionKey** | **PartitionKey** |선택 사항입니다. 읽을 테이블 엔터티의 파티션 키입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#usage) 섹션을 참조하세요.| 
|**rowKey** |**RowKey** | 선택 사항입니다. 읽을 테이블 엔터티의 행 키입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#usage) 섹션을 참조하세요.| 
|**take** |**Take** | 선택 사항입니다. JavaScript에서 읽을 수 있는 엔터티의 최대 수입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#usage) 섹션을 참조하세요.| 
|**filter** |**Filter** | 선택 사항입니다. JavaScript에서 테이블 입력에 대한 OData 필터 식입니다. 이 속성을 사용하는 방법에 대한 지침은 [사용](#usage) 섹션을 참조하세요.| 
|**connection** |**연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 설정은 "AzureWebJobs" 접두사 앱 설정의 이름 또는 연결 문자열 이름일 수 있습니다. 예를 들어 설정 이름이 "AzureWebJobsMyStorage"인 경우 여기에서 "MyStorage"를 지정할 수 있습니다. Functions 런타임은 "AzureWebJobsMyStorage"라는 앱 설정을 자동으로 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

* **한 행 읽기**

  `partitionKey` 및 `rowKey`를 설정합니다. `T <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. `T`는 일반적으로 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* **하나 이상의 행 읽기**

  `IQueryable<T> <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. `T`는 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `IQueryable` 메서드를 사용하여 필요한 필터링을 수행할 수 있습니다 `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.  

  > [!NOTE]
  > `IQueryable`은 [Functions v2 런타임](functions-versions.md)에서 지원되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 [CloudTable paramName 메서드 매개 변수를 사용](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)합니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **한 행 읽기**

  `partitionKey` 및 `rowKey`를 설정합니다. `T <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. `T`는 일반적으로 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* **하나 이상의 행 읽기**

  `IQueryable<T> <paramName>` 메서드 매개 변수를 사용하여 테이블 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. `T`는 `ITableEntity`를 구현하거나 `TableEntity`에서 파생되는 형식입니다. `IQueryable` 메서드를 사용하여 필요한 필터링을 수행할 수 있습니다 `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.  

  > [!NOTE]
  > `IQueryable`은 [Functions v2 런타임](functions-versions.md)에서 지원되지 않습니다. 대신 Azure Storage SDK를 사용하여 테이블을 읽는 [CloudTable paramName 메서드 매개 변수를 사용](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)합니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="java"></a>[Java](#tab/java)

[Tableinput](/java/api/com.microsoft.azure.functions.annotation.tableinput) 특성은 함수를 트리거한 테이블 행에 대 한 액세스 권한을 제공합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`filter` 및 `take` 속성을 설정합니다. `partitionKey` 또는 `rowKey`를 설정하지 않습니다. `context.bindings.<BINDING_NAME>`을 사용하여 입력 테이블 엔터티(또는 여러 엔터티)에 액세스합니다. 역직렬화된 개체는 `RowKey` 및 `PartitionKey` 속성을 가집니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

데이터는 *function.json* 파일의 `name` 키에 지정된 것처럼 입력 매개 변수로 전달됩니다. `partitionKey` 및 `rowKey`를 지정하면 특정 레코드를 필터링할 수 있습니다. 자세한 내용은 [PowerShell 예제](#example)를 참조하세요.

# <a name="python"></a>[Python](#tab/python)

테이블 데이터는 JSON 문자열로 함수에 전달됩니다. 입력 [예제](#example)에 표시된 대로 `json.loads`를 호출하여 메시지를 역직렬화합니다.

---

## <a name="next-steps"></a>다음 단계

* [함수에서 Table Storage 데이터 쓰기](./functions-bindings-storage-table-output.md)
