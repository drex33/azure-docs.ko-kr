---
title: 함수에 대 한 Azure SQL 출력 바인딩
description: Azure Functions에서 Azure SQL 출력 바인딩을 사용 하는 방법을 알아봅니다.
author: dzsquared
ms.topic: reference
ms.date: 11/9/2021
ms.author: drskwier
ms.openlocfilehash: f2909917f7cdab501fd3667a452c835c9cb8f915
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352736"
---
# <a name="azure-sql-output-binding-for-azure-functions"></a>Azure Functions에 대 한 Azure SQL 출력 바인딩

Azure SQL 출력 바인딩을 사용 하면 데이터베이스에 쓸 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-azure-sql.md)를 참조하세요.


<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [Http 트리거, 한 레코드 쓰기](#http-trigger-write-one-record-c)
* [Http 트리거, IAsyncCollector를 사용 하 여 레코드 작성](#http-trigger-write-records-using-iasynccollector-c)

이 예에서는 `ToDoItem` 형식 및 해당 데이터베이스 테이블을 참조 합니다.

```cs
namespace AzureSQLSamples
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public int Priority { get; set; }
        public string Description { get; set; }
    }
}
```

```sql
CREATE TABLE dbo.ToDo (
    [Id] int primary key,
    [Priority] int null,
    [Description] nvarchar(200) not null
)
```

<a id="http-trigger-write-one-record-c"></a>

### <a name="http-trigger-write-one-record"></a>Http 트리거, 한 레코드 쓰기

다음 예제에서는 Queue storage의 메시지에 제공된 데이터를 사용하여 문서를 데이터베이스에 추가하는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace AzureSQLSamples
{
    public static class WriteOneRecord
    {
        [FunctionName("WriteOneRecord")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
        {
            newItem = new ToDoItem
            {
                Id = req.Query["id"],
                Description =req.Query["desc"]
            };

            log.LogInformation($"C# HTTP trigger function inserted one row");
            return new CreatedResult($"/api/addtodo", newItem);
        }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### <a name="http-trigger-write-records-using-iasynccollector"></a>HTTP 트리거, IAsyncCollector를 사용 하 여 레코드 작성

다음 예제에서는 HTTP POST 본문 JSON에 제공 된 데이터를 사용 하 여 레코드 컬렉션을 데이터베이스에 추가 하는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.Products", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 JavaScript를 지원 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 Python을 지원 하지 않습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C # 클래스 라이브러리](functions-dotnet-class-library.md)에서 [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs) 특성을 사용 합니다.

특성의 생성자는 SQL 명령 텍스트와 연결 문자열 설정 이름을 사용 합니다. 출력 바인딩의 경우 SQL 명령 문자열은 데이터를 저장할 테이블 이름입니다. 연결 문자열 설정 이름은 `local.settings.json` Azure SQL 또는 SQL Server 인스턴스에 대 한 [연결 문자열](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) 을 포함 하는 응용 프로그램 설정 (로컬 개발의 경우)에 해당 합니다.

다음은 메서드 서명의 `Sql` 특성 예제입니다.

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
    {
        ...
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 JavaScript를 지원 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Azure Functions에 대 한 Azure SQL 바인딩은 현재 Python을 지원 하지 않습니다.

---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## <a name="next-steps"></a>다음 단계

- [데이터베이스에서 데이터 읽기 (입력 바인딩)](./functions-bindings-azure-sql-input.md)
